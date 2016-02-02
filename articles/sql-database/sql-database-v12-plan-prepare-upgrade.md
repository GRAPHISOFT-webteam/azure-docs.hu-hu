<properties
    pageTitle="規劃升級至 SQL Database V12 |Microsoft Azure"
    description="說明升級至 Azure SQL Database V12 版本的相關準備和限制。"
    services="sql-database"
    documentationCenter=""
    authors="MightyPen"
    manager="jeffreyg"
    editor=""/>


<tags
    ms.service="sql-database"
    ms.workload="data-management" 
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="10/08/2015"
    ms.author="genemi"/>



# 規劃和準備升級至 SQL Database V12

本主題描述將 Azure SQL Database 從 V11 版升級至 V12 版必須執行的規劃和準備。


新 [Azure 入口網站](http://portal.azure.com/) 是用來支援您升級至 V12。


下表列出 V12 的其他說明主題。


| 標題和連結| 內容的描述|
| :--- | :--- |
| [什麼是 SQL Database V12 新功能](sql-database-v12-whats-new.md)| 描述 V12 如何提升 Azure SQL Database 以與 Microsoft SQL Server 並駕齊驅的詳細資料。|
| [在 SQL Database V12 中建立資料庫](sql-database-get-started.md)| 描述如何建立全新 V12 版本的 Azure SQL Database。內容描述各種選項，而非只是空的資料庫。|


## 事先規劃

下列小節描述在您採取動作將 Azure SQL Database 升級至 V12 之前，必須學習的知識和決策制訂。

### 版本釐清

本文件是關於將 Microsoft Azure SQL Database 從 V11 升級至 V12 版。 更正式的版本編號接近 Transact-SQL 陳述式 **SELECT @@version;** 報告的下列兩個值：


- 12.0.2000.8 *(或更高版本，V12)*
- 11.0.9228.18 *(V11)*
 - 有時 V11 也稱為 SAWA v2。

### 服務層規劃

從 V12 開始，Azure SQL Database 將僅支援名為「基本」、「標準」和「高階」的服務層。 V12 不支援 Web 和商務服務層。 因此，如果您打算將目前是 Web 或商務服務層的 Azure SQL Database 升級，您必須決定新的服務層應該是什麼。


如需「基本」、「標準」和「高階」服務層的詳細資訊，請參閱：

- [SQL Database 服務層](sql-database-service-tiers.md)
- [SQL Database Web/商務資料庫升級至新的服務層](sql-database-upgrade-new-service-tiers.md)



### 檢閱異地複寫設定

如果您的 Azure SQL Database 已設定為異地複寫，您應先記錄其目前的設定、停止異地複寫，然後再開始升級準備動作。 在升級完成後，您必須重新設定資料庫的異地複寫。


對策是保留來源，並在資料庫複本上測試。


## 準備動作

規劃完成之後，您可以執行下列小節中描述的動作步驟，為最終升級階段做準備。


本說明主題上方所連結之說明主題中，有最終升級階段的詳細描述。


### 服務層動作

V12 不支援 Web 和商務服務定價層級。


如果您的 V11 Azure SQL Database 是 Web 或商務資料庫，升級程序會提供將資料庫切換到支援之服務層的選項。 升級會建議適合您資料庫之工作負載歷程記錄的服務層。 不過，您可以選擇任何您喜歡之支援的服務層。


開始升級之前，您可以透過將您的 V11 資料庫切離 Web 和商務服務層，以減少升級期間的必要步驟。 您可以使用新 [Azure 入口網站](http://portal.azure.com/)。


如果您不確定要切換到哪個服務層，「標準」層的 S2 層級會是實用的初始選擇。 較低層的資源會比 Web 和商務層來得少。


### 在升級期間暫停異地複寫

如果異地複寫在您資料庫中是作用中狀態，將無法升級至 V12。 您必須先重新設定資料庫以停止使用異地複寫。


在升級完成後，您可以設定資料庫再次使用異地複寫。


### Azure VM 上的用戶端

如果您的用戶端在 Azure 虛擬機器 (VM) 上執行時，用戶端程式連線至 SQL Database V12，您就必須開啟此 VM 上的下列範圍連接埠：

- 11000-11999
- 14000-14999


按一下 [ [這裡](sql-database-develop-direct-route-ports-adonet-v12.md) 如需詳細資訊的 SQL Database V12 的連接埠。 SQL Database V12 的效能增強功能需要這些連接埠。


## <a id="limitations"></a>升級至 V12 期間和之後的限制

### V12 的入口網站

有三個 Azure 的入口網站，每個入口網站都有不同的 SQL Database V12 功能。


- [http://portal.azure.com/](http://portal.azure.com/)<br/>此 Azure 入口網站的新，仍處於預覽狀態。 此入口網站還未完全公開上市 (GA)。 此入口網站：
 - 可以管理您的 V12 伺服器和資料庫。
 - 可以將 V11 資料庫升級至 V12。

- [http://manage.windowsazure.com/](http://manage.windowsazure.com/)<br/>此 Azure 傳統入口網站可能最終淘汰時。 此入口網站：
 - 可以管理您的 V12 伺服器和資料庫。
 - *無法*將 V11 資料庫升級至 V12。

- (http://*yourservername*。.database.windows.net)<br/>
Azure SQL 資料庫傳統入口網站:
 - 可以*不* 管理 V12 伺服器。


我們鼓勵您使用 Visual Studio 2013 (VS2013) 連線到您的 Azure SQL Database。 VS2013 可用於下列工作：


- 執行 Transact-SQL 陳述式。
- 設計結構描述。
- 開發線上或離線資料庫。


您可以改為連接 [Visual Studio Community 2013](https://www.visualstudio.com/en-us/news/vs2013-community-vs.aspx/), ，這是 VS2013 免費且功能完整的版本。


您可以在較舊的 Azure 傳統入口網站的 [資料庫] 頁面上，按一下 [在 Visual Studio 中開啟]**** 在電腦上啟動 VS2013，來連線至您的 Azure SQL Database。


如需另一個替代方式，您可以使用 SQL Server Management Studio (SSMS) 2014年與 [CU6](http://support.microsoft.com/kb/3031047/) 連接到 Azure SQL 資料庫。 此部落格文章會詳細:<br/>[工具更新 Azure SQL database 的用戶端](http://azure.microsoft.com/blog/2014/12/22/client-tooling-updates-for-azure-sql-database/)。


### 升級至 V12 *期間*的限制

升級至 V12 期間，V11 資料庫仍然可供存取資料。 還有一些限制需要注意。


| 限制| 說明|
| :--- | :--- |
| 升級期間| 升級期間取決於大小、版本和伺服器中的資料庫數目而定。升級程序可以執行數小時至數天伺服器，尤其是對於具有資料庫的伺服器:<br/><br/>* 大於 50 GB，或<br/>* 在非高階服務層<br/><br/>建立新的資料庫升級期間在伺服器上也會增加升級的時間。|
| 沒有異地複寫| 目前正在進行從 V11 升級的 V12 伺服器不支援異地複寫。|
| 資料庫在升級至 V12 的最終階段會短暫地無法使用| 屬於 V11 伺服器的資料庫在升級過程中仍可以使用。不過，伺服器和資料庫的連接是在最後一個階段中，當透過交換器會開始從 V11 準備 V12 短暫地無法存取。<br/><br/>40 秒的範圍期間切換到 5 分鐘。大多數伺服器的切換作業預期會在 90 秒內完成。如果有大量資料庫，或是當資料庫具有大量寫入工作負載時，則伺服器的切換時間會增加。|


### 升級至 V12 *之後*的限制

| 限制| 說明|
| :--- | :--- |
| 無法還原成 V11| 在升級完成之後，就無法還原或復原結果。|
| Web 或商務層| 在升級開始之後，新的 V12 資料庫的伺服器就無法再辨識或接受 Web 或商務服務層。|



### 在升級至 V12 *之後*匯出和匯入

您可以匯出或匯入 V12 資料庫使用 [Azure 入口網站](http://portal.azure.com/)。 或者，您可以使用任何下列工具進行匯出或匯入：


- SQL Server Management Studio (SSMS)
- Visual Studio 2013
- 資料層應用程式架構 (DacFx)


不過，若要使用這些工具，您必須先安裝其最新的更新，以確保它們支援新的 V12 功能：


- [SQL Server Management studio 2014 的累積更新 6](http://support2.microsoft.com/kb/3031047)
- [SQL Server 資料庫，在 Visual Studio 2013 工具的 2015 年 2 月更新](https://msdn.microsoft.com/data/hh297027)
- [2015 年 2 月資料層應用程式架構 (DacFx) 的 Azure SQL Database V12](http://www.microsoft.com/download/details.aspx?id=45886)


> [AZURE.NOTE] 上述工具連結已於 2015 年 3 月 2 日當天或之後更新。 我們建議您使用這些工具的較新更新。


#### 自動匯出

預覽仍然可以使用「自動匯出」。 使用「自動匯出」時不需要任何用戶端工具更新。 如果您選擇接受產生的檔案，並且匯入內部部署伺服器，則需要上面所列出的工具更新才能成功匯入。


### 將刪除的 V11 資料庫還原至 V12

下列案例說明已刪除的 V11 Azure SQL Database 可以還原至 V12 Azure SQL Database 伺服器。

1. 假設您有一部 V11 Azure SQL Database 伺服器。 <br/> 伺服器中，您會有一個資料庫在過時的 Web 和商務服務層。
2. 您刪除該資料庫。
3. 您將伺服器升級至 V12。
4. 接著，您將資料庫還原到該伺服器。 <br/> 資料庫因此會升級至 V12，Standard 服務層的 S0 層級。
5. 如果 S0 不是您偏好的選項，您可以將資料庫切換至任何支援的服務層。


### PowerShell Cmdlet

PowerShell Cmdlet 可用來啟動、停止或監視從 V11 或任何其他 V12 以前版本升級至 Azure SQL Database V12。

- [使用 PowerShell 進行升級至 SQL Database V12](sql-database-upgrade-server.md)

如需這些 PowerShell Cmdlet 的參考文件，請參閱：


- [Get AzureRMSqlServerUpgrade](https://msdn.microsoft.com/library/azure/mt603582.aspx)
- [開始 AzureRMSqlServerUpgrade](https://msdn.microsoft.com/library/azure/mt619403.aspx)
- [停止 AzureRMSqlServerUpgrade](https://msdn.microsoft.com/library/azure/mt603589.aspx)


Stop- Cmdlet 表示取消，不是暫停。 升級一旦停止就沒有任何方法可以繼續，只能再次從頭開始。 Stop- Cmdlet 會清除並釋放所有適當的資源。


## 失敗解決方式

如果升級因為任何異常原因而失敗，您的 V11 資料庫仍會保持作用中，並且能正常使用。


## 相關連結

- Microsoft Azure [預覽功能](http://azure.microsoft.com/services/preview/)




[subheading 1]: #subheading-1 

