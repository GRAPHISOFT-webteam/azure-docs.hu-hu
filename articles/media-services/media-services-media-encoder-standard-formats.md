<properties 
    pageTitle="Media Encoder Standard 格式和轉碼器" 
    description="本主題提供 Azure Media Encoder Standard 格式和轉碼器的概觀。" 
    services="media-services" 
    documentationCenter="" 
    authors="juliako,anilmur" 
    manager="dwrede" 
    editor=""/>

<tags 
    ms.service="media-services" 
    ms.workload="media" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="10/15/2015"  
    ms.author="juliako"/>

#Media Encoder Standard 格式和轉碼器


本文件包含您可以在 Media Encoder Standard 中使用的常見匯入和匯出檔案格式清單。


##輸入容器/檔案格式

檔案格式 (副檔名) |支援
---|---|---|---
FLV (使用 H.264 和 AAC 轉碼器) (.flv) |[是] 
MXF (.mxf) |[是] 
GXF (.gxf) |[是] 
MPEG2 PS，MPEG2 TS，3GP (.ts、.ps、.3gp、.3gpp、.mpg) |[是] 
Windows Media 視訊 (WMV) / ASF (.wmv、.asf) |[是] 
AVI (未壓縮 8 位元/10 位元) (.avi) |[是] 
MP4 (.mp4、.m4a、.m4v) / ISMV (.isma、.ismv) |[是] 
[Microsoft 數位視訊 recording (dvr-ms)](https://msdn.microsoft.com/library/windows/desktop/dd692984) (.dvr-ms) |[是] 
Matroska WebM (.mkv) |[是] 
WAVE WAV (.wav) |[是] 
QuickTime (.mov) |[是]
 
###輸入容器中的音訊格式 

Media Encoder Standard 支援在輸入容器中帶有下列音訊格式：

- MXF、GXF 和 QuickTime 檔案，其具有交錯立體聲或 5.1 範例的音訊音軌

或

- MXF、GXF 及 QuickTime 檔案，其中該音訊當做個別的 PCM 曲目攜帶，但可從檔案中繼資料推算通道對應 (立體聲或 5.1)

請注意，將在不久的將來提供明確/使用者提供的通道對應支援。


##輸入視訊轉碼器

輸入視訊 Codecs|支援
---|---|---|---
AVC 8 位元/10 位元，高達 4:2:2，包括 AVCIntra |8 位元 4:2:0 到 4:2:2 
Avid DNxHD (使用 mxf) |[是] 
DVCPro/DVCProHD (使用 mxf) |[是] 
(在 AVI 檔案) 的數位視訊 (DV) |[是]
JPEG 2000                                           |[是] 
Mpeg-2 (高達 422 Profile 和 High Level，包括 XDCAM、 XDCAM HD、 XDCAM IMX、 CableLabs ® 和 d10 等變種) |最高 422 設定檔 
MPEG-1                                              |[是] 
VC-1/WMV9                                           |[是] 
組織 HQ/HQX |否 
MPEG-4 Part 2                                       |[是] 
[Theora](https://en.wikipedia.org/wiki/Theora)      |[是] 
YUV420 未壓縮或夾層 |[是]
Apple ProRes 422 |[是]
Apple ProRes 422 LT |[是]
Apple ProRes 422 總部 |[是]
Apple ProRes Proxy|[是]
Apple ProRes 4444 |[是]
Apple ProRes 4444 XQ |[是]



##輸入音訊轉碼器

輸入音訊 Codecs|支援
---|---|---|---
AAC (AAC-LC、 和 Aac-hev2; 高達 5.1) |[是] 
MPEG 層 2|[是] 
MP3 (mpeg-1 音訊層 3) |[是] 
Windows Media Audio|[是] 
WAV/PCM|[是] 
[FLAC](https://en.wikipedia.org/wiki/FLAC)</a>|[是] 
[這種門外漢只](https://en.wikipedia.org/wiki/Opus _(audio_format) |[是] 
[Vorbis](https://en.wikipedia.org/wiki/Vorbis)</a>|[是] 
AMR (調適性多重速率) |[是]
AES (SMPTE 331m 和 302m，AES3-2003年) |否 
Dolby® E                                    |否 
Dolby ® Digital (AC3) |否 
Dolby ® Digital Plus (e-ac3) |否 


##輸出格式和轉碼器

下表會列出支援匯出的轉碼器和檔案格式清單。


檔案格式|視訊轉碼器|音訊轉碼器
---|---|---
MP4 <br/><br/>(包括多位元速率 MP4 容器) |H.264 (高、主要和基準設定檔)|AAC-LC、HE-AAC v1、HE-AAC v2 
MPEG2-TS |H.264 (高、主要和基準設定檔)|AAC-LC、HE-AAC v1、HE-AAC v2 



##媒體服務學習路徑

[AZURE.INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

##提供意見反應

[AZURE.INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]

##另請參閱

[透過 Azure Media Services 編碼的隨選內容](media-services-encode-asset.md)

[如何使用 Media Encoder Standard 進行編碼](media-services-dotnet-encode-with-media-encoder-standard.md)

