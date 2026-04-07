# Let's Go Phishing  (Lab)

## Task: Let's GoPhishing!

### Video: Let's GoPhishing!

**Estimated time:** 20 minutes

### Goal

This lab covers the process of creating and sending a phishing email with Gophish.

### Pre-requisites

This lab does not have any pre-requisites.

### Requirements

This lab does not require any configuration or setup.

***

## Solution

### **Step 1:** Open the lab link to access the Windows GUI instance

This lab environment will provide you with access to a pre-configured Windows system that has all the required tools installed.

### **Step 2:** Starting the Gophish server

To begin with, you will need to startup the Gophish server, this can be done by double clicking on the **gophish.exe** shortcut on the desktop of the Windows system you have been provided access to as shown in the following screenshot.

<figure><img src="https://assets.ine.com/content/ptp/AlexisAhmed/VOD-4379/LAB-3874/1.png" alt=""><figcaption></figcaption></figure>

As shown in the following screenshot, once the Gophish server is started up you will be provided with a command prompt session that will display the current working state of the Gophish server in addition to the local port it is listening on.

<figure><img src="https://assets.ine.com/content/ptp/AlexisAhmed/VOD-4379/LAB-3874/2.png" alt=""><figcaption></figcaption></figure>

In this case, the Gophish server is listening on port **https://127.0.0.1:3333**.

You can access the Gophish admin panel by navigating to the aforementioned address in a web browser like Firefox.

<figure><img src="https://assets.ine.com/content/ptp/AlexisAhmed/VOD-4379/LAB-3874/3.png" alt=""><figcaption></figcaption></figure>

The admin panel does not have a valid SSL certificate, as a result, you will need to click on the advanced button and click on "accept the risk and continue" as shown in the following screenshot.

<figure><img src="https://assets.ine.com/content/ptp/AlexisAhmed/VOD-4379/LAB-3874/4.png" alt=""><figcaption></figcaption></figure>

You will now be prompted with the Gophish admin panel authentication form, you can login with the following credentials: **admin:phishingpasswd**.

<figure><img src="https://assets.ine.com/content/ptp/AlexisAhmed/VOD-4379/LAB-3874/5.png" alt=""><figcaption></figcaption></figure>

After logging in successfully, you will be redirected to the Gophish dashboard.

<figure><img src="https://assets.ine.com/content/ptp/AlexisAhmed/VOD-4379/LAB-3874/6.png" alt=""><figcaption></figcaption></figure>

### **Step 3:** Setting up a Gophish sending profile

Now that we have started the Gophish server and logged in to the admin panel, we can begin the process of setting up a phishing email.

To begin with, we will need to setup a sending profile, this can be done by clicking on the **Sending Profiles** menu item on the sidebar of the dashboard as shown in the following screenshot.

<figure><img src="https://assets.ine.com/content/ptp/AlexisAhmed/VOD-4379/LAB-3874/7.png" alt=""><figcaption></figcaption></figure>

We can now setup a new sending profile by clicking on the **New Profile** button.

<figure><img src="https://assets.ine.com/content/ptp/AlexisAhmed/VOD-4379/LAB-3874/8.png" alt=""><figcaption></figcaption></figure>

This will bring up the sending profile customization prompt where you can specify different criteria based on your requirements, in this case, we will use the following configuration for the sending profile:

1. Name: red
2. From: info [**support@demo.ine.local**](mailto:support@demo.ine.local)
3. Host: localhost:25
4. Username: red@demo.ine.local
5. Password: penetrationtesting

<figure><img src="https://assets.ine.com/content/ptp/AlexisAhmed/VOD-4379/LAB-3874/9.png" alt=""><figcaption></figcaption></figure>

After configuring the sending profile accordingly, we can test the configuration by clicking on the **Send Test Email** button as shown in the following screenshot.

<figure><img src="https://assets.ine.com/content/ptp/AlexisAhmed/VOD-4379/LAB-3874/10.png" alt=""><figcaption></figcaption></figure>

This will prompt you to specify the details of the recipient, in this case we will be sending the test email to **victim@demo.ine.local** whose mailbox has already been configured in Thunderbird.

<figure><img src="https://assets.ine.com/content/ptp/AlexisAhmed/VOD-4379/LAB-3874/11.png" alt=""><figcaption></figcaption></figure>

To verify that the test email was sent successfully, we can open up the Thunderbird email client installed and configured on the Windows system. As shown in the following screenshot, the test email was sent and received successfully thus confirming that the sending profile is functional.

<figure><img src="https://assets.ine.com/content/ptp/AlexisAhmed/VOD-4379/LAB-3874/12.png" alt=""><figcaption></figcaption></figure>

We can now save the sending profile.

<figure><img src="https://assets.ine.com/content/ptp/AlexisAhmed/VOD-4379/LAB-3874/13.png" alt=""><figcaption></figcaption></figure>

### **Step 4:** Setting up a Gophish landing page

In order for us to setup a successful phishing campaign, we will need to setup a landing page where target users will be directed to for credential harvesting.

To begin with, we can click on the **Landing Pages** menu item on the side menu of the dashboard as shown in the following screenshot.

<figure><img src="https://assets.ine.com/content/ptp/AlexisAhmed/VOD-4379/LAB-3874/14.png" alt=""><figcaption></figcaption></figure>

We can then create a new landing page by clicking on the **New Page** button as highlighted in the preceding screenshot.

This will bring up the landing page customization prompt, where we can specify the site we would like to emulate or clone for the purpose of the phishing campaign.

In this case, we will be using a custom landing page being hosted on the **localhost:8080**.

As shown in the following screenshot, we can specify the landing page name and click on the **Import Site** button to import the pre-existing landing page.

<figure><img src="https://assets.ine.com/content/ptp/AlexisAhmed/VOD-4379/LAB-3874/15.png" alt=""><figcaption></figcaption></figure>

This will prompt you to specify the URL of the site you would like to import, as shown in the following screenshot, we will specify the URL **http://localhost:8080**, after which, you can click on import.

<figure><img src="https://assets.ine.com/content/ptp/AlexisAhmed/VOD-4379/LAB-3874/16.png" alt=""><figcaption></figcaption></figure>

After importing the site, you will be able to modify the appearance of the site and the data that is collected.

In this case, we will be capturing all submitted data and will be redirecting users to the landing page hosted on **localhost:8080**.

<figure><img src="https://assets.ine.com/content/ptp/AlexisAhmed/VOD-4379/LAB-3874/17.png" alt=""><figcaption></figcaption></figure>

After configuring the landing page, we can save the page by clicking on the **Save Page** button as shown in the preceding screenshot.

### **Step 5:** Setting up a Gophish email template

Now that we have setup the sending profile and landing page, we can move on to the next step which involves setting up the email template for this phishing campaign.

To begin with, we can create a new email template by clicking on the **Email Templates** menu item on the sidebar. You will then need to click on the **New Template** button as shown in the following screenshot.

<figure><img src="https://assets.ine.com/content/ptp/AlexisAhmed/VOD-4379/LAB-3874/18.png" alt=""><figcaption></figcaption></figure>

An email template called **Password Reset Email.txt** has been provided to you in the lab environment on the Desktop of the Windows system as shown in the following screenshot.

<figure><img src="https://assets.ine.com/content/ptp/AlexisAhmed/VOD-4379/LAB-3874/19.png" alt=""><figcaption></figcaption></figure>

You will need to copy the content of the email template as we will be using it for our phishing campaign.

We can now setup the new Gophish email template. As shown in the following screenshot, paste the content of the email template you copied previously in the body section of the email template.

<figure><img src="https://assets.ine.com/content/ptp/AlexisAhmed/VOD-4379/LAB-3874/20.png" alt=""><figcaption></figcaption></figure>

We can now save the email template by clicking on the **Save Template** button.

<figure><img src="https://assets.ine.com/content/ptp/AlexisAhmed/VOD-4379/LAB-3874/21.png" alt=""><figcaption></figcaption></figure>

### **Step 6:** Setting up a users & groups

The next step will involve selecting or specifying the target users for our phishing campaign.

The lab environment has provided you with a file called **targets.csv** on the Desktop of the Windows system as shown in the following screenshot.

<figure><img src="https://assets.ine.com/content/ptp/AlexisAhmed/VOD-4379/LAB-3874/22.png" alt=""><figcaption></figcaption></figure>

We can import these users by clicking on the **Users & Groups** menu item on the sidebar of the dashboard as shown in the following screenshot.

<figure><img src="https://assets.ine.com/content/ptp/AlexisAhmed/VOD-4379/LAB-3874/23.png" alt=""><figcaption></figcaption></figure>

You will then need to click on the **New Group** button as shown in the preceding screenshot.

This will bring up the users & groups menu, where we can bulk import our users by selecting the **targets.csv** file stored on the Desktop.

<figure><img src="https://assets.ine.com/content/ptp/AlexisAhmed/VOD-4379/LAB-3874/24.png" alt=""><figcaption></figcaption></figure>

After importing the users, we can save the changes.

### **Step 7:** setting up a Gophish campaign

The final step will involve setting up our phishing campaign.

We can create a new campaign by clicking on the **Campaigns** menu item on the sidebar of the dashboard as shown in the following screenshot.

<figure><img src="https://assets.ine.com/content/ptp/AlexisAhmed/VOD-4379/LAB-3874/25.png" alt=""><figcaption></figcaption></figure>

Creating a new campaign will bring up the campaign configuration menu where you will need to specify the email template, landing page and sending profile you created in the previous steps.

You will also need to specify the Groups/users you imported.

<figure><img src="https://assets.ine.com/content/ptp/AlexisAhmed/VOD-4379/LAB-3874/26.png" alt=""><figcaption></figcaption></figure>

We can now launch the campaign by clicking on the launch campaign button highlighted in the preceding screenshot.

Launching the campaign will redirect you to the campaign dashboard that will provide you with a summary of statistics pertinent to the phishing campaign.

<figure><img src="https://assets.ine.com/content/ptp/AlexisAhmed/VOD-4379/LAB-3874/27.png" alt=""><figcaption></figcaption></figure>

We can check the emails sent to the target email by opening up the Thunderbird email client.

<figure><img src="https://assets.ine.com/content/ptp/AlexisAhmed/VOD-4379/LAB-3874/28.png" alt=""><figcaption></figcaption></figure>

As shown in the preceding screenshot, the phishing campaign was sent successfully.

We can test the phishing page by clicking on the **Reset Password** link sent in the phishing email.

After clicking on the phishing link, we can check the Gophish dashboard to confirm that the phishing link was clicked and opened.

<figure><img src="https://assets.ine.com/content/ptp/AlexisAhmed/VOD-4379/LAB-3874/29.png" alt=""><figcaption></figcaption></figure>

## Conclusion

In this lab, we explored the process of setting up and sending a phishing campaign with Gophish.
