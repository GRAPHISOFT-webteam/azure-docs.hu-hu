<properties 
    pageTitle="Azure 搜尋服務索引子自訂 | Microsoft Azure | 雲端託管搜尋服務" 
    description="了解如何自訂 Azure 搜尋服務 (Microsoft Azure 上之託管的雲端搜尋服務) 中的設定和索引子原則。" 
    services="search" 
    documentationCenter="" 
    authors="chaosrealm" 
    manager="pablocas" 
    editor=""/>

<tags 
    ms.service="search" 
    ms.devlang="rest-api" 
    ms.workload="search" 
    ms.topic="article" 
    ms.tgt_pltfrm="na" 
    ms.date="11/04/2015" 
    ms.author="eugenesh"/>


# Azure 搜尋服務索引子自訂

設定 Azure 搜尋中的索引子，讓您將資料來源與目標索引之間的欄位重新命名、將從資料庫資料表的字串轉換成字串集合、在資料來源上切換變更偵測原則、URL 編碼包含不安全 URL 字元的文件索引鍵，並容許失敗以編製一些文件的索引。

如果您不熟悉 Azure 搜尋服務索引子，您可能想要先查看下列文章：

- [Azure SQL Database 連接至 Azure 搜尋服務使用索引子](search-howto-connecting-azure-sql-database-to-azure-search-using-indexers-2015-02-28.md)
- [子連接 DocumentDB 與 Azure 搜尋服務使用索引子](../documentdb/documentdb-search-indexer.md)
- [具有索引子支援的.NET SDK](https://msdn.microsoft.com/library/dn951165.aspx) 或
- [索引子 REST API 參考](https://msdn.microsoft.com/library/azure/dn946891.aspx)

## 重新命名資料來源與目標索引之間的欄位

**欄位對應**是協調欄位定義之間差異的屬性。 最常見的範例會在違反 Azure 搜尋服務命名規則的欄位名稱中找到。 請考慮使用前置底線的一或多個欄位名稱從哪裡開始的來源資料表 (例如 `_id`)。 Azure 搜尋服務不允許欄位名稱的開頭是底線，因此必須重新命名欄位。

下列範例說明更新索引子以包含 「 重新命名 」 的欄位對應 `_id` 欄位將資料來源的 `識別碼` 目標索引中的欄位:

    PUT https://[service name].search.windows.net/indexers/myindexer?api-version=[api-version]
    Content-Type: application/json
    api-key: [admin key]
    {
        "dataSourceName" : "mydatasource",
        "targetIndexName" : "myindex",
        "fieldMappings" : [ { "sourceFieldName" : "_id", "targetFieldName" : "id" } ] 
    } 

**附註：**您需要使用預覽 API 2015-02-28-Preview 版本以使用欄位對應。

您可以指定多個欄位對應：

    "fieldMappings" : [ 
        { "sourceFieldName" : "_id", "targetFieldName" : "id" },
        { "sourceFieldName" : "_timestamp", "targetFieldName" : "timestamp" },
     ]

來源和目標欄位名稱皆有區分大小寫。

## 從資料庫資料表將字串轉換成字串集合

您也可以使用「對應函數」**，將欄位對應用來轉換來源欄位值。

一個這類函式， `jsonArrayToStringCollection`, ，它會剖析為目標索引中的 collection (edm.string) 欄位包含字串的欄位格式化為 JSON 陣列。 該函數主要用來搭配 Azure SQL 索引子，因為 SQL 不具備原生的集合資料類型。 其用法如下：

    "fieldMappings" : [ { "sourceFieldName" : "tags", "mappingFunction" : { "name" : "jsonArrayToStringCollection" } } ] 

例如，如果來源欄位包含字串 `["red"、"white"、"blue"]`, ，然後 [目標] 欄位的型別 `collection (edm.string)` 會填入三個值 `「 紅色 」`, ，`"white"` 和 `"blue"`。

請注意， `targetFieldName` 屬性為選用; 如果省略，則 `sourceFieldName` 會使用值。

## 切換資料來源的變更偵測原則

在 Azure 搜尋服務中，要使用哪個變更偵測原則的決策多半是由您的資料來源和資料結構描述支援的項目決定。 經過一段時間，特別是當您升級或移轉資料庫，您可能想要切換到另一種類型的變更偵測原則。 例如，或許您剛剛將您的 Azure SQL Database 更新為較新版本，支援整合變更追蹤，所以您想要從上限標準原則切換至整合變更追蹤原則。 或者也許您決定使用不同的資料行做為上限標準。

如果您只是呼叫 PUT 資料來源 REST API 以更新您的資料來源，您可能會得到 400 回應，並且有如下的錯誤訊息：


    "Change detection policy cannot be changed for data source '…' because indexer '…' references this data source and has a non-empty change tracking state. You can use Reset API to reset the indexer's change tracking state, and retry this call."

 您可能會猜想這是什麼意思，以及如何解決。 此錯誤的發生原因是因為 Azure 搜尋服務會維護與您的變更偵測原則相關聯的內部狀態。 變更原則時，現有狀態會失效，因為它不會套用到新的原則。 這表示索引子必須使用新原則重頭開始編製資料來源的索引，如此對您有潛在的影響 (例如，資料庫的額外負載，或額外網路頻寬費用)。 也就是為什麼 Azure 搜尋服務會要求您呼叫 [重設索引子 API](https://msdn.microsoft.com/library/azure/dn946897.aspx) 重設與目前變更偵測原則相關聯的狀態，原則可以是使用一般 PUT 資料來源呼叫變更。 當然，Azure 搜尋服務無法自動為您進行重設，但我們認為重要的是，藉由呼叫重設 API 以明確地確認您了解含意。

## URL 編碼的文件金鑰，包含不安全 URL 字元

Azure 搜尋服務會將文件索引鍵欄位內的字元限制為 URL 安全字元，因為使用者必須能夠依其索引鍵查閱文件。 因此當您需要編製索引的文件在索引鍵欄位中包含這類字元時會發生什麼事？ 如果您使用用戶端 SDK 或 REST API 自行編製文件索引，您可以 URL 編碼索引鍵。 使用索引子，您可以告知 Azure 搜尋服務以 URL 編碼您的金鑰設定 **base64EncodeKeys** 參數 `true` 時建立或更新索引子:

    PUT https://[service name].search.windows.net/indexers/myindexer?api-version=[api-version]
    Content-Type: application/json
    api-key: [admin key]
    {
        "dataSourceName" : "mydatasource",
        "targetIndexName" : "myindex",
        "parameters" : { "base64EncodeKeys": true }
    }

如需編碼方式的詳細資訊，請參閱此 [MSDN 文章](http://msdn.microsoft.com/library/system.web.httpserverutility.urltokenencode.aspx)。

注意：如果您需要搜尋或篩選索引鍵值，您必須同樣的編碼在要求中使用的索引鍵，使您的要求符合搜尋索引中儲存的編碼值。


## 容許失敗以編製一些文件的索引

根據預設，只要單一文件無法進行索引編製，Azure 搜尋服務索引子就會停止編製索引。 根據您的情況而定，您可以選擇容忍某些失敗 (例如，如果您重複地重新編製整個資料來源的索引)。 Azure 搜尋服務提供兩個索引子參數以微調此行為：

- **maxFailedItems**：在索引子執行發生錯誤前，可能無法編製索引的項目數。 預設值為 0。
- **maxFailedItemsPerBatch**：在索引子執行發生錯誤前，單一批次中可能無法編製索引的項目數。 預設值為 0。

建立或更新您的索引子時，您可以指定其中一個參數或同時指定兩個參數，隨時變更這些值：

    PUT https://[service name].search.windows.net/indexers/myindexer?api-version=[api-version]
    Content-Type: application/json
    api-key: [admin key]
    {
        "dataSourceName" : "mydatasource",
        "targetIndexName" : "myindex",
        "parameters" : { "maxFailedItems" : 10, "maxFailedItemsPerBatch" : 5 }
    }

即使您選擇容許某些失敗，傳回哪個文件失敗的資訊 [取得索引子狀態 API](https://msdn.microsoft.com/library/azure/dn946884.aspx)。

目前是如此。 如果您有任何想法和建議未來內容的想法，推文給我們使用 #AzureSearch 雜湊標記，或提出您的想法上我們 [UserVoice 頁面](http://feedback.azure.com/forums/263029-azure-search)。





