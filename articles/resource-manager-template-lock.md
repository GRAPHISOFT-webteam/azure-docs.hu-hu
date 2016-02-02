<properties
   pageTitle="資源鎖定的資源管理員範本 | Microsoft Azure"
   description="說明資源鎖定的資源管理員結構描述。"
   services="azure-resource-manager"
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
   ms.date="10/25/2015"
   ms.author="tomfitz"/>


# 資源鎖定 - 範本結構描述

在資源及其子資源上建立新的鎖定。

## 結構描述格式

若要建立鎖定，請將下列結構描述新增到範本的資源區段。

    {
        "type": enum,
        "apiVersion": "2015-01-01",
        "name": string,
        "dependsOn": [ array values ],
        "properties":
        {
            "level": enum,
            "notes": string
        }
    }

## 值

下表描述您在結構描述中必須設定的值。

| 名稱| 類型| 必要| 允許的值| 說明|
| ---- | ---- | -------- | ---------------- | ----------- |
| 類型| 列舉| 是| | 要建立的資源類型。|
| apiVersion| 列舉| 是| **2015-01-01**| 要用來建立資源的應用程式開發介面 (API) 版本。|
| 名稱| 字串| 是| | |
| dependsOn| array| 否| 以逗號分隔的資源名稱或資源唯一識別碼清單。| |
| properties| 物件| 是| (如下所示)| |

### 屬性物件

| 名稱| 類型| 必要| 允許的值| 說明|
| ------- | ---- | ---------------- | -------- | ----------- |
| 層級| 列舉| 是| | 要套用至範圍的鎖定類型。CanNotDelete 允許修改但防止刪除，ReadOnly 防止修改或刪除。|
| 版本| 字串| 否| 512 個字元| 鎖定的描述。|


## 如何使用鎖定資源

您可以將此資源新增至範本，以避免在資源上進行指定的動作。 鎖定會套用到所有的使用者和群組。 一般而言，您只能套用鎖定一段有限的時間，例如當處理序正在執行時，或者您想要確定組織中的某個人不會不小心修改或刪除資源。

若要建立或刪除管理鎖定，您必須擁有 **Microsoft.Authorization/*** 或 **Microsoft.Authorization/locks/*** 動作的存取權。 


鎖定會套用至指定的資源和任何子資源。 如果您將多個鎖定套用至資源，最嚴格的鎖定優先順序較高。 




## 範例

下列範例會將唯讀鎖定套用到 Web 應用程式。

    {
        "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
        "contentVersion": "1.0.0.0",
        "parameters": {
            "hostingPlanName": {
                "type": "string"
            }
        },
        "variables": {
            "siteName": "[concat('site',uniqueString(resourceGroup().id))]"
        },
        "resources": [
            {
                "apiVersion": "2015-08-01",
                "name": "[variables('siteName')]",
                "type": "Microsoft.Web/sites",
                "location": "[resourceGroup().location]",
                "properties": {
                    "serverFarmId": "[parameters('hostingPlanName')]"
                },
            },
            {
                "type": "Microsoft.Web/sites/providers/locks",
                "apiVersion": "2015-01-01",
                "name": "[concat(variables('siteName'),'/Microsoft.Authorization/MySiteLock')]",
                "dependsOn": [ "[variables('siteName')]" ],
                "properties":
                {
                    "level": "ReadOnly",
                    "notes": "my notes"
                }
             }
        ],
        "outputs": {}
    }

接下來的範例會將唯讀鎖定套用到資源群組。

    {
        "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
        "contentVersion": "1.0.0.0",
        "parameters": {},
        "variables": {},
        "resources": [
            {
                "type": "Microsoft.Authorization/locks",
                "apiVersion": "2015-01-01",
                "name": "MyGroupLock",
                "properties":
                {
                    "level": "ReadOnly",
                    "notes": "my notes"
                }
            }
        ],
        "outputs": {}
    }

## 後續步驟

- 
- 





