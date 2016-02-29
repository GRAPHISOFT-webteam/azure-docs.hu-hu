
<properties 
   pageTitle="開始使用 Azure 傳統入口網站在傳統部署模型中建立網際網路面向的負載平衡器 | Microsoft Azure"
   description="了解如何使用 Azure 傳統入口網站在傳統部署模型中建立網際網路面向的負載平衡器"
   services="load-balancer"
   documentationCenter="na"
   authors="joaoma"
   manager="carmonm"
   editor=""
   tags="azure-service-management"
/>
<tags  
   ms.service="load-balancer"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="12/07/2015"
   ms.author="joaoma" />

# 開始在 Azure 傳統入口網站中建立網際網路面向的負載平衡器 (傳統)

[AZURE.INCLUDE [load-balancer-get-started-internet-classic-selectors-include.md](../../includes/load-balancer-get-started-internet-classic-selectors-include.md)]

[AZURE.INCLUDE [load-balancer-get-started-internet-intro-include.md](../../includes/load-balancer-get-started-internet-intro-include.md)]

[AZURE.INCLUDE [azure-arm-classic-important-include](../../includes/azure-arm-classic-important-include.md)] 本文章涵蓋傳統部署模型。 您也可以 [了解如何建立網際網路面向的負載平衡器搭配使用 Azure 資源管理員](load-balancer-get-started-internet-arm-ps.md)。

[AZURE.INCLUDE [load-balancer-get-started-internet-scenario-include.md](../../includes/load-balancer-get-started-internet-scenario-include.md)]


## 為虛擬機器設定網際網路面向的負載平衡器

為了使雲端服務的各虛擬機器的網際網路流量達到負載平衡，您必須建立負載平衡的集合。 此程序假設您已建立虛擬機器，而且全都在相同的雲端服務內。

**設定虛擬機器的負載平衡集合**

1. 在 Azure 傳統入口網站中，按一下 [ **虛擬機器**, ，然後按一下 [虛擬機器中的負載平衡集的名稱。
2.  按一下 [ **端點**, ，然後按一下 [ **新增**。

4.  在 **將端點加入至虛擬機器** 頁面上，按一下向右箭號。

4.  在 **指定端點的詳細資料** 頁面:
    - 在 **名稱**, ，輸入端點的名稱，或從常見通訊協定的預先定義端點的清單中選取名稱。
    -  在 **通訊協定**, ，視需要選取端點，TCP 或 UDP 型別所需的通訊協定。
    -  在 **公用連接埠和私人連接埠**, ，輸入您想將虛擬機器使用，視需要的連接埠號碼。 您可以在虛擬機器上使用私人連接埠和防火牆規則，以適合應用程式的方式來重新導向流量。 私人連接埠可與公用連接埠相同。 例如，對於 Web (HTTP) 端點的流量，您可以將連接埠 80 同時指派給公用和私人連接埠。

5.  選取 **建立負載平衡集**, ，然後按一下向右箭號。

6.  在 **設定負載平衡集** 頁面上，輸入負載平衡集名稱，然後指派 Azure 負載平衡器探查行為的值。
負載平衡器會使用探查，來判斷負載平衡集合中的虛擬機器是否可用於接收連入流量。

7.  按一下核取記號以建立負載平衡端點。 您會看到 **是** 中 **負載平衡集名稱** 資料行的 **端點** 的虛擬機器] 頁面。

8.  在入口網站中，按一下 [ **虛擬機器**, ，按一下 [負載平衡集合中的其他虛擬機器的名稱，按一下 **端點**, ，然後按一下 [ **新增**。

9.  在 **將端點加入至虛擬機器** 頁面上，按一下 **將端點加入至現有的負載平衡集**, 選取負載平衡集的名稱，然後按一下向右箭號。

10. 在 **指定端點的詳細資料** 頁面上，輸入端點的名稱，然後按一下 [核取記號。
針對負載平衡集合中的其他虛擬機器，重複步驟 8-10。



## 後續步驟

[開始設定內部負載平衡器](load-balancer-internal-getstarted.md)

[設定負載平衡器分配模式](load-balancer-distribution-mode.md)

[設定負載平衡器的閒置 TCP 逾時設定](load-balancer-tcp-idle-timeout.md)


