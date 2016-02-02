<properties 
    pageTitle="Azure Data Factory 簡介" 
    description="了解如何使用 Azure Data Factory 服務撰寫資料處理、資料儲存和資料移動服務，以建立產生可信資訊的管線。" 
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
    ms.topic="get-started-article" 
    ms.date="11/18/2015" 
    ms.author="spelluru"/>


# Azure Data Factory 服務簡介

## 概觀

Data Factory 是雲端架構資料整合服務，用來協調以及自動移動和轉換資料。 就像製造廠運轉設備來取得原物料並將之轉換成成品一樣，Data Factory 會協調現有服務的流程來收集原始資料，並將之轉換成隨時可用的資訊。

Data Factory 會在內部部署、雲端資料來源和 SaaS 之間運作，以便擷取、準備、轉換、分析及發行您的資料。 使用 Data Factory 將服務撰寫到受管理的資料流程管線來轉換資料類 [Azure HDInsight (Hadoop)](http://azure.microsoft.com/documentation/services/hdinsight/) 和 [Azure 批次](http://azure.microsoft.com/documentation/services/batch/) 巨量資料的運算需求，以及與 [Azure Machine Learning](http://azure.microsoft.com/documentation/services/machine-learning/) 將您的分析解決方案的運作。 不僅僅是表格式的監視檢視，還使用了豐富的視覺效果呈現 Data Factory，來快速顯示歷程和資料管線之間的相依性。 從單一的統一檢視來監視所有的資料流程管線，輕鬆找出問題並設定監視警示。

![概觀](./media/data-factory-introduction/data-factory-overview.png)

**圖 1。**從許多不同的內部部署資料來源收集資料、擷取和準備資料，透過各種轉換來組織並分析資料，然後發行隨時可用的資料以供使用。

您可以視需求隨時使用 Data Factory，在可靠的排程下收集各種外型和大小的資料，並加以轉換和發行，以便得到深入的了解。 Data Factory 用來建立高度可用的資料流管線，適用於不同產業中的許多案例並滿足其分析管的需求。 線上零售商會用它來產生個人化 [產品建議](data-factory-product-reco-usecase.md) 根據客戶瀏覽行為。 遊戲工作室利用它來了解 [的行銷效率](data-factory-customer-profiling-usecase.md) 活動。 直接向我們的客戶學習他們如何及為何使用 Data Factory，藉由檢閱 [客戶案例研究](data-factory-customer-case-studies.md)。

> [AZURE.VIDEO azure-data-factory-overview]

## 重要概念

Azure Data Factory 有幾個主要實體會共同運作，來定義輸入和輸出資料、處理事件以及執行指定之資料流程所需的排程和資源。

![重要概念](./media/data-factory-introduction/key-concepts.png)

**圖 2.**資料集、活動、管線和連結服務之間的關聯性。


### 活動

活動會定義在您資料上執行的動作。 每個活動會取得零或多個 [資料集](data-factory-create-datasets.md) 做為輸入，並產生一或多個資料集做為輸出。 活動代表 Azure Data Factory 中的協調流程單位。 例如，您可以使用 [複製活動](data-factory-data-movement-activities.md) ，以協調從一個資料集複製資料到另一個。 同樣地，您可能使用 [Hive 活動](data-factory-data-transformation-activities.md) 這將會在 Azure HDInsight 叢集來轉換或分析您的資料上執行 Hive 查詢。 Azure Data Factory 提供大量的資料轉換、分析和資料移動活動。

### 管線

[管線](data-factory-create-pipelines.md) 是活動的邏輯群組。 可用來將活動群組成一個單位，共同執行任務。 比方說，清除記錄檔資料可能需要照順序進行數個轉換活動。 此順序可能會有複雜的排程和相依性，需要加以協調流程並自動化。 這些活動全都可以群組成名為 “CleanLogFiles” 的單一管線。 "CleanLogFiles"可以再部署、 排程，或刪除一個單一單位，而不是獨立管理每個個別的活動。

### 資料集

[資料集](data-factory-create-datasets.md) 命名參考/指標的資料，您想要做為輸入或輸出的活動。 資料集會在包括資料表、檔案、資料夾和文件在內的各種資料存放區中，識別資料結構。

### 連結的服務

連結的服務會定義 Data Factory 所需的資訊，以便連接到外部資源。 Data Factory 中的連結服務，有兩個用途：

- 代表資料存放區，但不是限於，包括內部部署 SQL Server、 Oracle DB、 檔案共用或 Azure Blob 儲存體帳戶。 如前所述，資料集代表透過連結服務連接到 Data Factory 之資料存放區中的架構。
- 用來代表可裝載活動執行的計算資源。 例如，“HDInsightHive Activity” 會在 HDInsight Hadoop 叢集上執行。

有了資料集、活動、管線和連結的服務這四個簡單的概念之後，您隨時可以開始使用！ 您可以 [建置您的第一個管線](data-factory-build-your-first-pipeline.md)  從頭，或依照下列中的指示，部署現成的樣本我們 [Data Factory 範例](data-factory-samples.md) 文件。







