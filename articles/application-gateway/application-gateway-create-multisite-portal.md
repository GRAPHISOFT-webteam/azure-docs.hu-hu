---
title: "Hozzon létre egy alkalmazás több webhely-üzemeltetés – az Azure portálon |} Microsoft Docs"
description: "Ismerje meg, amelyen az Azure portál használatával több hely Alkalmazásátjáró létrehozása."
services: application-gateway
author: davidmu1
manager: timlt
editor: tysonn
ms.service: application-gateway
ms.topic: article
ms.workload: infrastructure-services
ms.date: 01/26/2018
ms.author: davidmu
ms.openlocfilehash: 403c6c254d8547b09e42f0b1561e5eff350a1f9b
ms.sourcegitcommit: 059dae3d8a0e716adc95ad2296843a45745a415d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/09/2018
---
# <a name="create-an-application-gateway-with-multiple-site-hosting-using-the-azure-portal"></a>Hozzon létre egy alkalmazás több helyrendszerszerepkört üzemeltető, az Azure portál használatával

Az Azure portál segítségével konfigurálhatja [több webhely tárolása](application-gateway-multi-site-overview.md) létrehozásakor egy [Alkalmazásátjáró](application-gateway-introduction.md). Ebben az oktatóanyagban hoz létre a virtuálisgép-méretezési csoportok használatával háttérkészletek menüpontot. Ezután konfigurálja figyelők és szabályok alapján a tartományok, amelyek a saját győződjön meg arról, hogy a webes forgalom érkezik a készletek a megfelelő kiszolgálókat. Ez az oktatóanyag feltételezi, hogy Ön a tulajdonosa több tartományok és felhasználási mintái *www.contoso.com* és *www.fabrikam.com*.

Ebből a cikkből megismerheti, hogyan:

> [!div class="checklist"]
> * Application Gateway létrehozása
> * Virtuális gépek háttérkiszolgálókhoz létrehozása
> * Hozzon létre háttérkészletek menüpontot a háttérkiszolgálókon
> * Figyelők és útválasztási szabályok létrehozása
> * Create a CNAME record in your domain

![Többhelyes útválasztási – példa](./media/application-gateway-create-multisite-portal/scenario.png)

Ha nem rendelkezik Azure-előfizetéssel, mindössze néhány perc alatt létrehozhat egy [ingyenes fiókot](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) a virtuális gép létrehozásának megkezdése előtt.

## <a name="log-in-to-azure"></a>Jelentkezzen be az Azure-ba

Jelentkezzen be az Azure portálon, a [http://portal.azure.com](http://portal.azure.com)

## <a name="create-an-application-gateway"></a>Application Gateway létrehozása

Egy virtuális hálózatot az Ön által létrehozott erőforrások közötti kommunikációra van szükség. Két alhálózat ebben a példában jönnek létre: egyet az Alkalmazásátjáró, míg a másik a háttérkiszolgálókhoz. Az Alkalmazásátjáró létrehozott egyszerre egy virtuális hálózatot is létrehozhat.

1. Kattintson a **új** az Azure portál bal felső sarkában található.
2. Válassza ki **hálózati** majd **Application Gateway** kiemelt listájában.
3. Adja meg ezeket az értékeket az Alkalmazásátjáró:

    - *myAppGateway* – az Alkalmazásátjáró nevét.
    - *myResourceGroupAG* – az új erőforráscsoport.

    ![Új Alkalmazásátjáró létrehozása](./media/application-gateway-create-multisite-portal/application-gateway-create.png)

4. Fogadja el a további beállításoknál az alapértelmezett értékeket, és kattintson a **OK**.
5. Kattintson a **virtuális hálózatot választ**, kattintson a **hozzon létre új**, és ezekkel az értékekkel adja meg a virtuális hálózat:

    - *myVNet* – a virtuális hálózat nevét.
    - *10.0.0.0/16* – a virtuális hálózat címtere.
    - *myAGSubnet* – az alhálózati név.
    - *10.0.0.0/24* – az alhálózati címtartományt.

    ![Virtuális hálózat létrehozása](./media/application-gateway-create-multisite-portal/application-gateway-vnet.png)

6. Kattintson a **OK** a virtuális hálózati és alhálózati létrehozásához.
7. Kattintson a **egy nyilvános IP-cím kiválasztása**, kattintson a **hozzon létre új**, és írja be a nyilvános IP-cím neve. Ebben a példában a nyilvános IP-cím neve *myAGPublicIPAddress*. Fogadja el a további beállításoknál az alapértelmezett értékeket, és kattintson a **OK**.
8. Fogadja el az alapértelmezett értékeket, a figyelő a konfigurációhoz, hagyja a webalkalmazási tűzfal le van tiltva, és kattintson **OK**.
9. Tekintse át a beállításokat az Összegzés lapon, és kattintson **OK** a hálózati erőforrások és az Alkalmazásátjáró létrehozása. Az alkalmazás-átjáró hozható létre, várjon, amíg a telepítés sikeresen befejeződik, mielőtt továbblép a következő szakaszban több percig is eltarthat.

### <a name="add-a-subnet"></a>Adjon hozzá egy alhálózatot

1. Kattintson a **összes erőforrás** a bal oldali menüből, majd **myVNet** erőforrások listából.
2. Kattintson a **alhálózatok**, és kattintson a **alhálózati**.

    ![Hozzon létre az alhálózatot](./media/application-gateway-create-multisite-portal/application-gateway-subnet.png)

3. Adja meg *myBackendSubnet* neveként az alhálózati majd **OK**.

## <a name="create-virtual-machines"></a>Virtuális gépek létrehozása

Ebben a példában két virtuális gép az Alkalmazásátjáró háttér-kiszolgálóként használandó hoz létre. IIS ellenőrzése, hogy a forgalom útválasztásához megfelelően van a virtuális gépeken is telepítse.

1. Kattintson az **Új** lehetőségre.
2. Kattintson a **számítási** majd **Windows Server 2016 Datacenter** kiemelt listájában.
3. Adja meg a virtuális gép ezeket az értékeket:

    - *contosoVM* – a virtuális gép nevét.
    - *azureuser* – a rendszergazdai felhasználónevet.
    - *Azure123456!* a jelszó.
    - Válassza ki **meglévő**, majd válassza ki *myResourceGroupAG*.

4. Kattintson az **OK** gombra.
5. Válassza ki **DS1_V2** a virtuális gépet, majd kattintson a méretét **válasszon**.
6. Győződjön meg arról, hogy **myVNet** van kiválasztva a virtuális hálózat és az alhálózat van **myBackendSubnet**. 
7. Kattintson a **letiltott** letiltani a rendszerindítási diagnosztika.
8. Kattintson a **OK**, tekintse át a beállításokat az Összegzés lapon, és kattintson a **létrehozása**.

### <a name="install-iis"></a>Az IIS telepítése

1. Az interaktív rendszerhéjat, és győződjön meg arról, hogy van-e állítva **PowerShell**.

    ![Egyéni kiterjesztés telepítése](./media/application-gateway-create-multisite-portal/application-gateway-extension.png)

2. A következő parancsot az IIS telepítése a virtuális gépen: 

    ```azurepowershell-interactive
    $publicSettings = @{ "fileUris" = (,"https://raw.githubusercontent.com/davidmu1/samplescripts/master/appgatewayurl.ps1");  "commandToExecute" = "powershell -ExecutionPolicy Unrestricted -File appgatewayurl.ps1" }
    Set-AzureRmVMExtension `
      -ResourceGroupName myResourceGroupAG `
      -Location eastus `
      -ExtensionName IIS `
      -VMName contosoVM `
      -Publisher Microsoft.Compute `
      -ExtensionType CustomScriptExtension `
      -TypeHandlerVersion 1.4 `
      -Settings $publicSettings
    ```

3. A második virtuális gép létrehozásához, és a lépéseket, amelyek az imént befejeződött az IIS telepítése. Írja be a neveket a *fabrikamVM* nevét és a Set-AzureRmVMExtension VMName értékénél.

## <a name="create-backend-pools-with-the-virtual-machines"></a>Háttér-címkészletek létrehozása a virtuális gépekkel

1. Kattintson a **összes erőforrás** majd **myAppGateway**.
2. Kattintson a **háttérkészletek**, és kattintson a **Hozzáadás**.
3. Adjon meg egy *contosoPool* , és adja hozzá *contosoVM* használatával **Hozzáadás cél**.

    ![Adja hozzá a háttérkiszolgálókon](./media/application-gateway-create-multisite-portal/application-gateway-multisite-backendpool.png)

4. Kattintson az **OK** gombra.
5. Kattintson a **háttérkészletek** majd **Hozzáadás**.
6. Hozzon létre a *fabrikamPool* rendelkező a *fabrikamVM* imént befejeződött lépéseit követve.

## <a name="create-listeners-and-routing-rules"></a>Figyelők és útválasztási szabályok létrehozása

1. Kattintson a **figyelői** majd **többhelyes**.
2. Adja meg a figyelő ezeket az értékeket:
    
    - *contosoListener* – a figyelő nevét.
    - *www.contoso.com* -állomás neve példában cserélje le a tartomány nevét.

3. Kattintson az **OK** gombra.
4. Hozzon létre egy második figyelőt nevével *fabrikamListener* és a második tartománynevét használja. Ebben a példában *www.fabrikam.com* szolgál.

Szabályok feldolgozása sorrendben szerepelnek, és a forgalom irányítja a rendszer az első függetlenül sajátlagossága figyelembe vétele egyező szabály használatával. Például ha egy szabályt egy alapszintű figyelő és egy többhelyes figyelő mindkét ugyanazt a portot használó szabály, a szabály a többhelyes figyelőjével szerepelnie kell a szabály a vártnak megfelelően működik az alapvető figyelő ahhoz, hogy a többhelyes szabály előtt. 

Ebben a példában két új szabályt hoz létre, és törölje az alapértelmezett szabály, amely az Alkalmazásátjáró létrehozásakor jött létre. 

1. Kattintson a **szabályok** majd **alapvető**.
2. Adja meg *contosoRule* nevét.
3. Válassza ki *contosoListener* a figyelőhöz.
4. Válassza ki *contosoPool* a háttérkészlet.

    ![Elérési út alapú szabály létrehozása](./media/application-gateway-create-multisite-portal/application-gateway-multisite-rule.png)

5. Kattintson az **OK** gombra.
6. Hozzon létre egy második szabály nevének használatával *fabrikamRule*, *fabrikamListener*, és *fabrikamPool*.
7. Törli az alapértelmezett szabályt nevű *Szabály1* kattintson rá, majd **törlése**.

## <a name="create-a-cname-record-in-your-domain"></a>Create a CNAME record in your domain

Nyilvános IP-címmel az Alkalmazásátjáró létrehozása után lekérni a DNS-címét, és hozzon létre egy CNAME rekordot a tartomány segítségével. A-rekordok használata nem ajánlott, mert a VIP módosíthatja az Alkalmazásátjáró újraindításakor.

1. Kattintson a **összes erőforrás**, és kattintson a **myAGPublicIPAddress**.

    ![Rekord Alkalmazásátjáró DNS-címét](./media/application-gateway-create-multisite-portal/application-gateway-multisite-dns.png)

2. Copy the DNS address and use it as the value for a new CNAME record in your domain.

## <a name="test-the-application-gateway"></a>Az Alkalmazásátjáró tesztelése

1. Adjon meg a tartománynevet a böngésző címsorába. Such as, http://www.contoso.com.

    ![Az alkalmazás átjáró contoso hely tesztelése](./media/application-gateway-create-multisite-portal/application-gateway-iistest.png)

2. Módosítsa a címet a másik tartományban, és az alábbihoz hasonlót kell megjelennie:

    ![Az alkalmazás átjáró fabrikam hely tesztelése](./media/application-gateway-create-multisite-portal/application-gateway-iistest2.png)

## <a name="next-steps"></a>További lépések

Ebben a cikkben megtanulta, hogyan:

> [!div class="checklist"]
> * Application Gateway létrehozása
> * Virtuális gépek háttérkiszolgálókhoz létrehozása
> * Hozzon létre háttérkészletek menüpontot a háttérkiszolgálókon
> * Figyelők és útválasztási szabályok létrehozása
> * Create a CNAME record in your domain

> [!div class="nextstepaction"]
> [További tudnivalók az Alkalmazásátjáró teendők](application-gateway-introduction.md)