<properties 
    pageTitle="Licensing Microsoft® Smooth Streaming Client Porting Kit" 
    description="了解如何授權 Microsoft® Smooth Streaming Client Porting Kit。" 
    services="media-services" 
    documentationCenter="" 
    authors="xpouyat,vsood" 
    manager="dwrede" 
    editor=""/>

<tags 
    ms.service="media-services" 
    ms.workload="media" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="10/30/2015"  
    ms.author="xpouyat"/>

#Licensing Microsoft® Smooth Streaming Client Porting Kit

##概觀

Microsoft Smooth Streaming Client Porting Kit (**SSPK** 簡稱) 是一個 Smooth Streaming 的用戶端實作，已最佳化，可幫助內嵌的裝置製造商、 纜線和行動通訊業者、 內容服務提供者、 手持式製造商、 獨立軟體廠商 (Isv) 及解決方案提供者產品和服務進行串流處理調適性串流建立 Smooth Streaming 格式中的內容。 SSPK 是 Smooth Streaming 用戶端的裝置和平台獨立實作，可由被授權者移植到任何裝置和平台。 

以下是一個高層級架構，而 IIS Smooth Streaming Porting Kit 方塊是 Microsoft 所提供的 Smooth Streaming 用戶端實作並包含播放 Smooth Streaming 內容的所有核心邏輯。 然而，特定裝置或平台的合作夥伴可藉由實作適當的介面來移植該架構。 

![SSPK](./media/media-services-sspk/sspk-arch.png)

##說明

SSPK 是根據可提供絕佳商業價值的條款授權。 SSPK 授權提供給業界：

- Smooth Streaming Porting Kit 的 C++ 原始碼 
  - 實作 Smooth Streaming 用戶端功能
  - 新增格式剖析、啟發學習法、緩衝處理邏輯等等
- 播放器應用程式 API 
  - 可與媒體播放器應用程式互動的程式設計介面
- 平台抽象層 (PAL) 介面 
  - 用於與作業系統 (執行緒、通訊端) 互動的程式設計介面
- 硬體抽象層 (HAL) 介面 
  - 用於與硬體 A/V 解碼器 (解碼、轉譯) 互動的程式設計介面
- 數位版權管理 (DRM) 介面 
  - 可透過 DRM 抽象層 (DAL) 處理 DRM 的程式設計介面
  - Microsoft PlayReady Porting Kit 個別出貨，但可透過這個介面整合。 如需有關 Microsoft PlayReady Device 授權的詳細資訊，請按一下 [這裡](http://www.microsoft.com/playready/licensing/device_technology.mspx#pddipdl)。
- 實作範例 
  - 適用於 Linux 的 PAL 實作範例
  - 適用於 GStreamer 的 HAL 實作範例

##授權選項

Microsoft Smooth Streaming Client Porting Kit 乃根據兩份不同的授權合約提供給被授權者：一份適用於開發 Smooth Streaming 用戶端中期產品，另一份適用於將 Smooth Streaming 用戶端最終產品散發給使用者。
 
- 晶片組製造商、 系統整合者，或獨立軟體廠商 (Isv) 需要產生程式碼開發過渡產品，Microsoft Smooth Streaming Client Porting Kit 的移植套件 **過渡產品授權** 應該執行。
- 裝置製造商或需要發佈權限給使用者，Microsoft Smooth Streaming Client Porting Kit Smooth Streaming 用戶端最後產品的 Isv **最終產品授權** 應該執行。

###Microsoft Smooth Streaming Client Porting Kit 中期產品授權

Microsoft 依據本授權提供 Smooth Streaming Client Porting Kit 和必要的智慧財產權，以便開發 Smooth Streaming 用戶端中期產品並散發給其他可散發 Smooth Streaming 用戶端最終產品的 Smooth Streaming Client Porting Kit 裝置被授權者。

####免費結構

美國 $50000 單次許可費用 Smooth Streaming Client Porting Kit 提供存取。 

###Microsoft Smooth Streaming Client Porting Kit 最終產品授權

Microsoft 依據本授權提供所有必要的智慧財產權，以便從其他 Smooth Streaming Client Porting Kit 被授權者接收 Smooth Streaming 用戶端中期產品，並將公司自有品牌的 Smooth Streaming 用戶端最終產品散發給一般使用者。

####免費結構

Smooth Streaming 用戶端最終產品乃根據權利金模型提供，細節如下：

- 送交的每一裝置實作為 $0.10 美元
- 每年的權利金上限為 $50,000 美元
- 每年前 10,000 個裝置實作不需權利金 

##授權程序和 SSPK 存取

請將電子郵件 [sspkinfo@microsoft.com](mailto:sspkinfo@microsoft.com) 所有授權的查詢。

 [SSPK 發佈入口網站](https://microsoft.sharepoint.com/teams/SSPKDOWNLOAD/) 註冊過渡期間被授權者存取。

中期與最終 SSPK 授權可以技術將問題提交到 [smoothpk@microsoft.com](mailto:smoothpk@microsoft.com)。

##Microsoft Smooth Streaming 用戶端中期產品合約被授權者

- Adroit Business Solutions, Inc
- Advanced Digital Broadcast SA
- AirTies Kablosuz Iletism Sanayive Dis Ticaret A.S.
- Albis Technologies Ltd.
- Alticast Corporation
- Amazon Digital Services, Inc.
- Amlogic, Co., Ltd.
- AVC Multimedia Software Co., Ltd.
- AwoX S.A.
- Axinom GmbH
- Cisco Systems, Inc.
- EchoStar Purchasing Corporation
- Enseo, Inc.
- Fluendo SA
- GIGABYTE Technology
- Guangdong OPPO Mobile Telecommunications Corp., Ltd.
- Infomir GMBH
- Inside Secure
- Irdeto USA Inc.
- Ittiam Systems Private Ltd
- Koninklijke Philips Electronics N.V.
- LG CNS Co., Ltd
- Liberty Global Services BV
- MediaTek Inc.
- MStar Co, Ltd
- Nextscape Inc.
- OpenTV, Inc.
- Panasonic Corporation
- Research In Motion Limited
- Saffron Digital Limited
- Sichuan Changhong Electric Co., Ltd
- SoftAtHome
- Tatung Technology Inc.
- TCL Technoly Electronics (Huizhou) Co., Ltd.
- Technicolor Delivery Technologies, SAS
- Teleca US Software Solutions LLC
- Telechips Inc.
- TeleIDEA BV
- Tivo Inc.
- Vestel Elektronik Sanayi ve Ticaret A.S.
- VimpelCom (OJSC)
- VisualOn, Inc.
- WOOJEON & HANDAN Co., Ltd
- ZTE Corporation

##Microsoft Smooth Streaming 用戶端最終產品合約被授權者

- Advanced Digital Broadcast SA
- AirTies Kablosuz Iletism Sanayive Dis Ticaret A.S.
- Albis Technologies Ltd.
- Altech Multimedia International (Pty) Ltd
- Amazon Digital Services, Inc.
- AmTRAN Technology Co., Ltd.
- Arcadyan Technology Corporation
- Arcelik A.S
- AwoX S.A.
- Axinom GmbH
- British Sky Broadcasting Limited
- CastPal Technology Inc., Shenzhen
- Cisco Systems, Inc.
- Compal Electronics, Inc.
- Dongguan Digital AV Technology Corp., Ltd.
- Enseo, Inc.
- Filmflex Movies Limited
- Fluendo SA
- GIGABYTE Technology
- Guangdong OPPO Mobile Telecommunications Corp., Ltd.
- Hisense International Co., Ltd
- Homecast Co.,Ltd
- Hon Hai Precision Industry Co., Ltd.
- Infomir GMBH
- Koninklijke Philips Electronics N.V.
- Kaonmedia Co., Ltd.
- KDDI Corporation
- Loewe Opta GmbH
- NETGEAR, Inc.
- Nintendo Co., Ltd.
- Orange SA
- Pace plc
- Panasonic Corporation
- PIXELA Corporation
- Saffron Digital Limited
- Sagemcom Broadband SAS
- Sharp Corporation
- Shenzhen Coship Electronics CO., LTD
- Shenzhen Jiuzhou Electric Co.,Ltd
- Shenzhen Skyworth Digital Technology Co., Ltd
- Sichuan Changhong Electric Co., Ltd.
- SmarDTV S.A.
- SoftAtHome
- TCL Overseas Marketing (Macao Commercial Offshore) Limited
- Technicolor Delivery Technologies, SAS
- TeleIDEA BV
- Toshiba Lifestyle Products & Services Corporation
- TP Vision Netherlands B.V.
- Virgin Media Limited
- VIZIO, Inc.
- Wistron Corporation
- WOOJEON & HANDAN Co., Ltd
- WOOX Innovations Limited
- ZTE Corporation

##媒體服務學習路徑

[AZURE.INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

##提供意見反應

[AZURE.INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]

