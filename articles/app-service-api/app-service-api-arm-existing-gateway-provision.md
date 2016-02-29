<properties 
    pageTitle="以現有閘道部署 API 應用程式" 
    description="使用 Azure 資源管理員範本，部署使用現有閘道和應用程式服務方案的 API 應用程式。" 
    services="app-service" 
    documentationCenter="" 
    authors="tfitzmac" 
    manager="wpickett" 
    editor=""/>

<tags 
    ms.service="app-service" 
    ms.workload="web" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="11/03/2015" 
    ms.author="tomfitz"/>

# 以現有閘道佈建 API 應用程式

在本主題中，您將學習如何建立 Azure 資源管理員範本，以部署 Azure API 應用程式和現有的閘道。 您將學習如何定義要部署哪些資源， 
如何定義執行部署時所指定的參數。 您可以直接在自己的部署中使用此範本，或自訂此範本以符合您的需求。

如需建立範本的詳細資訊，請參閱 [編寫 Azure 資源管理員範本](../resource-group-authoring-templates.md)。

如需部署應用程式的詳細資訊，請參閱 [部署複雜應用程式如預期般在 Azure 中的](../app-service-web/app-service-deploy-complex-application-predictably.md)。

如需完整的範本，請參閱 [API 應用程式與現有閘道的範本](https://github.com/Azure/azure-quickstart-templates/blob/master/201-api-app-gateway-existing/azuredeploy.json)。

## 部署內容

在此範本中，您將部署與現有應用程式服務主控方案和現有閘道相關聯的 API 應用程式。

若要自動執行部署，請按一下下列按鈕：

[![Deploy 到 Azure](http://azuredeploy.net/deploybutton.png)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F201-api-app-gateway-existing%2Fazuredeploy.json)

## 參數

[AZURE.INCLUDE [app-service-api-deploy-parameters](../../includes/app-service-api-deploy-parameters.md)]

### hostingPlanId

現有 App Service 主控方案的識別碼。

    "hostingPlanId": {
      "type": "string"
    }

### hostingPlanSettings

現有主控方案的設定。

    "hostingPlanSettings": {
      "type": "Object",
      "defaultValue": {
        "hostingEnvironment": ""
      }
    }

## 變數

此範本會定義部署資源時使用的變數。

    "variables": {
      "packageId": "Microsoft.ApiApp"
    }
    
下方使用的值為 **variables('packageId')**。 它包含 API 應用程式的 NuGet 套件 ID。

## 要部署的資源

### 主控 API 應用程式的 Web 應用程式

建立主控 API 應用程式的 Web 應用程式。 

請注意， **種類** 設為 **apiApp** 這麼做會通知 Azure 入口網站此 web 應用程式裝載 API 應用程式。 入口網站會在瀏覽 Web 應用程式刀鋒視窗中隱藏此 Web 應用程式。 應用程式包含的延伸模組 
若要安裝預設空白 API 應用程式封裝。 API 應用程式與主控 Web 應用程式之間會定義一個連結。 應用程式設定區段中會包含主控 API 應用程式的必要值。  **ServerFarmId** 屬性設定為您提供的值 **hostingPlanId** 參數。

    {
      "type": "Microsoft.Web/sites",
      "apiVersion": "2015-04-01",
      "name": "[parameters('apiAppName')]",
      "location": "[parameters('location')]",
      "kind": "apiApp",
      "resources": [
        {
          "type": "siteextensions",
          "apiVersion": "2015-02-01",
          "name": "[variables('packageId')]",
          "dependsOn": [
            "[resourceId('Microsoft.Web/sites', parameters('apiAppName'))]"
          ],
          "properties": {
            "type": "WebRoot",
            "feed_url": "http://apiapps-preview.nuget.org/api/v2/",
            "version": "0.9.4"
          }
        },
        {
          "type": "providers/links",
          "apiVersion": "2015-01-01",
          "name": "Microsoft.Resources/apiApp",
          "dependsOn": [
            "[resourceId('Microsoft.Web/sites', parameters('apiAppName'))]"
          ],
          "properties": {
            "targetId": "[resourceId('Microsoft.AppService/apiapps', parameters('apiAppName'))]"
          }
        }
      ],
      "properties": {
        "name": "[parameters('apiAppName')]",
        "gatewaySiteName": "[parameters('gatewayName')]",
        "serverFarmId": "[parameters('hostingPlanId')]",
        "hostingEnvironment": "[parameters('hostingPlanSettings').hostingEnvironment]",
        "siteConfig": {
          "appSettings": [
            {
              "name": "EMA_MicroserviceId",
              "value": "[parameters('apiAppName')]"
            },
            {
              "name": "EMA_Secret",
              "value": "[parameters('apiAppSecret')]"
            },
            {
              "name": "EMA_RuntimeUrl",
              "value": "[concat('https://', parameters('gatewayName'), '.azurewebsites.net')]"
            },
            {
              "name": "WEBSITE_START_SCM_ON_SITE_CREATION",
              "value": "1"
            }
          ]
        }
      }
    }

### API 應用程式

建立 API 應用程式。

請注意，主控 Web 應用程式和閘道的名稱會定義為 API 應用程式中的屬性。 

    {
      "type": "Microsoft.AppService/apiapps",
      "apiVersion": "2015-03-01-preview",
      "name": "[parameters('apiAppName')]",
      "location": "[parameters('location')]",
      "resources": [
        {
          "type": "providers/links",
          "apiVersion": "2015-01-01",
          "name": "Microsoft.Resources/apiAppSite",
          "dependsOn": [
            "[resourceId('Microsoft.AppService/apiapps', parameters('apiAppName'))]"
          ],
          "properties": {
            "targetId": "[resourceId('Microsoft.Web/sites', parameters('apiAppName'))]"
          }
        }
      ],
      "dependsOn": [
        "[resourceId('Microsoft.Web/sites/siteextensions', parameters('apiAppName'), variables('packageId'))]"
      ],
      "properties": {
        "package": {
          "id": "[variables('packageId')]"
        },
        "host": {
          "resourceName": "[parameters('apiAppName')]"
        },
        "gateway": {
          "resourceName": "[parameters('gatewayName')]"
        }
      }
    }


## 執行部署的命令

[AZURE.INCLUDE [app-service-deploy-commands](../../includes/app-service-deploy-commands.md)]

### PowerShell

    New-AzureResourceGroupDeployment -TemplateUri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/201-api-app-gateway-existing/azuredeploy.json

### Azure CLI

    azure group deployment create --template-uri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/201-api-app-gateway-existing/azuredeploy.json


 

