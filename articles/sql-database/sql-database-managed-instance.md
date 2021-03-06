---
title: "Az Azure SQL adatbázis felügyelt példány áttekintése |} Microsoft Docs"
description: "Ez a témakör ismerteti az Azure SQL adatbázis felügyelt példánya, és elmagyarázza, hogyan működik, és hogyan eltér az Azure SQL-adatbázis egy adatbázist."
services: sql-database
documentationcenter: na
author: bonova
ms.reviewer: carlrab
manager: cguyer
editor: 
ms.assetid: 
ms.service: sql-database
ms.custom: DBs & servers
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: Active
ms.date: 03/07/2018
ms.author: bonova
ms.openlocfilehash: dc3c93a1a13f3e10f9159d26411d6337c0269722
ms.sourcegitcommit: 8c3267c34fc46c681ea476fee87f5fb0bf858f9e
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/09/2018
---
# <a name="what-is-a-managed-instance-preview"></a>Mi az a felügyelt példánya (előzetes verzió)?

Az Azure SQL adatbázis által felügyelt (előzetes verzió) példány új funkciója, az Azure SQL Database, közelében SQL Server helyszíni, 100 %-os kompatibilitást biztosít natív biztosító [virtuális hálózathoz (VNet)](../virtual-network/virtual-networks-overview.md) orvosló végrehajtása közös biztonsági problémákat, és egy [üzleti modell](https://azure.microsoft.com/pricing/details/sql-database/) kedvező a helyszíni SQL Server-ügyfelek számára. Felügyelt példány lehetővé teszi, hogy a meglévő SQL Server és az ügyfelek számára növekedési az eltolás mértékét megadó a felhőbe minimális alkalmazás- és adatbázis módosításait, azok a helyszíni alkalmazások. Egy időben példány által felügyelt összes PaaS-képességet (automatikus javítás és verzió frissítések, biztonsági mentés, magas rendelkezésre állás), amely jelentősen csökkenti a felügyeleti terheket és a teljes Birtoklási megőrzi.
 
Az alábbi ábra bemutatja azokat a felügyelt példány a kulcsfontosságú szolgáltatásokat:

![a legfontosabb jellemzők](./media/sql-database-managed-instance/key-features.png)

Felügyelt példány van envisioned előnyben részesített platformként a következő esetekben: 

- Helyszíni SQL Server / szeretnék áttelepíteni egy teljes körűen felügyelt szolgáltatást minimális az alkalmazások IaaS-ügyfelek tervezése módosításokat.
- SQL-adatbázisok, akik meg szeretnék hagyatkoznia ISV-k engedélyezése az ügyfelek áttelepítése a felhőbe, és így a jelentős versenyelőnyt eléréséhez, vagy a globális piaci elérni. 

Általános rendelkezésre állás a felügyelt példány célja, hogy megközelíti a 100 %-os támadási felületét kompatibilitási legújabb verziójával a helyszíni SQL Server egy előkészített kiadás csomagban biztosításához. 

A következő táblázat a körvonal különbségek kulcsát, és envisioned használati forgatókönyvek SQL IaaS, az Azure SQL Database és a felügyelt példány között:

| | Használati eset | 
| --- | --- | 
|Felügyelt példány |Szeretnék nagyszámú alkalmazások áttelepítése a helyszíni vagy a IaaS, önálló beépített, vagy a megadott ISV ügyfelek számára a lehető alacsony áttelepítési beavatkozást, javaslatot felügyelt példány. Használja a teljesen automatikus [adatok áttelepítési szolgáltatás (DMS)](/sql/dma/dma-overview) Azure, az ügyfelek növekedési és a helyszíni SQL Server shift felügyelt példányához, amely kompatibilis a helyszíni SQL Server és a teljes elkülönítési felhasználói példányok natív VNET támogatását.  A frissítési garancia, továbbíthatja az kedvezményes díjszabás egy SQL-adatbázis felügyelt példány használatával a meglévő licencét a [Azure hibrid használja az SQL Server juttatása](../virtual-machines/windows/hybrid-use-benefit-licensing.md).  SQL adatbázis felügyelt-példány a legjobb áttelepítés célja a fokozott biztonságot és a gazdag programozhatóság felület igénylő SQL Server-példányok a felhőben. |
|Azure SQL Database |Az ügyfelek új több-bérlős SaaS-alkalmazások fejlesztéséhez vagy szándékosan átalakítása egy több-bérlős SaaS alkalmazásban, a meglévő helyszíni alkalmazásokat javaslatot rugalmas készletek. Ez a modell előnyei a következők: <br><ul><li>Licencét kínál az előfizetés (az ISV-k) a kínál az üzleti modell átalakítás</li></ul><ul><li>Egyszerű és listajele távra bérlők elszigetelésére</li></ul><ul><li>Adatbázis-központú egyszerűsített programozási modellt</li></ul><ul><li>Így anélkül, hogy elérte-e a rögzített felső határ kiterjesztése</li></ul>Az ügyfelek új alkalmazásfejlesztés SaaS több-bérlős, amelynek munkaterhelés stabillá és kiszámíthatóvá, nem önálló adatbázisok javaslatot. Ez a modell előnyei a következők:<ul><li>Adatbázis-központú egyszerűsített programozási modellt</li></ul>  <ul><li>Az egyes adatbázisok kiszámítható teljesítmény</li></ul>|
|SQL-infrastruktúra |Az ügyfelek, melynek segítségével testre szabhatja az operációs rendszer vagy az adatbázis-kiszolgáló, valamint szempontjából külső alkalmazások mellett az SQL Server (futtató ugyanabból virtuális), a specifikus követelményekkel rendelkező javaslatot SQL virtuális gépek / IaaS az optimális megoldás|
|||

![elhelyezéséhez](./media/sql-database-managed-instance/positioning.png)

## <a name="how-to-programmatically-identify-a-managed-instance"></a>A felügyelt példánya programozott módon azonosítása

Az alábbi táblázat néhány tulajdonságok, Transact SQL keresztül érhető el, hogy észleli, hogy működik-e az alkalmazás felügyelt példánnyal, és fontos tulajdonságainak beolvasása.

|Tulajdonság|Érték|Megjegyzés|
|---|---|---|
|`@@VERSION`|Microsoft SQL Azure (RTM) - 12.0.2000.8 2018-03-07 Copyright (C) 2018 Microsoft Corporation.|Ez az érték legyen, amelyik SQL-adatbázisban is szerepel.|
|`SERVERPROPERTY ('Edition')`|SQL Azure|Ez az érték legyen, amelyik SQL-adatbázisban is szerepel.|
|`SERVERPROPERTY('EngineEdition')`|8|Ez az érték egyedileg azonosítja a felügyelt példány.|
|`@@SERVERNAME`, `SERVERPROPERTY ('ServerName')`|Teljes példány DNS-nevét a következő formátumban:<instanceName>.<dnsPrefix>. Database.Windows.NET, ahol <instanceName> az ügyfél által megadott név közben <dnsPrefix> része automatikusan létrehozott globális DNS-név egyediségi biztosítása neve ("wcus17662feb9ce98", például)|Példa: a-felügyelt-instance.wcus17662feb9ce98.database.windows.net|

## <a name="key-features-and-capabilities-of-a-managed-instance"></a>Legfontosabb funkcióira és képességeire felügyelt-példány 

| **A PaaS előnyei** | **Az üzletmenet folytonossága** |
| --- | --- |
|Hardver megvásárlása és kezelése <br>Egyetlen felügyeleti terhet az alapul szolgáló infrastruktúra kezelése <br>Gyors kiosztás és a szolgáltatás skálázás <br>Automatikus javítás és verzió frissítése <br>Integráció más PaaS adatszolgáltatások |SLA-t 99,99 % üzemideje  <br>A beépített magas rendelkezésre állás <br>Az automatikus biztonsági mentés a védett adatok <br>Ügyfél konfigurálható biztonsági mentés megőrzési időszak (rögzített nyilvános előzetes verziójában 7 nap) <br>A felhasználó által kezdeményezett biztonsági mentések <br>Pont idő adatbázis visszaállítása funkció |
|**Biztonsági és megfelelőségi** | **Felügyeleti**|
|(Virtuális integráció, egyetlen-bérlő szolgáltatás, dedikált számítási és tárolási elkülönített környezet <br>Az átvitel során az adatok titkosítása <br>Az Azure AD-alapú hitelesítés, egyszeri bejelentkezéses támogatás <br>Megfelelőségi szabványoknak megfelelő azonos Azure SQL adatbázis <br>SQL auditing (SQL-naplózás) <br>Fenyegetések észlelése |Az Azure Resource Manager API a szolgáltatás üzembe helyezési és a méretezés automatizálásához <br>Az Azure portál funkció manuális szolgáltatáshoz kiépítés, és a méretezésről <br>Áttelepítési adatszolgáltatás 

![Egyszeri bejelentkezés](./media/sql-database-managed-instance/sso.png) 

## <a name="managed-instance-service-tier"></a>Felügyelt példány szolgáltatási rétegben

Felügyelt példány tipikus rendelkezésre állás és a közös IO késésre vonatkozó követelmény az alkalmazások tervezett egyetlen szolgáltatásréteg - általános célú - kezdetben érhető el.

Az alábbi lista ismerteti az általános célú szolgáltatásréteg főbb jellemzője: 

- A legtöbb, a tipikus teljesítménye és magas rendelkezésre ÁLLÁSÚ követelmények üzleti alkalmazások kialakítása 
- Nagy teljesítményű prémium szintű Azure storage (8 TB) 
- 100 adatbázisok / példány 

A réteg függetlenül is válassza ki a tárolási és számítási kapacitás. 

A következő ábra szemlélteti a aktív számítási és a szolgáltatási rétegben lévő redundáns csomópontok.
 
![Általános célú szolgáltatási rétegben](./media/sql-database-managed-instance/general-purpose-service-tier.png) 

A következőkben olvashat az általános célú szolgáltatásréteg a legfontosabb jellemzők:

|Szolgáltatás | Leírás|
|---|---|
| VCores * száma | 8, 16, 24|
| SQL Server-verzió / összeállítása | SQL Server legújabb (elérhető) |
| Minimális mérete | 32 GB |
| Maximális tárolómérete | 8 TB |
| Várt tárolási iops-érték | Az adatfájl (adatfájl függ) 500-7500 iops-értéket. Lásd: [prémium szintű Storage](../virtual-machines/windows/premium-storage-performance.md#premium-storage-disk-sizes) |
| Adatfájlok (sorok) az adatbázis másodpercenkénti száma | Többszörös | 
| Adatbázisonként (napló) fájlok száma | 1 | 
| Az automatikus biztonsági mentés felügyelete | Igen |
| HA | Távoli tároló alapján és [Azure Service Fabric](../service-fabric/service-fabric-overview.md) |
| Beépített példány adatbázis ellenőrzésének és metrikák | Igen |
| A szoftverfrissítések automatikus javítás | Igen |
| VNet - Azure Resource Manager telepítés | Igen |
| VNet - klasszikus telepítési modell | Nem |
| Portál támogatása | Igen|
|||

\* Egy virtuális core jelenti. a logikai Processzor érhető el, hogy a hardver generációja közül választhat. 4 logikai processzorok Intel E5-2673 v3 alapulnak. generációból (Haswell) 2,4 GHz órajelű és generációs 5 logikai processzorok Intel E5-2673 v4 alapul (Broadwell) 2.3 GHz órajelű.  

## <a name="advanced-security-and-compliance"></a>Magas szintű biztonság és megfelelőség 

### <a name="managed-instance-security-isolation"></a>Felügyelt példány biztonsági elkülönítés 

Felügyelt példány további biztonsági elkülönítés Azure felhőben más bérlők számára. Biztonsági elkülönítés tartalmazza: 

- Natív virtuális hálózati végrehajtása és a helyszíni környezetben Azure Express Route vagy VPN-átjárót, kapcsolattal 
- SQL-végpont csak egy magánhálózati IP-cím, lehetővé téve a biztonságos kapcsolat a saját Azure vagy hibrid hálózatok keresztül van közzétéve.
- Single-bérlő az alapul szolgáló dedikált infrastruktúrát (számítási, tároló)

A következő ábra bemutatja azokat a elkülönítési tervezési: 

![Magas rendelkezésre állás](./media/sql-database-managed-instance/application-deployment-topologies.png)  

### <a name="auditing-for-compliance-and-security"></a>Naplózás a megfelelőség és biztonság szolgálatában 

Felügyelt példány [naplózás](sql-database-auditing.md) nyomon követi az adatbázisok események, mind az írás őket naplózási jelentkezzen be az Azure storage-fiók. Naplózás segíthet a törvényi megfelelőség fenntartásában, ismerje meg adatbázis-tevékenység, és azok az eltérések és rendellenességek, amelyek üzleti problémát jelenthetnek, vagy a biztonság megsértésére betekintést. 

### <a name="data-encryption-in-motion"></a>Adattitkosítás menet közben 

Felügyelt példány titkosítja az adatokat titkosítás használata a Transport Layer Security mozgásérzékelési adatainak megadásával.

A transport layer security, SQL-adatbázis felügyelt példány lehetőséget biztosít útban, inaktív és lekérdezés feldolgozás során bizalmas adatok védelmének [mindig titkosítja](/sql/relational-databases/security/encryption/always-encrypted-database-engine). Az Always Encrypted az iparágban elsőként kínál páratlan adatbiztonságot a bizalmas adatok eltulajdonításával járó illetéktelen behatolások ellen. Például mindig titkosítja, a hitelkártyaszámok tárolódnak az adatbázisban titkosítottan mindig, még akkor is lekérdezés feldolgozása, visszafejtési történő használata lehetővé engedéllyel rendelkező munkatársak vagy alkalmazásokat, hogy az adatok feldolgozása során. 

### <a name="dynamic-data-masking"></a>Dinamikus adatmaszkolás 

SQL-adatbázis [dinamikus adatmaszkolási](/sql/relational-databases/security/dynamic-data-masking) korlátozza a bizalmas adatok veszélyeztetettségének által maszkolás a replikaadatok számára. Dinamikus adatmaszkolási segít a jogosulatlan hozzáférés elkerülése érdekében bizalmas adatokat azáltal, hogy meghatározza mekkora a bizalmas adatokat, hogy láthatóvá váljon a az alkalmazási rétegre gyakorolt minimális hatás mellett. Ez a szabályzatalapú biztonsági funkció elrejti a bizalmas adatokat egy kijelölt adatbázismezőkön végrehajtott lekérdezés eredményhalmazában, miközben az adatbázis adatait nem módosítja. 

### <a name="row-level-security"></a>Sorszintű biztonság 

[A sorszintű biztonsági](/sql/relational-databases/security/row-level-security) szabályozható a hozzáférést egy adatbázis tábla sorainak lehetővé teszi, hogy a felhasználó lekérdezése jellemzői alapján (például a csoport tagságát vagy végrehajtási környezet által). A sorszintű biztonság (RLS) egyszerűsíti az alkalmazás védelmének megtervezését és kódolását. Az RLS használatával korlátozásokat érvényesíthet az adatsorokhoz való hozzáférésre. Például annak érdekében, hogy munkavállalók férhessenek hozzá csak az adatok sorokat, amelyek az osztály megfelelő, vagy egy adatok hozzáférés korlátozása csak a vonatkozó adatokat. 

### <a name="threat-detection"></a>Fenyegetések észlelése 

Az Azure SQL Database [Fenyegetésészlelés](sql-database-threat-detection.md) kiegészíti a naplózást, a szolgáltatás által észlelt szokatlan és potenciálisan káros kísérletek eléréséhez, vagy a biztonsági rések elleni adatbázisok beépített biztonsági eszközintelligencia további réteget megadásával. Gyanús tevékenységeket, a potenciális biztonsági réseket, figyelmeztetést, és SQL-injektálás támadások, továbbá az adatbázis rendellenes hozzáférési mintákat. Figyelmeztetések-ból is megtekinthetők [az Azure Security Center](https://azure.microsoft.com/services/security-center/) és a gyanús tevékenység részleteinek megadása, és vizsgálja meg, és a fenyegetések mérséklésére művelet javasolja.  

### <a name="azure-active-directory-integration-and-multi-factor-authentication"></a>Azure Active Directory-integráció és többtényezős hitelesítés 

Az SQL Database az [Azure Active Directory-integráció](sql-database-aad-authentication.md) által lehetővé teszi adatbázis-felhasználók és más Microsoft-szolgáltatások identitásainak központi kezelését. Ez a funkció egyszerűsíti az engedélyek kezelését és fokozza a biztonságot. Az Azure Active Directory a [többtényezős hitelesítés](sql-database-ssms-mfa-authentication-configure.md) (MFA) támogatásával javítja az adatok és alkalmazások biztonságát, miközben támogatja az egyszeri bejelentkezést. 

### <a name="authentication"></a>Hitelesítés 
SQL-adatbázis hitelesítési módját felhasználók azok identitásának igazolásához az adatbázishoz való kapcsolódáskor hivatkozik. Az SQL Database két hitelesítési típust támogat:  

- SQL-hitelesítés, amely felhasználónevet és jelszót használja.
- Az Azure Active Directory-hitelesítéssel, amely Azure Active Directory által kezelt identitások használ, és a felügyelt és integrált tartományok használata támogatott.  

### <a name="authorization"></a>Engedélyezés

Engedélyezési milyen felhasználói teheti meg egy Azure SQL Database belül, és a felhasználói fiókja adatbázis szerepkörtagságok és objektumszintű engedélyeinek hivatkozik. Felügyelt példány azonos engedélyezési funkciókkal rendelkeznek, mint az SQL Server 2017 rendelkezik. 

## <a name="database-migration"></a>Adatbázis-áttelepítés 

Felügyelt példány célok felhasználói esetek tömeges adatbázis áttelepítéssel helyszíni vagy infrastruktúra-szolgáltatási adatbázis hitelesítés megvalósításához.  Felügyelt példányát támogatja több adatbázis áttelepítési lehetőségek: 

### <a name="data-migration-service"></a>Áttelepítési adatszolgáltatás

Az Azure-adatbázis áttelepítési szolgáltatás egy olyan teljes körűen felügyelt szolgáltatás lehetővé minimális állásidővel adatok Azure platformon több adatbázis forrásból zökkenőmentes áttelepítés.   Ez a szolgáltatás leegyszerűsíti a meglévő harmadik féltől származó és az SQL Server-adatbázisok áthelyezése az Azure-bA szükséges feladatok. Központi telepítési beállítások közé tartoznak az Azure SQL Database, a példány által felügyelt és az SQL Server Azure virtuális gép nyilvános előzetes verzió: Lásd: [hogyan telepítheti át a helyi adatbázis felügyelt példányhoz DMS használatával](https://aka.ms/migratetoMIusingDMS).  

### <a name="backup-and-restore"></a>Biztonsági mentés és visszaállítás  

Az áttelepítési módszer SQL biztonsági mentés az Azure blob storage kihasználja. Azure storage-blob tárolt biztonsági is állítható helyre közvetlenül kezelt példány. 

## <a name="sql-features-supported"></a>Támogatott SQL-szolgáltatások 

A felügyelt példány célok megközelíti a 100 %-os támadási felületét is kompatibilisek a helyszíni SQL Server szolgáltatás általános rendelkezésre állása csak szakaszaiban hamarosan képes biztosítani. A szolgáltatások és összehasonlító listáját lásd: [általános SQL-szolgáltatások](sql-database-features.md).
 
A felügyelt példány támogatja az előző verziókkal való kompatibilitás SQL 2008-adatbázishoz.  Az SQL 2005 adatbázis-kiszolgálók közvetlen áttelepítése támogatott, az áttelepített SQL 2005-adatbázisok kompatibilitási szintje nem frissítették, hogy az SQL 2008. 
 
A következő ábra bemutatja azokat a támadási felületét kompatibilitási felügyelt példányban:  

![Áttelepítés](./media/sql-database-managed-instance/migration.png) 

### <a name="key-differences-between-sql-server-on-premises-and-managed-instance"></a>Helyszíni SQL Server és a felügyelt példány közötti legfőbb különbségek 

Felügyelt példány előnyöket nem mindig naprakészen-dátumig a felhőben, ami azt jelenti, hogy egyes funkciók a helyszíni SQL Server lehet akár elavult, már nincs, vagy valamilyen alternatívával szolgálni rendelkezik.  Ha az eszközök ismeri fel, hogy egy adott szolgáltatással némileg eltérő módon működik-e, vagy hogy a szolgáltatás nem fut egy olyan környezetben, nem teljes mértékben szabályozhatja, vannak bizonyos esetekben: 

- Magas rendelkezésre állású beépített és előre konfigurálva van. Mindig a magas rendelkezésre állású szolgáltatások nem érhetők egy ugyanúgy, mint az SQL IaaS-megvalósítások 
- Az automatikus biztonsági mentés és időponthoz kötött visszaállításra. Ügyfél kezdeményezhet `copy-only` biztonsági mentések, amelyek nem zavarják a automatikus biztonsági mentési láncolatát. 
- Felügyelt példány nem engedélyezi a teljes fizikai elérési útját adja meg, így az összes megfelelő forgatókönyv támogatott eltérően kell: RESTORE DB nem támogatja a WITH MOVE, hozzon létre DB nem engedélyezi a fizikai elérési útját, TÖMEGES Beszúrás működik a Azure BLOB, csak stb. 
- Felügyelt példányát támogatja [az Azure AD-alapú hitelesítés](sql-database-aad-authentication.md) felhő alternatívájaként Windows-hitelesítést. 
- Felügyelt példány automatikusan kezeli a XTP fájlcsoport és a fájlok a memórián belüli online Tranzakciófeldolgozási objektumokat tartalmazó adatbázisok
 
### <a name="managed-instance-administration-features"></a>Felügyelt példány felügyeleti funkciók  

A felügyelt példány lehetővé teszik a rendszergazdának a legtöbb vállalati lényegre összpontosíthat. Sok rendszergazda/DBA rendszertevékenységét nem szükségesek, vagy egyszerű. Például az operációs rendszer / RDBMS telepítési és javítását, dinamikus példány átméretezése és a konfiguráció, a biztonsági mentések, a adatbázis-replikáció (rendszeradatbázisok), a magas rendelkezésre állású konfiguráció és a állapotának és teljesítményének a figyelési adatok konfigurálása adatfolyamokat.  

> [!IMPORTANT]
> Támogatott, részben támogatott és nem támogatott funkciók listáját lásd: [SQL adatbázis-szolgáltatások](sql-database-features.md). Felügyelt példányok és SQL Server T-SQL különbségeit listájáért lásd: [példány T-SQL különbségek kezelt SQL Server kiszolgálóról](sql-database-managed-instance-transact-sql-information.md)
 
## <a name="next-steps"></a>További lépések

- A szolgáltatások és összehasonlító listáját lásd: [általános SQL-szolgáltatások](sql-database-features.md).
- Az oktatóanyag, amely létrehoz egy kezelt példányt, és visszaállítja egy adatbázis biztonsági másolatból, lásd: [hozzon létre egy felügyelt példányt](sql-database-managed-instance-tutorial-portal.md).
- Egy oktatóanyag az Azure adatbázis áttelepítési szolgáltatás (DMS) áttelepítés használatával, lásd: [DMS használatával felügyelt példány áttelepítési](../dms/tutorial-sql-server-to-managed-instance.md).
