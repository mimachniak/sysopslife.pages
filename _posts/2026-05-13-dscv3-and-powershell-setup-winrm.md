---
title: "Setting Up WinRM HTTPS Listener with DSC v3 and PowerShell"
classes: wide
date: 2026-05-13
excerpt: "This post shows how to automate a secure WinRM HTTPS listener configuration using Microsoft DSC v3. The solution combines native DSC v3 resources — Registry, FirewallRuleList, Service — with the PowerShellScript transitional resource to provision a self-signed certificate and wire it to the listener, all orchestrated from a single PowerShell script."
toc: true
header:
  teaser: /assets/images/teaser-main-v1.png
  og_image: /assets/images/teaser-main-v1.png
categories:
 - DSC
 - PowerShell
tags:
  - IaaC
  - Code
  - WinRM
  - Security
published: true
hidden: false
#author: Michal Machniak
---

![](/assets/images/DSC/dsc-flow.PNG)

# Setting Up a WinRM HTTPS Listener with DSC v3 and PowerShell

## Introduction

Windows Remote Management (WinRM) is the Microsoft implementation of the WS-Management protocol. By default WinRM operates over HTTP on port 5985 — adequate for internal lab environments, but not suitable for production. Enabling the **HTTPS listener** on port 5986 encrypts the entire management channel using TLS and requires a certificate bound to the listener.

Configuring this manually involves multiple steps: creating or importing a certificate, writing registry values under a dynamically named GUID key, opening the firewall, and ensuring the WinRM service is running. One missed step means the listener is broken or insecure.

This post automates the whole process with **Microsoft DSC v3** — a standalone, declarative engine that applies YAML configuration documents and supports native Windows resources out of the box.

---

## Solution Overview

The configuration is split into two focused DSC YAML documents and one PowerShell orchestrator:

| File | Purpose |
|---|---|
| `ps-script-certificate.dsc.yaml` | Creates a self-signed certificate in the local machine store |
| `winrm.dsc.yaml` | Configures registry keys, firewall rule, and WinRM service |
| `winrm.ps1` | Orchestrates both documents, passing the certificate thumbprint between them |

The split is intentional: the certificate step runs under the **current** security context (the default), while the WinRM registry and firewall configuration requires an **elevated (restricted)** security context.

---

## Prerequisites

**DSC v3 installed:**

```powershell
winget install --id Microsoft.DSC
```

**Verify resources are available:**

```powershell
dsc resource list Microsoft.Windows/Registry
dsc resource list Microsoft.Windows/FirewallRuleList
dsc resource list Microsoft.Windows/Service
dsc resource list Microsoft.DSC.Transitional/PowerShellScript
```

---

## Phase 1 — Create the Self-Signed Certificate

### `ps-script-certificate.dsc.yaml`

```yaml
$schema: https://aka.ms/dsc/schemas/v3/bundled/config/document.json

metadata:
  Microsoft.DSC:
    requiredSecurityContext: current

resources:
  - type: Microsoft.DSC.Transitional/PowerShellScript
    name: CreateSelfSignedCertificate
    properties:
      GetScript: |
          $dnsName = $env:COMPUTERNAME
          $cert = Get-ChildItem Cert:\LocalMachine\My |
              Where-Object {
                  $_.Subject -eq "CN=$dnsName"
              }

          return @{
              Result = if ($cert) { "Present" } else { "Absent" }
              Thumbprint = if ($cert) { $cert.Thumbprint } else { $null }
          }
      TestScript: |
          $dnsName = $env:COMPUTERNAME
          $cert = Get-ChildItem Cert:\LocalMachine\My |
              Where-Object {
                  $_.Subject -eq "CN=$dnsName"
              }

          return ($null -ne $cert)
      SetScript: |
        $dnsName = $env:COMPUTERNAME
        $cert = Get-ChildItem Cert:\LocalMachine\My |
              Where-Object {
                  $_.Subject -eq "CN=$dnsName"
              }
        if (-not $cert) {
            $cert = New-SelfSignedCertificate -DnsName $dnsName -CertStoreLocation "Cert:\LocalMachine\My" -KeyLength 2048 -HashAlgorithm SHA256
            }
        return @{ Thumbprint = $cert.Thumbprint }
```

### Resource: `Microsoft.DSC.Transitional/PowerShellScript`

This is a **transitional resource** — a bridge between DSC v3's native model and imperative PowerShell logic. It implements the three-method contract that every DSC resource must expose:

| Script property | Role |
|---|---|
| `GetScript` | Returns the current state. Queries `Cert:\LocalMachine\My` for a certificate whose subject matches `CN=$env:COMPUTERNAME`. Returns `Present`/`Absent` and the thumbprint if found. |
| `TestScript` | Returns `$true` if the certificate already exists, `$false` if DSC needs to run `SetScript`. |
| `SetScript` | Creates the certificate with `New-SelfSignedCertificate` using SHA256, a 2048-bit key, and the computer name as the DNS name. Returns the thumbprint so it can be captured by the orchestrator. |

> **Note:** `requiredSecurityContext: current` means this document can be applied by any user context. Creating a certificate in `Cert:\LocalMachine\My` still requires administrative rights in practice, but the DSC security context flag here is set to `current` since the operation does not touch system-wide WinRM configuration.

---

## Phase 2 — Configure the WinRM HTTPS Listener

### `winrm.dsc.yaml`

```yaml
$schema: https://aka.ms/dsc/schemas/v3/bundled/config/document.json

metadata:
  Microsoft.DSC:
    requiredSecurityContext: restricted

parameters:
  certThumbprint:
    type: string
    description: Thumbprint of the certificate to use for the WinRM HTTPS listener.
    defaultValue: "341B58BC25D182D844B64BF7FC52D1D751625960"

resources:

  - name: WinRM HTTPS listener - Address
    type: Microsoft.Windows/Registry
    properties:
      _exist: true
      keyPath: HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows\CurrentVersion\WSMAN\Listener\*+HTTPS
      valueName: Address
      valueData:
        String: "*"

  - name: WinRM HTTPS listener - Transport
    type: Microsoft.Windows/Registry
    properties:
      _exist: true
      keyPath: HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows\CurrentVersion\WSMAN\Listener\*+HTTPS
      valueName: Transport
      valueData:
        String: "HTTPS"

  - name: WinRM HTTPS listener - Port
    type: Microsoft.Windows/Registry
    properties:
      _exist: true
      keyPath: HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows\CurrentVersion\WSMAN\Listener\*+HTTPS
      valueName: Port
      valueData:
        DWord: 5986

  - name: WinRM HTTPS listener - certThumbprint
    type: Microsoft.Windows/Registry
    properties:
      _exist: true
      keyPath: HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows\CurrentVersion\WSMAN\Listener\*+HTTPS
      valueName: certThumbprint
      valueData:
        String: "[parameters('certThumbprint')]"

  - name: WinRM HTTPS listener - hostname
    type: Microsoft.Windows/Registry
    properties:
      _exist: true
      keyPath: HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows\CurrentVersion\WSMAN\Listener\*+HTTPS
      valueName: hostname
      valueData:
        String: "[envvar('COMPUTERNAME')]"

  - name: WinRM firewall rules
    type: Microsoft.Windows/FirewallRuleList
    properties:
      rules:
        - name: Windows Remote Management (HTTPS-In)
          description: Allow inbound TCP traffic on port 5986 for WinRM HTTPS.
          applicationName: System
          protocol: 6
          localPorts: '5986'
          direction: Inbound
          action: Allow
          enabled: true
          profiles:
            - Domain
            - Private

  - name: WinRM Service
    type: Microsoft.Windows/Service
    properties:
      name: WinRM
      _exist: true
      status: Running
      startType: Automatic
```

### Resource: `Microsoft.Windows/Registry`

The **Registry** resource is DSC v3's native way to manage Windows registry keys and values. Each instance targets one specific value.

WinRM stores its listener configuration under:

```
HKLM:\SOFTWARE\Microsoft\Windows\CurrentVersion\WSMAN\Listener\<GUID>+HTTPS
```

The `<GUID>` portion is generated randomly when the listener is first created and is not predictable. DSC v3's Registry resource supports the `*` wildcard in `keyPath`, so `*+HTTPS` matches the listener key regardless of its GUID, making the configuration portable across machines.

Key properties used across all five Registry resource instances:

| Property | Meaning |
|---|---|
| `_exist: true` | Ensures the key/value must exist (set) rather than be absent (delete) |
| `keyPath` | Full registry path including hive, key, and wildcard |
| `valueName` | The registry value name to manage |
| `valueData` | Typed value — `String` for text, `DWord` for 32-bit integer |

**Values configured:**

| `valueName` | Type | Value | Purpose |
|---|---|---|---|
| `Address` | String | `*` | Bind to all IP addresses |
| `Transport` | String | `HTTPS` | HTTPS transport |
| `Port` | DWord | `5986` | Standard WinRM HTTPS port |
| `certThumbprint` | String | `[parameters('certThumbprint')]` | Certificate bound to the listener |
| `hostname` | String | `[envvar('COMPUTERNAME')]` | Machine hostname |

Notice two DSC v3 expression functions in use:

- **`[parameters('certThumbprint')]`** — resolves the `certThumbprint` parameter, which is supplied at runtime from the orchestrator script.
- **`[envvar('COMPUTERNAME')]`** — reads the `COMPUTERNAME` environment variable at apply time, making the configuration self-adapting to any machine.

### Resource: `Microsoft.Windows/FirewallRuleList`

The **FirewallRuleList** resource manages one or more Windows Firewall rules as a single unit. It maps directly to Windows Defender Firewall rules and supports both creating new rules and enforcing the desired state of existing ones.

```yaml
- name: WinRM firewall rules
  type: Microsoft.Windows/FirewallRuleList
  properties:
    rules:
      - name: Windows Remote Management (HTTPS-In)
        description: Allow inbound TCP traffic on port 5986 for WinRM HTTPS.
        applicationName: System
        protocol: 6        # TCP
        localPorts: '5986'
        direction: Inbound
        action: Allow
        enabled: true
        profiles:
          - Domain
          - Private
```

Key fields:

| Field | Value | Meaning |
|---|---|---|
| `protocol` | `6` | TCP (IANA protocol number) |
| `localPorts` | `5986` | Destination port for WinRM HTTPS |
| `direction` | `Inbound` | Allow incoming connections |
| `action` | `Allow` | Permit traffic |
| `profiles` | `Domain`, `Private` | Active on domain-joined and private networks |

> The `Public` profile is intentionally excluded — exposing WinRM on public networks is a security risk.

### Resource: `Microsoft.Windows/Service`

The **Service** resource ensures the WinRM Windows service is running and set to start automatically:

```yaml
- name: WinRM Service
  type: Microsoft.Windows/Service
  properties:
    name: WinRM
    _exist: true
    status: Running
    startType: Automatic
```

| Property | Value | Meaning |
|---|---|---|
| `name` | `WinRM` | Windows service name |
| `_exist` | `true` | Service must exist |
| `status` | `Running` | Service must be in the running state |
| `startType` | `Automatic` | Service starts with Windows |

### Security Context: `restricted`

`winrm.dsc.yaml` declares `requiredSecurityContext: restricted`, which is the default and signals that the configuration must be applied by an elevated (administrative) process. Writing to `HKLM`, creating firewall rules, and managing services all require administrator rights — the security context declaration makes this requirement explicit and prevents accidental application from a non-elevated shell.

---

## Orchestrator: `winrm.ps1`

The PowerShell script ties both documents together. It:

1. Applies the certificate configuration and extracts the thumbprint from the DSC output
2. Passes the thumbprint as an inline parameter to the WinRM listener configuration

```powershell
Write-Host "DSC for certificate"

$result = dsc config set --file .\ps-script-certificate.dsc.yaml --output-format pretty-json | ConvertFrom-Json -Depth 20

$thumbprint = if ($result.results.result.afterState.output -is [System.Array]) {
    $result.results.result.afterState.output[0].Thumbprint
} else {
    $result.results.result.afterState.output.Thumbprint
}

if (-not $thumbprint) {
    throw "Thumbprint was not found in DSC output."
}

Write-Host "Certificate thumbprint: $thumbprint"

$inlineParams = @{
    parameters = @{
        certThumbprint = $thumbprint
    }
} | ConvertTo-Json

Write-Host "DSC - WinRM HTTPS setup"

dsc config --parameters $inlineParams set --file .\winrm.dsc.yaml
```

**Key points:**

- `dsc config set --output-format pretty-json` returns structured JSON, including the `afterState` for each resource. The `PowerShellScript` resource returns the `SetScript` output in `.results.result.afterState.output`.
- The thumbprint is extracted by checking whether the output is an array (multiple results) or a single object, making the extraction robust.
- The inline parameters JSON is passed via `dsc config --parameters <json>` which injects it at runtime, replacing `[parameters('certThumbprint')]` in the YAML.
- A guard (`throw`) stops execution immediately if the thumbprint is missing rather than proceeding with an empty value.

---

## Applying the Configuration

Run the orchestrator from the directory containing all three files:

```powershell
# Test only — no changes applied
dsc config test --file .\ps-script-certificate.dsc.yaml

# Full apply
.\winrm.ps1
```

To verify the listener was created:

```powershell
winrm enumerate winrm/config/listener

Get-ChildItem WSMan:\localhost\Listener

```

Expected output should include a listener with `Transport = HTTPS` and `Port = 5986`.

To test the connection from a remote machine:

```powershell
Test-WSMan -ComputerName <hostname> -UseSSL
```

---

## Summary

| Resource | Type | What it configures |
|---|---|---|
| `Microsoft.DSC.Transitional/PowerShellScript` | Transitional | Self-signed TLS certificate in `Cert:\LocalMachine\My` |
| `Microsoft.Windows/Registry` (×5) | Native | WinRM listener registry values under `WSMAN\Listener\*+HTTPS` |
| `Microsoft.Windows/FirewallRuleList` | Native | Inbound TCP 5986 rule on Domain and Private profiles |
| `Microsoft.Windows/Service` | Native | WinRM service — Running, Automatic start |

DSC v3 makes this multi-step process repeatable, auditable, and idempotent. The split between certificate creation and listener configuration, the use of `[parameters()]` and `[envvar()]` expressions, and the `requiredSecurityContext` declaration together produce a configuration that is both portable and security-aware.

---

## References

- [DSC v3 documentation](https://learn.microsoft.com/en-us/powershell/dsc/overview)
- [Microsoft.Windows/Registry resource](https://learn.microsoft.com/en-us/powershell/dsc/reference/microsoft/windows/registry/overview)
- [Microsoft.Windows/Service resource](https://learn.microsoft.com/en-us/powershell/dsc/reference/microsoft/windows/service/overview)
- [Microsoft.Windows/FirewallRuleList resource](https://learn.microsoft.com/en-us/powershell/dsc/reference/microsoft/windows/firewallrulelist/overview)
- [WinRM HTTPS setup — Microsoft Docs](https://learn.microsoft.com/en-us/windows/win32/winrm/installation-and-configuration-for-windows-remote-management)

- [Microsoft DSCv3 Github](https://github.com/PowerShell/DSC)
- [DSC community Github](https://github.com/dsccommunity/ActiveDirectoryDsc)
- [Blog code examples Github](https://github.com/mimachniak/sysopslife-scripts)