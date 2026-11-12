# Windows Password Hashes

<figure><img src="../../.gitbook/assets/image (2114).png" alt=""><figcaption></figcaption></figure>

<figure><img src="../../.gitbook/assets/image (2115).png" alt=""><figcaption></figcaption></figure>

Prima si fa privilege escalation, e poi con la sessione amministrativa si fa il dump delle credenziali.

Tool come mimikatz vanno a leggere la cache del processo LSASS che interagisce con LSA per avere una copia degli hash NTLM.

<figure><img src="../../.gitbook/assets/image (2116).png" alt=""><figcaption></figcaption></figure>

<figure><img src="../../.gitbook/assets/image (2117).png" alt=""><figcaption></figcaption></figure>

<figure><img src="../../.gitbook/assets/image (2118).png" alt=""><figcaption></figcaption></figure>

Non hanno password salts, quindi possono essere craccate con brute force e rainbow attack.

<figure><img src="../../.gitbook/assets/image (2119).png" alt=""><figcaption></figcaption></figure>

