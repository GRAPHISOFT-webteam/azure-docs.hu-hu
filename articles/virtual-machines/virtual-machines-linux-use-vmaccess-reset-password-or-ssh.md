<properties
    pageTitle="從 Azure CLI 重設 Linux VM 密碼 | Microsoft Azure"
    description="如何使用VMAccess 延伸模組從 Azure 傳統入口網站或 CLI 來重設 Linux VM 密碼和 SSH 金鑰、SSH 組態並刪除使用者帳戶。"
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
    ms.date="08/28/2015"
    ms.author="cynthn"/>

# 如何為 Linux 虛擬機器重設密碼或 SSH #

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-classic-include.md)] 資源管理員模型。


如果您無法連接至 Linux 虛擬機器，因為密碼遺失、 不正確的安全殼層 (SSH) 金鑰或 SSH 設定有問題，請重設密碼或 SSH 金鑰或修復 SSH 組態中使用 Azure 入口網站或 VMAccessForLinux 延伸模組。 請注意，這篇文章適用於使用建立虛擬機器 **傳統** 部署模型。

## Azure 入口網站

若要重設 SSH 組態中的 [Azure 入口網站](https://portal.azure.com), ，按一下 [ **瀏覽** > **虛擬機器** > *Linux 虛擬機器* > **重設遠端存取**。 範例如下。

![](./media/virtual-machines-linux-use-vmaccess-reset-password-or-ssh/Portal-RDP-Reset-Linux.png)

若要重設的名稱和密碼，以 sudo 權限或 SSH 公開金鑰中的使用者帳戶 [Azure 入口網站](https://portal.azure.com), ，按一下 [ **瀏覽** > **虛擬機器** > *Linux 虛擬機器* > **所有設定** > **密碼重設**。 範例如下。

![](./media/virtual-machines-linux-use-vmaccess-reset-password-or-ssh/Portal-PW-Reset-Linux.png)


## Azure CLI 和 PowerShell

您將需要下列項目：

- Microsoft Azure Linux Agent 2.0.5 版或更新版本。 虛擬機器映像庫中的大多數 Linux 映像均包含 2.0.5 版。 若要找出所安裝的版本，執行 **waagent-版本**。 若要更新代理程式，請依照 [Azure Linux 代理程式使用者指南] 中的指示。
- Azure 命令列介面 (CLI)。 如需設定 Azure CLI 的詳細資訊，請參閱 [安裝和設定 Azure 命令列介面](../xplat-cli-install.md)。
- Azure PowerShell。 您將使用 Set-AzureVMExtension Cmdlet 中的命令，自動載入和設定 VMAccessForLinux 延伸模組。 如需設定 Azure PowerShell 的詳細資訊，請參閱 < 如何安裝和設定 Azure PowerShell]。
- 新的密碼或一組 SSH 金鑰 (如果要重設其中一項)。 如果您想要重設 SSH 組態，則不需要這些。

### 不需安裝

不需要先安裝 VMAccess 延伸項目即可使用。 只要虛擬機器安裝 Linux 代理程式，延伸模組便會自動載入時執行的 Azure PowerShell 命令使用 **Set-azurevmextension** 指令程式。

## 使用 Azure CLI

使用 Azure CLI，您將能夠使用 **azure** 命令從命令列介面 (Bash、 終端機、 命令提示字元) 來存取命令。 執行 **azure vm 延伸模組設定 – 協助** 詳細的擴充功能使用方式。

使用 Azure CLI，您可以執行下列工作：

+ [重設密碼](#pwresetcli)
+ [重設 SSH 金鑰](#sshkeyresetcli)
+ [重設密碼和 SSH 金鑰](#resetbothcli)
+ [建立新的 sudo 使用者帳戶](#createnewsudocli)
+ [重設 SSH 組態](#sshconfigresetcli)
+ [刪除使用者](#deletecli)
+ [顯示 VMAccess 延伸模組的狀態](#statuscli)

### <a name="pwresetcli"></a>重設密碼

步驟 1：使用這些內容來建立名為 PrivateConf.json 的檔案，以取代預留位置值。

    {
    "username":"currentusername",
    "password":"newpassword",
    "expiration":"2016-01-01",
    }

步驟 2：執行這個命令，以您的虛擬機器名稱來取代 "vmname"。

    azure vm extension set vmname VMAccessForLinux Microsoft.OSTCExtensions 1.* –-private-config-path PrivateConf.json

### <a name="sshkeyresetcli"></a>重設 SSH 金鑰

步驟 1：使用這些內容來建立名為 PrivateConf.json 的檔案，以取代預留位置值。

    {
    "username":"currentusername",
    "ssh_key":"contentofsshkey",
    }

步驟 2：執行這個命令，以您的虛擬機器名稱來取代 "vmname"。

    azure vm extension set vmname VMAccessForLinux Microsoft.OSTCExtensions 1.* --private-config-path PrivateConf.json

### <a name="resetbothcli"></a>重設密碼和 SSH 金鑰

步驟 1：使用這些內容來建立名為 PrivateConf.json 的檔案，以取代預留位置值。

    {
    "username":"currentusername",
    "ssh_key":"contentofsshkey",
    "password":"newpassword",
    }

步驟 2：執行這個命令，以您的虛擬機器名稱來取代 "vmname"。

    azure vm extension set vmname VMAccessForLinux Microsoft.OSTCExtensions 1.* --private-config-path PrivateConf.json

### <a name="createnewsudocli"></a>建立新的 sudo 使用者帳戶

如果您忘記使用者名稱，可以使用 VMAccess，利用 sudo 授權單位來建立一個新的帳戶。 在此案例中，將不會修改現有的使用者名稱和密碼。

若要建立新的 sudo 使用者與密碼存取權限，使用中的指令碼 [重設密碼](#pwresetcli) 並指定新的使用者名稱。

若要建立新的 sudo 使用者 SSH 金鑰存取權限，使用中的指令碼 [重設 SSH 金鑰](#sshkeyresetcli) 並指定新的使用者名稱。

您也可以使用 [重設密碼和 SSH 金鑰](#resetbothcli) 來建立新使用者的密碼和 SSH 金鑰存取權限。

### <a name="sshconfigresetcli"></a>重設 SSH 組態

如果 SSH 組態處於不良的狀態，您可能也會無法存取 VM。 您可以使用 VMAccess 延伸模組，將組態重設為其預設狀態。 若要這樣做，只需將 "reset_ssh" 金鑰設為 "True"。 延伸模組將會重新啟動 SSH 伺服器、開啟 VM 上的 SSH 連接埠，然後將 SSH 組態重設為預設值。 使用者帳戶 (名稱、密碼或 SSH 金鑰) 將不會變更。

> [AZURE.NOTE] 取得重設 SSH 組態檔位於 /etc/ssh/sshd_config。

步驟 1：使用此內容建立名為 PrivateConf.json 的檔案。

    {
    "reset_ssh":"True",
    }

步驟 2：執行這個命令，以您的虛擬機器名稱來取代 "vmname"。

    azure vm extension set vmname VMAccessForLinux Microsoft.OSTCExtensions 1.* --private-config-path PrivateConf.json

### <a name="deletecli"></a>刪除使用者

如果您想要刪除使用者帳戶而不直接登入 VM，可以使用這個指令碼。

步驟 1：使用這個內容來建立名為 PrivateConf.json 的檔案，以取代預留位置值。

    {
    "remove_user":"usernametoremove",
    }

步驟 2：執行這個命令，以您的虛擬機器名稱來取代 "vmname"。

    azure vm extension set vmname VMAccessForLinux Microsoft.OSTCExtensions 1.* --private-config-path PrivateConf.json

### <a name="statuscli"></a>顯示 VMAccess 延伸模組的狀態

若要顯示 VMAccess 延伸模組的狀態，請執行下列命令。

    azure vm extension get


## 使用 Azure PowerShell

您將使用 **Set-azurevmextension** cmdlet，進行任何變更 VMAccess 讓您進行。 在所有情況下，都可以開始使用雲端服務名稱和虛擬機器名稱來取得虛擬機器物件，並將它儲存在變數中。

填入雲端服務和虛擬機器名稱，然後在系統管理員層級 Azure PowerShell 命令提示字元執行下列命令。 取代引號中，包括裡面 < 和 > 字元。

    $CSName = "<cloud service name>"
    $VMName = "<virtual machine name>"
    $vm = Get-AzureVM -ServiceName $CSName -Name $VMName

如果您不知道雲端服務和虛擬機器名稱，執行 **Get-azurevm** 以顯示目前訂閱中的所有 Vm 的該項資訊。


> [AZURE.NOTE] 以 $ 開頭的命令列在 PowerShell 命令中設定使用更新版本的 PowerShell 變數。

如果您已經使用 Azure 傳統入口網站建立虛擬機器，請執行下列額外的命令。

    $vm.GetInstance().ProvisionGuestAgent = $true

此命令將避免在下列各節執行 Set AzureVMExtension 命令時發生「必須先在 VM 物件啟用佈建客體代理程式，才能設定 IaaS VM 存取延伸項目」錯誤。

然後，您可以執行下列工作：

+ [重設密碼](#password)
+ [重設 SSH 金鑰](#SSHkey)
+ [重設密碼和 SSH 金鑰](#both)
+ [重設 SSH 組態](#config)
+ [刪除使用者](#delete)
+ [顯示 VMAccess 延伸模組的狀態](#status)

### <a name="password"></a>重設密碼

填入目前的 Linux 使用者名稱和新的密碼，然後執行這些命令。

    $UserName = "<current Linux account name>"
    $Password = "<new password>"
    $PrivateConfig = '{"username":"' + $UserName + '", "password": "' +  $Password + '"}'
    $ExtensionName = "VMAccessForLinux"
    $Publisher = "Microsoft.OSTCExtensions"
    $Version =  "1.*"
    Set-AzureVMExtension -ExtensionName $ExtensionName -VM $vm -Publisher $Publisher -Version $Version -PrivateConfiguration $PrivateConfig | Update-AzureVM

> [AZURE.NOTE] 如果您想要重設密碼或 SSH 金鑰，為現有的使用者帳戶，請務必輸入確切的使用者名稱。 如果輸入不同的名稱，VMAccess 延伸項目會建立新的使用者帳戶，並將密碼指派給該帳戶。


### <a name="SSHKey"></a>重設 SSH 金鑰

填入目前 Linux 使用者名稱以及包含 SSH 金鑰的憑證路徑，然後執行這些命令。

    $UserName = "<current Linux user name>"
    $Cert = Get-Content "<certificate path>"
    $PrivateConfig = '{"username":"' + $UserName + '", "ssh_key":"' + $cert + '"}'
    $ExtensionName = "VMAccessForLinux"
    $Publisher = "Microsoft.OSTCExtensions"
    $Version =  "1.*"
    Set-AzureVMExtension -ExtensionName $ExtensionName -VM  $vm -Publisher $Publisher -Version $Version -PrivateConfiguration $PrivateConfig | Update-AzureVM

### <a name="both"></a>重設密碼和 SSH 金鑰

填入目前 Linux 使用者名稱、新的密碼，以及包含 SSH 金鑰的憑證路徑，然後執行這些命令。

    $UserName = "<current Linux user name>"
    $Password = "<new password>"
    $Cert = Get-Content "<certificate path>"
    $PrivateConfig = '{"username":"' + $UserName + '", "password": "' +  $Password + '", "ssh_key":"' + $cert + '"}'
    $ExtensionName = "VMAccessForLinux"
    $Publisher = "Microsoft.OSTCExtensions"
    $Version =  "1.*"
    Set-AzureVMExtension -ExtensionName $ExtensionName -VM  $vm -Publisher $Publisher -Version $Version -PrivateConfiguration $PrivateConfig | Update-AzureVM

### <a name="config"></a>重設 SSH 組態

SSH 組態中的錯誤可導致您無法存取虛擬機器。 您可以將 SSH 組態重設為其預設狀態來修正此問題。 這將移除組態中所有新的存取參數，例如使用者名稱、密碼和 SSH 金鑰，但是這不會變更使用者帳戶的密碼或 SSH 金鑰。 延伸項目會重新啟動 SSH 伺服器，並開啟虛擬機器上的 SSH 連接埠，然後將 SSH 組態重設為預設值。

執行這些命令。

    $PrivateConfig = '{"reset_ssh": "True"}'
    $ExtensionName = "VMAccessForLinux"
    $Publisher = "Microsoft.OSTCExtensions"
    $Version = "1.*"
    Set-AzureVMExtension -ExtensionName $ExtensionName -VM  $vm -Publisher $Publisher -Version $Version -PrivateConfiguration $PrivateConfig | Update-AzureVM

> [AZURE.NOTE] SSH 組態檔位於 /etc/ssh/sshd_config。

### <a name="delete"></a> 刪除使用者

填入要刪除的 Linux 使用者名稱，然後執行這些命令。

    $UserName = "<Linux user name to delete>"
    $PrivateConfig = "{"remove_user": "' + $UserName + '"}"
    $ExtensionName = "VMAccessForLinux"
    $Publisher = "Microsoft.OSTCExtensions"
    $Version = "1.*"
    Set-AzureVMExtension -ExtensionName $ExtensionName -VM $vm -Publisher $Publisher -Version $Version -PrivateConfiguration $PrivateConfig | Update-AzureVM


### <a name="status"></a>顯示 VMAccess 延伸模組的狀態

若要顯示 VMAccess 延伸模組的狀態，請執行下列命令。

    $vm.GuestAgentStatus


## 其他資源

[Azure VM 延伸模組和功能][]

[連線到 Azure 虛擬機器透過 RDP 或 SSH][]


<!--Link references-->
[Azure Linux Agent User Guide]: virtual-machines-linux-agent-user-guide.md
[How to install and configure Azure PowerShell]: ../install-configure-powershell.md
[Azure VM Extensions and Features]: virtual-machines-extensions-features.md
[Connect to an Azure virtual machine with RDP or SSH]: http://msdn.microsoft.com/library/azure/dn535788.aspx

