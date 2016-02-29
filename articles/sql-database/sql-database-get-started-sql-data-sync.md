<properties
    pageTitle="開始使用 SQL 資料庫資料同步"
    description="本教學課程可協助您開始使用 Azure SQL 資料同步 (預覽)。"
    services="sql-database"
    documentationCenter=""
    authors="spelluru"
    manager="JennieHubbard"
    editor=""/>

<tags
    ms.service="sql-database"
    ms.workload="data-management"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="10/27/2015"
    ms.author="jhubbard"/>


#開始使用 Azure SQL 資料同步 (預覽)
在本教學課程中，您將了解使用 Azure 傳統入口網站的 Azure SQL 資料同步基本概念。

本教學課程假設學員只有最少的 SQL Server 和 Azure SQL Database 使用經驗。 在本教學課程中，您將建立已完全設定的混合 (SQL Server 和 SQL Database 案例) 同步群組，並依照您設定的排程進行同步。

> [AZURE.NOTE] 設定 Azure SQL 資料同步，先前位於 MSDN 上的完整技術文件現在是您下載單一.pdf [這裡](http://download.microsoft.com/download/4/E/3/4E394315-A4CB-4C59-9696-B25215A19CEF/SQL_Data_Sync_Preview.pdf)。

## 步驟 1：連接到 Azure SQL Database

1. 登入 [傳統入口網站](http://manage.windowsazure.com)。

2. 按一下 [ **SQL 資料庫** 的左窗格中。

3. 按一下 [ **同步** 頁面的底部。 當您按一下 [同步處理，此時會顯示項目，您可以加入- **新的同步處理群組** 和 **新增同步代理程式**。

4. 若要啟動新的 SQL 資料同步代理程式精靈，請按一下 [ **新增同步代理程式**。

5. 如果尚未新增代理程式之前， **按一下這裡下載**。

    ![Image1](./media/sql-database-get-started-sql-data-sync/SQLDatabaseScreen-Figure1.PNG)


## 步驟 2：新增用戶端代理程式
只有在您要將內部部署 SQL Server 資料庫納入同步群組時，才需要執行這個步驟。 
如果同步群組只有 SQL Database 執行個體，請跳到步驟 4。

<a id="InstallRequiredSoftware"></a>
### 步驟 2a：安裝必要的軟體
請務必在您安裝用戶端代理程式的電腦上安裝下列項目。

- **.NET Framework 4.0**

 您可以安裝.NET Framework 4.0 從 [這裡](http://go.microsoft.com/fwlink/?linkid=205836)。

- **Microsoft SQL Server 2008 R2 SP1 系統 CLR 類型 (x86)**

 您可以從安裝 Microsoft SQL Server 2008 R2 SP1 系統 CLR 類型 (x86)  [這裡](http://www.microsoft.com/download/en/details.aspx?id=26728)

- **Microsoft SQL Server 2008 R2 SP1 共用管理物件 (x86)**

 您可以從安裝 Microsoft SQL Server 2008 R2 SP1 共用管理物件 (x86) [這裡](http://www.microsoft.com/download/en/details.aspx?id=26728)



<a id="InstallClient"></a>
### 步驟 2b：安裝新的用戶端代理程式

依照 [安裝用戶端代理程式 (SQL 資料同步)](http://download.microsoft.com/download/4/E/3/4E394315-A4CB-4C59-9696-B25215A19CEF/SQL_Data_Sync_Preview.pdf) 安裝代理程式。



<a id="RegisterSSDb"></a>
### 步驟 2c：完成新增 SQL 資料同步代理程式精靈

1.  返回 [新增 SQL 資料同步代理程式] 精靈。
2.  給予代理程式有意義的名稱。
3.  從下拉式清單中，選取 **區域** (資料中心) 以裝載此代理程式。
4.  從下拉式清單中，選取 **訂閱** 以裝載此代理程式。
5.  按一下向右箭頭。



## 步驟 3：向用戶端代理程式註冊 SQL Server 資料庫

安裝用戶端代理程式之後，註冊您要納入代理程式之同步群組中的每個內部部署 SQL Server 資料庫。
若要向代理程式註冊資料庫，請遵循的指示 [用戶端代理程式註冊 SQL Server 資料庫](http://download.microsoft.com/download/4/E/3/4E394315-A4CB-4C59-9696-B25215A19CEF/SQL_Data_Sync_Preview.pdf)。



## 步驟 4：建立同步群組


<a id="StartNewSGWizard"></a>
### 步驟 4a：啟動新增同步群組精靈

1.  返回 [傳統入口網站](http://manage.windowsazure.com)。
2.  按一下 [ **SQL 資料庫**。
3.  按一下 [ **加入同步** 頁面的底部，然後選取新的同步處理群組 」 下拉式清單中。

    ![Image2](./media/sql-database-get-started-sql-data-sync/NewSyncGroup-Figure2.png)



<a id=""></a>
### 步驟 4b：輸入基本設定


1.  為同步群組輸入有意義的名稱。
2.  從下拉式清單中，選取 **區域** (資料中心) 以裝載此同步群組。
3. 按一下向右箭頭。

    ![Image3](./media/sql-database-get-started-sql-data-sync/NewSyncGroupName-Figure3.PNG)


<a id="DefineHubDB"></a>
### 步驟 4c：定義同步中樞

1. 從下拉式清單中選取要作為同步群組中心的 SQL Database 執行個體。
2. 此 SQL 資料庫執行個體-輸入的認證 **HUB USERNAME** 和 **HUB PASSWORD**。
3. 等待 SQL 資料同步確認使用者名稱和密碼。 確認認證後，密碼的右邊會出現綠色核取記號。
4. 從下拉式清單中，選取 **衝突解決** 原則。

 **Hub Wins** -寫入中心資料庫中的任何變更都會寫入參考資料庫，並覆寫相同參考資料庫記錄的變更。 在功能上，這表示寫入中心的第一項變更會傳播至其他資料庫。


 **用戶端獲勝** -寫入中心的變更會覆寫參考資料庫中的變更。 在功能上，這表示寫入中心的最後一項變更會是保留並傳播至其他資料庫的變更。

5.  按一下向右箭頭。

    ![Image4](./media/sql-database-get-started-sql-data-sync/NewSyncGroupHub-Figure4.PNG)

<a id="AddRefDB"></a>
### 步驟 4d：新增參考資料庫


對您要新增至同步群組的其他各個資料庫，重複執行此步驟。

1. 從下拉式清單中選取要新增的資料庫。

    下拉式清單中的資料庫包括已向代理程式和 SQL Database 執行個體註冊的 SQL Server 資料庫。
2.  輸入此資料庫中的認證 **USERNAME** 和 **密碼**。
3.  從下拉式清單中，選取 **同步處理方向** 此資料庫。

    **雙向** -參考資料庫中的變更會寫入中心資料庫中，並對中心資料庫的變更會寫入參考資料庫。

    **從中樞同步處理** -資料庫收到來自中心的更新。 但不會將變更傳送至中心。

    **同步處理到中樞** -資料庫將更新傳送到中心。 但中心的變更不會寫入此資料庫中。

4.  若要完成同步群組的建立，請按一下精靈右下方的核取記號。 等待 SQL 資料同步確認認證。 綠色核取記號表示已確認認證。

5.  再按一次核取記號。 這會讓您回到 **同步** 下 SQL 資料庫] 頁面。 此同步群組現在會與您的其他同步群組和代理程式列在一起。

    ![Image5](./media/sql-database-get-started-sql-data-sync/NewSyncGroupReference-Figure5.PNG)


## 步驟 5：定義要同步的資料

Azure SQL 資料同步可讓您選取要同步的資料表和資料欄。 如果您也想篩選資料欄，僅只同步具有特定值 (例如，年齡 >=65) 的資料列，請使用 Azure 的 SQL 資料同步入口網站以及「選取要同步的資料表、資料欄和資料列」的文件，以定義要同步的資料。

1.  返回 [傳統入口網站](http://manage.windowsazure.com)。
2.  按一下 [ **SQL 資料庫**。
3.  按一下 [ **同步** ] 索引標籤。
4.  按一下此同步群組的名稱。
5.  按一下 [ **同步處理規則** ] 索引標籤。
6.  選取您要提供同步群組結構描述的資料庫。
7.  按一下向右箭頭。
8.  按一下 [ **重新整理結構描述**。
9.  對於資料庫中的每個資料表，選取要納入同步處理的資料欄。
    - 無法選取具有不支援資料類型的資料欄。
    - 如果未選取資料表中的任何資料欄，則此資料表不會納入同步群組中。
    - 若要選取/取消選取所有資料表，請按一下畫面底部的 [選取]。
10. 按一下 [ **儲存**, ，然後等待同步群組完成佈建。
11. 若要返回 [資料同步] 登陸頁面，請按一下畫面左上方的向後鍵 (在同步群組名稱之上)。

    ![Image6](./media/sql-database-get-started-sql-data-sync/NewSyncGroupSyncRules-Figure6.PNG)

## 步驟 6：設定同步群組

按一下 [資料同步] 登陸頁面底部的 [同步]，一律可以同步處理同步群組。
如果您要同步群組依照排程進行同步處理，請設定該同步群組。

1.  返回 [傳統入口網站](http://manage.windowsazure.com)。
2.  按一下 [ **SQL 資料庫**。
3.  按一下 [ **同步** ] 索引標籤。
4.  按一下此同步群組的名稱。
5.  按一下 [ **設定** ] 索引標籤。
6.  **AUTOMATIC SYNC**
    - 若要設定同步處理群組依設定頻率進行同步處理，請按一下 [ **ON**。 按一下 [同步]，仍可依照需求進行同步處理。
    - 按一下 [ **OFF** 設定只有當您按一下 [同步處理時的同步處理群組。
7.  **SYNC FREQUENCY**
    - 如果 [AUTOMATIC SYNC] 為 [開啟] 狀態，請設定同步頻率。 頻率必須介於 5 分鐘與 1 個月之間。
8.  按一下 [ **儲存**。

![Image7](./media/sql-database-get-started-sql-data-sync/NewSyncGroupConfigure-Figure7.PNG)

恭喜！ 您已建立一個同時包含 SQL Database 執行個體與 SQL Server 資料庫的同步群組。

## 後續步驟
如需 SQL Database 與 SQL 資料同步的其他資訊，請參閱：

* [下載完整的 SQL 資料同步技術文件](http://download.microsoft.com/download/4/E/3/4E394315-A4CB-4C59-9696-B25215A19CEF/SQL_Data_Sync_Preview.pdf)
* [SQL Database 概觀](sql-database-technical-overview.md)
* [資料庫生命週期管理](https://msdn.microsoft.com/library/jj907294.aspx)
 

 

