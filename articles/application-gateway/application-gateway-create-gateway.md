<properties
   pageTitle="建立、啟動或刪除應用程式閘道 | Microsoft Azure"
   description="本頁面提供建立、設定、啟動和刪除 Azure 應用程式閘道的指示。"
   documentationCenter="na"
   services="application-gateway"
   authors="joaoma"
   manager="jdial"
   editor="tysonn"/>
<tags
   ms.service="application-gateway"
   ms.devlang="na"
   ms.topic="hero-article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="11/10/2015"
   ms.author="joaoma"/>


# 建立、啟動或刪除應用程式閘道

應用程式閘道是第 7 層負載平衡器。 它提供在不同伺服器之間進行容錯移轉、效能路由傳送 HTTP 要求，而不論它們是在雲端或內部部署中。 應用程式閘道具有下列應用程式傳遞功能：HTTP 負載平衡、以 Cookie 為基礎的工作階段同質性、SSL 卸載。

> [AZURE.SELECTOR]
- [Azure Classic PowerShell](application-gateway-create-gateway.md)
- [Azure Resource Manager PowerShell](application-gateway-create-gateway-arm.md)
- [Azure Resource Manager template](application-gateway-create-gateway-arm-template.md)



<BR>

本文將逐步引導您完成建立和設定、啟動及刪除應用程式閘道的步驟。

>[AZURE.IMPORTANT] 使用 Azure 資源之前，請務必了解 Azure 目前有「資源管理員」和「傳統」兩種部署模型。 請確定您了解 [部署模型和工具](azure-classic-rm.md) 之前使用的任何 Azure 資源。 您可以按一下本文頂端的索引標籤，檢視不同工具的文件。本文件將說明使用 Azure 傳統部署建立應用程式閘道的方式。 若要使用的資源管理員版本，請移至 [建立資源管理員搭配使用的應用程式閘道部署](application-gateway-create-gateway-arm.md)。





## 開始之前

1. 使用 Web Platform Installer 安裝最新版的 Azure PowerShell Cmdlet 。 您可以下載並安裝最新版的 **Windows PowerShell** 區段 [下載頁面](http://azure.microsoft.com/downloads/)。
2. 請確認您的運作中虛擬網路具有有效子網路。 確定沒有虛擬機器或雲端部署正在使用子網路。 應用程式閘道必須單獨位於虛擬網路子網路中。
3. 您將設定為使用應用程式閘道的伺服器必須存在，或是在虛擬網路中建立其端點，或是指派公用 IP/VIP。

## 建立應用程式閘道需要什麼？

當您使用 **New-AzureApplicationGateway** 命令來建立應用程式閘道時，不需進行任何設定，而且必須使用 XML 或設定物件來設定新建立的資源。


值如下：

- **後端伺服器集區：**後端伺服器的 IP 位址清單。 列出的 IP 位址應屬於虛擬網路子網路或是公用 IP/VIP。
- **後端伺服器集區設定：**每個集區都有一些設定，例如連接埠、通訊協定和以 Cookie 為基礎的同質性。 這些設定會繫結至集區，並套用至集區內所有伺服器。
- **前端連接埠：**此連接埠是在應用程式閘道上開啟的公用連接埠。 流量會達到此連接埠，然後重新導向至其中一個後端伺服器。
- **接聽程式：**接聽程式具有前端連接埠、通訊協定 (Http 或 Https，都區分大小寫) 和 SSL 憑證名稱 (如果已設定 SSL 卸載)。
- **規則：**規則會繫結接聽程式和後端伺服器集區，並定義流量達到特定接聽程式時應該導向至哪個後端伺服器集區。 目前只支援*基本*規則。 *基本*規則是循環配置資源的負載分配。



## 建立新的應用程式閘道

您必須遵守下列步驟順序來建立應用程式閘道：

1. 建立應用程式閘道資源。
2. 建立設定 XML 檔案或設定物件。
3. 認可新建立應用程式閘道資源的設定。

### 建立應用程式閘道資源

若要建立的閘道，使用 `New-azureapplicationgateway` cmdlet，替換您自己的值。 請注意，此時不會開始為閘道計費。 會在稍後的步驟中於成功啟動閘道之後開始計費。

下列範例會使用虛擬網路 (稱為 "testvnet1") 與子網路 (稱為 "subnet-1") 來建立新的應用程式閘道。


    PS C:\> New-AzureApplicationGateway -Name AppGwTest -VnetName testvnet1 -Subnets @("Subnet-1")
    
    VERBOSE: 4:31:35 PM - Begin Operation: New-AzureApplicationGateway
    VERBOSE: 4:32:37 PM - Completed Operation: New-AzureApplicationGateway
    Name       HTTP Status Code     Operation ID                             Error
    ----       ----------------     ------------                             ----
    Successful OK                   55ef0460-825d-2981-ad20-b9a8af41b399

 *Description*、*InstanceCount* 和 *GatewaySize* 為選用參數。


**驗證** ，已建立閘道，您可以使用 `Get AzureApplicationGateway` 指令程式。




    PS C:\> Get-AzureApplicationGateway AppGwTest
    Name          : AppGwTest
    Description   :
    VnetName      : testvnet1
    Subnets       : {Subnet-1}
    InstanceCount : 2
    GatewaySize   : Medium
    State         : Stopped
    VirtualIPs    : {}
    DnsName       :

>[AZURE.NOTE]  InstanceCount** 的預設值是 2，最大值是 10。 *GatewaySize* 的預設值是 Medium。 您可以選擇 Small、Medium 和 Large。


 因為尚未啟動閘道，所以 *Vip* 和 *DnsName* 會顯示為空白。 閘道處於執行中狀態之後，將會建立這些項目。

## 設定應用程式閘道

您可以使用 XML 或設定物件來設定應用程式閘道。

## 使用 XML 設定應用程式閘道

在下列範例中，您將使用 XML 檔案來設定所有的應用程式閘道設定，並將它們認可到應用程式閘道資源。

### 步驟 1

將下列文字複製到 [記事本]。

    <?xml version="1.0" encoding="utf-8"?>
    <ApplicationGatewayConfiguration xmlns:i="http://www.w3.org/2001/XMLSchema-instance" xmlns="http://schemas.microsoft.com/windowsazure">
        <FrontendPorts>
            <FrontendPort>
                <Name>(name-of-your-frontend-port)</Name>
                <Port>(port number)</Port>
            </FrontendPort>
        </FrontendPorts>
        <BackendAddressPools>
            <BackendAddressPool>
                <Name>(name-of-your-backend-pool)</Name>
                <IPAddresses>
                    <IPAddress>(your-IP-address-for-backend-pool)</IPAddress>
                    <IPAddress>(your-second-IP-address-for-backend-pool)</IPAddress>
                </IPAddresses>
            </BackendAddressPool>
        </BackendAddressPools>
        <BackendHttpSettingsList>
            <BackendHttpSettings>
                <Name>(backend-setting-name-to-configure-rule)</Name>
                <Port>80</Port>
                <Protocol>[Http|Https]</Protocol>
                <CookieBasedAffinity>Enabled</CookieBasedAffinity>
            </BackendHttpSettings>
        </BackendHttpSettingsList>
        <HttpListeners>
            <HttpListener>
                <Name>(name-of-the-listener)</Name>
                <FrontendPort>(name-of-your-frontend-port)</FrontendPort>
                <Protocol>[Http|Https]</Protocol>
            </HttpListener>
        </HttpListeners>
        <HttpLoadBalancingRules>
            <HttpLoadBalancingRule>
                <Name>(name-of-load-balancing-rule)</Name>
                <Type>basic</Type>
                <BackendHttpSettings>(backend-setting-name-to-configure-rule)</BackendHttpSettings>
                <Listener>(name-of-the-listener)</Listener>
                <BackendAddressPool>(name-of-your-backend-pool)</BackendAddressPool>
            </HttpLoadBalancingRule>
        </HttpLoadBalancingRules>
    </ApplicationGatewayConfiguration>

編輯設定項目括號間的值。 以 .xml 副檔名儲存檔案
>[AZURE.IMPORTANT] 通訊協定項目 Http 或 Https 會區分大小寫。

下列範例示範如何使用設定檔來設定應用程式閘道，使公用連接埠 80 上的 Http 流量達到負載平衡，並將網路流量傳送到 2 個 IP 位址之間的後端連接埠 80：

    <?xml version="1.0" encoding="utf-8"?>
    <ApplicationGatewayConfiguration xmlns:i="http://www.w3.org/2001/XMLSchema-instance" xmlns="http://schemas.microsoft.com/windowsazure">
        <FrontendPorts>
            <FrontendPort>
                <Name>FrontendPort1</Name>
                <Port>80</Port>
            </FrontendPort>
        </FrontendPorts>
        <BackendAddressPools>
            <BackendAddressPool>
                <Name>BackendPool1</Name>
                <IPAddresses>
                    <IPAddress>10.0.0.1</IPAddress>
                    <IPAddress>10.0.0.2</IPAddress>
                </IPAddresses>
            </BackendAddressPool>
        </BackendAddressPools>
        <BackendHttpSettingsList>
            <BackendHttpSettings>
                <Name>BackendSetting1</Name>
                <Port>80</Port>
                <Protocol>Http</Protocol>
                <CookieBasedAffinity>Enabled</CookieBasedAffinity>
            </BackendHttpSettings>
        </BackendHttpSettingsList>
        <HttpListeners>
            <HttpListener>
                <Name>HTTPListener1</Name>
                <FrontendPort>FrontendPort1</FrontendPort>
                <Protocol>Http</Protocol>
            </HttpListener>
        </HttpListeners>
        <HttpLoadBalancingRules>
            <HttpLoadBalancingRule>
                <Name>HttpLBRule1</Name>
                <Type>basic</Type>
                <BackendHttpSettings>BackendSetting1</BackendHttpSettings>
                <Listener>HTTPListener1</Listener>
                <BackendAddressPool>BackendPool1</BackendAddressPool>
            </HttpLoadBalancingRule>
        </HttpLoadBalancingRules>
    </ApplicationGatewayConfiguration>

### 步驟 2

接下來，您將設定應用程式閘道。 您將使用 `組 AzureApplicationGatewayConfig` 指令程式與組態 XML 檔案。


    PS C:\> Set-AzureApplicationGatewayConfig -Name AppGwTest -ConfigFile "D:\config.xml"
    
    VERBOSE: 7:54:59 PM - Begin Operation: Set-AzureApplicationGatewayConfig
    VERBOSE: 7:55:32 PM - Completed Operation: Set-AzureApplicationGatewayConfig
    Name       HTTP Status Code     Operation ID                             Error
    ----       ----------------     ------------                             ----
    Successful OK                   9b995a09-66fe-2944-8b67-9bb04fcccb9d

## 使用設定物件設定應用程式閘道

下列範例示範如何使用設定物件來設定應用程式閘道。 所有的設定項目都必須個別設定，然後再加入至應用程式閘道設定物件。 建立組態物件之後，您將使用 `組 AzureApplicationGateway` 命令以認可至先前建立的應用程式閘道資源組態。
>[AZURE.NOTE] 在將值指派到各個設定物件之前，您必須宣告 PowerShell 要用來儲存的物件種類。 用來建立個別項目的第一行會定義將使用哪些 Microsoft.WindowsAzure.Commands.ServiceManagement.Network.ApplicationGateway.Model(物件名稱)。

### 步驟 1

建立所有的個別設定項目。

建立前端 IP，如下列範例所示。

    PS C:\> $fip = New-Object Microsoft.WindowsAzure.Commands.ServiceManagement.Network.ApplicationGateway.Model.FrontendIPConfiguration
    PS C:\> $fip.Name = "fip1"
    PS C:\> $fip.Type = "Private"
    PS C:\> $fip.StaticIPAddress = "10.0.0.5"

建立前端連接埠，如下列範例所示。

    PS C:\> $fep = New-Object Microsoft.WindowsAzure.Commands.ServiceManagement.Network.ApplicationGateway.Model.FrontendPort
    PS C:\> $fep.Name = "fep1"
    PS C:\> $fep.Port = 80

建立後端伺服器集區

 定義將加入至後端伺服器集區的 IP 位址，如下列範例所示。


    PS C:\> $servers = New-Object Microsoft.WindowsAzure.Commands.ServiceManagement.Network.ApplicationGateway.Model.BackendServerCollection
    PS C:\> $servers.Add("10.0.0.1")
    PS C:\> $servers.Add("10.0.0.2")

 使用 $server 物件，將值加入至後端集區物件 ($pool)。

    PS C:\> $pool = New-Object Microsoft.WindowsAzure.Commands.ServiceManagement.Network.ApplicationGateway.Model.BackendAddressPool
    PS C:\> $pool.BackendServers = $servers
    PS C:\> $pool.Name = "pool1"

建立後端伺服器集區設定

    PS C:\> $setting = New-Object Microsoft.WindowsAzure.Commands.ServiceManagement.Network.ApplicationGateway.Model.BackendHttpSettings
    PS C:\> $setting.Name = "setting1"
    PS C:\> $setting.CookieBasedAffinity = "enabled"
    PS C:\> $setting.Port = 80
    PS C:\> $setting.Protocol = "http"

建立接聽程式

    PS C:\> $listener = New-Object Microsoft.WindowsAzure.Commands.ServiceManagement.Network.ApplicationGateway.Model.HttpListener
    PS C:\> $listener.Name = "listener1"
    PS C:\> $listener.FrontendPort = "fep1"
    PS C:\> $listener.FrontendIP = "fip1"
    PS C:\> $listener.Protocol = "http"
    PS C:\> $listener.SslCert = ""

建立規則

    PS C:\> $rule = New-Object Microsoft.WindowsAzure.Commands.ServiceManagement.Network.ApplicationGateway.Model.HttpLoadBalancingRule
    PS C:\> $rule.Name = "rule1"
    PS C:\> $rule.Type = "basic"
    PS C:\> $rule.BackendHttpSettings = "setting1"
    PS C:\> $rule.Listener = "listener1"
    PS C:\> $rule.BackendAddressPool = "pool1"

### 步驟 2

將所有的個別設定項目指派給應用程式閘道設定物件 ($appgwconfig)：

將前端 IP 加入至設定

    PS C:\> $appgwconfig = New-Object Microsoft.WindowsAzure.Commands.ServiceManagement.Network.ApplicationGateway.Model.ApplicationGatewayConfiguration
    PS C:\> $appgwconfig.FrontendIPConfigurations = New-Object "System.Collections.Generic.List[Microsoft.WindowsAzure.Commands.ServiceManagement.Network.ApplicationGateway.Model.FrontendIPConfiguration]"
    PS C:\> $appgwconfig.FrontendIPConfigurations.Add($fip)

將前端連接埠加入至設定

    PS C:\> $appgwconfig.FrontendPorts = New-Object "System.Collections.Generic.List[Microsoft.WindowsAzure.Commands.ServiceManagement.Network.ApplicationGateway.Model.FrontendPort]"
    PS C:\> $appgwconfig.FrontendPorts.Add($fep)

將後端伺服器集區加入至設定

    PS C:\> $appgwconfig.BackendAddressPools = New-Object "System.Collections.Generic.List[Microsoft.WindowsAzure.Commands.ServiceManagement.Network.ApplicationGateway.Model.BackendAddressPool]"
    PS C:\> $appgwconfig.BackendAddressPools.Add($pool)  

將後端集區設定加入至設定

    PS C:\> $appgwconfig.BackendHttpSettingsList = New-Object "System.Collections.Generic.List[Microsoft.WindowsAzure.Commands.ServiceManagement.Network.ApplicationGateway.Model.BackendHttpSettings]"
    PS C:\> $appgwconfig.BackendHttpSettingsList.Add($setting)

將接聽程式加入至設定

    PS C:\> $appgwconfig.HttpListeners = New-Object "System.Collections.Generic.List[Microsoft.WindowsAzure.Commands.ServiceManagement.Network.ApplicationGateway.Model.HttpListener]"
    PS C:\> $appgwconfig.HttpListeners.Add($listener)

將規則加入至設定

    PS C:\> $appgwconfig.HttpLoadBalancingRules = New-Object "System.Collections.Generic.List[Microsoft.WindowsAzure.Commands.ServiceManagement.Network.ApplicationGateway.Model.HttpLoadBalancingRule]"
    PS C:\> $appgwconfig.HttpLoadBalancingRules.Add($rule)

### 步驟 3

將設定物件認可到應用程式閘道資源時使用 `組 AzureApplicationGatewayConfig`。

    Set-AzureApplicationGatewayConfig -Name AppGwTest -Config $appgwconfig

## 啟動閘道

一旦已設定閘道，使用 `Start-azureapplicationgateway` 指令程式來啟動閘道。 成功啟動閘道之後，會開始應用程式閘道計費。

> [AZURE.NOTE] `Start-azureapplicationgateway` cmdlet 可能需要 15-20 分鐘才能完成。



    PS C:\> Start-AzureApplicationGateway AppGwTest
    
    VERBOSE: 7:59:16 PM - Begin Operation: Start-AzureApplicationGateway
    VERBOSE: 8:05:52 PM - Completed Operation: Start-AzureApplicationGateway
    Name       HTTP Status Code     Operation ID                             Error
    ----       ----------------     ------------                             ----
    Successful OK                   fc592db8-4c58-2c8e-9a1d-1c97880f0b9b

## 確認閘道狀態

使用 `Get AzureApplicationGateway` cmdlet 來檢查閘道狀態。 如果上一個步驟中的 Start-AzureApplicationGateway** 成功，則狀態應該是執行中**，而且 Vip 和 DnsName 應該具有有效的輸入。

下列範例顯示應用程式閘道已啟動、 正在執行且準備好將流量傳送到 `http://<generated-dns-name>.cloudapp.net`。

    PS C:\> Get-AzureApplicationGateway AppGwTest
    
    VERBOSE: 8:09:28 PM - Begin Operation: Get-AzureApplicationGateway
    VERBOSE: 8:09:30 PM - Completed Operation: Get-AzureApplicationGateway
    Name          : AppGwTest
    Description   :
    VnetName      : testvnet1
    Subnets       : {Subnet-1}
    InstanceCount : 2
    GatewaySize   : Medium
    State         : Running
    Vip           : 138.91.170.26
    DnsName       : appgw-1b8402e8-3e0d-428d-b661-289c16c82101.cloudapp.net

## 刪除應用程式閘道

刪除應用程式閘道：

1. 使用 `停止 AzureApplicationGateway` cmdlet 停止閘道。
2. 使用 `移除 AzureApplicationGateway` cmdlet 移除閘道。
3. 確認已移除閘道使用 `Get AzureApplicationGateway` 指令程式。

下列範例所示 `停止 AzureApplicationGateway` 指令程式的第一行，後面接著輸出。

    PS C:\> Stop-AzureApplicationGateway AppGwTest
    
    VERBOSE: 9:49:34 PM - Begin Operation: Stop-AzureApplicationGateway
    VERBOSE: 10:10:06 PM - Completed Operation: Stop-AzureApplicationGateway
    Name       HTTP Status Code     Operation ID                             Error
    ----       ----------------     ------------                             ----
    Successful OK                   ce6c6c95-77b4-2118-9d65-e29defadffb8

一旦應用程式閘道處於已停止狀態，使用 `移除 AzureApplicationGateway` 指令程式來移除服務。


    PS C:\> Remove-AzureApplicationGateway AppGwTest
    
    VERBOSE: 10:49:34 PM - Begin Operation: Remove-AzureApplicationGateway
    VERBOSE: 10:50:36 PM - Completed Operation: Remove-AzureApplicationGateway
    Name       HTTP Status Code     Operation ID                             Error
    ----       ----------------     ------------                             ----
    Successful OK                   055f3a96-8681-2094-a304-8d9a11ad8301

若要確認已移除的服務，您可以使用 `Get AzureApplicationGateway` 指令程式。 這不是必要步驟。


    PS C:\> Get-AzureApplicationGateway AppGwTest
    
    VERBOSE: 10:52:46 PM - Begin Operation: Get-AzureApplicationGateway
    
    Get-AzureApplicationGateway : ResourceNotFound: The gateway does not exist.
    .....

## 後續步驟

如果您想要設定 SSL 卸載，請參閱 [設定應用程式閘道以進行 SSL 卸載](application-gateway-ssl.md)。

如果您想要將應用程式閘道設為與 ILB 搭配使用，請參閱 [具有內部負載平衡器 (ILB) 建立應用程式閘道](application-gateway-ilb.md)。

如果您想進一步了解一般負載平衡選項，請參閱：

- [Azure 負載平衡器](https://azure.microsoft.com/documentation/services/load-balancer/)
- [Azure 流量管理員](https://azure.microsoft.com/documentation/services/traffic-manager/)





