<properties
    pageTitle="SharePoint Server 2013 伺服器陣列第 3 階段 | Microsoft Azure"
    description="在 Azure SharePoint Server 2013 伺服器陣列第 3 階段中，建立電腦和 SQL Server 叢集，並啟用可用性群組。"
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

# SharePoint 內部網路伺服器陣列工作負載第 3 階段：設定 SQL Server 基礎結構

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-rm-include.md)] 傳統部署模型。

在 Azure 基礎結構服務內，使用 SQL Server AlwaysOn 可用性群組部署內部網路專用的 SharePoint 2013 的這個階段中，您需要設定兩部  SQL Server 電腦與叢集多數節點電腦，再將其併入 Windows Server 叢集。

您必須先完成這個階段才能前往 [第 4 階段](virtual-machines-workload-intranet-sharepoint-phase4.md)。 請參閱 [使用 SQL Server AlwaysOn 可用性群組在 Azure 中部署 SharePoint](virtual-machines-workload-intranet-sharepoint-overview.md) 的所有階段。

> [AZURE.NOTE] 這些指示使用 Azure 映像庫中的 SQL Server 映像，您需支付使用的 SQL Server 授權成本。 您也可在 Azure 中建立虛擬機器並安裝您自己的 SQL Server 授權，但您必須擁有軟體保證和授權機動性，才能在虛擬機器上使用 SQL Server 授權，包括 Azure 虛擬機器在內。 如需虛擬機器上安裝 SQL Server 的詳細資訊，請參閱 [SQL Server 安裝](https://msdn.microsoft.com/library/bb500469.aspx)。

## 在 Azure 中建立 SQL Server 叢集虛擬機器

SQL Server 虛擬機器共有兩部。 一部 SQL Server 包含可用性群組中主要資料庫複本。 另一部 SQL Server 包含次要備份複本。 提供的備份可確保高可用性。 另一部額外的虛擬機器是為了叢集多數節點而設。

使用下列方塊中的 PowerShell 命令為上述三個伺服器建立虛擬機器。 指定變數值，移除 < 和 > 字元。 請注意，此 PowerShell 命令集使用下表中的值：

- 資料表 M，適用於虛擬機器
- 資料表 V，適用於虛擬網路設定
- 資料表 S，適用於子網路
- 資料表 ST，適用於儲存體帳戶
- 資料表 A，適用於可用性設定組

還記得您定義中的資料表 M [第 2 階段: 設定網域控制站](virtual-machines-workload-intranet-sharepoint-phase2.md) 和資料表 V、 S、 ST 和在 [第 1 階段: 設定 Azure](virtual-machines-workload-intranet-sharepoint-phase1.md)。

> [AZURE.NOTE] 下列命令會將使用 Azure PowerShell 1.0 和更新版本。 如需詳細資訊，請參閱 [Azure PowerShell 1.0](https://azure.microsoft.com/blog/azps-1-0/)。

當您提供所有適當值後，在 Azure PowerShell 命令提示字元中執行結果區塊。


    # Set up key variables
    $rgName="<your resource group name>"
    $locName="<Azure location of your resource group>"
    $saName="<Table ST – Item 1 – Storage account name column>"
    $vnetName="<Table V – Item 1 – Value column>"
    $avName="<Table A – Item 2 – Availability set name column>"
    
    # Create the first SQL Server virtual machine
    $vmName="<Table M – Item 3 - Virtual machine name column>"
    $vmSize="<Table M – Item 3 - Minimum size column>"
    $vnet=Get-AzureRMVirtualNetwork -Name $vnetName -ResourceGroupName $rgName
    $nic=New-AzureRMNetworkInterface -Name ($vmName +"-NIC") -ResourceGroupName $rgName -Location $locName -SubnetId $vnet.Subnets[1].Id
    $avSet=Get-AzureRMAvailabilitySet –Name $avName –ResourceGroupName $rgName 
    $vm=New-AzureRMVMConfig -VMName $vmName -VMSize $vmSize -AvailabilitySetId $avset.Id
    
    $diskSize=<size of the extra disk for SQL data in GB>
    $diskLabel="<the label on the disk>"
    $storageAcc=Get-AzureRMStorageAccount -ResourceGroupName $rgName -Name $saName
    $vhdURI=$storageAcc.PrimaryEndpoints.Blob.ToString() + "vhds/" + $vmName + "-SQLDataDisk.vhd"
    Add-AzureRMVMDataDisk -VM $vm -Name $diskLabel -DiskSizeInGB $diskSize -VhdUri $vhdURI  -CreateOption empty
    
    $cred=Get-Credential -Message "Type the name and password of the local administrator account for the first SQL Server computer." 
    $vm=Set-AzureRMVMOperatingSystem -VM $vm -Windows -ComputerName $vmName -Credential $cred -ProvisionVMAgent -EnableAutoUpdate
    $vm=Set-AzureRMVMSourceImage -VM $vm -PublisherName MicrosoftSQLServer -Offer SQL2014-WS2012R2 -Skus Enterprise -Version "latest"
    $vm=Add-AzureRMVMNetworkInterface -VM $vm -Id $nic.Id
    $storageAcc=Get-AzureRMStorageAccount -ResourceGroupName $rgName -Name $saName
    $osDiskUri=$storageAcc.PrimaryEndpoints.Blob.ToString() + "vhds/" + $vmName + "-OSDisk.vhd"
    $vm=Set-AzureRMVMOSDisk -VM $vm -Name "OSDisk" -VhdUri $osDiskUri -CreateOption fromImage
    New-AzureRMVM -ResourceGroupName $rgName -Location $locName -VM $vm
    
    # Create the second SQL Server virtual machine
    $vmName="<Table M – Item 4 - Virtual machine name column>"
    $vmSize="<Table M – Item 4 - Minimum size column>"
    $nic=New-AzureRMNetworkInterface -Name ($vmName +"-NIC") -ResourceGroupName $rgName -Location $locName -SubnetId $vnet.Subnets[1].Id
    $vm=New-AzureRMVMConfig -VMName $vmName -VMSize $vmSize -AvailabilitySetId $avset.Id
    
    $diskSize=<size of the extra disk for SQL data in GB>
    $diskLabel="<the label on the disk>"
    $vhdURI=$storageAcc.PrimaryEndpoints.Blob.ToString() + "vhds/" + $vmName + "-ADDSDisk.vhd"
    Add-AzureRMVMDataDisk -VM $vm -Name $diskLabel -DiskSizeInGB $diskSize -VhdUri $vhdURI  -CreateOption empty
    
    $cred=Get-Credential -Message "Type the name and password of the local administrator account for the second SQL Server computer." 
    $vm=Set-AzureRMVMOperatingSystem -VM $vm -Windows -ComputerName $vmName -Credential $cred -ProvisionVMAgent -EnableAutoUpdate
    $vm=Set-AzureRMVMSourceImage -VM $vm -PublisherName MicrosoftSQLServer -Offer SQL2014-WS2012R2 -Skus Enterprise -Version "latest"
    $vm=Add-AzureRMVMNetworkInterface -VM $vm -Id $nic.Id
    $storageAcc=Get-AzureRMStorageAccount -ResourceGroupName $rgName -Name $saName
    $osDiskUri=$storageAcc.PrimaryEndpoints.Blob.ToString() + "vhds/" + $vmName + "-OSDisk.vhd"
    $vm=Set-AzureRMVMOSDisk -VM $vm -Name "OSDisk" -VhdUri $osDiskUri -CreateOption fromImage
    New-AzureRMVM -ResourceGroupName $rgName -Location $locName -VM $vm
    
    # Create the cluster majority node server
    $vmName="<Table M – Item 5 - Virtual machine name column>"
    $vmSize="<Table M – Item 5 - Minimum size column>"
    $nic=New-AzureRMNetworkInterface -Name ($vmName +"-NIC") -ResourceGroupName $rgName -Location $locName -SubnetId $vnet.Subnets[1].Id
    $vm=New-AzureRMVMConfig -VMName $vmName -VMSize $vmSize -AvailabilitySetId $avset.Id
    $cred=Get-Credential -Message "Type the name and password of the local administrator account for the cluster majority node server." 
    $vm=Set-AzureRMVMOperatingSystem -VM $vm -Windows -ComputerName $vmName -Credential $cred -ProvisionVMAgent -EnableAutoUpdate
    $vm=Set-AzureRMVMSourceImage -VM $vm -PublisherName MicrosoftWindowsServer -Offer WindowsServer -Skus 2012-R2-Datacenter -Version "latest"
    $vm=Add-AzureRMVMNetworkInterface -VM $vm -Id $nic.Id
    $storageAcc=Get-AzureRMStorageAccount -ResourceGroupName $rgName -Name $saName
    $osDiskUri=$storageAcc.PrimaryEndpoints.Blob.ToString() + "vhds/" + $vmName + "-OSDisk.vhd"
    $vm=Set-AzureRMVMOSDisk -VM $vm -Name "OSDisk" -VhdUri $osDiskUri -CreateOption fromImage
    New-AzureRMVM -ResourceGroupName $rgName -Location $locName -VM $vm

> [AZURE.NOTE] 因為這些虛擬機器的內部網路應用程式，他們不指派公用 IP 位址或 DNS 網域名稱標籤，會公開至網際網路。 不過，這也表示您無法從 Azure 入口網站連線到它們。  **連接** ] 按鈕將無法在檢視虛擬機器的內容時。 使用「遠端桌面連線」配件或其他遠端桌面工具，來使用虛擬機器的私人 IP 位址或內部網路 DNS 名稱連接該虛擬機器。

## 設定 SQL Server 電腦

對於每部執行 SQL Server 的虛擬機器，使用您選擇的遠端桌面用戶端，並建立遠端桌面連線。 使用其內部網路 DNS 或電腦名稱，以及本機系統管理員帳戶的認證。

對於每部執行 SQL Server 的虛擬機器，請在 Windows PowerShell 提示中使用下列命令，將其加入適當的 AD DS 網域。

    $domName="<AD DS domain name to join, such as corp.contoso.com>"
    Add-Computer -DomainName $domName
    Restart-Computer

請注意，您必須在輸入 Add-Computer 命令後提供網域帳戶認證。

重新啟動之後，使用具有本機系統管理員權限的帳戶重新連接到網域。

請為每部 SQL Server 執行以下操作：

1. 使用 [登入虛擬機器使用遠端桌面連線程序](virtual-machines-workload-intranet-sharepoint-phase2.md#logon) 使用本機系統管理員帳戶的認證登入。

2. 使用 [初始化空磁碟程序](virtual-machines-workload-intranet-sharepoint-phase2.md#datadisk) 兩次，每個 SQL server，以新增額外的資料磁碟的一次。

3. 從 Windows PowerShell 命令提示字元執行下列命令。

        md f:\Data
        md f:\Log
        md f:\Backup

4. 使用 [來測試連線能力程序](virtual-machines-workload-intranet-sharepoint-phase2.md#testconn) 來測試組織網路位置的連線。 此程序可確保 DNS 名稱解析正常運作 (即虛擬網路中的虛擬機器已由 DNS 伺服器正確設定)，且封包可在跨單位虛擬網路內往來傳送。

使用下列程序兩次，分別為兩部 SQL server 進行設定，讓新的資料庫以及帳戶和權限使用 F: 磁碟機。

1.  在 [開始] 畫面上輸入 **SQL Studio**, ，然後按一下 [ **SQL Server 2014 Management Studio**。
2.  在 **連接到伺服器**, ，按一下 [ **連接**。
3.  在左窗格中，以滑鼠右鍵按一下最上層節點 — 以機器名稱命名的預設執行個體，然後按一下 [ **屬性**。
4.  在 **伺服器內容**, ，按一下 [ **資料庫設定**。
5.  在 **資料庫預設位置**, ，設定下列值:
- 如 **資料**, ，路徑設定為 **f:\Data**。
- 如 **記錄**, ，路徑設定為 **f:\Log**。
- 如 **備份**, ，路徑設定為 **f:\Backup**。
- 只有新的資料庫才會使用這些位置。
6.  按一下 [ **確定** 關閉視窗。
7.  在左窗格中，展開 **安全性資料夾**。
8.  以滑鼠右鍵按一下 **登入**, ，然後按一下 [ **新登入**。
9.  在 **登入名稱**, ，型別 *網域*\sp_farm_db (在其中 *網域* 是指建立 sp_farm_db 帳戶所在的網域名稱)。
10. 下 **選取頁面**, ，按一下 [ **伺服器角色**, ，按一下 [ **sysadmin**, ，然後按一下 [ **確定**。
11. 關閉 SQL Server 2014 Management Studio。

使用下列程序兩次，分別為兩部 SQL server 進行設定，允許使用 sp_farm_db 帳戶進行遠端桌面連線。

1.  在 [開始] 畫面中，以滑鼠右鍵按一下 **這台電腦**, ，然後按一下 [ **屬性**。
2.  在 **系統** ] 視窗中，按一下 [ **遠端設定**。
3.  在 **遠端桌面** 區段中，按一下 **選取使用者**, ，然後按一下 [ **新增**。
4.  在 **輸入物件名稱來選取**, ，型別 [網域]**\sp_farm_db**, ，然後按一下 [ **確定** 三次。

SQL Server 需要一個連接埠讓用戶端存取資料庫伺服器， 同時也需要連接埠來連線 SQL Server Management Studio，並管理高可用性群組。 接下來，在系統管理員層級 Windows PowerShell 命令提示字元中執行下列命令兩次，分別為兩部 SQL server 進行設定，新增允許輸入流量至 SQL Server 的防火牆規則。

    New-NetFirewallRule -DisplayName "SQL Server ports 1433, 1434, and 5022" -Direction Inbound –Protocol TCP –LocalPort 1433,1434,5022 -Action Allow

請在每一部 SQL Server 虛擬機器中，以本機系統管理員的身分登出。

如需在 Azure 中的 SQL Server 效能最佳化，請參閱 [的 Azure 虛擬機器中 SQL Server 效能最佳作法](virtual-machines-sql-server-performance-best-practices.md)。 同時您也可以停用 SharePoint 伺服器陣列儲存體帳戶的異地備援儲存體 (GRS)，並使用儲存空間最佳化 IOP。

## 設定叢集多數節點伺服器

使用 [登入虛擬機器使用遠端桌面連線程序](virtual-machines-workload-intranet-sharepoint-phase2.md#logon) 叢集多數節點使用網域帳戶的認證登入。

在叢集多數節點中，使用 [來測試連線能力程序](virtual-machines-workload-intranet-sharepoint-phase2.md#testconn) 來測試組織網路位置的連線。

## 建立 Windows 伺服器叢集

SQL Server AlwaysOn 可用性群組依賴 Windows Server 的容錯移轉叢集 (WSFC) 功能。  該功能可讓多部機器在叢集中以群組的方式加入。 當其中一部機器故障，第二部機器則可取而代之。 因此，第一個工作便是為加入叢集的所有機器啟用容錯移轉叢集功能，其中包括：

- 主要 SQL Server
- 次要 SQL Server
- 叢集多數節點

容錯移轉叢集需要至少三部虛擬機器。 其中兩部用來主控 SQL Server。 第二部 SQL Server VM 為同步處理的次要複本，以確保主要機器故障時，不會遺失任何資料。 第三部機器不需主控 SQL Server。 叢集多數節點可在 WSFC 中提供仲裁。 由於 WSFC 叢集依賴仲裁來監控狀態，因此一定要有多數來確保 WSFC 叢集為上線狀態。 若叢集裡只有兩部機器，當其中一部故障時，就沒有多數的情況。 如需詳細資訊，請參閱 [WSFC 仲裁模式和投票組態 (SQL Server)](http://msdn.microsoft.com/library/hh270280.aspx)。

請在兩部 SQL Server 電腦與叢集多數節點中，於系統管理員層級的 Windows PowerShell 命令提示字元中執行下列命令。

    Install-WindowsFeature Failover-Clustering -IncludeManagementTools

由於目前 Azure 中 DHCP 的非 RFC 相容行為，無法成功建立 Windows Server 容錯移轉叢集 (WSFC) 叢集。 如需詳細資訊，請在 Azure 虛擬機器中 SQL Server 的「高可用性和災害復原」內搜尋「Azure 網路中的 WSFC 叢集行為」。  不過還是有相對的因應措施。 使用下列步驟建立叢集。

1.  登入主要 SQL Server 虛擬機器與 **sp_install** 帳戶。
2.  從 [開始] 畫面中，輸入 **容錯移轉**, ，然後按一下 [ **容錯移轉叢集管理員**。
3.  在左窗格中，以滑鼠右鍵按一下 **容錯移轉叢集管理員**, ，然後按一下 [ **建立叢集**。
4.  在您開始前] 頁面上，按一下 [ **下一步**。
5.  在 [選取伺服器] 頁面中，輸入主要 SQL Server 電腦的名稱，請按一下 **新增**, ，然後按一下 [ **下一步**。
6.  在 [驗證警告] 頁面中，按一下 [**否。我不需要從 Microsoft 取得此叢集的支援，也不想要執行驗證測試。按 [下一步] 後，繼續建立叢集**, ，然後按一下 [ **下一步**。
7.  在管理叢集] 頁面中的存取點 **叢集名稱** ] 文字方塊中，輸入您叢集的名稱，然後按一下 **下一步**。
8.  在 [確認] 頁面中，按一下 [ **下一步** 開始建立叢集。
9.  在 [摘要] 頁面中，按一下 [ **完成**。
10. 在左側窗格中按一下您的新叢集。 在 **叢集核心資源** 區段的 [內容] 窗格中，開啟您的伺服器叢集名稱。  **IP 位址** 資源會顯示在 **失敗** 狀態。 由於指派給叢集的 IP 位址與虛擬機器相同，因此該 IP 位址資源無法上線， 造成位址重覆的狀況。
11. 以滑鼠右鍵按一下失敗 **IP 位址** 資源，然後再按一下 **屬性**。
12. 在 **IP 位址內容** 對話方塊中，按一下 [ **靜態 IP 位址**。
13. 輸入未使用的 IP 位址範圍對應至 SQL server 所在位置，子網路中，然後按一下 **確定**。
14. 以滑鼠右鍵按一下失敗的 IP 位址資源，然後按一下 **上線**。 等待兩個資源上線。 待叢集名稱資源上線後，將使用新的 Active Directory (AD) 電腦帳戶更新網域控制站。 此 AD 帳戶稍後將用來執行可用性群組叢集服務。
15. 現在 AD 帳戶已建立，請讓叢集名稱離線。 以滑鼠右鍵按一下叢集名稱，在 **叢集核心資源**, ，然後按一下 [ **離線**。
16. 若要移除叢集 IP 位址，以滑鼠右鍵按一下 **IP 位址**, ，按一下 [ **移除**, ，然後按一下 [ **是** 出現提示時。 由於叢集資源依賴 IP 位址資源，因此無法再次上線。 不過，可用性群組不需依賴叢集名稱或 IP 位址即可正常運作。 因此叢集名稱可保持離線狀態。
17. 將其餘節點加入叢集，以滑鼠右鍵按一下左窗格中的叢集名稱，然後按一下 [ **加入節點**。
18. 在您開始前] 頁面上，按一下 [ **下一步**。
19. 在 [選取伺服器] 頁面中，輸入名稱，然後按一下 **新增** 將次要 SQL server 和叢集多數節點新增到叢集。 新增兩部電腦之後, 按 **下一步**。
若無法新增機器，並出現錯誤訊息「遠端登錄並未執行」，請執行下列步驟。 登入機器、開啟 [服務] 嵌入式管理單元 (services.msc) 並啟用遠端登錄。 如需詳細資訊，請參閱 [無法連線到遠端登錄服務](http://technet.microsoft.com/library/bb266998.aspx)。
20. 在 [驗證警告] 頁面中，按一下 [**否。我不需要從 Microsoft 取得此叢集的支援，也不想要執行驗證測試。按 [下一步] 後，繼續建立叢集**, ，然後按一下 [ **下一步**。
21. 在 [確認] 頁面中，按一下 [ **下一步**。
22. 在 [摘要] 頁面中，按一下 [ **完成**。
23. 在左窗格中，按一下 [ **節點**。 畫面中應該會列出三台電腦。

## 啟用 AlwaysOn 可用性群組

下一個步驟則是使用 SQL Server 組態管理員啟用 AlwaysOn 可用性群組。 請注意，SQL Server 中的可用性群組與 Azure 可用性設定組有所不同。 可用性群組包含高可用性和可復原的資料庫。  Azure 可用性設定組會將虛擬機器配置到不同的容錯網域。 如需容錯網域的詳細資訊，請參閱 [管理虛擬機器的可用性](virtual-machines-manage-availability.md)。

使用這些步驟啟用 SQL Server 上的 AlwaysOn 可用性群組。

1.  登入主要 SQL server using **sp_farm_db** 帳戶或某些其他 SQL server 具有 sysadmin 伺服器角色的帳戶。
2.  在 [開始] 畫面上輸入 **SQL Server 組態**, ，然後按一下 [ **SQL Server 組態管理員**。
3.  在左窗格中，按一下 [ **SQL Server 服務**。
4.  在 [內容] 窗格中，按兩下 **SQL Server (MSSQLSERVER)**。
5.  在 **SQL Server (MSSQLSERVER) 屬性**, ，按一下 [ **AlwaysOn 高可用性** 索引標籤上，選取 **啟用 AlwaysOn 可用性群組**, ，按一下 [ **套用**, ，然後按一下 [ **確定** 出現提示時。 先不要關閉屬性視窗。
6.  按一下 [虛擬-機器-管理-可用性] 索引標籤，然後輸入 [網域]**\sqlservice** 中 **帳戶名稱**。 輸入 sqlservice 帳戶密碼 **密碼** 和 **確認密碼**, ，然後按一下 [ **確定**。
7.  在 [訊息] 視窗中，按一下 [ **是** 重新啟動 SQL Server 服務。
8.  登入次要 SQL Server 並重複此程序。

下圖顯示已成功完成此階段設定，並以電腦名稱預留位置表示。

![](./media/virtual-machines-workload-intranet-sharepoint-phase3/workload-spsqlao_03.png)

## 後續步驟

- 使用 [第 4 階段](virtual-machines-workload-intranet-sharepoint-phase4.md) 繼續設定此工作負載。

