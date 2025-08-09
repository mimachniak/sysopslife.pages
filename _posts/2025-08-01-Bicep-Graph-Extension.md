---
title: "Bicep Microsoft Entra (Graph) Extension - GA"
classes: wide
date: 2025-08-01
excerpt: "The Microsoft Entra (Graph) Bicep Extension allows you to provision and manage Microsoft Entra ID (formerly Azure AD) resources using Bicep, extending Infrastructure as Code beyond Azure resources into identity management."
toc: true
header:
  teaser: /assets/images/teaser-main-v1
  og_image: /assets/images/teaser-main-v1
categories:
 - Azure
 - EntraID
tags:
  - Bicep
  - IaaC
published: true
hidden: false
---

## Description 

The Microsoft Entra (Graph in v1 and beat) Bicep Extension allows you to provision and manage Microsoft Entra ID (formerly Azure AD) resources using Bicep, extending Infrastructure as Code beyond Azure resources into identity management.

Bicep works only with Azure Resource Manager (ARM) resources. With this extension, you can deploy and configure Entra ID:

* Applications: [Bicep Applications](https://learn.microsoft.com/en-us/graph/templates/bicep/reference/applications?view=graph-bicep-beta)
* Service Principals: [Bicep Service Principals](https://learn.microsoft.com/en-us/graph/templates/bicep/reference/serviceprincipals?view=graph-bicep-beta)
* App Roles & API Permissions: [Bicep App Roles & API Permissions](https://learn.microsoft.com/azure/azure-resource-manager/bicep/overvie)
* Federated idenity cedentials: [Bicep Federated idenity cedentials:](https://learn.microsoft.com/en-us/graph/templates/bicep/reference/federatedidentitycredentials?view=graph-bicep-beta)
* Groups & Group Membership **(no mailenabled / distribution)**: [Bicep Groups & Group Membership](https://learn.microsoft.com/en-us/graph/templates/bicep/reference/groups?view=graph-bicep-beta)
* Users **(ReadOnly mode)**: [Bicep Users](https://learn.microsoft.com/en-us/graph/templates/bicep/reference/users?view=graph-bicep-beta)

This is powered by Microsoft Graph API under the hood, so changes made through Bicep are reflected directly in Entra ID.

Key Benefits:

* Unified IaC – manage Azure and Entra resources in a single Bicep deployment
* Automated Identity Management – create apps, assign roles, and manage groups as code
* CI/CD Ready – integrate identity provisioning into DevOps pipelines

## Microsoft Artifact Registry

Extension verions can be found in Microsoft Artifact Registry

**Microsoft Graph Bicep Extension (beta):** https://mcr.microsoft.com/artifact/mar/bicep/extensions/microsoftgraph/beta/tags  
**Microsoft Graph Bicep Extension:**  https://mcr.microsoft.com/artifact/mar/bicep/extensions/microsoftgraph/v1.0/tags  


## Example of code

Example below deploy secuirty group in two verions of API:

```bicep

extension 'br:mcr.microsoft.com/bicep/extensions/microsoftgraph/beta:1.0.0' // Load beta extension
extension 'br:mcr.microsoft.com/bicep/extensions/microsoftgraph/v1.0:1.0.0' // Load v1.0 extension

resource res_account_v1 'Microsoft.Graph/users@v1.0' existing = {
  userPrincipalName: 'Tomasz.Oscypek@sys4ops.pl' // Existing user only supported in this edition
}

output accountId_v1 string = res_account_v1.id


resource res_account_beta 'Microsoft.Graph/users@beta' existing = {
  userPrincipalName: 'Tomasz.Oscypek@sys4ops.pl' // Existing user only supported in this edition
}

output accountId_beta string = res_account_beta.id


resource res_secuirty_group_beta 'Microsoft.Graph/groups@beta' = {
  displayName: 'Bicep Beta Security Group'
  description: 'Security group created by Bicep Beta extension'
  securityEnabled: true
  mailEnabled: false // Need to be false is not supported by v1.0 and beta
  mailNickname: 'bicep-beta-security-group'
  owners: {
    relationships: [res_account_beta.id]
  }
  uniqueName: 'BicepBetaSecurityGroup' // No whitspaces allowed

}

resource res_secuirty_group_v1 'Microsoft.Graph/groups@beta' = {
  displayName: 'Bicep Security Group'
  description: 'Security group created by Bicep extension'
  securityEnabled: true
  mailEnabled: false // Need to be false is not supported by v1.0 and beta
  mailNickname: 'bicep-security-group'
  uniqueName: 'BicepSecurityGroup' // No whitspaces allowed
  members: {
    relationships: [res_account_v1.id]
  }


}
```

## Deploy options 

We can deploy this code on any target scope as it support, and using **Azure CLI** or **PowerShell**. For troubleshooting is better to deploy on Resource Group or Subscription as it is easy to read deployment logs and check mistakes, example descibe below.

```powershell

New-AzDeployment -Location "North Europe" -Name "AAD" -TemplateFile .\main-aad.bicep

New-AzResourceGroupDeployment -ResourceGroupName "D-AUT-ITW" -Name "AAD" -TemplateFile .\main-aad.bicep

```

### Example deploy on tenat level and logs

```powershell

New-AzDeployment -Location "North Europe" -Name "AAD" -TemplateFile .\main-aad.bicep  

```

Error message that was outputed on console:

```powershell

New-AzDeployment: 17:24:23 - The deployment 'AAD' failed with error(s). Showing 2 out of 2 error(s).
Status Message: {"error":{"code":"Forbidden","target":"/resources/res_secuirty_group_v1","message":"Insufficient privileges to complete the operation. Graph client request id: cde03e9e-5a5c-48f1-83cb-3e1d385d3cb5. Graph request timestamp: 2025-07-31T15:24:20Z."}} (Code:DeploymentOperationFailed)

Status Message: {"error":{"code":"Forbidden","target":"/resources/res_secuirty_group_beta","message":"Insufficient privileges to complete the operation. Graph client request id: 9b988e59-3ad3-4d27-b60c-4560513d2325. Graph request timestamp: 2025-07-31T15:24:20Z."}} (Code:DeploymentOperationFailed)

CorrelationId: d95e67b1-ce6a-40d2-a9f4-31e16566d235

```

Issue in code was find when deployment was change to **Resource Group**  

```powershell

<code id='' style='white-space:pre-wrap'><div>{"error":{"code":"BadRequest","target":"/resources/res_secuirty_group_v1","message":"Property 'UniqueName' cannot contain whitespace. Graph client request id: 9296c621-e36a-4c80-b704-a7e9baf37096. Graph request timestamp: 2025-07-31T15:29:23Z."}}</div></code></br> (Code: DeploymentOperationFailed)

```
## Quick templates from Microsoft

https://github.com/microsoftgraph/msgraph-bicep-types/tree/main/quickstart-templates


