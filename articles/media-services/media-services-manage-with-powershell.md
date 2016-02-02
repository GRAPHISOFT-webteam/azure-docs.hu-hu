<properties 
    pageTitle="利用 PowerShell 管理 Azure Media Services 帳戶" 
    description="瞭解如何管理 PowerShell cmdlet 與 Azure Media Services 帳戶。" 
    authors="Juliako" 
    manager="dwrede" 
    editor="" 
    services="media-services" 
    documentationCenter=""/>

<tags 
    ms.service="media-services" 
    ms.workload="media" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="12/08/2015" 
    ms.author="juliako"/>



# 利用 PowerShell 管理 Azure Media Services 帳戶

> [AZURE.SELECTOR]
- [Portal](media-services-create-account.md)
- [PowerShell](media-services-manage-with-powershell.md)
- [REST](http://msdn.microsoft.com/library/azure/dn194267.aspx)


> [AZURE.NOTE] 若要建立 Azure 媒體服務帳戶，您必須擁有 Azure 帳戶。 如果您沒有帳戶，只需要幾分鐘的時間就可以建立免費試用帳戶。 如需詳細資訊，請參閱 <a href="http://www.windowsazure.com/pricing/free-trial/?WT.mc_id=A8A8397B5" target="_blank">Azure 免費試用</a>。

## 概觀

此文章示範如何利用 PowerShell cmdlet 管理 Azure Media Services 帳戶。
>[AZURE.NOTE]
> 若要完成此教學課程，您需要 Azure 帳戶。 如果您沒有帳戶，只需要幾分鐘的時間就可以建立免費試用帳戶。 如需詳細資訊，請參閱 <a href="http://www.windowsazure.com/pricing/free-trial/?WT.mc_id=A8A8397B5" target="_blank">Azure 免費試用</a>。

## 安裝 Microsoft Azure PowerShell cmdlet

若要安裝最新版本的 Azure PowerShell cmdlet，請參閱 [如何安裝和設定 Azure PowerShell](../powershell-install-configure.md)

## 選取 Azure 訂用帳戶

安裝和設定 PowerShell cmdlet 之後，請指定您想使用的訂用帳戶。

若要取得可用的訂用帳戶清單，執行下列 cmdlet：

    PS C:\> Get-AzureSubscription

然後，選取其中一個，方法如下：

    PS C:\> Select-AzureSubscription "TestSubscription"

## 取得儲存體帳戶名稱

Azure 媒體服務使用 Azure 儲存體來儲存媒體內容。 建立新的媒體服務帳戶時，請將它與儲存體帳戶產生關聯。 儲存體帳戶必須屬於您計劃用於媒體服務帳戶相同的訂閱。

在這個範例中，會使用現有的儲存體帳戶。  [Get-azurestorageaccount](https://msdn.microsoft.com/library/azure/dn495134.aspx) cmdlet 會取得儲存體帳戶中目前的訂閱。 找出您想與媒體帳戶建立關聯的儲存體帳戶，然後取得其名稱 (StorageAccountName)。

    StorageAccountDescription : 
    AffinityGroup             :
    Location                  : East US
    GeoReplicationEnabled     : True
    GeoPrimaryLocation        : East US
    GeoSecondaryLocation      : West US
    Label                     : storagetest001
    StorageAccountStatus      : Created
    StatusOfPrimary           : Available
    StatusOfSecondary         : Available
    Endpoints                 : {https://storagetest001.blob.core.windows.net/,
                                https://storagetest001.queue.core.windows.net/,
                                https://storagetest001.table.core.windows.net/}
    AccountType               : Standard_GRS
    StorageAccountName        : storatetest001
    OperationDescription      : Get-AzureStorageAccount
    OperationId               : e919dd56-7691-96db-8b3c-2ceee891ae5d
    OperationStatus           : Succeeded

## 建立新的媒體服務帳戶

若要建立新的 Azure 媒體服務帳戶使用 [New-azuremediaservicesaccount](https://msdn.microsoft.com/library/azure/dn495286.aspx) cmdlet 可以提供媒體服務帳戶名稱、 資料中心位置，就會加以建立，以及儲存體帳戶名稱。


    PS C:\> New-AzureMediaServicesAccount -Name "amstestaccount001" -StorageAccountName "storagetest001" -Location "East US"

## 取得媒體服務帳戶

一旦您建立一或多個媒體服務帳戶，您就可以使用來列出資訊 [Get-azuremediaservicesaccount](https://msdn.microsoft.com/library/azure/dn495286.aspx)


    PS C:\> Get-AzureMediaServicesAccount
    
    AccountId       Name                State
    ---------       ----                 -----
    xxxxxxxxxx      amstestaccount001   Active

您可以提供 Name 參數，這樣就可以得到更詳細的資訊，其中包括帳戶金鑰。

    PS C:\> Get-AzureMediaServicesAccount -Name amstestaccount001

## 重新產生媒體服務存取金鑰

如果您想要更新媒體服務主要或次要存取金鑰，使用 [New-azuremediaserviceskey](https://msdn.microsoft.com/library/azure/dn495215.aspx)。 
您需要提供帳戶名稱，然後指定您想要重新產生的金鑰 (主要或次要)。

如果不希望 PowerShell 詢問確認問題，可以使用 -Force 參數。

    PS C:\> New-AzureMediaServicesKey -Name "amstestaccount001" -KeyType "Primary" -Force

## 移除媒體服務帳戶

當您準備好刪除 Azure 媒體帳戶時，請使用 [Remove-azuremediaservicesaccount](https://msdn.microsoft.com/library/azure/dn495220.aspx)。

    PS C:\> Remove-AzureMediaServicesAccount -Name "amstestaccount001" -Force

## 媒體服務學習路徑

[AZURE.INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

## 提供意見反應

[AZURE.INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]






