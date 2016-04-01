<properties
    pageTitle="使用 Azure Powershell 建立 Linux VM | Microsoft Azure"
    description="了解如何使用 Azure PowerShell 建立和預先設定 Linux VM。"
    services="virtual-machines"
    documentationCenter=""
    authors="cynthn"
    manager="timlt"
    editor=""
    tags="azure-service-management"/>

<tags
    ms.service="virtual-machines"
    ms.workload="infrastructure-services"
    ms.tgt_pltfrm="vm-linux"
    ms.devlang="na"
    ms.topic="article"
    ms.date="11/11/2015"
    ms.author="cynthn"/>

# 使用 Azure PowerShell 建立和預先設定 Linux 虛擬機器

> [AZURE.SELECTOR]
- [Azure CLI](virtual-machines-linux-tutorial.md)
- [PowerShell](virtual-machines-ps-create-preconfigure-linux-vms.md)

<br>

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-classic-include.md)] 資源管理員模型。
 
這些步驟說明如何使用用於建立 Azure PowerShell 命令集的填空方式建立 Linux 虛擬機器。 如果您剛使用 Azure PowerShell 或只想知道可指定哪些值來成功設定組態，這個方法相當實用。 

您將建置命令集將一組命令區塊複製到文字檔或 PowerShell ISE 然後填入變數值並移除 < 和 > 字元。 請參閱這兩個 [範例](#examples) 以了解最終產生的結果這篇文章的結尾。

Windows 型虛擬機器系列主題，請參閱 [使用 Azure PowerShell 建立 windows 虛擬機器](virtual-machines-ps-create-preconfigure-windows-vms.md)。

## 安裝 Azure PowerShell

如果您還沒有這麼做， [安裝和設定 Azure PowerShell](../install-configure-powershell.md)。 然後，開啟 Azure PowerShell 命令提示字元。

## 設定您的訂閱和儲存體帳戶

透過 Azure PowerShell 命令提示字元執行下列命令，以設定 Azure 訂用帳戶和儲存體帳戶。 

您可以取得正確的訂閱名稱，從 **SubscriptionName** 的輸出屬性 **Get-azuresubscription** 命令。 

您可以取得正確的儲存體帳戶名稱，從 **標籤** 的輸出屬性 **Get-azurestorageaccount** 命令後您發出 Select-azuresubscription 命令。 

取代引號中，包括裡面 < 和 > 字元，以正確的名稱。

    $subscr="<subscription name>"
    $staccount="<storage account name>"
    Select-AzureSubscription -SubscriptionName $subscr –Current
    Set-AzureSubscription -SubscriptionName $subscr -CurrentStorageAccountName $staccount


## 尋找您要使用的映像

接下來，您必須決定您要使用之映像的 ImageFamily 值。 您可以透過下列命令取得可用 ImageFamily 值的清單。

    Get-AzureVMImage | select ImageFamily -Unique

以下是以 Linux 為基礎的電腦本身 ImageFamily 值的一些範例：

- Ubuntu Server 12.10
- CoreOS Alpha
- SUSE Linux Enterprise Server 12

開啟您所選擇的文字編輯器的新執行個體，或是 PowerShell 整合式指令碼環境 (ISE) 的執行個體。 將以下內容複製到新的文字檔或 PowerShell ISE 中，以取代 ImageFamily 值。

    $family="<ImageFamily value>"
    $image=Get-AzureVMImage | where { $_.ImageFamily -eq $family } | sort PublishedDate -Descending | select -ExpandProperty ImageName -First 1

## 指定名稱、大小和可用性設定組 (選擇性) 

選擇兩個命令區塊的其中一個，啟動命令集 (必要)。

**選項 1**︰ 指定虛擬機器名稱和大小。

    $vmname="<machine name>"
    $vmsize="<Specify one: Small, Medium, Large, ExtraLarge, A5, A6, A7, A8, A9>"
    $vm1=New-AzureVMConfig -Name $vmname -InstanceSize $vmsize -ImageName $image

**選項 2**︰ 指定名稱、 大小和可用性設定組名稱。

    $vmname="<machine name>"
    $vmsize="<Specify one: Small, Medium, Large, ExtraLarge, A5, A6, A7, A8, A9>"
    $availset="<set name>"
    $vm1=New-AzureVMConfig -Name $vmname -InstanceSize $vmsize -ImageName $image -AvailabilitySetName $availset

D、 DS 或 G 系列虛擬機器的 InstanceSize 值，請參閱 [虛擬機器和雲端服務大小](https://msdn.microsoft.com/library/azure/dn197896.aspx)。


## 設定使用者存取安全性選項

**選項 1**︰ 指定初始的 Linux 使用者名稱和密碼 （必要）。 選擇強式密碼。 若要檢查其強度，請參閱 [密碼檢查程式 ︰ 使用強式密碼](https://www.microsoft.com/security/pc-security/password-checker.aspx)。

    $cred=Get-Credential -Message "Type the name and password of the initial Linux account."
    $vm1 | Add-AzureProvisioningConfig -Linux -LinuxUser $cred.GetNetworkCredential().Username -Password $cred.GetNetworkCredential().Password

**選項 2**︰ 指定在訂閱中的已部署的 SSH 金鑰組。

    $vm1 | Add-AzureProvisioningConfig -Linux -SSHKeyPairs "<SSH key pairs>"

如需詳細資訊，請參閱 [如何在 Azure 上的 Linux 使用 SSH](virtual-machines-linux-use-ssh-key.md)。

**選項 3**︰ 在訂閱中指定的已部署的 SSH 公開金鑰清單。

    $vm1 | Add-AzureProvisioningConfig -Linux - SSHPublicKeys "<SSH public keys>"

Linux 型虛擬機器的其他預先組態選項，請參閱的語法 **Linux** 參數中設定 [Add-azureprovisioningconfig](https://msdn.microsoft.com/library/azure/dn495299.aspx)。


## 選擇性：指派靜態 DIP

也可選擇將特定 IP 位址 (稱為靜態 DIP) 指派給虛擬機器。

    $vm1 | Set-AzureStaticVNetIP -IPAddress <IP address>

您可以確認特定 IP 位址可用於下列命令。

    Test-AzureStaticVNetIP –VNetName <VNet name> –IPAddress <IP address>

## 選擇性：指派虛擬機器給特定子網路 

將虛擬機器指派給 Azure 虛擬網路中的特定子網路。

    $vm1 | Set-AzureSubnet -SubnetNames "<name of the subnet>"

    
## 選擇性：新增資料磁碟
    
將下列項目新增至命令集，以新增資料磁碟至虛擬機器。

    $disksize=<size of the disk in GB>
    $disklabel="<the label on the disk>"
    $lun=<Logical Unit Number (LUN) of the disk>
    $hcaching="<Specify one: ReadOnly, ReadWrite, None>"
    $vm1 | Add-AzureDataDisk -CreateNew -DiskSizeInGB $disksize -DiskLabel $disklabel -LUN $lun -HostCaching $hcaching

## 選擇性：將虛擬機器新增至現有的負載平衡 

將下列項目新增至命令集，以將虛擬機器新增至外部流量的現有負載平衡集。

    $prot="<Specify one: tcp, udp>"
    $localport=<port number of the internal port>
    $pubport=<port number of the external port>
    $endpointname="<name of the endpoint>"
    $lbsetname="<name of the existing load-balanced set>"
    $probeprotocol="<Specify one: tcp, http>"
    $probeport=<TCP or HTTP port number of probe traffic>
    $probepath="<URL path for probe traffic>"
    $vm1 | Add-AzureEndpoint -Name $endpointname -Protocol $prot -LocalPort $localport -PublicPort $pubport -LBSetName $lbsetname -ProbeProtocol $probeprotocol -ProbePort $probeport -ProbePath $probepath

## 決定如何啟動虛擬機器建立程序 

選擇下列其中一個命令區塊，以新增區塊至命令集來啟動虛擬機器建立程序。

**選項 1**︰ 在現有的雲端服務中建立虛擬機器。

    New-AzureVM –ServiceName "<short name of the cloud service>" -VMs $vm1

雲端服務的簡短名稱出現於 Azure 傳統入口網站內的 Azure 雲端服務清單，或出現於 Azure 入口網站的資源群組清單中。

**選項 2**︰ 在現有的雲端服務和虛擬網路中建立虛擬機器。

    $svcname="<short name of the cloud service>"
    $vnetname="<name of the virtual network>"
    New-AzureVM –ServiceName $svcname -VMs $vm1 -VNetName $vnetname

## 執行命令集

檢閱您在文字編輯器或 PowerShell ISE 中建立的 Azure PowerShell 命令集，並確定您已經指定所有變數且變數都有正確的值。 另外，請確定您已移除所有 < 和 > 字元。

將命令集複製到剪貼簿，然後以滑鼠右鍵按一下 [開啟 Azure PowerShell 命令提示字元]。 這將發出命令集作為一系列的 PowerShell 命令，並建立 Azure 虛擬機器。 

建立虛擬機器之後，請參閱 [如何登入執行 Linux 的虛擬機器](virtual-machines-linux-how-to-log-on.md)。

如果您想要重複使用命令集，可以：

- 將此命令集儲存為 PowerShell 指令碼檔案 (*.ps1)
- 將此設為 Azure 自動化 runbook 中的命令儲存 **自動化** 部分 Azure 傳統入口網站

## <a id="examples"></a>範例

以下是使用上述步驟建置 Azure PowerShell 命令集的兩個範例，這些命令集可在 Azure 中建立以 Linux 為基礎的虛擬機器。

### 範例 1

我需要 PowerShell 命令集建立 MySQL 伺服器的初始 Linux 虛擬機器：

- 使用 Ubuntu Server 12.10 映像。
- 名稱為 AZMYSQL1。
- 具有 500 GB 的額外資料磁碟。
- 靜態 IP 位址為 192.168.244.4。
- 位於 AZDatacenter 虛擬網路的後端子網路中。
- 位於 Azure TailspinToys 雲端服務中。

以下是建立這個虛擬機器的相對應 Azure PowerShell 命令集，各個區塊之間均有空白行，以求編排清晰。

    $family="Ubuntu Server 12.10"
    $image=Get-AzureVMImage | where { $_.ImageFamily -eq $family } | sort PublishedDate -Descending | select -ExpandProperty ImageName -First 1

    $vmname="AZMYSQL1"
    $vmsize="Large"
    $vm1=New-AzureVMConfig -Name $vmname -InstanceSize $vmsize -ImageName $image

    $cred=Get-Credential -Message "Type the name and password of the initial Linux account."
    $vm1 | Add-AzureProvisioningConfig -Linux -LinuxUser $cred.GetNetworkCredential().Username -Password $cred.GetNetworkCredential().Password

    $vm1 | Set-AzureSubnet -SubnetNames "BackEnd"

    $vm1 | Set-AzureStaticVNetIP -IPAddress 192.168.244.4

    $disksize=500
    $disklabel="MySQLData"
    $lun=0
    $hcaching="None"
    $vm1 | Add-AzureDataDisk -CreateNew -DiskSizeInGB $disksize -DiskLabel $disklabel -LUN $lun -HostCaching $hcaching

    $svcname="Azure-TailspinToys"
    $vnetname="AZDatacenter"
    New-AzureVM –ServiceName $svcname -VMs $vm1 -VNetName $vnetname

### 範例 2

我需要 PowerShell 命令集建立 Apache 伺服器的 Linux 虛擬機器：

- 使用 SUSE Linux Enterprise Server 12 映像。
- 名稱為 LOB1。
- 具有 50 GB 的額外資料磁碟。
- 是標準 Web 流量的 LOBServers 負載平衡器集的成員
- 位於 AZDatacenter 虛擬網路的前端子網路中。
- 位於 Azure TailspinToys 雲端服務中。

以下是建立這個虛擬機器的相對應 Azure PowerShell 命令集。

    $family="SUSE Linux Enterprise Server 12"
    $image=Get-AzureVMImage | where { $_.ImageFamily -eq $family } | sort PublishedDate -Descending | select -ExpandProperty ImageName -First 1

    $vmname="LOB1"
    $vmsize="Medium"
    $vm1=New-AzureVMConfig -Name $vmname -InstanceSize $vmsize -ImageName $image

    $cred=Get-Credential -Message "Type the name and password of the initial Linux account."
    $vm1 | Add-AzureProvisioningConfig -Linux -LinuxUser $cred.GetNetworkCredential().Username -Password $cred.GetNetworkCredential().Password

    $vm1 | Set-AzureSubnet -SubnetNames "FrontEnd"

    $disksize=50
    $disklabel="LOBApp"
    $lun=0
    $hcaching="ReadWrite"
    $vm1 | Add-AzureDataDisk -CreateNew -DiskSizeInGB $disksize -DiskLabel $disklabel -LUN $lun -HostCaching $hcaching

    $prot="tcp"
    $localport=80
    $pubport=80
    $endpointname="LOB1"
    $lbsetname="LOBServers"
    $probeprotocol="tcp"
    $probeport=80
    $probepath="/"
    $vm1 | Add-AzureEndpoint -Name $endpointname -Protocol $prot -LocalPort $localport -PublicPort $pubport -LBSetName $lbsetname -ProbeProtocol $probeprotocol -ProbePort $probeport -ProbePath $probepath

    $svcname="Azure-TailspinToys"
    $vnetname="AZDatacenter"
    New-AzureVM –ServiceName $svcname -VMs $vm1 -VNetName $vnetname

## 其他資源

[虛擬機器文件](http://azure.microsoft.com/documentation/services/virtual-machines/)

[Azure 虛擬機器常見問題集](http://msdn.microsoft.com/library/azure/dn683781.aspx)

[Azure 虛擬機器的概觀](http://msdn.microsoft.com/library/azure/jj156143.aspx)

[如何安裝和設定 Azure PowerShell](../install-configure-powershell.md)

[如何登入執行 Linux 的虛擬機器](virtual-machines-linux-how-to-log-on.md)

[使用 Azure PowerShell 建立和預先設定以 Windows 為基礎的虛擬機器](virtual-machines-ps-create-preconfigure-windows-vms.md)


