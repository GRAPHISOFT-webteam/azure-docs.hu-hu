<properties 
    pageTitle="使用 castLabs 將 Widevine 授權傳遞到 Azure 媒體服務" 
    description="本文說明如何使用 Azure 媒體服務 (AMS) 來傳遞 AMS 使用 PlayReady 與 Widevine DRM 動態加密的資料流。 PlayReady 授權來自媒體服務 PlayReady 授權伺服器，Widevine 授權由 castLabs 授權伺服器傳遞。" 
    services="media-services" 
    documentationCenter="" 
    authors="Mingfeiy,willzhan,Juliako" 
    manager="dwrede" 
    editor=""/>

<tags 
    ms.service="media-services" 
    ms.workload="media" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="10/08/2015"  
    ms.author="juliako"/>


#使用 castLabs 將 Widevine 授權傳遞到 Azure 媒體服務

> [AZURE.SELECTOR]
- [Axinom](media-services-axinom-integration.md)
- [castLabs](media-services-castlabs-integration.md)

##概觀

本文說明如何使用 Azure 媒體服務 (AMS) 來傳遞 AMS 使用 PlayReady 與 Widevine DRM 動態加密的資料流。 PlayReady 授權來自媒體服務 PlayReady 授權伺服器，Widevine 授權是由 **castLabs** 授權伺服器。

若要播放串流 CENC (PlayReady 和/或 Widevine) 所保護的內容，您可以使用  [Azure Media Player](http://amsplayer.azurewebsites.net/azuremediaplayer.html)。 請參閱 [AMP 文件](http://amp.azure.net/libs/amp/latest/docs/) 如需詳細資訊。

以下為 Azure 媒體服務與 castLabs 整合架構概況圖。

![整合](./media/media-services-castlabs-integration/media-services-castlabs-integration.png)

##一般系統設定

- 媒體內容儲存在 AMS 中。
- 內容金鑰的金鑰識別碼儲存在 castLabs 與 AMS 中。
- castLabs 與 AMS 皆有內建權杖驗證。 以下幾節將會討論驗證權杖。 
- 當用戶端要求串流視訊時，使用動態加密內容 **一般加密** (CENC) 並動態封裝到 Smooth Streaming 與 DASH AMS。 我們也會針對 HLS 串流通訊協定傳遞 PlayReady M2TS 基礎資料流加密。
- PlayReady 授權擷取自 AMS 授權伺服器，Widevine 授權擷取自 castLabs 授權伺服器。 
- 媒體播放器會根據平台功能自動決定提取哪些授權。 

##產生驗證權杖以取得授權

castLabs 與 AMS 皆支援使用 JWT (JSON Web Token) 權杖格式進行授權。 

###AMS 中的 JWT 權杖 

下表說明 AMS 中的 JWT 權杖。 

Issuer|所選安全權杖服務 (STS) 中的簽發者字串
---|---
Audience|所使用 STS 中的對象字串
Claims|一組宣告
NotBefore|權杖的生效日期
Expires|權杖的有效期限
SigningCredentials|PlayReady 授權伺服器、castLabs 授權伺服器與 STS 之間共用的金鑰，可以是對稱或非對稱金鑰。

###castLabs 中的 JWT 權杖

下表說明 castLabs 中的 JWT 權杖。 

名稱|說明
---|---
optData|JSON 字串，其中包含您的相關資訊。 
crt|JSON 字串，其中包含資產、其授權資訊與播放權限的相關資訊。
iat|Epoch 中目前的日期時間。
jti|權杖的唯一識別碼 (每個權杖在 castLabs 系統中只使用一次)。

##範例解決方案設定 

 [範例方案](https://github.com/AzureMediaServicesSamples/CastlabsIntegration) 包含兩個專案:

-   主控台應用程式，用於為 PlayReady 與 Widevine 在已內嵌的資產上設定 DRM 限制。
-   Web 應用程式，用於遞出權杖，可視為「非常簡易」的 STS 版本。


若要使用主控台應用程式：

1.  變更 app.config 以設定 AMS 認證、castLabs 認證、STS 組態及共用的金鑰。
2.  將資產上傳到 AMS。
3.  從已上傳的資產中取得 UUID，然後變更 Program.cs 檔中的行 32：

         var objIAsset = _context.Assets.Where(x => x.Id == "nb:cid:UUID:dac53a5d-1500-80bd-b864-f1e4b62594cf").FirstOrDefault();

4.  使用 AssetId 命名 castLabs 系統中的資產 (Program.cs 檔中的行 44)。

    您必須設定 AssetId **castLabs**; 它必須是唯一的英數字元字串。

5.  執行程式。


若要使用 Web 應用程式 (STS)：

1.  變更 web.config 以設定 castlabs 商店識別碼、STS 組態和共用的金鑰。
2.  部署到 Azure 網站。
3.  瀏覽至該網站。

##播放視訊

若要播放使用一般加密 (PlayReady 和/或 Widevine) 的視訊，您可以使用 [Azure Media Player](http://amsplayer.azurewebsites.net/azuremediaplayer.html)。 執行主控台應用程式時，會回應內容金鑰識別碼和資訊清單 URL。

1.  開啟新索引標籤，並啟動 STS: http://[yourStsName].azurewebsites.net/api/token/assetid/[yourCastLabsAssetId]/contentkeyid/[thecontentkeyid]。
2.  移至 [Azure Media Player](http://amsplayer.azurewebsites.net/azuremediaplayer.html)。
3.  貼上資料流 URL。
4.  按一下 [ **進階選項** 核取方塊。
5.  在 **保護** 下拉式清單中，選取 PlayReady 和/或 Widevine。
6.  在 [權杖] 文字方塊中貼上您從 STS 取得的權杖。 
    
    castLab 授權伺服器不需要權杖前有 “Bearer=” 前置詞。 因此請先移除該前置詞，再提交權杖。
7.  更新播放程式。
8.  視訊應隨即播放。


##媒體服務學習路徑

[AZURE.INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

##提供意見反應

[AZURE.INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]

