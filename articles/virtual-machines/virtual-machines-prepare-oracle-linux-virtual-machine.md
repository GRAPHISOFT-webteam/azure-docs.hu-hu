<properties 
pageTitle="準備 Azure 的 Oracle Linux 虛擬機器 | Microsoft Azure" 
description="逐步完成在 Microsoft Azure 中執行 Linux 的 Oracle 虛擬機器設定。" 
services="virtual-machines" 
authors="bbenz" 
documentationCenter="virtual-machines"
tags="azure-service-management,azure-resource-manager"
/>

<tags 
ms.service="virtual-machines" 
ms.devlang="na" 
ms.topic="article" 
ms.tgt_pltfrm="vm-linux" 
ms.workload="infrastructure-services" 
ms.date="06/22/2015" 
ms.author="bbenz" />

#準備用於 Azure 的 Oracle Linux 虛擬機器

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-both-include.md)]


-   [準備用於 Azure 的 Oracle Linux 6.4+ 虛擬機器](virtual-machines-linux-create-upload-vhd-oracle.md)

-   [準備用於 Azure 的 Oracle Linux 7.0+ 虛擬機器](virtual-machines-linux-create-upload-vhd-oracle.md)

##必要條件
本文假設您已將 Oracle Linux 作業系統安裝到虛擬硬碟。 有多個工具可用來建立 .vhd 檔案，例如，像是 Hyper-V 的虛擬化解決方案。 如需指示，請參閱 [安裝 HYPER-V，並建立虛擬機器](http://technet.microsoft.com/library/hh846766.aspx)。

**Oracle Linux 安裝注意事項**

- Hyper-V 和 Azure 都支援 Oracle 的 Red Hat 相容核心及其 UEK3 (Unbreakable Enterprise Kernel)。 若要獲得最佳結果，請在準備執行 Oracle Linux VHD 的同時，確實更新到最新核心。

- Hyper-V 和 Azure 不支援 Oracle 的 UEK2，因為它不包含必要的驅動程式。

- Azure 不支援較新的 VHDX 格式。 您可以使用 Hyper-V 管理員或 Convert-VHD Cmdlet，將磁碟轉換為 VHD 格式。

- 安裝 Linux 系統時，我們建議您使用標準磁碟分割而不是 LVM (常是許多安裝的預設設定)。 這可避免 LVM 與複製之虛擬機器的名稱衝突，特別是為了疑難排解而需要將作業系統磁碟連接至其他虛擬機器時。 LVM 或 [RAID](virtual-machines-linux-configure-raid.md) 可能會在資料磁碟上使用，如果願意的話。

- 由於 2.6.37 以下的 Linux 核心版本有錯誤，因此較大的 VM 不支援 NUMA。 這個問題主要會影響使用上游 Red Hat 2.6.32 Kernel 的散發套件。 手動安裝 Azure Linux 代理程式 (waagent) 將會自動停用 Linux Kernel GRUB 組態中的 NUMA。 您可以在以下步驟中找到與此有關的詳細資訊。

- 請勿在作業系統磁碟上設定交換磁碟分割。 您可以設定 Linux 代理程式在暫存資源磁碟上建立交換檔。 您可以在以下步驟中找到與此有關的詳細資訊。

- 所有 VHD 的大小都必須是 1 MB 的倍數。

##Oracle Linux 6.4+
您必須在作業系統中完成特定組態步驟，虛擬機器才能在 Azure 中執行。

1. 在 Hyper-V 管理員的中央窗格中，選取虛擬機器。

2. 按一下 [ **連接** 開啟虛擬機器的視窗。

3. 執行下列命令以解除安裝 NetworkManager：

        # sudo rpm -e --nodeps NetworkManager

    >[AZURE.NOTE]如果尚未安裝封裝，此命令將會失敗，並出現錯誤訊息。 這是預期行為。

4. 建立名為 **網路** 在 /etc/sysconfig/ 目錄，其中包含下列文字:

    `NETWORKING=yes`  
    `HOSTNAME=localhost.localdomain`

5.  建立名為 **ifcfg-eth0** 在 /etc/sysconfig/network-scripts/ / 目錄，其中包含下列文字:

        DEVICE=eth0
        ONBOOT=yes
        BOOTPROTO=dhcp
            TYPE=Ethernet
            USERCTL=no
            PEERDNS=yes
        IPV6INIT=no

6.  移動 (或移除) udev 角色可防止產生乙太網路介面的靜態規則。 在 Azure 或 Hyper-V 中複製虛擬機器時，這些規則會造成問題：

        # sudo mkdir -m 0700 /var/lib/waagent
        # sudo mv /lib/udev/rules.d/75-persistent-net-generator.rules /var/lib/waagent/ 2\>/dev/null
        # sudo mv /etc/udev/rules.d/70-persistent-net.rules /var/lib/waagent/ 2\>/dev/null

7.  若要確保開機時會啟動網路服務，可執行下列命令：

        # chkconfig network on

8.  執行下列命令以安裝 python-pyasn1：

        # sudo yum install python-pyasn1

9.  修改 grub 組態中的核心開機那一行，使其額外包含用於 Azure 的核心參數。 作法是，在文字編輯器中開啟 "/boot/grub/menu.lst"，並確定預設核心包含下列參數：

        console=ttyS0 earlyprintk=ttyS0 rootdelay=300 numa=off

    這也將確保所有主控台訊息都會傳送給第一個序列埠，以協助 Azure 支援團隊進行問題偵錯程序。 因為 Oracle Red Hat 相容核心的一個錯誤，這將會停用 NUMA。

    除了上述說明，我們建議您 *移除* 下列參數:

        rhgb quiet crashkernel=auto

    在雲端環境中，我們會將所有記錄傳送到序列埠，因此不適合使用圖形化和無訊息啟動。

    如有需要，您可以保留 `crashkernel` 選項的設定。但請注意，此參數將會減少 VM 中約 128 MB 或以上的可用記憶體數量。 這在較小的 VM 中可能會造成問題。

10.  確定您已安裝 SSH 伺服器，並已設定為在開機時啟動。 這通常是預設值。

11.  執行以下命令來安裝 Azure Linux 代理程式：

        # sudo yum 安裝 WALinuxAgent

    Note that installing the WALinuxAgent package will remove the NetworkManager and NetworkManager-gnome packages if they were not already removed as described in step 2.

12.  請勿在作業系統磁碟上建立交換空間。

    The Azure Linux Agent can automatically configure swap space by using the local resource disk that is attached to the VM after provisioning on Azure. Note that the local resource disk is a *temporary* disk, and it might be emptied when the VM is deprovisioned. After you install the Azure Linux Agent (see previous step), modify the following parameters in /etc/waagent.conf appropriately:

        ResourceDisk.Format=y

        ResourceDisk.Filesystem=ext4

        ResourceDisk.MountPoint=/mnt/resource

        ResourceDisk.EnableSwap=y

        ResourceDisk.SwapSizeMB=2048 ## NOTE: set this to whatever you need it to be.

13.  執行下列命令，以取消佈建虛擬機器，並準備將其佈建於 Azure 上：

        # sudo waagent-force-取消佈建
        # export HISTSIZE=0
        # logout

14.  按一下 [ **動作-\ > 關閉** HYPER-V 管理員中。 您現在可以將 Linux VHD 上傳至 Azure。

##Oracle Linux 7.0+
**Oracle Linux 7 中的變更**

準備用於 Azure 的 Oracle Linux 7 虛擬機器的程序相當類似於 Oracle Linux 6 的程序。 不過，有幾個重要的差異值得一提：

-   Azure 支援 Red Hat 相容核心和 Oracle 的 UEK3。 我們建議使用 UEK3 核心。

-   NetworkManager 封裝不會再與 Azure Linux 代理程式發生衝突。 依預設會安裝此封裝，建議您不要將它移除。

-   GRUB2 現已作為預設的開機載入器使用，因此我們已變更編輯核心參數的程序 (如下所示)。

-   XFS 現為預設的檔案系統。 如有需要，您仍可使用 ext4 檔案系統。

**組態步驟**

1.  在 Hyper-V 管理員中，選取虛擬機器。

2.  按一下 [ **連接** 開啟虛擬機器的主控台視窗。

3.  建立名為 **網路** 在 /etc/sysconfig/ 目錄，其中包含下列文字:

        NETWORKING=yes
        HOSTNAME=localhost.localdomain

4.  建立名為 **ifcfg-eth0** 在 /etc/sysconfig/network-scripts/ / 目錄，其中包含下列文字:

        DEVICE=eth0
        ONBOOT=yes
        BOOTPROTO=dhcp
        TYPE=Ethernet
        USERCTL=no
            PEERDNS=yes
        IPV6INIT=no

5.  移動 (或移除) udev 角色可防止產生乙太網路介面的靜態規則。 在 Microsoft Azure 或 Hyper-V 中複製虛擬機器時，這些規則會造成問題。

        # sudo mkdir -m 0700 /var/lib/waagent
        # sudo mv /lib/udev/rules.d/75-persistent-net-generator.rules /var/lib/waagent/ 2>/dev/null
        # sudo mv /etc/udev/rules.d/70-persistent-net.rules /var/lib/waagent/ 2>/dev/null

6.  若要確保開機時會啟動網路服務，可執行下列命令：

        # sudo chkconfig network on

7.  執行下列命令以安裝 python-pyasn1 封裝：

        # sudo yum install python-pyasn1

8.  執行下列命令，以清除目前的 yum 中繼資料並安裝任何更新：

        # sudo yum clean all
        # sudo yum -y update

9.  修改 grub 組態中的核心開機那一行，使其額外包含用於 Azure 的核心參數。 若要這樣做，請開啟"/ 等/預設/grub"中的文字編輯器，編輯 GRUB\_CMDLINE\_LINUX 參數，例如:

        GRUB\_CMDLINE\_LINUX="rootdelay=300 console=ttyS0 earlyprintk=ttyS0"

    這也將確保所有主控台訊息都會傳送給第一個序列埠，有助於 Azure 支援團隊進行問題偵錯程序。 除了上述說明，我們建議您 *移除* 下列參數:

        rhgb quiet crashkernel=auto

    在雲端環境中，我們會將所有記錄傳送到序列埠，因此不適合使用圖形化和無訊息啟動。

    如有需要，您可以保留 `crashkernel` 選項的設定。但請注意，此參數將會減少 VM 中約 128 MB 或以上的可用記憶體數量。 這在較小的 VM 中可能會造成問題。

10.  在您完成編輯 "/etc/default/grub" 之後，請執行下列命令以重建 grub 組態：

        # sudo grub2 mkconfig-o /boot/grub2/grub.cfg

11.  確定您已安裝 SSH 伺服器，並已設定為在開機時啟動。 這通常是預設值。

12.  執行以下命令來安裝 Azure Linux 代理程式：

        # sudo yum 安裝 WALinuxAgent

13.  請勿在作業系統磁碟上建立交換空間。

    The Azure Linux Agent can automatically configure swap space by using the local resource disk that is attached to the VM after provisioning on Azure. Note that the local resource disk is a *temporary* disk, and it might be emptied when the VM is deprovisioned. After you install the Azure Linux Agent (see previous step), modify the following parameters in /etc/waagent.conf appropriately:

        ResourceDisk.Format=y
        ResourceDisk.Filesystem=ext4
        ResourceDisk.MountPoint=/mnt/resource
        ResourceDisk.EnableSwap=y
        ResourceDisk.SwapSizeMB=2048 ## NOTE: Set this to whatever you need it to be.

14.  執行下列命令，以取消佈建虛擬機器，並準備將其佈建於 Azure 上：

        # sudo waagent-force-取消佈建
        # export HISTSIZE=0
        # logout

15.  按一下 [ **動作-\ > 關閉** HYPER-V 管理員中。 您現在可以將 Linux VHD 上傳至 Azure。

