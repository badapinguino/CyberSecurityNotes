# DNS Zone Transfers

<figure><img src="../../daniele-badagliacca-docs/docs/.gitbook/assets/image (1679).png" alt=""><figcaption></figcaption></figure>

<figure><img src="../../daniele-badagliacca-docs/docs/.gitbook/assets/image (1680).png" alt=""><figcaption></figcaption></figure>

<figure><img src="../../daniele-badagliacca-docs/docs/.gitbook/assets/image (1681).png" alt=""><figcaption></figcaption></figure>

<figure><img src="../../daniele-badagliacca-docs/docs/.gitbook/assets/image (1682).png" alt=""><figcaption></figcaption></figure>

Un zone file è un file che contiene i record di un dominio.

<figure><img src="../../daniele-badagliacca-docs/docs/.gitbook/assets/image (1751).png" alt=""><figcaption></figcaption></figure>

Esempio su dnsdumpster con zontransfer.me

<figure><img src="../../daniele-badagliacca-docs/docs/.gitbook/assets/image (1752).png" alt=""><figcaption></figcaption></figure>

<figure><img src="../../daniele-badagliacca-docs/docs/.gitbook/assets/image (1753).png" alt=""><figcaption></figcaption></figure>

Abbiamo il domain authority, e due Name Server il primario e secondario.

Oltre dnsrecon possiamo usare anche dnsenum (che fa anche active dns recoineissance)

### Host file

Prima di usare il dns il pc cercherà sul file hosts: in linux /etc/hosts

<figure><img src="../../daniele-badagliacca-docs/docs/.gitbook/assets/image (1754).png" alt=""><figcaption></figcaption></figure>

### dnsenum: Zone transfer

<figure><img src="../../daniele-badagliacca-docs/docs/.gitbook/assets/image (1755).png" alt=""><figcaption></figcaption></figure>

<figure><img src="../../daniele-badagliacca-docs/docs/.gitbook/assets/image (1756).png" alt=""><figcaption></figcaption></figure>

In questo modo possiamo individuare più informazioni rispetto a prima.

<figure><img src="../../daniele-badagliacca-docs/docs/.gitbook/assets/image (1757).png" alt=""><figcaption></figcaption></figure>

Possiamo trovare qualche dominio che punta internamente.

I name server interni possono servire a risolvere altri nomi dominio o IP interni, e possiamo trovare info sulla struttura interna in questo modo, che potremmo usare una volta entrati.

### dig

<figure><img src="../../daniele-badagliacca-docs/docs/.gitbook/assets/image (1758).png" alt=""><figcaption></figcaption></figure>

axfr è lo switch per fargli provare il zone transfer, poi specifichiamo il name server e infine il nome dominio.

dig è meglio perché il tool precedente dnsenum fa troppe azioni e non si riesce bene a controllarle dicendogli cosa ci serve e cosa no.



Su alcuni domini, dove lo zone transfer non è abilitato (es: se si ha cloudflare come misura di sicurezza) si ottiene un errore:

<figure><img src="../../daniele-badagliacca-docs/docs/.gitbook/assets/image (1759).png" alt=""><figcaption></figcaption></figure>



### dnsenum: fase di Brute Force

Durante il bruteforce prende il file dns.txt e prova ad aggiungerli come terzo livello al nome dominio per trovare altri subdomain, esempio del contenuto del file:

<figure><img src="../../daniele-badagliacca-docs/docs/.gitbook/assets/image (1760).png" alt=""><figcaption></figcaption></figure>



### Fierce

<figure><img src="../../daniele-badagliacca-docs/docs/.gitbook/assets/image (1761).png" alt=""><figcaption></figcaption></figure>

Trova quindi tramite dns dei range di indirizzi ip e hostname di un dominio.

```
/fierce -dns <dominio>
```

<figure><img src="../../daniele-badagliacca-docs/docs/.gitbook/assets/image (1762).png" alt=""><figcaption></figcaption></figure>

<figure><img src="../../daniele-badagliacca-docs/docs/.gitbook/assets/image (1763).png" alt=""><figcaption></figcaption></figure>
