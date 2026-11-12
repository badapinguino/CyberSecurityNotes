# Port Scanning With Auxiliary Modules

Utilizziamo gli auxiliary modules di Metasploit.

<figure><img src="../../.gitbook/assets/image (1793).png" alt=""><figcaption></figcaption></figure>

Vengono usati per recuperare le informazioni, fanno un po' tutto tranne l'exploitation delle vulnerabilità

Nella post-exploitation si intende che possiamo guardare nella rete privata/interna quali altri IP ci sono dentro una rete alla quale appartiene il PC/Server che abbiamo exploitato. Questo perché così possiamo evitare di scaricare e importare nmap nel PC/Server exploitato.

<figure><img src="../../.gitbook/assets/image (1794).png" alt=""><figcaption></figcaption></figure>

Quello che faremo in quella fase di esempio di post-exploitation è:

1. Otteniamo una meterpreter shell sul primo target che è connesso a internet
2. Aggiungiamo una network route che ci permetta di utilizzare gli auxiliary modules per scannerizzare un secondo target non connesso a internet, ma connesso alla rete interna del nostro primo target.
3. Questo per identificare l'IP del secondo target e poi eseguire un port scan su di esso



```
service postresql start
msfconsole
db_status
workspace -a Port_Scan
workspace
```

Cerchiamo l'auxiliary module di portscan:

<figure><img src="../../.gitbook/assets/image (1795).png" alt=""><figcaption></figcaption></figure>

Scegliamo il modulo da utilizzare, in questo caso un normale scan tcp:

<figure><img src="../../.gitbook/assets/image (1796).png" alt=""><figcaption></figcaption></figure>

<figure><img src="../../.gitbook/assets/image (1797).png" alt=""><figcaption></figcaption></figure>

Attenzione le porte sono 65 mila, se vogliamo scansionarle tutte dobbiamo modificare

<figure><img src="../../.gitbook/assets/image (1798).png" alt=""><figcaption></figcaption></figure>

Impostiamo l'IP destinazione con quello del server vittima/target

Per eseguire usiamo run

<figure><img src="../../.gitbook/assets/image (1799).png" alt=""><figcaption></figcaption></figure>

Scarichiamo la pagina web con curl visto che non abbiamo una GUI per vederla:

<figure><img src="../../.gitbook/assets/image (1800).png" alt=""><figcaption></figcaption></figure>

Come vediamo ospita una applicazione web chiamata XODA

Andiamo ad exploitarla velocemente:

<figure><img src="../../.gitbook/assets/image (1801).png" alt=""><figcaption></figcaption></figure>

In questo caso dobbiamo cambiare l'RHOSTS e il TARGETURI:

<figure><img src="../../.gitbook/assets/image (1802).png" alt=""><figcaption></figcaption></figure>

Ora avviamo l'exploit e attendiamo che ci ritorni una meterpreter shell:

<figure><img src="../../.gitbook/assets/image (1803).png" alt=""><figcaption></figcaption></figure>

<figure><img src="../../.gitbook/assets/image (1599).png" alt=""><figcaption></figcaption></figure>

Ora dobbiamo avviare una shell normale bash e capire qual è l'IP della rete interna:

<figure><img src="../../.gitbook/assets/image (1600).png" alt=""><figcaption></figcaption></figure>

Ora con CTRL+C usciamo e torniamo alla shell meterpreter.

Dobbiamo ora usare il comando autoroute:

<figure><img src="../../.gitbook/assets/image (1601).png" alt=""><figcaption></figcaption></figure>

Mettiamo la sessione in background:

<figure><img src="../../.gitbook/assets/image (1602).png" alt=""><figcaption></figcaption></figure>

<figure><img src="../../.gitbook/assets/image (1603).png" alt=""><figcaption></figcaption></figure>

Ora possiamo eseguire il portscan sulla seconda vittima, il secondo target. Impostando l'IP della rete interna che identifica la seconda vittima, il portscan verrà effettuato automaticamente passando per lla prima vittima, questo perché abbiamo configurato la route con autoroute.

<figure><img src="../../.gitbook/assets/image (1604).png" alt=""><figcaption></figcaption></figure>

Come possiamo vedere qui effettivamente stiamo andando a trovare le porte aperte sul secondo target.

**Ecco come può essere molto utile l'auxiliary module del portscan presente su Metasploit** (MSF).

In poche parole abbiamo usato un pivot point che era la vittima 1 per scannerizzare la sua rete interna (credo che questo sia il pivoting)

## Scan UDP

Ora proviamo con il modulo per fare lo scan UDP: udp\_sweep

<figure><img src="../../.gitbook/assets/image (1605).png" alt=""><figcaption></figcaption></figure>

<figure><img src="../../.gitbook/assets/image (1606).png" alt=""><figcaption></figcaption></figure>

Dobbiamo impostare solo l'RHOSTS

<figure><img src="../../.gitbook/assets/image (1607).png" alt=""><figcaption></figcaption></figure>

Facciamo lo scan sul primo target, quindi prendiamo l'IP della rete sulla quale siamo con la macchina attaccante e sostituiamo il 2 con il 3 che è l'IP della prima vittima.

<figure><img src="../../.gitbook/assets/image (1609).png" alt=""><figcaption></figcaption></figure>

In questo caso non c'erano porte UDP aperte.

## Conclusione

L'obiettivo di questa sessione pratica era dimostrare con un esempio l'utilità degli auxiliary modules per il port scanning, non solo nella fase iniziale di information gathering ma anche nella parte di post-exploitation per fare pivoting su altri host in altre reti target.

Nei prossimi video vedremo come usare gli auxiliary modules per fare enumeration su tutti i vari servizi più comuni.
