# Directory Enumeration with Gobuster (Lab)

## Overview

Web Application Pentesting Tools can prove to be very helpful while performing penetration testing. In this lab exercise, we will take a look at how to use Gobuster to perform directory enumeration on the Mutillidae web application.

In this lab environment, you will be provided with GUI access to a Kali machine. The target machine accessible at **demo.ine.local** hosts the mutillidae \[https://github.com/webpwnized/mutillidae] web application.

**Objective:** Perform directory enumeration with Gobuster.

## Solutions

### **Step 1:** Open the lab link to access the Kali machine.

<figure><img src="https://assets.ine.com/lab/learningpath/dd9fc4212fd856222a59099aab2054217703c99d671b9a67314f8a60f70ad0b4.jpg" alt=""><figcaption></figcaption></figure>

### **Step 2:** Run an Nmap scan on the target to find open ports.

```
nmap demo.ine.local
```

<figure><img src="https://assets.ine.com/lab/learningpath/1cbe794fdaba590a97f6a7d81ea099e5f3375f2d44cef3c93353b8615dcd9564.jpg" alt=""><figcaption></figcaption></figure>

Port 80 and Port 3306 are open.

### **Step 3:** Start Firefox and navigate to the URL: \[http://demo.ine.local]

<figure><img src="https://assets.ine.com/lab/learningpath/bc455490ce3910d99f37f83c563efe7d20bf2d22b8e329f29a81f265f843ec1f.jpg" alt=""><figcaption></figcaption></figure>

An instance of Mutillidae is running at port 80 of the target.

### **Step 4:** Start a terminal and run the gobuster command to get the available options in the gobuster tool.

```
gobuster
```

<figure><img src="https://assets.ine.com/lab/learningpath/f1497a69e0d3a84ec264c8b94aebda776a04194ca17b1b79161f6cfb79c6d882.jpg" alt=""><figcaption></figcaption></figure>

Check the available commands for dir mode in gobuster.

```
gobuster dir --help
```

<figure><img src="https://assets.ine.com/lab/learningpath/c3e2a3cffa5431559b168f5c658c012732bbfd7b94532b92b77a9d16f1070a3a.jpg" alt=""><figcaption></figcaption></figure>

### **Step 5:** Run the gobuster while passing the URL and wordlist as an argument.

```
gobuster dir -u http://demo.ine.local -w /usr/share/wordlists/dirb/common.txt
```

**Note:** -u flag is used to specify the URL of the target whereas the -w flag is used to specify the wordlist with its full path.

<figure><img src="https://assets.ine.com/lab/learningpath/fa7bffeac49118aead5c85a74d78d4b1bb0b5f68ff937f3443894a57c0dd8581.jpg" alt=""><figcaption></figcaption></figure>

### **Step 6:** Run the gobuster scan while ignoring the 403 and 404 status codes.

```
gobuster dir -u http://demo.ine.local -w /usr/share/wordlists/dirb/common.txt -b 403,404
```

**Note:** The -b flag is used to specify the status codes which has to be ignored.

<figure><img src="https://assets.ine.com/lab/learningpath/27e05f117aa7129755397f16ab7c66237adc0b8715f11c9fb43a5aded1027261.jpg" alt=""><figcaption></figcaption></figure>

### **Step 7:** Run the gobuster scan while ignoring 403 and 404 status code files/directories, and run the scan to find the specific file extensions (.php, .xml, .txt).

```
gobuster dir -u http://demo.ine.local -w /usr/share/wordlists/dirb/common.txt -b 403,404 -x .php,.xml,.txt -r
```

**Note:** -x flag is used to find the files which have the specified extensions. -r flag is used to specify to follow any redirects or 302 status code pages.

<figure><img src="https://assets.ine.com/lab/learningpath/7094343280db9f5c2a1394227caab81e4ab1da48563a0dc5d072ee79da43c097.jpg" alt=""><figcaption></figcaption></figure>

### **Step 8:** Run the gobuster to scan the ‘/data’ directory while ignoring the 403 and 404 status code pages/directories. Find the files with the extensions such as .php, .xml, .txt.

```
gobuster dir -u http://demo.ine.local/data -w /usr/share/wordlists/dirb/common.txt -b 403,404 -x .php,.xml,.txt -r
```

**Note:** the ‘/data’ directory is appended in the target URL. The gobuster will start scanning from the /data directory.

<figure><img src="https://assets.ine.com/lab/learningpath/9d77713dec1e142c585d9337a0589a172e6b65c70fa3b680a3f3d93b391f7f47.jpg" alt=""><figcaption></figcaption></figure>

Found an accounts.xml file inside the /data directory.

### **Step 9:** Navigate to the accounts.xml file and check its contents.

**URL:** \[http://demo.ine.local/data/accounts.xml]

<figure><img src="https://assets.ine.com/lab/learningpath/5e17c4ef9e4d3806b350e1f2fb3b3d0e75ca5ab679d5478f4d82b97bdf31a431.jpg" alt=""><figcaption></figcaption></figure>

The login credentials have been revealed in the accounts.xml file.

## References:

1. Gobuster (https://github.com/OJ/gobuster)
2. Mutillidae (https://sourceforge.net/projects/mutillidae/)
