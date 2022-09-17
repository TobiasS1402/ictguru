---
layout: post
author: tobias
title: "Windows Admin Center (WAC) ACME certificates with PFsense"
date: 2021-08-06T18:18:42+02:00
categories: [Microsoft, Pfsense]
description: "Running Windows Admin Center with ACME certificates from PFsense."
image: assets/images/ssdsdasd.png
---
In this guide I will show you how to setup automatic certificate renewal for Windows Admin Center (WAC) with the PFsense ACME package. The starting requirement for this guide is that you have already setup the ACME solution within PFsense, if not you can use [This guide](https://blog.vonhewitt.com/2017/08/using-pfsenses-acme-package-to-generate/).

#### Creating a certificate user

![Assigned privileges for the certificate user](/assets/images/certmgmt-privileges.png)

  * First navigate to [this github script I created](https://github.com/TobiasS1402/Powershell) and download it to your Windows Admin Center instance.
  * Second you should create a custom Pfsense user with restricted access, do this by going to _System -> User Manager -> Add_. After that, create a user and name it accordingly.
  * After creating the user, you should create a PFsense group to assign this new user to. In addition, edit the group to assign correct privileges. Under assigned privileges add _WebCfg - System: Certificate Manager_ and _WebCfg - Dashboard (all)_. Set the variable `$PFSENSE_USERNAME` and `$PFSENSE_PASSWORD` in the script for the certificate user we have created and change the `$SITE` variable to the FQDN or ip-address of the PFsense instance. 

#### Setting the certificate variables 

![The certificate ID for *.ictguru.nl as example.](/assets/images/pfsense-ID.png)

  * Head to _System -> Certificate manager -> Certificates_. You should hover over the export certificate button of the certificate you'll want to enroll for your WAC instance. We are looking for the certificate id, this is the red string in the screenshot. Set this as value for the `$CERTID` variable.
  * In addition we should set the following variables. `$CERTNAME` will be the name for your certificate once downloaded, `$CERTDIR` is the location where the certificates will be downloaded and merged into a .pfx file. `$PFXPASSWORD` is the password that will be set for your certificate file and `$WACPORT` is the port on which your Windows Admin Center instance will be running. 

#### Creating the scheduled task for automatic renewal

After that, we can create scheduled task on the WAC server. Preferably a service account with administrative privileges for running the script. Create the scheduled task according to the screenshots below, for running script i recommend setting the program to `powershell.exe` and the arguments to `-NoProfile -NoLogo -NonInteractive -ExecutionPolicy Bypass -File c:\PfsenseACME-WacSSL.ps1`

![Scheduled task with service user](/assets/images/acme_general-1.png)

![Monthly triggers for the scheduled task](/assets/images/acme_triggers.png)

Test the scheduled task that you have created above and check the results by going to your WAC server in you browser. After that, you should be greeted with a smaal green lock inside your web browser. And just like that, automatic, valid and free certificates deployed inside your network on Windows!

![The certificate as seen from the browser](/assets/images/certificate.png)