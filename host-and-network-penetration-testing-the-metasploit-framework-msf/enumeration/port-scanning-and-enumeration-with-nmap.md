# Port Scanning & Enumeration with Nmap

<figure><img src="../../.gitbook/assets/image (2).png" alt=""><figcaption></figcaption></figure>

Utilizziamo Nmap in particolare perché i suoi risultati possiamo importarli nel MSF (Metasploit Framework) per vulnerability detection e exploitation.

<figure><img src="../../.gitbook/assets/image (1) (1).png" alt=""><figcaption></figcaption></figure>

Per trovare versioni dei servizi running sulle porte aperte e l'OS:

```
nmap -Pn -sV -O <IP>
```

<figure><img src="../../.gitbook/assets/image (2) (1).png" alt=""><figcaption></figcaption></figure>

Salvare i risultati in XML:

```
nmap -Pn -sV -O <IP> -oX nome_file.xml
```

<figure><img src="../../.gitbook/assets/image (3).png" alt=""><figcaption></figcaption></figure>

<figure><img src="../../.gitbook/assets/image (4).png" alt=""><figcaption></figcaption></figure>

<figure><img src="../../.gitbook/assets/image (5).png" alt=""><figcaption></figcaption></figure>
