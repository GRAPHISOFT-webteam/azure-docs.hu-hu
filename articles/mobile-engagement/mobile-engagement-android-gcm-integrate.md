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

#如何整合 GCM 與 Mobile Engagement

> [AZURE.IMPORTANT] 您必須依照所述如何整合 Engagement Android 文件在遵循本指南之前的整合程序。
>
> 只有當您已整合 Reach 模組以用於任何時間促銷活動支援，才適用本文件。 若要在應用程式中整合 Reach 促銷活動，請先閱讀「如何在 Android 上整合 Engagement Reach」。

##簡介

整合 GCM 可讓您推播應用程式。

推送到 GCM 裝載的 SDK 一律包含資料物件中的 `azme` 金鑰。 因此，如果您在應用程式中因為其他目的使用 GCM，可以根據該金鑰篩選推送。

> [AZURE.IMPORTANT] 裝置執行 Android 2.2 或以上所述，有安裝而 Google Play 啟用背景連線可以推送使用 GCM;不過，您可以整合這段程式碼安全地不支援的裝置 (只使用對應方式)。

##註冊 GCM 並啟用 GCM 服務

如果尚未這麼做，請在您的 Google 帳戶上啟用 GCM 服務。

在撰寫這份文件 (2014 年 2 月 5 日) 的時候您可以依照下列程序進行: [<http://developer.android.com/guide/google/gcm/gs.html>]。

請只進行該程序中在您的帳戶上啟用 GCM 的部分。 確定您已到達 **取得 API 金鑰** 區段中，不閱讀並返回此頁面，而不遵循任何進一步的 Google 程序。

程序說明 **專案編號** 做為 **GCM 寄件者識別碼**, ，您需要稍後在此程序。

> [AZURE.IMPORTANT] **專案編號** 並不是與混淆 **專案識別碼**。 專案識別碼現在與專案編號不同 (在新專案上是一個名稱)。 您需要在 Engagement SDK 中整合是 **專案編號** ，並顯示於 **概觀** 功能表 [Google Developers Console]。

##SDK 整合

### 管理裝置註冊

每個裝置都必須傳送註冊命令給 Google 伺服器，否則無法連線該裝置。

也可以從 GCM 通知取消註冊裝置 (解除安裝應用程式時會自動取消裝置的註冊)。

如果您使用 [GCM client library], ，您可以直接讀取 android sdk-gcm-接收。

如果您尚未自行傳送註冊對應方式，您可以讓 Engagement 自動註冊您的裝置。

若要啟用此功能，請將下列內容加入 `AndroidManifest.xml` 檔案的 `<application/>` 標記中：

            <!-- If only 1 sender, don't forget the \n, otherwise it will be parsed as a negative number... -->
            <meta-data android:name="engagement:gcm:sender" android:value="<Your Google Project Number>\n" />

### 將註冊識別碼傳遞給 Engagement 推播服務，並接收通知

若要將裝置的註冊識別碼傳遞給 Engagement 推播服務並接收其通知，請將下列內容加入 `AndroidManifest.xml` 檔案的 `<application/>` 標記中 (即使您自行管理裝置註冊，也請這麼做)：

            <receiver android:name="com.microsoft.azure.engagement.gcm.EngagementGCMEnabler"
              android:exported="false">
              <intent-filter>
                <action android:name="com.microsoft.azure.engagement.intent.action.APPID_GOT" />
              </intent-filter>
            </receiver>

            <receiver android:name="com.microsoft.azure.engagement.gcm.EngagementGCMReceiver" android:permission="com.google.android.c2dm.permission.SEND">
              <intent-filter>
                <action android:name="com.google.android.c2dm.intent.REGISTRATION" />
                <action android:name="com.google.android.c2dm.intent.RECEIVE" />
                <category android:name="<your_package_name>" />
              </intent-filter>
            </receiver>

確定您在 `AndroidManifest.xml` 中具有下列權限 (在 `</application>` 標記之後)。

            <uses-permission android:name="com.google.android.c2dm.permission.RECEIVE" />
            <uses-permission android:name="<your_package_name>.permission.C2D_MESSAGE" />
            <permission android:name="<your_package_name>.permission.C2D_MESSAGE" android:protectionLevel="signature" />

##授與伺服器 API 金鑰的存取權給 Engagement

如果尚未這麼做，建立 **伺服器 API 金鑰** 上 [Google Developers Console]。

伺服器金鑰 **不能有 IP 限制**。

在撰寫本文的時候 (2014 年 2 月 5 日)，此程序如下：

-   若要這樣做，請開啟 [Google Developers Console]。
-   選取在程序與之前相同的專案 (具有 **專案編號** 中整合 `AndroidManifest.xml`)。
-   移至 [API 與驗證 -\> 認證]，在 [公用 API 存取] 區段中按一下 [建立新金鑰]。
-   選取 [伺服器金鑰]。
-   在下一個畫面上，保留為空白 **(沒有 IP 限制)**, ，然後按一下 [建立。
-   複製所產生 **API 金鑰**。
-   移至 $/\#application/YOUR\_ENGAGEMENT\_APPID/native-push。
-   在 GCM 區段中，以您剛剛產生並複製的 API 金鑰進行編輯。

現在，您在建立 Reach 公告與輪詢時可以選取 [任何時間]。

> [AZURE.IMPORTANT] Engagement 實際上需要 **伺服器金鑰**, ，Engagement 伺服器無法使用 Android 金鑰。

##測試

現在請閱讀＜如何在 Android 上測試 Engagement 整合＞，確認您的整合。


[<http://developer.android.com/guide/google/gcm/gs.html>]:http://developer.android.com/guide/google/gcm/gs.html
[Google Developers Console]:https://cloud.google.com/console
[GCM client library]:http://developer.android.com/guide/google/gcm/gs.html#libs
[Google Developers Console]:https://cloud.google.com/console

