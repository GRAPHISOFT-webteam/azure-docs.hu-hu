<properties
   pageTitle="金鑰保存庫的資源管理員範本 | Microsoft Azure"
   description="顯示金鑰保存庫的資源管理員結構描述。"
   services="azure-resource-manager,key-vault"
   documentationCenter="na"
   authors="tfitzmac"
   manager="wpickett"
   editor=""/>

<tags
   ms.service="azure-resource-manager"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="12/15/2015"
   ms.author="tomfitz"/>


# 金鑰保存庫範本結構描述

建立金鑰保存庫。

## 結構描述格式

若要建立金鑰保存庫，請將下列結構描述新增到範本的資源區段。

    {
        "type": "Microsoft.KeyVault/vaults",
        "apiVersion": "2015-06-01",
        "name": string,
        "location": string,
        "properties": {
            "enabledForDeployment": bool,
            "enabledForTemplateDeployment": bool,
            "enabledForVolumeEncryption": bool,
            "tenantId": string,
            "accessPolicies": [
                {
                    "tenantId": string,
                    "objectId": string,
                    "permissions": {
                        "keys": [ keys permissions ],
                        "secrets": [ secrets permissions ]
                    }
                }
            ],
            "sku": {
                "name": enum,
                "family": "A"
            }
        },
        "resources": [
             child resources
        ]
    }

## 值

下表描述您在結構描述中必須設定的值。

| 名稱| 類型| 必要| 允許的值| 說明|
| ---- | ---- | -------- | ---------------- | ----------- |
| 類型| 列舉| 是| **Microsoft.KeyVault/vaults**| 要建立的資源類型。|
| apiVersion| 列舉| 是| | 要用來建立資源的應用程式開發介面 (API) 版本。|
| 名稱| 字串| 是| | 要建立的金鑰保存庫名稱。此名稱在整個 Azure 中必須是唯一的。|
| location| 字串| 是| | 要裝載金鑰保存庫的區域。|
| properties| 物件| 是| | 指定要建立之金鑰保存庫類型的物件。|
| 資源| array| 否| | 金鑰保存庫的子資源。|

<a id="properties" />
### 屬性物件

| 名稱| 類型| 必要| 允許的值| 說明|
| ---- | ---- | -------- | ---------------- | ----------- |
| enabledForDeployment| 布林值| 否| **true** 或 **false**| 指定是否要針對虛擬機器或 Service Fabric 部署啟用保存庫。|
| enabledForTemplateDeployment| 布林值| 否| **true** 或 **false**| 指定是否要啟用保存庫，以便在資源管理員範本部署中使用。|
| enabledForVolumeEncryption| 布林值| 否| **true** 或 **false**| 指定是否要啟用保存庫來進行磁碟區加密。|
| tenantId| 字串| 是| 全域唯一識別碼| 訂用帳戶的租用戶識別碼。您可以使用 **Get-AzureRMSubscription** PowerShell Cmdlet 來擷取此識別碼。|
| accessPolicies| array| 是| | 最多 16 個物件的陣列，可指定使用者或服務主體的權限。|
| sku| 物件| 是| | 金鑰保存庫的 SKU。|

<a id="accesspolicies" />
### properties.accessPolicies 物件

| 名稱| 類型| 必要| 允許的值| 說明|
| ---- | ---- | -------- | ---------------- | ----------- |
| tenantId| 字串| 是| 全域唯一識別碼| 此存取原則中包含 **objectId** 之 Azure Active Directory 租用戶的租用戶識別碼|
| objectId| 字串| 是| 全域唯一識別碼| AAD 使用者或服務主體的物件識別碼，其將具備保存庫的存取權。您可以從 **Get-AzureRMADUser** 或 **Get-AzureRMADServicePrincipal** Cmdlet 擷取此值。|
| 權限| 物件| 是| | 為 Active Directory 物件授與此保存庫相關的權限。|

<a id="permissions" />
### properties.accessPolicies.permissions 物件

| 名稱| 類型| 必要| 允許的值| 說明|
| ---- | ---- | -------- | ---------------- | ----------- |
| 金鑰| array| 是| | 為此 Active Directory 物件授與此保存庫中金鑰相關的權限。此值必須指定為允許值的陣列。|
| 密碼| array| 是| | 為此 Active Directory 物件授與此保存庫中密碼相關的權限。此值必須指定為允許值的陣列。|

<a id="sku" />
### properties.sku 物件

| 名稱| 類型| 必要| 允許的值| 說明|
| ---- | ---- | -------- | ---------------- | ----------- |
| 名稱| 列舉| 是| | 要使用的 KeyVault 服務層。Standard 支援密碼和軟體保護的金鑰Premium 會新增對於 HSM 保護之金鑰的支援。|
| family| 列舉| 是| **A**| 要使用的 sku 系列。


## 範例

下列範例會部署金鑰保存庫和密碼。

    {
        "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
        "contentVersion": "1.0.0.0",
        "parameters": {
            "keyVaultName": {
                "type": "string",
                "metadata": {
                    "description": "Name of the vault"
                }
            },
            "tenantId": {
                "type": "string",
                "metadata": {
                   "description": "Tenant Id for the subscription and use assigned access to the vault. Available from the Get-AzureRMSubscription PowerShell cmdlet"
                }
            },
            "objectId": {
                "type": "string",
                "metadata": {
                    "description": "Object Id of the AAD user or service principal that will have access to the vault. Available from the Get-AzureRMADUser or the Get-AzureRMADServicePrincipal cmdlets"
                }
            },
            "keysPermissions": {
                "type": "array",
                "defaultValue": [ "all" ],
                "metadata": {
                    "description": "Permissions to grant user to keys in the vault. Valid values are: all, create, import, update, get, list, delete, backup, restore, encrypt, decrypt, wrapkey, unwrapkey, sign, and verify."
                }
            },
            "secretsPermissions": {
                "type": "array",
                "defaultValue": [ "all" ],
                "metadata": {
                    "description": "Permissions to grant user to secrets in the vault. Valid values are: all, get, set, list, and delete."
                }
            },
            "vaultSku": {
                "type": "string",
                "defaultValue": "Standard",
                "allowedValues": [
                    "Standard",
                    "Premium"
                ],
                "metadata": {
                    "description": "SKU for the vault"
                }
            },
            "enabledForDeployment": {
                "type": "bool",
                "defaultValue": false,
                "metadata": {
                    "description": "Specifies if the vault is enabled for VM or Service Fabric deployment"
                }
            },
            "enabledForTemplateDeployment": {
                "type": "bool",
                "defaultValue": false,
                "metadata": {
                    "description": "Specifies if the vault is enabled for ARM template deployment"
                }
            },
            "enableVaultForVolumeEncryption": {
                "type": "bool",
                "defaultValue": false,
                "metadata": {
                    "description": "Specifies if the vault is enabled for volume encryption"
                }
            },
            "secretName": {
                "type": "string",
                "metadata": {
                    "description": "Name of the secret to store in the vault"
                }
            },
            "secretValue": {
                "type": "securestring",
                "metadata": {
                    "description": "Value of the secret to store in the vault"
                }
            }
        },
        "resources": [
        {
            "type": "Microsoft.KeyVault/vaults",
            "name": "[parameters('keyVaultName')]",
            "apiVersion": "2015-06-01",
            "location": "[resourceGroup().location]",
            "tags": {
                "displayName": "KeyVault"
            },
            "properties": {
                "enabledForDeployment": "[parameters('enabledForDeployment')]",
                "enabledForTemplateDeployment": "[parameters('enabledForTemplateDeployment')]",
                "enabledForVolumeEncryption": "[parameters('enableVaultForVolumeEncryption')]",
                "tenantId": "[parameters('tenantId')]",
                "accessPolicies": [
                {
                    "tenantId": "[parameters('tenantId')]",
                    "objectId": "[parameters('objectId')]",
                    "permissions": {
                        "keys": "[parameters('keysPermissions')]",
                        "secrets": "[parameters('secretsPermissions')]"
                    }
                }],
                "sku": {
                    "name": "[parameters('vaultSku')]",
                    "family": "A"
                }
            },
            "resources": [
            {
                "type": "secrets",
                "name": "[parameters('secretName')]",
                "apiVersion": "2015-06-01",
                "tags": { "displayName": "secret" },
                "properties": {
                    "value": "[parameters('secretValue')]"
                },
                "dependsOn": [
                    "[concat('Microsoft.KeyVault/vaults/', parameters('keyVaultName'))]"
                ]
            }]
        }]
    }

## 後續步驟

- 
- 






