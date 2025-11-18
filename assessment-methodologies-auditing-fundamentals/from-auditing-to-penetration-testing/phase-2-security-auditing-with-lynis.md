# Phase 2 - Security Auditing with Lynis

Automatizzeremo un audit security su un server linux, implementeremo le contromisure.

<figure><img src="../../.gitbook/assets/image (706).png" alt=""><figcaption></figcaption></figure>

<figure><img src="../../.gitbook/assets/image (707).png" alt=""><figcaption></figcaption></figure>

## Demo

<figure><img src="../../.gitbook/assets/image (708).png" alt=""><figcaption></figcaption></figure>

<figure><img src="../../.gitbook/assets/image (709).png" alt=""><figcaption></figcaption></figure>

<figure><img src="../../.gitbook/assets/image (710).png" alt=""><figcaption></figcaption></figure>

<figure><img src="../../.gitbook/assets/image (711).png" alt=""><figcaption></figcaption></figure>

<figure><img src="../../.gitbook/assets/image (712).png" alt=""><figcaption></figcaption></figure>

Scarica il tar.gz, ma annulla il download e copia l'url in modo da usare wget da terminale sul server linux su cui fare security audit.

<figure><img src="../../.gitbook/assets/image (713).png" alt=""><figcaption></figcaption></figure>

<figure><img src="../../.gitbook/assets/image (714).png" alt=""><figcaption></figcaption></figure>

<figure><img src="../../.gitbook/assets/image (715).png" alt=""><figcaption></figcaption></figure>

<figure><img src="../../.gitbook/assets/image (716).png" alt=""><figcaption></figcaption></figure>

<figure><img src="../../.gitbook/assets/image (717).png" alt=""><figcaption></figcaption></figure>

<figure><img src="../../.gitbook/assets/image (718).png" alt=""><figcaption></figcaption></figure>

### Per eseguire l'audit sul sistema stesso

```
./linis audit system
```

<figure><img src="../../.gitbook/assets/image (719).png" alt=""><figcaption></figcaption></figure>

Ed iniziera ad effettuare una serie di check:

<figure><img src="../../.gitbook/assets/image (720).png" alt=""><figcaption></figcaption></figure>



Se volessimo sapere quali sono i check che fa possiamo controllare alla voce Controls sul sito ufficiale:

<figure><img src="../../.gitbook/assets/image (721).png" alt=""><figcaption></figcaption></figure>

<figure><img src="../../.gitbook/assets/image (722).png" alt=""><figcaption></figcaption></figure>

<figure><img src="../../.gitbook/assets/image (723).png" alt=""><figcaption></figcaption></figure>

Ci presenta tutte le vulnerabilità e suggerimenti, con relativo link al controllo per come risolverli.

<figure><img src="../../.gitbook/assets/image (724).png" alt=""><figcaption></figcaption></figure>

<figure><img src="../../.gitbook/assets/image (725).png" alt=""><figcaption></figcaption></figure>

Ad esempio qui ci viene detto anche cosa correggere.



<figure><img src="../../.gitbook/assets/image (727).png" alt=""><figcaption></figcaption></figure>

Ha copiato su un txt i controlli definiti nella policy nella scorsa phase 1.

Ora dobbiamo trovare un linguaggio per comunicare a Lynis quali sono questi nostri controlli da verificare. Possiamo farlo attraverso i controlli, precisamente con i control IDs che corrispondono ai nostri controlli. Prendiamo ad es. quello per il malware scanner:

<figure><img src="../../.gitbook/assets/image (728).png" alt=""><figcaption></figcaption></figure>

<figure><img src="../../.gitbook/assets/image (729).png" alt=""><figcaption></figcaption></figure>

<figure><img src="../../.gitbook/assets/image (730).png" alt=""><figcaption></figcaption></figure>

<figure><img src="../../.gitbook/assets/image (731).png" alt=""><figcaption></figcaption></figure>

### Effettuare dei test di controlli specifici

```
./lynis audit system --tests "HRDN-7230"
```

<figure><img src="../../.gitbook/assets/image (732).png" alt=""><figcaption></figcaption></figure>

Performed ce n'è uno solo.

<figure><img src="../../.gitbook/assets/image (733).png" alt=""><figcaption></figcaption></figure>

### Salvare i risultati di lynis

<figure><img src="../../.gitbook/assets/image (734).png" alt=""><figcaption></figcaption></figure>

<figure><img src="../../.gitbook/assets/image (735).png" alt=""><figcaption></figcaption></figure>

<figure><img src="../../.gitbook/assets/image (736).png" alt=""><figcaption></figcaption></figure>

```
./lynis audit system --auditor "Daniele"
cat /var/log/lynis-report.dat
```



### Altre opzioni utili

* \--quiet   Non mostra l'output sullo schermo
* \--quick    Skippa conferma di alcuni check se richiesta





### Esempio di analisi di un controllo non presente e remediaton

<figure><img src="../../.gitbook/assets/image (737).png" alt=""><figcaption></figcaption></figure>

<figure><img src="../../.gitbook/assets/image (738).png" alt=""><figcaption></figcaption></figure>

Siccome il controllo è fallito perché non c'è un malware scan, come remediaton andiamo ad installarne uno.

```
sudo apt-get install clamav -y
sudo apt-get install chrootkit
sudo apt-get install rkhunter
```

<figure><img src="../../.gitbook/assets/image (739).png" alt=""><figcaption></figcaption></figure>

E lo abbiamo installato.

<figure><img src="../../.gitbook/assets/image (740).png" alt=""><figcaption></figcaption></figure>

Stranamente però se rifacciamo lo scan ci dice ancora che non è installato.

Però guardando i suggerimenti ci dice di installare altri antivirus come ad esempio rkhunter o chkrootkit:

<figure><img src="../../.gitbook/assets/image (741).png" alt=""><figcaption></figcaption></figure>

Quindi proviamo ad installare chkrootkit

<figure><img src="../../.gitbook/assets/image (742).png" alt=""><figcaption></figcaption></figure>

Ma ancora non ci vede l'anti malware, allora proviamo ad installare rkhunter.

Continua a non vederlo, quindi lo rilancia senza il check specifico per il malware e controlla dopo tutti i check. Così funziona e vede l'anti malware software.

<figure><img src="../../.gitbook/assets/image (743).png" alt=""><figcaption></figcaption></figure>

&#x20;![](<../../.gitbook/assets/image (744).png>)

Stranamente se rieseguiamo il test del controllo specifico ancora non viene visto, boh molto strano.
