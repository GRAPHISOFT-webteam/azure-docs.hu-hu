<properties
   pageTitle="準備和測試優惠以部署至 Azure Marketplace | Microsoft Azure"
   description="將供應項目部署至 Azure Marketplace 之前，關於提供行銷內容、設定價格方案和測試供應項目的詳細指示。"
   services="marketplace-publishing"
   documentationCenter=""
   authors="HannibalSII"
   manager=""
   editor=""/>

<tags
   ms.service="marketplace"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="Azure"
   ms.workload="na"
   ms.date="11/30/2015"
   ms.author="hascipio"/>

# 使用行銷內容完成供應項目建立程序
在發佈程序的這個步驟中，您需要在 Azure Marketplace 中提供特定的行銷內容，以及關於您的優惠和 (或) SKU 的詳細資料。 例如，您將提供產品的描述、公司標誌、價目表、方案的詳細資料，以及其他將您的優惠和 (或) SKU 推送至預備環境的必要資訊。 此資訊會做為 Azure 入口網站中的行銷內容。 您將開始的程序 [發佈入口網站][link-pubportal]。

## 步驟 1：提供 Marketplace 行銷內容
**英文為預設值，且只支援的語言。**請確定欄位中提供的所有資訊都是英文。 在您進入預備環境之前，所有資訊皆可隨時編輯。

  1. 移至 「 發佈入口網站， [https://publish.windowsazure.com](https://publish.windowsazure.com)。
  2. 在左窗格中，按一下 [ **行銷** ] 索引標籤。
  3. 在 [主面板中，按一下 [ **英文 (美國)** ] 按鈕。

### 詳細資料
1. 輸入供應項目摘要、長摘要和供應項目的描述。
2.  以 PNG 格式上傳必要規格的影像 (在發佈入口網站中提及)，每種大小各一個。

  ![繪圖](media/marketplace-publishing-push-to-staging/pubportal-02.png)

  *優惠標題、描述和標誌*

### 連結
在 **連結** 左列上索引標籤，輸入任何連結可協助客戶的資訊。 輸入每個連結的名稱和 URL。

![繪圖](media/marketplace-publishing-push-to-staging/pubportal-02.png)

### 範例影像 (選擇性)
> [AZURE.NOTE] 包括範例影像是選擇性步驟。 您可以填寫其他行銷內容來符合推送至預備環境的需求。

在 **範例影像** 索引標籤上 [左] 功能表上，按一下來上傳新映像 **上傳新映像**。 如果您有現有的映像，而且您想要取代它，請按一下 **取代影像**。

![繪圖](media/marketplace-publishing-push-to-staging/pubportal-03.png)

### 方案
![繪圖](media/marketplace-publishing-push-to-staging/pubportal-02.png)

### 法律
在 **法律** 索引標籤上，提供使用您原則/詞彙的連結。 輸入或貼上詞彙的大型 **使用條款** 方塊。

> [AZURE.IMPORTANT] 所有欄位都必須都具有項目，包括的映像，您就可以推送至預備環境。


## 步驟 2：設定價格
### 定價模式
|定價模式 |說明 |
|---------------|------------------------------------------|
|基本| 購買時支付的每月分期費率，例如 $10 美元/月|
|耗用量 (又稱為 使用方式、 計量器) | 每次使用付費，由供應項目發行者定義。 無法定義每一基座、每位使用者等等的超額部分，因為並沒有使用者分數的概念，也沒有比例分攤的功能。 使用量由合作夥伴每小時報告一次。 客戶依每月計費週期付款，而非預付式的月繳方案。 |
|免費試用 | 客戶在一段有限期間內可以免費使用，之後就依正常費率付費。 |
|免費層 | 方案一律免費。 |
| 移轉 (也稱為 轉換或升級/降級) 的計劃 | 使用者從目前方案改用另一種可接受方案的概念，由合作夥伴定義。 |

**依供應項目類型的價格模式**

> [AZURE.IMPORTANT] 某些計價模型的可用性會因優惠類型。 請參閱下表。

| | 僅基本 | 僅耗用量 | 基本 + 耗用量 |
|---|---|---|---|
| 虛擬機器映像 | 否 | 是 | 否|
| 開發人員服務 | 是 | 是 | 是 |
| 資料服務 | 是 | 否 | 否 |

### 2.1. 設定 VM 價格
> [AZURE.NOTE] BYOL 只支援虛擬機器。

1.  在 **定價** 索引標籤上，您會看到所有支援的市場。 選取適當的市場移至價格欄位。
2.  發佈入口網站上提供的連結將會顯示定價資訊，協助您決定 SKU 的價格。
3.  如果您的 SKU 是 BYOL，請選取外部授權 (BYOL) SKU 可用性的核取方塊。
4.  如果您的 SKU 為每小時，請輸入軟體的價格。 沒有價格的 SKU 無法購買或使用。

  > [AZURE.NOTE] 若您有 BYOL 和每小時的 Sku，請務必將探討這兩個元件: BYOL 核取方塊和價格為每小時的值。

5.  定價精靈隨即開啟。 請繼續進行此程序以完成您的定價，如果您選擇允許在您指定市場之外購買，請為其他國家定價。
6.  有些國家採用 ISV 匯款。 若要在 ISV 匯款國家銷售，您必須能夠在您的 SKU 上索取與收集稅金，而且您應該計算與支付稅金給該國政府。 Microsoft 不負責提供法務或稅務指導。  如需銷售目標國家/地區的相關資訊，請參閱本文件簡介中的＜優惠的銷售目標國家/地區＞一節。

### 2.2. 設定開發人員服務價格
方案可以是基本 + 耗用量的任意組合，其中基本是每月價格，而超額部分是每次使用付費價格 (詳細資訊請見下文)。

**範例:**  Contoso 開發人員服務供應項目

| 規劃 | 價格 | 包括 | 移轉路徑 |
|-------|------|-------|-------|
|免費|$0 美元/月|基本功能。|可以移轉至其他方案|
|Bronze|$10 美元/月|基本功能和 1,000 個功能 X 的配額。|可以移轉至 Bronze Plus、Silver 和 Gold 方案|
|Bronze Plus|免費試用期間：$0 美元/月 + $0 美元/meter01 |功能 X 10000 的配額和基本功能。 一旦使用功能 X 配額時，客戶可以支付每 meter01 透過使用。|可以移轉至 Silver Plus 和 Gold 方案|
|Bronze Plus| 付費期間 (也稱為 免費試用版已過期): 每月 $10 + $0.05/meter01|功能 X 10000 的配額和基本功能。 一旦使用功能 X 配額時，客戶可以支付每 meter01 透過使用。|可以移轉至 Silver Plus 和 Gold 方案|
|Silver|$0.15 美元/meter01|客戶可以透過 meter01 (適用於 X 功能)，依每次使用付費。|可以移轉至 Bronze 和 Gold 方案|
|Silver Plus|$20 美元/月 + $0.15 美元/meter01 + $0.01 美元/meter02|10000 的功能 X 和 Y 功能的 100 的配額和基本功能。 一旦使用的功能 X 配額時，客戶可以支付每 meter01 透過使用。  功能 Y 配額用完時，客戶可以透過 meter02，依每次使用付費。|可以移轉至 Bronze Plus 和 Gold 方案|
|Gold|$1000 美元/月|10,000 個功能 X、1,000 個功能 Y 和無限制的功能 Z 的配額。|可以移轉至所有方案，免費除外|

## 步驟 3：提供支援資訊
有些資訊會在認證階段完成填寫。 您可以使用下列步驟來加入或編輯資訊。 合約詳細資料僅用於合作夥伴與 Microsoft 之間的內部通訊。 支援 URL 可供一般客戶使用。

1.  移至 **支援** 發佈入口網站左側的標題。
2.  下方輸入資訊 **工程連絡人**。
3.  下方輸入資訊 **客戶支援**。 如果您只提供電子郵件支援，請輸入虛設的電話號碼，該內容會改用您提供的電子郵件。
4.  輸入支援 URL。

## 步驟 4：選擇 Azure Marketplace 類別
 **類別** ] 索引標籤提供的選取項目陣列。 您的優惠可能會落在這些選取項目中，而且您最多可選取五個類別。

## 行銷活動的呈現方式
以下是提供行銷資訊的使用方式的詳細的檢視上 [Azure Marketplace 網站](http://azure.microsoft.com/marketplace) 和 [Azure 預覽入口網站](https://ms.portal.azure.com)。

### Azure Marketplace
![繪圖](media/marketplace-publishing-push-to-staging/acom-catalog-01.png)

![繪圖](media/marketplace-publishing-push-to-staging/acom-catalog-02.png)

*Azure.com Marketplace 網站上的優惠清單*

![繪圖](media/marketplace-publishing-push-to-staging/acom-listing-details-01.png)

*Azure.com Marketplace 網站上的優惠描述詳細資料*

![繪圖](media/marketplace-publishing-push-to-staging/acom-listing-details-02.png)

*Azure.com Marketplace 網站上的優惠定價詳細資料*

### Azure Preview 入口網站
![繪圖](media/marketplace-publishing-push-to-staging/portal-catalog-01.png)

*Azure Preview 入口網站中的優惠清單*

![繪圖](media/marketplace-publishing-push-to-staging/portal-listing-details-01.png)

*Azure Preview 入口網站中的優惠描述詳細資料*

## 後續步驟
既然已載入您的 Marketplace 內容，讓我們繼續在預備環境中測試您的優惠。 不過，您必須從下列清單選取適當的優惠類型，因為步驟會隨著優惠類型而有所不同。

||虛擬機器映像 |開發人員服務 | 資料服務 | 解決方案範本 |
|----|----|----|----|----|
| **步驟 3. 推入至預備環境優惠** | [在預備環境中測試您的 VM 優惠](marketplace-publishing-vm-image-test-in-staging.md) | 在預備環境中測試您的開發人員服務優惠 | [在預備環境中測試您的資料服務優惠](marketplace-publishing-data-service-test-in-staging.md) | [在預備環境中測試您的解決方案範本](marketplace-publishing-solution-template-test-in-staging.md) |

## 另請參閱
- [使用者入門：如何將優惠發佈至 Azure Marketplace](marketplace-publishing-getting-started.md)

[img-map-acom]:media/marketplace-publishing-push-to-staging/pubportal-mapping-acom.jpg
[img-map-portal]:media/marketplace-publishing-push-to-staging/pubportal-mapping-azure-portal.jpg
[img-map-link]:media/marketplace-publishing-push-to-staging/marketing-content-guide-links.jpg
[img-map-logo]:media/marketplace-publishing-push-to-staging/marketing-content-guide-logos.jpg
[img-map-title]:media/marketplace-publishing-push-to-staging/marketing-content-guide-publisher-offer.png

[link-pubportal]:https://publish.windowsazure.com
[link-push-to-production]:marketplace-publishing-push-to-production.md

