# FTP Enumeration

<figure><img src="../.gitbook/assets/image (26).png" alt=""><figcaption></figcaption></figure>

FTP permette di trasferire dati tra client e server

```
service postgresql start
msfconsole
workspace -a FTP_ENUM
use auxiliary/scanner/portscan/tcp
set RHOSTS <IP>
run // verifichiamo che ftp sia attivo sull'IP target

```

## Modulo per verificare la versione di FTP

Stiamo cercando lo scanner module

```
search type:auxiliary name:ftp
use auxiliary/scanner/ftp/ftp_version
set RHOSTS <IP>
run
back
```

<figure><img src="../.gitbook/assets/image (1) (1).png" alt=""><figcaption></figcaption></figure>

<figure><img src="../.gitbook/assets/image (2) (1).png" alt=""><figcaption></figcaption></figure>

In questo caso ci sono diversi moduli per sfruttare vulnerabilità di ProFTPD, ma non eseguiamo nulla perché non siamo ancora alla fase di exploitation (l'ultimo sarebbe l'unico che funziona in questo caso).



## Modulo per bruteforce FTP

Al fine di identificare username e password

```
use auxiliary/scanner/ftp/ftp_login
set RHOSTS <IP>
set USER_FILE /user/share/metasploit-framework/data/wordlists/common_users.txt
set PASS_FILE /user/share/metasploit-framework/data/wordlists/unix_passwords.txt
```

<figure><img src="../.gitbook/assets/image (3) (1).png" alt=""><figcaption></figcaption></figure>

Le opzioni da modificare sono ovviamente l'RHOSTS ma anche USER\_FILE che serve per indicare un file contenente una lista di username (o USERNAME nel caso in cui vogliamo trovare la password di solo un username), e PASS\_FILE che serve per indicare un file contenente una lista di password (o una singola PASSWORD). E se vogliamo la BRUTEFORCE\_SPEED.

Quando trova una coppia di username e password viene evidenziato in verde:

<figure><img src="../.gitbook/assets/image (4) (1).png" alt=""><figcaption></figcaption></figure>

Infine proviamo a connetterci con le credenziali trovate tramite la shell normale (usciamo quindi da msfconsole)

```
ftp <IP> // e inseriamo username e password quando ce lo chiede. Se dà errore potrebbe essere perché abbiamo appena fatto un bruteforce quindi cerca di bloccarci, dobbiamo aspettare e provare più tardi
```

<figure><img src="../.gitbook/assets/image (8) (1).png" alt=""><figcaption></figcaption></figure>

## Modulo per connettersi ad FTP anonimamente (anonymous login)

```
search type:auxiliary name:ftp
use auxiliary/scanner/ftp/anonymous
set RHOSTS <IP>
run
```

<figure><img src="../.gitbook/assets/image (9) (1).png" alt=""><figcaption></figcaption></figure>

In questo caso non è possibile accedere il server FTP anonimously
