<properties 
    pageTitle="使用 Panda 建立 Azure blob 儲存體資料功能 | Microsoft Azure" 
    description="如何使用 Panda Python 封裝對儲存在 Azure blob 容器的資料建立功能。" 
    services="machine-learning,storage" 
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

#使用 Panda 建立 Azure blob 儲存體資料功能

這 **功能表** 主題連結，說明如何設計功能，針對各種環境中的資料。 此工作是 Cortana 分析程序 (CAP) 中的一個步驟。

[AZURE.INCLUDE [cap-create-features-data-selector](../../includes/cap-create-features-selector.md)]

##簡介

本文件說明如何建立資料儲存在 Azure blob 容器使用的功能 [Pandas](http://pandas.pydata.org/) Python 封裝。 概述如何載入 Panda 資料框架後，接著會示範如何使用 Python 指令碼，產生帶有指標值與分類收納功能的分類功能。

## 先決條件
本文假設您已經：

* 建立您的 Azure blob 儲存體帳戶並將您的資料儲存在該處。 如果您需要設定一個帳戶的指示，請參閱 [建立 Azure 儲存體帳戶](../hdinsight-get-started.md#storage)


## 將資料載入至 Pandas 資料框架
若要進行探索和操作資料集，必須從 Blob 來源將資料集下載至本機檔案，然後將其載入 Pandas 資料框架中。 以下是此程序的遵循步驟：

1. 使用 Blob 服務，透過下列 Python 程式碼範例，從 Azure Blob 下載資料。 使用您的特定值來取代下列程式碼中的變數： 

        from azure.storage import BlobService
        import tables
        
        STORAGEACCOUNTNAME= <storage_account_name>
        STORAGEACCOUNTKEY= <storage_account_key>
        LOCALFILENAME= <local_file_name>        
        CONTAINERNAME= <container_name>
        BLOBNAME= <blob_name>

        #download from blob
        t1=time.time()
        blob_service=BlobService(account_name=STORAGEACCOUNTNAME,account_key=STORAGEACCOUNTKEY)
        blob_service.get_blob_to_path(CONTAINERNAME,BLOBNAME,LOCALFILENAME)
        t2=time.time()
        print(("It takes %s seconds to download "+blobname) % (t2 - t1))


2. 從下載的檔案中，將資料讀取至 Pandas 資料框架。

        #LOCALFILE is the file path 
        dataframe_blobdata = pd.read_csv(LOCALFILE)

現在您已經準備好探索資料並在此資料集上產生功能。
    
##<a name="blob-featuregen"></a>功能產生
    
接下兩節會說明如何使用 Python 指令碼，產生帶有指標值和分類收納功能的分類功能。

###<a name="blob-countfeature"></a>以指標值為基礎的功能產生

類別功能可使用如下的方式來建立：

1. 檢查類別資料行的分佈：
    
        dataframe_blobdata['<categorical_column>'].value_counts()

2. 產生每個資料行值的指標值

        #generate the indicator column
        dataframe_blobdata_identity = pd.get_dummies(dataframe_blobdata['<categorical_column>'], prefix='<categorical_column>_identity')

3. 將指標資料行與原始資料框架聯結在一起 
 
            #Join the dummy variables back to the original data frame
            dataframe_blobdata_with_identity = dataframe_blobdata.join(dataframe_blobdata_identity)

4. 移除原始變數本身：

        #Remove the original column rate_code in df1_with_dummy
        dataframe_blobdata_with_identity.drop('<categorical_column>', axis=1, inplace=True)
    
###<a name="blob-binningfeature"></a>分類收納功能產生

若要產生分類收納功能，我們可使用如下的方式繼續進行：

1. 新增一系列的資料行，以分類收納數值資料行
 
        bins = [0, 1, 2, 4, 10, 40]
        dataframe_blobdata_bin_id = pd.cut(dataframe_blobdata['<numeric_column>'], bins)
        
2. 將分類收納轉換為一系列的布林值變數

        dataframe_blobdata_bin_bool = pd.get_dummies(dataframe_blobdata_bin_id, prefix='<numeric_column>')
    
3. 最後，將虛擬變數新增回原始資料框架中

        dataframe_blobdata_with_bin_bool = dataframe_blobdata.join(dataframe_blobdata_bin_bool) 

##<a name="sql-featuregen"></a>將資料寫回 Azure Blob 並在 AzureMachine Learning 中取用

探索資料並建立必要功能，您可以將資料上傳之後 (取樣性或功能性) 至 Azure blob，然後使用它在 Azure 機器學習中使用下列步驟 ︰
請注意，Azure Machine Learning Studio 也可以建立額外的功能。 
1. 將資料框架寫入本機檔案中

        dataframe.to_csv(os.path.join(os.getcwd(),LOCALFILENAME), sep='\t', encoding='utf-8', index=False)

2. 將資料上傳至 Azure Blob，如下所示：

        from azure.storage import BlobService
        import tables

        STORAGEACCOUNTNAME= <storage_account_name>
        LOCALFILENAME= <local_file_name>
        STORAGEACCOUNTKEY= <storage_account_key>
        CONTAINERNAME= <container_name>
        BLOBNAME= <blob_name>

        output_blob_service=BlobService(account_name=STORAGEACCOUNTNAME,account_key=STORAGEACCOUNTKEY)    
        localfileprocessed = os.path.join(os.getcwd(),LOCALFILENAME) #assuming file is in current working directory
        
        try:
       
        #perform upload
        output_blob_service.put_block_blob_from_path(CONTAINERNAME,BLOBNAME,localfileprocessed)
        
        except:         
            print ("Something went wrong with uploading blob:"+BLOBNAME)

3. 現在可以使用 Azure Machine Learning 從 blob 讀取資料 [讀取器](https://msdn.microsoft.com/library/azure/4e1b0fe6-aded-4b3f-a36f-39b8862b9004/) 模組，如下列畫面所示 ︰
 
![讀取器 Blob](./media/machine-learning-data-science-process-data-blob/reader_blob.png)


 

