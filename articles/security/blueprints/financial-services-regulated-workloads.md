---
title: "Azure biztonsági és megfelelőségi tervezetének - FFIEC pénzügyi szabályozott munkaterhelések"
description: "Azure biztonsági és megfelelőségi tervezetének - FFIEC pénzügyi szabályozott munkaterhelések"
services: security
documentationcenter: na
author: simorjay
manager: mbaldwin
editor: tomsh
ms.assetid: 17794288-9074-44b5-acc8-1dacceb3f56c
ms.service: security
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 02/09/2018
ms.author: frasim
ms.openlocfilehash: a1167f56f595f905c6338868806351345c06b91a
ms.sourcegitcommit: b32d6948033e7f85e3362e13347a664c0aaa04c1
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/13/2018
---
# <a name="azure-security-and-compliance-blueprint---ffiec-financial-services-regulated-workloads"></a>Azure biztonsági és megfelelőségi tervezetének - FFIEC pénzügyi szabályozott munkaterhelések

## <a name="overview"></a>Áttekintés

Az Azure biztonsági és megfelelőségi tervezetének - FFIEC pénzügyi szolgáltatások Regulated munkaterhelések segít a biztonságos és a megfelelő platform telepítése egy platformszolgáltatási webalkalmazásként való kezelése a felhőben található bizalmas adatok kezelésére terveztek. Szerkezeti terve automatizált parancsfájlokat és útmutatást, amely egy egyszerű referencia-architektúrában és, amely egyszerűbbé teszi a Microsoft Azure-megoldások bevezetését megjelenítve áll. Ez tervezetének mutatja be egy végpont megoldás a terheket és a felhőben történő telepítésének csökkentésére keresést szervezetek igényeinek.

Ez tervezetének beállítása szerint a American Intézet a urakat például - SOC 1, SOC 2, a fizetési kártya Industry Data Security Standards szabvány Tanács DSS 3.2-es és a FFIEC szigorú kompatibilis szabványok követelményeinek teljesítéséhez célja a gyűjtemény, tárolási és a bizalmas pénzügyi adatok lekérését. A megfelelő kezelését az ilyen adatok biztonságos, megfelelő, többrétegű környezetben pénzügyi adatokat kezelő megoldás üzembe helyezésével mutatja be. A megoldás egy-végpontok PaaS Azure-alapú megoldás van telepítve. 

Szerkezeti terve kiszolgálására alapjaként létrehozása és kezelése a felhőben található védett adatok követelmények megértése érdekében ügyfelek számára készült. A megoldás nem használható, éles környezetben-van, de a megértése, tervezése és telepítése az Azure-szolgáltatások; biztonságos és megfelelő módon használja a Microsoft Azure ügyfelek kiindulópontként tervezték.

Egy akkreditált auditor igazolnia kell a tervezetének alapján üzemi ügyfél megoldások. Megoldások a részletekről az egyes ügyfelek általi megvalósítása és geográfiai függően eltérhetnek.

Nézze meg ezt a gyors áttekintést a megoldás működéséről, [videó](https://aka.ms/fsiblueprintvideo) ismerteti és bemutatja a központi telepítés.

## <a name="solution-components"></a>Megoldás-összetevők

Az architektúra a következő összetevőkből áll, és az Azure PCI DSS megfelelőségi megoldásban való központi telepítés szolgáltatásait használja.

- **Architekturális diagramja**. Az ábrán látható a referencia-architektúrában, a Contoso webes tároló megoldásban.
- **A központi telepítési sablonok**. Ebben a felállásban [Azure Resource Manager-sablonok](/azure/azure-resource-manager/resource-group-overview#template-deployment) automatikusan központi telepítésével a architektúra összetevői a Microsoft Azure konfigurációs paraméterek megadásával a telepítés során.
- **Automatikus központi telepítési parancsfájlok**. Ezek a parancsfájlok segítségével a végpont megoldás üzembe helyezéséhez. A parancsfájlok foglalják magukban:
    - A házirendmodul-telepítésének és [globális rendszergazda](/azure/active-directory/active-directory-assign-admin-roles-azure-portal) telepítési parancsfájl használatával telepítse, és győződjön meg arról, hogy a szükséges PowerShell-modulok és a globális rendszergazdai szerepkörök helyesen vannak konfigurálva. 
    - Telepítés PowerShell-parancsfájl használata a végpont megoldás, .zip fájlt és egy előre elkészített bemutató webalkalmazás tartalmazó .bacpac fájlba keresztül megadott [SQL adatbázis minta](https://github.com/Microsoft/azure-sql-security-sample). a tartalom. Ez a megoldás forráskódját készen áll a felülvizsgálatra [fizetés feldolgozása tervezetének kód tárház][code-repo]. 

## <a name="architectural-diagram"></a>Architekturális diagramja

![](images/pci-architectural-diagram.png)

## <a name="user-scenario"></a>A felhasználói forgatókönyv

Szerkezeti terve az alábbi címek az alábbi használati eset.

> Ez a forgatókönyv azt mutatja be, hogyan egy fiktív webes tároló áthelyezése egy PaaS bizalmas adatokat a felhőalapú Azure-alapú megoldás. A megoldást kezelési és alapszintű felhasználói adatok és a kijelölt bizalmas adatok gyűjteménye mutatja be. Ez a munkahelyi alapszik, az Azure biztonsági és megfelelőségi tervezetének - környezetek PCI DSS-kompatibilis fizetés feldolgozásakor. További információ a munkahelyi kibővítve a ["Tekintse át és útmutatást a megvalósítás"](https://aka.ms/pciblueprintprocessingoverview) papír biztosít a PCI DSS-kompatibilis környezetben áttekintése.

### <a name="use-case"></a>Használati eset
Egy kis webes tároló nevű *Contoso webes tároló* áthelyezheti a felhő tájékoztatást tartalmazó pénzügyi adatok. 

A rendszergazda a Contoso webes tároló olyan megoldás, amely gyorsan telepíthető a célok eléréséhez keres. A-a-koncepció igazolása (POC) azt fogja használni, és beszéljék meg, hogyan Azure használható összegyűjtésére, tárolására és szigorúbb megfelelőségi követelményeknek megfelelően pénzügyi adatokat lekérni az érdekelt felekkel.

> Fogja végző megfelelő biztonsági és megfelelőségi értékelést a használják a Koncepció architektúrával beépített követelmények változhatnak megoldások a megvalósítás vagy földrajzi hely alapján. 

### <a name="elements-of-the-foundational-architecture"></a>A legalapvetőbb architektúra elemei

A legalapvetőbb architektúra a következő fiktív elemekkel célja:

Tartomány hely `contosowebstore.com`

Felhasználói szerepkörök bemutatják a használati eset, és adja meg a felhasználói felület betekintést alkalmazzák.

#### <a name="role-site-and-subscription-admin"></a>Szerepkör: Hely és az előfizetés rendszergazdai

|Elem      |Példa|
|----------|------|
|Felhasználónév: |`adminXX@contosowebstore.com`|
| Név: |`Global Admin Azure PCI Samples`|
|Felhasználó típusa:| `Subscription Administrator and Azure Active Directory Global Administrator`|

- A rendszergazdai fiók nem olvasható be a pénzügyi információ látható. Minden műveletek bekerülnek a naplóba.
- A rendszergazdai fiók nem kezelése, és jelentkezzen be az SQL-adatbázis.
- A rendszergazdai fiókot az Active Directory és az előfizetések kezelheti.

#### <a name="role-sql-administrator"></a>Szerepkör: SQL-rendszergazdai

|Elem      |Példa|
|----------|------|
|Felhasználónév: |`sqlAdmin@contosowebstore.com`|
| Név: |`SQLADAdministrator PCI Samples`|
| Utónév: |`SQL AD Administrator`|
|Vezetéknév: |`PCI Samples`|
|Felhasználó típusa:| `Administrator`|

- A sqladmin fiók nem szűretlen pénzügyi információk megtekintése. Minden műveletek bekerülnek a naplóba.
- A sqladmin fiók SQL-adatbázis segítségével kezelhető.

#### <a name="role-clerk"></a>Szerepkör: adminisztrátor

|Elem      |Példa|
|----------|------|
|Felhasználónév:| `receptionist_EdnaB@contosowebstore.com`|
| Név: |`Edna Benson`|
| Utónév:| `Edna`|
|Vezetéknév:| `Benson`|
| Felhasználó típusa: |`Member`|

Edna Benson a recepciós és üzleti kezelő. Győződjön meg arról, hogy ügyféladatok pontos és számlázási befejeződött ő feladata. Edna a Contoso webes tároló bemutató webhelyet minden interakció a bejelentkezett felhasználó. Edna legyen jogosultsága a következő: 

- Edna hozhat létre, és olvassa el a felhasználói adatokat.
- Edna módosíthatja a felhasználói adatokat.
- Edna felülírhatnak-e pénzügyi adatokat.
- Edna fiók nem szűretlen pénzügyi információk megtekintése.



### <a name="contoso-webstore---estimated-pricing"></a>Contoso webes tároló - becsült díjszabása

A legalapvetőbb architektúra és példa-webalkalmazást egy havi díj struktúra és figyelembe kell venni a megoldás osztályozás óránként használati költségekkel rendelkeznek. Ezek a költségek használatával megbecsülhető a [Azure költségszámítás Számológép](https://azure.microsoft.com/pricing/calculator/). Től szeptember 2017, ez a megoldás becsült havi költsége van ~ $2500 Ez magában foglalja a $1000/mo használati költség ASE 2-es verzió. Ezek a költségek a használat mennyisége alapján változhatnak, és van változhatnak. Az ügyfél a becsült havi költségek kiszámításához időpontban a központi telepítés pontosabb becslést háruló. 

Ebben a megoldásban használt Azure-szolgáltatásokat. Az üzembe helyezési architektúrája részleteit találhatók a [üzembe helyezési architektúrája](#deployment-architecture) szakasz.

>- Application Gateway
>- Azure Active Directory
>- App Service Environment v2
>- OMS szolgáltatáshoz
>- Azure Key Vault
>- Network Security Groups (Hálózati biztonsági csoportok)
>- Azure SQL DB
>- Azure Load Balancer
>- Application Insights
>- Azure Security Center
>- Azure Web App
>- Azure Automation
>- Azure Automation-forgatókönyv
>- Azure DNS
>- Azure Virtual Network
>- Az Azure virtuális gépek szolgáltatás
>- Azure-erőforráscsoportot és házirendek
>- Azure Blob Storage
>- Az Azure Active Directory szerepköralapú hozzáférés-vezérlés (RBAC)

## <a name="deployment-architecture"></a>Üzembe helyezési architektúrája

Az alábbi szakasz részletesen fejlesztése és megvalósítása elemek.

### <a name="network-segmentation-and-security"></a>Hálózati szegmentálást és biztonság

![](images/pci-tiers-diagram.png)

#### <a name="application-gateway"></a>Application Gateway

A legalapvetőbb architektúra csökkenti a biztonsági rések Alkalmazásátjáró használata webalkalmazási tűzfal (waf-ot), és az engedélyezett OWASP szabálykészletben kockázatát. További funkciók a következők:

- [End-to-End-SSL](/azure/application-gateway/application-gateway-end-to-end-ssl-powershell)
- [SSL-kiszervezés](/azure/application-gateway/application-gateway-ssl-portal) engedélyezve
- [A TLS 1.0 és 1.1](/azure/application-gateway/application-gateway-end-to-end-ssl-powershell) le van tiltva
- [Webalkalmazási tűzfal](/azure/application-gateway/application-gateway-webapplicationfirewall-overview) (WAF mód)
- [Megelőző módja](/azure/application-gateway/application-gateway-web-application-firewall-portal) rendelkező OWASP 3.0 szabálykészletben
- [Diagnosztikai naplózás](/azure/application-gateway/application-gateway-diagnostics) engedélyezve
- [Egyéni állapot-mintavételi csomagjai](/azure/application-gateway/application-gateway-create-gateway-portal)
- [Az Azure Security Center](https://azure.microsoft.com/services/security-center) és [Azure Advisor](/azure/advisor/advisor-security-recommendations), amelyek biztosítják, hogy további védelmi és értesítések. Az Azure Security Center megbízhatóságibesorolás-kezelési rendszert is tartalmaz.

#### <a name="virtual-network"></a>Virtuális hálózat

A legalapvetőbb architektúra egy címterében 10.0.0.0/16 titkos virtuális hálózat határozza meg.

#### <a name="network-security-groups"></a>Network security groups (Hálózati biztonsági csoportok)

A hálózati réteg mindegyikén dedikált hálózati biztonsági csoport (NSG):

- A tűzfal és az alkalmazás átjáró WAF DMZ hálózati biztonsági csoport
- Egy NSG-t felügyeleti jumpbox (megerősített gazdagép)
- Egy NSG-t az app service Environment-környezet

Az NSG-k, amelyek mindegyikének adott portok és protokollok a biztonságos és a megfelelő műveletet, a megoldás számára. 

Ezenkívül a következő konfigurációk engedélyezve vannak az egyes NSG:

- Engedélyezett [diagnosztikai naplók és események](/azure/virtual-network/virtual-network-nsg-manage-log) storage-fiókban tárolt 
- Az OMS szolgáltatáshoz kapcsolódó a [NSG-t a diagnosztika](https://github.com/krnese/AzureDeploy/blob/master/AzureMgmt/AzureMonitor/nsgWithDiagnostics.json)

 
#### <a name="subnets"></a>Alhálózatok
 Ellenőrizze, hogy az egyes alhálózatokon társítva a megfelelő NSG-e.

#### <a name="custom-domain-ssl-certificates"></a>Custom domain SSL certificates
 HTTPS-forgalom engedélyezve van, az egyéni tartomány SSL-tanúsítványt használ.

### <a name="data-at-rest"></a>Inaktív adat

Az architektúra inaktív adatok titkosítását, adatbázis-naplózás és egyéb intézkedések segítségével védelmet nyújt.

#### <a name="azure-storage"></a>Azure Storage

A titkosított adatokat nyugalmi követelményeinek megfelelően, minden [Azure Storage](https://azure.microsoft.com/services/storage/) használ [Storage szolgáltatás titkosítási](/azure/storage/storage-service-encryption).

#### <a name="azure-sql-database"></a>Azure SQL Database

Az Azure SQL Database-példányt használja a következő adatbázis biztonsági intézkedéseket:

- [AD-alapú hitelesítés és engedélyezés](/azure/sql-database/sql-database-aad-authentication)
- [SQL adatbázis-naplózás](/azure/sql-database/sql-database-auditing-get-started)
- [Átlátható adattitkosítás](/sql/relational-databases/security/encryption/transparent-data-encryption-azure-sql)
- [Tűzfal-szabályok](/azure/sql-database/sql-database-firewall-configure), így ASE feldolgozókészletek és az ügyfél IP-kezelése
- [SQL fenyegetések észlelése](/azure/sql-database/sql-database-threat-detection-get-started)
- [Mindig titkosított oszlopokat](/azure/sql-database/sql-database-always-encrypted-azure-key-vault)
- [SQL-adatbázis dinamikus adatmaszkolási](/azure/sql-database/sql-database-dynamic-data-masking-get-started), a telepítés utáni PowerShell-parancsfájl használatával

### <a name="logging-and-auditing"></a>Naplózás és naplózás

[Az Operations Management Suite (OMS)](/azure/operations-management-suite/) is adja meg a Contoso webes tároló az összes rendszer és a felhasználói tevékenység kiterjedt naplózás, közé tartoznak a pénzügyi adatok naplózását. Módosítások tekintse át, és pontossága ellenőrizni. 

- **Tevékenységi naplóit.**  [Tevékenységi naplóit](/azure/monitoring-and-diagnostics/monitoring-overview-activity-logs) Észreveheti az olyan erőforrást az előfizetésében a végrehajtott műveletek.
- **Diagnosztikai naplók.**  [Diagnosztikai naplók](/azure/monitoring-and-diagnostics/monitoring-overview-of-diagnostic-logs) minden erőforrás által kibocsátott összes naplófájlt. Ezek a naplók a Windows rendszer-eseménynaplói, az Azure Blob storage-naplók, a táblák és a várólista naplók tartalmazzák.
- **Tűzfal naplókat.**  Az Alkalmazásátjáró teljes diagnosztikai biztosít, és hozzáférési naplókat. Az Application Gateway-erőforrásokra, amelyekre engedélyezve WAF érhetők el naplók tűzfal.
- **Napló archiválása.**  Minden diagnosztikai naplók írni egy központosított és titkosított Azure Storage-fiókjához megadott megőrzési időtartam (2 nap) archiválási vannak konfigurálva. Naplók majd kezelését, tárolását és dashboarding Azure Naplóelemzés csatlakozik. [Naplófájl Analytics](https://azure.microsoft.com/services/log-analytics) egy OMS-szolgáltatás, amely összegyűjti és elemzi a felhőben lévő erőforrások által létrehozott adatok segítségével és a helyszíni környezetben.

### <a name="encryption-and-secrets-management"></a>Titkosítás és a titkos kulcsok kezelése

A Contoso webes tároló titkosítja az összes bizalmas adatokat, és az Azure Key Vault segítségével kulcsok kezelése, és megelőzheti a CHD lekérése.

- [Az Azure Key Vault](https://azure.microsoft.com/services/key-vault/) segítségével megakadályozhatja a felhőalapú alkalmazások és szolgáltatások által használt titkosítási kulcsok és titkos. 
- [Erőforráscsoportoknál](/sql/relational-databases/security/encryption/transparent-data-encryption) minden pénzügyi ügyféladatok titkosítására szolgál.
- Adatok tárolása lemez használatával [Azure Disk Encryption](/azure/security/azure-security-disk-encryption) és a BitLocker.

### <a name="identity-management"></a>Identitáskezelés

A következő technológiákat identitás biztosítása a felügyeleti képességek az Azure környezetben.

- [Azure Active Directory (Azure AD)](https://azure.microsoft.com/services/active-directory/) a Microsoft több-bérlős felhőalapú címtár- és identitáskezelési szolgáltatás. Minden felhasználó a megoldáshoz az Azure Active Directoryban, beleértve az SQL-adatbázist elérő felhasználók jöttek létre.
- Az alkalmazás-hitelesítés az Azure AD használatával történik. További információkért lásd: [alkalmazások integrálása az Azure Active Directory](/azure/active-directory/develop/active-directory-integrating-applications). Emellett az adatbázis oszlopok titkosítását is az Azure AD segítségével hitelesíti az alkalmazás az Azure SQL Database. További információkért lásd: [mindig titkosítja: bizalmas adatokat az SQL-adatbázis védelme](/azure/sql-database/sql-database-always-encrypted-azure-key-vault). 
- [Az Azure Active Directory Identity Protection](/azure/active-directory/active-directory-identityprotection) észleli a potenciális biztonsági réseket, amely hatással lehet a szervezet identitásait, konfigurálja az automatikus válaszokat ad a szervezet identitásait kapcsolódó észlelt gyanús tevékenységek és gyanús incidensek megvizsgálja, és végrehajtja a megfelelő művelettel hárítsa el őket.
- [Azure szerepköralapú hozzáférés-vezérlés (RBAC)](/azure/active-directory/role-based-access-control-configure) lehetővé teszi, hogy pontosan célzott hozzáférés-kezelés az Azure-bA. Előfizetés hozzáférés korlátozódik előfizetés-rendszergazdaként, és az Azure Key Vault hozzáférés korlátozott minden felhasználó számára.

Az Azure SQL Database biztonsági szolgáltatásaival kapcsolatos további tudnivalókért tekintse meg a [Contoso klinikán bemutató alkalmazás](https://github.com/Microsoft/azure-sql-security-sample) minta.
   
### <a name="web-and-compute-resources"></a>Web- és számítási erőforrásokat

#### <a name="app-service-environment"></a>App Service-környezet

[Az Azure App Service](/azure/app-service/) egy felügyelt szolgáltatás webes alkalmazások telepítéséhez. A Contoso webes tároló alkalmazás van telepítve egy [App Service Web App](/azure/app-service-web/app-service-web-overview).

[Az Azure App Service Environment-környezet (ASE v2)](/azure/app-service/app-service-environment/intro) egy App Service szolgáltatás egy teljesen elkülönített és dedikált környezetben történő biztonságos futtatására, nagy méretekben App Service-alkalmazásokhoz. egy prémium szintű PCI DSS-kompatibilitás engedélyezése a legalapvetőbb architektúra segítségével service-csomagot is.

ASEs csak az egyetlen ügyfél-alkalmazások futtatása érdekében elkülönített, és mindig telepített virtuális hálózatba. Ügyfelek mindkét bejövő és kimenő hálózati forgalom részletes szabályozhatják, és alkalmazások képes kapcsolatot létrehozni a nagy sebességű biztonságos helyszíni vállalati erőforrások virtuális hálózatokon keresztül.

Ez az architektúra engedélyezett a következő vezérlők konfigurációk ASEs használja:

- Egy védett virtuális hálózaton belül gazdagép és a hálózati biztonsági szabályok
- Önaláírt ILB tanúsítványt a HTTPS-kommunikációra konfigurált ASE
- [Belső terheléselosztási mód](/azure/app-service-web/app-service-environment-with-internal-load-balancer) (mód 3)
- [A TLS 1.0](/azure/app-service-web/app-service-app-service-environment-custom-settings) le van tiltva
- [A TLS titkosító](/azure/app-service-web/app-service-app-service-environment-custom-settings) megváltozott
- Vezérlő [bejövő forgalom N/W portok](/azure/app-service-web/app-service-app-service-environment-control-inbound-traffic) 
- [WAF - adatok](/azure/app-service-web/app-service-app-service-environment-web-application-firewall)
- [SQL adatbázis-forgalom](/azure/app-service-web/app-service-app-service-environment-network-architecture-overview) engedélyezett


#### <a name="jumpbox-bastion-host"></a>Jumpbox (megerősített gazdagép)

Az App Service Environment-környezet van védve, és lezárja, mert szükség van egy olyan mechanizmus DevOps kiadások és módosítások szükségesek, például a web app használatával Kudu kell megfigyelniük, elképzelhető, hogy engedélyezi. A virtuális gép védett NAT terheléselosztó, amely lehetővé teszi a csatlakoztassa a virtuális Gépet egy eltérő 3389-es TCP-porton mögött. 

A virtuális gép létrehozása egy jumpbox (megerősített állomás) a következő beállításokat, mint:

-   [Kártevőirtó-bővítmény](/azure/security/azure-security-antimalware)
-   [OMS-bővítmény](/azure/virtual-machines/virtual-machines-windows-extensions-oms)
-   [Azure Diagnostics-bővítményt](/azure/virtual-machines/virtual-machines-windows-extensions-diagnostics-template)
-   [Az Azure Disk Encryption](/azure/security/azure-security-disk-encryption) Azure Key Vault használatával 
-   Egy [automatikus leállítási házirendet](https://azure.microsoft.com/blog/announcing-auto-shutdown-for-vms-using-azure-resource-manager/) használaton virtuálisgép-erőforrások fogyasztásának csökkentése érdekében

### <a name="security-and-malware-protection"></a>Biztonsági és a kártevők elleni védelem

[Az Azure Security Center](https://azure.microsoft.com/services/security-center/) központosított összes Azure-erőforrások biztonsági állapotát jeleníti meg. Egy pillanat alatt ellenőrizze, hogy a megfelelő biztonsági vezérlőket teljesül, és megfelelően konfigurálva, és gyorsan azonosíthatja a figyelmet igénylő erőforrásokat.  

[Az Azure Advisor](/azure/advisor/advisor-overview) , amelynek segítségével személyre szabott felhő tanácsadó kövesse a bevált gyakorlatokat az Azure-környezetekhez optimalizálása érdekében. Az erőforrás-konfigurációhoz és használat telemetriai adatai elemzi, és az segít fokozni a költséghatékonyság, a teljesítmény, a magas rendelkezésre állású és az Azure-erőforrások biztonsági megoldások javasolja.

[A Microsoft Antimalware](/azure/security/azure-security-antimalware) Azure felhőszolgáltatások és virtuális gépek esetén, amelyek segítségével azonosításához és eltávolításához a vírusok, kémprogramok és más, kártevő szoftverek, konfigurálható riasztást küld, ha ismert a valós idejű védelem funkció kártevő vagy nemkívánatos szoftverek próbálják telepíteni magukat Azure rendszeren.

### <a name="operations-management"></a>Operatív ügyek

#### <a name="application-insights"></a>Application Insights

Használjon [Application Insights](https://azure.microsoft.com/services/application-insights/) ahhoz, hogy a gyakorlatban használható elemzések Alkalmazáskezelés teljesítmény és azonnali analytics segítségével.

#### <a name="log-analytics"></a>Log Analytics

[Naplófájl Analytics](https://azure.microsoft.com/services/log-analytics/) az Operations Management Suite (OMS), amely összegyűjti és elemzi az adatok a felhőben lévő erőforrások által generált segít szolgáltatás és a helyszíni környezetben.

#### <a name="oms-solutions"></a>OMS-megoldások

A további OMS-megoldások legyen tekinthető és konfigurálva: 
- [Activity Log Analytics](/azure/monitoring-and-diagnostics/monitoring-overview-activity-logs)
- [Azure hálózatelemzés](/azure/log-analytics/log-analytics-azure-networking-analytics?toc=%2fazure%2foperations-management-suite%2ftoc.json)
- [Azure SQL Analytics](/azure/log-analytics/log-analytics-azure-sql)
- [Változáskövetés](/azure/log-analytics/log-analytics-change-tracking?toc=%2fazure%2foperations-management-suite%2ftoc.json)
- [Key Vault-elemzés](/azure/log-analytics/log-analytics-azure-key-vault?toc=%2fazure%2foperations-management-suite%2ftoc.json)
- [Szolgáltatástérkép](/azure/operations-management-suite/operations-management-suite-service-map)
- [Biztonság és naplózás](https://www.microsoft.com/cloud-platform/security-and-compliance)
- [Kártevőirtó](/azure/log-analytics/log-analytics-malware?toc=%2fazure%2foperations-management-suite%2ftoc.json)
- [Frissítéskezelés](/azure/operations-management-suite/oms-solution-update-management)

### <a name="security-center-integration"></a>A Security Center integration

Alapértelmezett telepítési célja, hogy a megfelelő és biztonságos konfigurációs állapotát jelző Security Center javaslatait az alapértékek biztosítása. Adatok gyűjtése az Azure Security Center engedélyezheti. További információkért lásd: [az Azure Security Center – első lépések](/azure/security-center/security-center-get-started).

## <a name="deploy-the-solution"></a>A megoldás üzembe helyezése

A megoldás telepítéséhez összetevők érhetők el a [tervezetének kód tárház][code-repo]. A központi telepítés a legalapvetőbb architektúra keresztül Microsoft PowerShell v5 több lépésre van szükség. A webhelyhez való kapcsolódás, (például contoso.com) egy egyéni tartománynevet kell megadnia. Ez adott meg a `-customHostName` váltani a 2. További információkért lásd: [vásároljon egy egyéni tartománynevet, az Azure Web Apps](/azure/app-service-web/custom-dns-web-site-buydomains-web-app). Egy egyéni tartománynevet nem sikeres telepítéséhez és futtatásához szükséges, de nem lehet kapcsolódni a webhelyhez bemutatási célokra.

A parancsfájlok tartományi felhasználók hozzáadása az Azure AD-bérlő megadott. A Microsoft javasolja létrehozása egy új Azure AD bérlői kívánja használni, mint egy tesztet.

Ha a telepítés során problémába ütközik, lásd: [– gyakori kérdések és hibaelhárítás](https://github.com/Azure/pci-paas-webapp-ase-sqldb-appgateway-keyvault-oms/blob/master/pci-faq.md).

A Microsoft ajánlja, hogy egy tisztán PowerShell üzembe helyezéséhez használható. Azt is megteheti győződjön meg arról, hogy a legújabb modulok szükséges megfelelő végrehajtását a telepítési parancsfájlokat használ. Ez a példa jelentkezik be, a jumpbox (megerősített gazdagépen), és a következő parancsok végrehajtása. Vegye figyelembe, hogy ez lehetővé teszi az egyéni tartomány parancsot.


1. Telepítse a szükséges modulokat, és megfelelően beállítani a rendszergazdai szerepköröket.
 
    ```powershell
     .\0-Setup-AdministrativeAccountAndPermission.ps1 
        -azureADDomainName contosowebstore.com
        -tenantId XXXXXXXX-XXXX-XXXX-XXXX-XXXXXXXXXXXX
        -subscriptionId XXXXXXXX-XXXX-XXXX-XXXX-XXXXXXXXXXXX
        -configureGlobalAdmin 
        -installModules
    ```
    A részletes használati útmutatásért lásd: [parancsfájl-utasításokat - telepítő rendszergazdai fiókot és engedéllyel](https://github.com/Azure/pci-paas-webapp-ase-sqldb-appgateway-keyvault-oms/blob/master/0-Setup-AdministrativeAccountAndPermission.md).
    
2. A megoldás-frissítés-management telepítéséhez. 
 
    ```powershell
    .\1-DeployAndConfigureAzureResources.ps1 
        -resourceGroupName contosowebstore
        -globalAdminUserName adminXX@contosowebstore.com 
        -globalAdminPassword **************
        -azureADDomainName contosowebstore.com 
        -subscriptionID XXXXXXXX-XXXX-XXXX-XXXX-XXXXXXXXXXXX 
        -suffix PCIcontosowebstore
        -customHostName contosowebstore.com
        -sqlTDAlertEmailAddress edna@contosowebstore.com 
        -enableSSL
        -enableADDomainPasswordPolicy 
    ```
    
    Részletes használati útmutatásért lásd: [parancsfájl-utasításokat - telepítése és konfigurálása Azure-erőforrások](https://github.com/Azure/pci-paas-webapp-ase-sqldb-appgateway-keyvault-oms/blob/master/1-DeployAndConfigureAzureResources.md).
    
3. Naplózás és figyelés OMS Szolgáltatáshoz. Amikor a megoldást már telepítették egy [a Microsoft Operations Management Suite (OMS)](/azure/operations-management-suite/operations-management-suite-overview) munkaterület megnyitása, és a megoldás tárházban sablonjainak minta segítségével bemutatják, hogyan konfigurálható a figyelési irányítópult . A minta OMS-sablonok, tekintse meg a [omsDashboards mappa](https://github.com/Azure/pci-paas-webapp-ase-sqldb-appgateway-keyvault-oms/blob/master/1-DeployAndConfigureAzureResources.md). Vegye figyelembe, hogy az adatokat az OMS-sablonok esetében megfelelően telepítendő kell gyűjteni. Ez akár is igénybe vehet egy óráig vagy tovább attól függően, hogy a hely tevékenység.
 
    Az OMS-naplózás beállításakor vegye figyelembe, beleértve az ezekhez az erőforrásokhoz:
 
    - Microsoft.Network/applicationGateways
    - Microsoft.Network/NetworkSecurityGroups
    - Microsoft.Web/serverFarms
    - Microsoft.Sql/servers/databases
    - Microsoft.Compute/virtualMachines
    - Microsoft.Web/sites
    - Microsoft.KeyVault/Vaults
    - Microsoft.Automation/automationAccounts
 

    
## <a name="threat-model"></a>Fenyegetések modellezése

Egy adatfolyam-diagram (DFD) és a Contoso webes tároló a minta fenyegetések modellezése [tervezetének fenyegetések modellezése](https://aka.ms/pciblueprintthreatmodel).

![](images/pci-threat-model.png)



## <a name="customer-responsibility-matrix"></a>Ügyfél felelősségi mátrix

Ügyfelek felelőssége másolatának megőrzése a [felelős összegzés mátrix](https://aka.ms/fsiblueprintcrm), amely ismerteti, hogy az ügyfél és a Microsoft Azure felelőssége felelőssége FFIEC követelmények.



## <a name="disclaimer-and-acknowledgments"></a>Jogi nyilatkozat és a nyugtázások

2017. szeptember

- Ez a dokumentum csak tájékoztatási célokat szolgál. A MICROSOFT ÉS AVYAN ELLENŐRIZZE NINCS SEMMILYEN KIFEJEZETT KIFEJEZETT, VÉLELMEZETT VAGY FELELŐSSÉGET A JELEN DOKUMENTUMBAN SZEREPLŐ INFORMÁCIÓK. Ez a dokumentum biztosított ",-van." Információk és nézetek ebben a dokumentumban, beleértve az URL-CÍMEK és más internetes webhelyet, értesítés nélkül változhatnak. Ez a dokumentum olvasásakor az ügyfelek az alkalmazást saját felelősségére használja.  
- Ez a dokumentum nem biztosít semmilyen jogot semmilyen Microsoft vagy Avyan termék vagy a megoldások található szellemi tulajdonhoz rendelkező ügyfelek.  
- Az ügyfelek azonban másolható és ez a dokumentum belső hivatkozási célokra használja.  

  > [!NOTE]
  > Bizonyos e dokumentumban szereplő ajánlásokhoz eredményezhet megnövekedett adat-, hálózati vagy számítási erőforrás-használat az Azure-ban, és előfordulhat, hogy növelje az ügyfél Azure licencek vagy előfizetések költségeit.  

- Ebben a dokumentumban a megoldás szándék szerint egy eligazodást architektúra, és nem használható mint-éles célokat szolgál. Megfelelőség elérésében megköveteli, hogy az ügyfelek vegye fel a kapcsolatot az auditor a végső ügyfél megoldás érvényesítéséhez.  
- Összes felhasználói nevét, a tranzakció rekordok és a kapcsolódó adatokat ezen a lapon nem valóságosak, a legalapvetőbb architektúra hoztak létre, és csak illusztrációs célokat szolgálnak. Nincs valós association vagy a kapcsolat célja, és nincs műve.  
- Ez a megoldás fejlesztette ki közösen Microsoft és a Avyan tanácsadás, és, akkor a [MIT licenccel](https://opensource.org/licenses/MIT).

### <a name="document-authors"></a>A dokumentum szerzője

* *Frank Simorjay (Microsoft)*  

[code-repo]: https://github.com/Azure/pci-paas-webapp-ase-sqldb-appgateway-keyvault-oms "Kód tárház"
