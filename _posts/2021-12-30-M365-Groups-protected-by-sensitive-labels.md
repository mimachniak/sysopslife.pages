---
title: "Microsoft 365 Groups – Sensitive labels assignment for data protection and external sharing. "
classes: wide
excerpt: "Microsoft 365 Groups – Sensitive labels assignment for data protection and external sharing. "
toc: true
header:
  teaser: 
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
4 - Check Group Unified Settings in Azure Active Directory

```powershell

$m365GroupUnifiedSetting = (Get-AzureADDirectorySetting | where -Property DisplayName -Value "Group.Unified" -EQ)
$m365GroupUnifiedTemplate = Get-AzureADDirectorySettingTemplate -Id 62375ab9-6b52-47ed-826b-58e47e0e304b
$aadSetting = $m365GroupUnifiedTemplate.CreateDirectorySetting()

```

5 - Check value for Group Unified settings

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

6 - Setup value for setting: EnableMIPLabels

```powershell

$aadSetting["EnableMIPLabels"] = "True"

```

7 - Save Azure Active Directory settigs for Group Unified

```powershell

Set-AzureADDirectorySetting -Id $m365GroupUnifiedSetting.Id -DirectorySetting $aadSetting

```

8 - Check value for setting: EnableMIPLabels

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

## Sensitive label’s naming and description


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

After planning our settings we can configure  sensitive labels and policy in our organization for Microsoft 365 groups. 

1- Logon to Microsoft 365 compliance ccenter:  [https://compliance.microsoft.com/homepage](https://compliance.microsoft.com/homepage)  

![](/assets/images/M365-Sensitive-labels/M365-Sensitive-labels-1.png)