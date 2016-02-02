<properties 
   pageTitle="DNS 區域上的作業 | Microsoft Azure" 
   description="您可以使用 Azure Powershell 管理 DNS 區域。如何在 Azure DNS 上更新、刪除及建立 DNS 區域" 
   services="dns" 
   documentationCenter="na" 
   authors="joaoma" 
   manager="carmonm" 
   editor=""/>

<tags
   ms.service="dns"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services" 
   ms.date="11/24/2015"
   ms.author="joaoma"/>


# 如何管理使用 PowerShell 的 DNS 區域

> [AZURE.SELECTOR]
- [Azure CLI](dns-operations-dnszones-cli.md)
- [PowerShell](dns-operations-dnszones.md)



本指南說明如何管理 DNS 區域。 有助於了解管理 DNS 區域所需執行的作業序列。


## 建立新的 DNS 區域

若要建立新的 DNS 區域來裝載您的網域，請使用 New-AzureRmDnsZone cmdlet：

        PS C:\> $zone = New-AzureRmDnsZone -Name contoso.com -ResourceGroupName MyAzureResourceGroup [–Tag $tags] 

此作業會在 Azure DNS 中建立新的 DNS 區域，並傳回對應至該區域的本機物件。 您可以選擇性地指定陣列的 Azure 資源管理員標記，如需詳細資訊，請參閱 [Etag 和標記](../dns-getstarted-create-dnszone.md#Etags-and-tags)。

區域的名稱在資源群組內必須是唯一的，且區域必須尚未存在，否則作業會失敗。

不同的資源群組或不同的 Azure 訂用帳戶中，可重複使用相同的區域名稱。 雖然多個區域共用相同的名稱，但每個執行個體會被指派不同的名稱伺服器位址，而且從父系網域只能委派一個執行個體。 請參閱 [將網域委派給 Azure DNS](dns-domain-delegation.md) 如需詳細資訊。

## 取得 DNS 區域

若要抓取 DNS 區域，請使用 Get-AzureRmDnsZone cmdlet：

        PS C:\> $zone = Get-AzureRmDnsZone -Name contoso.com –ResourceGroupName MyAzureResourceGroup

此作業會傳回對應至 Azure DNS 中現有區域的 DNS 區域物件。 這個物件包含區域的相關資料 (例如記錄集的數目)，但不包含記錄集本身。

## 列出 DNS 區域

您可以從 Get-AzureRmDnsZone 中省略區域名稱，以列舉資源群組中的所有區域：

    PS C:\> $zoneList = Get-AzureRmDnsZone -ResourceGroupName MyAzureResourceGroup

此作業會傳回一系列的區域物件。

## 更新 DNS 區域

您可以使用 Set-AzureRmDnsZone 變更至 DNS 區域資源。 這不會更新任何 DNS 記錄集內的區域 (請參閱 [如何管理 DNS 記錄](dns-operations-recordsets.md))。 它只用來更新區域資源本身的屬性。 這在目前限於區域資源的 Azure 資源管理員「標記」。 請參閱 [Etag 和標記](dns-getstarted-create-dnszone.md#Etags-and-tags) 如需詳細資訊。

請使用下列兩種方法之一來更新 DNS 區域：

### 選項 1

使用區域名稱和資源群組來指定區域。

    PS C:\> Set-AzureRmDnsZone -Name contoso.com -ResourceGroupName MyAzureResourceGroup [-Tag $tags]

### 選項 2

使用 Get-AzureRmDnsZone 傳回的 $zone 物件來指定區域：

    PS C:\> $zone = Get-AzureRmDnsZone -Name contoso.com -ResourceGroupName MyAzureResourceGroup
    PS C:\> <..modify $zone.Tags here...>
    PS C:\> Set-AzureRmDnsZone -Zone $zone [-Overwrite]

使用 Set-AzureRmDnsZone 搭配 $zone 物件時，將會使用 ‘Etag’ 檢查，以確保不會覆寫並行變更。 您可以使用選擇性的 ‘-Overwrite’ 參數來停用這些檢查。 請參閱 [Etag 和標記](dns-getstarted-create-dnszone.md#Etags-and-tags) 如需詳細資訊。

## 刪除 DNS 區域

您可以使用 Remove-AzureRmDnsZone cmdlet 刪除 DNS 區域。

在 Azure DNS 中刪除 DNS 區域之前，您必須刪除所有記錄集，但建立區域時在區域的根自動建立的 NS 和 SOA 記錄除外。

請使用下列兩種方法之一來移除 DNS 區域：

### 選項 1

使用區域名稱和資源群組名稱來指定區域：

    PS C:\> Remove-AzureRmDnsZone -Name contoso.com -ResourceGroupName MyAzureResourceGroup [-Force] 

此作業具有選擇性的 '-Force' 參數，可不提示您確認您想要移除 DNS 區域。

### 選項 2

使用 Get-AzureRmDnsZone 傳回的 $zone 物件來指定區域：

    PS C:\> $zone = Get-AzureRmDnsZone -Name contoso.com -ResourceGroupName MyAzureResourceGroup
    PS C:\> Remove-AzureRmDnsZone -Zone $zone [-Force] [-Overwrite]

'-Force' 參數與「選項 1」中相同。

為 ' 集-AzureRmDnsZone'，以指定使用 $zone 物件的區域可啟用 'etag' 檢查，以確保不會刪除並行的變更。 <BR>
選擇性的 ‘-Overwrite’ 旗標可停用這些檢查。 請參閱 [Etag 和標記](dns-getstarted-create-dnszone.md#Etags-and-tags) 如需詳細資訊。

區域物件也可以經由管道輸送，而不是當做參數傳遞：

    PS C:\> Get-AzureRmDnsZone -Name contoso.com -ResourceGroupName MyAzureResourceGroup | Remove-AzureRmDnsZone [-Force] [-Overwrite]

## 後續步驟

[管理 DNS 記錄](dns-operations-recordsets.md)

[使用.NET SDK 自動化作業](dns-sdk.md)




