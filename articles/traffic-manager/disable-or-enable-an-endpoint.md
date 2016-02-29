<properties
   pageTitle="停用或啟用流量管理員端點 | Microsoft Azure"
   description="本文將協助停用或啟用流量管理員設定檔端點。"
   services="traffic-manager"
   documentationCenter="na"
   authors="joaoma"
   manager="carmonm"
   editor="tysonn" />
<tags 
   ms.service="traffic-manager"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="12/02/2015"
   ms.author="joaoma" />

# 停用或啟用流量管理員端點

您也可以停用屬於流量管理員設定檔一部分的個別端點。 端點包括雲端服務和網站。 停用端點會將端點做為設定檔的一部分保留，但是設定檔會以好像未包含端點的方式運作。 此動作對於暫時移除處於維護模式、或被重新部署的端點而言非常有用。 端點一旦再次啟動並執行，即可予以啟用。

>[AZURE.NOTE] **停用端點會與其在 Azure 中的部署狀態無關。健全的端點將會持續運作，並且即使在流量管理員中停用該服務時仍然可以接收流量。此外，在一個設定檔中停用端點並不會影響它在另一個設定檔中的狀態。**

## 若要停用端點

1. 在 Azure 傳統入口網站的 [流量管理員] 窗格中，找出包含您要修改端點設定的流量管理員設定檔，然後按一下設定檔名稱右側的箭號。 這會開啟設定檔的設定頁面。
1. 在頁面頂端，按一下 [ **端點** 以檢視您的組態中包含的端點。 
1. 按一下您想要停用，然後按一下 [端點 **停用** 頁面的底部。
1. 根據流量管理員網域名稱的 DNS 存留時間 (TTL) 設定，流量將會停止流向端點。 您可以從流量管理員設定檔的 [組態] 頁面變更 TTL。

## 若要啟用端點


1. 在 Azure 傳統入口網站的 [流量管理員] 窗格中，找出包含您要修改端點設定的流量管理員設定檔，然後按一下設定檔名稱右側的箭號。 這會開啟設定檔的設定頁面。
1. 在頁面頂端，按一下 [ **端點** 以檢視您的組態中包含的端點。
1. 按一下您想要啟用，然後按一下 [端點 **啟用** 頁面的底部。
1. 流量會如設定檔指定再次開始流向服務。

## 後續步驟

[流量管理員 - 停用、啟用或刪除設定檔](disable-enable-or-delete-a-profile.md)

[疑難排解流量管理員的已降級狀態](traffic-manager-troubleshooting-degraded.md)

[流量管理員的效能考量](traffic-manager-performance-considerations.md)
