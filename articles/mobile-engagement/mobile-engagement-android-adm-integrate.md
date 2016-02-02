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



# 如何整合 ADM 與 Engagement

> [AZURE.IMPORTANT] 您必須遵循＜如何在 Android 上整合 Engagement＞文件中所述的整合程序，才能接著依照本指南操作。
>
> 只有當您已整合 Reach 模組以用於任何時間促銷活動支援，才適用本文件。 若要在應用程式中整合 Reach 促銷活動，請先閱讀「如何在 Android 上整合 Engagement Reach」。

## 簡介

整合 ADM 可在目標為 Amazon Android 裝置時推送。

ADM 裝載一律推送至 SDK 包含 `azme` 資料物件中的索引鍵。 因此，如果您在應用程式中因為其他目的使用 ADM，可以根據該金鑰篩選推送。
> [AZURE.IMPORTANT] Amazon Device Messaging 只支援執行 Android 4.0.3 或更新版本的 Amazon Kindle 裝置；不過，您仍可將這段程式碼安全地整合至其他裝置。

## 註冊 ADM

如果尚未這麼做，您必須在 Amazon 帳戶上啟用 ADM。

詳細程序: [<https://developer.amazon.com/sdk/adm/credentials.html>]。

完成此程序後，您會得到：

-   OAuth 認證 (用戶端識別碼和用戶端密碼)，讓 Engagement 能夠推播您的裝置。
-   API 金鑰，必須整合到您的應用程式。

## SDK 整合

### 管理裝置註冊

每個裝置都必須傳送註冊命令給 ADM 伺服器，否則無法連線該裝置。

如果您已經使用 [ADM 用戶端程式庫]，並已經有 [整合的 ADM] 您可以直接移至 android sdk-adm-接收。

如果您還沒有整合的 ADM，Engagement 提供一個在應用程式中啟用的更簡便方式：

編輯您 `AndroidManifest.xml` 檔案:

-   新增 Amazon 命名空間，檔案應該類似這樣：

        <?xml version="1.0" encoding="utf-8"?>
        <manifest xmlns:android="http://schemas.android.com/apk/res/android"
                  xmlns:amazon="http://schemas.amazon.com/apk/res/android"

-   內部 `< 應用程式 / >` 標記中，加入此區段:

    <amazon:enable-feature
       android:name="com.amazon.device.messaging"
       android:required="false"/>
    
    <meta-data android:name="engagement:adm:register" android:value="true" />

-   加入 amazon 標記後，如果您的專案建置目標低於 Android 2.1，可能會出現建置錯誤。 您必須使用 **Android 2.1 +** 建置目標 (別擔心，您仍可 `minSdkVersion` 設為 4)。
-   ADM API 金鑰整合為資產依 [此程序]。

然後依照下一節的指示進行。

### 將註冊識別碼傳遞給 Engagement 推播服務，並接收通知

若要註冊裝置的識別碼給 Engagement 推播服務並接收其通知，請將下列內容加入您 `AndroidManifest.xml` 檔案中，內部 `< 應用程式 / >` 標記 (即使您使用未搭配 Engagement ADM):

        <receiver android:name="com.microsoft.azure.engagement.adm.EngagementADMEnabler"
          android:exported="false">
          <intent-filter>
            <action android:name="com.microsoft.azure.engagement.intent.action.APPID_GOT"/>
          </intent-filter>
        </receiver>
    
         <receiver android:name="com.microsoft.azure.engagement.adm.EngagementADMReceiver"
           android:permission="com.amazon.device.messaging.permission.SEND">
          <intent-filter>
            <action android:name="com.amazon.device.messaging.intent.REGISTRATION"/>
            <action android:name="com.amazon.device.messaging.intent.RECEIVE"/>
            <category android:name="<your_package_name>"/>
          </intent-filter>
        </receiver>   

請確定您有下列權限，您 `AndroidManifest.xml` (之前 `< / 應用程式 >` 標記)。

        <uses-permission android:name="android.permission.WAKE_LOCK"/>
        <uses-permission android:name="com.amazon.device.messaging.permission.RECEIVE"/>
        <uses-permission android:name="<your_package_name>.permission.RECEIVE_ADM_MESSAGE"/>
        <permission android:name="<your_package_name>.permission.RECEIVE_ADM_MESSAGE" android:protectionLevel="signature"/>

## 授與 Engagement OAuth 認證

在 $/\#application/YOUR\_APPID/native-push 提交您的 OAuth 認證 (用戶端識別碼和用戶端密碼)。

現在，您在建立 Reach 公告與輪詢時可以選取 [任何時間]。



[<https://developer.amazon.com/sdk/adm/credentials.html>]: https://developer.amazon.com/sdk/adm/credentials.html 
[adm client library]: https://developer.amazon.com/sdk/adm/setup.html 
[integrated adm]: https://developer.amazon.com/sdk/adm/integrating-app.html 
[this procedure]: https://developer.amazon.com/sdk/adm/integrating-app.html#Asset 

