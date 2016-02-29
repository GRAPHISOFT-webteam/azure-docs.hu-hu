
<properties
        pageTitle="用 VM 擴充功能監視 Linux VM | Microsoft Azure"
        description="了解如何使用 Linux 診斷擴充功能監視 Azure 中 Linux VM 的效能和診斷資料。"
        services="virtual-machines"
        documentationCenter=""
        authors="NingKuang"
        manager="timlt"
        editor=""
        tags="azure-service-management"/>

<tags
        ms.service="virtual-machines"
        ms.workload="infrastructure-services"
        ms.tgt_pltfrm="vm-linux"
        ms.devlang="na"
        ms.topic="article"
        ms.date="07/20/2015"
        ms.author="Ning"/>


# 使用 Linux 診斷擴充功能監視 Linux VM 的效能和診斷資料

## 簡介

Linux 診斷延伸模組可利用下列功能協助使用者監視在 Microsoft Azure 上執行的 Linux VM：

- 將 Linux VM 的系統效能、診斷和 syslog 資料收集並上傳至使用者的儲存體資料表。
- 讓使用者自訂將要收集並上傳的資料度量。
- 讓使用者將指定的記錄檔上傳至指定的儲存體資料表。

若為 2.0 版，資料包括：

- 所有 Linux Rsyslog 記錄，包括系統、安全性及應用程式記錄檔。
- 在此指定的所有系統資料 [文件](https://scx.codeplex.com/wikipage?title=xplatproviders")。
- 使用者指定記錄檔。

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-classic-include.md)] 資源管理員模型。


## 如何啟用延伸模組
您可以透過啟用擴充功能 [Azure 入口網站](https://ms.portal.azure.com/#), ，Azure PowerShell 或 Azure CLI 指令碼。

若要檢視和設定系統和效能資料直接從 Azure 入口網站，請遵循下列 [步驟](http://azure.microsoft.com/blog/2014/09/02/windows-azure-virtual-machine-monitoring-with-wad-extension/ "Windows 部落格的 URL")。


本文將著重在透過 Azure CLI 命令啟動及設定延伸模組。這可讓您直接從儲存體資料表讀取和檢視資料。


## 必要條件
- Microsoft Azure Linux Agent 2.0.6 版或更新版本。
請注意，大部分的 Azure VM Linux 資源庫映像包含版本 2.0.6 或更新版本。 您可以執行 **WAAgent-版本** 確認 VM 中安裝的版本。 如果執行 VM 版本早於 2.0.6，您可以遵循這些 [指示](https://github.com/Azure/WALinuxAgent "instructions") 來更新它。
- [Azure CLI](./xplat-cli-install.md)。 請依照下列 [本指引](./xplat-cli-install.md) 來設定您的電腦上的 Azure CLI 環境。 安裝 Azure CLI 之後，您可以使用 **azure** 命令從命令列介面 (Bash、 終端機、 命令提示字元) 來存取 Azure CLI 命令。 例如，執行 **azure vm 延伸模組集--說明** 詳細使用方式，針對執行 **azure 登入** 若要登入 Azure，請執行 **vm list** 列出擁有在 Azure 的所有虛擬機器。
- 儲存資料的儲存體帳戶。 您需要先前建立的儲存體帳戶名稱和存取金鑰才能將資料上傳至您的儲存體。


## 使用 Azure CLI 命令啟動 Linux 診斷延伸模組

###  案例 1. 以預設的資料集啟動延伸模組
若為 2.0 版和更新版本，將會收集的預設資料包括：

- 所有 Rsyslog 資訊 (包括系統、安全性及應用程式記錄檔)。  
- 基礎系統資料的核心集，注意完整的資料集是在此描述 [文件](https://scx.codeplex.com/wikipage?title=xplatproviders)。
如果您想要啟用額外的資料，請繼續進行案例 2 和 3 的步驟。

步驟 1. 使用下列內容建立名為 PrivateConfig.json 的檔案。

    {
        "storageAccountName":"the storage account to receive data",
        "storageAccountKey":"the key of the account"
    }

步驟 2. 執行 **azure vm 延伸模組設定 vm_name LinuxDiagnostic Microsoft.OSTCExtensions 2。* --private-config-path PrivateConfig.json**。


###   案例 2. 自訂效能監視器度量  
本節描述如何自訂效能和診斷資料的資料表。

步驟 1. 使用下一個範例中顯示的內容建立名為 PrivateConfig.json 的檔案。 指定您想要收集的特定資料。

針對所有支援的提供者和變數，請參考這 [文件](https://scx.codeplex.com/wikipage?title=xplatproviders)。 您可以有多個查詢，並藉由將更多查詢附加至指令碼，將它們儲存成多個資料表。

根據預設，一律會收集 Rsyslog 資料。

    {
        "storageAccountName":"storage account to receive data",
        "storageAccountKey":"key of the account",
        "perfCfg":[
            {"query":"SELECT PercentAvailableMemory, AvailableMemory, UsedMemory ,PercentUsedSwap FROM SCX_MemoryStatisticalInformation","table":"LinuxMemory"
            }
          ]
    }


步驟 2. 執行 **azure vm 延伸模組設定 vm_name LinuxDiagnostic Microsoft.OSTCExtensions 2。*
--private-config-path PrivateConfig.json**。


###   案例 3. 上傳您自己的記錄檔
本節說明如何收集和上傳特定記錄檔至儲存體帳戶。
您必須指定記錄檔的路徑，並指定要儲存記錄檔的資料表名稱。 您可以將多個檔案/資料表項目加入至指令碼，以擁有多個記錄檔。

步驟 1. 使用下列內容建立名為 PrivateConfig.json 的檔案。

    {
        "storageAccountName":"the storage account to receive data",
        "storageAccountKey":"key of the account",
        "fileCfg":[
            {"file":"/var/log/mysql.err",
             "table":"mysqlerr"
            }
          ]
    }


步驟 2. 執行 **azure vm 延伸模組設定 vm_name LinuxDiagnostic Microsoft.OSTCExtensions 2。*
--private-config-path PrivateConfig.json**。


###   案例 4. 停用 Linux 監視器延伸模組
步驟 1. 使用下列內容建立名為 PrivateConfig.json 的檔案。

    {
        "storageAccountName":"the storage account to receive data",
        "storageAccountKey":"the key of the account",
        “perfCfg”:[],
        “enableSyslog”:”False”
    }


步驟 2. 執行 **azure vm 延伸模組設定 vm_name LinuxDiagnostic Microsoft.OSTCExtensions 2。*
--private-config-path PrivateConfig.json**。


## 檢閱資料
效能和診斷資料會儲存在 Azure 儲存體資料表中。 檢閱 [這篇文章](storage-ruby-how-to-use-table-storage.md) 以了解如何存取儲存體資料表使用 Azure CLI 指令碼中的資料。

此外，您可以使用下列 UI 工具來存取資料：

1.  使用 Visual Studio 伺服器總管。 瀏覽至儲存體帳戶。 執行 VM 約 5 分鐘後，您應該會看到四個預設資料表："LinuxCpu"、"LinuxDisk"、"LinuxMemory" 和 "Linuxsyslog"。 按兩下資料表名稱以檢視資料。
2.  使用 [Azure 儲存體總管](https://azurestorageexplorer.codeplex.com/ "Azure 儲存體總管") 來存取資料。

![image](./media/virtual-machines-linux-diagnostic-extension/no1.png)

如果已啟用案例 2 和 3 中指定的 fileCfg 或 perfCfg，您可以使用先前的工具來檢視非預設的資料。



## 已知問題
- 若為 2.0 版，只能透過指令碼存取 Rsyslog 資訊和客戶指定記錄檔。
- 若為 2.0 版，如果您已經先透過指令碼啟用 Linux 診斷延伸模組，您就無法從入口網站檢視資料。 如果您先從入口網站啟用延伸模組，指令碼就會持續運作。

