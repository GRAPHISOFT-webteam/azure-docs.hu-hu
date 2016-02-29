<properties 
   pageTitle="設定 Azure 傳統入口網站中的 VPN 閘道 | Microsoft Azure"
   description="此文章將逐步引導您設定虛擬網路 VPN 閘道，以及將 VPN 閘道路由類型從靜態變更為動態或從動態變更為靜態。"
   services="vpn-gateway"
   documentationCenter="na"
   authors="cherylmc"
   manager="carolz"
   editor=""
   tags="azure-service-management"/>

<tags 
   ms.service="vpn-gateway"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="10/21/2015"
   ms.author="cherylmc" />

# 設定 Azure 傳統入口網站中的 VPN 閘道

>[AZURE.NOTE] 請務必了解 Azure 目前使用兩種部署模型: 資源管理員，以及傳統。 開始您的組態之前，請確定您瞭解部署模型和工具。 部署模型的相關資訊，請參閱 [Azure 部署模型](../azure-classic-rm.md)。

本文章是針對傳統部署模型與 Azure 傳統入口網站 (而非 Azure Portal) 所撰寫。 此時，如果您想要使用入口網站為傳統部署模型設定 VPN 閘道，您必須使用 Azure 傳統入口網站。 

如果您想要在 Azure 和內部部署位置之間建立安全的跨單位連接，您將需要設定 VPN 閘道。 閘道有不同的類型，而您將建立的閘道類型取決於您的網路設計計劃，以及您想要使用的內部部署 VPN 裝置。 例如，某些連線選項，例如點對站台連接，則需要動態路由閘道。 如果您想要將閘道設定為同時支援點對站台 (P2S) 連線和站台對站台 (S2S) 連接，則您將必須設定動態路由閘道，即使站台對站台可以使用其中一種閘道路由類型進行設定。 此外，您必須確定要用於站台對站台連線的裝置將支援所要建立的閘道類型。 請參閱 [關於 VPN 閘道](vpn-gateway-about-vpngateways.md)。

## 組態概觀

以下程序將逐步引導您在 Azure 傳統入口網站中設定 VPN 閘道。 這些步驟適用於使用服務管理模式建立並且會顯示在 Azure 傳統入口網站中的 VNet 閘道。 這些不是 Preview 入口網站或使用資源管理員模式設定 VNet 的使用步驟。 您可以找到有關建立使用中的資源管理員模式建立虛擬網路的 VNet 閘道 [建立虛擬網路使用站台對站連線使用 Azure 資源管理員和 PowerShell ](vpn-gateway-create-site-to-site-rm-powershell.md)。

設定您的閘道之前，您必須先建立虛擬網路。 若要建立跨單位連線的虛擬網路的步驟，請參閱 [與站台對站 VPN 連線設定虛擬網路](vpn-gateway-site-to-site-create.md), ，或 [設定虛擬網路點對站 VPN 連線與](vpn-gateway-point-to-site-create.md)。 然後，使用下方步驟來設定 VPN 閘道，並收集您設定 VPN 裝置所需的資訊。 

如果您已擁有 VPN 閘道，而且您想要變更路由類型，請參閱 [如何變更您的 VPN 閘道類型](#how-to-change-your-vpn-gateway-type)。

1. [建立 VPN 閘道](#create-a-vpn-gateway)

1. [收集 VPN 裝置組態的資訊](#gather-information-for-your-vpn-device-configuration)

1. [設定 VPN 裝置](#configure-your-vpn-device)

1. [驗證您的區域網路範圍和 VPN 閘道 IP 位址](#verify-your-local-network-ranges-and-vpn-gateway-ip-address)

## 建立 VPN 閘道

1. 在 Azure 傳統入口網站，在 **網路** 頁面上，確認您的虛擬網路的 [狀態] 欄 **建立**。

1. 在 **名稱** 資料行中，按一下 [虛擬網路的名稱。

1. 在 **儀表板** 頁面上，注意此 VNet 尚未設定閘道。 當您進行設定閘道的步驟時將會看到此狀態。

![閘道未建立](./media/vpn-gateway-configure-vpn-gateway-mp/IC717025.png)


接下來，在頁面底部，按一下 [ **建立閘道**。 您可以選取其中一個 *靜態路由* 或 *動態路由*。 您選取的路由類型取決於許多因素。 例如，您 VPN 裝置所支援的類型，以及您是否需要支援點對站台連接。 檢查 [關於 VPN 裝置的虛擬網路連線](vpn-gateway-about-vpn-devices.md) 以驗證您所需要的路由類型。 一旦建立閘道之後，您必須刪除並重新建立閘道才能變更閘道類型。 當系統提示您確認是否要建立閘道，請按一下 [ **是**。

![閘道類型](./media/vpn-gateway-configure-vpn-gateway-mp/IC717026.png)

閘道正在建立時，請注意頁面上的閘道圖形會變更為黃色，並顯示 *正在建立閘道*。 閘道建立作業最多可能需要花費 25 分鐘。 您必須先等候閘道建立完成，才能使用其他組態設定向前移動。

![閘道正在建立](./media/vpn-gateway-configure-vpn-gateway-mp/IC717027.png)

當閘道變更為 *連接*, ，您可以收集所需的 VPN 裝置的資訊。

![閘道正在連接](./media/vpn-gateway-configure-vpn-gateway-mp/IC717028.png)

## 收集 VPN 裝置組態的資訊

建立閘道之後，接著收集 VPN 裝置組態的資訊。 這項資訊位於 **儀表板** 虛擬網路的頁面:

1. **閘道 IP 位址-** 的 IP 位址位於 **儀表板** 頁面。 您必須在閘道建立完成之後才能看見 IP。

1. **共用的金鑰-** 按一下 **管理金鑰** 螢幕的底部。 按一下金鑰旁邊的圖示，將它複製到剪貼簿，然後貼上並儲存金鑰。 請注意，此按鈕僅在單一 S2S VPN 通道時才能正常運作。 如果您有多個 S2S VPN 通道，請使用取得虛擬網路閘道共用金鑰 API 或 PowerShell Cmdlet。

![管理金鑰](./media/vpn-gateway-configure-vpn-gateway-mp/IC717029.png)


## 設定 VPN 裝置

完成上一個步驟之後，您或您的網路管理員將需要設定 VPN 裝置以便建立連接。 請參閱 [關於 VPN 裝置的虛擬網路連線](vpn-gateway-about-vpn-devices.md) 如需有關 VPN 裝置。

設定 VPN 裝置之後，您可以在 VNet 的 [儀表板] 頁面上檢視更新的連接資訊。

您也可以執行下列任一命令以測試連接：

|                      | Cisco ASA             | Cisco ISR/ASR         | Juniper SSG/ISG | Juniper SRX/J                            |
|----------------------|-----------------------|-----------------------|-----------------|------------------------------------------|
| **檢查主要模式 SA**  | show crypto isakmp sa | show crypto isakmp sa | get ike cookie  | show security ike security-association   |
| **檢查快速模式 SA** | show crypto ipsec sa  | show crypto ipsec sa  | get sa          | show security ipsec security-association |


## 驗證您的區域網路範圍和 VPN 閘道 IP 位址

### 驗證您的 VPN 閘道 IP 位址

為了讓閘道能正確連接，您 VPN 裝置的 IP 位址必須針對您為跨單位組態所指定的區域網路進行正確設定。 一般而言，這會在設定站台對站台設定程序期間進行設定。 不過，如果您先前使用此區域網路搭配不同的裝置，或此區域網路的 IP 位址已變更，則您會想要編輯設定，以便指定正確的閘道 IP 位址。

1. 若要確認您的閘道 IP 位址，請按一下 [ **網路** 入口網站的左的窗格，然後再選取 **區域網路** 頁面的頂端。 您會看到針對每個本機網路所建立的 VPN 閘道位址。 若要編輯的 IP 位址，請選取 VNet，然後按一下 [ **編輯** 頁面的底部。

1. 在 **指定本機網路詳細資料** 頁面上，編輯 IP 位址，然後按一下頁面底部的下一步箭頭。

1. 在 **指定位址空間** 頁面上，按一下以儲存設定的右下角的核取記號。

### 驗證您區域網路的位址範圍

針對要流經閘道通往內部部署位置的正確流量，您將需要驗證已列出所要包含在本機網路組態中的每個 IP 位址範圍。 根據您的內部部署位置的網路組態，這可以是一大工程因為每個範圍都必須列在您的 Azure **區域網路** 組態。 針對包含在列出範圍內 IP 位址所繫結的流量，接著會透過虛擬網路 VPN 閘道來傳送。 您列出的 IP 位址範圍不必是私人範圍，不過您會想要驗證內部部署組態能夠接收輸入流量。

若要新增或編輯區域網路的範圍，請遵循下方程序。

1. 若要編輯區域網路的 IP 位址範圍，請按一下 [ **網路** 入口網站的左的窗格，然後再選取 **區域網路** 頁面的頂端。 在入口網站中，最簡單的方式檢視您所列出的範圍位於 **編輯** 頁面。 若要查看您的範圍，請選取 VNet，然後按一下 [ **編輯** 頁面的底部。

1. 在 **指定本機網路詳細資料** 頁面上，請勿進行任何變更。 按一下頁面底部的 [下一步] 箭頭。

1. 在 **指定位址空間** 頁面上，變更您網路位址空間。 然後按一下核取記號以儲存您的組態。

## 如何檢視閘道流量

您可以檢視您的閘道和閘道流量從您的虛擬網路 **儀表板** 頁面。

在 **儀表板** 頁面上，您可以檢視下列:

- 流經閘道，包括資料輸入和資料輸出的資料量。

- 針對虛擬網路所指定的 DNS 伺服器名稱。

- 您的閘道與 VPN 裝置之間的連接。

- 用來設定閘道與 VPN 裝置連接的共用金鑰。


## 如何變更您的 VPN 閘道類型

因為某些連線組態僅適用於特定閘道類型，您可能會發現需要變更現有 VPN 閘道的閘道類型。 例如，您可能要將點對站台連接新增至具有靜態閘道的現有站台對站台連線。 點對站台需要動態閘道，這表示若要進行設定，您必須將閘道類型從靜態變更為動態。

如果您需要變更 VPN 閘道路由類型，將會刪除現有的閘道，然後使用新的路由類型重新建立閘道。 若要變更閘道路由類型，您不需要刪除整個虛擬網路。

在變更閘道類型之前，請確定並驗證 VPN 裝置可支援所要使用的路由類型。 若要下載新的路由組態範例，以及檢查 VPN 裝置需求，請參閱 [關於 VPN 裝置的虛擬網路連線](vpn-gateway-about-vpn-devices.md)。

>[AZURE.IMPORTANT] 當您刪除虛擬網路 VPN 閘道時，會釋放指派給該閘道的 VIP。 當您重新建立閘道時，系統會將新的 VIP 指派給它。

1. **刪除現有的 VPN 閘道。**

    在 **儀表板** 虛擬網路] 頁面上，瀏覽至頁面底部，按一下 **刪除閘道**。 等候閘道已完成刪除的通知。 當在畫面上收到通知，表示您的閘道已完成刪除時，接下來您可以建立新的閘道。

1. **建立新的 VPN 閘道。**

    使用在頁面頂端的程序，建立新的閘道: [建立 VPN 閘道](#create-a-vpn-gateway)。


## 後續步驟

您可以進一步了解本文中的虛擬網路跨單位連線: [關於虛擬網路的安全的跨單位連線](vpn-gateway-cross-premises-options.md)。

您可以將虛擬機器加入您的虛擬網路。 請參閱 [如何建立自訂的虛擬機器](../virtual-machines/virtual-machines-create-custom.md)。

如果您想要設定點對站 VPN 連線，請參閱 [設定點對站 VPN 連線](vpn-gateway-point-to-site-create.md)。

 

