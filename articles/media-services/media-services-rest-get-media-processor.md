<properties 
    pageTitle="如何建立媒體處理器 | Microsoft Azure" 
    description="了解如何建立媒體處理器元件，為 Azure 媒體服務的媒體內容進行編碼、格式轉換、加密或解密。" 
    services="media-services" 
    documentationCenter="" 
    authors="Juliako" 
    manager="dwrede" 
    editor=""/>

<tags 
    ms.service="media-services" 
    ms.workload="media" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="12/05/2015" 
    ms.author="juliako"/>


#如何：取得媒體處理器執行個體


> [AZURE.SELECTOR]
- [.NET](media-services-get-media-processor.md)
- [REST](media-services-rest-get-media-processor.md)

##概觀

在媒體服務中，媒體處理器是可處理特定處理工作的元件，例如編碼、格式轉換、加密或解密媒體內容。 您通常會在建立媒體內容的編碼、加密或格式轉換工作時建立媒體處理器。

下表提供每個可用媒體處理器的名稱和說明。

媒體處理器名稱|說明|相關資訊
---|---|---
Azure Media Encoder|讓您使用 Azure Media Encoder 執行編碼工作。|[Azure Media Encoder](media-services-encode-asset.md#azure_media_encoder)
Media Encoder Standard|可讓您使用 Media Encoder Standard 執行編碼工作。|[Azure Media Encoder](media-services-encode-asset.md#media_encoder_standard)
Media Encoder Premium Workflow|可讓您使用 Media Encoder Premium Workflow 執行編碼工作。|[Media Encoder Premium Workflow](media-services-encode-asset.md#media_encoder_premium_wokrflow)
Azure Media Indexer| 可讓您的媒體檔案和內容可供搜尋，以及產生隱藏式輔助字幕和關鍵字。|[使用 Azure Media Indexer 編輯媒體檔案編製索引](media-services-index-content.md)。
Azure Media Hyperlapse (預覽)|可讓您使用影片穩定讓影片中的「巔簸」變得平滑。 也可讓您將內容加速至可使用的片段。|        [Azure Media Hyperlapse](http://azure.microsoft.com/blog/?p=286281&preview=1&_ppp=61e1a0b3db)</a>
Storage Decryption| 可讓您對使用儲存體加密功能加密的媒體資產進行解密。|N/A
Windows Azure Media 封裝程式|可讓您將媒體資產從 .mp4 轉換為 Smooth Streaming 格式。 此外，也可讓您將媒體資產從 Smooth Streaming 轉換為 Apple HTTP Live Streaming (HLS) 格式。|[Azure Media Packager 的工作預設字串](http://msdn.microsoft.com/library/hh973635.aspx)
Windows Azure 媒體編碼程式|可讓您使用 PlayReady Protection 為媒體資產加密。|[Azure Media Packager 的工作預設字串](http://msdn.microsoft.com/library/hh973610.aspx)

##取得 MediaProcessor

>[AZURE.NOTE] 當使用媒體服務 REST API，適用下列考量 ︰
>
>在媒體服務中存取實體時，您必須在 HTTP 要求中設定特定的標頭欄位和值。 如需詳細資訊，請參閱 [媒體服務 REST API 開發設定](media-services-rest-how-to-use.md)。

>順利連接到 https://media.windows.net 之後，您會收到 301 重新導向，指定另一個媒體服務 URI。 中所述，您必須將新的 uri 的後續呼叫 [連線到媒體服務使用 REST API](media-services-rest-connect_programmatically.md)。 



下列 REST 呼叫示範如何依名稱取得媒體處理器執行個體 (在此情況下， **Azure Media Encoder**)。 

    
要求：

    GET https://media.windows.net/api/MediaProcessors()?$filter=Name%20eq%20'Azure%20Media%20Encoder' HTTP/1.1
    DataServiceVersion: 1.0;NetFx
    MaxDataServiceVersion: 3.0;NetFx
    Accept: application/json
    Accept-Charset: UTF-8
    User-Agent: Microsoft ADO.NET Data Services
    Authorization: Bearer http%3a%2f%2fschemas.xmlsoap.org%2fws%2f2005%2f05%2fidentity%2fclaims%2fnameidentifier=juliakoams1&urn%3aSubscriptionId=zbbef702-e769-477b-2233-bc4d3aa97387&http%3a%2f%2fschemas.microsoft.com%2faccesscontrolservice%2f2010%2f07%2fclaims%2fidentityprovider=https%3a%2f%2fwamsprodglobal001acs.accesscontrol.windows.net%2f&Audience=urn%3aWindowsAzureMediaServices&ExpiresOn=1423635565&Issuer=https%3a%2f%2fwamsprodglobal001acs.accesscontrol.windows.net%2f&HMACSHA256=6zwXEn7YJzVJbVCNpqDUjBLuE5iUwsdJbWvJNvpY3%2b8%3d
    x-ms-version: 2.11
    Host: media.windows.net
    
回應：
    
    HTTP/1.1 200 OK
    Cache-Control: no-cache
    Content-Length: 273
    Content-Type: application/json;odata=minimalmetadata;streaming=true;charset=utf-8
    Server: Microsoft-IIS/8.5
    x-ms-client-request-id: 8a291764-4ed7-405d-aa6e-d3ebabb0b3f6
    request-id: dceeb559-48b5-48e1-81d3-d324b6203d51
    x-ms-request-id: dceeb559-48b5-48e1-81d3-d324b6203d51
    X-Content-Type-Options: nosniff
    DataServiceVersion: 3.0;
    X-Powered-By: ASP.NET
    Strict-Transport-Security: max-age=31536000; includeSubDomains
    Date: Wed, 11 Feb 2015 00:19:56 GMT
    
    {"odata.metadata":"https://wamsbayclus001rest-hs.cloudapp.net/api/$metadata#MediaProcessors","value":[{"Id":"nb:mpid:UUID:1b1da727-93ae-4e46-a8a1-268828765609","Description":"Azure Media Encoder","Name":"Azure Media Encoder","Sku":"","Vendor":"Microsoft","Version":"4.4"}]}




##媒體服務學習路徑

[AZURE.INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

##提供意見反應

[AZURE.INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]


##後續步驟
現在您知道如何取得媒體處理器執行個體，請移至 [如何為資產編碼][] 主題，了解如何使用 Azure Media Encoder 為資產編碼。

[How to Encode an Asset]: media-services-rest-encode-asset.md
[Task Preset Strings for the Azure Media Encoder]: http://msdn.microsoft.com/library/jj129582.aspx
[How to: Connect to Media Services Programmatically]: ../media-services-rest-connect_programmatically/ 


