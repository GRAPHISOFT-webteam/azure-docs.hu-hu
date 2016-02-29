<properties 
   pageTitle="如何在 ARM 模式使用範本建立 NSG | Microsoft Azure"
   description="了解如何在 ARM 中使用範本建立和部署 NSG"
   services="virtual-network"
   documentationCenter="na"
   authors="telmosampaio"
   manager="carmonm"
   editor="tysonn"
   tags="azure-resource-manager"
/>
<tags 
   ms.service="virtual-network"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="11/20/2015"
   ms.author="telmos" />

# 如何使用範本建立 NSG

[AZURE.INCLUDE [virtual-networks-create-nsg-selectors-arm-include](../../includes/virtual-networks-create-nsg-selectors-arm-include.md)]

[AZURE.INCLUDE [virtual-networks-create-nsg-intro-include](../../includes/virtual-networks-create-nsg-intro-include.md)]

[AZURE.INCLUDE [azure-arm-classic-important-include](../../includes/azure-arm-classic-important-include.md)] 本文涵蓋了資源管理員部署模型。 您也可以 [在傳統部署模型中建立 Nsg](virtual-networks-create-nsg-classic-ps.md)。

[AZURE.INCLUDE [virtual-networks-create-nsg-scenario-include](../../includes/virtual-networks-create-nsg-scenario-include.md)]

## 範本檔案中的 NSG 資源

您可以檢視並下載 [範例範本](https://raw.githubusercontent.com/telmosampaio/azure-templates/master/201-IaaS-WebFrontEnd-SQLBackEnd/)。

下節依據上述案例顯示前端 NSG 的定義。

      "apiVersion": "2015-06-15",
      "type": "Microsoft.Network/networkSecurityGroups",
      "name": "[parameters('frontEndNSGName')]",
      "location": "[resourceGroup().location]",
      "tags": {
        "displayName": "NSG - Front End"
      },
      "properties": {
        "securityRules": [
          {
            "name": "rdp-rule",
            "properties": {
              "description": "Allow RDP",
              "protocol": "Tcp",
              "sourcePortRange": "*",
              "destinationPortRange": "3389",
              "sourceAddressPrefix": "Internet",
              "destinationAddressPrefix": "*",
              "access": "Allow",
              "priority": 100,
              "direction": "Inbound"
            }
          },
          {
            "name": "web-rule",
            "properties": {
              "description": "Allow WEB",
              "protocol": "Tcp",
              "sourcePortRange": "*",
              "destinationPortRange": "80",
              "sourceAddressPrefix": "Internet",
              "destinationAddressPrefix": "*",
              "access": "Allow",
              "priority": 101,
              "direction": "Inbound"
            }
          }
        ]
      }

若要建立 NSG 與前端子網路的關聯，您必須變更範本中的子網路定義，並使用 NSG 的參考識別碼。

        "subnets": [
          {
            "name": "[parameters('frontEndSubnetName')]",
            "properties": {
              "addressPrefix": "[parameters('frontEndSubnetPrefix')]",
              "networkSecurityGroup": {
                "id": "[resourceId('Microsoft.Network/networkSecurityGroups', parameters('frontEndNSGName'))]"
              }
            }
          }, ...

請注意，在範本中已對後端 NSG 和後端子網路完成相同作業。

## 使用按一下即部署來部署 ARM 範本

公用儲存機制中可用的範例範本會使用一個包含預設值的參數檔案，這些預設值可用來產生上述案例。 若要部署使用按一下要部署此範本，請遵循 [此連結](http://github.com/telmosampaio/azure-templates/tree/master/201-IaaS-WebFrontEnd-SQLBackEnd-NSG), ，按一下 [ **部署至 Azure**, 、 取代預設參數值，如有必要，並依照入口網站中的指示。

## 使用 PowerShell 部署 ARM 範本

若要使用 PowerShell 部署您下載的 ARM 範本，請依照下列步驟執行。

[AZURE.INCLUDE [powershell-preview-include.md](../../includes/powershell-preview-include.md)]

1. 如果您從未使用 Azure PowerShell，請參閱 [如何安裝和設定 Azure PowerShell](powershell-install-configure.md) 並遵循指示以登入 Azure，並選取您的訂閱結束。

3. 執行 **新增 AzureRmResourceGroup** 指令程式來建立資源群組範本中使用。

        New-AzureRmResourceGroup -Name TestRG -Location uswest `
            -TemplateFile 'https://raw.githubusercontent.com/telmosampaio/azure-templates/master/201-IaaS-WebFrontEnd-SQLBackEnd/azuredeploy.json' `
            -TemplateParameterFile 'https://raw.githubusercontent.com/telmosampaio/azure-templates/master/201-IaaS-WebFrontEnd-SQLBackEnd/azuredeploy.parameters.json'  

    預期的輸出：

        ResourceGroupName : TestRG
        Location          : westus
        ProvisioningState : Succeeded
        Tags              : 
        Permissions       : 
                            Actions  NotActions
                            =======  ==========
                            *                  
                            
        Resources         : 
                            Name                Type                                     Location
                            ==================  =======================================  ========
                            sqlAvSet            Microsoft.Compute/availabilitySets       westus  
                            webAvSet            Microsoft.Compute/availabilitySets       westus  
                            SQL1                Microsoft.Compute/virtualMachines        westus  
                            SQL2                Microsoft.Compute/virtualMachines        westus  
                            Web1                Microsoft.Compute/virtualMachines        westus  
                            Web2                Microsoft.Compute/virtualMachines        westus  
                            TestNICSQL1         Microsoft.Network/networkInterfaces      westus  
                            TestNICSQL2         Microsoft.Network/networkInterfaces      westus  
                            TestNICWeb1         Microsoft.Network/networkInterfaces      westus  
                            TestNICWeb2         Microsoft.Network/networkInterfaces      westus  
                            NSG-BackEnd         Microsoft.Network/networkSecurityGroups  westus  
                            NSG-FrontEnd        Microsoft.Network/networkSecurityGroups  westus  
                            TestPIPSQL1         Microsoft.Network/publicIPAddresses      westus  
                            TestPIPSQL2         Microsoft.Network/publicIPAddresses      westus  
                            TestPIPWeb1         Microsoft.Network/publicIPAddresses      westus  
                            TestPIPWeb2         Microsoft.Network/publicIPAddresses      westus  
                            TestVNet            Microsoft.Network/virtualNetworks        westus  
                            testvnetstorageprm  Microsoft.Storage/storageAccounts        westus  
                            testvnetstoragestd  Microsoft.Storage/storageAccounts        westus  
                            
        ResourceId        : /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/TestRG

## 使用 Azure CLI 部署 ARM 範本

若要使用 Azure CLI 部署 ARM 範本，請依照下列步驟執行。

1. 如果您從未使用 Azure CLI，請參閱 [安裝和設定 Azure CLI](xplat-cli-install.md) 並遵循指示，選取您的 Azure 帳戶和訂閱為止。
2. 執行 **azure 組態模式** 命令以切換至資源管理員模式，如下所示。

        azure config mode arm

    此為上述命令的預期輸出內容：

        info:    New mode is arm

4. 執行 **azure 群組部署建立** 指令程式來部署新的 VNet 使用的範本和參數檔案下載並修改上面。 輸出後顯示的清單可說明所使用的參數。

        azure group create -n TestRG -l westus -f 'https://raw.githubusercontent.com/telmosampaio/azure-templates/master/201-IaaS-WebFrontEnd-SQLBackEnd/azuredeploy.json' -e 'https://raw.githubusercontent.com/telmosampaio/azure-templates/master/201-IaaS-WebFrontEnd-SQLBackEnd/azuredeploy.parameters.json'

    預期的輸出：

        info:    Executing command group create
        info:    Getting resource group TestRG
        info:    Creating resource group TestRG
        info:    Created resource group TestRG
        info:    Initializing template configurations and parameters
        info:    Creating a deployment
        info:    Created template deployment "azuredeploy"
        data:    Id:                  /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/TestRG
        data:    Name:                TestRG
        data:    Location:            westus
        data:    Provisioning State:  Succeeded
        data:    Tags: null
        data:    
        info:    group create command OK

    - **-n (或--名稱)**。 要建立之資源群組的名稱。
    - **-l (或--location)**。 將會建立將在其中建立資源群組的 Azure 區域。
    - **-f (或--範本檔案)**。 ARM 範本檔案的路徑。
    - **-e (或--參數檔案)**。 ARM 參數檔案的路徑。


