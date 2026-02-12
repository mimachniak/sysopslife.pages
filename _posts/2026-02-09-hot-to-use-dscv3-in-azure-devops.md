---
title: "How to Use AzureDevOpsDscv3 in Azure DevOps Pipelines"
classes: wide
date: 2026-02-09
excerpt: "A real world example that uses AzureDevOpsDscv3 to create a project, add a group, and assign users with a repeatable Azure DevOps pipeline."
toc: true
header:
  teaser: /assets/images/teaser-main-v1.png
  og_image: /assets/images/teaser-main-v1.png
categories:
 - Azure
 - DevOps
tags:
  - DSC
  - AzureDevOps
  - Pipeline
published: true
hidden: false
#author: Michal Machniak
---

# How to Use AzureDevOpsDscv3 in Azure DevOps Pipelines

This post shows a real-life example for using [AzureDevOpsDscv3](https://github.com/mimachniak/AzureDevOpsDscv3) inside an Azure DevOps pipeline. The goal is to define Azure DevOps projects, users, and groups as code, then apply the configuration in a controlled pipeline. 

## Scenario

You are onboarding a new product team. Every environment must have:

- A standard project created from a Git template
- A group created from Entra ID
- A baseline set of users

Instead of clicking in the UI, you use DSC v3 to declare everything, and the pipeline applies it consistently across environments.

## Prerequisites

- Azure DevOps organization and a PAT with permissions to manage projects and users
- A self-hosted or Microsoft-hosted Windows agent
- PowerShell 7 and DSC v3 (`dsc` CLI)
- The AzureDevOpsDscv3 module from the PowerShell Gallery

In the pipeline we will install the required modules, so the agent stays clean and the process is repeatable.

## Repository Layout

Example structure you can place in your repo:

```
.
├─ dsc
│  └─ ado.dsc.yaml
├─ pipelines
│  └─ ado-dsc.yml
└─ README.md
```

## DSC v3 Configuration File

Below is a simplified config that creates a project, adds a user, and links an Entra ID group. Save it as `dsc/ado.dsc.yaml`.

```yaml
$schema: https://aka.ms/dsc/schemas/v3/bundled/config/document.json
parameters:
	Token:
		type: string
		defaultValue: PAT-Token
resources:
- name: Configure Azure DevOps
	type: Microsoft.Windows/WindowsPowerShell
	properties:
		resources:
		- name: Create project
			type: AzureDevOpsDscv3/ProjectResource
			properties:
				Organization: ExampleOrganization
				ProjectName: Contoso-Platform
				Description: "Project created via DSC v3"
				pat: "[parameters('Token')]"
				SourceControlType: Git
				Ensure: Present
		- name: Add user
			type: AzureDevOpsDscv3/OrganizationUserResource
			properties:
				UserPrincipalName: dev1@contoso.com
				Organization: ExampleOrganization
				AccessLevel: Basic
				Ensure: Present
				pat: "[parameters('Token')]"
		- name: Add group
			type: AzureDevOpsDscv3/OrganizationGroupResource
			properties:
				GroupOriginId: 00000000-0000-0000-0000-000000000000
				GroupDisplayName: Contoso-DevTeam
				Organization: ExampleOrganization
				AccessLevel: Basic
				Ensure: Present
				pat: "[parameters('Token')]"
```

Notes:

- `GroupOriginId` is the Entra ID group object ID.
- `pat` is sourced from the `Token` parameter, which we inject in the pipeline.

## Azure DevOps Pipeline

Create a pipeline YAML file, for example `pipelines/ado-dsc.yml`.

```yaml
trigger:
- main

pool:
	vmImage: windows-latest

variables:
- name: DscConfigPath
	value: dsc/ado.dsc.yaml

steps:
- checkout: self

- task: PowerShell@2
	displayName: Install DSC v3 and AzureDevOpsDscv3
	inputs:
		pwsh: true
		targetType: inline
		script: |
			Set-StrictMode -Version Latest
			$ErrorActionPreference = 'Stop'

			#(Note: If you are on a 32-bit system, use .x86 instead).
            winget install Microsoft.VCRedist.2015+.x64 

            # Install latest stable
            winget install --id 9NVTPZWRC6KQ --source msstore
			Install-Module -Name AzureDevOpsDscv3 -Scope CurrentUser -Force
			dsc --version

- task: PowerShell@2
	displayName: Inject PAT and apply configuration
	inputs:
		pwsh: true
		targetType: inline
		script: |
			Set-StrictMode -Version Latest
			$ErrorActionPreference = 'Stop'

			$configPath = "$(DscConfigPath)"
			$tempPath = Join-Path $env:Agent_TempDirectory 'ado.dsc.yaml'

			(Get-Content $configPath -Raw) -replace 'PAT-Token', $env:ADO_PAT | Set-Content $tempPath

			dsc -l debug config set --file $tempPath
	env:
		ADO_PAT: $(ADO_PAT)
```

### Secure the PAT

Create a variable in your Azure DevOps pipeline or a variable group:

- Name: `ADO_PAT`
- Type: secret

Keep the PAT scoped to the minimum permissions required to manage projects, users, and groups in your organization.

## Run the Pipeline

1. Commit the DSC config and pipeline YAML.
2. Create a new pipeline from `pipelines/ado-dsc.yml`.
3. Run the pipeline.

The pipeline installs the required modules, injects the PAT into the config, and executes `dsc config set` to apply the desired state.

## Real-World Tips

- Store multiple configs per environment and pass the file path as a pipeline parameter.
- Use a dedicated service account for the PAT to keep audit trails clean.
- Add a validation step that runs `dsc config get` (or `dsc resource list`) to verify expected resources.
- For production, add a manual approval gate between validation and apply.

## Troubleshooting

- If `dsc` is not found, confirm the `Microsoft.PowerShell.DSC` module is installed and available in the `pwsh` session.
- If the pipeline fails to create a project, check the PAT scope and the Azure DevOps organization name.
- For group assignment, verify `GroupOriginId` is the Entra ID object ID, not the display name.

## Closing

This pattern lets you manage Azure DevOps configuration as code with minimal drift. Start small with one project and a few users, then expand to include repos, pipelines, and permissions in a controlled, repeatable way.
