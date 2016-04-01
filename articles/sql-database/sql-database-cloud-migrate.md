<properties
   pageTitle="將 SQL Server Database 移轉到 Azure SQL Database"
   description="Microsoft Azure SQL Database、資料庫部署、資料庫移轉、匯入資料庫，匯出資料庫、移轉精靈"
   services="sql-database"
   documentationCenter=""
   authors="carlrabeler"
   manager="jeffreyg"
   editor=""/>

<tags
   ms.service="sql-database"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="data-management"
   ms.date="10/12/2015"
   ms.author="carlrab"/>

# 將 SQL Server Database 移轉到 Azure SQL Database

將內部部署資料庫移到 Azure SQL Database 的複雜度，根據您的資料庫和應用程式設計，以及您對於停機時間的容忍度而異。 對相容的資料庫而言，移轉至 Azure SQL Database 是直接的結構描述和資料移動作業，對結構描述的變更很少 (如果有的話)，而且重新設計應用程式的幅度也很小或甚至不需要。 [Azure SQL Database V12](../sql-database-v12-whats-new.md) 提供與 SQL Server 2014 和 SQL Server 2016 的幾乎完全的引擎相容性。 Microsoft Azure SQL Database 中完全支援大多數的 SQL Server 2016 Transact-SQL 陳述式。 這包括 SQL Server 資料類型、運算子和字串、算術、邏輯、指標函式和大部分應用程式相依的其他 Transact-SQL 元素。 部分支援或不支援的函式通常與 SQL Database 如何管理資料庫的差異 (例如檔案、高可用性和安全性功能) 相關，或與 service broker 的特殊目的功能相關。 因為 SQL Database 會隔離許多功能與 master 資料庫的相依性，許多伺服器層級活動是不當且不受支援的。 SQL Server 中已被取代的功能在 SQL Database 中通常不受支援。 資料庫和應用程式依賴 [部分或不支援函式](../sql-database-transact-sql-information.md) 需要稍微重新設計，才可以移轉。

將 SQL Server Database 移轉到 Azure SQL Database 的工作流程為：

 1. [判斷您的資料庫是否相容](#determine-if-your-database-is-compatible)
 2. [如果不相容，請修正資料庫相容性問題](#fix-database-compatibility-issues)
 3. [移轉相容的資料庫](#options-to-migrate-a-compatible-database-to-azure-sql-database)

>[AZURE.NOTE] 若要移轉其他類型的資料庫，包括 Microsoft Access、 Sybase、 MySQL Oracle 和 DB2 到 Azure SQL Database，請參閱 [SQL Server 移轉小幫手](http://blogs.msdn.com/b/ssma/)。

## 判斷您的資料庫是否相容
有兩種主要的方法，可用來判斷您的來源資料庫是否相容。
- 匯出資料層應用程式 ︰ 這個方法在 Management Studio 中使用精靈，來分析您的資料庫，並顯示資料庫的相容性問題，如果有的話，在主控台上。
- SQLPackage ︰ 這個方法會使用 SQLPackage.exe [sqlpackage.exe](https://msdn.microsoft.com/library/hh550080.aspx) 命令列公用程式來分析您的資料庫及產生報告。 SQLPackage 隨附於 Visual Studio 和 SQL Server。

> [AZURE.NOTE] 沒有將使用追蹤檔案做為其他來源的資訊來測試應用程式層級，以及在資料庫層級的相容性的第三個方法。 這是 [SQL Azure 移轉精靈](http://sqlazuremw.codeplex.com/), ，Codeplex 上的免費工具。 不過，此工具目前可能會發現相容性錯誤，對 Azure SQL Database V11 會構成問題，但是對 Azure SQL Database V12 不會構成問題。

如果偵測到資料庫不相容，您必須先修正這些不相容，才能將資料庫移轉至 Azure SQL Database。 如需有關如何修正資料庫相容性問題的指引，請移至 [修正資料庫相容性問題](#fix-database-compatibility-issues)。

> [AZURE.IMPORTANT] 這些選項不會攔截所有相容性問題之間的 SQL Server 資料庫的不同層級 （也就是層級 90、 100 和 110）。 如果您從較舊的資料庫 (等級 80、90、100 和 110) 進行移轉，您應先完成升級程序 (至少在開發環境中)，並在 SQL Server 2014 或更新版本完成一次，然後移轉到 Azure SQL Database。

## 使用 sqlpackage.exe 判斷您的資料庫是否相容

1. 開啟命令提示字元並變更包含最新版 sqlpackage.exe 的目錄。 此公用程式隨附於 Visual Studio 和 SQL Server。 您也可以 [下載](https://msdn.microsoft.com/library/mt204009.aspx) 最新版的 SQL Server Data Tools，若要取得此公用程式。
2. 以環境的下列引數執行下列 sqlpackage.exe 命令：

    ' sqlpackage.exe /Action:Export /ssn: < 伺服器名稱 > /sdn: < database_name > /tf: < target_file > /p: tabledata = < schema_name.table_name >>< 輸出檔案 > 2 > & 1'

  	| 引數  | 說明  |
  	|---|---|
  	| < 伺服器名稱 >  | 來源伺服器名稱  |
  	| < > database_name  | 來源資料庫名稱  |
  	| < > target_file  | BACPAC 檔案的檔案名稱和位置  |
  	| < > schema_name.table_name  | 其資料將會輸出到目標檔案的資料表  |
  	| < 輸出檔案 >  | 具有錯誤的輸出檔案的檔案名稱和位置 (若有的話)  |

    使用 /P:TableName 引數的原因是我們只想要測試資料庫相容性以匯出至 Azure SQL DB V12，而不是從所有資料表匯出資料。 不幸的是，sqlpackage.exe 的匯出引數不支援不擷取任何資料表，因此您必須指定單一的小型資料表。 < 輸出檔案 > 將包含報表的任何錯誤。 "> 2>&1" 字串會將從命令執行產生的標準輸出和標準錯誤輸送到指定的輸出檔。

    ![從 [工作] 功能表匯出資料層應用程式](./media/sql-database-cloud-migrate/TestForCompatibilityUsingSQLPackage01.png)

3. 開啟輸出檔案並檢視相容性錯誤 (如果有的話)。 如需有關如何修正資料庫相容性問題的指引，請移至 [修正資料庫相容性問題](#fix-database-compatibility-issues)。

    ![從 [工作] 功能表匯出資料層應用程式](./media/sql-database-cloud-migrate/TestForCompatibilityUsingSQLPackage02.png)

## 使用匯出資料層應用程式判斷您的資料庫是否相容

1. 確認您擁有 13.0.600.65 版或更新版本的 SQL Server Management Studio。 新版的 Management Studio 會每月更新以維持與 Azure 入口網站的更新同步。

     > [AZURE.IMPORTANT] 下載 [最新](https://msdn.microsoft.com/library/mt238290.aspx) 版本的 SQL Server Management Studio。 建議您一律使用最新版本的 Management Studio。

2. 開啟 Management Studio 並連接到您在 [物件總管] 中的來源資料庫。
3. 在 [物件總管] 中的來源資料庫上按一下滑鼠右鍵，指向 **工作**, ，然後按一下 **匯出資料層應用程式...**

    ![從 [工作] 功能表匯出資料層應用程式](./media/sql-database-cloud-migrate/TestForCompatibilityUsingSSMS01.png)

4. 在 [匯出] 精靈中，按一下 [ **下一步**, ，然後在 **設定** 索引標籤上，設定匯出，將 BACPAC 檔案儲存到本機磁碟的位置或 Azure blob。 如果您有沒有資料庫相容性問題，才會儲存 BACPAC 檔案。 如果有相容性問題，它們將顯示在主控台上。

    ![匯出設定](./media/sql-database-cloud-migrate/TestForCompatibilityUsingSSMS02.png)

5. 按一下 [ **進階] 索引標籤** 清除 **全選** 核取方塊以略過匯出資料。 此時我們的目標是只測試相容性。

    ![匯出設定](./media/sql-database-cloud-migrate/TestForCompatibilityUsingSSMS03.png)

6. 按一下 [ **下一步** 然後按一下 [ **完成**。 資料庫相容性問題 (如果有的話) 將會在精靈驗證結構描述之後出現。

    ![匯出設定](./media/sql-database-cloud-migrate/TestForCompatibilityUsingSSMS04.png)

7. 如果沒有任何錯誤出現，代表您的資料庫相容且您已準備好移轉。 如果您有錯誤，您必須加以修正。 若要查看錯誤，請按一下 [ **錯誤** 的 **驗證結構描述**。 如何修正這些錯誤，請移至 [修正資料庫相容性問題](#fix-database-compatibility-issues)。

    ![匯出設定](./media/sql-database-cloud-migrate/TestForCompatibilityUsingSSMS05.png)

8.  如果 *.BACPAC 檔案已成功產生，則您的資料庫與 SQL Database 相容，且您已準備好移轉。

## 將相容資料庫移轉至 Azure SQL Database 的選項

您已經確認具有相容的資料庫之後，您需要選擇您的移轉方法。 首先，您需要決定是否經得起在移轉期間將資料庫移出實際執行環境之外。 如果不能，請使用下面討論的 SQL Server 交易複寫。 如果您可以負擔一些停機時間，或者您是執行實際執行資料庫的測試移轉，您稍後可能會選擇使用交易複寫進行移轉，請考慮下列三種方法之一。

### 在具有停機時間的情況下，移轉相容資料庫   
下面清單討論當移轉發生時，以及在將使用者和應用程式指向 Azure SQL Database 中的移轉資料庫之前，您可以負擔一些停機時間的情況下，將相容資料庫移轉至 Azure SQL Database 的選項。 使用這些方法，您會在資料庫存在的某個時間點移轉資料庫。

> [AZURE.WARNING] 之前使用任何一種方法將資料庫移轉，請確定以確保交易一致性，在移轉期間發生的任何使用中交易。 有許多方法來停止資料庫，無法停用用戶端連線到建立 [資料庫快照集](https://msdn.microsoft.com/library/ms175876.aspx)。

- 適用於小型到中型的資料庫，移轉 [相容](#determine-if-your-database-is-compatible) SQL Server 2005 或更新版本的資料庫很簡單，只要執行 [將資料庫部署到 Microsoft Azure 資料庫精靈](#use-deploy-database-to-microsoft-azure-database-wizard) 在 SQL Server Management Studio。 如果您有連線挑戰 （沒有連線能力、 低頻寬或逾時問題），您可以 [移轉使用 BACPAC](#use-a-bacpac-to-migrate-a-sql-server-database-to-azure-sql-database) 到 Azure SQL Database 的 SQL Server 資料庫。
- 中型至大型資料庫，或當您擁有連線挑戰 [移轉使用 BACPAC](#use-a-bacpac-to-migrate-a-sql-server-database-to-azure-sql-database) 到 Azure SQL Database 的 SQL Server 資料庫。 使用此方法，您可以使用 SQL Server Management Studio 匯出的資料和結構描述以 [BACPAC](https://msdn.microsoft.com/library/ee210546.aspx#Anchor_4) 檔案 （儲存在本機或 Azure blob），然後匯入 BACPAC 檔案到您的 Azure SQL 執行個體。 如果您在 Azure blob 儲存 BACPAC，您也可以匯入 BACPAC 檔案從 [Azure 入口網站](sql-database-import.md) 或 [使用 PowerShell](sql-database-import-powershell.md)。
- 對於較大的資料庫，您可以分別移轉結構描述和資料，以達到最佳效能。 使用此方法，建立 [BACPAC 檔案，但不含資料](#use-a-bacpac-to-migrate-a-sql-server-database-to-azure-sql-database) 這個 BACPAC 匯入 Azure SQL Database。 已匯入到 Azure SQL 資料庫的結構描述之後，就使用 [BCP](https://msdn.microsoft.com/library/ms162802.aspx) 成一般檔案擷取資料，然後將這些檔案匯入 Azure SQL Database。

     ![SSMS 移轉圖表](./media/sql-database-cloud-migrate/01SSMSDiagram_new.png)

### 在沒有停機時間的情況下，移轉相容資料庫

當您在移轉發生時無法負擔從實際執行中移除 SQL Server 資料庫，可以使用 SQL Server 交易複寫做為移轉解決方案。 使用交易複寫，開始移轉和完成移轉之間發生的資料或結構描述的所有變更都會出現在您的 Azure SQL Database。 移轉完成之後，您只需要變更您的應用程式的連接字串，將其指向您的 Azure SQL Database，而不是指向您的內部部署資料庫。 一旦交易複寫清空留在內部部署資料庫的任何變更，且所有應用程式指向 Azure DB，您現在可以安全地解除安裝複寫，讓您的 Azure SQL Database 做為實際執行系統。

 ![SeedCloudTR 圖表](./media/sql-database-cloud-migrate/SeedCloudTR.png)


交易複寫是內建技術，與 SQL Server 6.5 之後的 SQL Server 整合。 它是大部分 DBA 知道他們會體驗到的非常成熟且經過實證的技術。 使用 [SQL Server 2016 preview](http://www.microsoft.com/server-cloud/products/sql-server-2016/), ，您就可以設定為您 Azure SQL Database [交易式複寫訂閱者](https://msdn.microsoft.com/library/mt589530.aspx) 您在內部發行集。 您從 Management Studio 設定它的經驗，與在內部部署伺服器上設定交易複寫訂閱者完全相同。 下列 SQL Server 版本支援此案例：

 - SQL Server 2016 CTP3 (預覽版) 和更新版本 
 - SQL Server 2014 SP1 CU3 和更新版本
 - SQL Server 2014 RTM CU10 和更新版本
 - SQL Server 2012 SP2 CU8 和更新版本
 - SQL Server 2013 SP3 (當它發行時)

您也可以使用交易複寫以移轉內部部署資料庫的子集。 您複寫至 Azure SQL Database 的發佈可以限制為複寫的資料庫中資料表的子集。 此外，對於複寫的每一個資料表，您可以將資料限制為資料列的子集和 (或) 資料行的子集。

## 使用將資料庫部署到 Microsoft Azure Database 精靈

將資料庫部署到 SQL Server Management Studio 中的 Microsoft Azure 資料庫精靈 」 會移轉的移轉 [相容](#determine-if-your-database-is-compatible) SQL Server 2005 或更新版本的資料庫直接到 Azure SQL 邏輯伺服器執行個體。

> [AZURE.NOTE] 下列步驟假設您已經有 [佈建](../sql-database-get-started.md) 邏輯您 Azure SQL 執行個體，並且手邊的連接資訊。

1. 確認您擁有 13.0.600.65 版或更新版本的 SQL Server Management Studio。 新版的 Management Studio 會每月更新以維持與 Azure 入口網站的更新同步。

     > [AZURE.IMPORTANT] 下載 [最新](https://msdn.microsoft.com/library/mt238290.aspx) 版本的 SQL Server Management Studio。 建議您一律使用最新版本的 Management Studio。

2. 開啟 Management Studio 並連接到您在 [物件總管] 中的來源資料庫。
3. 在 [物件總管] 中的來源資料庫上按一下滑鼠右鍵，指向 **工作**, ，然後按一下 **將資料庫部署到 Microsoft Azure SQL Database...**

    ![從 [工作] 功能表部署至 Azure](./media/sql-database-cloud-migrate/MigrateUsingDeploymentWizard01.png)

4.  在 [部署精靈] 中，按一下 [ **下一步**, ，然後按一下 [ **連接** 來設定您的 Azure SQL 資料庫伺服器的連線。

    ![從 [工作] 功能表部署至 Azure](./media/sql-database-cloud-migrate/MigrateUsingDeploymentWizard002.png)

5. 在 [連接到伺服器] 對話方塊中，輸入連接到 Azure SQL Database 伺服器的連接資訊。

    ![從 [工作] 功能表部署至 Azure](./media/sql-database-cloud-migrate/MigrateUsingDeploymentWizard00.png)

5.  提供 **新的資料庫名稱** Azure SQL DB 上的資料庫，設定 **版本的 Microsoft Azure SQL Database** （服務層） **最大資料庫大小**, ，**服務目標** （效能層級） 和 **暫存檔名稱** 此精靈會在移轉程序期間建立的 BACPAC 檔案。 請參閱 [Azure SQL Database 服務層](sql-database-service-tiers.md) 如需有關服務層和效能層級。

    ![匯出設定](./media/sql-database-cloud-migrate/MigrateUsingDeploymentWizard02.png)

6.  完成精靈以移轉資料庫。 移轉時間取決資料庫部署的大小及複雜性，可能需要數分鐘到數小時。
7.  使用 [物件總管] 時，請連接到 Azure SQL Database 伺服器中已移轉的資料庫。
8.  使用 Azure 入口網站時，請檢視您的資料庫和它的屬性。

## 使用 BACPAC 將 SQL Server Database 移轉到 Azure SQL Database

對於中型至大型資料庫，或當您遭遇連線挑戰時，您可以將移轉程序分成兩個個別的步驟。 您可以將匯出的結構描述和資料 [BACPAC](https://msdn.microsoft.com/library/ee210546.aspx#Anchor_4) 檔案中使用一個或兩個方法。

- [使用 SQL Server Management Studio 匯出至 BACPAC 檔案](#export-a-compatible-sql-server-database-to-a-bacpac-file-using-sql-server-management-studio)
- [使用 SqlPackage 匯出至 BACPAC](#export-a-compatible-sql-server-database-to-a-bacpac-file-using-sqlpackage)

您可以在本機或 Azure blob 中儲存這個 BACPAC。 然後您可以使用數種方法中的其中一種，將這個 BACPAC 檔案匯入至 Azure SQL Database。

- [使用 SQL Server Management Studio 從 BACPAC 檔案匯入至 Azure SQL Database](#import-from-a-bacpac-file-into-azure-sql-database-using-sql-server-management-studio)
- [使用 SqlPackage 從 BACPAC 檔案匯入至 Azure SQL Database](#import-from-a-bacpac-file-into-azure-sql-database-using-sqlpackage)
- [使用 Azure 入口網站從 BACPAC 檔案匯入至 Azure SQL Database](sql-database-import.md)
- [使用 PowerShell 從 BACPAC 檔案匯入至 Azure SQL Database](sql-database-import-powershell.md)

## 使用 SQL Server Management Studio 將相容的 SQL Server Database 匯出到 BACPAC 檔案

使用下列步驟來使用 Management Studio 匯出移轉 [相容](#determine-if-your-database-is-compatible) 到 BACPAC 檔案的 SQL Server 資料庫。

1. 確認您擁有 13.0.600.65 版或更新版本的 SQL Server Management Studio。 新版的 Management Studio 會每月更新以維持與 Azure 入口網站的更新同步。

     > [AZURE.IMPORTANT] 下載 [最新](https://msdn.microsoft.com/library/mt238290.aspx) 版本的 SQL Server Management Studio。 建議您一律使用最新版本的 Management Studio。

2. 開啟 Management Studio 並連接到您在 [物件總管] 中的來源資料庫。

    ![從 [工作] 功能表匯出資料層應用程式](./media/sql-database-cloud-migrate/MigrateUsingBACPAC01.png)

3. 在 [物件總管] 中的來源資料庫上按一下滑鼠右鍵，指向 **工作**, ，然後按一下 **匯出資料層應用程式...**

    ![從 [工作] 功能表匯出資料層應用程式](./media/sql-database-cloud-migrate/TestForCompatibilityUsingSSMS01.png)

4. 在匯出精靈中，將匯出設定為將 BACPAC 檔案儲存到本機磁碟位置或 Azure Blob。 匯出的 BACPAC 一律會包含完整的資料庫結構描述，以及預設之所有資料表的資料。 如果您想要排除部分或所有資料表的資料，請使用 [進階] 索引標籤。 比方說，您可能會選擇只匯出參考資料表的資料，而不是所有資料表的資料。

    ![匯出設定](./media/sql-database-cloud-migrate/MigrateUsingBACPAC02.png)

## 使用 SqlPackage 將相容的 SQL Server Database 匯出到 BACPAC 檔案

使用下列步驟來使用 [SqlPackage.exe](https://msdn.microsoft.com/library/hh550080.aspx) 命令列公用程式匯出移轉 [相容](#determine-if-your-database-is-compatible) 到 BACPAC 檔案的資料庫。

> [AZURE.NOTE] 下列步驟假設您已佈建 Azure SQL Database 伺服器、 手邊的連接資訊，並確認您的來源資料庫是相容。

1. 開啟命令提示字元並變更包含 sqlpackage.exe 命令列公用程式的目錄 - 此公用程式隨附於 Visual Studio 和 SQL Server。
2. 以環境的下列引數執行下列 sqlpackage.exe 命令：

    ' sqlpackage.exe /Action:Export /ssn: < 伺服器名稱 > /sdn: < database_name > /tf: < target_file >

  	| 引數  | 說明  |
  	|---|---|
  	| < 伺服器名稱 >  | 來源伺服器名稱  |
  	| < > database_name  | 來源資料庫名稱  |
  	| < > target_file  | BACPAC 檔案的檔案名稱和位置  |

    ![從 [工作] 功能表匯出資料層應用程式](./media/sql-database-cloud-migrate/TestForCompatibilityUsingSQLPackage01b.png)

## 使用 SQL Server Management Studio 從 BACPAC 檔案匯入至 Azure SQL Database

使用下列步驟從 BACPAC 檔案匯入至 Azure SQL Database。

> [AZURE.NOTE] 下列步驟假設您已佈建您的 Azure SQL 邏輯執行個體和手邊的連接資訊。

1. 確認您擁有 13.0.600.65 版或更新版本的 SQL Server Management Studio。 新版的 Management Studio 會每月更新以維持與 Azure 入口網站的更新同步。

    > [AZURE.IMPORTANT] 下載 [最新](https://msdn.microsoft.com/library/mt238290.aspx) 版本的 SQL Server Management Studio。 建議您一律使用最新版本的 Management Studio。

2. 開啟 Management Studio 並連接到您在 [物件總管] 中的來源資料庫。

    ![從 [工作] 功能表匯出資料層應用程式](./media/sql-database-cloud-migrate/MigrateUsingBACPAC01.png)

3. 一旦建立 BACPAC，連接到 Azure SQL Database 伺服器，請以滑鼠右鍵按一下 **資料庫** 資料夾，然後按一下 **匯入資料層應用程式...**

    ![匯入資料層應用程式功能表項目](./media/sql-database-cloud-migrate/MigrateUsingBACPAC03.png)

4.  在匯入精靈中，選擇剛才匯出的 BACPAC 檔案，即可在 Azure SQL Database 中建立新的資料庫 。

    ![匯入設定](./media/sql-database-cloud-migrate/MigrateUsingBACPAC04.png)

5.  提供 **新的資料庫名稱** Azure SQL DB 上的資料庫，設定 **版本的 Microsoft Azure SQL Database** （服務層） **最大資料庫大小** 和 **服務目標** （效能層級）。

    ![資料庫設定](./media/sql-database-cloud-migrate/MigrateUsingBACPAC05.png)

6.  按一下 [ **下一步** 然後按一下 [ **完成** BACPAC 檔案匯入到 Azure SQL Database 伺服器中的新資料庫。

7. 使用 [物件總管] 時，請連接到 Azure SQL Database 伺服器中已移轉的資料庫。

8.  使用 Azure 入口網站時，請檢視您的資料庫和它的屬性。

## 使用 SqlPackage 從 BACPAC 檔案匯入至 Azure SQL Database

使用下列步驟來使用 [SqlPackage.exe](https://msdn.microsoft.com/library/hh550080.aspx) 命令列公用程式從 BACPAC 檔案匯入相容的 SQL Server 資料庫 （或 Azure SQL database）。

> [AZURE.NOTE] 下列步驟假設您已佈建 Azure SQL Database 伺服器和手邊的連接資訊。

1. 開啟命令提示字元並變更包含 sqlpackage.exe 命令列公用程式的目錄 - 此公用程式隨附於 Visual Studio 和 SQL Server。
2. 以環境的下列引數執行下列 sqlpackage.exe 命令：

    ' sqlpackage.exe /Action:Import /tsn: < 伺服器名稱 > /tdn: < database_name > /tu: < s e _ > /tp: < 密碼 > /sf: < source_file >

  	| 引數  | 說明  |
  	|---|---|
  	| < 伺服器名稱 >  | 目標伺服器名稱  |
  	| < > database_name  | 目標資料庫名稱  |
  	| < s e _ >  | 目標伺服器中的使用者名稱 |
  	| < 密碼 >  | 使用者的密碼  |
  	| < > source_file  | 匯入的 BACPAC 檔案的檔案名稱和位置  |

    ![從 [工作] 功能表匯出資料層應用程式](./media/sql-database-cloud-migrate/TestForCompatibilityUsingSQLPackage01c.png)


## 修正資料庫相容性問題

如果您判斷您的來源 SQL Server 資料庫不相容，您有許多選項可修復的資料庫相容性問題的 [先前找出](#determine-if-your-database-is-compatible)。

- 使用 [SQL Azure 移轉精靈](http://sqlazuremw.codeplex.com/)。 您可以使用 Codeplex 工具從不相容的來源資料庫產生 T-SQL 指令碼，精靈會轉換該資料庫使其與 SQL Database 相容，然後連接至 Azure SQL Database 以執行此指令碼。 這項工具也會分析追蹤檔案以判斷相容性問題。 只能使用結構描述產生指令碼，或者指令碼可以 BCP 格式包含資料。 其他文件，包括逐步指引可用 codeplex [SQL Azure 移轉精靈](http://sqlazuremw.codeplex.com/)。  

 ![SAMW 移轉圖表](./media/sql-database-cloud-migrate/02SAMWDiagram.png)

 > [AZURE.NOTE] 請注意，並非所有不相容的結構描述可以偵測到此精靈可處理的內建轉換。 無法處理的不相容指令碼將會報告為錯誤，並在產生的指令碼中插入註解。 如果偵測到許多錯誤，請使用 Visual Studio 或 SQL Server Management Studio 來逐步執行和修正無法使用 SQL Server 移轉精靈修正的每個錯誤。

- 使用 Visual Studio。 您可以使用 Visual Studio 將資料庫結構描述匯入 Visual Studio 資料庫專案以進行分析。 若要進行分析，請將專案的目標平台指定為 SQL Database V12，然後再建置專案。 如果建置成功，則資料庫為相容。 如果建置失敗，您可以使用 Visual Studio 的 SQL Server Data Tools ("SSDT") 解決錯誤。 一旦專案成功建置，您可以將該專案發佈回來做為來源資料庫的複本，然後使用 SSDT 中的資料比較功能，將資料從來源資料庫複製到 Azure SQL V12 相容資料庫。 此更新的資料庫接著會部署至 Azure SQL Database 使用選項 [先前討論](#options-to-migrate-a-compatible-database-to-azure-sql-database)。

 ![VSSSDT 移轉圖表](./media/sql-database-cloud-migrate/03VSSSDTDiagram.png)

 > [AZURE.NOTE] 如果需要僅限結構描述的移轉，則結構描述可以直接從 Visual Studio 直接發行至 Azure SQL Database。 當資料庫結構描述需要的變更超過移轉精靈單獨可處理的數量時，使用這個方法。

- SQL Server Management Studio。 您可以修正問題在 Management Studio 中使用各種 TRANSACT-SQL 命令，例如 **ALTER DATABASE**。


