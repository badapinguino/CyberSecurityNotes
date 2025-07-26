# Nmap Scripting Engine (NSE)

<figure><img src="../../.gitbook/assets/image (131).png" alt=""><figcaption></figcaption></figure>

Continuiamo da dove abbiamo lasciato, vorremmo usare l'nmap scripting engine per identificare l'OS.

NSE è un motore che mette a disposizione nmap per poterci creare degli script.

Per trovare tutti gli script messi a disposizione da NSE possiamo cercare in una cartella:

```
ls -al /usr/share/nmap/scripts
```

<figure><img src="../../.gitbook/assets/image (132).png" alt=""><figcaption></figcaption></figure>

Utilizzano il linguaggio Lua per essere programmati.

Possiamo cercare script rilevanti usando grep, ad es.:

<figure><img src="../../.gitbook/assets/image (133).png" alt=""><figcaption></figcaption></figure>
