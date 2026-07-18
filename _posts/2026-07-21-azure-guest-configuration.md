---
title: "Azure Custom Guest Configuration with PowerShell DSC"
classes: wide
date: 2026-07-21
excerpt: "Build, test, publish, and assign a custom Azure Guest Configuration package using PowerShell DSC and Azure Policy."
toc: true
categories:
  - Azure
  - DSC
  - PowerShell
tags:
  - Azure Policy
  - Guest Configuration
  - PowerShell DSC
  - IaaC
published: false
hidden: true
---

# Azure Custom Guest Configuration with PowerShell DSC

Azure Guest Configuration extends Azure Policy into the operating system of Azure virtual machines and Arc-enabled servers. A custom guest configuration lets you describe a desired state in PowerShell DSC, package it, host it, and use Azure Policy to audit or remediate that state at scale.

This article walks through a Windows example that enforces a legal logon message. The same process applies to IIS features, Windows optional features, users, registry values, and other DSC resources.

> **Important:** Test every package in a non-production subscription first. An `AuditAndSet` package changes the guest operating system when it detects drift.

---

## How the pieces fit together

The workflow has five parts:

1. Author a DSC configuration and compile it into a MOF file.
2. Package the MOF with the Guest Configuration module.
3. Validate the package locally and calculate its content hash.
4. Upload the ZIP package to HTTPS-accessible blob storage.
5. Generate and assign an Azure Policy definition that references the package.

Azure Policy evaluates compliance through the Guest Configuration extension on the machine. The extension downloads the package, evaluates the DSC resources, and reports the result back to Azure Policy.

---

## Prerequisites

Before creating a custom package, prepare the authoring workstation and Azure environment.

| Requirement | Why it is needed |
|---|---|
| Windows authoring workstation | This example uses Windows PowerShell DSC resources and creates a Windows MOF. |
| PowerShell 7 | Recommended for building and testing Guest Configuration packages. |
| `PSDscResources` module | Supplies the DSC resources used by the configuration, such as `Registry`. |
| `GuestConfiguration` module | Creates, validates, and turns a package into an Azure Policy definition. |
| Azure PowerShell sign-in | Required to upload content and deploy or assign the generated policy. |
| HTTPS-accessible package URI | Each target machine must be able to download the ZIP package from the URI. Azure Blob Storage is a common choice. |
| Azure permissions | Use a role that can create policy definitions and assignments, and can upload the package to the selected storage account. |
| Supported Azure VM or Arc-enabled server | The target must be able to receive the Guest Configuration extension and reach Azure Policy and the package URI. |

Install the two DSC modules on the authoring workstation:

```powershell
Install-Module -Name PSDscResources -Scope CurrentUser
Install-Module -Name GuestConfiguration -Scope CurrentUser

Get-Module -ListAvailable -FullyQualifiedName PSDscResources
Get-Module -ListAvailable -FullyQualifiedName GuestConfiguration
```

Sign in to the tenant and select the subscription where the policy will be assigned:

```powershell
Connect-AzAccount
Set-AzContext -Subscription '<subscription-id-or-name>'
```

---

## Step 1: Author and compile the DSC configuration

Guest Configuration packages use a compiled DSC MOF. The following configuration writes a title and body for the Windows interactive logon message.

```powershell
Configuration SetupLogonMessage {
    Import-DscResource -ModuleName PSDscResources

    Node 'localhost' {
        Registry LogonMessageTitle {
            Key       = 'HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows\CurrentVersion\Policies\System'
            ValueName = 'legalnoticecaption'
            ValueData = 'Security Warning'
            ValueType = 'String'
            Ensure    = 'Present'
            Force     = $true
        }

        Registry LogonMessageBody {
            Key       = 'HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows\CurrentVersion\Policies\System'
            ValueName = 'legalnoticetext'
            ValueData = 'Authorized access only. All activities are monitored and recorded.'
            ValueType = 'String'
            Ensure    = 'Present'
            DependsOn = '[Registry]LogonMessageTitle'
            Force     = $true
        }
    }
}

SetupLogonMessage -OutputPath .\SetupLogonMessage
```

Compilation creates `SetupLogonMessage.mof` in the output folder. Keep the configuration source in version control alongside the generated policy files, but rebuild the MOF and package whenever the configuration changes.

---

## Step 2: Create the Guest Configuration package

Choose the package type deliberately:

| Type | Behaviour |
|---|---|
| `Audit` | Reports whether the setting is compliant; it never changes the machine. |
| `AuditAndSet` | Reports compliance and applies the DSC configuration when the machine is non-compliant. |

For the logon-message configuration, create an `AuditAndSet` package:

```powershell
$params = @{
    Name          = 'SetupLogonMessage'
    Configuration = './SetupLogonMessage/SetupLogonMessage.mof'
    Type          = 'AuditAndSet'
    Force         = $true
    # FrequencyMinutes = 180 # Default is 15 minutes.
}

New-GuestConfigurationPackage @params
```

The command produces `SetupLogonMessage.zip`. For an audit-only configuration, change only the `Type` value:

```powershell
$params = @{
    Name          = 'UserExample'
    Configuration = './UserExample/UserExample.mof'
    Type          = 'Audit'
    Force         = $true
}

New-GuestConfigurationPackage @params
```

---

## Step 3: Validate the package before publishing

Validate locally before you place the ZIP in storage or create a policy definition. This catches packaging errors and gives you the hash that Azure Policy uses to identify the exact package version.

```powershell
Get-FileHash .\SetupLogonMessage.zip
Get-GuestConfigurationPackageComplianceStatus -Path .\SetupLogonMessage.zip
```

The compliance command should return a result instead of a packaging or resource-loading error. If it fails, confirm that every DSC resource referenced by the MOF is available to the Guest Configuration packaging process and that the MOF path is correct.

---

## Step 4: Publish the ZIP to a stable HTTPS URI

Upload the ZIP to Azure Blob Storage. The policy definition must reference a URI that every target machine can reach over HTTPS. A blob URI with a time-limited SAS token is useful for testing, but production packages should use an access method and renewal process that will not unexpectedly expire.

After upload, keep the content URI and the SHA-256 value from `Get-FileHash` available. Treat a package update as a versioned release: publish a new ZIP, validate it, then update the policy definition or create a new policy version.

---

## Step 5: Generate the Azure Policy definition

`New-GuestConfigurationPolicy` creates Azure Policy artifacts from the package metadata. Provide a unique policy ID, the hosted package URI, platform, and version. The generated files can then be deployed at the management group or subscription scope that matches your governance model.

```powershell
$contentUri = 'https://<storage-account>.blob.core.windows.net/guest-configuration/SetupLogonMessage.zip?<sas-token>'

$policyConfig = @{
    PolicyId      = '<new-guid>'
    ContentUri    = $contentUri
    DisplayName   = 'Configure Windows logon message'
    Description   = 'Audits and configures the Windows interactive logon message.'
    Path          = './policies/setup-logon-message.json'
    Platform      = 'Windows'
    PolicyVersion = '1.0.0'
}

New-GuestConfigurationPolicy @policyConfig
```

Review the generated JSON before deployment. In particular, verify the package URI, platform, package type, version, and hash. These details determine what the Guest Configuration extension downloads and evaluates.

---

## Step 6: Deploy and assign the policy

Deploy the generated policy definition, then create an assignment scoped to the resource group, subscription, or management group containing the target machines. A Bicep deployment is a convenient way to keep the definition and assignment under source control:

```powershell
New-AzResourceGroupDeployment `
    -ResourceGroupName 'D-AZDPL2026-WIN-RG02' `
    -TemplateFile ..\bicep\main-security-policy.bicep
```

For an `AuditAndSet` policy, make sure the policy assignment and Guest Configuration extension deployment are allowed for the target scope. Azure Policy will install or update the required extension according to the policy definition, then the guest reports compliance after its next evaluation cycle.

---

## Verify compliance in Azure

Open **Azure Policy** and inspect the assignment's compliance results. Select a non-compliant machine to see the Guest Configuration assignment and remediation details. On the machine, confirm that the configured registry values appear under:

```text
HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows\CurrentVersion\Policies\System
```

Expect a delay between assignment, extension installation, package download, and the first compliance result. Do not use an immediate policy result as proof that the configuration failed.

---

## Common problems

| Symptom | What to check |
|---|---|
| Package creation fails | Confirm the MOF exists and that required DSC modules are installed and importable. |
| Local validation fails | Run `Get-GuestConfigurationPackageComplianceStatus` from the package directory and resolve missing resource dependencies. |
| Machines show no result | Confirm the VM or Arc server is supported, connected, and able to receive the Guest Configuration extension. |
| Package download fails | Verify DNS, HTTPS egress, storage firewall rules, and that the package URI or SAS token remains valid. |
| Unexpected remediation | Confirm the package was built with `Audit`, not `AuditAndSet`, before assigning it broadly. |
| A package update is ignored | Increase the policy/package version and update the policy definition to point to the intended artifact. |

---

## Summary

Custom Guest Configuration turns a DSC configuration into an Azure Policy control that can be measured across a fleet. The reliable loop is simple: compile the MOF, package it, validate it locally, publish it to a durable HTTPS location, generate the policy, and assign it to a limited test scope before expanding the rollout.