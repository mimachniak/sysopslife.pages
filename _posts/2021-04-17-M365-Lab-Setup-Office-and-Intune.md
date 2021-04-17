---
title: "[EN] Micosoft 365 Setup Lab Environment M365 and Intune"
excerpt: "[EN] Micosoft 365 Setup Lab Environment - Create your own Lab with domains setup or prepare new tenant for you company. In this article we will go through creating new tenant, add custom domains for LAB purpose, assigned licences. " 
toc: true
classes: wide
header:
  image: /assets/images/M365-Lab/M365-Lab-Home.png
  teaser: /assets/images/M365-Lab/M365-Lab-Home.png
categories:
  - SysOps
  - Configuration
tags:
  - M365
  - Intune
published: false
hidden: true
---

## Why I write this article

Hi I wrote this article to help other start with Office 365 and Intune, without intervention on existing tenant or start as green filed for company to begin. I hope this guide we help you to avoid misconfiguration.

## Prerequisites

Before we begin with this you will need to consider few things:  

+ Is this will be LAB environment and won't be converted to production.
  + LAB Only: Tenat name suppose not be connected to organization name, once used tenant name connot be reused or changed.. **Example:** demo{Random Numbers} 
  + LAB Only: Use the highest licences like E5 to check all settings and dependencies for compare.
  + LAB Only: Don't use production custom domains, domain can be assigne only to one **Tenant**.
+ Is this PoC Pilot environment that will be converted to production.
  + PoC: Tenat name suppose not be connected to organization name, once used tenant name connot be reused or changed. **Example:** poc{Random Numbers} 
  + PoC: Use the highest licences like E5 to check all settings and dependenciesfor compare.
  + PoC: Collect user case that you like to present.
  + PoC: Don't use production custom domains, domain can be assigne only to one **Tenant**.
+ Is this Pilot environment that will be converted to production.
  + Pilot: Tenant name need to be connected to Organization for adoption propose.
  + Pilot: Tenant name need to be approve by bussines becasue will be used as first part of URL in **Sharepoint** and **OneDrive**
  + Pilot: Use licences that match Organization business cases to avoid misconfiguration.
  + Pilot: Don't use production custom domains, to avoid data flow distributions.  

>   
> **Notice:** Tenant name cannot be change after creation.
>  
--------------------------
## First Azure Active Directory Tenant
### What is Tenant ?

 **Tenant** Is isolated dedicated space for our organization and our assets. The Tenant is the container for items of your Organization such as users, domains, subscriptions, devices, permissios, Office 365 Data. 

Exmaple of data isolation

![](/assets/images/M365-Lab/M365-Lab-Tenant.png)


### Create Azure Active Directory Tenant
## Custom domian for LAB propuse without paying

## Custom domian for Pilot propuse without paying

## Bring you own custom domain
## Activate trial licences

### Office 365
### Intune (Enterprise Mobility Suite)

## Create Custom Accounts
