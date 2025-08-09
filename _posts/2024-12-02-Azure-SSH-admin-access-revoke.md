---
title: "A Guide to Revoking and Reconfiguring SSH Keys on Azure VMs"
classes: wide
date: 2024-12-02
excerpt: "In this post, you’ll learn how to securely revoke access to your Azure Virtual Machines by replacing compromised or outdated SSH keys. Whether you’re responding to a potential security breach or performing routine key rotation, this step-by-step guide will walk you through identifying the affected keys, removing them from your VM, and safely adding new ones."
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


## Prerequisites


* Azure CLI installed

## Generate public and private key for new administrator

  ![](/assets/images/Azure/SSH/1-SSH.png) 
  ![](/assets/images/Azure/SSH/2-SSH.png)  

## Create an administrative/sudo user or revoke exsiting one

In Azure CLI, login to your account then setup subscription that is hosting VM, and run Azure CLI command with those options:  

* **--resource-group** - Name of resource group that is hosting Virtula Machine. 
* **--name** - name of Virtula Machnine 
* **--username** - new administrator account name or existing one.
* **--ssh-key-value** - path to your saved public SSH key.
```
az login
az account set --subscription "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"
az vm user update --resource-group rg-admin-001 --name vm-linuxadmin --username revokeadmin --ssh-key-value "C:\Temp\SSH\azure-admin-vm-public-key.pub"

```

## Connect to Virtula Machine using new account and SSH key (PuttY example)

1. Start PuTTY
2. Enter IP address of Virtual Machnine 

  ![](/assets/images/Azure/SSH/3-SSH.png)  

3. On menu naviagate to **Connection** --> **Data** and enter admini login

    ![](/assets/images/Azure/SSH/5-SSH.png)  

4. On menu naviagate to **Connection** --> **SSH** --> **Auth** --> **Credentials** and load Private key located on the local storage.

   ![](/assets/images/Azure/SSH/4-SSH.png)  
5. Connect to VM with new sudo administrator account. 

