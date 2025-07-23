# Ping Sweeps

<figure><img src="../../../.gitbook/assets/image (120).png" alt=""><figcaption></figcaption></figure>

<figure><img src="../../../.gitbook/assets/image (121).png" alt=""><figcaption></figcaption></figure>

<figure><img src="../../../.gitbook/assets/image (122).png" alt=""><figcaption></figcaption></figure>

<figure><img src="../../../.gitbook/assets/image (123).png" alt=""><figcaption></figcaption></figure>

### ping

```
ping 10.4.31.111
ping -c 5 10.4.31.111 //manda 5 richieste ping di numero in Linux, in Windows basta sostituire la c con una n
```

Per lanciare wireshark su una interfaccia di rete specifica:

```
sudo wireshark -i eth1
```

Possiamo usare wireshark per vedere le richieste di ping che abbiamo inviato

<figure><img src="../../../.gitbook/assets/image (124).png" alt=""><figcaption></figcaption></figure>

In questo caso l'host sembra offline ma è da verificare, magari non risponde agli ICMP.

Per fare un ping su tutta la rete e vedere chi è attivo:

```
ping -b -c 1 10.1.0.0 // -b significa broadcast
```

### fping

Altro tool utile a fare ping su tutta la rete (ping sweep): fping

```
fping -a -g 10.10.23.0/24 //-g generate target list
// -S imposta l'IP sorgente
```

fping usa comunque ICMP.

<figure><img src="../../../.gitbook/assets/image (125).png" alt=""><figcaption></figcaption></figure>

Perché così tutti gli errori vengono nascosti.

### nmap

ICMP ping sweep con nmap, che esegue ICMP echo request, poi un TCP SYN alla porta 443, poi TCP ACK alla porta 80, e un ICMP timestamp request:

```
nmap -sn 10.4.31.111
```

L'host non risponde a ICMP ma è un windows ed è attivo come si può vedere da questo coamando:

<figure><img src="../../../.gitbook/assets/image (126).png" alt=""><figcaption></figcaption></figure>
