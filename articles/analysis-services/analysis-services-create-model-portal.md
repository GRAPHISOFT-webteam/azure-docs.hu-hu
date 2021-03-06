---
title: "A táblázatos modellek létrehozása az Azure Analysis Services Web-tervezővel |} Microsoft Docs"
description: "Megtudhatja, hogyan hozhat létre egy Azure Analysis Services táblázatos modell a webalkalmazás-tervező használata Azure-portálon."
services: analysis-services
documentationcenter: 
author: minewiskan
manager: kfile
editor: 
tags: 
ms.assetid: 
ms.service: analysis-services
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: na
ms.date: 02/14/2018
ms.author: owend
ms.openlocfilehash: ff2f1dd89796a30d4e0fee0810ff45ff57f5f8bf
ms.sourcegitcommit: c765cbd9c379ed00f1e2394374efa8e1915321b9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/28/2018
---
# <a name="create-a-model-in-azure-portal"></a>A modell létrehozása Azure-portálon

Az Azure Analysis Services-webalkalmazás designer (előzetes verzió) Azure-portálon szolgáltatás biztosítja az gyorsan és egyszerűen és szerkesztése a táblázatos modellek és lekérdezi a modell adatok jobb a böngészőben. 

Ne feledje, a webes Designer **előzetes**. Új funkciók hozzáadott folyamatosan, kép, amíg a funkciók korlátozva. A speciális modell fejlesztéshez és teszteléshez érdemes használni a Visual Studio (SSDT) és az SQL Server Management Studio (SSMS).

## <a name="before-you-begin"></a>Előkészületek

- Egy, a Standard vagy fejlesztői réteg Azure Analysis Services-kiszolgálóhoz. A webalkalmazás-tervezővel létrehozott új modellek DirectQuery, csak ezek a rétegek támogatja.
- Egy Azure SQL Database, Azure SQL Data warehouse-bA vagy egy adatforrást a Power BI Desktop (.pbix) fájlt. A Power BI Desktop fájlok támogatása az Azure SQL Database, Azure SQL Data Warehouse, Oracle és Teradata adatforrások létrehozott új modellek.
- Egy SQL Server-fiókkal és jelszóval csatlakozás az Azure SQL Database vagy az Azure SQL Data Warehouse-adatforrásokhoz.

## <a name="sign-in-to-the-azure-portal"></a>Jelentkezzen be az Azure Portalra

Jelentkezzen be az [Azure Portalra](https://portal.azure.com/).

## <a name="to-create-a-new-tabular-model"></a>Egy új táblázatos modell létrehozása

1. A Server **áttekintése** > **webes Tervező**, kattintson a **nyitott**.

    ![A modell létrehozása Azure-portálon](./media/analysis-services-create-model-portal/aas-create-portal-overview-wd.png)

2. A **webes Tervező** > **modellek**, kattintson a **+ Hozzáadás**.

    ![A modell létrehozása Azure-portálon](./media/analysis-services-create-model-portal/aas-create-portal-models.png)

3. A **új modell**, írja be a modell neve, majd válassza ki egy adatforrást.

    ![Azure-portálon az új modell párbeszédpanel](./media/analysis-services-create-model-portal/aas-create-portal-new-model.png)

4. A **Connect**, adja meg a kapcsolat tulajdonságait. Felhasználónév és jelszó SQL Server fióknak kell lennie.

     ![Csatlakozás Azure-portálon párbeszédpanel](./media/analysis-services-create-model-portal/aas-create-portal-connect.png)

5. A **táblák és nézetek**, válassza ki a modell tartalmazza, és kattintson a táblákat **létrehozása**. Egy kulcspár rendelkező táblák közötti kapcsolatok jönnek létre automatikusan.

     ![Válassza ki a táblák és nézetek](./media/analysis-services-create-model-portal/aas-create-portal-tables.png)

Az új modell jelenik meg a böngészőben. Itt a következőket teheti:   

- Query modelladatok húzzon mezőket a Lekérdezéstervezőben, és vegye fel a szűrőket.
- Hozzon létre új mértékek táblában.
- Módosítsa a modell metaadatait a json-szerkesztő segítségével.
- Nyissa meg a modellt a Visual Studio (SSDT), a Power BI Desktop vagy az Excel.

![Válassza ki a táblák és nézetek](./media/analysis-services-create-model-portal/aas-create-portal-query.png)

> [!NOTE]
> Modell metaadatainak szerkesztése, illetve a böngészőben új mértékek létrehozása menti ezeket a módosításokat az Azure-ban a modellhez. Ha még dolgozik a modell SSDT, a Power BI Desktop vagy az Excel, a modell szinkronban kérheti le.


## <a name="next-steps"></a>További lépések 
[Adatbázis-szerepkörök és a felhasználók kezelése](analysis-services-database-users.md)  
[Kapcsolódás Excellel](analysis-services-connect-excel.md)  


