<properties 
    pageTitle="Azure Government 開發人員指南" 
    description="這為 Azure Government 的開發應用程式提供功能和指引的比較" 
    services="" 
    documentationCenter="" 
    authors="Joharve2" 
    manager="Chrisnie" 
    editor=""/>

<tags 
    ms.service="multiple" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.tgt_pltfrm="na" 
    ms.workload="azure-government" 
    ms.date="10/29/2015" 
    ms.author="jharve"/>


#  Microsoft Azure Government 開發人員指南 

<p> Microsoft Azure Government 是 Microsoft Azure 的實體及網路隔離執行個體。  此開發人員指南將針對應用程式開發人員和系統管理員需要與這些 Azure 個別區域互動及處理的差異，提供相關提供詳細資料。

<!--Table of contents for topic, the words in brackets must match the heading wording exactly-->


## 本主題內容


+ [概觀](#Overview)
+ [開發人員指導方針](#Guidance)
+ [Microsoft Azure Government 中目前可用的功能](#Features)
+ [端點對應](#Endpoint)
+ [後續步驟](#next)


## <a name="Overview"></a>概觀

Microsoft Azure Government 是 Microsoft Azure 服務處理美國的安全性和法規遵循需求的個別執行個體 聯邦機構、 狀態和地方政府和及其解決方案提供者。 Azure Government 提供實體及網路隔離，從非美國 政府部署，並提供篩選的美國 人員。 

Microsoft 提供一些工具來建立雲端應用程式並部署至 Microsoft 的全域 Microsoft Azure 服務 (全域服務) 及 Microsoft Azure Government 服務。

建立應用程式並部署至 Azure Government 服務時，由於這與全域服務相反，因此開發人員必須知道這兩種服務的主要差異。  特別是針對安裝及設定其程式設計環境、設定端點、撰寫應用程式，以及將其部署為 Azure Government 的服務等方面。

這份文件中的資訊概述這些差異，並提供資訊以補充說明 [Azure Government](http://www.azure.com/gov "Azure Government") 站台和 [Microsoft Azure 技術文件庫](http://msdn.microsoft.com/cloud-app-development-msdn "MSDN") MSDN 上。 正式資訊也可能會提供其他許多位置例如 [Microsoft Azure 信任中心](http://azure.microsoft.com/support/trust-center/ "Microsoft Azure 信任中心"), ，[Azure 文件中心](http://azure.microsoft.com/documentation/) 和 [Azure 部落格](http://azure.microsoft.com/blog/ "Azure 部落格")。 

此內容的適用對象是要對 Microsoft Azure Government 進行部署的合作夥伴和開發人員。



## <a name="Guidance"></a>開發人員指導方針
由於大部分目前可用的技術內容皆假設應用程式是針對「全域服務」開發，而不是針對 Microsoft Azure Government，因此確保開發人員知道為裝載於 Azure Government 而開發的應用程式有何主要差異，便相當重要。

- 首先，有服務和功能差異，這表示「全域服務」特定區域中的某些功能可能無法在 Azure Government 中提供。

- 其次，Azure Government 中所提供的功能與「全域服務」有組態差異。  因此，您應該檢閱您的範例程式碼、組態及步驟，以確保您是在「Azure Government 雲端服務」環境內建置和執行。


## <a name="Features"></a> Microsoft Azure Government 中目前可用的功能
Azure Government 目前在在「美國政府愛荷華州」(US GOV IOWA) 及「美國政府維吉尼亞州 (US GOV VIRGINIA)」區域有下列可用的服務：

- 虛擬機器
- 雲端服務
- 儲存體
- Active Directory
- 排程器
- 虛擬網路
- SQL Database
- Azure 檔案
- 媒體服務
- 流量管理員
- 服務匯流排
- StorSimple
- Redis 快取
- Azure 備份
- 自動化
- ExpressRoute
- 等

有其他服務可用，並且會持續新增更多服務。  如需最新的服務清單，請參閱 [區域頁面](http://azure.microsoft.com/regions/#services) 當中將醒目提示每個可用的區域及其服務。  

目前，「美國政府愛荷華州」和「美國政府維吉尼亞州」是支援 Azure Government 的資料中心。  如需目前的資料中心及可用的服務，請參閱上述區域頁面。

## <a name="Endpoint"></a>端點對應

將公用 Microsoft Azure 與 SQL Database 端點對應至 Azure Government 特定端點時，可使用下表做為指南。


服務類型|Azure 公用|Azure Government
---|---|---
管理入口網站|manage.windowsazure.com|manage.windowsazure.us
一般|*.windows.net|*.usgovcloudapi.net
核心|*.core.windows.net|*.core.usgovcloudapi.net
計算|*.cloudapp.net|*.usgovcloudapp.net
Blob 儲存體|*.blob.core.windows.net|   *.blob.core.usgovcloudapi.net
佇列儲存體|*.queue.core.windows.net|*.queue.core.usgovcloudapi.net
資料表儲存體|*.table.core.windows.net|*.table.core.usgovcloudapi.net
服務管理|management.core.windows.net|management.core.usgovcloudapi.net
SQL Database|*.database.windows.net|*.database.usgovcloudapi.net
ARM 負載平衡端點|https://management.windows.net|https://management.usgovcloudapi.net  

* 對於 ARM 透過 Azure AD 的驗證，請參閱 [驗證 Azure 資源管理員要求](https://msdn.microsoft.com/library/azure/dn790557.aspx)

## <a name="next"></a>後續步驟

如果您有興趣深入了解 Azure Government，請利用下面的連結。

- **[註冊試用版](https://azuregov.microsoft.com/trial/azuregovtrial)**

- **[取得和存取 Azure Government](http://azure.com/gov)**

- **[Azure Government 概觀](/azure-government-overview)**

- **[Azure Government 部落格](http://blogs.msdn.com/b/azuregov/)**

- **[Azure 的相容性](http://azure.microsoft.com/support/trust-center/compliance/)**

<!--Anchors-->



<!-- Images. -->

[1]: ./media/azure-government-developer-guide/publisherguide.png


<!--Link references-->
[Link 1 to another azure.microsoft.com documentation topic]: virtual-machines-windows-tutorial.md
[Link 2 to another azure.microsoft.com documentation topic]: web-sites-custom-domain-name.md
[Link 3 to another azure.microsoft.com documentation topic]: storage-whatis-account.md

