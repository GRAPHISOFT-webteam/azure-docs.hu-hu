<properties 
   pageTitle="連絡 Microsoft 支援服務 | Microsoft Azure"
   description="了解如何建立支援要求和在 StorSimple 裝置上啟動支援工作階段。"
   services="storsimple"
   documentationCenter=""
   authors="alkohli"
   manager="carolz"
   editor="" />
<tags 
   ms.service="storsimple"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="12/02/2015"
   ms.author="alkohli" />


# 連絡 Microsoft 支援服務

如果您使用 Microsoft Azure StorSimple 解決方案時遇到任何問題，您可以向技術支援人員提出服務要求。 在與支援工程師進行線上工作階段時，可能也需要在您的 StorSimple 裝置上啟動支援工作階段。 本文將引導您：

- 如何建立支援要求。
- 如何在 StorSimple 裝置的 Windows PowerShell 介面中啟動支援工作階段。

檢閱 [StorSimple 8000 系列支援 Sla 和資訊](https://msdn.microsoft.com/library/mt433077.aspx) 建立支援要求之前。

## 建立支援要求

執行下列步驟來建立支援要求：

#### 建立支援要求

1. 可以透過建立支援要求 [Azure 傳統入口網站](http://manage.windowsazure.com/)。 在 [入口網站](http://manage.windowsazure.com/), ，按一下您 **帳戶名稱** 然後按一下 [ **連絡 Microsoft 支援服務**。

    ![透過 ManagementPortal 連絡 MS 支援服務](./media/storsimple-contact-microsoft-support/IC777286.png)

2. 在 [連絡 Microsoft 支援服務]**** 對話方塊中：

    1. 從下拉式清單中，選取與 StorSimple Manager 服務相關聯的目標 [訂用帳戶]****。 將 [支援類型]**** 指定為 [技術]****。 您必須已付費購買支援方案，才能啟用技術支援。

    2. 按一下核取圖示 ![核取圖示](./media/storsimple-contact-microsoft-support/IC740895.png) 至 **建立票證**。

3. 在 [Microsoft 支援服務]**** 視窗中，從 [產品]**** 下拉式清單中選擇 [StorSimple]****。

    ![連絡 Microsoft 支援服務 - 產品](./media/storsimple-contact-microsoft-support/IC777288.png)

4. 遵循螢幕上的指示，將您的要求正確分類，並清楚、明確地描述您的問題。

提交您的要求之後，支援工程師會儘速連絡您來處理您的要求。

## 在 Windows PowerShell for StorSimple 中啟動支援工作階段

若要對您使用 StorSimple 裝置時可能會遇到的任何問題進行疑難排解，您必須連絡 Microsoft 支援服務小組。 Microsoft 支援服務可能需要使用支援工作階段來登入您的裝置。

執行下列步驟來啟動支援工作階段：

#### 啟動支援工作階段

1. 從遠端電腦使用序列主控台或透過 telnet 工作階段，直接存取裝置。 若要這樣做，請依照 [使用 PuTTY 來連接至裝置序列主控台](storsimple-deployment-walkthrough.md#use-putty-to-connect-to-the-device-serial-console)。

2. 在開啟的工作階段中，按 **Enter** 鍵來取得命令提示字元。

3. 在序列主控台功能表中，選取選項 1 [使用完整存取權登入]****。

4. 在提示字元中輸入下列密碼：

    `密碼 1`

5. 在出現提示時輸入下列命令：

    `啟用 HcsSupportAccess`

6. 您會看到加密的字串。 將此字串複製到 [記事本] 之類的文字編輯器。

7. 儲存這個字串，並透過電子郵件傳送給 Microsoft 支援服務。

> [AZURE.IMPORTANT] 您可以藉由執行停用支援存取 `停用 HcsSupportAccess`。 在起始工作階段之後 8 小時，StorSimple 裝置也會嘗試停用支援存取。 最好在起始支援工作階段之後變更 StorSimple 裝置認證。




