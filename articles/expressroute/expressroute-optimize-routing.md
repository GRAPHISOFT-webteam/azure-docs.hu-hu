---
title: "Az ExpressRoute-útválasztás optimalizálása | Microsoft Docs"
description: "Ez az oldal részletesen ismerteti, hogy hogyan optimalizálható az útválasztás, ha egy ügyfél több olyan ExpressRoute-kapcsolatcsoporttal rendelkezik, amely összeköti Microsoftot az ügyfél vállalati hálózatával."
documentationcenter: na
services: expressroute
author: charwen
manager: carmonm
editor: 
ms.assetid: fca53249-d9c3-4cff-8916-f8749386a4dd
ms.service: expressroute
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 01/27/2017
ms.author: charwen
translationtype: Human Translation
ms.sourcegitcommit: 1b26e82f862a3b2149024d863b907899e14e7d86
ms.openlocfilehash: 404929cf0def75d92d8bb6de8b41be3aecced458


---
# <a name="optimize-expressroute-routing"></a>Az ExpressRoute-útválasztás optimalizálása
Ha több ExpressRoute-kapcsolatcsoporttal rendelkezik, több útvonalon csatlakozhat a Microsofthoz. Ennek eredményeképpen előfordulhat, hogy az útválasztás nem lesz optimális – azaz a forgalom hosszabb úton jut el a Microsofthoz, illetve a Microsofttól az Ön hálózatába. Minél hosszabb a hálózati útvonal, annál nagyobb a késés. A késés közvetlen hatással van az alkalmazások teljesítményére és a felhasználói élményre. Ez a cikk ezt a problémát mutatja be, és ismerteti, hogyan optimalizálható az útválasztás a standard útválasztási technológiák segítségével.

## <a name="suboptimal-routing-from-customer-to-microsoft"></a>Optimálisnál rosszabb útválasztás az ügyféltől a Microsoft felé
Vizsgáljuk meg az útválasztási problémát egy példán keresztül. Tegyük fel, hogy van két irodája az Egyesült Államokban, egy Los Angelesben és egy New Yorkban. Az irodák egy nagykiterjedésű hálózaton (WAN) keresztül csatlakoznak, amely lehet a saját gerinchálózata vagy a szolgáltatója IP-alapú VPN hálózata. Két ExpressRoute-kapcsolatcsoporttal rendelkezik, egy az USA nyugati régiójában és egy az USA keleti régiójában, amelyek szintén a WAN hálózathoz csatlakoznak. Nyilvánvaló, hogy két útvonalon keresztül csatlakozhat a Microsoft hálózatához. Most tegyük fel, hogy az USA nyugati régiójában és az USA keleti régiójában is rendelkezik egy Azure üzemelő példánnyal (például az Azure App Service-szel). Az a szándéka, hogy a Los Angeles-i felhasználóit az USA nyugati Azure-régiójához, a New York-i felhasználóit pedig az USA keleti Azure-régiójához csatlakoztassa, mivel a szolgáltatás-rendszergazdája azt hirdeti az egyes irodákban lévő felhasználóknak, hogy a közelebbi Azure szolgáltatáshoz férjenek hozzá az optimális élmény érdekében. Sajnos ez a terv csak a keleti parti felhasználók esetében működik jól, a nyugati parti felhasználók esetében nem. A probléma oka a következő. Mindkét ExpressRoute-kapcsolatcsoporton meghirdetjük az USA keleti Azure-régiójához tartozó előtagot (23.100.0.0/16) és az USA nyugati Azure-régiójához tartozó előtagot (13.100.0.0/16) is. Ha nem tudja, hogy melyik előtag melyik régióból származik, nem tudja különböző módon kezelni őket. A WAN hálózat azt hiheti, hogy mindkét előtag közelebb van az USA keleti régiójához, mint az USA nyugati régiójához, és ezért mindkét iroda felhasználóit az USA keleti régiójában lévő ExpressRoute-kapcsolatcsoporthoz irányíthatja. Ennek eredménye, hogy sok elégedetlen felhasználó lesz a Los Angeles-i irodában.

![](./media/expressroute-optimize-routing/expressroute-case1-problem.png)

### <a name="solution-use-bgp-communities"></a>Megoldás: BGP-közösségek használata
Az útválasztás mindkét irodához való optimalizálásához ismernie kell, hogy melyik előtag származik az USA nyugati Azure-régiójából és melyik az USA keleti Azure-régiójából. Ezt az információt a [BGP-közösség értékeivel](expressroute-routing.md) kódoljuk. Egy egyedi BGP-közösségértéket rendeltünk hozzá mindegyik Azure-régióhoz, például a „12076:51004” előtagot az USA keleti régiójához, a „12076:51006” előtagot az USA nyugati régiójához. Most, hogy tudja, melyik előtag melyik Azure-régióból származik, konfigurálhatja, hogy melyik ExpressRoute-kapcsolatcsoportot részesíti előnyben. Mivel az útválasztási információ cseréje a BGP segítségével történik, a BGP helyi preferenciaértékével befolyásolhatja az útválasztást. A példában magasabb helyi preferenciaértéket rendelhet hozzá a 13.100.0.0/16 előtaghoz az USA nyugati régiójában, mint az USA keleti régiójában, és hasonlóképpen magasabb helyi preferenciaértéket rendelhet hozzá a 23.100.0.0/16 előtaghoz az USA keleti régiójában, mint az USA nyugati régiójában. Ez a konfiguráció biztosítja, hogy ha a Microsoft felé mindkét útvonal elérhető, a Los Angelesben lévő felhasználók az USA nyugati régiójában lévő ExpressRoute-kapcsolatcsoport segítségével csatlakoznak az USA nyugati Azure-régiójához, míg a New Yorkban lévő felhasználók az USA keleti régiójában lévő ExpressRoute-ot használják az USA keleti Azure-régiójához való csatlakozásra. Az útválasztás minkét oldalon optimalizálva van. 

![](./media/expressroute-optimize-routing/expressroute-case1-solution.png)

## <a name="suboptimal-routing-from-microsoft-to-customer"></a>Optimálisnál rosszabb útválasztás a Microsofttól az ügyfél felé
Itt látható egy másik példa, amelyben a Microsofttól induló kapcsolatok hosszabb útvonalat vesznek igénybe a hálózat eléréséhez. Ebben az esetben helyszíni Exchange-kiszolgálókat és az Exchange Online-t használja egy [hibrid környezetben](https://technet.microsoft.com/library/jj200581%28v=exchg.150%29.aspx). Az irodák egy WAN hálózathoz csatlakoznak. Mindkét irodában meghirdeti a helyszíni kiszolgálók előtagjait a Microsoft számára a két ExpressRoute-kapcsolatcsoporton keresztül. Az Exchange Online bizonyos esetekben, például a postaládák áttelepítésekor kapcsolatot kezdeményez a helyszíni kiszolgálókkal. Sajnos a Los Angeles-i irodához irányuló kapcsolat az USA keleti régiójában lévő ExpressRoute-kapcsolatcsoporton keresztül van átirányítva, mielőtt az áthaladna az egész kontinensen vissza a nyugati partra. A probléma oka hasonló az első esetéhez. A Microsoft hálózata információ hiányában nem tudja megállapítani, hogy melyik ügyfélelőtag van közel az USA keleti régiójához, és melyik az USA nyugati régiójához. Emiatt a rossz útvonalat választja a Los Angeles-i irodához.

![](./media/expressroute-optimize-routing/expressroute-case2-problem.png)

### <a name="solution-use-as-path-prepending"></a>Megoldás: AS PATH előtag-beillesztés
A problémára két megoldás létezik. Az első, hogy egyszerűen a Los Angeles-i iroda helyszíni előtagját (177.2.0.0/31) hirdeti meg az USA nyugati régiójában lévő ExpressRoute-kapcsolatcsoporton, és a New York-i iroda helyszíni előtagját (177.2.0.2/31) hirdeti meg az USA keleti régiójában lévő ExpressRoute-kapcsolatcsoporton. Ennek eredményeképpen a Microsoftnak csak egy útvonal áll rendelkezésére, amelyen az egyes irodákhoz csatlakozhat. Nincs félreérthetőség, és az útválasztás optimalizálva van. Ezzel a kialakítással kell kidolgoznia egy feladatátvételi stratégiát. Abban az esetben, ha a Microsoft felé az ExpressRoute-on keresztül haladó útvonal megszakad, biztosítania kell, hogy az Exchange Online továbbra is csatlakozni tudjon a helyszíni kiszolgálókhoz. 

A második megoldás az, hogy továbbra is meghirdeti mindkét előtagot mindkét ExpressRoute-kapcsolatcsoporton, és emellett egy tippet ad arról, hogy melyik előtag melyik irodához van közelebb. Mivel támogatjuk a BGP AS PATH előtag-beillesztést, konfigurálhatja az előtaghoz tartozó AS PATH előtag-beillesztést, és befolyásolhatja az útválasztást. Ebben a példában meghosszabbíthatja az USA keleti régiójában az 172.2.0.0/31 előtag AS PATH értékét, aminek hatására az USA nyugati régiójában lévő ExpressRoute-kapcsolatcsoportot fogjuk előnyben részesíteni az ehhez az előtaghoz irányuló forgalom esetében (mivel a hálózatunk úgy fogja látni, hogy az előtaghoz tartozó útvonal rövidebb a nyugati parton). Hasonlóképpen meghosszabbíthatja az USA nyugati régiójában az 172.2.0.2/31 előtag AS PATH értékét, aminek hatására az USA keleti régiójában lévő ExpressRoute-kapcsolatcsoportot fogjuk előnyben részesíteni. Az útválasztás mindkét iroda esetében optimalizálva van. Ezzel a kialakítással, ha az egyik ExpressRoute-kör megszakad, az Exchange Online továbbra is elérheti az Ön hálózatát a másik ExpressRoute-kapcsolatcsoporton és a WAN hálózaton keresztül. 

> [!IMPORTANT]
> Az AS PATH értékében lévő privát AS-számokat eltávolítjuk a Microsoft társviszony-létesítésen keresztül fogadott előtagok esetében. Az AS PATH értékéhez nyilvános AS-számokat kell hozzáfűznie a Microsoft társviszony-létesítés útválasztásának befolyásolásához.
> 
> 

![](./media/expressroute-optimize-routing/expressroute-case2-solution.png)

> [!IMPORTANT]
> Bár az itt felsorolt példák a Microsoftra és a Nyilvános társviszony-létesítésre vonatkoznak, ugyanezek a funkciók a magánhálózati társviszony-létesítéshez is támogatottak. Emellett egyetlen ExpressRoute-kapcsolatcsoporton belül működik az AS Path előtag-beillesztés is, így befolyásolva az elsődleges és a másodlagos útvonalak kiválasztását.
> 
> 




<!--HONumber=Jan17_HO4-->


