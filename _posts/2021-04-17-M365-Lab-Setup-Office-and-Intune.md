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


![](/assets/images/M365-Lab/Tenant-Decision.png)

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

 **LAB** Tenant and won't be used for production so in this case name nad location dosen't matter, but if we will create **PoC** or **Pilot** tenant that in feature can be converted to production we have to be more awere when data will be stored and what name is used.  

>
> **Notice:** Tenant **"Location"** have impact on services that we can use in Office 365, for example **Phone Systems**
>  

### Create Azure Active Directory Tenant

**Step 1:** Open a browser in InPrivate session, and navigate in browser to [https://account.azure.com/organization/](https://account.azure.com/organization/) 

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

**Step 4:** Verify your identity by SMS code or phone call

![](/assets/images/M365-Lab/M365-Tenant-4.png)

![](/assets/images/M365-Lab/M365-Tenant-5.png)

**Step 5:** Finish createion of tenant.

![](/assets/images/M365-Lab/M365-Tenant-6.png)

**Step 6:** Veryfication of tenant settings. Log on to Azure Active Directory admin center by URL: [https://aad.portal.azure.com/](https://aad.portal.azure.com/)

In Azure Active Directory Portal navigate to **"Overview"**  

![](/assets/images/M365-Lab/M365-Tenant-7.png)  

In Azure Active Directory Portal navigate to **"Properties"**

![](/assets/images/M365-Lab/M365-Tenant-8.png)  

## Custom domian for LAB propuse without paying (LAB / PoC)

### How to obtain domain ?

Domains can be bough on any DNS provider on your country or region, choosing domains is only depending on business needs. But for LAB or PoC propuse some time is good to have dedicated domin for testing and good representation of bussines case, but there is non free domain. 

But with help of community and with credentials for  "Gerenios Ltd" we can generated free domain dedicated for Office365 with names:

>
> **Notice:** Those domains are only LAB and POC propuse, don't use them for production. 
>

* MyO365.site
* MyO365.net
* MyO365.online

Orginal link to post: [https://o365blog.com/post/my-o365-site/](https://o365blog.com/post/my-o365-site/) 
### DNS records for Office 365

**Step 1:** Open a browser in InPrivate session, and navigate in browser to [https://portal.office.com/](https://portal.office.com/) .
**Step 2:** Login with credentials created in previous step like **adm.local@*****.onmicosoft.com**.
**Step 3:** On diffrent tab navigate to link [https://www.myo365.site/](https://www.myo365.site/) .
**Step 4:** On page click **"Login"**.

![](/assets/images/M365-Lab/M365-Lab-Domain-1.png)

**Step 5:** Approve delegation and permission for Tenant.

![](/assets/images/M365-Lab/M365-Lab-Domain-2.png)

**Step 6:** Enter friendly name of domain like **sys4ops** or if this demo **demo{Random Number}** example **demo53567.MyO365.site**, don't close tab in browser.

![](/assets/images/M365-Lab/M365-Lab-Domain-3.png)

**Step 7:** On next tab in browser go to  [https://admin.office.com/](https://admin.office.com/)  
**Step 8:** In Admin panel go to **Setup** and **Domains**.  
**Step 9:** Click on **Add domain**.  

![](/assets/images/M365-Lab/M365-Lab-Domain-5.png) 

**Step 10:** Enter your domain name generated in step 6 like in example **demo53567.MyO365.site**.

![](/assets/images/M365-Lab/M365-Lab-Domain-6.png)  

**Step 11:** Confirm usage of domain

![](/assets/images/M365-Lab/M365-Lab-Domain-6-1.png) 

**Step 12:** Domain verification option in Office 365 chose **"TXT"** DNS record

![](/assets/images/M365-Lab/M365-Lab-Domain-7.png) 

**Step 13:** Copy value from **"TXT Value"**

![](/assets/images/M365-Lab/M365-Lab-Domain-8.png) 


**Step 14:** Go back to tab form step 6 ([https://www.myo365.site/](https://www.myo365.site/)) and past **"TXT Value"** to domain verification failed and **"Save"**.  

![](/assets/images/M365-Lab/M365-Lab-Domain-9.png) 

**Step 15:** Go back to tab with [https://admin.office.com/](https://admin.office.com/) and start domain verification, click **"Veryfie"**.  


![](/assets/images/M365-Lab/M365-Lab-Domain-10.png) 


**Step 16:** In section **"Add DNS record"** go to bottom and expand **"Advance"** and select options for **"Skype for Bussines (Teams)"** and **"Intune"**

![](/assets/images/M365-Lab/M365-Lab-Domain-11.png) 
![](/assets/images/M365-Lab/M365-Lab-Domain-11-1.png) 
![](/assets/images/M365-Lab/M365-Lab-Domain-11-2.png) 


## Activate trial licences

Microsoft allow to activate **once** peer tenant trial licences for diffrent products on different versions, trial licences are activate for 30 days and sometime can be extended to another 30 days. 
When we prepare pre-production tenant we can add trial licences and start PoC without paying money, onboard pilot users for testing. 

**Trial expired:**

* We cannot on the same tenant launch another trial for the same version of product and licences level.
* Content will be stored for 30 days, of license won't be active content will be deleted.
* Adding production licences we don't have to re assign them to users if are in the same licences level. Trail will expired production licences will be active. **Example:** Trial license was M365 E5 and production bought by organization are M365 E5 as well.

### Office 365


### Intune (Enterprise Mobility Suite)

## Create Custom Accounts


