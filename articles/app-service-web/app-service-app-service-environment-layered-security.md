<properties 
    pageTitle="具有 App Service 環境的多層式安全性架構" 
    description="實作具有 App Service 環境的多層式安全性架構。" 
    services="app-service" 
    documentationCenter="" 
    authors="stefsch" 
    manager="wpickett" 
    editor=""/>

<tags 
    ms.service="app-service" 
    ms.workload="na" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="10/13/2015" 
    ms.author="stefsch"/>   

# 實作具有 App Service 環境的多層式安全性架構。

## 概觀 ##
 
由於 App Service 環境提供部署至虛擬網路的隔離執行階段環境，因此開發人員能夠建立多層式安全性架構，針對每個實體應用程式層提供不同層級的網路存取。

常見的需求之一，是要隱藏對 API 後端的一般網際網路存取，而只允許由上游 Web 應用程式呼叫 API。  [網路安全性群組 (Nsg)][NetworkSecurityGroups] 可以包含 App Service 環境的子網路上用來限制對 API 應用程式的公用存取。

下圖顯示的範例架構，具有部署在 App Service 環境上的 WebAPI 型應用程式。  三個不同的 Web 應用程式執行個體部署在三個不同的 App Service 環境上，使後端呼叫相同的 WebAPI 應用程式。

![概念性架構][ConceptualArchitecture] 

綠色加號指出包含 "apiase" 之子網路上的網路安全性群組，允許來自於上游 Web 應用程式的傳入呼叫，及其本身的呼叫。  不過，相同的網路安全群組明確拒絕網際網路對一般輸入流量的存取。 

此主題的其餘部分將逐步解說在包含 "apiase" 的子網路上設定網路安全群組所需的步驟。

## 判斷網路行為 ##
若要知道需要哪些網路安全性規則，您必須判斷哪些網路用戶端將能夠聯繫包含 API 應用程式的 App Service 服務環境，而哪些用戶端將遭到封鎖。

由於 [網路安全性群組 (Nsg)][NetworkSecurityGroups] 會套用至子網路，並可以在 App Service 環境部署至子網路，NSG 中所包含的規則適用於 **所有** App Service 環境上執行應用程式。  舊本文的範例架構而言，一旦將網路安全性群組套用至包含 "apiase" 的子網路後，所有在 "apiase" App Service 環境上執行的應用程式都將受到相同安全性規則集的保護。 

- **判斷輸出 IP 位址的上游呼叫端 ︰**  的 IP 位址或上游呼叫者的地址？  這些位址在 NSG 中必須明確地允許存取。  由於 App Service 環境之間的呼叫會被視為「網際網路」呼叫，因此這表示指派給這三個上游 App Service 環境的輸出 IP 位址，在 "apiase" 子網路的 NSG 中必須是允許存取的。   如需有關判斷輸出的 IP 位址，如在 App Service 環境中執行的應用程式，請參閱 [網路架構][NetworkArchitecture] 概觀文章。
- **後端 API 應用程式是否需要呼叫本身？**  後端應用程式有時候需要呼叫本身，這是常會被忽略而難以察覺的情況。  如果 App Service 環境上的後端 API 應用程式需要呼叫本身，我們也會將其視為「網際網路」呼叫。  在範例架構中，這必須允許 "apiase" App Service 環境的輸出 IP 位址進行存取。

## 設定網路安全性群組 ##
得知輸出 IP 位址集後，下一步是要建構網路安全群組。  因為 App Service 環境是目前僅支援"v1"的虛擬網路， [NSG 組態][NetworkSecurityGroupsClassic] 使用的傳統 NSG 支援 Powershell 中完成。

在範例架構中，環境位於美國中南部，因此會在該區域中建立空的 NSG：

    New-AzureNetworkSecurityGroup -Name "RestrictBackendApi" -Location "South Central US" -Label "Only allow web frontend and loopback traffic"

先明確允許 Azure 的管理基礎結構中所述的發行項上加入規則 [輸入流量][InboundTraffic] App Service 環境。

    #Open ports for access by Azure management infrastructure
    Get-AzureNetworkSecurityGroup -Name "RestrictBackendApi" | Set-AzureNetworkSecurityRule -Name "ALLOW AzureMngmt" -Type Inbound -Priority 100 -Action Allow -SourceAddressPrefix 'INTERNET' -SourcePortRange '*' -DestinationAddressPrefix '*' -DestinationPortRange '454-455' -Protocol TCP
    
接著要新增兩個規則，以允許從第一個上游 App Service 環境 ("fe1ase") 進行 HTTP 和 HTTPS 呼叫。

    #Grant access to requests from the first upstream web front-end
    Get-AzureNetworkSecurityGroup -Name "RestrictBackendApi" | Set-AzureNetworkSecurityRule -Name "ALLOW HTTP fe1ase" -Type Inbound -Priority 200 -Action Allow -SourceAddressPrefix '65.52.xx.xyz'  -SourcePortRange '*' -DestinationAddressPrefix '*' -DestinationPortRange '80' -Protocol TCP
    Get-AzureNetworkSecurityGroup -Name "RestrictBackendApi" | Set-AzureNetworkSecurityRule -Name "ALLOW HTTPS fe1ase" -Type Inbound -Priority 300 -Action Allow -SourceAddressPrefix '65.52.xx.xyz'  -SourcePortRange '*' -DestinationAddressPrefix '*' -DestinationPortRange '443' -Protocol TCP

為第二個和第三個上游 App Service 環境 ("fe2ase" 和 "fe3ase") 重複相同步驟。

    #Grant access to requests from the second upstream web front-end
    Get-AzureNetworkSecurityGroup -Name "RestrictBackendApi" | Set-AzureNetworkSecurityRule -Name "ALLOW HTTP fe2ase" -Type Inbound -Priority 400 -Action Allow -SourceAddressPrefix '191.238.xyz.abc'  -SourcePortRange '*' -DestinationAddressPrefix '*' -DestinationPortRange '80' -Protocol TCP
    Get-AzureNetworkSecurityGroup -Name "RestrictBackendApi" | Set-AzureNetworkSecurityRule -Name "ALLOW HTTPS fe2ase" -Type Inbound -Priority 500 -Action Allow -SourceAddressPrefix '191.238.xyz.abc'  -SourcePortRange '*' -DestinationAddressPrefix '*' -DestinationPortRange '443' -Protocol TCP
    
    #Grant access to requests from the third upstream web front-end
    Get-AzureNetworkSecurityGroup -Name "RestrictBackendApi" | Set-AzureNetworkSecurityRule -Name "ALLOW HTTP fe3ase" -Type Inbound -Priority 600 -Action Allow -SourceAddressPrefix '23.98.abc.xyz'  -SourcePortRange '*' -DestinationAddressPrefix '*' -DestinationPortRange '80' -Protocol TCP
    Get-AzureNetworkSecurityGroup -Name "RestrictBackendApi" | Set-AzureNetworkSecurityRule -Name "ALLOW HTTPS fe3ase" -Type Inbound -Priority 700 -Action Allow -SourceAddressPrefix '23.98.abc.xyz'  -SourcePortRange '*' -DestinationAddressPrefix '*' -DestinationPortRange '443' -Protocol TCP

最後，授與後端 API 的 App Service 環境輸出 IP 位址的存取權，使其能夠回呼本身。

    #Allow apps on the apiase environment to call back into itself
    Get-AzureNetworkSecurityGroup -Name "RestrictBackendApi" | Set-AzureNetworkSecurityRule -Name "ALLOW HTTP apiase" -Type Inbound -Priority 800 -Action Allow -SourceAddressPrefix '70.37.xyz.abc'  -SourcePortRange '*' -DestinationAddressPrefix '*' -DestinationPortRange '80' -Protocol TCP
    Get-AzureNetworkSecurityGroup -Name "RestrictBackendApi" | Set-AzureNetworkSecurityRule -Name "ALLOW HTTPS apiase" -Type Inbound -Priority 900 -Action Allow -SourceAddressPrefix '70.37.xyz.abc'  -SourcePortRange '*' -DestinationAddressPrefix '*' -DestinationPortRange '443' -Protocol TCP

無須設定其他網路安全性規則，因為依預設每個 NSG 有一組預設規則會封鎖來自網際網路的輸入存取。

網路安全性群組中的完整規則清單顯示如下。  請留意最後一個規則 (反白顯示) 如何封鎖未被明確授與存取權的所有呼叫端進行的輸入存取。

![NSG 組態][NSGConfiguration] 

最後一個步驟是將 NSG 套用至包含 "apiase" App Service 環境的子網路。  

     #Apply the NSG to the backend API subnet
    Get-AzureNetworkSecurityGroup -Name "RestrictBackendApi" | Set-AzureNetworkSecurityGroupToSubnet -VirtualNetworkName 'yourvnetnamehere' -SubnetName 'API-ASE-Subnet'

NSG 套用至子網路後，將只有三個上游 App Service 環境以及包含 API 後端的 App Service 環境能夠呼叫 "apiase" 環境。


## 其他連結和資訊 ##
設定 [網路安全性群組][NetworkSecurityGroupsClassic] 傳統的虛擬網路上。 

了解 [輸出 IP 位址][NetworkArchitecture] 與 App Service 環境。

[網路連接埠][InboundTraffic] App Service 環境所使用。

[AZURE.INCLUDE [app-service-web-whats-changed](../../includes/app-service-web-whats-changed.md)]

[AZURE.INCLUDE [app-service-web-try-app-service](../../includes/app-service-web-try-app-service.md)]

<!-- LINKS -->
[NetworkSecurityGroups]: https://azure.microsoft.com/documentation/articles/virtual-networks-nsg/
[NetworkArchitecture]:  https://azure.microsoft.com/documentation/articles/app-service-app-service-environment-network-architecture-overview/
[NetworkSecurityGroupsClassic]:  https://azure.microsoft.com/documentation/articles/virtual-networks-create-nsg-classic-ps/
[InboundTraffic]:  https://azure.microsoft.com/en-us/documentation/articles/app-service-app-service-environment-control-inbound-traffic/

<!-- IMAGES -->
[ConceptualArchitecture]: ./media/app-service-app-service-environment-layered-security/ConceptualArchitecture-1.png
[NSGConfiguration]:  ./media/app-service-app-service-environment-layered-security/NSGConfiguration-1.png


