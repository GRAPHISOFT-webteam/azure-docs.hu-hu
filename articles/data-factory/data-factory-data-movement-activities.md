<properties 
    pageTitle="資料移動活動" 
    description="了解您可以在 Data Factory 管線中用來移動資料的 Data Factory 實體。" 
    services="data-factory" 
    documentationCenter="" 
    authors="spelluru" 
    manager="jhubbard" 
    editor="monicar"/>

<tags 
    ms.service="data-factory" 
    ms.workload="data-services" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="12/01/2015" 
    ms.author="spelluru"/>

# 資料移動活動
 [複製活動](#copyactivity) 執行資料由 Azure Data Factory 和活動中的移動 [供全域使用的資料移動服務](#global) ，可以複製安全、 可靠且可擴充的方式的各種資料存放區之間的資料。 此服務會根據來源與接收資料存放區的位置，自動選擇用最佳區域來執行資料移動作業。 系統會使用目前最靠近接收資料存放區的區域。
 
讓我們來了解在不同情況下發生這個資料移動的方式。

## 在兩個雲端資料存放區之間複製資料
當來源和接收 (目的地) 資料存放區同時位於雲端時，複製活動就會經歷下列階段，將資料從來源複製/移動到接收資料存放區。 資料移動服務

1. 從來源資料存放區讀取資料
2.  根據輸入資料集、輸出資料集和複製活動的組態，執行序列化/還原序列化、壓縮/解壓縮、資料行對應及類型轉換 
3.  將資料寫入目的地資料存放區

![從雲端複製到雲端](.\media\data-factory-data-movement-activities\cloud-to-cloud.png)


## 在內部部署資料存放區和雲端資料存放區之間複製資料
若要 [安全公司防火牆後方的內部資料存放區與雲端資料存放區之間移動資料](#moveonpremtocloud), ，您將需要安裝資料管理閘道器，是進行混合式資料移動和處理，在內部部署電腦上的代理程式。 資料管理閘道可以和資料存放區本身安裝於同一部電腦上，或者安裝於具備可觸達該資料存放區之存取權的個別電腦上。 在此案例中，序列化/還原序列化、壓縮/解壓縮、資料行對應及類型轉換都是透過資料管理閘道來執行。 資料移動服務並未包含在此案例中。 

![從內部部署複製到雲端](.\media\data-factory-data-movement-activities\onprem-to-cloud.png)

## 複製自/至 Azure Iaas VM 上資料存放區的資料 
您也可以使用資料管理閘道，將資料移出/移入裝載於 Azure IaaS VM (基礎結構即為服務的虛擬機器) 上支援的資料存放區。 在此情況下，資料管理閘道可以和資料存放區本身安裝於同一部 Azure VM 上，或者安裝於具備可觸達該資料存放區之存取權的個別 VM 上。 

## 支援的資料存放區
複製活動將資料從複製 **來源** 資料存放區 **接收** 資料存放區。 Data factory 支援下列資料存放區和 **可以從任何來源可以將資料寫入任何接收**。 按一下資料存放區以了解如何從該存放區複製資料以及將資料複製到該存放區。 

| 來源| 接收 |
|:------- | :---- |
| <ul><li>[Azure Blob](data-factory-azure-blob-connector.md)</li><li>[Azure 資料表](data-factory-azure-table-connector.md)</li><li>[Azure SQL Database](data-factory-azure-sql-connector.md)</li><li>[Azure SQL 資料倉儲](data-factory-azure-sql-data-warehouse-connector.md)</li><li>[Azure DocumentDB](data-factory-azure-documentdb-connector.md)</li><li>[Azure 資料湖市集](data-factory-azure-datalake-connector.md)</li><li>[SQL Server 在內部部署/Azure IaaS](data-factory-sqlserver-connector.md)</li><li>[檔案系統在內部部署/Azure IaaS](data-factory-onprem-file-system-connector.md)</li><li>[Oracle 資料庫在內部部署/Azure IaaS](data-factory-onprem-oracle-connector.md)</li><li>[MySQL 資料庫在內部部署/Azure IaaS ](data-factory-onprem-mysql-connector.md)</li><li>[DB2 資料庫在內部部署/Azure IaaS](data-factory-onprem-db2-connector.md)</li><li>[Teradata 資料庫在內部部署/Azure IaaS ](data-factory-onprem-teradata-connector.md)</li><li>[Sybase 資料庫在內部部署/Azure IaaS](data-factory-onprem-sybase-connector.md)</li><li>[PostgreSQL 資料庫在內部部署/Azure IaaS](data-factory-onprem-postgresql-connector.md)</li></ul> | <ul><li>[Azure Blob](data-factory-azure-blob-connector.md)</li><li>[Azure 資料表](data-factory-azure-table-connector.md)</li><li>[Azure SQL Database](data-factory-azure-sql-connector.md)</li><li>[Azure SQL 資料倉儲](data-factory-azure-sql-data-warehouse-connector.md)</li><li>[Azure DocumentDB](data-factory-azure-documentdb-connector.md)</li><li>[Azure 資料湖市集](data-factory-azure-datalake-connector.md)</li><li>[SQL Server 在內部部署/Azure IaaS](data-factory-sqlserver-connector.md)</li><li>[檔案系統在內部部署/Azure IaaS](data-factory-onprem-file-system-connector.md)</li></ul> |


## 教學課程
如需使用 「 複製活動的快速教學課程，請參閱 [教學課程: Azure Data Factory 管線中使用複製活動](data-factory-get-started.md)。  在教學課程中，您會使用複製活動將資料從 Azure Blob 儲存體複製到 Azure SQL 資料庫。 下列區段列出複製活動支援的所有來源與接收。 

## <a name="copyactivity"></a>複製活動
複製活動會採用一個輸入資料集 (**來源**) 和一個輸出資料集 (**接收器**)。 資料複製是根據活動上指定的排程以批次方式完成。 若要深入了解整體活動定義於高的層級，例如各種 JSON 區段和屬性可用於所有活動，請參閱 [了解管線 & 活動](data-factory-create-pipelines.md) 文件。

複製活動提供下列功能：

### <a name="global"></a>全域可用的資料移動
即使 Azure Data Factory 本身只能在美國西部和北歐地區使用，但是資料移動服務支援的複製活動可供下列區域和地理位置全域使用。 全域可用性的拓撲可確保有效資料移動，避免大部分情況下的跨區域躍點。

| 區域 | [地理位置] |
| ------ | --------- | 
| 美國中部 | US |
| 美國東部 | US |
| 美國東部 2 | US |
| 美國中北部 | US |
| 美國中南部 | US |
| 美國西部 | US |
| 巴西南部 | 拉丁美洲 |
| 北歐 | EMEA |
| 西歐 | EMEA |
| 東南亞 | APAC |
| 日本東部 | APAC |

請注意： 

- 如果您要複製的資料 **內部部署資料來源** 至 **定域機組** ，反之亦然 (例如: 內部部署 SQL Server]-> [Azure Blob)，資料移動實際上是藉由 **資料管理閘道器** 不參與資料移動服務與內部部署環境中。
- 如果您要複製 **定域機組來源** 至 **定域機組目的地** (例如: Azure Blob]-> [Azure SQL)、 **資料移動服務** 挑選部署 **最接近中相同的地理位置的接收位置** 傳輸。 例如，如果您要從東南亞複製到日本西部，就可以使用日本東部的資料移動服務部署來執行複製活動。 當來源和目的地位於同一個地理位置，且該地理位置 (例如，目前是澳大利亞) 中沒有可用的資料移動服務時，複製活動將會失敗，而不會透過替代的地理位置。 附註：資料移動服務也會延伸到澳大利亞。 

### <a name="moveonpremtocloud"></a>安全地在內部部署位置與雲端之間移動資料
現代資料整合的挑戰之一是順暢地在內部部署和雲端之間來回移動資料。 資料管理閘道器是您可以安裝內部部署以啟用混合式資料管線的代理程式。 

資料閘道器提供下列功能： 

1.  安全地管理內部部署資料存放區的存取權。
2.  在相同資料處理站內建立內部部署資料存放區和雲端資料存放區的模型及移動資料。
3.  具有用於監視和管理的單一窗格，可利用資料處理站雲端為基礎的儀表板看見閘道器的狀態。

您應該將您的資料來源做為內部部署資料來源 (亦即在防火牆後面) 即使使用 **ExpressRoute** 和 **使用閘道** 來建置服務與資料來源之間的連線。 

請參閱 [內部和雲端之間移動資料](data-factory-move-data-between-onprem-and-cloud.md) 如需詳細資訊。

### 可靠且符合成本效益的資料移動
複製活動是設計來利用可靠的方式移動大量資料，可跨各種資料來源抵抗暫時性錯誤。 資料可以符合成本效益的方式利用此選項複製，以透過線路啟用壓縮。

### 跨不同類型系統的類型轉換
不同的資料存放區有不同的原生類型系統。 複製活動會利用下列 2 個步驟的方法執行自動類型轉換，從來源類型到接收類型：

1. 從原生來源類型轉換成 .NET 類型
2. 從 .NET 類型轉換成原生接收類型

您可以在各自的資料存放區連接器文章中的資料存放區找到指定原生類型系統到 .NET 的對應。 您可以在建立資料表時使用這些對應來判斷適當的類型，如此就能在複製活動期間執行正確的轉換。

### 使用不同的檔案格式。
複製活動支援各種不同的檔案格式，包括二進位、文字及 Avro 格式，以用於檔案式存放區。 您可以使用複製活動，將資料從某種格式轉換為另一種。 範例：文字 (CSV) 轉 Avro。  如果非結構化資料，您可以省略 **結構** 屬性的 JSON 定義中 [資料集](data-factory-create-datasets.md)。 

### 複製活動屬性
名稱、描述、輸入和輸出資料表、各種原則等屬性都適用於所有活動類型。 中可用的屬性 **typeProperties** 活動區段另一方面會隨著每個活動類型。 

在複製活動 **typeProperties** 區段的來源類型而異，可接收。 以上所列的每個資料存放區特定頁面都會記錄這些資料存放區類型專屬的屬性。



