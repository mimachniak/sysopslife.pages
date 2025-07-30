---
title: "Logging into a Linux Server Connected to Azure Arc Using Entra ID from portal and OpenSSH client"
classes: wide
date: 2025-07-30
excerpt: "Azure Arc extends the power of Azure to your on-premises and multi-cloud environments. One great feature it enables is logging into Linux servers using Entra ID (formerly Azure AD). This provides centralized identity management, RBAC, and conditional access for your Linux infrastructure. In this post, I'll walk you through how to enable and use Entra ID to log in to a Linux server connected to Azure Arc."
toc: true
header:
  teaser: /assets/images/page-header-teaser.png
  og_image: /assets/images/page-header-og-image.png
categories:
 - Azure
tags:
  - Shell
  - VirtualMachine
published: true
hidden: false
---

## Description 

Azure Arc extends the power of Azure to your on-premises and multi-cloud environments. One great feature it enables is logging into Linux servers using Entra ID (formerly Azure AD). This provides centralized identity management, RBAC, and conditional access for your Linux infrastructure. In this post, I'll walk you through how to enable and use Entra ID to log in to a Linux server connected to Azure Arc.  

| Distribution |	Version |
| ------------- | ------------- |
| AlmaLinux |	AlmaLinux 8, AlmaLinux 9 |
| Azure Linux (formerly known as Common Base Linux Mariner) |	CBL-Mariner 2.0, Azure Linux 3.0 |
| Debian |	Debian 9, Debian 10, Debian 11, Debian 12 |
| openSUSE |	openSUSE Leap 42.3, openSUSE Leap 15.1 to 15.5, openSUSE Leap 15.6+ |
| Oracle |	Oracle Linux 8, Oracle Linux 9 |
| RedHat Enterprise Linux (RHEL) |	RHEL 7.4 to RHEL 7.9, RHEL 8.3+, RHEL 9.0+ |
| Rocky |	Rocky 8, Rocky 9 |
| SUSE Linux Enterprise Server (SLES) |	SLES 12, SLES 15.1 to 15.5, SLES 15.6+ |
| Ubuntu |	Ubuntu 16.04 to Ubuntu 24.04 |


![](/assets/images/Azure/AADSSHLogin-0.png)  

## Prerequisites


* A Linux server connected to Azure Arc.
* Azure CLI installed.
* You are an Entra ID user with at least Virtual Machine User Login role.
* Azure Connected Machine agent is installed and running.

## Step 1: Enable Entra ID Login on the Arc-Enabled Server

```powwershell

az connectedmachine extension create \
  --machine-name <server-name> \
  --resource-group <resource-group> \
  --location <location> \
  --name AADSSHLoginForLinux \
  --publisher Microsoft.Azure.ActiveDirectory \
  --type AADSSHLoginForLinux \
  --type-handler-version 1.0

```

> Note: Replace **server-name**, **resource-group**, and **location** with appropriate values.

![](/assets/images/Azure/AADSSHLogin-1.png)  

## Step 2: Assign Entra ID Role

Assign the Virtual Machine User Login role (or Virtual Machine Administrator Login if you need sudo access) to a user or group for the connected machine resource:

```powwershell

az role assignment create \
  --assignee <user-object-id or user-UPN> \
  --role "Virtual Machine User Login" \
  --scope "/subscriptions/<sub-id>/resourceGroups/<resource-group>/providers/Microsoft.HybridCompute/machines/<server-name>"

```

![](/assets/images/Azure/AADSSHLogin-3.png)  

## Step 3: Install AAD Login Extension (if not already installed)

Check if the extension is installed:

```bash

ls /var/lib/waagent/custom-script/

```

You can also verify via the Azure portal under the "Extensions + applications" blade of the Arc server.

## Step 4: Login to the Server Using az ssh or ssh with AAD

If using the Azure CLI az ssh command:

```bash

az ssh arc --subscription <subscription-id> --resource-group <resource-group> --name <server-name>

```

![](/assets/images/Azure/AADSSHLogin-4.png)  

Alternatively, sign in to Azure Linux VMs with Microsoft Entra ID supports exporting the OpenSSH certificate and configuration. That means you can use any SSH clients that support OpenSSH-based certificates to sign in through Microsoft Entra ID

Export SSH configuration for Virtual Machnine

```bash 

az ssh config --file ~/.ssh/config --ip <server-fqdn or IP>

```

Import configuration on local SSh client (Windows example)

![](/assets/images/Azure/AADSSHLogin-5.png)  

Create config files for host with <server-fqdn or IP> on local SSH client in .ssh folder

```bash

Host 172.19.0.20
	User michal.machniak@xxxxx.pl
	CertificateFile "C:/Users/Administrator.AD/.ssh/az_ssh_config/172.19.0.20/id_rsa.pub-aadcert.pub"
	IdentityFile "C:/Users/Administrator.AD/.ssh/az_ssh_config/172.19.0.20/id_rsa"

```

Connect from local to server using Entra ID credentianls

```bash

ssh <entra-username>@<server-fqdn or IP>

```

![](/assets/images/Azure/AADSSHLogin-6.png)  

Your SSH client must support OpenSSH and be configured to request tokens. If needed, install Azure CLI login extension:

```bash

az extension add --name ssh

```

## Step 5: Troubleshooting Tips

Ensure your user is assigned the proper Entra ID role.

Make sure the AADSSHLoginForLinux extension is in "Provisioning succeeded" state.

Check /var/log/auth.log on the server for login errors.

Use az ssh arc -d for debugging SSH connection.

## Summary

Logging in to Linux servers with Entra ID via Azure Arc improves your security posture and simplifies identity management. By following these steps, you can ensure secure, role-based access to your Arc-connected servers with the power of Entra ID.