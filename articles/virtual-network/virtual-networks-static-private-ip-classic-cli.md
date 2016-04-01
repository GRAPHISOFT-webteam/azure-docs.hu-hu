<properties 
   pageTitle="如何使用 CLI 在傳統模式中設定靜態私人 IP| Microsoft Azure"
   description="了解靜態私人 IP (DIP) 以及如何在傳統模式中使用 CLI 進行管理"
   services="virtual-network"
   documentationCenter="na"
   authors="telmosampaio"
   manager="carmonm"
   editor="tysonn"
   tags="azure-service-management"
/>
<tags 
   ms.service="virtual-network"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="12/11/2015"
   ms.author="telmos" />

# 如何在 Azure CLI 設定靜態的私人 IP 位址 (傳統)

[AZURE.INCLUDE [virtual-networks-static-private-ip-selectors-classic-include](../../includes/virtual-networks-static-private-ip-selectors-classic-include.md)]

[AZURE.INCLUDE [virtual-networks-static-private-ip-intro-include](../../includes/virtual-networks-static-private-ip-intro-include.md)]

[AZURE.INCLUDE [azure-arm-classic-important-include](../../includes/azure-arm-classic-important-include.md)] 本文章涵蓋傳統部署模型。 您也可以 [管理資源管理員部署模型中的靜態私人 IP 位址](virtual-networks-static-private-ip-arm-cli.md)。

下列範例 Azure CLI 命令會預期已經建立簡單的環境。 如果您想要執行命令，因為它們會顯示在這份文件，先建立測試環境中所述 [建立 vnet](virtual-networks-create-vnet-classic-cli.md)。

## 建立 VM 時如何指定靜態私人 IP 位址
若要建立新的 VM，名為 *DNS01* 新的雲端服務中名為 *TestService* 根據上述情況，請遵循下列步驟 ︰

1. 如果您從未使用 Azure CLI，請參閱 [安裝和設定 Azure CLI](xplat-cli-install.md) 並遵循指示，選取您的 Azure 帳戶和訂閱為止。
1. 執行 **azure 服務建立** 命令建立雲端服務。

        azure service create TestService --location uscentral

    預期的輸出：

        info:    Executing command service create
        info:    Creating cloud service
        data:    Cloud service name TestService
        info:    service create command OK
    
2. 執行 **azure 建立 vm** 命令來建立 VM。 請注意靜態私人 IP 位址的值。 輸出後顯示的清單可說明所使用的參數。

        azure vm create -l centralus -n DNS01 -w TestVNet -S "192.168.1.101" TestService bd507d3a70934695bc2128e3e5a255ba__RightImage-Windows-2012R2-x64-v14.2 adminuser AdminP@ssw0rd

    預期的輸出：

        info:    Executing command vm create
        warn:    --vm-size has not been specified. Defaulting to "Small".
        info:    Looking up image bd507d3a70934695bc2128e3e5a255ba__RightImage-Windows-2012R2-x64-v14.2
        info:    Looking up virtual network
        info:    Looking up cloud service
        warn:    --location option will be ignored
        info:    Getting cloud service properties
        info:    Looking up deployment
        info:    Retrieving storage accounts
        info:    Creating VM
        info:    OK
        info:    vm create command OK

    - **-l （或--location）**。 將要建立 VM 的 Azure 區域。 我們的案例， *centralus*。
    - **-n （或--）**。 要建立之 VM 的名稱。
    - **-w （或--虛擬網路名稱）**。 將建立 VM 之 VNet 的名稱。 
    - **-S （或--靜態 ip）**。 VM 的靜態私人 IP 位址
    - **TestService**。 將建立 VM 之雲端服務的名稱。
    - **bd507d3a70934695bc2128e3e5a255ba__RightImage-Windows-2012R2-x64-v14.2**。 用來建立 VM 的映像。
    - **adminuser**。 Windows VM 的本機系統管理員。
    - **AdminP@ssw0rd**。 Windows VM 的本機系統管理員密碼。

## 如何擷取 VM 的靜態私人 IP 位址資訊
若要檢視使用上述指令碼建立之 VM 的靜態私人 IP 位址資訊，請執行下列 Azure CLI 命令，並觀察值 *網路 StaticIP*:

    azure vm static-ip show DNS01

預期的輸出：

    info:    Executing command vm static-ip show
    info:    Getting virtual machines
    data:    Network StaticIP "192.168.1.101"
    info:    vm static-ip show command OK

## 如何移除 VM 的靜態私人 IP 位址
若要移除上述指令碼中新增至 VM 的靜態私人 IP 位址，請執行下列 Azure CLI 命令：
    
    azure vm static-ip remove DNS01

預期的輸出：

    info:    Executing command vm static-ip remove
    info:    Getting virtual machines
    info:    Reading network configuration
    info:    Updating network configuration
    info:    vm static-ip remove command OK

## 如何將靜態私人 IP 位址新增至現有的 VM
若要將靜態私人 IP 位址新增至使用上述指令碼建立之 VM，請執行下列命令：

    azure vm static-ip set DNS01 192.168.1.101

預期的輸出：

    info:    Executing command vm static-ip set
    info:    Getting virtual machines
    info:    Looking up virtual network
    info:    Reading network configuration
    info:    Updating network configuration
    info:    vm static-ip set command OK

## 後續步驟

- 深入了解 [保留公用 IP](../virtual-networks-reserved-public-ip) 位址。
- 深入了解 [執行個體層級公用 IP (ILPIP)](../virtual-networks-instance-level-public-ip) 位址。
- 請參閱 [保留的 IP REST Api](https://msdn.microsoft.com/library/azure/dn722420.aspx)。


