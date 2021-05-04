---
title: "Microsoft 365 Setup Lab Environment M365 and Intune with free domain names"
excerpt: "Microsoft 365 Setup Lab Environment - Create your own Lab with domains setup or prepare new tenant for you company. In this article we will go through creating new tenant, add custom domains for LAB purpose, assigned licenses. " 
toc: true
classes: wide
header:
  #image: /assets/images/M365-Lab/M365-Lab-Home.png
  teaser: /assets/images/M365-Lab/M365-Lab-Home.png
categories:
  - SysOps
  - Configuration
tags:
  - M365
  - Intune
published: false
hidden: true
---

<p align="center">
<img src="/assets/images/M365-Lab/M365-Lab-Home.png?raw=true" alt="Microsoft 365 Setup Lab Environment M365 and Intune with free domain names."/>
</p>

## Why I write this article

Hi, I wrote this article to help with creation of first Microsoft 365 tenant that can be convert to production environment. In this article I will highlight some point to avoid mistake that I have made in past, all information are base on my experience. 

## Prerequisites 

Before we begin with this you will need to consider few things and answer questions:  

+ Is this will be LAB environment and will not be converted to production.
  + LAB Only: Tenant name suppose not be connected to organization name, once used tenant name cannot be reused or changed. **Example:** demo{Random Numbers} 
  + LAB Only: Use the highest licenses like E5 to check all settings and dependencies for compare.
  + LAB Only: Don't use production custom domains, domain can be assigned only to one **Tenant**.
  + LAB Only: Location of Tenant don't need to be compliance with organization location.
+ Is this PoC Pilot environment that will be converted to production.
  + PoC: Tenant name suppose not be connected to organization name, once used tenant name cannot be reused or changed. **Example:** poc{Random Numbers} 
  + PoC: Use the highest licenses like E5 to check all settings and dependencies for compare.
  + PoC: Collect user case that you like to present.
  + PoC: Don't use production custom domains, domain can be assigned only to one **Tenant**.
  + PoC: Location of Tenant need to be compliance with organization location.
+ Is this Pilot environment that will be converted to production.
  + Pilot: Tenant name need to be connected to Organization for adoption propose.
  + Pilot: Tenant name need to be approve by business because will be used as first part of URL in **SharePoint** and **OneDrive**
  + Pilot: Use licenses that match Organization business cases to avoid misconfiguration.
  + Pilot: Don't use production custom domains, to avoid data flow distributions. 
  + Pilot: Location of Tenant need to be compliance with organization location.

>   
> **Notice:** Tenant **"Name"** cannot be change after creation.
>  
> **Notice:** Tenant **"Location"** cannot be change after creation.
>  
>


![](/assets/images/M365-Lab/M365-Tenant-decision.png)

--------------------------
## First Azure Active Directory Tenant that will host our data.

Why we need new Azure Active Directory Tenant ?

* New **tenant** will be created because your organization starts testing Microsoft 365.
* New **tenant** will be crated if our organization migrate from on-prem solutions to Microsoft365.
* New **tenant** can be created because our organization need Lab environment and don't like mess up with existing configuration.
* New **tenant** can be created because 2 organizations merge to new one.
* New **tenant** can be created because organization is rebranding.
* New **tenant** name of tenant is not valid by organization.


### What is Tenant ?

 **Tenant** Is isolated dedicated space for our organization and our assets. The Tenant is the container for items of your Organization such as users, domains, subscriptions, devices, permissions, Office 365 Data. Tenant is fundamental for all services in Microsoft Cloud.

**Example** of data isolation in Microsoft tenant

![](/assets/images/M365-Lab/M365-Lab-Tenant.png)


Microsoft for make simple onboarding organization to Office365 or Azure, sometimes provision tenant automatically without passing any information, as the name of tenant is using e-mail address and location is take form our accounts.   
**Example** of this scenario when tenant can be create without any prompt. 
 - MSDN Subscriptions.
 - Azure free trial Subscriptions.  

 **LAB** Tenant and won't be used for production so in this case name and location doesn’t matter, but if we will create **PoC** or **Pilot** tenant that in feature can be converted to production we have to be more aware when data will be stored and what name is used.  

>
> **Notice:** Tenant **"Location"** have impact on services that we can use in Office 365, for example **Phone Systems**
>  

### Create Azure Active Directory Tenant

**Step 1:** Open a browser in InPrivate session, and navigate in browser to [https://account.azure.com/organization/](https://account.azure.com/organization/) 

![](/assets/images/M365-Lab/M365-Tenant-1.png)

**Step 2:** Fill up date of your organization

**Country** - Location of tenant and Datacenter region, for data processing.
**Name and Surname** - use for display on first account
**Contact Number** - for presales and support.
**Organization Name** - Display name of organization it will be used in adoption process, can be change latter.
**Size** - information only.

![](/assets/images/M365-Lab/M365-Tenant-2.png)

**Step 3:** Tenant creation data

**Account ID** - Will be used to create first user in our **tenant**.  
>
> **Best practice:** As Account ID plase don't use standard names like: Administrator, admin. 
> **Best practice:** Use first account to create your admin account and assign rights.
>
**Domain Name** - Will be used to create default domain in our **tenant**, cannot be change and will be used to create URL's for **SharePoint** and **OneDrive**.

![](/assets/images/M365-Lab/M365-Tenant-3.png)  

**Step 4:** Verify your identity by SMS code or phone call

![](/assets/images/M365-Lab/M365-Tenant-4.png)

![](/assets/images/M365-Lab/M365-Tenant-5.png)

**Step 5:** Finish creation of tenant.

![](/assets/images/M365-Lab/M365-Tenant-6.png)

**Step 6:** Verification of tenant settings. Log on to Azure Active Directory admin center by URL: [https://aad.portal.azure.com/](https://aad.portal.azure.com/)

In Azure Active Directory Portal navigate to **"Overview"**  

![](/assets/images/M365-Lab/M365-Tenant-7.png)  

In Azure Active Directory Portal navigate to **"Properties"**

![](/assets/images/M365-Lab/M365-Tenant-8.png)  

## Custom domain for LAB purpose without paying (LAB / PoC)

### How to obtain free custom domain name for M365 Tenant?

Domains can be bough on any DNS provider on your country or region, choosing domains is only depending on business needs. But for LAB or PoC propose some time is good to have dedicated domain for testing and good representation of business case, but there is non free domain. 

But with help of community and with credentials for  "Gerenios Ltd" we can generated free domain dedicated for Office365 with names:

>
> **Notice:** Those domains are only LAB and POC purpose, don't use them for production. 
>

* MyO365.site
* MyO365.net
* MyO365.online

Original link to post: [https://o365blog.com/post/my-o365-site/](https://o365blog.com/post/my-o365-site/), all rights reserved to **Nestori Syynimaa**
### DNS records for Office 365

**Step 1:** Open a browser in InPrivate session, and navigate in browser to [https://portal.office.com/](https://portal.office.com/) .
**Step 2:** Login with credentials created in previous step like **adm.local@*****.onmicosoft.com**.
**Step 3:** On different tab navigate to link [https://www.myo365.site/](https://www.myo365.site/).  
**Step 4:** On page click **"Login"** and if you are ask for credentials use your account that was created for new **tenant** with global administrator rights.  

![](/assets/images/M365-Lab/M365-Lab-Domain-1.png)

**Step 5:** Approve delegation and permission for Tenant.

![](/assets/images/M365-Lab/M365-Lab-Domain-2.png)

**Step 6:** Enter friendly name of domain like **sys4ops** or if this demo **demo{Random Number}** example **demo53567.MyO365.site**, don't close tab in browser.

![](/assets/images/M365-Lab/M365-Lab-Domain-3.png)

**Step 7:** On next tab in browser go to  [https://admin.office.com/](https://admin.office.com/).  
**Step 8:** In Admin panel go to **Setup** and **Domains**.  
**Step 9:** Click on **Add domain**.  

![](/assets/images/M365-Lab/M365-Lab-Domain-5.png) 

**Step 10:** Enter your domain name generated in step 6 like in example **demo53567.MyO365.site**.  

![](/assets/images/M365-Lab/M365-Lab-Domain-6.png)  

**Step 11:** Confirm usage of domain.  

![](/assets/images/M365-Lab/M365-Lab-Domain-6-1.png) 

**Step 12:** Domain verification option in Office 365 chose **"TXT"** DNS record.  

![](/assets/images/M365-Lab/M365-Lab-Domain-7.png) 

**Step 13:** Copy value from **"TXT Value"**.  

![](/assets/images/M365-Lab/M365-Lab-Domain-8.png) 


**Step 14:** Go back to tab form step 6 ([https://www.myo365.site/](https://www.myo365.site/)) and past **"TXT Value"** to domain verification failed and **"Save"**.  

![](/assets/images/M365-Lab/M365-Lab-Domain-9.png) 

**Step 15:** Go back to tab with [https://admin.office.com/](https://admin.office.com/) and start domain verification, click **"Verify**.  


![](/assets/images/M365-Lab/M365-Lab-Domain-10.png) 


**Step 16:** In section **"Add DNS record"** go to bottom and expand **"Advance"** and select options for **"Skype for Business (Teams)"** and **"Intune"**  

![](/assets/images/M365-Lab/M365-Lab-Domain-11.png) 
![](/assets/images/M365-Lab/M365-Lab-Domain-11-1.png) 
![](/assets/images/M365-Lab/M365-Lab-Domain-11-2.png) 


## Activate trial license

Microsoft allow to activate **once** peer tenant trial license for different products on licenses level, trial licenses are activate for 30 days and sometime can be extended to another 30 days. 
When we prepare pre-production tenant we can add trial license and start PoC without paying any money and onboard pilot users for testing. 

> **Trial expired:**
>
> * We cannot on the same tenant launch another trial for the same version of product and license level.
> * Content will be stored for 30 days, of license won't be active content will be deleted.
> * Adding production licenses we don't have to re assign them to users if are in the same license level. Trail will expired production licenses will be active. **Example:** Trial license was M365 E5 and production bought by organization > are M365 E5 as well.
>  

### Microsoft 365

Activating Microsoft 365 trial license, if your business is using Office 365 you can active separately. Chose license level and bundle to you business requirements, in LAB environment I will use the highes that is E5.
* Office 365 Trial (E3/E5) - License plans: [Microsoft 365](https://www.microsoft.com/en-us/microsoft-365/business/compare-all-microsoft-365-business-products?&activetab=tab:primaryr2)
* Enterprise Mobility Suite) - Intune bundle licenses are described in this post: [Intune-for-beginners/#choosing-license](https://sysopslife.sys4ops.pl/2021/04/03/Intune-for-beginners/#choosing-license)

Kudos to this blog what you can check license SKU compare: [Compare Microsoft office 365 plans/](https://lazyadmin.nl/compare-microsoft-office-365-plans/)


**Step 1:** In browser go to URL [https://admin.office.com/](https://admin.office.com/)and login with global administrator credentials. 

![](/assets/images/M365-Lab/M365-Licences-1.png) 

**Step 2:** In left navigation menu go to **"Billing"** and **"Purchase Services"**.

![](/assets/images/M365-Lab/M365-Licences-2.png) 


**Step 3:** On search  box please type license name or level to filter information, in this example we like to lunch Intune and Office 365, so we are choosing to use Microsoft 365 **E5** as a trial. Click on choose license.

![](/assets/images/M365-Lab/M365-Licences-3.png) 

**Step 4:** In license details navigate to **Start free trial**.

![](/assets/images/M365-Lab/M365-Licences-4.png) 

**Step 5:** Confirm that you are not robot and confirm **trial**.

![](/assets/images/M365-Lab/M365-Licences-5.png) 
![](/assets/images/M365-Lab/M365-Licences-6.png) 
![](/assets/images/M365-Lab/M365-Licences-7.png) 

**Step 6:** Trial licenses are available and can be assigned to user or groups.
## Create new Azure AD user (Cloud only) that will be add to group with licences.

>
> **Notice:** User can be synchronize from local Active Directory. 
>

**Step 1:** Install PowerShell modules.

```powershell

Install-module AzureAD

```

**Step 2:** Check that AzureAD or AzureADpreview is installed.

```powershell

# Check that Azure AD preview is installed
Get-InstalledModule -Name AzureADPreview

Version    Name                                Repository           Description                                                                                                              
-------    ----                                ----------           -----------                                                                                                              
2.0.2.134  AzureADPreview                      PSGallery            Azure Active Directory V2 Preview Module. ...                                                                            

# Check that Azure AD is installed

Get-InstalledModule -Name AzureAD
                                                                         


```

**Step 3:** Log on to Azure Active Directory.

```powershell

# Connect to Azure Active Directory

Connect-AzureAD 

Account                                 Environment TenantId                             TenantDomain                  AccountType
-------                                 ----------- --------                             ------------                  -----------
adm.local@demoM36534556.onmicrosoft.com AzureCloud  d4520405-eabf-47ea-998f-15c7f9d4b845 demoM36534556.onmicrosoft.com User       


```

**Step 3:** Create example accounts

```powershell

# Enter your domian of tenant that will be used as UPN
$userDomain = "@demoM36534556.onmicrosoft.com"

$PasswordProfile = New-Object -TypeName Microsoft.Open.AzureAD.Model.PasswordProfile
$PasswordProfile.Password = "Your test Users passwords"

# Create example users

New-AzureADUser -DisplayName "John Doe" -UserPrincipalName John.Doe$userDomain -MailNickName "JohnDoe" -AccountEnabled $true -PasswordProfile $PasswordProfile
New-AzureADUser -DisplayName "Jane Doe" -UserPrincipalName Jane.Doe$userDomain -MailNickName "JaneDoe" -AccountEnabled $true -PasswordProfile $PasswordProfile
New-AzureADUser -DisplayName "Tom Smith" -UserPrincipalName Tom.Smith$userDomain -MailNickName "TomSmith" -AccountEnabled $true -PasswordProfile $PasswordProfile


<#

Additional data for account with information

   [-City <String>]
   [-CompanyName <String>]
   [-Country <String>]
   [-Department <String>]
   [-GivenName <String>]
   [-JobTitle <String>]
   [-Mobile <String>]
   [-PhysicalDeliveryOfficeName <String>]
   [-PostalCode <String>]
   [-PreferredLanguage <String>]
   [-ShowInAddressList <Boolean>]
   [-State <String>]
   [-StreetAddress <String>]
   [-Surname <String>]
   [-TelephoneNumber <String>]
   [-UsageLocation <String>]
   [-UserPrincipalName <String>]
   [-UserState <String>]

#>


```
## Link: Create Custom accounts and groups for license assignment

Guide how to create groups in Azure Active Directory: [Group creation in Azure Active Directory](https://sysopslife.sys4ops.pl/2021/04/24/M365-AADGroups-with-license/)

## Link: Intune configuration for first time

Guide Intune (Endpoint Manager) - How to start the implementation and what to pay attention to: [Intune (Endpoint Manager) - How to start the implementation and what to pay attention to](https://sysopslife.sys4ops.pl/2021/04/03/Intune-for-beginners/)
