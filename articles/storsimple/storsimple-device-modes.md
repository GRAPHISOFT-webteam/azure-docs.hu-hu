<properties 
   pageTitle="變更 StorSimple 裝置模式 | Microsoft Azure"
   description="描述 StorSimple 裝置模式並說明如何使用 Windows PowerShell for StorSimple 來變更裝置的模式。"
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

# 變更 StorSimple 裝置的裝置模式

本文提供 StorSimple 裝置各種運作模式的簡短描述。 StorSimple 裝置有三種運作模式：標準、維護和復原。 

閱讀本文之後，您將了解：

- 何謂 StorSimple 裝置模式
- 如何找出 StorSimple 裝置所處的模式
- 如何從正常的維護模式，變更和 *反之亦然*


只能透過 StorSimple 裝置的 Windows PowerShell 介面執行上述的管理工作。

## 關於 StorSimple 裝置模式

StorSimple 裝置可以在標準、維護和復原模式下運作。 以下簡短描述每一種模式。

### 標準模式

這定義為完整設定的 StorSimple 裝置的標準運作模式。 根據預設，您的裝置會以標準模式運作。

### 維護模式

有時候 StorSimple 裝置可能需要進入維護模式。 此模式可讓您在裝置上執行維護和安裝干擾性的更新，例如磁碟韌體的相關更新。

您只能透過 Windows PowerShell for StorSimple 讓系統進入維護模式。 在此模式中，所有的 I/O 要求都會暫停。 靜態隨機存取記憶體 (NVRAM) 之類的服務或叢集服務也會停止。 當您進入或結束此模式時，兩個控制器都會重新啟動。 當您結束維護模式時，所有的服務都將繼續執行，而且應該是健康情況良好。 這可能需要幾分鐘的時間。

>[AZURE.NOTE] **正常運作的裝置僅支援維護模式。不支援在其中一個或兩個控制器無法運作的裝置上。**
</br>

### 復原模式

復原模式可以形容成「含網路支援的 Windows 安全模式」。 復原模式會連絡 Microsoft 支援服務小組，並允許他們在系統上執行診斷。 復原模式的主要目的是擷取系統記錄檔。

如果您的系統進入復原模式，您應該連絡 Microsoft 支援服務來請示後續步驟。 如需詳細資訊，請移至 [連絡 Microsoft 支援服務](storsimple-contact-microsoft-support.md)。

>[AZURE.NOTE] **您無法將裝置放在修復模式。如果裝置處於錯誤狀態，復原模式會嘗試讓裝置進入可讓 Microsoft 支援服務人員檢查的狀態。**

## 判斷 StorSimple 裝置模式

#### 判斷目前的裝置模式

1. 登入裝置序列主控台中的步驟 [使用 PuTTY 來連接至裝置序列主控台](storsimple-deployment-walkthrough.md#use-putty-to-connect-to-the-device-serial-console)。
2. 查看裝置的序列主控台功能表中的橫幅訊息。 此訊息會明確指出裝置處於維護模式或復原模式。 如果訊息不含任何關於系統模式的特定資訊，就表示裝置處於標準模式。

## 變更 StorSimple 裝置模式 

您可以讓 StorSimple 裝置進入維護模式 (從標準模式)，以執行維護或安裝維護模式更新。 執行下列程序進入或結束維護模式。

> [AZURE.IMPORTANT] 進入維護模式之前, 確認兩個裝置控制器狀況良好藉由存取 **硬體狀態** 上 **維護** Azure 傳統入口網站中的頁面。 如果控制站的健康情況不好，請連絡 Microsoft 支援服務以進行後續步驟。 如需詳細資訊，請移至 [連絡 Microsoft 支援服務](storsimple-contact-microsoft-support.md)。

#### 進入維護模式

1. 登入裝置序列主控台中的步驟 [使用 PuTTY 來連接至裝置序列主控台](storsimple-deployment-walkthrough.md#use-putty-to-connect-to-the-device-serial-console)。

2. 在序列主控台功能表中，選擇選項 1 **完整存取權登入**。 出現提示時，提供 **裝置系統管理員密碼**。 預設密碼為：`Password1`。

3. 在命令提示字元中，輸入： 

    `Enter-HcsMaintenanceMode`

4. 您將會看到警告訊息，告知您維護模式將中斷所有 I/O 要求並提供與 Azure 傳統入口網站的連線，而系統將提示您進行確認。 型別 **Y** 進入維護模式。

5. 這兩個控制站都將重新啟動。 完成重新啟動時，會出現另一個訊息，指出裝置處於維護模式。


#### 結束維護模式

1. 登入裝置序列主控台。 從橫幅訊息確認您的裝置處於維護模式。

2. 在命令提示字元中，輸入：

    `Exit-HcsMaintenanceMode`

3. 隨即會出現警告訊息和確認訊息。 型別 **Y** 結束維護模式。

4. 這兩個控制站都將重新啟動。 重新啟動完成時，會出現另一個訊息，指出裝置處於標準模式。


## 後續步驟

了解如何 [套用標準和維護模式更新](storsimple-update-device.md) StorSimple 裝置上。



