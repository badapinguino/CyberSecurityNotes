# Nmap Scripting Engine (NSE)

<figure><img src="../../../.gitbook/assets/image (557).png" alt=""><figcaption></figcaption></figure>

Continuiamo da dove abbiamo lasciato, vorremmo usare l'nmap scripting engine per identificare l'OS.

NSE è un motore che mette a disposizione nmap per poterci creare degli script.

Per trovare tutti gli script messi a disposizione da NSE possiamo cercare in una cartella:

```
ls -al /usr/share/nmap/scripts
```

<figure><img src="../../../.gitbook/assets/image (558).png" alt=""><figcaption></figcaption></figure>

Utilizzano il linguaggio Lua per essere programmati.

Possiamo cercare script rilevanti usando grep, ad es.:

<figure><img src="../../../.gitbook/assets/image (559).png" alt=""><figcaption></figcaption></figure>

Questi script sono organizzati nelle seguenti categorie:

* Auth: relativi ad autenticazione e credenziali
* Broadcast: usati per scoprire dispositivi sulla rete con discovery broadcast
* Default: un set di script con info utili senza ingaggiare il target in maniera pericolosa (dangerous), quindi che non vengono exploitate vulnerabilità. Verranno eseguiti una serie di script solo sulle porte con servizi rilevanti per quello script
* Discovery
* Exploitation
* Bruteforce: attacchi bruteforce o per trovare credenziali
* Safe

## Default script scan

```
nmap -sS -sV -sC -p- -T4 <IP> // -sC default script scan
```

<figure><img src="../../../.gitbook/assets/image (415).png" alt=""><figcaption></figcaption></figure>

In questo caso una misconfigurazione del servizio mongodb ci ha dato informazioni sulla versione del OS.

<figure><img src="../../../.gitbook/assets/image (416).png" alt=""><figcaption></figcaption></figure>

Da mongodb possiamo anche prendere l'hostname del PC che in questo caso è victim-1

<figure><img src="../../../.gitbook/assets/image (417).png" alt=""><figcaption></figcaption></figure>

Obbiamo anche la versione del mongodb.

<figure><img src="../../../.gitbook/assets/image (418).png" alt=""><figcaption></figcaption></figure>

In questo caso è stato eseguito mongodb-databases.nse perché è nella categoria default come si può vedere con questi comandi:

<figure><img src="../../../.gitbook/assets/image (419).png" alt=""><figcaption></figcaption></figure>

## Eseguire un singolo script

```
nmap -sS -sV --script=mongodb-info -p- -T4 <IP>
```



<figure><img src="../../../.gitbook/assets/image (420).png" alt=""><figcaption></figcaption></figure>

<figure><img src="../../../.gitbook/assets/image (421).png" alt=""><figcaption></figcaption></figure>

<figure><img src="../../../.gitbook/assets/image (422).png" alt=""><figcaption></figcaption></figure>

Ad esempio il fatto che qui sopra ci sia scritto che per memcached non sia richiesta autenticazione è utile per le fasi successive, come punto di entrata del pentest.

<figure><img src="../../../.gitbook/assets/image (423).png" alt=""><figcaption></figcaption></figure>

è possibile eseguire più di uno script nello stesso comando

<figure><img src="../../../.gitbook/assets/image (424).png" alt=""><figcaption></figcaption></figure>

Si possono eseguire anche tutti gli script di un servizio, come ftp, usando la wildcard \*



## Combinare -sC -sV -sO: version detection, script scanning e OS detection (+ traceroute)

```
nmap -sS -A -p- -T4 <IP>
```

Possiamo usare il flag -A al posto di elencare tutti i flag citati

<figure><img src="../../../.gitbook/assets/image (425).png" alt=""><figcaption></figcaption></figure>
