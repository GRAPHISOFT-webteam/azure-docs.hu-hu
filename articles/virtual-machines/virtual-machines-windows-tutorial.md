<properties
    pageTitle="在 Azure 入口網站中建立執行 Windows 的虛擬機器 | Microsoft Azure"
    description="了解如何使用 Azure 入口網站中的 Azure Marketplace，建立執行 Windows 的 Azure VM 或虛擬電腦"
    keywords="azure vm,create a virtual machine,virtual computer,setting up a virtual machine"
    services="virtual-machines"
    documentationCenter=""
    authors="cynthn"
    manager="timlt"
    editor=""
    tags="azure-resource-manager"/>
<tags
    ms.service="virtual-machines"
    ms.workload="infrastructure-services"
    ms.tgt_pltfrm="vm-windows"
    ms.devlang="na"
    ms.topic="hero-article"
    ms.date="08/14/2015"
    ms.author="cynthn"/>


# 在 Azure 入口網站中建立執行 Windows 的虛擬機器

> [AZURE.SELECTOR]
- [Azure Portal - Windows](virtual-machines-windows-tutorial.md)
- [Azure PowerShell](virtual-machines-ps-create-preconfigure-windows-resource-manager-vms.md)
- [Azure PowerShell - Template](virtual-machines-create-windows-powershell-resource-manager-template.md)
- [Azure Portal - Linux](virtual-machines-linux-tutorial-portal-rm.md)
- [Azure CLI](virtual-machines-linux-tutorial.md)


<br>



本教學課程示範在 Azure 入口網站中建立 Azure 虛擬機器有多麼容易，只需數分鐘。 我們將使用 Windows Server 2012 R2 資料中心映像做為範例來建立虛擬機器，但這只是 Azure 提供之眾多映像的其中一個。 您的映像選擇取決於訂用帳戶。 例如，桌面映像可能可供 MSDN 訂閱者使用。

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-rm-include.md)] 傳統部署模型。 如需資源管理員的相關詳細資訊，請參閱 [Azure 資源管理員概觀](resource-group-overview.md)。

您也可以使用自己的映像，搭配資源管理員範本或自動化工具來建立虛擬機器。 若要深入了解不同的方法，請參閱 [建立 Windows 虛擬機器的不同方式](virtual-machines-windows-choices-create-vm.md)。

[AZURE.INCLUDE [free-trial-note](../../includes/free-trial-note.md)]

## 影片逐步解說

以下是本教學課程的逐步解說。

[AZURE.VIDEO create-a-virtual-machine-running-windows-in-the-azure-preview-portal]

## 選取虛擬機器映像

1. 登入 Azure 入口網站。

2. 在 [中樞] 功能表上，依序按一下 [新增]**** > [計算]**** > [Windows Server 2012 R2 資料中心]****。

    ![顯示在 Preview 入口網站中可用 Azure VM 映像的螢幕擷取畫面](./media/virtual-machines-windows-tutorial/marketplace_new.png)
    >[AZURE.TIP] 若要尋找其他映像，請按一下 [Marketplace]****，然後搜尋或篩選可用的項目。

3. 在 [Windows Server 2012 R2 資料中心]**** 頁面中，選取 [選取部署模型]**** 下的 [資源管理員]****。 按一下 [建立]****。

    ![顯示可針對 Azure VM 選取的部署模型的螢幕擷取畫面](./media/virtual-machines-windows-tutorial/marketplace_search_select.png)

## 建立虛擬機器

選取映像之後，您可以針對多數組態使用 Azure 的預設設定，並快速建立虛擬機器。

1. 在 [建立虛擬機器]**** 刀鋒視窗中，按一下 [基本]****。 輸入您想給予虛擬機器的**名稱**、系統管理**使用者名稱**，以及強式**密碼**。 如果您有一個以上的訂用帳戶，請針對新的虛擬機器，以及新的或現有的**資源群組**和 Azure 資料中心**位置**，指定一個訂用帳戶。

    ![顯示可針對 Azure VM 選取的基本設定的螢幕擷取畫面](./media/virtual-machines-windows-tutorial/create_vm_basics.PNG)
    >[AZURE.NOTE]**使用者名稱** 是指您要用來管理伺服器的系統管理帳戶。 建立一個很難讓人猜到，但您可以記住的密碼。 **您將需要使用者名稱和密碼才能登入虛擬機器**。

2. 按一下 [大小]****，然後選取符合需求的適當虛擬機器大小。 每個大小會指定計算核心、記憶體和其他功能的數目，例如支援將會影響價格的進階儲存體。 Azure 會自動根據您選擇的映像來建議特定大小。

    ![顯示您可以選取的 Azure VM 大小的螢幕擷取畫面](./media/virtual-machines-windows-tutorial/create_vm_size.PNG)
    >[AZURE.NOTE] 進階儲存體可供某些區域的 DS 系列虛擬機器使用。 進階儲存體對於如資料庫這類資料密集的工作負載是最佳的儲存體選項。 如需詳細資訊，請參閱 [高階儲存體: Azure 虛擬機器工作負載的高效能儲存體](storage-premium-storage-preview-portal.md)。

3. 按一下 [設定]**** 以查看新虛擬機器的儲存體和網路設定。 若為第一個虛擬機器，通常您可以接受預設的設定。 如果您選取支援的虛擬機器大小，可以藉由選取 [磁碟類型]**** 下的 [進階 (SSD)]**** 嘗試進階儲存體。

    ![顯示可針對 Azure VM 設定的選用功能的螢幕擷取畫面](./media/virtual-machines-windows-tutorial/create_vm_settings.PNG)

6. 按一下 [摘要]****，以檢閱您的設定選擇。 當您檢閱或更新完設定時，請按一下 [建立]****。

    ![顯示針對 Azure VM 所做之設定選擇的摘要的螢幕擷取畫面](./media/virtual-machines-windows-tutorial/create_vm_summary.PNG)

8. 當 Azure 建立虛擬機器時，您可以在 [中樞] 功能表的 [通知]**** 中持續追蹤進度。 Azure 建立虛擬機器之後，除非您在 [建立虛擬機器]**** 刀鋒視窗中清除 [釘選到開始面板]****，否則您將會在 [開始面板] 上看到虛擬機器。

## 登入虛擬機器

建立虛擬機器之後，您就可以登入虛擬機器以管理其設定，以及將在虛擬機器上執行的應用程式。
>[AZURE.NOTE] 如需需求和疑難排解秘訣，請參閱 [連接到 Azure 虛擬機器透過 RDP 或 SSH](https://msdn.microsoft.com/library/azure/dn535788.aspx)。

1. 如果您尚未登入 Azure 入口網站，請先登入。

2. 在 [開始面板] 上按一下虛擬機器。 如果您需要尋找它，請按一下 [全部瀏覽]**** > [最近]****，或 [全部瀏覽]**** > [虛擬機器]****。 然後從清單中選取您的虛擬機器。

3. 在 [虛擬機器] 刀鋒視窗中，按一下 [連接]****。

    ![顯示您在 Azure VM 刀鋒視窗上找到 ](./media/virtual-machines-windows-tutorial/connect_vm_portal.png)

4. 按一下 [開啟]****，以使用系統自動為 Windows Server 虛擬機器建立的遠端桌面通訊協定檔案。

5. 按一下 [連接]****。

6. 輸入您建立虛擬機器時設定的使用者名稱和密碼，然後按一下 [確定]****。

7. 按一下 [是]**** 以驗證虛擬機器的身分識別。

    您現在可以開始使用虛擬機器，就如同操作任何其他伺服器一樣。

## 後續步驟

* 使用 Azure PowerShell 和 Azure CLI 以 [找出並選取虛擬機器映像](resource-groups-vm-searching.md)。
* 自動化虛擬機器和工作負載部署和管理使用 [Azure 資源管理員](virtual-machines-how-to-automate-azure-resource-manager.md) 和 [Azure 資源管理員範本](http://azure.microsoft.com/documentation/templates/)。





