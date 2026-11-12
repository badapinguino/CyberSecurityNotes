# Phase 3 - Conduct Penetration Test

<figure><img src="../../.gitbook/assets/image (745).png" alt=""><figcaption></figcaption></figure>

Faremo dei pentest molto base sui risultati degli audit.

<figure><img src="../../.gitbook/assets/image (746).png" alt=""><figcaption></figcaption></figure>

<figure><img src="../../.gitbook/assets/image (747).png" alt=""><figcaption></figcaption></figure>

Le recomendations sarnno legate a quanto era scritto nell'audit report dicendo avete fatto le azioni correttive ma non hanno funzionato quindi vi consigliamo di fare questo e quest'altro.

## Demo

Come pentester andremo a testare che siano stati implementati i controlli che avevamo evidenziato nella Phase 2 partendo dalle policy:

<figure><img src="../../.gitbook/assets/image (748).png" alt=""><figcaption></figcaption></figure>

Quindi password-based authentication, complessità delle password.



Per far ciò useremo un tool hydra per testare l'utente root con delle password semplici via ssh:

```
hydra -l root -P /usr/share/seclists/Passwords/xeto-net-10-million-passwords.txt ssh://<TARGET IP>:<TARGET PORT> -t 2 -v    //
```

<figure><img src="../../.gitbook/assets/image (749).png" alt=""><figcaption></figcaption></figure>

Già il fatto che ci sia scritto che password authentication è supported significa che non è stata fatta la remediation come da policy (disabilitazion SSH via password, utilizzo della sola autenticazione con key; ed anche il fatto che il root user dovrebbe essere disabilitato).

Attendiamo che finisca di fare il brute force per capire se effettivamente riusciamo ad accedere al server oppure se le password sono abbastanza sicure.



Se hydra ci sta mettendo troppo si può interrompere con CTRL+C e poi cambiare il numero di thread e riprendere da dove aveva terminato con il flag "-I" (i maiuscola).

```
hydra -l root -P /usr/share/seclists/Passwords/xeto-net-10-million-passwords.txt ssh://<TARGET IP>:<TARGET PORT> -t 4 -I
```

Usando la wordlist stiamo testando la password complexity perché se riusciamo a trovare delle password significa che non statto usando delle password complesse.



<figure><img src="../../.gitbook/assets/image (750).png" alt=""><figcaption></figcaption></figure>

Abbiamo trovato la password per il root user. Questo significa che l'organizzazione non sta usando password complesse.

### Remediation, suggerimenti per

Come redemediation possono o cambiare password più sicure oppure cambiare la porta di default SSH e disabilitare il root login:

<figure><img src="../../.gitbook/assets/image (751).png" alt=""><figcaption></figcaption></figure>

<figure><img src="../../.gitbook/assets/image (752).png" alt=""><figcaption></figcaption></figure>

<figure><img src="../../.gitbook/assets/image (753).png" alt=""><figcaption></figcaption></figure>

Oppure possiamo limitare il rate dei tentativi di login (rate limit).

Per far ciò dobbiamo entrare nel firewall

<figure><img src="../../.gitbook/assets/image (754).png" alt=""><figcaption></figcaption></figure>

Non abbiamo installati fw su questo server quindi lo installiamo e rendiamo sicuri alcuni servizi come ssh.



Ecco a cosa serve il pentest alla fine di un security audit.

