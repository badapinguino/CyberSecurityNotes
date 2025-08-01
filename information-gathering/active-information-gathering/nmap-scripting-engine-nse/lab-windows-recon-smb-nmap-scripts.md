---
description: >-
  Laboratorio utile per utilizzare gli script di nmap utili a enumerare utenti,
  cartelle, gruppi, servizi su SMB alla porta 445
---

# Lab - Windows Recon: SMB Nmap Scripts

**Step 1:** Open the lab link to access the Kali machine.

![Content Image](https://assets.ine.com/lab/learningpath/5de2abda185766a6faa9758fdd41afb19dc68cd409f1cbc1eef6691e90135df1.jpg)

**Step 2:** Ping the target machine to see if it’s alive or not.

**Command:**

```
ping -c 5 demo.ine.local
```

![Content Image](https://assets.ine.com/lab/learningpath/52aa75e82cb67e554496bbf336118971e3c91d021e758d9076991d467bfbb00a.jpg)

We can observe that the target machine is alive and we have successfully sent and received all five packets.

**Step 3:** Run a Nmap scan against the target.

**Command:**

```
nmap demo.ine.local
```

![Content Image](https://assets.ine.com/lab/learningpath/6fc453d9d3814b2b2a13940c1eeb07cefb2f86a398eac548c8f945d0d8f0e2a4.jpg)

**Step 4:** We have discovered that multiple ports are open. SMB port 445 is also exposed. We will run the Nmap script to list the supported protocols and dialects of an SMB server.

**Command:**

```
nmap -p445 --script smb-protocols demo.ine.local
```

<img src="https://assets.ine.com/lab/learningpath/fe20e91723d31ced48543cc53df5b4afc14214c4cfee2949f1d81efecdf0509e.jpg" alt="Content Image" data-size="original">

**Step 5:** Running security mode script to return the information about the SMB security level.

**Command:**

```
nmap -p445 --script smb-security-mode demo.ine.local
```

![Content Image](https://assets.ine.com/lab/learningpath/c5201fdf4f5c1a40c01a5db15cce77ebbfaf9792f5896134ec4996f89ad57302.jpg)

We have tried to access the target SMB server using a guest user and we have received SMB security level information.

We can find more information from the following link: https://nmap.org/nsedoc/scripts/smb-security-mode.html

**Step 6:** We have the SMB server credentials i.e administrator:smbserver\_771. We will use it with Nmap script to scan the target to discover sensitive information.

Enumerating the users logged into a system through an SMB share with Nmap script. First, we won’t use any credentials to see the output.

**Command:**

```
nmap -p445 --script smb-enum-sessions demo.ine.local
```

![Content Image](https://assets.ine.com/lab/learningpath/78479427236965b99a965aa45681d53544dae2f649d52394e36e2bb9d379dd68.jpg)

We can observe that on the target machine we have discovered that user bob is logged into without any credentials.

This is possible because the target machine is running with the guest login enable configuration and it is a misconfiguration.

In case guest login is not enabled we can always use valid credentials of the target machine to discover the same information.

**Command:**

```
nmap -p445 --script smb-enum-sessions --script-args smbusername=administrator,smbpassword=smbserver_771 demo.ine.local
```

![Content Image](https://assets.ine.com/lab/learningpath/b5557486ac8d3bd803e95766134d0821838e265aa859c3b95d68e16885122cc0.jpg)

**Step 7:** Enumerating all available shares.

**Command:**

```
nmap -p445 --script smb-enum-shares demo.ine.local
```

![Content Image](https://assets.ine.com/lab/learningpath/ee7cd2df4ee1485d928742f1e292eb954bf4e7bea4b4fd2d73e15157fb4263ae.jpg)![Content Image](https://assets.ine.com/lab/learningpath/780bb6ec7013a2a7e2972cc10363cae594115fbde563dac147d7f5e6a2592319.jpg)

We can observe, in the output that we have accessed all the shares using guest users and we have received the permission of each folder or drive.

Also, we can notice that IPC$ share has read and write permissions.

[About IPC$ share](https://docs.microsoft.com/en-us/troubleshoot/windows-server/networking/inter-process-communication-share-null-session)

"The IPC$ share is also known as a null session connection. By using this session, Windows lets anonymous users perform certain activities, such as enumerating the names of domain accounts and network shares."

Scanning all shares using valid credentials to check the permissions.

**Command:**

```
nmap -p445 --script smb-enum-shares --script-args smbusername=administrator,smbpassword=smbserver_771 demo.ine.local
```

![Content Image](https://assets.ine.com/lab/learningpath/5ca48ca916874d9658426cc3d27563c85809c8fafcad29efd86a5a8333b0f998.jpg)![Content Image](https://assets.ine.com/lab/learningpath/6950302c9203afd25b6683806e6290fbe992c18a424b159a3cff60deb01d69be.jpg)![Content Image](https://assets.ine.com/lab/learningpath/eba30abb14fcc8de4b153a2f48dbba5f48e56dd4ad1d3cced7d840d2582c7180.jpg)

We can observe that the administrator user has read and write privilege to the entire C$. i.e C:\\

**Step 8:** Enumerate the windows users on a target machine.

**Command:**

```
nmap -p445 --script smb-enum-users --script-args smbusername=administrator,smbpassword=smbserver_771 demo.ine.local
```

![Content Image](https://assets.ine.com/lab/learningpath/e54b719a389fc8c36059adbf0c11e77dd295d2f05a35923cf3814ab330009fd5.jpg)

We can observe that there are three users present on the target machine. i.e Administrator, bob, Guest.

**Step 9:** Get information about the server statistics. It uses port 445 and port 139 to fetch the details.

**Command:**

```
nmap -p445 --script smb-server-stats --script-args smbusername=administrator,smbpassword=smbserver_771 demo.ine.local
```

![Content Image](https://assets.ine.com/lab/learningpath/7b034029c77dfff966490eed6771499d436f6cb1489f66a042c4b170975223ad.jpg)

We can notice that we have received failed logins, permission & system errors, and opened files and print jobs.

**Please note:** There is a possibility that the above output would be different in your case which is completely okay.

**Step 10:** Enumerating available domains on a target machine.

**Command:**

```
nmap -p445 --script smb-enum-domains --script-args smbusername=administrator,smbpassword=smbserver_771 demo.ine.local
```

![Content Image](https://assets.ine.com/lab/learningpath/b18e0cfaee4eec333230a7609129a541ddaf357d99d0173efb6421c85204bfef.jpg)

We have received the information about the built-in domain on the target machine.

**Step 11:** Enumerating available user groups on a target machine.

**Command:**

```
nmap -p445 --script smb-enum-groups --script-args smbusername=administrator,smbpassword=smbserver_771 demo.ine.local
```

![Content Image](https://assets.ine.com/lab/learningpath/26e311f07191cda3dcb997b74298d237cf6b613318086a25efe84517f178e0fb.jpg)

**Step 12:** Enumerating services on a target machine.

**Command:**

```
nmap -p445 --script smb-enum-services --script-args smbusername=administrator,smbpassword=smbserver_771 demo.ine.local
```

![Content Image](https://assets.ine.com/lab/learningpath/039114429816fb9698b47ffc8d4923c7569a88a7c51435a1343f786c6e9757ff.jpg)![Content Image](https://assets.ine.com/lab/learningpath/b639ab375e6f9ba19172f85b419981f1c06b960a6f8759a7650e1ce916439732.jpg)![Content Image](https://assets.ine.com/lab/learningpath/561d0d4afc333efdae181fe5d2518636f409bba1e04c6c5e8fe79c9a49e5d369.jpg)

**Step 13:** Enumerating all the shared folders and drives then running the ls command (The ls command is used to list files or directories, similarly dir in windows) on all the shared folders.

**Command:**

```
nmap -p445 --script smb-enum-shares,smb-ls --script-args smbusername=administrator,smbpassword=smbserver_771 demo.ine.local
```

![Content Image](https://assets.ine.com/lab/learningpath/4f4b18e5ea7d2612c0fd52e5aefd8fc4dc585596cad5282d13dcff2a08715fcb.jpg)![Content Image](https://assets.ine.com/lab/learningpath/681e9f3ee069d88cd8bb03150de8f6f3058705983892d460669d598001ad84b5.jpg)![Content Image](https://assets.ine.com/lab/learningpath/66c318eeba2da72caf2c583fc44bf967cc2fab7c0f7473e496858667de532f6d.jpg)![Content Image](https://assets.ine.com/lab/learningpath/35f8973c869ea5b592862172544b5b9c5b74e3a40c84ab1fb2b2ebc20f9bb453.jpg)![Content Image](https://assets.ine.com/lab/learningpath/693a0a1e7f878f587de75060098d1aedc709bee4527faf1fb44aa4f76516b62a.jpg)

## Conclusion

In this lab, we learned how to run Nmap scripts to enumerate the Windows target machine's SMB service.
