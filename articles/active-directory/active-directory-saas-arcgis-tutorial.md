<properties 
    pageTitle="教學課程：Azure Active Directory 與 ArcGIS 整合 | Microsoft Azure" 
    description="了解如何使用 ArcGIS 搭配 Azure Active Directory 來啟用單一登入、自動化佈建和更多功能！" 
    services="active-directory" 
    authors="markusvi"  
    documentationCenter="na" 
    manager="stevenpo"/>
<tags 
    ms.service="active-directory" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.tgt_pltfrm="na" 
    ms.workload="identity" 
    ms.date="10/22/2015" 
    ms.author="markvi" />

#教學課程：Azure Active Directory 與 ArcGIS 整合

本教學課程的目的是要示範 Azure 與 ArcGIS 的整合。 本教學課程中說明的案例假設您已經具有下列項目：

-   有效的 Azure 訂閱
-   啟用 ArcGIS 單一登入的訂用帳戶

完成本教學課程之後, 您已指派給 ArcGIS 的 Azure AD 使用者將能夠登入位於您 ArcGIS 公司網站 (服務提供者起始登入)，應用程式的單一登入或使用 [存取面板簡介](active-directory-saas-access-panel-introduction.md)。

本教學課程中說明的案例由下列建置組塊組成：

1.  啟用 ArcGIS 的應用程式整合
2.  設定單一登入
3.  設定使用者佈建
4.  指派使用者

![案例](./media/active-directory-saas-arcgis-tutorial/IC784735.png "Scenario")
##啟用 ArcGIS 的應用程式整合

本節的目的是要說明如何啟用 ArcGIS 的應用程式整合。

###若要啟用 ArcGIS 的應用程式整合，請執行下列步驟：

1.  在 Azure 管理入口網站中，在左的導覽窗格中，按一下 [ **Active Directory**。

    ![Active Directory](./media/active-directory-saas-arcgis-tutorial/IC700993.png "Active Directory")

2.  從 **目錄** 清單中，選取您要啟用目錄整合的目錄。

3.  若要開啟 [應用程式] 檢視中，目錄檢視中，按一下 [ **應用程式** 上方功能表中。

    ![應用程式](./media/active-directory-saas-arcgis-tutorial/IC700994.png "Applications")

4.  按一下 [ **新增** 頁面的底部。

    ![新增應用程式](./media/active-directory-saas-arcgis-tutorial/IC749321.png "Add application")

5.  在 **您想要** ] 對話方塊中，按一下 [ **從資源庫新增應用程式**。

    ![從組件庫新增應用程式](./media/active-directory-saas-arcgis-tutorial/IC749322.png "Add an application from gallerry")

6.  在 **搜尋方塊**, ，型別 **ArcGIS**。

    ![應用程式庫](./media/active-directory-saas-arcgis-tutorial/IC784736.png "Applcation Gallery")

7.  在 [結果] 窗格中，選取 **ArcGIS**, ，然後按一下 [ **完成** 加入應用程式。

    ![ArcGIS](./media/active-directory-saas-arcgis-tutorial/IC784737.png "ArcGIS")
##設定單一登入

本節的目的是要說明如何依據 SAML 通訊協定來使用同盟，讓使用者能夠用自己的 Azure AD 帳戶驗證至 ArcGIS。

###若要設定單一登入，請執行下列步驟：

1.  在 Azure AD 入口網站上 **ArcGIS** 應用程式整合頁面上，按一下 [ **設定單一登入** 開啟 * * 設定單一登入 * * 對話方塊。

    ![設定單一登入](./media/active-directory-saas-arcgis-tutorial/IC784738.png "Configure Single Sign-On")

2.  在 **您希望使用者如何登入 ArcGIS** 頁面上，選取 **Microsoft Azure AD 單一登入**, ，然後按一下 [ **下一步**。

    ![設定單一登入](./media/active-directory-saas-arcgis-tutorial/IC784739.png "Configure Single Sign-On")

3.  在 **設定應用程式 URL** 頁面上，於 **ArcGIS 登入 URL** 文字方塊中，輸入 URL，讓使用者中用來登入使用以下模式 」*https://company.maps.arcgis.com*」，然後按一下 [ **下一步**。

    ![設定應用程式 URL](./media/active-directory-saas-arcgis-tutorial/IC784740.png "Configure App URL")

4.  在 **設定單一登入在 ArcGIS** 頁面上，按一下 **下載中繼資料**, ，然後將儲存在本機電腦上的中繼資料檔案。

    ![設定單一登入](./media/active-directory-saas-arcgis-tutorial/IC784741.png "Configure Single Sign-On")

5.  在不同的網頁瀏覽器視窗中，以系統管理員身分登入您的 ArcGIS 公司網站。

6.  按一下 [ **編輯設定**。

    ![編輯設定](./media/active-directory-saas-arcgis-tutorial/IC784742.png "Edit Settings")

7.  按一下 [ **安全性**。

    ![安全性](./media/active-directory-saas-arcgis-tutorial/IC784743.png "Security")

8.  在 **企業登入**, ，按一下 [ **設定身分識別提供者**。

    ![企業登入](./media/active-directory-saas-arcgis-tutorial/IC784744.png "Enterprise Logins")

9.  在 **設定身分識別提供者** 組態頁面上，執行下列步驟:

    ![設定識別提供者](./media/active-directory-saas-arcgis-tutorial/IC784745.png "Set Identity Provider")

    1.  在 [名稱] 文字方塊中，輸入您的組織名稱。
    2.  如 **企業身分識別提供者的中繼資料，才會提供使用**, ，請選取 **檔案**。
    3.  若要上傳您下載的中繼資料檔案，請按一下 [ **選擇檔案**。
    4.  按一下 [ **設定身分識別提供者**。

10. 在 Azure AD 入口網站中，選取單一登入設定確認，，然後按一下 [ **完成** 關閉 **設定單一登入** ] 對話方塊。

    ![設定單一登入](./media/active-directory-saas-arcgis-tutorial/IC784746.png "Configure Single Sign-On")
##設定使用者佈建

若要讓 Azure AD 使用者可以登入 ArcGIS，必須將他們佈建到 ArcGIS。  
ArcGIS 需以手動方式佈建。

###若要設定使用者佈建，請執行下列步驟：

1.  登入您 **ArcGIS** 租用戶。

2.  按一下 [ **邀請成員**。

    ![邀請成員](./media/active-directory-saas-arcgis-tutorial/IC784747.png "Invite Members")

3.  選取 **自動新增成員，而不是傳送電子郵件**, ，然後按一下 [ **下一步**。

    ![自動加入成員](./media/active-directory-saas-arcgis-tutorial/IC784748.png "Add Members Automatically")

4.  在 **成員** 對話方塊頁面上，執行下列步驟:

    ![加入並檢閱](./media/active-directory-saas-arcgis-tutorial/IC784749.png "Add and review")

    1.  輸入 **名字**, ，**姓氏** 和 **電子郵件** 您想要佈建之有效 AAD 帳戶。
    2.  按一下 [ **新增並檢閱**。

5.  檢閱您所輸入，然後按一下的資料 **新增成員**。

    ![加入成員](./media/active-directory-saas-arcgis-tutorial/IC784750.png "Add member")

>[AZURE.NOTE] 您可以使用任何其他 ArcGIS 使用者帳戶建立工具或 Api 來佈建 AAD 使用者帳戶提供 ArcGIS。

##指派使用者

若要測試您的組態，則需指派您所允許使用您應用程式的 Azure AD 使用者，藉此授予其存取組態的權限。

###若要將使用者指派給 ArcGIS，請執行下列步驟：

1.  在 Azure AD 入口網站中建立測試帳戶。

2.  在 * * ArcGIS * * 應用程式整合頁面上，按一下 [ **指派使用者**。

    ![指派使用者](./media/active-directory-saas-arcgis-tutorial/IC784751.png "Assign Users")

3.  選取測試使用者，請按一下 **指派**, ，然後按一下 [ **是** 以確認您的作業。

    ![是](./media/active-directory-saas-arcgis-tutorial/IC767830.png "Yes")

如果要測試您的單一登入設定，請開啟存取面板。 如需有關存取面板的詳細資訊，請參閱 [存取面板簡介](active-directory-saas-access-panel-introduction.md)。

