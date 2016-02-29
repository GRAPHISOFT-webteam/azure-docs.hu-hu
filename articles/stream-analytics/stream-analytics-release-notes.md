<properties 
    pageTitle="串流分析版本資訊 | Microsoft Azure" 
    description="Azure 串流分析版本資訊" 
    services="stream-analytics" 
    documentationCenter="" 
    authors="jeffstokes72" 
    manager="paulettm" 
    editor="cgronlun"/>

<tags 
    ms.service="stream-analytics" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.tgt_pltfrm="na" 
    ms.workload="data-services" 
    ms.date="12/10/2015" 
    ms.author="jeffstok"/>

#Microsoft 串流分析版本資訊

## 串流分析 2015 年 12 月 10 日版本的注意事項 ##

此版本包含下列更新。

課程名稱 | 說明
---|---
REST API 版本更新 | REST API 的版本已更新至 2015-10-01。 可以在 MSDN 上找到詳細資料 [資料流分析管理 REST API 參考](https://msdn.microsoft.com/library/azure/dn835031.aspx) 和 [中資料流分析的機器學習整合](stream-analytics-how-to-configure-azure-machine-learning-endpoints-in-stream-analytics.md)。
Azure 機器學習的整合 | 此版本開始支援 Azure 機器學習的使用者定義函數。 可以找到教學課程 [這裡](stream-analytics-machine-learning-tutorial.md) 以及一般的部落格公告 [這裡](http://blogs.technet.com/b/machinelearning/archive/2015/12/10/apply-azure-ml-as-a-function-in-azure-stream-analytics.aspx)。

## 串流分析 2015/11/12 版本的注意事項 ##

此版本包含下列更新。

課程名稱 | 說明
---|---
SELECT 的新行為 | 「串流分析」中的 SELECT 已經延伸成允許使用 * 做為巢狀記錄的屬性存取子。 如需詳細資訊，請參閱 [http://msdn.microsoft.com/library/mt622759.aspx](http://msdn.microsoft.com/library/mt622759.aspx "複雜資料型別")。

## 串流分析 2015/10/22 版本的注意事項 ##

此版本包含下列更新。

課程名稱 | 說明
---|---
其他查詢語言功能 | 資料流分析已展開的查詢語言包括下列功能: [ABS](https://msdn.microsoft.com/library/azure/mt574054.aspx), ，[CEILING](https://msdn.microsoft.com/library/azure/mt605286.aspx), ，[EXP](https://msdn.microsoft.com/library/azure/mt605289.aspx), ，[FLOOR](https://msdn.microsoft.com/library/azure/mt605240.aspx), ，[電源](https://msdn.microsoft.com/library/azure/mt605287.aspx), ，[登](https://msdn.microsoft.com/library/azure/mt605290.aspx), ，[方](https://msdn.microsoft.com/library/azure/mt605288.aspx), ，和 [SQRT](https://msdn.microsoft.com/library/azure/mt605238.aspx)。
移除了彙總限制  | 此版本移除一個查詢 15 個彙總的限制。 現在每一個查詢的彙總數目沒有限制。
新增了 GROUP BY System.Timestamp 功能 |  [GROUP BY](https://msdn.microsoft.com/library/azure/dn835023.aspx) 函式現在允許任一 items> 或 [System.Timestamp](https://msdn.microsoft.com/library/azure/mt598501.aspx)。
新增了適用於輪轉和跳動視窗的 OFFSET | 根據預設， [輪轉](https://msdn.microsoft.com/library/azure/dn835055.aspx) 和 [Hopping](https://msdn.microsoft.com/library/azure/dn835041.aspx) windows 皆與零的時間 (1/1/0001 12:00:00 UTC)。 新的 (選擇性) 參數 'offsetsize' 允許指定自訂的 位移 (或對齊)。


## 串流分析 2015/09/29 版本的注意事項 ##

此版本包含下列更新。

課程名稱 | 說明
---|---
Azure IoT Suite 公用預覽 | Azure IoT Suite 公用預覽包含串流分析。
Azure Preview 入口網站整合 | 在 Azure 管理入口網站中的持續存在，除了資料流分析目前已整合在 [Azure Preview 入口網站](http://azure.microsoft.com/overview/preview-portal/)。 請注意，Preview 入口網站中的串流分析功能目前是 Azure 管理入口網站所提供功能的子集，不支援瀏覽器中查詢測試、Power BI 輸出組態和在有存取權限的訂用帳戶中瀏覽或建立新的輸入和輸出資源。
支援 DocumentDB 輸出 | 現在可以輸出資料流分析工作 [DocumentDB](http://azure.microsoft.com/services/documentdb/)。
支援 IoT 中心輸入 | 串流分析作業現在可以內嵌來自 IoT 中心的資料。
異質事件的時間戳記格式 | 當單一資料流包含多個具有不同欄位中的時間戳記的事件類型時，您現在可以使用 [TIMESTAMP BY](http://msdn.microsoft.com/library/mt573293.aspx) 使用運算式來指定每個案例的不同時間戳記欄位。

## 資料流分析 2015/09/10 版本的注意事項 ##

此版本包含下列更新。

課程名稱|說明
---|---
支援 PowerBI 群組|若要啟用與其他 Power BI 使用者共用的資料，資料流分析工作可以立即寫入 [PowerBI 群組](stream-analytics-define-outputs.md#power-bi) Power BI 帳戶內。

## 串流分析 2015 年 8 月 20 日版本的注意事項 ##

此版本包含下列更新。

課程名稱|說明
---|---
加入 LAST 函式 | [上次](http://msdn.microsoft.com/library/mt421186.aspx) 函式現在是可用於資料流分析工作，讓您能夠擷取最近的事件中指定的時間範圍內的事件資料流。
新的陣列函數|陣列函數 [GetArrayElement](http://msdn.microsoft.com/library/mt270218.aspx), ，[GetArrayElements](http://msdn.microsoft.com/library/mt298451.aspx) 和 [GetArrayLength](http://msdn.microsoft.com/library/mt270226.aspx) 已提供下載。
新的記錄函數|記錄函式 [GetRecordProperties](http://msdn.microsoft.com/library/mt270221.aspx) 和 [GetRecordPropertyValue](http://msdn.microsoft.com/library/mt270220.aspx) 已提供下載。

## 串流分析 07/30/2015 版本的注意事項 ##

此版本包含下列更新。

課程名稱|說明
---|---
與 Azure 識別碼分離的 Power BI 組織識別碼|此功能可讓 [Power BI 輸出](stream-analytics-power-bi-dashboard.md) 在任何 Azure 帳戶類型 (Live Id 或組織識別碼) 下啟用 ASA 工作。 此外，您可以擁有 Azure 帳戶的組織識別碼，並使用另一個識別碼用於授權 Power BI 輸出。
服務匯流排佇列輸出的支援|[服務匯流排佇列](stream-analytics-connect-data-event-outputs.md#service-bus-queues) 輸出現在已可用於資料流分析工作。
服務匯流排主題輸出的支援|[服務匯流排主題](stream-analytics-connect-data-event-outputs.md#service-bus-topics) 輸出現在已可用於資料流分析工作。

## 串流分析 2015/07/09 版本的注意事項 ##

此版本包含下列更新。


課程名稱|說明
---|---
自訂 Blob 輸出資料分割|Blob 儲存體輸出現在允許指定寫入輸出 Blob 的頻率，以及輸出資料路徑資料夾結構的結構和格式的選項。 

## 串流分析 2015/05/03 版本的注意事項 ##

此版本包含下列更新。


課程名稱|說明
---|---
增加次序錯誤允許的時間範圍的最大值|次序錯誤允許的時間範圍的大小上限現在是 59:59 (MM:SS)
JSON 輸出格式：以行分隔或陣列|現在當輸出至 Blob 儲存體或事件中心時，還有另一個選擇，就是以 JSON 物件的陣列輸出，或透過以新行分隔 JSON 物件來輸出。 

## 串流分析 2015/04/16 版本的注意事項 ##


課程名稱|說明
---|---
Azure 儲存體帳戶組態的延遲|第一次在某個區域中建立串流分析工作時，系統會提示您建立新的儲存體帳戶或指定現有帳戶，以監視該區域中的串流分析工作。 由於設定監視作業會有所延遲，若在 30 分鐘內於相同區域中建立另一個串流分析工作，系統將提示您指定第二個儲存體帳戶，而不會在 [監視儲存體帳戶] 下拉式清單中顯示最近設定的帳戶。 若要避免建立不必要的儲存體帳戶，請在第一次於某區域中建立工作的 30 分鐘後，再於該區域中佈建其他工作。
工作升級|串流分析目前無法對執行中工作的定義或組態進行即時編輯。 若要變更執行中工作的輸入、輸出、查詢、調整或組態，您必須先停止工作。
從輸入來源推斷而來的資料類型|如果未使用 CREATE TABLE 陳述式，系統會從輸入格式衍生輸入類型，例如 CSV 的所有欄位都是字串。 必須使用 CAST 函數，將欄位明確轉換成正確的類型，以避免發生類型不一致錯誤。
遺漏的欄位在輸出後會變成 null 值|參考的欄位如果在輸入來源中找不到，將會在輸出事件中產生 null 值。
WITH 陳述式前面必須是 SELECT 陳述式|在您的查詢中，SELECT 陳述式後面必須是 WITH 陳述式中定義的下列子查詢。
記憶體不足問題|串流分析工作如果可以容忍大量的失控事件和/或存在大量狀態的複雜查詢，可能會導致工作耗盡記憶體，進而造成工作重新開始。 啟動作業和停止作業會顯示在工作的作業記錄中。 若要避免此行為，請將查詢橫向擴充至多個資料分割。 在未來版本中，將會藉由降低受影響之工作的效能 (而不是加以重新啟動)，來解決這項限制。
沒有裝載時間戳記的大型 Blob 輸入，可能會造成記憶體不足問題|Blob 儲存體用於大量的檔案時，如果未透過 TIMESTAMP BY 指定時間戳記欄位，則可能會造串流分析工作崩潰。 要避免此問題，請讓每個 Blob 的大小保持在 10 MB 以下。
SQL Database事件容量限制|使用 SQL Database 做為輸出來源時，非常大量的輸出資料可能會導致串流分析工作無法在限定的時間內完成。 要解決這個問題，請使用彙總或篩選運算子減少輸出量，或改以 Azure Blob 儲存體或事件中樞做為輸出來源。
PowerBI 資料集只能包含一個資料表|PowerBI 不允許指定的資料集中存在多個資料表。

## 取得說明
如需進一步的協助，請嘗試我們 [Azure 串流分析論壇](https://social.msdn.microsoft.com/Forums/en-US/home?forum=AzureStreamAnalytics)

## 後續步驟

- [Azure 資料流分析簡介](stream-analytics-introduction.md)
- [開始使用 Azure 資料流分析](../stream.analytics.get.started.md)
- [調整 Azure 資料流分析工作](stream-analytics-scale-jobs.md)
- [Azure Stream Analytics 查詢語言參考](https://msdn.microsoft.com/library/azure/dn834998.aspx)
- [Azure 串流分析管理 REST API 參考](https://msdn.microsoft.com/library/azure/dn835031.aspx)
 

