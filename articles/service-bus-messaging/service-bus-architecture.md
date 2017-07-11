---
title: "Az Azure Service Bus üzenetfeldolgozási architektúrájának áttekintése | Microsoft Docs"
description: "A cikk ismerteti az Azure Service Bus üzenet- és továbbításfeldolgozási architektúráját."
services: service-bus-messaging
documentationcenter: na
author: sethmanheim
manager: timlt
editor: 
ms.assetid: baf94c2d-0e58-4d5d-a588-767f996ccf7f
ms.service: service-bus-messaging
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 05/18/2017
ms.author: sethm
ms.translationtype: Human Translation
ms.sourcegitcommit: 8f987d079b8658d591994ce678f4a09239270181
ms.openlocfilehash: ced46c64c1c105aa987759e05ab3680bc399f9a0
ms.contentlocale: hu-hu
ms.lasthandoff: 05/18/2017


---
<a id="service-bus-architecture" class="xliff"></a>

# Service Bus-architektúra
Ez a cikk ismerteti az Azure Service Bus üzenetfeldolgozási architektúrát.

<a id="service-bus-scale-units" class="xliff"></a>

## A Service Bus skálázási egységei
A Service Bus rendszerezése *skálázási egységek* szerint történik. A skálázási egység egy telepítési egység, amely a szolgáltatás futtatásához szükséges összes összetevőt tartalmazza. Minden régió telepít egy vagy több Service Bus skálázási egységet.

A skálázási egységre egy Service Bus-névtér lesz leképezve. A skálázási egység kezeli a Service Bus-entitások minden típusát: a továbbítókat és a közvetítőalapú üzenettovábbítási entitásokat (üzenetsorok, témakörök, előfizetések). A Service Bus skálázási egység az alábbi összetevőkből áll:

* **Átjárócsomópontok készlete.** Az átjárócsomópontok hitelesítik a bejövő kérelmeket és kezelik a továbbítási kérelmeket. Minden átjáró nyilvános IP-címmel rendelkezik.
* **Üzenetközvetítő csomópontok készlete.** Az üzenetközvetítő csomópontok az üzenetküldési entitásokkal kapcsolatos kérelmeket dolgozzák fel.
* **Egy átjárótároló.** Az átjárótároló a skálázási egységben definiált összes entitás adatait tartalmazza. Az átjáró tároló az SQL Azure-adatbázisban jön létre.
* **Több üzenetküldési tároló.** Az üzenetküldési tárolók a skálázási egységben definiált összes üzenetsor, témakör és előfizetés üzeneteit tartalmazzák. Emellett az előfizetések összes adata is megtalálható bennük. Ha a [particionált üzenetküldési entitások](service-bus-partitioning.md) engedélyezve vannak, az üzenetsor vagy témakör egyetlen üzenetküldési tárolóra lesz leképezve. Az előfizetések ugyanabban az üzenetküldési tárolóban vannak, mint a szülő témakörük. A Service Bus [Prémium szintű üzenetkezelés](service-bus-premium-messaging.md) kivételével az üzenetküldési tárolók az SQL Azure-adatbázisokban vannak.

<a id="containers" class="xliff"></a>

## Tárolók
Minden üzenetküldési entitás egy adott tárolóhoz van társítva. A tároló egy logikai szerkezet, amely pontosan egy üzenetküldési tárolót használ a tároló szempontjából releváns összes adat tárolására. Az egyes tárolók egy üzenetközvetítő csomóponthoz vannak társítva. Általában több tároló van, mint üzenetközvetítő csomópont. Ezért az egyes üzenetközvetítő csomópontok több tárolót is betöltenek. A tárolók elosztása az üzenetközvetítő csomópontok között úgy történik, hogy a csomópontok terhelése egyenlő legyen. Ha a terhelési mintázatok megváltoznak (pl. az egyik tároló terhelése túlzottan megnő), vagy ha egy üzenetküldési csomópont átmenetileg nem érhető el, akkor a rendszer újra szétosztja a tárolókat az üzenetközvetítő csomópontok között.

<a id="processing-of-incoming-messaging-requests" class="xliff"></a>

## Bejövő üzenetküldési kérelmek feldolgozása
Amikor egy ügyfél kérelmet küld a Service Busnak, az Azure Load Balancer továbbítja azt valamelyik átjáró csomópontnak. Az átjárócsomópont engedélyezi a kérelmet. Ha a kérelem egy üzenetküldési entitásra vonatkozik (üzenetsor, témakör, előfizetés), akkor az átjáró kikeresi az entitást az átjáró tárolójából, és meghatározza, hogy az entitás melyik üzenetküldési tárolóban található. Ezután megkeresi, hogy melyik üzenetközvetítő csomópont szolgálja ki a tárolót, és elküldi a kérelmet ennek az üzenetközvetítő csomópontnak. Az üzenetközvetítő csomópont feldolgozza a kérelmet, és frissíti az entitás állapotát a tárolóban. Az üzenetközvetítő csomópont ezután visszaküldi a választ az átjárócsomópontnak, az pedig visszaküld egy megfelelő választ az eredeti kérelmet kibocsátó ügyfélnek.

![Bejövő üzenetküldési kérelmek feldolgozása](./media/service-bus-architecture/ic690644.png)

<a id="processing-of-incoming-relay-requests" class="xliff"></a>

## Bejövő továbbítási kérelmek feldolgozása
Amikor egy ügyfél kérelmet küld az [Azure Relay](/azure/service-bus-relay/) szolgáltatásnak, az Azure Load Balancer továbbítja azt valamelyik átjárócsomópontnak. Ha kérelem figyelési kérelem, az átjárócsomópont létrehoz egy új továbbítót. Ha kérelem egy adott továbbítóhoz való kapcsolódásra irányul, az átjárócsomópont továbbítja a kapcsolódási kérelmet annak az átjárócsomópontnak, amely a továbbító tulajdonosa. A továbbítót birtokló átjárócsomópont küld egy szinkronizálási kérelmet a figyelő ügyfélnek, amelyben arra kéri a figyelőt, hogy hozzon létre egy ideiglenes csatornát ahhoz az átjárócsomóponthoz, amely a kapcsolódási kérelmet kapta.

Ha a továbbítási kapcsolat létrejött, az ügyfelek üzeneteket válthatnak a szinkronizálásra használt átjárócsomóponton keresztül.

![Bejövő WCF Relay továbbítási kérelmek feldolgozása](./media/service-bus-architecture/ic690645.png)

<a id="next-steps" class="xliff"></a>

## Következő lépések
Most, hogy elolvasta a Service Bus architektúrájának áttekintését, további információkért kövesse az alábbi hivatkozásokat:

* [Service Bus messaging overview](service-bus-messaging-overview.md) (A Service Bus üzenetkezelésének áttekintése)
* [Az Azure Relay áttekintése](../service-bus-relay/relay-what-is-it.md)
* [A Service Bus alapjai](service-bus-fundamentals-hybrid-solutions.md)
* [Üzenetsor-kezelési megoldás a Service Bus által kezelt üzenetsorok használatával](service-bus-dotnet-multi-tier-app-using-service-bus-queues.md)



