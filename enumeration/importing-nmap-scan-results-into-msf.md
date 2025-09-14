# Importing Nmap Scan Results into MSF

#### Caricare file xml nel DB metasploit

1. Avviare il DB metasploit: service postgresql start
2. Entrare in metasploit: msfconsole
3. Creare un nuovo workspace: workspace -a \<nome workspace>
4. Verificare che siamo nel nuovo workspace: workspace
5. Caricare il file XML: db\_import nomefile.xml
6. Verificare che sia caricato con i comandi: hosts e services

<figure><img src="../.gitbook/assets/image (2) (1) (1) (1) (1) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

Si può anche inizializzare e performare una nmap scan direttamente dentro metasploit ed il cui output verrà automaticamente inserito nel database:

```
db_nmap <comando>
db_nmap -Pn -sV -O <IP>
```

è possibile anche chiedere di mostrare le vulnerabilità, ma per ora non ne abbiamo perché non siamo ancora arrivati a quel punto:

<figure><img src="../.gitbook/assets/image (3) (1) (1) (1) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>
