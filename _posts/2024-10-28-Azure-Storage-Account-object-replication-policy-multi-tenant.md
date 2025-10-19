---
title: "Azure Storage Account how to setup policy for object replication blobs between multi tenant for more then 10 blobs."
classes: wide
date: 2024-10-28
excerpt: "Setup object replication policy for 10 to 1000 blobs between two storage account in different tenants."
toc: true
header:
  teaser: /assets/images/teaser-main-v1.png
  og_image: /assets/images/teaser-main-v1.png
categories:
 - Azure
tags:
  - PowerShell
  - Automation
  - Azure
  - CrossTenant
published: true
hidden: false
---


## Prerequisites

* Storage Account in different tenants.
* Az PowerShell module.

## Configuration 

Configuration to setup in each storage account, we need to allow object replication between diffrent tenants.

  1. Navigate to **Storage Account**
  2. In navigation go to **Object replication**
  3. In context menu go to **Advanced Settings**
  4. Allow cross tenant object access  
    
     ![](/assets/images/Azure/ST-Sync-Multiple-Object/st-sync-00.png)  

     ![](/assets/images/Azure/ST-Sync-Multiple-Object/st-sync-0.png)  




### Confiuration destination storage account

1. Prepare policy file in destination storage account 
  * **ruleId** - for each blob need to unique guid
  * **minCreationTime** - For sync all content we need to setup date / time before day of creation
  * **sourceAccount** - object ID of source storage account
  * **destinationAccount**  - object ID of destinationAccount storage account

```json
{
  "properties": {
    "sourceAccount": "/subscriptions/xxxxxxxxxxxxxxxxxxxxxx/resourceGroups/RG_name_source/providers/Microsoft.Storage/storageAccounts/storageaccount_name_source",
    "destinationAccount": "/subscriptions/xxxxxxxxxxxxxxxxxxxxxx/resourceGroups/RG_name_destination/providers/Microsoft.Storage/storageAccounts/storageaccount_name_destination",
    "rules": [
      {
        "ruleId": "1f45eace-d5fb-4e8e-9e1c-cf8219615644", // Rule ID need to be guid random generated
        "sourceContainer": "dms5",
        "destinationContainer": "dms5",
        "filters": {
          "minCreationTime": "1601-01-01T00:00:00Z" // For sync all content we need to setup date / time before day of creation
        }
      },
      {
        "ruleId": "0fa15940-e42e-4a25-959e-88f8132eaf9b",
        "sourceContainer": "h0472",
        "destinationContainer": "h0472",
        "filters": {
          "minCreationTime": "1601-01-01T00:00:00Z"
        }
      },
      {
        "ruleId": "3bfa51f9-a4fb-4acf-a59a-8c5af8e4d61b",
        "sourceContainer": "h0473",
        "destinationContainer": "h0473",
        "filters": {
          "minCreationTime": "1601-01-01T00:00:00Z"
        }
      },
      {
        "ruleId": "d5be2cea-7459-4dbb-865a-11c4f53ae3b1",
        "sourceContainer": "h0781",
        "destinationContainer": "h0781",
        "filters": {
          "minCreationTime": "1601-01-01T00:00:00Z"
        }
      },
      {
        "ruleId": "5822c0c4-8b66-425f-8120-1f7d678737d7",
        "sourceContainer": "h0984",
        "destinationContainer": "h0984",
        "filters": {
          "minCreationTime": "1601-01-01T00:00:00Z"
        }
      },
      {
        "ruleId": "ec69dd0d-44d7-446e-b447-e5311e232241",
        "sourceContainer": "h1142",
        "destinationContainer": "h1142",
        "filters": {
          "minCreationTime": "1601-01-01T00:00:00Z"
        }
      },
      {
        "ruleId": "6f5fad1b-39ea-4d42-ad86-6c73719a4b66",
        "sourceContainer": "h1871",
        "destinationContainer": "h1871",
        "filters": {
          "minCreationTime": "1601-01-01T00:00:00Z"
        }
      },
      {
        "ruleId": "6f0a0272-24de-42cc-bccc-359edeafe71f",
        "sourceContainer": "h1988",
        "destinationContainer": "h1988",
        "filters": {
          "minCreationTime": "1601-01-01T00:00:00Z"
        }
      },
      {
        "ruleId": "848fd02e-1ab9-4e82-8f19-569e05abdba5",
        "sourceContainer": "h2161",
        "destinationContainer": "h2161",
        "filters": {
          "minCreationTime": "1601-01-01T00:00:00Z"
        }
      },
      {
        "ruleId": "db855105-a5d3-4202-9874-49386d94ba69",
        "sourceContainer": "h2907",
        "destinationContainer": "h2907",
        "filters": {
          "minCreationTime": "1601-01-01T00:00:00Z"
        }
      },
      {
        "ruleId": "6df9d571-93a5-4e6c-bb55-e9040b29e88c",
        "sourceContainer": "h3054",
        "destinationContainer": "h3054",
        "filters": {
          "minCreationTime": "1601-01-01T00:00:00Z"
        }
      },
      {
        "ruleId": "85235bc7-8852-4631-b19b-e8b30119966e",
        "sourceContainer": "h3118",
        "destinationContainer": "h3118",
        "filters": {
          "minCreationTime": "1601-01-01T00:00:00Z"
        }
      },
      {
        "ruleId": "6bb2c27c-c649-4d60-b603-20280871ccca",
        "sourceContainer": "h3254",
        "destinationContainer": "h3254",
        "filters": {
          "minCreationTime": "1601-01-01T00:00:00Z"
        }
      },
      {
        "ruleId": "615a2c0d-a803-491b-b1df-2d74be197ea0",
        "sourceContainer": "h3263",
        "destinationContainer": "h3263",
        "filters": {
          "minCreationTime": "1601-01-01T00:00:00Z"
        }
      },
      {
        "ruleId": "dfd63c9d-60e2-4806-8b55-f01cc308677c",
        "sourceContainer": "h3264",
        "destinationContainer": "h3264",
        "filters": {
          "minCreationTime": "1601-01-01T00:00:00Z"
        }
      },
      {
        "ruleId": "77701a25-2dca-4fd3-8b4f-590f12324471",
        "sourceContainer": "h3265",
        "destinationContainer": "h3265",
        "filters": {
          "minCreationTime": "1601-01-01T00:00:00Z"
        }
      }
    ]
  }
}

```

2.  Logon to https://portal.azure.com
3.  Navigate to **Storage Account**
4.  In navigation go to **Object replication** and **Upload Rules**
    ![](/assets/images/Azure/ST-Sync-Multiple-Object/st-sync-1.png)  
5. Check that rules include all object and destination and source is correct.
    ![](/assets/images/Azure/ST-Sync-Multiple-Object/st-sync-3.png)  


### Confiuration source storage account object replication policy

1. Get object replication rule ID on destination storage account.

```powershell

Get-AzStorageObjectReplicationPolicy -ResourceGroupName "RG_name_destination" -StorageAccountName "storageaccount_name_destination""

```
   ![](../assets/images/Azure/ST-Sync-Multiple-Object/st-sync-4.png)  

2. Prepare policy file in destination storage account 
  * **ruleId** - for each blob need to unique guid
  * **minCreationTime** - For sync all content we need to setup date / time before day of creation
  * **sourceAccount** - object ID of source storage account
  * **destinationAccount**  - object ID of destinationAccount storage account
  * **policyId** - policy ID of uploaded replication rules in destination storage account.

```json
{
  "properties": {
    "policyId": "a50abfe6-68df-xxxx-xxxx-013b9f2a5050",
    "sourceAccount": "/subscriptions/xxxxxxxxxxxxxxxxxxxxxx/resourceGroups/RG_name_source/providers/Microsoft.Storage/storageAccounts/storageaccount_name_source",
    "destinationAccount": "/subscriptions/xxxxxxxxxxxxxxxxxxxxxx/resourceGroups/RG_name_destination/providers/Microsoft.Storage/storageAccounts/pstorageaccount_name_destination",
    "rules": [
      {
        "ruleId": "1f45eace-d5fb-4e8e-9e1c-cf8219615644", // Rule ID need to be guid random generated
        "sourceContainer": "dms5",
        "destinationContainer": "dms5",
        "filters": {
          "minCreationTime": "1601-01-01T00:00:00Z" // For sync all content we need to setup date / time before day of creation
        }
      },
      {
        "ruleId": "0fa15940-e42e-4a25-959e-88f8132eaf9b",
        "sourceContainer": "h0472",
        "destinationContainer": "h0472",
        "filters": {
          "minCreationTime": "1601-01-01T00:00:00Z"
        }
      },
      {
        "ruleId": "3bfa51f9-a4fb-4acf-a59a-8c5af8e4d61b",
        "sourceContainer": "h0473",
        "destinationContainer": "h0473",
        "filters": {
          "minCreationTime": "1601-01-01T00:00:00Z"
        }
      },
      {
        "ruleId": "d5be2cea-7459-4dbb-865a-11c4f53ae3b1",
        "sourceContainer": "h0781",
        "destinationContainer": "h0781",
        "filters": {
          "minCreationTime": "1601-01-01T00:00:00Z"
        }
      },
      {
        "ruleId": "5822c0c4-8b66-425f-8120-1f7d678737d7",
        "sourceContainer": "h0984",
        "destinationContainer": "h0984",
        "filters": {
          "minCreationTime": "1601-01-01T00:00:00Z"
        }
      },
      {
        "ruleId": "ec69dd0d-44d7-446e-b447-e5311e232241",
        "sourceContainer": "h1142",
        "destinationContainer": "h1142",
        "filters": {
          "minCreationTime": "1601-01-01T00:00:00Z"
        }
      },
      {
        "ruleId": "6f5fad1b-39ea-4d42-ad86-6c73719a4b66",
        "sourceContainer": "h1871",
        "destinationContainer": "h1871",
        "filters": {
          "minCreationTime": "1601-01-01T00:00:00Z"
        }
      },
      {
        "ruleId": "6f0a0272-24de-42cc-bccc-359edeafe71f",
        "sourceContainer": "h1988",
        "destinationContainer": "h1988",
        "filters": {
          "minCreationTime": "1601-01-01T00:00:00Z"
        }
      },
      {
        "ruleId": "848fd02e-1ab9-4e82-8f19-569e05abdba5",
        "sourceContainer": "h2161",
        "destinationContainer": "h2161",
        "filters": {
          "minCreationTime": "1601-01-01T00:00:00Z"
        }
      },
      {
        "ruleId": "db855105-a5d3-4202-9874-49386d94ba69",
        "sourceContainer": "h2907",
        "destinationContainer": "h2907",
        "filters": {
          "minCreationTime": "1601-01-01T00:00:00Z"
        }
      },
      {
        "ruleId": "6df9d571-93a5-4e6c-bb55-e9040b29e88c",
        "sourceContainer": "h3054",
        "destinationContainer": "h3054",
        "filters": {
          "minCreationTime": "1601-01-01T00:00:00Z"
        }
      },
      {
        "ruleId": "85235bc7-8852-4631-b19b-e8b30119966e",
        "sourceContainer": "h3118",
        "destinationContainer": "h3118",
        "filters": {
          "minCreationTime": "1601-01-01T00:00:00Z"
        }
      },
      {
        "ruleId": "6bb2c27c-c649-4d60-b603-20280871ccca",
        "sourceContainer": "h3254",
        "destinationContainer": "h3254",
        "filters": {
          "minCreationTime": "1601-01-01T00:00:00Z"
        }
      },
      {
        "ruleId": "615a2c0d-a803-491b-b1df-2d74be197ea0",
        "sourceContainer": "h3263",
        "destinationContainer": "h3263",
        "filters": {
          "minCreationTime": "1601-01-01T00:00:00Z"
        }
      },
      {
        "ruleId": "dfd63c9d-60e2-4806-8b55-f01cc308677c",
        "sourceContainer": "h3264",
        "destinationContainer": "h3264",
        "filters": {
          "minCreationTime": "1601-01-01T00:00:00Z"
        }
      },
      {
        "ruleId": "77701a25-2dca-4fd3-8b4f-590f12324471",
        "sourceContainer": "h3265",
        "destinationContainer": "h3265",
        "filters": {
          "minCreationTime": "1601-01-01T00:00:00Z"
        }
      }
    ]
  }
}
```
4.  In navigation go to **Object replication** and **Upload Rules**
    ![](/assets/images/Azure/ST-Sync-Multiple-Object/st-sync-1.png)  
5. Check that rules include all object and destination and source is correct.
    ![](/assets/images/Azure/ST-Sync-Multiple-Object/st-sync-7.png) 

