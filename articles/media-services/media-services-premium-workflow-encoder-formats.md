<properties 
    pageTitle="Media Encoder Premium Workflow 格式和轉碼器" 
    description="本主題提供 Media Encoder Premium Workflow 格式和轉碼器的概觀" 
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

#Media Encoder Premium Workflow 格式和轉碼器


**請注意** 本主題中討論的 Media Encoder Premium Workflow 媒體處理器不適用於中國。 

這份文件包含一份輸入及輸出檔案格式和轉碼器所支援的公用預覽版本 **媒體編碼器高階工作流程** 編碼器。

[Media Encoder Premium Worflow 輸入格式和轉碼器](#input_formats)

[Media Encoder Premium Worflow 輸出格式和轉碼器](#output_formats)

**媒體編碼器高階工作流程** 支援隱藏式字幕述 [這](#closed_captioning) 一節。 


##<a id="input_formats"></a>Media Encoder Premium Worflow 輸入格式和轉碼器

下節列出此媒體處理器支援做為輸入的轉碼器和檔案格式。

###輸入容器/檔案格式

- Adobe® Flash® F4V
- MXF/SMPTE 377M
- GXF
- MPEG-2 傳輸資料流
- MPEG-2 程式資料流
- MPEG-4/MP4
- Windows Media/ASF
- AVI (未壓縮 8 位元/10 位元)

###輸入視訊轉碼器

- AVC 8 位元/10 位元，高達 4:2:2，包括 AVCIntra
- Avid DNxHD (使用 MXF)
- DVCPro/DVCProHD (使用 MXF)
- JPEG2000
- MPEG-2 (高達 422 Profile 和 High Level，包括 XDCAM、XDCAM HD、XDCAM IMX、CableLabs ® 和 D10 等變種)
- MPEG-1
- Windows Media 視訊/VC-1

###輸入音訊轉碼器

- AES (SMPTE 331M 和 302M，AES3-2003)
- Dolby® E
- Dolby® Digital (AC3)
- AAC (AAC-LC、AAC-HE 和 AAC-HEv2；高達 5.1)
- MPEG Layer 2
- MP3 (MPEG-1 音訊層 3)
- Windows Media 音訊
- WAV/PCM
 
##<a id="output_format"></a>Media Encoder Premium Worflow 輸出格式和轉碼器

下節列出此媒體處理器支援做為輸出的轉碼器和檔案格式。

###輸出容器/檔案格式

- Adobe® Flash® F4V
- MXF (OP1a、XDCAM 和 AS02)
- DPP (包括 AS11)
- GXF
- MPEG-4/MP4
- Windows Media/ASF
- AVI (未壓縮 8 位元/10 位元)
- Smooth Streaming 檔案格式 (PIFF 1.3)
- MPEG-TS 


###輸出視訊轉碼器

- AVC (H.264；8 位元；高達 High Profile、Level 5.2；4K Ultra HD；AVC Intra)
- Avid DNxHD (使用 MXF)
- DVCPro/DVCProHD (使用 MXF)
- MPEG-2 (高達 422 Profile 和 High Level，包括 XDCAM、XDCAM HD、XDCAM IMX、CableLabs ® 和 D10 等變種)
- MPEG-1
- Windows Media 視訊/VC-1
- JPEG 縮圖建立

###輸出音訊轉碼器

- AES (SMPTE 331M 和 302M，AES3-2003)
- Dolby® Digital (AC3)
- Dolby® Digital Plus (E-AC3) 高達 7.1
- AAC (AAC-LC、AAC-HE 和 AAC-HEv2；高達 5.1)
- MPEG Layer 2
- MP3 (MPEG-1 音訊層 3)
- Windows Media 音訊

##<a id="closed_captioning"></a>支援隱藏式字幕

內嵌時， **媒體編碼器高階工作流程** 支援 ︰

1. SCC 檔案
1. SMPTE-TT 檔案
1. CEA-608/CEA-708 – 以使用者資料 (H.264 基礎資料流、ATSC/53、SCTE20 的 SEI 訊息) 的形式傳送，或以 MXF/GXF 檔案中的輔助資料形式傳送
1. STL 字幕檔案

輸出時，可以使用下列選項：

1. CEA-608 至 CEA-708 翻譯
1. CEA-608/CEA-708 傳遞 (內嵌在 H.264 基礎資料流的 SEI 訊息，或執行為 MXF 檔案中的輔助資料)
1. SCC
1. SMPTE 計時文字 (來自來源 CEA-608 經由 SMPTE RP2052，包括建立 DFXP 檔案)
1. SRT 副標題檔案
1. DVB 副標題資料流

注意：Azure 媒體服務並不支援透過串流傳遞上述所有的輸出格式。

##已知問題

如果您的輸入視訊不包含隱藏式字幕，輸出資產仍然會包含空白 TTML 檔案。 


##媒體服務學習路徑

[AZURE.INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

##提供意見反應

[AZURE.INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]


