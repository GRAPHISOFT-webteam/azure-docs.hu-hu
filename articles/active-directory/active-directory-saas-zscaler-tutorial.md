---
title: "Oktatóanyag: Azure Active Directoryval integrált Zscaler |} Microsoft Docs"
description: "Megtudhatja, hogyan konfigurálhatja az egyszeri bejelentkezés Azure Active Directory és Zscaler között."
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.assetid: 68c453f7-aff1-4614-92d3-9b86f3ad99dc
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/08/2017
ms.author: jeedes
ms.openlocfilehash: ffb413289e9bc284c8043bc867ab00a47c8e1516
ms.sourcegitcommit: e266df9f97d04acfc4a843770fadfd8edf4fa2b7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/11/2017
---
# <a name="tutorial-azure-active-directory-integration-with-zscaler"></a>Oktatóanyag: Azure Active Directoryval integrált Zscaler

Ebben az oktatóanyagban elsajátíthatja Zscaler integrálása az Azure Active Directory (Azure AD).

Zscaler integrálása az Azure AD lehetővé teszi a következő előnyöket biztosítja:

- Megadhatja a Zscaler hozzáféréssel rendelkező Azure AD-ben
- Engedélyezheti a felhasználóknak, hogy automatikusan beolvasása bejelentkezett Zscaler (egyszeri bejelentkezés) számára a saját Azure AD-fiókok
- Kezelheti a fiókokat, egy központi helyen – az Azure-portálon

Ha meg szeretné ismerni az Azure AD SaaS integrálásáról további adatait, tekintse meg [alkalmazás-hozzáférés és egyszeri bejelentkezés az Azure Active Directoryval](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Előfeltételek

Konfigurálása az Azure AD-integrációs Zscaler, a következőkre van szükség:

- Az Azure AD szolgáltatásra
- Egy Zscaler egyszeri bejelentkezés engedélyezve van az előfizetés

> [!NOTE]
> Ez az oktatóanyag lépéseit teszteléséhez nem ajánlott használata termelési környezetben.

Ebben az oktatóanyagban a lépéseket teszteléséhez kövesse ezeket a javaslatokat:

- Ne használja az éles környezetben, nem szükséges.
- Ha még nem rendelkezik az Azure AD próbaverziójának környezetben, egy hónapos próbaverzió kaphat [Itt](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Forgatókönyv leírása
Ebben az oktatóanyagban tesztelése az Azure AD egyszeri bejelentkezéshez egy tesztkörnyezetben. Ebben az oktatóanyagban leírt forgatókönyv két fő építőelemeket áll:

1. A gyűjteményből Zscaler hozzáadása
2. És tesztelés az Azure AD konfigurálása egyszeri bejelentkezés

## <a name="adding-zscaler-from-the-gallery"></a>A gyűjteményből Zscaler hozzáadása
Az Azure AD integrálása a Zscaler konfigurálásához kell hozzáadnia Zscaler a gyűjteményből a felügyelt SaaS-alkalmazások listájára.

**A gyűjteményből Zscaler hozzáadásához hajtsa végre az alábbi lépéseket:**

1. Az a  **[Azure-portálon](https://portal.azure.com)**, kattintson a bal oldali navigációs panelen **Azure Active Directory** ikonra. 

    ![Active Directory][1]

2. Navigáljon a **vállalati alkalmazások**. Ezután lépjen **összes alkalmazás**.

    ![Alkalmazások][2]
    
3. Új alkalmazás hozzáadásához kattintson **új alkalmazás** párbeszédpanel tetején gombra.

    ![Alkalmazások][3]

4. Írja be a keresőmezőbe, **Zscaler**.

    ![Az Azure AD tesztfelhasználó létrehozása](./media/active-directory-saas-zscaler-tutorial/tutorial_zscaler_search.png)

5. Az eredmények panelen válassza ki a **Zscaler**, és kattintson a **Hozzáadás** gombra kattintva vegye fel az alkalmazást.

    ![Az Azure AD tesztfelhasználó létrehozása](./media/active-directory-saas-zscaler-tutorial/tutorial_zscaler_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>És tesztelés az Azure AD konfigurálása egyszeri bejelentkezés
Ebben a szakaszban, konfigurálás és tesztelés az Azure AD egyszeri bejelentkezéshez "Britta Simon" nevű tesztfelhasználó alapján Zscaler.

Az egyszeri bejelentkezés működéséhez az Azure AD meg kell tudja, hogy mi a párjukhoz felhasználó Zscaler a felhasználó Azure AD-ben. Ez azt jelenti az Azure AD-felhasználó és a kapcsolódó felhasználó a Zscaler közötti kapcsolat kapcsolatot kell létrehozni.

Zscaler, rendelje hozzá a értékének a **felhasználónév** értékeként Azure AD-ben a **felhasználónév** a hivatkozás kapcsolat létrehozására.

Az Azure AD egyszeri bejelentkezést a Zscaler tesztelése és konfigurálása, hogy végezze el a következő építőelemeket kell:

1. **[Az Azure AD az egyszeri bejelentkezés konfigurálása](#configuring-azure-ad-single-sign-on)**  – lehetővé teszi a felhasználók a szolgáltatás használatához.
2. **[Proxybeállítások konfigurálása](#configuring-proxy-settings)**  – a Proxybeállítások konfigurálása az Internet Explorerben
3. **[Az Azure AD tesztfelhasználó létrehozása](#creating-an-azure-ad-test-user)**  – az Azure AD egyszeri bejelentkezést a Britta Simon teszteléséhez.
4. **[Zscaler tesztfelhasználó létrehozása](#creating-a-zscaler-test-user)**  - való Britta Simon valami Zscaler, amely csatolva van a felhasználó az Azure AD-ábrázolását.
5. **[Az Azure AD-teszt felhasználó hozzárendelése](#assigning-the-azure-ad-test-user)**  - Britta Simon használata az Azure AD az egyszeri bejelentkezés engedélyezése.
6. **[Egyszeri bejelentkezés tesztelése](#testing-single-sign-on)**  – győződjön meg arról, hogy működik-e a konfiguráció.

### <a name="configuring-azure-ad-single-sign-on"></a>Az Azure AD az egyszeri bejelentkezés konfigurálása

Ebben a szakaszban az Azure AD egyszeri bejelentkezés engedélyezése az Azure portálon, és konfigurálása egyszeri bejelentkezéshez az Zscaler alkalmazásban.

**Konfigurálása az Azure AD az egyszeri bejelentkezés Zscaler, hajtsa végre az alábbi lépéseket:**

1. Az Azure portálon a a **Zscaler** alkalmazás integráció lapján, kattintson a **egyszeri bejelentkezés**.

    ![Egyszeri bejelentkezés konfigurálása][4]

2. Az a **egyszeri bejelentkezés** párbeszédablakban válassza **mód** , **SAML-alapú bejelentkezés** egyszeri bejelentkezés engedélyezése.
 
    ![Egyszeri bejelentkezés konfigurálása](./media/active-directory-saas-zscaler-tutorial/tutorial_zscaler_samlbase.png)

3. Az a **Zscaler tartomány és az URL-címek** területen tegye a következőket:

    ![Egyszeri bejelentkezés konfigurálása](./media/active-directory-saas-zscaler-tutorial/tutorial_zscaler_url.png)

    Az a **bejelentkezési URL-cím** szövegmező, adja meg a következő minta használatával URL-címe:`https://<companyname>.zsclaer.net`

    > [!NOTE] 
    > Ez az érték nincs valós. Frissítse ezt az értéket a tényleges bejelentkezési URL-címet. Ügyfél [Zscaler ügyfél-támogatási csoport](https://www.zscaler.com/company/contact) lekérni ezt az értéket. 

4. Az a **SAML-aláíró tanúsítványa** kattintson **Certificate(Base64)** , és mentse a tanúsítványfájlt, a számítógépen.

    ![Egyszeri bejelentkezés konfigurálása](./media/active-directory-saas-zscaler-tutorial/tutorial_zscaler_certificate.png) 

5. Kattintson a **mentése** gombra.

    ![Egyszeri bejelentkezés konfigurálása](./media/active-directory-saas-zscaler-tutorial/tutorial_general_400.png)

6. A a **Zscaler konfigurációs** kattintson **konfigurálása Zscaler** megnyitásához **bejelentkezés konfigurálása** ablak. Másolás a **SAML-alapú egyszeri bejelentkezési URL-címe** a a **rövid összefoglaló szakasz.**

    ![Egyszeri bejelentkezés konfigurálása](./media/active-directory-saas-zscaler-tutorial/tutorial_zscaler_configure.png) 

7. Egy másik webes böngészőablakban jelentkezzen be a ZScaler vállalati webhely rendszergazdaként.

8. Kattintson a felső menüben **felügyeleti**.
   
    ![Felügyeleti](./media/active-directory-saas-zscaler-tutorial/ic800206.png "felügyeleti")

9. A **rendszergazdák kezelése & szerepkörök**, kattintson a **felhasználók kezelése & hitelesítési**.   
            
    ![Felhasználók & hitelesítés kezelése](./media/active-directory-saas-zscaler-tutorial/ic800207.png "felhasználók & hitelesítés kezelése")

10. Az a **hitelesítési beállítások kiválasztása a szervezet** területen tegye a következőket:   
                
    ![Hitelesítési](./media/active-directory-saas-zscaler-tutorial/ic800208.png "hitelesítés")
   
    a. Válassza ki **hitelesítés, SAML-alapú egyszeri bejelentkezést használó**.

    b. Kattintson a **paramétereinek a konfigurálása SAML-alapú egyszeri bejelentkezés**.

11. Az a **konfigurálása SAML-alapú egyszeri bejelentkezés paraméterek** párbeszédpanel lapon hajtsa végre az alábbi lépéseket, és kattintson **kész**

    ![Egyszeri bejelentkezés](./media/active-directory-saas-zscaler-tutorial/ic800209.png "egyszeri bejelentkezés")
    
    a. Beillesztés a **SAML-alapú egyszeri bejelentkezési URL-címe** értéket, amely az Azure-portálról másolta a **, amelyhez a hitelesítéshez a felhasználóknak legyenek elküldve az SAML-portál URL-címe** szövegmező.
    
    b. Az a **attribútumot a bejelentkezési nevet tartalmazó** szövegmezőhöz típus **NameID**.
    
    c. A letöltött tanúsítvány feltöltése, kattintson a **Zscaler pem**.
    
    d. Válassza ki **SAML-alapú automatikus-kiépítés engedélyezése**.

12. Az a **felhasználói hitelesítés beállítása** párbeszédpanel lapon, a következő lépésekkel:

    ![Felügyeleti](./media/active-directory-saas-zscaler-tutorial/ic800210.png "felügyeleti")
    
    a. Kattintson a **Save** (Mentés) gombra.

    b. Kattintson a **aktiválásához**.

## <a name="configuring-proxy-settings"></a>Proxybeállítások konfigurálása
### <a name="to-configure-the-proxy-settings-in-internet-explorer"></a>A Proxybeállítások konfigurálása az Internet Explorerben

1. Start **Internet Explorer**.

2. Válassza ki **Internetbeállítások** a a **eszközök** menü megnyitása a **Internetbeállítások** párbeszédpanel.   
    
     ![Internetbeállítások](./media/active-directory-saas-zscaler-tutorial/ic769492.png "Internetbeállítások")

3. Kattintson a **kapcsolatok** fülre.   
  
     ![Kapcsolatok](./media/active-directory-saas-zscaler-tutorial/ic769493.png "kapcsolatok")

4. Kattintson a **LAN-beállítások** megnyitásához a **LAN-beállítások** párbeszédpanel.

5. A Proxy server területen tegye a következőket:   
   
    ![Proxykiszolgáló](./media/active-directory-saas-zscaler-tutorial/ic769494.png "proxykiszolgáló")

    a. Válassza ki **proxykiszolgálót használni a helyi hálózaton**.

    b. Írja be a címet szövegmező **gateway.zscaler.net**.

    c. Írja be a Port szövegmező **80**.

    d. Válassza ki **proxykiszolgáló kihagyása helyi címek esetén**.

    e. Kattintson a **OK** bezárásához a **helyi hálózati (LAN) beállításai** párbeszédpanel.

6. Kattintson a **OK** bezárásához a **Internetbeállítások** párbeszédpanel.

> [!TIP]
> Ezek az utasítások belül tömör verziója most el tudja olvasni a [Azure-portálon](https://portal.azure.com), míg az alkalmazás beállításakor!  Ez az alkalmazás a hozzáadása után a **Active Directory > Vállalati alkalmazások** egyszerűen kattintson a **egyszeri bejelentkezés** lapra, és a beágyazott dokumentációja keresztül a **konfigurációs** szakasz alján. További Itt a embedded dokumentációjából szolgáltatásról: [az Azure AD beágyazott dokumentáció]( https://go.microsoft.com/fwlink/?linkid=845985)
> 

### <a name="creating-an-azure-ad-test-user"></a>Az Azure AD tesztfelhasználó létrehozása
Ez a szakasz célja a tesztfelhasználó létrehozása az Azure portálon Britta Simon nevezik.

![Az Azure AD-felhasználó létrehozása][100]

**Tesztfelhasználó létrehozása az Azure AD-ban, hajtsa végre az alábbi lépéseket:**

1. Az a **Azure-portálon**, a bal oldali navigációs ablaktábláján kattintson **Azure Active Directory** ikonra.

    ![Az Azure AD tesztfelhasználó létrehozása](./media/active-directory-saas-zscaler-tutorial/create_aaduser_01.png) 

2. Azon felhasználók listájának megtekintéséhez keresse fel **felhasználók és csoportok** kattintson **minden felhasználó**.
    
    ![Az Azure AD tesztfelhasználó létrehozása](./media/active-directory-saas-zscaler-tutorial/create_aaduser_02.png) 

3. Lehetőségre a **felhasználói** párbeszédpanel, kattintson a **Hozzáadás** párbeszédpanel tetején.
 
    ![Az Azure AD tesztfelhasználó létrehozása](./media/active-directory-saas-zscaler-tutorial/create_aaduser_03.png) 

4. Az a **felhasználói** párbeszédpanel lapon, a következő lépésekkel:
 
    ![Az Azure AD tesztfelhasználó létrehozása](./media/active-directory-saas-zscaler-tutorial/create_aaduser_04.png) 

    a. Az a **neve** szövegmezőhöz típus **BrittaSimon**.

    b. Az a **felhasználónév** szövegmezőhöz típusa a **e-mail cím** a BrittaSimon.

    c. Válassza ki **megjelenítése jelszó** írja le a értékének a **jelszó**.

    d. Kattintson a **Create** (Létrehozás) gombra.
 
### <a name="creating-a-zscaler-test-user"></a>Zscaler tesztfelhasználó létrehozása

Ahhoz, hogy az Azure AD-felhasználók ZScaler bejelentkezni, akkor ki kell építenie ZScaler.  
ZScaler, ha egy kézi tevékenység.

### <a name="to-configure-user-provisioning-perform-the-following-steps"></a>Adja meg a felhasználók átadása, hajtsa végre az alábbi lépéseket:

1. Jelentkezzen be a **Zscaler** bérlő.

2. Kattintson a **felügyeleti**.   
   
    ![Felügyeleti](./media/active-directory-saas-zscaler-tutorial/ic781035.png "felügyeleti")

3. Kattintson a **felhasználókezelés**.   
        
     ![Adja hozzá](./media/active-directory-saas-zscaler-tutorial/ic781036.png "hozzáadása")

4. Az a **felhasználók** lapra, majd **Hozzáadás**.
      
    ![Adja hozzá](./media/active-directory-saas-zscaler-tutorial/ic781037.png "hozzáadása")

5. A felhasználó hozzáadása a szakaszban a következő lépésekkel:
        
    ![Felhasználó hozzáadása](./media/active-directory-saas-zscaler-tutorial/ic781038.png "felhasználó hozzáadása")
   
    a. Típus a **UserID**, **felhasználó megjelenített neve**, **jelszó**, **jelszó megerősítése**, majd válassza ki **csoportok**és a **részleg** egy érvényes AAD-fióknevet, amelyet kiépítését.

    b. Kattintson a **Save** (Mentés) gombra.

> [!NOTE]
> Bármely más ZScaler felhasználói fiók létrehozása eszközök vagy rendelkezés AAD felhasználói fiókokhoz ZScaler által nyújtott API-k.

### <a name="assigning-the-azure-ad-test-user"></a>Az Azure AD-teszt felhasználó hozzárendelése

Ebben a szakaszban engedélyezze Britta Simon által biztosított hozzáférés Zscaler Azure egyszeri bejelentkezéshez használandó.

![Felhasználó hozzárendelése][200] 

**Britta Simon hozzárendelése Zscaler, hajtsa végre az alábbi lépéseket:**

1. Az Azure-portálon, nyissa meg az alkalmazások nézet, majd nyissa meg a könyvtár nézetet, és navigáljon **vállalati alkalmazások** kattintson **összes alkalmazás**.

    ![Felhasználó hozzárendelése][201] 

2. Az alkalmazások listában válassza ki a **Zscaler**.

    ![Egyszeri bejelentkezés konfigurálása](./media/active-directory-saas-zscaler-tutorial/tutorial_zscaler_app.png) 

3. A bal oldali menüben kattintson a **felhasználók és csoportok**.

    ![Felhasználó hozzárendelése][202] 

4. Kattintson a **Hozzáadás** gombra. Válassza ki **felhasználók és csoportok** a **hozzáadása hozzárendelés** párbeszédpanel.

    ![Felhasználó hozzárendelése][203]

5. A **felhasználók és csoportok** párbeszédablakban válassza **Britta Simon** a felhasználók listában.

6. Kattintson a **válasszon** gombra **felhasználók és csoportok** párbeszédpanel.

7. Kattintson a **hozzárendelése** gombra **hozzáadása hozzárendelés** párbeszédpanel.
    
### <a name="testing-single-sign-on"></a>Egyszeri bejelentkezés tesztelése

Ebben a szakaszban az Azure AD egyszeri bejelentkezés beállításai a hozzáférési panelen tesztelése.

Ha a hozzáférési panelen Zscaler csempére kattint, akkor kell beolvasása automatikusan bejelentkezett az Zscaler alkalmazására.
A hozzáférési Panel kapcsolatos további információkért lásd: [a hozzáférési Panel bemutatása](active-directory-saas-access-panel-introduction.md).

## <a name="additional-resources"></a>További források

* [Az Azure Active Directoryval SaaS-alkalmazások integrációjával kapcsolatos bemutatók felsorolása](active-directory-saas-tutorial-list.md)
* [Mi az az alkalmazás-hozzáférés és egyszeri bejelentkezés az Azure Active Directoryban?](active-directory-appssoaccess-whatis.md)

<!--Image references-->

[1]: ./media/active-directory-saas-zscaler-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-zscaler-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-zscaler-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-zscaler-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-zscaler-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-zscaler-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-zscaler-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-zscaler-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-zscaler-tutorial/tutorial_general_203.png

