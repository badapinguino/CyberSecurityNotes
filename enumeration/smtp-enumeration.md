# SMTP Enumeration

<figure><img src="../.gitbook/assets/image (182).png" alt=""><figcaption></figcaption></figure>

Con SMTP possiamo trovare un elenco degli utenti presenti sul sistema, e di conseguenza possiamo usare questi utenti per fare dei brute force su SSH o altri protocolli.

```
service postgresql start
msfconsole
workspace -a SMTP_Enum
setg RHOSTS <IP target>
setg RHOST <IP target>
search type:auxiliary name:smtp
```

<figure><img src="../.gitbook/assets/image (183).png" alt=""><figcaption></figcaption></figure>

## SMTP version enumeration

```
use auxiliary/scanner/smtp/smtp_version
run
```

<figure><img src="../.gitbook/assets/image (184).png" alt=""><figcaption></figcaption></figure>

Ci mostra la versione di SMTP che è running, in questo caso Postfix, e il dominio per questo mail server (openmailbox.xyz)

## SMTP user enumeration

```
use auxiliary/scanner/smtp/smtp_enum     // user enumeration
run
```

<figure><img src="../.gitbook/assets/image (185).png" alt=""><figcaption></figcaption></figure>

La descrizione ci spiega come ottiene la lista degli utenti

UNIXONLY è true perché sappiamo che è un sistema linux, stesso motivo per cui lo USER\_FILE è la default unix\_users.txt

<figure><img src="../.gitbook/assets/image (186).png" alt=""><figcaption></figcaption></figure>

Questa lista di utenti sono informazioni utili per poi effettuare altri attacchi come SSH brute force, oppure per capire per fare cosa è configurato il server (www-data forse è un webserver).

Successivamente nella fase di exploitation vedremo come exploitare una versione vulnerabile di SMTP, ma verrà vista in futuro, in questo caso non possiamo fare altro per quanto riguarda l'enumeration e ci fermiamo qui.



#### Alternativa senza Metasploit: comando smtp-user-enum

```
smtp-user-enum -U /usr/share/commix/src/txt/usernames.txt -t demo.ine.local
```

### SMTP server name e banner

```
nmap -sV -script banner demo.ine.local
```

### Connessione con netcan

#### Connessione + banner e servername

```
nc demo.ine.local 25
```

#### Verificare esistenza di un utente

```
VRFY admin@openmailbox.xyz   // dove admin è username e openmailbox.xyz è servername
```

Se restituisce 252, e in generale non un errore, allora significa che esiste

#### Inviare mail via telnet

```
telnet demo.ine.local 25
HELO attacker.xyz
mail from: admin@attacker.xyz
rcpt to:root@openmailbox.xyz
data
Subject: Hi Root
Hello,
This is a fake mail sent using telnet command.
From,
Admin
.
```

#### Comandi utili

{% embed url="https://www.samlogic.net/articles/smtp-commands-reference.htm" %}

### Inviare una mail con sendemail

```
sendemail -f admin@attacker.xyz -t victimuser@openmailbox.xyz -s demo.ine.local -u Fakemail -m "Hi root, a fake from admin
```
