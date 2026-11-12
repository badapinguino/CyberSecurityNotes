# HTTP Basics Lab - Part 1

<figure><img src="../../../daniele-badagliacca-docs/docs/.gitbook/assets/image (2978).png" alt=""><figcaption></figcaption></figure>

<figure><img src="../../../daniele-badagliacca-docs/docs/.gitbook/assets/image (2979).png" alt=""><figcaption></figcaption></figure>

Visitiamo la pagina web del IP target, che contiene un semplice Content Management System con anche un'area di login.

Tracciamo le richieste attraverso Wireshark.

## Avviare Wireshark

```
wireshark -i eth1
## doppio click sull'interfaccia per avviare lo sniffing
```

<figure><img src="../../../daniele-badagliacca-docs/docs/.gitbook/assets/image (2980).png" alt=""><figcaption></figcaption></figure>

<figure><img src="../../../daniele-badagliacca-docs/docs/.gitbook/assets/image (2981).png" alt=""><figcaption></figcaption></figure>

<figure><img src="../../../daniele-badagliacca-docs/docs/.gitbook/assets/image (2982).png" alt=""><figcaption></figcaption></figure>

## Cosa succede quando carico la pagina web?

<figure><img src="../../../daniele-badagliacca-docs/docs/.gitbook/assets/image (2983).png" alt=""><figcaption></figcaption></figure>

<figure><img src="../../../daniele-badagliacca-docs/docs/.gitbook/assets/image (2984).png" alt=""><figcaption></figcaption></figure>

3 way handshake del TCP per stabilire la connessione come prima cosa: SYN -> SYN/ACK -> ACK

<figure><img src="../../../daniele-badagliacca-docs/docs/.gitbook/assets/image (2985).png" alt=""><figcaption></figcaption></figure>

## Contenuto della richiesta GET / HTTP

<figure><img src="../../../daniele-badagliacca-docs/docs/.gitbook/assets/image (2986).png" alt=""><figcaption></figcaption></figure>

Wireshark mostra tutti i livelli dello stack iso osi.

Nel dettaglio c'è anche il contenuto della richiesta HTTP:

<figure><img src="../../../daniele-badagliacca-docs/docs/.gitbook/assets/image (2987).png" alt=""><figcaption></figcaption></figure>

Possiamo anche vedere la porta TCP a cui è stata stabilita la connessione (una porta random per il client). Che possiamo vedere tra le connessioni attive con il comando `netstat -antp`

<figure><img src="../../../daniele-badagliacca-docs/docs/.gitbook/assets/image (2989).png" alt=""><figcaption></figcaption></figure>

<figure><img src="../../../daniele-badagliacca-docs/docs/.gitbook/assets/image (2988).png" alt=""><figcaption></figcaption></figure>

## Risposta HTTP

<figure><img src="../../../daniele-badagliacca-docs/docs/.gitbook/assets/image (2990).png" alt=""><figcaption></figcaption></figure>

contiene il codice HTML della pagina, con anche il JS eventuale. E se è presente un foglio CSS di riferimento effettua un'altra chiamata per scaricare il foglio di stile CSS.

<figure><img src="../../../daniele-badagliacca-docs/docs/.gitbook/assets/image (98).png" alt=""><figcaption></figcaption></figure>

<figure><img src="../../../daniele-badagliacca-docs/docs/.gitbook/assets/image (99).png" alt=""><figcaption></figcaption></figure>



Con wireshark è possibile anche seguire un flusso di richieste e risposte TCP: tasto dx su una richiesta TCP come ad esempio l'handshake -> Follow -> TCP Stream.

<figure><img src="../../../daniele-badagliacca-docs/docs/.gitbook/assets/image (100).png" alt=""><figcaption></figcaption></figure>

<figure><img src="../../../daniele-badagliacca-docs/docs/.gitbook/assets/image (101).png" alt=""><figcaption></figcaption></figure>

<figure><img src="../../../daniele-badagliacca-docs/docs/.gitbook/assets/image (102).png" alt=""><figcaption></figcaption></figure>

Possiamo vedere la compressione in gzip del file css.

<figure><img src="../../../daniele-badagliacca-docs/docs/.gitbook/assets/image (103).png" alt=""><figcaption></figcaption></figure>

è possibile segnarsi il nome del file e andare su Wireshark -> File -> Export Objects -> HTTP Objects.

<figure><img src="../../../daniele-badagliacca-docs/docs/.gitbook/assets/image (104).png" alt=""><figcaption></figcaption></figure>

E possiamo scaricare i vari file che ci sono stati inviati tra cui le pagine HTTP, i file CSS e i JavaScript.

<figure><img src="../../../daniele-badagliacca-docs/docs/.gitbook/assets/image (105).png" alt=""><figcaption></figcaption></figure>

<figure><img src="../../../daniele-badagliacca-docs/docs/.gitbook/assets/image (106).png" alt=""><figcaption></figcaption></figure>
