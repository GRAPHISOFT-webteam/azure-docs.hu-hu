<properties 
    pageTitle="如何建立媒體處理器 | Microsoft Azure" 
    description="了解如何建立媒體處理器元件，為 Azure 媒體服務的媒體內容進行編碼、格式轉換、加密或解密。程式碼範例以 C# 撰寫，並使用 Media Services SDK for .NET。" 
    services="media-services" 
    documentationCenter="" 
    authors="juliako" 
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



# 如何：取得媒體處理器執行個體

> [AZURE.SELECTOR]
- [.NET](media-services-get-media-processor.md)
- [REST](media-services-rest-get-media-processor.md)



## 概觀

在媒體服務中，媒體處理器是可處理特定處理工作的元件，例如編碼、格式轉換、加密或解密媒體內容。 您通常會在建立媒體內容的編碼、加密或格式轉換工作時建立媒體處理器。

下表提供每個可用媒體處理器的名稱和說明。

 媒體處理器名稱| 說明| 相關資訊
---|---|---
 Azure Media Encoder| 讓您使用 Azure Media Encoder 執行編碼工作。| [Azure 媒體編碼程式](media-services-encode-asset.md#azure_media_encoder)
 Media Encoder Standard| 可讓您使用 Media Encoder Standard 執行編碼工作。| [Azure 媒體編碼程式](media-services-encode-asset.md#media_encoder_standard)
 Media Encoder Premium Workflow| 可讓您使用 Media Encoder Premium Workflow 執行編碼工作。| [媒體編碼器高階工作流程](media-services-encode-asset.md#media_encoder_premium_wokrflow)
 Azure Media Indexer| 可讓您的媒體檔案和內容可供搜尋，以及產生隱藏式輔助字幕和關鍵字。| [使用 Azure Media Indexer 編輯媒體檔案編製索引](media-services-index-content.md)。
 Azure Media Hyperlapse (預覽)| 可讓您使用影片穩定讓影片中的「巔簸」變得平滑。也可讓您將內容加速至可使用的片段。| [Azure Media Hyperlapse](http://azure.microsoft.com/blog/?p=286281&preview=1&_ppp=61e1a0b3db)</a>
 Storage Decryption| 可讓您對使用儲存體加密功能加密的媒體資產進行解密。| N/A
 Windows Azure Media 封裝程式| 可讓您將媒體資產從 .mp4 轉換為 Smooth Streaming 格式。此外，也可讓您將媒體資產從 Smooth Streaming 轉換為 Apple HTTP Live Streaming (HLS) 格式。| [Azure Media Packager 的工作預設的字串](http://msdn.microsoft.com/library/hh973635.aspx)
 Windows Azure 媒體編碼程式| 可讓您使用 PlayReady Protection 為媒體資產加密。| [Azure Media Packager 的工作預設的字串](http://msdn.microsoft.com/library/hh973610.aspx)

## 取得 MediaProcessor

下列方法將說明如何取得媒體處理器執行個體。 程式碼範例假設會使用名為的模組層級變數 **_context** 來參考伺服器內容 > 一節所述 [How to: 連線到媒體服務程式設計的方式]。

    private static IMediaProcessor GetLatestMediaProcessorByName(string mediaProcessorName)
    {
         var processor = _context.MediaProcessors.Where(p => p.Name == mediaProcessorName).
            ToList().OrderBy(p => new Version(p.Version)).LastOrDefault();
    
        if (processor == null)
            throw new ArgumentException(string.Format("Unknown media processor", mediaProcessorName));
    
        return processor;
    }

## 媒體服務學習路徑

[AZURE.INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

## 提供意見反應

[AZURE.INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]

## 後續步驟

現在您知道如何取得媒體處理器執行個體，請移至 [如何編碼資產 []][] 主題，了解如何使用 Azure Media Encoder 為資產編碼。


[how to encode an asset]: media-services-encode-asset.md 
[task preset strings for the azure media encoder]: http://msdn.microsoft.com/library/jj129582.aspx 
[how to: connect to media services programmatically]: ../media-services-set-up-computer/ 

