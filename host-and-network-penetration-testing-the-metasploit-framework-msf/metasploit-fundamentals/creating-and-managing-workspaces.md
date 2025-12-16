# Creating & Managing Workspaces

<figure><img src="../../.gitbook/assets/image.png" alt=""><figcaption></figcaption></figure>

Per ogni engagement è necessario creare workspace diversi, per evitare di mischiare dati di clienti e/o macchine/ambienti diversi.

{% hint style="info" %}
è importante creare workspace diversi per ogni assessment in modo da tenere separati i risultati
{% endhint %}

Tutti i dati sono salvati nel MSF DB. Essenziale che il Db sia attivo e quindi msfconsole sia connesso al MSF DB per non perdere i dati ad ogni restart.

***



```
db_status //test della connessione al DB
workspace -h
workspace
host
workspace -a Test
workspace
hosts
workspace default
hosts
workspace -a INE //creiamo un workspace per ogni company name per i vari pentest nella vita reale
workspace
workspace Test
workspace -d Test //per eliminare il workspace
workspace
workspace -r INE PTA //per rinominare un workspace, in questo caso da INE a PTA
workspace
```

<figure><img src="../../.gitbook/assets/image (1).png" alt=""><figcaption></figcaption></figure>

<figure><img src="../../.gitbook/assets/image (2).png" alt=""><figcaption></figcaption></figure>

<figure><img src="../../.gitbook/assets/image (3).png" alt=""><figcaption></figcaption></figure>

C'è un workspace di default che viene automaticamente selezionato (mostrato con l'asterisco).

Traccia i dati anche di sessioni precedenti:

<figure><img src="../../.gitbook/assets/image (4).png" alt=""><figcaption></figcaption></figure>

<figure><img src="../../.gitbook/assets/image (5).png" alt=""><figcaption></figcaption></figure>

<figure><img src="../../.gitbook/assets/image (6).png" alt=""><figcaption></figcaption></figure>

<figure><img src="../../.gitbook/assets/image (7).png" alt=""><figcaption></figcaption></figure>

<figure><img src="../../.gitbook/assets/image (8).png" alt=""><figcaption></figcaption></figure>
