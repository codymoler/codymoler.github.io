---
title: "How to join an Ubuntu 20.04 machine to Active Directory"
categories:
  - Tutorials
tags:
  - ubuntu
  - active-directory
toc: true
toc_label: "Table of Contents"
---

In this tutorial, we will be performing the steps to bind an Ubuntu 20.04 device to an Active Directory domain using realmd. These steps have also been tested to work with Ubuntu 18.04.
{: .notice}

## Step 1: Perform updates
```
apt-get update
```

## Step 2: Install realmd, sssd, adcli
```
apt-get install -y sssd-ad sssd-tools realmd adcli
```

## Step 3: Create/Edit krb5 configuration file
```
nano /etc/krb5.conf
```

## Step 4: Modify /etc/krb5.conf
This configuration provides some default options. Modify the domain name to match your Active Directory configuration.
```
[libdefaults]
MYDOMAIN.NET
rdns = false
```
*Note:* Make sure to enter your domain name in ALL CAPS.
{: .notice--warning}

## Step 5: Install remaining packages
```
apt-get install -y krb5-user sssd-krb5
```

## Step 6: Change your hostname to a fully qualified domain name (FQDN)
The hostname should match the computer object in your Active Directory tree.
{% capture notice-2 %}
 **Example**
* Computer name = mymachine
* Domain name = mydomain.net
* Hostname will be mymachine.mydomain.net
{% endcapture %}

<div class="notice">
  {{ notice-2 | markdownify }}
</div>
```
hostnamectl set-hostname mymachine.mydomain.net
```

## Step 7: Grab Kerberos ticket
You will need to use an AD account with permission to join computers to AD.  After entering the command you will be prompted for the password.
```
kinit yourusername
```

## Step 8: Join the system to the domain
Unless your AD account is the default "Administrator" account, use the -U flag to use the correct AD account.
```
realm join -v -U yourusername mydomain.net
```
*Note:* This step will create a computer object in the default domain directory. If you don't want it to be in the default directory, make sure to create the computer object in AD before this step. Realm will automatically find the created object and update it.
{: .notice--warning}

## Step 9: Modify pam to automatically create a home directory for AD users
```
pam-auth-update
```
Check "activate mkhomedir". Tab and hit Enter to select Ok.

## Step 10: Test to see if the integration is working correctly
ID a domain user. The machine is joined correctly if the user is found.
```
id user@mydomain.net
```

## Optional Steps
Update your sudoers file to include your domain administrators security group with full sudo access:

Add a file to sudoers.d which follows the standard format for permissions. Enter the group:
```
%mydomainadmingroup@MYDOMAIN.NET ALL=(ALL) NOPASSWD:ALL
```

Tell realm to not let anyone but the selected group login:

```
realm permit -g mydomainadmingroup@MYDOMAIN.NET
```

Login using SSH via another terminal:
```
ssh -l myusername@mydomain.net mymachine
```

Automate it! This process can easily be applied to a script:
{% gist f90c93d996d8ff614930fa6bbd2e237e %}