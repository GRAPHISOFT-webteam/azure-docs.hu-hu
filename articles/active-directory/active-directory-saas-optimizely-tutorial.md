---
title: "Oktatóanyag: Azure Active Directoryval integrált Optimizely |} Microsoft Docs"
description: "Megtudhatja, hogyan konfigurálhatja az egyszeri bejelentkezés Azure Active Directory és Optimizely között."
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.assetid: 28ef03e1-9aad-4301-af97-d94e853edc74
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/23/2017
ms.author: jeedes
ms.openlocfilehash: e44e1621632bf2c8a3c4050b718d2721f5132d06
ms.sourcegitcommit: e266df9f97d04acfc4a843770fadfd8edf4fa2b7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/11/2017
---
# <a name="tutorial-azure-active-directory-integration-with-optimizely"></a>Oktatóanyag: Azure Active Directoryval integrált Optimizely

Ebben az oktatóanyagban elsajátíthatja Optimizely integrálása az Azure Active Directory (Azure AD).

Optimizely integrálása az Azure AD lehetővé teszi a következő előnyöket biztosítja:

- Megadhatja a Optimizely hozzáféréssel rendelkező Azure AD-ben
- Engedélyezheti a felhasználóknak, hogy automatikusan beolvasása bejelentkezett Optimizely (egyszeri bejelentkezés) számára a saját Azure AD-fiókok
- Kezelheti a fiókokat, egy központi helyen – az Azure-portálon

Ha meg szeretné ismerni az Azure AD SaaS integrálásáról további adatait, tekintse meg [alkalmazás-hozzáférés és egyszeri bejelentkezés az Azure Active Directoryval](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Előfeltételek

Konfigurálása az Azure AD-integrációs Optimizely, a következőkre van szükség:

- Az Azure AD szolgáltatásra
- Egy Optimizely egyszeri bejelentkezés engedélyezve van az előfizetés

> [!NOTE]
> Ez az oktatóanyag lépéseit teszteléséhez nem ajánlott használata termelési környezetben.

Ebben az oktatóanyagban a lépéseket teszteléséhez kövesse ezeket a javaslatokat:

- Ne használja az éles környezetben, nem szükséges.
- Ha még nem rendelkezik az Azure AD próbaverziójának környezetben, egy hónapos próbaverzió kaphat [Itt](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Forgatókönyv leírása
Ebben az oktatóanyagban tesztelése az Azure AD egyszeri bejelentkezéshez egy tesztkörnyezetben. Ebben az oktatóanyagban leírt forgatókönyv két fő építőelemeket áll:

1. A gyűjteményből Optimizely hozzáadása
2. És tesztelés az Azure AD konfigurálása egyszeri bejelentkezés

## <a name="adding-optimizely-from-the-gallery"></a>A gyűjteményből Optimizely hozzáadása
Az Azure AD integrálása a Optimizely konfigurálásához kell hozzáadnia Optimizely a gyűjteményből a felügyelt SaaS-alkalmazások listájára.

**A gyűjteményből Optimizely hozzáadásához hajtsa végre az alábbi lépéseket:**

1. Az a  **[Azure-portálon](https://portal.azure.com)**, kattintson a bal oldali navigációs panelen **Azure Active Directory** ikonra. 

    ![Active Directory][1]

2. Navigáljon a **vállalati alkalmazások**. Ezután lépjen **összes alkalmazás**.

    ![Alkalmazások][2]
    
3. Új alkalmazás hozzáadásához kattintson **új alkalmazás** párbeszédpanel tetején gombra.

    ![Alkalmazások][3]

4. Írja be a keresőmezőbe, **Optimizely**.

    ![Az Azure AD tesztfelhasználó létrehozása](./media/active-directory-saas-optimizely-tutorial/tutorial_optimizely_search.png)

5. Az eredmények panelen válassza ki a **Optimizely**, és kattintson a **Hozzáadás** gombra kattintva vegye fel az alkalmazást.

    ![Az Azure AD tesztfelhasználó létrehozása](./media/active-directory-saas-optimizely-tutorial/tutorial_optimizely_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>És tesztelés az Azure AD konfigurálása egyszeri bejelentkezés
Ebben a szakaszban konfigurálása, és tesztelés az Azure AD egyszeri bejelentkezéshez "Britta Simon." nevű tesztfelhasználó alapján Optimizely

Az egyszeri bejelentkezés működéséhez az Azure AD meg kell tudja, hogy mi a párjukhoz felhasználó Optimizely a felhasználó Azure AD-ben. Ez azt jelenti az Azure AD-felhasználó és a kapcsolódó felhasználó a Optimizely közötti kapcsolat kapcsolatot kell létrehozni.

Ez a hivatkozás kapcsolat létesíti értéket rendeli az **felhasználónév** értékeként Azure AD-ben a **felhasználónév** Optimizely a.

Az Azure AD egyszeri bejelentkezést a Optimizely tesztelése és konfigurálása, hogy végezze el a következő építőelemeket kell:

1. **[Az Azure AD az egyszeri bejelentkezés konfigurálása](#configuring-azure-ad-single-sign-on)**  – lehetővé teszi a felhasználók a szolgáltatás használatához.
2. **[Az Azure AD tesztfelhasználó létrehozása](#creating-an-azure-ad-test-user)**  – az Azure AD egyszeri bejelentkezést a Britta Simon teszteléséhez.
3. **[Egy Optimizely tesztfelhasználó létrehozása](#creating-an-optimizely-test-user)**  - való Britta Simon valami Optimizely, amely csatolva van a felhasználó az Azure AD-ábrázolását.
4. **[Az Azure AD-teszt felhasználó hozzárendelése](#assigning-the-azure-ad-test-user)**  - Britta Simon használata az Azure AD az egyszeri bejelentkezés engedélyezése.
5. **[Egyszeri bejelentkezés tesztelése](#testing-single-sign-on)**  – győződjön meg arról, hogy működik-e a konfiguráció.

### <a name="configuring-azure-ad-single-sign-on"></a>Az Azure AD az egyszeri bejelentkezés konfigurálása

Ebben a szakaszban az Azure AD egyszeri bejelentkezés engedélyezése az Azure portálon, és konfigurálása egyszeri bejelentkezéshez az Optimizely alkalmazásban.

**Konfigurálása az Azure AD az egyszeri bejelentkezés Optimizely, hajtsa végre az alábbi lépéseket:**

1. Az Azure portálon a a **Optimizely** alkalmazás integráció lapján, kattintson a **egyszeri bejelentkezés**.

    ![Egyszeri bejelentkezés konfigurálása][4]

2. Az a **egyszeri bejelentkezés** párbeszédablakban válassza **mód** , **SAML-alapú bejelentkezés** egyszeri bejelentkezés engedélyezése.
 
    ![Egyszeri bejelentkezés konfigurálása](./media/active-directory-saas-optimizely-tutorial/tutorial_optimizely_samlbase.png)

3. Az a **Optimizely tartomány és az URL-címek** területen tegye a következőket:

    ![Egyszeri bejelentkezés konfigurálása](./media/active-directory-saas-optimizely-tutorial/tutorial_optimizely_url.png)

    a. Az a **bejelentkezési URL-cím** szövegmező, adja meg a következő minta használatával URL-címe:`https://app.optimizely.net/<instance name>`

    b. Az a **azonosító** szövegmező, adja meg a következő minta használatával URL-címe:`urn:auth0:optimizely:contoso`

    > [!NOTE] 
    > Ezek az értékek nincsenek tényleges. A tényleges bejelentkezési URL-cím és azonosítója, amelynek az ismertetése, az oktatóanyag későbbi részében fogja frissítse az értéket. 

4. Az a **SAML-aláíró tanúsítványa** kattintson **Certificate(Base64)** , és mentse a tanúsítványfájlt, a számítógépen.

    ![Egyszeri bejelentkezés konfigurálása](./media/active-directory-saas-optimizely-tutorial/tutorial_optimizely_certificate.png) 

5. Kattintson a **mentése** gombra.

    ![Egyszeri bejelentkezés konfigurálása](./media/active-directory-saas-optimizely-tutorial/tutorial_general_400.png)

6. A a **Optimizely konfigurációs** kattintson **konfigurálása Optimizely** megnyitásához **bejelentkezés konfigurálása** ablak. Másolás a **SAML-alapú egyszeri bejelentkezési URL-címe** a a **rövid összefoglaló szakasz.**

    ![Egyszeri bejelentkezés konfigurálása](./media/active-directory-saas-optimizely-tutorial/tutorial_optimizely_configure.png) 

7. Egyszeri bejelentkezés konfigurálása **Optimizely** oldalán található, lépjen kapcsolatba a Optimizely fiókkezelővel, és adja meg a letöltött **tanúsítvány (Base64)**, és **SAML-alapú egyszeri bejelentkezési URL-címe**. 

8. Az e-maileket válaszul Optimizely tesz lehetővé a bejelentkezési URL-cím (a Szolgáltató által kezdeményezett SSO) és a azonosítóértékek (Service Provider entitás azonosítója).

    a. Másolás a **Szolgáltató által kezdeményezett egyszeri bejelentkezési URL-cím** Optimizely, és illessze be a megadott a **URL-cím bejelentkezési** textbox **Optimizely tartomány és az URL-címek** szakaszt, Azure-portálon 

    b. Másolás a **Service Provider Entitásazonosító** Optimizely, és illessze be a megadott a **azonosító** textbox **Optimizely tartomány és az URL-címek** szakaszt, Azure-portálon 

9. Egy másik böngészőablakban bejelentkezés az Optimizely alkalmazására.

10. Kattintson, akkor a fiók neve felső sarokban, majd **Fiókbeállítások**.
   
    ![Az Azure AD-egyszeri bejelentkezés](./media/active-directory-saas-optimizely-tutorial/tutorial_optimizely_09.png)

11. A fiók lapon jelölje be a jelölőnégyzetet **SSO engedélyezése** az egyszeri bejelentkezés a a **áttekintése** szakasz.
   
    ![Az Azure AD-egyszeri bejelentkezés](./media/active-directory-saas-optimizely-tutorial/tutorial_optimizely_10.png)
    
12. Kattintson a **mentése**

> [!TIP]
> Ezek az utasítások belül tömör verziója most el tudja olvasni a [Azure-portálon](https://portal.azure.com), míg az alkalmazás beállításakor!  Ez az alkalmazás a hozzáadása után a **Active Directory > Vállalati alkalmazások** egyszerűen kattintson a **egyszeri bejelentkezés** lapra, és a beágyazott dokumentációja keresztül a **konfigurációs** szakasz alján. További Itt a embedded dokumentációjából szolgáltatásról: [az Azure AD beágyazott dokumentáció]( https://go.microsoft.com/fwlink/?linkid=845985)
> 

### <a name="creating-an-azure-ad-test-user"></a>Az Azure AD tesztfelhasználó létrehozása
Ez a szakasz célja a tesztfelhasználó létrehozása az Azure portálon Britta Simon nevezik.

![Az Azure AD-felhasználó létrehozása][100]

**Tesztfelhasználó létrehozása az Azure AD-ban, hajtsa végre az alábbi lépéseket:**

1. Az a **Azure-portálon**, a bal oldali navigációs ablaktábláján kattintson **Azure Active Directory** ikonra.

    ![Az Azure AD tesztfelhasználó létrehozása](./media/active-directory-saas-optimizely-tutorial/create_aaduser_01.png) 

2. Azon felhasználók listájának megtekintéséhez keresse fel **felhasználók és csoportok** kattintson **minden felhasználó**.
    
    ![Az Azure AD tesztfelhasználó létrehozása](./media/active-directory-saas-optimizely-tutorial/create_aaduser_02.png) 

3. Lehetőségre a **felhasználói** párbeszédpanel, kattintson a **Hozzáadás** párbeszédpanel tetején.
 
    ![Az Azure AD tesztfelhasználó létrehozása](./media/active-directory-saas-optimizely-tutorial/create_aaduser_03.png) 

4. Az a **felhasználói** párbeszédpanel lapon, a következő lépésekkel:
 
    ![Az Azure AD tesztfelhasználó létrehozása](./media/active-directory-saas-optimizely-tutorial/create_aaduser_04.png) 

    a. Az a **neve** szövegmezőhöz típus **BrittaSimon**.

    b. Az a **felhasználónév** szövegmezőhöz típusa a **e-mail cím** a Britta Simon.

    c. Válassza ki **megjelenítése jelszó** írja le a értékének a **jelszó**.

    d. Kattintson a **Create** (Létrehozás) gombra.
 
### <a name="creating-an-optimizely-test-user"></a>Egy Optimizely tesztfelhasználó létrehozása

Ebben a szakaszban egy Optimizely Britta Simon nevű felhasználót hoz létre.

1. Válassza ki a kezdőlapon **közreműködők** fülre.

2. Kattintson az új közreműködő hozzáadása a projekthez, **új közreműködő**.
   
    ![Az Azure AD tesztfelhasználó létrehozása](./media/active-directory-saas-optimizely-tutorial/create_aaduser_10.png)

3. Adja meg az e-mail címet, és szerepkört rendel hozzá. Kattintson a **meghívása**.

    ![Az Azure AD tesztfelhasználó létrehozása](./media/active-directory-saas-optimizely-tutorial/create_aaduser_11.png)

4. Az e-mailt a meghívás kapnak. Rendelkeznek Optimizely jelentkezzenek be az e-mail címet használja.

### <a name="assigning-the-azure-ad-test-user"></a>Az Azure AD-teszt felhasználó hozzárendelése

Ebben a szakaszban engedélyezze Britta Simon által biztosított hozzáférés Optimizely Azure egyszeri bejelentkezéshez használandó.

![Felhasználó hozzárendelése][200] 

**Britta Simon hozzárendelése Optimizely, hajtsa végre az alábbi lépéseket:**

1. Az Azure-portálon, nyissa meg az alkalmazások nézet, majd nyissa meg a könyvtár nézetet, és navigáljon **vállalati alkalmazások** kattintson **összes alkalmazás**.

    ![Felhasználó hozzárendelése][201] 

2. Az alkalmazások listában válassza ki a **Optimizely**.

    ![Egyszeri bejelentkezés konfigurálása](./media/active-directory-saas-optimizely-tutorial/tutorial_optimizely_app.png) 

3. A bal oldali menüben kattintson a **felhasználók és csoportok**.

    ![Felhasználó hozzárendelése][202] 

4. Kattintson a **Hozzáadás** gombra. Válassza ki **felhasználók és csoportok** a **hozzáadása hozzárendelés** párbeszédpanel.

    ![Felhasználó hozzárendelése][203]

5. A **felhasználók és csoportok** párbeszédablakban válassza **Britta Simon** a felhasználók listában.

6. Kattintson a **válasszon** gombra **felhasználók és csoportok** párbeszédpanel.

7. Kattintson a **hozzárendelése** gombra **hozzáadása hozzárendelés** párbeszédpanel.
    
### <a name="testing-single-sign-on"></a>Egyszeri bejelentkezés tesztelése

Ebben a szakaszban az Azure AD egyszeri bejelentkezés beállításai a hozzáférési panelen tesztelése.

Ha a hozzáférési panelen Optimizely csempére kattint, akkor kell beolvasása automatikusan bejelentkezett az Optimizely alkalmazására. 

## <a name="additional-resources"></a>További források

* [Az Azure Active Directoryval SaaS-alkalmazások integrációjával kapcsolatos bemutatók felsorolása](active-directory-saas-tutorial-list.md)
* [Mi az az alkalmazás-hozzáférés és egyszeri bejelentkezés az Azure Active Directoryban?](active-directory-appssoaccess-whatis.md)



<!--Image references-->

[1]: ./media/active-directory-saas-optimizely-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-optimizely-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-optimizely-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-optimizely-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-optimizely-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-optimizely-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-optimizely-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-optimizely-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-optimizely-tutorial/tutorial_general_203.png

