<properties 
    pageTitle="從 Azure Blob 儲存體來回移動資料 | Microsoft Azure" 
    description="從 Azure Blob 儲存體來回移動資料" 
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
    ms.date="10/12/2015" 
    ms.author="bradsev;sunliangms;sachouks;mohabib" />

# 從 Azure Blob 儲存體來回移動資料

[AZURE.INCLUDE [cap-ingest-data-selector](../../includes/cap-ingest-data-selector.md)]

## 簡介

以下是有關從 Azure Blob 儲存體來回移動資料所使用之技術的指引連結：

[AZURE.INCLUDE [blob-storage-tool-selector](../../includes/machine-learning-blob-storage-tool-selector.md)]

哪一種方法最適合，將取決於您的案例。  [進階分析程序和技術 (ADAPT) 在 Azure Machine Learning 中的案例](../machine-learning-data-science-plan-sample-scenarios.md) 文章可協助您判斷進階的分析程序中使用的資料科學工作流程的各種所需的資源。

> [AZURE.NOTE] 如需 Azure blob 儲存體的完整介紹，請參閱 [Azure Blob 基本概念](../storage-dotnet-how-to-use-blobs.md) 和  [Azure Blob 服務](https://msdn.microsoft.com/library/azure/dd179376.aspx)。 

> [AZURE.TIP] 或者，您可以使用 [Azure Data Factory](https://azure.microsoft.com/services/data-factory/) 建立和排程管線，將會從 Azure blob 儲存體下載資料、 將它傳遞至已發佈的 Azure Machine Learning web 服務、 接收預測性分析的結果，並上傳結果至儲存體。 如需詳細資訊，請參閱 [使用 Azure Data Factory 和 Azure Machine Learning 建立預測管線](../data-factory/data-factory-create-predictive-pipelines.md)。

## 必要條件

本文件假設您擁有 Azure 訂用帳戶、儲存體帳戶和該帳戶的對應儲存體金鑰。 上傳/下載資料之前，您必須知道 Azure 儲存體帳戶名稱和帳戶金鑰。 

- 若要設定 Azure 訂用帳戶，請參閱 [免費試用一個月](https://azure.microsoft.com/pricing/free-trial/)。
- 如需建立儲存體帳戶的指示，以及用來取得帳戶和金鑰資訊，請參閱 [關於 Azure 儲存體帳戶](../storage-create-storage-account.md)。




 

