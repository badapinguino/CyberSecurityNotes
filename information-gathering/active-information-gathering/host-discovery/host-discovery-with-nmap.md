# Host Discovery with Nmap

Per utilizzare nmap il comando ha sempre questo schema:

nmap scanoptions target

Per alcuni tipi di scan nmap ha bisogno di un utente root o sudo.

Per avere delle informazioni sulle vari opzioni guardare la documentazione che è ben suddivisa per argomento: nmap -h

<figure><img src="../../../.gitbook/assets/image (5) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

Per scansionare quali host attivi su una rete:

```
nmap -sn <IP>/24 // oppure <IP start>-<IP dest>
```

```
nmap -sn <IP>/24 --send-ip // Non esegue degli ARP per ogni IP
```

<figure><img src="../../../.gitbook/assets/image (6) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

<figure><img src="../../../.gitbook/assets/image (7) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

<figure><img src="../../../.gitbook/assets/image (9) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

Come abbiamo appreso possiamo vedere anche i pacchetti TCP che sono stati inviati, lo scan infatti sappiamo che invia anche i TCP, inatti manda i SYN alla 443 e gli ACK alla 80. Volendo possiamo modificare queste porte.

Qual è il problema con questo ping scan? Usa ICMP e fa quindi rumore.

Nmap può scannerizzare anche una lista di IP in un file txt, ogni IP deve essere in una nuova riga del file:

```
nmap -sn -iL targets.txt
```

### TCP SYN Ping

Serve per fare uno scan su singole porte per vedere se l'host è online, meglio non farlo su tutte le porte perché è più lento.

```
nmap -sn -PS <IP> // mettendo -sn gli diciamo di non fare lo scan su tutte le porte, ma solo per vedere se l'host è online
nmap -sn -PS22 <IP> // SYN PING sulla porta 22
nmap -sn -PS1-1000 <IP> // Non sapendo quale porti sono aperte, meglio specificare un range di porte
```

-PS di default fa un ping SYN alla porta 80, ma si può specificare una porta diversa

<figure><img src="../../../.gitbook/assets/image (11) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

<figure><img src="../../../.gitbook/assets/image (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

### TCP ACK PING Scan

Manda un pacchetto TCP con ACK flag ad una porta 80, se l'host risponde con RST allora sappiamo che è online.

```
nmap -sn -PA <IP>
nmap -sn -PA3389 <IP>
```

Non è molto affidabile perché ogni tanto dei sistemi potrebbero essere configurati per rifiutare dei pacchetti ACK non conosciuti, oppure il firewall (come windows) che filtra e non invia i pacchetti TCP con flag RST.

<figure><img src="../../../.gitbook/assets/image (2) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

&#x20;In base a questo possiamo anche capire se c'è un firewall, guardando assieme SYN e ACK.

### ICMP (specific) ping scan

ICMP echo request puro, non combinato con altro. Non molto consigliato.

```
nmap -sn -PE <IP> --send-ip // altrimenti senza --send-ip non sempre parte 
```

Windows non accetta pacchetti ICMP, quindi non molto utile.



<figure><img src="../../../.gitbook/assets/image (3) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

## Metodologia consigliata:

```
nmap -sn -v -T4 <IP> // -v ci dà più info e capiamo come mai nmap è arrivato a quel risultato
nmap -sn -PS21,22,25,80,445,3389,8080 -T4 <IP> // SYN Scan su le porte comuni
nmap -sn -PS21,22,25,80,445,3389,8080 -PU137,138 -T4 <IP> // Aggiungiamo le porte UDP relativi alle porte NETBIOS, per PC Windows
```

<figure><img src="../../../.gitbook/assets/image (4) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

I risultati arrivano molto più veloci rispetto al primo comando, su una grossa rete può essere utile e fare la differenza.

Nel caso di Windows combina anche le UDP perché ci sono alcune porte usate in windows (es. 137 e 138 usate da NETBIOS), perché alcuni host potrebbero avere firewall sulle porte TCP ma non configurati sulle UDP, così li troviamo. Ci mette pochissimo in più
