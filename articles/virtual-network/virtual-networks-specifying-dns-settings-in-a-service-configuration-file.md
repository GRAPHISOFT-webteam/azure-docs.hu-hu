<properties 
   pageTitle="指定服務組態檔中的 DNS 設定 | Microsoft Azure"
   description="使用虛擬網路的服務組態檔指定自訂的 DNS 設定"
   services="virtual-network"
   documentationCenter="na"
   authors="joaoma"
   manager="carmonm"
   editor="tysonn" />
<tags 
   ms.service="virtual-network"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="12/07/2015"
   ms.author="joaoma" />

# 指定服務組態檔中的 DNS 設定

## DNS 項目

服務組態檔可能包含 DnsServers 項目，以及服務會使用的網域名稱系統 (DNS) 伺服器的 IPv4 位址清單。 服務組態檔中的設定優先於網路組態檔中的設定。 如需詳細資訊，請參閱 [Azure 服務組態結構描述 (.cscfg 檔)](https://msdn.microsoft.com/library/azure/ee758710.aspx)。

**NetworkConfiguration 項目**

      <DnsServers>
        <DnsServer name="ID1" IPAddress="IPAddress1" />
        <DnsServer name="ID2" IPAddress="IPAddress2" />
        <DnsServer name="ID3" IPAddress="IPAddress3" />
      </DnsServers>

>[AZURE.WARNING]  **名稱** 屬性中 **DnsServer** 項目只會當做參考名稱。 它不代表 DNS 伺服器的主機名稱。 每個 **DnsServer** 屬性值必須是唯一的整個 Microsoft Azure 訂用帳戶。

## 另請參閱

[Azure 服務組態結構描述 (.cscfg)](https://msdn.microsoft.com/library/windowsazure/ee758710)

[Azure 虛擬網路組態結構描述](http://go.microsoft.com/fwlink/?LinkId=248093)

[使用網路組態檔設定虛擬網路](http://go.microsoft.com/fwlink/?LinkId=248094)

[關於管理入口網站中的虛擬網路設定](http://go.microsoft.com/fwlink/?LinkId=248092)



