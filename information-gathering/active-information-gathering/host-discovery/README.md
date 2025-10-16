# Host Discovery

Identificare gli host attivi sulla rete

Tecniche utilizzate per fare Host Discovery:

* Ping sweeps (ICMP Echo Requests): alcuni host come i windows non rispondono ai ping
* ARP Scanning: Utilizza l'Address Resolution Protocol, ma funziona solo all'interno della stessa rete (stesso broadcat domain)
* TCP SYN Ping (Stealth/Half-Open Scan): più stealth, guarda se risponde con SYN-ACK, usa una porta comune a cui invia il SYN per capire se è attivo.
* UDP Ping: Se host non rispondono a ICMP o TCP probles
* TCP ACK Ping: se risponde RST è vivo.
* SYN-ACK Ping: se riceve RST l'host è attivo.

<figure><img src="../../../.gitbook/assets/image (10) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>
