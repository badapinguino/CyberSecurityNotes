# Alternate Data Streams

<figure><img src="../../.gitbook/assets/image (11) (1).png" alt=""><figcaption></figcaption></figure>

è un ottimo modo per nascondere payload in file autentici in modo da rendere difficile l'individuazione da parte di amministratori, utenti o Anti Virus basati sulle sole firme.

## Esempio su ambiente Windows 7 VM

### Come funziona il resource stream e come ci si può scrivere ed accedere

<figure><img src="../../.gitbook/assets/image (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

<figure><img src="../../.gitbook/assets/image (2) (1) (1).png" alt=""><figcaption></figcaption></figure>

Creiamo un file legittimo di esempio.

<figure><img src="../../.gitbook/assets/image (3) (1) (1).png" alt=""><figcaption></figcaption></figure>

E inseriamo un testo base giusto per riempire lo spazio e lo salviamo sul Desktop:

<figure><img src="../../.gitbook/assets/image (4) (1) (1).png" alt=""><figcaption></figcaption></figure>

Quando apriamo un file vediamo il data stream, quindi in questo caso la stringa Hello World!

<figure><img src="../../.gitbook/assets/image (5) (1) (1).png" alt=""><figcaption></figcaption></figure>

Quando andiamo a vedere le proprietà/metadata del file invece andiamo a vedere il Resource Stream, e quindi andremo essenzialmente a creare un payload malevolo e lo inseriremo qui.

<figure><img src="../../.gitbook/assets/image (6) (1) (1).png" alt=""><figcaption></figcaption></figure>

Ma possiamo creare un file nascosto che verrà aperto automaticamente dal primo (test.txt) se lo scriviamo come secondo file a seguito di due punti (:), ossia come:

```
notepad test.txt:secret.txt
```

<figure><img src="../../.gitbook/assets/image (7) (1) (1).png" alt=""><figcaption></figcaption></figure>

<figure><img src="../../.gitbook/assets/image (8) (1) (1).png" alt=""><figcaption></figcaption></figure>

e possiamo scrivere all'interno così del file secret.txt che è nascosto dietro test.txt:

<figure><img src="../../.gitbook/assets/image (9) (1) (1).png" alt=""><figcaption></figcaption></figure>

Se salviamo e riapriamo il file presente sul Desktop vediamo solo il contenuto di text.txt che è vuoto:

<figure><img src="../../.gitbook/assets/image (10) (1) (1).png" alt=""><figcaption></figcaption></figure>

E anche se da cmd guardiamo il contenuto della cartella Desktop vediamo solo test.txt, che è 0 byte tra l'altro:

<figure><img src="../../.gitbook/assets/image (11) (1) (1).png" alt=""><figcaption></figcaption></figure>

Possiamo anche scrivere dentro il file test.txt:

<figure><img src="../../.gitbook/assets/image (12) (1).png" alt=""><figcaption></figcaption></figure>

E se lo riapriamo vediamo solo la parte visibile.

Se vogliamo accedere al file nascosto dobbiamo fare sempre con i due punti, che significa accedere al resource stream

<figure><img src="../../.gitbook/assets/image (13) (1).png" alt=""><figcaption></figcaption></figure>

<figure><img src="../../.gitbook/assets/image (14) (1).png" alt=""><figcaption></figcaption></figure>

<figure><img src="../../.gitbook/assets/image (15) (1).png" alt=""><figcaption></figcaption></figure>

### Attacco

Abbiamo il file binario di winPEAS che serve a fare local enumeration su sistema windows per identificare vulnerabilità che possono essere exploitate per poter fare privilege escalation.

Lo usiamo come esempio di payload, rinominandolo appunto payload e spostandolo in una cartella C:\Temp

<figure><img src="../../.gitbook/assets/image (16) (1).png" alt=""><figcaption></figcaption></figure>

<figure><img src="../../.gitbook/assets/image (17) (1).png" alt=""><figcaption></figcaption></figure>

<figure><img src="../../.gitbook/assets/image (18).png" alt=""><figcaption></figcaption></figure>

<figure><img src="../../.gitbook/assets/image (19).png" alt=""><figcaption></figcaption></figure>

Redirezioniamo il file payload.exe nel Resource Stream di un nuovo legittimo file:

<figure><img src="../../.gitbook/assets/image (20).png" alt=""><figcaption></figcaption></figure>

<figure><img src="../../.gitbook/assets/image (21).png" alt=""><figcaption></figcaption></figure>

Quello che un attaccante farebbe è inserire del contenuto nel file windowslog in modo da renderlo credibile e non sospetto, essendo un test noi ci scriviamo solo:

<figure><img src="../../.gitbook/assets/image (22).png" alt=""><figcaption></figcaption></figure>

<figure><img src="../../.gitbook/assets/image (23).png" alt=""><figcaption></figcaption></figure>

Possiamo anche cancellare il file payload.exe tanto ormai è contenuto nel resource stream di windowslog.txt.

Proviamo ora ad avviare il payload:

<figure><img src="../../.gitbook/assets/image (24).png" alt=""><figcaption></figcaption></figure>

Creiamo un symbolic link in system32 in modo da poter avviare il programma altrimenti non funziona:

<figure><img src="../../.gitbook/assets/image (25).png" alt=""><figcaption></figcaption></figure>

Ok dobbiamo farlo con un cmd con privilegi amministrativi:

<figure><img src="../../.gitbook/assets/image (26).png" alt=""><figcaption></figcaption></figure>

Ora, ogni qualvolta scriviamo wupdate nel terminale ci eseguirà il payload winpeas.exe:

<figure><img src="../../.gitbook/assets/image (27).png" alt=""><figcaption></figcaption></figure>

Potevamo fare lo stesso per un meterpreter payload generato con msfvenom.

è un buon modo che pochi conoscono per evitare la detection di antivirus molto semplici e personale IT e amministratori.
