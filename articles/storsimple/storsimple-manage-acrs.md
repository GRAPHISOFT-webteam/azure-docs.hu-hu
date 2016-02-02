<properties 
   pageTitle="管理 StorSimple 中的存取控制記錄 | Microsoft Azure"
   description="說明如何使用存取控制記錄 (ACR) 以判斷哪些主機可以連接至 StorSimple 裝置上的磁碟區。"
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


# 使用 StorSimple Manager 服務管理存取控制記錄

## 概觀

存取控制記錄 (ACR) 可讓您指定哪些主機可連接至 StorSimple 裝置上的磁碟區。 ACR 設為特定的磁碟區，並且包含主機的 iSCSI 限定名稱 (IQN)。 當主機嘗試連線到磁碟區時，裝置會檢查與該磁碟區相關聯的 ACR 的 IQN 名稱，如果相符，則會建立連接。 [**設定**] 頁面上的存取控制記錄區段會顯示具有主機對應 IQN 的所有存取控制記錄。

本教學課程將說明下列常見 ACR 相關工作：

- 加入存取控制記錄
- 編輯存取控制記錄
- 刪除存取控制記錄

> [AZURE.IMPORTANT] 
> 
> -將 ACR 指派到磁碟區，格外謹慎磁碟區不要讓並行存取由一個以上的非叢集主機因為這可能會損毀磁碟區。 
> -時從磁碟區刪除 ACR，請確定對應的主機未存取磁碟區因為刪除作業可能會導致讀寫中斷。

## 加入存取控制記錄

使用 StorSimple Manager 服務的 [設定]**** 頁面加入 ACR。 一般而言，您會讓一個 ACR 與一個磁碟區產生關聯。

執行下列步驟以加入 ACR。

#### 加入存取控制記錄

1. 在服務登陸頁面上，選取您的服務，連按兩下該服務名稱，然後按一下 [**設定**] 索引標籤。

2. 在 [**存取控制記錄**] 底下的表格式清單中，提供 ACR 的 [**名稱**]。

3. 在 [**iSCSI 啟動器名稱**] 下方，提供 Windows 主機的 IQN 名稱。 若要取得 Windows Server 主機的 IQN，請執行下列動作：

   - 在 Windows 主機上啟動 Microsoft iSCSI 啟動器。
   - 在 [iSCSI 啟動器屬性] 視窗的 [設定] 索引標籤上，選取並複製 [啟動器名稱] 欄位的字串。
   - 在 Azure 傳統入口網站的 ACR 表格上的 [iSCSI 啟動器名稱****] 欄位中貼上此字串。

4. 按一下 [**儲存**] 以儲存新建立的 ACR。 表格式清單會更新以反映此新增。

## 編輯存取控制記錄

您可以使用 Azure 傳統入口網站中的 [設定]**** 頁面編輯 ACR。
> [AZURE.NOTE] 您只能修改目前未在使用中的 ACR。 若要編輯與目前正在使用中的磁碟區相關聯的 ACR，您必須先讓磁碟區離線。

執行下列步驟以編輯 ACR。

#### 編輯存取控制記錄

1. 在服務登陸頁面上，選取您的服務，連按兩下該服務名稱，然後按一下 [**設定**] 索引標籤。

2. 在存取控制記錄的表格式清單中，將滑鼠停留在您想要修改的 ACR 上。

3. 為 ACR 提供新的名稱和/或 IQN。

4. 按一下 [**儲存**] 以儲存修改的 ACR。 表格式清單會更新以反映此變更。

## 刪除存取控制記錄

您可以使用 Azure 傳統入口網站中的 [設定]**** 頁面刪除 ACR。
> [AZURE.NOTE] 您只能刪除目前未在使用中的 ACR。 若要刪除與目前正在使用中的磁碟區相關聯的 ACR，您必須先讓磁碟區離線。

執行下列步驟來刪除存取控制記錄。

#### 刪除存取控制記錄

1. 在服務登陸頁面上，選取您的服務，連按兩下該服務名稱，然後按一下 [**設定**] 索引標籤。

2. 在存取控制記錄 (ACR) 的表格式清單中，將滑鼠停留在您想要刪除的 ACR 上。

3. 刪除圖示 (**x**) 會出現在您選取之 ACR 資料行的最右邊。 按一下 [**x**] 圖示，以刪除 ACR。

4. 當系統提示您確認時，按一下 [**是**] 繼續進行刪除。 表格式清單會更新以反映刪除。

## 後續步驟

- 深入了解 [管理 StorSimple 磁碟區](storsimple-manage-volumes.md)。
- 深入了解 [使用 StorSimple Manager 服務管理 StorSimple 裝置](storsimple-manager-service-administration.md)。





