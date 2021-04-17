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
published: true
hidden: true
---

## Why I write this article

Hi I wrote this article to help other start with Office 365 and Intune, without intervention on existing tenant or start as green filed for company to begin. I hope this guide we help you to avoid misconfiguration.

## Prerequisites

Before we begin with this you will need to consider few things:  

+ Is this will be LAB environment and won't be converted to production.
  + Use domain .onmicrosoft.com
  + test all settings
+ Is this PoC or Pilot environment that will be converted to production.

```flow
st=>start: Login
op=>operation: Login operation
cond=>condition: Successful Yes or No?
e=>end: To admin

st->op->cond
cond(yes)->e
cond(no)->op
```

                    
```seq
Andrew->China: Says Hello 
Note right of China: China thinks\nabout it 
China-->Andrew: How are you? 
Andrew->>China: I am good thanks!
```

## First Azure Active Directory Tenant
### What is Tenant ?

 **Tenant** Is isolated dedicated space for our organization for our assets. It is within the overall O365 Data Center which would be the apartment complex. The Tenant is the container for items of your Organization such as users, domains, subscriptions, devices, permissios.  



### Create Azure Active Directory Tenant
## Custom domian for LAB propuse without paying

## Bring you own custom domain
## Activate trial licences

### Office 365
### Intune (Enterprise Mobility Suite)

## Create Custom Accounts
