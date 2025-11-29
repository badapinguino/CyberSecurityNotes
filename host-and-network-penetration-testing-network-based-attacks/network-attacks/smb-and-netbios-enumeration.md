---
description: Windows Enumeration
---

# SMB & NetBIOS Enumeration

<figure><img src="../../.gitbook/assets/image (1) (1).png" alt=""><figcaption></figcaption></figure>

<figure><img src="../../.gitbook/assets/image (2) (1).png" alt=""><figcaption></figcaption></figure>

<figure><img src="../../.gitbook/assets/image (3) (1).png" alt=""><figcaption></figcaption></figure>

<figure><img src="../../.gitbook/assets/image (5) (1).png" alt=""><figcaption></figcaption></figure>

## Verifica ambiente di laboratorio

<figure><img src="../../.gitbook/assets/image.png" alt=""><figcaption></figcaption></figure>

Se guardiamo nel file hosts abbiamo due macchine:&#x20;

* demo.ine.local che è raggiungibile dal nostro sistema Kali attaccante
* demo1.ine.local che non è direttamente raggiungibile dal nostro sistema ma sarà necessario effettuare un Pivoting per raggiungerla

<figure><img src="../../.gitbook/assets/image (1).png" alt=""><figcaption></figcaption></figure>

### Nmap scan sul primo target

<figure><img src="../../.gitbook/assets/image (2).png" alt=""><figcaption></figcaption></figure>

Possiamo vedere che abbiamo Netbios-SSN sulla porta 139, e SMB running sulla porta 445.

## Netbios enumeration

## Usiamo un tool nbtscan

SW free per scan delle reti con Netbios.

```
nbtscan
whatis nbtscan
nbtscan 10.4.30.0/24
```

<figure><img src="../../.gitbook/assets/image (3).png" alt=""><figcaption></figcaption></figure>

<figure><img src="../../.gitbook/assets/image (4).png" alt=""><figcaption></figcaption></figure>

Se proviamo a fare lo scan solo dell'IP del primo server demo non funziona, dobbiamo farlo dell'intera rete:

<figure><img src="../../.gitbook/assets/image (5).png" alt=""><figcaption></figcaption></figure>

