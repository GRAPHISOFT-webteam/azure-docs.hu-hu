<properties
    pageTitle="Azure PowerShell 1.0 Preview 資源管理員變更 |Microsoft Azure"
    description="說明在資源管理員 Cmdlet 中針對 Azure PowerShell 1.0 Preview 所做的變更。"
    services="azure-resource-manager"
    documentationCenter="na"
    authors="ravbhatnagar"
    manager="ryjones"
    editor=""/>

<tags
    ms.service="azure-resource-manager"
    ms.devlang="na"
    ms.topic="article"
    ms.tgt_pltfrm="powershell"
    ms.workload="na"
    ms.date="10/09/2015"
    ms.author="gauravbh;tomfitz"/>


# 對於 Azure 資源管理員管理 PowerShell Cmdlet 的變更

在 Azure PowerShell 1.0 Preview 版本中，我們對於管理 Cmdlet 做了一些改善。 這些改善不包括 Cmdlet 名稱變更，其為 1.0 Preview 的一部分。 
這其中有一些改善包含了大型變更，可能會破壞您現有的指令碼。 我們希望是 
將這些變更來改善您的經驗。 我們想要聽聽您對於這些變更的意見反應，而我們會將該意見反應納入 Azure PowerShell 1.0。 因此，請試用新的 Cmdlet，然後提供意見反應給我們。

## 從資源群組中將範本部署功能分離出來

在 0.9.8 版本中，所有的範本部署參數也都會出現在資源群組 Cmdlet 中。 因此在 New-azureresourcegroup，您可以建立新的資源群組，以及， 
提供您想要部署之範本的詳細資料。 相同的範本部署功能也會出現在 New-AzureResourceGroupDeployment 中。 
在 1.0 Preview 中，我們將此功能分離出來。 現在，新增 AzureRMResourceGroup 會提供建立新的資源群組的功能，並會提供新增 AzureRmResourceGroupDeployment 
部署範本的功能。 您可以使用管線來搭配使用這兩者。 這可讓 Cmdlet 更容易了解和使用。

## 合併稽核記錄檔 Cmdlet

對於稽核記錄檔，我們提供許多 Cmdlet 來取得不同範圍的記錄檔；例如，Get-AzureResourceProviderLog、Get-AzureResourceGroupLog、Get-AzureSubscriptionIdLog 及 Get-AzureResourceLog。 若要取得記錄檔， 
您通常必須執行的記錄檔指令程式的組合。 這種使用經驗不是最理想的。 我們已彙總成單一的指令程式可呼叫在不同的範圍，透過這項功能 
使用參數。 現在，您可以呼叫 Get-AzureRmLog 搭配適當的參數來指定範圍。

在版本 0.9.8 中，若要取得特定資源群組的記錄檔，您要執行某些項目，如下所示：

    Get-AzureResourceGroupLog -ResourceGroup <resource-group-name>

若要取得特定資源的記錄檔，您要執行下列作業：

     Get-AzureResourceLog -ResourceId
     /subscriptions/#######-####-####-####-############/resourcegroups/<resource-group-name>/providers/<provider-namespace>/
     <resource-name>

在 1.0 預覽中，您可以藉由執行下列 Cmdlet 取得相同資訊。 若要取得資源群組的記錄檔，您要執行：

     Get-AzureRmLog -ResourceGroup <resource-group-name>

若要取得特定資源的記錄檔，您要執行：

     Get-AzureRmLog -ResourceId /subscriptions/#######-####-####-####-############/resourcegroups/<resource-group-name>
     /providers/<provider-namespace>/<resource-name>

## 對於適用於資源和資源群組之 Get Cmdlet 的變更

我們已併入對於 Get-AzureRmResource 和 Get-AzureRmResourceGroup Cmdlet 的變更，因此，這些 Cmdlet 現在可以僅針對資源提供者直接進行查詢。 我們已將針對快取進行查詢的功能從名為 Find-AzureRmResource* 的新 Cmdlet 中分離出來。 如果您想要尋找具有特定標記的資源群組，可以使用新的 Find-AzureRmResourceGroup Cmdlet。 透過這項變更，查詢標記的參數 
已從 Get AzureRmResource 和 Get AzureRmResourceGroup 指令程式。

在 0.9.8 中，若要找到包含特定標記的所有資源群組，您要執行：

    Get-AzureResourceGroup -Tag <tag-object>

在 1.0 預覽中，您要執行下列 Cmdlet 以達成上述案例：

    Find-AzureRmResourceGroup -Tag <tag-object>

## 已移除 Test-AzureResource 和 Test-AzureResourceGroup

這些 Cmdlet 無法針對每一個案例和資源類型可靠地運作。 我們正在努力針對此功能找出更好的解決方案。 在此同時，我們不希望您 
若要繼續使用並不可靠的指令程式。 我們已在此版本中移除這些 Cmdlet，且將在未來版本中加入可靠的解決方案。

## Get-AzureRmResourceProvider 的改善

現在，您可以使用 Get-AzureRmResourceProvider Cmdlet 來取得資源提供者的位置資訊。 它將告訴您某個特定區域中可以使用哪些提供者和類型。 此外，您可以使用這個指令程式 
取得特定的提供者的可用位置清單。 我們已移除 Get-azurelocation cmdlet，因為您可以透過取得位置的所有資訊 
Get AzureRmResourceProvider 指令程式。

在 0.9.8 中，若要取得所有支援位置的清單，您要執行：

     Get-AzureLocation

以及若要取得提供者的註冊狀態，您要執行：

     Get-AzureProvider -ListAvailable

在 1.0 預覽中，您可以僅使用如下所示的一個 Cmdlet 來完成上述作業：

     Get-AzureRmResourceProvider -Location <location>

上述作業會篩選結果，僅顯示指定位置中可用的提供者和類型。

或者，您可以將結果篩選為特定提供者，如下所示：

     Get-AzureRmResourceProvider -ProviderNamespace <provider-namespace>

上述作業會篩選結果，僅顯示支援指定提供者的位置。

## 原則 Cmdlet

我們已新增對於 Azure 資源管理員的原則支援。 這個版本中已新增可用來管理原則的 PowerShell Cmdlet。 如需原則的詳細資訊，請參閱 
[的使用原則來管理資源，並控制存取](resource-manager-policy.md)。





