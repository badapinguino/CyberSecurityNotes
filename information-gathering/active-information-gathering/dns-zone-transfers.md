# DNS Zone Transfers

<figure><img src="../../.gitbook/assets/image (20) (1) (1).png" alt=""><figcaption></figcaption></figure>

<figure><img src="../../.gitbook/assets/image (21) (1) (1).png" alt=""><figcaption></figcaption></figure>

<figure><img src="../../.gitbook/assets/image (22) (1) (1).png" alt=""><figcaption></figcaption></figure>

<figure><img src="../../.gitbook/assets/image (23) (1) (1).png" alt=""><figcaption></figcaption></figure>

Un zone file è un file che contiene i record di un dominio.

<figure><img src="../../.gitbook/assets/image (92).png" alt=""><figcaption></figcaption></figure>

Esempio su dnsdumpster con zontransfer.me

<figure><img src="../../.gitbook/assets/image (93).png" alt=""><figcaption></figcaption></figure>

<figure><img src="../../.gitbook/assets/image (94).png" alt=""><figcaption></figcaption></figure>

Abbiamo il domain authority, e due Name Server il primario e secondario.

Oltre dnsrecon possiamo usare anche dnsenum (che fa anche active dns recoineissance)

### Host file

Prima di usare il dns il pc cercherà sul file hosts: in linux /etc/hosts

<figure><img src="../../.gitbook/assets/image (95).png" alt=""><figcaption></figcaption></figure>

### dnsenum: Zone transfer

<figure><img src="../../.gitbook/assets/image (96).png" alt=""><figcaption></figcaption></figure>

<figure><img src="../../.gitbook/assets/image (97).png" alt=""><figcaption></figcaption></figure>

In questo modo possiamo individuare più informazioni rispetto a prima.

<figure><img src="../../.gitbook/assets/image (98).png" alt=""><figcaption></figcaption></figure>

Possiamo trovare qualche dominio che punta internamente.

I name server interni possono servire a risolvere altri nomi dominio o IP interni, e possiamo trovare info sulla struttura interna in questo modo, che potremmo usare una volta entrati.

### dig

<figure><img src="../../.gitbook/assets/image (99).png" alt=""><figcaption></figcaption></figure>

axfr è lo switch per fargli provare il zone transfer, poi specifichiamo il name server e infine il nome dominio.

dig è meglio perché il tool precedente dnsenum fa troppe azioni e non si riesce bene a controllarle dicendogli cosa ci serve e cosa no.



Su alcuni domini, dove lo zone transfer non è abilitato (es: se si ha cloudflare come misura di sicurezza) si ottiene un errore:

<figure><img src="../../.gitbook/assets/image (100).png" alt=""><figcaption></figcaption></figure>



### dnsenum: fase di Brute Force

Durante il bruteforce prende il file dns.txt e prova ad aggiungerli come terzo livello al nome dominio per trovare altri subdomain, esempio del contenuto del file:

<figure><img src="../../.gitbook/assets/image (101).png" alt=""><figcaption></figcaption></figure>



### Fierce

<figure><img src="../../.gitbook/assets/image (102).png" alt=""><figcaption></figcaption></figure>

Trova quindi tramite dns dei range di indirizzi ip e hostname di un dominio.

```
/fierce -dns <dominio>
```

<figure><img src="../../.gitbook/assets/image (103).png" alt=""><figcaption></figcaption></figure>

<figure><img src="../../.gitbook/assets/image (104).png" alt=""><figcaption></figcaption></figure>
