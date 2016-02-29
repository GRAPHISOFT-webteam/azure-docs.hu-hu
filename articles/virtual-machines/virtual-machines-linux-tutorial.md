<properties
    pageTitle="建立執行 Linux 的虛擬機器 | Microsoft Azure"
    description="了解如何使用 Azure 和 Azure 命令列介面的映像來建立 Azure Linux 虛擬機器或 Azure Ubuntu 虛擬機器。"
    keywords="linux virtual machine,virtual machine linux,Azure Linux,azure ubuntu" 
    services="virtual-machines"
    documentationCenter=""
    authors="squillace"
    manager="timlt"
    editor="tysonn"
    tags="azure-resource-manager" />

<tags
    ms.service="virtual-machines"
    ms.workload="infrastructure-services"
    ms.tgt_pltfrm="vm-linux"
    ms.devlang="na"
    ms.topic="hero-article"
    ms.date="10/21/2015"
    ms.author="rasquill"/>

# 建立執行 Linux 的虛擬機器

> [AZURE.SELECTOR]
- [Azure 入口網站-Windows](virtual-machines-windows-tutorial.md)
- [Azure PowerShell](virtual-machines-ps-create-preconfigure-windows-resource-manager-vms.md)
- [Azure PowerShell-範本](virtual-machines-create-windows-powershell-resource-manager-template.md)
- [Azure 入口網站的 Linux](virtual-machines-linux-tutorial-portal-rm.md)
- [Azure CLI](virtual-machines-linux-tutorial.md)

<br>

您可以從命令列或入口網站，輕鬆建立執行 Linux 的 Azure 虛擬機器 (VM)。 本教學課程會示範如何使用 Azure 命令列介面 (CLI)，快速建立在 Azure 中，執行 Ubuntu Server VM 的 Mac、 Linux 和 Windows 連線至使用 **ssh**, ，以及建立和掛接新磁碟。 本主題會使用 Ubuntu Server VM，但您也可以建立 Linux 虛擬機器使用 [您自己的映像作為範本](virtual-machines-linux-create-upload-vhd.md)。

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-rm-include.md)] 傳統部署模型。

[AZURE.INCLUDE [free-trial-note](../../includes/free-trial-note.md)]

## 影片逐步解說

以下是本教學課程的逐步解說。

[AZURE.VIDEO building-a-linux-virtual-machine-tutorial]

## 安裝 Azure CLI

第一個步驟是 [安裝 Azure CLI](../xplat-cli-install.md)。

很好。 現在，輸入 `azure config mode arm` 來確定您正處於資源管理模式。

更棒。 現在 [工作或學校識別碼登入](../xplat-cli-connect.md#use-the-log-in-method) 輸入 `azure login` 並遵循您的 Azure 帳戶互動式登入體驗的提示。

> [AZURE.NOTE] 如果您有工作或學校識別碼，而且您知道您不需要啟用雙重要素驗證，您可以使用 `azure login -u` 以及工作或學校識別碼登入，而不需要互動式工作階段。 如果您沒有工作或學校識別碼，您可以 [從您個人的 Microsoft 帳戶建立工作或學校識別碼](resource-group-create-work-id-from-personal.md)。

## 建立 Azure 虛擬機器

型別 `azure group create <my-group-name> westus` 取代 _& lt; 我群組名稱 & gt;_ 是唯一的群組名稱 (您可以使用不同的區域如果您想要)。 您應該會看到如下的內容：

    azure group create myuniquegroupname westus
    info:    Executing command group create
    + Getting resource group myuniquegroupname
    + Creating resource group myuniquegroupname
    info:    Created resource group myuniquegroupname
    data:    Id:                  /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/myuniquegroupname
    data:    Name:                myuniquegroupname
    data:    Location:            westus
    data:    Provisioning State:  Succeeded
    data:    Tags:
    data:
    info:    group create command OK

現在輸入 `azure vm quick-create` 來建立 VM，而您將會收到輸入其餘參數的提示。 使用您剛剛建立，並針對資源群組名稱 **ImageURN** 值之外，使用 `canonical:ubuntuserver:14.04.2-LTS:latest`, ，好讓您體驗看起來如下所示。 請注意，`azure vm quick-create` 命令會提示其建立、裝載和連接到 Linux VM 所需的基本資訊，包括：

- 資源群組名稱和 VM 名稱
- 部署位置
- 作業系統類型和映像 URN 字串
- 使用者名稱和密碼

然後建立裝載 VM 所需的基礎結構。 其中包括：

- VHD 儲存體和額外磁碟的 Azure 儲存體帳戶
- VM 的 NIC
- 含子網路的 Vnet
- 公用 IP 位址
- 子網域

        azure vm quick-create
        info:    Executing command vm quick-create
        Resource group name: myuniquegroupname
        Virtual machine name: myuniquevmname
        Location name: westus
        Operating system Type [Windows, Linux]: Linux
        ImageURN (format: "publisherName:offer:skus:version"): canonical:ubuntuserver:14.04.2-LTS:latest
        User name: ops
        Password: *********
        Confirm password: *********
        + Looking up the VM "myuniquevmname"
        info:    Using the VM Size "Standard_D1"
        info:    The [OS, Data] Disk or image configuration requires storage account
        + Retrieving storage accounts
        info:    Could not find any storage accounts in the region "westus", trying to create new one
        + Creating storage account "cli3c0464f24f1bf4f014323" in "westus"
        + Looking up the storage account cli3c0464f24f1bf4f014323
        + Looking up the NIC "myuni-westu-1432328437727-nic"
        info:    An nic with given name "myuni-westu-1432328437727-nic" not found, creating a new one
        + Looking up the virtual network "myuni-westu-1432328437727-vnet"
        info:    Preparing to create new virtual network and subnet
        / Creating a new virtual network "myuni-westu-1432328437727-vnet" [address prefix: "10.0.0.0/16"] with subnet "myuni-westu-1432328437727-snet"+[address prefix: "10.0.1.0/24"]
        + Looking up the virtual network "myuni-westu-1432328437727-vnet"
        + Looking up the subnet "myuni-westu-1432328437727-snet" under the virtual network "myuni-westu-1432328437727-vnet"
        info:    Found public ip parameters, trying to setup PublicIP profile
        + Looking up the public ip "myuni-westu-1432328437727-pip"
        info:    PublicIP with given name "myuni-westu-1432328437727-pip" not found, creating a new one
        + Creating public ip "myuni-westu-1432328437727-pip"
        + Looking up the public ip "myuni-westu-1432328437727-pip"
        + Creating NIC "myuni-westu-1432328437727-nic"
        + Looking up the NIC "myuni-westu-1432328437727-nic"
        + Creating VM "myuniquevmname"
        + Looking up the VM "myuniquevmname"
        + Looking up the NIC "myuni-westu-1432328437727-nic"
        + Looking up the public ip "myuni-westu-1432328437727-pip"
        data:    Id                              :/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/myuniquegroupname/providers/Microsoft.Compute/virtualMachines/myuniquevmname
        data:    ProvisioningState               :Succeeded
        data:    Name                            :myuniquevmname
        data:    Location                        :westus
        data:    FQDN                            :myuni-westu-1432328437727-pip.westus.cloudapp.azure.com
        data:    Type                            :Microsoft.Compute/virtualMachines
        data:
        data:    Hardware Profile:
        data:      Size                          :Standard_D1
        data:
        data:    Storage Profile:
        data:      Image reference:
        data:        Publisher                   :canonical
        data:        Offer                       :ubuntuserver
        data:        Sku                         :14.04.2-LTS
        data:        Version                     :latest
        data:
        data:      OS Disk:
        data:        OSType                      :Linux
        data:        Name                        :cli3c0464f24f1bf4f0-os-1432328438224
        data:        Caching                     :ReadWrite
        data:        CreateOption                :FromImage
        data:        Vhd:
        data:          Uri                       :https://cli3c0464f24f1bf4f014323.blob.core.windows.net/vhds/cli3c0464f24f1bf4f0-os-1432328438224.vhd
        data:
        data:    OS Profile:
        data:      Computer Name                 :myuniquevmname
        data:      User Name                     :ops
        data:      Linux Configuration:
        data:        Disable Password Auth       :false
        data:
        data:    Network Profile:
        data:      Network Interfaces:
        data:        Network Interface #1:
        data:          Id                        :/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/myuniquegroupname/providers/Microsoft.Network/networkInterfaces/myuni-westu-1432328437727-nic
        data:          Primary                   :true
        data:          MAC Address               :00-0D-3A-31-55-31
        data:          Provisioning State        :Succeeded
        data:          Name                      :myuni-westu-1432328437727-nic
        data:          Location                  :westus
        data:            Private IP alloc-method :Dynamic
        data:            Private IP address      :10.0.1.4
        data:            Public IP address       :191.239.51.1
        data:            FQDN                    :myuni-westu-1432328437727-pip.westus.cloudapp.azure.com
        info:    vm quick-create command OK

您的 VM 已啟動且正在執行中，並等候您進行連接。

## 連線到 Linux 虛擬機器

與 Linux 虛擬機器，您通常使用連接 **ssh**。 

> [AZURE.NOTE] 本主題會連接到 VM，使用使用者名稱和密碼。若要使用公用和私用金鑰組來與您的 VM 通訊，請參閱 [如何搭配使用 SSH 與 Azure 上的 Linux](virtual-machines-linux-use-ssh-key.md)。 您可以修改 **SSH** 以建立連線的 Vm `azure vm quick-create` 命令，以使用 `azure vm reset-access` 命令來重設 **SSH** 完全存取、 新增或移除使用者，或新增，安全地存取的公用金鑰檔案。 本文使用的使用者名稱和密碼 **SSH** 以求簡單明瞭。

如果您不熟悉連接與 **ssh**, ，此命令會採用表單 `ssh <username>@<publicdnsaddress> -p <the ssh port>`。 在此案例中，我們使用的使用者名稱和密碼的上一個步驟和連接埠 22，這是預設 **ssh** 連接埠。

    ssh ops@myuni-westu-1432328437727-pip.westus.cloudapp.azure.com -p 22
    The authenticity of host 'myuni-westu-1432328437727-pip.westus.cloudapp.azure.com (191.239.51.1)' can't be established.
    ECDSA key fingerprint is bx:xx:xx:xx:xx:xx:xx:xx:xx:x:x:x:x:x:x:xx.
    Are you sure you want to continue connecting (yes/no)? yes
    Warning: Permanently added 'myuni-westu-1432328437727-pip.westus.cloudapp.azure.com,191.239.51.1' (ECDSA) to the list of known hosts.
    ops@myuni-westu-1432328437727-pip.westus.cloudapp.azure.com's password:
    Welcome to Ubuntu 14.04.2 LTS (GNU/Linux 3.16.0-37-generic x86_64)

     * Documentation:  https://help.ubuntu.com/

      System information as of Fri May 22 21:02:32 UTC 2015

      System load: 0.37              Memory usage: 2%   Processes:       207
      Usage of /:  41.4% of 1.94GB   Swap usage:   0%   Users logged in: 0

      Graph this data and manage this system at:
        https://landscape.canonical.com/

      Get cloud support with Ubuntu Advantage Cloud Guest:
        http://www.ubuntu.com/business/services/cloud

    0 packages can be updated.
    0 updates are security updates.



    The programs included with the Ubuntu system are free software;
    the exact distribution terms for each program are described in the
    individual files in /usr/share/doc/*/copyright.

    Ubuntu comes with ABSOLUTELY NO WARRANTY, to the extent permitted by
    applicable law.

    ops@myuniquevmname:~$

現在已連線到 VM，您已準備好連接磁碟。

## 連接和掛接磁碟

連接新磁碟很快。 只要輸入 `azure vm disk attach-new <myuniquegroupname> <myuniquevmname> <size-in-GB>`，就能為 VM 建立並連接新的 GB 磁碟。 您應該會看到類似下面的畫面：

    azure vm disk attach-new myuniquegroupname myuniquevmname 5
    info:    Executing command vm disk attach-new
    + Looking up the VM "myuniquevmname"
    info:    New data disk location: https://cliexxx.blob.core.windows.net/vhds/myuniquevmname-20150526-0xxxxxxx43.vhd
    + Updating VM "myuniquevmname"
    info:    vm disk attach-new command OK


現在讓我們使用 `dmesg | grep SCSI` 來尋找該磁碟 (您用來探索新磁碟的方法可能有所不同)。 在此案例中，它看起來如下：

    dmesg | grep SCSI
    [    0.294784] SCSI subsystem initialized
    [    0.573458] Block layer SCSI generic (bsg) driver version 0.4 loaded (major 252)
    [    7.110271] sd 2:0:0:0: [sda] Attached SCSI disk
    [    8.079653] sd 3:0:1:0: [sdb] Attached SCSI disk
    [ 1828.162306] sd 5:0:0:0: [sdc] Attached SCSI disk

而且，在本主題的案例中，`sdc` 磁碟是我們想要的磁碟。 現在使用 `sudo fdisk /dev/sdc` 來分割磁碟 -- 假設在您的案例中，磁碟為 `sdc`，使它成為磁碟分割 1 上的主要磁碟，並接受其他預設值。

    sudo fdisk /dev/sdc
    Device contains neither a valid DOS partition table, nor Sun, SGI or OSF disklabel
    Building a new DOS disklabel with disk identifier 0x2a59b123.
    Changes will remain in memory only, until you decide to write them.
    After that, of course, the previous content won't be recoverable.

    Warning: invalid flag 0x0000 of partition table 4 will be corrected by w(rite)

    Command (m for help): n
    Partition type:
       p   primary (0 primary, 0 extended, 4 free)
       e   extended
    Select (default p): p
    Partition number (1-4, default 1): 1
    First sector (2048-10485759, default 2048):
    Using default value 2048
    Last sector, +sectors or +size{K,M,G} (2048-10485759, default 10485759):
    Using default value 10485759

在命令提示字元中，輸入 `p` 來建立磁碟分割：

    Command (m for help): p

    Disk /dev/sdc: 5368 MB, 5368709120 bytes
    255 heads, 63 sectors/track, 652 cylinders, total 10485760 sectors
    Units = sectors of 1 * 512 = 512 bytes
    Sector size (logical/physical): 512 bytes / 512 bytes
    I/O size (minimum/optimal): 512 bytes / 512 bytes
    Disk identifier: 0x2a59b123

       Device Boot      Start         End      Blocks   Id  System
    /dev/sdc1            2048    10485759     5241856   83  Linux

    Command (m for help): w
    The partition table has been altered!

    Calling ioctl() to re-read partition table.
    Syncing disks.

使用檔案系統寫入磁碟分割和 **mkfs** 命令，並指定檔案系統類型和裝置名稱。 在本主題中，我們將使用先前內容中的 `ext4` 和 `/dev/sdc1`：

    sudo mkfs -t ext4 /dev/sdc1
    mke2fs 1.42.9 (4-Feb-2014)
    Discarding device blocks: done
    Filesystem label=
    OS type: Linux
    Block size=4096 (log=2)
    Fragment size=4096 (log=2)
    Stride=0 blocks, Stripe width=0 blocks
    327680 inodes, 1310464 blocks
    65523 blocks (5.00%) reserved for the super user
    First data block=0
    Maximum filesystem blocks=1342177280
    40 block groups
    32768 blocks per group, 32768 fragments per group
    8192 inodes per group
    Superblock backups stored on blocks:
        32768, 98304, 163840, 229376, 294912, 819200, 884736

    Allocating group tables: done
    Writing inode tables: done
    Creating journal (32768 blocks): done
    Writing superblocks and filesystem accounting information: done

現在我們會使用 `mkdir`，建立目錄來掛接檔案系統：

    sudo mkdir /datadrive

而您可以使用 `mount` 來掛接目錄：

    sudo mount /dev/sdc1 /datadrive

資料磁碟現在可以當做 `/datadrive` 來使用。

    ls
    bin   datadrive  etc   initrd.img  lib64       media  opt   root  sbin  sys  usr  vmlinuz
    boot  dev        home  lib         lost+found  mnt    proc  run   srv   tmp  var

> [AZURE.NOTE] 您也可以連接到 Linux 虛擬機器使用 SSH 金鑰進行識別。 如需詳細資訊，請參閱 [如何搭配使用 SSH 與 Azure 上的 Linux](virtual-machines-linux-use-ssh-key.md)。

## 後續步驟

請記住，新的磁碟將通常無法使用的 vm 除非您將該資訊寫入重新開機，是否您 [fstab](http://en.wikipedia.org/wiki/Fstab) 檔案。 如果您想，您可以加入數個更多的磁碟和 [設定 RAID](virtual-machines-linux-configure-raid.md)。 

若要深入了解 Azure 上的 Linux，請參閱：

- [Azure 上的 Linux 和開放原始碼運算](virtual-machines-linux-opensource.md)

- [如何使用 Azure 命令列介面](../virtual-machines-command-line-tools.md)

- [使用適用於 Linux 的 Azure CustomScript 延伸模組部署 LAMP 應用程式](virtual-machines-linux-script-lamp.md)

- [Azure 上 Linux 的 Docker 虛擬機器擴充程式](virtual-machines-docker-vm-extension.md)

