<properties 
   pageTitle="管理虛擬網路 (VNet) 所使用的 DNS 伺服器"
   description="了解如何新增和移除虛擬網路 (VNet) 中的 DNS 伺服器"
   services="virtual-network"
   documentationCenter="na"
   authors="telmosampaio"
   manager="carmonm"
   editor="tysonn" />
<tags 
   ms.service="virtual-network"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="12/11/2015"
   ms.author="telmos" />

# 管理虛擬網路 (VNet) 所使用的 DNS 伺服器

您可以在管理入口網站或網路組態檔中，管理用於 VNet 的 DNS 伺服器清單。 您可以為每個 VNet 新增最多 12 部 DNS 伺服器。 指定 DNS 伺服器時，請務必確認您針對環境以正確的順序列出您的 DNS 伺服器。 DNS 伺服器清單不會使用循環配置資源， 而會依其指定的順序來使用。 如果可以連接至清單上的第一部 DNS 伺服器，則用戶端將使用該 DNS 伺服器，無論 DNS 伺服器是否運作正常。 若要變更虛擬網路的 DNS 伺服器順序，請從清單中移除 DNS 伺服器，然後以您想要的順序將其重新加入。

>[AZURE.WARNING] 更新 DNS 清單之後，您必須重新啟動位於虛擬網路，使其挑選新的 DNS 伺服器設定的虛擬機器。 虛擬機器將繼續使用其目前的組態，直到重新啟動為止。

## 使用管理入口網站編輯虛擬網路的 DNS 伺服器清單

1. 登入 **管理入口網站**。

1. 在導覽窗格中，按一下 **網路**, ，然後按一下 [虛擬網路中的名稱 **名稱** 資料行。

1. 按一下 [ **設定**。

1. 在 **DNS 伺服器**, ，您可以設定下列各項:

    - **若要登錄 (add) 新的 DNS 伺服器 –** 只要的方塊中輸入名稱和 IP 位址。 這會將 DNS 伺服器新增至虛擬網路 DNS 伺服器清單，並且也會向 Azure 登錄 DNS 伺服器。

    - **若要新增先前登錄 – DNS 伺服器** 如果您已經向 Azure 登錄 DNS 伺服器，可以選取從預先填入的清單。

    - **若要移除 DNS 伺服器從虛擬網路 –** 按一下您想要移除的伺服器旁的 [X]。 請注意，這僅會從此虛擬網路清單移除伺服器。 DNS 伺服器會在 Azure 中保持登錄狀態，以便您其他的虛擬網路使用。 若要在訂閱中刪除 DNS 伺服器，請移至 **網路]-> [DNS 伺服器** 頁面。

    - **若要重新排序 DNS 伺服器 –** 移除所有的 DNS 伺服器所列，然後將它們重新加入您想要的順序。 請記住，這不是循環配置資源 DNS 清單。

    - **若要重新命名 DNS 伺服器 –** 反白顯示在清單中，DNS 伺服器，然後輸入新名稱。 這會在 Azure 中登錄新的 DNS 伺服器，以及將其新增至您虛擬網路的 DNS 伺服器清單。 舊的 DNS 伺服器及其 IP 位址會在 Azure 保持登錄狀態。 您可以刪除上 **DNS 伺服器** 頁面上，如果您不會將其用於任何其他虛擬網路。

1. 按一下 [ **儲存** 要儲存新的 DNS 伺服器組態頁面的底部。

1. 重新啟動位於虛擬網路的虛擬機器，以便取得新的 DNS 設定。

## 編輯使用網路組態檔的 DNS 伺服器清單

若要使用網路組態檔來編輯 DNS 伺服器清單，您需要先從管理入口網站匯出您的組態設定。 接下來，您需要編輯網路組態檔，並透過管理入口網站將它匯入回來。 下方是完成此程序的高階步驟清單。

1. 將您的虛擬網路設定匯出至網路組態檔。 如需詳細資訊，若要匯出網路組態設定，請參閱 [虛擬網路設定匯出至網路組態檔](virtual-networks-using-network-configuration-file.md)。

1. 指定您虛擬網路的 DNS 伺服器資訊。 如需指定 DNS 伺服器的詳細資訊，請參閱 [虛擬網路組態檔中指定 DNS 伺服器](virtual-networks-specifying-a-dns-settings-in-a-virtual-network-configuration-file.md)。 如需有關網路組態檔的詳細資訊，請參閱 [Azure 虛擬網路組態結構描述](https://msdn.microsoft.com/library/azure/jj157100.aspx) 和 [虛擬網路使用網路組態檔設定](virtual-networks-using-network-configuration-file.md)。

1. 匯入網路組態檔。 如需詳細資訊，以匯入網路組態檔，請參閱 [匯入網路組態檔](virtual-networks-using-network-configuration-file.md)。

1. 重新啟動位於虛擬網路的虛擬機器，以便取得新的 DNS 設定。

## 後續步驟

[如何管理虛擬網路 (VNet) 屬性](../virtual-networks-settings)

[如何在虛擬網路中使用公用 IP 位址](../virtual-networks-public-ip-within-vnet)

[如何刪除虛擬網路 (VNet)](../virtual-networks-delete-vnet) 

