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
