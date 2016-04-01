<properties
   pageTitle="在 Visual Studio 中建立和管理 Azure 虛擬機器 | Microsoft Azure"
   description="在 Visual Studio 中建立和管理 Azure 虛擬機器"
   services="visual-studio-online"
   documentationCenter="na"
   authors="TomArcher"
   manager="douge"
   editor="" />
<tags
   ms.service="virtual-machines"
   ms.devlang="multiple"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="09/30/2015"
   ms.author="tarcher" />

# 在 Visual Studio 中建立和管理 Azure 虛擬機器

在 Visual Studio 中使用 [伺服器總管]，即可在 Azure 中建立虛擬機器。

## 在伺服器總管中建立 Azure 虛擬機器

雖然您可以建立虛擬機器中 [Azure 管理入口網站](http://go.microsoft.com/fwlink/?LinkID=253103), ，您也可以建立在 Azure 中虛擬機器，使用伺服器總管] 中的命令。 例如，虛擬機器可用於在一般負載平衡的公用端點背後提供一個前端。

### 建立新的虛擬機器

1. 在 [伺服器總管] 中，開啟 **Azure** 節點，然後按一下 **虛擬機器**。

1. 在內容功能表中，按一下 [ **建立虛擬機器**。

     **建立新的虛擬機器** 精靈] 隨即出現。

    ![建立虛擬機器命令](./media/vs-azure-tools-virtual-machines-create-manage/IC718342.png)

1. 在 **選擇訂閱** 頁面上，選取要建立虛擬機器時所使用的訂閱，然後按一下 **下一步**。

    如果您未登入 Azure，按一下 [ **登入** 登入。 接著，如果尚未選取 Azure 訂用帳戶，請在下拉式清單方塊中進行選取。

1. 在 **選取虛擬機器映像** 頁面上，選取中的映像類型 **映像類型** 下拉式清單方塊，然後選取 [中的虛擬機器映像 **映像名稱** 清單方塊。 當您完成時，按一下 [ **下一步**。

    ![選取虛擬機器映像頁面](./media/vs-azure-tools-virtual-machines-create-manage/IC744137.png)

    您可選擇下列映像類型。

    - **公用的映像** 列出的作業系統和伺服器軟體，例如 Windows Server 和 SQL Server 的虛擬機器映像。

    - **MSDN 映像** 列出可用 MSDN 訂閱者，例如 Visual Studio 和 Microsoft Dynamics 軟體的虛擬機器映像。

    - **私人映像** 清單的特殊化和一般化您已建立的虛擬機器映像。

    若要深入了解特製化和一般化的虛擬機器，請參閱 [VM 映像](http://azure.microsoft.com/blog/2014/04/14/vm-image-blog-post/)。 請參閱 [如何擷取 Windows 虛擬機器作為範本使用](http://azure.microsoft.com/documentation/articles/virtual-machines-capture-image-windows-server/) 的預先設定的虛擬機器變成範本可讓您快速建立新的虛擬機器的相關資訊。

    按一下虛擬機器映像名稱，即可查看頁面右側的映像相關資訊。

    >[AZURE.NOTE] 您無法新增虛擬機器映像， **公用映像** 或 **MSDN 映像** 列出，因為它們是唯讀。 您所建立的所有虛擬機器會都新增至 **私人映像** 清單。

    >如果您是具有 Visual Studio 層級訂用帳戶的 MSDN 訂閱者，您可以建立一個預先建置的 Azure 虛擬機器，其中包含 Visual Studio 以及其他數個映像。 如需詳細資訊，請參閱 [Visual Studio 中建立虛擬機器所使用的映像 Visual Studio 2013 資源庫映像的 MSDN 訂閱者](http://visualstudio2013msdngalleryimage.azurewebsites.net) 和 [MSDN 訂閱](https://www.visualstudio.com/products/msdn-subscriptions-vs)。 |

1. 在 **虛擬機器基本設定** 頁面上，輸入電腦名稱，然後新增 [虛擬機器大小，以及使用者名稱和密碼的規格。 當您完成時，按一下 [ **下一步**。

    您將透過遠端桌面使用新名稱和密碼來登入電腦，所以最好能記新名稱和密碼，以免忘記。 Visual Studio 中建立 Azure 虛擬機器之後，您可以變更其大小和中的其他設定 [Azure 管理入口網站](http://go.microsoft.com/fwlink/?LinkID=253103)。

        >[AZURE.NOTE] 如果您選擇較大的虛擬機器的大小，可套用額外的費用。 請參閱 [虛擬機器定價詳細資料](http://azure.microsoft.com/pricing/details/virtual-machines/) 如需詳細資訊。

1. 在 Visual Studio 中建立的虛擬機器需要雲端服務。 在 **雲端服務設定** 頁面上，選取雲端服務的虛擬機器，或按一下 **< 建立新的 … >** 如果您還沒有雲端服務，或想要使用新的下拉式清單中。 儲存體帳戶也是必要，因此選擇儲存體帳戶 （或建立新的儲存體帳戶） 中 **儲存體帳戶** 下拉式清單方塊。 請參閱 [Microsoft Azure 儲存體簡介](./storage/storage-introduction/) 如需詳細資訊。

1. 如果您想要指定虛擬網路 (這是選擇性的)，請在 [虛擬網路] 和 [子網路] 下拉式清單方塊中進行選取。

    屬於可用性集合的虛擬機器會部署到不同的容錯網域。 請參閱 [Azure 虛擬網路](http://www.windowsazure.com/services/virtual-network/) 如需詳細資訊。

1. 如果您想讓虛擬機器屬於可用性設定組 （也是選擇性的），選取 **指定可用性設定組** 核取方塊，然後選擇 [可用性設定組中的下拉式清單方塊。 當您完成時，選擇 [ **下一步** ] 按鈕。

    將虛擬機器新增至可用性集合，有助於讓應用程式在網路故障、本機磁碟硬體故障和任何規劃停機期間仍可使用。 您必須使用 [Azure 管理入口網站](http://go.microsoft.com/fwlink/?LinkID=253103) 建立虛擬網路、 子網路和可用性集。 請參閱 [管理虛擬機器的可用性](http://www.windowsazure.com/documentation/articles/manage-availability-virtual-machines/) 如需詳細資訊。

1. 在 **端點** 頁面上，指定您想要提供給您的虛擬機器的使用者公開端點。 例如，除了預設啟用的遠端桌面和 PowerShell 端點以外，您可以選擇啟用 HTTP (連接埠 80)。 若要加入端點，請在 **連接埠名稱** 下拉式清單方塊，然後選擇 **新增** ] 按鈕。 若要移除端點，請選擇紅色 **X** 端點清單中的名稱旁邊。

    ![虛擬機器精靈中的 [端點] 頁面。](./media/vs-azure-tools-virtual-machines-create-manage/IC718351.png)

    可用的端點取決於您為虛擬機器選取的雲端服務。 請參閱 [Azure 服務端點](https://azure.microsoft.com/documentation/articles/virtual-machines-set-up-endpoints/) 如需詳細資訊。

    >[AZURE.NOTE] 啟用公用端點讓服務在您的虛擬機器上使用網際網路。 請務必在您的虛擬機器上安裝及正確設定端點和服務，例如設定端點的存取控制清單 (ACL)。 請參閱 [如何設定端點的虛擬機器](https://azure.microsoft.com/documentation/articles/virtual-machines-set-up-endpoints/) 如需詳細資訊。

1. 完成後設定虛擬機器設定時，選擇 [ **建立** ] 按鈕以建立虛擬機器。

    當 Azure 建立虛擬機器， **Azure 活動記錄檔** 顯示虛擬機器建立作業的進度。

    ![虛擬機器活動記錄 - 進行中。](./media/vs-azure-tools-virtual-machines-create-manage/IC744138.png)

    若要檢視僅虛擬機器資訊，請選擇 [ **虛擬機器** 索引標籤中 **Azure 活動記錄檔**。

    ![虛擬機器活動記錄 - 已完成。](./media/vs-azure-tools-virtual-machines-create-manage/IC744139.png)

    如果作業成功完成時，新的虛擬機器會出現在 **虛擬機器** 伺服器總管] 中的節點。 您可以按一下 [登入 **使用遠端桌面連線** 捷徑。

    ![出現在 [伺服器總管] 中的虛擬機器。](./media/vs-azure-tools-virtual-machines-create-manage/IC744140.png)

## 管理虛擬機器

在虛擬機器組態頁面上，除了關閉、連接、重新整理檢查點以及將其新增至選取的虛擬機器以外，您也可以檢視或變更虛擬機器的設定。 您可以：

- 變更虛擬機器大小。

- 選取要用於虛擬機器的可用性集合。

- 新增、移除或變更公用端點的設定。

- 新增、移除或設定虛擬機器擴充功能。

- 檢視與虛擬機器相關聯的磁碟資訊。

### 檢視或變更虛擬機器設定

1. 在 [伺服器總管] 中，選擇您的虛擬機器中 **Azure 虛擬機器** 節點。

1. 在捷徑功能表上選擇 [ **設定** 若要檢視虛擬機器組態頁面。

    ![Azure 虛擬機器組態頁面](./media/vs-azure-tools-virtual-machines-create-manage/IC744141.png)

1. 檢視虛擬機器資訊或加以變更。

### 儲存或還原虛擬機器的狀態

當您設定虛擬機器並在其上安裝軟體時，最好能建立虛擬機器檢查點，以便定期儲存您的進度。 檢查點是虛擬機器的目前狀態快照或映像。 如果虛擬機器發生問題，或您想重新設定虛擬機器，您可將它還原到先前的檢查點狀態，而非重頭開始，以節省時間。

### 建立虛擬機器檢查點

1. 在 [伺服器總管] 中，選擇您的虛擬機器中 **Azure 虛擬機器** 節點。

1. 在捷徑功能表上選擇 [ **設定** 若要檢視虛擬機器組態頁面。

1. 在 [設定] 頁面上選擇 [ **擷取映像** ] 按鈕。

    ![Azure 組態頁面擷取按鈕](./media/vs-azure-tools-virtual-machines-create-manage/IC744142.png)

     **擷取虛擬機器** ] 對話方塊隨即出現。

    ![Azure 擷取虛擬機器對話方塊](./media/vs-azure-tools-virtual-machines-create-manage/IC744143.png)

1. 提供映像標籤和描述。 系統會提供預設標籤和描述，但您可以用自己的標籤和描述加以覆寫。

1. 如果您已經有這部虛擬機器上執行 Sysprep，選取 **我已經在虛擬機器上執行 Sysprep** 方塊。

    Sysprep 是一個工具，可從虛擬機器的 Windows 版本中移除系統特定資料，讓其成為其他人可使用的範本。 請參閱 [如何擷取 Windows 虛擬機器作為範本使用](http://azure.microsoft.com/documentation/articles/virtual-machines-capture-image-windows-server/) 如需詳細資訊。

1. 完成後設定擷取設定時，選擇 [ **擷取** ] 按鈕以建立檢查點。

    當 Azure 建立檢查點， **Azure 活動記錄檔** 顯示作業的進度。

    ![擷取虛擬機器檢查點](./media/vs-azure-tools-virtual-machines-create-manage/IC744144.png)

    當檢查點作業完成時，您會看到它在 **Azure 活動記錄檔**。

    ![已完成檢查點作業](./media/vs-azure-tools-virtual-machines-create-manage/IC744145.png)

## 管理虛擬機器檢查點

### 若要將虛擬機器還原到先前儲存的狀態

- 請依照下列所述的步驟 [逐步 ︰ 執行定域機組還原 Microsoft Azure 虛擬機器的使用 PowerShell-第 2 部分](http://blogs.technet.com/b/keithmayer/archive/2014/02/04/step-by-step-perform-cloud-restores-of-windows-azure-virtual-machines-using-powershell-part-2.aspx)。

### 刪除檢查點

1. 移至 [Azure 管理入口網站](http://go.microsoft.com/fwlink/?LinkID=253103)。

1. 在虛擬機器組態] 頁面上，選擇 [ **映像** 在頁面頂端的索引標籤。

1. 選擇您想要刪除，然後選擇 [檢查的點 **刪除**  在頁面底部的按鈕。

## 關閉虛擬機器

1. 在 [伺服器總管] 中，選擇您想要在關閉虛擬機器 **Azure 虛擬機器** 節點。

1. 在捷徑功能表上，選擇 **關機** 命令，或選擇 **設定** 以檢視虛擬機器組態] 頁面上，然後選擇 [ **關機** ] 按鈕。

## 後續步驟

若要深入了解建立虛擬機器，請參閱 [建立執行 Linux 的虛擬機器](virtual-machines-linux-tutorial.md) 和 [建立在 Azure 預覽入口網站中執行 Windows 的虛擬機器](virtual-machines-windows-tutorial.md)。


