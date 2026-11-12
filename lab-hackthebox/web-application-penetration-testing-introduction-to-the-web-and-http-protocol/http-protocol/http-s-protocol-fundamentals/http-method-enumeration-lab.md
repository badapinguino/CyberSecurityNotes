# HTTP Method Enumeration (Lab)

## Overview

This lab focuses on enumerating and testing HTTP methods on a web server. You will learn to interact with various web pages of the application to discover supported HTTP methods and explore the potential for unauthorized actions like file uploads and deletions.

## Lab Environment

In this lab environment, you will be provided with GUI access to a Kali machine. The target machine will be accessible at **demo.ine.local**.

**Objective:** To understand and identify the HTTP methods supported by different web pages within a web application

## Tools

The best tools for this lab are:

* Burp Suite
* Curl
* DiRB

***

## Solutions

In this exercise, we will take a look at how to use Burp Suite and curl to enumerate the HTTP Methods supported by various web pages. Inspecting the web application.

## Inspecting the web application.

There are two new links which can be followed from the home page. The login link on the navigation bar and the blog post. The login link redirects to "login.php" and the Blog link redirects to "post.php".

#### **Step 1:** Following Links: Click on the login Link.

URL: http://demo.ine.local/login.php

<figure><img src="https://assets.ine.com/lab/learningpath/0b73ead7dec2b69120b5e9b4190ec86f6458d1f1321a24c22e6f5baf2d0cd10e.png" alt=""><figcaption></figcaption></figure>

The login page contains a form.

#### **Step 2:** Identify the endpoint which processes the form fields. Right-click on the web page and click on the view source.

The parameters are passed in a POST request to the same "login.php" page.

<figure><img src="https://assets.ine.com/lab/learningpath/46cf689cd58f92dafa07952a07056394d9c1380f954d269c1c4a64382812cc6e.png" alt=""><figcaption></figcaption></figure>

#### **Step 3:** Login to the web application with the provided credentials.

**Username:** john **Password:** password

<figure><img src="https://assets.ine.com/lab/learningpath/6b32782b9524cf42577bef45dc89e6e6d02dcf08a032e26bff3841c276911728.png" alt=""><figcaption></figcaption></figure>

After login instead of the login link "Welcome John" message is displayed.

#### **Step 4:** Follow the remaining link. Click on the blog post.

The Web pages which can be accessed by following the links are index.php, login.php and post.php.

<figure><img src="https://assets.ine.com/lab/learningpath/dfb7e35326a94c42f276d2639f769a92b048f731da5f9d3e74153af08edeba9e.png" alt=""><figcaption></figcaption></figure>

### Using dirb to identify hidden directories.

```
dirb http://demo.ine.local
```

<figure><img src="https://assets.ine.com/lab/learningpath/1733f2a0e68d9775ad48f09543bec6c7eed8e26c566fcf7614b1258f4a239b93.png" alt=""><figcaption></figcaption></figure>

The directories which are present on the server are css, img, js, mail, uploads and vendor.

### Interacting with the home page with CURL.

#### **Step 1:** Sending GET request:

```
curl -X GET demo.ine.local
```

<figure><img src="https://assets.ine.com/lab/learningpath/20f6117775961a1b5ce872e84f8d65b86722126ec7b45c2465555dec6bfc30c5.png" alt=""><figcaption></figcaption></figure>

#### **Step 2:** Sending HEAD request

```
curl -I demo.ine.local
```

<figure><img src="https://assets.ine.com/lab/learningpath/05cc0790b052c90e518c99780cedc9457ede8fea75206e806df8cbb08e7df6db.png" alt=""><figcaption></figcaption></figure>

#### **Step 3:** Sending OPTIONS request.

```
curl -X OPTIONS demo.ine.local -v
```

<figure><img src="https://assets.ine.com/lab/learningpath/56f05537dda77aa65b284db86f4ed7951e2eaef0bdf0c722330b6c0ab85ec0ec.png" alt=""><figcaption></figcaption></figure>

The supported methods are GET, HEAD and OPTIONS. Accessing the web page should produce an error.

#### **Step 4:** Sending POST Request.

```
curl -X POST demo.ine.local
```

<figure><img src="https://assets.ine.com/lab/learningpath/dd02290f70ac4d74def626bd00c6e9a39ed31cfedf5e6110efec4f4faf91b7a2.png" alt=""><figcaption></figcaption></figure>

#### **Step 5:** Sending PUT Request

```
curl -XPUT demo.ine.local
```

![Content Image](https://assets.ine.com/lab/learningpath/3751f4c7584fe3b79a839d13148561f6156de9fee225dc83212a9e4cac826632.png)

### Interacting with the login.php page with CURL.

**Step 1:** Sending OPTIONS Request

```
curl -X OPTIONS demo.ine.local/login.php -v
```

<figure><img src="https://assets.ine.com/lab/learningpath/9119db1b55739a5235262edbdd558f10906dde90498f35b082073b2638c0fdb2.png" alt=""><figcaption></figcaption></figure>

The allowed methods include: GET,POST,HEAD,OPTIONS.

#### **Step 2:** Sending POST Request.

```
curl -X POST demo.ine.local/login.php
```

<figure><img src="https://assets.ine.com/lab/learningpath/37ee50248483fc229821cad1cff321c7a1e80c3406ff3776727130cf679feaff.png" alt=""><figcaption></figcaption></figure>

Unlike the home page (index.php). The login page supports POST method.

#### **Step 3:** Passing the username and password to the login.php page.

```
curl -X POST demo.ine.local/login.php -d "name=john&password=password" -v
```

The login page returned a different response than before. The response contains 302 redirects.

<figure><img src="https://assets.ine.com/lab/learningpath/f33e5be799db9e6473e5a228c87c1ca4eef535e2201c04642461c9216c8898e0.png" alt=""><figcaption></figcaption></figure>

### Interacting with the post.php page with CURL.

#### **Step 1:** Sending OPTIONS request.

```
curl -X OPTIONS demo.ine.local/post.php -v
```

<figure><img src="https://assets.ine.com/lab/learningpath/679110933011d0e0f40e9b6d205a744033fdf6358ecd87e1eee55e591b4c221f.png" alt=""><figcaption></figcaption></figure>

Similar to login.php, post.php has GET, POST, HEAD and OPTIONS methods enabled.

### Interacting with the uploads directory

#### **Step 1:** Checking the content of /uploads directory.

**URL:** http://demo.ine.local/uploads

<figure><img src="https://assets.ine.com/lab/learningpath/3d09c8fcf435b9a29e87e68bc0d82ce471a125b17a06f2455fe2d276eb81c84e.png" alt=""><figcaption></figcaption></figure>

#### **Step 2:** Sending OPTIONS request to /uploads directory.

```
curl -X OPTIONS demo.ine.local/uploads/
curl -X OPTIONS demo.ine.local/uploads/ -v
```

<figure><img src="https://assets.ine.com/lab/learningpath/33f1ac679bbe3df25a1e2fd8b8586d343587334b6ca9a80f2784ec0e4a74f3ea.png" alt=""><figcaption></figcaption></figure>

The Webdav module is enabled on the Apache Server, Webdav module allows file upload via the **PUT** method.

#### **Step 3:** Uploading a file with the PUT method.

```
echo "Hello World" > hello.txt
curl demo.ine.local/uploads/ --upload-file hello.txt
```

<figure><img src="https://assets.ine.com/lab/learningpath/d63315c4ce6741629bb7dc892780879901905210e84e9341ea0f713cee09ec64.png" alt=""><figcaption></figcaption></figure>

#### **Step 4:** Checking the content of /uploads directory.

The file "hello.txt" was uploaded successfully.

<figure><img src="https://assets.ine.com/lab/learningpath/eb05b6d72ef81acd7f9d7c5f8d5a73eb44396c5f0925734357b2264210301548.png" alt=""><figcaption></figcaption></figure>

#### **Step 5:** Using the DELETE method to delete the file.

```
curl -XDELETE demo.ine.local/uploads/hello.txt
```

<figure><img src="https://assets.ine.com/lab/learningpath/6493d94c35e24d33bf99af2ea6618353134d1d8ca140659365d5bf02e60ea9a4.png" alt=""><figcaption></figcaption></figure>

#### **Step 6:** Checking the content of /uploads directory.

The file was deleted successfully.

<figure><img src="https://assets.ine.com/lab/learningpath/3d89311381befa182ee9b2792e975ad27015b03cd53385952d8672a6468c1d95.png" alt=""><figcaption></figcaption></figure>

### Interacting with the web page with Burp Suite

#### **Step 1:** Set the FoxyProxy to use the burp proxy. Click on the Fox icon and select "Burp Suite"

<figure><img src="https://assets.ine.com/lab/learningpath/7d2996c436247043765f3ef03b03fdd3bf4d059a373981c813aa6e18a3ee4aa7.png" alt=""><figcaption></figcaption></figure>

#### **Step 2:** Start burp suite. Reload the page and the request will be intercepted

<figure><img src="https://assets.ine.com/lab/learningpath/912c0a90088b351966e9569d0cbe9c5e9c66641d39376d640a123fb69bf4bfa3.png" alt=""><figcaption></figcaption></figure>

#### **Step 3:** Sending request to Repeater

<figure><img src="https://assets.ine.com/lab/learningpath/3b2da29347b1e21153ebc6b62021c726b100528b3f362c2c16c8d3c844e4fbd0.png" alt=""><figcaption></figcaption></figure>

Repeater Tab:

<figure><img src="https://assets.ine.com/lab/learningpath/b2a19f2c703c19eaf8237e79dff8eabb58f5569670f1c7d110c3b34df131f53c.png" alt=""><figcaption></figcaption></figure>

#### **Step 4:** Sending GET Request.

Repeater Tab:

<figure><img src="https://assets.ine.com/lab/learningpath/7eee5228ee1f4137e1c1c017e52969732cc8f077d47fa433ae7c244f95b351f7.png" alt=""><figcaption></figcaption></figure>

Response Tab:

<figure><img src="https://assets.ine.com/lab/learningpath/126dac47c11841e7b9de0f70f54f2f0948d3079cd79b0a45a08ca7e47fe2adb2.png" alt=""><figcaption></figcaption></figure>

#### **Step 5:** Sending HEAD Request

<figure><img src="https://assets.ine.com/lab/learningpath/d0bb061379ec8e591fdd5499103cf60082e974a344e0e81267b73437d35e9ee5.png" alt=""><figcaption></figcaption></figure>

Response Tab:

<figure><img src="https://assets.ine.com/lab/learningpath/ef85254c72172db2d54dd3774e440499ffd29702de1c14c1eb20c1ceb8308d54.png" alt=""><figcaption></figcaption></figure>

#### **Step 6:** Sending OPTIONS request.

<figure><img src="https://assets.ine.com/lab/learningpath/d26f4fea6eb5efe4b1f12949c3be183a85a1c2094f30d1344fc27c918a1d1d66.png" alt=""><figcaption></figcaption></figure>

Response Tab:

<figure><img src="https://assets.ine.com/lab/learningpath/a38254de4c9bc2774aa4fc76bed0a5b3266acc941fcd8e3b22df497883317453.png" alt=""><figcaption></figcaption></figure>

**Step 7:** Sending POST request.

<figure><img src="https://assets.ine.com/lab/learningpath/97bc26de862263f5eadfa1be4eb9898dc7ebfc592d51bf89cf3240a93f88e33a.png" alt=""><figcaption></figcaption></figure>

Response Tab:

<figure><img src="https://assets.ine.com/lab/learningpath/4d212164e46e1b1e9bd7ba4c66e7291eb521a0101a1c64754af91f20ab1ed3d1.png" alt=""><figcaption></figcaption></figure>

POST method is not allowed.

#### **Step 8:** Sending a POST request to login.php with incorrect login credentials.

<figure><img src="https://assets.ine.com/lab/learningpath/16ed7ca3e55e39c0e1ecfc1a8f875cb5aedf657367d42601c50617dc5a27b2e5.png" alt=""><figcaption></figcaption></figure>

Response Tab:

<figure><img src="https://assets.ine.com/lab/learningpath/58762116d9af7fde95778bccb75aeb13bbf3b1e465512b12cbf345b88775aeaa.png" alt=""><figcaption></figcaption></figure>

200 OK responses are received.

#### **Step 9:** Sending POST request with valid login credentials.

<figure><img src="https://assets.ine.com/lab/learningpath/58270af77a8dcd1b024407fd253c0e196b8f2dc1f22a299b0106d9379154bf07.png" alt=""><figcaption></figcaption></figure>

Response Tab:

<figure><img src="https://assets.ine.com/lab/learningpath/745423d1eb69f36e5ffca3cf40165cd8b3ddfad8e021695be419518c535c16a9.png" alt=""><figcaption></figcaption></figure>

The login credentials were correct and as a result, 302 responses were received to index.php.

#### **Step 10:** Uploading a file with the PUT method

<figure><img src="https://assets.ine.com/lab/learningpath/d47e270a473e2e62f8bc26f71a8dbf99657dcaa8e306a2c8cd3a8de0b9542c01.png" alt=""><figcaption></figcaption></figure>

Response Tab:

<figure><img src="https://assets.ine.com/lab/learningpath/a419d85f1f11f0198c974cd52f5a8072e4458c47096f763d904bedd8819caf0f.png" alt=""><figcaption></figcaption></figure>

The file was uploaded Successfully.

Check the files in the/uploads directory.

<figure><img src="https://assets.ine.com/lab/learningpath/c25fa3d6e0153e94b22c24c501ce6efa15e2f00c6d2141d961efbe594c665e00.png" alt=""><figcaption></figcaption></figure>

Response Tab:

<figure><img src="https://assets.ine.com/lab/learningpath/6348206046c83140474de43a6a8160f368ccc3c4133641ea726e7394fd3cae84.png" alt=""><figcaption></figcaption></figure>

The file "hello.txt" was uploaded successfully.

Checking the Content of the uploaded file.

<figure><img src="https://assets.ine.com/lab/learningpath/2a72503bf0d40b56f79595ee4a1a2be3ac9bac3ed480072da9ec671367cc4478.png" alt=""><figcaption></figcaption></figure>

Response Tab:

<figure><img src="https://assets.ine.com/lab/learningpath/4f1323f8c1055f9b1fa3ed0e07ea3d963220392d0066bc0b304832f21a860165.png" alt=""><figcaption></figcaption></figure>

#### **Step 11:** Deleting the File.

<figure><img src="https://assets.ine.com/lab/learningpath/79a33564faced46143ec62b1a3ac12d6fec8d22bf61a5abdd1375cbef274a8d4.png" alt=""><figcaption></figcaption></figure>

Response Tab:

<figure><img src="https://assets.ine.com/lab/learningpath/0d683aba13b45d5a2b58702cfdfd4dfeda68a2cd72bfedaf1e196adedb707386.png" alt=""><figcaption></figcaption></figure>

The file was deleted. Check the files in the uploads directory.

<figure><img src="https://assets.ine.com/lab/learningpath/72a169af5ee0d36f3f883c904b40b55c1ea94f1e901c976d4a31ee08ccf970da.png" alt=""><figcaption></figcaption></figure>

Response Tab:

<figure><img src="https://assets.ine.com/lab/learningpath/726c0d89fb0b9f80682fcce230eba33f4ebb0e7fa08474bddf21ea31c65a205d.png" alt=""><figcaption></figcaption></figure>

## References:

* [Curl](https://linux.die.net/man/1/curl)
* [Burp Suite](https://portswigger.net/burp/documentation/desktop/getting-started)
* [Dirb](https://tools.kali.org/web-applications/dirb)
