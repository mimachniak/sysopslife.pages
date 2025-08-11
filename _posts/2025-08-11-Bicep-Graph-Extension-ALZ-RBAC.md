---
title: "Using the Microsoft Azure Bicep Graph Extension to Create Security Groups and Assign Roles in an Azure Landing Zone"
classes: wide
date: 2025-08-11
excerpt: "In large, multi-subscription environments, such as Azure Landing Zones, managing identity and access at scale is a constant challenge. Security groups are a key component of Azure RBAC (Role-Based Access Control), enabling centralized control of permissions for teams and workloads.
While you can configure them manually in the Azure Portal, Infrastructure as Code (IaC) ensures consistency, repeatability, and compliance.
With Bicep and the Microsoft Graph extension, we can define security groups and assign Azure roles programmatically—integrating identity management directly into our landing zone deployment workflows. "
toc: true
header:
  teaser: /assets/images/teaser-main-v1
  og_image: /assets/images/teaser-main-v1
categories:
 - Azure
tags:
  - Bicep
  - IaaC
  - EntraID
published: false
hidden: true
---

## Description 

In large, multi-subscription environments, such as Azure Landing Zones, managing identity and access at scale is a constant challenge. Security groups are a key component of Azure RBAC (Role-Based Access Control), enabling centralized control of permissions for teams and workloads.
While you can configure them manually in the Azure Portal, Infrastructure as Code (IaC) ensures consistency, repeatability, and compliance.

With Bicep and the Microsoft Graph extension, we can define security groups and assign Azure roles programmatically—integrating identity management directly into our landing zone deployment workflows

## Why Use the Microsoft Graph Extension in Bicep?
By default, Bicep focuses on Azure Resource Manager (ARM) resources. However, security groups in Entra ID (formerly Azure AD) are not ARM resources—they are managed through Microsoft Graph. The Bicep Graph extension bridges this gap, enabling you to:

* Create Azure AD / Entra ID security groups.

* Assign users or service principals to those groups.

* Bind those groups to Azure roles within subscriptions or resource groups.

* This allows a single Bicep deployment to set up both your Azure resources and the associated identity access controls.

## Example of code : Creating a Security Group and Assigning a Role

```powershell

/*
SUMMARY: The Management Groups module deploys a management group hierarchy in a customer's tenant under the 'Tenant Root Group'.
DESCRIPTION: Management Group hierarchy is created through a tenant-scoped Azure Resource Manager (ARM) deployment.  The hierarchy is:
  * Tenant Root Group
      * Organization
        ** Platform
        ** DEV
        ** STAGE
        ** PROD
      * Default - new onborded subscriptions
AUTHOR/S: Michał Machniak
VERSION: 1.0
Logs: Get-AzTenantDeployment -id /providers/Microsoft.Resources/deployments/azl | Get-AzTenantDeploymentOperation
Logs: Get-AzTenantDeployment -id /providers/Microsoft.Resources/deployments/azl-rbac | Get-AzTenantDeploymentOperation
*/

targetScope = 'tenant'

extension 'br:mcr.microsoft.com/bicep/extensions/microsoftgraph/beta:1.0.0' // Load beta extension
extension 'br:mcr.microsoft.com/bicep/extensions/microsoftgraph/v1.0:1.0.0' // Load v1.0 extension

///////////////////////// PARAMETERS /////////////////////////

param par_list_secuirty_groups_name array = [
  {
    name: 'GAL-AAD-MG-Organization-Org-Reader'
    description: 'Security group with permissions to read the management group hierarchy'
    role: 'Reader'
    securityEnabled: true
    mailEnabled: false // Need to be false is not supported by v1.0 and beta
    uniqueName: 'GALAADMGXYZOrgOrgReader' // No whitspaces allowed
    MangmentGroupId: 'Organization' // Management Group ID to assign the group to
  }
  {
    name: 'GAL-AAD-MG-Organization-Contributor'
    description: 'Security group with permissions to contribute to the management group hierarchy'
    role: 'Contributor'
    securityEnabled: true
    mailEnabled: false // Need to be false is not supported by v1.0 and beta
    uniqueName: 'GALAADMGXYZOrgOrgContributor' // No whitspaces allowed
    MangmentGroupId: 'Organization' // Management Group ID to assign the group to
  }
  {
    name:'GAL-AAD-MG-Organization-Owner'
    description: 'Security group with permissions to manage the management group hierarchy'
    role: 'Owner'
    securityEnabled: true
    mailEnabled: false // Need to be false is not supported by v1.0 and beta
    uniqueName: 'GALAADMGXYZOrgOrgOwner' // No whitspaces allowed
    MangmentGroupId: 'Organization' // Management Group ID to assign the group to
  }
  // Dev Groups
  {
    name: 'GAL-AAD-MG-DEV-Reader'
    description: 'Security group with permissions to read the management group hierarchy'
    role: 'Reader'
    securityEnabled: true
    mailEnabled: false // Need to be false is not supported by v1.0 and beta
    uniqueName: 'GALAADMGDEVReader' // No whitspaces allowed
    MangmentGroupId: 'DEV' // Management Group ID to assign the group to
  }
  {
    name: 'GAL-AAD-MG-DEV-Contributor'
    description: 'Security group with permissions to contribute to the management group hierarchy'
    role: 'Contributor'
    securityEnabled: true
    mailEnabled: false // Need to be false is not supported by v1.0 and beta
    uniqueName: 'GALAADMGDEVContributor' // No whitspaces allowed
    MangmentGroupId: 'DEV' // Management Group ID to assign the group to

  }
  {
    name:'GAL-AAD-MG-DEV-Owner'
    description: 'Security group with permissions to manage the management group hierarchy'
    role: 'Owner'
    securityEnabled: true
    mailEnabled: false // Need to be false is not supported by v1.0 and beta
    uniqueName: 'GALAADMGDEVOwner' // No whitspaces allowed
    MangmentGroupId: 'DEV' // Management Group ID to assign the group to
  }
  // Prod groups
  {
    name: 'GAL-AAD-MG-PROD-Reader'
    description: 'Security group with permissions to read the management group hierarchy'
    role: 'Reader'
    securityEnabled: true
    mailEnabled: false // Need to be false is not supported by v1.0 and beta
    uniqueName: 'GALAADMGPRODReader' // No whitspaces allowed
    MangmentGroupId: 'PROD' // Management Group ID to assign the group to
  }
  {
    name: 'GAL-AAD-MG-PROD-Contributor'
    description: 'Security group with permissions to contribute to the management group hierarchy'
    role: 'Contributor'
    securityEnabled: true
    mailEnabled: false // Need to be false is not supported by v1.0 and beta
    uniqueName: 'GALAADMGPRODContributor' // No whitspaces allowed
    MangmentGroupId: 'PROD' // Management Group ID to assign the group to

  }
  {
    name:'GAL-AAD-MG-PROD-Owner'
    description: 'Security group with permissions to manage the management group hierarchy'
    role: 'Owner'
    securityEnabled: true
    mailEnabled: false // Need to be false is not supported by v1.0 and beta
    uniqueName: 'GALAADMGPRODOwner' // No whitspaces allowed
    MangmentGroupId: 'PROD' // Management Group ID to assign the group to
  }
    // Prod Default
  {
    name: 'GAL-AAD-MG-Default-Reader'
    description: 'Security group with permissions to read the management group hierarchy'
    role: 'Reader'
    securityEnabled: true
    mailEnabled: false // Need to be false is not supported by v1.0 and beta
    uniqueName: 'GALAADMGDefualtReader' // No whitspaces allowed
    MangmentGroupId: 'Default' // Management Group ID to assign the group to
  }
  {
    name: 'GAL-AAD-MG-Default-Contributor'
    description: 'Security group with permissions to contribute to the management group hierarchy'
    role: 'Contributor'
    securityEnabled: true
    mailEnabled: false // Need to be false is not supported by v1.0 and beta
    uniqueName: 'GALAADMDefualtContributor' // No whitspaces allowed
    MangmentGroupId: 'Default' // Management Group ID to assign the group to
  }
  {
    name:'GAL-AAD-MG-Default-Owner'
    description: 'Security group with permissions to manage the management group hierarchy'
    role: 'Owner'
    securityEnabled: true
    mailEnabled: false // Need to be false is not supported by v1.0 and beta
    uniqueName: 'GALAADMGDefualtOwner' // No whitspaces allowed
    MangmentGroupId: 'Default' // Management Group ID to assign the group to
  }

]


resource res_secuirty_group_alz 'Microsoft.Graph/groups@beta' = [for sec_group in par_list_secuirty_groups_name: {
  displayName: sec_group.name
  description: sec_group.description
  securityEnabled: true
  mailEnabled: false // Need to be false is not supported by v1.0 and beta
  mailNickname: toLower('${sec_group.name}')
  owners: {
    relationships: []
  }
  members: {
    relationships: []
  }
  uniqueName: sec_group.uniqueName  // No whitspaces allowed

}]



// Assign security groups to management groups - need delay against the creation of the security groups

module mod_mg_role_permissions '.modules/RoleAssigment/managmentGroups.bicep' = [for i in range(0, length(par_list_secuirty_groups_name)) : {
  name: guid(par_list_secuirty_groups_name[i].name)
  scope: managementGroup('${par_list_secuirty_groups_name[i].MangmentGroupId}')
  params: {
    principalId: res_secuirty_group_alz[i].id
    roleDefinitionIdOrName: par_list_secuirty_groups_name[i].role
    managementGroupId: par_list_secuirty_groups_name[i].MangmentGroupId
  }
  dependsOn: [
    res_secuirty_group_alz
  ]
}]

output out_groups_id array = [for i in range(0, length(par_list_secuirty_groups_name)): res_secuirty_group_alz[i].id]

```

## Example Deployment Command:

```powershell

New-AzTenantDeployment -Location "North Europe" -Name "AAD-v2" -TemplateFile .\main-aad-loop.bicep

az deployment sub tenant --location northeurope --template-file .\main-aad-loop.bicep

```

## Link to repo with code:

You can find code example in my GitHub repository: [Github Bicep code example](https://github.com/mimachniak/sysopslife-scripts/tree/master/Azure/bicep/Microsoft-Graph-code)

## Know issue 

When you create security groups in EntraID, it will take time for Graph API to procced chnages, so you can see that assigne permissions will faild because object wasn not found in Entra I, so this will required to rerun bicep as EntraID need some time. 

## Conclusion
By using the Microsoft Azure Bicep Graph extension, you can extend your IaC workflows beyond Azure resources, directly into identity and access management. This approach helps ensure that your Azure Landing Zone deployments are secure, consistent, and repeatable—with all permissions configured as code.


