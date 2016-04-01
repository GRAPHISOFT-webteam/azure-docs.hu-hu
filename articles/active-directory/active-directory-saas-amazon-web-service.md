<properties
    pageTitle="教學課程：Azure Active Directory 與 Amazon Web 服務 (AWS) 整合 | Microsoft Azure"
    description="了解如何搭配 Azure Active Directory 使用 Amazon Web Service (AWS) 來啟用單一登入、自動化佈建和更多功能！"
    services="active-directory"
    documentationCenter=""
    authors="markusvi"
    manager="stevenpo"
    editor=""/>

<tags
    ms.service="active-directory"
    ms.workload="identity"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="09/30/2015"
    ms.author="markvi"/>


# 教學課程：Azure Active Directory 與 Amazon Web 服務 (AWS) 整合

本教學課程的目標在於告訴您如何使用 Azure Active Directory (Azure AD) 整合 Amazon Web 服務 (AWS)。<br>與 Azure AD 整合 Amazon Web 服務 (AWS) 可以提供下列優點 ︰ 

- 您可以在 Azure AD 中控制可存取 Amazon Web 服務 (AWS) 的人員。 
- 您可以讓使用者使用他們的 Azure AD 帳戶自動登入 Amazon Web 服務 (AWS) (單一登入)
- 您可以在 Azure Active Directory 入口網站集中管理您的帳戶。

如果您想要知道更多詳細與 Azure AD 的 SaaS 應用程式整合，請參閱 [什麼是應用程式存取和單一登入與 Azure Active Directory](active-directory-appssoaccess-whatis.md)。

## 必要條件 

若要設定 Azure AD 與 Amazon Web 服務 (AWS) 整合，您需要下列項目：

- Azure AD 訂用帳戶
- 已啟用 Amazon Web 服務 (AWS) 單一登入的訂用帳戶


> [AZURE.NOTE] 若要測試的步驟，本教學課程中，我們不建議使用生產環境。


若要測試本教學課程中的步驟，您應該遵循這些建議：

- 除非必要，否則您不應使用生產環境，。
- 如果您沒有 Azure AD 試用環境，您可以取得一個月試用 [這裡](https://azure.microsoft.com/pricing/free-trial/)。 

 
## 案例描述
此教學課程的目標是讓您在測試環境中測試 Azure AD 單一登入。 <br>
本教學課程中說明的案例包含三個主要建置區塊 ︰

1. 從資源庫新增 Amazon Web 服務 (AWS) 
2. 設定並測試 Azure AD 單一登入


## 從資源庫新增 Amazon Web 服務 (AWS)
若要設定 Amazon Web 服務 (AWS) 與 Azure AD 整合，您需要從資源庫將 Amazon Web 服務 (AWS) 新增到受管理的 SaaS App 清單。

### 若要從資源庫新增 Amazon Web 服務 (AWS)，請執行下列步驟：

1. 在 **Azure 管理入口網站**, ，在左的導覽窗格中，按一下 [ **Active Directory**。 
   <br><br>![Active Directory][1]<br> 

2. 從 **目錄** 清單中，選取您要啟用目錄整合的目錄。

3. 若要開啟 [應用程式] 檢視中，目錄檢視中，按一下 [ **應用程式** 上方功能表中。 
   <br><br>![應用程式][2]<br>

4. 按一下 [ **新增** 頁面的底部。 
   <br><br>![應用程式][3]<br>

5. 在 **您想要** ] 對話方塊中，按一下 [ **從資源庫新增應用程式**。 
   <br><br>![應用程式][4]<br>

6. 在 [搜尋] 方塊中，輸入 **Amazon Web 服務 (AWS)**。
   <br><br>![應用程式][5]<br>

7. 在 [結果] 窗格中，選取 **Amazon Web 服務 (AWS)**, ，然後按一下 [ **完成** 加入應用程式。
   <br><br>![應用程式][6]<br>



##  設定並測試 Azure AD 單一登入
本節目標是說明如何以名為 "Britta Simon" 的測試使用者為基礎，使用 Amazon Web 服務 (AWS) 來設定及測試 Azure AD 單一登入。

若要讓單一登入運作，Azure AD 必須知道 Amazon Web 服務 (AWS) 與 Azure AD 中互相對應的使用者。 換句話說，必須建立 Azure AD 使用者和相關的使用者在 Amazon Web 服務 (AWS) 之間的連結關聯性。<br>
藉由指定的值建立此連結關聯性 **使用者名稱** 在 Azure AD 中的值為 **Username** Amazon Web 服務 (AWS)。
 
若要使用 Amazon Web 服務 (AWS) 來設定並測試 Azure AD 單一登入，您需要完成下列建置組塊：

1. **[設定 Azure AD 單一單一登入](#configuring-azure-ad-single-single-sign-on)** -若要讓使用者可以使用這項功能。
2. **[建立 Azure AD 測試使用者](#creating-an-azure-ad-test-user)** -Azure AD 單一登入與 Britta Simon 測試。
4. **[建立測試使用者 Amazon Web 服務 (AWS)](#creating-a-halogen-software-test-user)** -使對應的 Britta Simon 在 Amazon Web 服務 (AWS) 連結到她在 Azure AD 代表。
5. **[指派 Azure AD 測試使用者](#assigning-the-azure-ad-test-user)** -讓 Britta Simon，若要使用 Azure AD 單一登入。
5. **[測試單一登入](#testing-single-sign-on)** -若要確認設定是否可以運作。

### 設定 Azure AD 單一登入

本節的目標是 Azure AD 單一登入 Azure AD 入口網站中啟用，並在 Amazon Web 服務 (AWS) 應用程式中設定單一登入。<br>
Amazon Web 服務 (AWS) 應用程式需要特定的格式，會要求您新增自訂屬性對應到 SAML 判斷提示您 **saml 權杖屬性** 組態。 
以下螢幕擷取畫面顯示上述的範例。


<br><br>![設定單一登入][27]<br>

**若要使用 Amazon Web 服務 (AWS) 設定 Azure AD 單一登入，請執行下列步驟：**

1. 在 Azure AD 入口網站上 **Amazon Web 服務 (AWS)** 應用程式整合頁面上，按一下 [ **設定單一登入** 開啟 **設定單一登入**  ] 對話方塊。
<br><br>![設定單一登入][7]<br>

2. 在 **您希望使用者如何登入 Amazon Web 服務 (AWS)** 頁面上，選取 **Azure AD 單一登入**, ，然後按一下 [ **下一步**。
<br><br>![設定單一登入][8]<br>

3. 在 **設定應用程式設定** 對話方塊頁面上，按一下 [下一步]。 
<br><br>![設定應用程式設定][9]<br>
 
4. 在 **設定單一登入在 Amazon Web 服務 (AWS)** 頁面上，按一下 **下載中繼資料**, ，然後將儲存在本機電腦上的中繼資料檔案。
<br><br>![設定單一登入][10]<br>

5. 在不同的瀏覽器視窗中，以系統管理員身分登入您的 Amazon Web 服務 (AWS) 公司網站。

6. 按一下 [ **主控台首頁**。
<br><br>![設定單一登入][11]<br>

7. 按一下 [ **身分識別與存取管理**。 
<br><br>![設定單一登入][12]<br>

8. 按一下 [ **身分識別提供者**, ，然後按一下 [ **建立提供者**。 
<br><br>![設定單一登入][13]<br>

9. 在 **設定提供者** 對話方塊頁面上，執行下列步驟 ︰ 
<br><br>![設定單一登入][14]<br>

     a. 做為 **提供者類型**, ，請選取 **SAML**。

     b. 在 **提供者名稱** 文字方塊中，輸入提供者名稱 (例如 ︰ *WAAD*)。

     c. 若要上傳您下載的中繼資料檔案，請按一下 [ **選擇檔案**。

     d. 按一下 [ **下一步**。


10. 在 **驗證提供者資訊** 對話方塊頁面上，按一下 [ **建立**。 
<br><br>![設定單一登入][15]<br>

11. 按一下 [ **角色**, ，然後按一下 [ **建立新角色**。 
<br><br>![設定單一登入][16]<br>

12. 在 **設定角色名稱** ] 對話方塊中，執行下列步驟 ︰ 
<br><br>![設定單一登入][17]<br>

     a. 在 **角色名稱** 文字方塊中，輸入角色名稱 (例如 ︰ *TestUser*)。

     b. 按一下 [ **下一步**。

13. 在 **選取角色類型** ] 對話方塊中，執行下列步驟 ︰ 
<br><br>![設定單一登入][18]<br>

     a. 選取 **身分識別提供者存取的角色**。

     b. 在 **SAML 提供者的授與 Web 單一登入 (WebSSO) 存取** 區段中，按一下 **選取**。


14. 在 **建立信任** ] 對話方塊中，執行下列步驟 ︰  
<br><br>![設定單一登入][19]<br>
     
     a. SAML 提供者中，選取您已建立 previousley SAML 提供者 (例如 ︰ *WAAD*) 

     b. 按一下 [ **下一步**。


15. 在 **確認角色信任** ] 對話方塊中，按一下 [ **下一步**。 
<br><br>![設定單一登入][32]<br>


16. 在 **附加原則** ] 對話方塊中，按一下 [ **下一步**。  
<br><br>![設定單一登入][33]<br>


17. 在 **檢閱** ] 對話方塊中，執行下列步驟 ︰   
<br><br>![設定單一登入][34]<br>

     a. 複製 **角色 ARN** 值。

     b. 複製 **信任實體** ARN 值。

     c. 按一下 [ **建立角色**。 

18. 在 Azure AD 入口網站中，選取單一登入設定確認，，然後按一下 [ **下一步**。
<br><br>![什麼是 Azure AD Connect][20]<br>

19. 在 **單一登入確認** 頁面上，按一下 **完成** 關閉 **設定單一登入** ] 對話方塊。
<br><br>![什麼是 Azure AD Connect][22]<br>


20. 在頂端功能表中，按一下 **屬性** 開啟 **SAML 權杖屬性** ] 對話方塊。 
<br><br>![設定單一登入][21]<br>

21. 按一下 [ **新增使用者屬性**。 
<br><br>![設定單一登入][23]<br>

22. 在 [新增使用者屬性] 對話方塊中，執行下列步驟。 
<br><br>![設定單一登入][24]<br> 

     a. 在 **屬性名稱** 文字方塊中，輸入 **https://aws.amazon.com/SAML/Attributes/Role**。

     b. 在 **屬性值** 文字方塊中，輸入 **[角色 ARN 值] [受信任實體 ARN value]**。

     >[AZURE.TIP] 這些是您已在建立您的角色時從 [檢閱] 對話方塊複製的值。 

     c. 按一下 [ **完成** 關閉 **加入使用者屬性** ] 對話方塊。

23. 按一下 [ **新增使用者屬性**。 
<br><br>![設定單一登入][23]<br>


24. 在 [新增使用者屬性] 對話方塊中，執行下列步驟。 
<br><br>![設定單一登入][25]<br>


     a. In the **Attribute Name** textbox, type **https://aws.amazon.com/SAML/Attributes/RoleSessionName**.

     b. In the **Attribute Value** textbox, type **userprincipalname**.

     c. Click **Complete** to close the **Add User Attribute** dialog.


25. 按一下 [ **套用變更**。 
<br><br>![設定單一登入][26]<br>





### 建立 Azure AD 測試使用者

本節的目標是在 Azure 入口網站中建立名為 Britta Simon 的測試使用者。<br>
在 [使用者] 清單中選取 **Britta Simon**。<br><br>![建立 Azure AD 測試使用者](./media/active-directory-saas-amazon-web-service/create_aaduser_01.png)<br>

**若要在 Azure AD 中建立測試使用者，請執行下列步驟：**

1. 在 **Azure 管理入口網站**, ，在左的導覽窗格中，按一下 [ **Active Directory**。
<br><br>![建立 Azure AD 測試使用者](./media/active-directory-saas-amazon-web-service/create_aaduser_02.png)<br> 

2. 從 **目錄** 清單中，選取您要啟用目錄整合的目錄。

3. 若要顯示在上方功能表中的使用者，清單中，按一下 [ **使用者**。
<br><br>![建立 Azure AD 測試使用者](./media/active-directory-saas-amazon-web-service/create_aaduser_03.png)<br> 
 
4. 若要開啟 **新增使用者** ] 對話方塊的底部工具列上，按一下 [ **新增使用者**。 
<br><br>![建立 Azure AD 測試使用者](./media/active-directory-saas-amazon-web-service/create_aaduser_04.png)<br> 

5. 在 **告訴我們這位使用者** 對話方塊頁面上，執行下列步驟 ︰ 
<br><br>![建立 Azure AD 測試使用者](./media/active-directory-saas-amazon-web-service/create_aaduser_05.png)<br> 

  1. 針對 [使用者類型]，選取 [您組織中的新使用者]。
  2. 在 [使用者名稱 **文字方塊**, ，型別 **britta Simon**。
  3. 按 [下一步]。

6.  在 **使用者設定檔** 對話方塊頁面上，執行下列步驟 ︰ 
<br><br>![建立 Azure AD 測試使用者](./media/active-directory-saas-amazon-web-service/create_aaduser_06.png)<br> 

  1. 在 **名字** 文字方塊中，輸入 **Britta**。  
  2. 在 **姓氏** 文字方塊中輸入 **Simon**。
  3. 在 **顯示名稱** 文字方塊中，輸入 **Britta Simon**。
  4. 在 **角色** 清單中，選取 **使用者**。
  5. 按一下 [ **下一步**。

7. 在 **取得暫時密碼** 對話方塊頁面上，按一下 [ **建立**。
<br><br>![建立 Azure AD 測試使用者](./media/active-directory-saas-amazon-web-service/create_aaduser_07.png)<br> 
 
8. 在 **取得暫時密碼** 對話方塊頁面上，執行下列步驟 ︰
<br><br>![建立 Azure AD 測試使用者](./media/active-directory-saas-amazon-web-service/create_aaduser_08.png)<br> 

  1. 請記下的值 **新密碼**。
  2. 按一下 [ **完整**。   
  
 
### 建立 Amazon Web 服務 (AWS) 測試使用者

本節目標是在 Amazon Web 服務 (AWS) 中建立名為 Britta Simon 的使用者。

### 若要在 Amazon Web 服務 (AWS) 中建立名為 Britta Simon 的使用者，請執行以下步驟：

1. 登入您 **Amazon Web 服務 (AWS)** 系統管理員身分的公司網站。

2. 按一下 [ **主控台首頁** 圖示。 
<br><br>![設定單一登入][11]<br>

3. 按一下 [身分識別與存取管理。 
<br><br>![設定單一登入][28]<br>

4. 儀表板，按一下 [使用者]，然後按一下 [建立新的使用者。 
<br><br>![設定單一登入][29]<br>

5. 在 [建立使用者] 對話方塊中，執行下列步驟 ︰ 
<br><br>![設定單一登入][30]<br>

     a. 在 **輸入使用者名稱** 文字方塊，在 Azure AD 中輸入 Brita Simon 的使用者名稱 (userprincipalname)。

     b. 按一下 [ **建立**。




### 指派 Azure AD 測試使用者

本節目標是授與 Britta Simon 對 Amazon Web Service (AWS) 的存取權，讓她能夠使用 Azure 單一登入。

![指派使用者][31]

**若要將 Britta Simon 指派到 CloudPassage，請執行以下步驟：**

1. 在 Azure 入口網站中，若要開啟 [應用程式] 檢視中，目錄檢視中，按一下 [ **應用程式** 上方功能表中。
<br><br>![指派給使用者][26]<br>

2. 在應用程式清單中，選取 **Amazon Web 服務 (AWS)**。
<br><br>![指派給使用者][27]<br>

1. 在頂端功能表中，按一下 [ **使用者**。
<br><br>![指派給使用者][25]<br>

1. 在 [使用者] 清單中選取 **Britta Simon**。

2. 在底部工具列中，按一下 [ **指派**。
<br><br>![指派給使用者][29]<br>

### 測試單一登入

本節目標是要測試您的 Azure AD 單一登入組態使用存取面板。<br>
當您在存取面板中按一下 [Amazon Web 服務 (AWS)] 磚時，應該會自動登入您的 Amazon Web 服務 (AWS) 應用程式。


## 其他資源

* [如何與 Azure Active Directory 整合 SaaS 應用程式的教學課程清單](active-directory-saas-tutorial-list.md)
* [什麼是搭配 Azure Active Directory 的應用程式存取和單一登入？](active-directory-appssoaccess-whatis.md)

<!--Image references-->
[1]: ./media/active-directory-saas-amazon-web-service/tutorial_general_01.png
[2]: ./media/active-directory-saas-amazon-web-service/tutorial_general_02.png
[3]: ./media/active-directory-saas-amazon-web-service/tutorial_general_03.png
[4]: ./media/active-directory-saas-amazon-web-service/tutorial_general_04.png
[5]: ./media/active-directory-saas-amazon-web-service/ic795019.png
[6]: ./media/active-directory-saas-amazon-web-service/ic795020.png
[7]: ./media/active-directory-saas-amazon-web-service/ic795027.png
[8]: ./media/active-directory-saas-amazon-web-service/ic795028.png
[9]: ./media/active-directory-saas-amazon-web-service/capture23.png
[10]: ./media/active-directory-saas-amazon-web-service/capture24.png
[11]: ./media/active-directory-saas-amazon-web-service/ic795031.png
[12]: ./media/active-directory-saas-amazon-web-service/ic795032.png
[13]: ./media/active-directory-saas-amazon-web-service/ic795033.png
[14]: ./media/active-directory-saas-amazon-web-service/ic795034.png
[15]: ./media/active-directory-saas-amazon-web-service/ic795035.png
[16]: ./media/active-directory-saas-amazon-web-service/ic795022.png
[17]: ./media/active-directory-saas-amazon-web-service/ic795023.png
[18]: ./media/active-directory-saas-amazon-web-service/ic795024.png
[19]: ./media/active-directory-saas-amazon-web-service/ic795025.png
[20]: ./media/active-directory-saas-amazon-web-service/ic7950351.png
[21]: ./media/active-directory-saas-amazon-web-service/tutorial_general_80.png
[22]: ./media/active-directory-saas-amazon-web-service/ic7950352.png
[23]: ./media/active-directory-saas-amazon-web-service/tutorial_general_81.png
[24]: ./media/active-directory-saas-amazon-web-service/ic7950353.png
[25]: ./media/active-directory-saas-amazon-web-service/tutorial_general_15.png
[26]: ./media/active-directory-saas-amazon-web-service/tutorial_general_18.png
[27]: ./media/active-directory-saas-amazon-web-service/ic7950357.png
[28]: ./media/active-directory-saas-amazon-web-service/ic7950321.png
[29]: ./media/active-directory-saas-amazon-web-service/tutorial_general_16.png
[30]: ./media/active-directory-saas-amazon-web-service/ic795038.png
[31]: ./media/active-directory-saas-amazon-web-service/tutorial_general_17.png
[32]: ./media/active-directory-saas-amazon-web-service/ic7950251.png
[33]: ./media/active-directory-saas-amazon-web-service/ic7950252.png
[34]: ./media/active-directory-saas-amazon-web-service/ic7950253.png

























