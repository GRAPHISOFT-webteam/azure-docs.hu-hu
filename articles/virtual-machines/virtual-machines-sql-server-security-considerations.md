<properties 
    pageTitle="Azure 中的 SQL Server 安全性考量 | Microsoft Azure"
    description="本主題關於以傳統部署模型建立的資源，並提供一般指導方針來保護在 Azure 虛擬機器中執行的 SQL Server。"
    services="virtual-machines"
    documentationCenter="na"
    authors="rothja"
    manager="jeffreyg"
   editor="monicar"    
   tags="azure-service-management"/>
<tags 
    ms.service="virtual-machines"
    ms.devlang="na"
    ms.topic="article"
    ms.tgt_pltfrm="vm-windows-sql-server"
    ms.workload="infrastructure-services"
    ms.date="12/04/2015"
    ms.author="jroth" />


# Azure 虛擬機器中的 SQL Server 安全性考量

本主題包含整體安全性指導方針，可協助制定 Azure VM 中 SQL Server 執行個體的存取安全。 不過，為了確保能更有效保護 Azure 中的 SQL Server 資料庫執行個體，除了 Azure 的安全性最佳作法外，我們也建議您實作傳統的內部部署安全性作法。

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-classic-include.md)] 資源管理員模型。


如需有關 SQL Server 安全性作法的詳細資訊，請參閱 [SQL Server 2008 R2 安全性最佳作法-操作與系統管理工作](http://download.microsoft.com/download/1/2/A/12ABE102-4427-4335-B989-5DA579A4D29D/SQL_Server_2008_R2_Security_Best_Practice_Whitepaper.docx)

Azure 符合多種業界規範及標準，可讓您使用在虛擬機器中執行的 SQL Server，建置相容的解決方案。 Azure 遵循法規的相關資訊，請參閱 [Azure 信任中心](http://azure.microsoft.com/support/trust-center/)。

以下是設定及連接到 Azure VM 中的 SQL Server 執行個體時，應考量的安全性建議清單。

## 管理帳戶的考量：

- 建立不是名為 **Administrator** 的唯一本機系統管理員帳戶。

- 為您的所有帳戶使用複雜的強式密碼。 如需如何建立強式密碼的詳細資訊，請參閱 [建立強式密碼](http://go.microsoft.com/fwlink/?LinkId=293596) 安全和資訊安全中心 」 中的文件。

- 根據預設，Azure 會在 SQL Server 虛擬機器安裝期間選取 Windows 驗證。 因此，系統會停用 **SA** 登入，並由安裝程式指派密碼。 我們建議最好不要使用或啟用 **SA** 登入。 以下是需要 SQL 登入時的替代策略：
    - 建立具有 **CONTROL SERVER** 權限的 SQL 帳戶。
    - 如果您必須使用 **SA** 登入，請啟用此登入，並將它重新命名，然後指派新密碼。
    - 先前提到的兩個選項，都需要將驗證模式變更為 SQL Server 和 Windows 驗證模式。 如需詳細資訊，請參閱「變更伺服器驗證模式」。

- 建立具有 CONTROL SERVER 權限的 SQL 帳戶。

- 如果您必須使用 SA 登入，請啟用此登入，並將它重新命名，然後指派新密碼。

- 先前提到的兩個選項，都需要將驗證模式變更為 **SQL Server 和 Windows 驗證模式**。 如需詳細資訊，請參閱 [變更伺服器驗證模式](https://msdn.microsoft.com/library/ms188670.aspx)。

## 保護 Azure 虛擬機器之連接的考量：

- 請考慮使用 [Azure 虛擬網路](../virtual-network/virtual-networks-overview.md) 來管理虛擬機器，而不是公用的 RDP 連接埠。

- 如果虛擬機器上有任何不使用的端點，請將它們全部移除。

- 針對 Azure 虛擬機器中的 SQL Server Database Engine 執行個體，啟用加密的連接選項。 使用簽署的憑證設定 SQL Server 執行個體。 如需詳細資訊，請參閱 [啟用加密連接到 Database Engine](https://msdn.microsoft.com/library/ms191192.aspx) 和 [連接字串語法](https://msdn.microsoft.com/library/ms254500.aspx)。

- 如果應該只從特定的網路存取您的虛擬機器，請使用 Windows 防火牆來限制存取特定的 IP 位址或網路子網路。 您也可以考慮在端點上加入 ACL 來限制流量，只開放給您允許的用戶端。 如需使用 Acl 與端點的指示，請參閱 [端點上管理 ACL](virtual-machines-set-up-endpoints.md#manage-the-acl-on-an-endpoint)

## 後續步驟

如果您也想要的效能最佳實務範例，請參閱 [的 Azure 虛擬機器中 SQL Server 效能最佳作法](virtual-machines-sql-server-performance-best-practices.md)。

若要在 Azure Vm 中執行 SQL Server 相關之其他主題，請參閱 [SQL Server 的 Azure 虛擬機器總覽](virtual-machines-sql-server-infrastructure-services.md)。






