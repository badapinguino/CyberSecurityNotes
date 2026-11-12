# Port Scanning & Enumeration with Nmap

<figure><img src="../../daniele-badagliacca-docs/docs/.gitbook/assets/image (1033).png" alt=""><figcaption></figcaption></figure>

Utilizziamo Nmap in particolare perché i suoi risultati possiamo importarli nel MSF (Metasploit Framework) per vulnerability detection e exploitation.

<figure><img src="../../daniele-badagliacca-docs/docs/.gitbook/assets/image (1034).png" alt=""><figcaption></figcaption></figure>

Per trovare versioni dei servizi running sulle porte aperte e l'OS:

```
nmap -Pn -sV -O <IP>
```

<figure><img src="../../daniele-badagliacca-docs/docs/.gitbook/assets/image (1035).png" alt=""><figcaption></figcaption></figure>

Salvare i risultati in XML:

```
nmap -Pn -sV -O <IP> -oX nome_file.xml
```

<figure><img src="../../daniele-badagliacca-docs/docs/.gitbook/assets/image (1036).png" alt=""><figcaption></figcaption></figure>

<figure><img src="../../daniele-badagliacca-docs/docs/.gitbook/assets/image (1037).png" alt=""><figcaption></figcaption></figure>

<figure><img src="../../daniele-badagliacca-docs/docs/.gitbook/assets/image (1038).png" alt=""><figcaption></figcaption></figure>
