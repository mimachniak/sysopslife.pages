---
title: "ADConnect migration error on Out sync rules"
excerpt: "How to handle error caused by Out rules in migration process, that is pointing to attributes mapping: AttributeFlowMapping's specified target attribute 'extension_87ce92dfb96b4ac689f8fxxxxxxxxxx6_extensionAttribute1' is not a defined attribute type." 
toc: true
classes: wide
categories:
  - SysOps
  - Configuration
  - Migration
tags:
  - M365
  - ADConnect
published: true
hidden: false
---

## Why I write this article

Hi, In last migration process of connect between servers I was using new preview feature, that is allowing exporting existing configuration like
* Synchronization filters
* synchronization rules 
* Account settings
* Sing In settigs 
* etc.  

In some custom implementation AD connect have Out and In synchronization rules, and migration process don't work for Out rules but rest is working so we like to use it for 90 % of configuration. So in this post I will write workaround for this issue.

## How to prepare AD connect to migration 

Whole process is described in Microsoft docs under this link: 
[Migrate settings from an existing server](https://docs.microsoft.com/en-us/azure/active-directory/hybrid/how-to-connect-import-export-config#migrate-settings-from-an-existing-server)

## Error when importing AD connect settings with custom **Out** rules

**Step 1:** In install AD connect process, choose custom and select **Import Synchronization settings**

![](/assets/images/AdConnect/ADC-01.PNG)

**Step 2:** Follow steps in installation wizard until it finished with error

![](/assets/images/AdConnect/ADC-02.PNG)
![](/assets/images/AdConnect/ADC-03.PNG)
![](/assets/images/AdConnect/ADC-04.PNG)
![](/assets/images/AdConnect/ADC-05.PNG)
![](/assets/images/AdConnect/ADC-06.PNG)

**Step 3:** Log verification to check with rule is causing issue, logs file are located on folder: **C:\ProgramData\AADConnect** or you can navigate from AD connect interface. File name starts:  **trace-**  

* In error we can check that issue is with this attribute: **extension_87ce92dfb96b4ac689f8f53836bffaa6_extensionAttribute1**
* Error and attribute are connected to this syc rule:  AAD - User DirectoryExtension - Cloned - 10/14/2019 11:09:04 AM (**0b0cfd4f-e8b8-47b2-8a15-b0557cef3949**)
* Rule ID with error :  **0b0cfd4f-e8b8-47b2-8a15-b0557cef3949**

`**Note**: Rule ID will be required to implement workaround.`


>
>
> [19:54:05.912] [ 26] [ERROR] Out to AAD - User DirectoryExtension - Cloned - 10/14/2019 11:09:04 AM (**0b0cfd4f-e8b8-47b2-8a15-b0557cef3949**): AttributeFlowMapping's specified target attribute **'extension_87ce92dfb96b4ac689f8f53836bffaa6_extensionAttribute1'** is not a defined attribute type.
> Microsoft.IdentityManagement.PowerShell.Cmdlet.AddADSyncRuleCmdlet
> 
> 
> Exception Data (Raw): Microsoft.Online.Deployment.PowerShell.PowerShellInvocationException: Out to AAD - User DirectoryExtension - Cloned - 10/14/2019 11:09:04 AM (**0b0cfd4f-e8b8-47b2-8a15-b0557cef3949**): AttributeFlowMapping's specified target attribute 'extension_87ce92dfb96b4ac689f8f53836bffaa6_extensionAttribute1' is not a defined attribute type.
> Microsoft.IdentityManagement.PowerShell.Cmdlet.AddADSyncRuleCmdlet
>
> ---> System.ServiceModel.FaultException`1[Microsoft.Azure.ActiveDirectory.ADSyncManagement.Contract.ADSyncManagementServiceFault]: Out to AAD - User DirectoryExtension - Cloned - 10/14/2019 11:09:04 AM (0b0cfd4f-e8b8-47b2-8a15-b0557cef3949): AttributeFlowMapping's specified target attribute 'extension_87ce92dfb96b4ac689f8f53836bffaa6_extensionAttribute1' is not a defined attribute type.
> 
> Server stack trace: 
>   at System.ServiceModel.Channels.ServiceChannel.HandleReply(ProxyOperationRuntime operation, ProxyRpc& rpc)
>   at System.ServiceModel.Channels.ServiceChannel.Call(String action, Boolean oneway, ProxyOperationRuntime operation, Object[] ins, Object[] outs, TimeSpan timeout)
>   at System.ServiceModel.Channels.ServiceChannelProxy.InvokeService(IMethodCallMessage methodCall, ProxyOperationRuntime operation)
>   at System.ServiceModel.Channels.ServiceChannelProxy.Invoke(IMessage message)

> Exception rethrown at [0]: 
>   at System.Runtime.Remoting.Proxies.RealProxy.HandleReturnMessage(IMessage reqMsg, IMessage retMsg)
>   at System.Runtime.Remoting.Proxies.RealProxy.PrivateInvoke(MessageData& msgData, Int32 type)
>   at Microsoft.Azure.ActiveDirectory.ADSyncManagement.Contract.IADSyncManagementService.SetSynchronizationRule(String syncRuleXml)
>   at Microsoft.IdentityManagement.PowerShell.Cmdlet.AddADSyncRuleCmdlet.ProcessRecord()
>   --- End of inner exception stack trace ---
>   at Microsoft.Online.Deployment.PowerShell.PowerShellAdapter.TypeDependencies.InvokePowerShell(IPowerShell powerShell)
>  at Microsoft.Online.Deployment.PowerShell.PowerShellAdapter.InvokePowerShellCommand(String commandName, InitialSessionState initialSessionState, IDictionary`2 commandParameters, Boolean isScript)
>   at Microsoft.Online.Deployment.PSModule.Tasks.AADSync.ConfigureAADSyncTask`1.ImportSynchronizationRules(String directoryName, Guid connectorIdentifier, List`1 standardSynchronizationRules, List`1 customSynchronizationRules)
>   at Microsoft.Online.Deployment.PSModule.Tasks.AADSync.ConfigureAADSyncTask`1.CreateNewConnectors(TContext context)
>   at Microsoft.Online.Deployment.PSModule.Tasks.AADSync.ConfigureAADSyncTask`1.ConfigureSyncEngine(TContext context)
>   at Microsoft.Online.Deployment.PSModule.Tasks.AADSync.ConfigureAADSyncTask`1.Execute()
>   at Microsoft.Online.Deployment.Framework.Workflow.WorkflowTask.ExecuteWrapper()
>[19:54:05.916] [ 26] [INFO ] MicrosoftOnlinePersistedStateProvider.Save: saving the persisted state file
>[19:54:05.916] [ 26] [INFO ] MicrosoftOnlinePersistedStateProvider.UpdateFileProtection: updating file protection from the persisted state file: C:\ProgramData\AADConnect\PersistedState.xml, isAddProtection: False
>[19:54:05.931] [ 26] [INFO ] MicrosoftOnlinePersistedStateProvider.UpdateFileProtection: updating file protection from the persisted state file: C:\ProgramData\AADConnect\PersistedState.xml, isAddProtection: True
>[19:54:05.932] [ 26] [INFO ] PerformConfigurationPageViewModel.PerformWorkflowInstallationAndUpdateState: result of installation operations - Failed
>[19:54:05.932] [ 26] [ERROR] ExecuteADSyncConfiguration: configuration failed.  Skipping export of synchronization policy.  resultStatus=Failed
>[19:54:05.968] [ 26] [ERROR] PerformConfigurationPageViewModel: We encountered a problem and couldn’t complete the integration.
>[19:54:05.968] [ 26] [ERROR] PerformConfigurationPageViewModel: An error occurred executing Configure AAD Sync task: Out to AAD - User DirectoryExtension - Cloned - 10/14/2019 11:09:04 AM (0b0cfd4f-e8b8-47b2-8a15-b0557cef3949): AttributeFlowMapping's specified target attribute >'extension_87ce92dfb96b4ac689f8f53836bffaa6_extensionAttribute1' is not a defined attribute type.
> Microsoft.IdentityManagement.PowerShell.Cmdlet.AddADSyncRuleCmdlet
>

Microsoft.IdentityManagement.PowerShell.Cmdlet.AddADSyncRuleCmdlet


![](/assets/images/AdConnect/ADC-07.PNG)

**Step 4:** Check that attributes exist in Azure Active Directory

```powershell

Connect-AzureAD

Account                                Environment TenantId                             TenantDomain          AccountTy
                                                                                                              pe
-------                                ----------- --------                             ------------          ---------
admin.xxxxxxxxx@zzzzz.onmicrosoft.com AzureCloud  aaaaaa-bbbb-ffff-ccccc-gggggggggggggg zzzzz.onmicrosoft.com User

```

**Step 5:** Check on existing user that parameters exist in Azure Active Directory

```powershell

$AADUser = Get-AzureADUser -ObjectId UserPrincipalName
$AADUser | Select -ExpandProperty ExtensionProperty

Key                                                             Value
---                                                             -----
odata.metadata                                                  https://graph.windows.net/aaaaaaaaaa-b25b-456f-b283-cf...
odata.type                                                      Microsoft.DirectoryServices.User
createdDateTime                                                 19.10.2020 08:49:15
employeeId
onPremisesDistinguishedName                                     CN=aaaaaa,OU=bbbbbb,OU=bbb,OU=yyyy,OU=zzzz...
userIdentities                                                  []
extension_87ce92dfb96b4ac689f8f53836bffaa6_extensionAttribute1  ttttttttttttttttttttttttttttttttttttt

```

## Error resolving workaround

**Step 1:** Uninstall AD connect, you don't have to remove all components just **Sync Engine**
**Step 2:** On local drive of new AD connect server navigate to folder with exported configuration 