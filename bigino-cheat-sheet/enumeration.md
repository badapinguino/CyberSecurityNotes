# Enumeration

## Port scanning & enumeration con nmap + import in Metasploit

### Salvare i risultati di uno scan nmap con XML

```
nmap -Pn -sV -O <IP> -oX nome_file.xml
```

### Importare risultati Nmap in Metasploit Framework

#### Caricare file xml nel DB metasploit

1. Avviare il DB metasploit: service postgresql start
2. Entrare in metasploit: msfconsole
3. Creare un nuovo workspace: workspace -a \<nome workspace>
4. Verificare che siamo nel nuovo workspace: workspace
5. Caricare il file XML: db\_import nomefile.xml
6. Verificare che sia caricato con i comandi: hosts, services, vulns

## Avviare Metasploit (MSF)

```
service postresql start
msfconsole
db_status
workspace -a <Nome_Workspace>
setg RHOSTS <IP target>
setg RHOST <IP target>
```

## MSF: Port scanning con auxiliary modules

### Scan TCP

```
use auxiliary/scanner/portscan/tcp
set RHOSTS <IP target>
run
```

### Scan UDP

```
use auxiliary/scanner/discovery/udp_sweep
set RHOSTS <IP target>
run
```

## FTP Enumeration

### Modulo per verificare la versione FTP

```
search type:auxiliary name:ftp
use auxiliary/scanner/ftp/ftp_version
set RHOSTS <IP>
run
```

### Modulo per bruteforce FTP login <a href="#modulo-per-bruteforce-ftp" id="modulo-per-bruteforce-ftp"></a>

Al fine di identificare username e password

```
use auxiliary/scanner/ftp/ftp_login
set RHOSTS <IP>
set USER_FILE /user/share/metasploit-framework/data/wordlists/common_users.txt
set PASS_FILE /user/share/metasploit-framework/data/wordlists/unix_passwords.txt
```

### Modulo per connettersi ad FTP anonimamente (anonymous login) <a href="#modulo-per-connettersi-a-d-ftp-anonimamente-anonymous-login" id="modulo-per-connettersi-a-d-ftp-anonimamente-anonymous-login"></a>

```
search type:auxiliary name:ftp
use auxiliary/scanner/ftp/anonymous
set RHOSTS <IP>
run
```

## SMB Enumeration

1. Enumerare la versione SMB
2. Enumerare gli utenti
3. Enumerare le share
4. Brute force attack per trovare le password degli utenti
5. Accesso alle share tramite le password trovate con il brute force

### SMB version

```
service postgresql start
msfconsole
workspace -a SMB_ENUM
setg RHOSTS <IP dest>
search type:auxiliary name:smb //per filtrare i risultati
use auxiliary/scanner/smb/smb_version
show options //vediamo che c'è RHOSTS è già stato impostato tramite la variabile globale
run // verifichiamo la versione di SMB
```

### Metodo alternativo usando nmap senza metasploit

Find the exact version of samba server by using appropriate nmap script.

```
nmap --script smb-os-discovery.nse -p 445 demo.ine.local
```

## SMB users enumeration

```
use auxiliary/scanner/smb/smb_enumusers
info // possiamo vedere qualche dettaglio in più sul modulo come nome, licenza, options
run
```

## SMB shares enumeration

Per enumerare tutte le cartelle share sul server SMB

```
use auxiliary/scanner/smb/smb_enumshares
show options // tra le opzioni utili al massimo c'è SMBPass e SMBUser nel caso li avessimo e poi ShowFiles per mostrare informazioni dettagliate che potrebbero tornare utili
set ShowFiles true
run
```

### Per verificare se è permesso il login anonimo

Using smbclient determine whether anonymous connection (null session) is allowed on the samba server or not.

```
smbclient -L demo.ine.local -N
```

Anonymous connection is allowed since shares are displayed without requirement of password.

### SMB Login brute force module <a href="#smb-login-brute-force-module" id="smb-login-brute-force-module"></a>

```
use auxiliary/scanner/smb/smb_login
show options // in questo caso dobbiamo impostare SMBUser e PASS_FILE, poi eventualmente BRUTEFORCE_SPEED, STOP_ON_SUCCESS e il numero di THREADS
set SMBUser admin
set PASS_FILE /usr/share/metasploit-framework/data/wordlists/unix_passwords.txt
run
```

### Accesso alle share e file con utility SMBclient (fuori da Metasploit)

```
smbclient -L \\\\192.91.46.3\\ -U admin
```

#### Accesso alla singola share

```
smbclient \\\\<IP>\\public -U admin
```

