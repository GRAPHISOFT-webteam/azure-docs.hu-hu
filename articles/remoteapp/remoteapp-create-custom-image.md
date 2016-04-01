<properties
    pageTitle="如何為 Azure RemoteApp 建立自訂範本映像 | Microsoft Azure"
    description="了解如何為 Azure RemoteApp 建立自訂範本映像 您可以使用此範本與混合式或雲端集合搭配。"
    services="remoteapp"
    documentationCenter=""
    authors="lizap"
    manager="mbaldwin"
    editor=""/>

<tags
    ms.service="remoteapp"
    ms.workload="compute"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="09/12/2015" 
    ms.author="elizapo"/>

# 如何為 Azure RemoteApp 建立自訂範本映像
Azure RemoteApp 會使用 Windows Server 2012 R2 範本映像來主控您要與使用者共用的所有程式。 若要建立自訂 RemoteApp 範本映像，您可以從現有的映像建立，或是建立新映像。 


> [AZURE.TIP] 您知道您可以從 Azure VM 建立映像嗎？ 這是真的，而且它可以減少匯入映像所花費的時間。 查明步驟 [這裡](remoteapp-image-on-azurevm.md)。

可上傳用於 Azure RemoteApp 的映像有下列需求：


- 映像大小應為 MB 的倍數。 如果您嘗試上傳的映像大小不是正確的倍數，上傳作業會失敗。
- 映像大小必須為 127 GB 或更小。
- 必須在 VHD 檔案上 (目前不支援 VHDX 檔案 [Hyper-V 虛擬硬碟])。
- VHD 不能是第 2 代虛擬機器。
- VHD 可以固定大小或動態擴充。 建議採用動態擴充 VHD 的做法，因為這會比固定大小 VHD 檔案更快速地上傳至 Azure。
- 磁碟必須使用主開機記錄 (MBR) 分割樣式進行初始化。 GUID 磁碟分割資料表 (GPT) 磁碟分割樣式不受支援。
- VHD 必須包含單一 Windows Server 2012 R2 安裝。 它可包含多個磁碟區，但只有其中一個包含 Windows 安裝。
- 必須安裝「遠端桌面工作階段主機 (RDSH)」角色和「桌面體驗」功能。
- 遠端桌面連線代理人角色必須 *不* 安裝。
- 必須停用「加密檔案系統 (EFS)」。
- 映像必須使用參數進行 sysprep 處理 **/oobe /generalize /shutdown** (不要使用 **/mode: vm** 參數)。
- 不支援從快照鏈結上傳您的 VHD。


**開始之前**

在建立服務之前，您必須執行下列作業：

- [註冊](http://azure.microsoft.com/services/remoteapp/) remoteapp。
- 在 Active Directory 中建立使用者帳戶，以做為 RemoteApp 服務帳戶。 限制此帳戶的權限，使其只能將機器加入網域中。 請參閱 [設定 Azure RemoteApp 的 Active Directory](remoteapp-ad.md) 如需詳細資訊。
- 收集內部部署網路的相關資訊：IP 位址資訊和 VPN 裝置詳細資料。
- 安裝 [PowerShell](../install-configure-powershell.md) 模組。
- 收集您想授與存取權之使用者的相關資訊。 這可以是使用者 Microsoft 帳戶資訊或 Active Directory 工作帳戶資訊。



## 建立範本映像 ##

以下是從頭建立新範本映像的高階步驟：

1.  找出 Windows Server 2012 R2 Update DVD 或 ISO 映像。
2.  建立 VHD 檔案。
4.  安裝 Windows Server 2012 R2。
5.  安裝「遠端桌面工作階段主機 (RDSH)」角色和「桌面體驗」功能。
6.  安裝您的應用程式所需的其他功能。
7.  安裝及設定您的應用程式。 若要更輕鬆地共用應用程式，新增任何應用程式或您要共用的程式 **啟動** 映像，特別是在 **%systemdrive%\ProgramData\Microsoft\Windows\Start Menu\Programs 的功能表。
8.  執行您的應用程式所需的任何其他 Windows 設定。
9.  停用「加密檔案系統 (EFS)」。
10. **必要 ︰** 前往 Windows Update 並安裝所有重要的更新。
9.  進行映像的 SYSPREP 處理。

建立新映像的詳細步驟為：

1.  找出 Windows Server 2012 R2 Update DVD 或 ISO 映像。
2.  使用「磁碟管理」建立 VHD 檔案。
    1.  啟動「磁碟管理」(diskmgmt.msc)。
    2.  建立會動態擴充、大小 40 GB 或更大的 VHD。 (請估計 Windows、您的應用程式和自訂所需的空間量。 已安裝 RDSH 角色和「桌面體驗」功能的 Windows Server 將需要約 10 GB 的空間)。
        1.  按一下 [ **動作 > 建立 VHD**。
        2.  指定位置、大小和 VHD 格式。 選取 **動態擴充**, ，然後按一下 [ **確定**。

            此作業會執行數秒。 VHD 建立完成後，您會在 [磁碟管理] 主控台中看見不具任何磁碟機代號、狀態為「未初始化」的新磁碟。

        - 以滑鼠右鍵按一下磁碟 （不是未配置空間），以及 [ **初始化磁碟**。 選取 **MBR** （主開機記錄） 做為磁碟分割樣式，然後按一下 **確定**。
        - 建立新的磁碟區 ︰ 以滑鼠右鍵按一下未配置的空間，然後按一下 **新增簡單磁碟區**。 您可以接受精靈中的預設值，但請務必指派磁碟機代號，以避免在上傳範本映像時發生問題。
        - 以滑鼠右鍵按一下磁碟，然後按一下 **中斷連結 VHD**。





1. 安裝 Windows Server 2012 R2：
    1. 建立新的虛擬機器。 在 [Hyper-V 管理員] 或 [用戶端 Hyper-V] 中使用 [新增虛擬機器精靈]。
        1. 在 [Specify Generation] 頁面上選擇 [  **第 1 代**。
        2. 在 [連接虛擬硬碟] 頁面中，選取 **使用現有的虛擬硬碟**, ，並瀏覽至您在上一個步驟中建立的 VHD。
        2. 在 [安裝選項] 頁面中，選取 [ **從開機 CD/DVD_ROM 安裝作業系統**, ，然後選取 [Windows Server 2012 R2 安裝媒體的位置。
        3. 在精靈中選擇其他安裝 Windows 和應用程式所需的選項。 完成精靈。
    2.  在精靈完成後，編輯虛擬機器的設定，並進行安裝 Windows 和程式，例如虛擬處理器數目，然後按一下所需的任何其他變更 **確定**。
    4.  連接到虛擬機器，並安裝 Windows Server 2012 R2。
1. 安裝「遠端桌面工作階段主機 (RDSH)」角色和「桌面體驗」功能：
    1. 啟動伺服器管理員。
    2. 按一下 [ **新增角色及功能** [歡迎] 畫面上，或從 **管理** 功能表。
    3. 按一下 [ **下一步** 在您開始前] 頁面上。
    4. 選取 **角色型或功能型安裝**, ，然後按一下 [ **下一步**。
    5. 從清單中，選取本機電腦，然後按一下 **下一步**。
    6. 選取 **遠端桌面服務**, ，然後按一下 [ **下一步**。
    7. 展開 **使用者介面與基礎結構** ，然後選取 **桌面體驗**。
    8. 按一下 [ **新增功能**, ，然後按一下 [ **下一步**。
    9. 在 [遠端桌面服務] 頁面中，按一下 [ **下一步**。
    10. 按一下 [ **遠端桌面工作階段主機**。
    11. 按一下 [ **新增功能**, ，然後按一下 [ **下一步**。
    12. 在 [確認安裝選項] 頁面中，選取 [ **需要時自動重新啟動目的地伺服器**, ，然後按一下 [ **是** 上重新啟動警告。
    13. 按一下 [ **安裝**。 電腦會重新啟動。
1.  安裝您的應用程式所需的其他功能，例如 .NET Framework 3.5。 若要安裝這些功能，請執行 [新增角色和功能] 精靈。
7.  安裝並設定您要透過 RemoteApp 發佈的程式和應用程式。

>[AZURE.IMPORTANT]
>
>請在安裝應用程式之前先安裝 RDSH 角色，以確保可在映像上傳至 RemoteApp 之前發現任何關於應用程式相容性的問題。
>
>請確定捷徑到您的應用程式 (**.lnk** 檔案) 會出現在 **啟動** ] 功能表上的所有使用者 (%systemdrive%\ProgramData\Microsoft\Windows\Start Menu\Programs)。 也請確認您在中看到的圖示 **啟動** 功能表是您想讓使用者看到。 如果不是，請變更圖示。 (沒有 *有* 加入至應用程式] 功能表上，但這樣做的話在 RemoteApp 中發佈應用程式更輕鬆。 否則，當您發佈應用程式時，您必須提供應用程式的安裝路徑。)


8.  執行您的應用程式所需的任何其他 Windows 設定。
9.  停用「加密檔案系統 (EFS)」。 在提高權限的命令視窗上執行下列命令：

        Fsutil behavior set disableencryption 1

    或者，您可以在登錄中設定或新增下列 DWORD 值：

        HKLM\System\CurrentControlSet\Control\FileSystem\NtfsDisableEncryption = 1
9.  如果您要建置您的映像，在 Azure 虛擬機器內，重新命名 **\%windir%\Panther\Unattend.xml** 檔案，因為這將會封鎖上傳指令碼稍後無法正常運作。 將此檔案名稱變更為 Unattend.old，以便當您需要回復部署時，仍舊保有這個檔案可用。
10. 前往 Windows Update 並安裝所有重要的更新。 您可能必須執行 Windows Update 很多次才能取得所有更新。 (有時候您安裝更新，而該更新本身又需要另外的更新。)
10. 進行映像的 SYSPREP 處理。 在提高權限的命令提示字元上執行下列命令：

    **C:\Windows\System32\sysprep\sysprep.exe /generalize /oobe /shutdown**

    **注意 ︰** 不使用 **/mode: vm** 即使這是虛擬機器的 SYSPREP 命令的參數。


## 後續步驟 ##
現在您的自訂範本映像已經就緒，您需要將該映像上傳至您的 RemoteApp 收藏。 使用下列文章中的資訊建立您的收藏：


- [如何建立 RemoteApp 的混合式收藏](remoteapp-create-hybrid-deployment.md)
- [如何建立 RemoteApp 的雲端收藏](remoteapp-create-cloud-deployment.md)
 

