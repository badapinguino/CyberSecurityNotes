---
description: DNS Passive Reconaissance
---

# DNS Recon

Identifichiamo i record associati con un dominio DNS, non facciamo nulla di attivo.

<figure><img src="../../.gitbook/assets/image (24) (1) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

## DNS Recon

```sh
dnsrecon -d <nomedominio.com>
```

<figure><img src="../../.gitbook/assets/image (25) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

<figure><img src="../../.gitbook/assets/image (26) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

Informazioni utili:

MX Mail Server

Name Servers

<figure><img src="../../.gitbook/assets/image (27) (1) (1).png" alt=""><figcaption></figcaption></figure>

Da qui si può vedere che stanno usando come mail Google, un record A che associa il nome dominio all'IP, TXT invece viene usato per verificare per tracciare il sito con Google Analytics

## DNS Dumpster

Sito dnsdumpser.com

<figure><img src="../../.gitbook/assets/image (28) (1) (1).png" alt=""><figcaption></figcaption></figure>

<figure><img src="../../.gitbook/assets/image (29) (1) (1).png" alt=""><figcaption></figcaption></figure>

Sui pulsantini si possono avere delle info aggiuntive, ad esempio quali altri siti usano questo server DNS:

<figure><img src="../../.gitbook/assets/image (30) (1) (1).png" alt=""><figcaption></figcaption></figure>

<figure><img src="../../.gitbook/assets/image (31) (1) (1).png" alt=""><figcaption></figcaption></figure>

Vediamo poi i record A e ci sono anche dei subdomain in questo caso, abbiamo scoperto infatti il forum di hackersploit.

<figure><img src="../../.gitbook/assets/image (32) (1) (1).png" alt=""><figcaption></figcaption></figure>

Infine possiamo vedere il grafo (in beta) che ci consente di vedere cosa abbiamo mappato:

<figure><img src="../../.gitbook/assets/image (33) (1) (1).png" alt=""><figcaption></figcaption></figure>

Il mail server non è protetto da cloudflare.

Quest'immagine del grafo può essere scaricata con un PNG o un Excel. Utile da inserire in report

<figure><img src="../../.gitbook/assets/image (34) (1) (1).png" alt=""><figcaption><p>Mappa il dominio per noi e lo mostra in modo comprensibile</p></figcaption></figure>

Cercando un altro sito vediamo:

<figure><img src="../../.gitbook/assets/image (61).png" alt=""><figcaption><p>Da qui possiamo vedere che uno dei DNS server è hostato su AWS.</p></figcaption></figure>

<figure><img src="../../.gitbook/assets/image (62).png" alt=""><figcaption></figcaption></figure>

<figure><img src="../../.gitbook/assets/image (63).png" alt=""><figcaption><p>Ed ecco il mapping del dominio.</p></figcaption></figure>

