---
title: "How to Use DSC v3 Resources for Azure Arc and What Options Can Be Configured"
classes: wide
date: 2026-06-22
excerpt: "How to use DSC v3 resources for Azure Arc, what configuration options can be set, and practical export and set examples for Microsoft.Azure.Arc/AgentConfiguration."
toc: true
header:
  teaser: /assets/images/teaser-main-v1.png
  og_image: /assets/images/teaser-main-v1.png
categories:
 - DSC
 - Azure
tags:
  - DSCv3
  - AzureArc
  - IaC
  - PowerShell
published: true
hidden: false
#author: Michal Machniak
---

# How to Use DSC v3 Resources for Azure Arc and What Options Can Be Configured

This guide shows how to discover DSC v3 resources for Azure Arc, how to list and filter resources available on your machine, and how to read current configuration state before applying changes.

The resource used in this catalog is `Microsoft.Azure.Arc/AgentConfiguration`.


---

## Prerequisites

Install DSC v3:

```powershell
winget install --id Microsoft.DSC --exact --source winget
```

Make the resource discoverable:

```powershell
$repoRoot = "D:\Git\AzureConnectedMachineDsc"
$env:DSC_RESOURCE_PATH = Join-Path $repoRoot 'dsc_resources'
```

Confirm the resource is visible:

```powershell
dsc resource list | Select-String 'Microsoft.Azure.Arc/AgentConfiguration'
```

---

## How to List and Get DSC Resources

List all DSC resources currently available:

```powershell
dsc resource list
```

List only Azure Arc-related resources:

```powershell
dsc resource list | Select-String 'Microsoft.Azure.Arc'
```

Get current state from the Azure Arc resource:

```powershell
'{}' | dsc resource get -r Microsoft.Azure.Arc/AgentConfiguration -f - | ConvertFrom-Json
```

The `get` output includes an `actualState` object with values such as:

- `incomingConnectionsEnabled`
- `guestConfigurationEnabled`
- `extensionsEnabled`
- `extensionAllowlist`
- `extensionBlocklist`
- `configMode`
- `agentInstalled`

Use this output as your baseline before creating desired-state payloads for `test` and `set`.

---

## Configurable Options

The resource supports these writable options:

| Property | Type | Allowed values | Description |
|---|---|---|---|
| `incomingConnectionsEnabled` | boolean or null | `true`, `false`, `null` | Enables or disables incoming connections for the Arc agent. |
| `guestConfigurationEnabled` | boolean or null | `true`, `false`, `null` | Enables or disables Guest Configuration. |
| `extensionsEnabled` | boolean or null | `true`, `false`, `null` | Enables or disables extension handling. |
| `extensionAllowlist` | array of string or null | Any extension IDs | Explicit list of allowed extensions. |
| `extensionBlocklist` | array of string or null | Any extension IDs | Explicit list of blocked extensions. |
| `configMode` | string or null | `monitor`, `full`, `null` | Agent configuration mode. |
| `proxyUrl` | string or null | Any URL string or `null` | Proxy endpoint used by the agent. |

Read-only output fields:

- `agentInstalled`
- `_inDesiredState`

Important runtime notes:

- `set` operation requires elevated security context.
- Unknown properties are rejected.
- `configMode` only accepts `monitor` or `full`.

---

## Quick Get and Test

Get current state:

```powershell
'{}' | dsc resource get -r Microsoft.Azure.Arc/AgentConfiguration -f - | ConvertFrom-Json
```

Test desired state:

```powershell
$desired = @{
	incomingConnectionsEnabled = $false
	guestConfigurationEnabled  = $false
	extensionsEnabled          = $false
	extensionAllowlist         = @(
		"Microsoft.Azure.AzureDefenderForServers/MDE.Windows"
		"Microsoft.Azure.Monitor/AzureMonitorWindowsAgent"
	)
	extensionBlocklist         = @(
		"Microsoft.Azure.Automation.HybridWorker/HybridWorkerForWindows"
		"Microsoft.Azure.Automation/HybridWorkerForLinux"
		"Microsoft.Azure.Extensions/CustomScript"
		"Microsoft.Cplat.Core/RunCommandHandlerLinux"
		"Microsoft.Cplat.Core/RunCommandHandlerWindows"
		"Microsoft.Compute/CustomScriptExtension"
		"Microsoft.EnterpriseCloud.Monitoring/MicrosoftMonitoringAgent"
		"Microsoft.EnterpriseCloud.Monitoring/OMSAgentForLinux"
	)
	configMode = "full"
}

$desired | ConvertTo-Json -Depth 10 -Compress |
	dsc resource test -r Microsoft.Azure.Arc/AgentConfiguration -f - |
	ConvertFrom-Json
```

---

## Example: Export Current Configuration

Use `export` to capture live, non-null settings and reuse them as a baseline:

```powershell
$exported = '{}' |
	dsc resource export -r Microsoft.Azure.Arc/AgentConfiguration -f - |
	ConvertFrom-Json

$exported | ConvertTo-Json -Depth 10 | Set-Content .\arc-agent-export.json
```

Why this helps:

- Baselines current production settings.
- Makes onboarding additional servers easier.
- Supports drift investigations.

---

## Tier 0 Server Baseline (Recommended)

For Tier 0 servers, use a strict Arc agent profile with the following requirements:

- `incomingconnections.enabled` = `false`
- `guestconfiguration.enabled` = `false`
- `extensions.allowlist` = `Microsoft.Azure.Monitor/AzureMonitorWindowsAgent,Microsoft.Azure.AzureDefenderForServers/MDE.Windows`

In the DSC resource payload, these map to:

- `incomingConnectionsEnabled = $false`
- `guestConfigurationEnabled = $false`
- `extensionAllowlist = @("Microsoft.Azure.Monitor/AzureMonitorWindowsAgent", "Microsoft.Azure.AzureDefenderForServers/MDE.Windows")`

Tier 0 set example:

```powershell
$tier0Desired = @{
	incomingConnectionsEnabled = $false
	guestConfigurationEnabled  = $false
	extensionAllowlist         = @(
		"Microsoft.Azure.Monitor/AzureMonitorWindowsAgent"
		"Microsoft.Azure.AzureDefenderForServers/MDE.Windows"
	)
}

$tier0Desired | ConvertTo-Json -Depth 10 -Compress |
	dsc resource set -r Microsoft.Azure.Arc/AgentConfiguration -f - |
	ConvertFrom-Json

$tier0Desired | ConvertTo-Json -Depth 10 -Compress |
	dsc resource test -r Microsoft.Azure.Arc/AgentConfiguration -f - |
	ConvertFrom-Json
```

This baseline reduces attack surface by disabling incoming Arc connections, turning off guest configuration, and allowing only the required monitoring and defender extensions.

---

## Example: Set Desired Configuration

Apply a hardened configuration with allowlist/blocklist control and full mode:

```powershell
$desired = @{
	incomingConnectionsEnabled = $false
	guestConfigurationEnabled  = $false
	extensionsEnabled          = $false
	extensionAllowlist         = @(
		"Microsoft.Azure.AzureDefenderForServers/MDE.Windows"
		"Microsoft.Azure.Monitor/AzureMonitorWindowsAgent"
	)
	extensionBlocklist         = @(
		"Microsoft.Azure.Automation.HybridWorker/HybridWorkerForWindows"
		"Microsoft.Azure.Automation/HybridWorkerForLinux"
		"Microsoft.Azure.Extensions/CustomScript"
		"Microsoft.Cplat.Core/RunCommandHandlerLinux"
		"Microsoft.Cplat.Core/RunCommandHandlerWindows"
		"Microsoft.Compute/CustomScriptExtension"
		"Microsoft.EnterpriseCloud.Monitoring/MicrosoftMonitoringAgent"
		"Microsoft.EnterpriseCloud.Monitoring/OMSAgentForLinux"
	)
	configMode = "full"
}

$desired | ConvertTo-Json -Depth 10 -Compress |
	dsc resource set -r Microsoft.Azure.Arc/AgentConfiguration -f - |
	ConvertFrom-Json
```

After `set`, run a validation pass:

```powershell
$desired | ConvertTo-Json -Depth 10 -Compress |
	dsc resource test -r Microsoft.Azure.Arc/AgentConfiguration -f - |
	ConvertFrom-Json
```

You should see `_inDesiredState: true` when everything is applied correctly.

---

## Troubleshooting Tips

If DSC cannot find the resource:

- Verify `DSC_RESOURCE_PATH` points to the `dsc_resources` folder.
- Run `dsc resource list` and check for `Microsoft.Azure.Arc/AgentConfiguration`.

If `set` fails with security context errors:

- Run PowerShell as Administrator.

If the agent is not installed:

- Install Azure Arc Connected Machine Agent (`azcmagent`) first.

---

## Summary

This catalog gives you a practical DSC v3 interface for Azure Arc agent local configuration. The best operational pattern is:

1. `export` current state as baseline.
2. Define desired hardened settings.
3. `set` and then `test` for drift-free compliance.

It is simple, scriptable, and easy to plug into CI/CD pipelines for consistent Arc agent posture across servers.

## References

- [Microsoft DSCv3 Github](https://github.com/PowerShell/DSC)
- [DSC community Github](https://github.com/mimachniak/AzureConnectedMachineDsc)
- [Blog code examples Github](https://github.com/mimachniak/sysopslife-scripts)

