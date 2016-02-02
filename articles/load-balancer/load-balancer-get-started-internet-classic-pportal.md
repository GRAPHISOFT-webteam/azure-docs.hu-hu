<properties 
   pageTitle="開始使用 Azure 入口網站在傳統部署模型中建立網際網路面向的負載平衡器 | Microsoft Azure"
   description="了解如何使用 Azure 入口網站在傳統部署模型中建立網際網路面向的負載平衡器"
   services="load-balancer"
   documentationCenter="na"
   authors="joaoma"
   manager="carolz"
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


# 開始在 Azure 入口網站中建立網際網路面向的負載平衡器 (傳統)

[AZURE.INCLUDE [load-balancer-get-started-internet-classic-selectors-include.md](../../includes/load-balancer-get-started-internet-classic-selectors-include.md)]

[AZURE.INCLUDE [load-balancer-get-started-internet-intro-include.md](../../includes/load-balancer-get-started-internet-intro-include.md)]

[AZURE.INCLUDE [azure-arm-classic-important-include](../../includes/azure-arm-classic-important-include.md)] 本文章涵蓋傳統部署模型。 您也可以 [了解如何建立網際網路面向的負載平衡器搭配使用 Azure 資源管理員](load-balancer-get-started-internet-arm-ps.md)。


[AZURE.INCLUDE [load-balancer-get-started-internet-scenario-include.md](../../includes/load-balancer-get-started-internet-scenario-include.md)]



## 開始使用 Azure 入口網站建立負載平衡器端點

若要從 Azure 入口網站建立網際網路面向的負載平衡器 (傳統) 部署模型，請依照下列步驟。

1. 從瀏覽器，瀏覽至 http://portal.azure.com，並有必要，請登入您的 Azure 帳戶。

2. 移至 [虛擬機器 (傳統)] 刀鋒視窗 > 選取虛擬機器。

3. 在虛擬機器"essentials"分頁 > 選取 [所有設定]

4. 按一下 [負載平衡集]。

5. 建立新的負載平衡器，請按一下 [加入] 圖示上方負載平衡集刀鋒視窗。

6. 針對網際網路面向的負載平衡器，將 [負載平衡集類型] 選取為公用。

7. 按一下 [設定必要設定] 開啟 [選擇負載平衡集]，然後按 [建立負載平衡集]。

8. 在 [建立負載平衡集] 刀鋒視窗中，建立負載平衡集的名稱。 填寫名稱、公用連接埠，探查通訊協定，探查連接埠。

9. 視需要變更探查間隔和重試次數。

10. (選擇性) 如果您想要，可以從 [建立負載平衡器集] 刀鋒視窗之中設定存取控制規則。

11. 按一下 [確定] 回到 [加入負載平衡集] 刀鋒視窗。

12. 按一下 [確定] 並等候新的負載平衡器資源顯示在 [負載平衡器集] 刀鋒視窗中。

## 後續步驟

[開始設定內部負載平衡器](load-balancer-internal-getstarted.md)

[設定負載平衡器分配模式](load-balancer-distribution-mode.md)

[設定負載平衡器的閒置 TCP 逾時設定](load-balancer-tcp-idle-timeout.md)





