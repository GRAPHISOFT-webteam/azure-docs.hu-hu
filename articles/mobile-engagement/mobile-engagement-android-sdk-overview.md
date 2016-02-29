<properties
    pageTitle="Azure Mobile Engagement Android SDK 整合"
    description="Android SDK for Azure Mobile Engagement 的最新更新和程序"
    services="mobile-engagement"
    documentationCenter="mobile"
    authors="piyushjo"
    manager="dwrede"
    editor="" />

<tags
    ms.service="mobile-engagement"
    ms.workload="mobile"
    ms.tgt_pltfrm="mobile-android"
    ms.devlang="Java"
    ms.topic="article"
    ms.date="08/10/2015"
    ms.author="piyushjo" />


#適用於 Azure Mobile Engagement 的 Android SDK

從這裡開始，取得有關如何將 Azure Mobile Engagement 整合在應用程式中的所有詳細資料。 如果您想要試試看第一次，請務必先完成我們 [15 分鐘教學課程](mobile-engagement-android-get-started.md)。

按一下以查看 [SDK 內容](mobile-engagement-android-sdk-content.md)。

##整合程序
1. 從這裡開始: [如何在 Android 應用程式中整合 Mobile Engagement](mobile-engagement-android-integrate-engagement.md)

2. 通知: [如何在 Android 應用程式中整合 Reach (通知)](mobile-engagement-android-integrate-engagement-reach.md)
    1. Google Cloud Messaging (GCM): [如何整合 GCM 與 Mobile Engagement](mobile-engagement-android-gcm-integrate.md)
    2. Amazon Device Messaging (ADM): [如何整合 ADM 與 Mobile Engagement](mobile-engagement-android-adm-integrate.md)

3. 標記計劃實作: [如何使用進階的 Mobile Engagement 標記 API Android 應用程式中](mobile-engagement-android-use-engagement-api.md)


##版本資訊

##4.1.3 (12/9/2015)

- 穩定性改進。

##4.1.0 (2015/08/25)

- 處理 Android M 的新權限模型。
- 現在可以在執行階段，而不是使用設定位置功能  `AndroidManifest.xml`。
- 修正權限錯誤：如果您使用 `ACCESS_FINE_LOCATION`，就不再需要 `ACCESS_COARSE_LOCATION`。
- 穩定性改進。

如需較早版本，請參閱 [完成版本資訊](mobile-engagement-android-release-notes.md)。

##升級程序

如果您已經整合舊版 SDK 到您的應用程式，請參閱 [升級程序](mobile-engagement-android-upgrade-procedure.md)。

