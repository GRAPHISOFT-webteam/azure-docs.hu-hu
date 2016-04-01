<properties 
   pageTitle="管理您的 StorSimple 備份原則 | Microsoft Azure"
   description="說明如何使用 StorSimple Manager 服務建立並管理手動備份、備份排程與備份保留。"
   services="storsimple"
   documentationCenter="NA"
   authors="SharS"
   manager="carolz"
   editor=""/>
<tags 
   ms.service="storsimple"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="TBD"
   ms.date="12/14/2015"
   ms.author="v-sharos"/>

# 使用 StorSimple Manager 服務管理備份原則

[AZURE.INCLUDE [storsimple-version-selector-manage-backup-policies](../../includes/storsimple-version-selector-manage-backup-policies.md)]

## 概觀

本教學課程說明如何使用 StorSimple Manager 服務 **備份原則** 頁面控制備份程序和備份保留 StorSimple 磁碟區。 它也會說明如何完成手動備份。

 **備份原則** 頁面可讓您管理備份原則並排程本機和雲端快照。 (備份原則用來設定磁碟區集合的備份排程和備份保留)。備份原則可讓您同時建立多個磁碟區的快照。 這表示備份原則所建立的備份將會是與當機時一致的複本。 此頁面會列出備份原則、其類型、相關聯的磁碟區、保留的備份數目，以及啟用這些原則的選項。

 **備份原則** 頁面也可讓您篩選現有的備份原則，由一個或多個下列欄位 ︰

- **原則名稱** – 與原則相關聯的名稱。 不同類型的原則包括：

   - 已排程的原則 (由使用者明確建立)。
   - 自動原則 (建立磁碟區時啟用此磁碟區選項的預設備份時所建立)。 這些原則會被命名為 VolumeName_Default，其中磁碟區名稱指的是傳統入口網站使用者所設定之 StorSimple 磁碟區的名稱。 自動原則會導致每日雲端快照於 22:30 裝置時間開始。
   - 匯入的原則 (原先是在 StorSimple Snapshot Manager 中所建立)。 這些包含說明從中匯入原則之 StorSimple Snapshot Manager 主機的標記。

- **磁碟區** – 與原則相關聯的磁碟區。 建立備份時，會將與備份原則相關聯的所有磁碟區群組在一起。

- **上次成功備份** – 使用此原則所進行的最後一個成功備份的時間與日期。

- **下一次備份** – 下次排定的備份，就會起始此原則的時間與日期。

- **排程** – 與備份原則相關聯的排程數目。

您可以從這個頁面執行的常用作業包括：

- 新增備份原則 
- 新增或修改排程 
- 刪除備份原則 
- 進行手動備份 
- 建立具有多個磁碟區和排程的自訂備份原則 

## 新增備份原則

新增備份原則，以自動排程備份。 在 Azure 傳統入口網站中執行下列步驟，以便為 StorSimple 裝置新增備份原則。 新增原則之後，您可以定義排程 (請參閱 [新增或修改排程](#add-or-modify-a-schedule))。

[AZURE.INCLUDE [storsimple-add-backup-policy](../../includes/storsimple-add-backup-policy.md)]

![可用的視訊](./media/storsimple-manage-backup-policies/Video_icon.png) **可用的視訊**

若要看影片，示範如何建立在本機或雲端備份原則，按一下 [ [這裡](http://azure.microsoft.com/documentation/videos/create-storsimple-backup-policies/)。


## 新增或修改排程

您可以在 StorSimple 裝置上新增或修改附加到現有備份原則的排程。 在 Azure 傳統入口網站中執行下列步驟，以新增或修改排程。

[AZURE.INCLUDE [storsimple-add-modify-backup-schedule](../../includes/storsimple-add-modify-backup-schedule.md)]

## 刪除備份原則

在 Azure 傳統入口網站中執行下列步驟，以便刪除 StorSimple 裝置上的備份原則。

[AZURE.INCLUDE [storsimple-delete-backup-policy](../../includes/storsimple-delete-backup-policy.md)]


## 進行手動備份

在 Azure 傳統入口網站中執行下列步驟，以針對單一磁碟區建立隨選 (手動) 備份。

[AZURE.INCLUDE [storsimple-create-manual-backup](../../includes/storsimple-create-manual-backup.md)]

## 建立具有多個磁碟區和排程的自訂備份原則

在 Azure 傳統入口網站中執行下列步驟，以建立具有多個磁碟區和排程的自訂備份原則。

[AZURE.INCLUDE [storsimple-create-custom-backup-policy](../../includes/storsimple-create-custom-backup-policy.md)]


## 後續步驟

- 深入了解 [使用 StorSimple Manager 服務管理 StorSimple 裝置](storsimple-manager-service-administration.md)。

