# Web Server Enumeration

<figure><img src="../.gitbook/assets/image (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

Primi passi per avviare metsploit e configurarlo brevemente per questa sessione:

```
service postgresql start
msfconsole
workspace -a Web_Enum
setg RHOSTS <IP target>
setg RHOST <IP target>
search type:auxiliary name:http
```

<figure><img src="../.gitbook/assets/image (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

## HTTP version enumeration

```
use auxiliary/scanner/http/http_version
// se è HTTPS imposta la variabile SSL a true: set SSL true
run
```

<figure><img src="../.gitbook/assets/image (2) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

Ci viene restituita la versione del web server e il sistema operativo

## HTTP header enumeration

```
use auxiliary/scanner/http/http_header
run
```

<figure><img src="../.gitbook/assets/image (3) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

Ci dà info sul server version e il fatto che il linguaggio è html. Simile al risultato di http\_version

## Analizzare il robots.txt

robots.txt elenca le pagine che non devono essere indicizzate nei search engine

```
use auxxiliary/scanner/http/robots_txt
run
```

<figure><img src="../.gitbook/assets/image (4) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

Abbiamo scoperto che lo sviluppatore del server non voleva che qualcuno accedesse alla directory data e alla dir secure.

Scopriamo cosa contiene scaricando con curl:

```
curl http://<IP>/data/
curl http://<IP>/secure/
```

<figure><img src="../.gitbook/assets/image (5) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

<figure><img src="../.gitbook/assets/image (7) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

Ci dice che non abbiamo accesso perché il browser ci avrebbe chiesto username e password e noi non le abbiamo inserite. Possiamo provare a fare un bruteforce.

## Directory bruteforce

Si fa bruteforce di un webserver cercando tutte le directory con un dizionario per identificare quali directory sono presenti nel webserver, anche quelle che normalmente non sono accessibili via link nelle pagine.

<pre><code>use auxiliary/scanner/http/dir_scanner
show options  //nella variabile DICTIONARY c'è il file che contiene le parole usate come directory
<strong>// se vogliamo possiamo eseguire: set DICTIONARY /usr/share/metasploit-framework/data/wordlists/directory.txt
</strong><strong>run
</strong></code></pre>

<figure><img src="../.gitbook/assets/image (8) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

<figure><img src="../.gitbook/assets/image (9) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

Ci dà anche qualche informazione sul fatto che alcune pagine per essere accedute hanno bisogno di una WebDav authentication, e quindi probabilmente ci viene data anche qualche info sulla tipologia di applicazione usata (WebDav).

## File bruteforcing

Facciamo un bruteforce per trovare dei file, invece che directory come fatto prima.

```
use auxiliary/scanner/http/files_dir
show options //si può modificare il DICTIONARY, EXTensions dei file e il PATH
run
```

<figure><img src="../.gitbook/assets/image (10) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

In questo caso viene impostato come dictionary un file di wmap, che è un modulo per metasploit framework usato per fare vulnerability scanning per webapplication.

<figure><img src="../.gitbook/assets/image (11) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

Sono state trovati un po' di file in particolare index.html e test.php. Tutti i 301 invece sono redirect.

<figure><img src="../.gitbook/assets/image (12) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

Andiamo ora a fare bruteforce della cartella secure che ci restituisce 401 quindi è dietro ad una autenticazione.

## Bruteforce login pagine HTTP

Usiamo un modulo che ci consente di fare bruteforce su un authentication form

```
use auxiliary/scanner/http/http_login
set AUTH_URI /secure/    //dove secure è <pagina con autenticazione>
unset USERPASS_FILE    // meglio non farlo perché a me senza non ha funzionato nel lab
run
```

<figure><img src="../.gitbook/assets/image (13) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

<figure><img src="../.gitbook/assets/image (15) (1) (1) (1) (1) (1) (1) (1) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

I parametri che andiamo a modificare sono: AUTH\_URI che è il path della directory che richiede il form di autenticazione, e andiamo a rimuovere l'impostazione di default a USERPASS\_FILE perché utilizzeremo i già impostati PASS\_FILE e USER\_FILE.

<figure><img src="../.gitbook/assets/image (16) (1) (1) (1) (1) (1) (1) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

<figure><img src="../.gitbook/assets/image (17) (1) (1) (1) (1) (1) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

Non abbiamo trovato nessuna credenziale, quindi proviamo a cambiare le liste usate per qualcosa di più robusto:

```
set USER_FILE /usr/share/metasploit-framework/data/wordlists/namelist.txt
set PASS_FILE /usr/share/metasploit-framework/data/wordlists/unix_passwords.txt
set VERBOSE false  //cambiamo anche il VERBOSE per evitare che ci faccia vedere tutti i tentativi
run
```

<figure><img src="../.gitbook/assets/image (18) (1) (1) (1) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

<figure><img src="../.gitbook/assets/image (19) (1) (1) (1) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

Non sappiamo perché ma ci ha dato un errore

<figure><img src="../.gitbook/assets/image (20) (1) (1) (1) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

Ma comunque anche lasciandolo eseguire non trova nulla quindi terminiamo e usiamo un altro modulo per trovare le credenziali.

## Modulo che utilizza il UserDir directive presente in Apache per determinare quali utenti esistono e quali no

```
use auxiliary/scanner/http/apache_userdir_enum
info
set USER_FILE /usr/share/metasploit-framework/data/wordlists/common_users.txt
run
```

<figure><img src="../.gitbook/assets/image (21) (1) (1) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

<figure><img src="../.gitbook/assets/image (22) (1) (1) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

Abbiamo trovato così qualche utente del webserver, e possiamo usare questa informazione inserendola come utente nel bruteforce del login. Ad esempio potrebbe essere interessante l'utente rooty.

## Bruteforce login pagine HTTP con un utente trovato con il modulo precedente di Apache UserDir Enum

Usiamo l'utente rooty e per usarlo lo inseriamo in un file txt che verrà usato in input al modulo (impostandolo come parametro USER\_FILE)

```
use auxiliary/scanner/http/http_login
echo "rooty" > user.txt
set USER_FILE /root/user.txt
run
```

<figure><img src="../.gitbook/assets/image (24) (1) (1) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

E ci viene restituito nuovamente un errore quindi per il momento imposta VERBOSE a true per vedere cosa sta succedendo.

<figure><img src="../.gitbook/assets/image (25) (1) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

Sembra ci venga restituito un errore nel momento in cui raggiungiamo una certa password. Potremmo provare con altri password file e altri utenti.

Comunque sia qui voleva dimostrare che ci sono molti auxiliary modules che possiamo usare e che possono essere utili in vari contesti. Tutti i moduli mostrati possono essere usati su qualsiasi webserver come Apache, Engine x (nginx), Microsoft IIS, ricordiamoci però di impostare la porta corretta e SSL se il server usa HTTPS.

## Effettuare un upload di un file sul webserver

```
use auxiliary/scanner/http/http_put
set RHOSTS victim-1
set PATH /data
set FILENAME test.txt
set FILEDATA "Welcome To AttackDefense"
run
```

### Cancellare il file caricato

```
use auxiliary/scanner/http/http_put
set RHOSTS victim-1
set PATH /data
set FILENAME test.txt
set ACTION DELETE
run
```
