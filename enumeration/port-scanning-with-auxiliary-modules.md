# Port Scanning With Auxiliary Modules

Utilizziamo gli auxiliary modules di Metasploit.

<figure><img src="../.gitbook/assets/image (134).png" alt=""><figcaption></figcaption></figure>

Vengono usati per recuperare le informazioni, fanno un po' tutto tranne l'exploitation delle vulnerabilità

Nella post-exploitation si intende che possiamo guardare nella rete privata/interna quali altri IP ci sono dentro una rete alla quale appartiene il PC/Server che abbiamo exploitato. Questo perché così possiamo evitare di scaricare e importare nmap nel PC/Server exploitato.

<figure><img src="../.gitbook/assets/image (135).png" alt=""><figcaption></figcaption></figure>

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

<figure><img src="../.gitbook/assets/image (136).png" alt=""><figcaption></figcaption></figure>

Scegliamo il modulo da utilizzare, in questo caso un normale scan tcp:

<figure><img src="../.gitbook/assets/image (137).png" alt=""><figcaption></figcaption></figure>

<figure><img src="../.gitbook/assets/image (138).png" alt=""><figcaption></figcaption></figure>

Attenzione le porte sono 65 mila, se vogliamo scansionarle tutte dobbiamo modificare

<figure><img src="../.gitbook/assets/image (139).png" alt=""><figcaption></figcaption></figure>

Impostiamo l'IP destinazione con quello del server vittima/target

Per eseguire usiamo run

<figure><img src="../.gitbook/assets/image (140).png" alt=""><figcaption></figcaption></figure>

Scarichiamo la pagina web con curl visto che non abbiamo una GUI per vederla:

<figure><img src="../.gitbook/assets/image (141).png" alt=""><figcaption></figcaption></figure>

Come vediamo ospita una applicazione web chiamata XODA

Andiamo ad exploitarla velocemente:

<figure><img src="../.gitbook/assets/image (142).png" alt=""><figcaption></figcaption></figure>

In questo caso dobbiamo cambiare l'RHOSTS e il TARGETURI:

<figure><img src="../.gitbook/assets/image (143).png" alt=""><figcaption></figcaption></figure>

Ora avviamo l'exploit e attendiamo che ci ritorni una meterpreter shell:

<figure><img src="../.gitbook/assets/image (144).png" alt=""><figcaption></figcaption></figure>
