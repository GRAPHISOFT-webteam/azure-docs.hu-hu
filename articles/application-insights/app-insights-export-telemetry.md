<properties 
    pageTitle="從 Application Insights 的遙測連續匯出" 
    description="匯出診斷和使用量資料至 Microsoft Azure 中的儲存體，並從那裡下載。" 
    services="application-insights" 
    documentationCenter=""
    authors="alancameronwills" 
    manager="douge"/>

<tags 
    ms.service="application-insights" 
    ms.workload="tbd" 
    ms.tgt_pltfrm="ibiza" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="11/15/2015" 
    ms.author="awills"/>
 
# 從 Application Insights 匯出遙測

想要對您的遙測執行一些自訂的分析？ 或也許您想要對具有特定屬性的事件以電子郵件寄送警示？ 連續匯出很適合此用途。 在 Application Insights 入口網站中看見的事件，可以使用 JSON 格式匯出到 Microsoft Azure 中的儲存體。 從那裡，您可以下載資料並編寫處理所需的任何程式碼。  

取得連續匯出的免費試用期間，以及在 [標準及高階定價方案](http://azure.microsoft.com/pricing/details/application-insights/)。

(如果您只想要 [一次性匯出](app-insights-metrics-explorer.md#export-to-excel) 的您看到度量或搜尋刀鋒視窗中，按一下 [匯出] 刀鋒視窗的頂端。 如果您想要在 Power BI 中查看資料，使用 [配接器](http://blogs.msdn.com/b/powerbi/archive/2015/11/04/explore-your-application-insights-data-with-power-bi.aspx) ，亦 *不* 使用連續匯出。)

## 建立儲存體帳戶

如果您還沒有「傳統」儲存體帳戶，請立即建立一個。


1. 建立 「 傳統 」 的儲存體帳戶中的訂閱 [Azure 入口網站](https://portal.azure.com)。

    ![在 Azure 入口網站中，依序選擇 [新增]、[資料]、[儲存體]](./media/app-insights-export-telemetry/030.png)

2. 建立容器。

    ![在新的儲存體中，選取 [容器]，按一下容器磚，然後按一下 [新增]](./media/app-insights-export-telemetry/040.png)

## <a name="setup"></a> 設定連續匯出

在 Application Insights 入口網站中應用程式的 [概觀] 刀鋒視窗上，開啟 [連續匯出]： 

![向下捲動並按一下 [連續匯出]](./media/app-insights-export-telemetry/01-export.png)

加入匯出，然後選擇 [ [Azure 儲存體帳戶](../storage-introduction.md) 您想要放置資料的位置:

![按一下 [加入]、[匯出目的地]、[儲存體帳戶]，然後建立新儲存區或選擇現有儲存區](./media/app-insights-export-telemetry/02-add.png)

選擇您想要匯出的事件類型：

![按一下 [選擇事件類型]](./media/app-insights-export-telemetry/03-types.png)


建立匯出之後，就會開始進行。 (您只會取得建立匯出之後送抵的資料。) 

資料出現在 Blob 中之前可能有大約一小時的延遲。

如果稍後想要變更事件類型，只需要編輯匯出：

![按一下 [選擇事件類型]](./media/app-insights-export-telemetry/05-edit.png)

若要停止資料流，請按一下 [停用]。 再次按一下 [啟用] 時，資料流將會以新資料重新啟動。 您無法取得在停用匯出時送抵入口網站的資料。

若要永久停止資料流，請刪除匯出。 這麼做不會將您的資料從儲存體刪除。

#### 無法加入或變更匯出？

* 若要加入或變更匯出，您需要擁有者、參與者或 Application Insights 參與者存取權。 [了解角色][roles]。

## <a name="analyze"></a> 您取得什麼事件?

匯出的資料是我們從您的應用程式接收的原始遙測，只不過我們加入了從用戶端 IP 位址計算的位置資料。 

未包含其他計算的度量。 例如，我們不會匯出平均 CPU 使用率，但我們會匯出用以計算平均的原始遙測。

資料也包含結果的任何 [可用性 web 測試](app-insights-monitor-web-app-availability.md) 已設定。 

> [AZURE.NOTE] **取樣。**如果您的應用程式傳送大量資料，且您是使用 Application Insights SDK for ASP.NET 版本 2.0.0-beta3 或更新版本，則調適性取樣功能可能會運作，並只傳送一部分的遙測資料。 [進一步了解取樣。](app-insights-sampling.md)

## <a name="get"></a> 檢查資料

您可以直接在入口網站中檢查儲存體。 按一下 [ **瀏覽**, 、 選取儲存體帳戶，然後再開啟 **容器**。

若要檢查 Visual Studio 中的 Azure 儲存體，請開啟 **檢視**, ，**雲端總管**。 (如果您沒有該功能表命令，您需要安裝 Azure SDK: 開啟 **新的專案** ] 對話方塊中，展開 [Visual C# / 雲端，然後選擇 **取得 Microsoft Azure SDK for.NET**。)

當您開啟 Blob 存放區時，您會看到含有一組 Blob 檔案的容器。 衍生自您 Application Insights 的資源名稱、其檢測金鑰、遙測-類型/日期/時間之每個檔案的 URI。 (資源名稱全部小寫，而檢測金鑰會省略連字號。)

![使用適合的工具檢查 Blob 儲存區](./media/app-insights-export-telemetry/04-data.png)

日期和時間為 UTC，並且是遙測存放在儲存區的時間 - 而不是產生的時間。 因此，如果您編寫程式碼來下載資料，它可以透過資料線性地移動。


## <a name="format"></a> 資料格式

* 每個 Blob 是包含多個以 '\n' 分隔的列的文字檔案。
* 每列是未格式化的 JSON 文件。 如果您想要靜靜地仔細觀看，請在 Visual Studio 中開啟，並依序選擇 [編輯]、[進階]、[格式檔案]：

![使用合適的工具檢視遙測](./media/app-insights-export-telemetry/06-json.png)

時間期間依刻度為單位，10000 刻度 = 1 毫秒。 例如，這些值顯示從瀏覽器傳送要求用了 10 毫秒的時間，接收它用了 30 毫秒，以及在瀏覽器中處理頁面用了 1.8 秒：

    "sendRequest": {"value": 10000.0},
    "receiveRequest": {"value": 30000.0},
    "clientProcess": {"value": 17970000.0}

[屬性類型和值的詳細資料模型參考。](app-insights-export-data-model.md)

## 處理資料

就小型規模而言，您可以編寫一些程式碼來取出您的資料，將它讀取為試算表等等。 例如：

    private IEnumerable<T> DeserializeMany<T>(string folderName)
    {
      var files = Directory.EnumerateFiles(folderName, "*.blob", SearchOption.AllDirectories);
      foreach (var file in files)
      {
         using (var fileReader = File.OpenText(file))
         {
            string fileContent = fileReader.ReadToEnd();
            IEnumerable<string> entities = fileContent.Split('\n').Where(s => !string.IsNullOrWhiteSpace(s));
            foreach (var entity in entities)
            {
                yield return JsonConvert.DeserializeObject<T>(entity);
            }
         }
      }
    }

如需較大型的程式碼範例，請參閱 [使用背景工作角色][exportasa]。



## <a name="delete"></a>刪除舊資料
請注意，您負責管理儲存容量，以及在必要時刪除舊資料。 

## 如果您重新產生儲存體金鑰...

如果您變更儲存體的金鑰，連續匯出將停止運作。 您將在 Azure 帳戶中看到通知。 

開啟 [連續匯出] 分頁，並編輯您的匯出。 編輯 [匯出目的地]，但只保留選取相同的儲存體。 按一下 [確定] 以確認。

![編輯連續匯出，開啟並關閉匯出目的地。](./media/app-insights-export-telemetry/07-resetstore.png)

連續匯出將重新開始。

## 匯出範例


* [使用背景工作角色匯出至 SQL][exportcode]
* [使用串流分析匯出至 SQL][exportasa]
* [使用串流分析匯出至 Power BI](app-insights-export-power-bi.md)
 * 請注意這不是使用 Power BI 的標準方式。 沒有 [配接器](http://blogs.msdn.com/b/powerbi/archive/2015/11/04/explore-your-application-insights-data-with-power-bi.aspx) 這並不需要連續匯出。


在更大型規模而言，請考慮 [HDInsight](http://azure.microsoft.com/services/hdinsight/) -雲端中的 Hadoop 叢集。 HDInsight 提供各種技術，用於管理和分析巨量資料。



## 問答集

* *但我想要的只是一次性下載圖表。*  
 
    是的，您可以這麼做。 在刀鋒視窗的頂端，按一下 [ [匯出資料](app-insights-metrics-explorer.md#export-to-excel)。

* *我設定匯出，但我的儲存區中沒有資料。*

    自從設定匯出之後，Application Insights 是否從您的應用程式收到任何遙測？ 您將只會收到新資料。

* *我嘗試設定匯出，但被拒絕存取*

    如果帳戶是組織所擁有，您必須是擁有者或參與者群組的成員。

    <!-- Your account has to be either a paid-for account, or in the free trial period. -->

* *我是否能直接匯出到我自己的內部部署儲存區？* 

    否，抱歉。 我們的匯出引擎目前僅適用於 Azure 儲存體。  

* *放置在我的儲存區中的資料量有任何限制？* 

    編號 我們將持續送入資料，直到刪除匯出為止。 如果我們到達 Blob 儲存體的外部限制，將會停止，但那個限制很大。 您可以自行控制使用的儲存體數量。  

* *應該在儲存體中看到多少 Blob？*

 * 針對您選取要匯出的每個資料類型，會每分鐘建立一個新的 Blob (如果有可用的資料)。 
 * 此外，針對具有高流量的應用程式，則會配置額外的分割單位。 在此情況下，每個單位會每分鐘建立一個 Blob。


* *我對我的儲存體重新產生了金鑰，或變更了容器的名稱，現在匯出沒有作用。*

    編輯匯出並開啟匯出目的地分頁。 照舊保留選取相關的儲存體，並按一下 [確定] 來確認。 匯出將重新開始。 如果變更是在最近幾天內，您不會遺失資料。

* *我可以暫停匯出嗎？*

    是。 按一下 [停用]。

## 程式碼範例

* [從 Application Insights 串流處理到 Power BI](app-insights-export-power-bi.md)
* [使用背景工作角色剖析匯出的 JSON][exportcode]
* [使用串流分析匯出至 SQL][exportasa]

* [屬性類型和值的詳細資料模型參考。](app-insights-export-data-model.md)

<!--Link references-->

[exportcode]: app-insights-code-sample-export-telemetry-sql-database.md
[exportasa]: app-insights-code-sample-export-sql-stream-analytics.md
[roles]: app-insights-resources-roles-access-control.md

 

