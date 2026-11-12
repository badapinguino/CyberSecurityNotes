# Google Dorks/Hacking

Per limitare tutti i risultati ad un dominio cercare su Google:

```
site:ine.com
```

Per limitare al fatto che ci sia una certa parola dentro l'url si usa inurl:

```
site:ine.com inurl:admin
```

Per mostrare solo i subdomain usiamo site:\*.ine.com

```
site:*.ine.com
```

Per filtrare le parole del titolo si usa intitle:

```
site:*.ine.com intitle:admin
```

Così si trovano dei sottodomini magari di pagine che non dovrebbero essere mostrate, con dei login interni. Tipo una subdomain enumeration a mano

Per cercare un tipo file: usare filetype:

```
/site:*.ine.com filetype:xlsx
```

Se stiamo mcercando siti con directory listing disponibile, solitamente per misconfigurazioni sul server, possiamo cercare index of

```
/intitle:index of
```

<figure><img src="../../.gitbook/assets/image (500).png" alt=""><figcaption></figcaption></figure>

Se vogliamo trovare una vecchia versione di un sito web: cache:

```
cache:ine.co
```

Altrimenti usiamo waybackmachine

Pssiamo trovare informazioni sensibili che sono state poi rimosse.



inurl:auth\_user\_fiole.txt

<figure><img src="../../.gitbook/assets/image (501).png" alt=""><figcaption></figcaption></figure>

inurl:passwd.txt

<figure><img src="../../.gitbook/assets/image (502).png" alt=""><figcaption></figcaption></figure>

## Google Hacking Database

<figure><img src="../../.gitbook/assets/image (503).png" alt=""><figcaption><p>Ha già degli esempi di query disponibili</p></figcaption></figure>



Possibilità di filtri per categoria e immagine:

<figure><img src="../../.gitbook/assets/image (504).png" alt=""><figcaption></figcaption></figure>
