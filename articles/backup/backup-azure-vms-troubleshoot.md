<properties
    pageTitle="移難排解 Azure 虛擬機器備份 | Microsoft Azure"
    description="Azure 虛擬機器備份與還原的疑難排解"
    services="backup"
    documentationCenter=""
    authors="trinadhk"
    manager="shreeshd"
    editor=""/>

<tags
    ms.service="backup"
    ms.workload="storage-backup-recovery"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="12/15/2015"
    ms.author="trinadhk;aashishr;jimpark"/>



# Azure 虛擬機器備份的疑難排解

您可以疑難排解將 Azure 備份使用於下表所列資訊時發生的錯誤。

## 探索

| 備份作業| 錯誤詳細資料| 因應措施|
| -------- | -------- | -------|
| 探索| 無法探索新的項目 - Microsoft Azure 備份發生內部錯誤。等候幾分鐘的時間，然後再次嘗試操作。| 在 15 分鐘之後重試探索程式。
| 探索| 無法探索新的項目 – 另一個探索作業正在進行中。請等待目前的探索作業完成。| None|

## 註冊

| 備份作業| 錯誤詳細資料| 因應措施|
| -------- | -------- | -------|
| 註冊| 連接至虛擬機器的資料磁碟數目超過支援的限制 - 請卸離此虛擬機器上的某些資料磁碟，然後重試作業。Azure 備份最多支援 16 個資料磁碟連接至 Azure 虛擬機器進行備份。| None|
| 註冊| Microsoft Azure 備份遇到內部錯誤 - 等候幾分鐘的時間，然後再次嘗試操作。如果問題持續發生，請連絡 Microsoft 支援服務。| 您可以取得此錯誤，因為不支援下列設定的其中一個: <ul><li>Premium LRS </ul>|
| 註冊| 因為安裝代理程式作業逾時而註冊失敗| 請檢查是否支援虛擬機器的作業系統版本。|
| 註冊| 命令執行失敗 - 另一項作業正在此項目上進行。請等到前一項作業完成| None|
| 註冊| 不支援備份在進階儲存體中儲存虛擬硬碟的虛擬機器| None|
| 註冊| 虛擬機器代理程式不存在於虛擬機器上：請安裝所需的必要元件 VM 代理程式，並重新啟動作業。| [閱讀更多](#vm-agent) 有關 VM 代理程式安裝，以及如何驗證 VM 代理程式安裝。|

## 備份

| 備份作業| 錯誤詳細資料| 因應措施|
| -------- | -------- | -------|
| 備份| 從備份保存庫複製 VHD 已逾時 - 請在幾分鐘內重試此作業。如果問題持續發生，請連絡 Microsoft 支援服務。| 要複製的資料太多時會發生此問題。請檢查您的資料磁碟是否少於 16 個。|
| 備份| 無法與 VM 代理程式通訊來取得快照集狀態。快照 VM 子工作逾時。-請請參閱有關如何解決此問題的疑難排解指南。| 如果 VM 代理程式發生問題，或以某種方式封鎖對 Azure 基礎結構的網路存取，則會擲回這個錯誤。<ul><li>深入了解 [VM 代理程式問題偵錯](#vm-agent) <li>深入了解 [網路問題偵錯](#networking) </ul><br>如果 VM 代理程式不會造成任何問題，然後重新啟動 VM。有時，不正確的 VM 狀態可能會導致問題，重新啟動 VM 可清除此「錯誤狀態」|
| 備份| 備份因為內部錯誤而失敗 - 請在幾分鐘內重試此作業。如果問題持續發生，請連絡 Microsoft 支援服務。| 發生此錯誤原因 2: <ol><li> 沒有太多要複製的資料。<li>在刪除原始的 VM，因此無法進行備份。若要保留已刪除 VM 的備份資料但阻止備份錯誤，請取消保護 VM 並選擇保留資料。這樣會停止備份排程以及週期性的錯誤訊息。|
| 備份| 無法在選取的項目上安裝 Azure 復原服務延伸模組 - VM 代理程式是 Azure 復原服務延伸模組的必要條件。請安裝 Azure VM 代理程式並重新啟動註冊作業| <ol> <li>檢查 VM 代理程式是否已正確安裝。<li>請確定已正確設定 VM 組態中的旗標。</ol>[閱讀更多](#validating-vm-agent-installation) 有關 VM 代理程式安裝，以及如何驗證 VM 代理程式安裝。|
| 備份| 命令執行失敗 - 另一項作業目前正在此項目上進行。請等到前一項作業完成，然後重試| VM 的現有備份或還原作業正在執行中，而當現有作業正在執行時，無法啟動新的作業。|
| 備份| 擴充功能安裝失敗，發生錯誤「COM+ 無法與 Microsoft Distributed Transaction Coordinator 通話」| 這通常表示 COM+ 服務未執行。請連絡 Microsoft 支援服務來協助解決此問題。|
| 備份| 快照集作業失敗，發生 VSS 作業錯誤「這個磁碟機已由 BitLocker 磁碟機加密鎖定」。您必須至 [控制台] 解除鎖定這個磁碟機。| 對 VM 上的所有磁碟機關閉 BitLocker，並觀察是否已解決 VSS 問題|
| 備份| 不支援備份在進階儲存體中儲存虛擬硬碟的虛擬機器| None|
| 備份| 找不到 Azure 虛擬機器。| 當刪除主要 VM 但備份原則繼續尋找 VM 來執行備份時，就會發生這種情況。若要修正此錯誤: <ol><li>重新建立虛擬機器具有相同名稱和相同的資源群組名稱 [雲端服務名稱] <br>(OR) <li> 停用此 VM 的保護，如此就不會建立備份工作 </ol>|
| 備份| 虛擬機器代理程式不存在於虛擬機器上：請安裝所需的必要元件 VM 代理程式，並重新啟動作業。| [閱讀更多](#vm-agent) 有關 VM 代理程式安裝，以及如何驗證 VM 代理程式安裝。|

## 作業

| 作業| 錯誤詳細資料| 因應措施|
| -------- | -------- | -------|
| 取消工作| 不支援取消這種工作類型：請等待工作完成。| None|
| 取消工作| 工作不是處於可取消的狀態：請等待工作完成。<br>或者<br> 所選取的工作不是處於可取消的狀態-請等候工作完成。| 工作極有可能幾乎要完成，請等待作業完成|
| 取消工作| 無法取消工作，因為它不在進行中：只支援取消進行中的工作。請嘗試取消進行中的工作。| 這起因於暫時性狀態。請稍候再重試取消作業|
| 取消工作| 無法取消工作：請等待工作完成。| None|


## 還原

| 作業| 錯誤詳細資料| 因應措施|
| -------- | -------- | -------|
| 還原| 還原失敗並發生雲端內部錯誤| <ol><li>您嘗試還原的雲端服務被設定為 DNS 設定。您可以檢查 <br>$deployment = Get-azuredeployment-ServiceName"ServiceName"-Slot"Production"Get-azuredns-dnssettings $deployment。Dnssettings<br>如果沒有設定位址，這表示會設定為 DNS 設定。<br><li>要想要還原的雲端服務以 ReservedIP 設定，並在雲端服務中現有的 Vm 皆處於停止狀態。<br>您可以檢查雲端服務是否有保留 IP 的使用下列 powershell cmdlet:<br>$deployment = Get-azuredeployment-ServiceName"servicename"-Slot"Production"$ dep。ReservedIPName <br><li>您嘗試還原具有下列特殊的網路組態中以相同的雲端服務的虛擬機器。 <br>-在負載平衡器組態 (內部和外部) 下的虛擬機器<br>-虛擬機器具有多個保留 Ip<br>-具有多個 Nic 的虛擬機器<br>請在 UI 中選取新的雲端服務，或請參閱 [還原考量](backup-azure-restore-vms.md/#restoring-vms-with-special-network-configurations) 特殊的網路組態的 vm</ol>|
| 還原| 所選取的 DNS 名稱已被使用：請指定不同的 DNS 名稱，然後再試一次。| 此處的 DNS 名稱是指雲端服務名稱 (結尾通常是 .cloudapp.net)。這必須是唯一的。如果您遇到這個錯誤，您需要在還原期間選擇不同的 VM 名稱。<br><br> 請注意，此錯誤只會顯示 Azure 入口網站的使用者。透過 PowerShell 執行還原作業將會成功，因為它只會還原磁碟，並不會建立 VM。當您在磁碟還原作業之後明確建立 VM 時，將會遇到此錯誤。|
| 還原| 指定的虛擬網路組態不正確：請指定不同的虛擬網路組態，然後再試一次。| None|
| 還原| 指定的雲端服務使用保留的 IP，但不符合所要還原的虛擬機器組態：請指定另一個不使用保留 IP 的雲端服務，或選擇從另一個復原點還原。| None|
| 還原| 雲端服務已達到輸入端點數目限制：請指定不同的雲端服務或使用現有的端點來重試作業。| None|
| 還原| 備份保存庫和目標儲存體帳戶位於兩個不同區域：請確定還原作業中指定的儲存體帳戶，位於與備份保存庫相同的 Azure 區域。| None|
| 還原| 不支援還原作業所指定的儲存體帳戶：只支援具有本地備援或異地備援複寫設定的基本/標準儲存體帳戶。請選取支援的儲存體帳戶| None|
| 還原| 還原作業所指定的儲存體帳戶類型不在線上：請確定還原作業中指定的儲存體帳戶在線上| 這可能是因為 Azure 儲存體的暫時性錯誤或運作中斷。請選擇另一個儲存體帳戶。|
| 還原| 已達到資源群組配額：請從預覽入口網站刪除一些資源群組，或連絡 Azure 支援以提高限制。| None|
| 還原| 選取的子網路不存在：請選取已存在的子網路| None|


## 原則

| 作業| 錯誤詳細資料| 因應措施|
| -------- | -------- | -------|
| 建立原則| 無法建立原則：請減少保留選項以繼續設定原則。| None|


## VM 代理程式

### 設定 VM 代理程式

一般而言，VM 代理程式已存在於從 Azure 資源庫建立的 VM 中。 不過，從內部部署資料中心移轉的虛擬機器不會安裝 VM 代理程式。 對於這類 VM，必須明確安裝 VM 代理程式。 深入了解 [現有 VM 上安裝 VM 代理程式](http://blogs.msdn.com/b/mast/archive/2014/04/08/install-the-vm-agent-on-an-existing-azure-vm.aspx)。

若為 Windows VM：

- 下載並安裝 [代理程式 MSI](http://go.microsoft.com/fwlink/?LinkID=394789&clcid=0x409)。您需要有系統管理員權限，才能完成安裝。
- [更新 VM 屬性](http://blogs.msdn.com/b/mast/archive/2014/04/08/install-the-vm-agent-on-an-existing-azure-vm.aspx) ，表示已安裝代理程式。

如為 Linux VM：

- 安裝最新 [Linux 代理程式](https://github.com/Azure/WALinuxAgent) github。
- [更新 VM 屬性](http://blogs.msdn.com/b/mast/archive/2014/04/08/install-the-vm-agent-on-an-existing-azure-vm.aspx) ，表示已安裝代理程式。


### 更新 VM 代理程式

若為 Windows VM：

- 更新 VM 代理程式很簡單，只要重新安裝 [VM 代理程式二進位檔](http://go.microsoft.com/fwlink/?LinkID=394789&clcid=0x409)。不過，您需要確定在更新 VM 代理程式時，沒有任何執行中的備份作業。

如為 Linux VM：

- 按照指示 [更新 Linux VM 代理程式 ](../virtual-machines-linux-update-agent.md)。


### 驗證 VM 代理程式安裝

如何檢查 Windows VM 上的 VM 代理程式版本：

1. 登入 Azure 虛擬機器，然後瀏覽至 *C:\WindowsAzure\Packages* 資料夾。 您應該會發現 WaAppAgent.exe 檔案已存在。
2. 在該檔案上按一下滑鼠右鍵，前往 [屬性]****，然後選取 [詳細資料]**** 索引標籤。 [產品版本] 欄位應為 2.6.1198.718 或更高版本

## 網路

如同所有的延伸模組，備份延伸模組需要存取公用網際網路才能運作。 沒有公用網際網路的存取權，可能會以各種不同方式的資訊清單形式顯現：

- 延伸模組安裝可能會失敗
- 備份作業 (如磁碟快照) 可能會失敗
- 顯示備份作業狀態可能會失敗

已在解析網際網路的公用位址的需求 [這裡](http://blogs.msdn.com/b/mast/archive/2014/06/18/azure-vm-provisioning-stuck-on-quot-installing-extensions-on-virtual-machine-quot.aspx)。 您必須檢查 VNET 的 DNS 設定，並確定可以解析 Azure URI。

正確完成名稱解析後，也必須提供 Azure IP 的存取權。 若要解除封鎖對 Azure 基礎結構的存取，請遵循下列步驟進行：

1. 將 Azure 資料中心的 IP 範圍列入允許清單。
    - 取得清單 [Azure 資料中心 Ip](https://www.microsoft.com/download/details.aspx?id=41653) 清單。
    - 解除封鎖 Ip 使用 [New-netroute](https://technet.microsoft.com/library/hh826148.aspx) 指令程式。 在提升權限的 PowerShell 視窗中 (以系統管理員身分執行)，於 Azure VM 內執行這個 Cmdlet。
    - 將規則加入 NSG (若已有規則)，以允許存取該 IP。
2. 建立 HTTP 流量的行經路徑
    - 如果已有一些網路限制 (例如，網路安全性群組)，請部署 HTTP Proxy 伺服器來路由傳送流量。 您可以找到部署 HTTP Proxy 伺服器的步驟 [這裡](backup-azure-vms-prepare.md#2-network-connectivity)。
    - 將規則加入 NSG (若已有規則)，以允許從 HTTP Proxy 存取網際網路。





