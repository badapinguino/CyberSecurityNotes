# Port Scanning & Enumeration with Nmap

Utilizziamo Nmap in particolare perché i suoi risultati possiamo importarli nel MSF (Metasploit Framework) per vulnerability detection e exploitation

Per trovare versioni dei servizi running sulle porte aperte e l'OS:

```
nmap -Pn -sV -O <IP>
```

Salvare i risultati in XML:

```
nmap -Pn -sV -O <IP> -oX nome_file.xml
```

