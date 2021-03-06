---
title: "Intune (Endpoint Manager) - How to deploy Office templates and keep them up to date (recurring)."
classes: wide
excerpt: "Intune (Endpoint Manager) - How to deploy Custom Office templates and keep them up to date. Templates are often store on some file share from where computer / user can download them, so storage is some of limitation when we like to use full cloud MDM in form of SaaS."
toc: true
header:
  teaser: /assets/images/teaser/Intune-Office-Template.png
categories:
  - SysOps
  - Office
  - PowerShell
tags:
  - Intune
  - MDM
  - Office
  - Automation
  - Azure
published: true
hidden: false
---

![image-center](/assets/images/Intune-Office-Template.png)

## Why I write this article

Organization often use **custom office templates** that are available in **Word/Excel/PowerPoint** as custom. Those templates contain logs, footers of organization and custom fonts. 

IT ops deploy **custom office and templates** to End User devices using **Group Policy** in **Active Directory** and file share to store those **custom office templates**. This task is common but have few limitations:
-	Users need to be connected over VPN and have access to file share
-	Custom templates don’t update after change on file share
-	This solution requires Active Directory management 

To allow move management to Intune and keep business process of delivering custom office templates to devices that are manage by Intune and don’t connect to **Active Directory** we develop this solution base on PowerShell scripts. 

Intune witch PowerShell scripts also have some limitations like 
-	PowerShell scripts deploy by won’t run schedule time all time
-	Intune agent don’t have any profile

To avoid these limitations, we split this solution to 2 parts:
1.	Registered solution on local computer that will in dedicated schedule call script.
2.	Call script each time when will be running will download fresh **custom office templates** to local drive.

On this article I will show how to do it with full cloud based solution, for this I will use Azure **storage account** and Intune with **PowerShell**.


## Prerequisites 

On this article we will use: 
* Azure storage account
* Intune (Endpoint Manager)
* PowerShell scripts

## General flow and execution

Templates will be refreshed each time user log on to PC by scheduler task that will execute script form URL

![](/assets/images/Office-template-mechanimz.png)

## Azure subscription creation

First we will need to create free Azure Subscription:


Link : [Create Free Azure account](https://azure.microsoft.com/en-us/free/)
Guide:
<iframe width="560" height="315" src="https://www.youtube.com/embed/0KEfaUfolVs" title="YouTube video player" frameborder="0" allow="accelerometer; autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture" allowfullscreen></iframe>  
  

## Azure storage account configuration

To storage office templete and script responsible for deploying templates in local machine, we will use azure storage account with **public blob container**. Storage account is one of cheaper solution to keep data use in this solution. Cost is based on amount of data counted in GB per month so for scripts and documents template it won't be so match. 

### Create storage account in Azure

a) Logon to [https://portal.azure.com ](https://portal.azure.com)  
b) On top search bar type **storage account**  

![](/assets/images/Azure/azure-st-1.PNG)  

c)  On navigation bar click **Create**  

![](/assets/images/Azure/azure-st-2.PNG)

d) Enter data in **Basic** tab  

![](/assets/images/Azure/azure-st-3.PNG)

e) On **Advanced** tab click **Next**  
f) On **Networking** tab click **Next**  
g) On **Data protection** tab click **Next**  
h) On **Tags** tab click **Next**  
i) On **Review + Create** tab click **Create** if Validation passed  

![](/assets/images/Azure/azure-st-4.PNG)

### Create public blob on storage account

a) Logon to [https://portal.azure.com ](https://portal.azure.com)  
b) On top search bar type **storage account**  
c) Click on storage account created previously  
d) On left navigation menu click on **Containers** and n navigation bar click   **Container**

![](/assets/images/Azure/azure-st-5.PNG)


e) Enter date for new container and chage public access level then click   **Create**

![](/assets/images/Azure/azure-st-6.PNG)


f) Upload office template files by clicking **Upload** on navigation menu  

![](/assets/images/Azure/azure-st-7.PNG)

![](/assets/images/Azure/azure-st-8.PNG)

g) Click on created container with name **scripts** and upload script  

![](/assets/images/Azure/azure-st-12.PNG)

![](/assets/images/Azure/azure-st-13.PNG)

## PowerShell scripts data preparation for all actions

Powershell scripts used for this solution can be found on my repository in GitHub under this link: [https://github.com/mimachniak/sysopslife-scripts/tree/master/Intune](https://github.com/mimachniak/sysopslife-scripts/tree/master/Intune)

### Script responsible for downloading templates to local environment

First, we will prepare script that is responsible for downloading Office templates form Storage account to local drive and put those templates in dedicated folder that is also set by this script. So as starting point you need to prepare configuration parameters dedicated for your organization or use my.   

a) Change parameter date dedicated for your environment, script can be downloaded form GitHub: [Intune-DocumentTemplateStorageAccount.ps1](https://github.com/mimachniak/sysopslife-scripts/blob/master/Intune/Intune-DocumentTemplateStorageAccount.ps1)

Parameters that need to be change for you environment

```powershell

$LogLocation = 'C:\Users\Public\Documents\' # Location of lgs generated by execution of the script
$LogFileName = 'OfficeTemplates.log' # log file name


Start-Transcript -Path  $LogLocation$LogFileName # Create log each run script

$storageAccountName = "dofficetemplate.blob.core.windows.net" # Azure Storage Acccount Url
$containerName = "office-template" # Azure storage account blob container with files
$TemplateFolderName = 'Office Templates' # Folder name on desktop when files willbe downloaded

```
b) Storage account **Name** is available in **Endpoints** section in Azure on **Blob**  

![](/assets/images/Azure/azure-st-9.PNG)


Full script body will look like this 

```powershell

<#  
.SYNOPSIS  
    Script will download new Office templates each time it runs.
.DESCRIPTION  
    Script connects to remote azure storage account, and download new Office templates.
    Template are refresh each time script run and putted in dedicated folder.
 
    
.NOTES  
    File Name  : Intune-DocumentTemplateStorageAccount.ps1 
    Author     : Michal Machniak  
    Requires   : PowerShell V5.1
.LINK

    Github: https://github.com/mimachniak/sysopslife-scripts
    Site: https://sysopslife.sys4ops.pl/
    Modules links:
#>

#------------------------------------ Input parameters  ------------------------------------------------- #

$LogLocation = 'C:\Users\Public\Documents\' # Location of lgs generated by execution of the script
$LogFileName = 'OfficeTemplates.log' # log file name


Start-Transcript -Path  $LogLocation$LogFileName# Create log each run script

$storageAccountName = "dofficetemplate.blob.core.windows.net" # Azure Storage Acccount Url
$containerName = "office-template" # Azure storage account blob container with files
$TemplateFolderName = 'Office Templates' # Folder name on desktop when files willbe downloaded



    $MyDocumentsPath = [Environment]::GetFolderPath("MyDocuments") # Get default location of Documents
    $CustomTemplatesLocation = $MyDocumentsPath+"\"+$TemplateFolderName #Folder for custom templates

    if ((Test-Path $CustomTemplatesLocation) -eq $false)
        {
            New-Item -Path $MyDocumentsPath -Name $TemplateFolderName -ItemType "directory"
        }

        $CustomWordTemplatesLocation = Get-ItemProperty -Path "HKCU:\SOFTWARE\Microsoft\Office\16.0\Word\Options" -Name "PersonalTemplates"
        $CustomPowerPointTemplatesLocation = Get-ItemProperty -Path "HKCU:\SOFTWARE\Microsoft\Office\16.0\PowerPoint\Options" -Name "PersonalTemplates"

    if ($CustomWordTemplatesLocation -ne $null)
        {
            Write-Host "PersonalTemplates reg exist in HKCU:\SOFTWARE\Microsoft\Office\16.0\Word\Options"
        }
        else {

            New-ItemProperty -Path HKCU:\SOFTWARE\Microsoft\Office\16.0\Word\Options -Name "PersonalTemplates" -Value $CustomTemplatesLocation -PropertyType ExpandString -Force # Setup custom temaplate folder for Word
        }


    if ($CustomPowerPointTemplatesLocation -ne $null)
        {
            Write-Host "PersonalTemplates reg exist in HKCU:\SOFTWARE\Microsoft\Office\16.0\PowerPoint\Options"
        }
        else {

            New-ItemProperty -Path HKCU:\SOFTWARE\Microsoft\Office\16.0\PowerPoint\Options -Name "PersonalTemplates" -Value $CustomTemplatesLocation -PropertyType ExpandString -Force # Setup custom temaplate folder for PowerPoint
        }

write-host "https://$storageAccountName/$containerName/?comp=list" # URL with files

$xml = Invoke-WebRequest -Method Get -Uri "https://$storageAccountName/$containerName/?comp=list" -ContentType "application/xml" # Get All files in Blob Container without SAS toke
[xml] $i = $xml.Content -replace "ï»¿", ""
$ListBlobs = $i.EnumerationResults.Blobs.Blob

    foreach ($BlobFile in $ListBlobs)
    {

        $TemplatesLocation = $CustomTemplatesLocation+"\"+$BlobFile.Name

        Write-Host "Location of file [$TemplatesLocation]"

        $fileDownloadBlob = try {

                Invoke-WebRequest -Uri $BlobFile.Url -OutFile $TemplatesLocation

                Write-Host "File downloaded [$($BlobFile.Name)]"
            }
            catch [System.Net.WebException] {

                Write-Warning "Exception was caught: $($_.Exception.Message)"
    
            }
    }

Stop-Transcript # End logging

```

c) Upload script to **Container** on storage account, the same **container** name **scripts**  

![](/assets/images/Azure/azure-st-13.PNG)

d) Click on uploade script with name **Intune-DocumentTemplateStorageAccount.ps1**  
f) Copy **URL** and past it to second script that will be deploy by **Intune**  

![](/assets/images/Azure/azure-st-11.PNG)

### Script responsible registering by Intune task scheduler that will execute script each time user log in to desktop.

a) Change parameter data dedicated for your environment, script can be downloaded form my repository on GitHub: [Windows10-taskScheduler-register-executescript-url.ps1](https://github.com/mimachniak/sysopslife-scripts/blob/master/Intune/Windows10-taskScheduler-register-executescript-url.ps1)

Parameters that need to be change for you environment 

```powershell

$LogLocation = 'C:\Users\Public\Documents\' # Location of lgs generated by execution of the script
$LogFileName = 'IntunePowerShell.log' # log file name

$storageAccountNameScriptUrl = 'https://dofficetemplate.blob.core.windows.net/scripts/Intune-DocumentTemplateStorageAccount.ps1' # Azure Storage Acccount Url to script

```
Full script body will look like this 

```powershell

<#  
.SYNOPSIS  
    Execute PowerShell script form URL.
.DESCRIPTION  
    Create Windows 10 task that will executer script publish over URL, not on local disk.
 
    
.NOTES  
    File Name  : Windows10-taskScheduler-register-executescript-url.ps1 
    Author     : Michal Machniak  
    Requires   : PowerShell V5.1
.LINK

    Github: https://github.com/mimachniak/sysopslife-scripts
    Site: https://sysopslife.sys4ops.pl/
    Modules links:
#>

#------------------------------------ Input parameters  ------------------------------------------------- #

$LogLocation = 'C:\Users\Public\Documents\' # Location of lgs generated by execution of the script
$LogFileName = 'IntunePowerShell.log' # log file name

$storageAccountNameScriptUrl = 'https://dofficetemplate.blob.core.windows.net/office-template/Intune-DocumentTemplateStorageAccount.ps1' # Azure Storage Acccount Url

Start-Transcript -Path  $LogLocation$LogFileName # Create log each run script

$argumentSring = '-ExecutionPolicy Unrestricted -WindowStyle Hidden -NonInteractive -NoLogo'+' "'+'iex ((New-Object System.Net.WebClient).DownloadString('+"'"+$storageAccountNameScriptUrl+"'"+'))'+'"'
$argumentSring
$action = New-ScheduledTaskAction -Execute 'Powershell.exe' -Argument $argumentSring
$trigger = New-ScheduledTaskTrigger -AtLogon -User $env:USERNAME
Register-ScheduledTask -Action $action -Trigger $trigger -TaskName "Intune Office Template" -User $env:USERNAME -Description "Refreshe Office Templates"

Stop-Transcript # End logging

```

b) Logon to https://endpoint.microsoft.com [https://endpoint.microsoft.com ](https://endpoint.microsoft.com) with minimum permission as Intune Administrator  
c) In Endpoint Manager Console navigate to section **Devices --> Windows --> PowerShell scripts**  

![](/assets/images/Intune/Intune-OfficeTemplate-1.png)

d) On navigation click **Add**  
f) Enter **Name** and **Description**  

![](/assets/images/Intune/Intune-OfficeTemplate-2.png)

g) On **Script Settings** add script and select **Run this script using the logged on credentials**  

![](/assets/images/Intune/Intune-OfficeTemplate-3.png)

>
> The script runs with the users’ credentials on the client computer. By default, the script runs in system context.
>

f) On **Assignments** and **All Users** or selected dedicated group of **Users** for which you like to implement this solution  

![](/assets/images/Intune/Intune-OfficeTemplate-4.png)

h) Click **Next** and **Add**

![](/assets/images/Intune/Intune-OfficeTemplate-5.png)

## Windows 10 verification

### Intune policy synchronization force

a) Log on to device  
b) Navigate to **Settings --> Accounts --> Access work or school**  
b) Click on connection and then on **Info**  

![](/assets/images/Intune/Intune-OfficeTemplate-6.png)

c) Scroll down and click on **Sync**  

![](/assets/images/Intune/Intune-OfficeTemplate-7.png)

d) After sync completed (can take to 90 min) go to **task scheduler** and verify that new task was registered under **Task Scheduler Library**   
e) Verify that task was registered on Windows 10 in **Task Scheduler Library**    

![](/assets/images/Intune/Intune-OfficeTemplate-8.png)

f) Run taks in ***task scheduler** and verify that templates was downloaded to local folder or re-logon user to trigger **task scheduler**  

![](/assets/images/Intune/Intune-OfficeTemplate-9.png)

g) Verify that after task run templates files are downloaded to designated location on local hard drive 

![](/assets/images/Intune/Intune-OfficeTemplate-10.png)

i) Verify that Microsft Word and Excel show document templates in section **Personal**

![](/assets/images/Intune/Intune-OfficeTemplate-11.png)


j) Log files will be created for each action and replace each run under path **C:\Users\Public\Documents** all logs are super pass on each run  

* IntunePowerShell.log - Contains logs of Intune executing tasks that will runs script responsible for registering in Task Scheduler Library recurring task that responsible for downloading Office templates to local drive
* OfficeTemplates.log - Contains logs of script that is responsible for downloading templates to local drive, script is executed form URL  

![](/assets/images/Intune/Intune-OfficeTemplate-12.png)

```text

**********************
Windows PowerShell transcript start
Start time: 20210903001903
Username: AzureAD\MichalMachniak
RunAs User: AzureAD\MichalMachniak
Configuration Name: 
Machine: DESKTOP-FQQ6986 (Microsoft Windows NT 10.0.19041.0)
Host Application: C:\Windows\SysWOW64\WindowsPowerShell\v1.0\powershell.exe -executionPolicy bypass -file C:\Program Files (x86)\Microsoft Intune Management Extension\Policies\Scripts\3a7dc62f-8b76-4e72-ab46-7ed2d7810eea_ce648ef6-e1f5-465d-b730-e71939ee35b4.ps1
Process ID: 7384
PSVersion: 5.1.19041.1151
PSEdition: Desktop
PSCompatibleVersions: 1.0, 2.0, 3.0, 4.0, 5.0, 5.1.19041.1151
BuildVersion: 10.0.19041.1151
CLRVersion: 4.0.30319.42000
WSManStackVersion: 3.0
PSRemotingProtocolVersion: 2.3
SerializationVersion: 1.1.0.1
**********************
Transcript started, output file is C:\Users\Public\Documents\IntunePowerShell.log
-ExecutionPolicy Unrestricted -WindowStyle Hidden -NonInteractive -NoLogo "iex ((New-Object System.Net.WebClient).DownloadString('https://dofficetemplate.blob.core.windows.net/office-template/Intune-DocumentTemplateStorageAccount.ps1'))"

TaskPath                                       TaskName                          State
--------                                       --------                          -----
\                                              Intune Office Template            Ready
**********************
Windows PowerShell transcript end
End time: 20210903001904
**********************

```

```text

**********************
Windows PowerShell transcript start
Start time: 20210903203139
Username: AzureAD\MichalMachniak
RunAs User: AzureAD\MichalMachniak
Configuration Name: 
Machine: DESKTOP-FQQ6986 (Microsoft Windows NT 10.0.19041.0)
Host Application: C:\Windows\System32\WindowsPowerShell\v1.0\powershell.exe
Process ID: 5552
PSVersion: 5.1.19041.1151
PSEdition: Desktop
PSCompatibleVersions: 1.0, 2.0, 3.0, 4.0, 5.0, 5.1.19041.1151
BuildVersion: 10.0.19041.1151
CLRVersion: 4.0.30319.42000
WSManStackVersion: 3.0
PSRemotingProtocolVersion: 2.3
SerializationVersion: 1.1.0.1
**********************
Transcript started, output file is C:\Users\Public\Documents\OfficeTemplates.log
PersonalTemplates reg exist in HKCU:\SOFTWARE\Microsoft\Office\16.0\Word\Options
PersonalTemplates reg exist in HKCU:\SOFTWARE\Microsoft\Office\16.0\PowerPoint\Options
PersonalTemplates reg exist in HKCU:\SOFTWARE\Microsoft\Office\16.0\Excel\Options
https://dofficetemplate.blob.core.windows.net/office-template/?comp=list
Location of file [C:\Users\MichalMachniak\OneDrive - sys4opshub\Documents\Office Templates\Demo-tempate -v2.dotx]
File downloaded [Demo-tempate -v2.dotx]
Location of file [C:\Users\MichalMachniak\OneDrive - sys4opshub\Documents\Office Templates\Demo-tempate.dotx]
File downloaded [Demo-tempate.dotx]
**********************
Windows PowerShell transcript end
End time: 20210903203140
**********************


```

## Summary

I hope this article will allow to eliminate in your organization another step that block you witch moving to cloud, if you like this post please share it or send feedback. 