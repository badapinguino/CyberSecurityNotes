# Port Scanning With Nmap

### Default nmap port scan

```
nmap <IP>
```

è il default nmap port scan, quindi consiste in un SYN Scan con host discovery automaticamente, e poi manda dei pacchetti SYN alle porte specificate, quando non sono specificate porte manda il pacchetto alle 1000 porte più usate. Il processo è: SYN -> risposta SYN/ACK se porta è aperta -> RST. Se chiusa: SYN -> RST, se firewall: SYN -> nessuna risposta.

SYN viene usato solo se usiamo sudo o siamo privileged user. Altrimenti usa il tCP connect scan, quindi completa il 3-way handshake in questo caso.

### Fast scanning profile:

Scannerizza le prime 100 porte più comuni (-F) e skippa l'host discovery (-Pn)

```
nmap -Pn -F <IP> // si può aggiungere -sS per fare sempre il SYN scan
```

### SYN scan

SYN scan è anche chiamato stealth scan perché sono pacchetti che solitamente si vedono in rete quindi non creano sospetti, ma soprattutto perché non completando il 3-way handshake la connessione non viene loggata.

### TCP connect scan

Completa il 3-way handshake, è la versione di default per utenti non root che usano nmap

```
nmap -Pn -sT <IP>
```

Porte aperte: SYN -> SYN ACK -> ACK e poi RST, ACK.

Porte chiuse: SYN -> RST

Porte filtrate (fw): SYN -> nessuna risposta

Fa molto rumore ed è facilmente identificabile.

### Tip

Quando si lavora con sistemi Windows target, se vediamo filtered vuol dire che c'è di mezzo un firewall windows, altrimenti sarebbe closed (se non ci fossero regole per quella porta sul Windows fireall oppure se non fosse attivo il fw.

## Scannerizzare porte UDP

```
nmap -sU <IP> // scan generica delle porte UDP, delle 1000 porte più comuni
nmap -Pn -sU -p53,137,138,139 // -Pn nessun test che sia online prima di cercare le porte
```
