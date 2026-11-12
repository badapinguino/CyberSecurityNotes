# File & Directory Brute-Force

<figure><img src="../../../daniele-badagliacca-docs/docs/.gitbook/assets/image (81).png" alt=""><figcaption></figcaption></figure>

<figure><img src="../../../daniele-badagliacca-docs/docs/.gitbook/assets/image (82).png" alt=""><figcaption></figcaption></figure>

<figure><img src="../../../daniele-badagliacca-docs/docs/.gitbook/assets/image (83).png" alt=""><figcaption></figcaption></figure>

<figure><img src="../../../daniele-badagliacca-docs/docs/.gitbook/assets/image (84).png" alt=""><figcaption></figcaption></figure>

<figure><img src="../../../daniele-badagliacca-docs/docs/.gitbook/assets/image (85).png" alt=""><figcaption></figcaption></figure>

<figure><img src="../../../daniele-badagliacca-docs/docs/.gitbook/assets/image (86).png" alt=""><figcaption></figcaption></figure>

***

```
gobuster
```

<figure><img src="../../../daniele-badagliacca-docs/docs/.gitbook/assets/image (87).png" alt=""><figcaption></figcaption></figure>

<figure><img src="../../../daniele-badagliacca-docs/docs/.gitbook/assets/image (88).png" alt=""><figcaption></figcaption></figure>

## Simple directory bruteforce con Gobuster

```
gobuster dir -u http://demo.ine.local -w /usr/share/wordlists/dirb/common.txt
```

<figure><img src="../../../daniele-badagliacca-docs/docs/.gitbook/assets/image (89).png" alt=""><figcaption></figcaption></figure>

<figure><img src="../../../daniele-badagliacca-docs/docs/.gitbook/assets/image (90).png" alt=""><figcaption></figcaption></figure>

<figure><img src="../../../daniele-badagliacca-docs/docs/.gitbook/assets/image (91).png" alt=""><figcaption></figcaption></figure>

### Filtriamo il risultato escludendo le risposte di errore (4xx)

```

gobuster dir -u http://demo.ine.local -w /usr/share/wordlists/dirb/common.txt -b 403,404  #volendo possiamo filtrare anche le 301 che sono redirect
```

<figure><img src="../../../daniele-badagliacca-docs/docs/.gitbook/assets/image (92).png" alt=""><figcaption></figcaption></figure>

<figure><img src="../../../daniele-badagliacca-docs/docs/.gitbook/assets/image (93).png" alt=""><figcaption></figcaption></figure>

<figure><img src="../../../daniele-badagliacca-docs/docs/.gitbook/assets/image (94).png" alt=""><figcaption></figcaption></figure>

### Specifichiamo l'estensione del file da cercare

```

gobuster dir -u http://demo.ine.local -w /usr/share/wordlists/dirb/common.txt -b 403,404 -x .php,.xml,.txt -r  #-r è ricorsivo
```

<figure><img src="../../../daniele-badagliacca-docs/docs/.gitbook/assets/image (95).png" alt=""><figcaption></figcaption></figure>

<figure><img src="../../../daniele-badagliacca-docs/docs/.gitbook/assets/image (96).png" alt=""><figcaption></figcaption></figure>

### Cerchiamo file in una directory specifica (es. /data)

```

gobuster dir -u http://demo.ine.local/data -w /usr/share/wordlists/dirb/common.txt -b 403,404 -x .php,.xml,.txt -r
```

<figure><img src="../../../daniele-badagliacca-docs/docs/.gitbook/assets/image (3070).png" alt=""><figcaption></figcaption></figure>

<figure><img src="../../../daniele-badagliacca-docs/docs/.gitbook/assets/image (97).png" alt=""><figcaption></figcaption></figure>
