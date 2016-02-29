<properties
   pageTitle="如何使用「巨量資料」資料來源"
   description="反白顯示 「 巨型資料 」 資料來源，包括 Azure Blob 儲存體和 Hadoop HDFS 搭配使用 Azure 資料目錄的模式的操作說明文章。"
   services="data-catalog"
   documentationCenter=""
   authors="steelanddata"
   manager="NA"
   editor=""
   tags=""/>
<tags
   ms.service="data-catalog"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="data-catalog"
   ms.date="11/20/2015"
   ms.author="maroche"/>


# 如何使用 Azure 資料目錄中的巨量資料來源

## 簡介
**Microsoft Azure 資料目錄** 是完全受管理的雲端服務，可作為的註冊系統和探索企業資料來源系統。 換句話說， **Azure 資料目錄** 的重點，協助使用者探索、 了解，以及使用資料來源，並可協助組織從現有的資料來源，包括巨量資料獲得更多價值。

**Azure 資料目錄** 支援註冊 Azure 部落格儲存體 blob 和目錄，以及 Hadoop HDFS 檔案和目錄。 這些資料來源的半結構化的本質提供絕佳的彈性，但是這也表示使用者必須考慮資料來源以進行註冊以取得最大價值的組織方式 **Azure 資料目錄**。

## 將目錄視為邏輯資料集

組織巨量資料來源的常見模式是將目錄視為邏輯資料集。 最上層目錄可用來定義資料集，子資料夾定義資料分割，而它們所包含的檔案儲存資料本身。

此模式的可能範例如下：

    \vehicle_maintenance_events
        \2013
        \2014
        \2015
            \01
                \2015-01-trailer01.csv
                \2015-01-trailer92.csv
                \2015-01-canister9635.csv
                ...
    \location_tracking_events
        \2013
        ...

在此範例中，vehicle_maintenance_events 和 location_tracking_events 代表邏輯資料集。 上述每個資料夾包含資料檔案，依據年份和月份組織成子資料夾。 上述每個資料夾可能包含數百或數千個檔案。

在此模式中，註冊個別的檔案與 **Azure 資料目錄** 可能毫無意義。 應該註冊代表資料集的目錄，對使用資料的使用者才有意義。

## 參考資料檔案

互補模式是將參考資料集儲存為個別檔案。 這些資料集可視為巨量資料的「小」部分，通常類似於分析資料模型中的維度。 參考資料檔案中的記錄，可以為巨量資料存放區中儲存在其他地方的大量資料檔案提供情境脈絡。

此模式的可能範例如下：

    \vehicles.csv
    \maintenance_facilities.csv
    \maintenance_types.csv

當分析師或資料科學家使用較大目錄結構中包含的資料時，這些參考檔案中的資料可以為較大資料集裡只依名稱或識別碼來參考的實體，提供更詳細的資訊。

在此模式中，它會合理註冊個別參考的資料檔案與 **Azure 資料目錄**。 每個檔案都代表資料集，且每個都可以加上附註和個別地探索。

## 替代模式

上述模式只是巨量資料存放區的兩種可能的組織方式，但是每一種實作各不相同。 不論您的資料來源結構方式，具有註冊大量的資料來源時 **Azure 資料目錄**, ，專注於註冊的檔案和目錄代表其他人在組織內的值會是資料集。 註冊所有檔案和目錄可能造成目錄雜亂，導致使用者難以找所需的資料。

## 摘要
註冊資料來源與 **Azure 資料目錄** 使其更容易探索和了解。 您可以註冊並加註代表邏輯資料集的巨量資料檔案和目錄，以協助使用者找到並使用所需的巨量資料來源。

