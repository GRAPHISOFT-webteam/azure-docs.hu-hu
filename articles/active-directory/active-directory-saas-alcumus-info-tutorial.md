<properties
    pageTitle="教學課程：Azure Active Directory 與 Alcumus Info Exchange 整合 | Microsoft Azure"
    description="了解如何設定 Azure Active Directory 與 Alcumus Info Exchange 之間的單一登入。"
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
    ms.date="10/06/2015"
    ms.author="markusvi"/>


# 教學課程：Azure Active Directory 與 Alcumus Info Exchange 整合

本教學課程的目標在於告訴您如何使用 Azure Active Directory (Azure AD) 整合 Alcumus 資訊交換。<br>與 Azure AD 整合 Alcumus 資訊 Exchange 可以提供下列優點: 

- 您可以在 Azure AD 中控制可存取 Alcumus Info Exchange 的人員。 
- 您可以讓使用者使用他們的 Azure AD 帳戶自動登入 Alcumus Info Exchange (單一登入)
- 您可以在 Azure Active Directory 入口網站集中管理您的帳戶。

如果您想要知道更多詳細與 Azure AD 的 SaaS 應用程式整合，請參閱 [什麼是應用程式存取和單一登入與 Azure Active Directory](active-directory-appssoaccess-whatis.md)。

## 必要條件 

若要設定 Azure AD 與 Alcumus Info Exchange 整合，您需要下列項目：

-  [Azure AD](http://azure.microsoft.com/) 訂用帳戶
-  [Alcumus 資訊 Exchange](http://www.alcumusgroup.com/) 單一登入啟用的訂閱


> [AZURE.NOTE] 若要測試的步驟，本教學課程中，我們不建議使用生產環境。


若要測試本教學課程中的步驟，您應該遵循這些建議：

- 除非必要，否則您不應使用生產環境，。
- 如果您沒有 Azure AD 試用環境，您可以取得一個月試用 [這裡](https://azure.microsoft.com/pricing/free-trial/)。 

 
## 案例描述
此教學課程的目標是讓您在測試環境中測試 Azure AD 單一登入。 <br>
本教學課程中說明的案例包含三個主要建置區塊:

1. 從資源庫新增 Alcumus Info Exchange 
2. 設定並測試 Azure AD 單一登入


## 從資源庫新增 Alcumus Info Exchange
若要設定 Alcumus Info Exchange 與 Azure AD 整合，您需要從資源庫將 Alcumus Info Exchange 新增到受管理的 SaaS 應用程式清單。

**若要從資源庫新增 Alcumus Info Exchange，請執行下列步驟：**

1. 在 **Azure 管理入口網站**, ，在左的導覽窗格中，按一下 [ **Active Directory**。 
<br><br>![Active] Directory[] 1<br>

2. 從 **目錄** 清單中，選取您要啟用目錄整合的目錄。

3. 若要開啟 [應用程式] 檢視中，目錄檢視中，按一下 [ **應用程式** 上方功能表中。
<br><br>![應用程式][] 2<br>

4. 按一下 [ **新增** 頁面的底部。
<br><br>![應用程式][] 3<br>

5. 在 **您想要** ] 對話方塊中，按一下 [ **從資源庫新增應用程式**。
<br><br>![應用程式][] 4<br>

6. 在 [搜尋] 方塊中，輸入 **Alcumus 資訊 Exchange**。
<br><br>![應用程式][] 5<br>

7. 在 [結果] 窗格中，選取 **Alcumus 資訊 Exchange**, ，然後按一下 [ **完成** 加入應用程式。
<br><br>![應用程式][400]<br>



##  設定並測試 Azure AD 單一登入
本節目標是說明如何以名為 "Britta Simon" 的測試使用者為基礎，使用 Alcumus Info Exchange 來設定及測試 Azure AD 單一登入。

若要讓單一登入運作，Azure AD 必須知道 Alcumus Info Exchange 與 Azure AD 中互相對應的使用者。 換句話說，必須在 Azure AD 使用者和 Alcumus Info Exchange 中的相關使用者之間建立連結關聯性。<br>
藉由指定的值建立此連結關聯性 **使用者名稱** 在 Azure AD 中的值為 **Username** Alcumus 資訊交換中。
 
若要使用 Alcumus Info Exchange 來設定並測試 Azure AD 單一登入，您需要完成下列建置組塊：

1. **[設定 Azure AD 單一登入](#configuring-azure-ad-single-single-sign-on)** -若要讓使用者可以使用這項功能。
2. **[建立 Azure AD 測試使用者](#creating-an-azure-ad-test-user)** -Azure AD 單一登入與 Britta Simon 測試。
4. **[建立測試使用者 Alcumus 資訊 Exchange](#creating-a-alcumus-info-exchange-test-user)** -使對應的 Britta Simon 連結到她在 Azure AD 代表 Alcumus 資訊交換中。
5. **[指派 Azure AD 測試使用者](#assigning-the-azure-ad-test-user)** -讓 Britta Simon，若要使用 Azure AD 單一登入。
5. **[測試單一登入](#testing-single-sign-on)** -若要確認設定是否可以運作。

### 設定 Azure AD 單一登入

本節目標是要啟用 Azure AD 單一登入 Azure AD 入口網站中，並 Alcumus 資訊 Exchange 應用程式中設定單一登入。<br>

**若要使用 Alcumus Info Exchange 設定 Azure AD 單一登入，請執行下列步驟：**

1. 在 Azure AD 入口網站上 **Alcumus 資訊 Exchange** 應用程式整合頁面上，按一下 [ **設定單一登入** 開啟 **設定單一登入**  ] 對話方塊。<br><br>
![設定單一登入][] 6

2. 在 **您希望使用者如何登入 Alcumus 資訊 Exchange** 頁面上，選取 **Azure AD 單一登入**, ，然後按一下 [ **下一步**。<br><br>
![Azure AD 單一登入][] 7

3. 在 **設定應用程式設定** 對話方塊頁面上，執行下列步驟: 
<br><br>![Azure AD 單一登入][] 8<br>
 
     3.1 在 **回覆 URL** 文字方塊中，輸入已設定，如您所 Alcumus 資訊 Exchange 支援小組的取用者 URL。

     > [AZURE.NOTE] 如果您不知道正確的值為何，請連絡 Alcumus 資訊 Exchange 支援小組，透過 [helpdesk@alcumusgroup.com](mailto:helpdesk@alcumusgroup.com)。

     3.2. 按一下 [ **下一步**。
 
4. 在 **Alcumus 資訊 Exchange 在設定單一登入** 頁面上，按一下 **下載中繼資料**, ，然後將儲存在本機電腦上的中繼資料檔案。<br><br>![何謂 Azure AD Connect][] 9

5. 連絡 Alcumus 資訊 Exchange 支援小組透過 [helpdesk@alcumusgroup.com](mailto:helpdesk@alcumusgroup.com), 中繼資料檔案中，為他們提供，它們可讓他們知道他們應該為您啟用 SSO。


6. 在 Azure AD 入口網站中，選取單一登入設定確認，，然後按一下 [ **下一步**。 <br><br>![何謂 Azure AD Connect][] 10

7. 在 **單一登入確認** 頁面上，按一下 **完成**。  <br><br>![何謂 Azure AD Connect][] 11




### 建立 Azure AD 測試使用者
本節的目標是要在 Azure 入口網站中建立名為 Britta Simon 的測試使用者。<br>
在 [使用者] 清單中選取 **Britta Simon**。<br><br>![建立 Azure AD 使用者][] 20<br>

**若要在 Azure AD 中建立測試使用者，請執行下列步驟：**

1. 在 **Azure 管理入口網站**, ，在左的導覽窗格中，按一下 [ **Active Directory**。<br>
![建立 Azure AD 測試使用者](./media/active-directory-saas-alcumus-info-tutorial/create_aaduser_02.png) 

2. 從 **目錄** 清單中，選取您要啟用目錄整合的目錄。

3. 若要顯示在上方功能表中的使用者，清單中，按一下 [ **使用者**。<br>![建立 Azure AD 測試使用者](./media/active-directory-saas-alcumus-info-tutorial/create_aaduser_03.png) 
 
4. 若要開啟 **新增使用者** ] 對話方塊的底部工具列上，按一下 [ **新增使用者**。 <br>![建立 Azure AD 測試使用者](./media/active-directory-saas-alcumus-info-tutorial/create_aaduser_04.png) 

5. 在 **告訴我們這位使用者** 對話方塊頁面上，執行下列步驟: <br>![建立 Azure AD 測試使用者](./media/active-directory-saas-alcumus-info-tutorial/create_aaduser_05.png) 
  1. 針對 [使用者類型]，選取 [您組織中的新使用者]。
  2. 在 [使用者名稱 **文字方塊**, ，型別 **britta Simon**。
  3. 按 [下一步]。

6.  在 **使用者設定檔** 對話方塊頁面上，執行下列步驟: <br>![建立 Azure AD 測試使用者](./media/active-directory-saas-alcumus-info-tutorial/create_aaduser_06.png) 
  1. 在 **名字** 文字方塊中，輸入 **Britta**。  
  2. 在 **姓氏** 文字方塊中輸入 **Simon**。
  3. 在 **顯示名稱** 文字方塊中，輸入 **Britta Simon**。
  4. 在 **角色** 清單中，選取 **使用者**。
  5. 按一下 [ **下一步**。

7. 在 **取得暫時密碼** 對話方塊頁面上，按一下 [ **建立**。<br>![建立 Azure AD 測試使用者](./media/active-directory-saas-alcumus-info-tutorial/create_aaduser_07.png) 
 
8. 在 **取得暫時密碼** 對話方塊頁面上，執行下列步驟:<br>![建立 Azure AD 測試使用者](./media/active-directory-saas-alcumus-info-tutorial/create_aaduser_08.png) 
  1. 請記下的值 **新密碼**。
  2. 按一下 [ **完整**。   

  
 
### 建立 Alcumus Info Exchange 測試使用者

本節目標是在 Alcumus Info Exchange 中建立名為 Britta Simon 的使用者。

**若要在 Alcumus Info Exchange 中建立名為 Britta Simon 的使用者，請執行以下步驟：**

1. 連絡 Alcumus 資訊 Exchange 支援小組透過 [helpdesk@alcumusgroup.com](mailto:helpdesk@alcumusgroup.com),，


### 指派 Azure AD 測試使用者

本節目標是讓 Britta Simon，授予其存取權 Alcumus 資訊 Exchange 使用 Azure 單一登入。
<br><br>![指派使用者][200]

**若要將 Britta Simon 指派到 Alcumus Info Exchange，請執行以下步驟：**

1. 在 Azure 入口網站中，若要開啟 [應用程式] 檢視中，目錄檢視中，按一下 [ **應用程式** 上方功能表中。<br>
<br><br>![指派給使用者][201]
2. 在應用程式清單中，選取 **Alcumus 資訊 Exchange**。
<br><br>![指派給使用者][202]
1. 在頂端功能表中，按一下 [ **使用者**。<br>
<br><br>![指派給使用者][203]
1. 在 [使用者] 清單中選取 **Britta Simon**。

2. 在底部工具列中，按一下 [ **指派**。
<br><br>![指派給使用者][205]



### 測試單一登入

本節目標是要測試您的 Azure AD 單一登入組態使用存取面板。<br>
當您在存取面板中按一下 [Alcumus Info Exchange] 磚時，應該會自動登入您的 Alcumus Info Exchange 應用程式。


## 其他資源

* [如何與 Azure Active Directory 整合 SaaS 應用程式的教學課程清單](active-directory-saas-tutorial-list.md)
* [什麼是搭配 Azure Active Directory 的應用程式存取和單一登入？](active-directory-appssoaccess-whatis.md)

<!--Image references-->
[1]: ./media/active-directory-saas-alcumus-info-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-alcumus-info-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-alcumus-info-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-alcumus-info-tutorial/tutorial_general_04.png
[5]: ./media/active-directory-saas-alcumus-info-tutorial/tutorial_alcumus_01.png
[6]: ./media/active-directory-saas-alcumus-info-tutorial/tutorial_alcumus_02.png
[7]: ./media/active-directory-saas-alcumus-info-tutorial/tutorial_alcumus_03.png
[8]: ./media/active-directory-saas-alcumus-info-tutorial/tutorial_alcumus_04.png
[9]: ./media/active-directory-saas-alcumus-info-tutorial/tutorial_alcumus_05.png
[10]: ./media/active-directory-saas-alcumus-info-tutorial/tutorial_alcumus_06.png
[11]: ./media/active-directory-saas-alcumus-info-tutorial/tutorial_alcumus_07.png
[20]: ./media/active-directory-saas-alcumus-info-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-alcumus-info-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-alcumus-info-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-alcumus-info-tutorial/tutorial_alcumus_08.png
[203]: ./media/active-directory-saas-alcumus-info-tutorial/tutorial_general_203.png
[204]: ./media/active-directory-saas-alcumus-info-tutorial/tutorial_general_204.png
[205]: ./media/active-directory-saas-alcumus-info-tutorial/tutorial_general_205.png
[400]: ./media/active-directory-saas-alcumus-info-tutorial/tutorial_alcumus_402.png
