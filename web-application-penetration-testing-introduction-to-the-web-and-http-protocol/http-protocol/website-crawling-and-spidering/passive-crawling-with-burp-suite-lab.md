# Passive Crawling with Burp Suite (Lab)

## Overview

Web Application Pentesting Tools can prove to be very helpful while performing penetration testing.

In this lab exercise, we will take a look at how to use [Burp Suite](https://portswigger.net/burp/documentation/desktop/tools) to perform passive crawling on the [Mutillidae](https://github.com/webpwnized/mutillidae) web application.

## Lab Environment

In this lab environment, you will be provided with GUI access to a Kali machine. The target machine running the [Mutillidae](https://github.com/webpwnized/mutillidae) web application will be accessible at **demo.ine.local**.

**Objective:** Perform passive crawling on the web application with Burp Suite.

## Tools

The best tools for this lab are:

* Nmap
* Burp Suite

***

## Solutions

### **Step 1:** Open the lab link to access the Kali machine.

<figure><img src="https://assets.ine.com/lab/learningpath/a2fcb0d93a5c4e939ddcab66c095ea88229ef06b0380b0ce71639c582b3b6def.jpg" alt=""><figcaption></figcaption></figure>

### **Step 2:** Check if the target machine is reachable:

```
ping -c 4 demo.ine.local
```

<figure><img src="https://assets.ine.com/lab/learningpath/79b40c8b58036aaf6d6428ccfec6e3eae4194786940ee27b0ddfd45298864015.jpg" alt=""><figcaption></figcaption></figure>

The target is reachable.

### **Step 3:** Run an nmap scan against the target:

```
nmap -sS -sV demo.ine.local
```

<figure><img src="https://assets.ine.com/lab/learningpath/ef504f0859afdfbe52656baa8aab3a431896995dd89f605f1b5e6844d27582bb.jpg" alt=""><figcaption></figcaption></figure>

Port 80 and 3306 are open.

### **Step 4:** Access the web application using firefox.

```
firefox http://demo.ine.local
```

<figure><img src="https://assets.ine.com/lab/learningpath/415072ec7dab3497f4c909c6145b6c043067bfc414a241488f9733957a93e882.jpg" alt=""><figcaption></figcaption></figure>

### **Step 5:** The target is running OWASP Mutillidae II. Configure the firefox browser to use burp suite proxy.

<figure><img src="https://assets.ine.com/lab/learningpath/0bdd8f116cd43a6d1e225ebebb1703ff0a342562e5d698410ac0aae029716461.jpg" alt=""><figcaption></figcaption></figure>

### **Step 6:** Start burp suite.

<figure><img src="https://assets.ine.com/lab/learningpath/232c585fb74602aa20bbab3d1f0313ad0e6775d31cc4add730d217079a0caf25.jpg" alt=""><figcaption></figcaption></figure>

Go to the Proxy tab, and turn off the intercept.

<figure><img src="https://assets.ine.com/lab/learningpath/2ce1427dba7eff3b287cef79040c9f07e3f11137c68d131b996b2b8cc7a3ebd8.jpg" alt=""><figcaption></figcaption></figure>

### **Step 7:** Navigate to the Dashboard tab.

<figure><img src="https://assets.ine.com/lab/learningpath/508a80d0cfc9de6a3fca8dc63182c05ee28a7604362e7ee2a5cf96af8689f154.jpg" alt=""><figcaption></figcaption></figure>

You will see that Passive Crawling is enabled.

**Browse the Mutillidae application and burp will automatically crawl the visited pages.**

The passive crawler statistics are mentioned.

<figure><img src="https://assets.ine.com/lab/learningpath/037fb1baaf5ec753668f2c6ebae93ae5a7f9291505e0251d267dfcd416a6cbab.jpg" alt=""><figcaption></figcaption></figure>

### **Step 8:** Go to the "HTTP history" tab under Proxy.

<figure><img src="https://assets.ine.com/lab/learningpath/3a1c87199e6413fb6e14be393f6e83266625f2abe3c9931723849dd85df96ec5.jpg" alt=""><figcaption></figcaption></figure>

The visited web pages will appear under this tab.

### **Step 9:** Navigate to “Target” tab and the sitemap of the web application will be displayed.

<figure><img src="https://assets.ine.com/lab/learningpath/540b238c96d6056c03754e59336bf0f7bbcdf4a1cf13cf88a179c920e879aec4.jpg" alt=""><figcaption></figcaption></figure>

## Conclusion

In this lab, we saw how to use Burp Suite to perform passive crawling on a web application.

## References

* [Burp Suite](https://portswigger.net/burp)
* [Mutillidae II](https://sourceforge.net/projects/mutillidae/)
