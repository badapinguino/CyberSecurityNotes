# Firewall Detection & IDS Evasion

## ACK scan per vedere presenza di firewall

In nmap possiamo usare il flag -sA che fa un ACK scan, serve per capire se una porta è filtrata oppure no. Da ciò capiamo che se è filtrata c'è un stateful firewall.

nmap -Pn -sS -F \<IP>

<figure><img src="../../.gitbook/assets/image (405).png" alt=""><figcaption></figcaption></figure>

In questo caso abbiamo eseguito uno SYN scan ma già ci dice che le porte sono chiuse (e non filtrate)

```
nmap -Pn -sA <IP>
```

<figure><img src="../../.gitbook/assets/image (406).png" alt=""><figcaption></figcaption></figure>

In questo caso eseguendo un ACK scan (-sA) possiamo vedere che le porte non sono filtrate. Ce lo dice esplicitamente, quindi non c'è un FW.

## IDS Evasion

### Frammentazione pacchetti

<figure><img src="../../.gitbook/assets/image (407).png" alt=""><figcaption></figcaption></figure>

Una delle cose è frammentare i pacchetti, così il firewall non è in grado di leggere il contenuto e capire dai singoli pacchetti cosa sono

```
nmap -Pn -sS -sV -f <IP> // -f per frammentazione dei pacchetti
```

<figure><img src="../../.gitbook/assets/image (408).png" alt=""><figcaption></figcaption></figure>

### MTU

Possiamo anche specificare l'mtu con --mtu e impostare ad esempio l'mtu a 32 che sarebbe la Maximum Transmitted Unit. Mettendo a 32 però non viene frammentato effettivamente il pacchetto perché è più piccolo di 32. Quindi possiamo provare con 8

```
nmap -Pn -sS -sV -f --mtu 8 <IP> // massima dimensione dei frammenti è 8 byte
```

### Data Length

Possiamo anche impostare il ttl (time to live) come opzione oppure --data-length che mette dei bit a caso alla fine del pacchetto per farlo risultare una certa misura:

<figure><img src="../../.gitbook/assets/image (6) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

<figure><img src="../../.gitbook/assets/image (409).png" alt=""><figcaption></figcaption></figure>

### Decoy IP

Per evadere gli IDS c'è anche la possibilità di inviare i pacchetti da un IP sorgente diverso, come ad esempio quello del router (gateway IP) della rete alla quale siamo connessi, così se degli analisti guardano il traffico di rete gli sembrerà che è il router che fa le richieste. Questo vale solo se si è all'interno della stessa rete però.

~~Quindi bisogna scegliere un IP nella stessa rete, anche per vedere poi le risposte immagino.~~\
è possibile avere più decoy IP.

Ora proviamo a unire queste opzioni viste fino ad ora:

```
nmap -Pn -sS -sV -p445,3389 -f --data-length 200 -D 10.10.23.1,10.10.23.2 <IP dest> // -D Sembra che gli scan arrivino da questi due IP
```

<figure><img src="../../.gitbook/assets/image (410).png" alt=""><figcaption></figcaption></figure>

Ecco cosa vediamo da wireshark così:

<figure><img src="../../.gitbook/assets/image (411).png" alt=""><figcaption></figcaption></figure>

<figure><img src="../../.gitbook/assets/image (412).png" alt=""><figcaption></figcaption></figure>

Le risposte però vengono mandate al nostro IP vero quindi è un po' sospetto, però le richieste originali arrivavano dal IP decoy che avevamo fornito.

### Specificare una porta sorgente da cui far inviare il pacchetto

Bisogna usare il flag -g seguito dal numero di porta

```
nmap -Pn -sS -sV -g <PORT> <IP>  // -g specifica la porta sorgente
```

<figure><img src="../../.gitbook/assets/image (413).png" alt=""><figcaption></figcaption></figure>

<figure><img src="../../.gitbook/assets/image (414).png" alt=""><figcaption></figcaption></figure>

Come si può vedere da wireshark la source port è effettivamente quella che abbiamo impostato noi, in questo modo sembra quasi che è un servizio DNS (porta 53) che fa la richiesta, o comunque una porta specifica e non una porta generata a caso che può essere più sospetta.
