---
title: "ADConnect migration error on Out sync rules"
excerpt: "How to handle error caused by Out rules in migration process, that is pointing to attributes mapping: AttributeFlowMapping's specified target attribute 'extension_87ce92dfb96b4ac689f8fxxxxxxxxxx6_extensionAttribute1' is not a defined attribute type." 
toc: true
classes: wide
categories:
  - SysOps
  - Configuration
  - Migration
tags:
  - M365
  - ADConnect
published: true
hidden: false
---

## Why I write this article

Hi, In last migration process of connect between servers I was using new preview feature, that is allowing exporting existing configuration like
* Synchronization filters
* synchronization rules 
* Account settings
* Sing In settigs 
* etc.  

In some custom implementation AD connect have Out and In synchronization rules, and migration process don't work for Out rules but rest is working so we like to use it for 90 % of configuration. So in this post I will write workaround for this issue.

## How to prepare AD connect to migration 

Whole process is described in Microsoft docs under this link: 
[Migrate settings from an existing server](https://docs.microsoft.com/en-us/azure/active-directory/hybrid/how-to-connect-import-export-config#migrate-settings-from-an-existing-server)

## Error when importing AD connect settings with custom **Out** rules

**Step 1:** In install AD connect process, choose custom and select **Import Synchronization settings**

![](/assets/images/AdConnect/ADC-01.PNG)

**Step 2:** Follow steps in installation wizard until it finished with error

![](/assets/images/AdConnect/ADC-02.PNG)
![](/assets/images/AdConnect/ADC-03.PNG)
![](/assets/images/AdConnect/ADC-04.PNG)
![](/assets/images/AdConnect/ADC-05.PNG)
![](/assets/images/AdConnect/ADC-06.PNG)

**Step 3:** Log verification to check with rule is causing issue, logs file are located on folder: **C:\ProgramData\AADConnect** or you can navigate from AD connect interface. File name starts:  **trace**
>
> Exception Data (Raw): Microsoft.Online.Deployment.PowerShell.PowerShellInvocationException: Out to AAD - User DirectoryExtension - Cloned - 10/14/2019 11:09:04 AM **(0b0cfd4f-e8b8-47b2-8a15-b0557cef3949)**: AttributeFlowMapping's specified target attribute 'extension_87ce92dfb96b4ac689f8f53836bffaa6_extensionAttribute1' is not a defined attribute type.
>

Microsoft.IdentityManagement.PowerShell.Cmdlet.AddADSyncRuleCmdlet


![](/assets/images/AdConnect/ADC-07.PNG)