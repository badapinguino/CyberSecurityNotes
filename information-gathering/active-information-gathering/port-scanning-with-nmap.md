# Port scanning with Nmap

Questo è l'esempio di lab environment:

<figure><img src="../../.gitbook/assets/image (536).png" alt=""><figcaption></figcaption></figure>

SYN scan sulle porte più usate in TCP è quello che viene fatto con la default nmap scan

<figure><img src="../../.gitbook/assets/image (537).png" alt=""><figcaption></figcaption></figure>

Isistemi windows solicamente blocano i ping probes, quindi i pacchetti ICMP

<figure><img src="../../.gitbook/assets/image (538).png" alt=""><figcaption></figcaption></figure>

Con -Pn viene detto a nmap di non verificare se l'host è online con il ping ma direttamente guardare le porte.



Se vogliamo scannerizzare tutte le porte TCP: -p-

Se voglio una porta specifica: -p80 oppure -p80,443,3389



Se la porta è filtered o c'è uin firewall o è chiusa

Per le 100 porte più usate: -F (che sta per fast)



Per fare un UDP port scan: -sU

Per verbose, quindi vedere i risultati di tutte le porte: -v

Per service detection, quindi identificare i servizi per ogni porta: -sV

<figure><img src="../../.gitbook/assets/image (539).png" alt=""><figcaption></figcaption></figure>

Operation system detection scan, per identificare quale SO: -O

Utile per poter cercare poi eventuali vulnerabilità della versione del sistema operativo

<figure><img src="../../.gitbook/assets/image (441).png" alt=""><figcaption></figcaption></figure>



Default script scan, viene eseguito una serie di script che cerca di identificare ogni versione dei servizi aperti: -sC

<figure><img src="../../.gitbook/assets/image (442).png" alt=""><figcaption></figcaption></figure>



Aggressive Scan: Service version detection, os detection e standard script scan (-sV -O -sC): -A

Velocizzare o rallentare uno scan: -T4 o da -T0 a -T5 dove più basso è il numero più è lento.

<figure><img src="../../.gitbook/assets/image (443).png" alt=""><figcaption></figcaption></figure>

<figure><img src="../../.gitbook/assets/image (444).png" alt=""><figcaption></figcaption></figure>



Per salvare su file l'output: -oN \<nometile>

<figure><img src="../../.gitbook/assets/image (445).png" alt=""><figcaption></figcaption></figure>

L'output può essere in test nomrale come quello che appare sul terminale: -oN, o in XML: -oX
