---
layout: post
author: tobias
title: "Windows Admin Center (WAC) ACME certificates with PFsense"
date: 2021-08-06T18:18:42+02:00
categories: [Microsoft, Pfsense]
description: "Running Windows Admin Center with ACME certificates from PFsense."
image: assets/images/ssdsdasd.png
---
In this guide I will show you how to setup automatic certificate renewal for Windows Admin Center (WAC) with the PFsense ACME package. The starting requirement for this guide is that you have already setup the ACME solution within PFsense, if not you can use <a href="https://blog.vonhewitt.com/2017/08/using-pfsenses-acme-package-to-generate/" target="_blank" rel="noreferrer noopener nofollow">this guide</a>.

#### Creating a certificate user

<div class="wp-block-image">
  <figure class="alignright size-large"><img loading="lazy" width="1024" height="201" src="https://ictguru.nl/wp-content/uploads/2021/10/certmgmt-privileges-1024x201.png" alt="" class="wp-image-206" srcset="https://ictguru.nl/wp-content/uploads/2021/10/certmgmt-privileges-1024x201.png 1024w, https://ictguru.nl/wp-content/uploads/2021/10/certmgmt-privileges-300x59.png 300w, https://ictguru.nl/wp-content/uploads/2021/10/certmgmt-privileges-768x150.png 768w, https://ictguru.nl/wp-content/uploads/2021/10/certmgmt-privileges.png 1144w" sizes="(max-width: 767px) 89vw, (max-width: 1000px) 54vw, (max-width: 1071px) 543px, 580px" /><figcaption>Assigned privileges for the certificate user</figcaption></figure>
</div>

  * First navigate to <a href="https://github.com/TobiasS1402/Powershell/blob/main/PfsenseACME-WacSSL.ps1" target="_blank" rel="noreferrer noopener">this github script i created</a> and download it to your Windows Admin Center instance.
  * Second you should create a custom Pfsense user with restricted access, do this by going to _System -> User Manager -> Add_. After that, create a user and name it accordingly.
  * After creating the user, you should create a PFsense group to assign this new user to. In addition, edit the group to assign correct privileges. Under assigned privileges add _**&#8220;WebCfg &#8211; System: Certificate Manager&#8221; and &#8220;WebCfg &#8211; Dashboard (all)&#8221;**_. Set the variable `$PFSENSE_USERNAME` and `$PFSENSE_PASSWORD` in the script for the certificate user we have created and change the `$SITE` variable to the PFsense FQDN or ip-address. 

#### Setting the certificate variables 

<div class="wp-block-image">
  <figure class="alignright size-large is-resized"><img loading="lazy" src="https://ictguru.nl/wp-content/uploads/2021/10/pfsense-ID-1024x153.png" alt="" class="wp-image-204" width="697" height="104" srcset="https://ictguru.nl/wp-content/uploads/2021/10/pfsense-ID-1024x153.png 1024w, https://ictguru.nl/wp-content/uploads/2021/10/pfsense-ID-300x45.png 300w, https://ictguru.nl/wp-content/uploads/2021/10/pfsense-ID-768x114.png 768w, https://ictguru.nl/wp-content/uploads/2021/10/pfsense-ID.png 1517w" sizes="(max-width: 697px) 100vw, 697px" /><figcaption>The certificate ID for *.ictguru.nl as example.</figcaption></figure>
</div>

  * Head to S_ystem -> Certificate manager -> Certificates_. You should hover over the export certificate button of the certificate you&#8217;ll want to enroll for your WAC instance. We are looking for the certificate id, this is the red string in the screenshot. Set this as value for the `$CERTID` variable.
  * In addition we should set the following variables. `$CERTNAME` will be the name for your certificate once downloaded, `$CERTDIR` is the location where the certificates will be downloaded and merged into a .pfx file. `$PFXPASSWORD` is the password that will be set for your certificate file and `$WACPORT` is the port on which your Windows Admin Center instance will be running. 

#### Creating the scheduled task for automatic renewal

After that, we can create scheduled task on the WAC server. Preferably a service account with administrative privileges for running the script. Create the scheduled task according to the screenshots below, for running script i recommend setting the program to `<em>powershell.exe</em>` and the arguments to _`-NoProfile -NoLogo -NonInteractive -ExecutionPolicy Bypass -File c:\PfsenseACME-WacSSL.ps1`._

<div class="wp-block-image">
  <figure class="alignleft size-full"><img loading="lazy" width="633" height="481" src="https://ictguru.nl/wp-content/uploads/2021/08/acme_general-1.png" alt="" class="wp-image-102" srcset="https://ictguru.nl/wp-content/uploads/2021/08/acme_general-1.png 633w, https://ictguru.nl/wp-content/uploads/2021/08/acme_general-1-300x228.png 300w" sizes="(max-width: 633px) 100vw, 633px" /></figure>
</div><figure class="wp-block-image size-full is-resized">

<img loading="lazy" src="https://ictguru.nl/wp-content/uploads/2021/08/acme_triggers.png" alt="" class="wp-image-101" width="566" height="488" srcset="https://ictguru.nl/wp-content/uploads/2021/08/acme_triggers.png 597w, https://ictguru.nl/wp-content/uploads/2021/08/acme_triggers-300x259.png 300w" sizes="(max-width: 566px) 100vw, 566px" /> </figure> 

Test the scheduled task that you have created above and check the results by going to your WAC server in you browser. After that, you should be greeted with a smaal green lock inside your web browser like so.<figure class="wp-block-image size-full">

<img loading="lazy" width="625" height="611" src="https://ictguru.nl/wp-content/uploads/2021/08/certificate.png" alt="" class="wp-image-103" srcset="https://ictguru.nl/wp-content/uploads/2021/08/certificate.png 625w, https://ictguru.nl/wp-content/uploads/2021/08/certificate-300x293.png 300w" sizes="(max-width: 625px) 100vw, 625px" /> </figure>