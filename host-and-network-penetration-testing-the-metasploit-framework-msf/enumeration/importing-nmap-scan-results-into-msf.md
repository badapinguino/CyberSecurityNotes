# Importing Nmap Scan Results into MSF

#### Caricare file xml nel DB metasploit

1. Avviare il DB metasploit: service postgresql start
2. Entrare in metasploit: msfconsole
3. Creare un nuovo workspace: workspace -a \<nome workspace>
4. Verificare che siamo nel nuovo workspace: workspace
5. Caricare il file XML: db\_import nomefile.xml
6. Verificare che sia caricato con i comandi: hosts e services

<figure><img src="../../daniele-badagliacca-docs/docs/.gitbook/assets/image (1039).png" alt=""><figcaption></figcaption></figure>

<figure><img src="../../daniele-badagliacca-docs/docs/.gitbook/assets/image (1040).png" alt=""><figcaption></figcaption></figure>

<figure><img src="../../daniele-badagliacca-docs/docs/.gitbook/assets/image (1041).png" alt=""><figcaption></figcaption></figure>

<figure><img src="../../daniele-badagliacca-docs/docs/.gitbook/assets/image (1612).png" alt=""><figcaption></figcaption></figure>

Si può anche inizializzare e performare una nmap scan direttamente dentro metasploit ed il cui output verrà automaticamente inserito nel database:

```
db_nmap <comando>
db_nmap -Pn -sV -O <IP>
```

<figure><img src="../../daniele-badagliacca-docs/docs/.gitbook/assets/image (1042).png" alt=""><figcaption></figcaption></figure>

<figure><img src="../../daniele-badagliacca-docs/docs/.gitbook/assets/image (1043).png" alt=""><figcaption></figcaption></figure>

è possibile anche chiedere di mostrare le vulnerabilità, ma per ora non ne abbiamo perché non siamo ancora arrivati a quel punto:

<figure><img src="../../daniele-badagliacca-docs/docs/.gitbook/assets/image (1613).png" alt=""><figcaption></figcaption></figure>
