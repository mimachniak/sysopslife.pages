---
title: "Mastering Azure Networking: Best Practices for Building Scalable, Secure Cloud Infrastructure"
classes: wide
date: 2025-10-20
excerpt: "Discover how to build a secure and scalable Azure network from the ground up. Learn how to start with a simple network for a small company and seamlessly extend it into an enterprise-grade architecture. Explore best practices for Azure Landing Zones, hub-and-spoke topology, private connectivity, DNS configuration, and network security to design flexible, future-ready cloud infrastructure in Microsoft Azure. "
toc: true
header:
  teaser: /assets/images/teaser-main-v1.png
  og_image: /assets/images/teaser-main-v1.png
categories:
 - Azure
tags:
  - Network
  - Security
published: true
hidden: false
author: Michal Machniak
---

# Building a Network in Azure — From Zero to Hero

Building a well-structured and secure network in Azure is one of the foundational steps toward a successful cloud deployment. Whether you’re migrating workloads, designing new environments, or improving existing infrastructure, Azure networking provides powerful tools to help you design for scalability, performance, and governance.

In this post, we’ll walk through the core principles and best practices for building a network in Azure — from initial planning to advanced connectivity options.

---

## 🧭 Start with the Azure Landing Zone Concept

Before diving into subnets and IP ranges, it’s crucial to understand the **Azure Landing Zone** model. 

Microsoft defines an **Azure Landing Zone** as an environment that implements key design principles across **eight design areas** — governance, security, identity, networking, operations, management, and more. It ensures that your environment can scale and support multiple applications and workloads consistently.

### Platform vs Application Landing Zones

Azure uses **subscriptions** to isolate and scale resources:

- **Platform landing zones** host shared services like networking, identity, and monitoring.
- **Application landing zones** host workloads and apps.

This separation allows for cleaner management boundaries, better security, and easier automation.

---

## 🕸️ Designing Your Network Topology

One of the most common approaches for Azure networking is the **hub-and-spoke topology**.

### Hub-and-Spoke Model

In this model:
- The **hub** is the central network that hosts shared components like firewalls, DNS, or VPN gateways.
- The **spokes** are individual VNets (Virtual Networks) that connect to the hub and host application workloads.

This approach provides:
- Centralized management of connectivity and security.
- Isolation between applications.
- Easier integration with on-premises networks.

You can also use **Azure Virtual Network Manager** to create and manage:
- **Hub-and-spoke topology**
- **Mesh topology** (in preview)
- **Hybrid hub-spoke with direct spoke-to-spoke connectivity**

![](/assets/images/Azure/network/PL-Azure%20Networking-topology.png)  

---

## 🧱 Planning Your Network

### Mapping on-premises network to Azure network  

Mapping VLANs to Azure networking depends on how and where you’re connecting Azure to your on-premises or extended network. 
Azure itself doesn’t use VLANs internally—it uses Virtual Networks (VNets) for segmentation—but VLANs still matter when you’re integrating Azure with your physical or hybrid infrastructure.  

- Use subnets within a VNet to logically segment traffic (similar to VLANs).
- Apply Network Security Groups (NSGs) or Azure Firewall rules for isolation.
- VLAN tagging or trunking is not available between Azure VMs or subnets.


### Naming Conventions

A consistent naming convention is the foundation of an organized environment.  
Follow Microsoft’s **Cloud Adoption Framework** recommendations for naming standards:
> [Cloud Adoption Framework | Microsoft Learn](https://learn.microsoft.com/azure/cloud-adoption-framework/)

![](/assets/images/Azure/network/PL-Azure%20Networking-naming.drawio.png)  

### IP Addressing

When planning your VNets and subnets, avoid overlapping with your on-premises or partner networks.

#### Common private address ranges:
- `10.0.0.0/8`
- `172.16.0.0/12`
- `192.168.0.0/16`
- `100.64.0.0/10` (shared address space)

Keep subnet masks small, such as `/28` or `/26`, to maintain flexibility and minimize wasted IPs.

#### Remember:
Azure reserves certain IPs in every subnet:
- `.0` — network address  
- `.1` — default gateway  
- `.2` and `.3` — Azure DNS mapping  
- `.255` — broadcast address

#### Subnet nameing and functionality example 


| **Subnet Name**            | **Description**                                                                 |
|-----------------------------|---------------------------------------------------------------------------------|
| AzureFirewallSubnet         | Reserved for Azure Firewall                                                    |
| GatewaySubnet               | Reserved for Azure Virtual Gateway                                             |
| AzureBastionSubnet          | Reserved for Azure Bastion                                                     |
| ApplicationGatewaySubnet    | Custom – required delegation for application gateway                           |
| ManagementSubnet            | Custom – for any management services like Update Managers, Scanning tools      |
| IdentitySubnet              | Custom – for identity services like Active Directory, PIM, PAM                 |
| DmzSubnet                   | Custom – for services that will be published to the Internet                   |
| ApplicationSubnet           | Custom – for applications like CRM front, Reporting Services                   |
| BackendSubnet               | Custom – backend API / Integration bus                                         |
| DatabaseSubnet              | Custom – for database services                                                 |


#### Subnet ranges

| **Subnet Name**            | **Network Mask** |
|-----------------------------|------------------|
| AzureFirewallSubnet         | /26              |
| GatewaySubnet               | /26              |
| AzureBastionSubnet          | /26              |
| ApplicationGatewaySubnet    | /26              |
| ManagementSubnet            | /28              |
| IdentitySubnet              | /24              |
| DmzSubnet                   | /24              |
| ApplicationSubnet           | /24              |
| BackendSubnet               | /24              |
| DatabaseSubnet              | /24              |


#### Tool for to help design your subnets

Visual Subnet Calculator

The Visual Subnet Calculator is a free, browser-based tool designed to assist network administrators and IT professionals in subnetting IPv4 networks.  
It offers a straightforward interface to quickly visualize and manage subnets without requiring advanced mathematical calculations.  

> [Visual Subnet Calculator](https://www.davidc.net/sites/default/subnets/subnets.html)

![](/assets/images/Azure/network/PL-Azure%20Networking-planner.png)  
---

## 🔐 Security and Access

Security starts with **Network Security Groups (NSGs)** is easier to unblock traffic, then setup block rules on production environment.  
It’s a best practice to:
- Create **custom rules**.
- Block all inbound and outbound traffic by default.
- Explicitly allow only what’s required.

![](/assets/images/Azure/network/PL-Azure%20Networking-NSG.png)  

![](/assets/images/Azure/network/PL-Azure%20Networking-Private-Subnet.png)  

---

## 🔗 Private Connectivity

When connecting Azure services privately:
- Use **Private Link** for secure, private access to Azure services like Storage, SQL, and Key Vault.
- Use **Private DNS Zones** to manage DNS resolution for private endpoints.
  - A single Private DNS Zone can be linked to multiple VNets.
  - No need to deploy a separate DNS resolver for every network.  

**Microsoft DNS**: Azure IP address **168.63.129.16** is a virtual public IP address that facilitates communication channels to Azure platform resources. Customers can define any address space for their private virtual network in Azure. Therefore, the Azure platform resources must be presented as a unique public IP address


---

## 🔍 Monitoring Your Network

Don’t forget about visibility and diagnostics.  
Use **Network Watcher** to:
- Monitor traffic flows.
- Diagnose connectivity issues.
- Capture packets and analyze performance.

---

## 🧰 Is One Subscription Enough?

For small environments, a single Azure subscription can be sufficient.  
However, for larger organizations, **multiple subscriptions** provide better scalability, governance, and isolation between workloads or teams.

Example of building and envoling network in azure for organization  

### Example for organization with one Azure subscription

#### Example 1
- One Azure subscription
- One Azure Virtual Network 
- Azure Virtual Network with subntes (segmentation)
- Network Secuirty groups for all subnetes with DenyRules for Inbound / Outbound
- Azure Virtual Gateway for Site to Site VPN connection  
- Azure Nat Gateway for NAT outbound traffic

![](/assets/images/Azure/network//PL-Azure%20Networking%20from%20Zero%20to%20Hero-V1.drawio.png)  

#### Example 2
- One Azure subscription
- One Azure Virtual Network 
- Azure Virtual Network with subntes (segmentation)
- Network Secuirty groups for all subnetes with DenyRules for Inbound / Outbound
- Azure Virtual Gateway for Site to Site VPN connection  
- Azure Nat Gateway for NAT outbound traffic
- Azure Private DNS zones dedicated for Azure SQL services
- Azure privet link for Azure SQL server
- Azure SQL server without any Internet access

![](/assets/images/Azure/network//PL-Azure%20Networking%20from%20Zero%20to%20Hero-V2.drawio.png)  

#### Example 2
- One Azure subscription
- One Azure Virtual Network 
- Azure Virtual Network with subntes (segmentation)
- Network Secuirty groups for all subnetes with DenyRules for Inbound / Outbound
- Azure Virtual Gateway for Site to Site VPN connection  
- Azure Nat Gateway for NAT outbound traffic
- Azure Private DNS zones dedicated for Azure SQL services
- Azure privet link for Azure SQL server
- Azure SQL server without any Internet access
- Azure Firewall that inspect all traffic between subnets
- Azure user definie route to pass traffic between subnets over Azure firewall

![](/assets/images/Azure/network//PL-Azure%20Networking%20from%20Zero%20to%20Hero-V3.drawio.png)  

### Example for organization with multiple Azure subscription (Hub)

#### Example 1
- 3 Azure subscription
- 3 Azure Virtual Network 
- Azure Virtual Network with subntes (segmentation)
- Network Secuirty groups for all subnetes with DenyRules for Inbound / Outbound
- Azure Virtual Gateway for Site to Site VPN connection  
- Azure Nat Gateway for NAT outbound traffic
- Azure Private DNS zones dedicated for Azure SQL services
- Azure privet link for Azure SQL server

![](/assets/images/Azure/network//PL-Azure%20Networking%20from%20Zero%20to%20Hero-V4.drawio.png)  

- 3 Azure subscription
- 3 Azure Virtual Network 
- Azure Virtual Network with subntes (segmentation)
- Network Secuirty groups for all subnetes with DenyRules for Inbound / Outbound
- Azure Virtual Gateway for Site to Site VPN connection  
- Azure Nat Gateway for NAT outbound traffic
- Azure Private DNS zones dedicated for Azure SQL services
- Azure privet link for Azure SQL server
- Azure Network Manager

![](/assets/images/Azure/network//PL-Azure%20Networking%20from%20Zero%20to%20Hero-V5.drawio.png)



---

## ⚙️ Tips and Best Practices

- Create good naming convention 
- Keep it simple 
- Subnet with small mask like 28 / 26
- One Virtual Network per Azure Subscription per region
- Use NAT gateway 
- Avoid overlap network spaces with you on-premises environment 
- Use Hub spoken network if it’s needed 
- Not all resources need to have privet access
- DNS resolving is important – all azure resources work over HTTPS
- Good description of routing tables
- Private DNS Zones can be linked to multiple Vnet don’t need to user DNS resolver
- NSG create custom rule block all Inboud / Outboud traffic 
- For internet access, use **NAT Gateways** instead of assigning public IPs directly to VMs.



---

## 🧩 Summary

Building a robust Azure network requires thoughtful design and attention to detail.  
Start with a solid landing zone, plan your IPs and subnets carefully, use secure connectivity methods, and monitor everything.

When done right, your Azure network becomes the backbone for all your cloud workloads — secure, scalable, and future-proof.

---

*Author: Michał Machniak*  
*System Administrator / Cloud Architect / DevOps*  
[mmachniak.net](https://mmachniak.net)

> “Automate everything you can — but plan your network manually first.”

