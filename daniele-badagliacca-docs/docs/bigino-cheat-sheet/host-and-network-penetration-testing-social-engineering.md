# Host & Network Penetration Testing: Social Engineering

## Introduction to Social Engineering

L'ingegneria sociale è una tecnica di post-exploitation (e spesso di accesso iniziale) che non si basa su vulnerabilità tecniche del software, ma sullo sfruttamento del fattore umano. È definita come l'atto di manipolare le persone affinché compiano azioni o divulghino informazioni riservate.

### Panoramica delle Tecniche

Le slide identificano diverse metodologie utilizzate dagli attaccanti per interagire con i target:

| **Tecnica**    | **Descrizione Dettagliata**                                                                                                                                |
| -------------- | ---------------------------------------------------------------------------------------------------------------------------------------------------------- |
| Phishing       | L'invio di messaggi fraudolenti (solitamente email) che sembrano provenire da fonti affidabili per rubare dati sensibili.                                  |
| Spear Phishing | Una variante mirata del phishing. L'attacco è progettato per un individuo, un gruppo o un'organizzazione specifica dopo una fase di raccolta informazioni. |
| Vishing        | Phishing vocale. L'attaccante utilizza chiamate telefoniche o messaggi vocali per ingannare la vittima.                                                    |
| Smishing       | Phishing tramite SMS. Utilizza messaggi di testo per indurre l'utente a cliccare su link malevoli.                                                         |
| Pretexting     | La creazione di uno scenario inventato per indurre la vittima a fornire informazioni o eseguire azioni.                                                    |
| Baiting        | Sfrutta la curiosità o l'avidità offrendo qualcosa di gratuito (es. malware camuffato da software utile) per infettare un sistema.                         |
| Tailgating     | Un attacco fisico in cui una persona non autorizzata segue un dipendente autorizzato in un'area riservata.                                                 |

### Il Ciclo di Attacco dello Spear Phishing

Lo Spear Phishing segue un processo strutturato in cinque fasi distinte:

1. Planning & Reconnaissance: L'attaccante identifica i target e raccoglie informazioni (nomi, ruoli, email, interessi) tramite social media o siti aziendali.
2. Message Crafting: Creazione di un messaggio altamente personalizzato che sembri provenire da una fonte interna o fidata (es. HR, IT, Manager).
3. Delivery: Il messaggio viene inviato al target tramite email, social network o altri canali di messaggistica.
4. Deception & Manipulation: La vittima viene ingannata dal contenuto e spinta a interagire con l'elemento malevolo (link o allegato).
5. Exploitation: Una volta eseguita l'azione, l'attaccante ottiene l'accesso iniziale, ruba le credenziali o installa un payload sul sistema.

***

## Pretexting

Il Pretexting è una tecnica di ingegneria sociale che coinvolge la creazione di una situazione inventata (un pretesto) per indurre una vittima a fornire informazioni o eseguire azioni che normalmente non farebbe.

### Caratteristiche Fondamentali del Pretexting

Secondo le slide, un attacco di pretexting efficace si basa su quattro pilastri:

* Establishing Trust (Rapport): L'attaccante costruisce una relazione con la vittima. Questo avviene spesso tramite il "mirroring", ovvero l'adattamento del proprio linguaggio, tono e comportamento a quello del target per creare familiarità.
* Manipulating Emotions: Gli attaccanti sfruttano leve psicologiche come la curiosità, la paura, l'urgenza o la simpatia. Queste emozioni offuscano il giudizio della vittima, rendendola più incline a collaborare.
* Maintaining Consistency: Per non far fallire l'inganno, l'attaccante deve mantenere la storia e il personaggio in modo coerente durante tutta l'interazione.
* Information Gathering: L'obiettivo finale è estrarre dati sensibili o ottenere accessi fisici/logici attraverso la manipolazione del contesto creato.

### Scenari ed Esempi Reali

Le slide evidenziano tre scenari tipici utilizzati durante gli engagement:

#### 1. Tech Support Scam

L'attaccante finge di essere un membro del supporto tecnico (interno o di un fornitore noto). Sostiene che ci sia un problema urgente (es. "il tuo computer sta inviando spam" o "abbiamo rilevato un virus") per convincere l'utente a fornire la password o a installare un software di controllo remoto.

#### 2. Job Interview Scam

Un attaccante si finge un recruiter o un datore di lavoro. Sfruttando il desiderio di carriera della vittima, richiede documenti personali, coordinate bancarie o informazioni aziendali riservate sotto il pretesto di un processo di assunzione.

#### 3. Emergency Situations

Viene creato un senso di urgenza estremo (es. "il server sta crashando", "c'è una fuga di dati in corso"). La vittima, presa dal panico o dal desiderio di aiutare a risolvere l'emergenza, salta le normali procedure di sicurezza e fornisce accessi o codici MFA.

### Impatto e Difesa

L'impatto del successo di questi attacchi può essere devastante, portando a:

* Violazioni massicce di dati (Data Breaches).
* Perdite finanziarie dirette.
* Danni permanenti alla reputazione aziendale.

Mitigazione: L'unica difesa efficace è la Security Awareness Training. I dipendenti devono essere formati a riconoscere i segnali di manipolazione, a verificare l'identità del richiedente tramite canali indipendenti e a non agire mai sotto pressione emotiva o urgenza ingiustificata.

#### Risorse Esterne

Per la simulazione di questi attacchi, le slide suggeriscono di consultare il repository:

* `L4bF0x/PhishingPretexts` (GitHub) - Una libreria di pretesti reali pronti all'uso per campagne di phishing.

***

## 🎣 Phishing con Gophish: Guida Completa al Laboratorio

Gophish è un framework di phishing open-source progettato per rendere semplice il setup di campagne di sensibilizzazione e test di penetrazione. In questo laboratorio, l'obiettivo è configurare l'intero stack: dal server di invio alla pagina di cattura credenziali.

### 1. Setup e Configurazione del Server

#### Avvio del Servizio

1. Eseguire `gophish.exe` sul desktop. Si aprirà un terminale che mostra lo stato del server.
2. Endpoint di Amministrazione: Di default, Gophish ascolta su `https://127.0.0.1:3333`.
3. Accesso: Aprire Firefox, ignorare l'avviso di sicurezza (SSL non valido) e loggarsi con:
   * User: `admin`
   * Pass: `phishingpasswd`

{% hint style="info" %}
Hardening & Performance: In contesti reali, modifica `config.json` per cambiare l'IP di ascolto e rimuovi i riferimenti a `fonts.googleapis.com` nei file HTML (`base.html` e `login.html`) per evitare caricamenti lenti o leak di informazioni verso Google.
{% endhint %}

### 2. Configurazione dei Componenti della Campagna

Il successo di una campagna dipende dalla corretta impostazione di quattro moduli fondamentali presenti nella barra laterale:

#### A. Sending Profile (Il Mittente)

Definisce come le email verranno inviate (il server SMTP).

* Host: `localhost:25` (Verifica con `netstat -antp` se la porta è attiva).
* Username: `red@demo.ine.local`
* Password: `penetrationtesting`
* Test: Usa il tasto Send Test Email verso `victim@demo.ine.local` e verifica la ricezione su Thunderbird.

#### B. Landing Page (La Trappola)

È la pagina web su cui l'utente atterra dopo il click.

* Import Site: Usa questa funzione per clonare un sito esistente. Nel lab, puntiamo a `http://localhost:8080`.
* Credential Harvesting: Assicurati di spuntare Capture Submitted Data e Capture Passwords.
* Redirect: Imposta un reindirizzamento al sito reale dopo l'invio dei dati per non destare sospetti.

#### C. Email Template (Il Messaggio)

Il corpo del messaggio che attira la vittima.

* Nel laboratorio, copia il contenuto dal file `Password Reset Email.txt` presente sul Desktop.
* Puoi usare variabili come `{{.URL}}` per inserire automaticamente il link univoco di tracciamento di Gophish.

#### D. Users & Groups (I Target)

L'elenco dei "bersagli".

* Utilizza la funzione Bulk Import caricando il file `targets.csv` dal Desktop. Questo file contiene nomi ed email degli utenti individuati durante la fase di Information Gathering.

### 3. Lancio e Monitoraggio della Campagna

Una volta pronti i moduli, si procede alla creazione della Campaign:

1. Nome: Assegna un nome identificativo.
2. Associazione: Seleziona il Template, la Landing Page, il Sending Profile e il Gruppo creati in precedenza.
3. URL: Inserisci l'indirizzo del listener di Gophish (es. `http://<IP_ATTACCANTE>` o `http://localhost`). Questo URL verrà usato per generare i link nelle email.
4. Launch: Clicca su Launch Campaign.

#### Analisi dei Risultati

Dalla Dashboard di Gophish puoi monitorare in tempo reale:

* Email Sent: Messaggi consegnati con successo.
* Clicked Link: Quanti utenti hanno cliccato.
* Submitted Data: Chi ha inserito effettivamente le proprie credenziali.

***

### 📝 Note Tecniche per il Pentester

* Bind vs Reverse: Se esegui un attacco su una rete interna tramite pivoting, ricorda che Gophish deve essere raggiungibile dalla vittima.
* Cleanup: Una volta terminato il test, cancella la campagna e i database (`gophish.db`) se contengono password reali catturate per errore.
* Certificati: In produzione, usa sempre `use_tls: true` con certificati validi (es. Let's Encrypt) per evitare che i browser blocchino la tua Landing Page.
