<properties 
    pageTitle="媒體服務版本資訊" 
    description="媒體服務版本資訊" 
    services="media-services" 
    documentationCenter="" 
    authors="Juliako" 
    manager="dwrede" 
    editor=""/>

<tags 
    ms.service="media-services" 
    ms.workload="media" 
    ms.tgt_pltfrm="media" 
    ms.devlang="dotnet" 
    ms.topic="article" 
    ms.date="12/09/2015"   
    ms.author="juliako"/>


# Azure 媒體服務版本資訊

這些版本資訊彙總了舊版的變更和已知問題。

>[AZURE.NOTE] 我們想要聽聽我們的客戶，並專注於修正會影響您的問題。 若要回報問題或提問，請張貼在 [Azure Media Services MSDN Forum]。

- [目前的已知問題](#issues)
- [REST API 版本歷程記錄](#rest_version_history)
- [2015 年 12 月版本](#dec_changes_15)
- [2015 年 11 月版本](#nov_changes_15)
- [2015 年 10 月版本](#oct_changes_15)
- [2015 年 9 月版本](#september_changes_15)
- [2015 年 8 月版本](#august_changes_15)
- [2015 年 7 月版本](#july_changes_15)
- [2015 年 6 月版本](#june_changes_15)
- [2015 年 5 月版本](#may_changes_15)
- [2015 年 4 月版本](#april_changes_15)
- [2015 年 3 月版本](#march_changes_15)
- [2015 年 2 月版本](#february_changes_15)
- [2015 年 1 月版本](#january_changes_15)
- [2014 年 12 月版本](#december_changes_14)
- [2014 年 11 月版本](#november_changes_14)
- [2014 年 10 月版本](#october_changes_14)
- [2014 年 9 月版本](#september_changes_14)
- [2014 年 8 月版本](#august_changes_14)
- [2014 年 7 月版本](#july_changes_14)
- [2014 年 5 月版本](#may_changes_14)
- [2014 年 4 月版本](#april_changes_14) 
- [2014 年 1\2 月版本](#jan_feb_changes_14) 
- [2013 年 12 月版本](#december_changes_13)
- [2013 年 11 月版本](#november_changes_13)
- [2013 年 8 月版本](#august_changes_13)
- [2013 年 6 月版本](#june_changes_13)
- [2012 年 12 月版本](#december_changes_12)
- [2012 年 11 月版本](#november_changes_12)
- [2012 年 6 月預覽版本](#june_changes_12)


##<a id="issues"></a>目前的已知問題

### <a id="general_issues"></a>媒體服務一般問題

問題|說明
---|---
有幾個常用的 HTTP 標頭未提供於 REST API 中。|如果您使用 REST API 開發媒體服務應用程式，您會發現有些常用的 HTTP 標頭欄位 (包括 CLIENT-REQUEST-ID、REQUEST-ID 和 RETURN-CLIENT-REQUEST-ID) 不受支援。 這些標頭將在未來的更新中加入。
使用包含逸出字元 (例如 %20) 的檔案名稱為資產編碼時，作業會失敗，並出現「MediaProcessor：找不到檔案。」|要新增至資產並編碼的檔案，其名稱只能包含英數字元和空格。 此問題將在未來的更新中修正。
屬於 Azure Storage SDK 3.x 版的 ListBlobs 方法無法運作。|媒體服務產生 SAS Url 時是根據 [2012年-02-12](http://msdn.microsoft.com/library/azure/dn592123.aspx) 版本。 如果您要使用 Azure Storage SDK 列出 Blob 容器中的 Blob，請使用屬於 Azure Storage SDK 2.x 版的 [CloudBlobContainer.ListBlobs](http://msdn.microsoft.com/library/microsoft.windowsazure.storage.blob.cloudblobcontainer.listblobs.aspx) 方法。 屬於 Azure Storage SDK 3.x 版的 ListBlobs 方法將會失敗。
媒體服務節流機制會針對向服務發出過多要求的應用程式限制資源使用量。 服務可能會傳回「服務無法使用 (503)」HTTP 狀態碼。|如需詳細資訊，請參閱 503 HTTP 狀態碼，在描述 [Azure 媒體服務錯誤碼](http://msdn.microsoft.com/library/azure/dn168949.aspx) 主題。


### <a id="dotnet_issues"></a>Media Services SDK for .NET 問題

問題|說明
---|---
SDK 中的媒體服務物件無法序列化，因此無法與 Azure 快取搭配運作。|如果您嘗試序列化 SDK AssetCollection 物件以將其新增至 Azure 快取，將會擲回例外狀況。

##<a id="rest_version_history"></a>REST API 版本歷程記錄

如需媒體服務 REST API 版本歷程記錄資訊，請參閱 [Azure Media Services REST API Reference]。

##<a id="dec_changes_15"></a>2015 年 12 月版本

Azure SDK 小組發行新版的 [Azure SDK for PHP](http://github.com/Azure/azure-sdk-for-php) 套件，其中包含適用於 Microsoft Azure 媒體服務的更新和新功能。 特別是，Azure Media Services SDK for PHP 現在支援最新 [內容保護](media-services-content-protection-overview.md) 功能 ︰ 動態加密使用 AES 和 DRM （PlayReady 和 Widevine） 逾時或無限制的語彙基元。 它也支援調整 [編碼單元](media-services-dotnet-encoding-units.md)。

如需詳細資訊，請參閱：

-  [Microsoft Azure Media Services SDK for PHP](http://southworks.com/blog/2015/12/09/new-microsoft-azure-media-services-sdk-for-php-release-available-with-new-features-and-samples/) 部落格。
- 下列 [程式碼範例](http://github.com/Azure/azure-sdk-for-php/tree/master/examples/MediaServices) 可幫助您快速入門 ︰
    - **vodworkflow_aes.php**︰ 這是示範如何使用 aes-128 動態加密和金鑰傳遞服務的 PHP 檔案。 它根據所述的.NET 範例 [這](media-services-protect-with-aes128.md) 文件。
    - **vodworkflow_aes.php**︰ 這是示範如何使用 PlayReady 動態加密和授權傳遞服務的 PHP 檔案。 它根據所述的.NET 範例 [這](media-services-protect-with-drm.md) 文件。
    - **scale_encoding_units.php**︰ 這是示範如何調整編碼保留的單位的 PHP 檔案。


##<a id="nov_changes_15"></a>2015 年 11 月版本

Azure 媒體服務現在在雲端提供 Google Widevine 授權傳遞服務。 如需詳細資訊，請參閱 [此公告部落格](http://azure.microsoft.com/blog/announcing-google-widevine-license-delivery-services-public-preview-in-azure-media-services/)。 此外，請參閱 [本教學課程](media-services-protect-with-drm.md) 和 [GitHub 儲存機制](http://github.com/Azure-Samples/media-services-dotnet-dynamic-encryption-with-drm)。 

請注意，Azure 媒體服務所提供的 Widevine 授權傳遞服務為預覽狀態。 如需詳細資訊，請參閱 [這篇部落格](http://azure.microsoft.com/blog/announcing-google-widevine-license-delivery-services-public-preview-in-azure-media-services/)。

##<a id="oct_changes_15"></a>2015 年 10 月版本

Azure 媒體服務 (AMS) 已經上線中下列資料中心 ︰ 巴西南部、 印度西部、 印度南部和印度中央。 您現在可以使用 Azure 傳統入口網站來 [建立媒體服務帳戶](media-services-create-account.md#create-a-media-services-account-using-quick-create) 並執行各種工作所述 [這裡](https://azure.microsoft.com/documentation/services/media-services/)。 不過，這些資料中心不會啟用即時編碼。 此外，並非所有類型的編碼保留單元都可用於這些資料中心。

- 巴西南部 ︰ 只提供標準和基本的編碼保留單元
- 印度西部、 印度南部和印度中央 ︰ 只有基本編碼保留單位都可以使用


##<a id="september_changes_15"></a>2015 年 9 月版本 

- AMS 現在提供以 Widevine Modular DRM 技術，保護點播視訊 (VOD) 和即時資料流的能力。 您可以使用下列的傳遞服務夥伴來幫助您傳送 Widevine 授權 ︰ [Axinom](http://www.axinom.com/press/ibc-axinom-drm-6/), ，[EZDRM](http://ezdrm.com/), ，[castLabs](http://castlabs.com/company/partners/azure/)。 如需詳細資訊，請參閱 [這篇部落格](http://azure.microsoft.com/blog/azure-media-services-adds-google-widevine-packaging-for-delivering-multi-drm-stream/)。

    您可以使用 [AMS.NET SDK](https://www.nuget.org/packages/windowsazure.mediaservices/) （從版本 3.5.1 開始） 或 REST API 來設定您要使用 Widevine AssetDeliveryConfiguration。  

- AMS 已新增對 Apple ProRes 影片的支援。 您現在可以上傳使用 Apple ProRes 或其他轉碼器的 QuickTime 來源視訊檔案。 如需詳細資訊，請參閱 [這篇部落格](http://azure.microsoft.com/blog/announcing-support-for-apple-prores-videos-in-azure-media-services/)。

- 您現在可以使用 Media Encoder Standard 進行字幕裁剪和即時封存解壓縮。 如需詳細資訊，請參閱 [這篇部落格](http://azure.microsoft.com/blog/sub-clipping-and-live-archive-extraction-with-media-encoder-standard/)。

- 進行了下列篩選更新： 

    - 您現在可以使用 Apple HTTP Live Streaming (HLS) 格式搭配僅限音訊的篩選條件。 這項更新可讓您在 URL 中指定 (audio-only=false) 來移除僅限音訊的曲目。
    - 在定義您的資產篩選條件時，現在您可以結合多個 (最多 3 個) 篩選器到單一 URL 中。

    如需詳細資訊，請參閱 [這](http://azure.microsoft.com/blog/azure-media-services-release-dynamic-manifest-composition-remove-hls-audio-only-track-and-hls-i-frame-track-support) 部落格。

- AMS 現在支援 HLS v4 的 I-Frames。  I-Frames 支援最佳化向前快轉和倒轉的作業。 根據預設，所有 HLS v4 輸出都包含 I-Frames 播放清單 (EXT-X-I-FRAME-STREAM-INF)。
 
    如需詳細資訊，請參閱 [這](http://azure.microsoft.com/blog/azure-media-services-release-dynamic-manifest-composition-remove-hls-audio-only-track-and-hls-i-frame-track-support) 部落格。

##<a id="august_changes_15"></a>2015 年 8 月版本

- 現在已有適用於 Java 0.8.0 版本的 Azure 媒體服務 SDK 以及新的範例可用。 如需詳細資訊，請參閱：

    - [部落格文章](http://southworks.com/blog/2015/08/25/microsoft-azure-media-services-sdk-for-java-v0-8-0-released-and-new-samples-available/)
    - [Java 範例存放庫](https://github.com/southworkscom/azure-sdk-for-media-services-java-samples)
- 含有多重音訊串流支援的 Azure 媒體播放器更新。 如需詳細資訊，請參閱：
    - [部落格文章](https://azure.microsoft.com/blog/2015/08/13/azure-media-player-update-with-multi-audio-stream-support/)

##<a id="july_changes_15"></a>2015 年 7 月版本

- 宣布 Media Encoder Standard 的一般可用性。 如需詳細資訊，請參閱 [此部落格文章](http://azure.microsoft.com/blog/2015/07/16/announcing-the-general-availability-of-media-encoder-standard/)。

    Media Encoder Standard 使用中所述的預設 [這](http://go.microsoft.com/fwlink/?LinkId=618336) 一節。 請注意，當使用 4k 編碼的預設，您應該取得 **高階** 保留單元類型。 如需詳細資訊，請參閱 [如何調整編碼](media-services-portal-encoding-units)。
- 直播即時字幕與 Azure 媒體服務和播放器。 如需詳細資訊，請參閱  [此部落格文章](https://azure.microsoft.com/blog/2015/07/08/live-real-time-captions-with-azure-media-services-and-player/)

###媒體服務 .NET SDK 更新

Azure 媒體服務 .NET SDK 現在是版本 3.4.0.0。 此版本中加入了下列功能：  

- 即時封存的實作支援。 請注意，您無法下載包含即時封存的資產。
- 動態篩選的實作支援。
- 實作功能，可讓使用者在刪除資產時保留儲存體容器的。
- 通道中重試原則的相關 Bug 修正。
- 啟用  **媒體編碼器高階工作流程**。

##<a id="june_changes_15"></a>2015 年 6 月版本

###媒體服務 .NET SDK 更新

Azure 媒體服務 .NET SDK 現在是版本 3.3.0.0。 此版本中加入了下列功能：  

- 支援 OpenId Connect 探索規格
- 支援處理識別提供者端的金鑰變換 

如果您使用的身分識別提供者會公開 OpenID Connect 探索文件 (如同下列提供者：Azure Active Directory、Google、Salesforce)，您可以指示 Azure 媒體服務從 OpenID Connect 探索規格取得 JWT 權杖驗證的簽署金鑰。 

如需詳細資訊，請參閱 [使用 Json Web 金鑰從 OpenID Connect 探索規格使用 JWT 權杖中 Azure 媒體服務的驗證](http://gtrifonov.com/2015/06/07/using-json-web-keys-from-openid-connect-discovery-spec-to-work-with-jwt-token-authentication-in-azure-media-services/)。


##<a id="may_changes_15"></a>2015 年 5 月版本

發表下列新功能：

- [使用媒體服務進行即時編碼的預覽功能](media-services-manage-live-encoder-enabled-channels.md)
- [動態資訊清單](media-services-dynamic-manifest-overview.md)
- [Azure Media Hyperlapse 媒體處理器的預覽功能](http://azure.microsoft.com/blog/?p=286281&preview=1&_ppp=61e1a0b3db)

##<a id="april_changes_15"></a>2015 年 4 月版本

###一般媒體服務更新

- [發表 Azure Media Player](http://azure.microsoft.com/blog/2015/04/15/announcing-azure-media-player/)。
- 從媒體服務 REST 2.10 開始，設定為擷取 RTMP 通訊協定的通道，會和主要與次要擷取 URL 一起建立。 如需詳細資訊，請參閱 [通道擷取組態](media-services-manage-channels-overview.md#channel_input)
- Azure 媒體索引器更新
    - 支援西班牙文語言
    - 新的組態 xml 格式
    
    如需詳細資訊，請參閱 [這篇部落格](http://azure.microsoft.com/blog/2015/04/13/azure-media-indexer-spanish-v1-2/)。
###媒體服務 .NET SDK 更新

Azure 媒體服務 .NET SDK 現在是版本 3.2.0.0。

以下是一些屬於客戶面向的更新：
 
- **中斷變更**︰ 變更 **TokenRestrictionTemplate.Issuer** 和 **TokenRestrictionTemplate.Audience** 字串型別。 
- 與建立自訂重試原則相關的更新。 
- 與上傳/下載檔案相關的錯誤修正。 
-  **MediaServicesCredentials** 類別現在接受主要和次要存取控制端點做為驗證對象。



##<a id="march_changes_15"></a>2015 年 3 月版本

### 一般媒體服務更新

- 媒體服務也提供 Azure CDN 整合。 為了支援整合， **CdnEnabled** 屬性加入至 **StreamingEndpoint**。  **CdnEnabled** 可以搭配 REST Api 從版本 2.9 開始時 (如需詳細資訊，請參閱 [StreamingEndpoint](https://msdn.microsoft.com/library/azure/dn783468.aspx))。  **CdnEnabled** 適用於.NET SDK 版本 3.1.0.2 開始 (如需詳細資訊，請參閱 [StreamingEndpoint](https://msdn.microsoft.com/library/azure/microsoft.windowsazure.mediaservices.client.istreamingendpoint(v=azure.10).aspx))。
- 公告的 **媒體編碼器高階工作流程**。 如需詳細資訊，請參閱 [介紹 Azure 媒體服務編碼高階](http://azure.microsoft.com/blog/2015/03/05/introducing-premium-encoding-in-azure-media-services)。
 


##<a id="february_changes_15"></a>2015 年 2 月版本

### 一般媒體服務更新

媒體服務 REST API 目前的版本為 2.9。 從此版本開始，Azure CDN 整合就可以應用到串流端點。 如需詳細資訊，請參閱 [StreamingEndpoint](https://msdn.microsoft.com/library/dn783468.aspx)。

##<a id="january_changes_15"></a>2015 年 1 月版本

### 一般媒體服務更新

內容保護與動態加密通用版本上市 (GA) 的公告。 如需詳細資訊，請參閱 [Azure 媒體服務以增強串流安全性正式推出的 DRM 技術](http://azure.microsoft.com/blog/2015/01/29/azure-media-services-enhances-streaming-security-with-general-availability-of-drm-technology/)。

###媒體服務 .NET SDK 更新

Azure 媒體服務 .NET SDK 現在是版本 3.1.0.1。

此版本中將預設 Microsoft.WindowsAzure.MediaServices.Client.ContentKeyAuthorization.TokenRestrictionTemplate 建構函式標示為過時。 新的建構函式會接受 TokenType 做為引數。

    TokenRestrictionTemplate template = new TokenRestrictionTemplate(TokenType.SWT);


##<a id="december_changes_14"></a>2014 年 12 月版本

###一般媒體服務更新

- 部分更新和新功能已加入到 Azure Media Indexer 處理器。 如需詳細資訊，請參閱 [Azure Media Indexer 1.1.6.7 的版本資訊](http://azure.microsoft.com/blog/2014/12/03/azure-media-indexer-version-1-1-6-7-release-notes/)。
- 加入新的 REST API，可讓您更新編碼保留單元 ︰ [EncodingReservedUnitType 與 REST](http://msdn.microsoft.com/library/azure/dn859236.aspx)。
- 已加入金鑰傳遞服務的 CORS 支援。
- 已完成查詢授權原則選項的效能改進。
- 在中國資料中心， [金鑰傳遞 URL](http://msdn.microsoft.com/library/azure/ef4dfeeb-48ae-4596-ab28-44d6b36d8769#get_delivery_service_url) 現在是針對每位客戶 （就像其他資料中心）。
- 已加入 HLS 自動目標持續時間。 在執行即時資料流時，會一律動態封裝 HLS。 依預設，媒體服務會根據從即時編碼器收到的主要畫面格間隔 (KeyFrameInterval，也稱為圖片群組 – GOP)，自動計算 HLS 區段封裝比例 (FragmentsPerSegment) 。 如需詳細資訊，請參閱 [Working with Azure Media Services Live Streaming]。
 
###媒體服務 .NET SDK 更新

- [Azure 媒體服務.NET SDK](http://www.nuget.org/packages/windowsazure.mediaservices/) 現在是版本 3.1.0.0。
- 將 .Net SDK 相依性升級至 .NET 4.5 Framework。
- 已加入新的 API，可讓您更新編碼保留單元。 如需詳細資訊，請參閱 [更新保留單元類型和增加編碼 Ru 使用.NET](http://msdn.microsoft.com/library/azure/jj129582.aspx)。
- 已加入權杖驗證的 JWT (JSON Web 權杖) 支援。 如需詳細資訊，請參閱 [Azure 媒體服務和動態加密中的 JWT 權杖驗證](http://www.gtrifonov.com/2015/01/03/jwt-token-authentication-in-azure-media-services-and-dynamic-encryption/)。
- 已在 PlayReady 授權範本中加入 BeginDate 和 ExpirationDate 的相對位移。


##<a id="november_changes_14"></a>2014 年 11 月版本

- 媒體服務現在可讓您透過 SSL 連線擷取即時的 Smooth Streaming (FMP4) 內容。 若要透過 SSL 擷取，請務必將擷取 URL 更新為 HTTPS。  如需即時資料流的詳細資訊，請參閱 [Working with Azure Media Services Live Streaming]。
- 請注意，目前您無法透過 SSL 連線擷取 RTMP 即時資料流。
- 您也可以透過 SSL 連線串流您的內容。 若要這樣做，請確定您的串流 URL 以 HTTPS 開頭。
- 請注意，只有在您從中傳遞內容的串流端點在 2014 年 9 月 10 日之後建立時，才能透過 SSL 串流。 如果您的串流 URL 是根據 9 月 10 日之後建立的串流端點，則 URL 會包含 "streaming.mediaservices.windows.net" (新格式)。 包含 "origin.mediaservices.windows.net" (舊格式) 的串流 URL 不支援 SSL。 如果您的 URL 是舊格式，而且您想要能夠透過 SSL 串流 [建立新的串流端點](media-services-manage-origins.md)。 使用根據新的串流端點建立的 URL，透過 SSL 串流處理內容。
   
##<a id="october_changes_14"></a>2014 年 10 月版本

### <a id="new_encoder_release"></a>Media Services Encoder 版本

發表新版的 Media Services Azure Media Encoder。 使用最新的 Azure Media Encoder 時，您只需要為輸出 GB 付費，除此之外，新編碼器也與舊編碼器的功能相容。 如需詳細資訊 [Media Services Pricing Details])。

### <a id="oct_sdk"></a>媒體服務 .NET SDK 

Media Services SDK for .NET 延伸目前的版本為 2.0.0.3。

Media Services SDK for .NET 目前的版本為 3.0.0.8。

做了下列變更：

- 重試原則類別中的重整。
- 將使用者代理字串加入至 http 要求標頭。
- 新增 nuget 還原建置步驟。
- 修正案例測試，以使用來自儲存機制的 x509 憑證。
- 更新通道和串流結束時，驗證設定。
 

### 新的 GitHub 儲存機制，以主控媒體服務範例

範例位於 [Azure 媒體服務範例 GitHub 儲存機制](https://github.com/Azure/Azure-Media-Services-Samples)。


##<a id="september_changes_14"></a>2014 年 9 月版本

媒體服務 REST 中繼資料目前的版本為 2.7。 如需最新 REST 更新的詳細資訊，請參閱 [Azure Media Services REST API Reference]。

Media Services SDK for .NET 目前的版本為 3.0.0.7。
 
### <a id="sept_14_breaking_changes"></a>重大變更

* **原始** 已重新命名為 [StreamingEndpoint]。
* 使用時的預設行為的變更 **Azure 傳統入口網站** 編碼並發行 MP4 檔案。

過去，在使用 Azure 傳統入口網站發佈單一檔案 MP4 視訊資產時，會建立 SAS URL (SAS URL 可讓您從 Blob 儲存體下載視訊)。 現在，當您使用 Azure 傳統入口網站編碼並發佈單一檔案 MP4 視訊資產時，產生的 URL 會指向 Azure 媒體服務串流端點。  這項變更並不會影響未由 Azure 媒體服務編碼、而直接上傳至媒體服務並發佈的 MP4 視訊。

現在，您有下列兩個選項可以解決問題。

* 啟用串流單元，並使用動態封裝功能將 .mp4 資產串流處理為 Smooth Streaming 簡報。

* 建立 SAS URL 以下載 (或累進播放) .mp4。 如需如何建立 SAS 定位器的詳細資訊，請參閱 [Delivering Content]。


### <a id="sept_14_GA_changes"></a>GA 版本中的新功能/案例

* **索引器媒體處理器**。 如需詳細資訊，請參閱 [Indexing Media Files with Azure Media Indexer]。

*  [StreamingEndpoint] 實體現在可讓您新增自訂網域 （主機） 名稱。

    對於要作為媒體服務串流端點名稱的自訂網域名稱，您必須將自訂主機名稱新增至您的串流端點。 請使用媒體服務 REST API 或 .NET SDK 來新增自訂主機名稱。
    
    您必須考量下列事項：
    
    * 您必須具有自訂網域名稱的擁有權。
    
    * 網域名稱的擁有權必須通過 Azure 媒體服務的驗證。 若要驗證網域，建立 CName，將對應 <MediaServicesAccountId>.<parent domain> 到 verifydns.< mediaservices-dns 區域的 >。 
    
    * 您必須建立另一個 CName，將自訂主機名稱 (例如，sports.contoso.com) 對應到媒體服務 StreamingEndpont 的主機名稱 (例如，amstest.streaming.mediaservices.windows.net)。


    For more information, see the **CustomHostNames** property in the [StreamingEndpoint] topic.

### <a id="sept_14_preview_changes"></a>公用預覽版本中的新功能/案例

* 即時資料流預覽。 如需詳細資訊，請參閱 [Working with Azure Media Services Live Streaming]。

* 金鑰傳遞服務。 如需詳細資訊，請參閱 [Using AES-128 Dynamic Encryption and Key Delivery Service]。

* AES 動態加密。 如需詳細資訊，請參閱 [Using AES-128 Dynamic Encryption and Key Delivery Service]。

* PlayReady 授權傳遞服務。 如需詳細資訊，請參閱 [Using PlayReady Dynamic Encryption and License Delivery Service]。

* PlayReady 動態加密。 如需詳細資訊，請參閱 [Using PlayReady Dynamic Encryption and License Delivery Service]。

* 媒體服務 PlayReady 授權範本。 如需詳細資訊，請參閱 [Media Services PlayReady License Template Overview]。

* 串流儲存體加密資產。 如需詳細資訊，請參閱 [Streaming Storage Encrypted Content]。

##<a id="august_changes_14"></a>2014 年 8 月版本

當您為資產編碼時，在完成編碼工作時將會產生輸出資產。 在此版本之前，Azure Media Services Encoder 會產生輸出資產的相關中繼資料。 自此版本開始，編碼程式會同時產生輸入資產的相關中繼資料。 如需詳細資訊，請參閱 [Input Metadata] 和 [Output Metadata] 主題。


##<a id="july_changes_14"></a>2014 年 7 月版本

Azure Media Services Packager 和 Encryptor 完成了下列錯誤修正：

* 將即時封存資產多工轉換為 HTTP 即時資料流時，只會播放音訊 – 此問題已獲得修正，現在已會同時播放音訊和視訊。

* 將資產封裝為 HTTP 即時資料流和 AES 128 位元信封加密時，封裝的串流無法在 Android 裝置上播放 – 此問題已獲得修正，封裝的串流已可在支援 HTTP 即時資料流的 Android 裝置上播放。

##<a id="may_changes_14"></a>2014 年 5 月版本

### <a id="may_14_changes"></a>一般媒體服務更新

您現在可以使用 [Dynamic Packaging] 來串流處理 HTTP 即時資料流 (HLS) v3。 若要串流處理 HLS v3，請將下列格式新增至原始定位器路徑：* .ism/manifest(format=m3u8-aapl-v3)。 如需詳細資訊，請參閱 [Nick Drouin's Blog]。

動態封裝現在也支援根據使用 PlayReady 靜態加密的 Smooth Streaming，來傳遞使用 PlayReady 加密的 HLS (v3 和 v4)。 如需如何使用 PlayReady 加密 Smooth Streaming，請參閱 [Protecting Smooth Stream with PlayReady]。

### <a name="may_14_donnet_changes"></a>媒體服務 .NET SDK 更新

媒體服務 .NET SDK 3.0.0.5 版包含以下改良：

* 上傳/下載媒體資產的速度和恢復能力優於以往。

* 重試邏輯和暫時性例外狀況處理已有改善： 

    * 因查詢、儲存變更、上傳或下載檔案而造成的例外狀況，在暫時性錯誤偵測和重試邏輯方面已有改善。 
    
    * 當 Web 例外狀況出現時 (例如，在 ACS 權杖要求期間)，您會發現嚴重錯誤現在的失效速度已變快。

如需詳細資訊，請參閱 [Retry Logic in the Media Services SDK for .NET]。

##<a id="april_changes_14"></a>2014 年 4 月編碼器版本

### <a name="april_14_enocer_changes"></a>Media Services Encoder 更新

* 已新增對使用 Grass Valley EDIUS 非線性編輯器撰寫的 AVI 檔案進行擷取的支援；其中的視訊會使用 Grass Valley HQ/HQX 轉碼器略為壓縮。 如需詳細資訊，請參閱 [Grass Valley Announces EDIUS 7 Streaming Through the Cloud]。

* 新增了為 Media Encoder 所產生的檔案指定命名慣例的支援。 如需詳細資訊，請參閱 [Controlling Media Service Encoder Output Filenames]。

* 新增了視訊和 (或) 音訊重疊的支援。 如需詳細資訊，請參閱 [Creating Overlays]。

* 新增了結合多個視訊片段的支援。 如需詳細資訊，請參閱 [Stitching Video Segments]。

* 修正了與 MP4 相關的轉碼；其中，音訊是使用 MPEG-1 Audio Layer 3 (aka MP3) 編碼的。


##<a id="jan_feb_changes_14"></a>2014 年 1/2 月版本

### <a name="jan_fab_14_donnet_changes"></a>Azure 媒體服務 .NET SDK 3.0.0.1、3.0.0.2 和 3.0.0.3

3.0.0.1 和 3.0.0.2 中的變更包括：

* 修正了使用 OrderBy 陳述式進行 LINQ 查詢的用法相關問題。

* 分割中的測試方案 [GitHub] 分成單元測試和案例測試。

如需有關變更的詳細資訊，請參閱 ︰ [Azure Media Services .NET SDK 3.0.0.1 and 3.0.0.2 releases]。

3.0.0.3 中做了下列變更：

* 已升級 Azure 儲存體相依性而使用 3.0.3.0 版。 

* 修正 3.0 回溯相容性問題。*。* 釋出。 


##<a id="december_changes_13"></a>2013 年 12 月版本

### <a name="dec_13_donnet_changes"></a>Azure 媒體服務 .NET SDK 3.0.0.0

>[AZURE.NOTE] 3.0 版本沒有與 2.4 回溯相容。

媒體服務 SDK 目前的最新版本為 3.0.0.0。 您可以從 Nuget 下載最新的封裝，或取得從 [GitHub]。

從媒體服務 SDK 3.0.0.0 版開始，您可以重複使用 [Azure Active Directory Access Control Service (ACS)] 語彙基元。 如需詳細資訊，請參閱中的 「 重複使用存取控制服務權杖 」 一節 [Connecting to Media Services with the Media Services SDK for .NET] 主題。

### <a name="dec_13_donnet_ext_changes"></a>Azure 媒體服務 .NET SDK 延伸模組 2.0.0.0

Azure 媒體服務 .NET SDK 延伸是一組延伸方法和協助程式函數，可簡化您的程式碼以及使用 Azure 媒體服務進行開發的工作。 您可以取得最新版本，從 [Azure Media Services .NET SDK Extensions]。

##<a id="november_changes_13"></a>2013 年 11 月版本

### <a name="nov_13_donnet_changes"></a>Azure 媒體服務 .NET SDK 變更

自此版本起，Media Services SDK for .NET 已可處理在呼叫媒體服務 REST API 層時可能發生的暫時性錯誤。

##<a id="august_changes_13"></a>2013 年 8 月版本

### <a name="aug_13_powershell_changes"></a>Azure SDK 工具中包含的媒體服務 PowerShell Cmdlet

下列媒體服務 PowerShell cmdlet 現在包含在 [azure-sdk-tools]。

* Get-AzureMediaServices 

    例如，`Get-AzureMediaServicesAccount`。

* New-AzureMediaServicesAccount 

    例如，`New-AzureMediaServicesAccount -Name “MediaAccountName” -Location “Region” -StorageAccountName “StorageAccountName”`。

* New-AzureMediaServicesKey 

    例如，`New-AzureMediaServicesKey -Name “MediaAccountName” -KeyType Secondary -Force`。

* Remove-AzureMediaServicesAccount 

    例如，`Remove-AzureMediaServicesAccount -Name “MediaAccountName” -Force`。

##<a id="june_changes_13"></a>2013 年 6 月版本

### <a name="june_13_general_changes"></a>Azure 媒體服務變更

本節說明的變更是 2013 年 6 月媒體服務版本所包含的更新。

* 能夠將多個儲存體帳戶連結至媒體服務帳戶。 

    StorageAccount
    
    Asset.StorageAccountName 和 Asset.StorageAccount

* 能夠更新 Job.Priority。 

* 實體和屬性的相關通知： 

    JobNotificationSubscription
    
    NotificationEndPoint
    
    Job

* Asset.Uri 

* Locator.Name 

### <a name="june_13_dotnet_changes"></a>Azure 媒體服務 .NET SDK 變更

2013 年 6 月媒體服務 SDK 版本包含下列變更。 GitHub 提供的最新媒體服務 SDK。

* 自 2.3.0.0 版起，媒體服務 SDK 已可支援將多個儲存體帳戶連結至媒體服務帳戶的作業。 支援此功能的 API 如下：
    
    IStorageAccount 類型。
    
    Microsoft.WindowsAzure.MediaServices.Client.CloudMediaContext.StorageAccounts 屬性。
    
    StorageAccount 屬性。
    
    StorageAccountName 屬性。
    
    如需詳細資訊，請參閱 [Managing Media Services Assets across Multiple Storage Accounts]。

* API 的相關通知。 自 2.2.0.0 版起，您已能夠聽取 Azure 佇列儲存體通知。 如需詳細資訊，請參閱 [Handling Media Services Job Notifications]。
    
    Microsoft.WindowsAzure.MediaServices.Client.IJob.JobNotificationSubscriptions 屬性。
    
    Microsoft.WindowsAzure.MediaServices.Client.INotificationEndPoint 類型。
    
    Microsoft.WindowsAzure.MediaServices.Client.IJobNotificationSubscription 類型。
    
    Microsoft.WindowsAzure.MediaServices.Client.NotificationEndPointCollection 類型。
    
    Microsoft.WindowsAzure.MediaServices.Client.NotificationEndPointType 類型。
    
    Microsoft.WindowsAzure.MediaServices.Client.NotificationJobState 類型。

* 對 Azure Storage Client SDK 2.0 (Microsoft.WindowsAzure.StorageClient.dll) 的相依性。

* 對 OData 5.5 (Microsoft.Data.OData.dll) 的相依性。


##<a id="december_changes_12"></a>2012 年 12 月版本

### <a name="dec_12_dotnet_changes"></a>Azure 媒體服務 .NET SDK 變更

* Intellisense：針對許多類型新增了遺漏的 Intellisense 文件。

* Microsoft.Practices.TransientFaultHandling.Core：修正了 SDK 對此組件的舊版仍有相依性的問題。 SDK 現已參考此組件的 5.1.1209.1 版。

修正在 2012 年 11 月 SDK 中發現的問題：

* IAsset.Locators.Count：現在，當所有定位器刪除後，此計數已會正確地在新的 IAsset 介面上報告。

* IAssetFile.ContentFileSize：現在，當 IAssetFile.Upload(filepath) 完成上傳後，會正確設定此值。

* IAssetFile.ContentFileSize：現已可在建立資產檔案後設定此屬性。 此檔案過去是唯讀的。

* IAssetFile.Upload(filepath)：已修正此同步上傳方法在將多個檔案上傳至資產時會擲回以下錯誤的問題。 此錯誤為「伺服器無法驗證要求。 請確定授權標頭的值 (包括簽章在內) 格式正確。」

* IAssetFile.UploadAsync：已修正無法同時上傳超過 5 個檔案的問題。

* IAssetFile.UploadProgressChanged：此事件現在由 SDK 提供。

* IAssetFile.DownloadAsync(string, BlobTransferClient, ILocator, CancellationToken)：現在提供此方法多載。

* IAssetFile.DownloadAsync：已修正無法同時下載超過 5 個檔案的問題。

* IAssetFile.Delete()：已修正若未上傳任何用於 IAssetFile 的檔案，呼叫刪除時可能會擲回例外狀況的問題。

* 工作：已修正在使用工作範本鏈結「MP4 to Smooth Streams 工作」與「PlayReady Protection 工作」時不會建立任何工作的問題。

* EncryptionUtils.GetCertificateFromStore()：此方法已不會再因為無法根據憑證組態問題找到憑證，而擲回 Null 參考例外狀況。

##<a id="november_changes_12"></a>2012 年 11 月版本

本節說明的變更是 2012 年 11 月 (2.0.0.0 版) SDK 所包含的更新。 進行這些變更時，可能必須修改或重寫為 2012 年 6 月預覽 SDK 版本撰寫的程式碼。

* Assets
    
    IAsset.Create(assetName) 是唯一的資產建立函數。 IAsset.Create 已不會在方法呼叫期間上傳檔案。 請使用 IAssetFile 進行上傳。
    
    IAsset.Publish 方法和 AssetState.Publish 列舉值已從服務 SDK 中移除。 任何依存於此值的程式碼都必須重寫。

* FileInfo

    此類別已移除，並由 IAssetFile 取代。

    IAssetFiles

    IAssetFile 已取代 FileInfo，且具有不同的行為。 若要加以使用，請具現化 IAssetFiles 物件，然後使用媒體服務 SDK 或 Azure 儲存體 SDK 進行檔案上傳。 您可以使用下列 IAssetFile.Upload 多載：

    * IAssetFile.Upload(filePath)：會封鎖執行緒的同步方法，建議僅在上傳單一檔案時使用。
    
    * IAssetFile.UploadAsync(filePath, blobTransferClient, locator, cancellationToken)：非同步方法。 這是一般常用的上傳機制。 

    已知錯誤：使用 cancellationToken 會取消上傳。不過，工作的取消狀態可以是許多狀態的任何一種。 您必須正確捕捉並處理例外狀況。

* 定位器
    
    原始來源的特定版本已移除。 SAS 特定 context.Locators.CreateSasLocator(asset, accessPolicy) 將標示為已被取代，或由 GA 移除。 請參閱＜新功能＞底下的＜定位器＞一節，以瞭解更新後的行為。


##<a id="june_changes_12"></a>2012 年 6 月預覽版本

以下是 SDK 的 11 月版本中包含的新功能。

* 刪除實體

    IAsset、IAssetFile、ILocator、IAccessPolicy、IContentKey 物件現已會在物件層級上刪除 (即 IObject.Delete())，而無須在「集合」中刪除 (即 cloudMediaContext.ObjCollection.Delete(objInstance))。

* 定位器

    定位器現在必須使用 CreateLocator 方法來建立，並使用 LocatorType.SAS 或 LocatorType.OnDemandOrigin 列舉值作為您要建立之特定定位器類型的引數。

    定位器已新增新的屬性，以便取得您的內容可使用的 URI。 定位器的這項重新設計旨在為日後的協力廠商擴充能力提供更大的彈性，以及提高媒體用戶端應用程式的易用性。

* 非同步方法支援

    所有方法皆已新增非同步支援。


##媒體服務學習路徑

[AZURE.INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

##提供意見反應

[AZURE.INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]


<!-- Anchors. -->

<!-- Images. -->

<!-- URLs. -->
[Azure Media Services MSDN Forum]: http://social.msdn.microsoft.com/forums/azure/home?forum=MediaServices
[Azure Media Services REST API Reference]: http://msdn.microsoft.com/library/azure/hh973617.aspx 
[Media Services Pricing Details]: http://azure.microsoft.com/pricing/details/media-services/
[Input Metadata]: http://msdn.microsoft.com/library/azure/dn783120.aspx
[Output Metadata]: http://msdn.microsoft.com/library/azure/dn783217.aspx
[Delivering Content]: http://msdn.microsoft.com/library/azure/hh973618.aspx
[Indexing Media Files with Azure Media Indexer]: http://msdn.microsoft.com/library/azure/dn783455.aspx
[StreamingEndpoint]: http://msdn.microsoft.com/library/azure/dn783468.aspx
[Working with Azure Media Services Live Streaming]: http://msdn.microsoft.com/library/azure/dn783466.aspx
[Using AES-128 Dynamic Encryption and Key Delivery Service]: http://msdn.microsoft.com/library/azure/dn783457.aspx
[Using PlayReady Dynamic Encryption and License Delivery Service]: http://msdn.microsoft.com/library/azure/dn783467.aspx
[Preview features]: http://azure.microsoft.com/services/preview/
[Media Services PlayReady License Template Overview]: http://msdn.microsoft.com/library/azure/dn783459.aspx
[Streaming Storage Encrypted Content]: http://msdn.microsoft.com/library/azure/dn783451.aspx
[Azure Classic Portal]: https://manage.windowsazure.com
[Dynamic Packaging]: http://msdn.microsoft.com/library/azure/jj889436.aspx
[Nick Drouin's Blog]: http://blog-ndrouin.azurewebsites.net/hls-v3-new-old-thing/
[Protecting Smooth Stream with PlayReady]: http://msdn.microsoft.com/library/azure/dn189154.aspx
[Retry Logic in the Media Services SDK for .NET]: http://msdn.microsoft.com/library/azure/dn745650.aspx
[Grass Valley Announces EDIUS 7 Streaming Through the Cloud]: http://www.streamingmedia.com/Producer/Articles/ReadArticle.aspx?ArticleID=96351&utm_source=dlvr.it&utm_medium=twitter
[Controlling Media Service Encoder Output Filenames]: http://msdn.microsoft.com/library/azure/dn303341.aspx
[Creating Overlays]: http://msdn.microsoft.com/library/azure/dn640496.aspx
[Stitching Video Segments]: http://msdn.microsoft.com/library/azure/dn640504.aspx
[Azure Media Services .NET SDK 3.0.0.1 and 3.0.0.2 releases]: http://www.gtrifonov.com/2014/02/07/windows-azure-media-services-.net-sdk-3.0.0.2-release/
[Azure Active Directory Access Control Service (ACS)]: http://msdn.microsoft.com/library/hh147631.aspx
[Connecting to Media Services with the Media Services SDK for .NET]: http://msdn.microsoft.com/library/azure/jj129571.aspx
[Azure Media Services .NET SDK Extensions]: https://github.com/Azure/azure-sdk-for-media-services-extensions/tree/dev
[azure-sdk-tools]: https://github.com/Azure/azure-sdk-tools
[GitHub]: https://github.com/Azure/azure-sdk-for-media-services
[Managing Media Services Assets across Multiple Storage Accounts]: http://msdn.microsoft.com/library/azure/dn271889.aspx
[Handling Media Services Job Notifications]: http://msdn.microsoft.com/library/azure/dn261241.aspx
 


