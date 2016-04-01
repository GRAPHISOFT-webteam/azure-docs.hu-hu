<properties 
   pageTitle="管理 StorSimple 儲存體帳戶 | Microsoft Azure"
   description="說明如何使用 StorSimple Manager 的 [設定] 頁面來加入、編輯、刪除或替換儲存體帳戶的安全性金鑰。"
   services="storsimple"
   documentationCenter="NA"
   authors="SharS"
   manager="carolz"
   editor="" />
<tags 
   ms.service="storsimple"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="TBD"
   ms.date="12/01/2015"
   ms.author="v-sharos" />

# 使用 StorSimple Manager 服務管理儲存體帳戶

## 概觀

 **設定** 頁面會顯示可以建立 StorSimple Manager 服務中的所有全域服務參數。 這些參數可以套用到與該服務連線的所有裝置，還包括：

- 儲存體帳戶 
- 頻寬範本 
- 存取控制記錄 

本教學課程說明如何使用 **設定** 頁面來新增、 編輯或刪除儲存體帳戶或替換儲存體帳戶的安全性金鑰。

 ![[設定] 頁面](./media/storsimple-manage-storage-accounts/HCS_ConfigureService.png)  

儲存體帳戶包含的認證可供裝置用來存取雲端服務提供者的儲存體帳戶。 對於 Microsoft Azure 儲存體帳戶，像是帳戶名稱與主要存取金鑰就屬於這些認證。 

在 **設定** 頁面上，會建立包含下列資訊以表格格式顯示的計費訂用帳戶的所有儲存體帳戶 ︰

- **名稱** – 建立時指派給該帳戶的唯一名稱。
- **啟用 SSL** – 是否已啟用 SSL，以及裝置對雲端的通訊都會透過安全通道。
- **使用** – 使用儲存體帳戶的磁碟區數目。

最常見的工作與相關儲存體帳戶，可對 **設定** 頁面 ︰

- 新增儲存體帳戶 
- 編輯儲存體帳戶 
- 刪除儲存體帳戶 
- 替換儲存體帳戶的金鑰 

## 儲存體帳戶類型

有三種儲存體帳戶類型能與 StorSimple 裝置搭配使用。

- **自動產生的儲存體帳戶** – 第一次建立服務時，正如其名，自動產生這種類型的儲存體帳戶。 若要深入了解如何建立此儲存體帳戶，請參閱 [步驟 1 ︰ 建立新的服務](storsimple-deployment-walkthrough-u1.md#step-1-create-a-new-service) 中 [部署在內部部署 StorSimple 裝置](storsimple-deployment-walkthrough.md)。 
- **儲存體帳戶的服務訂閱** – 這些是與服務相同的訂閱相關聯的 Azure 儲存體帳戶。 若要深入了解如何使用這些儲存體建立帳戶，請參閱 [關於 Azure 儲存體帳戶](../storage/storage-create-storage-account.md)。 
- **服務訂閱以外的儲存體帳戶** – 這些是不與您的服務相關聯且可能存在的服務建立之前的 Azure 儲存體帳戶。

## 新增儲存體帳戶

您可以提供唯一的易記名稱以及與儲存體帳戶(搭配指定的雲端服務提供者) 連結的存取認證來新增儲存體帳戶。 您也能選擇啟用安全通訊端層 (SSL) 模式，建立裝置與雲端之間網路通訊的安全通道。

您可以為指定的雲端服務提供者建立多個帳戶。 不過，請注意，建立儲存體帳戶之後，您無法變更雲端服務提供者。

儲存體帳戶在儲存時，服務會嘗試與您的雲端服務提供者通訊。 此時會驗證您提供的認證與存取資料。 只有當驗證成功時，才會建立儲存體帳戶。 如果驗證失敗，則會顯示適當的錯誤訊息。

> [AZURE.NOTE] 新增儲存體帳戶的程序根據您使用的軟體版本而有所不同。 請務必依照您 StorSimple 版本適用的程序執行。

[AZURE.INCLUDE [add-a-storage-account-update1](../../includes/storsimple-configure-new-storage-account-u1.md)]

[AZURE.INCLUDE [add-a-storage-account](../../includes/storsimple-configure-new-storage-account.md)]

## 編輯儲存體帳戶

您可以編輯磁碟區容器所使用的儲存體帳戶。 如果您編輯的儲存體帳戶目前正在使用中，唯一可修改的欄位就是儲存體帳戶的存取金鑰。 您可以提供新的儲存體存取金鑰，並儲存更新的設定。

#### 若要編輯儲存體帳戶

1. 在服務登陸頁面上，選取您的服務、 按兩下服務名稱，然後按一下 **設定**。

2. 按一下 [ **新增/編輯儲存體帳戶**。

3. 在 **新增/編輯儲存體帳戶** ] 對話方塊中 ︰

  1. 在下拉式清單中 **儲存體帳戶**, ，選擇您想要修改的現有帳戶。 這也包含服務初次建立時自動產生的儲存體帳戶。
  2. 如果有需要，您可以修改 **啟用 SSL 模式** 選取項目。
  3. 您可以選擇替換儲存體帳戶存取金鑰。 請參閱 [的儲存體帳戶金鑰輪替](#key-rotation-of-storage-accounts) 如需有關如何執行金鑰替換。
  4. 按一下核取圖示 ![核取圖示](./media/storsimple-manage-storage-accounts/HCS_CheckIcon.png) 儲存設定。 上的設定更新 **設定** 頁面。 按一下 [ **儲存** 以儲存剛更新的設定。

     ![編輯儲存體帳戶](./media/storsimple-manage-storage-accounts/HCs_AddEditStorageAccount.png)
  
## 刪除儲存體帳戶

> [AZURE.IMPORTANT] 只有當它不是由磁碟區容器，您可以刪除儲存體帳戶。 如果磁碟區容器正在使用儲存體帳戶，請先刪除磁碟區容器，然後再刪除相關聯的儲存體帳戶。

#### 若要刪除儲存體帳戶

1. 在 StorSimple Manager 服務登陸頁面上，選取您的服務、 按兩下服務名稱，然後按一下 **設定**。

2. 在儲存體帳戶的表格式清單中，將滑鼠停留在您想要刪除的帳戶上方。

3. 刪除圖示 (**x**) 會出現在該儲存體帳戶最右側的欄。 按一下 [ **x** 圖示以刪除認證。

4. 當系統提示您確認，按一下 [ **是** 繼續進行刪除。 表格式清單會更新以反映所做的變更。

## 替換儲存體帳戶的金鑰

基於安全性理由，通常是在資料中心內才需要替換金鑰。 

> [AZURE.NOTE] 下列金鑰輪替資訊和輪替程序適用於只有 Microsoft Azure 儲存體帳戶。 若您使用的是其他雲端服務提供者，可以透過該提供者的儀表板管理儲存體帳戶金鑰。
 
每個 Microsoft Azure 訂用帳戶可以有一或多個相關聯的儲存體帳戶。 訂用帳戶與每個儲存體帳戶的存取金鑰可以控制這些帳戶的存取權。 

當您建立儲存體帳戶時，Azure 會產生兩個 512 位元的儲存體存取金鑰，可在存取儲存體帳戶時用於驗證。 有這兩個儲存體存取金鑰，您不需要中斷儲存體服務或對該服務的存取，就能重新產生金鑰。 目前正在使用中的索引鍵是 *主要* 金鑰，而備用金鑰稱為 *次要* 索引鍵。 當 Microsoft Azure StorSimple 裝置存取您的雲端儲存體服務提供者時必須提供這些兩個機碼之一。

## 什麼是替換金鑰？

一般而言，應用程式只使用其中一個金鑰來存取您的資料。 經過一段時間之後，您可以讓應用程式切換為使用第二個金鑰。 在您將應用程式切換至次要金鑰之後，可以淘汰第一個金鑰，然後產生新的金鑰。 這種使用兩個金鑰的方式可讓您的應用程式存取資料，卻不會產生任何停機時間。

儲存體帳戶金鑰一律以加密的格式儲存在服務中。 不過，您可以透過 StorSimple Manager 服務來重設。 服務可為相同訂用帳戶中的所有儲存體帳戶取得主要金鑰與次要金鑰，包括儲存體服務中建立的帳戶以及 StorSimple Manager 服務初次建立時產生的預設儲存體帳戶。 StorSimple Manager 服務將一律從 Azure 傳統入口網站取得這些金鑰，再以加密的方式儲存。

## 替換工作流程

Microsoft Azure 系統管理員可以直接存取儲存體帳戶 (透過 Microsoft Azure 儲存體服務) 來重新產生或變更主要金鑰或次要金鑰。 StorSimple Manager 服務不會自動發現這項變更。

若要通知 StorSimple Manager 服務所做的變更，您需要存取 StorSimple Manager 服務，存取儲存體帳戶，然後同步處理主要或次要金鑰 (根據哪一個有變更而定)。 服務接著會取得最新的金鑰，將金鑰加密，然後將加密的金鑰傳送給裝置。

#### 若要同步服務 (僅限 Azure only) 之訂用帳戶中的儲存體帳戶金鑰

1. 在 **服務** 頁面上，按一下 **設定** ] 索引標籤。

2. 按一下 [ **新增/編輯儲存體帳戶**。

3. 在對話方塊中，執行下列動作：

  1. 選取您要同步處理其金鑰的儲存體帳戶。 儲存體帳戶金鑰是以加密的狀態顯示。
  2. 在 StorSimple Manager 服務中，您需要更新先前在 Microsoft Azure 儲存體服務中變更的金鑰。 如果主要存取金鑰有所變更 （重新產生），按一下 [ **同步處理主要金鑰**。 如果次要金鑰有所變更，按一下 [ **同步處理次要金鑰**。

    ![synchronize keys](./media/storsimple-manage-storage-accounts/HCS_KeyRotationStorageAccountSameSubscriptionAsService.png)

#### 若要同步處理服務訂用帳戶外的儲存體帳戶金鑰

1. 在 **服務** 頁面上，按一下 **設定** ] 索引標籤。

2. 按一下 [ **新增/編輯儲存體帳戶**。

3. 在對話方塊中，執行下列動作：

  1. 選取您要更新其存取金鑰的儲存體帳戶。
  2. 您必須更新 StorSimple Manager 服務中的儲存體存取金鑰。 在此情況下，您可以看到儲存體存取金鑰。 輸入新的金鑰在 **儲存體帳戶存取金鑰**] 方塊。 
  3. 儲存您的變更。 現在應已更新您的儲存體帳戶存取金鑰。

## 後續步驟

- 深入了解 [StorSimple 安全性](storsimple-security.md)。
- 深入了解 [使用 StorSimple Manager 服務管理 StorSimple 裝置](storsimple-manager-service-administration.md)。

