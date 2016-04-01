<properties
    pageTitle="建立執行 MySQL 的 VM | Microsoft Azure"
    description="以傳統部署模型建立執行 Windows Server 2012 R2 的 Azure 虛擬機器，然後在該虛擬機器上安裝及設定 MySQL 資料庫。"
    services="virtual-machines"
    documentationCenter=""
    authors="cynthn"
    manager="timlt"
    editor="tysonn"
    tags="azure-service-management"/>

<tags
    ms.service="virtual-machines"
    ms.workload="infrastructure-services"
    ms.tgt_pltfrm="vm-windows"
    ms.devlang="na"
    ms.topic="article"
    ms.date="11/09/2015"
    ms.author="cynthn"/>


# 在以傳統部署模型建立且執行 Windows Server 2012 R2 的虛擬機器上安裝 MySQL

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-classic-include.md)] 資源管理員模型。


[MySQL](http://www.mysql.com) 是受歡迎的開放原始碼、 SQL 資料庫。 使用 [Azure 傳統入口網站](http://manage.windowsazure.com), ，您可以建立執行 Windows Server 2012 R2 的映像庫的虛擬機器。 您可以接著安裝並將它設定為 MySQL 伺服器。

如需 Linux 上安裝 MySQL 的指示，請參閱 ︰ [如何在 Azure 上安裝 MySQL](virtual-machines-linux-install-mysql.md)。

本教學課程說明如何：

- 透過 Azure 傳統入口網站建立執行 Windows Server 2012 R2 的虛擬機器。

- 在虛擬機器上安裝並執行 MySQL 5.6.23 社群版本作為 MySQL 伺服器。


## 建立執行 Windows Server 的虛擬機器

[AZURE.INCLUDE [virtual-machines-create-WindowsVM](../../includes/virtual-machines-create-windowsvm.md)]

## 連接資料磁碟

建立虛擬機器之後，您可以選擇連接其他資料磁碟。 這被建議用於生產工作負載，並避免用完空間上的作業系統磁碟機 （c:），其中包含作業系統。

請參閱 [如何將資料磁碟連接至 Windows 虛擬機器](storage-windows-attach-disk.md) 並遵循指示來連接空的磁碟。 將主機快取設定設為 **無** 或 **唯讀**。

## 登入虛擬機器

接著，您將登入虛擬機器，以安裝 MySQL。

[AZURE.INCLUDE [virtual-machines-log-on-win-server](../../includes/virtual-machines-log-on-win-server.md)]

##在虛擬機器上安裝和執行 MySQL Community Server

請依照下列步驟安裝、設定和執行 MySQL Server 社群版本：

> [AZURE.NOTE] 這些步驟適用於 5.6.23.0 社群版本 MySQL 和 Windows Server 2012 R2。 對於 MySQL 或 Windows Server 的不同版本，操作方式可能不同。

1.  您已連接至虛擬機器使用遠端桌面之後，請按一下 **Internet Explorer** 從 [開始] 畫面。
2.  選取 **工具** 按鈕 （齒輪圖示），右上角，然後按一下 **網際網路選項**。 按一下 [ **安全性** 索引標籤上，按一下 [ **受信任的網站** 圖示，然後再按一下 **網站** ] 按鈕。 將 http://*.mysql.com 加入至信任的網站清單。 按一下 [ **關閉**, ，然後按一下 [ **確定**。
3.  在 Internet Explorer 的網址列中，輸入 http://dev.mysql.com/downloads/mysql/。
4.  使用 MySQL 網站尋找並下載 Windows 版 MySQL 最新版安裝程式。 選擇「MySQL 安裝程式」時，請下載具有完整檔案集的版本 (例如檔案大小為 282.4 MB 的 mysql-installer-community-5.6.23.0.msi)，然後儲存該安裝程式。
5.  安裝程式已完成下載，請按一下 **執行** 來啟動安裝。
6.  在 **授權合約** 頁面上，接受授權合約，然後按一下 **下一步**。
7.  在 **選擇安裝類型** 頁面上，按一下 [安裝類型，再按 [ **下一步**。 下列步驟假設選取 **僅適用於 Server** 安裝類型。
8.  在 **安裝** 頁面上，按一下 **Execute**。 安裝完成時，按一下 [ **下一步**。
9.  在 **產品組態** 頁面上，按一下 **下一步**。
10. 在 **類型和網路** 頁面上，指定所需的組態類型和連線選項，如有需要包括 TCP 連接埠。 選取 **顯示進階選項**, ，然後按一下 [ **下一步**。

    ![](./media/virtual-machines-mysql-windows-server-2008r2/MySQL_TypeNetworking.png)

11. 在 **帳戶及角色** 頁面上，指定強式的 MySQL 根密碼。 視需要新增其他 MySQL 使用者帳戶，然後按一下 **下一步**。

    ![](./media/virtual-machines-mysql-windows-server-2008r2/MySQL_AccountsRoles_Filled.png)

12. 在 **Windows 服務** 頁面上，對於執行 MySQL 伺服器作為 Windows 服務，如有需要指定變更預設設定，然後按一下 **下一步**。

    ![](./media/virtual-machines-mysql-windows-server-2008r2/MySQL_WindowsService.png)

13. 在 **進階選項** 頁面上，如有需要指定記錄選項的變更，然後按一下 **下一步**。

    ![](./media/virtual-machines-mysql-windows-server-2008r2/MySQL_AdvOptions.png)

14. 在 **套用伺服器組態** 頁面上，按一下 **Execute**。 完成組態步驟時，按一下 [ **完成**。
15. 在 **產品組態** 頁面上，按一下 **下一步**。
16. 在 **安裝完成** 頁面上，按一下 **將記錄複製到剪貼簿** 如果您想要檢查更新的版本，然後按一下 [ **完成**。
17. 從 [開始] 畫面中，輸入 **mysql**, ，然後按一下 [ **MySQL 5.6 命令列用戶端**。
18. 輸入您在步驟 11 中指定的根密碼，隨即提示您發出命令來設定 MySQL。 如需命令和語法的詳細資訊，請參閱 [MySQL 參考手冊](http://dev.mysql.com/doc/refman/5.6/en/server-configuration-defaults.html)。

    ![](./media/virtual-machines-mysql-windows-server-2008r2/MySQL_CommandPrompt.png)

19. 您也可以使用 C:\Program Files (x86)\MySQL\MySQL Server 5.6\my-default.ini 檔案中的項目設定伺服器預設設定，例如基底與資料目錄和磁碟機。 如需詳細資訊，請參閱 [5.1.2 伺服器組態預設值](http://dev.mysql.com/doc/refman/5.6/en/server-configuration-defaults.html)。

## 設定端點

如果您要 MySQL 伺服器服務可供網際網路上的 MySQL 用戶端電腦使用，則必須設定 MySQL 伺服器服務接聽的 TCP 連接埠端點，並建立額外的 Windows 防火牆規則。 這是 TCP 連接埠 3306，除非您指定不同的連接埠 **類型和網路** 頁面 （先前程序的步驟 10）。


> [AZURE.NOTE] 您應該仔細考慮安全性的影響這麼做，因為這會將 MySQL 伺服器服務提供給網際網路上的所有電腦。 您可以定義來源 IP 位址的集合，以便使用具備存取控制清單 (ACL) 的端點。 如需詳細資訊，請參閱 [如何設定端點的虛擬機器](virtual-machines-set-up-endpoints.md)。


若要設定 MySQL Server 服務的端點：

1.  在 Azure 傳統入口網站中，按一下 [ **虛擬機器**, ，按一下 MySQL 虛擬機器的名稱，然後按一下 **端點**。
2.  在命令列中，按一下 [ **新增**。
3.  在 **將端點加入至虛擬機器** 頁面上，按一下向右箭號。
4.  如果您使用預設 MySQL TCP 連接埠 3306，請按一下 **MySQL** 中 **名稱**, ，然後按一下核取記號。
5.  如果您使用不同的 TCP 連接埠，輸入中的唯一名稱 **名稱**。 選取 **TCP** 通訊協定，在輸入中的連接埠號碼 **公用連接埠** 和 **私用連接埠**, ，然後按一下核取記號。

## 新增允許 MySQL 流量的 Windows 防火牆規則

若要新增允許來自網際網路之 MySQL 流量的「Windows 防火牆」規則，請在 MySQL 伺服器虛擬機器上，於提升權限的 Windows PowerShell 命令提示字元中執行下列命令。

    New-NetFirewallRule -DisplayName "MySQL56" -Direction Inbound –Protocol TCP –LocalPort 3306 -Action Allow -Profile Public


    
## 測試您的遠端連線


若要測試 Azure 虛擬機器上執行的 MySQL Server 服務遠端連線，您必須先決定與執行 MySQL 伺服器的虛擬機器包含在內的雲端服務相對應的 DNS 名稱。

1.  在 Azure 傳統入口網站中，按一下 [ **虛擬機器**, ，按一下您的 MySQL 伺服器虛擬機器，名稱，然後按一下 **儀表板**。
2.  虛擬機器儀表板中，請注意 **DNS 名稱** 值下 **快速概覽** 一節。 下列是一個範例：

    ![](./media/virtual-machines-mysql-windows-server-2008r2/MySQL_DNSName.png)

3.  在執行 MySQL 或 MySQL 用戶端的本機電腦中執行下列命令，以使用 MySQL 使用者的身分登入：

        mysql -u <yourMysqlUsername> -p -h <yourDNSname>

    例如，MySQL 使用者名稱 dbadmin3 和虛擬機器的 testmysql.cloudapp.net  DNS 名稱所適用的命令如下：

        mysql -u dbadmin3 -p -h testmysql.cloudapp.net


## 其他資源

如需 MySQL 的詳細資訊，請參閱 [MySQL 文件](http://dev.mysql.com/doc/)。


