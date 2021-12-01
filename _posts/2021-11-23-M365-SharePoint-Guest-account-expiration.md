---
title: "SharePoint Online - Guest access to site or OneDrive will expire automatically after period of time."
classes: wide
excerpt: "SharePoint Online - New Guest expire policy that will remove access for guest account after period of time form SharePoint Site and OneDrive."
toc: true
header:
  teaser: 
categories:
  - SysOps
  - SharePoint
  - OneDrive
tags:
  - Office
  - Automation
  - Guest
  - Access
  - Policy
published: true
hidden: false
---


## Why I write this article

When we manage organization that is collaborating which other B2B partners we are often ask how e control access partners to information. Of course, there are multiple ways to answer this question. 
One of new approaches in Microsoft 365 we are able to use guest expiration policies that will track all guest access across tenant and automatically remove unnecessary access to information base on tenant, site police. 
On this article I will show how to configure guest policy expiration on different levels, how to review/remove and extend access in SharePoint and OneDrive.

## SharePoint Online Administrator Center

As a starting point we will configure SharePoint sharing policy. Policy settings are located on SharePoint Admin Center, that is accessible by link [https://{domain}-admin.sharepoint.com/](https://{domain}-admin.sharepoint.com/) or you can navigate from Office365 Admin Center. 

1. In admin center on left side contex menu navigate to **Policies** section
2. Go to **Sharing**
3. Extend **More external sharing settings**  
4. Select **Guest access to a site or OneDrive will expire automatically after theis many days** 
5. Set number of days number from **30** to **730**

![](/assets/images/M365-Guest-Policy/M365-Guest-SPO-admin.png)

## SharePoint Online Administrator - Site sharing policies

Site can override global settings, so we can use different policy for different site.
-	Extended period of expiration 
-	Disable expiration period for guest access  































//https://support.microsoft.com/en-us/office/manage-guest-expiration-for-a-site-25bee24f-42ad-4ee8-8402-4186eed74dea?ui=en-us&rs=en-us&ad=us