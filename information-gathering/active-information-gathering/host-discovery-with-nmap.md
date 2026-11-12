# Host Discovery With Nmap

Trovare gli host in una rete

Usiamo un ping sweep o ping scan per trovare gli host connessi nella rete

Per fare lo scan dei dispositivi attivi  usiamo il flag -sn

<figure><img src="../../daniele-badagliacca-docs/docs/.gitbook/assets/image (1764).png" alt=""><figcaption></figcaption></figure>

Facciamo praticamente un ping con ICMP echo.

<figure><img src="../../daniele-badagliacca-docs/docs/.gitbook/assets/image (1765).png" alt=""><figcaption></figcaption></figure>

In questo caso abbiamo router, motherboard del windows workstation e infine la kali linux VM.

Dopo aver trovato i dispositivi faremo poi lo scan delle porte su ogni host trovato.

### Netdiscover

<figure><img src="../../daniele-badagliacca-docs/docs/.gitbook/assets/image (1766).png" alt=""><figcaption></figcaption></figure>

sudo + ![](<../../daniele-badagliacca-docs/docs/.gitbook/assets/image (1767).png>)

<figure><img src="../../daniele-badagliacca-docs/docs/.gitbook/assets/image (1768).png" alt=""><figcaption></figcaption></figure>

In questo caso viene usato ARP come protocollo, con delle ARP requests.



