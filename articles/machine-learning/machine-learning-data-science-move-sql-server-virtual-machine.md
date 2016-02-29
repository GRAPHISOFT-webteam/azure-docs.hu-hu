<properties 
    pageTitle="移動資料至 Azure 虛擬機器上的 SQL Server | Azure" 
    description="從一般檔案或內部部署的 SQL Server 移動資料至 Azure VM 上的 SQL Server" 
    services="machine-learning" 
    documentationCenter="" 
    authors="bradsev" 
    manager="paulettm" 
    editor="cgronlun" />

<tags 
    ms.service="machine-learning" 
    ms.workload="data-services" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="10/12/2015" 
    ms.author="fashah;mohabib;bradsev" /> 

# 移動資料至 Azure 虛擬機器上的 SQL Server

這 **功能表** 主題連結，說明如何擷取資料到目標環境，可以儲存和處理期間 Cortana 分析程序 (CAP) 資料。

[AZURE.INCLUDE [cap-ingest-data-selector](../../includes/cap-ingest-data-selector.md)]


## 簡介
**這份文件** 概述移動資料從一般檔案 (CSV 或 TSV 格式) 或從內部部署 SQL Server 到 SQL Server 的 Azure 虛擬機器上的選項。 這些用於將資料移至雲端的工作是 Azure 所提供 Cortana 分析程序的一部分。

主題會概述機器學習時，將資料移到 Azure SQL Database 的選項，請參閱 [將資料移至 Azure SQL Database，Azure Machine Learning 的](machine-learning-data-science-move-sql-azure.md)。

下表摘要說明移動資料至 Azure 虛擬機器上之 SQL Server 的選項。

<b>來源</b> |<b>目的地：Azure VM 上的 SQL Server</b> |
------------------ |-------------------- |
<b>一般檔案</b> |1. <a href="#insert-tables-bcp">命令列大量複製公用程式 (BCP) </a><br> 2. <a href="#insert-tables-bulkquery">大量插入 SQL 查詢 </a><br> 3. <a href="#sql-builtin-utilities">SQL Server 中的圖形化內建公用程式</a>
<b>內部部署 SQL Server</b> | 1. <a href="#deploy-a-sql-server-database-to-a-microsoft-azure-vm-wizard">將 SQL Server Database 部署到 Microsoft Azure VM 精靈</a><br> 2. <a href="#export-flat-file">匯出至一般檔案 </a><br> 3. <a href="#sql-migration">SQL Database 移轉精靈 </a> <br> 4. <a href="#sql-backup">資料庫備份和還原 </a><br>

請注意，本文件假設 SQL 命令是從 SQL Server Management Studio 或 Visual Studio 資料庫總管中執行。

> [AZURE.TIP] 或者，您可以使用 [Azure Data Factory](https://azure.microsoft.com/services/data-factory/) 來建立和排程管線，以將資料移至 SQL Server VM 在 Azure 上。 如需詳細資訊，請參閱 [將使用 Azure Data Factory (複製活動) 的資料複製](../data-factory/data-factory-copy-activity.md)。


## <a name="prereqs"></a>先決條件
本教學課程假設您有：

*  **Azure 訂用帳戶**。 如果您沒有訂閱，您可以註冊 [免費試用版](https://azure.microsoft.com/pricing/free-trial/)。
*  **Azure 儲存體帳戶**。 在本教學課程中，您將使用 Azure 儲存體帳戶來儲存資料。 如果您沒有 Azure 儲存體帳戶，請參閱 [建立儲存體帳戶](storage-create-storage-account.md#create-a-storage-account) 文件。 建立儲存體帳戶之後，您必須取得用來存取儲存體的帳戶金鑰。 請參閱 [檢視、 複製和重新產生儲存體存取金鑰](storage-create-storage-account.md#view-copy-and-regenerate-storage-access-keys)。
* 佈建 **Azure VM 上的 SQL Server**。 如需指示，請參閱 [將 Azure SQL Server 虛擬機器設定為 IPython Notebook 伺服器供進階分析](machine-learning-data-science-setup-sql-server-virtual-machine.md)。
* 安裝並設定 **PowerShell** 在本機。 如需指示，請參閱 [如何安裝和設定 Azure PowerShell](powershell-install-configure.md)。


## <a name="filesource_to_sqlonazurevm"></a> Azure VM 上的 SQL Server 從一般檔案來源移動資料

如果您的資料位於一般檔案 (使用資料列或資料行格式排列) 中，可透過下列方法，將它移到 Azure 上的 SQL Server VM：

1. [命令列大量複製公用程式 (BCP)](#insert-tables-bcp) 
2. [大量插入 SQL 查詢 ](#insert-tables-bulkquery)
3. [SQL Server 中的圖形化內建公用程式 (匯入/匯出，SSIS)](#sql-builtin-utilities)


### <a name="insert-tables-bcp"></a>命令列大量複製公用程式 (BCP)

BCP 是與 SQL Server 一起安裝的命令列公用程式，是最快速移動資料的其中一種方式。 它的運作方式可跨越這三個 SQL Server 版本 (內部部署的 SQL Server、SQL Azure，以及 Azure 上的 SQL Server VM)。 

> [AZURE.NOTE] **我的資料應該儲存的 BCP?**  
> 雖然並非必要，但是，擁有包含與目標 SQL Server 位於相同電腦上之來源資料的檔案可讓傳輸速度 (網路速度與本機磁碟 IO 速度) 變得更快。 您可以移動包含機器資料的一般檔案安裝 SQL Server 使用各種檔案複製工具例如 [AZCopy](../storage-use-azcopy.md), ，[Azure 儲存體總管](https://azurestorageexplorer.codeplex.com/) 或 windows 複製/貼上透過遠端桌面通訊協定 (RDP)。

1. 確定已在目標 SQL Server 資料庫上建立資料庫和資料表。 以下是如何使用 `Create Database` 和 `Create Table` 命令來執行此作業的範例：

        CREATE DATABASE <database_name>
    
        CREATE TABLE <tablename>
        (
            <columnname1> <datatype> <constraint>,
            <columnname2> <datatype> <constraint>,
            <columnname3> <datatype> <constraint>
        ) 
    
2. 從安裝 BCP 的電腦命令列中發出下列命令，以產生說明資料表結構描述的格式檔案。

    `bcp dbname..tablename format nul -c -x -f exportformatfilename.xml -S servername\sqlinstance -T -t \t -r \n` 

3. 使用 BCP 命令來將資料插入資料庫，如下所示。 若假設 SQL Server 安裝於同一部電腦上，這應該就能從命令列順利運作：

    `bcp dbname..tablename in datafilename.tsv -f exportformatfilename.xml -S servername\sqlinstancename -U username -P password -b block_size_to_move_in_single_attemp -t \t -r \n`

> **最佳化 BCP 插入** ，請參閱下列文件 [最佳化大量匯入的指導方針](https://technet.microsoft.com/library/ms177445%28v=sql.105%29.aspx) 以將這類插入最佳化。

### <a name="insert-tables-bulkquery-parallel"></a>平行插入以進行更快速的資料移動

如果您要移動的資料很大，就可以在 PowerShell 指令碼中同時平行執行多個 BCP 命令來加快運作速度。

> [AZURE.NOTE] **巨量資料擷取** 
> 若要將針對大型和超大型資料集載入的資料最佳化，可以使用多個檔案群組和資料分割資料表來進行邏輯與實體資料庫資料表的資料分割。 如需建立和載入資料至資料分割資料表的詳細資訊，請參閱  [平行載入 SQL 資料分割資料表](machine-learning-data-science-parallel-load-sql-partitioned-tables.md)。


下列 PowerShell 指令碼範例將示範使用 BCP 平行插入：
    
    $NO_OF_PARALLEL_JOBS=2

     Set-ExecutionPolicy RemoteSigned #set execution policy for the script to execute
     # Define what each job does
       $ScriptBlock = {
           param($partitionnumber)

           #Explictly using SQL username password
           bcp database..tablename in datafile_path.csv -F 2 -f format_file_path.xml -U username@servername -S tcp:servername -P password -b block_size_to_move_in_single_attempt -t "," -r \n -o path_to_outputfile.$partitionnumber.txt

            #Trusted connection w.o username password (if you are using windows auth and are signed in with that credentials)
            #bcp database..tablename in datafile_path.csv -o path_to_outputfile.$partitionnumber.txt -h "TABLOCK" -F 2 -f format_file_path.xml  -T -b block_size_to_move_in_single_attempt -t "," -r \n 
      }
    

    # Background processing of all partitions
    for ($i=1; $i -le $NO_OF_PARALLEL_JOBS; $i++)
    {
      Write-Debug "Submit loading partition # $i"
      Start-Job $ScriptBlock -Arg $i      
    }
    

    # Wait for it all to complete
    While (Get-Job -State "Running")
    {
      Start-Sleep 10
      Get-Job
    }
    
    # Getting the information back from the jobs
    Get-Job | Receive-Job
    Set-ExecutionPolicy Restricted #reset the execution policy


### <a name="insert-tables-bulkquery"></a>大量插入 SQL 查詢

[大量插入 SQL 查詢](https://msdn.microsoft.com/library/ms188365) 可將資料匯入的資料庫資料列/資料行為基礎的檔案 (支援的型別將會涵蓋[準備大量匯出或匯入 (SQL Server) 的資料](https://msdn.microsoft.com/library/ms188609)) 主題。 

此處提供一些大量插入的命令範例，如下所示：  

1. 分析您的資料，並在匯入之前設定任何自訂選項，以確定 SQL Server 資料庫會針對任何特殊欄位 (例如日期) 假設相同的格式。 以下範例示範如何將日期格式設為「年-月-日」 (如果您的資料包含「年-月-日」格式的日期)：

        SET DATEFORMAT ymd; 
    
2. 使用大量匯入陳述式來匯入資料：

        BULK INSERT <tablename>
        FROM    
        '<datafilename>'
        WITH 
        (
        FirstRow=2,
        FIELDTERMINATOR =',', --this should be column separator in your data
        ROWTERMINATOR ='\n'   --this should be the row separator in your data
        )
      

### <a name="sql-builtin-utilities"></a>SQL Server 中的內建公用程式

您可以使用 SQL Server Integration Services (SSIS)，將資料從一般檔案匯入 Azure 上的 SQL Server VM。 
SSIS 適用於兩種 Studio 環境。 如需詳細資訊，請參閱 [Integration Services (SSIS) 和 Studio 環境](https://technet.microsoft.com/library/ms140028.aspx):

- 如需 SQL Server Data Tools 的詳細資訊，請參閱 [Microsoft SQL Server Data Tools](https://msdn.microsoft.com/data/tools.aspx)  
- 如需匯入/匯出精靈的詳細資訊，請參閱 [SQL Server 匯入和匯出精靈](https://msdn.microsoft.com/library/ms141209.aspx)

## <a name="sqlonprem_to_sqlonazurevm"></a>將資料從內部部署 SQL Server 移至 SQL Server，在 Azure VM 上

您也可以使用下列移轉策略：

1. [將 SQL Server Database 部署到 Microsoft Azure VM 精靈](#deploy-a-sql-server-database-to-a-microsoft-azure-vm-wizard)
2. [匯出至一般檔案](#export-flat-file) 
3. [SQL Database 移轉精靈](#sql-migration)
4. [資料庫備份和還原](#sql-backup)

我們將在下方說明這每一項內容：

### 將 SQL Server Database 部署到 Microsoft Azure VM 精靈

 **將 SQL Server 資料庫部署到 Microsoft Azure VM 精靈** 是將資料從內部部署 SQL Server 執行個體移至 SQL Server，在 Azure VM 上的簡單及建議的方法。 詳細的步驟，以及其他替代方案的討論，請參閱 [資料庫移轉至 Azure VM 上的 SQL Server](../virtual-machines/virtual-machines-migrate-onpremises-database.md)。

### <a name="export-flat-file"></a>匯出至一般檔案

各種方法可用來從大量匯出資料的內部部署 SQL Server 中所述 [大量匯入和匯出的資料 (SQL Server)](https://msdn.microsoft.com/library/ms175937.aspx) 主題。 本文件將提供大量複製程式 (BCP) 做為範例。 一旦將資料匯出至一般檔案之後，就可以使用大量匯入功能來將它匯入另一部 SQL Server。 

1. 使用 BCP 公用程式，從內部部署的 SQL Server 將資料匯出至檔案，如下所示

    `bcp dbname..tablename out datafile.tsv -S  servername\sqlinstancename -T -t \t -t \n -c`

2. 針對步驟 1 中匯出的資料表結構描述使用 `create database` 和 `create table`，在 Azure 的 SQL Server VM 上建立資料庫和資料表。

3. 建立格式檔案，用以說明要匯出/匯入之資料的資料表結構描述。 格式檔案的詳細資料如下所述 [建立格式檔案 (SQL Server)](https://msdn.microsoft.com/library/ms191516.aspx)。

    從 SQL Server 電腦執行 BCP 時產生格式檔案 

        bcp dbname..tablename format nul -c -x -f exportformatfilename.xml -S servername\sqlinstance -T -t \t -r \n 

    針對 SQL Server 遠端執行 BCP 時產生格式檔案 

        bcp dbname..tablename format nul -c -x -f  exportformatfilename.xml  -U username@servername.database.windows.net -S tcp:servername -P password  --t \t -r \n
        
    
4. 使用任何一節所述的方法 [從檔案來源移動資料](#filesource_to_sqlonazurevm) 將一般檔案中的資料移至 SQL Server。

### <a name="sql-migration"></a>SQL Database 移轉精靈

[SQL Server 資料庫移轉精靈](http://sqlazuremw.codeplex.com/) 提供方便使用的方式，兩個 SQL server 執行個體之間移動資料。 它讓使用者能夠對應來源與目的地資料表之間的資料結構描述，選擇資料行類型和其他各種功能。 它會在幕後使用大量複製 (BCP) 功能。 SQL Database 移轉精靈歡迎畫面的螢幕擷取畫面如下所示。  

![SQL Server 移轉精靈][2]

### <a name="sql-backup"></a>資料庫備份和還原

SQL Server 支援： 

1. [資料庫備份和還原功能](https://msdn.microsoft.com/library/ms187048.aspx) (兩者皆可為本機檔案或以 bacpac 匯出至 blob) 和 [資料層應用程式](https://msdn.microsoft.com/library/ee210546.aspx) (使用 bacpac)。 
2. 使用複製的資料庫直接在 Azure 上建立 SQL Server VM，或者複製到現有 SQL Azure 資料庫的能力。 如需詳細資訊，請參閱 [使用複製資料庫精靈 」](https://msdn.microsoft.com/library/ms188664.aspx)。 

SQL Server Management Studio 的資料庫備份/還原選項的螢幕擷取畫面如下所示。

![SQL Server 匯入工具][1]

## 資源

[將資料庫移轉至 Azure VM 上的 SQL Server](../virtual-machines/virtual-machines-migrate-onpremises-database.md)

[Azure 虛擬機器上的 SQL Server 概觀](../virtual-machines/virtual-machines-sql-server-infrastructure-services.md)

[1]: ./media/machine-learning-data-science-move-sql-server-virtual-machine/sqlserver_builtin_utilities.png
[2]: ./media/machine-learning-data-science-move-sql-server-virtual-machine/database_migration_wizard.png

