---
title: "[EN] Micosoft 365 Setup Lab Environment M365 and Intune"
excerpt: "[EN] Micosoft 365 Setup Lab Environment - Create your own Lab with domains setup or prepare new tenant for you company. In this article we will go through creating new tenant, add custom domains for LAB purpose, assigned licences. " 
toc: true
classes: wide
header:
  image: /assets/images/M365-Lab/M365-Lab-Home.png
  teaser: /assets/images/M365-Lab/M365-Lab-Home.png
categories:
  - SysOps
  - Configuration
tags:
  - M365
  - Intune
published: true
hidden: true
---

## Why I write this article

Hi I wrote this article to help other start with Office 365 and Intune, without intervention on existing tenant or start as green filed for company to begin. I hope this guide we help you to avoid misconfiguration.

## Prerequisites

Before we begin with this you will need to consider few things:  

+ Is this will be LAB environment and won't be converted to production.
  + LAB Only: Tenat name suppose not be connected to organization name, once used tenant name connot be reused or changed.. **Example:** demo{Random Numbers} 
  + LAB Only: Use the highest licences like E5 to check all settings and dependencies for compare.
  + LAB Only: Don't use production custom domains, domain can be assigne only to one **Tenant**.
  + LAB Only: Location of Tenant don't need to be compliance with organization location.
+ Is this PoC Pilot environment that will be converted to production.
  + PoC: Tenat name suppose not be connected to organization name, once used tenant name connot be reused or changed. **Example:** poc{Random Numbers} 
  + PoC: Use the highest licences like E5 to check all settings and dependenciesfor compare.
  + PoC: Collect user case that you like to present.
  + PoC: Don't use production custom domains, domain can be assigne only to one **Tenant**.
  + PoC: Location of Tenant need to be compliance with organization location.
+ Is this Pilot environment that will be converted to production.
  + Pilot: Tenant name need to be connected to Organization for adoption propose.
  + Pilot: Tenant name need to be approve by bussines becasue will be used as first part of URL in **Sharepoint** and **OneDrive**
  + Pilot: Use licences that match Organization business cases to avoid misconfiguration.
  + Pilot: Don't use production custom domains, to avoid data flow distributions. 
  + Pilot: Location of Tenant need to be compliance with organization location.

>   
> **Notice:** Tenant **"Name"** cannot be change after creation.
>  
> **Notice:** Tenant **"Location"** cannot be change after creation.
>  
>
--------------------------
## First Azure Active Directory Tenant
### What is Tenant ?

 **Tenant** Is isolated dedicated space for our organization and our assets. The Tenant is the container for items of your Organization such as users, domains, subscriptions, devices, permissions, Office 365 Data. Tenant is fundamental for all services in Microsoft Cloud.

**Exmaple** of data isolation

![](/assets/images/M365-Lab/M365-Lab-Tenant.png)


Microsoft for make simple onboarding organization to Office365 or Azure, sometimes provsion tenant automatical without passing any information, as the name of tenant is using e-mail address and location is take form oure accounts.   
**Example** of this scenario
 - MSDN Subscriptions.
 - Azure free trial Subscriptions.

 **LAB** that is not connected to our organization this name nad location dosen't matter, but if we will create **PoC** or **Pilot** tenant that in feature can be converted to production we have to be more awere when is created and what name is used.  

>
> **Notice:** Tenant **"Location"** have impact on services that we can use in Office 365, for example **Phone Systems**
>
### Create Azure Active Directory Tenant

**Step 1:** Open a browser in InPrivate session, and navigate in browser to https://account.azure.com/organization

![](/assets/images/M365-Lab/M365-Tenant-1.png)

**Step 2:** Fill up date of your organization

**Country** - Location of tenant and Datacenter region, for data processing.
**Name and Surname** - use for display on first account
**Contact Number** - for pre sales and support.
**Organization Name** - Display name of organization it will be used in adoption process, can be change latter.
**Size** - information only.

![](/assets/images/M365-Lab/M365-Tenant-2.png)

**Step 2:** Tenant creation data

**Account ID** - Will be used to create first user in our **tenant**.  
>
> **Best practice:** As Account ID plase don't use standard names like: Administrator, admin. 
> **Best practice:** Use first account to create your admin account and assign rights.
>
**Domain Name** - Will be used to create default domin in our **tenant**, cannot be change and will be used to create URL's for **Sharepoint** and **Onedrive**.

![](/assets/images/M365-Lab/M365-Tenant-3.png)

**Step 4:** Verify your identity but SMS code or phone call

![](/assets/images/M365-Lab/M365-Tenant-4.png)

![](/assets/images/M365-Lab/M365-Tenant-5.png)
## Custom domian for LAB propuse without paying (LAB / PoC)

## Custom domian for pilot or production

## Bring you own custom domain
## Activate trial licences

### Office 365
### Intune (Enterprise Mobility Suite)

## Create Custom Accounts
