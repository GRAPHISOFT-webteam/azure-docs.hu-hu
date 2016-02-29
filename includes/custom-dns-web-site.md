#設定 Azure 網站的自訂網域名稱

當您建立網站時，Azure 會提供好記的子 azurewebsites.net 網域上讓使用者可以存取您的網站使用 http://&lt;mysite>.azurewebsites.net 之類的 URL。 不過，如果您將網站設定為共用或標準模式，則可以將網站對應到您自己的網域名稱。

您也可以選擇使用 Azure 流量管理員來負載平衡連入網站的流量。 如需有關如何使用網站的流量管理員運作方式的詳細資訊，請參閱 [控制 Azure 網站的流量使用 Azure 流量管理員][trafficmanager]。

> [AZURE.NOTE] 這項工作中的程序套用至 Azure 網站。對於雲端服務，請參閱 <a href="/develop/net/common-tasks/custom-dns/">在 Azure 中設定自訂網域名稱</a>。

> [AZURE.NOTE] 這項工作中的步驟會要求您將網站設定為共用或標準模式中，這可能會變更您多少付費訂用帳戶。 如需詳細資訊，請參閱<a href="/pricing/details/web-sites/">網站定價詳細資料</a>。

本文內容：

-   [了解 CNAME 和 A 記錄](#understanding-records)
-   [將網站設定為共用或標準模式](#bkmk_configsharedmode)
-   [將網站新增至流量管理員](#trafficmanager)
-   [新增自訂網域的 CNAME](#bkmk_configurecname)
-   [新增自訂網域的 A 記錄](#bkmk_configurearecord)

<h2><a name="understanding-records"></a>了解 CNAME 和 A 記錄</h2>

CNAME (或別名記錄) 和 A 記錄都可讓您將網域名稱和網站產生關聯，但運作方式不同。

###CNAME 或別名記錄

CNAME 記錄會對應 *特定* 網域，例如 **contoso.com** 或 **www.contoso.com**, ，到正式網域名稱。 在此情況下，正式網域名稱為 **(& s) lt; p >。 azurewebsites.net** 您 Azure 網站的網域名稱或 **(& s) lt; p >。 m** 流量管理員設定檔的網域名稱。 CNAME 建立之後，建立一個別名 **(& s) lt; p >。 azurewebsites.net** 或 **(& s) lt; p >。 m** 網域名稱。 CNAME 項目會解析為 IP 位址的您 **(& s) lt; p >。 azurewebsites.net** 或 **(& s) lt; p >。 m** 網域名稱，網站的 IP 位址變更時，如果您不需要採取任何動作。

> [AZURE.NOTE] 某些網域註冊機構只允許您對應子網域時使用的 CNAME 記錄，例如 www.contoso.com，而不是根名稱，例如 contoso.com。 如需 CNAME 記錄的詳細資訊，請參閱註冊機構提供的文件、<a href="http://en.wikipedia.org/wiki/CNAME_record">維基百科 CNAME 記錄條目</a>，或 <a href="http://tools.ietf.org/html/rfc1035">IETF 網域名稱 - 實作與規格</a>文件。

###A 記錄

A 記錄將網域對應，例如 **contoso.com** 或 **www.contoso.com**, ，*或萬用字元網域* 例如 **\*.contoso.com**, ，IP 位址。 以 Azure 網站而言，就是指服務的虛擬 IP 或您為網站購買的特定 IP 位址。 讓 CNAME 記錄，A 記錄的主要優點是，您可以有一個項目使用萬用字元，例如 ***。 contoso.com**, ，這會處理多個子網域的要求，例如 **mail.contoso.com**, ，**login.contoso.com**, ，或 **www.contso.com**。

> [AZURE.NOTE] 因為 A 記錄會對應至靜態 IP 位址，所以無法自動解析變更您的網站的 IP 位址。 您在設定網站的自訂網域名稱設定時會提供 A 記錄所使用的 IP 位址。不過，如果您刪除又重新建立網站，或將網站模式變回免費，此值就可能改變。

> [AZURE.NOTE] A 記錄無法用於流量管理員的負載平衡。 如需詳細資訊，請參閱 [控制 Azure 網站的流量使用 Azure 流量管理員][trafficmanager]。

<a name="bkmk_configsharedmode"></a><h2>將網站設定為共用或標準模式</h2>

只有在 Azure 網站的共用和標準模式中，才能在網站上設定自訂網域名稱。 將網站從免費網站模式切換到共用或標準網站模式之前，您必須先移除網站訂用帳戶的支出上限。 如需有關共用與標準模式定價的詳細資訊，請參閱 [定價詳細資料][PricingDetails]。

1. 在瀏覽器中開啟 [管理入口網站][portal]。
2. 在 **網站** 索引標籤上，按一下您網站的名稱。

    ![] [standardmode1]

3. 按一下 [ **延展** ] 索引標籤。

    ![] [standardmode2]


4. 在 **一般** 區段中，按一下 [設定網站模式 **共用**。

    ![] [standardmode3]

    > [AZURE.NOTE] 如果您將此網站使用流量管理員，您必須使用選取標準模式而非共用。

5. 按一下 [ **儲存**。
6. 系統會將增加成本共用模式 (或標準模式下，如果您選擇 [標準)，按一下 [ **是** 如果貴用戶同意。

    <!--![][standardmode4]-->

    **附註**<br />
    如果發生『設定網站「網站名稱」的規模失敗』錯誤，您可以利用詳細資料按鈕來取得詳細資訊。

<a name="trafficmanager"></a><h2>(選用) 將網站新增至流量管理員</h2>

如果您要對網站使用流量管理員，請執行下列步驟。

1. 如果您還沒有流量管理員設定檔，使用中的資訊 [建立流量管理員設定檔使用 [快速建立][createprofile] 建立一個。 請注意 **。 m** 與您的流量管理員設定檔相關聯的網域名稱。 稍後將會使用該資訊。

2. 使用中的資訊 [新增或刪除端點][addendpoint] 將網站新增為流量管理員設定檔中的端點。

    > [AZURE.NOTE] 如果新增端點時未列出您的網站，請確認已將它設定為標準模式。 您必須讓網站使用標準模式，才能與流量管理員搭配使用。

3. 登入 DNS 註冊機構的網站，然後移至 DNS 管理頁面。 尋找標示為網站的連結或區域 **網域名稱**, ，**DNS**, ，或 **Name Server Management**。

4. 現在，找出可選取或輸入 CNAME 記錄的地方。 您可能需要從下拉式清單中或移至進階設定頁面，才能選取記錄類型。 您應該尋找的單字 **CNAME**, ，**別名**, ，或 **子網域**。

5. 您也必須為 CNAME 提供網域或子網域別名。 例如， **www** 如果您想要建立別名 **www.customdomain.com**。

5. 您也必須提供主機名稱，做為此 CNAME 別名的正式網域名稱。 這是 **。 m** 為您的網站名稱。

例如，下列 CNAME 記錄轉送所有流量 **www.contoso.com** 至 **contoso.trafficmgr.com**, ，網站的網域名稱:

<table border="1" cellspacing="0" cellpadding="5" style="border: 1px solid #000000;">
<tr>
<td><strong>別名/主機名稱/子網域</strong></td>
<td><strong>正式網域</strong></td>
</tr>
<tr>
<td>www</td>
<td>contoso.trafficmgr.com</td>
</tr>
</table>

訪客 **www.contoso.com** 客絕對看不到真正的主機
(contoso.azurewebsite.net)，因此使用者看不到轉送過程。

> [AZURE.NOTE] 如果您正對某個網站使用流量管理員，您不需遵循後續各節中的步驟 '**新增自訂網域的 CNAME**'和'**新增自訂網域的 A 記錄**'。 在前述步驟中建立的 CNAME 記錄，會將連入的流量路由傳送至流量管理員，然後將流量路由傳送至網站端點。

<a name="bkmk_configurecname"></a><h2>新增自訂網域的 CNAME</h2>

若要建立 CNAME 記錄，您必須使用註冊機構提供的工具，在 DNS 表格中為自訂網域新增項目。 各註冊機構指定 CNAME 記錄的方法都很類似，只是稍微不同，但概念都一樣。

1. 使用其中一種方法來尋找 **。 azurewebsite.net** 指派給您網站的網域名稱。

    * 登入 [Azure 管理入口網站][portal], ，選取您的網站，再選取 **儀表板**, ，然後尋找 **網站 URL** 中的項目 **快速瀏覽** 一節。

    * 安裝和設定 [Powershell](/manage/install-and-configure-windows-powershell/), ，然後使用下列命令:

            get-azurewebsite yoursitename | select hostnames

    * 安裝和設定 [Azure 命令列介面](/manage/install-and-configure-cli/), ，然後使用下列命令:

            azure site domain list yoursitename

    儲存此 **。 azurewebsite.net** 命名，因為它會使用下列步驟。

3. 登入 DNS 註冊機構的網站，然後移至 DNS 管理頁面。 尋找標示為網站的連結或區域 **網域名稱**, ，**DNS**, ，或 **Name Server Management**。

4. 現在，找出可選取或輸入 CNAME 記錄的地方。 您可能需要從下拉式清單中或移至進階設定頁面，才能選取記錄類型。 您應該尋找的單字 **CNAME**, ，**別名**, ，或 **子網域**。

5. 您也必須為 CNAME 提供網域或子網域別名。 例如， **www** 如果您想要建立別名 **www.customdomain.com**。 如果您想要建立根網域的別名，它可能會列出成為 '**@**' 註冊機構的 DNS 工具中的符號。

5. 您也必須提供主機名稱，做為此 CNAME 別名的正式網域名稱。 這是 **。 azurewebsite.net** 為您的網站名稱。

例如，下列 CNAME 記錄轉送所有流量 **www.contoso.com** 至 **contoso.azurewebsite.net**, ，網站的網域名稱:

<table border="1" cellspacing="0" cellpadding="5" style="border: 1px solid #000000;">
<tr>
<td><strong>別名/主機名稱/子網域</strong></td>
<td><strong>正式網域</strong></td>
</tr>
<tr>
<td>www</td>
<td>contoso.azurewebsite.net</td>
</tr>
</table>

訪客 **www.contoso.com** 客絕對看不到真正的主機
(contoso.azurewebsite.net)，因此使用者看不到轉送過程
。

> [AZURE.NOTE] 上述範例僅適用於流量 __www__ 子網域。 因為 CNAME 記錄不能使用萬用字元，所以您必須為每一個網域/子網域建立一個 CNAME。 如果您想要直接來自子網域，例如 *。 contoso.com，您的 azurewebsite.net 位址，您可以設定 __URL 重新導向__ 或 __URL 轉送__ 項目，在 DNS 設定，或建立 A 記錄。

> [AZURE.NOTE] 可能需要一些時間，CNAME 才能傳播至整個 DNS 系統。 必須等到 CNAME 傳播之後，您才能設定網站的 CNAME。 您可以使用 <a href="http://www.digwebinterface.com/">http://www.digwebinterface.com/</a> 之類的服務來驗證 CNAME 已生效。

###將網域名稱新增至網站

當網域名稱的 CNAME 記錄傳播完成之後，您必須將此記錄與網站產生關聯。 您可以使用 Azure 命令列介面 (Azure CLI) 或 Azure 管理入口網站，將 CNAME 記錄所定義的自訂網域名稱新增至網站。

**使用命令列工具新增網域名稱**

安裝和設定 [Azure 命令列介面](/manage/install-and-configure-cli/), ，然後使用下列命令:

    azure site domain add customdomain yoursitename

例如，下列會加入自訂網域名稱的 **www.contoso.com** 至 **contoso.azurewebsite.net** 網站:

    azure site domain add www.contoso.com contoso

您可以使用下列命令來確認已將自訂網域名稱新增至網站：

    azure site domain list yoursitename

此命令傳回的清單應該包含自訂網域名稱，以及預設 **。 azurewebsite.net** 項目。

**使用 Azure 管理入口網站新增網域名稱**

1. 在瀏覽器中開啟 [Azure 管理入口網站][portal]。

2. 在 **網站** 索引標籤上，按一下您網站的名稱，選取 **儀表板**, ，然後選取 **管理網域** 從頁面底部。

    ![][setcname2]

6. 在 **網域名稱** 文字] 方塊中，輸入您已設定的網域名稱。

    ![][setcname3]

6. 按一下核取記號以接受網域名稱。

當組態完成之後時，自訂網域名稱會列示在 **網域名稱** 區段 **設定** 您網站的網頁。

<a name="bkmk_configurearecord"></a><h2>新增自訂網域的 A 記錄</h2>

若要建立 A 記錄，您必須先找出網站的 IP 位址。 然後，利用註冊機構提供的工具，在 DNS 表格中為自訂網域新增項目。 各註冊機構指定 A 記錄的方法都很類似，只是稍微不同，但概念都一樣。 除了建立 A 記錄，您還必須建立 CNAME 記錄，供 Azure 用來驗證 A 記錄。

1. 在瀏覽器中開啟 [Azure 管理入口網站][portal]。

2. 在 **網站** 索引標籤上，按一下您網站的名稱，選取 **儀表板**, ，然後選取 **管理網域** 從畫面底部。

    ![][setcname2]

5. 在 **管理自訂網域** ] 對話方塊中，找出 **設定 A 記錄時所要使用的 IP 位址**。 複製 IP 位址。 建立 A 記錄時會使用此位址。

5. 在 **管理自訂網域** ] 對話方塊中，在對話方塊頂端的文字結尾的附註 awverify 網域名稱。 它應該是 **awverify.mysite.azurewebsites.net** 其中 **mysite** 是您的網站名稱。 這是在建立驗證 CNAME 記錄時所使用的網域名稱，請複製下來。

6. 登入 DNS 註冊機構的網站，然後移至 DNS 管理頁面。 尋找標示為網站的連結或區域 **網域名稱**, ，**DNS**, ，或 **Name Server Management**。

6. 找出可選取或輸入 A 和 CNAME 記錄的地方。 您可能需要從下拉式清單中或移至進階設定頁面，才能選取記錄類型。

7. 執行下列步驟來建立 A 記錄：

    1. 選取或輸入將使用 A 記錄的網域或子網域。 例如，選取 **www** 如果您想要建立別名 **www.customdomain.com**。 如果您想要建立萬用字元項目，為所有子網域，請輸入 '__*__'。 這將會涵蓋所有子網域，例如 **mail.customdomain.com**, ，**login.customdomain.com**, ，和 **www.customdomain.com**。

        如果您想要建立根網域的 A 記錄，它可能會列出成為 '**@**' 註冊機構的 DNS 工具中的符號。

    2. 在提供的欄位中，輸入雲端服務的 IP 位址。 這樣會將 A 記錄中使用的網域項目與雲端服務部署的 IP 位址產生關聯。

        例如，下列 A 記錄會將從所有流量都轉送 **contoso.com** 至 **137.135.70.239**, ，我們已部署的應用程式的 IP 位址:

        <table border="1" cellspacing="0" cellpadding="5" style="border: 1px solid #000000;">
        <tr>
        <td><strong>主機名稱/子網域</strong></td>
        <td><strong>IP 位址</strong></td>
        </tr>
        <tr>
        <td>@</td>
        <td>137.135.70.239</td>
        </tr>
        </table>

        此範例示範建立根網域的 A 記錄。 如果您想要建立萬用字元項目來涵蓋所有子網域，請輸入 '__*__' 作為子網域。

7. 接下來，建立具有別名的 CNAME 記錄 **awverify**, ，正式網域和 **awverify.mysite.azurewebsites.net** 先前取得的。

    > [AZURE.NOTE] 有些註冊機構可能運作別名 awverify，其他可能需要完整的別名網域名稱 awverify.www.customdomainname.com 或 awverify.customdomainname.com。

    例如，以下會建立一筆 CNAME 記錄，可供 Azure 用來驗證 A 記錄組態。

    <table border="1" cellspacing="0" cellpadding="5" style="border: 1px solid #000000;">
    <tr>
    <td><strong>別名/主機名稱/子網域</strong></td>
    <td><strong>正式網域</strong></td>
    </tr>
    <tr>
    <td>awverify</td>
    <td>awverify.contoso.azurewebsites.net</td>
    </tr>
    </table>

> [AZURE.NOTE] 它可能需要一些時間，awverify CNAME 才能傳播至整個 DNS 系統。 必須等到 awverify CNAME 傳播之後，您才能為網站設定 A 記錄所定義的自訂網域名稱。 您可以使用 <a href="http://www.digwebinterface.com/">http://www.digwebinterface.com/</a> 之類的服務來驗證 CNAME 已生效。

###將網域名稱新增至網站

之後 **awverify** 網域名稱的 CNAME 記錄傳播完成之後，您可以將產生關聯與您的網站 A 記錄所定義的自訂網域。 您可以使用 Azure CLI 或 Azure 管理入口網站，將記錄所定義的自訂網域名稱新增至網站。

**使用 Azure 命令列介面 (Azure CLI) 新增網域名稱**

安裝和設定 [Azure CLI](/manage/install-and-configure-cli/), ，然後使用下列命令:

    azure site domain add customdomain yoursitename

例如，下列會加入自訂網域名稱的 **contoso.com** 至 **contoso.azurewebsite.net** 網站:

    azure site domain add contoso.com contoso

您可以使用下列命令來確認已將自訂網域名稱新增至網站：

    azure site domain list yoursitename

此命令傳回的清單應該包含自訂網域名稱，以及預設 **。 azurewebsite.net** 項目。

**使用 Azure 管理入口網站新增網域名稱**

1. 在瀏覽器中開啟 [Azure 管理入口網站][portal]。

2. 在 **網站** 索引標籤上，按一下您網站的名稱，選取 **儀表板**, ，然後選取 **管理網域** 從頁面底部。

    ![][setcname2]

6. 在 **網域名稱** 文字] 方塊中，輸入您已設定的網域名稱。

    ![][setcname3]

6. 按一下核取記號以接受網域名稱。

當組態完成之後時，自訂網域名稱會列示在 **網域名稱** 區段 **設定** 您網站的網頁。

> [AZURE.NOTE] 加入至您的網站 A 記錄所定義的自訂網域名稱之後，您可以移除 awverify CNAME 記錄，註冊機構所提供的工具。 不過，如果未來想要新增另一筆 A 記錄，就必須重新建立 awverify 記錄，才能將新的 A 記錄所定義的新網域名稱與網站產生關聯。

## 後續步驟

-   [如何管理網站](/manage/services/web-sites/how-to-manage-websites/)

-   [設定網站的 SSL 憑證](/develop/net/common-tasks/enable-ssl-web-site/)


<!-- Bookmarks -->

[Configure your web sites for shared mode]: #bkmk_configsharedmode
[Configure the CNAME on your domain registrar]: #bkmk_configurecname
[Configure a CNAME verification record on your domain registrar]: #bkmk_configurecname
[Configure an A record for the domain name]:#bkmk_configurearecord
[Set the domain name in management portal]: #bkmk_setcname

<!-- Links -->

[PricingDetails]: /pricing/details/
[portal]: http://manage.windowsazure.com
[digweb]: http://www.digwebinterface.com/
[cloudservicedns]: ../articles/custom-dns.md
[trafficmanager]: ../articles/app-service-web/web-sites-traffic-manager.md
[addendpoint]: ../articles/traffic-manager/traffic-manager-endpoints.md
[createprofile]: ../articles/traffic-manager/traffic-manager-manage-profiles.md

<!-- images -->

[setcname1]: ../media/dncmntask-cname-5.png


<!-- images -->
[standardmode1]: ./media/custom-dns-web-site/dncmntask-cname-1.png
[standardmode2]: ./media/custom-dns-web-site/dncmntask-cname-2.png
[standardmode3]: ./media/custom-dns-web-site/dncmntask-cname-3.png
[standardmode4]: ./media/custom-dns-web-site/dncmntask-cname-4.png


[setcname2]: ./media/custom-dns-web-site/dncmntask-cname-6.png
[setcname3]: ./media/custom-dns-web-site/dncmntask-cname-7.png

