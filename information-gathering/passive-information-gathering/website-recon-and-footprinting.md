---
description: Passive recoinassance di un sito web
---

# Website Recon & Footprinting

Footprinting: information gathering ma con informazioni più specifiche

Cosa cerchiamo?

* IP
* Directories hidden from search engines
* names
* email
* numeri di telefono
* indirizzi fisici
* web technologies used on website

### Trovare gli IP

```sh
host <dominio.com>
```

<figure><img src="../../.gitbook/assets/image (35) (1).png" alt=""><figcaption><p>Ci restituisce gli IP, IPv6 e il mailserver con il dominio mail</p></figcaption></figure>

Si possono poi cercare delle informazioni come le mail o indirizzi nelle pagine del sito come nel footer

### Cartelle nascoste

#### Robots.txt

File /robots.txt e serve per indicare ai motori di ricerca quali pagine mostrare e non mostrare

<figure><img src="../../.gitbook/assets/image (36) (1).png" alt=""><figcaption><p>Questo già ci dice che il sito è fatto in wordpress, e c'è la pagina admin che chiede al motore di ricerca che non deve mostrarla</p></figcaption></figure>

#### Sitemap.xml o Sitemap\_index.xml

Serve per mostrare ai motori di ricerca come organizzare e mostrare in modo ordinato le pagine

<figure><img src="../../.gitbook/assets/image (37) (1).png" alt=""><figcaption></figcaption></figure>

<figure><img src="../../.gitbook/assets/image (38) (1).png" alt=""><figcaption><p>Potrebbe tornare utile se trovassimo dei nomi ad esempio qui</p></figcaption></figure>

<figure><img src="../../.gitbook/assets/image (39) (1).png" alt=""><figcaption><p>Qui potremmo trovare delle pagine che non vengono mostrate dall'interfaccia del sito</p></figcaption></figure>

<figure><img src="../../.gitbook/assets/image (40) (1).png" alt=""><figcaption><p>Questa è una pagina da controllare quando abbiamo siti wordpress perché possiamo trovare categorie nascoste al front-end</p></figcaption></figure>

### Plugin browser utili

#### builtwith

Ci dice quali tecnologie vengono usate dal sito web

<figure><img src="../../.gitbook/assets/image (41) (1).png" alt=""><figcaption><p>Dai vari plugin e widget possiamo capire che è un sito wordpress</p></figcaption></figure>

<figure><img src="../../.gitbook/assets/image (42) (1).png" alt=""><figcaption><p>Ci fornisce anche una lista dei subdomain</p></figcaption></figure>

#### Wappalyzer

Un altro web technology profiler

### WhatWeb comando

Utile per conoscere le tecnologie di un sito da shell

```sh
whatweb <dominio.com>
```

<figure><img src="../../.gitbook/assets/image (43) (1).png" alt=""><figcaption></figcaption></figure>

Da questo esempio possiamo vedere che passa per un proxy cloudflare che poi redireziona, e che usa JQuery, Modernizr, PHP, Worpress, ecc...

### Scaricare un intero sito

#### HTTTrack

<figure><img src="../../.gitbook/assets/image (44) (1).png" alt=""><figcaption></figcaption></figure>

è possibile installarlo su Linux

<figure><img src="../../.gitbook/assets/image (45) (1).png" alt=""><figcaption></figcaption></figure>

<figure><img src="../../.gitbook/assets/image (46) (1).png" alt=""><figcaption><p>Si può decidere se scaricare tutto il sito o solo alcune pagine</p></figcaption></figure>

<figure><img src="../../.gitbook/assets/image (47) (1).png" alt=""><figcaption><p>In questo caso è fallito perché il proxy non permette di farlo</p></figcaption></figure>

