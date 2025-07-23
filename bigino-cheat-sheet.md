# Bigino - Cheat Sheet

## Fasi del Penetration Testing

<figure><img src=".gitbook/assets/image (117).png" alt=""><figcaption></figcaption></figure>

## Information Gathering

<figure><img src=".gitbook/assets/image (119).png" alt=""><figcaption></figcaption></figure>

### Passive Information Gathering

#### Sito web & footprinting

```
host <dominio.com>  //Trovare gli IP di un dominio
```

Verificare i seguenti file via web browser:

* robots.txt: indica ai motori di ricerca quali pagine mostrare e quali no
* sitemap.xml o sitemap\_index.xml: Mostra ai motori di ricerca l'ordine in cui mostrare le pagine

Conoscere le tecnologie di un sito, estensioni del browser:

* builtwith
* wappalyzer

```
whatweb <dominio.com>  // Mostra le tecnologie usate da un sito
```

Scaricare un sito web:

* HTTTrack: sito web

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

#### DNS Recon

Identificare i record associati ad un dominio DNS

```
dnsrecon -d <nomedominio.com>
```

DNS Dumpster: Sito web analogo al comando, con qualche info in più

#### WAF Detection: wafw00f

```
wafw00f <dominio.org>

wafw00f <dominio.org> -a  // Testa tutti i WAF che supporta, senza fermarsi al primo più probabile
```

#### Subdomain enumeration: Sublist3r

Trovare tutti i sottodomini passivamente attraverso le info disponibili sui motori di ricerca

```
sublist3r -d <dominio.org>
sublist3r -d <dominio.org> -e google,yahoo,bing // specifica i motori di ricerca da usare
```

#### Google Hacking

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

#### Email harvesting: theHarvester

Programma su GitHub usato per trovare mail, nome, IP, url e varie informazioni di una azienda.

Bisogna selezionare le fonti da cui fargli cercare le info

```
theHarvester -d <azienda o dominio> -b <duckduckgo,baidu,bing,yahoo,urlscan...>
```

#### Leaked Password Database

Possiamo utilizzare haveibeenpwned.com per trovare password di dipendenti coinvolti in data breach.

### Active Information Gathering

#### DNS Zone Transfer

<figure><img src=".gitbook/assets/image (13).png" alt=""><figcaption></figcaption></figure>

<figure><img src=".gitbook/assets/image (14).png" alt=""><figcaption></figcaption></figure>

Possiamo usare due strumenti per trovare se ci sono dei server DNS attivi con domini diversi rispetto al dominio cercato (zone transfer):

* dnsdumpster.com (sito)
* dnsrecon (comando)
* dnsenum (comando)
* dig (comando)
* fierce (comando)

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

#### Nmap: Host Discovery

Per trovare gli host in una rete (facendo ping scan/sweep)

```
sudo nmap -sn <IP>/24
```

Netdiscover: alternativa per scoprire IP sulla rete tramite protocollo ARP

```
netdiscover -i eth0 -r <IP>/24
```

#### Nmap: Port Scanning

Scansione porte aperte su in IP o una rete

```
nmap -T4 -A -p- <IP>
```

Vari flag usati:

* -Pn: fa verifica host attivi guardando le porte invece che usare ICMP (ping)
* -p-: scansiona tutte le porte
* -p80 oppure -p0,443,3389: scansiona porte specifiche
* -F: 100 porte più usate
* -sU: UDP scan
* -v: verbose per vedere risultati di tutte le porte
* -sV: service detection, identifica i servizi running su ogni porta
* -O: Operating System detection, identifica l'SO
* -sC: Default script scan, viene eseguita una serie di script di default per identificare meglio le versioni dei servizi aprti
* -A: Aggressive scan, praticamente corrisponde a -sV -O -sC
* -T0 -T4 -T5: Regola la velocità dello scan da paranoid (0) a insane (5)
* -oN oppure -oX: Salva su file l'output (txt o xml)
