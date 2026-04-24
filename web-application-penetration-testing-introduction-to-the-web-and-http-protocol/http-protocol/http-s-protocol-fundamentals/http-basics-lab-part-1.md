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

contiene il codice HTML della pagina, con anche il JS eventuale. E se è presente un foglio CSS di riferimento effettua un'altra chiamata per scaricare il foglio di stile CSS.

<figure><img src="../../../.gitbook/assets/image (18).png" alt=""><figcaption></figcaption></figure>

<figure><img src="../../../.gitbook/assets/image (1) (1) (1).png" alt=""><figcaption></figcaption></figure>



Con wireshark è possibile anche seguire un flusso di richieste e risposte TCP: tasto dx su una richiesta TCP come ad esempio l'handshake -> Follow -> TCP Stream.

<figure><img src="../../../.gitbook/assets/image (2) (1) (1).png" alt=""><figcaption></figcaption></figure>

<figure><img src="../../../.gitbook/assets/image (3) (1) (1).png" alt=""><figcaption></figcaption></figure>

<figure><img src="../../../.gitbook/assets/image (4) (1) (1).png" alt=""><figcaption></figcaption></figure>

Possiamo vedere la compressione in gzip del file css.

<figure><img src="../../../.gitbook/assets/image (5) (1) (1).png" alt=""><figcaption></figcaption></figure>

è possibile segnarsi il nome del file e andare su Wireshark -> File -> Export Objects -> HTTP Objects.

<figure><img src="../../../.gitbook/assets/image (6) (1).png" alt=""><figcaption></figcaption></figure>

E possiamo scaricare i vari file che ci sono stati inviati tra cui le pagine HTTP, i file CSS e i JavaScript.

<figure><img src="../../../.gitbook/assets/image (7) (1).png" alt=""><figcaption></figcaption></figure>

<figure><img src="../../../.gitbook/assets/image (8) (1).png" alt=""><figcaption></figcaption></figure>
