# Host & Network Penetration Testing: The Metasploit Framework (MSF)

## Metasploit Framework Overview

### Terminologia essenziale

* Interface (Interfaccia): Metodi per interagire con il Metasploit Framework.
* Module (Modulo): Frammenti di codice che eseguono un compito particolare; un esempio di modulo è l'exploit.
* Vulnerability (Vulnerabilità): Debolezza o falla in un sistema informatico o in una rete che può essere sfruttata.
* Exploit: Frammento di codice o modulo utilizzato per trarre vantaggio da una vulnerabilità all'interno di un sistema, servizio o applicazione.
* Payload: Frammento di codice consegnato al sistema target da un exploit con l'obiettivo di eseguire comandi arbitrari o fornire accesso remoto a un attaccante.
* Listener (Ascoltatore): Un'utility che resta in attesa di una connessione in entrata proveniente da un target.

### Metasploit Framework Interfaces <a href="#metasploit-framework-interfaces" id="metasploit-framework-interfaces"></a>

| MSFconsole        | Interfaccia principale "all-in-one" che fornisce accesso a tutte le funzionalità.         | La più usata nel corso e nell'esame.                               |
| ----------------- | ----------------------------------------------------------------------------------------- | ------------------------------------------------------------------ |
| MSFcli            | Utility a riga di comando per facilitare la creazione di script di automazione.           | Dismessa nel 2015; le sue funzioni sono ora incluse in MSFconsole. |
| Armitage          | GUI basata su Java che semplifica discovery, exploit e post-exploit.                      | Ottima per visualizzare graficamente gli host compromessi.         |
| Community Edition | Interfaccia GUI basata su web per semplificare discovery e identificazione vulnerabilità. | Soluzione orientata al browser.                                    |
|                   |                                                                                           |                                                                    |

### Moduli e Componenti del MSF

* **Exploit**: Sfrutta una vulnerabilità, solitamente accoppiato a un payload.
* **Payload**: Codice eseguito post-exploitation (es. una _reverse shell_ che connette il target all'attaccante).
* **Encoder**: Utilizzato per offuscare i payload ed evitare il rilevamento da parte degli antivirus (es. `shikata_ga_nai`).
* **NOPS**: Assicurano la stabilità del payload e che le dimensioni siano consistenti durante l'esecuzione.
* **Auxiliary**: Moduli per funzionalità aggiuntive come port scanning ed enumerazione (non caricano payload).

### Tipi di Payload

Quando si configura un attacco, MSF mette a disposizione due tipologie principali:

1. **Non-Staged Payload:** Inviato al sistema target "così com'è" insieme all'exploit in un unico blocco.
2. **Staged Payload:** Inviato in due parti:
   * **Stager**: Stabilisce un canale di comunicazione stabile tra attaccante e target.
   * **Stage**: I componenti del payload vero e proprio scaricati successivamente dallo stager.

#### Meterpreter Payload (Meta-Interpreter)

È il payload più avanzato di Metasploit:

* Esecuzione in memoria: Viene eseguito interamente nella memoria del target, rendendo molto difficile il rilevamento da parte delle analisi forensi.
* Funzionalità: Fornisce un interprete di comandi interattivo per navigazione nel file system, keylogging ed esecuzione di comandi di sistema.

### Struttura del File System e Percorsi

Conoscere la posizione dei moduli è utile per aggiungere script personalizzati:

* Directory di Sistema (Linux): `/usr/share/metasploit-framework/modules`.
* Directory Utente (Linux): `~/.ms4/modules` (per moduli personalizzati dell'utente, da caricare suddividendoli per sistema operativo / programma o servizio).
* Struttura generale: Il file system è organizzato in cartelle chiare come `app`, `config`, `data`, `db`, **`modules`**, `plugins`, `scripts` e `tools`.

### Fasi del Penetration Test con MSF

#### Il Penetration Testing Execution Standard (PTES)

Il PTES è una metodologia di penetration testing sviluppata da un team di professionisti della sicurezza informatica.

* Obiettivo: Offrire uno standard completo e aggiornato per l'esecuzione dei test di sicurezza.
* Roadmap: MSF può essere adottato come roadmap per comprendere le varie fasi che compongono un penetration test e come integrarsi in ognuna di ess

<figure><img src="../.gitbook/assets/image (1110).png" alt=""><figcaption></figcaption></figure>

Metasploit Framework copre l'intero ciclo di vita dell'attacco:

| Information Gathering & Enumeration | Moduli Auxiliary e integrazione con Nmap.               |
| ----------------------------------- | ------------------------------------------------------- |
| Vulnerability Scanning              | Moduli Auxiliary e Nessus.                              |
| Exploitation                        | Moduli Exploit e Payloads.                              |
| Post Exploitation                   | Meterpreter e moduli Post.                              |
| Privilege Escalation                | Moduli Post e Meterpreter.                              |
| Maintaining Persistent Access       | Moduli di Persistence e Post, Meterpreter.              |
| Clearing Tracks                     | Pulizia dei log (es. Windows Event Log) o bash history. |

