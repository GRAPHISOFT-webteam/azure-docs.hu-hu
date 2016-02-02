<properties
   pageTitle="VM 上使用範本的自訂指令碼 | Microsoft Azure"
   description="使用自訂指令碼擴充功能搭配資源管理員範本，將 Windows 和 Linux Azure VM 組態工作自動化"
   services="virtual-machines"
   documentationCenter=""
   authors="kundanap"
   manager="timlt"
   editor=""
   tags="azure-resource-manager"/>

<tags
   ms.service="virtual-machines"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="vm-multiple"
   ms.workload="infrastructure-services"
   ms.date="11/01/2015"
   ms.author="kundanap"/>


# 使用自訂指令碼擴充功能搭配 Azure 資源管理員範本

這篇文章概要說明如何以自訂指令碼擴充功能撰寫 Azure 資源管理員範本，以啟動 Linux 或 Windows VM 上的工作負載。

如需自訂指令碼的概觀延伸請參閱文章 [這裡](virtual-machines-extensions-customscript.md)。

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-rm-include.md)] [傳統部署模型](virtual-machines-extensions-customscript.md)。

自從自訂指令碼擴充功能可供使用後，已廣泛用來在 Windows 和 Linux VM 上設定工作負載。 隨著 Azure 資源管理員範本的引入，使用者現在可以建立單一的範本，不只可用於佈建 VM，也可在 VM 上設定工作負載。

## 關於 Azure 資源管理員範本

Azure 資源管理員範本可讓您藉由定義資源之間的相依性，以宣告方式指定 JSON 語言中的 Azure IaaS 基礎結構。 如需 Azure 資源管理員範本的詳細概觀，請參閱以下文章：

- [資源群組概觀](../resource-group-overview)
- [以 Azure CLI 部署範本](virtual-machines-deploy-rmtemplates-azure-cli)
- [以 Azure Powershell 部署範本](virtual-machines-deploy-rmtemplates-powershell)

### 必要條件

1. 安裝最新的 Azure PowerShell Cmdlet 或從 Azure CLI [這裡](http://azure.microsoft.com/downloads)。
2. 如果現有的 VM 上執行指令碼，則請確定 VM 代理程式已啟用的 VM 上，如果沒有，請依照 [這](virtual-machines-extensions-install) 進行安裝。
3. 將您想要在 VM 上執行的指令碼上傳到 Azure 儲存體。 指令碼可以來自單一或多個儲存體容器。
4. 或者也能將指令碼上傳至 Github 帳戶。
5. 指令碼應該以由延伸模組依序要啟動的項目指令碼啟動其他指令碼的方式來撰寫。

## 使用自訂指令碼擴充功能

為了部署範本中，我們使用相同版本的 Azure 服務管理 Api 中可用的自訂指令碼延伸模組。 本延伸模組支援將檔案上傳至 Azure 儲存體帳戶或 Github 位置的相同參數和案例。 搭配範本使用時，主要差異在於應該指定正確版本的延伸模組，而不是以 majorversion.* 格式指定版本。

 ## Linux VM 的範本範例

在範本的資源區段中定義下列延伸模組資源

      {
    "type": "Microsoft.Compute/virtualMachines/extensions",
    "name": "MyCustomScriptExtension",
    "apiVersion": "2015-05-01-preview",
    "location": "[parameters('location')]",
    "dependsOn": ["[concat('Microsoft.Compute/virtualMachines/',parameters('vmName'))]"],
    "properties":
    {
      "publisher": "Microsoft.OSTCExtensions",
      "type": "CustomScriptForLinux",
      "typeHandlerVersion": "1.2",
      "settings": {
      "fileUris": [ "https: //raw.githubusercontent.com/Azure/azure-quickstart-templates/master/mongodb-on-ubuntu/mongo-install-ubuntu.sh                        ],
      "commandToExecute": "shmongo-install-ubuntu.sh"
      }
    }
    }

## Windows VM 的範本範例

在範本的資源區段中定義下列資源

       {
       "type": "Microsoft.Compute/virtualMachines/extensions",
       "name": "MyCustomScriptExtension",
       "apiVersion": "2015-05-01-preview",
       "location": "[parameters('location')]",
       "dependsOn": [
           "[concat('Microsoft.Compute/virtualMachines/',parameters('vmName'))]"
       ],
       "properties": {
           "publisher": "Microsoft.Compute",
           "type": "CustomScriptExtension",
           "typeHandlerVersion": "1.4",
           "settings": {
               "fileUris": [
               "http://Yourstorageaccount.blob.core.windows.net/customscriptfiles/start.ps1"
           ],
           "commandToExecute": "powershell.exe -ExecutionPolicy Unrestricted -File start.ps1"
         }
       }
     }

在上述範例中，以您自己的設定取代檔案的 URL 和檔案名稱。

撰寫範本之後，您可以使用 Azure CLI 或 Azure Powershell 部署它們。

如需在 VM 上使用自訂指令碼擴充功能來設定應用程式的完整範例，請參閱以下範例。

<a href="https://github.com/Azure/azure-quickstart-templates/blob/b1908e74259da56a92800cace97350af1f1fc32b/mongodb-on-ubuntu/azuredeploy.json/" target="_blank">Linux VM 上的自訂指令碼延伸模組</a>。
</br>
<a href="https://github.com/Azure/azure-quickstart-templates/blob/b1908e74259da56a92800cace97350af1f1fc32b/201-list-storage-keys-windows-vm/azuredeploy.json/" target="_blank">在 Windows VM 上的自訂指令碼延伸模組</a>。





