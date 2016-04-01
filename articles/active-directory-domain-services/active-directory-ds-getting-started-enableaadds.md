<properties
    pageTitle="Azure Active Directory 網域服務預覽：開始使用 | Microsoft Azure"
    description="開始使用 Azure Active Directory 網域服務"
    services="active-directory-ds"
    documentationCenter=""
    authors="mahesh-unnikrishnan"
    manager="udayh"
    editor="inhenk"/>

<tags
    ms.service="active-directory-ds"
    ms.workload="identity"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="10/16/2015"
    ms.author="maheshu"/>

# Azure AD 網域服務 *（預覽）* -快速入門

## 步驟 3：啟用 Azure AD 網域服務
在此步驟中，您可以針對目錄啟用 Azure AD 網域服務。 執行下列組態步驟，針對您的目錄啟用 Azure AD 網域服務。

1. 瀏覽至 **Azure 管理入口網站** ([https://manage.windowsazure.com](https://manage.windowsazure.com))。
2. 選取 **Active Directory** 在左窗格中的節點。
3. 選取您要啟用 Azure AD 網域服務的 Azure AD 租用戶 (目錄)。

    ![選取 Azure AD 目錄](./media/active-directory-domain-services-getting-started/select-aad-directory.png)

4. 按一下 [ **設定** ] 索引標籤。

    ![設定目錄的索引標籤](./media/active-directory-domain-services-getting-started/configure-tab.png)

5. 向下一節捲動 **網域服務**。

    ![網域服務組態區段](./media/active-directory-domain-services-getting-started/domain-services-configuration.png)

6. 切換 [標題] 選項 **啟用此目錄的網域服務** 至 **是**。 您將會注意到頁面上出現了更多適用於 Azure AD 網域服務的組態選項。

    ![啟用網域服務](./media/active-directory-domain-services-getting-started/enable-domain-services.png)

    > [AZURE.NOTE] 當您啟用您的租用戶的 Azure AD 網域服務時，Azure AD 會產生並儲存所需的驗證使用者 Kerberos 和 NTLM 認證雜湊。

7. 指定 **網域服務的 DNS 網域名稱**。
   - 目錄的預設網域名稱 (也就是結束與 **。 onmicrosoft.com** 網域尾碼) 預設會選取。
   - 清單包含所有已針對 Azure AD 目錄設定的網域，包括您在 [網域] 索引標籤中設定的已驗證以及未驗證的網域。
   - 此外，您也可以輸入自訂網域名稱將它新增至此清單中。

     > [AZURE.WARNING] 請確認網域前置詞 （例如您指定網域名稱。 'contoso' 中 'contoso.local' 網域名稱) 是不超過 15 個字元。 您無法建立網域前置詞超過 15 個字元的「Azure AD 網域服務」網域。

8. 下一個步驟是選取您想要在其中使用「Azure AD 網域服務」的虛擬網路。 選取您剛建立標題為下拉式清單中的虛擬網路 **連接至此虛擬網路的網域服務**。
   - 請確定您指定的虛擬網路屬於「Azure AD 網域服務」所支援的 Azure 區域。
   - 請參閱 [區域頁面](active-directory-ds-regions.md) 若要查看支援的 Azure 區域清單。

9. 當您完成選取上述選項之後，按一下 [ **儲存** 從工作窗格底部的頁面，讓 Azure AD 網域服務。
10. 頁面會顯示 ' 暫止...' 狀態，為您的目錄啟用 Azure AD 網域服務時。

    ![啟用網域服務 - 擱置中狀態](./media/active-directory-domain-services-getting-started/enable-domain-services-pendingstate.png)

    > [AZURE.NOTE] Azure AD 網域服務提供高可用性的受管理的網域。 當您第一次針對網域啟用「Azure AD 網域服務」時，您會注意到可在虛擬網路上使用「網域服務」的 IP 位址會一個接著一個顯示。 一旦服務針對您的網域啟用高可用性之後，第二個 IP 位址將會立即顯示。 當設定高可用性時，作用為您的網域中，您應該會看到兩個 IP 位址在 **網域服務** 區段 **設定** ] 索引標籤。

11. 大約 20-30 分鐘後，您會看到的網域服務是在虛擬網路上可用的第一個 IP 位址 **IP 位址** 欄位 **設定** 頁面。

    ![網域服務已啟用 - 已佈建第一個 IP](./media/active-directory-domain-services-getting-started/domain-services-enabled-firstdc-available.png)

12. 若高可用性會針對您的網域進行運作，則您將會在頁面上看見兩個 IP 位址。 這些都是可在您所選虛擬網路上使用 Azure AD 網域服務的 IP 位址。 記下這些 IP 位址，以更新您虛擬網路的 DNS 設定。 這個步驟會讓虛擬網路上的虛擬機器連線到網域，以進行像是加入網域等作業。

    ![網域服務已啟用 - 已佈建兩個 IP](./media/active-directory-domain-services-getting-started/domain-services-enabled-bothdcs-available.png)

> [AZURE.NOTE] Azure AD 目錄的大小而定 (數字的使用者，群組等)，它將會需要一些時間才能在 Azure AD 網域服務中取得目錄的內容。 這個同步處理程序會在背景執行。 對於具有成千上萬個物件的大型目錄，可能需要一天或兩天的時間，在 Azure AD 網域服務中同步處理所有使用者、群組成員資格和認證並加以使用。


---
[**下一個步驟-更新 DNS 的 Azure 虛擬網路設定。**](active-directory-ds-getting-started-dns.md)


