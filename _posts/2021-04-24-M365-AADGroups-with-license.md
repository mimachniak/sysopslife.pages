---
title: "[EN] Azure Active Directory License assignment base on groups."
excerpt: "[EN] Azure Active Directory License assignment base on groups - keeping licenses assignment for user in organization when we have different variants and levels on different type of user can be very hard, that why to keep standard we are using group assigment of licences." 
toc: true
classes: wide
header:
  image: /assets/images/M365-Lab/M365-Lab-Groups-main.png
  teaser: /assets/images/M365-Lab/M365-Lab-Groups-main.png
  og_image: /assets/images/M365-Lab/M365-Lab-Groups-main.png
categories:
  - SysOps
  - PowerShell
  - Configuration
tags:
  - M365
  - Azure
  - Licences
published: true
hidden: false
---

## Why I write this article

Hi, I wrote this article to help with license management and keeping standard in organization. Most of organization in process of onboarding need assignment license to employee base on function, group assignment licenses simplify this step.

## Prerequisites

In this article we will use PowerShell so modules that need to be install to perform all steps.

```powershell
Install-module AzureADLicensing
Install-module AzureAD
Install-module Az
```

**License requirement** 
To use group base license assignment, you need have Azure Active Directory Tenant on level P1 or P2.

## Azure Active Directory Group creation

**Step 1:** Log on to Azure Active Directory admin center by URL: [https://aad.portal.azure.com/](https://aad.portal.azure.com/)

![](/assets/images/Intune/intune-aad-1.PNG)

**Step 2:** On left navigation menu go to **"Azure Active Directory"**

**Step 3:** On left menu navigate to **Groups**

![](/assets/images/M365-Lab/M365-Lab-Groups-1.PNG)

**Step 4:** Click on **"New group"** to start process of creation

![](/assets/images/M365-Lab/M365-Lab-Groups-2.PNG)

**Step 5:** Fill up data for group and **"Create"**
* Group type - security
* Group name -  base on your naming convention in organization
* Group description 

![](/assets/images/M365-Lab/M365-Lab-Groups-3.PNG)

**Step 6:** Verified that group was created, and click on group name to go to settings of group.


![](/assets/images/M365-Lab/M365-Lab-Groups-4.PNG)

**Step 7:** Verified that group was created, and click on group name to go to settings of group. In group settings on menu go to **"Licenses"** options.

![](/assets/images/M365-Lab/M365-Lab-Groups-9.PNG)

**Step 8:** Click in **"Licenses"** section, **"Assignments"**.

> Best practice: We don't need to activate all services in group, please choose those services that are onboarded to your organization.  
> Best practice: Different group can have different services enabled. If you assign groups with different services to user, they will merge.  

![](/assets/images/M365-Lab/M365-Lab-Groups-6.PNG)


**Step 9:** Select licenses **(1)** that you like to assign to group. Not this that in organization you can have multiple licenses. After assignment go back to **"Group Settings (2)"** in navigation. 

![](/assets/images/M365-Lab/M365-Lab-Groups-7.PNG)

**Step 10:** Verify that licenses are assigned.
* Product - License name and level like Exchange Plan 1, Microsoft 365 E5
* State - Active or expired. 
* Enable Services - number of active services, like Teams, Exchange etc.  

> Best practice: Different group can have different services enabled. If you assign groups with different services to user, they will merge. 

**Step 11:** Add users to group, in group **Settings** in menu navigate to **"Members" (1)** and click **"Add members" (2)** and typ user name that you like to add to group.


![](/assets/images/M365-Lab/M365-Lab-Groups-10.PNG)  
![](/assets/images/M365-Lab/M365-Lab-Groups-11.PNG)

**Step 13:** Go to user **settings** on menu go to **"Licenses"** options, and verified that licenses are assigned and inherited form group.

![](/assets/images/M365-Lab/M365-Lab-Groups-12.PNG)
## Azure Active Directory Group creation with PowerShell  

**Step 1:** Install PowerShell modules.

```powershell
Install-module AzureADLicensing
Install-module AzureAD
Install-module Az
```


**Step 2:** Log on to Azure Active Directory.

```powershell

# Connect to Azure Active Directory

Connect-AzureAD 

Account                                 Environment TenantId                             TenantDomain                  AccountType
-------                                 ----------- --------                             ------------                  -----------
adm.local@demoM36534556.onmicrosoft.com AzureCloud  d4520405-eabf-47ea-998f-15c7f9d4b845 demoM36534556.onmicrosoft.com User       


```

**Step 3:** Log on to Azure Account.

```powershell

# Connect to Azure

Connect-AzAccount 

Account                                 SubscriptionName TenantId                             Environment
-------                                 ---------------- --------                             -----------
adm.local@demoM36534556.onmicrosoft.com                  d4520405-eabf-47ea-998f-15c7f9d4b845 AzureCloud

```


**Step 4:** Create security group in Azure AD. Copy **ObjectId** of group.

```powershell

# Create New Security group

New-AzureADGroup -DisplayName "GLA-M365E5-PS-Full" -Description "Group for license assignment" -SecurityEnabled $true -MailEnabled $false -MailNickName "NotSet" 

ObjectId                             DisplayName        Description                 
--------                             -----------        -----------                 
e6a5dbb0-5a2d-44c3-a23f-c4b841d27040 GLA-M365E5-PS-Full Group for license assignment


```

**Step 5:** Get all License type and data available in tenant. Copy **accountSkuId**

```powershell
# Get all licences type and data

Get-AADLicenseSku 

name            : Microsoft 365 E5
accountId       : d4520405-eabf-47ea-998f-15c7f9d4b845
accountSkuId    : demoM36534556:SPE_E5
availableUnits  : 23
totalUnits      : 25
consumedUnits   : 2
skuId           : 06ebc4ee-1bb5-47dd-8120-11324bc54e06
isDepartment    : False
warningUnits    : 0
serviceStatuses : {@{provisioningStatus=Success; servicePlan=}, @{provisioningStatus=Success; servicePlan=}, @{provisioningStatus=Success; servicePlan=}, @{provisioningStatus=Success; servicePlan=}...}

```

**Step 6:** List all groups in Azure AD to get Object ID of group.

```powershell

# List all groups in Azure AD

Get-AzureADGroup 

ObjectId                             DisplayName        Description
--------                             -----------        -----------
21e83992-076d-45f6-8cb7-d588448703b9 All Company        This is the default group for everyone in the network
66736b4a-b3e4-418c-b396-12b1bd2aed04 demoM365Lab        demoM365Lab
cdda1741-9077-4021-896f-30191a6c70df GLA-M365E5-Full
e6a5dbb0-5a2d-44c3-a23f-c4b841d27040 GLA-M365E5-PS-Full Group for license assignment

```

**Step 7:** Assign M365 Full license to security group. Enter data from previous steps: **ObjectId** when licenses will be assigned, and license SKU ID: **accountSkuId**.

```powershell
# Assign M365 Full license to group created

Add-AADGroupLicenseAssignment -groupId "e6a5dbb0-5a2d-44c3-a23f-c4b841d27040" -accountSkuId demoM36534556:SPE_E5 

```

**Step 8:** # Full script for group creation and licences assigment with passing data.

```powershell

$AADGroupName = "GLA-M365E5-PS2-Full"
$licencesSSKUName = "Microsoft 365 E5"


# Create New Security Group

$AADGroup = Get-AzureADGroup -SearchString $AADGroupName

if (([string]::IsNullOrEmpty($AADGroup))) # check that group exist
{

    Write-Host "Creating Azure AD group for licence base" -ForegroundColor Green
    
    $AADGroup = New-AzureADGroup -DisplayName $AADGroupName -Description "Group for license assignment" -SecurityEnabled $true -MailEnabled $false -MailNickName "NotSet"
    $AADGroupId = $AADGroup.ObjectId 

}
else
{

    Write-Host "Azure AD [$($AADGroup.DisplayName)] group for licence base alredy exist" -ForegroundColor Yellow

}


# Get SKU licences by Name

Write-Host "Get licence inforation" -ForegroundColor Green

$m365Sku= Get-AADLicenseSku | Where-Object {$_.name -match $licencesSSKUName}
$m365SkuID = $m365Sku.accountSkuId

# Assign M365 Full license to group created

Write-Host "Group License Assignment : $($AADGroup.DisplayName)" -ForegroundColor Green

Add-AADGroupLicenseAssignment -groupId $AADGroupId -accountSkuId $m365SkuID

# Verify license assignment

Get-AADGroupLicenseAssignment -groupId $AADGroupId

```

## GitHub link to repository of scripts

Full script can be found on my GitHUB: [https://github.com/mimachniak/sysopslife-scripts/blob/master/AAD/AAD-Groups-with-SKU-Licences.ps1](https://github.com/mimachniak/sysopslife-scripts/blob/master/AAD/AAD-Groups-with-SKU-Licences.ps1)
