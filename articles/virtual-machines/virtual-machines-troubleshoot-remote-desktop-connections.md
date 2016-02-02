<properties
    pageTitle="疑難排解遠端桌面連線到 Azure VM | Microsoft Azure"
    description="在 Windows VM 上疑難排解遠端桌面連線錯誤。取得快速因應步驟、錯誤訊息的說明和詳細的網路疑難排解。"
    keywords="Remote desktop error,remote desktop connection error,cannot connect to VM,remote desktop troubleshooting"
    services="virtual-machines"
    documentationCenter=""
    authors="dsk-2015"
    manager="timlt"
    editor=""
    tags="top-support-issue,azure-service-management,azure-resource-manager"/>

<tags
    ms.service="virtual-machines"
    ms.workload="infrastructure-services"
    ms.tgt_pltfrm="vm-windows"
    ms.devlang="na"
    ms.topic="article"
    ms.date="10/27/2015"
    ms.author="dkshir"/>


# 疑難排解執行 Windows 之 Azure 虛擬機器的遠端桌面連線

有許多原因可能導致嘗試連線到 Windows 的 Azure 虛擬機器時發生遠端桌面 (RDP) 錯誤。 問題與 VM 上的 RDP 軟體、基礎主機電腦、網路連線或連接的用戶端上相關。 本文將協助您找出原因並加以更正。

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-both-include.md)]

本文僅適用於執行 Windows 之 Azure 虛擬機器。 對於執行 Linux 的 Azure 虛擬機器，請參閱 [疑難排解 SSH 連接至 Azure VM](virtual-machines-troubleshoot-ssh-connections.md)。

如果您需要更多協助，在本文中的任何時間點，您可以與 Azure 專家 [MSDN Azure 和堆疊溢位論壇](http://azure.microsoft.com/support/forums/)。 或者，您也可以提出 Azure 支援事件。 移至 [Azure 支援網站](http://azure.microsoft.com/support/options/) ，然後按一下 **取得支援**。

「基本步驟」的第一節列出解決常見連接問題的步驟，第二節提供特定錯誤訊息的解決方案步驟，而最後一節協助執行每個網路元件的詳細疑難排解。

## 修正傳統部署模型中常見遠端桌面錯誤的步驟

這些基本步驟可協助解決使用傳統部署模型所建立之虛擬機器中最常見的遠端桌面連線失敗。 在執行每個步驟之後，請嘗試重新連接至 VM。

- 重設遠端桌面服務從 [Azure 入口網站](https://portal.azure.com) 與 RDP 伺服器修復啟動問題。<br>
    依序按一下 [全部瀏覽] > [虛擬機器 (傳統)] > 您的 Windows 虛擬機器 > [重設遠端存取]****。

    ![顯示重設 RDP 設定的螢幕擷取畫面](./media/virtual-machines-troubleshoot-remote-desktop-connections/Portal-RDP-Reset-Windows.png)

- 重新啟動虛擬機器其他的啟動問題。<br>
    依序按一下 [全部瀏覽] > [虛擬機器 (傳統)] > 您的 Windows 虛擬機器 > [重新啟動]****。

- 調整大小的 VM，以修正任何主機的問題。<br>
    依序按一下 [全部瀏覽] > [虛擬機器 (傳統)] > 您的 Windows 虛擬機器 > [設定] > [大小]****。 如需詳細步驟，請參閱 [調整虛擬機器的大小](https://msdn.microsoft.com/library/dn168976.aspx)。

- 檢閱您的 VM 主控台記錄檔或螢幕擷取畫面以修正開機問題。
    依序按一下 [全部瀏覽] > [虛擬機器 (傳統)] > 您的 Windows 虛擬機器 > [開機診斷]****。

- 檢查 VM 的資源健康情況是否有任何平台問題。 
    依序按一下 [全部瀏覽] > [虛擬機器 (傳統)] > 您的 Windows 虛擬機器 > [檢查健康情況]****

## 修正 [資源管理員] 部署模型中常見遠端桌面錯誤的步驟

這些基本步驟可協助解決使用資源管理員部署模型所建立之虛擬機器中最常見的遠端桌面連線失敗。 在執行每個步驟之後，請嘗試重新連接至 VM。

- 重設使用 Powershell 的遠端存取<br>
    a. 如果您還沒有這麼做， [安裝 Azure PowerShell，並連接到您的 Azure 訂閱](../powershell-install-configure.md) 使用 Azure AD 方法。

    b. 切換至資源管理員模式。

    ```
    Switch-AzureMode -Name AzureResourceManager
    ```
    c. 執行 Set-AzureVMAccessExtension 命令來重設您的 RDP 連線，如下列範例所示。

    ```
    Set-AzureVMExtension -ResourceGroupName "myRG" -VMName "myVM" -Name "myVMAccessExtension" -ExtensionType "VMAccessAgent" -Publisher "Microsoft.Compute" -typeHandlerVersion "2.0" -Location Westus
    ```

- 重新啟動虛擬機器其他的啟動問題。<br>
    依序按一下 [全部瀏覽] > [虛擬機器] > 您的 Windows 虛擬機器 > [重新啟動]****。

- 調整大小的 VM，以修正任何主機的問題。<br>
    依序按一下 [全部瀏覽] > [虛擬機器] > 您的 Windows 虛擬機器 > [設定] > [大小]****。

- 檢閱 VM 的主控台記錄檔或螢幕擷取畫面，以修正開機問題。
    依序按一下 [全部瀏覽] > [虛擬機器] > 您的 Windows 虛擬機器 > [開機診斷]****。


## 疑難排解特定的遠端桌面連線錯誤

以下是您嘗試連線 Azure 虛擬機器和遠端桌面時可能最常遇到的錯誤：

1. [遠端桌面連線錯誤: 遠端工作階段已中斷連線，因為沒有可用的遠端桌面授權伺服器來提供授權](#rdplicense)。

2. [遠端桌面連線錯誤: 遠端桌面無法找到電腦 「 名稱 」](#rdpname)。

3. [遠端桌面連線錯誤: 發生驗證錯誤。 本機安全性授權單位不能 contacted](#rdpauth)。

4. [Windows 安全性錯誤: 您的認證無法運作](#wincred)。

5. [遠端桌面連線錯誤: 這部電腦無法連線到遠端電腦](#rdpconnect)。

<a id="rdplicense"></a>
### 遠端桌面連線錯誤：遠端工作階段已中斷連線，因為沒有可用的遠端桌面授權伺服器來提供授權。

原因：遠端桌面角色的 120 天授權寬限期已過期，您需要安裝授權。

做為因應措施，請從入口網站儲存 RDP 檔案的本機複本，並在 Windows PowerShell 命令提示字元執行該命令來連接。

        mstsc <File name>.RDP /admin

這只會停用該連接的授權。

如果您並非真的需要多於兩個對虛擬機器的同步遠端桌面連線，則您可以使用伺服器管理員來移除遠端桌面伺服器角色。

另請參閱 [Azure VM 失敗並 「 沒有可用的遠端桌面授權伺服器 」](http://blogs.msdn.com/b/wats/archive/2014/01/21/rdp-to-azure-vm-fails-with-quot-no-remote-desktop-license-servers-available-quot.aspx) 部落格文章。

<a id="rdpname"></a>
### 遠端桌面連線錯誤：遠端桌面無法找到電腦「名稱」。

原因：您電腦上的遠端桌面用戶端無法解析此 RDP 檔案設定中的電腦名稱。

可能的解決方案：

- 如果您位於組織內部網路，請確保您的電腦能存取並傳送 HTTPS 流量給 Proxy 伺服器。
- 如果您使用本機儲存的 RDP 檔案，請嘗試使用入口網站所產生的檔案。 如此可確保您的虛擬機器或雲端服務和虛擬機器的端點連接埠有正確的 DNS 名稱。 以下是由入口網站產生的 RDP 檔案範例：

        full address:s:tailspin-azdatatier.cloudapp.net:55919
        prompt for credentials:i:1


RDP 檔案中的位址部分有雲端服務的完整網域名稱，包含 VM (在本範例中為 tailspin-azdatatier.cloudapp.net) 以及遠端桌面流量端點的外部 TCP 連接埠 (55919)。

<a id="rdpauth"></a>
### 遠端桌面連線錯誤：發生驗證錯誤。無法連絡本機安全性授權。

原因：目標 VM 在認證的使用者名稱部分找不到安全性授權。

當您的使用者名稱是表單 *SecurityAuthority*\\*UserName* (範例: CORP\User1)， *SecurityAuthority* 部分是 (適用於本機安全性授權) 的虛擬機器的電腦名稱或 Active Directory 網域名稱。

可能的解決方案：

- 如果您的使用者帳戶是本機的 VM，請檢查 VM 名稱是否正確拼字。
- 如果該帳戶位於 Active Directory 網域，請檢查網域名稱的拼字。
- 如果該帳戶是 Active Directory 網域帳戶且網域名稱的拼字正確，請確認該網域內可以使用網域控制站。 網域控制站電腦並未啟動，在包含網域控制站的 Azure 虛擬網路中是常見的問題。 您可以使用本機系統管理員帳戶做為因應措施，而非網域帳戶。

<a id="wincred"></a>
### Windows 安全性錯誤：您的認證無法運作。

原因：目標 VM 無法驗證您的帳戶名稱和密碼。

以 Windows 為基礎的電腦可以驗證本機帳戶或網域帳戶之認證。

- 本機帳戶，請使用 *ComputerName*\\*UserName* 語法 (範例: SQL1\Admin4798)。
- 網域帳戶，請使用 *DomainName*\\*UserName* 語法 (範例: CONTOSO\johndoe)。

如果您在新的 Active Directory 樹系將虛擬機器提升為網域控制器，您用來登入的本機系統管理員帳戶也會轉換為對等的帳戶，在新樹系和網域中使用相同的密碼。 本機系統管理員帳戶隨即刪除。 例如，如果您以本機系統管理員帳戶 DC1\DCAdmin 登入，並提升此虛擬機器為 corp.contoso.com 網域中新樹系的網域控制器，則會刪除 DC1\DCAdmin 本機帳戶，並且以相同密碼建立一個新的網域帳戶 (CORP\DCAdmin)。

請確保帳戶名稱為虛擬機器可驗證為有效帳戶的名稱，且密碼正確。

如果您需要變更本機系統管理員帳戶的密碼，請參閱 [如何重設密碼或遠端桌面服務的 Windows 虛擬機器](virtual-machines-windows-reset-password.md)。

<a id="rdpconnect"></a>
### 遠端桌面連線錯誤：這部電腦無法連線到遠端電腦。

原因：用來連接的帳戶並沒有遠端桌面的登入權限。

每部 Windows 電腦都有遠端桌面使用者本機群組，其中包含能夠遠端登入的帳戶和群組。 本機系統管理員群組成員也有權限，即使這些帳戶未列在遠端桌面使用者本機群組中。 對於加入網域的機器，本機系統管理員群組也包含此網域的網域系統管理員。

請確保您正用於連接的帳戶具有遠端桌面登入權限。 請使用網域或本機系統管理員帳戶做為因應措施，以透過遠端桌面連線，並使用電腦管理嵌入式管理單元 (**系統工具 > 本機使用者和群組 > 群組 > 遠端桌面使用者**)，將想要的帳戶新增到遠端桌面使用者本機群組。

## 詳細的遠端桌面錯誤疑難排解

如果沒有任何這些錯誤發生，您仍然無法連線至 VM，透過遠端桌面，讀取 [這篇文章](virtual-machines-rdp-detailed-troubleshoot.md) ，找出其他原因。


## 其他資源

[Azure IaaS (Windows) 診斷封裝](https://home.diagnostics.support.microsoft.com/SelfHelp?knowledgebaseArticleFilter=2976864)

[如何重設密碼或遠端桌面服務的 Windows 虛擬機器](virtual-machines-windows-reset-password.md)

[如何安裝和設定 Azure PowerShell](../install-configure-powershell.md)

[疑難排解以 Linux 為基礎之 Azure 虛擬機器的安全殼層 (SSH) 連線](virtual-machines-troubleshoot-ssh-connections.md)

[疑難排解 Azure 的虛擬機器上執行的應用程式的存取](virtual-machines-troubleshoot-access-application.md)





