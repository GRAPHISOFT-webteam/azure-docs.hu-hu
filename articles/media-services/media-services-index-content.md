<properties
    pageTitle="使用 Azure Media Indexer 編輯媒體檔案索引"
    description="Azure Media Indexer 讓您能將媒體檔案的內容變成可搜尋，並產生隱藏式字幕和關鍵字的全文檢索記錄。本主題說明如何使用 Media Indexer。"
    services="media-services"
    documentationCenter=""
    authors="Juliako,Asolanki,johndeu"
    manager="dwrede"
    editor=""/>

<tags
    ms.service="media-services"
    ms.workload="media"
    ms.tgt_pltfrm="na"
    ms.devlang="dotnet"
    ms.topic="article"
    ms.date="10/15/2015"   
    ms.author="juliako"/>



# 使用 Azure Media Indexer 編輯媒體檔案索引

> [AZURE.SELECTOR]
- [Portal](media-services-manage-content.md#index)
- [.NET](media-services-index-content.md)



Azure Media Indexer 讓您能將媒體檔案的內容變成可搜尋，並產生隱藏式字幕和關鍵字的全文檢索記錄。 您可以處理一份媒體檔或是批次處理多個媒體檔案。
>[AZURE.IMPORTANT] 在編製內容索引時，請務必使用語音非常清楚的媒體檔案 (不含背景音樂、噪音、效果或麥克風雜音)。 適當內容的一些範例有：錄製的會議、演講或簡報。 下列內容可能不適合用來編製索引：電影、電視節目、任何具有混合音訊與音效的內容、錄製效果不良有背景噪音 (雜音) 的內容。


索引工作可產生下列輸出檔案：

- 下列格式的隱藏式輔助字幕檔案：**SAMI**、**TTML** 和 **WebVTT**。

    隱藏式輔助字幕檔案包含稱為 Recognizability 的標記，它會根據來源視訊中的語音可辨識度來為索引工作評分。 您可以使用 Recognizability 值篩選輸出檔的實用性。 較低的分數表示由於音訊品質所致的不良索引結果。
- 關鍵字檔案 (XML)。
- 與 SQL Server 搭配使用的音訊編製索引 blob 檔案 (AIB)。

    如需詳細資訊，請參閱 [搭配 Azure Media Indexer 和 SQL Server 使用 AIB 檔案](http://azure.microsoft.com/blog/2014/11/03/using-aib-files-with-azure-media-indexer-and-sql-server/)。


本主題示範如何建立索引工作**建立資產的索引**和**建立多個檔案的索引**。

最新的 Azure Media Indexer 更新，請參閱 [媒體服務部落格](#preset)。

## 針對索引工作使用組態和資訊清單檔

您可以使用工作組態來為索引工作指定更多詳細資料。 例如，您可以指定要用於媒體檔案的中繼資料。 語言引擎會使用此中繼資料來擴充其詞彙，並大幅提升語音辨識準確度。 您也可以指定想要的輸出檔案。

您也可以使用資訊清單檔，一次處理多個媒體檔案。

如需詳細資訊，請參閱 [Azure Media Indexer 的工作預設](#)。

## 編製資產索引

下列方法會將媒體檔案上傳為資產，並建立工作來編製資產索引。

請注意，如果未指定組態檔案，將會使用所有預設設定編製媒體檔案的索引。

    static bool RunIndexingJob(string inputMediaFilePath, string outputFolder, string configurationFile = "")
    {
        // Create an asset and upload the input media file to storage.
        IAsset asset = CreateAssetAndUploadSingleFile(inputMediaFilePath,
            "My Indexing Input Asset",
            AssetCreationOptions.None);
    
        // Declare a new job.
        IJob job = _context.Jobs.Create("My Indexing Job");
    
        // Get a reference to the Azure Media Indexer.
        string MediaProcessorName = "Azure Media Indexer",
        IMediaProcessor processor = GetLatestMediaProcessorByName(MediaProcessorName);
    
        // Read configuration from file if specified.
        string configuration = string.IsNullOrEmpty(configurationFile) ? "" : File.ReadAllText(configurationFile);
    
        // Create a task with the encoding details, using a string preset.
        ITask task = job.Tasks.AddNew("My Indexing Task",
            processor,
            configuration,
            TaskOptions.None);
    
        // Specify the input asset to be indexed.
        task.InputAssets.Add(asset);
    
        // Add an output asset to contain the results of the job.
        task.OutputAssets.AddNew("My Indexing Output Asset", AssetCreationOptions.None);
    
        // Use the following event handler to check job progress.  
        job.StateChanged += new EventHandler<JobStateChangedEventArgs>(StateChanged);
    
        // Launch the job.
        job.Submit();
    
        // Check job execution and wait for job to finish.
        Task progressJobTask = job.GetExecutionProgressTask(CancellationToken.None);
        progressJobTask.Wait();
    
        // If job state is Error, the event handling
        // method for job progress should log errors.  Here we check
        // for error state and exit if needed.
        if (job.State == JobState.Error)
        {
            Console.WriteLine("Exiting method due to job error.");
            return false;
        }
    
        // Download the job outputs.
        DownloadAsset(task.OutputAssets.First(), outputFolder);
    
        return true;
    }
    
    static IAsset CreateAssetAndUploadSingleFile(string filePath, string assetName, AssetCreationOptions options)
    {
        IAsset asset = _context.Assets.Create(assetName, options);
    
        var assetFile = asset.AssetFiles.Create(Path.GetFileName(filePath));
        assetFile.Upload(filePath);
    
        return asset;
    }
    
    static void DownloadAsset(IAsset asset, string outputDirectory)
    {
        foreach (IAssetFile file in asset.AssetFiles)
        {
            file.Download(Path.Combine(outputDirectory, file.Name));
        }
    }
    
    static IMediaProcessor GetLatestMediaProcessorByName(string mediaProcessorName)
    {
        var processor = _context.MediaProcessors
        .Where(p => p.Name == mediaProcessorName)
        .ToList()
        .OrderBy(p => new Version(p.Version))
        .LastOrDefault();
    
        if (processor == null)
            throw new ArgumentException(string.Format("Unknown media processor",
                                                       mediaProcessorName));
    
        return processor;
    }  

### <a id="output_files"></a>輸出檔

索引工作預設會產生下列輸出檔案。 檔案會儲存在第一個輸出資產。

當有多個輸入媒體檔案時，索引子會產生工作輸出的資訊清單檔，名為 'JobResult.txt'。 每個輸入媒體檔案，產生的 AIB、 SAMI、 TTML、 WebVTT 和關鍵字檔案會循序編號和名為使用 「 別名 」。

 檔案名稱| 說明
----------|------------
 __InputFileName.aib__| 音訊索引 blob 檔案。<br /><br /> 音訊索引 Blob (AIB) 檔案是在 Microsoft SQL server 中使用全文檢索搜尋可搜尋的二進位檔案。AIB 檔比簡單的字幕檔案強大，因為它包含每個字的替代字，允許更豐富的搜尋經驗。<br/> <br/>它需要在電腦執行 Microsoft SQL server 2008 或更新版本上 Indexer SQL 附加元件的安裝。使用 Microsoft SQL Server 全文檢索搜尋來搜尋 AIB 可以比搜尋 WAMI 產生之隱藏式字幕檔案提供更正確的搜尋結果。這是因為 AIB 包含發音類似的替代字，而隱藏式字幕檔案則包含音訊每一節的最高信賴字。如果搜尋說是最重要的它被建議搭配 Microsoft SQL Server 使用 Aib 搭配使用。<br/><br/>若要下載附加元件，請按一下 [ <a href="http://aka.ms/indexersql">Azure Media Indexer SQL 附加元件</a>。<br/><br/>也可以利用其他搜尋引擎，例如 Apache Lucene/Solr 直接編製索引的影片，根據隱藏式輔助字幕和關鍵字 XML 檔案，但這會導致較不精確的搜尋結果。
 __InputFileName.smi__<br />__InputFileName.ttml__<br />__InputFileName.vtt__| SAMI、 TTML 和 WebVTT 格式隱藏式字幕 (CC) 檔案。<br/><br/>可用於讓音訊和視訊檔案聽力障礙人士。<br/><br/>關閉的字幕檔案包含稱為標記 <b>Recognizability</b> 可辨識的來源視訊中的語音會根據為索引工作評分。您可以使用的值 <b>Recognizability</b> 螢幕輸出檔的實用性。較低的分數表示由於音訊品質所致的不良索引結果。
 __InputFileName.kw.xml<br />InputFileName.info__| 關鍵字與資訊檔案。<br/><br/>關鍵字檔案是 XML 檔案，其中包含從語音內容擷取，以及頻率和位移的資訊的關鍵字。<br/><br/>資訊檔案是純文字檔，其中包含精細辨識每個詞彙相關資訊。第一行是特殊行並包含可辨識分數。後續每一行皆是下列資料的清單 (以 tab 鍵分隔)：開始時間、結束時間、文字/片語、信賴值。時間是以秒為單位，信賴值則是以 0-1 的數字標示。<br/><br/>範例行: 「 1.20 1.45 word 0.67 」 <br/><br/>這些檔案可以用於多種用途，例如執行語音分析，或公開至如 Bing、 Google 或 Microsoft SharePoint，使媒體檔案更容易被找到，或甚至是用來提供更相關的廣告搜尋引擎。
 __JobResult.txt__| Output manifest, present only when indexing multiple files, containing the following information:<br/><br/><table border="1"><tr><th>InputFile</th><th>Alias</th><th>MediaLength</th><th>Error</th></tr><tr><td>a.mp4</td><td>Media_1</td><td>300</td><td>0</td></tr><tr><td>b.mp4</td><td>Media_2</td><td>0</td><td>3000</td></tr><tr><td>c.mp4</td><td>Media_3</td><td>600</td><td>0</td></tr></table><br/>



如果不是所有輸入媒體檔案都成功編製索引，則索引工作將會失敗，錯誤碼為 4000。 如需詳細資訊，請參閱 [錯誤碼](#error_codes)。

## 編製多個檔案的索引

下列方法會將多個媒體檔案上傳為資產，並建立工作來批次編製這些檔案的索引。

會建立 .lst 副檔名的資訊清單檔，並上傳到資產。 資訊清單檔案包含所有資產檔案的清單。 如需詳細資訊，請參閱 [Azure Media Indexer 的工作預設](https://msdn.microsoft.com/library/azure/dn783454.aspx)。

    static bool RunBatchIndexingJob(string[] inputMediaFiles, string outputFolder)
    {
        // Create an asset and upload to storage.
        IAsset asset = CreateAssetAndUploadMultipleFiles(inputMediaFiles,
            "My Indexing Input Asset - Batch Mode",
            AssetCreationOptions.None);
    
        // Create a manifest file that contains all the asset file names and upload to storage.
        string manifestFile = "input.lst";            
        File.WriteAllLines(manifestFile, asset.AssetFiles.Select(f => f.Name).ToArray());
        var assetFile = asset.AssetFiles.Create(Path.GetFileName(manifestFile));
        assetFile.Upload(manifestFile);
    
        // Declare a new job.
        IJob job = _context.Jobs.Create("My Indexing Job - Batch Mode");
    
        // Get a reference to the Azure Media Indexer.
        string MediaProcessorName = "Azure Media Indexer";
        IMediaProcessor processor = GetLatestMediaProcessorByName(MediaProcessorName);
    
        // Read configuration.
        string configuration = File.ReadAllText("batch.config");
    
        // Create a task with the encoding details, using a string preset.
        ITask task = job.Tasks.AddNew("My Indexing Task - Batch Mode",
            processor,
            configuration,
            TaskOptions.None);
    
        // Specify the input asset to be indexed.
        task.InputAssets.Add(asset);
    
        // Add an output asset to contain the results of the job.
        task.OutputAssets.AddNew("My Indexing Output Asset - Batch Mode", AssetCreationOptions.None);
    
        // Use the following event handler to check job progress.  
        job.StateChanged += new EventHandler<JobStateChangedEventArgs>(StateChanged);
    
        // Launch the job.
        job.Submit();
    
        // Check job execution and wait for job to finish.
        Task progressJobTask = job.GetExecutionProgressTask(CancellationToken.None);
        progressJobTask.Wait();
    
        // If job state is Error, the event handling
        // method for job progress should log errors.  Here we check
        // for error state and exit if needed.
        if (job.State == JobState.Error)
        {
            Console.WriteLine("Exiting method due to job error.");
            return false;
        }
    
        // Download the job outputs.
        DownloadAsset(task.OutputAssets.First(), outputFolder);
    
        return true;
    }
    
    private static IAsset CreateAssetAndUploadMultipleFiles(string[] filePaths, string assetName, AssetCreationOptions options)
    {
        IAsset asset = _context.Assets.Create(assetName, options);
    
        foreach (string filePath in filePaths)
        {
            var assetFile = asset.AssetFiles.Create(Path.GetFileName(filePath));
            assetFile.Upload(filePath);
        }
    
        return asset;
    }

### 部分成功的工作

如果不是所有輸入媒體檔案都成功編製索引，則索引工作將會失敗，錯誤碼為 4000。 如需詳細資訊，請參閱 [錯誤碼](#error_codes)。


會產生 (與成功工作) 相同的輸出。 您可以參閱輸出資訊清單檔，根據 Error 欄位值找出哪些輸入檔案失敗。 針對失敗的輸入檔案，將不會產生結果的 AIB、SAMI、TTML、WebVTT 和關鍵字檔案。

### <a id="preset"></a> Azure Media Indexer 的工作預設值

在工作旁邊提供選擇性工作預設，即可自訂從 Azure 媒體索引器處理。 以下說明此組態 xml 的格式。

 名稱| 需要| 說明
----|----|---
 __輸入__| false| 您想要編製索引的資產檔案。</p><p>Azure Media Indexer 支援下列媒體檔案格式: MP4、 WMV、 MP3、 M4A、 WMA、 AAC、 WAV。</p><p>您可以指定檔案名稱 (s) 中 **名稱** 或 **清單** 屬性 **輸入** 項目 (如下所示)。如果未指定要索引的資產檔案，就會挑出主要檔案。如果沒有主要資產檔案設定]，輸入資產中的第一個檔案編製索引。</p><p>若要明確指定資產檔案名稱，請執行:<br />`< 輸入 name="TestFile.wmv">`<br /><br />您也可以索引多個資產檔案一次 (最多 10 個檔案)。若要這樣做:<br /><br /><ol class="ordered"><li><p>建立文字檔 (資訊清單檔案)，並為它指定.lst 副檔名。</p></li><li><p>您輸入資產中加入資訊清單檔案的所有資產檔案名稱清單。</p></li><li><p>新增 (上傳) thanifest 檔案到資產。</p></li><li><p>指定的輸入清單屬性中的資訊清單檔案的名稱。<br />`< 輸入 list="input.lst">`</li></ol><br /><br />附註: 如果您超過 10 個檔案新增至資訊清單檔案時，索引工作會失敗並出現 2006年錯誤碼。
 __中繼資料__| false| 用於詞彙調節之指定資產檔案的中繼資料。若要準備索引子以辨識非標準的詞彙例如專有名詞的單字很有用。<br />`< 中繼資料索引鍵 ="..."value="..."/ >` <br /><br />可以提供 __值__ 的預先定義的 __金鑰__。目前支援下列機碼:<br /><br />「 標題 」 和 「 描述 」-調整語言詞彙調適用於您的工作的模型，並增進語音辨識準確度。值植入網際網路搜尋來尋找內容相關的文字文件，使用內容加強內部字典索引工作的持續時間。<br />`< 中繼資料索引鍵 ="title"value ="[Title 的媒體檔案]"/ >`<br />`< 中繼資料索引鍵 ="description"value ="媒體檔案描述 / > 「`
 __功能__ <br /><br /> 1.2 版中加入。目前唯一支援的功能是語音辨識 ("ASR")。| false| 語音辨識功能具有下列設定機碼:<table><tr><th><p>金鑰</p></th>     <th><p>描述</p></th><th><p>範例值</p></th></tr><tr><td><p>語言</p></td><td><p>自然語言，能夠辨識多媒體檔案中。</p></td><td><p>English, Spanish</p></td></tr><tr><td><p>CaptionFormats</p></td><td><p>a semicolon-separated list of the desired output caption formats (if any)</p></td><td><p>ttml;sami;webvtt</p></td></tr><tr><td><p>GenerateAIB</p></td><td><p>布林旗標，指定 AIB 檔案時是否需要 (使用於 SQL Server 和客戶索引子 IFilter)。     如需詳細資訊，請參閱 <a href="http://azure.microsoft.com/blog/2014/11/03/using-aib-files-with-azure-media-indexer-and-sql-server/">搭配 Azure Media Indexer 和 SQL Server 使用 AIB 檔案</a>。</p></td><td><p>True;False</p></td></tr><tr><td><p>GenerateKeywords</p></td><td><p>布林旗標，指定關鍵字 XML 檔案是否必要。</p></td><td><p>True;值為 false。</p></td></tr><tr><td><p>ForceFullCaption</p></td><td><p>布林旗標，指定是否要強制完整字幕 (不論信心層級)。</p><p>預設為 false，在此情況下單字和片語具有較少的 50%的信心層級會從最後一個標題輸出中省略和被省略符號 ("…") 取代。省略符號是適用於標題品質控制和稽核。</p></td><td><p>True;值為 false。 </p></td></tr></table>

### <a id="error_codes"></a>錯誤代碼

如果發生錯誤，Azure 媒體索引器應回報下列其中一個錯誤碼：

 代碼| 名稱| 可能的原因
-----|------|------------------
 2000| 無效的組態| 無效的組態
 2001| 無效的輸入資產| 遺漏輸入資產或資產是空的。
 2002| 無效的資訊清單| 資訊清單是空的或資訊清單包含無效的項目。
 2003| 無法下載媒體檔案| 資訊清單檔中的 URL 無效。
 2004| 不支援的通訊協定| 不支援媒體 URL 的通訊協定。
 2005| 不支援的檔案類型| 不支援輸入媒體檔案類型。
 2006| 太多輸入檔案| 輸入資訊清單中有超過 10 個檔案。
 3000| 無法解碼媒體檔案| 不支援的媒體轉碼器 <br/>或<br/> 已損毀的媒體檔案 <br/>或<br/> 輸入媒體沒有音訊資料流。
 4000| 批次編製索引已部分成功| 某些輸入媒體檔案無法編製索引。如需詳細資訊，請參閱 <a href="output_files">輸出檔</a>。
 其他| 內部錯誤| 請連絡技術支援小組。indexer@microsoft.com


## <a id="supported_languages"></a>支援的語言

目前支援英文和西班牙文。 如需詳細資訊，請參閱 [v1.2 版本部落格文章](http://azure.microsoft.com/blog/2015/04/13/azure-media-indexer-spanish-v1-2/)。


## 媒體服務學習路徑

[AZURE.INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

## 提供意見反應

[AZURE.INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]



## 相關連結

[搭配 Azure Media Indexer 和 SQL Server 使用 AIB 檔案](http://azure.microsoft.com/blog/2014/11/03/using-aib-files-with-azure-media-indexer-and-sql-server/)











