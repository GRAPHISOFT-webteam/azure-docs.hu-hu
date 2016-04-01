<properties 
   pageTitle="開啟或關閉您的 StorSimple 裝置 | Microsoft Azure"
   description="說明如何開啟新的 StorSimple 裝置、開啟曾關閉或失去電源的裝置，以及關閉執行中的裝置。"
   services="storsimple"
   documentationCenter=""
   authors="alkohli"
   manager="carolz"
   editor="" />
<tags 
   ms.service="storsimple"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="TBD"
   ms.date="12/02/2015"
   ms.author="alkohli" />

# 開啟或關閉您的 StorSimple 裝置 

## 概觀

正常系統作業並不需要關閉 Microsoft Azure StorSimple 裝置。 不過，您可能需要開啟新的裝置或裝置，必須關閉。 一般而言，在您需要更換故障的硬體、實際移動單元，或將裝置報廢的情況下才需要關機。 本教學課程將說明在不同案例中開啟和關閉 StorSimple 裝置的必要程序。

下表列出開啟和關閉 StorSimple 裝置的多種案例，並提供適當程序的連結。

|案例|參考主題|
|:-------|:---------------|
|開啟新的裝置|[開啟新的裝置](#turn-on-a-new-device)<ul><li>[只有主要機箱的新裝置](#new-device-with-primary-enclosure-only)</li><li>[具有 EBOD 機箱的新裝置](#new-device-with-ebod-enclosure)</li></ul>|
|在關機後開啟裝置|[在關機後開啟裝置](#turn-on-a-device-after-shutdown)<ul><li>[只有主要機箱的裝置](#device-with-primary-enclosure-only)</li><li>[具有 EBOD 機箱的裝置](#device-with-ebod-enclosure)</li></ul>|
|在電源中斷後開啟裝置|[在電源中斷後開啟裝置](#turn-on-a-device-after-a-power-loss)<ul><li>[只有主要機箱的裝置](#8100)</li><li>[具有 EBOD 機箱的裝置](#8600)</li></ul>|
|在主要機箱和 EBOD 連線中斷後開啟裝置|[在主要機箱和 EBOD 機箱連線中斷後開啟裝置](#turn-on-a-device-after-the-primary-and-EBOD-enclosure-connection-is-lost)|
|關閉執行中的裝置|[關閉執行中的裝置](#turn-off-a-running-device)<ul><li>[只有主要機箱的裝置](#8100a)</li><li>[具有 EBOD 機箱的裝置](#8600a)</li></ul>|

## 開啟新的裝置

根據裝置型號是 8100 或 8600，初次開啟 Microsoft Azure StorSimple 裝置的步驟有所不同。 8100 具有單一主要機箱，而 8600 是具有主要機箱與 EBOD 機箱的雙重機箱裝置。 下列各節涵蓋這兩個型號的詳細步驟。

- [只有主要機箱的新裝置](#new-device-with-primary-enclosure-only)

- [具有 EBOD 機箱的新裝置](#new-device-with-ebod-enclosure)

### 只有主要機箱的新裝置

StorSimple 8100 型是單一機箱裝置。 您的裝置包含備援電源和冷卻模組 (PCM)。 這兩個 PCM 都必須安裝，並且連接到不同的電源來源，以確保高可用性。

請執行下列步驟，以連接您的裝置的電源線。

[AZURE.INCLUDE [storsimple-cable-8100-for-power](../../includes/storsimple-cable-8100-for-power.md)]

>[AZURE.NOTE]完成裝置設定和纜線連接的指示，請移至 [安裝 StorSimple 8100 裝置](storsimple-8100-hardware-installation.md)。 請確定有確實地依照指示進行。

### 具有 EBOD 機箱的新裝置

StorSimple 8600 型同時具有主要機箱和 EBOD 機箱。 這需要使用纜線將單元連接在一起，以取得序列連接 SCSI (SAS) 連線與電源。

第一次設定此裝置時，請先執行 SAS 佈線步驟，然後再完成電源佈線步驟。

[AZURE.INCLUDE [storsimple-sas-cable-8600](../../includes/storsimple-sas-cable-8600.md)]

[AZURE.INCLUDE [storsimple-cable-8600-for-power](../../includes/storsimple-cable-8600-for-power.md)]

>[AZURE.NOTE]完成裝置設定和纜線連接的指示，請移至 [安裝 StorSimple 8600 裝置](storsimple-8600-hardware-installation.md)。 請確定有確實地依照指示進行。

## 在關機後開啟裝置

根據裝置型號是 8100 或 8600，Microsoft Azure StorSimple 裝置關閉後再開啟它的步驟有所不同。 8100 具有單一主要機箱，而 8600 是具有主要機箱與 EBOD 機箱的雙重機箱裝置。

- [只有主要機箱的裝置](#device-with-primary-enclosure-only)

- [具有 EBOD 機箱的裝置](#device-with-ebod-enclosure)

### 只有主要機箱的裝置

在關機之後，請使用下列程序來開啟只有主要機箱而沒有 EBOD 機箱的 StorSimple 裝置。

#### 若要開啟只有主要機箱的裝置

1. 請確定電源和冷卻模組 (PCM) 上的電源開關都在 OFF 的位置。 如果開關不是在 OFF 的位置，請將它們切換到 OFF 的位置，並等候燈號熄滅。

2. 將 PCM 上的電源開關都切換到 ON 的位置，開啟裝置。 裝置應該會開啟。

3. 檢查下列項目來確認裝置是否完全開啟：

    1. PCM 模組上的「OK」LED 燈號都是綠色。

    2. 控制器上的狀態 LED 燈號是持續的綠燈。

    3. 其中一個控制器上的藍色 LED 燈號正在閃爍，指出控制器正作用中。

    如果有任何不符合上述的情況，則裝置的狀態不良。 請 [連絡 Microsoft 支援服務](storsimple-contact-microsoft-support.md)。

### 具有 EBOD 機箱的裝置

在關機之後，請使用下列程序來開啟具有主要機箱和 EBOD 機箱的 StorSimple 裝置。 請確實遵循說明依序執行每個步驟。 若沒有這麼做，可能會導致資料遺失。

#### 若要開啟具有主要和 EBOD 機箱的裝置

1. 請確定 EBOD 機箱已經連接至主要機箱。 如需詳細資訊，請參閱 [安裝 StorSimple 8600 裝置](storsimple-8600-hardware-installation.md)。

2. 請確定 EBOD 和主要機箱上的電源和冷卻模組 (PCM) 都在 OFF 的位置。 如果開關不是在 OFF 的位置，請將它們切換到 OFF 的位置，並等候燈號熄滅。

3. 將 PCM 上的電源開關都切換到 ON 的位置，先開啟 EBOD 機箱。 PCM 的 LED 燈號應該是綠色。 此單元上的 EBOD 控制器 LED 綠色燈號指出 EBOD 機箱已經開啟。

4. 將 PCM 上的電源開關都切換到 ON 的位置，開啟主要機箱。 現在整個系統應該已經開啟。

5. 確認 SAS LED 燈號都是綠色，確保 EBOD 機箱和主要機箱之間的連線狀態良好。

## 在電源中斷後開啟裝置

電源中斷會關閉 Microsoft Azure StorSimple 裝置。 電源中斷可能會發生在其中一個電源供應器或同時發生在兩個電源供應器上。 根據裝置型號是 8100 或 8600，復原步驟有所不同。 8100 具有單一主要機箱，而 8600 是具有主要機箱與 EBOD 機箱的雙重機箱裝置。 本節將說明每個案例的修復程序。

- [只有主要機箱的裝置](#8100)

- [具有 EBOD 機箱的裝置](#8600)

### 只有主要機箱的裝置<a name="8100">

如果其中一個電源供應器的電源中斷，系統還是可以繼續正常作業。 不過，為確保裝置的高可用性，請儘速恢復電源供應器的電源。

如果同時在兩個電源供應器上發生電源中斷，系統會以有條理的方式關閉。 當電源恢復時，系統將會自動開啟。

### 具有 EBOD 機箱的裝置<a name="8600">

#### 單一電源供應器電源中斷

如果主要機箱或 EBOD 機箱的其中一個電源供應器電源中斷，系統還是可以繼續正常作業。 不過，為確保裝置的高可用性，請儘速恢復電源供應器的電源。

#### 主要機箱和 EBOD 機箱的電源供應器同時電源中斷

如果同時在兩個電源供應器上發生電源中斷，EBOD 機箱將會立即關閉，而主要機箱會以有條理的方式關閉。 當電源恢復時，應用裝置會自動啟動。

如果電源是以手動方式關閉，則執行下列步驟來恢復系統的電源。

1. 開啟 EBOD 機箱。

2. 在 EBOD 機箱開啟之後，開啟主要機箱。

### EBOD 機箱上的兩個電源供應器同時電源中斷

當安裝纜線時，您必須確定 EBOD 機箱絕對不會單獨連接至個別的 PDU。 如果 EBOD 和主要機箱同時故障，系統將會復原。

如果只有 EBOD 機箱的兩個電源供應器同時故障，系統將不會自動復原。 執行下列步驟開啟系統並將其還原至良好狀態：

1. 如果主要機箱已經開啟，請將電源和冷卻模組 (PCM) 關閉。

2. 請等候幾分鐘，讓系統關閉。

3. 開啟 EBOD 機箱。

4. 在 EBOD 機箱開啟之後，開啟主要機箱。

## 在主要機箱和 EBOD 機箱連線中斷後開啟裝置

如果待命控制器與對應的 EBOD 控制器連線中斷，裝置仍會繼續運作。 如果系統作用中的控制器和對應的 EBOD 控制器連線中斷，則應該會進行容錯移轉且裝置應該會繼續正常運作。

當移除兩條序列連接 SCSI (SAS) 纜線，或切斷 EBOD 機箱與主要機箱間的連線，裝置將會停止運作。 此時請執行下列步驟。

### 若要在連線中斷後開啟裝置

1. 進入裝置的背面。

2. 如果在 EBOD 機箱和主要機箱間的 SAS 纜線連線損毀，EBOD 機箱上所有的 SAS 通道 LED 燈號將會關閉。

3. 關閉 EBOD 機箱和主要機箱上的電源和冷卻模組 (PCM)。

4. 等候兩個機箱背面的所有燈號關閉。

5. 重新插入 SAS 纜線，並確定在 EBOD 機箱和主要機箱間有良好的連線。

6. 將 PCM 上的開關都切換到 ON 的位置，先開啟 EBOD 機箱。

7. 檢查綠色 LED 燈號是否亮起，確定 EBOD 機箱已經開啟。

8. 開啟主要機箱。

9. 透過檢查控制器綠色 LED 燈號是否亮起，確定主要機箱已經開啟。

10. 透過檢查 SAS 通道 LED 燈號 (每個 EBOD 控制器有 4 個) 全部亮起，確認 EBOD 機箱與主要機箱間的連線良好。

>[AZURE.IMPORTANT] 如果 SAS 纜線損壞或 EBOD 機箱與主要機箱之間的連線時，不是件好事，開啟系統，它將會進入修復模式。 請 [連絡 Microsoft 支援服務](storsimple-contact-microsoft-support.md) 如果發生這種情況。

## 關閉執行中的裝置

如果執行中的 Microsoft Azure StorSimple 裝置需要移動、報廢，或是更換運作失常的元件，就可能需要關機。 根據 Microsoft Azure StorSimple 裝置的型號是 8100 或 8600，步驟會有所不同。 8100 具有單一主要機箱，而 8600 是具有主要機箱與 EBOD 機箱的雙重機箱裝置。 本節將詳細說明關閉執行中裝置的步驟。

- [具有主要機箱的裝置](#8100a)

- [具有 EBOD 機箱的裝置](#8600a)

### 具有主要機箱的裝置 <a name="8100a"> 

目前沒有方法從 Azure 傳統入口網站關閉執行中的 StorSimple 裝置。 關閉它的唯一方式是使用 Windows PowerShell for StorSimple。 若要以有條理的方式關閉裝置，請進入 Windows PowerShell for StorSimple 並依照下列步驟進行。

>[AZURE.IMPORTANT] 請勿關閉執行中的裝置所使用的裝置背面的電源按鈕。
>
>關閉裝置之前，請確定所有的裝置元件狀態良好。 在 Azure 傳統入口網站，瀏覽至 **裝置** > **維護** > **硬體狀態**, ，並確認所有元件的狀態為綠色。 這只適用於狀態良好的系統。 如果系統要關閉以更換故障的元件，您會看到故障 （紅色） 或降級 （黃色） 狀態中的個別元件 **硬體狀態**。

透過裝置序列主控台或 Windows PowerShell 遠端處理，您可以連線至 Windows PowerShell for StorSimple。 在您進入 Windows PowerShell for StorSimple 之後，執行下列步驟以關閉執行中的裝置。

#### 若要關閉執行中的裝置

1. 連接至裝置的序列主控台。

2. 在出現的功能表中，確認您所連接的控制器是 **待命** 控制站。 如果不是待命控制器，請中斷控制器連接，然後連接至其他控制器。

3. 在序列主控台功能表中，選擇 [ **選項 1** 登入待命控制器具有完整存取權。

4. 在出現提示時輸入： 

    `Stop-HCSController`

    這應該會關閉目前的待命控制器。

    >[AZURE.IMPORTANT] 等待控制器完全關閉再繼續進行下一個步驟。

5. 若要確認關閉已經完成，請檢查裝置背面。 控制器的故障 LED 燈號應該是持續的紅燈。

6. 透過序列主控台連接至作用中的控制器，並依照相同步驟將它關閉。

7. 完全關閉兩個控制器之後，兩個控制器上的狀態 LED 燈號應該是閃爍的紅燈。

8. 如果您需要在此時將裝置完全關閉，請將電源和冷卻模組 (PCM) 上的電源開關切換為 OFF 的位置。

9. 若要確認該裝置已完全關閉，請檢查裝置背面的所有燈號都已關閉。

### 具有 EBOD 機箱的裝置<a name="8600a">

>[AZURE.IMPORTANT] 在關閉之前的主要機箱和 EBOD 機箱，請確定所有裝置元件都的狀況良好。 在 Azure 傳統入口網站，瀏覽至 **裝置** > **維護** > **硬體狀態**, ，並確認所有元件都均狀況良好。

#### 若要關閉具有 EBOD 機箱的執行中裝置

1. 依照所列出的所有步驟 [具有主要機箱裝置](#8100a)。

2. 關閉主要機箱之後，將電源和冷卻模組 (PCM) 的開關都切換至 OFF 以關閉 EBOD。

3. 若要確認 EBOD 已關閉，請檢查 EBOD 機箱背面的所有燈號都已關閉。

>[AZURE.NOTE] 用來連接 EBOD 機箱與主要機箱的 SAS 纜線應該後才會移除之前的系統關機。

## 後續步驟

[請連絡 Microsoft 支援](storsimple-contact-microsoft-support.md) 如果您遇到問題時開啟或關閉 StorSimple 裝置。



