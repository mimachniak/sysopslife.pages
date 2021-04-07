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
Example with price found on Microsft sites: 

| Type | EMS - E3  | User licenses in Microsoft Intune |
| ------------- | ------------- | ------------- |
| Cost | 8.80 $   | 8 $ |

Pricing and features of EMS: [Enterprise Mobility + Security](https://www.microsoft.com/pl-pl/microsoft-365/enterprise-mobility-security/compare-plans-and-pricing "link title")

## Steps for first configuration

### DNS configuration in Office Admin Panel

`Note: To perform this task you need to have access to DNS Server or host provider.`

#### Prerequisites 