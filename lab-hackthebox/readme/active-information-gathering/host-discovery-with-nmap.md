# Host Discovery With Nmap

Trovare gli host in una rete

Usiamo un ping sweep o ping scan per trovare gli host connessi nella rete

Per fare lo scan dei dispositivi attivi  usiamo il flag -sn

<figure><img src="../../.gitbook/assets/image (531).png" alt=""><figcaption></figcaption></figure>

Facciamo praticamente un ping con ICMP echo.

<figure><img src="../../.gitbook/assets/image (532).png" alt=""><figcaption></figcaption></figure>

In questo caso abbiamo router, motherboard del windows workstation e infine la kali linux VM.

Dopo aver trovato i dispositivi faremo poi lo scan delle porte su ogni host trovato.

### Netdiscover

<figure><img src="../../.gitbook/assets/image (533).png" alt=""><figcaption></figcaption></figure>

sudo + ![](<../../.gitbook/assets/image (534).png>)

<figure><img src="../../.gitbook/assets/image (535).png" alt=""><figcaption></figcaption></figure>

In questo caso viene usato ARP come protocollo, con delle ARP requests.



