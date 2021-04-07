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

> EnterpriseEnrollment - To simplify enrollment, create a domain name server (DNS) alias (CNAME record type) that redirects enrollment requests to Intune servers. Otherwise, users trying to connect to Intune must enter the Intune server name during enrollment.  
> EnterpriseRegistration - Azure Active Directory has a different CNAME that it uses for device registration for iOS/iPadOS, Android, and Windows devices. Intune conditional access requires devices to be registered, also called "workplace joined".

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

![](/assets/images/Intune/intune-aad-1.png)

**Step 2:** On left navigation menu go to **"Azure Active Directory"**

![](/assets/images/Intune/intune-aad-2.png)

**Step 3:** In options navigate to **"Mobility (MDM and MAM)"**

![](/assets/images/Intune/intune-aad-3.png)

**Step 4:** Select **"Microsoft Intune"**

![](/assets/images/Intune/intune-aad-4.png)