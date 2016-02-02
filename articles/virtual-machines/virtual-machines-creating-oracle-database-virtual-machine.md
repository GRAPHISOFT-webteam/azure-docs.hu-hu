<properties
    pageTitle="使用 Azure 傳統入口網站建立 Oracle 資料庫 VM |Microsoft Azure"
    description="了解如何使用傳統部署模型和 Azure 入口網站建立內含 Oracle 資料庫的虛擬機器"
    services="virtual-machines"
    authors="bbenz"
    documentationCenter=""
    tags="azure-service-management"/>

<tags
    ms.service="virtual-machines"
    ms.devlang="na"
    ms.topic="article"
    ms.tgt_pltfrm="Windows"
    ms.workload="infrastructure-services"
    ms.date="06/22/2015"
    ms.author="bbenz" />


# 在 Azure 中建立 Oracle 資料庫虛擬機器

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-classic-include.md)] 資源管理員模型。


以下範例說明如何在 Azure 中，根據 Microsoft 提供且在 Windows Server 2012 上執行的 Oracle 資料庫映像來建立虛擬機器 (VM)。 有兩個步驟。 首先，建立 VM，然後在 VM 內建立 Oracle 資料庫。 顯示的範例是 Oracle 資料庫版本 12c，但步驟幾乎與版本 11g 相同。

## 在 Azure 中建立 Oracle 資料庫 VM

1.  登入 [Azure 入口網站](https://ms.portal.azure.com/)。

2.  按一下 [Marketplace]****，接著按一下 [運算]****，然後在搜尋方塊中輸入 **Oracle**。

3.  選取一個可用的 Oracle 資料庫映像，包括**版本 11g、版本 12c、標準版、企業版，或是常用選項或進階選項套件組合之一。**檢閱您選取的映像相關資訊 (例如，建議的最小大小)，然後按 [下一步]****。

4.  指定 VM 的 [主機名稱]****。

5.  指定 VM 的 [使用者名稱]****。 請注意，此使用者是用於從遠端登入 VM，這不是 Oracle 資料庫使用者名稱。

6.  指定並確認 VM 的密碼，或提供 SSH 公用金鑰。

7.  選擇一個 [定價層]****。 請注意，預設會顯示建議的定價層，以查看所有設定選項，請按一下右上方的 [檢視全部]****。

8.  考量下列因素，並依照需要設定選用設定：

    a. 讓 [儲存體帳戶]**** 保持不變，以使用 VM 名稱建立新的儲存體帳戶。

    b. 維持 [可用性集合]**** 為 [未設定]。

    c. 此時請勿新增任何 [端點]****。

9.  選擇或建立資源群組。

10. 選擇 [訂用帳戶]****。

11. 選擇 [位置]****。

12. 按一下 [建立]****，即會開始建立 VM 的程序。 VM 的狀態為 [執行中]****之後，請繼續進行下一個程序。


## 在 Azure 中使用 Oracle 資料庫 VM 建立您的資料庫

1.  登入 [Azure 入口網站](https://ms.portal.azure.com/)。

2.  按一下 [虛擬機器]****。

3.  按一下要登入的 VM 名稱。

4.  按一下 [連接]****。

5.  視需要回應提示以連接 VM。 當要求提供系統管理員名稱和密碼的提示出現時，請使用在建立 VM 時提供的值。

6.  建立名為 **ORACLE_HOSTNAME** 的環境變數，並將其值設定為 VM 的電腦名稱。 您可以使用下列步驟建立環境變數：

    a. 在 Windows 中，按一下 [開始]****，輸入**控制台**，按一下 [控制台]**** 圖示，然後依序按一下 [系統及安全性]****、[系統]****、[進階系統設定]****。

    b. 按一下 [進階]**** 索引標籤，然後按一下 [環境變數]****。

    c. 在 [系統變數]**** 區段底下，按一下 [新增]**** 來建立變數。

    d. 在 [新增系統變數]**** 對話方塊中，輸入 **ORACLE_HOSTNAME** 做為變數的名稱，然後輸入 VM 的電腦名稱做為值。 若要判斷電腦名稱，請開啟命令提示字元並執行 **SET COMPUTERNAME** (該命令的輸出將包含電腦名稱)。

    e. 按一下 [確定]**** 來儲存新的環境變數並關閉 [新增系統變數]**** 對話方塊。

    f. 關閉由控制台開啟的其他對話方塊。

7.  在 Windows 中，按一下 [開始]****，然後輸入「資料庫組態輔助程式」****。 按一下 [資料庫組態輔助程式]**** 圖示。

8.  在 [資料庫組態輔助程式]**** 精靈中，視需要針對每個對話方塊步驟提供值：

    a. **步驟 1：** 按一下 [建立資料庫]****，然後按 [下一步]****。

        ![](media/virtual-machines-creating-oracle-database-virtual-machine/image5.png)

    b. **步驟 2：**輸入 [全域資料庫名稱]**** 的值。 輸入並確認 [管理密碼]**** 的值。 此密碼是用於 Oracle 資料庫的 **SYSTEM** 使用者。 清除 [建立做為容器資料庫]****。 按 [下一步]****。

        ![](media/virtual-machines-creating-oracle-database-virtual-machine/image6.png)

    c. **步驟 3：**必要條件檢查應會自動繼續進行，繼續執行**步驟 4**。

    d. **步驟 4：**檢閱 [建立資料庫 - 摘要]**** 選項，然後按一下 [完成]****。

        ![](media/virtual-machines-creating-oracle-database-virtual-machine/image7.png)

    e. **步驟 5：**[進度頁面]**** 將會報告資料庫建立狀態。

        ![](media/virtual-machines-creating-oracle-database-virtual-machine/image8.png)

    f. 建立資料庫之後，您可以選擇使用 [密碼管理]**** 對話方塊。 針對您的需求，視需要修改密碼設定，然後關閉對話方塊以結束 [資料庫組態輔助程式]**** 精靈。

## 確認資料庫已安裝

1.  仍然登入您的 VM，啟動 SQL Plus 命令提示字元。 在 Windows 中，按一下 [* 啟動**, ，然後輸入 * * SQL Plus**。 按一下 [SQL Plus]**** 圖示。

2.  出現提示時，使用 **SYSTEM** 的使用者名稱以及您在建立 Oracle 資料庫時指定的密碼來登入。

3.  在 SQL Plus 命令提示字元中，執行下列命令。

        **select \* from GLOBAL\_NAME;**

    結果應該是您建立之資料庫的全域名稱。

    ![](media/virtual-machines-creating-oracle-database-virtual-machine/image9.png)

## 讓您能夠從遠端連接資料庫

若要讓您能夠從遠端連接資料庫 (例如，從執行 Java 程式碼的用戶端電腦)，將需要啟動資料庫接聽程式、在虛擬機器防火牆中開啟連接埠 1521，然後建立連接埠 1521 的公用端點。

### 啟動資料庫接聽程式

1.  登入您的 VM。

2.  開啟命令提示字元。

3.  在命令提示字元中，執行下列命令。

        **lsnrctl start**


> [AZURE.NOTE] 您可以執行 **lsnrctl status** 來檢查接聽程式的狀態。 當您想要停止接聽程式時，可以執行 **lsnrctl stop**。

### 在虛擬機器防火牆中開啟連接埠 1521

1.  在已登入虛擬機器的情況下，在 Windows 中按一下 [開始]****，輸入「具有進階安全性的 Windows 防火牆」****，然後按一下 [具有進階安全性的 Windows 防火牆]**** 圖示。 這會開啟 [具有進階安全性的 Windows 防火牆]**** 管理主控台。

2.  在防火牆管理主控台中，按一下左邊窗格內的 [輸入規則]**** (如果您沒有看到 [輸入規則]****，請展開左邊窗格內的最上層節點)，然後按一下右邊窗格內的 [新增規則]****。

3.  針對 [規則類型]****，選取 [連接埠]****，然後按一下 [下一步]****。

4.  對於 [通訊協定與連接埠]****，請選取 [TCP]****，選取 [特定本機連接埠]****，輸入 **1521** 做為連接埠，然後按一下 [下一步]****。

5.  選取 [允許連線]**** 並按一下 [下一步]****。

6.  接受套用規則之設定檔的預設值，然後按一下 [下一步]****。

7.  指定規則的名稱並選擇性指定描述，然後按一下 [完成]****。

### 建立連接埠 1521 的公用端點

1.  登入 [Azure 入口網站](https://ms.portal.azure.com/)。

2.  按一下 [瀏覽]****。

3.  按一下 [虛擬機器]****。

4.  選取虛擬機器。

5.  按一下 [設定]****。

6.  按一下 [端點]****。

7.  按一下 [新增]****。

8.  指定端點的名稱：

    a. 使用 [TCP]**** 做為通訊協定。

    b. 使用 [1521]**** 做為公用連接埠。

    c. 使用 [1521]**** 做為私人連接埠。

9.  維持其餘選項不變。

10. 按一下 [確定]****。

## 啟用 Oracle Database Enterprise Manager 遠端存取

如果您想要啟用遠端存取 Oracle Database Enterprise Manager 的功能，可在防火牆中開啟連接埠 5500，並在 Azure 傳統入口網站中針對 5500 建立虛擬機器端點 (使用稍早開啟連接埠 1521 以及針對 1521 建立端點的步驟)。然後，若要從遠端電腦執行 Oracle Enterprise Manager，開啟瀏覽器 url 的形式 `http://<<unique_domain_name>>:5500/em`。
> [AZURE.NOTE] 您可以判斷的值 * \<\<unique\_domain\_name\>\ > * 內 [Azure 傳統入口網站](https://ms.portal.azure.com/) 按一下 **虛擬機器** ，然後選取用來執行 Oracle 資料庫虛擬機器)。

## 設定常用選項和進階選項套件組合

如果您選擇 [Oracle Database 包含常用選項]**** 或 [Oracle Database 包含進階選項套件組合]****，則下一個步驟是在您的 Oracle 安裝中設定附加元件功能。 由於設定會根據您對於每個個別元件的需求而截然不同，因此，請參閱 Oracle 文件，以取得在 Windows 上設定這些功能的相關指示。

**Oracle Database 包含常用選項套件組合** 包含 Oracle Database Enterprise Edition 和授權包含執行個體的 [資料分割](http://www.oracle.com/us/products/database/options/partitioning/overview/index.html), ，[Active Data Guard](http://www.oracle.com/us/products/database/options/active-data-guard/overview/index.html), ，[Oracle 資料庫的微調組件](http://docs.oracle.com/html/A86647_01/tun_ovw.htm), ，[資料庫的 Oracle 診斷套件](http://docs.oracle.com/cd/B28359_01/license.111/b28287/options.htm#CIHIHDDJ), ，和 [Oracle 資料庫生命週期管理組件](http://www.oracle.com/technetwork/oem/lifecycle-mgmt-495331.html)。

**Oracle Database 包含進階選項套件組合** 授權包含的所有選項的執行個體包含常用選項套件組合中加上 [進階壓縮](http://www.oracle.com/us/products/database/options/advanced-compression/overview/index.html), ，[進階安全性](http://www.oracle.com/us/products/database/options/advanced-security/overview/index.html), ，[標籤安全性](http://www.oracle.com/us/products/database/options/label-security/overview/index.html), ，[資料庫保存庫](http://www.oracle.com/us/products/database/options/database-vault/overview/index.html), ，[進階分析](http://www.oracle.com/us/products/database/options/advanced-analytics/overview/index.html), ，[OLAP](http://docs.oracle.com/cd/E11882_01/license.112/e47877/options.htm#CIHGDEEF), ，[空間和圖形](http://docs.oracle.com/cd/E11882_01/license.112/e47877/options.htm#CIHGDEEF), ，[記憶體中資料庫快取](http://www.oracle.com/technetwork/products/timesten/overview/timesten-imdb-cache-101293.html), ，[資料遮罩的組件](http://docs.oracle.com/cd/E11882_01/license.112/e47877/options.htm#CHDGEEBB), ，與 Oracle 測試資料管理組件 (做為資料遮罩組件的一部分)。

## 其他資源

現在您已經設定虛擬機器並建立了資料庫，請查看下列主題以了解其他資訊。

-   [Oracle 虛擬機器映像-其他考量](virtual-machines-miscellaneous-considerations-oracle-virtual-machine-images.md)

-   [Oracle Database 12c 文件庫](http://www.oracle.com/pls/db1211/homepage)

-   [從 Java 應用程式連接到 Oracle 資料庫](http://docs.oracle.com/cd/E11882_01/appdev.112/e12137/getconn.htm#TDPJD136)

-   [Azure 的 oracle 虛擬機器映像](virtual-machines-oracle-list-oracle-virtual-machine-images.md)

-   [Oracle 資料庫 2 天 DBA 12c 版本 1](http://docs.oracle.com/cd/E16655_01/server.121/e17643/toc.htm)





