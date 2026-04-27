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

