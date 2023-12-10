---
title: "Microsoft 365 Global Private Access Setup and replace classic VPN. "
classes: wide
date: 2023-12-10
excerpt: "Private Access is new solution that allow to access internal resources, it replace classic VPN with  identity-centric Zero Trust Network Access (ZTNA)."
toc: true
header:
  teaser: /assets/images/teaser/MicrosoftGraphAPI-GAL-Sync-to-SPO_01.png
  og_image: /assets/images/teaser/MicrosoftGraphAPI-GAL-Sync-to-SPO_og.png
categories:
 - Microsoft365
 - Security
tags:
  - Access
  - Security
  - M365
published: true
hidden: false
---

## What is Entra Private Access

Microsoft Entra Private Access provides your users - whether in an office or working remotely - secured access to your private, corporate resources. Microsoft Entra Private Access builds on the capabilities of Microsoft Entra application proxy and extends access to any private resource, port, and protocol.  

Remote users can connect to private apps across hybrid and multicloud environments, private networks, and data centers from any device and network without requiring a VPN. The service offers per-app adaptive access based on Conditional Access policies, for more granular security than a VPN.  

### Key features

* Quick Access: Zero Trust based access to a range of IP addresses and/or FQDNs without requiring a legacy VPN.
* Per-app access for TCP apps (UDP support in development).
* Modernize legacy app authentication with deep Conditional Access integration.
* Provide a seamless end-user experience by acquiring network traffic from the desktop client and deploying side-by-side with your existing third-party SSE solutions.


## Prerequisites

### Licencing

* Preview requires a Microsoft Entra ID P1 license
* Production Microsoft 365 E3 is recommended
* Microsoft Entra Private Access and Microsoft Entra Internet Access may require different licenses.


### Client

* Operating Systems: Windows 10/11
* The device must be Azure AD joined or Hybrid Azure AD joined to a tenant that has onboarded to Global Secure Access.
* Internet connection to Azure AD and the Global Secure Access service.
* Local administrator permissions during the installation.
* Install the Global Secure Access client, download the installation package and install it on the designated end-user devices.



## Simple Architecture Overview

Architecture show simple infrastructure deployed on Azure without any public access to servers, all servers are connected to one Virtual Network and protected by Network Security Group  
Application proxy is installed on server that have access to Entra ID.  

![](/assets/images/Private-Access/AAD-M365-Private-Access-Architcture.png)

## Quick Access vs per-app access

* Quick Access
Quick Access for broader access to your network using Microsoft Entra Private Access.  

![](/assets/images/Private-Access/AAD-M365-Private-Access-1.png)

* per-app access
Specific private apps for granular segmented access to private access resources using Microsoft Entra Private Access.  

![](/assets/images/Private-Access/AAD-M365-Private-Access-2.png)

## Configuration steps 


### Setup application proxy

Microsoft Entra application proxy provides secure remote access to on-premises web applications. After a single sign-on to Microsoft Entra ID, users can access both cloud and on-premises applications through an external URL or an internal application portal. For example, Application Proxy can provide remote access and single sign-on to Remote Desktop, SharePoint, Teams, Tableau, Qlik, and line of business (LOB) applications.

Logon to portal [https://entra.microsoft.com/](https://entra.microsoft.com/)  
Navigte to **Application**.  
Navigate to **Enterprise Application**.  
On the menu click **Download connector service** and then **Accept terms & Download**  

![](/assets/images/Private-Access/AAD-M365-Private-Access-app-proxy-1.png)

Enable aaplication proxy on tenant level.  

![](/assets/images/Private-Access/AAD-M365-Private-Access-app-proxy-2.png)


### Setup Entra Private Access

Logon to portal [https://entra.microsoft.com/](https://entra.microsoft.com/)  


![](/assets/images/Private-Access/AAD-M365-Private-Access-gsa-1.png)


Navigate to **Global Secure Access** and go to **Applications**   
Navigate to **Quick Access**  

>
> On this setup we will use **Quick access** not **per-app access** that all to create more restrictive access.
>

On context menu click **Add Quick Access application segment**  

![](/assets/images/Private-Access/AAD-M365-Private-Access-gsa-2.png)


Switch setup to **IP address range (CIDR)** and put data for whole subnet  

![](/assets/images/Private-Access/AAD-M365-Private-Access-gsa-3.png)

![](/assets/images/Private-Access/AAD-M365-Private-Access-gsa-4.png)

### Install client

Logon to portal [https://entra.microsoft.com/](https://entra.microsoft.com/)  
Navigate to **Global Secure Access** and go to **Connect**   
Navigate to **Client Download**  

![](/assets/images/Private-Access/AAD-M365-Private-Access-gsa-5.png)

Install veyfication and client setup  

![](/assets/images/Private-Access/AAD-M365-Private-Access-gsa-6.png)

## More features that will available in next months

* Security profiles
* Web content filtering policies
* Audit logs
* Enriched Microsoft 365 logs​
* Workbooks


