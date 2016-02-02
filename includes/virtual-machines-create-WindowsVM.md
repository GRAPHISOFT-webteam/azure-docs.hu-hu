1. 登入 [Azure 入口網站](http://manage.windowsazure.com)。 簽出 [免費試用版](http://azure.microsoft.com/pricing/free-trial/) 提供如果您沒有訂閱。

2. 在視窗底部的命令列上，按一下 [新增]****。

3. 在 [計算]**** 下，依序按一下 [虛擬機器]**** 及 [從映像庫]****。

    ![瀏覽命令列中的來源資源庫](./media/virtual-machines-create-WindowsVM/fromgallery.png)

4. 之後的第一個畫面可讓您從可用的映像清單中，為您的虛擬機器 [**選擇映像**]。 (視您使用的訂用帳戶而定，可用的映像可能有所不同)。

5. 第二個畫面可讓您挑選電腦名稱、大小，及系統管理使用者名稱和密碼。 使用執行應用程式或工作負載所需的層次和大小。 以下是一些秘訣：

    - **新使用者名稱**是指用來管理伺服器的系統管理帳戶。 建立此帳戶的唯一密碼，並確定記住此密碼。 **您將需要使用者名稱和密碼才能登入虛擬機器**。

    - 虛擬機器的大小會影響其使用成本以及組態選項 (例如您可連接的資料磁碟數目)。 如需詳細資訊，請參閱 [虛擬機器的大小](../articles/virtual-machines-size-specs.md)。

6. 第三個畫面可讓您設定網路、儲存體和可用性的資源。 以下是一些祕訣：

    - [Cloud Service DNS Name]**** 是全域 DNS 名稱，它會成為用來連絡虛擬機器的 URI 一部分。 您將必須想出自己的雲端服務名稱，因為它在 Azure 中必須是唯一的。 雲端服務是很重要的使用案例 [多部虛擬機器](../articles/cloud-services-connect-virtual-machine.md)。

    - 在 [區域/同質群組/虛擬網路]**** 中，使用適合您位置的區域。 您也可以改選指定虛擬網路。
    >[AZURE.NOTE] 如果您想要讓虛擬機器使用虛擬網路，就「必須」****在建立虛擬機器時指定虛擬網路。 在建立 VM 後，您無法將虛擬機器加入虛擬網路。 如需詳細資訊，請參閱 [Azure 虛擬網路概觀](virtual-networks-overview.md)。
    >
    > 如需有關設定端點的詳細資訊，請參閱 [如何設定端點的虛擬機器](../articles/virtual-machines-set-up-endpoints.md)。

7. 第四個組態畫面可讓您安裝 VM 代理程式及設定部分可用延伸模組。
    >[AZURE.NOTE] VM 代理程式提供環境讓您安裝延伸模組，以協助您與虛擬機器互動或管理虛擬機器。 如需詳細資訊，請參閱 [有關 VM 代理程式和擴充功能](virtual-machines-extensions-agent-about.md)。  

8. 建立虛擬機器後，入口網站會在 [虛擬機器]**** 底下列出新的虛擬機器。 並建立對應的雲端服務和儲存體帳戶，且列於這些區段中。 虛擬機器和雲端服務都會自動啟動，而且它們的狀態會顯示為 [執行中]****。

    ![設定 VM 代理程式和需擬機器端點](./media/virtual-machines-create-WindowsVM/vmcreated.png)





