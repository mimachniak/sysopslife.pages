---
title: "[EN] Intune (Endpoint Manager) - How to start the implementation and what to pay attention to."
classes: wide
excerpt: "[EN] Intune (Endpoint Manager) - How to start the implementation and what to pay attention to"
toc: true
header:
  image: /assets/images/Intune/Intune-1.png
categories:
  - Intune
  - MDM
  - SysOps
tags:
  - Intune
  - MDM
  - Licences
---
## Why I write this article

This guide is create to help other IT pros that are starting with Office 365 / Intune services to help with base configuration of Intune and don't make the same mistake. Fallow this guide you will have global configuration of Intune ready.
## Whats is Intune or with new name Endpoint Manager

Intune is a 100% cloud-based mobile device management (MDM) and mobile application management (MAM) provider for your apps and devices. It lets you control features and settings 

![](/assets/images/Intune/intunearchitecture_wh.svg)

## Devices and Operating System that can be controlled by Intune (Endpoint Manager)

### Apple
* Apple iOS 12.0 and later
* Apple iPadOS 13.0 and later
* Mac OS X 10.13 and later

### Google
* Android 5.0 and later (including Samsung KNOX Standard 2.4 and higher)
* Android enterprise

### Microsoft
  
* Surface Hub.
* Windows 10 (Home, S, Pro, Education, and Enterprise versions).
* Windows 10 Enterprise 2019 LTSC.
* Windows 10 IoT Enterprise (x86, x64).
* Windows Holographic for Business.
* Windows 10 Teams (Surface Hub).
* Windows 10 1709 (RS3) and later, 
* Windows 8.1 RT, PCs running Windows 8.1 (Sustaining mode)

## Choosing license

Intune as a part of M365 is added licensing bundles to deliver all products that are necessary to manage organization.  
List of licences bundles that contain Intune (Endpoint Manager) User License.

* Microsoft 365 Bussines Premium
* Microsoft 365 E3 and E5

Extension bundle for Office 365 packed.
* Enterprise Mobility Suite E3 and E5

Standalone Intune licences

* User licenses in Microsoft Intune
* Device licenses in Microsoft Intune

`Note: Device licenses limits`
* Limits: Intune app protection policies
* Limits: Conditional access
* Limits: User-based management features, such as email and calendaring.

Bundle licences are more cost effective and give additional features to services.  
Example with price found on Microsoft sites: 

| Type | EMS - E3  | User licenses in Microsoft Intune |
| ------------- | ------------- | ------------- |
| Cost | 8.80 $   | 8 $ |

Pricing and features of EMS: [Enterprise Mobility + Security](https://www.microsoft.com/pl-pl/microsoft-365/enterprise-mobility-security/compare-plans-and-pricing "link title")  


`Note: If your company won't buy bundle license please consider buying Azure Active Directory Premium 1 to enabled "Conditional access features" and auto enrollment devices when they join Azure Active Directory`

## Steps for first configuration

### DNS configuration in Office Admin Panel

`Note: To perform this task you need to have access to DNS Server or host provider.`

**Step 1:** Create DNS entries

| Type | Name  | Value | TTL |
| ---| -- | ---- | ---- |
| CANME | EnterpriseEnrollment.CustomDomainName.com  | EnterpriseEnrollment.manage.microsoft.com | 3600 |
| CANME | EnterpriseRegistration.CustomDomainName.com | EnterpriseRegistration.windows.net | 3600 |

> **EnterpriseEnrollment** - To simplify enrollment, create a domain name server (DNS) alias (CNAME record type) that redirects enrollment requests to Intune servers. Otherwise, users trying to connect to Intune must enter the Intune server name during enrollment.  
>
> **EnterpriseRegistration**- Azure Active Directory has a different CNAME that it uses for device registration for iOS/iPadOS, Android, and Windows devices. Intune conditional access requires devices to be registered, also called "workplace joined".

Example:   
![](/assets/images/Intune/Intune-dns-2.png)

**Step 3:** Log on to Office 365 admin center by URL: [https://admin.microsoft.com](https://admin.microsoft.com)  
**Step 4:** On left navigation menu go to:  **Setup** --> **Domains**  
Edit existing **Custom Domain Name** and follow wizard

![](/assets/images/Intune/Intune-dns-3.png)

Add NEW **Custom Domain Name** and follow wizard

![](/assets/images/Intune/Intune-dns-4.png)

### Azure Active Directory configuration

**Step 1:** Log on to Azure Active Directory admin center by URL: [https://aad.portal.azure.com/](https://aad.portal.azure.com/)

![](/assets/images/Intune/intune-aad-1.PNG)

**Step 2:** On left navigation menu go to **"Azure Active Directory"**

**Step 3:** In options navigate to **"Mobility (MDM and MAM)"**

![](/assets/images/Intune/intune-aad-2.PNG)


**Step 4:** Select **"Microsoft Intune"**

![](/assets/images/Intune/intune-aad-3.PNG)

**Step 5:** Select **"Microsoft Intune configuration MDM and MAM"**

![](/assets/images/Intune/intune-aad-4.PNG)

Prerequisites

* Azure Active Directory Premium mini Plan 1  
* Microsoft Intune subscription

`Note: Microsoft 365: Premium, E3, E5 and Enterprise Mobility Suite E3 and E5 contian Azure Active Directory Premium 1`

**MDM user scope**

This section configure auto enrollment registered devices to Intune (EndPoint Manager). Auto Enrollment will be run in background task if: 

* Users add their work account to their personally owned devices
* Users join corporate-owned devices to Azure Active Directory

None - MDM automatic enrollment disabled  
Some - Select the Groups for Azure Active Directory (Synchronized groups from local Active Directory) that can automatically enroll their Windows 10 devices  
All - All users can automatically enroll their Windows 10 devices 

`Note: For pilot implementation limit to group`  
`Note: Azure AD join in hybrid model require additional configuration`  

> **Important and when use the same group for MDM and MAM**  
>
> If both the [MAM](https://docs.microsoft.com/en-us/mem/intune/apps/app-management) user scope and the MDM user scope (automatic MDM enrollment) are enabled for all users (or the same groups of users). The device will not be MDM enrolled, and Windows Information Protection (WIP) Policies will be applied if you have configured them.
>
>If your intent is to enable automatic enrollment for Windows BYOD devices to an MDM: configure the MDM user scope to All (or Some, and specify a group) and configure the MAM user scope to None (or Some, and specify a group – ensuring that users are not members of a group targeted by both MDM and [MAM](https://docs.microsoft.com/en-us/mem/intune/apps/app-management) user scopes).
>
> For corporate devices, the MDM user scope takes precedence if both MDM and [MAM](https://docs.microsoft.com/en-us/mem/intune/apps/app-management) user scopes are enabled. The device will get automatically enrolled in the configured MDM.  
>  


[What is MAM - Mobile Application Management](https://docs.microsoft.com/en-us/mem/intune/apps/app-management) 

## Intune (Endpoint Manager) Console

To access Intune (Endpoint Manager) web console you will need support browser (Edge, FireFox, Chrome ... not Internet Explorer) and we have 3 options to go

1. In web browser typ URL: [https://endpoint.microsoft.com/](https://endpoint.microsoft.com/) 
2. Office 365 Admin Center:  [https://admin.microsoft.com](https://admin.microsoft.com), on left navigation menu choose **"Endpoint Manager"** and you will be redirected to option 1.
3. Portal Azure: [https://portal.azure.com](https://portal.azure.com) in global search field type **"Intune"** and you will be redirected to option 1.

![](/assets/images/Intune/intune-em-1.png)

### Tenant Administration - Customization

**Step 1:** Go to Tenant Administration in Endpoint Manager web console

![](/assets/images/Intune/intune-em-2.png)

**Step 2:** On navigation menu go to **"Customizations"**

In this section we will configure how end user will see **"Company Portal"** on Web / Mobile or Desktop. In customization we have few options to adjust look up to our company layout. We can customize
* Organization name​
* Theme color
* Support information
* Configuration allowed by **"Company Portal"**


`Note: This is the default customization that's applied to all users and devices. It can be edited, but not deleted`

![](/assets/images/Intune/intune-em-3.png)

How End user will see oure settings to check this we can go to URL: [https://portal.manage.microsoft.com/](https://portal.manage.microsoft.com/). In **"Company Portal"** End user have:

* List of all devices and settings
* Access to application
* Notifications
* HelpDesk contact information


![](/assets/images/Intune/intune-em-4.png)  
![](/assets/images/Intune/intune-em-5.png)  
![](/assets/images/Intune/intune-em-6.png)  

### Tenant Administration - – Connectors and tokens
