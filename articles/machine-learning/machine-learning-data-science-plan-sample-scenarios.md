<properties
    pageTitle="Azure Machine Learning 中進階分析程序和技術的案例 | Microsoft Azure"
    description="針對 Azure Machine Learning 中的進階預測性分析程序選取適當的案例。"
    services="machine-learning"
    documentationCenter=""
    authors="msolhab"
    manager="paulettm"
    editor="" />

<tags
    ms.service="machine-learning"
    ms.workload="data-services"
    ms.tgt_pltfrm="na" 
    ms.devlang="na"
    ms.topic="article"
    ms.date="10/18/2015"
    ms.author="msolhab;bradsev" />



# 在 Azure 機器學習中的進階分析案例

本文概述可以運用 Cortana 分析程序 (CAP) 來處理的各種範例資料來源和目標案例。 本文將根據資料特性、來源位置和在 Azure 中的目標儲存機制，來說明處理順序中可用的選項。

最後一節提供**決策樹**，讓您在選取適合您資料和目標的範例案例時可以使用。

>[AZURE.INCLUDE [machine-learning-free-trial](../../includes/machine-learning-free-trial.md)]


下列各節均提供一個範例案例。 在每個案例中，都會列出可能的資料科學或進階分析流程，以及支援的 Azure 資源。
>[AZURE.NOTE] **對於所有下列案例，您必須：**

*   
*   


## 

![中小型本機檔案][1]

#### 其他 Azure 資源：無

1.  

2.  上傳資料集。

3.  建置從上傳的資料集開始的 Azure 機器學習實驗流程。

## 

![需要處理的中小型本機檔案][2]

#### 其他 Azure 資源：Azure 虛擬機器 (IPython Notebook 伺服器)

1.  建立執行 IPython Notebook 的 Azure 虛擬機器。

2.  將資料上傳至 Azure 儲存體容器。

3.  


4.  將資料轉換為已清理的表格式格式。

5.  將轉換的資料儲存在 Azure Blob 中。

6.  

7.  

8. 建置從內嵌的資料集開始的 Azure 機器學習實驗流程。

## 

![大型本機檔案][3]

#### 其他 Azure 資源：Azure 虛擬機器 (IPython Notebook 伺服器)

1.  建立執行 IPython Notebook 的 Azure 虛擬機器。

2.  將資料上傳至 Azure 儲存體容器。

3.  前置處理和清除 IPython Notebook 中的資料 (從 Azure Blob 存取資料)。

4.  若有需要，請將資料轉換為已清理的表格式格式。

5.  視需要瀏覽資料並建立功能。

6.  擷取中小型資料範例。

7.  將取樣資料儲存在 Azure Blob 中。

8. 

9. 

10. 建置從內嵌的資料集開始的 Azure ML 實驗流程。


## 

![中小型本機檔案至 Azure 中的 SQL DB][4]

#### 其他 Azure 資源：Azure 虛擬機器 (SQL Server / IPython Notebook 伺服器)

1.  建立執行 SQL Server + IPython Notebook 的 Azure 虛擬機器。

2.  將資料上傳至 Azure 儲存體容器。

3.  使用 IPython Notebook 前置處理和清除 Azure 儲存體容器中的資料。

4.  若有需要，請將資料轉換為已清理的表格式格式。

5.  將資料儲存至 VM-local 檔案 (IPython Notebook 會在 VM 上執行，本機磁碟是指 VM 磁碟機)。

6.  將資料載入執行於 Azure VM 的 SQL Server 資料庫。

    a.

        i.  Login to SQL Server VM
        ii. Run SQL Server Management Studio.
        iii. Create database and target tables.
        iv. Use one of the bulk import methods to load the data from VM-local files.

    b.
        datasets

        i.  Use ODBC connection string to access SQL Server on VM.
        ii. Create database and target tables.
        iii. Use one of the bulk import methods to load the data from VM-local files.

7.  視需要瀏覽資料並建立功能。 請注意，功能在資料庫資料表中無需具體化。 僅注意建立這些功能的必要查詢。

8. 若有需要，請決定資料範例大小。

9. 

10. 

11. 建置從內嵌的資料集開始的 Azure ML 實驗流程。

## 

![大型本機檔案至 Azure 中的 SQL DB][5]

#### 其他 Azure 資源：Azure 虛擬機器 (SQL Server / IPython Notebook 伺服器)

1.  建立執行 SQL Server 和 IPython Notebook 伺服器的 Azure 虛擬機器。

2.  將資料上傳至 Azure 儲存體容器。

3.  (選擇性) 前置處理和清除資料。

    a.
        

    b. 若有需要，請將資料轉換為已清理的表格式格式。

    c. 將資料儲存至 VM-local 檔案 (IPython Notebook 會在 VM 上執行，本機磁碟是指 VM 磁碟機)。

4.  將資料載入執行於 Azure VM 的 SQL Server 資料庫。

    a. 登入 SQL Server VM。

    b.
        

    c. 執行 SQL Server Management Studio。

    d. 建立資料庫和目標資料表。

    e. 使用其中一個大量匯入方法來載入資料。

    f. 如果需要資料表聯結，請建立索引以加速聯結。
 > [AZURE.NOTE] 如需加快大型資料的載入速度，建議您建立分割資料表並大量平行匯入資料。

5.  視需要瀏覽資料並建立功能。 請注意，功能在資料庫資料表中無需具體化。 僅注意建立這些功能的必要查詢。

6.  若有需要，請決定資料範例大小。

7.  

8. 

9. 從上傳的資料集開始的簡易 Azure ML 實驗流程

## 

![大型 SQL DB 內部部署至 Azure 中的 SQL DB][6]

#### 其他 Azure 資源：Azure 虛擬機器 (SQL Server / IPython Notebook 伺服器)

1.  建立執行 SQL Server 和 IPython Notebook 伺服器的 Azure 虛擬機器。

2.  使用其中一個資料匯出方法來將資料從 SQL Server 匯出成傾印檔案。

    a.
        
        
        
        

3.  將傾印檔案上傳至 Azure 儲存體容器。

4.  將資料載入執行於 Azure 虛擬機器的 SQL Server 資料庫。

    a. 登入 SQL Server VM。

    b. 將資料檔案從 Azure 儲存體容器下載到 local-VM 資料夾。

    c. 執行 SQL Server Management Studio。

    d. 建立資料庫和目標資料表。

    e. 使用其中一個大量匯入方法來載入資料。

    f. 如果需要資料表聯結，請建立索引以加速聯結。

> [AZURE.NOTE] 如需加快大型資料的載入速度，請建立分割資料表並大量平行匯入資料。

5.  視需要瀏覽資料並建立功能。 請注意，功能在資料庫資料表中無需具體化。 僅注意建立這些功能的必要查詢。

6.  若有需要，請決定資料範例大小。

7.  

8. 

9. 從上傳的資料集開始的簡易 Azure ML 實驗流程。

### 

![卸離本機 DB 和連結至 Azure 中的 SQL DB][7]

#### 其他 Azure 資源：Azure 虛擬機器 (SQL Server / IPython Notebook 伺服器)

若要在您的 SQL Server VM 中複寫整個 SQL Server 資料庫，您應將資料庫從一個位置/伺服器複製到另一個位置/伺服器 (假設資料庫可以暫時設定離線)。 您可以在 SQL Server Management Studio Object Explorer GUI 中，或使用對等的 Transact-SQL 命令來執行此作業。

1. 在來源位置卸離資料庫。
2. 在 Windows 檔案總管或 Windows 命令提示字元視窗中，將已卸離的資料庫檔案和記錄檔複製到位於 Azure 中 SQL Server VM 上的目標位置。
3. 將複製檔案連結至目標 SQL Server 執行個體。



## 

![本機目標 Hive 中的巨量資料][9]

#### 其他 Azure 資源：Azure HDInsight Hadoop 叢集和 Azure 虛擬機器 (IPython Notebook 伺服器)

1.  建立執行 IPython Notebook 伺服器的 Azure 虛擬機器。

2.  建立 Azure HDInsight Hadoop 叢集。

3.  (選擇性) 前置處理和清除資料。

    a.
        

    b. 若有需要，請將資料轉換為已清理的表格式格式。

    c. 將資料儲存至 VM-local 檔案 (IPython Notebook 會在 VM 上執行，本機磁碟是指 VM 磁碟機)。

4.  將資料上傳至步驟 2 中已選取 Hadoop 叢集的預設容器。

5.  將資料載入 Azure HDInsight Hadoop 叢集中的 Hive 資料庫。

    a. 登入 Hadoop 叢集的前端節點

    b. 開啟 Hadoop 命令列。

    c.

    d. 執行 Hive 查詢以建立資料庫和資料表，並將資料從 Blob 儲存體載入 Hive 資料表。
    > [AZURE.NOTE] 如果資料太大，使用者可以建立包含分割區的 Hive 資料表

6.  在 Hadoop 命令列中，視需要瀏覽資料並建立功能。 請注意，功能在資料庫資料表中無需具體化。 僅注意建立這些功能的必要查詢。

    a. 登入 Hadoop 叢集的前端節點

    b. 開啟 Hadoop 命令列。

    c.

    d. 在 Hadoop 叢集前端節點的 Hadoop 命令列中執行 Hive 查詢，以瀏覽資料並視需要建立功能。

7.  若有需要，取樣資料以符合 Azure Machine Learning Studio 需求。

8.  

9. 

10. 從上傳的資料集開始的簡易 Azure ML 實驗流程。

## 

------------------------


下圖摘要說明上述案例，以及讓您連結到每個分項案例的「進階分析程序和技術」選擇。 請注意，資料處理、資料探索、功能工程和取樣可能會出現在一或多個方法/環境中 (在來源、中繼和/或目標環境中)，且可以視需要反覆處理。 圖表的目的只是說明一些可能的流程，並不提供詳盡的列舉。

![範例 DS 程序逐步解說案例][8]

### 進階分析實務範例

如需透過公用資料集運用進階分析程序和技術的端對端 Azure Machine Learning 逐步解說，請參閱：


* 
* 




[1]: ./media/machine-learning-data-science-plan-sample-scenarios/dsp-plan-small-in-aml.png 
[2]: ./media/machine-learning-data-science-plan-sample-scenarios/dsp-plan-local-with-processing.png 
[3]: ./media/machine-learning-data-science-plan-sample-scenarios/dsp-plan-local-large.png 
[4]: ./media/machine-learning-data-science-plan-sample-scenarios/dsp-plan-local-to-db.png 
[5]: ./media/machine-learning-data-science-plan-sample-scenarios/dsp-plan-large-to-db.png 
[6]: ./media/machine-learning-data-science-plan-sample-scenarios/dsp-plan-db-to-db.png 
[7]: ./media/machine-learning-data-science-plan-sample-scenarios/dsp-plan-attach-db.png 
[8]: ./media/machine-learning-data-science-plan-sample-scenarios/dsp-plan-sample-scenarios.png 
[9]: ./media/machine-learning-data-science-plan-sample-scenarios/dsp-plan-local-to-hive.png 
[reader]: https://msdn.microsoft.com/library/azure/4e1b0fe6-aded-4b3f-a36f-39b8862b9004/ 

