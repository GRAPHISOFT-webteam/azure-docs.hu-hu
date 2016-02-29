<properties
   pageTitle="更新 StorSimple 裝置 | Microsoft Azure"
   description="說明如何使用 StorSimple 更新功能，來安裝一般和維護模式的更新和 Hotfix。"
   services="storsimple"
   documentationCenter="NA"
   authors="SharS"
   manager="adinah"
   editor="" />
<tags 
   ms.service="storsimple"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="TBD"
   ms.date="12/01/2015"
   ms.author="v-sharos" />

# 更新您的 StorSimple 8000 系列裝置

## 概觀

StorSimple 更新功能可讓您輕鬆地將 StorSimple 裝置保持為最新狀態。 根據更新類型而定，您可以透過 Azure 傳統入口網站或透過 Windows PowerShell 介面，將更新套用到裝置。 本教學課程說明更新類型，以及如何安裝每一種更新類型。

您可以套用兩種類型的裝置更新： 

- 一般 (或標準模式) 更新
- 維護模式更新

您可以透過 Azure 傳統入口網站或 Windows PowerShell 安裝一般更新，不過，必須使用 Windows PowerShell 來安裝維護模式更新。 

以下將個別說明每個更新類型。

### 一般更新

一般更新是當裝置處於標準模式時可安裝的非干擾性更新。 這些更新是透過 Microsoft Update 網站套用到每個裝置控制站。 

> [AZURE.IMPORTANT] 控制器容錯移轉可能會在更新期間發生。 不過，這不會影響系統的可用性或運作。

- 如需如何透過 Azure 傳統入口網站安裝一般更新的詳細資訊，請參閱 [透過 Azure 傳統入口網站安裝一般更新(#install-regular-updates-via-the-azure-classic-portal)。

- 您也可以透過 Windows PowerShell for StorSimple 安裝一般更新。 如需詳細資訊，請參閱 [for StorSimple 安裝定期更新透過 Windows PowerShell](#install-regular-updates-via-windows-powershell-for-storsimple)。

### 維護模式更新

維護模式更新是干擾性更新，例如，磁碟韌體升級或 USM 韌體升級。 這些更新需要使裝置進入維護模式。 如需詳細資訊，請參閱 [步驟 2: 進入維護模式](#step2)。 您無法使用 Azure 傳統入口網站來安裝維護模式更新。 您必須改用 Windows PowerShell for StorSimple。 

如需有關如何安裝維護模式更新的詳細資訊，請參閱 [透過 Windows PowerShell for StorSimple 安裝維護模式更新](#install-maintenance-mode-updates-via-windows-powershell-for-storsimple)。

> [AZURE.IMPORTANT] 維護模式更新必須分別套用至每個控制器。 

## 透過 Azure 傳統入口網站安裝一般更新

您可以使用 Azure 傳統入口網站，將更新套用到 StorSimple 裝置。

[AZURE.INCLUDE [storsimple-install-updates-manually](../../includes/storsimple-install-updates-manually.md)]

## 透過 Windows PowerShell for StorSimple 安裝一般更新

或者，您可以使用 Windows PowerShell for StorSimple 來套用一般 (標準模式) 更新。

> [AZURE.IMPORTANT] 雖然您可以安裝一般更新使用 Windows PowerShell for StorSimple，我們強烈建議您安裝 Azure 傳統入口網站透過定期更新。 從 Update 1 開始，自入口網站安裝更新前將執行前置檢查。 這些前置檢查可防止失敗，並確保提供更順暢的體驗。 

[AZURE.INCLUDE [storsimple-install-regular-updates-powershell](../../includes/storsimple-install-regular-updates-powershell.md)]

## 透過 Windows PowerShell for StorSimple 安裝維護模式

您使用 Windows PowerShell for StorSimple，將維護模式更新套用到 StorSimple 裝置。 在此模式中，所有的 I/O 要求都會暫停。 靜態隨機存取記憶體 (NVRAM) 之類的服務或叢集服務也會停止。 這兩個控制站會在您進入或結束此模式時重新啟動。 當您結束此模式時，所有的服務都將繼續執行，而且應該是健康情況良好的 (這可能需要數分鐘的時間)。

如果您需要套用維護模式更新，將會透過 Azure 傳統入口網站收到警示，指出您有必須安裝的更新。 這個警示將包含使用 Windows PowerShell for StorSimple 安裝更新的相關指示。 更新裝置之後，請使用相同的程序來將裝置變更為一般模式。 如需逐步指示，請參閱 [步驟 4: 結束維護模式](#step4)。

> [AZURE.IMPORTANT] 
> 
> - 進入維護模式之前, 確認兩個裝置控制器狀況良好藉由檢查 **硬體狀態** 上 **維護** Azure 傳統入口網站中的頁面。 如果控制站的健康情況不好，請連絡 Microsoft 支援服務以進行後續步驟。 如需詳細資訊，請參閱＜連絡 Microsoft 支援服務＞。 
> - 當您處於維護模式時，需要先在某一個控制站上套用更新，然後在另一個控制站上套用更新。

### 步驟 1：連接到序列主控台 <a name="step1">

首先，使用 PuTTY 之類的應用程式來存取序列主控台。 下列程序說明如何使用 PuTTY 來連接到序列主控台。

[AZURE.INCLUDE [storsimple-use-putty](../../includes/storsimple-use-putty.md)]

### 步驟 2：進入維護模式 <a name="step2">

連接到主控台之後，請判定是否有需要安裝的更新，然後進入維護模式加以安裝。

[AZURE.INCLUDE [storsimple-enter-maintenance-mode](../../includes/storsimple-enter-maintenance-mode.md)]

### 步驟 3：安裝更新 <a name="step3">

接下來，安裝您的更新。

[AZURE.INCLUDE [storsimple-install-maintenance-mode-updates](../../includes/storsimple-install-maintenance-mode-updates.md)]
 
### 步驟 4：結束維護模式 <a name="step4">

最後，結束維護模式。

[AZURE.INCLUDE [storsimple-exit-maintenance-mode](../../includes/storsimple-exit-maintenance-mode.md)]

## 透過 Windows PowerShell for StorSimple 安裝 Hotfix

不同於 Microsoft Azure StorSimple 的更新，Hotfix 是從共用資料夾進行安裝的。 和更新一樣，有兩種類型的 Hotfix： 

- 一般的 Hotfix 
- 維護模式的 Hotfix  

下列程序說明如何使用 Windows PowerShell for StorSimple，安裝一般和維護模式的 Hotfix。

[AZURE.INCLUDE [storsimple-install-regular-hotfixes](../../includes/storsimple-install-regular-hotfixes.md)]

[AZURE.INCLUDE [storsimple-install-maintenance-mode-hotfixes](../../includes/storsimple-install-maintenance-mode-hotfixes.md)]

## 如果您執行裝置的原廠重設，更新該怎麼辦？

如果將裝置重設為原廠設定，則所有更新都會遺失。 在註冊並設定原廠重設裝置之後，您必須透過 Azure 傳統入口網站和 (或) Windows PowerShell for StorSimple 手動安裝更新。 如需原廠重設的詳細資訊，請參閱 [將裝置重設為原廠預設設定](storsimple-manage-device-controller.md#reset-the-device-to-factory-default-settings)。

## 後續步驟

- 深入了解 [使用 Windows PowerShell for StorSimple 管理 StorSimple 裝置](storsimple-windows-powershell-administration.md)。
- 深入了解 [使用 StorSimple Manager 服務管理 StorSimple 裝置](storsimple-manager-service-administration.md)。
