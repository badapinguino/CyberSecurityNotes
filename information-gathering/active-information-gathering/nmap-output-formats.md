# Nmap Output Formats

Meglio loggare tutti i comandi eseguiti su una vittima mentre si fa un penetration test

L'obiettivo di questo video è di salvare i risultati di nmap in un formato comodo per noi come log o per darlo in input ad altri programmi come Metasploit Framework.

* oN: output normale come da riga di comando, va bene per un txt
* oX: XML che è utile per essere dato in input a Metasploit Framework Database (si può fare un workspace per ogni pentest fatto)
* oS: script kiddie
* oG: formato utile se si vuole usare grep
* oA: oN, oX e oG assieme
* -v: verbose
* \--reason: specifica perché una porta è nello stato indicato

<figure><img src="../../.gitbook/assets/image (1) (1) (1) (1) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

In qualsiasi formato decidiamo di salvare comunque viene mostrato il risultato a terminale:

<figure><img src="../../.gitbook/assets/image (2) (1) (1) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

```
nmap <IP> -oN nmap_normal.txt // output txt
nmap <IP> -oX nmap_xml.xml // output xml
```

<figure><img src="../../.gitbook/assets/image (3) (1) (1) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

## Caricare il file xml nel DB metasploit

prima bisogna avviare il db:

<figure><img src="../../.gitbook/assets/image (4) (1) (1) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

avviamo poi metaspolit:

<figure><img src="../../.gitbook/assets/image (5) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

possiamo poi creare un nuovo workspace per questa attività di pentest e usare questo:

<figure><img src="../../.gitbook/assets/image (6) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

ora carichiamo il file nel database, per il workspace appena creato

<figure><img src="../../.gitbook/assets/image (7) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

Una volta importati i risultati come li posso usare? ad esempio usando il comando hosts o services

<figure><img src="../../.gitbook/assets/image (8) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

<figure><img src="../../.gitbook/assets/image (9) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

Possiamo anche eseguire nmap usando il comando db\_nmap, ed il risultato verrà automaticamente salvato nel Metslpoit Database:

<figure><img src="../../.gitbook/assets/image (10) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

E vengono poi aggiornate le informazioni presenti sul database che avevamo importato prima:

<figure><img src="../../.gitbook/assets/image (11) (1) (1).png" alt=""><figcaption></figcaption></figure>

<figure><img src="../../.gitbook/assets/image (12) (1).png" alt=""><figcaption></figcaption></figure>

Come possiamo vedere questi risultati vengono salvati solo nel workspace che abbiamo selezionato all'inizio:

<figure><img src="../../.gitbook/assets/image (14) (1).png" alt=""><figcaption></figcaption></figure>

Se facciamo exit e poi riapriamo metasploit tramite il comando msfconsole, troviamo ancora i risultati salvati nel DB, questo fintanto che il DB rimane attivo.

## Greppable format

<figure><img src="../../.gitbook/assets/image (15) (1).png" alt=""><figcaption></figcaption></figure>

<figure><img src="../../.gitbook/assets/image (16) (1).png" alt=""><figcaption></figcaption></figure>

Esempio di utilizzo con il grepable format, nel caso specifico per avere delle informazioni in output personalizzate:

<figure><img src="../../.gitbook/assets/image (17) (1).png" alt=""><figcaption></figcaption></figure>
