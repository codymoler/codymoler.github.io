---
title: "How to cache an Active Directory mobile account on macOS before deployment"
categories:
  - Tutorials
tags:
  - macOS
  - mobile account
  - active directory
toc: true
toc_label: "Table of Contents"
---

In this tutorial, we will be performing the steps to create a mobile account for an Active Directory user without knowing the end user's password. This can be useful for IT administrators who want to alter the user's home directory or cache the password before ever meeting with the user in person.
{: .notice}

## Step 1: Bind the device to an Active Directory domain
If it isn't already, bind the macOS device to your Active Directory domain before continuing with the following steps. If you require assistance doing so, there are many articles that can be found with a quick Google search.

## Step 2: Use the Terminal
Open the Terminal application, which can be found in the Applications >> Utilities folder. Inside the terminal, enter the following command:
```
sudo /System/Library/CoreServices/ManagedClient.app/Contents/Resources/createmobileaccount -v -n [username]
```
*Note:* Make sure to replace [username] with the domain id of the user.
{: .notice--warning}

## Step 3: Follow the prompts
The command will ask to enter an administator password. After doing so, it will then prompt for a SecureToken account. If your device has FileVault enabled, enter an administrator's credentials. If not, you can skip this step by hitting Enter/Return.

## Step 4: Finish up
Once the command has finished, you will see a list of user information. This means that the account was successfully created. With this, the user's home directory has also been created and can be altered. The user can also be made an administrator, all without logging in! 

*Note:* If you do not see the user's information, then the command failed. Refer to the troubleshooting section below.
{: .notice--warning}

## Troubleshooting
The command above can sometimes fail if the device hasn't recently communicated with a domain controller. There are several ways to force the communication, one of which will be explained below.
1. Open System Preferences and navigate to Users & Groups.
2. Click the lock icon and enter an administator's credentials.
3. Select 'Login Options' and click the edit button beside 'Network Account Server'.
4. Click the 'Open Directory Utility' button.
5. Select 'Directory Editor' in the top menu.
6. If it is not already selected, change the option for 'Viewing Users in:' to your domain.
7. After a few moments, a list of domain records will populate the lefthand menu. This process forces the device to query a domain controller.

After following these steps, try the command once again. If it still fails, restart the device and try again.