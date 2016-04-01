<properties 
    pageTitle="將資源移動到新的資源群組" 
    description="使用 Azure PowerShell 或 REST API 將資源移動到至新的「Azure 資源管理員」資源群組。" 
    services="azure-resource-manager" 
    documentationCenter="" 
    authors="tfitzmac" 
    manager="wpickett" 
    editor=""/>

<tags 
    ms.service="azure-resource-manager" 
    ms.workload="multiple" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="11/19/2015" 
    ms.author="tomfitz"/>

# 將資源移動到新的資源群組或訂用帳戶

此主題說明如何將資源從某個資源群組移動到另一個資源群組。 您也可以將資源移動到新的訂用帳戶。 在下列情況中，您可能需要移動資源：

1. 因計費之目的，資源必須位於不同的訂用帳戶。
2. 資源不會再與先前的相同群組資源共用相同的生命週期。 您想要將它移動到新的資源群組，以便您可以將該資源與其他資源分開管理。
3. 資源現在共用與不同資源群組中之其他資源相同的生命週期。 您想要將它移動到其他資源所屬的資源群組，以便一起管理。

移動資源時有一些重要的考量：

1. 您無法變更資源的位置。 移動資源只會將它移動到新的資源群組。 新的資源群組可能會有不同的位置，但完成的 
變更資源的位置。
2. 您要將資源移動到其中的目的地資源群組，只應包含與該資源共用相同應用程式生命週期的資源。
3. 若使用 Azure PowerShell，請確定您使用最新版本。  **移動 AzureRmResource** 命令經常更新。 若要更新您的版本，執行 Microsoft Web Platform Installer 並檢查 
使用新版本。 如需詳細資訊，請參閱 [如何安裝和設定 Azure PowerShell](powershell-install-configure.md)。
4. 移動作業可能需要一段時間來完成，且作業期間您的 PowerShell 提示字元會等候直到作業完成。
5. 當移動資源時，會在作業期間鎖定來源群組和目標群組。 群組上的寫入和刪除作業將會封鎖，直到移動完成。

## 支援的服務

目前並非所有服務都支援移動資源的功能。

目前支援移動到新資源群組與訂用帳戶的服務有：

- API 管理
- 自動化
- 批次
- Data Factory
- DocumentDB
- HDInsight 叢集
- 金鑰保存庫
- Logic Apps
- Mobile Engagement
- 通知中樞
- Operational Insights
- Redis 快取
- 搜尋
- SQL Database
- Web 應用程式 (某些 [限制](app-service-web/app-service-move-resources.md) 套用)

支援移動到新資源群組，但不支援移動到新訂用帳戶的服務有：

- 虛擬機器 (傳統)
- 儲存體 (傳統)

目前不支援移動資源的服務有：

- 虛擬機器
- 虛擬網路
- 儲存體

使用 Web 應用程式時，您無法只移動 App Service 方案。 若要移動 Web 應用程式，您的選項如下：

- 如果目的地資源群組還沒有 Microsoft.Web 資源，將所有的資源從某個資源群組移動到不同的資源群組。
- 將 Web 應用程式移動到不同的資源群組，但在原始的資源群組中保留 App Service 方案。

## 使用 PowerShell 移動資源

[AZURE.INCLUDE [powershell-preview-inline-include](../includes/powershell-preview-inline-include.md)]

若要將現有的資源移動到另一個資源群組或訂用帳戶，使用 **移動 AzureRmResource** 命令。

第一個範例顯示如何將某個資源移動到新的資源群組。

    PS C:\> $resource = Get-AzureRmResource -ResourceName ExampleApp -ResourceGroupName OldRG
    PS C:\> Move-AzureRmResource -DestinationResourceGroupName NewRG -ResourceId $resource.ResourceId

第二個範例顯示如何將多個資源移動到新的資源群組。

    PS C:\> $webapp = Get-AzureRmResource -ResourceGroupName OldRG -ResourceName ExampleSite
    PS C:\> $plan = Get-AzureRmResource -ResourceGroupName OldRG -ResourceName ExamplePlan
    PS C:\> Move-AzureRmResource -DestinationResourceGroupName NewRG -ResourceId ($webapp.ResourceId, $plan.ResourceId)

若要移動到新的訂用帳戶，包含的值 **DestinationSubscriptionId** 參數。

## 使用 REST API 移動資源

若要將現有的資源移動到另一個資源群組或訂用帳戶，請執行：

    POST https://management.azure.com/subscriptions/{source-subscription-id}/resourcegroups/{source-resource-group-name}/moveResources?api-version={api-version} 

在要求主體中，您可以指定目標資源群組以及要移動的資源。 如需移動 REST 作業的詳細資訊，請參閱 [移動資源](https://msdn.microsoft.com/library/azure/mt218710.aspx)。

## 後續步驟
- [將 Azure PowerShell 與資源管理員搭配使用](./powershell-azure-resource-manager.md)
- [Azure CLI 搭配資源管理員使用](./virtual-machines/xplat-cli-azure-resource-manager.md)
- [使用 Azure 入口網站管理資源](azure-portal/resource-group-portal.md)
- [使用標記組織您的資源](./resource-group-using-tags.md)


