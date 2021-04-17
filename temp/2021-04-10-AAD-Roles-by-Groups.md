title: "[EN] Micosoft 365 Desired State Configuration (DSC) - Starting ..."
classes: wide
excerpt: "[EN] Micosoft 365 Desired State Configuration (DSC) - Starting ... describe how we can use this in real configuration and what problems will be resolved when solution is implemented. " 
toc: true
header:
  image: /assets/images/DSC/DSC-Home.png
categories:
  - SysOps
  - Scripts
  - Automation
tags:
  - DSC
  - M365
---

## Why I write this article

Change managment and writing documentation is always task that moste of IT Pros don't like but is required by security or bussines. Second part of this  task is to keep documents updated after each change, and this is always challenge because work goes first. But as all move forward now we have Infrastructure as Code, DevOps approche, GIT and Desired State Configuration that we can use to have always compliance in envariment.

## What is Desired State Configuration

 Configuration, management and maintenance of Windows-based servers and now Microsoft365. It allows a PowerShell script to specify the configuration of the machine or tenant using a declarative model in a simple standard way that is easy to maintain and understand. 

## What is Microsoft365DSC

Microsoft365DSC is an Open-Source initiative hosted on GitHub, lead by Microsoft engineers and maintained by the community. It allows you to write a definition for how your Microsoft 365 tenant should be configured, automate the deployment of that configuration, and ensures the monitoring of the defined configuration, notifying and acting on detected configuration drifts. It also allows you to extract a full-fidelity configuration out of any existing Microsoft 365 tenant. The tool covers all major Microsoft 365 workloads such as Exchange Online, Teams, Power Platforms, SharePoint and Security and Compliance.

**Home page of project:** [Microsoft 365 : Desired State Configuration](https://microsoft365dsc.com/ "link title")  