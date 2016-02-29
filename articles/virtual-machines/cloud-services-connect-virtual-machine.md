<properties
    pageTitle="連接雲端服務中的 VM | Microsoft Azure"
    description="將使用傳統部署模型建立的虛擬機器連接至 Azure 雲端服務或虛擬網路。"
    services="virtual-machines"
    documentationCenter=""
    authors="cynthn"
    manager="timlt"
    editor=""
    tags="azure-service-management"/>

<tags
    ms.service="virtual-machines"
    ms.workload="infrastructure-services"
    ms.tgt_pltfrm="vm-multiple"
    ms.devlang="na"
    ms.topic="article"
    ms.date="10/15/2015"
    ms.author="cynthn"/>


# 連接藉由虛擬網路或雲端服務，以傳統部署模型建立的虛擬機器

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-classic-include.md)] 資源管理員模型。


使用傳統部署模型建立的虛擬機器一律放置在雲端服務中。 雲端服務能做為容器，並提供唯一的公用 DNS 名稱、公用 IP 位址，以及一組透過網際網路存取虛擬機器的端點。 雲端服務可以位於虛擬網路，但這不是必要條件。

如果雲端服務不在虛擬網路，就稱為 *獨立* 雲端服務。 獨立雲端服務中的虛擬機器只能與使用其他虛擬機器之公用 DNS 名稱的其他虛擬機器通訊，該流量會透過網際網路傳送。 如果雲端服務是在虛擬網路中，該雲端服務中的虛擬機器可與虛擬網路中的所有其他虛擬機器通訊，而不需要透過網際網路傳送任何流量。

如果您將虛擬機器放在同一個獨立雲端服務中，您仍然可以使用負載平衡和可用性設定組。 如需詳細資訊，請參閱 [負載平衡虛擬機器](../articles/load-balance-virtual-machines.md) 和 [管理虛擬機器的可用性](../articles/manage-availability-virtual-machines.md)。 不過，您無法組織子網路上的虛擬機器，或將獨立雲端服務連線到內部部署網路。 以下是範例：

![獨立雲端服務中的虛擬機器](./media/howto-connect-vm-cloud-service/CloudServiceExample.png)

如果您將虛擬機器放在虛擬網路，您可以決定要將多少雲端服務用於負載平衡和可用性集。 此外，您可以利用內部部署網路的相同方式，在子網路上組織虛擬機器，並將虛擬網路連線到內部部署網路。 以下是範例：

![虛擬網路中的虛擬機器](./media/howto-connect-vm-cloud-service/VirtualNetworkExample.png)

若要在 Azure 中連線虛擬機器，建議使用虛擬網路。 最佳作法是在個別的雲端服務中設定應用程式的每一層。 不過，您可能需要將不同應用程式層的部分虛擬機器結合至相同的雲端服務，以維持在每個訂用帳戶最多有 200 項雲端服務的限制內。 若要檢閱本限制和其他限制，請參閱 [Azure 訂用帳戶和服務限制、 配額和條件約束](../azure-subscription-service-limits.md)。

## 連接虛擬網路中的 VM

若要連線虛擬網路中的虛擬機器：

1.  建立虛擬網路中的 [Azure 入口網站](../articles/virtual-network/virtual-networks-create-vnet-classic-pportal.md)。
2.  為部署一組建立雲端服務，以反映可用性設定組和負載平衡的設計。 在 Azure 傳統入口網站中，按一下 [ **新增 > 計算 > 雲端服務 > 自訂建立** 每個雲端服務。
3.  若要建立每個新的虛擬機器，請按一下 [ **新增 > 計算 > 虛擬機器 > 從組件庫**。 為 VM 選擇正確的雲端服務和虛擬網路。 如果雲端服務已加入虛擬網路，系統會為您選取服務名稱。

![選取虛擬機器的雲端服務](./media/howto-connect-vm-cloud-service/VMConfig1.png)

## 連接獨立雲端服務中的 VM

若要在獨立雲端服務中連接虛擬機器：

1.  建立雲端服務中的 [Azure 傳統入口網站](http://manage.windowsazure.com)。 按一下 [ **新增 > 計算 > 雲端服務 > 自訂建立**。 或者，當您建立第一部虛擬機器時，您可以為您的部署建立雲端服務。
2.  建立虛擬機器時，請選擇在上一個步驟中所建立雲端服務的名稱。
![將虛擬機器加入至現有的雲端服務。](./media/howto-connect-vm-cloud-service/Connect-VM-to-CS.png)

##資源
[虛擬機器負載平衡](../articles/load-balance-virtual-machines.md)

[管理虛擬機器的可用性](../articles/manage-availability-virtual-machines.md)

[虛擬網路組態工作](../documentation/services/virtual-machines/)

建立虛擬機器後，最好要新增資料磁碟，您的服務和工作負載才有地方可存放資料。 執行下列其中一項：

[如何將資料磁碟連接至 Linux 虛擬機器](../articles/virtual-machines/virtual-machines-linux-how-to-attach-disk.md)

[如何將資料磁碟附加至 Windows 虛擬機器](../articles/virtual-machines/storage-windows-attach-disk.md)

