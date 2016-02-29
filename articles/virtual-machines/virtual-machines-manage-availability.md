<properties
    pageTitle="管理 VM 可用性 | Microsoft Azure"
    description="了解如何使用多部虛擬機器以確保 Azure 應用程式的高可用性。"
    services="virtual-machines"
    documentationCenter=""
    authors="kenazk"
    manager="timlt"
    editor="tysonn"
    tags="azure-resource-manager,azure-service-management"/>

<tags
    ms.service="virtual-machines"
    ms.workload="infrastructure-services"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="07/23/2015"
    ms.author="kenazk"/>

# 管理虛擬機器的可用性

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-both-include.md)]

## 了解規劃和未規劃維護
有兩種可影響虛擬機器可用性的 Microsoft Azure 平台事件：規劃維護和非規劃維護。

- **規劃的維護事件** 是由 Microsoft 對基礎 Azure 平台進行改善整體可靠性、 效能和安全性的平台基礎結構的虛擬機器上執行的定期更新。 這些更新大多數都會在不影響虛擬機器或雲端服務的情況下執行。 但有時候，這些更新還是需要重新啟動虛擬機器，才能將必要的更新套用至平台基礎結構。

- **非計劃性的維護事件** 時發生的硬體或虛擬機器的實體基礎有某些方面的錯誤。 這可能包含本機網路錯誤、本機磁碟錯誤，或其他機架層級的錯誤。 Azure 會在偵測到此類錯誤時，自動從裝載虛擬機器且狀況不良的實體機器，將虛擬機器移轉至狀況良好的實體機器。 這類事件非常稀少，但可能會導致虛擬機器重新啟動。

## 設計高可用性的應用程式時要遵循最佳做法
為了減少一或多個這些事件造成的停機所帶來的影響，建議您為虛擬機器使用下列高可用性的最佳做法：

* [複本建立可用性設定組中設定多部虛擬機器]
* [將每個應用程式層設定至不同的可用性設定組]
* [結合負載平衡器與可用性設定組]
* [避免可用性設定組中的單一執行個體虛擬機器]

### 針對備援在可用性設定組中設定多部虛擬機器
若要為應用程式提供備援，建議您在可用性設定組中，將兩部以上的虛擬機器組成群組。 這項組態可以確保在規劃或未規劃的維護事件發生期間，至少有一部虛擬機器可以使用，且符合 99.95% 的 Azure SLA。 如需服務等級協定的詳細資訊，請參閱 「 雲端服務、 虛擬機器和虛擬網路 > 一節中的 [服務等級協定](../../../support/legal/sla/)。

基礎 Azure 平台會為可用性設定組中的每部虛擬機器指定一個更新網域 (UD) 和一個容錯網域 (FD)。 在指定的可用性設定組中，指派了五個非使用者可設定的 UD，表示虛擬機器群組和可同時重新啟動的基礎實體硬體。 當一個可用性設定組中設定了超過五部虛擬機器，會將第六部虛擬機器放入與第一部虛擬機器相同的 UD 中，而第七部則會放入與第二部相同的 UD 中，以此類推。 重新啟動的 UD 順序可能不會在規劃的維護事件期間循序進行，而只會一次重新啟動一個 UD。

FD 定義共用通用電源和網路交換器的虛擬機器群組。 根據預設，可用性設定組中設定的虛擬機器會分置於兩個 FD 中。 將虛擬機器放入可用性設定組，並無法保護應用程式不會遭受作業系統錯誤或特定應用程式錯誤，而只會限制可能的實體硬體錯誤、網路中斷或電源中斷所帶來的影響。

<!--Image reference-->
   ![UD FD configuration](./media/virtual-machines-manage-availability/ud-fd-configuration.png)

>[AZURE.NOTE] 如需指示，請參閱 [如何設定虛擬機器的可用性設定組] []。

### 將每個應用程式層設定至不同的可用性設定組中
若可用性設定組中的虛擬機器幾乎都一樣，且對應用程式而言具有相同用途，建議您針對每個應用程式層設定可用性設定組。  若您在相同的可用性設定組中放入兩個不同的階層，則可以一次重新啟動位於相同應用程式層的所有虛擬機器。 您可以藉由在可用性設定組中為每個層設定至少兩部虛擬機器，確保每個層中至少有一部虛擬機器可供使用。

例如，您可以在一個可用性設定組中，將所有虛擬機器放入執行 IIS、Apache、Nginx 等的應用程式前端中。 請確認相同的可用性設定組中只有放入前端的虛擬機器。 同樣地，也要確認資料層的虛擬機器僅會放在它們自己的可用性設定組中，如同複寫的 SQL Server 虛擬機器或 MySQL 虛擬機器。

<!--Image reference-->
   ![Application tiers](./media/virtual-machines-manage-availability/application-tiers.png)


### 將負載平衡器與可用性設定組結合
將 Azure 負載平衡器與可用性設定組結合，以獲得最多的應用程式備援能力。 Azure 負載平衡器會在多部虛擬機器之間分配流量。 我們的標準層虛擬機器中包含 Azure 負載平衡器。 請注意，並非所有的虛擬機器階層都包含 Azure 負載平衡器。 如需有關負載平衡虛擬機器的詳細資訊，請閱讀 [負載平衡虛擬機器](../load-balance-virtual-machines.md)。

若負載平衡器沒有設定為平衡多部虛擬機器之間的流量，則所有計劃性維護事件都只會影響處理流量的虛擬機器，並導致應用程式層中斷。 將同一個層的多部虛擬機器放在相同的負載平衡器和可用性設定組下，可讓至少一個執行個體持續處理流量。

### 避免可用性設定組中只有一部執行個體虛擬機器
避免一個可用性設定組中只有一部執行個體虛擬機器。 此組態中的虛擬機器不符合 SLA 的保證，且會在 Azure 規劃的維護事件期間發生停機狀況。 請注意，可用性集合中的單一虛擬機器執行個體，在多重執行個體虛擬機器的計畫性維護通知中，也會收到進階的電子郵件通知。 

<!-- Link references -->
[Configure multiple virtual machines in an Availability Set for redundancy]: #configure-multiple-virtual-machines-in-an-availability-set-for-redundancy
[Configure each application tier into separate Availability Sets]: #configure-each-application-tier-into-separate-availability-sets
[Combine the Load Balancer with Availability Sets]: #combine-the-load-balancer-with-availability-sets
[Avoid single instance virtual machines in Availability Sets]: #avoid-single-instance-virtual-machines-in-availability-sets
[How to Configure An Availability Set for virtual machines]: virtual-machines-how-to-configure-availability.md

