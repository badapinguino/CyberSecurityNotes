# Passive Crawling & Spidering with Burp Suite & OWASP ZAP

<figure><img src="../../../daniele-badagliacca-docs/docs/.gitbook/assets/image (3030).png" alt=""><figcaption></figcaption></figure>

<figure><img src="../../../daniele-badagliacca-docs/docs/.gitbook/assets/image (3031).png" alt=""><figcaption></figcaption></figure>

***

<figure><img src="../../../daniele-badagliacca-docs/docs/.gitbook/assets/image (3032).png" alt=""><figcaption></figcaption></figure>

## Passive crawling con Burp Suite

<figure><img src="../../../daniele-badagliacca-docs/docs/.gitbook/assets/image (3034).png" alt=""><figcaption></figcaption></figure>

Questa sezione è il passive crawler:

<figure><img src="../../../daniele-badagliacca-docs/docs/.gitbook/assets/image (3033).png" alt=""><figcaption></figcaption></figure>

Se clicchiamo sul tab Target e poi su Site map, possiamo vedere la mappa del sito che viene costruita:

<figure><img src="../../../daniele-badagliacca-docs/docs/.gitbook/assets/image (3035).png" alt=""><figcaption></figcaption></figure>

Disattiviamo l'intercept per evitare che blocchi le richieste HTTP:

<figure><img src="../../../daniele-badagliacca-docs/docs/.gitbook/assets/image (3036).png" alt=""><figcaption></figcaption></figure>

Navighiamo quindi il sito e tutte le sue pagine, provando anche a compilare i form come quelli di login, in questo modo facciamo passive crawling.

<figure><img src="../../../daniele-badagliacca-docs/docs/.gitbook/assets/image (3038).png" alt=""><figcaption></figcaption></figure>

<figure><img src="../../../daniele-badagliacca-docs/docs/.gitbook/assets/image (3037).png" alt=""><figcaption></figcaption></figure>

<figure><img src="../../../daniele-badagliacca-docs/docs/.gitbook/assets/image (3039).png" alt=""><figcaption></figcaption></figure>

<figure><img src="../../../daniele-badagliacca-docs/docs/.gitbook/assets/image (3040).png" alt=""><figcaption></figcaption></figure>

è possibile fare un New Live Task dalla Dashboard per fare un nuovo live passive crawler scan:

<figure><img src="../../../daniele-badagliacca-docs/docs/.gitbook/assets/image (3041).png" alt=""><figcaption></figcaption></figure>

## Spidering con OWASP ZAP

Menù Start -> 03 - Web Application Analysis -> owasp-zap

<figure><img src="../../../daniele-badagliacca-docs/docs/.gitbook/assets/image (3042).png" alt=""><figcaption></figcaption></figure>

<figure><img src="../../../daniele-badagliacca-docs/docs/.gitbook/assets/image (3043).png" alt=""><figcaption></figcaption></figure>

<figure><img src="../../../daniele-badagliacca-docs/docs/.gitbook/assets/image (3044).png" alt=""><figcaption></figcaption></figure>

Nella parte sotto della schermata c'è il traffico HTTP.

In alto possiamo switchare tra Safe Mode, Standard Mode, ATTACK Mode, ...

Navighiamo al sito internet target per farlo apparire tra i Sites in owasp zap. Facciamo un refresh (se abbiamo un errore fa nulla).

<figure><img src="../../../daniele-badagliacca-docs/docs/.gitbook/assets/image (3046).png" alt=""><figcaption></figcaption></figure>

Andiamo su Tools -> Spider...

<figure><img src="../../../daniele-badagliacca-docs/docs/.gitbook/assets/image (3047).png" alt=""><figcaption></figcaption></figure>

<figure><img src="../../../daniele-badagliacca-docs/docs/.gitbook/assets/image (3045).png" alt=""><figcaption></figcaption></figure>

Possiamo impostare una massima profondità:

<figure><img src="../../../daniele-badagliacca-docs/docs/.gitbook/assets/image (3048).png" alt=""><figcaption></figcaption></figure>

Avviamo lo Scan con Start Scan.

Nel traffico sotto i link verdi sono quelli interni al sito, quelli rossi sono verso l'esterno.

<figure><img src="../../../daniele-badagliacca-docs/docs/.gitbook/assets/image (3049).png" alt=""><figcaption></figcaption></figure>

Una volta raggiunto il 70% ha scansionato a sufficienza di solito.

Nel site map abbiamo trovato molte cartelle e file che prima non avevamo visto con Burp Suite.

Ad esempio un file config.inc, che possiamo aprire nel browser con un click del tasto destro del mouse.

<figure><img src="../../../daniele-badagliacca-docs/docs/.gitbook/assets/image (3050).png" alt=""><figcaption></figcaption></figure>

Abbiamo trovato anche una cartella passwords con dentro un file. Lo apriamo nel browser...

<figure><img src="../../../daniele-badagliacca-docs/docs/.gitbook/assets/image (3051).png" alt=""><figcaption></figcaption></figure>

E troviamo un file con gli account e le password:

<figure><img src="../../../daniele-badagliacca-docs/docs/.gitbook/assets/image (3052).png" alt=""><figcaption></figcaption></figure>

Vediamo anche una cartella phpmyadmin, quindi scopriamo anche il linguaggio usato.

Possiamo anche vedere tutti i file php ed eventualmente aprirli nel browser.

<figure><img src="../../../daniele-badagliacca-docs/docs/.gitbook/assets/image (3053).png" alt=""><figcaption></figcaption></figure>
