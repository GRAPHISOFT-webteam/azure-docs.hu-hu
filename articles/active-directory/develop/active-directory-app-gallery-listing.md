---
title: "Az alkalmazást az Azure Active Directory alkalmazáskatalógusában felsoroló |} Microsoft Docs"
description: "Hogyan lehet egy alkalmazás, amely támogatja az egyszeri bejelentkezés az Azure Active Directory-alkalmazásgyűjtemény listájára"
services: active-directory
documentationcenter: dev-center-name
author: bryanla
manager: mbaldwin
editor: 
ms.assetid: 820acdb7-d316-4c3b-8de9-79df48ba3b06
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 01/09/2018
ms.author: bryanla
ms.custom: aaddev
ms.openlocfilehash: 502fb555bd3b381c9be0ff04e210cc07f9bf6cd8
ms.sourcegitcommit: c765cbd9c379ed00f1e2394374efa8e1915321b9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/28/2018
---
# <a name="list-your-application-in-the-azure-active-directory-application-gallery"></a>Az alkalmazás szerepeltetése az Azure Active Directory alkalmazáskatalógusában


##  <a name="what-is-the-azure-ad-application-gallery"></a>Mi az az Azure AD application gallery?

Azure Active Directory (Azure AD) egy olyan felhőalapú identitás-szolgáltatás. A [az Azure AD application gallery](https://azure.microsoft.com/marketplace/active-directory/all/) a Azure piactér app Store-ból, ahol egyszeri bejelentkezést és a felhasználók átadása közzétett összes alkalmazás-összekötő. Az ügyfelek, akik használhatja az Azure Active Directory identitás-szolgáltatóként a különböző SaaS alkalmazás összekötők közzétett itt találja. A rendszergazdák adja hozzá az alkalmazásgyűjtemény összekötők konfigurálása és az összekötők használata egyszeri bejelentkezést és üzembe helyezését. Az Azure AD az egyszeri bejelentkezést, beleértve a SAML 2.0, az OpenID Connect, a OAuth és a WS-Fed összes fő összevonási protokollt támogatja. 

## <a name="what-are-the-benefits-of-listing-an-application-in-the-gallery"></a>Milyen előnyökkel listázása egy alkalmazás a katalógusban?

*  Ügyfelek keresése a legjobb lehetséges egyszeri bejelentkezést.

*  Az alkalmazás konfigurációja egyszerű és minimális. 

*  A gyors kereséséhez az alkalmazás a gyűjteményben.

*  Ingyenes, Basic, és az Azure AD Premium-ügyfelek ezt az integrációt is használja. 

*  Kölcsönös ügyfelek részletes konfigurációs oktatóanyag beolvasása. 

*  SCIM használó ügyfelek használható ugyanaz az alkalmazás telepítése.


##  <a name="prerequisites-implement-federation-protocol"></a>Előfeltételek: Megvalósítása összevonási protokoll

Az alkalmazás az Azure AD-alkalmazásgyűjtemény listázásához, először kell megvalósítani a következő összevonási protokollok, az Azure AD által támogatott. Olvassa el a feltételeket és kikötéseket, az Azure AD application gallery itt. 

*   **OpenID Connect**: a több-bérlős alkalmazás létrehozása az Azure ad-ben és a megvalósítását az [az Azure AD hozzájárulási keretrendszer](active-directory-integrating-applications.md#overview-of-the-consent-framework) az alkalmazáshoz. Így minden ügyfél biztosítani tudja az alkalmazás hozzájárul a bejelentkezési kérelem elküldése egy közös végpontot. Szabályozhatja a felhasználó hozzáférést a bérlő azonosítója és a felhasználói UPN megkapta a jogkivonatot a alapján. Az alkalmazás integrálja az Azure AD-val, hajtsa végre a [fejlesztő utasításokat](active-directory-authentication-scenarios.md).

*   **SAML 2.0** vagy **WS-Fed**: a funkció a teendő az SAML/WS-Fed SSO integráció SP vagy a kiállító terjesztési hely módban rendelkeznie kell az alkalmazást. Ha az alkalmazás támogatja az SAML 2.0, integrálva közvetlenül az Azure AD-bérlő segítségével a [egyéni alkalmazás hozzáadása utasításokat](../active-directory-saas-custom-apps.md).

*   **Egyszeri jelszó**: hozzon létre egy webalkalmazást, amely rendelkezik egy lap HTML-bejelentkezés konfigurálása [jelszó-alapú egyszeri bejelentkezést](../active-directory-appssoaccess-whatis.md). Egyszeri jelszó alapú is hívják jelszó vaulting, lehetővé teszi a felhasználói hozzáférés és a webes alkalmazásokhoz, amelyek nem támogatják az identitás-összevonási jelszavak kezeléséhez. Akkor célszerű is forgatókönyvek, amelyben több felhasználó meg szeretné osztani ugyanazt a fiókot, például a szervezet közösségi app fiókokhoz. 

## <a name="submit-the-request-in-the-portal"></a>Küldje el a kérést a portálon

Miután tesztelte, hogy működik-e az alkalmazások integrálása az Azure ad-vel, küldje el a hozzáférési kérelem a a [alkalmazás hálózati Portal](https://microsoft.sharepoint.com/teams/apponboarding/Apps). Ha rendelkezik Office 365-fiókkal, használja a portálhoz való bejelentkezéshez. Ha nem, jelentkezzen be Microsoft-fiókját (például az Outlook vagy a Hotmail) segítségével.

Miután bejelentkezik, a következő lap jelenik meg. Adjon meg egy üzleti indoklását anélkül, a szövegmezőbe, majd válassza ki **hozzáférés kérése**. A csapat ellenőrzi, hogy az adatokat, és hozzáférést tud biztosítani ennek megfelelően. Ezt követően jelentkezzen be a portálra, és küldje el a részletes kérést az alkalmazáshoz.

Ha hozzáférés probléma merül fel, lépjen kapcsolatba a [Azure AD SSO integrációs csoport](<mailto:SaaSApplicationIntegrations@service.microsoft.com>).

![Hozzáférési kérelem SharePoint-portál](./media/active-directory-app-gallery-listing/accessrequest.png)

## <a name="timelines"></a>Ütemtervek
    
A folyamat egy SAML 2.0-s vagy a WS-Fed alkalmazás a katalógusban listázása ütemtervét 7 – 10 nap.

   ![Ütemterv a gyűjtemény SAML-alapú alkalmazást listázása](./media/active-directory-app-gallery-listing/timeline.png)

A folyamat a katalógusban OpenID Connect alkalmazás listázása ütemtervét 2-5 munkanapon.

   ![Ütemterv a gyűjtemény SAML-alapú alkalmazást listázása](./media/active-directory-app-gallery-listing/timeline2.png)

## <a name="escalations"></a>Azok következményeinek

Bármely olyan, az e-mail küldése a [Azure AD SSO integrációs csoport](<mailto:SaaSApplicationIntegrations@service.microsoft.com>) és a lehető leghamarabb lesz válaszolunk.

