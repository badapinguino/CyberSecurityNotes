# Optimizing Nmap Scans

Rallentare lo scan serve nel caso in cui non vogliamo essere individuati nella rete.

I timing template sono da T0 a T5 e hanno dei nickname, ad esempio 0 è paranoid che viene usato per evadere gli IDS.

<figure><img src="../../.gitbook/assets/image (18) (1).png" alt=""><figcaption></figcaption></figure>

è possibile anche usare --scan-delay per dire quanto tempo deve passare tra ogni pacchetto inviato

## Host timeout

\--host-timeout utile quando ci sono diversi host scansionati come in host discovery, dopo un tot di tempo passa all'host successivo

<figure><img src="../../.gitbook/assets/image (1) (1) (1) (1) (1) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

con host timeout possiamo specificare secondi, minuti o ore

Fare attenzione ad usare questa opzione perché alcuni host ci mettono di più a rispondere e se impostiamo un timeout troppo basso potremmo perderci qualche host.

```
nmap --host-timeout 30s <IP>/24
```

## Scan delay

between probes

L'opzione serve a impostare un ritardo tra ogni pacchetto inviato

Per essere molto stealthy consiglia di impostarlo attorno ai 50 secondi.

```
nmap --scan-delay 15s <IP>
```

## Time templates

Più comodo e meglio rispetto a usare l'opzione scan-delay, meglio usare sneaky.

L'obiettivo di usare sneaky è evitare di essere individuati da un IDS.&#x20;

* T0: paranoid
* T1: sneaky - sembra quasi traffico normale talmente è frammentato l'invio dei pacchetti
* T2: polite
* T3: normal
* T4: aggressive
* T5: insane

