# WAF Detection With wafw00f

Identificare se un sito usa WAF con Wafwoof

<figure><img src="../../.gitbook/assets/image (64).png" alt=""><figcaption></figcaption></figure>

<figure><img src="../../.gitbook/assets/image (65).png" alt=""><figcaption></figcaption></figure>

Ti dice anche quale WAF sta proteggendo il sito.

```
wafw00f -l
```

Mostra la lista dei firewall che supporta wafwoof

### Utilizzo

```
wafw00f <dominio.org>
```

```
wafw00f <dominio.org> -a
```

Se aggiungiamo **-a** va a testare per tutti i WAF che ha in elenco:

<figure><img src="../../.gitbook/assets/image (66).png" alt=""><figcaption></figcaption></figure>

<figure><img src="../../.gitbook/assets/image (67).png" alt=""><figcaption></figcaption></figure>

In questo caso non essendoci un WAF sappiamo che l'IP trovato prima era il vero IP del server



<figure><img src="../../.gitbook/assets/image (68).png" alt=""><figcaption></figcaption></figure>
