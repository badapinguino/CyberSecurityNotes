# Information Gathering

<figure><img src="../.gitbook/assets/image (127).png" alt=""><figcaption></figcaption></figure>

## Passive Information Gathering

### Sito web & footprinting

```
host <dominio.com>  //Trovare gli IP di un dominio
```

#### Verificare i seguenti file via web browser:

* **robots.txt**: indica ai motori di ricerca quali pagine mostrare e quali no
* **sitemap.xml o sitemap\_index.xml**: Mostra ai motori di ricerca l'ordine in cui mostrare le pagine

#### Conoscere le tecnologie di un sito, estensioni del browser:

* **builtwith**
* **wappalyzer**

```
whatweb <dominio.com>  // Mostra le tecnologie usate da un sito
```

#### Scaricare un sito web:

* **HTTTrack**: sito web

```
webhttrack <sito> // Scaricare un sito web
```

```
httrack <http://target.sito> -O <cartella> // Scarica un sito in una cartella
```

#### Whois enumeration

Utile per conoscere informazioni sul proprietario di una risorsa internet (sito, dns, ip)

<pre><code><strong>whois &#x3C;dominio.com> // A chi è registrato un dominio, rinnovo, scadenza, mail, name server, DNSSEC.
</strong></code></pre>

Alternativa sito web **who.is**

#### Netcraft: Footprinting di un sito

Andare alla pagine Resources "What's that site running" Dà informazioni su un dominio, informazioni sulle tecnologie di un sito, name servers DNS, info sul register del dominio, ecc... Dà tutte le informazioni che abbiamo trovato prima a mano ma in maniera automatica.

### DNS Recon

Identificare i record associati ad un dominio DNS

```
dnsrecon -d <nomedominio.com>
```

**DNS Dumpster**: Sito web analogo al comando, con qualche info in più

### WAF Detection: wafw00f

```
wafw00f <dominio.org>

wafw00f <dominio.org> -a  // Testa tutti i WAF che supporta, senza fermarsi al primo più probabile
```

### Subdomain enumeration: Sublist3r

Trovare tutti i sottodomini passivamente attraverso le info disponibili sui motori di ricerca

```
sublist3r -d <dominio.org>
sublist3r -d <dominio.org> -e google,yahoo,bing // specifica i motori di ricerca da usare
```

### Google Hacking

* site:ine.com - Limitare risultati a un sito
* inurl:admin - L'url deve contenere una data parola
* site:\*.ine.com - Solo sottodomini del sito
* intitle:admin - Filtra parole del titolo
* filetype:xlsx - Filtra output per tipo file
* intitle:index of - Cerchiamo la pagina di indice con tutte le cartelle che potrebbe essere erroneamente esposta
* cache:ine.com - Per caricare un sito vecchio in cache (oppure **Wayback Machine**)
* inurl:auth\_user\_file.txt - Per cercare un file particolare con info di autenticazione utenti.
* inurl:passwd.txt - Per cercare eventuali file passwd esposti erroneamente

Sito utile: Exploit Database Google Hacking Database. Contiene query utili al nostro scopo.

### Email harvesting: theHarvester

Programma su GitHub usato per trovare mail, nome, IP, url e varie informazioni di una azienda.

Bisogna selezionare le fonti da cui fargli cercare le info

```
theHarvester -d <azienda o dominio> -b <duckduckgo,baidu,bing,yahoo,urlscan...>
```

### Leaked Password Database

Possiamo utilizzare haveibeenpwned.com per trovare password di dipendenti coinvolti in data breach.

## Active Information Gathering

### DNS Zone Transfer

<figure><img src="../.gitbook/assets/image (13) (1) (1) (1) (1) (1) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

<figure><img src="../.gitbook/assets/image (14) (1) (1) (1) (1) (1) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

Possiamo usare due strumenti per trovare se ci sono dei server DNS attivi con domini diversi rispetto al dominio cercato (zone transfer):

* **dnsdumpster.com** (sito)
* **dnsrecon** (comando)
* **dnsenum** (comando)
* **dig** (comando)
* **fierce** (comando)

```
dnsrecon -d <dominio es. zonetransfer.me>
```

```
dnsenum <dominio> //questo tool fa molte azioni e non si riesce bene a controllarle
```

```
dig axfr @<name server NS1> <dominio> 
// axfr è lo switch per fargli provare il zone transfer, poi specifichiamo il name server e infine il nome dominio.
```

<pre><code><strong>fierce -dns &#x3C;dominio> 
</strong><strong>// Trova tramite DNS dei range di indirizzi IP, oltre a zone transfer
</strong></code></pre>

### Nmap: Host Discovery

Per trovare gli host in una rete (facendo ping scan/sweep)

```
sudo nmap -sn <IP>/24  // -sn solo per vedere se host online. scan che usa anche ICMP, quindi fa rumore
nmap -sn <IP>/24 --send-ip // Non esegue degli ARP per ogni IP
nmap -sn -PS1-1000 <IP> // TCP SYN Ping alle porte 1-1000
nmap -sn -PA <IP> // TCP ACK Ping, porta 80 di default
nmap -sn -PE <IP> --send-ip // ICMP ping scan, senza --send-ip non sempre parte 
```

Metodologie consigliate per host discovery con Nmap:

```
nmap -sn -v -T4 <IP> // -v ci dà più info e capiamo come mai nmap è arrivato a quel risultato
nmap -sn -PS21,22,25,80,445,3389,8080 -T4 <IP> // SYN Scan su le porte più comuni
nmap -sn -PS21,22,25,80,445,3389,8080 -PU137,138 -T4 <IP> // Aggiungiamo le porte UDP relative alle porte NETBIOS, per PC Windows
```

Netdiscover: alternativa per scoprire IP sulla rete tramite protocollo ARP

```
netdiscover -i eth0 -r <IP>/24
```

Ping: fare ping su tutta la rete per vedere gli host attivi:

```
ping -b -c 1 <IP> // -b significa broadcast
fping -a -g <IP>/24 //-g generate target list // -S imposta l'IP sorgente
```

### Nmap: Port Scanning

Scansione porte aperte su un IP o una rete

#### Scan completo con script per tutte le porte&#x20;

```
nmap -T4 -A -p- <IP>
```

#### Fast scanning

Scannerizza le prime 100 porte più comuni (-F) e skippa l'host discovery (-Pn)

```
nmap -Pn -F <IP>  // si può aggiungere -sS per fare sempre il SYN scan
```

#### UDP scan

```
nmap -sU <IP> // scan generica delle porte UDP, delle 1000 porte più comuni
nmap -Pn -sU -p53,137,138,139 // -Pn nessun test che sia online prima di cercare le porte
```

#### Vari flag usati:

* -Pn: fa verifica host attivi guardando le porte invece che usare ICMP (ping)
* -p-: scansiona tutte le porte
* -p80 oppure -p0,443,3389: scansiona porte specifiche
* -F: 100 porte più usate
* -sU: UDP scan
* -v: verbose per vedere risultati di tutte le porte
* -sV: service detection, identifica i servizi running su ogni porta
* -O: Operating System detection, identifica l'SO
* -sC: Default script scan, viene eseguita una serie di script di default per identificare meglio le versioni dei servizi aprti
* -A: Aggressive scan, praticamente corrisponde a -sV -O -sC + traceroute
* -T0 -T4 -T5: Regola la velocità dello scan da paranoid (0) a insane (5)
* -oN oppure -oX: Salva su file l'output (txt o xml)
* -sT: TCP connect scan, esegue il 3-way handshake completo
* -sS: SYN scan o stealth scan, non lascia log di connessione perché non completa 3-way handshake
* -sA: ACK scan, utile per vedere presenza di firewall, indica se porte filtrate o non filtrate

### Nmap Scripting Engine

#### Lista degli script

```
ls -al /usr/share/nmap/scripts
```

#### Scan con script di default (-sC)

```
nmap -sS -sV -sC -p- -T4 <IP>  // -sC default script scan
```

#### Scan con esecuzione di uno script specifico

```
nmap -sS -sV --script=mongodb-info -p- -T4 <IP>
```

### Firewall detection

#### ACK scan per vedere se porte filtrate da firewall

```
nmap -Pn -sA <IP>
```

### IDS Evasion

#### Frammentazione pacchetti

Opzione -f per frammentare i pacchetti, opzione -mtu per impostare la dimensione massima dei frammenti

```
nmap -Pn -sS -sV -f <IP>  // -f per frammentazione dei pacchetti
nmap -Pn -sS -sV -f --mtu 8 <IP>  // massima dimensione dei frammenti è 8 byte
```

#### Data Length

è possibile specificare una lunghezza dei singoli pacchetti (aggiungendo byte randomici) con l'opzione ---data-length \<numero byte>

#### Decoy IP sorgente

è possibile specificare che i pacchetti partano da un IP sorgente differente:

```
nmap -Pn -sS -sV -D <IP1>,<IP2> <IP dest> // -D Sembra che gli scan arrivino da questi due IP
```

#### Specificare porta sorgente

Opzione -g \<numero porta> specifica la porta sorgente

```
nmap -Pn -sS -sV -g <PORT> <IP dest>  // -g specifica la porta sorgente
```

### Gestire i tempi per gli scan nmap

#### Timing templates

Usare sneaky per evitare di essere individuati da un IDS.&#x20;

* T0: paranoid
* T1: sneaky - sembra quasi traffico normale talmente è frammentato l'invio dei pacchetti
* T2: polite
* T3: normal
* T4: aggressive
* T5: insane

#### Host timeout

\--host-timeout < tempo in s,m,h> utile quando ci sono diversi host scansionati come in host discovery, dopo un tot di tempo passa all'host successivo

```
nmap --host-timeout 30s <IP>/24
```

#### Scan delay

L'opzione serve a impostare un ritardo tra ogni pacchetto inviato

Per essere molto stealthy consiglia di impostarlo attorno ai 50 secondi.

```
nmap --scan-delay 15s <IP>
```

### Nmap Output Formats

* oN: output normale come da riga di comando, va bene per un txt
* oX: XML che è utile per essere dato in input a Metasploit Framework Database (si può fare un workspace per ogni pentest fatto)
* oS: script kiddie
* oG: formato utile se si vuole usare grep
* oA: oN, oX e oG assieme
* -v: verbose
* \--reason: specifica perché una porta è nello stato indicato

#### Caricare file xml nel DB metasploit

1. Avviare il DB metasploit: service postgresql start
2. Entrare in metasploit: msfconsole
3. Creare un nuovo workspace: workspace -a pentest\_1
4. Caricare il file XML: db\_import nmap\_xml.xml
5. Verificare che sia caricato con i comandi: hosts e services

## Enumeration

## Quick tips

### Trovare un file flag

```
find / -name "flag"
```
