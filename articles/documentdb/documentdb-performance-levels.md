<properties 
    pageTitle="DocumentDB 中的效能等級 | Microsoft Azure" 
    description="了解 DocumentDB 中的效能等級如何可讓您依每個集合為基礎保留輸送量。" 
    services="documentdb" 
    authors="johnfmacintyre" 
    manager="jhubbard" 
    editor="monicar" 
    documentationCenter=""/>

<tags 
    ms.service="documentdb" 
    ms.workload="data-services" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="10/16/2015" 
    ms.author="johnmac"/>

#DocumentDB 中的效能等級

這篇文章提供概觀中的效能等級 [Microsoft Azure DocumentDB](http://azure.microsoft.com/services/documentdb/)。 

閱讀本文後，您將能夠回答下列問題：  

-   什麼是效能等級？
-   如何為資料庫帳戶保留輸送量？
-   如何處理效能等級？
-   效能等級的收費如何？

##效能等級的簡介

在標準帳戶下建立的每個 DocumentDB 集合將會佈建相關聯的效能等級。 效能等級可指定為 S1、S2 或 S3，效能範圍從最低到最高。 集合的效能等級決定保留給您應用程式的要求處理資源量。 在資料庫中的每一個集合可以有不同的效能等級，讓您為經常存取的集合指定較多的輸送量，以及為較不常存取的集合指定較少的輸送量。 任何集合的最小效能等級為 S1。

每個效能等級都有相關聯的要求單位 (RU) 費率限制。 這是依以效能等級為基礎保留給集合的輸送量，而且是該集合所專用。 集合可以透過建立 [Azure 傳統入口網站](http://portal.azure.com) 或任何 [DocumentDB Sdk](https://msdn.microsoft.com/library/azure/dn781482.aspx)。 DocumentDB API 可讓您指定集合的效能等級。 

集合效能等級|保留的輸送量
---|---
S1|250 RU/秒
S2|1000 RU/秒
S3|2500 RU/秒

DocumentDB 可供進行許多的資料庫作業，包括查詢、使用者定義函數 (UDF) 的查詢、預存程序和觸發程序。 與上述各項作業相關聯的處理成本，會因為完成作業所需的 CPU、IO 和記憶體而不同。 您不需要考慮和管理硬體資源，您可以將要求單位想成是執行各種資料庫作業以及服務應用程式要求時所需的資源數量。

> [AZURE.NOTE] 效能等級是依要求單位測量。 每個效能等級有相關聯的每秒最大要求單位速率。 您可以透過 Api 調整集合的效能層級或 [Azure 傳統入口網站](https://portal.azure.com/)。

##設定集合的效能等級
一旦建立集合，將保留集合根據指定效能等級的 RU 完整配置。 例如，如果集合設為 S3，集合便可處理 2500 RU/秒。 每個集合會保留其指定的輸送量和 10 GB 的資料庫儲存體。 集合的價格會隨著選擇的效能等級而不同 (S1、S2、S3)。 請注意，DocumentDB 會根據容量保留運作；藉由建立集合，已保留某個應用程式，並對其就保留的輸送量和資料庫儲存體收費，不論該儲存體和輸送量中有多少在使用中。

建立集合之後，您可以透過 DocumentDB SDK 或透過 Azure 傳統入口網站修改效能等級。 

> [AZURE.IMPORTANT] DocumentDB 標準集合依每小時費率收費，並且您建立的每個集合會被收取最小的一小時的使用量。 

如果您將集合的效能等級調整在一個小時內，您仍會被收取在該小時期間所設定最高的效能等級。 例如，如果您在上午 8:53 增加某個集合的效能等級，您會被從上午 8:00 開始收取新的層級費用。 同樣地，如果您在上午 8:53 減少效能等級，則會在上午 9:00 套用新的費率。

要求單位是根據設定的效能等級保留給每個集合。 要求單位消耗量是以每秒的速率來計算。 如果應用程式的速率超過集合上佈建的要求單位速率 (或效能等級)，便會受到節流控制，直到該速率降到該集合的預留層級以下。 如果您的應用程式需要較高等級的輸送量，您可以增加每個集合的效能等級。

> [AZURE.NOTE] 當您的應用程式超過一個或多個集合的效能層級時，要求將會降低每個集合為基礎。 這表示某些應用程式的要求可能會成功，而其他的可能受到節流控制。

##處理效能等級
DocumentDB 集合可讓您根據查詢模式和應用程式的效能需求來分割資料。 請參閱 [分割資料文件](documentdb-partition-data.md) 如需有關使用 DocumentDB 分割資料。 藉由 DocumentDB 的自動索引和查詢支援，在相同集合內共置異質文件是相當常見的。 決定是否應該使用不同集合的重要考量包括：

- 查詢 – 集合是查詢執行的範圍。 如果您需要跨一組文件進行查詢，最有效率的讀取模式是來自共置在單一集合的文件。
- 交易 – 集合是預存程序和觸發程序的交易網域。 所有交易的範圍為單一集合。 
- 效能 – 集合具有相關聯的效能等級。 這可確保每個集合透過保留的 RU 具有可預測的效能。 資料可以根據存取頻率，使用不同的效能等級配置到不同的集合。

> [AZURE.IMPORTANT] 請務必了解您將會收取完整的標準費率，根據您的應用程式所建立的集合數目。

建議您的應用程式使用少量的集合，除非您具有大量儲存體或輸送量的需求。 確定您更加了解建立新集合的應用程式模式。 您可以選擇將集合建立保留做為在您的應用程式外部處理的管理動作。 同樣地，調整集合的效能等級也將變更對集合收取的小時費率。 如果您的應用程式動態調整等級，您應該監控集合的效能等級。

##使用 Azure 入口網站變更效能層級

Azure 入口網站是您在管理集合的效能層級的其中一個選項。 請遵循下列步驟以從 Azure 傳統入口網站變更集合的效能層級。

1. 透過瀏覽至 [**Azure 入口網站**](https://portal.azure.com) 從瀏覽器。
2. 按一下 [ **瀏覽** 從左側導向列。
3. 在 **瀏覽** 集線器，按一下 [ **DocumentDB 帳戶** 下 **篩選** 標籤。
4. 在 **DocumentDB 帳戶** 刀鋒視窗中，按一下包含所要的 DocumentDB 帳戶。
5. 在 **DocumentDB 帳戶** 刀鋒視窗中，向下捲動至 **資料庫** ] 透鏡，然後按一下包含所要的資料庫。 
6. 在新開啟 **資料庫** 刀鋒視窗中，向下捲動至 **集合** ] 透鏡，然後選取您想要的集合。
7. 最後，在您 **集合** 刀鋒視窗中，尋找並按一下 **定價層** 排列 **使用量** 鏡頭。
8. 在 **選擇價格層** 刀鋒視窗中，按一下所需的效能層級，然後按一下 **選取** 刀鋒視窗的底部。 

>[AZURE.NOTE] 變更集合的效能層級，可能要花最多 2 分鐘。

![變更定價層][1]

##使用 .NET SDK 變更效能層級

變更集合的效能層級的另一個選項是透過我們的 SDK。 本節只涵蓋變更集合的效能層級使用我們 [.NET SDK](https://msdn.microsoft.com/library/azure/dn948556.aspx), ，是我們的其他類似的程序，但 [Sdk](https://msdn.microsoft.com/library/azure/dn781482.aspx)。 如果您不熟悉我們的.NET SDK，請造訪我們 [快速入門教學課程](documentdb-get-started.md)。

以下是變更提供項目類型的程式碼片段：

    //Fetch the resource to be updated
    Offer offer = client.CreateOfferQuery()
                              .Where(r => r.ResourceLink == "collection selfLink")    
                              .AsEnumerable()
                              .SingleOrDefault();
                              
    //Change the user mode to All
    offer.OfferType = "S3";
                        
    //Now persist these changes to the database by replacing the original resource
    Offer updated = await client.ReplaceOfferAsync(offer);

請瀏覽 [MSDN](https://msdn.microsoft.com/library/azure/microsoft.azure.documents.client.documentclient.aspx) 檢視其他範例，並深入了解我們提供的方法 ︰ 

- [**ReadOfferAsync**](https://msdn.microsoft.com/library/azure/microsoft.azure.documents.client.documentclient.readofferasync.aspx)
- [**ReadOffersFeedAsync**](https://msdn.microsoft.com/library/azure/microsoft.azure.documents.client.documentclient.readoffersfeedasync.aspx)
- [**ReplaceOfferAsync**](https://msdn.microsoft.com/library/azure/microsoft.azure.documents.client.documentclient.replaceofferasync.aspx)
- [**CreateOfferQuery**](https://msdn.microsoft.com/library/azure/microsoft.azure.documents.linq.documentqueryable.createofferquery.aspx) 

##後續步驟

若要深入了解 Azure DocumentDB 的價格和管理資料，請探索這些資源：
 
- [DocumentDB 定價](http://azure.microsoft.com/pricing/details/documentdb/)
- [管理 DocumentDB 容量](documentdb-manage.md) 
- [在 DocumentDB 中模型化資料](documentdb-modeling-data.md)
- [在 DocumentDB 中分割資料](documentdb-partition-data.md)

若要深入了解 DocumentDB，請參閱 Azure DocumentDB [文件](http://azure.microsoft.com/documentation/services/documentdb/)。 

[1]: ./media/documentdb-performance-levels/img1.png 


