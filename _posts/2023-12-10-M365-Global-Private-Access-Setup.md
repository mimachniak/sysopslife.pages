---
title: "Microsoft 365 Global Private Access Setup and replace classic VPN. "
classes: wide
date: 2023-12-10
excerpt: "Microsoft 365 Graph synchronization GAL to SharePoint Online List accross multiple tenants. "
toc: true
header:
  teaser: /assets/images/teaser/MicrosoftGraphAPI-GAL-Sync-to-SPO_01.png
  og_image: /assets/images/teaser/MicrosoftGraphAPI-GAL-Sync-to-SPO_og.png
categories:
 - Microsoft365
 - Security
 - VPN
tags:
  - Access
  - Security
  - M365
published: false
hidden: true
---

## Why I write this article


When we create SharePoint and multiple tenants we always see that we are missing information about users between tenant that are working in the same organization but have diffrent Microsoft365 tenants. One of solutions for ths is create list in SharePoint that will contain informations about users form diffrent tenants. On list we can create filters, viwes and easy search for contacts.  
For creation of this solution we will use Microsoft Graph API that is integrating all components in Office 365.

PowerShell script base on list of users in Microsoft365 tenant will 
-	Create new list item witch all user data 
-	Update existing list item witch user data
-   Script will create output summary and logs what was changed

Attributes downloaded for each user and add or update in SharePoint list.

    ```
            {
                "businessPhones": [
                    "+48 17 555 1122"
                ],
                "displayName": "Jan Kowalski",
                "givenName": "Jan",
                "jobTitle": "IT HelpDesk",
                "mail": "jan.kowalski@sys4ops.pl",
                "mobilePhone": "+48 666555111",
                "officeLocation": null,
                "preferredLanguage": null,
                "surname": "Kowalski",
                "userPrincipalName": "jan.kowalski@sys4ops.pl",
                "id": "1c227ac6-51a6-401e-bd39-96eee6270b0f"
        }
    ```
## Creation of Service Principals in Tenants

We need two service principal on 2 different tenants, one that will have access to user list when will get data and second one witch access to SharePoint Online list when we will add and update contacts.  

### Tenant witch user access

1. Log on to Azure Active Directory witch **Global Administrator Rights**
2. Navigate to **App registrations**

    ![](/assets/images/M365-GAL/M365-GAL-Sync-witch-SPO-01.png)  

3. Click on **New registration**

    ![](/assets/images/M365-GAL/M365-GAL-Sync-witch-SPO-02.png)  

4. Enter application **Name**

    ![](/assets/images/M365-GAL/M365-GAL-Sync-witch-SPO-03.png)  

5. Click in **Register** 
6. Open application 
7. Navigate to **API permissions**
8. Click **Add permission**

    ![](/assets/images/M365-GAL/M365-GAL-Sync-witch-SPO-04.png)  

9. Choose **Microsoft Graph**

    ![](/assets/images/M365-GAL/M365-GAL-Sync-witch-SPO-05.png) 

10. Choose **Application permissions** Your application runs as a background service or daemon without a signed-in user.

    ![](/assets/images/M365-GAL/M365-GAL-Sync-witch-SPO-06.png)  

11. Form list select those permissions  
    Directory.Read.All  
    MailboxSettings.Read  
    User.Read  
    User.Read.All  
12. Grant admin consent for permissions

    ![](/assets/images/M365-GAL/M365-GAL-Sync-witch-SPO-07.png)  

13. Navigate in application to **Certificates & secrets**
14. Click on **New Client secret**

    ![](/assets/images/M365-GAL/M365-GAL-Sync-witch-SPO-08.png)  

16. Save value of secret

### Tenant witch SharePoint Online list

1. Log on to Azure Active Directory witch **Global Administrator Rights**
2. Navigate to **App registrations**
3. Click on **New registration**
4. Enter application **Name**
5. Click in **Register** 
6. Open application 
7. Navigate to **API permissions**
8. Click **Add permission**
9. Choose **Microsoft Graph**
10. Choose **Application permissions** Your application runs as a background service or daemon without a signed-in user.
11. Form list select those permissions  
    Sites.Selected
    Sites.Read.All
12. Grant admin consent for permissions 

    ![](/assets/images/M365-GAL/M365-GAL-Sync-witch-SPO-09.png)  

13. Navigate in application to **Certificates & secrets**
14. Click on **New Client secret**
16. Save value of secret

## SharePoint online site and list preparation

1.	Logon to [https://portal.office.com](https://portal.office.com)
2.	Navigate to existing SharePoint Online site or create new one
3.	Create new Custom SharePoint List
4.	On **list settings** add new **columns** 

        DisplayName	Single line of text	
        givenName	Single line of text	
        surName	Single line of text	
        userPrincipalName	Single line of text
        mail	Single line of text	
        JobTittle	Single line of text	
        Office	Single line of text	
        Company	Single line of text	
        Department	Single line of text	
        City	Single line of text	
        StreetAddress	Single line of text	
        Province	Single line of text
        postalCode   Single line of text
        Country	Single line of text	
        Mobile	Single line of text	
        OfficePhoneNumber	Single line of text	

5.	On **list settings** in **indexed columns** create new index on **userPrincipalName** column

    ![](/assets/images/M365-GAL/M365-GAL-Sync-witch-SPO-10.png) 

    ![](/assets/images/M365-GAL/M365-GAL-Sync-witch-SPO-11.png)  

    ![](/assets/images/M365-GAL/M365-GAL-Sync-witch-SPO-12.png)   

6.	Install PowerShell module 

    ```powershell
    Install-Module -Name PnP.PowerShell
    ```

7.	Connect to SharePoint site that is hosting custom list 

    ```powershell
    Connect-PnPOnline https://tenantname.sharepoint.com/sites/siteName -Interactive
    ```
8.	Grant permissions for application created witch site permissions 

    ```powershell
    Grant-PnPAzureADAppSitePermission -AppId 'Application ID created witch Site.Selected permissions' -DisplayName 'App Name here' -Site 'https://tenantname.sharepoint.com/sites/sitename' -Permissions Write
    ```


## Source code and script

- spoSiteName - SharePoint online site name that will be hosting list example. **My Demo Site**
- spoListName - SharePoint custom list name example. **OrganizationContacts**
- spoTenantId - Microsoft 365 tenant ID witch SharePoint site example. **f5d4b86-859e-4b25-ad2a-074c9d598234d**
- spoClient_id - Application ID created in **3** on tenant witch SharePoint site example. **9f38f4c1-d1ca-4398-b760-52377b9426b3**
- spoClient_secret secret generated on step **14** ********************************
- aadTenantId - Microsoft 365 tenant ID witch users that will be added to SharePoint site list example. **d9b560f9-5cf3-4d1b-aeef-0a7e00934498**
- aadTlient_id - Application ID created in **3** on tenant witch users that will be added to SharePoint site list. example. **058f94e0-0647-4137-8bb9-893b0a953e1d** 
- aadClient_secret - secret generated on step **14** ***************************** 

    ![](/assets/images/M365-GAL/M365-GAL-Sync-witch-SPO-13.png) 

### Parameters description and example run

### Code on Github
Change parameter data dedicated for your environment, script can be downloaded form my repository on GitHub: [M365-GAL-sync-witch-SPO-ContactList-Client.ps1](https://github.com/mimachniak/sysopslife-scripts/blob/master/M365/M365-GAL-sync-witch-SPO-ContactList-Client.ps1)


