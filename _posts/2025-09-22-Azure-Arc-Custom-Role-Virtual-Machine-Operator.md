---
title: "Delegate for Azure ARC least-privilege access for hybrid servers - Custom role "
classes: wide
date: 2025-09-22
excerpt: "This blog post explains why organizations using Azure Arc–enabled servers should implement least-privilege access through custom roles. It walks through real-world scenarios where built-in Azure roles are either too broad or too restrictive and shows how to define a custom Azure role tailored for Arc hybrid compute,"
toc: true
header:
  teaser: /assets/images/teaser-main-v1
  og_image: /assets/images/teaser-main-v1
categories:
 - Azure
tags:
  - Shell
  - VirtualMachine
published: true
hidden: false
---

## Creating a Custom Azure Role for Azure Arc Hybrid Compute

When working with Azure Arc–enabled servers, you often need to control access in a way that doesn’t fit the built-in roles provided by Azure. While roles like Reader, Contributor, or Azure Connected Machine Onboarding cover many scenarios, they might either grant too many permissions or not enough.

That’s where custom roles come in. By defining a custom role, you can tailor access so that users, groups, or service principals only get the permissions they actually need — nothing more, nothing less.

## Why Create a Custom Role for Azure Arc?

Some common scenarios include:

- Delegated administration: Allowing specific teams to manage extensions or policies on Arc-enabled machines, without giving them full subscription-wide rights.

- Security hardening: Enforcing least-privilege access so that hybrid server operators can perform their job without unnecessary permissions.

- Operational separation: Letting one team onboard servers, while another team manages monitoring or updates.

For example, you may want a role that only allows installation and management of VM extensions (like Azure Monitor Agent or Defender for Cloud extensions) on Arc servers — but nothing else.

## Step 1: Identify the Required Permissions

Azure roles are defined as sets of Actions and NotActions. For Azure Arc hybrid compute, common permissions include:

* Microsoft.HybridCompute/machines/read – View Arc machines

* Microsoft.HybridCompute/machines/extensions/* – Manage extensions

* Microsoft.HybridCompute/machines/write – Update properties (if needed)

You can explore available permissions with:

```bash

az provider operation show --namespace Microsoft.HybridCompute

```

## Step 2: Define the Custom Role JSON

Save the following as ArcOperator.json:
```json
{
  "Name": "[Custom] Azure Arc Operator",
  "IsCustom": true,
  "Description": "View, update patch managment for hybride VM.",
  "Actions": [
                    "*/read",
                    "Microsoft.HybridCompute/operations/read",
                    "Microsoft.HybridCompute/osType/agentVersions/read",
                    "Microsoft.HybridCompute/osType/agentVersions/latest/read",
                    "Microsoft.HybridCompute/machines/read",
                    "Microsoft.HybridCompute/machines/installPatches/action",
                    "Microsoft.HybridCompute/machines/listAccessDetails/action",
                    "Microsoft.HybridCompute/machines/UpgradeExtensions/action",
                    "Microsoft.HybridCompute/machines/assessPatches/action",
                    "Microsoft.HybridCompute/machines/addExtensions/action",
                    "Microsoft.HybridCompute/machines/patchInstallationResults/read",
                    "Microsoft.HybridCompute/machines/patchInstallationResults/softwarePatches/read",
                    "Microsoft.HybridCompute/machines/extensions/read",
                    "Microsoft.HybridCompute/machines/extensions/write",
                    "Microsoft.HybridCompute/machines/extensions/delete",
                    "Microsoft.HybridCompute/machines/patchAssessmentResults/read",
                    "Microsoft.HybridCompute/machines/patchAssessmentResults/softwarePatches/read",
                    "Microsoft.HybridCompute/machines/runcommands/read",
                    "Microsoft.HybridCompute/machines/runcommands/write",
                    "Microsoft.HybridCompute/machines/runcommands/delete",
                    "Microsoft.HybridCompute/machines/write"
  ],
  "NotActions": [],
  "AssignableScopes": [
    "/subscriptions/<SUBSCRIPTION_ID>" # Can be Managment group / Resource group
  ]
}
```

## Step 3: Create the Role in Azure

Use the Azure CLI to create the role:

az role definition create --role-definition ./ArcOperator.json


To verify:
```bash

az role definition list --name "[Custom] Azure Arc Operator"

```

## Step 4: Assign the Role

Assign the role to a user, group, or managed identity:
```bash
az role assignment create \
  --assignee <USER_OR_SP_OBJECT_ID> \
  --role "Arc Extension Operator" \
  --scope /subscriptions/<SUBSCRIPTION_ID>/resourceGroups/<RESOURCE_GROUP>
```


This ensures that the principal can only manage Arc extensions within the defined scope.

## Conclusion

Azure Arc custom roles let you implement least-privilege access for hybrid servers. By carefully crafting role definitions, you can give teams exactly the permissions they need — no more, no less.

This approach improves security, simplifies operations, and ensures compliance with enterprise governance requirements.

