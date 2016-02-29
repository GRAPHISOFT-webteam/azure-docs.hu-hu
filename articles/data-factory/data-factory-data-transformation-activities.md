<properties 
    pageTitle="資料轉換活動 | Microsoft Azure" 
    description="了解使用 Azure Data Factory 服務轉換和分析資料的方法。" 
    services="data-factory" 
    documentationCenter="" 
    authors="spelluru" 
    manager="jhubbard" 
    editor="monicar"/>

<tags 
    ms.service="data-factory" 
    ms.workload="data-services" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="11/04/2015" 
    ms.author="spelluru"/>

# 使用 Azure Data Factory 進行轉換和分析

## 概觀
在 Azure Data Factory 轉換中的轉換活動，可處理您的原始資料以進行預測和深入了解。 轉換活動會在運算環境中執行，例如 Azure HDInsight 叢集或 Azure Batch。 Azure Data Factory 支援下列轉換活動新增到 [管線](data-factory-create-pipelines.md) 可以個別或與其他活動鏈結。


轉換活動 |  計算環境 
:----------------------- | :--------------------
[Hive](data-factory-hive-activity.md) | HDInsight [Hadoop] 
[Pig](data-factory-pig-activity.md) | HDInsight [Hadoop]  
[MapReduce](data-factory-map-reduce.md) | HDInsight [Hadoop]  
[Hadoop 資料流](data-factory-hadoop-streaming-activity.md) | HDInsight [Hadoop]
[機器學習活動: 批次執行和更新資源](data-factory-azure-ml-batch-execution-activity.md) | Azure VM 
[預存程序](data-factory-stored-proc-activity.md) | Azure SQL |
[資料湖分析 U-SQL](data-factory-usql-activity.md) | Azure 資料湖分析 
[DotNet](data-factory-use-custom-activities.md) | HDInsight [Hadoop] 或 Azure Batch
   

您需要為計算環境建立連結的服務，然後在定義轉換活動時使用連結的服務。 Data Factory 支援兩種類型的資計算環境。 

1. **隨**: 在此情況下，運算環境完全由 Data Factory。 Data Factory 服務會在工作提交前自動建立運算環境以處理資料，而在工作完成時予以移除。 使用者可以針對工作執行、叢集管理和啟動動作，設定和控制隨選計算環境的細微設定。 
2. **將您自己**: 在此情況下，您可以在 Data Factory 的連結服務註冊您自己的運算環境 (例如 HDInsight 叢集)。 運算環境由您自行管理，而 Data Factory 會使用它來執行活動。 

請參閱 [運算連結服務](data-factory-compute-linked-services.md) 文章來了解 Data Factory 所支援的運算連結服務。 


