# Paperwork

## Documentazione Pentest: Paperwork

### 1. Ricognizione e Accesso Iniziale (PJL Path Traversal)

* Vettore: Servizio di stampa LP/PJL in ascolto sulla porta TCP `9100`.
* Vulnerabilità: Mancanza di sanitizzazione dei path nel comando PJL `FSDOWNLOAD`, che permetteva un'evasione dalla directory virtuale (`0:/`) tramite path traversal.
* Sfruttamento: Tramite uno script Python custom, è stato inviato il comando di download file sfruttando una sequenza di risalita (`0:/../../../home/archivist/.ssh/authorized_keys`) per iniettare la chiave pubblica SSH direttamente nella home dell'utente `archivist`.
* Risultato: Accesso SSH stabilito con successo come utente `archivist`.

### 2. Enumerazione Post-Accesso (`archivist`)

* Verifiche iniziali:
  * `sudo -l`: Nessun privilegio sudo disponibile per l'utente corrente.
  * `find / -perm -4000`: Nessun binario SUID anomalo utile per l'escalation immediata.
  * Processi attivi (`ps aux` / `ss -tulpn`): Individuazione del demone custom `/usr/bin/paperwork-daemon` in esecuzione come `root`, in ascolto su un socket UNIX (`/run/paperwork/mgmt.sock`).

### 3. Privilege Escalation a Root (Unix Socket File Descriptor Leaking)

* Analisi del demone (`/usr/bin/paperwork-daemon`):
  * Il demone monitora il file di log dei comandi di stampa (`/home/archivist/printer/logs/commands.log`).
  * Se rileva parole chiave sospette come `FSDOWNLOAD`, `FSQUERY` o `FSUPLOAD`, attiva la funzione di `lockdown`.
  * Durante il lockdown, il demone invia un array di file descriptor ancillari (`SCM_RIGHTS`) tramite il socket UNIX al client connesso. Tra questi file descriptor è presente `admin_fd`, che punta al file protetto di configurazione `/etc/paperwork/admin_pins.conf` aperto con i privilegi di root.
* Fasi dello Exploit:
  1. Scrittura del trigger (`FSDOWNLOAD`) all'interno del file di log dei comandi per simulare un evento di sicurezza.
  2. Connessione al socket UNIX `/run/paperwork/mgmt.sock` tramite uno script Python.
  3. Ricezione del messaggio di alert e estrazione dei file descriptor passati via ancillary data (`SCM_RIGHTS`).
  4. Lettura diretta dal file descriptor privilegiato per estrarre il parametro `ADMIN_PASSWORD`.
* Chiusura del cerchio:
  * Credenziali estratte: `ADMIN_PASSWORD=ApparelMortuaryCedar22`
  * Accesso finale come `root` effettuato tramite SSH sfruttando la password amministrativa recuperata.

***

***

***

## RAPPORTO TECNICO DI PENETRATION TESTING

**Target:** Host `Paperwork` (IP: `10.129.204.168`)

**Data Assessment:** Novembre 2026

**Classificazione:** Riservato / Post-Assessment

### 1. Executive Summary

Nel corso dell'assessment di sicurezza mirato sull'infrastruttura **Paperwork**, il team di valutazione ha condotto un'analisi approfondita della superficie d'attacco esposta sia a livello di rete che localmente sul sistema operativo. L'obiettivo dell'audit era determinare la resilienza dei servizi applicativi e dei meccanismi di controllo degli accessi.

L'attività ha evidenziato **vulnerabilità critiche** sia nel perimetro esterno (servizi di stampa e protocolli PJL) sia nella logica di interprocess communication (IPC) interna. Sfruttando una catena d'attacco composta da un'vulnerabilità di _Path Traversal_ (per l'accesso iniziale) e da un difetto di progettazione nella gestione dei descrittori di file su socket UNIX (per la privilege escalation), è stato possibile compromettere integralmente l'host ottenendo i privilegi di root (`UID 0`).

### 2. Metodologia di Assessment

L'assessment è stato condotto seguendo una metodologia strutturata in fasi:

1. **Information Gathering & Reconnaissance:** Scansione delle porte di rete, identificazione dei servizi attivi (`CUPS/LPD`, Print Server PJL su porta `9100`, web application).
2. **Initial Access (Vulnerability Exploitation):** Analisi dei servizi di stampa e individuazione di difetti di validazione dei path.
3. **Local Enumeration:** Analisi della configurazione di sistema, dei cron job, dei permessi dei file, dei socket di dominio UNIX e dei processi in esecuzione.
4. **Privilege Escalation:** Sfruttamento di una falla logica nel demone di sistema (`paperwork-daemon`) per il leak di credenziali sensibili e file descriptor privilegiati.

### 3. Risultanze Tecniche e Dettaglio delle Vulnerabilità

#### VULN-01: Remote Code / Access Execution tramite PJL Path Traversal

* **Severità:** Critica (CVSS 9.8)
* **Componente Interessato:** Servizio Print Server / PJL (`TCP/9100`)
* **CVSS Vector:** `CVSS:3.1/AV:N/AC:L/PR:N/UI:N/S:U/C:H/I:H/A:H`

**Descrizione del Problema**

Il servizio in ascolto sulla porta 9100 implementa un emulatore basato su PJL (Printer Job Language) scritto in Python (`jetdirect.py`). Durante la gestione dei comandi di trasferimento file (es. `@PJL FSDOWNLOAD`), l'applicazione accetta stringhe di percorso senza applicare una corretta sanitizzazione o canonicalizzazione (`os.path.normpath` / `os.path.realpath`).

**Sfruttamento (PoC)**

Attraverso l'invio di un payload appositamente strutturato contenente sequenze di directory traversal (`0:/../../../`), è stato possibile evadere la root virtuale del servizio di stampa e scrivere direttamente all'interno del file system del sistema operativo:

```
name = b'0:/../../../home/archivist/.ssh/authorized_keys'
```

L'attaccante ha iniettato la propria chiave pubblica SSH nel file `authorized_keys` dell'utente non privilegiato `archivist`, ottenendo un accesso SSH pulito e persistente sulla macchina.

#### VULN-02: Privilege Escalation tramite Unix Socket File Descriptor Leaking (`SCM_RIGHTS`)

* **Severità:** Critica (CVSS 8.8)
* **Componente Interessato:** `/usr/bin/paperwork-daemon` & `/run/paperwork/mgmt.sock`
* **CVSS Vector:** `CVSS:3.1/AV:L/AC:L/PR:L/UI:N/S:U/C:H/I:H/A:H`

**Descrizione del Problema**

Il sistema esegue in background un demone di monitoraggio (`paperwork-daemon`) con i privilegi di `root`, il quale espone un socket di dominio UNIX (`/run/paperwork/mgmt.sock`) accessibile ai membri del gruppo di controllo (incluso l'utente `archivist`).

La logica applicativa del demone prevede che, qualora venga rilevata un'attività sospetta all'interno del file di log dei comandi (`/home/archivist/printer/logs/commands.log` tramite trigger come `FSDOWNLOAD`), il demone attivi una procedura di "lockdown". In tale stato, il processo invia un pacchetto di notifica ai client connessi sfruttando i messaggi ancillari (`SCM_RIGHTS`), trasferendo direttamente i file descriptor aperti internamente dal processo root. Tra questi file descriptor è incluso `admin_fd`, associato al file di configurazione protetto `/etc/paperwork/admin_pins.conf`.

**Sfruttamento (PoC)**

1.  L'utente `archivist` ha simulato un evento di sicurezza scrivendo la stringa trigger `FSDOWNLOAD` nel file di log dei comandi:

    ```
    echo "FSDOWNLOAD" > /home/archivist/printer/logs/commands.log
    ```
2.  Tramite uno script client Python personalizzato, è stata effettuata la connessione al socket UNIX `/run/paperwork/mgmt.sock` e intercettati i file descriptor ancillari ricevuti:

    ```
    msg, ancdata, flags, addr = s.recvmsg(1024, ancbuf_size)
    ```
3.  Estratto il descrittore di file corrispondente ad `admin_fd`, lo script ha letto direttamente il contenuto del file riservato, recuperando la password amministrativa in chiaro:

    ```
    ADMIN_PASSWORD=ApparelMortuaryCedar22
    ```
4. Utilizzando la credenziale ottenuta, è stato effettuato l'accesso amministrativo tramite SSH come utente `root`.

### 4. Analisi di Impatto

La combinazione di queste vulnerabilità ha permesso un completo compromesso della risorsa (Full System Compromise):

* **Confidenzialità:** Violata integralmente (accesso a dati sensibili, file di configurazione di root, chiavi private/pubbliche e flag di sistema).
* **Integrità:** Violata (possibilità di alterare file di sistema, configurazioni di avvio e schedulazioni).
* **Disponibilità:** A rischio in caso di interruzione dolosa dei servizi di stampa o dei demoni di gestione.

### 5. Raccomandazioni di Sicurezza e Remediation

Per mitigare le problematiche riscontrate, si raccomanda l'applicazione dei seguenti interventi correttivi:

1. **Sanitizzazione e Canonicalizzazione dei Path (VULN-01):**
   * Implementare rigorosi controlli di convalida sui percorsi file accettati dai comandi PJL.
   * Utilizzare funzioni di libreria sicure per verificare che il percorso risolto rimanga confinato all'interno della directory di lavoro designata (es. `os.path.realpath` confrontato con la base path autorizzata).
2. **Hardening dei Socket IPC e dei Permessi (VULN-02):**
   * Rivedere la logica di condivisione dei file descriptor tramite `SCM_RIGHTS`. Evitare assolutamente di esporre descrittori di file privilegiati (come file di configurazione di root) a utenti o processi non privilegiati tramite socket di IPC locali.
   * Applicare il principio del minimo privilegio, suddividendo i componenti del demone in sottoprocessi con privilegi ristretti e separati.
3. **Monitoraggio e Controllo degli Accessi:**
   * Introdurre un sistema di logging centralizzato e protetto (es. integrazione con SIEM o auditd) per tracciare anomalie di accesso ai socket locali e tentativi di manipolazione dei log di stampa.
