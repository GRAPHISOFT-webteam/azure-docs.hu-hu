<properties
    pageTitle="SharePoint Server 2013 伺服器陣列第 2 階段 | Microsoft Azure"
    description="在 Azure SharePoint Server 2013 伺服器陣列第 2 階段中，建立並設定兩個 Active Directory 複本網域控制站。"
    documentationCenter=""
    services="virtual-machines"
    authors="JoeDavies-MSFT"
    manager="timlt"
    editor=""
    tags="azure-resource-manager"/>

<tags
    ms.service="virtual-machines"
    ms.workload="infrastructure-services"
    ms.tgt_pltfrm="Windows"
    ms.devlang="na"
    ms.topic="article"
    ms.date="12/11/2015"
    ms.author="josephd"/>

# SharePoint 內部網路伺服器陣列工作負載第 2 階段：設定網域控制站

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-rm-include.md)] 傳統部署模型。

在 Azure 基礎結構服務內，使用 SQL Server AlwaysOn 可用性群組部署內部網路專用的 SharePoint 2013 伺服器陣列的這個階段中，您需要在 Azure 虛擬網路中設定兩個網域控制站。 此後即可在 Azure 虛擬網路中驗證 SharePoint 伺服器陣列資源的用戶端 Web 要求，而不是透過內部部署網路的站對站 VPN 或 Azure ExpressRoute 連線傳送該驗證流量。

您必須先完成這個階段才能前往 [第 3 階段](virtual-machines-workload-intranet-sharepoint-phase3.md)。 請參閱 [使用 SQL Server AlwaysOn 可用性群組在 Azure 中部署 SharePoint](virtual-machines-workload-intranet-sharepoint-overview.md) 的所有階段。

## 在 Azure 中建立網域控制站虛擬機器

首先，您必須填寫 **虛擬機器名稱** 資料行的資料表 M，修改虛擬機器大小，視需要在 **最小大小** 資料行。  

項目 | 虛擬機器名稱 | 資源庫映像 | 最小大小
--- | --- | --- | ---
1. | ______________ (第一個網域控制站，範例 DC1) | Windows Server 2012 R2 Datacenter | Standard_A2
2. | ______________ (第二個網域控制站，範例 DC2) | Windows Server 2012 R2 Datacenter | Standard_A2
3. | ______________ (第一部 SQL 電腦， 範例 SQL1) | Microsoft SQL Server 2014 Enterprise – Windows Server 2012 R2 | Standard_A5
4. | ______________ (第二部 SQL 電腦， 範例 SQL2) | Microsoft SQL Server 2014 Enterprise – Windows Server 2012 R2 | Standard_A5
5. | ______________ (叢集多數節點，範例 MN1) | Windows Server 2012 R2 Datacenter | Standard_A1
6. | ______________ (第一部 SharePoint 應用程式伺服器，範例 APP1) | Microsoft SharePoint Server 2013 試用版 – Windows Server 2012 R2 | Standard_A4
7. | ______________ (第二部 SharePoint 應用程式伺服器，範例 APP2) | Microsoft SharePoint Server 2013 試用版 – Windows Server 2012 R2 | Standard_A4
8. | ______________ (第一部 SharePoint 網頁伺服器，範例 WEB1) | Microsoft SharePoint Server 2013 試用版 – Windows Server 2012 R2 | Standard_A4
9. | ______________ (第二部 SharePoint 網頁伺服器，範例 WEB2) | Microsoft SharePoint Server 2013 試用版 – Windows Server 2012 R2 | Standard_A4

**資料表 M – SharePoint 2013 內部網路伺服器陣列在 Azure 中的虛擬機器**

虛擬機器大小的完整清單，請參閱 [虛擬機器的大小](virtual-machines-size-specs.md)。

使用下列的 Azure PowerShell 命令區塊為上述兩個網域控制站建立虛擬機器。 指定變數值，移除 < 和 > 字元。 請注意，此 Azure PowerShell 命令集使用下方的值：

- 資料表 M，適用於虛擬機器
- 資料表 V，適用於虛擬網路設定
- 資料表 S，適用於子網路
- 資料表 ST，適用於儲存體帳戶
- 資料表 A，適用於可用性設定組

還記得您定義資料表 V、 S、 ST 和在 [第 1 階段 ︰ 設定 Azure](virtual-machines-workload-intranet-sharepoint-phase1.md)。

> [AZURE.NOTE] 下列命令會將使用 Azure PowerShell 1.0 和更新版本。 如需詳細資訊，請參閱 [Azure PowerShell 1.0](https://azure.microsoft.com/blog/azps-1-0/)。

當您提供所有適當值後，在 Azure PowerShell 提示中執行結果區塊。

    # Set up key variables
    $rgName="<resource group name>"
    $locName="<Azure location of your resource group>"
    $saName="<Table ST – Item 1 – Storage account name column>"
    $vnetName="<Table V – Item 1 – Value column>"
    $avName="<Table A – Item 1 – Availability set name column>"
    
    # Create the first domain controller
    $vmName="<Table M – Item 1 - Virtual machine name column>"
    $vmSize="<Table M – Item 1 - Minimum size column>"
    $staticIP="<Table V – Item 6 - Value column>"
    $vnet=Get-AzureRMVirtualNetwork -Name $vnetName -ResourceGroupName $rgName
    $nic=New-AzureRMNetworkInterface -Name ($vmName +"-NIC") -ResourceGroupName $rgName -Location $locName -SubnetId $vnet.Subnets[1].Id -PrivateIpAddress $staticIP
    $avSet=Get-AzureRMAvailabilitySet –Name $avName –ResourceGroupName $rgName 
    $vm=New-AzureRMVMConfig -VMName $vmName -VMSize $vmSize -AvailabilitySetId $avset.Id
    
    $diskSize=<size of the extra disk for AD DS data in GB>
    $storageAcc=Get-AzureRMStorageAccount -ResourceGroupName $rgName -Name $saName
    $vhdURI=$storageAcc.PrimaryEndpoints.Blob.ToString() + "vhds/" + $vmName + "-ADDSDisk.vhd"
    Add-AzureRMVMDataDisk -VM $vm -Name "ADDSData" -DiskSizeInGB $diskSize -VhdUri $vhdURI  -CreateOption empty
    
    $cred=Get-Credential -Message "Type the name and password of the local administrator account for the first domain controller." 
    $vm=Set-AzureRMVMOperatingSystem -VM $vm -Windows -ComputerName $vmName -Credential $cred -ProvisionVMAgent -EnableAutoUpdate
    $vm=Set-AzureRMVMSourceImage -VM $vm -PublisherName MicrosoftWindowsServer -Offer WindowsServer -Skus 2012-R2-Datacenter -Version "latest"
    $vm=Add-AzureRMVMNetworkInterface -VM $vm -Id $nic.Id
    $storageAcc=Get-AzureRMStorageAccount -ResourceGroupName $rgName -Name $saName
    $osDiskUri=$storageAcc.PrimaryEndpoints.Blob.ToString() + "vhds/" + $vmName + "-OSDisk.vhd"
    $vm=Set-AzureRMVMOSDisk -VM $vm -Name "OSDisk" -VhdUri $osDiskUri -CreateOption fromImage
    New-AzureRMVM -ResourceGroupName $rgName -Location $locName -VM $vm
    
    # Create the second domain controller
    $vmName="<Table M – Item 2 - Virtual machine name column>"
    $vmSize="<Table M – Item 2 - Minimum size column>"
    $staticIP="<Table V – Item 7 - Value column>"
    $vnet=Get-AzureRMVirtualNetwork -Name $vnetName -ResourceGroupName $rgName
    $nic=New-AzureRMNetworkInterface -Name ($vmName +"-NIC") -ResourceGroupName $rgName -Location $locName -SubnetId $vnet.Subnets[1].Id -PrivateIpAddress $staticIP
    $avSet=Get-AzureRMAvailabilitySet –Name $avName –ResourceGroupName $rgName 
    $vm=New-AzureRMVMConfig -VMName $vmName -VMSize $vmSize -AvailabilitySetId $avset.Id
    
    $diskSize=<size of the extra disk for AD DS data in GB>
    $storageAcc=Get-AzureRMStorageAccount -ResourceGroupName $rgName -Name $saName
    $vhdURI=$storageAcc.PrimaryEndpoints.Blob.ToString() + "vhds/" + $vmName + "-ADDSDisk.vhd"
    Add-AzureRMVMDataDisk -VM $vm -Name "ADDSData" -DiskSizeInGB $diskSize -VhdUri $vhdURI  -CreateOption empty
    
    $cred=Get-Credential -Message "Type the name and password of the local administrator account for the second domain controller." 
    $vm=Set-AzureRMVMOperatingSystem -VM $vm -Windows -ComputerName $vmName -Credential $cred -ProvisionVMAgent -EnableAutoUpdate
    $vm=Set-AzureRMVMSourceImage -VM $vm -PublisherName MicrosoftWindowsServer -Offer WindowsServer -Skus 2012-R2-Datacenter -Version "latest"
    $vm=Add-AzureRMVMNetworkInterface -VM $vm -Id $nic.Id
    $storageAcc=Get-AzureRMStorageAccount -ResourceGroupName $rgName -Name $saName
    $osDiskUri=$storageAcc.PrimaryEndpoints.Blob.ToString() + "vhds/" + $vmName + "-OSDisk.vhd"
    $vm=Set-AzureRMVMOSDisk -VM $vm -Name "OSDisk" -VhdUri $osDiskUri -CreateOption fromImage
    New-AzureRMVM -ResourceGroupName $rgName -Location $locName -VM $vm

> [AZURE.NOTE] 因為這些虛擬機器的內部網路應用程式，他們不指派公用 IP 位址或 DNS 網域名稱標籤，會公開至網際網路。 不過，這也表示您無法從 Azure 入口網站連線到它們。  **連接** ] 按鈕將無法在檢視虛擬機器的內容時。 使用「遠端桌面連線」配件或其他遠端桌面工具，來使用虛擬機器的私人 IP 位址或內部網路 DNS 名稱連接該虛擬機器。

## 設定第一個網域控制站

使用您選擇的遠端桌面用戶端，並建立第一個網域控制站虛擬機器的遠端桌面連接。 使用其內部網路 DNS 或電腦名稱，以及本機系統管理員帳戶的認證。

接下来，您需要在第一個網域控制站新增額外的資料磁碟。

### <a id="datadisk"></a>初始化空磁碟

1.  在左窗格的 [伺服器管理員中，按一下 [ **檔案和存放服務**, ，然後按一下 [ **磁碟**。
2.  在 [內容] 窗格中，在 **磁碟** 群組中，按一下 [磁碟 **2** (與 **分割** 設為 **未知**)。
3.  按一下 [ **工作**, ，然後按一下 [ **新的磁碟區**。
4.  前上開始新的磁碟區精靈] 頁面，按一下 **下一步**。
5.  在 [選取伺服器和磁碟] 頁面上，按一下 [ **磁碟 2**, ，然後按一下 [ **下一步**。 出現提示時，按一下 [確定]。
6.  在 [指定大小的磁碟區] 頁面中，按一下 [ **下一步**。
7.  在 [指派成磁碟機代號或資料夾] 頁面中，按一下 [ **下一步**。
8.  在 [選取檔案系統設定] 頁面中，按一下 [ **下一步**。
9.  在 [確認選取項目] 頁面中，按一下 [ **建立**。
10. 完成時，按一下 **關閉**。

接下來，測試第一個網域控制站對組織網路中各個位置的連線能力。

### <a id="testconn"></a>測試連線能力

1.  從桌面開啟 Windows PowerShell 提示。
2.  使用 **ping** 命令來 ping 名稱和 IP 位址，您的組織網路上的資源。

此程序可確保 DNS 名稱解析正常運作 (即虛擬機器已使用內部部署 DNS 伺服器正確設定)，且此封包可在跨單位虛擬網路內往來傳送。 如果這個基本測試失敗，請連絡您的 IT 部門以疑難排解 DNS 名稱解析和封包傳遞問題。

接下來，從第一個網域控制站的 Windows PowerShell 命令提示字元執行下列命令：

    $domname="<DNS domain name of the domain for which this computer will be a domain controller>"
    Install-WindowsFeature AD-Domain-Services -IncludeManagementTools
    Install-ADDSDomainController -InstallDns –DomainName $domname  -DatabasePath "F:\NTDS" -SysvolPath "F:\SYSVOL" -LogPath "F:\Logs"

系統將提示您提供網域系統管理員帳戶的認證。 電腦會重新啟動。

## 設定第二個網域控制站

使用您選擇的遠端桌面用戶端，並建立第二個網域控制站虛擬機器的遠端桌面連接。 使用其內部網路 DNS 或電腦名稱，以及本機系統管理員帳戶的認證。

接下来，您需要在第二個網域控制站新增額外的資料磁碟。 請參閱 [初始化空磁碟程序](#datadisk)。

接下來，從第二個網域控制站的 Windows PowerShell 提示執行下列命令：

    $domname="<DNS domain name of the domain for which this computer will be a domain controller>"
    Install-WindowsFeature AD-Domain-Services -IncludeManagementTools
    Install-ADDSDomainController -InstallDns –DomainName $domname  -DatabasePath "F:\NTDS" -SysvolPath "F:\SYSVOL" -LogPath "F:\Logs"

系統將提示您提供網域系統管理員帳戶的認證。 電腦會重新啟動。

接下來，您需要為您的虛擬網路更新 DNS 伺服器，讓 Azure 將兩個新網域控制站的 IP 位址指派給虛擬機器，做為他們的 DNS 伺服器。 請注意，此程序會使用資料表 V (適用於虛擬網路設定) 和資料表 M (適用於虛擬機器) 的值。

1.  在 Azure 入口網站的左窗格中，按一下 [ **虛擬網路**, ，然後按一下 [虛擬網路 （資料表 V – 項目 1 – 值資料行） 的名稱。
2.  在 **設定** ] 窗格中，按一下 [ **DNS 伺服器**。
3.  在 **DNS 伺服器** ] 窗格中，輸入下列命令 ︰
    - 如 **主要 DNS 伺服器**︰ 資料表 V – 項目 6 – 值資料行
    - 如 **次要 DNS 伺服器**︰ 資料表 V – 項目 7 – 值資料行
4.  在 Azure 入口網站的左窗格中，按一下 [ **虛擬機器**。
5.  在 **虛擬機器] 窗格**, ，按一下第一個網域控制站 （資料表 M – 項目 1-虛擬機器名稱資料行） 的名稱。
6.  在虛擬機器的窗格中，按一下 [ **重新啟動**。
7.  第一個網域控制站啟動時，按一下第二個網域控制站名稱 **虛擬機器** 窗格 （資料表 M – 項目 2-虛擬機器名稱資料行）。
8.  在虛擬機器的窗格中，按一下 [ **重新啟動**。 等待第二個網域控制站啟動。

請注意，重新啟動兩個網域控制站後，他們才不會由內部部署 DNS 伺服器設定做為 DNS 伺服器。 由於他們本身都是 DNS 伺服器，因此當升級為網域控制站時，它們會自動由內部部署 DNS 伺服器設定做為 DNS 轉寄站。

接下來，我們需要建立 Active Directory 複寫站台，以確保 Azure 虛擬網路中的伺服器使用本機網域控制站。 使用網域系統管理員帳戶登入主要網域控制站，自系統管理員層級 Windows PowerShell 提示執行下列命令：

    $vnet="<Table V – Item 1 – Value column>"
    $vnetSpace="<Table V – Item 5 – Value column>"
    New-ADReplicationSite -Name $vnet 
    New-ADReplicationSubnet –Name $vnetSpace –Site $vnet

## 設定 SharePoint 伺服器陣列帳戶和權限

SharePoint 伺服器陣列需要下列使用者帳戶：

- sp_farm：管理 SharePoint 伺服器陣列的使用者帳戶。
- sp_farm_db：對 SQL Server 執行個體具有 sysadmin 權限的使用者帳戶。
- sp_install：具有安裝角色和功能所需之網域管理權限的使用者帳戶。
- sqlservice：可據以執行 SQL Server 執行個體的使用者帳戶。

接著，登入的任何電腦使用網域系統管理員帳戶網域的網域控制站的成員，請開啟系統管理員層級 Windows PowerShell 命令提示字元中，並執行這些命令 *一次一個*:

    New-ADUser -SamAccountName sp_farm -AccountPassword (read-host "Set user password" -assecurestring) -name "sp_farm" -enabled $true -PasswordNeverExpires $true -ChangePasswordAtLogon $false

    New-ADUser -SamAccountName sp_farm_db -AccountPassword (read-host "Set user password" -assecurestring) -name "sp_farm_db" -enabled $true -PasswordNeverExpires $true -ChangePasswordAtLogon $false

    New-ADUser -SamAccountName sp_install -AccountPassword (read-host "Set user password" -assecurestring) -name "sp_install" -enabled $true -PasswordNeverExpires $true -ChangePasswordAtLogon $false

    New-ADUser -SamAccountName sqlservice -AccountPassword (read-host "Set user password" -assecurestring) -name "sqlservice" -enabled $true -PasswordNeverExpires $true -ChangePasswordAtLogon $false

在執行每個命令時，系統都會提示您輸入密碼。 請記下這些帳戶名稱和密碼，並將其儲存在安全的位置。

接下来，執行下列步驟，為新的使用者帳戶新增其他帳戶屬性。

1.  從 [開始] 畫面中，輸入 **Active Directory 使用者**, ，然後按一下 [ **Active Directory 使用者和電腦**。
2.  在樹狀目錄窗格中，開啟您的網域，然後按一下 [ **使用者**。
3.  在 [內容] 窗格中，以滑鼠右鍵按一下 **sp_install**, ，然後按一下 [ **新增到群組**。
4.  在 **選取群組** 對話方塊中，輸入 **網域系統管理員**, ，然後按一下 [ **確定** 兩次。
5.  在對話方塊中，按一下 [ **檢視，按一下 [進階功能**。 這個選項可讓您在 [屬性] 視窗中看到 Active Directory 物件所有隱藏的容器以及隱藏的索引標籤。
6.  以滑鼠右鍵按一下您的網域名稱，然後按一下 [ **屬性**。
7.  在 **屬性** ] 對話方塊中，按一下 [ **安全性** 索引標籤，然後再按一下 **進階** ] 按鈕。
8.  在 **的進階安全性設定 <YourDomain>** ] 視窗中，按一下 [ **新增**。
9.  在 **權限項目 <YourDomain>** ] 視窗中，按一下 [ **選取一個主體**。
10. 在文字方塊中，輸入 **<YourDomain>\sp_install**, ，然後按一下 [ **確定**。
11. 選取 **允許** 的 **建立電腦物件**, ，然後按一下 [ **確定** 三次。

此處顯示已成功完成此階段設定，並以電腦名稱預留位置表示。

![](./media/virtual-machines-workload-intranet-sharepoint-phase2/workload-spsqlao_02.png)

## 後續步驟

- 使用 [第 3 階段](virtual-machines-workload-intranet-sharepoint-phase3.md) 繼續設定此工作負載。


