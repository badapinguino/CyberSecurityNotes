# HTTP Basics Lab - Part 1

<figure><img src="../../../.gitbook/assets/image (1745).png" alt=""><figcaption></figcaption></figure>

<figure><img src="../../../.gitbook/assets/image (1746).png" alt=""><figcaption></figcaption></figure>

Visitiamo la pagina web del IP target, che contiene un semplice Content Management System con anche un'area di login.

Tracciamo le richieste attraverso Wireshark.

## Avviare Wireshark

```
wireshark -i eth1
## doppio click sull'interfaccia per avviare lo sniffing
```

<figure><img src="../../../.gitbook/assets/image (1747).png" alt=""><figcaption></figcaption></figure>

<figure><img src="../../../.gitbook/assets/image (1748).png" alt=""><figcaption></figcaption></figure>

<figure><img src="../../../.gitbook/assets/image (1749).png" alt=""><figcaption></figcaption></figure>

## Cosa succede quando carico la pagina web?

<figure><img src="../../../.gitbook/assets/image (1750).png" alt=""><figcaption></figcaption></figure>

<figure><img src="../../../.gitbook/assets/image (1751).png" alt=""><figcaption></figcaption></figure>

3 way handshake del TCP per stabilire la connessione come prima cosa: SYN -> SYN/ACK -> ACK

<figure><img src="../../../.gitbook/assets/image (1752).png" alt=""><figcaption></figcaption></figure>

## Contenuto della richiesta GET / HTTP

<figure><img src="../../../.gitbook/assets/image (1753).png" alt=""><figcaption></figcaption></figure>

Wireshark mostra tutti i livelli dello stack iso osi.

Nel dettaglio c'è anche il contenuto della richiesta HTTP:

<figure><img src="../../../.gitbook/assets/image (1754).png" alt=""><figcaption></figcaption></figure>

Possiamo anche vedere la porta TCP a cui è stata stabilita la connessione (una porta random per il client). Che possiamo vedere tra le connessioni attive con il comando `netstat -antp`

<figure><img src="../../../.gitbook/assets/image (1756).png" alt=""><figcaption></figcaption></figure>

<figure><img src="../../../.gitbook/assets/image (1755).png" alt=""><figcaption></figcaption></figure>

## Risposta HTTP

<figure><img src="../../../.gitbook/assets/image (1757).png" alt=""><figcaption></figcaption></figure>

