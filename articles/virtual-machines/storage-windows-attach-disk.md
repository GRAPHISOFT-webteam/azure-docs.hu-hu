<properties
    pageTitle="將磁碟附加至 VM | Microsoft Azure"
    description="將資料磁碟連接到以傳統部署模型建立的 Windows 虛擬機器，並予以初始化。"
    services="virtual-machines, storage"
    documentationCenter=""
    authors="cynthn"
    manager="timlt"
    editor="tysonn"
    tags="azure-service-management"/>

<tags
    ms.service="virtual-machines"
    ms.workload="infrastructure-services"
    ms.tgt_pltfrm="vm-windows"
    ms.devlang="na"
    ms.topic="article"
    ms.date="10/14/2015"
    ms.author="cynthn"/>

# 將資料磁碟連接至以傳統部署模型建立的 Windows 虛擬機器

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-classic-include.md)] [資源管理員模型](virtual-machines-attach-disk-preview.md)。

如果您需要其他資料磁碟，可將空磁碟或現有的資料磁碟連接到 VM。 在這兩種情況下，磁碟實際上是.vhd 檔案必須位於 Azure 儲存體帳戶。 如果是新的磁碟，則在連接磁碟之後，您也需要將它初始化，使其可供 Windows VM 使用。

最好使用一或多個不同的磁碟來儲存虛擬機器的資料。 當您建立 Azure 虛擬機器時，它有一個對應至 C 磁碟機的作業系統的磁碟和暫存磁碟對應至 d 磁碟機。 **請勿使用暫存磁碟來儲存資料**。 顧名思義，暫存磁碟只提供暫時儲存空間。 它並不提供備援或備份，因為它不在 Azure 儲存體內。

## 影片逐步解說

以下是本教學課程的逐步解說。

[AZURE.VIDEO attaching-a-data-disk-to-a-windows-vm]

[AZURE.INCLUDE [howto-attach-disk-windows-linux](../../includes/howto-attach-disk-windows-linux.md)]

## <a id="initializeinWS"></a>如何: 初始化新的資料磁碟在 Windows Server

1. 連接至虛擬機器。 如需指示，請參閱 [如何登入執行 Windows Server 的虛擬機器][logon]。

2. 登入虛擬機器之後，請開啟 **伺服器管理員**。 在左窗格中，選取 **檔案和存放服務**。

    ![開啟伺服器管理員](./media/storage-windows-attach-disk/fileandstorageservices.png)

3. 展開功能表，然後選取 **磁碟**。

4.  **磁碟** 區段會列出磁碟。 在大部分情況下，會有磁碟 0、磁碟 1 和磁碟 2。 磁碟 0 是作業系統磁碟、 磁碟 1 是暫存磁碟 (這不應該用來儲存資料)，和磁碟 2 是您剛連接至虛擬機器的資料磁碟。 新的資料磁碟會列出磁碟分割成 **未知**。 以滑鼠右鍵按一下磁碟，然後選取 **初始化**。

5.  初始化磁碟時，您會收到將清除所有資料的通知。 按一下 [ **是** 認可此警告並初始化磁碟。 完成後，分割會被列為 **GPT**。 磁碟上按一下滑鼠右鍵，然後選取 **新的磁碟區**。

6.  使用預設值完成精靈。 當精靈完成時， **磁碟區** 區段會列出新的磁碟區。 磁碟此時將上線，可用來儲存資料。

    ![成功初始化磁碟區](./media/storage-windows-attach-disk/newvolumecreated.png)

> [AZURE.NOTE] 虛擬機器的大小會決定您可以附加至它的磁碟數目。 如需詳細資訊，請參閱 [虛擬機器的大小](virtual-machines-size-specs.md)。

## 其他資源

[如何從 Windows 虛擬機器卸離磁碟](storage-windows-detach-disk.md)

[有關虛擬機器的磁碟和 VHD](virtual-machines-disks-vhds.md)

[logon]: virtual-machines-log-on-windows-server.md

