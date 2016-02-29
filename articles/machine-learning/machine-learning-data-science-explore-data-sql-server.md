<properties 
    pageTitle="在 Azure 上瀏覽 SQL Server 虛擬機器中的資料 | Microsoft Azure" 
    description="如何瀏覽儲存在 Azure 上 SQL Server VM 中的資料。" 
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
    ms.date="10/20/2015" 
    ms.author="fashah;garye;bradsev" /> 

#在 Azure 上瀏覽 SQL Server 虛擬機器中的資料

這 **功能表** 主題連結，說明如何使用工具來瀏覽來自不同的儲存體環境。 此工作是 Cortana 分析程序 (CAP) 中的一個步驟。

[AZURE.INCLUDE [cap-explore-data-selector](../../includes/cap-explore-data-selector.md)]

##簡介

本文件涵蓋如何瀏覽儲存在 Azure 上 SQL Server VM 中的資料。 使用 SQL整理資料或使用 Python 這類程式設計語言，即可完成此動作。


> [AZURE.NOTE] 本文件中的範例 SQL 陳述式會假設資料位於 SQL Server 中。 如果不是，請參閱雲端資料科學程序圖，以了解如何將資料移至 SQL Server 中。



## <a name="sql-dataexploration"></a>瀏覽 SQL 資料使用 SQL 指令碼

以下是數個 SQL 指令碼範例，可用來探索儲存於 SQL Server 中的資料。

1. 取得每天的觀察計數

    `SELECT CONVERT(date, <date_columnname>) as date, count(*) as c from <tablename> group by CONVERT(date, <date_columnname>)` 

2. 取得類別資料行中的層級

    `select  distinct <column_name> from <databasename>`

3. 取得兩個類別資料行組合中的層級數目 

    `select <column_a>, <column_b>,count(*) from <tablename> group by <column_a>, <column_b>`

4. 取得數值資料行的分佈

    `select <column_name>, count(*) from <tablename> group by <column_name>`

> [AZURE.NOTE] 如需實用範例，您可以使用 [NYC 計程車資料集](http://www.andresmh.com/nyctaxitrips/) ，並參考標題為的 ipnb，以 [使用 IPython Notebook 和 SQL Server 有爭議的 NYC 資料](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Misc/DataScienceProcess/iPythonNotebooks/machine-Learning-data-science-process-sql-walkthrough.ipynb) 進行端對端逐步解說。

##<a name="python"></a>瀏覽 SQL 資料與 Python

使用 Python 來探索資料和產生功能，SQL Server 資料時，類似於處理中所述，使用 Python 的 Azure blob 中的資料 [資料科學環境中的處理 Azure Blob 資料](machine-learning-data-science-process-data-blob.md)。 資料必須從資料庫載入 Pandas 資料框架，然後就能進一步處理。 我們將在本節中說明連接到資料庫以及將資料載入資料框架的程序。

下列連接字串格式可用來使用 pyodbc (使用您的特定值來取代伺服器名稱、dbname、使用者名稱和密碼)，從 Python 連接到 SQL Server 資料庫：

    #Set up the SQL Azure connection
    import pyodbc   
    conn = pyodbc.connect('DRIVER={SQL Server};SERVER=<servername>;DATABASE=<dbname>;UID=<username>;PWD=<password>')

 [Pandas 程式庫](http://pandas.pydata.org/) 在 Python 中會提供一組豐富的資料結構和資料分析工具對 Python 程式設計進行資料操作。 下列程式碼會將從 SQL Server 資料庫傳回的結果讀取至 Pandas 資料框架：

    # Query database and load the returned results in pandas data frame
    data_frame = pd.read_sql('''select <columnname1>, <cloumnname2>... from <tablename>''', conn)

現在您可以使用 Pandas 資料框架中的主題涵蓋 [資料科學環境中的處理 Azure Blob 資料](machine-learning-data-science-process-data-blob.md)。

## Cortana 分析程序實務範例

使用公用資料集的 Cortana 分析程序的端對端逐步解說範例，請參閱 [作用中的 Cortana 分析程序: 使用 SQL Server](machine-learning-data-science-process-sql-walkthrough.md)。

 
