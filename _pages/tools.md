---
permalink: /tools/
title: "Tools"
excerpt: "Open-source tools and automation projects by Michal Machniak."
toc: true
---

Here are tools and projects I build to solve practical problems in Azure, PowerShell, and Desired State Configuration. Browse the [complete list of my public repositories on GitHub](https://github.com/mimachniak?tab=repositories).

## Azure governance

### [PowerShell.IGA.AccessGuard](https://github.com/mimachniak/PowerShell.IGA.AccessGuard)

A PowerShell module for managing Azure RBAC assignments across subscriptions and management groups. Export assignments, compare them with a baseline, report access drift, and reconcile changes. Available from the [PowerShell Gallery](https://www.powershellgallery.com/packages/PowerShell.IGA.AccessGuard).

## Desired State Configuration v3

### [AzureDevOpsDscv3](https://github.com/mimachniak/AzureDevOpsDscv3)

DSC v3 resources for configuring Azure DevOps organizations, projects, users, groups, and permissions. Available from the [PowerShell Gallery](https://www.powershellgallery.com/packages/AzureDevOpsDscv3).

### [ADSGroupPolicyDSCv3](https://github.com/mimachniak/ADSGroupPolicyDSCv3)

DSC v3 resources for managing Active Directory Group Policy objects, links, registry settings, and preference registry values. The resources can also export existing policy state into a configuration document.

### [AzureConnectedMachineDscV3](https://github.com/mimachniak/AzureConnectedMachineDscV3)

DSC v3 resources for inspecting and managing Azure Arc-enabled Server agent configuration.

### [DSC-DocsGenerator](https://github.com/mimachniak/DSC-DocsGenerator)

A PowerShell module that turns DSC v3 configuration test results into Markdown reports, including compliance summaries and desired-versus-actual resource state. Available from the [PowerShell Gallery](https://www.powershellgallery.com/packages/DSC-DocsGenerator).

### [DSC v3 Resources List](https://github.com/mimachniak/dscv3-resources-list)

A community-maintained catalog of DSC v3 resources and useful tools for discovering the growing DSC ecosystem.

## Azure and Bicep

### [AzureResources-NameGenerator](https://github.com/mimachniak/AzureResources-NameGenerator)

A PowerShell module for generating and validating Azure resource names using configurable naming schemas. It can also produce Bicep variables and `.bicepparam` output. Available from the [PowerShell Gallery](https://www.powershellgallery.com/packages/AzureResourcesNameGenerator).

### [Bicep Password Generator](https://github.com/mimachniak/bicep-ext-PassWordGenerator)

A Bicep extension that generates configurable passwords for local deployments.

## Scripts and examples

### [SysOps Life scripts](https://github.com/mimachniak/sysopslife-scripts)

A collection of PowerShell scripts and Bicep examples for Azure, Microsoft 365, Intune, Azure DevOps, SQL Server, and DSC.