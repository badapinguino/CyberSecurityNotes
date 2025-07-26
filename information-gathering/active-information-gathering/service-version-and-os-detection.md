# Service Version & OS Detection

Per identificare target network range devo usare ifconfig e trovare l'indirizzo di rete su cui fare la scansione

```
nmap -sn <IP>/24 //identificare host attivi sulla rete target a cui siamo connessi
```

Approfondiamo scansionando le porte dell'ip target

```
nmap -T4 -sS -p- <IP>
```

Ora cerchiamo di fare una service version detection su queste porte trovate

```
nmap -T4 -sS -sV -p- <IP>
```

Ora cerchiamo anche di identificare anche il sistema operativo

```
nmap -T4 -sS -sV -O -p- <IP>
```

Non è detto che nmap riesca ad identificare l'OS con certezza, ma potrebbe restituirci solo il TCP/IP fingerprint, da cui però possiamo ricavare qualche informazione come nel caso sotto il fatto che sia linux

<figure><img src="../../.gitbook/assets/image (128).png" alt=""><figcaption></figcaption></figure>

Per far fare una identificazione più aggressiva dell'OS possiamo usare il flag --osscan-guess

```
nmap -T4 -sS -sV -O -osscan-guess -p- <IP>
```

<figure><img src="../../.gitbook/assets/image (129).png" alt=""><figcaption></figcaption></figure>

In questo caso si riferisce al kernel linux, con windows sarebbe la versione di windows e anche la build.

Nel caso in cui non abbiamo delle versioni dei servizi accurate, possiamo fare lo stesso per la service version detection scan con il flag: --version-intensity con livello da 0 a 9, dove il più alto significa che è il più accurato possibile per quanto possa stabilire nmap.

```
/nmap -T4 -sS -sV --version-intensity 8 -O -osscan-guess -p- <IP>
```

<figure><img src="../../.gitbook/assets/image (130).png" alt=""><figcaption></figcaption></figure>

Dato che nmap non è in grado di trovare la distro linux, questo verrà fatto nella parte di corso di Enumeration
