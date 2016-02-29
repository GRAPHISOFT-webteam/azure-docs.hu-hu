<properties
   pageTitle="SQL 資料倉儲的彈性效能與延展性 | Microsoft Azure"
   description="使用資料倉儲單位相應增加和減少計算資源，了解 SQL 資料倉儲的彈性。 提供程式碼範例。"
   services="sql-data-warehouse"
   documentationCenter="NA"
   authors="TwoUnder"
   manager="barbkess"
   editor=""/>

<tags
   ms.service="sql-data-warehouse"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="data-services"
   ms.date="09/22/2015"
   ms.author="nicw;JRJ@BigBangData.co.uk;mausher"/>

# SQL 資料倉儲的彈性效能與延展性
如要彈性地增加或減少您的計算能力，您只需調整配置給 SQL 資料倉儲的資料倉儲單位 (DWU) 數量。 資料倉儲單位是 SQL 資料倉儲帶來的新概念，可讓您輕鬆又有效地進行管理。 本主題是資料倉儲單位的簡介，說明如何使用資料倉儲單位彈性調整計算能力。 本文也提供有關針對環境設定合理 DWU 值的一些初始指導。

## 什麼是資料倉儲單位？
Microsoft 會在幕後執行許多效能基準測試，以判斷需要多少硬體和哪些組態才能為客戶提供具競爭力的供應項目。  相應增加和減少計算能力可透過 100 個 DWU 的區塊完成，但不會提供 100 個 DWU 的所有倍數。  

## 應該使用多少 DWU 呢？
與其針對某個客戶類別提供可能合適的規範性 DWU 起始點，我們還是透過實用的方法著手解決這個問題。  SQL 資料倉儲的效能是以線性方式調整，在幾秒內就能從某個計算比例變更為另一個 (例如從 100 個 DWU 至 2000 個 DWU)。  這可讓您靈活嘗試並判斷案例的最適數目。    

1. 若是開發過程中的資料倉儲，可從少量的 DWU 開始。
2. 監視應用程式效能，觀察比較所選 DWU 數目與您觀察到的效能。
3. 透過假設線性標尺，判斷需要將效能加快或減慢多少才能達到您需求的最佳效能等級。 
4. 增加或減少選取的 DWU 數目。  服務會迅速回應並調整計算資源，以符合 DWU 需求。
5. 繼續進行調整，直到達到您業務需求的最佳效能為止。

如果應用程式的工作負載持續變動，請將效能等級上移或下移，以配合尖峰和離峰點。 比方說，如果工作負載尖峰通常落在月底，可規劃在尖峰天數期間新增更多 DWU，然後在尖峰期間結束之後相應減少。
 
## 相應增加和減少計算資源
SQL 資料倉儲不仰賴雲端儲存空間運作，其絕佳的彈性可讓您透過資料倉儲單位 (DWU) 的滑動標尺，增加、縮減或暫停計算能力。 這樣就能將您的計算能力彈性調整為最適合您業務的計算能力。  

若要提升計算能力，您可以使用 Azure 傳統入口網站中的縮放滑桿，將更多 DWU 加入服務。  您也可以透過 T-SQL、REST API 或 Powershell Cmdlet 加入 DWU。  雖然相應增加和減少計算能力會取消所有執行中或已進入佇列的活動，但此動作可在幾秒內完成，讓您以較多或較少的計算能力繼續進行。

在 [Azure 傳統入口網站][], 、 在頁面頂端的 SQL 資料倉儲，您可以按一下 '小數位數] 圖示，然後使用滑桿以增加或減少 Dwu 數量套用到資料倉儲後，再按一下 [儲存]。  如果您想要以程式設計方式變更級別，下列 T-SQL 程式碼示範如何針對您的 SQL 資料倉儲調整 DWU 配置：

```
ALTER DATABASE MySQLDW 
MODIFY (SERVICE_OBJECTIVE = 'DW1000')
;
```
請注意，您應該針對邏輯伺服器，而不是針對 SQL 資料倉儲執行個體本身，來執行這個 T-SQL。 

您也可以使用下列程式碼，透過 Powershell 達成相同結果：

```
Set-AzureSQLDatabase -DatabaseName "MySQLDW" -ServerName "MyServer.database.windows.net" -ServiceObjective "DW1000"
```

## 暫停計算資源
SQL 資料倉儲的獨一無二之處就是能夠視需要暫停和繼續計算。  如果團隊將有一段時間不使用資料倉儲執行個體 (例如在晚上、週末、特定假日，或出於任何其他原因)，則您可以在該時段暫停資料倉儲執行個體，並在恢復使用時接續之前的中斷處。  

暫停動作會將您的計算資源返還至資料中心的可用資源集區；而繼續動作會針對您設定的 DWU 取得所需的計算資源，並指派給您的資料倉儲執行個體。  

暫停和繼續執行計算能力可透過 [Azure 傳統入口網站][], 、 透過 REST Api 或 Powershell。  暫停會取消所有執行中或已進入佇列的活動；當您恢復使用時，可在幾秒內繼續執行計算資源。 

下列程式碼示範如何使用 PowerShell 執行暫停：

```
Suspend-AzureSqlDatabase –ResourceGroupName "ResourceGroup11" –ServerName
"Server01" –DatabaseName "Database02"
```

PowerShell 也可輕易地繼續執行服務：

```
Resume-AzureSqlDatabase –ResourceGroupName "ResourceGroup11" –ServerName "Server01" –DatabaseName "Database02"
```

如需有關如何使用 PowerShell 的詳細資訊，請參閱 [PowerShell cmdlet 簡介] [] 文件。

> [Azure.Note] 由於儲存體是分開計算，您的儲存體不會受到暫停。

## 後續步驟
如需效能概觀，請參閱 [效能概觀] []。

<!--Image references-->

<!--Article references-->
[performance overview]: sql-data-warehouse-overview-performance.md
[Introduction to PowerShell cmdlets]: sql-data-warehouse-get-started-powershell-cmdlets.md

<!--MSDN references-->


<!--Other Web references-->

[Azure Classic Portal]: http://portal.azure.com/

