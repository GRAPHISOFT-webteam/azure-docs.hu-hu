<properties
    pageTitle="在 Azure 中建立執行 Windows 的虛擬機器"
    description="在 Azure 傳統入口網站中建立 Windows 虛擬機器。"
    services="virtual-machines"
    documentationCenter=""
    authors="cynthn"
    manager="timlt"
    editor=""
    tags="azure-service-management"/>

<tags
    ms.service="virtual-machines"
    ms.workload="infrastructure-services"
    ms.tgt_pltfrm="vm-windows"
    ms.devlang="na"
    ms.topic="article"
    ms.date="08/11/2015"
    ms.author="cynthn"/>

# 在 Azure 傳統入口網站中建立執行 Windows 的虛擬機器

> [AZURE.SELECTOR]
- [Azure 入口網站](virtual-machines-windows-tutorial.md)
- [Azure 傳統入口網站](virtual-machines-windows-tutorial-classic-portal.md)
- [PowerShell: 資源管理員部署](virtual-machines-deploy-rmtemplates-powershell.md)
- [PowerShell: 傳統部署](virtual-machines-ps-create-preconfigure-windows-vms.md)

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-classic-include.md)] [資源管理員部署模型](virtual-machines-windows-tutorial.md)。

本教學課程示範在 Azure 傳統入口網站中建立 Azure 虛擬機器 (VM) 有多麼容易。 我們將使用 Windows Server 映像做為範例，這只是 Azure 提供眾多映像中的一種。 請注意，您可以選擇何種映像取決於您的訂用帳戶。 例如，桌面映像可能可供 MSDN 訂閱者使用。

您也可以建立使用 Vm [自己的映像](virtual-machines-create-upload-vhd-windows-server.md)。 若要深入了解其他方法，請參閱 [建立 Windows 虛擬機器的不同方式](virtual-machines-windows-choices-create-vm.md)。

[AZURE.INCLUDE [free-trial-note](../../includes/free-trial-note.md)]

## 影片逐步解說

以下是本教學課程的逐步解說。

[AZURE.VIDEO creating-a-windows-vm-on-microsoft-azure-classic-portal]

## <a id="createvirtualmachine"> </a>如何建立虛擬機器

本節說明如何使用 **從組件庫** Azure 傳統入口網站中的建立虛擬機器] 選項。 此選項提供更多的組態選擇比 **快速建立** 選項。 例如，如果您想要將虛擬機器加入虛擬網路，您需要使用 **從組件庫** 選項。

> [AZURE.NOTE] 您也可以嘗試建立虛擬機器、 使用增強的監視和診斷，請使用 Premium 儲存體，以及其他更豐富、 可自訂 Azure 入口網站。 兩個入口網站中用來設定虛擬機器的可用選項有許多重疊之處，但並不完全相同。 例如，使用入口網站設定包含進階儲存體的虛擬機器。

[AZURE.INCLUDE [virtual-machines-create-WindowsVM](../../includes/virtual-machines-create-windowsvm.md)]

## 後續步驟

- 登入虛擬機器。 如需指示，請參閱 [如何登入執行 Windows Server 的虛擬機器](virtual-machines-log-on-windows-server.md)。

- 附加磁碟來儲存資料。 您可以附加空的磁碟和含有資料的磁碟。 如需指示，請參閱 [附加資料磁碟教學課程](storage-windows-attach-disk.md)。

