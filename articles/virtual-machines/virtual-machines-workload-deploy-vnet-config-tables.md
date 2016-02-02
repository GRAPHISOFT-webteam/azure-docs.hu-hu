<properties
    pageTitle="使用組態資料表的虛擬網路 | Microsoft Azure"
    description="了解如何利用使用預先決定設定的組態資料表設定，來設定跨單位 Azure 虛擬網路。"
    documentationCenter=""
    services="virtual-machines"
    authors="JoeDavies-MSFT"
    manager="timlt"
    editor=""
    tags="azure-service-management"/>

<tags
    ms.service="virtual-machines"
    ms.workload="infrastructure-services"
    ms.tgt_pltfrm="Windows"
    ms.devlang="na"
    ms.topic="article"
    ms.date="10/20/2015"
    ms.author="josephd"/>


# 使用設定表格建立跨單位虛擬網路

[AZURE.INCLUDE [learn-about-deployment-models-classic-include](../../includes/learn-about-deployment-models-classic-include.md)] 資源管理員部署模型。

本主題將使用先前在下列設定表格組合中指定的設定，帶領您逐步建立跨單位虛擬網路：

- 表格 V：跨單位虛擬網路設定
- 表格 S：虛擬網路中的子網路
- 表格 D：內部部署 DNS 伺服器
- 表格 L：適用於區域網路的位址首碼

這些表格通常是在下列主題中進行填寫：說明在 Azure 中設定 IT 工作負載的方法，並包含跨單位虛擬網路。 請參閱 [第 1 階段: 設定 Azure](virtual-machines-workload-intranet-sharepoint-phase1.md) 範例。

下列程序會參考這些表格中的資訊，引導您進行完整的虛擬網路設定程序。 如果您未在另一個主題中，這些資料表中已經指定的設定，但您仍想要設定跨單位虛擬網路，請參閱 [設定 Azure 虛擬網路的跨單位站台對站台連線](../vpn-gateway/vpn-gateway-site-to-site-create.md)。
> [AZURE.NOTE] 此程序將逐步引導您使用網站間 VPN 連線來建立虛擬網路。 站台對站連線使用 Azure ExpressRoute 的相關資訊，請參閱 [ExpressRoute 技術概觀](../expressroute/expressroute-introduction.md)。

## 使用您的設定表格設定建立新的跨單位 Azure 虛擬網路

1. 登入 [Azure 傳統入口網站]。
2. 從工作列中，依序按一下 [新增] > [網路服務] > [虛擬網路] > [自訂建立]****。
3. 在 [虛擬網路詳細資料]**** 頁面上：
    - 在 [名稱]**** 中，輸入來自表格 V 之項目 1 的名稱。
    - 在 [位置]**** 中，選取來自表格 V 之項目 2 的區域。
4. 按 [下一步] 箭頭以繼續。
5. 在 [DNS 伺服器和 VPN 連線能力]**** 頁面上：
    - 在 [DNS 伺服器]**** 中，針對表格 D 中的每個項目設定易記名稱，以及您內部部署 DNS 伺服器的 IP 位址。
    - 在 [站對站連線能力]**** 中，選取 [設定網站間 VPN]****。
    - 如果您已經設定區域網路且想要使用該網路，可在 [區域網路]**** 中選取它的名稱。 如果您想要建立新的區域網路，可在 [區域網路]**** 中選取 [指定新的區域網路]****。
    - 如果您尚未為訂用帳戶設定區域網路，就看不到 [區域網路]**** 欄位。
6. 按 [下一步] 箭頭以繼續。
7. 在 [站對站連線能力]**** 頁面上 (如果您在步驟 5 中選取了 [指定新的區域網路]****)：
    - 在 [名稱]**** 中，輸入來自表格 V 之項目 3 的名稱 (區域網路名稱)。
    - 在 [VPN 裝置 IP 位址]**** 中，輸入來自表格 V 之項目 4 的位址。
    - 在 [位址空間]**** 中，針對表格 L 中的每個項目，輸入組織網路中與首碼 (在 [起始 IP]**** 中) 和首碼長度 (在 [CIDR (位址計數)]**** 中) 相關的 IP 位址空間。
8. 按 [下一步] 箭頭以繼續。
9. 在 [虛擬網路位址空間]**** 頁面上：
    - 在     **位址空間**, ，輸入表 V 中的項目 5 的虛擬網路的私人 IP 位址空間方面的前置詞 (在 **起始 IP**) 和首碼長度 (在 **CIDR (位址計數)**)。
    - 在 [子網路]**** 中，針對表格 S 中的每個項目：
        - 在 [子網路]**** 欄中輸入子網路的名稱，視需要覆寫預設名稱。
        - 輸入子網路上與首碼 (在 [起始 IP]**** 中) 和首碼長度 (在 [CIDR (位址計數)]**** 中) 相關的私人 IP 位址空間。
    - 按一下 [新增閘道子網路]****。
10. 按一下勾號以完成設定。

## 其他資源

[虛擬網路概觀](../virtual-network/virtual-networks-overview.md)

[虛擬網路組態工作](../documentation/services/virtual-machines/)

[設定 Azure 虛擬網路的跨單位站台對站台連線](../vpn-gateway/vpn-gateway-site-to-site-create.md)





