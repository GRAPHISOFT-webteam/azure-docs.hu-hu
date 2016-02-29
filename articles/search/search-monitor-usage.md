<properties 
   pageTitle="監視 Azure 搜尋服務中的使用狀況與統計資料 | Microsoft Azure | 雲端託管搜尋服務" 
   description="追蹤 Azure 搜尋服務 (Microsoft Azure 上之託管的雲端搜尋服務) 的資源耗用量和索引大小。" 
   services="search" 
   documentationCenter="" 
   authors="HeidiSteen" 
   manager="mblythe" 
   editor=""
   tags="azure-portal"/>

<tags
   ms.service="search"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="required" 
   ms.date="11/04/2015"
   ms.author="heidist"/>

# 監視在 Azure 搜尋服務中的使用量和統計資料

追蹤索引和文件大小的成長，可在達到您為服務所設定的上限之前，幫助您先主動調整容量。 

若要監視資源使用量、 計數和統計資料會輕易地檢視中 [Azure 傳統入口網站](https://portal.azure.com), ，但您也可以取得資訊以程式設計方式如果您要建置自訂服務的系統管理工具。 本文章涵蓋此兩種技術的步驟。

##在入口網站中檢視計數和度量 

1. 登入 [Azure 傳統入口網站](https://portal.azure.com)。 

2. 開啟 Azure 搜尋服務的服務儀表板。 可在首頁上找到並排的服務，或是您也可以使用 JumpBar 上的 [瀏覽] 來瀏覽服務。 請參閱 [建立服務](search-create-service-portal.md) 如需逐步指示。

[使用量] 部分包含計量，可告知您目前正在使用可用資源的哪些部分。

  ![][1]

請回想共用服務最多各可有多少複本和分割。 此外，它總共只能支援 10,000 份文件，或是 50 MB 的資料，以先達到的條件為準。

##使用 REST API 取得索引統計資料

Azure 搜尋服務 REST API 和 .NET SDK 都提供以程式設計方式存取服務度量。  如果您使用 [索引子](https://msdn.microsoft.com/library/azure/dn946891.aspx) 從 Azure SQL Database 或 DocumentDB 載入索引，則其他 API 也可用來取得您所需要的數字。 

  + [取得索引統計資料](https://msdn.microsoft.com/library/azure/dn798942.aspx)
  + [文件計數](https://msdn.microsoft.com/library/azure/dn798924.aspx)
  + [取得索引子狀態](https://msdn.microsoft.com/library/azure/dn946884.aspx)

## 後續步驟

檢閱 [限制和容量](search-limits-quotas-capacity.md) 來判斷資料分割和複本，如果現有容量不足，您將需要的組合。 

請瀏覽 [管理搜尋服務在 Microsoft Azure 上的](search-manage.md) 如需有關服務管理。

<!--Image references-->
[1]: ./media/search-monitor-usage/AzureSearch-Monitor1.PNG




 
