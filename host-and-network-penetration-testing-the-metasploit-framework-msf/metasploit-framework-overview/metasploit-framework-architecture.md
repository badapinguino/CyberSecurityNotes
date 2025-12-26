# Metasploit Framework Architecture

<figure><img src="../../.gitbook/assets/image (9) (1) (1).png" alt=""><figcaption></figcaption></figure>

Le librerie hanno una funzionalità analoga alle shared objects in linux o dll in windows. Essenzialmente ti permettono di estendere le funzionalità, contengono il codice necessario per estendere queste funzionalità, come ad esempio creare una socket TCP o in generale operazioni base a livello di sistema operativo per input/output.

<figure><img src="../../.gitbook/assets/image (1) (1) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

Exploit sfrutta la vulnerabilità e si porta al suo interno il payload, il payload è l'operazione che vogliamo venga eseguita dopo aver avuto accesso alla macchina. Ad esempio un payload può essere un comando o una reverse shell.

Gli encoders sono utili per offuscare il payload ed evitare venga identificato e bloccato dagli antivirus.

<figure><img src="../../.gitbook/assets/image (2) (1) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

<figure><img src="../../.gitbook/assets/image (3) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

<figure><img src="../../.gitbook/assets/image (4) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

Dato che il meterpreter payload è eseguito in memoria e non viene scaricato sulla vittima, questo permette di non lasciare traccia dell'esecuzione sulla vittima.

<figure><img src="../../.gitbook/assets/image (5) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

Per navigare nel MSF File System possiamo navigare su linux alla cartella:

```
/usr/share/metasploit-framework/
```

<figure><img src="../../.gitbook/assets/image (6) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

Nel secondo caso si intendono user custom modules.

## Navighiamo i moduli MSF e la sua struttura

<figure><img src="../../.gitbook/assets/image (7) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

<figure><img src="../../.gitbook/assets/image (8) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

Ogni cartella dei moduli contiene sottocartelle per ogni sottocategoria del modulo, ad esempio per auxiliary:

<figure><img src="../../.gitbook/assets/image (9) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

E così via, ad esempio se siamo nella fase di information gathering vogliamo usare degli scanner:

<figure><img src="../../.gitbook/assets/image (11) (1) (1).png" alt=""><figcaption></figcaption></figure>

Ad esempio degli scanner ftp:

<figure><img src="../../.gitbook/assets/image (10) (1) (1).png" alt=""><figcaption></figcaption></figure>

E qui c'è l'elenco di tutti i moduli di scanner ftp che sono disponibili.

Se devo scoprire gli host in una rete invece posso andare su discovery:

<figure><img src="../../.gitbook/assets/image (12) (1).png" alt=""><figcaption></figcaption></figure>

Se vogliamo fare della enumerazione smb, possiamo guardare tutti gli SMB auxiliary modules che ci consentono di enumerare le info smb su un target:

<figure><img src="../../.gitbook/assets/image (13) (1).png" alt=""><figcaption></figcaption></figure>

### Moduli exploit

Sono ordinati in base al sistema operativo del target

<figure><img src="../../.gitbook/assets/image (14) (1).png" alt=""><figcaption></figcaption></figure>

Se ad esempio sto cercando un exploit per Linux:

<figure><img src="../../.gitbook/assets/image (15) (1).png" alt=""><figcaption></figcaption></figure>

Ecco che sono sottocategorizzati per servizio o tipo di exploit.

Ad esempio per gli exploit del servizio mysql su linux:

<figure><img src="../../.gitbook/assets/image (16) (1).png" alt=""><figcaption></figcaption></figure>

Per ftp:

<figure><img src="../../.gitbook/assets/image (17) (1).png" alt=""><figcaption></figcaption></figure>

Per SO Windows:

<figure><img src="../../.gitbook/assets/image (18) (1).png" alt=""><figcaption></figcaption></figure>

Ancora vediamo una sottocategorizzazione per servizio o applicazione che è running su windows.

Ad esempio per MSsql questi sono i moduli exploit disponibili per windows:

<figure><img src="../../.gitbook/assets/image (19) (1).png" alt=""><figcaption></figcaption></figure>

### Encoders

Possiamo vedere tutti gli encoders che ci permettono di offuscare il payload divisi per architettura del sistema operativo o linguaggio:

<figure><img src="../../.gitbook/assets/image (20) (1).png" alt=""><figcaption></figcaption></figure>

Ad esempio se andiamo su x86 (architettura a 32bit) possiamo vedere i vari encoders tra cui il shikata\_ga\_nai utilizzato solitamente per Windows:

<figure><img src="../../.gitbook/assets/image (21) (1).png" alt=""><figcaption></figcaption></figure>

***

## Moduli custom

Per inserire moduli custom possiamo andare nella nostra cartella home, dalle opzioni selezionare la visualizzazione delle hidden folders, e poi possiamo andare sulla cartella .msf4/modules/ ed eventualmente caricare nostri moduli qui. Suddividendoli per sistema operativo / programma o servizio

<figure><img src="../../.gitbook/assets/image (22) (1).png" alt=""><figcaption></figcaption></figure>

***

Stessa navigazione si può vedere sulla interfaccia testuale:

<figure><img src="../../.gitbook/assets/image (23) (1).png" alt=""><figcaption></figcaption></figure>
