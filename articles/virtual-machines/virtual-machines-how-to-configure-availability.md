<properties
    pageTitle="設定 VM 的可用性設定組 | Microsoft Azure"
    description="使用 Azure 傳統入口網站和 Azure PowerShell，以傳統部署模型設定新的或現有虛擬機器的可用性設定組。"
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
    ms.date="08/10/2015"
    ms.author="cynthn"/>

# 如何以傳統部署模型設定虛擬機器的可用性設定組

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-classic-include.md)] 資源管理員模型。


可用性設定組可協助您的虛擬機器在停機期間 (例如維護期間) 仍然保持可用狀態。 在可用性設定組中置入二或多個類似設定的虛擬機器，將可針對虛擬機器所執行的應用程式或服務，建立維護其可用性所需的備援。 如需其運作方式的詳細資訊，請參閱 [管理虛擬機器的可用性] []。

同時使用可用性設定組和負載平衡端點，是協助確保您的應用程式一直可用並有效率執行的最佳做法。 如需負載平衡端點的詳細資訊，請參閱 [Azure 基礎結構服務的負載平衡] []。

您可以使用下列兩個選項的其中之一，將虛擬機器放入可用性集合：

- [選項 1: 建立虛擬機器和可用性設定一次][]. 然後，將新的虛擬機器加入您在建立這些虛擬機器時的設定組。
- [選項 2: 將現有的虛擬機器加入至可用性設定組][].

>[AZURE.NOTE] 您想要放入相同可用性設定組的虛擬機器必須屬於相同的雲端服務。

## <a id="createset"> </a>選項 1: 建立虛擬機器和可用性設定組，在相同的時間##

您可以使用 Azure 傳統入口網站或 Azure PowerShell 命令來執行此作業。

若要使用 Azure 傳統入口網站：

1. 如果您未曾執行過這項操作，請登入 Azure 傳統入口網站。

2. 在命令列中，按一下 [ **新增**。

3. 按一下 [ **虛擬機器**, ，然後按一下 [ **從組件庫**。

4. 使用前兩個畫面來選取映像、使用者名稱和密碼等。 如需詳細資訊，請參閱 [建立執行 Windows 的虛擬機器][]。

5. 在第三個畫面中，您可以設定網路、儲存體和可用性的資源。 執行下列動作：

    1. 選擇適當的雲端服務。 保留 **建立新的雲端服務** (除非您打算將此新的虛擬機器加入現有的虛擬機器雲端服務)。 然後，在 **Cloud Service DNS Name**, ，輸入的名稱。 此 DNS 名稱便會成為 URI (用來連絡虛擬機器) 的一部分。 雲端服務會以通訊和獨立群組的方式運作。 相同雲端服務中的所有虛擬機器可以彼此相互通訊、可以設定使用負載平衡，以及放置在相同的可用性設定組中。

    2. 在 **區域/同質群組/虛擬網路**, ，如果您打算使用其中一個指定的虛擬網路。 **重要**: 如果您想要將虛擬網路的虛擬機器時，您必須將虛擬機器加入虛擬網路建立虛擬機器時。 在建立虛擬機器後，您無法將虛擬機器加入至虛擬網路。 如需詳細資訊，請參閱 [虛擬網路概觀][]。

    3. 建立可用性設定組。 在 **可用性設定組**, ，保留 **建立可用性設定組**。 接著，輸入此設定組的名稱。

    4. 建立預設端點，並視需要新增更多端點。 您也可以稍後再新增端點。

    ![建立新虛擬機器的可用性集合](./media/virtual-machines-how-to-configure-availability/VMavailabilityset.png)

6. 在第四個畫面上，按一下您要安裝的擴充功能。 擴充程式提供可輕鬆管理虛擬機器的功能，例如執行反惡意程式碼或重設密碼。 如需詳細資訊，請參閱 [Azure VM 代理程式與 VM 擴充程式](virtual-machines-extensions-agent-about.md)。

7.  按一下箭號以建立虛擬機器和可用性設定組。

    從新的虛擬機器的儀表板，您可以按一下 **設定** 並查看虛擬機器是否隸屬於新的可用性設定組。

若要使用 Azure PowerShell 命令建立 Azure 虛擬機器，並將它加入至新的或現有的可用性集合，請參閱下列內容：

- [使用 Azure PowerShell 建立和預先設定以 Windows 為基礎的虛擬機器](virtual-machines-ps-create-preconfigure-windows-vms.md)
- [使用 Azure PowerShell 建立和預先設定以 Linux 為基礎的虛擬機器](virtual-machines-ps-create-preconfigure-linux-vms.md)

## <a id="addmachine"> </a>選項 2: 將現有的虛擬機器加入至可用性設定組##

在 Azure 傳統入口網站，您可以將現有的虛擬機器加入現有的可用性設定組
 或建立新的。 (請注意，相同可用性設定組中的虛擬機器必須隸屬於相同的雲端服務。)步驟幾乎完全相同。 有了 Azure PowerShell，您可以將虛擬機器加入現有的可用性設定組。

1. 如果您未曾執行過這項操作，請登入 Azure 傳統入口網站。

2. 在命令列中，按一下 [ **虛擬機器**。

3. 從虛擬機器的清單中，選取您想要加入至集合的虛擬機器名稱。

4. 從虛擬機器名稱下方的索引標籤，按一下 [ **設定**。

5. 在 [設定] 區段中，找出 **可用性設定組**。 執行下列其中一項：

    A. 選取 **建立可用性設定組**, ，然後輸入集的名稱。

    B. 選取 **選取可用性設定組**, ，然後從清單選取一組。

    ![建立現有虛擬機器的可用性集合](./media/virtual-machines-how-to-configure-availability/VMavailabilityExistingVM.png)

6. 按一下 [ **儲存**。

若要使用 Azure PowerShell 命令，請開啟系統管理員層級的 Azure PowerShell 工作階段並執行下列命令。 在預留位置 (例如 & lt;VmCloudServiceName & gt;)，取代引號中，包括裡面 < 和 > 字元，以正確的名稱。

    Get-AzureVM -ServiceName "<VmCloudServiceName>" -Name "<VmName>" | Set-AzureAvailabilitySet -AvailabilitySetName "<AvSetName>" | Update-AzureVM

>[AZURE.NOTE] 虛擬機器可能需要重新啟動以完成將它加入至可用性設定組。

## 其他資源

[服務管理中針對虛擬機器的文章]

<!-- LINKS -->
[Option 1: Create a virtual machine and an availability set at the same time]: #createset
[Option 2: Add an existing virtual machine to an availability set]: #addmachine

[Load balancing for Azure infrastructure services]: virtual-machines-load-balance.md
[Manage the availability of virtual machines]: virtual-machines-manage-availability.md
[Create a virtual machine running Windows]: virtual-machines-windows-tutorial.md
[Virtual Network overview]: virtual-networks-overview.md
[Articles for virtual machines in Service Management]: virtual-machines-service-management-articles.md

