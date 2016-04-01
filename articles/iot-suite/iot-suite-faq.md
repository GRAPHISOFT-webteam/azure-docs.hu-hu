<properties
  pageTitle="Azure IoT Suite 常見問題集 | Microsoft Azure"
  description="Azure IoT Suite 的常見問題集 。"
  services=""
  documentationCenter=""
  authors="aguilaaj"
  manager="timlt"
  editor=""/>

<tags
  ms.service="na"
  ms.devlang="na"
  ms.topic="hero-article"
  ms.tgt_pltfrm="na"
  ms.workload="na"
  ms.date="11/17/2015"
  ms.author="araguila"/>
   
# 常見問題集

### 我可以在一個訂用帳戶中佈建多少個 DocumentDB 執行個體？

5 個。 您可以建立支援票證來提高此限制，但根據預設，每一個訂用帳戶只可以佈建 5 個 DocumentDB 執行個體。 因此，您只可以在一個指定的訂用帳戶中最多佈建 5 個遠端監視預先設定的方案。

### 我可以在一個訂用帳戶中佈建多少個免費 Bing 地圖服務 API？

2 個。 您只可以在一個訂用帳戶中建立 2 個免費 Bing 地圖 API。 根據預設，遠端監視解決方案會隨著免費 Bing 地圖 API 一起佈建。 因此，您只可以在一個訂用帳戶中最多佈建 2 個遠端監視方案。

### 在 Azure 入口網站中刪除資源群組，與在 azureiotsuite.com 中對預先設定解決方案按一下 [刪除] 之間的差異為何？

- 如果您刪除預先設定的方案中 [azureiotsuite.com][lnk-azureiotsuite], ，會刪除已佈建，當您建立預先設定的方案的所有資源，如果您新增其他資源到資源群組，這些也會一併刪除。 

- 如果您刪除資源群組中的 [Azure 入口網站][lnk-azure-portal], ，您只能刪除該資源群組中的資源，您也要刪除 Azure Active Directory 應用程式中的預先設定的方案相關聯 [Azure 傳統入口網站][lnk-classic-portal]。

### 如何刪除 AAD 租用戶？

請參閱 Eric Golpe 部落格文章 [刪除 Azure AD 租用戶的逐步解說][lnk-delete-aad-tennant]。


[lnk-azure-portal]: https://portal.azure.com
[lnk-azureiotsuite]: https://www.azureiotsuite.com/
[lnk-classic-portal]: https://manage.windowsazure.com
[lnk-delete-aad-tennant]: http://blogs.msdn.com/b/ericgolpe/archive/2015/04/30/walkthrough-of-deleting-an-azure-ad-tenant.aspx

