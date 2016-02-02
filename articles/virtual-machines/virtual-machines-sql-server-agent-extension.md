<properties
    pageTitle="SQL Server IaaS 代理程式延伸模組 | Microsoft Azure"
    description="本主題會使用以傳統部署模型建立的資源，並說明 SQL Server 代理程式延伸模組，其可讓在 Azure 上執行 SQL Server 的 VM 使用自動化功能。"
    services="virtual-machines"
    documentationCenter=""
    authors="jeffgoll"
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
    ms.author="jeffreyg"/>


# SQL Server IaaS 代理程式延伸模組

這個延伸模組可讓 Azure 虛擬機器中的 SQL Server 使用這篇文章中列出的某些服務，這些服務只有在安裝此延伸模組時才能使用。 此延伸模組會為 Azure 入口網站中的 SQL Server 資源庫映像自動安裝。 它可以安裝在 Azure 中的任何 SQL Server VM，其已安裝 Azure VM 客體代理程式。

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-classic-include.md)] 資源管理員模型。


## 必要條件

使用 Powershell cmdlet 的需求：

- 最新的 Azure 命令列 SDK [這裡](http://azure.microsoft.com/downloads/)

在您的 VM 上使用延伸模組的需求：

- Azure VM 客體代理程式
- Windows Server 2012、Windows Server 2012 R2 或更新版本
- SQL Server 2012、SQL Server 2014 或更新版本

## 延伸模組可用的服務

- **SQL 自動化備份**：這項服務會針對 VM 中 SQL Server 的預設執行個體，自動化所有資料庫的備份排程。 若要查看此服務的詳細資訊，請參閱 [Azure 虛擬機器中 SQL Server 的自動化備份](virtual-machines-sql-server-automated-backup.md)。
- **SQL 自動化修補**: 這項服務可讓您設定維護視窗期間更新您的 VM 可能發生，您可以避免在您的工作負載的尖峰期間的更新。 若要查看此服務的詳細資訊，請參閱 [的 Azure 虛擬機器中 SQL Server 自動修補](virtual-machines-sql-server-automated-patching.md)。

## 利用 Powershell 新增延伸模組

如果您佈建您的 SQL Server VM 使用 [Azure 入口網站](https://portal.azure.com/), ，將會自動安裝延伸模組。 針對 SQL Server Vm 佈建 [Azure 傳統入口網站](https://manage.windowsazure.com), ，對於您授與自己 SQL 授權至 Vm，可以將此延伸模組加入至現有的 VM 使用下列 Azure PowerShell cmdlet。

**Set-AzureVMSqlServerExtension**

### 語法

Set-azurevmsqlserverextension [-VM] <IPersistentVM> [[-版本] <string>] [-AutoBackupSettings < AutoBackupSettings > ][-autopatchingsetttings <autopatchingsetttings>] [-確認 ][-whatif] [<CommonParameters>]
> [AZURE.NOTE] 建議省略 –Version 參數。 若沒有該參數，預設值為最新版本的延伸模組。

### 範例

    Get-AzureVM –ServiceName serviceName –Name vmName | Set-AzureVMSqlServerExtension –AutoBackupSettings $abs | Update-AzureVM**

## 檢查延伸模組的狀態

如果您想要檢查這個延伸模組以及與其相關聯之服務的狀態，您可以使用任一入口網站。 在您現有 VM 的詳細資料中，找出**設定**下的**延伸模組**。

您也可以使用下列 Azure PowerShell cmdlet。

**Get-AzureVMSqlServerExtension**

### 語法

Get AzureVMSqlServerExtension [[-VM] <IPersistentVM>] [[-版本] <string>] [<CommonParameters>]
> [AZURE.NOTE] 您可以省略 –Version 參數。 若沒有該參數，預設值為最新版本的延伸模組。

### 範例

    Get-AzureVM –ServiceName "service" –Name "vmname" | Get-AzureVMSqlServerExtension

## 利用 Powershell 移除延伸模組

如果您想要從您的 VM 中移除此延伸模組，您可以使用下列 Azure Powershell cmdlet。

**Remove-AzureVMSqlServerExtension**

### 語法

Remove-azurevmsqlserverextension VM <IPersistentVM> [<CommonParameters>]





