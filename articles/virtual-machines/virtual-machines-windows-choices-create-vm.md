<properties
    pageTitle="建立 Windows VM 的不同方式 | Microsoft Azure"
    description="列出使用資源管理員建立 Windows 虛擬機器的不同方式。"
    services="virtual-machines"
    documentationCenter=""
    authors="cynthn"
    manager="timlt"
    editor=""
    tags="azure-resource-manager"/>

<tags
    ms.service="virtual-machines"
    ms.devlang="na"
    ms.topic="index-page"
    ms.tgt_pltfrm="vm-windows"
    ms.workload="infrastructure-services"
    ms.date="10/22/2015"
    ms.author="cynthn"/>

# 使用資源管理員建立 Windows 虛擬機器的不同方式

Azure 提供建立虛擬機器的不同方式，因為虛擬機器適用於不同的使用者和用途。 這表示您需要針對虛擬機器以及建立方式進行一些選擇。 本文提供這些選項及指示連結的摘要說明。

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-rm-include.md)] 傳統部署模型。

我們最近推出 Azure 資源管理員範本做為一種方式，用以建立虛擬機器，並將它與其不同資源當成一個邏輯部署單位來管理。 以下提供這個可用方法的指示。 若要深入了解 Azure 資源管理員，以及如何管理資源當成一個單位，請參閱 [概觀] []。

## 工具選項

### GUI：Azure 入口網站

Azure 傳統入口網站的圖形化使用者介面是用來嘗試設定虛擬機器的簡單方法，特別是在您剛開始使用 Azure 時。 使用 Azure 入口網站來建立虛擬機器。

[建立執行 Windows 的虛擬機器][]

### 命令殼層：Azure CLI 或 Azure PowerShell

如果您偏好在命令殼層中工作，可選擇適用於 Mac 和 Linux 使用者的 Azure 命令列介面 (CLI) 或 Azure PowerShell (其中具有適用於 Azure 和自訂主控台的 Cmdlet)。

針對 Azure CLI，請參閱：

- [在使用適用於 Mac、Linux 及 Windows 的 Azure CLI 的虛擬機器操作中的對等資源管理員和服務管理命令][]
- [部署和管理虛擬機器上利用 Azure 資源管理員範本和 Azure CLI][]。

針對 Azure PowerShell，請參閱：

- [以資源管理員和 PowerShell 建立 Windows VM][]
- [利用資源管理員和 Azure PowerShell 建立及預先設定 Windows 虛擬機器][]
- [使用 Azure 資源管理員範本和 PowerShell 部署以及管理虛擬機器][]
- [利用 Resource Manager 範本和 PowerShell 建立 Windows 虛擬機器][]

### 開發環境：Visual Studio

[使用運算、網路和儲存體 .NET 程式庫部署 Azure 資源][]

## 作業系統和映像選項

根據您想要執行的作業系統來選擇映像。 Azure 與其合作夥伴提供許多映像，其中有些包括應用程式和工具。 或者，使用您自己的其中一個映像。 尋找您需要為應用程式使用中的資訊平台映像: [瀏覽並選取 [Windows PowerShell 和 Azure CLI 與 Azure 虛擬機器映像][]。

<!-- LINKS -->
[overview]: ../resource-group-overview.md

[Create a virtual machine running Windows]: virtual-machines-windows-tutorial.md

[Equivalent Resource Manager and Service Management commands for virtual machine operations with the Azure CLI for Mac, Linux, and Windows]:xplat-cli-azure-manage-vm-asm-arm.md
[Deploy and manage virtual machines using Azure Resource Manager templates and the Azure CLI]: virtual-machines-deploy-rmtemplates-azure-cli.md
[Create and preconfigure a Windows virtual machine with Resource Manager and Azure PowerShell]:  virtual-machines-ps-create-preconfigure-windows-resource-manager-vms.md
[Deploy and manage virtual machines using Azure Resource Manager templates and PowerShell]: virtual-machines-deploy-rmtemplates-powershell.md
[Create a Windows VM with Resource Manager and PowerShell]: virtual-machines-create-windows-powershell-resource-manager.md
[Create a Windows virtual machine with a Resource Manager template and PowerShell]: virtual-machines-create-windows-powershell-resource-manager-template-simple.md


[Navigate and select Azure virtual machine images with Windows PowerShell and the Azure CLI]: resource-groups-vm-searching.md
[Deploy Azure resources using the Compute, Network, and Storage .NET libraries]: virtual-machines-arm-deployment.md

[Sign in to the virtual machine]: virtual-machines-log-on-windows-server.md

[Base configuration test environment]: virtual-machines-base-configuration-test-environment.md

[Azure hybrid cloud test environments]: virtual-machines-hybrid-cloud-test-environments.md

