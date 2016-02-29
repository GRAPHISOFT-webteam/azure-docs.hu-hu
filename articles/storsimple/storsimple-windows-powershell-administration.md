<properties 
   pageTitle="PowerShell for StorSimple 裝置管理 | Microsoft Azure"
   description="了解如何使用 Windows PowerShell for StorSimple 管理 StorSimple 裝置。"
   services="storsimple"
   documentationCenter="NA"
   authors="alkohli"
   manager="carolz"
   editor="" />
<tags 
   ms.service="storsimple"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="TBD"
   ms.date="08/28/2015"
   ms.author="alkohli@microsoft.com" />

# 使用 Windows PowerShell for StorSimple 管理您的裝置

## 概觀

Windows PowerShell for StorSimple 提供命令列介面，可讓您用來管理 Microsoft Azure StorSimple 裝置。 一如其名，它是 Windows PowerShell 型的命令列介面，建置在有限制的 Runspace 之中。 從命令列使用者的觀點來看，有限制的 Runspace 就像是 Windows PowerShell 的受限版本。 這個介面具備 Windows PowerShell 的一些基本功能，同時又具有額外的專用 Cmdlet，相互搭配來管理 Microsoft Azure StorSimple 裝置。 

本文描述 Windows PowerShell for StorSimple 的功能，包括如何與此介面連線，以及使用此介面可執行之工作流程逐步程序的連結。 工作流程包含如何註冊您的裝置、在裝置上設定網路介面、安裝需要裝置處於維護模式的更新、變更裝置狀態、疑難排解您可能會遇到的任何問題。

閱讀本文之後，您將能夠：

- 使用 Windows PowerShell for StorSimple 連線至 StorSimple 裝置。

- 使用 Windows PowerShell for StorSimple 管理 StorSimple 裝置。

- 在 Windows PowerShell for StorSimple 中取得說明。

>[AZURE.NOTE]   

>- Windows PowerShell for StorSimple 的 Cmdlet 可讓您從序列主控台管理 StorSimple 裝置或透過「Windows PowerShell 遠端」進行管理。 如需每個個別的指令程式可以在此介面中使用的詳細資訊，請移至 [Windows PowerShell for StorSimple 的 cmdlet 參考](https://technet.microsoft.com/library/dn688168.aspx)。

>- Azure PowerShell StorSimple 的 Cmdlet 是另一組不同的指令程式，可讓您從命令列將 StorSimple 服務層級和移轉工作自動化。 StorSimple 的 Azure PowerShell cmdlet 的相關資訊，請移至 [Azure StorSimple cmdlet 參考文件](https://msdn.microsoft.com/library/azure/dn920427.aspx)。

您可以使用下列方法之一存取 Windows PowerShell for StorSimple：

- [連線至 StorSimple 裝置序列主控台](#connect-to-windows-powershell-for-storsimple-via-the-device-serial-console)
- [使用 Windows PowerShell 遠端連線至 StorSimple](#connect-remotely-to-storsimple-using-windows-powershell-for-storsimple)
    

## 透過裝置序列主控台連線至 Windows PowerShell for StorSimple

您可以 [下載 PuTTY](http://www.putty.org/) 或類似的終端機模擬軟體，以連線到 Windows PowerShell for StorSimple。 您需要將 PuTTY 設定為專門存取 Microsoft Azure StorSimple 裝置。 下列主題包含如何設定 PuTTy 並連接至裝置的詳細步驟。 並且說明序列主控台中的各種功能表選項。

### 關於序列主控台

當您透過序列主控台存取 StorSimple 裝置的 Windows PowerShell 介面時，會顯示一則橫幅訊息，後面接著功能表選項。 

橫幅訊息包含基本的 StorSimple 裝置資訊，例如：型號、名稱、已安裝的軟體版本、您要存取的控制器的狀態等。 下圖為橫幅訊息的範例。

![序列橫幅訊息](./media/storsimple-windows-powershell-administration/IC741098.png)



>[AZURE.IMPORTANT] 您可以使用橫幅訊息來識別您所連接的控制器是主動或被動。


下圖顯示序列主控台功能表中可用的各種 Runspace 選項。

![註冊裝置 2](./media/storsimple-windows-powershell-administration/IC740906.png)

您可選擇下列設定：

1. **完整存取權登入**
此選項可讓您連線 (使用適當的認證) 至 **SSAdminConsole** 本機控制器的 runspace。 (本機控制器是您目前正透過 StorSimple 裝置序列主控台存取的控制器)。 
也可使用此選項讓「Microsoft 支援」存取不受限制的 Runspace (支援工作階段)，以對任何可能的裝置問題進行疑難排解。 使用選項 1 登入之後，您可以允許 Microsoft 支援工程師執行特定 Cmdlet 去存取不受限制的 Runspace。 如需詳細資訊，請參閱 [啟動支援工作階段](storsimple-contact-microsoft-support.md#start-a-support-session-in-windows-powershell-for-storsimple)。

1. **登入對等控制器具有完整存取權**
此選項與選項 1 相同之處在於您可以連線 (使用適當的認證) 至 **SSAdminConsole** 對等控制器的 runspace。 因為 StorSimple 裝置是高可用性的裝置，具有兩個主動-被動組態的控制器；對等指的是您透過序列主控台存取的裝置中的其他控制器。
和選項 1 類似，此選項也可用於讓「Microsoft 支援」存取對等控制器上不受限制的 Runspace。

1. **連接具有限制存取**
此選項可用來存取 Windows PowerShell 介面以 limited 模式。 系統不會提示您輸入存取認證。 相較於選項 1 和 2，此選項會連線至更多限制的 Runspace。  採取選項 1 可使用的工作，其中一些在此 Runspace 中不能執行：

    - 重設為原廠設定
    - 變更密碼
    - 啟用或停用支援存取
    - 套用更新
    - 安裝 Hotfix 
                                                

    >[AZURE.NOTE] **This is the preferred option if you have forgotten the device administrator password and cannot connect through option 1 or 2.**

1. **變更語言**
此選項可讓您變更顯示語言的 Windows PowerShell 介面。 支援的語言有英文、日文、俄文、法文、南韓文、西班牙文、義大利文、德文、中文和巴西葡萄牙文。

請確定您使用下列的 PuTTY 設定從序列主控台連線到 Windows PowerShell 介面。

#### 設定 PuTTY

1. 在 PuTTY [重新設定] 對話方塊中，在 **類別** 窗格中，選取 **鍵盤**。

1. 請確定已選取下列選項 (當您啟動新的工作階段時，這些是預設設定)。 

    |鍵盤項目|選取|
    |---|---|
    |退格鍵|Control-? (127)|
    |Home 和 End 按鍵|標準|
    |功能鍵和數字鍵台|ESC[n~|
    |方向鍵的初始狀態|正常|
    |數字鍵台的初始狀態|正常|
    |啟用額外的鍵盤功能|Control-Alt 和 AltGr 不同|

    ![支援的 PuTTY 設定](./media/storsimple-windows-powershell-administration/IC740877.png)

1. 按一下 [ **套用**。

1. 在 **類別** 窗格中，選取 **轉譯**。

1. 在 **遠端字元集** 清單方塊中，選取 **utf-8**。

1. 在 **處理線條繪圖字元**, ，請選取 **使用 Unicode 線條繪圖字碼指標**。 下圖顯示正確的 PuTTY 選取。

    ![UTF PuTTY 設定](./media/storsimple-windows-powershell-administration/IC740878.png)

1. 按一下 [ **套用**。


您可以執行以下步驟使用 PuTTY 來連線至裝置序列主控台：

[AZURE.INCLUDE [storsimple-use-putty](../../includes/storsimple-use-putty.md)]


## 使用 Windows PowerShell 遠端連線至 StorSimple
使用 Windows PowerShell 遠端連線到 StorSimple 裝置。 當您以這種方式連線時，將不會看到功能表。 (只有當您使用裝置上的序列主控台進行連線時，才會看到功能表)。使用 Windows PowerShell 遠端連線到特定的 Runspace。 您也可以指定顯示語言。 

您使用設定的語言無關的顯示語言 **變更語言** 序列主控台功能表中的選項。 若未指定您的連線裝置的地區設定，遠端 PowerShell 將會自動為其挑選。

>[AZURE.NOTE] 如果您正在使用 Microsoft Azure 虛擬主機和 StorSimple 虛擬裝置，您可以使用 Windows PowerShell 遠端處理和虛擬主機連接到虛擬裝置。 如果您已從 Windows PowerShell 工作階段設定用來儲存資訊的主機上的共用位置，應該留意「所有人」主體只包含已驗證的使用者。 因此，如果您設定共用為允許所有人存取，且您連線未指定認證，則系統將使用未經驗證的「匿名」主體，而您將會看到錯誤。 若要修正此問題，在共用主機上您必須啟用「來賓」帳戶，然後給來賓帳戶完整存取權以進行共用，或您必須指定有效的認證以及 Windows PowerShell Cmdlet。


您可以透過 Windows PowerShell 遠端使用 HTTP 或 HTTPS 進行連線。 使用下列教學課程中的指示：

- [使用 http 遠端連線](storsimple-remote-connect.md#connect-through-http)
- [使用 https 遠端連線](storsimple-remote-connect.md#connect-through-https)

## 連線安全性考量

當您決定如何連線到 Windows PowerShell for StorSimple 時，請考慮下列事項：

- 直接連線至裝置序列主控台是安全的，但透過網路交換器連線至序列主控台則不是。 透過網路交換器連線至裝置序列主控台時，請務必注意安全性風險。

- 透過 HTTP 工作階段連線的安全性，比在網路上透過序列主控台連線更高。 雖然這不是最安全的方法，但在受信任的網路上是可接受的做法。

- 透過 HTTP 工作階段連線並使用自我簽署憑證，是最安全且建議的選項。


## 使用 Windows PowerShell for StorSimple 管理 StorSimple 裝置
下表摘要顯示所有可在 StorSimple 裝置的Windows PowerShell 介面中執行的一般管理工作和複雜工作流程。如需每個工作流程的詳細資訊，請按一下表格中的適當項目。

#### Windows PowerShell for StorSimple 的工作流程

|如果您想要執行此動作...|使用此程序。|
|---|---|
|登記裝置|[使用 Windows PowerShell for StorSimple 設定和註冊裝置](storsimple-deployment-walkthrough.md#step-3-configure-and-register-the-device-through-windows-powershell-for-storsimple) |
|設定 web proxy</br>檢視 Web Proxy 設定|[為 StorSimple 裝置設定 Web Proxy](storsimple-configure-web-proxy.md)|
|修改裝置上的 DATA 0 網路介面設定|[修改 StorSimple 裝置上的 DATA 0 網路介面](storsimple-modify-data-0.md)|
|停止控制器 </br> 重新啟動或關閉控制器 </br> 關閉裝置</br>將裝置重設為出廠預設設定。|[管理裝置控制器](storsimple-manage-device-controller.md)|
|安裝維護模式更新和 Hotfixe|[更新您的裝置](storsimple-update-device.md)|
|進入維護模式 </br>結束維護模式|[StorSimple 裝置模式](storsimple-device-modes.md)|
|建立支援封裝</br>解密並編輯支援封裝|[建立及管理支援封裝](storsimple-create-manage-support-package.md)|
|啟動支援工作階段</br>|[在 Windows PowerShell for StorSimple 中啟動支援工作階段](/storsimple-contact-microsoft-support.md#start-a-support-session-in-windows-powershell-for-storsimple)
 

## 在 Windows PowerShell for StorSimple 中取得說明

在 Windows PowerShell for StorSimple 中也有 Cmdlet 的說明。 此說明的最新版本也會線上提供，供您更新您系統上的說明。

在此介面中取得說明和在 Windows PowerShell 中類似，而且大部分與說明相關的 Cmdlet 都能用。 您可以在 TechNet 文件庫中找到協助 Windows PowerShell 的線上: [使用 Windows PowerShell 撰寫指令碼](http://go.microsoft.com/fwlink/?LinkID=108518)。

以下是此 Windows PowerShell 介面中各種說明類型的簡短描述，包括如何更新說明。

#### 取得 Cmdlet 的說明

- 使用下列命令可取得特定 Cmdlet 或功能的說明：`Get-Help <cmdlet-name>`

- 若要取得任何 Cmdlet 的線上說明，請使用前述 Cmdlet 搭配 `-Online` 參數：`Get-Help <cmdlet-name> -Online`

- 如需完整說明，可以使用 –Full 參數以及例如使用 `–Examples` 參數。

#### 更新說明

您可以輕鬆地更新 Windows PowerShell 介面中的說明。 請執行下列步驟更新您系統上的說明。

#### 更新 Cmdlet 說明

1. 開始使用 Windows PowerShell **系統管理員身分執行** 選項。

1. 在命令提示字元中，輸入：
    `Update-Help`

1. 將會安裝更新的說明檔。

1. 在說明檔案安裝之後，輸入：`Get-Help Get-Command`。 將會顯示可用說明的 Cmdlet 清單。


>[AZURE.NOTE] 若要取得任何 runspace 中的所有可用 cmdlet 的清單，請登入對應的功能表選項，並執行 `Get-Command` 指令程式。

## 後續步驟
如果遇到任何問題與 StorSimple 裝置執行其中一個以上的工作流程時，請參閱 [用於疑難排解 StorSimple 部署工具](storsimple-troubleshoot-deployment.md#tools-for-troubleshooting-storsimple-deployments)。


