---
description: enumerazione di informazioni relative alla registrazione di un sito web
---

# Whois Enumeration

Whois è un protocollo internet che viene usato per chiedere info sugli utenti assegnatari a cui è registrata una risorsa internet come un nome di dominio, un IP, o un Autonomous System

Informazioni che possiamo trovare:

* Nome dominio
* Data registrazione e ultimo update del dominio
* Con chi è stato registrato il dominio
* Azienda che ha registrato
  * Informazioni come nome della persona e email che hanno registrato il dominio o l'IP
  * Nome azienda
  * Indirizzo
* Name Server
* Range di indirizzi IP
* Subnet mask di un IP

## Comando whois

```
whois <dominio.com>
```

<figure><img src="../../.gitbook/assets/image (48).png" alt=""><figcaption></figcaption></figure>

Ci viene mostrato con chi è stato registrato il dominio (namecheap in questo esempio sopra)

Quando è stato rinnovato e creato

Quando scadrà il dominio

Vediamo anche l'abuse contact mail e phone

Il name server, che in questo caso punta a cloudflare

DNSSEC è abilitato quindi informazioni sulla persona fisica che ha registrato il dominio sono disponibili, come nome, e indirizzo mail.

<figure><img src="../../.gitbook/assets/image (49).png" alt=""><figcaption><p>Qui c'è anche qualche informazione in merito all'azienda che viene registrata oppure il nome delle persone</p></figcaption></figure>

### Who.is website

<figure><img src="../../.gitbook/assets/image (50).png" alt=""><figcaption></figcaption></figure>

<figure><img src="../../.gitbook/assets/image (51).png" alt=""><figcaption></figcaption></figure>

Ci dà le stesse informazioni che abbiamo visto + qualche info in merito al registrant, che in questo caso è anonimizzato da cloudflare inserendo delle informazioni a caso come l'islanda.

Può essere usato who.is anche su degli IP come ad esempio possiamo usare uno di questi IP del Name Server:

<figure><img src="../../.gitbook/assets/image (52).png" alt=""><figcaption></figcaption></figure>

<figure><img src="../../.gitbook/assets/image (53).png" alt=""><figcaption></figcaption></figure>

Eseguendo il comando da terminale ci dà qualche informazione in più come il range degli indirizzi IP, il CIDR o detto anche subnet range, data di registro e rinnovo, e info sull'organizzazione che sarebbero importanti se stiamo attaccando un'azienda:

<figure><img src="../../.gitbook/assets/image (54).png" alt=""><figcaption></figcaption></figure>
