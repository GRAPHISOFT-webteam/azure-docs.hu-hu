<properties 
    pageTitle="Windows 通用 app SDK 內容" 
    description="了解適用於 Azure Mobile Engagement 的 Windows 通用 app SDK 的內容"                    
    services="mobile-engagement" 
    documentationCenter="mobile" 
    authors="piyushjo" 
    manager="dwrede" 
    editor="" />

<tags 
    ms.service="mobile-engagement" 
    ms.workload="mobile" 
    ms.tgt_pltfrm="mobile-windows-store" 
    ms.devlang="dotnet" 
    ms.topic="article" 
    ms.date="08/10/2015" 
    ms.author="piyushjo" />


# Windows 通用 app SDK 內容

本文件列出及說明 SDK 在應用程式中部署的內容。

## `/Resources` 資料夾

此資料夾包含 Mobile Engagement 需要的所有資源。 您也可以自訂它們，以符合您的應用程式。

- `EngagementConfiguration.xml` : Mobile Engagement 的組態檔中，這是您可以在此自訂 Mobile Engagement 的設定 (Mobile Engagement 連接字串、 報告當機...)。

### /html 資料夾

- `EngagementNotification.html` : `通知` web 檢視 html 設計。

- `EngagementAnnouncement.html` : `公告` web 檢視 html 設計。

### /images 資料夾

- `EngagementIconNotification.png` : 顯示在通知左側的品牌圖示取代此品牌圖示。

- `EngagementIconOk.png` : `確定` 動作或驗證按鈕之 reach 內容頁面的圖示。

- `EngagementIconNOK.png` : `挪威克朗` reach 內容頁面之驗證按鈕停用時的圖示。

- `EngagementIconClose.png` : `關閉` reach 通知和內容的 [解除] 按鈕的圖示。

### /overlay 資料夾

- `EngagementBaseOverlay.cs` : 基底所使用的程式碼 `公告` 和 `通知` 覆疊。

- `EngagementOverlayAnnouncement.xaml` : `公告` xaml 設計。

- `EngagementOverlayAnnouncement.xaml.cs` : `EngagementOverlayAnnouncement.xaml` 連結的程式碼。

- `EngagementOverlayNotification.xaml` : `通知` xaml 設計。

- `EngagementOverlayNotification.xaml.cs` : `EngagementOverlayNotification.xaml` 連結的程式碼。

- `EngagementPageOverlay.cs` : `重疊` 宣告和通知顯示的程式碼。





