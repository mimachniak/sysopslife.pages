---
title: "Microsoft 365 Groups – Sensitive labels assignment for data protection and external sharing. "
classes: wide
date: 2022-01-06
excerpt: "Microsoft 365 Groups – Sensitive labels assignment for data protection and external sharing. "
toc: true
header:
  # image: /assets/images/M365-Sensitive-labels/M365-Sensitive-labels-21.png
  teaser: /assets/images/M365-Sensitive-labels/M365-Sensitive-labels-21.png
categories:
 - Microsoft365
 - Security
 - SharePoint
 - Outlook
 - Teams
tags:
  - PowerShell
  - Sensitive
  - Protection
  - Groups
  - Guest
  - Collaboration
  - Sharing
  - External
published: true
hidden: false
---


## Why I write this article

Microsoft Groups 365 are connected to services like Outlook Groups, SharePoint Sites, Teams, Planner, Yammer etc. and they allow exchange date witch users inside organization and outside organization (Guest accounts). Most of organization need to decide how to protect data stored in those groups. So one of the way is build manual process when Help Desk Team will create dedicated Microsoft 365 group, another way is building flow or use third party and setup settings for data exchange all those approaches evolve IT department that based on information from end user will create group and setup setting. 
On this article I like to show different approaches to this topic when end user will be responsible for creating Microsoft 365 Groups and base on chosen classification in process of creation we will automatically applied settings for data sharing in this group.

##	What are Microsoft 365 Groups

Groups in Microsoft 365 let you choose a set of people that you wish to collaborate with and easily set up a collection of resources for those people to share. Resources such as a shared Outlook inbox, shared calendar or a document library for collaborating on files.  

You don’t have to worry about manually assigning permissions to all those resources because adding members to the group automatically gives them the permissions they need to the tools your group provides. Additionally, groups are the new and improved experience for what we used to use distribution lists or shared mailboxes to do.  


###	Microsoft 365 Groups from a variety of tools including Outlook, Outlook on the web, Outlook Mobile, SharePoint, Planner, Teams, SharePoint and more

* Create a group in Outlook
* Create a Microsoft Team
* Create a family group
* Create a group in Yammer
* Create a team site in SharePoint Online
* Create a plan in Microsoft Planner

### The following resources are included in a Microsoft 365 Group:
*	A (hidden) shared Outlook inbox
*	A (hidden) shared calendar
*	A SharePoint document library and Site
*	A Power BI workspace
*	A Team (if the group was created from Teams)
*	A Planner (if the group was created from Planner)
*	Yammer (if the group was created from Yammer)
*	Roadmap (if Project for the web is licensed)

###	Microsoft 365 Group, you must decide if you want it to be a private group or a public group.
* Public group. Any user in your organization can join public groups without the need of an administrator or owner to add or approve them. Therefore, content in a public group can be seen by anybody in your organization as soon they join the group.
* Private group. Content in a private group can only be seen by the members of the group. People who want to join a private group must be approved by a group owner. Private groups are separated into discoverable and non-discoverable private groups.
  - Discoverable private group. These groups can be seen by all users of a tenant, and users can file a request to join the group.
  - Non-discoverable private group. These groups are only visible for users that are already members of the group.

# Prepartion of Azure Active Directory

1 - Open PowerShell Window
2 - Install Azure AD PowerShell module 
3 - Connect to Azure Active Directory by PowerShell

```powershell

Install-Module AzureADPreview
Import-Module AzureADPreview
Connect-AzureAD

```
**4** - Check Group Unified Settings in Azure Active Directory

```powershell

$m365GroupUnifiedSetting = (Get-AzureADDirectorySetting | where -Property DisplayName -Value "Group.Unified" -EQ)
$m365GroupUnifiedTemplate = Get-AzureADDirectorySettingTemplate -Id 62375ab9-6b52-47ed-826b-58e47e0e304b
$aadSetting = $m365GroupUnifiedTemplate.CreateDirectorySetting()

```

**5** - Check value for Group Unified settings

```powershell

$aadSetting.Values

## Output for [EnableMIPLabels ]##

Name                          Value
----                          -----
EnableMIPLabels               False
CustomBlockedWordsList
EnableMSStandardBlockedWords  False
ClassificationDescriptions
DefaultClassification
PrefixSuffixNamingRequirement
AllowGuestsToBeGroupOwner     False
AllowGuestsToAccessGroups     True
GuestUsageGuidelinesUrl
GroupCreationAllowedGroupId
AllowToAddGuests              True
UsageGuidelinesUrl
ClassificationList
EnableGroupCreation           True


```

**6** - Setup value for setting: EnableMIPLabels

```powershell

$aadSetting["EnableMIPLabels"] = "True"

```

**7** - Save Azure Active Directory settigs for Group Unified

```powershell

Set-AzureADDirectorySetting -Id $m365GroupUnifiedSetting.Id -DirectorySetting $aadSetting

```

**8** - Check value for setting: EnableMIPLabels

```powershell

$aadSetting.Values

## Output for [EnableMIPLabels ]##

Name                          Value
----                          -----
EnableMIPLabels               True
CustomBlockedWordsList
EnableMSStandardBlockedWords  False
ClassificationDescriptions
DefaultClassification
PrefixSuffixNamingRequirement
AllowGuestsToBeGroupOwner     False
AllowGuestsToAccessGroups     True
GuestUsageGuidelinesUrl
GroupCreationAllowedGroupId
AllowToAddGuests              True
UsageGuidelinesUrl
ClassificationList
EnableGroupCreation           True

```

## Sensitive label’s naming and settings


### Privacy and external user access settings detalies

These options apply to all Microsoft 365 Groups and teams that have this label applied. When applied, these settings will replace any existing privacy settings for the team or group. If the label is removed, users can change it again.

* Private – Only team owners and members can access the group or team, and only owners can add members
* Public – Anyone in organization can access the group or team (Include content) and add members.
* External user access – Let Microsoft 365 Group owners add people outside organization to the group as guest. 

### Control external sharing from labeled SharePoint sites

When this label is applied to a SharePoint site (Teams / Yammer / Outlook group), these settings will replace existing external sharing settings on this site.

* Anyone – Users can share files and folders using links that don’t require sin in 
* New and existing guest – Guest must sing in or provide verification code.
* Existing guest – Only guest in your organization directory.
* Only people in your organization – No external sharing allowed 

### Design labels name and settings  that need to be created in Microsoft365 Compliance Center 

Before we start implementation we need to plan naming convention  and settings for sensitive labels, when we are choosing names we need to remember that this will be visible for end user and base on name and description user will chose witch protection settings need to be applied.  

| Label name | Internal | B2B-Partner | External |
| ----------- | ----------- | ----------- | ------------ |
|Description | SharePoint, Teams, M365 groups and content can be access only by employees in organization. No external access is allowed. | SharePoint, Teams, M365 groups and content can be access by employees in organization and invited partners (Guest). | SharePoint, Teams, M365 groups and content can be access by employees in organization and invited partners with option to share content without authorization. | 
|Group settings | Private | Private | Private | 
|External user access | False | True | True | 
|Site settings | Only people in your organization | New and existing guests | Anyone | 

## Configuration sensitive labels for Microsoft 365 Groups  

After planning our settings we can configure sensitive labels and policy in our organization for Microsoft 365 groups. Policy will force user to chose **label** in process of creation: 
- Microsoft 365 Groups, 
- Microsoft Teams, 
- SharePoint Site, 
- Yammer, 
- Planner  

**1** - Logon to Microsoft 365 compliance ccenter:  [https://compliance.microsoft.com/homepage](https://compliance.microsoft.com/homepage)  
**2** - On left navigation menu go to **Information protection**  
![](/assets/images/M365-Sensitive-labels/M365-Sensitive-labels-1.png)  


**3** - On navigation menu select **labels**
![](/assets/images/M365-Sensitive-labels/M365-Sensitive-labels-2.png)  

**4** - Click **Create a label**  
![](/assets/images/M365-Sensitive-labels/M365-Sensitive-labels-3.png)  

**5** - Enter date on required fields 

- **Name** This is the name of the label your admins will see in the Microsoft 365 compliance center when configuring or managing labels  
- **Display name** This is the name of the label your users will see in the apps where it's published (like Word, Outlook, and SharePoint). Be sure to come with a name that helps them understand what it's used for (for example, 'Confidential' or 'Personal')  

- **Description for users** When this label is applied to content, this tooltip will appear to users when they view the label in their apps (like Word, Outlook, and Teams)  

- **Description for admins** This description will appear only to admins who manage this label in the security center or compliance center.  

>
> All information was planned in section : **Design labels name and settings  that need to be created in Microsoft365 Compliance Center**
>

![](/assets/images/M365-Sensitive-labels/M365-Sensitive-labels-4.png)  


**6** - Define the scope for this label we select only **Groups & Sites**  

![](/assets/images/M365-Sensitive-labels/M365-Sensitive-labels-5.png)  


**7** - Define protection settings for groups and sites we chose both options  

![](/assets/images/M365-Sensitive-labels/M365-Sensitive-labels-6.png)  

**8** - Define privacy and external user access settings  

>
> All information was planned in section : **Design labels name and settings  that need to be created in Microsoft365 Compliance Center**
>

![](/assets/images/M365-Sensitive-labels/M365-Sensitive-labels-7.png)  

**9** - Define external sharing and conditional access settings 

>
> All information was planned in section : **Design labels name and settings  that need to be created in Microsoft365 Compliance Center**
>

![](/assets/images/M365-Sensitive-labels/M365-Sensitive-labels-8.png)  

**10** - Review settings 

>
> Repeat step for all 3 labels witch changes on Define external sharing and conditional access settings 
> All information was planned in section : **Design labels name and settings  that need to be created in Microsoft365 Compliance Center**
>

![](/assets/images/M365-Sensitive-labels/M365-Sensitive-labels-9.png) 


**11** - **B2B-Partner** Define external sharing and conditional access settings 


![](/assets/images/M365-Sensitive-labels/M365-Sensitive-labels-11.png) 


**12** - **External** Define external sharing and conditional access settings 


![](/assets/images/M365-Sensitive-labels/M365-Sensitive-labels-12.png) 


## Configuration sensitive labels publish policy for Microsoft 365 Groups  

Creation of labels don’t force any policy to user or organization , to active labels we need to create policy for whole organization or dedicated group of users.  

**1** - Logon to Microsoft 365 compliance ccenter:  [https://compliance.microsoft.com/homepage](https://compliance.microsoft.com/homepage)  
**2** - On left navigation menu go to **Information protection**  
![](/assets/images/M365-Sensitive-labels/M365-Sensitive-labels-1.png)  


**3** - On navigation menu select **labels policies** and click **publish label**

![](/assets/images/M365-Sensitive-labels/M365-Sensitive-labels-13.png)  


**4** - Choose sensitivity labels to publish (Organization can have multiple policies)

![](/assets/images/M365-Sensitive-labels/M365-Sensitive-labels-14.png)  

**5** - Select sensitivity labels to publish

![](/assets/images/M365-Sensitive-labels/M365-Sensitive-labels-15.png)  

**6** - Select group of users that will be allowed to chose label 

![](/assets/images/M365-Sensitive-labels/M365-Sensitive-labels-16.png)  

**7** - Select group of users that will be allowed to chose label 

![](/assets/images/M365-Sensitive-labels/M365-Sensitive-labels-16.png)  


**8** - Policy settings for sites and groups  

**Apply this label by default to groups and sites** – this option will display this label to user as default when Microsoft 365 groups will be created   

**Requires users to apply a label to their group or sites** – this option will force users to use one of published labels and allow us to control settings for Microsoft 365 Groups 

![](/assets/images/M365-Sensitive-labels/M365-Sensitive-labels-17.png)  


**9** - Policy name and description 

![](/assets/images/M365-Sensitive-labels/M365-Sensitive-labels-18.png) 


##	End user experience when Microsoft 365 groups are create witch Microsoft Teams 

![](/assets/images/M365-Sensitive-labels/M365-Sensitive-labels-20.png) 


![](/assets/images/M365-Sensitive-labels/M365-Sensitive-labels-21.png) 