<properties
    pageTitle="建立 Oracle WebLogic Server 12c VM | Microsoft Azure"
    description="在 Microsoft Azure 中使用資源管理員部署模型建立執行 Windows Server 2012 的 Oracle WebLogic Server 12c 虛擬機器。"
    services="virtual-machines"
    authors="bbenz"
    documentationCenter=""
    tags="azure-resource-manager"/>

<tags
    ms.service="virtual-machines"
    ms.devlang="na"
    ms.topic="article"
    ms.tgt_pltfrm="na"
    ms.workload="infrastructure-services"
    ms.date="06/22/2015"
    ms.author="bbenz" />

#在 Azure 中建立 Oracle WebLogic Server 12c 虛擬機器
以下範例會向您說明如何在 Azure 中以由 Microsoft 所提供並在 Windows Server 2012 上執行的 Oracle WebLogic Server 12c 映像為基礎，建立虛擬機器。

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-rm-include.md)] 傳統部署模型。



##在 Azure 中建立 Oracle WebLogic Server 12c 虛擬機器

1. 登入 [Azure 入口網站](https://ms.portal.azure.com/)。

2. 按一下 [ **Marketplace**, ，按一下 [ **計算**, ，然後輸入 **Oracle** 在搜尋方塊。

3.  選取 **上 Windows Server 2012 的 Oracle WebLogic Server 12c Standard Edition** 或 **Windows Server 2012 上的 Oracle WebLogic Server 12c Enterprise Edition** 映像。  檢閱的資訊 (例如建議的大小)，此映像，然後按一下 **下一步**。

4.  指定 **主機名稱**VM 的。

5.  指定 **使用者名稱** vm。 注意，此使用者是用於從遠端登入 VM，這不是 Oracle 資料庫使用者名稱。

6.  指定並確認 VM 的密碼，或提供 SSH 公用金鑰。

7.  選擇 **定價層**。  請注意，顯示建議的定價層，以根據預設，若要查看所有的組態選項，請按一下 **檢視所有** 右上方。

8.  考量下列因素，並依照需要設定選用設定：
    1. 保留 **儲存體帳戶** 以-為使用 VM 名稱建立新的儲存體帳戶。
    2. 保留 **可用性設定組** 做為 「 未設定 」。
    3. 請勿新增任何 **端點** 這一次。

9.  選擇或建立 [資源群組](resource-group-portal.md)

10. 選擇 **訂用帳戶**

11. 選擇 **位置**


##若要在 Azure 中設定您的 Oracle WebLogic Server 12c 虛擬機器

1. 登入 [Azure 入口網站](https://ms.portal.azure.com/)。

2.  按一下 [ **虛擬機器**。

3.  按一下要登入之虛擬機器的名稱。

4.  按一下 [ **連接**。

5.  視需要回應提示以連接虛擬機器。 當要求提供系統管理員名稱和密碼的提示出現時，請使用在建立虛擬機器時提供的值。

6.  內 **WebLogic 平台快速入門** ] 對話方塊中，按一下 [ **開始使用 WebLogic Server**。 (如果 **WebLogic 平台快速入門** 不開啟對話方塊時，按一下以開啟 **Windows [開始**, ，輸入 **啟動 WebLogic Server 網域的系統管理伺服器**, ，然後按一下 [ **啟動 WebLogic Server 網域的系統管理伺服器** 圖示。)

7.  在 **歡迎** 對話方塊中，選取 **建立新的 WebLogic 網域** 然後按一下 [ **下一步**。

    ![](media/virtual-machines-creating-oracle-webLogic-server-12c-virtual-machine/image10.png)

8.  在 **選取網域來源** ] 對話方塊中，接受預設值，然後按一下 **下一步**。

    ![](media/virtual-machines-creating-oracle-webLogic-server-12c-virtual-machine/image11.png)

9.  在 **指定網域名稱和位置** ] 對話方塊中，接受預設值，然後按一下 **下一步**。

    ![](media/virtual-machines-creating-oracle-webLogic-server-12c-virtual-machine/image12.png)

10. 在 **設定系統管理員使用者名稱和密碼** 對話方塊:

    1.  [選用]變更使用者名稱從 **weblogic** 到您選擇的值。

    2.  指定並確認 WebLogic Server 系統管理員密碼。

    3.  按一下 [ **下一步**。

    ![](media/virtual-machines-creating-oracle-webLogic-server-12c-virtual-machine/image13.png)

11. 在 **設定伺服器開始模式與 JDK** 對話方塊中，選取 **實際執行模式**, ，選取可用的 JDK (或瀏覽器到想要的 JDK)，然後按一下 [ **下一步**。

    ![](media/virtual-machines-creating-oracle-webLogic-server-12c-virtual-machine/image14.png)

12. 在 **選取選用設定** ] 對話方塊中，不選取任何選項，以及 [ **下一步**。

    ![](media/virtual-machines-creating-oracle-webLogic-server-12c-virtual-machine/image15.png)

13. 在 **組態摘要** ] 對話方塊中，按一下 [ **建立**。

    ![](media/virtual-machines-creating-oracle-webLogic-server-12c-virtual-machine/image16.png)

14. 在 **建立網域** ] 對話方塊中，核取 **啟動管理伺服器** 然後按一下 [ **完成**。

    ![](media/virtual-machines-creating-oracle-webLogic-server-12c-virtual-machine/image17.png)

15. 命令提示字元 **startWebLogic.cmd** 已啟動。 出現提示時，請提供您的 WebLogic 使用者名稱與密碼。

##在 Azure 中於 Oracle WebLogic Server 12c 虛擬機器上安裝應用程式
1.  仍登入您的虛擬機器，複製位於本機 http://www.oracle.com/webfolder/technetwork/tutorials/obe/fmw/wls/12c/12-ManageSessions--4478/files/shoppingcart.war 的 shoppingcart.war 範例。 例如，建立名為 **c:\mywar** 並儲存在以 http://www.oracle.com/webfolder/technetwork/tutorials/obe/fmw/wls/12c/12-ManageSessions--4478/files/shoppingcart.war 戰 **c:\mywar**。

2.  開啟 **WebLogic Server 管理主控台**, ，http://localhost:7001/console。 出現提示時，請提供您的 WebLogic 使用者名稱與密碼。

3.  內 **WebLogic Server 管理主控台**, ，按一下 [ **鎖定與編輯**, ，按一下 [ **部署**, ，然後按一下 [ **安裝**。

4.  如 **路徑**, ，型別 **c:\mywar\shoppingcart.war。**

    ![](media/virtual-machines-creating-oracle-webLogic-server-12c-virtual-machine/image18.png)

    按一下 [ **下一步**。

5.  選取 [我**安裝此應用程式部署** 然後按一下 [ **下一步**。

6.  按一下 [ **完成**。

7.  內 **WebLogic Server 管理主控台**, ，按一下 [ **儲存**, ，然後按一下 [ **啟用變更**。

8.  按一下 [ **部署**, ，請選取 **購物車**, ，按一下 [ **啟動**, ，然後按一下 [ **服務所有要求**。 當系統提示您確認時，按一下 [ **是**。

9.  若要查看在本機執行的購物車應用程式，開啟瀏覽器 <http://localhost:7001/shoppingcart>

10. 為虛擬機器建立端點：

    1. 登入 [Azure 入口網站](https://ms.portal.azure.com/)。

    2.  按一下 [ **瀏覽**

    3.  按一下 [ **虛擬機器**

    4.  選取虛擬機器

    5.  按一下 [ **設定**

    6.  按一下 [ **端點**。

    7.  按一下 [ **新增**。

    8.  指定端點的名稱

        1. 使用 **TCP** 通訊協定

        2. 使用 **80** 公用連接埠

        3. 使用 **7001** 的私人連接埠。

    9.  維持其餘選項不變

    10. 按一下 [ **[確定]**

11. 允許透過防火牆輸入連線至連接埠 7001。

    1.  登入虛擬機器。

    2.  按一下 [ **Windows 開始**, ，型別 **具有進階安全性的 Windows 防火牆**, ，然後按一下 [ **具有進階安全性的 Windows 防火牆** 圖示。 這會開啟 **具有進階安全性的 Windows 防火牆** 管理主控台。

    3.  在防火牆管理主控台中，按一下 [ **輸入規則** 在左側窗格中 (如果您沒有看到 **輸入規則**, ，展開左窗格中的最上層節點)，然後按一下右側窗格中的 [新增規則。

    4.  如 **規則類型**, ，請選取 **連接埠** 按一下 **下一步**。

    5.  如 **通訊協定和連接埠**, ，請選取 **TCP**, ，請選取 **特定本機連接埠**, ，輸入 **7001** 做為連接埠，然後按一下 [ **下一步**。

    6.  選取 **允許連線** 按一下 **下一步**。

    7.  接受套用規則的設定檔的預設值，然後按一下 [ **下一步**。

    8.  指定規則，並選擇性地描述的名稱，然後按一下 **完成**。

12. 若要查看在網際網路上執行的購物車應用程式，請開啟瀏覽器並連線至格式為 `http://<<unique_domain_name>>/shoppingcart` 的 URL。 (您可以判斷的值 <<*unique_domain_name*>> 內 [Azure 入口網站](https://ms.portal.azure.com/) 按一下 **虛擬機器** ，然後選取您用來執行 Orable WebLogic Server 虛擬機器)。


##其他資源
現在您已經設定好執行 Oracle WebLogic Server 的虛擬機器，請查看下列主題以了解其他資訊。

-   [Oracle 虛擬機器映像 - 其他考量](virtual-machines-miscellaneous-considerations-oracle-virtual-machine-images.md)

-   [Oracle WebLogic Server 產品文件 (英文)](http://www.oracle.com/technetwork/middleware/weblogic/documentation/index.html)

-   [Microsoft Azure 上使用 Linux 的 Oracle WebLogic Server 12c (英文)](http://www.oracle.com/technetwork/middleware/weblogic/learnmore/oracle-weblogic-on-azure-wp-2020930.pdf)

-   [適用於 Azure 的 Oracle 虛擬機器映像](virtual-machines-oracle-list-oracle-virtual-machine-images.md)

