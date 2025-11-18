# Website Footprinting With Netcraft

Enumerare più informazioni possibili passivamente su un sito.

Netcraft (sito netcraft.com)

Dà informazioni su un dominio, informazioni sulle tecnologie di un sito, name servers DNS, info sul register del dominio, ecc... Dà tutte le informazioni che abbiamo trovato prima a mano ma in maniera automatica, molto utile.

<figure><img src="../../.gitbook/assets/image (481).png" alt=""><figcaption></figcaption></figure>

<figure><img src="../../.gitbook/assets/image (482).png" alt=""><figcaption></figcaption></figure>

Esempio di risultato:

<figure><img src="../../.gitbook/assets/image (483).png" alt=""><figcaption></figcaption></figure>

<figure><img src="../../.gitbook/assets/image (485).png" alt=""><figcaption></figcaption></figure>

<figure><img src="../../.gitbook/assets/image (486).png" alt=""><figcaption></figcaption></figure>

Informazioni importanti dal punto di vista di un pentester

* Background
  * Titolo del Sito
  * Data prima apparizione del sito
  * Descrizione
  * Lingua utilizzata
* Network
  * DNS Nameserver
  * Dominio
  * Owner e azienda di hosting
  * Nome dell'organizzazione
  * IP
  * Top level domain
  * DNS Sevurity Extensions enabled or not
* IP
  * Nome di ogni blocco IP
* SSL/TLS certificate information
  * Scadenza
  * Chi lo ha issued (rilasciato)
  * Se il sito è vulnerabile a SSLv3/POODLE o a Hearthbleed
  * SSL Certificate Chain
* L'history di chi ha hostato il sito (dove è stato hostato)
* SPF (Sender Policy Framework)
  * Descrive chi può inviare delle mail per conto del dominio.
* Web Tracker (importante)
  * Che analytics e tracker sono attivi sul sito (ex. Google Analytics, Wordpress Analytics)
* HTTP Accelerator
  * Fornisce informazioni sul proxy server utilizzato
* Server Side
  * Fornisce informazioni sui linguaggi utilizzati e web-technology
* Client Side
  * Frameworks usati (es. JQuery)&#x20;
  * linguaggi (es. JS)
* PHP Application
  * es. Wordpress
* RSS abilitato o no
* HTML5&#x20;
  * se usato per mobile optimization oppure no
