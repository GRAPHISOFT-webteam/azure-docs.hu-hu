<properties 
   pageTitle="關於 Azure 虛擬網路的站對站 VPN 閘道連線的 VPN 裝置 | Microsoft Azure"
   description="深入了解 VPN 裝置和 S2S VPN 閘道連線的 IPsec 參數。站對站連線可以用於混合式組態。本文包含適用於 VPN 閘道裝置的組態指示和範本。"
   services="vpn-gateway"
   documentationCenter="na"
   authors="cherylmc"
   manager="carolz"
   editor="" />
<tags 
   ms.service="vpn-gateway"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="12/14/2015"
   ms.author="cherylmc" />


# 關於站對站 VPN 閘道連線的 VPN 裝置

設定站對站 (S2S) VPN 連線需要 VPN 裝置。 站對站連線可以用來建立混合式解決方案，或者用於您想要在內部部署網路與虛擬網路之間建立安全連線之時。 這篇文章討論相容的 VPN 裝置和組態參數。 請注意，設定站對站連線時，您的 VPN 裝置需要公開的 IPv4 IP 位址。

如果您的裝置未出現在「經驗證的 VPN 裝置」表格中，請參閱本文的＜未經驗證的 VPN 裝置＞一節。 您的裝置可能仍然可以與 Azure 搭配使用。 如需 VPN 裝置的支援，請連絡裝置製造商。

**檢視表格時應注意的項目：**

- 靜態與動態路由的名稱已經變更。 您可能二種詞彙都會看到。 只有變更名稱，功能未變更。
    - 靜態路由 = 原則式
    - 動態路由 = 路由式
- 除非另有說明，否則高效能 VPN 閘道和路由式 VPN 閘道的規格相同。 例如，已經驗證與路由式 VPN 閘道相容的 VPN 裝置，也會與新的 Azure 高效能 VPN 閘道相容。


## 已經驗證的 VPN 裝置

我們已與裝置廠商合作驗證一組標準 VPN 裝置。 下面清單列出之裝置系列中的所有裝置應該都能與 Azure VPN 閘道搭配運作。 請參閱 [VPN 閘道](vpn-gateway-about-vpngateways.md) 文件，以確認您要建立方案，您想要設定的閘道類型。

為了協助設定您的 VPN 裝置，請參閱對應到適當裝置系列的連結。



| **廠商**| **裝置系列**| **作業系統最低版本**| **原則式**| **路由式**|
|---------------------------------|----------------------------------------------------------|----------------------------------------------------|------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Allied Telesis| AR 系列 VPN 路由器| 2.9.2| 敬請期待| 不相容|
| Barracuda Networks, Inc.| Barracuda NG Firewall| Barracuda NG Firewall 5.4.3| [Barracuda NG 防火牆](https://techlib.barracuda.com/display/BNGV54/How%20to%20Configure%20an%20IPsec%20Site-to-Site%20VPN%20to%20a%20Windows%20Azure%20VPN%20Gateway)| 不相容|
| Barracuda Networks, Inc.| Barracuda Firewall| Barracuda Firewall 6.5| [Barracuda 防火牆](https://techlib.barracuda.com/BFW/ConfigAzureVPNGateway)| 不相容|
| Brocade| Vyatta 5400 vRouter| Virtual Router 6.6R3 GA| [設定指示](http://www1.brocade.com/downloads/documents/html_product_manuals/vyatta/vyatta_5400_manual/wwhelp/wwhimpl/js/html/wwhelp.htm#href=VPN_Site-to-Site%20IPsec%20VPN/Preface.1.1.html)| 不相容|
| Check Point| Security Gateway| R75.40、R75.40VS| [設定指示](https://supportcenter.checkpoint.com/supportcenter/portal?eventSubmit_doGoviewsolutiondetails=&solutionid=sk101275)| [設定指示](https://supportcenter.checkpoint.com/supportcenter/portal?eventSubmit_doGoviewsolutiondetails=&solutionid=sk101275)|
| Cisco| ASA| 8.3| [Cisco 範例](http://go.microsoft.com/fwlink/p/?LinkID=717348)| 不相容|
| Cisco| ASR| IOS 15.1 (原則為基礎)，IOS 15.2 (路由式)| [Cisco 範例](http://go.microsoft.com/fwlink/p/?LinkID=717348)| [Cisco 範例](http://go.microsoft.com/fwlink/p/?LinkID=717348)|
| Cisco| ISR| IOS 15.0 (原則為基礎)，IOS 15.1 (路由式)| [Cisco 範例](http://go.microsoft.com/fwlink/p/?LinkID=717348)| [Cisco 範例](http://go.microsoft.com/fwlink/p/?LinkID=717348)|
| Citrix| CloudBridge MPX 裝置或 VPX 虛擬裝置| N/A| [整合指示](https://www.citrix.com/welcome.html?resource=%2Fdownloads%2Fcloudbridge%2Fbetas-and-tech-previews%2Fcloudbridge-azure-integration)| 不相容|
| Dell SonicWALL| TZ 系列，NSA 系列，SuperMassive 系列，E-class NSA 系列| SonicOS 5.8.x, [SonicOS 5.9.x](http://documents.software.dell.com/sonicos/5.9/microsoft-azure-configuration-guide/supported-platforms?ParentProduct=850), [SonicOS 6.x](http://documents.software.dell.com/sonicos/6.2/microsoft-azure-configuration-guide/supported-platforms?ParentProduct=646)| [指示 SonicOS 6.2](http://documents.software.dell.com/sonicos/6.2/microsoft-azure-configuration-guide?ParentProduct=646) [指示 SonicOS 5.9](http://documents.software.dell.com/sonicos/5.9/microsoft-azure-configuration-guide?ParentProduct=850)| [指示 SonicOS 6.2](http://documents.software.dell.com/sonicos/6.2/microsoft-azure-configuration-guide?ParentProduct=646) [指示 SonicOS 5.9](http://documents.software.dell.com/sonicos/5.9/microsoft-azure-configuration-guide?ParentProduct=850)|
| F5| BIG-IP 系列| N/A| [設定指示](https://devcentral.f5.com/articles/connecting-to-windows-azure-with-the-big-ip)| 不相容|
| Fortinet| FortiGate| FortiOS 5.0.7| [設定指示](http://docs.fortinet.com/fortigate/admin-guides)| [設定指示](http://docs.fortinet.com/fortigate/admin-guides)|
| Internet Initiative Japan (IIJ)| SEIL 系列| SEIL/X 4.60、SEIL/B1 4.60、SEIL/x86 3.20| [設定指示](http://www.iij.ad.jp/biz/seil/ConfigAzureSEILVPN.pdf)| 不相容|
| Juniper| SRX| JunOS 10.2 (原則為基礎)，JunOS 11.4 (路由式)| [Juniper 範例](http://go.microsoft.com/fwlink/p/?LinkId=717760)| [Juniper 範例](http://go.microsoft.com/fwlink/p/?LinkId=717760)|
| Juniper| J 系列| JunOS 10.4 9 (原則為基礎)，JunOS 11.4 (路由式)| [Juniper 範例](http://go.microsoft.com/fwlink/p/?LinkId=717760)| [Juniper 範例](http://go.microsoft.com/fwlink/p/?LinkId=717760)|
| Juniper| ISG| ScreenOS 6.3 (原則式和路由式)| [Juniper 範例](http://go.microsoft.com/fwlink/p/?LinkId=717760)| [Juniper 範例](http://go.microsoft.com/fwlink/p/?LinkId=717760)|
| Juniper| SSG| ScreenOS 6.2 (原則式和路由式)| [Juniper 範例](http://go.microsoft.com/fwlink/p/?LinkId=717760)| [Juniper 範例](http://go.microsoft.com/fwlink/p/?LinkId=717760)|
| Microsoft| 路由及遠端存取服務| Windows Server 2012| 不相容| [Microsoft 範例](http://go.microsoft.com/fwlink/p/?LinkId=717761)|
| Openswan| Openswan| 2.6.32| (敬請期待)| 不相容|
| Palo Alto Networks| 所有執行 PAN-OS 5.0 或更新版本的裝置| PAN-OS 5.x 或更新版本| [Palo 琴奧圖網路](https://support.paloaltonetworks.com/)| 不相容|
| Watchguard| 全部| Fireware XTM v11.x| [設定指示](http://customers.watchguard.com/articles/Article/Configure-a-VPN-connection-to-a-Windows-Azure-virtual-network/)| 不相容|


## 未經驗證的 VPN 裝置

如果沒有看到您的裝置列在「已經驗證的 VPN 裝置」表格 (上方) 中，它仍然可能可以與站對站連線搭配使用。 確認您的 VPN 裝置符合最低需求的閘道需求一節中所述 [關於 VPN 閘道](vpn-gateway-about-vpngateways.md#gateway-requirements) 文件。 符合最低需求的裝置應該也適合使用 VPN 閘道。 如需額外支援和設定指示，請連絡裝置製造商。


## 編輯裝置組態範本

下載提供的 VPN 裝置組態範本之後，您將需要取代其中一些值，以反映您環境的設定。

**編輯範本：**

1. 使用 [記事本] 開啟範本。
1. 搜尋和取代所有 <*text*> 字串置換為適合您環境的值。務必要包含 < 和 >。當有指定名稱時，您選取的名稱應該是唯一名稱。如果命令無法運作，請參閱裝置製造商文件。

| **範本中的文字**| **變更為**|
|----------------------------------|----------------------------------------------------------------------------------------------------------------------|
| < > RP_OnPremisesNetwork| 您為此物件選擇的名稱。例如：myOnPremisesNetwork|
| < > RP_AzureNetwork| 您為此物件選擇的名稱。例如：myAzureNetwork|
| < > RP_AccessList| 您為此物件選擇的名稱。例如：myAzureAccessList|
| < > RP_IPSecTransformSet| 您為此物件選擇的名稱。例如：myIPSecTransformSet|
| < > RP_IPSecCryptoMap| 您為此物件選擇的名稱。例如：myIPSecCryptoMap|
| < > SP_AzureNetworkIpRange| 指定範圍。例如：192.168.0.0|
| < > SP_AzureNetworkSubnetMask| 指定子網路遮罩。例如：255.255.0.0|
| < > SP_OnPremisesNetworkIpRange| 指定內部部署範圍。例如：10.2.1.0|
| < > SP_OnPremisesNetworkSubnetMask| 指定內部部署子網路遮罩。例如：255.255.255.0|
| < > SP_AzureGatewayIpAddress| 此為您虛擬網路的特定資訊，位於管理入口網站中的 [閘道器 IP 位址]****。|
| < > SP_PresharedKey| 此資訊專屬於您的虛擬網路，是 [管理入口網站] 中的管理金鑰。|



## IPsec  參數

### IKE 階段 1 設定

| **屬性**| **原則式**| **路由式和標準或高效能 VPN 閘道**|
|----------------------------------------------------|--------------------------------|------------------------------------------------------------------|
| IKE 版本| IKEv1| IKEv2|
| Diffie-Hellman 群組| 群組 2 (1024 位元)| 群組 2 (1024 位元)|
| 驗證方法| 預先共用金鑰| 預先共用金鑰|
| 加密演算法| AES256 AES128 3DES| AES256 3DES|
| 雜湊演算法| SHA1(SHA128)| SHA1(SHA128)|
| 階段 1 安全性關聯 (SA) 存留期 (時間)| 28,800 秒| 28,800 秒|


### IKE 階段 2 設定

| **屬性**| **原則式**| **路由式和標準或高效能 VPN 閘道**|
|--------------------------------------------------------------------------|------------------------------------------------|--------------------------------------------------------------------|
| IKE 版本| IKEv1| IKEv2|
| 雜湊演算法| SHA1(SHA128)| SHA1(SHA128)|
| 階段 2 安全性關聯 (SA) 存留期 (時間)| 3600 秒| -|
| 階段 2 安全性關聯 (SA) 存留期 (輸送量)| 102,400,000 KB| -|
| IPsec SA 加密 & 驗證優惠 (依喜好順序)| 1.ESP-AES256 2.ESP-AES128 3.ESP-3DES 4.N/A| 請參閱 *路由為基礎的閘道 IPsec 安全性關聯 (SA) 提供* (下方)|
| 完整轉寄密碼 (PFS)| 否| 是 (DH Group1)|
| 死的對等偵測| 不支援| 支援|

### 路由式閘道 IPsec 安全性關聯 (SA) 提供項目

下表列出 IPsec SA 加密及驗證提供項目。 提供項目是依其呈現或被接受的喜好設定順序而列出。

| **IPsec SA 加密及驗證提供項目**| **Azure 閘道器為啟動者**| **Azure 閘道器為回應者**|
|---------------------------------------------------|--------------------------------------------------------------|--------------------------------------------------------------|
| 1| ESP AES_256 SHA| ESP AES_128 SHA|
| 2| ESP AES_128 SHA| ESP 3_DES MD5|
| 3| ESP 3_DES MD5| ESP 3_DES SHA|
| 4| ESP 3_DES SHA| AH SHA1 與 ESP AES_128 與 null HMAC|
| 5| AH SHA1 與 ESP AES_256 與 null HMAC| AH SHA1 與 ESP 3_DES 與 null HMAC|
| 6| AH SHA1 與 ESP AES_128 與 null HMAC| AH MD5 與 ESP 3_DES 與 null HMAC，未提議留存期|
| 7| AH SHA1 與 ESP 3_DES 與 null HMAC| AH SHA1與 ESP 3_DES SHA1，無存留期|
| 8| AH MD5 與 ESP 3_DES 與 null HMAC，未提議留存期| AH MD5 與 ESP 3_DES MD5，無留存期|
| 9| AH SHA1與 ESP 3_DES SHA1，無存留期| ESP DES MD5|
| 10| AH MD5 與 ESP 3_DES MD5，無留存期| ESP DES SHA1，無存留期|
| 11| ESP DES MD5| AH SHA1 與 ESP DES null HMAC，未提議存留期|
| 12| ESP DES SHA1，無存留期| AH MD5 與 ESP DES null HMAC，未提議留存期|
| 13| AH SHA1 與 ESP DES null HMAC，未提議存留期| AH SHA1與 ESP DES SHA1，無存留期|
| 14| AH MD5 與 ESP DES null HMAC，未提議留存期| AH MD5 與 ESP DES MD5，無留存期|
| 15| AH SHA1與 ESP DES SHA1，無存留期| ESP SHA，無存留期|
| 16| AH MD5 與 ESP DES MD5，無留存期| ESP MD5，無存留期|
| 17| -| AH SHA，無留存期|
| 18| -| AH MD5，無留存期|


- 您可以使用路由式和高效能 VPN 閘道指定 IPsec ESP NULL 加密。 這適用於 Azure 網路內的 VNet 對 VNet 連線。

- 透過網際網路的跨單位連線，請使用含有加密和雜湊演算法的預設 Azure VPN 閘道設定 (如上表所列)，以確保重要通訊的安全性。










