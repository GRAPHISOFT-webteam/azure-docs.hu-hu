<properties
    pageTitle="在 Azure RemoteApp 中發佈 App | Microsoft Azure"
    description="了解如何在 Azure RemoteApp 中發佈應用程式和資源。"
    services="remoteapp"
    documentationCenter=""
    authors="lizap"
    manager="mbaldwin" />

<tags
    ms.service="remoteapp"
    ms.workload="tbd"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="09/12/2015"
    ms.author="elizapo" />



# 如何在 RemoteApp 中發佈應用程式

建立 RemoteApp 收藏之後，您必須發佈想供使用者使用的應用程式或資源。 隨您的訂閱提供的範本映像預設只會發佈幾個應用程式，若要共用其他應用程式，您必須發佈它們。
> [AZURE.NOTE] 您需要更新應用程式嗎？ 您將需要 [更新映像](remoteapp-update.md) 第一次。

在入口網站的 [**發佈**] 索引標籤中，按一下 [**發佈**]。 您可以從您範本映像的 [**開始**] 功能表新增應用程式，或是在範本映像提供安裝程式的路徑。 如果您選擇從 [開始]**** 功能表新增，請從清單中選擇要發佈的 App。 如果您選擇提供應用程式的路徑，請輸入應用程式的名稱和應用程式路徑。 請在路徑中使用變數，例如 "%systemdrive%" 而非 "c:\"。
> [AZURE.NOTE] 如果您想要加入您的應用程式從 **啟動** ] 功能表上，您必須先 * 該應用程式來新增 **啟動** ] 功能表上，在您範本 image.* 否則 RemoteApp 只會看到什麼 *是* 上 **啟動** ] 功能表上，而且您會覺得很困惑。 

>若要確定您的 App 位於 [開始]**** 功能表，請將捷徑檔案 (**.lnk**) 置於 %systemdrive%\ProgramData\Microsoft\Windows\Start Menu\Programs 資料夾內。
>
> 如果您忘記在建立範本時將應用程式新增到 [**開始**] 功能表，請選擇新增路徑到應用程式。 (或者重新建立您的範本映像，但是這會比較費工。)







