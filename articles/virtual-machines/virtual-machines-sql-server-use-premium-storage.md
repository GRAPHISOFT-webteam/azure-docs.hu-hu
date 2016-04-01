<properties 
    pageTitle="使用 Azure 高階儲存體搭配 SQL Server | Microsoft Azure"
    description="本文會使用以傳統部署模型建立的資源，並提供使用 Azure 高階儲存體搭配 Azure 虛擬機器上執行之 SQL Server 的指導方針。"
    services="virtual-machines"
    documentationCenter=""
    authors="danielsollondon"
    manager="jeffreyg"
   editor="monicar"    
   tags="azure-service-management"/>

<tags
    ms.service="virtual-machines"
    ms.devlang="na"
    ms.topic="article"
    ms.tgt_pltfrm="vm-windows-sql-server"
    ms.workload="infrastructure-services"
    ms.date="10/02/2015"
    ms.author="jroth"/>

# 在虛擬機器上搭配使用 Azure 高階儲存體和 SQL Server


## 概觀

[Azure 高階儲存體](../storage-premium-storage-preview-portal.md) 是新一代的存放裝置，可提供低延遲、 高輸送量 IO。 它最適合的索引鍵 IO 密集工作負載，例如 IaaS 上的 SQL Server [虛擬機器](http://azure.microsoft.com/services/virtual-machines/)。 

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-classic-include.md)] 資源管理員模型。
 

本文提供移轉執行 SQL Server 的虛擬機器來執行高階儲存體的規劃與指導方針。 這包括 Azure 基礎結構 (網路功能、儲存體) 和客體 Windows VM 步驟。 中的範例 [附錄](#appendix-migrating-a-multisite-alwayson-cluster-to-premium-storage) 示範如何移動更大規模的 Vm，以充分利用 PowerShell 改善本機 SSD 儲存體的全方位端對端移轉。

請務必了解在 IAAS VM 上搭配使用 Azure 高階儲存體和 SQL Server 的端對端處理程序。 其中包括：

- 識別要使用高階儲存體的必要條件。
- 在新部署中，將 IaaS 上的 SQL Server 部署到高階儲存體的範例。
- 移轉現有部署的範例，其中包括使用 SQL AlwaysOn 可用性群組的獨立伺服器和部署。
- 可能的移轉方法。
- 完整端對端範例，示範 Azure、 Windows 和 SQL Server 移轉現有 AlwaysOn 實作的步驟。

如需 SQL Server 在 Azure 虛擬機器上的背景資訊，請參閱 [Azure 虛擬機器中 SQL Server](virtual-machines-sql-server-infrastructure-services.md)。

**作者 ︰** Daniel Sol
**技術校閱 ︰** Luis Carlos Vargas Herring、 Sanjay Mishra、 Pravin Mital、 Juergen Thomas、 Gonzalo Ruiz。

## 適用於高階儲存體的必要條件

使用高階儲存體之前，必須具備數個必要條件。

### 機器大小

若要使用高階儲存體，您必須使用 DS 系列的虛擬機器 (VM)。 如果您未曾搭配使用 DS 系列的機器在雲端服務之前，您必須刪除現有的 VM、 保留連結的磁碟，然後建立新的雲端服務，再重新建立為 DS * 角色大小的 VM。 如需有關虛擬機器大小的詳細資訊，請參閱 [虛擬機器和雲端服務大小](virtual-machines-size-specs.md)。

### 雲端服務

如果 DS* VM 和高階儲存體是建立在新的雲端服務中，則您只能搭配使用它們。 如果您正在 Azure 中使用 SQL Server AlwaysOn，AlwaysOn 接聽程式將參考與雲端服務相關聯的 Azure 內部或外部負載平衡器 IP 位址。 本文會將重點放在如何於此案例中進行移轉的同時還保有可用性。

> [AZURE.NOTE] DS * 系列必須是第一個 VM 部署至新的雲端服務。

### 區域 VNET

DS * vm，您必須設定虛擬網路 (VNET) 中裝載為區域 Vm。 這會「拓寬」VNET 的範圍，以便在其他叢集中佈建更大規模的 VM，並允許在它們之間進行通訊。 在下列螢幕擷取畫面中，反白顯示的位置會顯示區域 VNET，而第一個結果顯示的是「範圍縮小」的 VNET。
 
![RegionalVNET][1]

您可以提高 Microsoft 支援票證以移轉到區域 VNET、Microsoft 將進行變更，然後完成區域 VNET 的移轉，在網路設定中變更屬性 AffinityGroup。 先匯出 PowerShell 中的網路設定，然後取代 **AffinityGroup** 屬性 **VirtualNetworkSite** 具有項目 **位置** 屬性。 指定 `Location = XXXX`，其中 `XXXX` 為 Azure 區域。 接著匯入新設定。

例如，考量下列 VNET 設定：

    <VirtualNetworkSite name="danAzureSQLnet" AffinityGroup="AzureSQLNetwork">
    <AddressSpace>
      <AddressPrefix>10.0.0.0/8</AddressPrefix>
      <AddressPrefix>172.16.0.0/12</AddressPrefix>
    </AddressSpace>
    <Subnets>
    ...
    </VirtualNetworkSite>

若要將此項目移至西歐的區域 VNET，請將設定變更為下列內容：

    <VirtualNetworkSite name="danAzureSQLnet" Location="West Europe">
    <AddressSpace>
      <AddressPrefix>10.0.0.0/8</AddressPrefix>
      <AddressPrefix>172.16.0.0/12</AddressPrefix>
    </AddressSpace>
    <Subnets>
    ...
    </VirtualNetworkSite>
    
### 儲存體帳戶

您需要建立新的儲存體帳戶，此帳戶是針對高階儲存體所設定。 請注意，高階儲存體的用法是設定於儲存體帳戶上，而非個別的 VHD 上，但在使用 DS* 系列 VM 時，您可以從高階和標準儲存體帳戶連結 VHD 的儲存體帳戶。 如果您不想將作業系統 VHD 放置於高階儲存體帳戶上，可考慮使用此項。

下列 **New-azurestorageaccountpowershell** 命令含有"Premium_LRS" **類型** 建立高階儲存體帳戶 ︰

    $newstorageaccountname = "danpremstor" 
    New-AzureStorageAccount -StorageAccountName $newstorageaccountname -Location "West Europe" -Type "Premium_LRS"   

### VHD 快取設定

在建立屬於高階儲存體帳戶一部分的磁碟間的主要差異在於磁碟快取設定。 針對 SQL Server 資料磁碟區磁碟建議您使用 '**讀取快取**'。 交易記錄磁碟區，磁碟快取設定應該設定為 '**無**'。 這與適用於標準儲存體帳戶的建議不同。

一旦連結 VHD 之後，就無法更改快取設定。 您需要中斷連結 VHD，然後使用更新的快取設定重新連結。

### Windows 儲存空間

您可以使用 [Windows 儲存空間](https://technet.microsoft.com/library/hh831739.aspx) 一樣使用先前的標準儲存體，這可讓您移轉已經使用儲存體空間的 VM。 中的範例 [附錄](#appendix-migrating-a-multisite-alwayson-cluster-to-premium-storage) （步驟 9 和之前） 示範如何使用 Powershell 程式碼，擷取並匯入連結多個 Vhd 的 VM。 

儲存集區會與標準的 Azure 儲存體帳戶搭配使用，以提高輸送量並降低延遲。 您可能會在針對新部署測試含有高階儲存體的儲存集區時尋找值，但它們會為儲存體設定增加額外的複雜度。 

#### 如何尋找哪些 Azure 虛擬磁碟對應到儲存集區

因為對於連結的 VHD 有不同的快取設定建議，所以您可能決定將 VHD 複製到高階儲存體帳戶。 但是，當您將它們重新連結到新的 DS 系列 VM 時，可能需要更改快取設定。 如果您擁有分別適用於 SQL 資料檔案和記錄檔的 VHD (而不是包含這兩者的單一 VHD)，就能以更簡單的方式套用高階儲存體的建議快取設.定。 

> [AZURE.NOTE] 如果您有相同的磁碟區上的 SQL Server 資料和記錄檔，您選擇的快取選項取決於您的資料庫工作負載的 IO 存取模式。 只有測試可以示範哪一個快取選項最適合這個案例。

但是，如果您正在使用由多個 VHD 組成的 Windows 儲存空間，就需要查看原始指令碼，以識別哪些連結的 VHD 位於哪個特定集區，然後就能針對每個磁碟據以設定快取設定。

如果您沒有原始指令碼可用來顯示哪些 VHD 會對應到儲存集區，就可以使用下列步驟來判斷磁碟/儲存集區對應。

針對每個磁碟，請使用下列步驟：

1. 取得清單中的磁碟連接至 VM **Get-azurevm** 命令 ︰

    Get-azurevm ServiceName <servicename> -Name <vmname> |Get AzureDataDisk

1. 記下 Diskname 和 LUN。

    ![DisknameAndLUN][2]

1. 從遠端桌面連接到 VM。 然後移至 **電腦管理** | **裝置管理員** | **磁碟機**。 看看每個 「 Microsoft 虛擬磁碟 」 的內容

    ![VirtualDiskProperties][3]

1. 此處的 LUN 編號是您在將 VHD 連結到 VM 時所指定的 LUN 編號的參考。
1. 針對 「 Microsoft 虛擬磁碟 」，請移至 **詳細資料** ] 索引標籤，然後在 **屬性** 清單中，移至 **驅動程式機碼**。 在 **值**, ，注意 **位移**, ，下列螢幕擷取畫面中為 0002。 0002 表示儲存集區參考的 PhysicalDisk2。

    ![VirtualDiskPropertyDetails][4]

2. 針對每個儲存集區，傾印出相關聯的磁碟：

    Get-StoragePool -FriendlyName AMS1pooldata | Get-PhysicalDisk

    ![GetStoragePool][5]
 
現在您可以使用此資訊，將連結的 VHD 關聯至儲存集區中的實體磁碟。

將 VHD 對應到儲存集區中的實體磁碟之後，接著就可以中斷連結，並將它們複製到高階儲存體帳戶，然後使用正確的快取設定來連結它們。 中的範例，請參閱 [附錄](#appendix-migrating-a-multisite-alwayson-cluster-to-premium-storage), ，步驟 8 到 12。 這些步驟示範如何將與 VM 連結的 VHD 磁碟設定擷取至 CSV 檔案、複製 VHD、更改磁碟設定快取設定，最後重新部署 VM 成為含有所有連結磁碟的 DS 系列 VM。

### VM 儲存體寬頻和 VHD 儲存體輸送量 

儲存體效能的程度取決於指定的 DS* VM 大小與 VHD 大小。 VM 對於連結的 VHD 數量以及它們將支援的最大頻寬 (MB/秒) 有不同的容許程度。 如需特定頻寬數，請參閱 [虛擬機器和雲端服務大小](virtual-machines-size-specs.md)。

提高 IOPS 可透過更大的磁碟大小來達成。 當您考量移轉路徑時，應將這點納入考慮。 如需詳細資訊， [適用於 IOPS 和磁碟類型，請參閱表格](../storage-premium-storage-preview-portal.md#scalability-and-performance-targets-when-using-premium-storage)。 

最後，請考量 VM 對於所有連結磁碟支援的磁碟頻寬上限各有不同。 在高負載下，您可針對該 VM 角色大小充分使用可用的最大磁碟頻寬。 例如，Standard_DS14 最多將支援 512MB/秒；因此，透過三個 P30 磁碟，您就能充分使用 VM 的磁碟頻寬。 但在此範例中，根據讀取和寫入 IO 的組合而定，可能會超過輸送量限制。

## 新的部署

接下來的兩節示範如何將 SQL Server VM 部署到高階儲存體。 如先前所提及，您不一定需要將作業系統磁碟放置於高階儲存體上。 如果您想要在作業系統 VHD 上放置任何需要大量 IO 的工作負載，就可以選擇執行這個動作。 

第一個範例示範使用現有的 Azure 資源庫映像。 第二個範例示範如何使用您在現有標準儲存體帳戶中擁有的自訂 VM 映像。

> [AZURE.NOTE] 這些範例假設您已經建立區域 VNET。

### 使用資源庫映像建立含有高階儲存體的新 VM

下列範例示範如何在高階儲存體上放置作業系統 VHD，並連結高階儲存體 VHD。 但是，您也可以將作業系統磁碟放置於標準儲存體帳戶中，然後連結位於高階儲存體帳戶中的 VHD。 以下將示範這兩個案例。

    $mysubscription = "DansSubscription"
    $location = "West Europe"
    
    #Set up subscription 
    Set-AzureSubscription -SubscriptionName $mysubscription 
    Select-AzureSubscription -SubscriptionName $mysubscription -Current  

#### 步驟 1：建立高階儲存體帳戶


    #Create Premium Storage account, note Type
    $newxiostorageaccountname = "danspremsams" 
    New-AzureStorageAccount -StorageAccountName $newxiostorageaccountname -Location $location -Type "Premium_LRS"  

 
#### 步驟 2：建立新的雲端服務

    $destcloudsvc = "danNewSvcAms" 
    New-AzureService $destcloudsvc -Location $location 


#### 步驟 3：保留雲端服務 VIP (選用)
    #check exisitng reserved VIP
    Get-AzureReservedIP
    
    $reservedVIPName = “sqlcloudVIP” 
    New-AzureReservedIP –ReservedIPName $reservedVIPName –Label $reservedVIPName –Location $location 

#### 步驟 4：建立 VM 容器
    #Generate storage keys for later 
    $xiostorage = Get-AzureStorageKey -StorageAccountName $newxiostorageaccountname 
    
    ##Generate storage acc contexts 
    $xioContext = New-AzureStorageContext –StorageAccountName $newxiostorageaccountname -StorageAccountKey $xiostorage.Primary   
    
    #Create container
    $containerName = 'vhds'
    New-AzureStorageContainer -Name $containerName -Context $xioContext

#### 步驟 5：在標準或高階儲存體上放置作業系統 VHD
    #NOTE: Set up subscription and default storage account which will be used to place the OS VHD in
    
    #If you want to place the OS VHD Premium Storage Account
    Set-AzureSubscription -SubscriptionName $mysubscription -CurrentStorageAccount  $newxiostorageaccountname  
    
    #If you wanted to place the OS VHD Standard Storage Account but attach Premium Storage VHDs then you would run this instead:
    $standardstorageaccountname = "danstdams" 
    
    Set-AzureSubscription -SubscriptionName $mysubscription -CurrentStorageAccount  $standardstorageaccountname

#### 步驟 6：建立 VM
    #Get list of available SQL Server Images from the Azure Image Gallery.
    $galleryImage = Get-AzureVMImage | where-object {$_.ImageName -like "*SQL*2014*Enterprise*"} 
    $image = $galleryImage.ImageName 
    
    #Set up Machine Specific Information
    $vmName = "dsDan1"
    $vnet = "dansvnetwesteur"
    $subnet = "SQL"
    $ipaddr = "192.168.0.8"
    
    #Remember to change to DS series VM
    $newInstanceSize = "Standard_DS1"
    
    #create new Avaiability Set 
    $availabilitySet = "cloudmigAVAMS"
    
    #Machine User Credentials
    $userName = "myadmin"
    $pass = "mycomplexpwd4*"
    
    #Create VM Config
    $vmConfigsl = New-AzureVMConfig -Name $vmName -InstanceSize $newInstanceSize -ImageName $image  -AvailabilitySetName $availabilitySet  ` | Add-AzureProvisioningConfig -Windows ` -AdminUserName $userName -Password $pass | Set-AzureSubnet -SubnetNames $subnet | Set-AzureStaticVNetIP -IPAddress $ipaddr
    
    #Add Data and Log Disks to VM Config
    #Note the size specified ‘-DiskSizeInGB 1023’, this will attach 2 x P30 Premium Storage Disk Type
    #Utilising the Premium Storage enabled Storage account
    
    $vmConfigsl | Add-AzureDataDisk -CreateNew -DiskSizeInGB 1023 -LUN 0 -HostCaching "ReadOnly"  -DiskLabel "DataDisk1" -MediaLocation "https://$newxiostorageaccountname.blob.core.windows.net/vhds/$vmName-data1.vhd"
    $vmConfigsl | Add-AzureDataDisk -CreateNew -DiskSizeInGB 1023 -LUN 1 -HostCaching "None"  -DiskLabel "logDisk1" -MediaLocation "https://$newxiostorageaccountname.blob.core.windows.net/vhds/$vmName-log1.vhd"
    
    #Create VM
    $vmConfigsl  | New-AzureVM –ServiceName $destcloudsvc -VNetName $vnet ## Optional (-ReservedIPName $reservedVIPName)  
    
    #Add RDP Endpoint
    $EndpointNameRDPInt = "3389"
    Get-AzureVM -ServiceName $destcloudsvc -Name $vmName | Add-AzureEndpoint -Name "EndpointNameRDP" -Protocol "TCP" -PublicPort "53385" -LocalPort $EndpointNameRDPInt  | Update-AzureVM
    
    #Check VHD storage account, these should be in $newxiostorageaccountname 
    Get-AzureVM -ServiceName $destcloudsvc -Name $vmName | Get-AzureDataDisk
    Get-AzureVM -ServiceName $destcloudsvc -Name $vmName |Get-AzureOSDisk
     

### 建立新 VM 以搭配使用高階儲存體和自訂映像

這個案例示範您現有的自訂映像是位於標準儲存體帳戶中。 如前所述，如果您想要將作業系統 VHD 放置於高階儲存體上，就需要複製存在於標準儲存體帳戶中的映像，並將其傳輸到高階儲存體，然後才能加以使用。 如果您擁有內部部署的映像，也可以使用這個方法，直接將它複製到高階儲存體帳戶。

#### 步驟 1：建立儲存體帳戶
    $mysubscription = "DansSubscription"
    $location = "West Europe"
    
    #Create Premium Storage account
    $newxiostorageaccountname = "danspremsams" 
    New-AzureStorageAccount -StorageAccountName $newxiostorageaccountname -Location $location -Type "Premium_LRS"  
    
    #Standard Storage account
    $origstorageaccountname = "danstdams" 
 
#### 步驟 2：建立雲端服務
    $destcloudsvc = "danNewSvcAms" 
    New-AzureService $destcloudsvc -Location $location 

 
#### 步驟 3：使用現有的映像
您可以使用現有的映像。 或者，您可以 [取得現有機器的映像](virtual-machines-capture-image-windows-server.md)。 請注意，您取得映像的機器不一定是 DS* 機器。 映像之後，下列步驟示範如何將它複製到高階儲存體帳戶，具有 **Start-azurestorageblobcopy** PowerShell 指令程式。 

    #Get storage account keys:
    #Standard Storage account
    $originalstorage =  Get-AzureStorageKey -StorageAccountName $origstorageaccountname
    #Premium Storage account
    $xiostorage = Get-AzureStorageKey -StorageAccountName $newxiostorageaccountname
    
    #Set up contexts for the storage accounts:
    $origContext = New-AzureStorageContext  –StorageAccountName $origstorageaccountname -StorageAccountKey $originalstorage.Primary
    $destContext = New-AzureStorageContext  –StorageAccountName $newxiostorageaccountname -StorageAccountKey $xiostorage.Primary  
 
#### 步驟 4：在儲存帳戶間複製 Blob
    #Get Image VHD 
    $myImageVHD = "dansoldonorsql2k14-os-2015-04-15.vhd"
    $containerName = 'vhds'
    
    #Copy Blob between accounts
    $blob = Start-AzureStorageBlobCopy -SrcBlob $myImageVHD -SrcContainer $containerName `
    -DestContainer vhds -Destblob "prem-$myImageVHD" `
    -Context $origContext -DestContext $destContext  

#### 步驟 5：定期檢查複製狀態：
    $blob | Get-AzureStorageBlobCopyState 

#### 步驟 6：將映像磁碟新增到訂用帳戶中的 Azure 磁碟儲存機制
    $imageMediaLocation = $destContext.BlobEndPoint+"/"+$myImageVHD 
    $newimageName = "prem"+"dansoldonorsql2k14"
    
    Add-AzureVMImage -ImageName $newimageName -MediaLocation $imageMediaLocation
 
> [AZURE.NOTE] 您可能會發現即使狀態回報為成功，您可能仍然得到磁碟租用錯誤。 在此情況下，請等待大約 10 分鐘的時間。

#### 步驟 7 ︰ 建置 VM
您會在此處從映像建置 VM，並連結兩個高階儲存體 VHD：

    $newimageName = "prem"+"dansoldonorsql2k14"
    #Set up Machine Specific Information
    $vmName = "dansolchild"
    $vnet = "westeur"
    $subnet = "Clients"
    $ipaddr = "192.168.0.41"
    
    #This will need to be a new cloud service
    $destcloudsvc = "danregsvcamsxio2"
    
    #Use to DS Series VM
    $newInstanceSize = "Standard_DS1"
    
    #create new Avaiability Set 
    $availabilitySet = "cloudmigAVAMS3"
    
    #Machine User Credentials
    $userName = "myadmin"
    $pass = "theM)stC0mplexP@ssw0rd!”
     
    
    #Create VM Config
    $vmConfigsl2 = New-AzureVMConfig -Name $vmName -InstanceSize $newInstanceSize -ImageName $newimageName  -AvailabilitySetName $availabilitySet  ` | Add-AzureProvisioningConfig -Windows ` -AdminUserName $userName -Password $pass | Set-AzureSubnet -SubnetNames $subnet | Set-AzureStaticVNetIP -IPAddress $ipaddr
    
    $vmConfigsl2 | Add-AzureDataDisk -CreateNew -DiskSizeInGB 1023 -LUN 0 -HostCaching "ReadOnly"  -DiskLabel "DataDisk1" -MediaLocation "https://$newxiostorageaccountname.blob.core.windows.net/vhds/$vmName-Datadisk-1.vhd" 
    $vmConfigsl2 | Add-AzureDataDisk -CreateNew -DiskSizeInGB 1023 -LUN 1 -HostCaching "None"  -DiskLabel "LogDisk1" -MediaLocation "https://$newxiostorageaccountname.blob.core.windows.net/vhds/$vmName-logdisk-1.vhd" 
     
    
    
    $vmConfigsl2 | New-AzureVM –ServiceName $destcloudsvc -VNetName $vnet 

## 未使用 AlwaysOn 可用性群組的現有部署

> [AZURE.NOTE] 針對現有部署，請先參閱 [必要條件](#prerequisites-for-premium-storage) 本主題的章節。

對於未使用 AlwaysOn 可用性群組的 SQL Server 部署，以及使用該可用性群組的部署有不同的考量。 如果您未使用 AlwaysOn 且目前擁有獨立的 SQL Server，就可以使用新的雲端服務和儲存帳戶升級到高階儲存體。 請考量下列選項：

- **建立新的 SQL Server VM**。 您可以建立新的 SQL Server VM 來使用高階儲存體帳戶，如＜新的部署＞中所述。 然後備份並還原 SQL Server 設定和使用者資料庫。 如果您會從內部或外部存取應用程式，就需要更新該應用程式，才能參考新的 SQL Server。 您需要複製所有「超出資料庫範圍」的物件，如同執行並存的 (SxS) SQL Server 移轉一樣。 這包含像是登入、憑證及連結的伺服器等物件。
- **移轉現有的 SQL Server VM**。 這需要使 SQL Server VM 離線，然後將它傳輸到新的雲端服務，其中包含將其連結的所有 VHD 複製到高階儲存體帳戶。 當 VM 上線時，應用程式將和以前一樣參考伺服器主機名稱。 請注意，現有磁碟的大小將影響效能特性。 例如，400 GB 的磁碟會調高為 P20。 如果您知道不需要該磁碟效能，則可重新建立 VM 做為 DS 系列 VM，然後連結所需大小/效能規格的高階儲存體 VHD。 然後，您可以中斷連結並重新連結 SQL DB 檔案。

> [AZURE.NOTE] 當複製 VHD 磁碟，您應該注意大小，視大小而定的 Premium 儲存體磁碟類型表示它們分成時，這會決定磁碟效能規格。 Azure 將調高為最接近的磁碟大小，因此，如果您擁有 400 GB 的磁碟，這將會調高為 P20。 根據您對於作業系統 VHD 的現有 IO 需求而定，可能不需要將此移轉到高階儲存體帳戶。

如果您的 SQL 可從外部存取，則雲端服務 VIP 將會變更。 您也必須更新端點、ACL 及 DNS 設定。

## 使用 AlwaysOn 可用性群組的現有部署

> [AZURE.NOTE] 針對現有部署，請先參閱 [必要條件](#prerequisites-for-premium-storage) 本主題的章節。

本節一開始，我們將查看 AlwaysOn 與 Azure 網路互動的方式。 然後將移轉細分為兩種案例：可容許一段停機時間的移轉，以及必須達到最低停機時間的移轉。

內部部署的 SQL Server AlwaysOn 可用性群組會使用內部部署的接聽程式，來註冊虛擬 DNS 名稱以及 IP 位址，其可在一或多部 SQL Server 之間共用。 當用戶端連結時，即會透過接聽程式 IP 將它們路由傳送到主要的 SQL Server。 這是當時擁有 AlwaysOn IP 資源的伺服器。
 
![DeploymentsUseAlwaysOn][6]

在 Microsoft Azure 中，您只能有一個已指派至 VM 上 NIC 的 IP 位址，因此，為了達到和內部部署一樣的抽象層，Azure 會利用已指派給內部/外部負載平衡器 (ILB/ELB) 的 IP 位址。 在伺服器之間共用的 IP 資源已設為與 ILB/ELB 相同的 IP。 這會在 DNS 中發佈，而用戶端流量是透過 ILB/ELB 傳遞到主要的 SQL Server 複本。 ILB/ELB 知道哪一部是主要的 SQL Server，因為它會使用探查來探查 AlwaysOn IP 資源。 在上一個範例中，它會探查每個擁有 ELB/ILB 所參考之端點的節點，無論哪個回應都是主要的 SQL Server。 

> [AZURE.NOTE] ILB 和 ELB 都已指派給特定的 Azure 雲端服務，因此在 Azure 中的任何雲端移轉很可能是表示負載平衡器 IP 會變更。 

### 移轉可允許一段停機時間的 AlwaysOn 部署

有兩種策略可用來移轉允許一段停機時間的 AlwaysOn 部署：

1. **在現有的 AlwaysOn 叢集中新增更多次要複本**
1. **移轉到新的 AlwaysOn 叢集**

#### 1.在現有的 AlwaysOn 叢集中新增更多次要複本

有一個策略是在 AlwaysOn 可用性群組中新增更多次要項目。 您需要將這些項目新增到新的雲端服務，然後使用新的負載平衡器 IP 來更新接聽程式。 

##### 停機時間點：

- 叢集驗證。
- 測試適用於新次要項目的 AlwaysOn 容錯移轉

如果您是在 VM 中使用 Windows 儲存集區來提供更高的 IO 輸送量，則將會在完整叢集驗證期間使它們離線。 當您將節點新增到叢集時，需要進行驗證測試。 執行測試的時間各有不同，因此，您應該在具代表性的測試環境中測試此項目，以取得這個動作需要花費的大約時間。 

您應該佈建可在新增加的節點上執行手動容錯移轉與混亂測試的時間，以確保 AlwaysOn 高可用性函式會如預期般運作。

![DeploymentUseAlwaysOn2][7]

> [AZURE.NOTE] 您應該停止 SQL Server 驗證執行之前，使用儲存集區的所有執行個體。 
##### 高階步驟

1. 在新的雲端服務中，使用連結的高階儲存體來建立兩部新的 SQL Server。
1. 複製完整備份及還原與 **NORECOVERY**。
1. 複製「超出使用者 DB 範圍」的相依物件，例如登入等項目。
1. 建立新的內部負載平衡器 (ILB) 或使用外部負載平衡器 (ELB)，然後在這兩個新節點上設定負載平衡的端點。
> [AZURE.NOTE] 檢查所有節點都都正確的端點組態，然後再繼續

1. 停止使用者/應用程式存取 SQL Server (如果正在使用儲存集區)。
1. 停止所有節點上的 SQL Server 引擎服務 (如果正在使用儲存集區)。
1. 將新節點新增到叢集，然後執行完整驗證。 
1. 驗證成功之後，啟動所有 SQL Server 服務。
1. 備份交易記錄，然後還原使用者資料庫。
1. 將新節點新增到 AlwaysOn 可用性群組，並使複寫 **同步**。 
1. AlwaysOn 根據多站台的範例中，新增 IP 位址資源的新雲端服務 ILB/ELB 透過 PowerShell [附錄](#appendix-migrating-a-multisite-alwayson-cluster-to-premium-storage)。 在 Windows 叢集中，設定 **可能的擁有者** 的 **IP 位址** 的新節點的資源。 請參閱 > 一節新增 IP 位址資源同一個子網路上 ' [附錄](#appendix-migrating-a-multisite-alwayson-cluster-to-premium-storage)。
1. 容錯移轉到其中一個新節點。
1. 使新節點成為自動容錯移轉夥伴並測試容錯移轉。
1. 從可用性群組移除原始節點。

##### 優點

- 您可以在將新的 SQL Server 新增到 AlwaysOn 之前進行測試 (SQL Server 和應用程式)。
- 您可以變更 VM 大小，並自訂儲存體大小以便完全符合您的需求。 但是，使所有 SQL 檔案路徑保持不變是非常實用的。
- 您可以控制將 DB 備份傳輸到次要複本的開始時機。 這不同於使用 Azure **Start-azurestorageblobcopy** commandlet 來複製 Vhd，因為那是非同步的複本。

##### 缺點
- 使用 Windows 儲存集區時，會在為新的其他節點進行完整叢集驗證期間產生叢集停機時間。 
- 根據 SQL Server 版本和次要複本的現有數目而定，您可能無法在不移除現有次要項目的情況下新增更多次要複本。
- 在設定次要項目時，可能需要較長的 SQL 資料傳輸時間。
- 當您平行執行新機器時，會在移轉期間產生額外成本。

#### 2.移轉到新的 AlwaysOn 叢集

另一種策略是在新的雲端服務中，使用全新的節點來建立全新的 AlwaysOn 叢集，然後重新導向用戶端來使用該節點。

##### 停機時間點

當您將應用程式和使用者傳輸到新的 AlwaysOn 接聽程式時，即會產生停機時間。 停機時間取決於下列各項：

- 用來將最後的交易記錄備份還原到新伺服器上之資料庫所需的時間。
- 用來更新用戶端應用程式以使用新的 AlwaysOn 接聽程式所需的時間。

##### 優點

- 您可以測試實際生產環境、SQL Server 及作業系統組建變更。
- 您可以選擇自訂儲存體，盡可能減少 VM 的大小。 這可能會降低成本。
- 您可以在此程序執行期間，更新 SQL Server 的組建或版本。 您也可以升級作業系統。
- 先前的 AlwaysOn 叢集可以做為穩定的回復目標。

##### 缺點

- 如果您想要同時執行這兩個 AlwaysOn 叢集，就需要變更接聽程式的 DNS 名稱。 因為用戶端應用程式字串必須反映新的接聽程式名稱，所以這會在移轉期間增加管理負荷。
- 您必須在這兩個環境之間實作同步處理機
制，使它們盡可能靠近彼此，以便在移轉之前將最後的同步處理需求降至最低。
- 當您正在執行新環境時，這會在移轉期間增加成本。

### 利用最少的停機時間移轉 AlwaysOn 部署

有兩種策略可利用最少的停機時間來移轉 AlwaysOn 部署：

1. **利用現有的次要項目：單一站台**
1. **利用現有的次要項目：多站台**

#### 1.利用現有的次要項目：單一站台

將停機時間降至最低的其中一種策略是取得現有的雲端次要項目，並從目前的雲端服務中移除它。 然後將 VHD 複製到新的高階儲存體帳戶，並在新的雲端服務中建立 VM。 接著在叢集設定和容錯移轉中更新接聽程式。

##### 停機時間點

- 當您使用負載平衡的端點更新最後一個節點時，即會產生停機時間。
- 根據您的用戶端/DNS 設定而定，用戶端連線可能會延遲。
- 如果您選擇使 AlwaysOn 叢集群組離線以交換出 IP 位址，則會產生額外的停機時間。 您可以針對新增的 IP 位址資源使用 OR 相依性和可能的擁有者，來避免發生這個情況。 請參閱 > 一節新增 IP 位址資源同一個子網路上 ' [附錄](#appendix-migrating-a-multisite-alwayson-cluster-to-premium-storage)。

> [AZURE.NOTE] 當您想加入的節點以成為 AlwaysOn 容錯移轉夥伴時，您需要新增參考負載平衡集的 Azure 端點。 當您執行 **Add-azureendpoint** 命令來執行這項操作，目前的連線保持開啟狀態，但新連線的接聽程式將無法更新負載平衡器之前建立。 測試時，若看見此項目持續 90-120 秒，就應該測試此項目。 

##### 優點

- 移轉期間不會產生額外的成本。
- 一對一移轉。
- 降低複雜度。
- 允許從高階儲存體 SKU 增加 IOPS。 從 VM 中斷連結磁碟並將其複製到新的雲端服務時，可以使用協力廠商的工具來增加 VHD 大小，以提供更高的輸送量。 增加 VHD 大小，請參閱這個 [論壇討論](https://social.msdn.microsoft.com/Forums/azure/4a9bcc9e-e5bf-4125-9994-7c154c9b0d52/resizing-azure-data-disk?forum=WAVirtualMachinesforWindows)。

##### 缺點

- 移轉期間會暫時遺失 HA 和 DR。
- 因為這是 1:1 移轉，所以必須使用最小的 VM 大小來支援 VHD 數目，而您可能無法縮小 VM 的大小。
- 這種情況下，應使用 Azure **Start-azurestorageblobcopy** commandlet，這是非同步。 複製完成時沒有 SLA。 複製的時間各有不同，儘管這取決於在佇列中等候的時間，但它還是會根據要傳輸的資料量來決定。 如果即將傳輸到另一個 Azure 資料中心 (該資料中心支援另一個區域的高階儲存體)，則會增加複製時間。 如果您只有 2 個節點，請考慮可能發生複製所需時間比測試時間還長的移轉案例。 這可能包括下列概念。
    - 在進行具有議定之停機時間的移轉之前，為 HA 新增第 3 個 SQL Server 臨時節點。
    - 在 Azure 排定的維護期間以外執行移轉。
    - 確保您已正確設定叢集仲裁。  

##### 高階步驟

這份文件不會示範完整的端對端範例，不過 [附錄](#appendix-migrating-a-multisite-alwayson-cluster-to-premium-storage) 提供可以用來執行此動作的詳細資料。
 
![MinimalDowntime][8]

- 蒐集磁碟設定，並移除節點 (不要刪除連結的 VHD)。
- 建立高階儲存體帳戶，並從標準儲存體帳戶複製 VHD
- 建立新的雲端服務，並在該雲端服務中重新部署 SQL2 VM。 使用複製的原始作業系統 VHD 並連結複製的 VHD 來建立 VM。
- 設定 ILB / ELB 並新增端點。
- 使用下列任一種方法來更新接聽程式：
    - 使 AlwaysOn 群組離線，並使用新的 ILB / ELB IP 位址來更新 AlwaysOn 接聽程式。 
    - 或者，透過 PowerShell，將新雲端服務 ILB/ELB 的 IP 位址資源新增到 Windows 叢集。 接著，將 IP 位資源的 [可能的擁有者] 設為移轉的節點 SQL2，並在 [網路名稱] 中將此設為  OR 相依性。 請參閱 > 一節新增 IP 位址資源同一個子網路上 ' [附錄](#appendix-migrating-a-multisite-alwayson-cluster-to-premium-storage)。
- 檢查連到用戶端的 DNS 設定/傳播。
- 移轉 SQL1 VM，然後執行步驟 2 - 4。
- 如果使用步驟 5ii，則新增 SQL1 做為新增 IP 位址資源的 [可能的擁有者]
- 測試容錯移轉。

#### 2.利用現有的次要項目：多站台

如果您的節點分佈於一個以上的 Azure 資料中心 (DC)，或者您擁有混合式環境，則您可在這個環境中使用 AlwaysOn 設定來將停機時間降至最低。

方法是針對內部部署或次要的 Azure DC，將 AlwaysOn 同步處理變更為「同步」，然後容錯移轉到該 SQL Server。 接著，將 VHD 複製到高階儲存體帳戶，並將機器重新部署到新的雲端服務。 更新接聽程式，然後進行容錯回復。 

##### 停機時間點

停機時間包含容錯移轉到替代 DC 和返回的時間。 它也會取決於您的用戶端/DNS 設定，而您的用戶端重新連線可能會延遲。
請考慮下列混合式 AlwaysOn 設定範例：

![MultiSite1][9]

##### 優點

- 您可以利用現有的基礎結構。
- 您可以選擇先在 DR Azure DC 上預先升級 Azure 儲存體。
- DR Azure DC 儲存體可以重新設定。
- 移轉期間至少有兩個容錯移轉，但不包括測試容錯移轉。
- 您不需要移動 SQL Server 資料來進行備份與還原。

##### 缺點

- 根據 SQL Server 的用戶端存取而定，若 SQL Server 是在應用程式的替代 DC 中執行，就可能會增加延遲。
- 將 VHD 複製到高階儲存體的時間可能會變長。 這可能會影響您對於是否要在可用性群組中保留該節點所做的決策。 如果需要在移轉期間執行需要大量記錄的工作負載，請將這點納入考慮，因為主要節點將必須在其交易記錄中保留未複寫的交易。 因此，這可能會顯著成長。
- 這種情況下，應使用 Azure **Start-azurestorageblobcopy** commandlet，這是非同步。 完成時沒有 SLA。 複製的時間各有不同，儘管這取決於在佇列中等候的時間，但它還是會根據要傳輸的資料量來決定。 因此，在第 2 個資料中心只需一個節點，而您應該在複製所需時間比測試時間還長的情況下，採取移轉步驟。 這可能包括下列概念。
    - 在進行具有議定之停機時間的移轉之前，為 HA 新增第 2 個 SQL 暫時節點。
    - 在 Azure 排定的維護期間以外執行移轉。 
    - 確保您已正確設定叢集仲裁。 

此案例假設您已記錄安裝且知道儲存體的對應方式，以進行變更來取得最佳的磁碟快取設定。

##### 高階步驟
![Multisite2][10]

- 使內部部署 / 替代 Azure DC 成為 SQL Server 主要項目，並使其成為另一個自動容錯移轉夥伴 (AFP)。 
- 從 SQL2 蒐集磁碟設定資訊，並移除節點 (不要刪除連結的 VHD)。
- 建立高階儲存體帳戶，並從標準儲存體帳戶複製 VHD。
- 建立新的雲端服務，並使用其連結的高階儲存體磁碟來建立 SQL2 VM。
- 設定 ILB / ELB 並新增端點。
- 使用新的  ILB / ELB IP 位址來更新 AlwaysOn 接聽程式，並測試容錯移轉。
- 檢查 DNS 設定。
- 將 AFP 變更為 SQL2，然後移轉 SQL1，並執行步驟 2 – 5。
- 測試容錯移轉。
- 將 AFP 切換回 SQL1 和 SQL2

## 附錄：將多站台 AlwaysOn 叢集移轉到高階儲存體 

本主題的剩餘內容提供將多站台 AlwaysOn 叢集轉換為高階儲存體的詳盡範例。 它還會將接聽程式從使用外部負載平衡器 (ELB) 轉換為內部負載平衡器 (ILB)。

### Environment

- Windows 2k12 / SQL 2k12
- SP 上 1 個 DB 檔案
- 每個節點 2 x 個儲存集區

![Appendix1][11]
 
### VM：
 
在此範例中，我們即將示範從 ELB 移動到 ILB 的方式。 ELB 可在 ILB 之前取得，因此，這會示範如何在移轉期間切換到前者。

![Appendix2][12]

### 預先步驟：連線到訂用帳戶

    Add-AzureAccount
    
    #Set up subscription
    Get-AzureSubscription 

#### 步驟 1：建立新的儲存體帳戶和雲端服務
    $mysubscription = "DansSubscription"
    $location = "West Europe"
    
    #Storage accounts
    #current storage account where the vm to migrate resides
    $origstorageaccountname = "danstdams"
    
    #Create Premium Storage account
    $newxiostorageaccountname = "danspremsams" 
    New-AzureStorageAccount -StorageAccountName $newxiostorageaccountname -Location $location -Type "Premium_LRS"  
    
    #Generate storage keys for later
    $originalstorage =  Get-AzureStorageKey -StorageAccountName $origstorageaccountname
    $xiostorage = Get-AzureStorageKey -StorageAccountName $newxiostorageaccountname
    
    #Generate storage acc contexts
    $origContext = New-AzureStorageContext  –StorageAccountName $origstorageaccountname -StorageAccountKey $originalstorage.Primary
    $xioContext = New-AzureStorageContext  –StorageAccountName $newxiostorageaccountname -StorageAccountKey $xiostorage.Primary  
    
    #Set up subscription and default storage account
    Set-AzureSubscription -SubscriptionName $mysubscription -CurrentStorageAccount $origstorageaccountname
    Select-AzureSubscription -SubscriptionName $mysubscription -Current 
    
    #CREATE NEW CLOUD SVC
    $vnet = "dansvnetwesteur"
    
    ##Existing cloud service
    $sourceSvc="dansolSrcAms"
    
    ##Create new cloud service
    $destcloudsvc = "danNewSvcAms" 
    New-AzureService $destcloudsvc -Location $location 

#### 步驟 2 ︰ 提高資源上允許的失敗 <Optional>
在隸屬於 AlwaysOn 可用性群組的特定資源上有下列限制：可以在一段期間內發生的失敗次數，而叢集服務將嘗試在其中重新啟動資源群組。 儘管您正在逐步執行此程序，但還是建議您提高此限制， 因為，如果您不會手動進行容錯移轉，以及藉由關閉機器來觸發容錯移轉，就會更接近這個限制。

明智的做法是將容錯度加倍，若要在容錯移轉叢集管理員中執行這個動作，請移至 AlwaysOn 資源群組的 [屬性]：
 
![Appendix3][13]

將 [最大失敗數目] 變更為 6。 

#### 步驟 3 ︰ 新增 IP 位址資源的叢集群組 <Optional>

如果您只有一個適用於叢集群組的 IP 位址且已將此位址指派給雲端子網路，請注意，如果您不小心在該網路上使雲端中的所有叢集節點離線，則叢集 IP 資源和叢集網路名稱將無法變成上線狀態。 發生這個情況時，將阻止更新其他叢集資源。 

#### 步驟 4：DNS 設定

若要實作流暢的轉換，取決於使用和更新 DNS 的方法。
安裝 AlwaysOn 時，它會建立 Windows 叢集資源群組，如果您開啟容錯移轉叢集管理員，就會看見它至少具有三個資源，文件中所指的是下列這兩個資源：

- 虛擬網路名稱 (VNN) - 這是 DNS 名稱，當用戶端想要透過 AlwaysOn 連線到 SQL Server 時要連接的名稱。
- IP 位址資源 - 這是與 VNN 相關聯的 IP 位址，您可以擁有一個以上這類位址，而且在多站台設定中，您將針對每個站台/子網路擁有一個 IP 位址。

連線到 SQL Sevrer 時，SQL Sevrer 用戶端驅動程式將抓取與接聽程式相關聯的 DNS 記錄，並嘗試連線到每個與 AlwaysOn 相關聯的 IP 位址，我們將在下列內容中討論數個會影響這一點的因素。

與接聽程式名稱相關聯的並行 DNS 記錄數目不只取決於相關聯的 IP 位址數目，而且還取決於適用於 AlwaysON VNN 資源之容錯移轉叢集中的 ‘RegisterAllIpProviders’ 設定。

當您在 Azure 中部署 AlwaysOn 時，有不同的步驟來建立接聽程式和 IP 位址，您必須手動將 ‘RegisterAllIpProviders’ 設為 1，這與內部部署的 AlwaysOn 部署不同，因為它已經設為 1。 

如果 ‘RegisterAllIpProviders’ 是 0，則您只會在與接聽程式相關聯的 DNS 中看見一個 DNS 記錄：

![Appendix4][14]

如果 ‘RegisterAllIpProviders’ 是 1：
 
![Appendix5][15]

下列程式碼將傾印出 VNN 設定，並為您設定它，請注意，若要讓變更生效，您需要使 VNN 離線，並使它再度上線，使接聽程式離線會導致用戶端連線中斷。 

    ##Always On Listener Name
    $ListenerName = "Mylistener"
    ##Get AlwaysOn Network Name Settings
    Get-ClusterResource $ListenerName| Get-ClusterParameter
    ##Set RegisterAllProvidersIP 
    Get-ClusterResource $ListenerName| Set-ClusterParameter RegisterAllProvidersIP  1 

在後續的移轉步驟中，您需要使用參考負載平衡器的更新 IP 位址來更新 AlwaysOn 接聽程式，這將涉及 IP 位址資源的移除與新增。 更新 IP 之後，您需要確定新的 IP 位址已在 DNS 區域中更新，而且用戶端正在更新它們的本機 DNS 快取。 

如果您的用戶端位於不同的網路區段並參考不同的 DNS 伺服器，您需要考量移轉期間 DNS 區域傳輸會發生什麼事，因為應用程式重新連線時間至少會受到任何適用於接聽程式之新 IP 位址的區域傳輸時間所限制。 如果您受到此處所討論的時間所限制，就應該與您的 Windows 小組討論並測試強制執行增量區域傳輸，同時降低 DNS 主機記錄的存留時間，讓用戶端能夠更新。 如需詳細資訊，請參閱 [增量區域傳輸](https://technet.microsoft.com/library/cc958973.aspx) 和 [Start-dnsserverzonetransfer](https://technet.microsoft.com/library/jj649917.aspx)。

針對 Azure 中與 AlwaysOn 上接聽程式相關聯的 DNS 記錄，其適用的 TTL 預設是 1200 秒。 如果您在移轉期間受到時間限制，可能希望降低這個時間，以確保用戶端可以使用適用於接聽程式的更新 IP 位址來更新他們的 DNS。 您可以藉由傾印出 VNN 設定來查看並修改設定：

    $AGName = "myProductionAG"
    $ListenerName = "Mylistener"
    #Look at HostRecordTTL
    Get-ClusterResource $ListenerName| Get-ClusterParameter
    
    #Set HostRecordTTL Examples
    Get-ClusterResource $ListenerName| Set-ClusterParameter -Name "HostRecordTTL" 120 

請注意，‘HostRecordTTL’ 越低，所造成的 DNS 流量就越高。

##### 用戶端應用程式設定

如果您的 SQL 用戶端應用程式支援 .Net 4.5 SQLClient，則您可以使用 ‘MULTISUBNETFAILOVER=TRUE’ 關鍵字，若它允許在容錯移轉期間更快速連線到 SQL AlwaysOn 可用性群組，則建議套用這個設定。 它會平行列舉所有與 AlwaysOn 接聽程式相關聯的 IP 位址，並在容錯移轉期間更頻繁地執行 TCP 連線重試速度。

如需有關上述設定的詳細資訊，請參閱 [MultiSubnetFailover 關鍵字和相關聯的功能](https://msdn.microsoft.com/library/hh213080.aspx#MultiSubnetFailover)。 另請參閱 [高可用性、 嚴重損壞修復的 SqlClient 支援](https://msdn.microsoft.com/library/hh205662(v=vs.110).aspx)。

#### 步驟 5：叢集仲裁設定

如果您一次至少會取出一部要關閉 SQL Server，就應該修改叢集仲裁設定，如果使用的是含有 2 個節點的檔案共用見證 (FSW)，就應該設定仲裁以允許節點多數並利用動態投票，這樣就能讓單一節點維持常設狀態。


    Set-ClusterQuorum -NodeMajority  

如需管理和設定叢集仲裁的詳細資訊，請參閱 [設定和管理 Windows Server 2012 容錯移轉叢集中仲裁](https://technet.microsoft.com/library/jj612870.aspx)。

#### 步驟 6：擷取現有的端點和 ACL
    #GET Endpoint info
    Get-AzureVM -ServiceName $destcloudsvc -Name $vmNameToMigrate | Get-AzureEndpoint
    #GET ACL Rules for Each EP, this example is for the AlwaysOn Endpoint
    Get-AzureVM -ServiceName $destcloudsvc -Name $vmNameToMigrate | Get-AzureAclConfig -EndpointName "myAOEndPoint-LB"  

將這些項目儲存為文字檔。

#### 步驟 7：變更容錯移轉夥伴和複寫模式

如果您有 2 部以上的 SQL Server，就應該將另一個 DC 上或內部部署中另一個次要項目的容錯移轉變更為 [同步]，並使其成為自動容錯移轉夥伴 (AFP)，這樣一來您就能在進行變更的同時保留 HA。 您可以透過 TSQL 執行這個動作，或透過 SSMS 來修改：
 
![Appendix6][16]

#### 步驟 8：從雲端服務移除次要的 VM

您應該規劃為先移轉雲端次要節點，如果這是目前的主要節點，就應該初始手動容錯移轉。

    $vmNameToMigrate="dansqlams2"
    
    #Check machine status 
    Get-AzureVM -ServiceName $sourceSvc -Name $vmNameToMigrate 
    
    #Shutdown secondary VM
    Get-AzureVM -ServiceName $sourceSvc -Name $vmNameToMigrate | stop-AzureVM
    
    
    #Extract disk configuration
    
    ##Building Existing Data Disk Configuration
    $file = "C:\Azure Storage Testing\mydiskconfig_$vmNameToMigrate.csv"
    $datadisks = @(Get-AzureVM -ServiceName $sourceSvc -Name $vmNameToMigrate | Get-AzureDataDisk )
    Add-Content $file “lun, vhdname, hostcaching, disklabel, diskName”
    foreach ($disk in $datadisks)
    {
      $vhdname = $disk.MediaLink.AbsolutePath -creplace  "/vhds/"
      $disk.Lun, , $disk.HostCaching, $vhdname, $disk.DiskLabel,$disks.DiskName 
    # Write-Host "copying disk $disk"
    $adddisk = "{0},{1},{2},{3},{4}" -f $disk.Lun,$vhdname, $disk.HostCaching, $disk.DiskLabel, $disk.DiskName 
    $adddisk | add-content -path $file
    }
    
    #Get OS Disk
    $osdisks = Get-AzureVM -ServiceName $sourceSvc -Name $vmNameToMigrate | Get-AzureOSDisk ## | select -ExpandProperty MediaLink
    $osvhdname = $osdisks.MediaLink.AbsolutePath -creplace  "/vhds/"
    $osdisks.OS, $osdisks.HostCaching, $osvhdname, $osdisks.DiskLabel, $osdisks.DiskName
    $addosdisk = "{0},{1},{2},{3},{4}" -f $osdisks.OS,$osvhdname, $osdisks.HostCaching, $osdisks.Disklabel , $osdisks.DiskName 
    $addosdisk | add-content -path $file
    
    #Import disk config
    $diskobjects  = Import-CSV $file
    
    #Check disk config, make sure below returns the disks associated with the VM
    $diskobjects 
    
    #Identify OS Disk
    $osdiskimport = $diskobjects | where {$_.lun -eq "Windows"}
    $osdiskforbuild = $osdiskimport.diskName
    
    #Check machibe is off
    Get-AzureVM -ServiceName $sourceSvc -Name  $vmNameToMigrate 
    
    #Drop machine and rebuild to new cls
    Remove-AzureVM -ServiceName $sourceSvc -Name $vmNameToMigrate 

#### 步驟 9：在 CSV 檔案中變更磁碟快取設定，然後儲存

針對資料磁碟區，應該這些設定設為 READONLY。

針對 TLOG 磁碟區，應該這些設定設為 NONE。
 
![Appendix7][17]

#### 步驟 10：複製 VHDS
    #Ensure you have created the container for these:
    $containerName = 'vhds'
    
    #Create container
    New-AzureStorageContainer -Name $containerName -Context $xioContext 
    
    ####DISK COPYING####
    #Get disks from csv, get settings for each VHDs and copy to Premium Storage accoun
    ForEach ($disk in $diskobjects)
       {
       $lun = $disk.Lun
       $vhdname = $disk.vhdname
       $cacheoption = $disk.HostCaching
       $disklabel = $disk.DiskLabel
       $diskName = $disk.DiskName
       Write-Host "Copying Disk Lun $lun, Label : $disklabel, VHD : $vhdname has cache setting : $cacheoption"
    
       #Start async copy
       Start-AzureStorageBlobCopy -srcUri "https://$origstorageaccountname.blob.core.windows.net/vhds/$vhdname" `
    -SrcContext $origContext `
    -DestContainer $containerName `
    -DestBlob $vhdname `
    -DestContext $xioContext
       }
     
 

您可以檢查 VHD 對高階儲存體帳戶的複製狀態：

    ForEach ($disk in $diskobjects)
       {
       $lun = $disk.Lun
       $vhdname = $disk.vhdname
       $cacheoption = $disk.HostCaching
       $disklabel = $disk.DiskLabel
       $diskName = $disk.DiskName
      
       $copystate = Get-AzureStorageBlobCopyState -Blob $vhdname -Container $containerName -Context $xioContext
    Write-Host "Copying Disk Lun $lun, Label : $disklabel, VHD : $vhdname, STATUS = " $copystate.Status 
       }
 
![Appendix8][18]

繼續等待，直到這所有設定都記錄為成功為止。

適用於個別 Blob 的資訊：

    Get-AzureStorageBlobCopyState -Blob "blobname.vhd" -Container $containerName -Context $xioContext 

#### 步驟 11：註冊作業系統磁碟

    #Change storage account
    Set-AzureSubscription -SubscriptionName $mysubscription -CurrentStorageAccount $newxiostorageaccountname 
    Select-AzureSubscription -SubscriptionName $mysubscription -Current 
    
    #Register OS disk
    $osdiskimport = $diskobjects | where {$_.lun -eq "Windows"}
    $osvhd = $osdiskimport.vhdname
    $osdiskforbuild = $osdiskimport.diskName
    
    #Registering OS disk, but as XIO disk
    $xioDiskName = $osdiskforbuild + "xio"
    Add-AzureDisk -DiskName $xioDiskName -MediaLocation  "https://$newxiostorageaccountname.blob.core.windows.net/vhds/$osvhd"  -Label "BootDisk" -OS "Windows"

#### 步驟 12：將次要項目匯入新的雲端服務

下列程式碼也會使用新增的選項，而您可以在此處匯入機器並使用可保留的 VIP。

    #Build VM Config
    $ipaddr = "192.168.0.5"
    #Remember to change to XIO
    $newInstanceSize = "Standard_DS13"
    $subnet = "SQL"
    
    #Create new Avaiability Set 
    $availabilitySet = "cloudmigAVAMS"
    
    #build machine config into object
    $vmConfig = New-AzureVMConfig -Name $vmNameToMigrate -InstanceSize $newInstanceSize -DiskName $xioDiskName -AvailabilitySetName $availabilitySet  ` | Add-AzureProvisioningConfig -Windows ` | Set-AzureSubnet -SubnetNames $subnet | Set-AzureStaticVNetIP -IPAddress $ipaddr
    
    #Reload disk config
    $diskobjects  = Import-CSV $file
    $datadiskimport = $diskobjects | where {$_.lun -ne "Windows"}
    
    ForEach ( $attachdatadisk in $datadiskimport) 
       {
    $label = $attachdatadisk.disklabel
    $lunNo = $attachdatadisk.lun
    $hostcach = $attachdatadisk.hostcaching
    $datadiskforbuild = $attachdatadisk.diskName
    $vhdname = $attachdatadisk.vhdname
    
    ###Attaching disks to a VM during a deploy to a new cloud service and new storage account is different from just attaching VHDs to just a redeploy in a new cloud service
    $vmConfig | Add-AzureDataDisk -ImportFrom -MediaLocation "https://$newxiostorageaccountname.blob.core.windows.net/vhds/$vhdname" -LUN $lunNo -HostCaching $hostcach -DiskLabel $label
    
    }
    
    #Create VM
    $vmConfig  | New-AzureVM –ServiceName $destcloudsvc –Location $location -VNetName $vnet ## Optional (-ReservedIPName $reservedVIPName)
 
#### 步驟 13：在新的雲端服務上建立 ILB，新增負載平衡的端點和 ACL
    #Check for existing ILB
    GET-AzureInternalLoadBalancer -ServiceName $destcloudsvc
    
    $ilb="sqlIntIlbDest"
    $subnet = "SQL"
    $IP="192.168.0.25"
    Add-AzureInternalLoadBalancer -ServiceName $destcloudsvc -InternalLoadBalancerName $ilb –SubnetName $subnet –StaticVNetIPAddress $IP
    
    #Endpoints
    $epname="sqlIntEP"
    $prot="tcp"
    $locport=1433
    $pubport=1433
    Get-AzureVM –ServiceName $destcloudsvc –Name $vmNameToMigrate  | Add-AzureEndpoint -Name $epname -Protocol $prot -LocalPort $locport -PublicPort $pubport -ProbePort 59999 -ProbeIntervalInSeconds 5 -ProbeTimeoutInSeconds 11  -ProbeProtocol "TCP" -InternalLoadBalancerName $ilb -LBSetName $ilb -DirectServerReturn $true | Update-AzureVM
    
    #SET Azure ACLs or Network Security Groups & Windows FWs 
     
    #http://msdn.microsoft.com/library/azure/dn495192.aspx
    
    ####WAIT FOR FULL AlwaysOn RESYNCRONISATION!!!!!!!!!#####

####步驟 14：更新 AlwaysOn 
    #在叢集節點上執行的程式碼
    $ClusterNetworkNameAmsterdam ="叢集網路 2"# azure 叢集子網路的網路名稱
    $newCloudServiceIPAmsterdam ="192.168.0.25"# 雲端服務的 IP 位址 
    
    $AGName = "myProductionAG"
    $ListenerName = "Mylistener" 
    
    
    Add-ClusterResource "IP Address $newCloudServiceIPAmsterdam" -ResourceType "IP Address" -Group $AGName -ErrorAction Stop |  Set-ClusterParameter -Multiple @{"Address"="$newCloudServiceIPAmsterdam";"ProbePort"="59999";SubnetMask="255.255.255.255";"Network"=$ClusterNetworkNameAmsterdam;"OverrideAddressMatch"=1;"EnableDhcp"=0} -ErrorAction Stop 
    
    #set dependancy and NETBIOS, then remove old IP address
    
    #set NETBIOS, then remove old IP address
    Get-ClusterGroup $AGName | Get-ClusterResource -Name "IP Address $newCloudServiceIPAmsterdam" | Set-ClusterParameter -Name EnableNetBIOS -Value 0 
     
    #set dependency to Listener (OR Dependency) and delete previous IP Address resource that references:
    
    #Make sure no static records in DNS 
    
![Appendix9][19]

現在，移除舊的雲端服務 IP 位址。

![Appendix10][20]
 
#### 步驟 15：DNS 更新檢查

您現在應該檢查 SQL Server 用戶端網路上的 DNS 伺服器，並確定已針對新增的 IP 位址將叢集新增為額外的主機記錄。 如果這些 DNS 伺服器尚未更新，請考慮強制執行 DNS 區域傳輸，並確保子網路中的用戶端能夠解析這兩個 AlwaysOn IP 位址，這樣一來，您就不需要等待進行自動 DNS 複寫。 

#### 步驟 16：重新設定 AlwaysOn 

此時，您要等待已移轉之節點的次要項目與內部部署節點重新進行完整的同步處理，並切換到同步複寫節點，使它成為 AFP。  

#### 步驟 17：移轉第二個節點
    $vmNameToMigrate="dansqlams1"
    
    Get-AzureVM -ServiceName $sourceSvc -Name $vmNameToMigrate 
    
    #Get endpoint information
    $endpoint = Get-AzureVM -ServiceName $sourceSvc  -Name $vmNameToMigrate | Get-AzureEndpoint
    
    #Shutdown VM
    Get-AzureVM -ServiceName $sourceSvc -Name $vmNameToMigrate | stop-AzureVM
    
    #Get disk config
    
    #Building Existing Data Disk Configuration
    $file = "C:\Azure Storage Testing\mydiskconfig_$vmNameToMigrate.csv"
    $datadisks = @(Get-AzureVM -ServiceName $sourceSvc -Name $vmNameToMigrate | Get-AzureDataDisk )
    Add-Content $file “lun, vhdname, hostcaching, disklabel, diskName”
    foreach ($disk in $datadisks)
    {
      $vhdname = $disk.MediaLink.AbsolutePath -creplace  "/vhds/"
      $disk.Lun, , $disk.HostCaching, $vhdname, $disk.DiskLabel,$disks.DiskName 
    # Write-Host "copying disk $disk"
    $adddisk = "{0},{1},{2},{3},{4}" -f $disk.Lun,$vhdname, $disk.HostCaching, $disk.DiskLabel, $disk.DiskName 
    $adddisk | add-content -path $file
    }
    
    #Get OS Disk
    $osdisks = Get-AzureVM -ServiceName $sourceSvc -Name $vmNameToMigrate | Get-AzureOSDisk ## | select -ExpandProperty MediaLink
    $osvhdname = $osdisks.MediaLink.AbsolutePath -creplace  "/vhds/"
    $osdisks.OS, $osdisks.HostCaching, $osvhdname, $osdisks.DiskLabel, $osdisks.DiskName
    $addosdisk = "{0},{1},{2},{3},{4}" -f $osdisks.OS,$osvhdname, $osdisks.HostCaching, $osdisks.Disklabel , $osdisks.DiskName 
    $addosdisk | add-content -path $file
    
    #Import disk config
    $diskobjects  = Import-CSV $file
    
    #Check disk configuration
    $diskobjects 
    
    #Identify OS Disk
    $osdiskimport = $diskobjects | where {$_.lun -eq "Windows"}
    $osdiskforbuild = $osdiskimport.diskName
    
    #Check machine is off
    Get-AzureVM -ServiceName $sourceSvc -Name  $vmNameToMigrate 
    
    #Drop machine and rebuild to new cls
    Remove-AzureVM -ServiceName $sourceSvc -Name $vmNameToMigrate 

#### 步驟 18：在 CSV 檔案中變更磁碟快取設定，然後儲存

針對資料磁碟區，應該這些設定設為 READONLY。

針對 TLOG 磁碟區，應該這些設定設為 NONE。
 
![Appendix11][21]

#### 步驟 19：為次要節點建立新的獨立儲存體帳戶
    $newxiostorageaccountnamenode2 = "danspremsams2" 
    New-AzureStorageAccount -StorageAccountName $newxiostorageaccountnamenode2 -Location $location -Type "Premium_LRS"  
    
    #Reset the storage account src if node 1 in a different storage account
    $origstorageaccountname2nd = "danstdams2"
    
    #Generate storage keys for later
    $xiostoragenode2 = Get-AzureStorageKey -StorageAccountName $newxiostorageaccountnamenode2
    
    #Generate storage acc contexts
    $xioContextnode2 = New-AzureStorageContext  –StorageAccountName $newxiostorageaccountnamenode2 -StorageAccountKey $xiostoragenode2.Primary  
    
    #Set up subscription and default storage account
    Set-AzureSubscription -SubscriptionName $mysubscription -CurrentStorageAccount $newxiostorageaccountnamenode2
    Select-AzureSubscription -SubscriptionName $mysubscription -Current 

#### 步驟 20：複製 VHDS
    #Ensure you have created the container for these:
    $containerName = 'vhds'
    
    #Create container
    New-AzureStorageContainer -Name $containerName -Context $xioContextnode2  
    
    ####DISK COPYING####
    ##get disks from csv, get settings for each VHDs and copy to Premium Storage accoun
    ForEach ($disk in $diskobjects)
       {
       $lun = $disk.Lun
       $vhdname = $disk.vhdname
       $cacheoption = $disk.HostCaching
       $disklabel = $disk.DiskLabel
       $diskName = $disk.DiskName
       Write-Host "Copying Disk Lun $lun, Label : $disklabel, VHD : $vhdname has cache setting : $cacheoption"
      
       #Start async copy
       Start-AzureStorageBlobCopy -srcUri "https://$origstorageaccountname2nd.blob.core.windows.net/vhds/$vhdname" `
        -SrcContext $origContext `
        -DestContainer $containerName `
        -DestBlob $vhdname `
        -DestContext $xioContextnode2
       }
    
    #Check for copy progress
    
    #check induvidual blob status
    Get-AzureStorageBlobCopyState -Blob "danRegSvcAms-dansqlams1-2014-07-03.vhd" -Container $containerName -Context $xioContext
     
    
您可以檢查所有 Vhd 的 VHD 複製狀態 ︰
    ForEach ($diskobjects $disk)
       {
       $lun = $disk。Lun
       $vhdname $disk.vhdname
       $cacheoption = $disk。Set-azuredatadisk
       $disklabel = $disk。DiskLabel
       $diskName = $disk。DiskName
      
       $copystate = Get-AzureStorageBlobCopyState -Blob $vhdname -Container $containerName -Context $xioContextnode2
    Write-Host "Copying Disk Lun $lun, Label : $disklabel, VHD : $vhdname, STATUS = " $copystate.Status 
       }
     
![Appendix12][22]

繼續等待，直到這所有設定都記錄為成功為止。

適用於個別 Blob 的資訊：
    #檢查檢查個別 blob 狀態
    Get-azurestorageblobcopystate-Blob"Danregsvcams-dansqlams1-dansqlams1-2014年-07-03.vhd"-容器 $containerName-Context $xioContextnode2

#### 步驟 21：註冊作業系統磁碟
    #change storage account to the new XIO storage account
    Set-AzureSubscription -SubscriptionName $mysubscription -CurrentStorageAccount $newxiostorageaccountnamenode2
    Select-AzureSubscription -SubscriptionName $mysubscription -Current 
    
    #Register OS disk
    $osdiskimport = $diskobjects | where {$_.lun -eq "Windows"}
    $osvhd = $osdiskimport.vhdname
    $osdiskforbuild = $osdiskimport.diskName
    
    #Registering OS disk, but as XIO disk
    $xioDiskName = $osdiskforbuild + "xio"
    Add-AzureDisk -DiskName $xioDiskName -MediaLocation  "https://$newxiostorageaccountnamenode2.blob.core.windows.net/vhds/$osvhd"  -Label "BootDisk" -OS "Windows" 
    
    #Build VM Config
    $ipaddr = "192.168.0.4"
    $newInstanceSize = "Standard_DS13"
    
    #Join to existing Avaiability Set 
    
    #Build machine config into object
    $vmConfig = New-AzureVMConfig -Name $vmNameToMigrate -InstanceSize $newInstanceSize -DiskName $xioDiskName -AvailabilitySetName $availabilitySet  ` | Add-AzureProvisioningConfig -Windows ` | Set-AzureSubnet -SubnetNames $subnet | Set-AzureStaticVNetIP -IPAddress $ipaddr
    
    #Reload disk config
    $diskobjects  = Import-CSV $file
    $datadiskimport = $diskobjects | where {$_.lun -ne "Windows"}
    
    ForEach ( $attachdatadisk in $datadiskimport) 
       {
    $label = $attachdatadisk.disklabel
    $lunNo = $attachdatadisk.lun
    $hostcach = $attachdatadisk.hostcaching
    $datadiskforbuild = $attachdatadisk.diskName
    $vhdname = $attachdatadisk.vhdname
    
    ###This is different to just a straight cloud service change
    #note if you do not have a disk label the command below will fail, populate as required.
    $vmConfig | Add-AzureDataDisk -ImportFrom -MediaLocation "https://$newxiostorageaccountnamenode2.blob.core.windows.net/vhds/$vhdname" -LUN $lunNo -HostCaching $hostcach -DiskLabel $label
    
    }
    
    #Create VM
    $vmConfig  | New-AzureVM –ServiceName $destcloudsvc –Location $location -VNetName $vnet -Verbose

#### 步驟 22：新增負載平衡端點和 ACL
    #Endpoints
    $epname="sqlIntEP"
    $prot="tcp"
    $locport=1433
    $pubport=1433
    Get-AzureVM –ServiceName $destcloudsvc –Name $vmNameToMigrate  | Add-AzureEndpoint -Name $epname -Protocol $prot -LocalPort $locport -PublicPort $pubport -ProbePort 59999 -ProbeIntervalInSeconds 5 -ProbeTimeoutInSeconds 11  -ProbeProtocol "TCP" -InternalLoadBalancerName $ilb -LBSetName $ilb -DirectServerReturn $true | Update-AzureVM
    
    
    #STOP!!! CHECK in the Azure classic portal or Machine Endpoints through powershell that these Endpoints are created!
    
    #SET ACLs or Azure Network Security Groups & Windows FWs 
     
    #http://msdn.microsoft.com/library/azure/dn495192.aspx

#### 步驟 23：測試容錯移轉

您現在應該讓移轉的節點與內部部署的 AlwaysOn 節點進行同步，使它處於同步複寫模式，並且繼續等待，直到它完成同步為止。 然後從內部部署容錯移轉到第一個移轉的節點，其為 AFP。 一旦該節點開始運作之後，請將最後一個移轉的節點變更為 AFP。

您應該在所有節點之間測試容錯移轉，並且完整執行混亂測試，以確保容錯移轉會如預期般及時執行。

#### 步驟 24：回復叢集仲裁設定 / DNS TTL / 容錯移轉夥伴 / 同步設定 
##### 在同一個子網路上新增 IP 位址資源

如果您只有 2 部 SQL Server 且想要將它們移轉到新的雲端服務，但卻想讓它們保留於同一個子網路中，則可在您刪除原始的 AlwaysOn IP 位址並加入新的 IP 位址時，避免使接聽程式離線。 如果您想要將 VM 移轉到其他子網路，就不需要執行這個動作，因為將有其他叢集網路會參考該子網路。

如果您在容錯移轉現有的主要項目之前，使移轉的次要項目上線，並新增適用於新雲端服務的新 IP 位址資源，就應該在叢集容錯移轉管理員內執行下列步驟：

若要新增 IP 位址，請參閱 [附錄](#appendix-migrating-a-multisite-alwayson-cluster-to-premium-storage), ，步驟 14。

1. 針對目前的 IP 位址資源，將 [可能的擁有者] 變更為「現有的主要 SQL Server」，在下列範例中為 ‘dansqlams4’：

    ![Appendix13][23]

1. 針對新的 IP 位址資源變更可能的擁有者為 「 已移轉次要 SQL Server 」，在下列範例中，'dansqlams5':

    ![Appendix14][24]

1. 完成此設定後就能進行容錯移轉，並且應該在移轉最後一個節點時編輯 [可能的擁有者]，如此便能新增該節點成為 [可能的擁有者]：

    ![Appendix15][25]

## 其他資源
- [Azure 高階儲存體](../storage-premium-storage-preview-portal.md)
- [虛擬機器](http://azure.microsoft.com/services/virtual-machines/)
- [Azure 虛擬機器中的 SQL Server](virtual-machines-sql-server-infrastructure-services.md)

<!-- IMAGES -->
[1]: ./media/virtual-machines-sql-server-use-premium-storage/1_VNET_Portal.png
[2]: ./media/virtual-machines-sql-server-use-premium-storage/2_Diskname_Lun.png
[3]: ./media/virtual-machines-sql-server-use-premium-storage/3_Virtual_Disk_Properties.png
[4]: ./media/virtual-machines-sql-server-use-premium-storage/4_Virtual_Disk_Properties_Details.png
[5]: ./media/virtual-machines-sql-server-use-premium-storage/5_Get_Storage_Pool.png
[6]: ./media/virtual-machines-sql-server-use-premium-storage/6_Deployments_Always_On.png
[7]: ./media/virtual-machines-sql-server-use-premium-storage/7_Add_More_Secondaries.png
[8]: ./media/virtual-machines-sql-server-use-premium-storage/8_Use_Existing_Secondary_Single_Site.png
[9]: ./media/virtual-machines-sql-server-use-premium-storage/9_Use_Existing_Secondary_Multi_Site.png
[10]: ./media/virtual-machines-sql-server-use-premium-storage/9_Use_Existing_Secondary_Multi_Site_b.png
[11]: ./media/virtual-machines-sql-server-use-premium-storage/10_Appendix_01.png
[12]: ./media/virtual-machines-sql-server-use-premium-storage/10_Appendix_02.png
[13]: ./media/virtual-machines-sql-server-use-premium-storage/10_Appendix_03.png
[14]: ./media/virtual-machines-sql-server-use-premium-storage/10_Appendix_04.png
[15]: ./media/virtual-machines-sql-server-use-premium-storage/10_Appendix_05.png
[16]: ./media/virtual-machines-sql-server-use-premium-storage/10_Appendix_06.png
[17]: ./media/virtual-machines-sql-server-use-premium-storage/10_Appendix_07.png
[18]: ./media/virtual-machines-sql-server-use-premium-storage/10_Appendix_08.png
[19]: ./media/virtual-machines-sql-server-use-premium-storage/10_Appendix_09.png
[20]: ./media/virtual-machines-sql-server-use-premium-storage/10_Appendix_10.png
[21]: ./media/virtual-machines-sql-server-use-premium-storage/10_Appendix_11.png
[22]: ./media/virtual-machines-sql-server-use-premium-storage/10_Appendix_12.png
[23]: ./media/virtual-machines-sql-server-use-premium-storage/10_Appendix_13.png
[24]: ./media/virtual-machines-sql-server-use-premium-storage/10_Appendix_14.png
[25]: ./media/virtual-machines-sql-server-use-premium-storage/10_Appendix_15.png
 

