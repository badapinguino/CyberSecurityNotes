# Phishing with Gophish

## Part 1

<figure><img src="../../../.gitbook/assets/image (1669).png" alt=""><figcaption></figcaption></figure>

<figure><img src="../../../.gitbook/assets/image (1670).png" alt=""><figcaption></figcaption></figure>

<figure><img src="../../../.gitbook/assets/image (1671).png" alt=""><figcaption></figcaption></figure>

<figure><img src="../../../.gitbook/assets/image (1672).png" alt=""><figcaption></figcaption></figure>

***

<figure><img src="../../../.gitbook/assets/image (1673).png" alt=""><figcaption></figcaption></figure>

<figure><img src="../../../.gitbook/assets/image (1674).png" alt=""><figcaption></figcaption></figure>

<figure><img src="../../../.gitbook/assets/image (1675).png" alt=""><figcaption></figcaption></figure>

Per questo test abbiamo un elenco di mail target che abbiamo ipoteticamente trovato nella fase di information gathering. In questo esempio andiamo a usare come target delle nostre campagne phishing la mail dell'intern. Infatti in Thunderbird abbiamo la sua mail configurata.

<figure><img src="../../../.gitbook/assets/image (1676).png" alt=""><figcaption></figcaption></figure>

Nella realtà dovremmo setuppare gophish su un server linux VPS. Comprare un dominio, impostare le mail che vorremmo usare come mittenti, ad esempio con AWS simple email service per inviare le mail (se usiamo AWS).

L'interfaccia GUI web di GoPhish carica dati da internet e quindi dobbiamo eliminare questi riferimenti per evitare caricamenti troppo lunghi.

<figure><img src="../../../.gitbook/assets/image (1678).png" alt=""><figcaption></figcaption></figure>

<figure><img src="../../../.gitbook/assets/image (1677).png" alt=""><figcaption></figcaption></figure>

Rimuoviamo le due righe fonts.googleapis.com sopra evidenziate nella pagina base.html.

Idem nella pagina login.html:

<figure><img src="../../../.gitbook/assets/image (1679).png" alt=""><figcaption></figcaption></figure>

Possiamo impostare un certificato SSL nel file config.json:

<figure><img src="../../../.gitbook/assets/image (1680).png" alt=""><figcaption></figcaption></figure>

<figure><img src="../../../.gitbook/assets/image (1681).png" alt=""><figcaption></figcaption></figure>

In caso reale dobbiamo cambiare il listen\_url di admin\_server con il dominio oppure l'IP del server gophish e la porta su cui ascolta.

Come listen\_url di phish\_server abbiamo invece il server web che hosterà la pagina o le pagine web malevole della campagna di phishing. Anche qui possiamo impostare sulla porta 80 o la 443 se vogliamo inserire anche i dati di un certificato (use\_tls: true e valorizzare anche cert\_path e key\_path).

Infine ci sono i parametri di connessione o quanto meno nome server e path di un file DB sqllite3 che conterrà tutte le informazioni che riguardano la campagna phishing (tra cui chi ha cliccato e chi ha inserito le credenziali).

Per avviare gophish cliccare su gophish.exe.

<figure><img src="../../../.gitbook/assets/image (1682).png" alt=""><figcaption></figcaption></figure>

Ora andiamo su firefox alla pagina di login di gophish https://localhost:3333

<figure><img src="../../../.gitbook/assets/image (1683).png" alt=""><figcaption></figcaption></figure>

Se dovessimo avere problemi di caricamento nonostante abbiamo tolto i riferimenti ai font google nei file html, possiamo andare nel file config.json e impostare use\_tls a false per il nostro admin\_server:

<figure><img src="../../../.gitbook/assets/image (1684).png" alt=""><figcaption></figcaption></figure>

Ora va, ci loggiamo con admin / phishingpasswd

<figure><img src="../../../.gitbook/assets/image (1685).png" alt=""><figcaption></figcaption></figure>

<figure><img src="../../../.gitbook/assets/image (1686).png" alt=""><figcaption></figcaption></figure>

***

## Part 2 (continua il precedente video)

<figure><img src="../../../.gitbook/assets/image (1687).png" alt=""><figcaption></figcaption></figure>

Cosa fare per creare una campagna phishing?

### Creare un Sending Profile

Ossia la mail che invierà i messaggi di phishing:

<figure><img src="../../../.gitbook/assets/image (1688).png" alt=""><figcaption></figcaption></figure>

<figure><img src="../../../.gitbook/assets/image (1689).png" alt=""><figcaption></figcaption></figure>

Per verificare se nell'host che fa da server c'è una porta 25 running possiamo farlo con il comando:

```
netstat -ano
```

<figure><img src="../../../.gitbook/assets/image (1690).png" alt=""><figcaption></figcaption></figure>

La password per l'utente email è penetrationtesting

<figure><img src="../../../.gitbook/assets/image (1691).png" alt=""><figcaption></figcaption></figure>

Proviamo ad inviare una mail di test al nostro Intern di cui abbiamo accesso alla mail con Thunderbird:

<figure><img src="../../../.gitbook/assets/image (1692).png" alt=""><figcaption></figcaption></figure>

<figure><img src="../../../.gitbook/assets/image (1693).png" alt=""><figcaption></figcaption></figure>

<figure><img src="../../../.gitbook/assets/image (1694).png" alt=""><figcaption></figcaption></figure>

### Creare una lending page

<figure><img src="../../../.gitbook/assets/image (1695).png" alt=""><figcaption></figcaption></figure>

<figure><img src="../../../.gitbook/assets/image (1696).png" alt=""><figcaption></figcaption></figure>

Si può decidere di catturare i dati inseriti nel form e anche le password, e poi redirezionare l'utente a una pagina magari non più malevola così non sospetterebbe di nulla.

<figure><img src="../../../.gitbook/assets/image (1697).png" alt=""><figcaption></figcaption></figure>

<figure><img src="../../../.gitbook/assets/image (1698).png" alt=""><figcaption></figcaption></figure>

### Creare un template email

<figure><img src="../../../.gitbook/assets/image (1699).png" alt=""><figcaption></figcaption></figure>

<figure><img src="../../../.gitbook/assets/image (1700).png" alt=""><figcaption></figcaption></figure>

<figure><img src="../../../.gitbook/assets/image (1701).png" alt=""><figcaption></figcaption></figure>

<figure><img src="../../../.gitbook/assets/image (1702).png" alt=""><figcaption></figcaption></figure>

Si possono anche aggiungere dei file come allegati:

<figure><img src="../../../.gitbook/assets/image (1703).png" alt=""><figcaption></figcaption></figure>

<figure><img src="../../../.gitbook/assets/image (1704).png" alt=""><figcaption></figcaption></figure>

### Creare un gruppo e gli utenti&#x20;

<figure><img src="../../../.gitbook/assets/image (1705).png" alt=""><figcaption></figcaption></figure>

<figure><img src="../../../.gitbook/assets/image (1706).png" alt=""><figcaption></figcaption></figure>

Importiamo come New Group gli utenti che abbiamo individuato presenti nel CSV.

<figure><img src="../../../.gitbook/assets/image (1707).png" alt=""><figcaption></figcaption></figure>

### Creare una nuova campagna

<figure><img src="../../../.gitbook/assets/image (1708).png" alt=""><figcaption></figcaption></figure>

<figure><img src="../../../.gitbook/assets/image (1709).png" alt=""><figcaption></figcaption></figure>

Come URL dobbiamo impostare nil gophish listneer, ci dovrebbe essere stato segnalato nel cmd quando abbiamo avviate gophish.exe, in questo caso era la porta 80 di localhost appunto.

<figure><img src="../../../.gitbook/assets/image (1710).png" alt=""><figcaption></figcaption></figure>

<figure><img src="../../../.gitbook/assets/image (1711).png" alt=""><figcaption></figcaption></figure>

<figure><img src="../../../.gitbook/assets/image (1712).png" alt=""><figcaption></figcaption></figure>

<figure><img src="../../../.gitbook/assets/image (1713).png" alt=""><figcaption></figcaption></figure>

<figure><img src="../../../.gitbook/assets/image (1714).png" alt=""><figcaption></figcaption></figure>

<figure><img src="../../../.gitbook/assets/image (1715).png" alt=""><figcaption></figcaption></figure>

Questa è la pagina che appare al click sul link:

<figure><img src="../../../.gitbook/assets/image (1716).png" alt=""><figcaption></figcaption></figure>

