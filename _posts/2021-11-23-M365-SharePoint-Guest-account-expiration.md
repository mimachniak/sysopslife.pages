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
This policy will be inherited globally for all sites new and existing one in our tenant and also all OneDrive sites.

1. In admin center on left side contex menu navigate to **Policies** section
2. Go to **Sharing**
3. Extend **More external sharing settings**  
4. Select **Guest access to a site or OneDrive will expire automatically after theis many days** 
5. Set number of days number from **30** to **730**

![](/assets/images/M365-Guest-Policy/M365-Guest-SPO-admin.png)

## SharePoint Online Administrator - Site sharing policies

Site collections create as standalone or Microsoft 365 Groups (Teams Site) policy can override against global settings, so we can use different policy for different sites but not for OneDrive sites. Witch override options is policy we can: 
-	Extended period of expiration 
-	Disable expiration period for guest access  

To setup policy for dedicated site we need to edit policy on this site form SharePoint Online admin center. 

1. In admin center on left side contex menu navigate to **Sites** section
2. Go to **Active Sites**
3. Select site collection name 



![](/assets/images/M365-Guest-Policy/M365-Guest-SPO-Admin-Site-1.png)


4. In site collection settings change tab to **Policies**

![](/assets/images/M365-Guest-Policy/M365-Guest-SPO-Admin-Site-3.png)

5. **Uncheck** Same as organization-level settings (XX Days) in Expiration of guest access, change settings that you like to override 
- Turn off guest expiration policy, by checking **Guest access doesn’t expire automatically** 
- Change expire policy to less restrictive or extend by changing value in **Guest access expires automatically after this many days** 


![](/assets/images/M365-Guest-Policy/M365-Guest-SPO-Admin-Site-2.png)

>
> OneDrive sites don't have option of overriding expiration policy for guest accounts.
>

## How to manage expiring guest access for OneDrive and SharePoint site

### Notification about guest expiring access

Notification about guest expiring access will be displayed on site level so site Owners and Admins can see how many guest access will expired: 

- Site collection administrators will receive an e-mail notification once per week informing you about all guests that will expire in the next 2 to 3 weeks.
- The banner will appear 2 to 3 weeks before the guest expiration date and will only display on the web app.
- The banner does not display in the mobile application.

![](/assets/images/M365-Guest-Policy/M365-Guest-SPO-Site-6.png)




















<!-- https://support.microsoft.com/en-us/office/manage-guest-expiration-for-a-site-25bee24f-42ad-4ee8-8402-4186eed74dea?ui=en-us&rs=en-us&ad=us -->
