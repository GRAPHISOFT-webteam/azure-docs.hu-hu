<properties 
    pageTitle="連接到 SQL Database：最佳做法 | Microsoft Azure" 
    description="針對從 ADO.NET 和 PHP 之類的技術連接到 Azure SQL Database 的用戶端程式，統合收集連結和最佳做法建議的入門主題。" 
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
    ms.date="11/13/2015" 
    ms.author="genemi"/>


# 連接到 SQL Database：最佳做法和設計方針


本主題是開始使用至 Azure SQL Database 的用戶端連線的好地方。 它提供指向各種技術之程式碼範例的連結，可用來連接到 SQL Database 並與其互動。 這些技術包括企業程式庫、JDBC 和 PHP 和更多技術。 所提供的資訊適用於您用來連接到 SQL Database 的所有特定技術。


<a id="a-tech-independent-recommend" name="a-tech-independent-recommend"></a>

## 與技術無關的建議


- [連接到 Azure SQL 資料庫以程式設計方式方針](http://msdn.microsoft.com/library/azure/ee336282.aspx) -討論包括 ︰
 - [連接埠和防火牆](sql-database-configure-firewall-settings.md)
 - 連接字串
- [Azure SQL Database 資源管理](http://msdn.microsoft.com/library/azure/dn338083.aspx) -討論包括 ︰
 - 資源管理
 - 限制強制執行
 - 節流


<a id="b-authentication-recommend" name="b-authentication-recommend"></a>

## 驗證建議


- 使用 Azure SQL Database 驗證，而不是 Azure SQL Database 中未提供的 Windows 驗證。
- 指定特定的資料庫，而非預設為 *主要* 資料庫。
 - 您無法使用 TRANSACT-SQL **使用 myDatabaseName;** 陳述式上切換到另一個資料庫的 SQL 資料庫。


### 自主使用者


將某人新增為 SQL Database 的使用者時，您有一些選擇：

- 新增 *登入* 寫密碼 **主要** 資料庫，然後再新增對應 *使用者* 到一或多個其他的資料庫相同的伺服器上。

- 新增 *內含的使用者* 具有一個或多個資料庫，並沒有連結至任何密碼 *登入* 中 **主要**。


自主使用者方法有其優點和缺點︰

- 優點是當資料庫中的所有使用者都是自主使用者時，資料庫即可輕鬆地在 Azure SQL Database 伺服器之間移動。

- 缺點是例行管理更加困難。 例如：
 - 卸除數個自主使用者比卸除一個登入更具挑戰性。
 - 身為數個資料庫中的自主使用者的人員可能需要記住或更新更多密碼。


提供進一步資訊- [所包含的資料庫使用者-讓您資料庫可攜式](http://msdn.microsoft.com/library/ff929188.aspx)。


<a id="c-connection-recommend" name="c-connection-recommend"></a>

## 連接建議


- 在您的用戶端連線邏輯中，將預設的逾時覆寫為 30 秒。
 - 預設 15 秒對於依賴網際網路的連線而言太短。


- 在託管您的用戶端程式的電腦上，請確定防火牆允許連接埠 1433 上的傳出 TCP 通訊。


- 如果您的用戶端在 Azure 虛擬機器 (VM) 上執行時，用戶端程式連接至 SQL Database V12，您就必須開啟此 VM 上 11000-11999 和 14000-14999 範圍的連接埠。 按一下 [ [這裡](sql-database-develop-direct-route-ports-adonet-v12.md) 如需詳細資訊。


- 若要處理 *暫時性錯誤*, ，加入 [*重試* 邏輯](#TransientFaultsAndRetryLogicGm) 用戶端程式與 Azure SQL Database 進行互動。


### 連接集區


如果您使用 [連接集區](http://msdn.microsoft.com/library/8xx3tyca.aspx), ，關閉的即時程式未主動使用它，而不準備重複使用該連線。

除非您的程式將立即重複使用其他作業的連線，而不暫停，否則我們建議使用下列模式：

- 開啟連線。
- 透過連線執行一項作業。
- 關閉連線。


### V12 中 1433 以外的連接埠


與 Azure SQL Database V12 的用戶端連線有時會略過 proxy 並直接與資料庫互動。 1433 以外的連接埠變得重要。 如需詳細資料，請參閱：<br/>
[針對 ADO.NET 4.5 及 SQL Database V12 的 1433 以外的連接埠](sql-database-develop-direct-route-ports-adonet-v12.md)


下一節會更詳細說明重試邏輯和暫時性錯誤處理。



<a name="TransientFaultsAndRetryLogicGm" id="TransientFaultsAndRetryLogicGm"></a>

&nbsp;

## 暫時性錯誤和重試邏輯


Azure 系統能夠在 SQL Database 服務出現繁重的工作負載時動態重新設定伺服器。

不過，重新設定可能會導致您的用戶端程式遺失其 SQL Database 連接。 此錯誤稱為 *暫時性錯誤*。

如果您的用戶端程式具有重試邏輯，在給予暫時性錯誤一些時間自行修正後，可以嘗試重新建立連接。

我們建議您在您第一次重試前延遲 5 秒鐘。 在少於 5 秒的延遲後重試，雲端服務會有超過負荷的風險。 對於後續每次重試，延遲應以指數方式成長，最大值為 60 秒。

討論 *封鎖期間* 使用 ADO.NET 的用戶端會提供 [SQL Server 連接共用 (ADO.NET)](http://msdn.microsoft.com/library/8xx3tyca.aspx)。


如需說明重試邏輯的程式碼範例，請參閱：
- [SQL Database 的用戶端快速入門程式碼範例](sql-database-develop-quick-start-client-code-samples.md)


### 暫時性錯誤的錯誤號碼


當使用 SQL 資料庫時，就會發生任何錯誤 [SqlException](http://msdn.microsoft.com/library/system.data.sqlclient.sqlexception.aspx) 就會擲回。  **SqlException** 包含數字錯誤碼，在其 **數目** 屬性。 如果錯誤碼識別暫時性錯誤，您的程式應該重試呼叫。


- [SQL Database 用戶端程式的錯誤訊息](sql-database-develop-error-messages.md#bkmk_connection_errors)
 - 其 **暫時性錯誤、 連接遺失錯誤** 區段是一個有理由自動重試的暫時性錯誤清單。
 - 例如，如果出現錯誤編號 40613，表示發生類似如下的狀況，則重試：<br/>*資料庫 'mydatabase' 伺服器 'theserver' 上不是目前可用的。*


如需進一步資訊，請參閱：
- [Azure SQL Database 開發：使用說明主題](http://msdn.microsoft.com/library/azure/ee621787.aspx)
- [疑難排解 Azure SQL Database 的連線問題](http://support.microsoft.com/kb/2980233/)


<a id="e-technologies" name="e-technologies"></a>

## 技術


下列主題包含數個語言和驅動程式技術的程式碼範例連結，可讓您從用戶端程式連接到 Azure SQL Database。


會針對在 Windows、Linux 和 Mac OS X 上執行的用戶端提供各種程式碼範例。


**一般範例 ︰** 有 [程式碼範例](sql-database-develop-quick-start-client-code-samples.md) 各種不同的程式設計語言，包括 PHP、 Python、 Node.js 和.NET CSharp。 此外，也提供在 Windows、Linux 和 Mac OS X 上執行的用戶端範例。


**彈性延展 ︰** 彈性擴縮資料庫的連線相關資訊，請參閱 ︰

- [開始使用 Azure SQL Database Elastic Scale 預覽](sql-database-elastic-scale-get-started.md)
- [資料相依路由](sql-database-elastic-scale-data-dependent-routing.md)


**驅動程式庫 ︰** 如需連接驅動程式庫的詳細資訊，包括建議的版本，請參閱 ︰

- [SQL Database 和 SQL Server 的連線庫](sql-database-libraries.md)



