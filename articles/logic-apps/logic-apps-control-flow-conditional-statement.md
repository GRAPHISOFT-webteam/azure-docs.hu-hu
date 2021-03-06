---
title: "Feltételes utasítások - feltétel – Azure Logic Apps Futtatás lépéseket |} Microsoft Docs"
description: "Futtassa a Logic Apps alkalmazást csak egy feltétel teljesítése után szükséges lépések. Hozzon létre a megadott feltételek alapján munkafolyamatokat futtató döntési fák."
services: logic-apps
keywords: "feltételes utasítások, döntési fák"
documentationcenter: 
author: ecfan
manager: anneta
editor: 
ms.assetid: 
ms.service: logic-apps
ms.workload: logic-apps
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/05/2018
ms.author: estfan; LADocs
ms.openlocfilehash: 486c1053f42ed3becc2c4b60accc993db7f24baa
ms.sourcegitcommit: 0b02e180f02ca3acbfb2f91ca3e36989df0f2d9c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/05/2018
---
# <a name="conditional-statements-run-steps-based-on-a-condition-in-logic-apps"></a>Feltételes utasítások: futtassa a logic apps egy feltétel alapján

Csak a megadott feltétel sikeres után hajtsa végre a lépéseket, használja a *feltételes utasítás*. Ez a struktúra összehasonlítja az adatokat a munkafolyamat jellemző vagy mezők ellen. Alapján futhat-e az adatok megfelel-e a feltétel más lépéseket is definiálhatók. Feltételek belül egymással ágyazhatja be.

Tegyük fel például, hogy túl sok e-mailt küld, amikor új elemek jelennek meg a webhely RSS-hírcsatorna logikai alkalmazás. Az e-mail üzenetek küldéséhez, csak akkor, ha az új elem egy adott karakterláncot tartalmaz feltételes utasítás adhat hozzá. 

> [!TIP]
> Különböző megadott értékek alapján más lépéseket futtatásához használja a [ *utasítás kapcsoló* ](../logic-apps/logic-apps-control-flow-switch-statement.md) helyette.

## <a name="prerequisites"></a>Előfeltételek

* Azure-előfizetés. Ha még nincs előfizetése, [regisztráljon egy ingyenes Azure-fiókra](https://azure.microsoft.com/free/).

* Alapszintű ismerete [logic Apps alkalmazások létrehozása](../logic-apps/quickstart-create-first-logic-app-workflow.md)

* Kövesse az ebben a cikkben példa [a minta logikai alkalmazás létrehozása](../logic-apps/quickstart-create-first-logic-app-workflow.md) Outlook.com vagy az Office 365 Outlook-fiókkal.

## <a name="add-a-condition"></a>Feltétel hozzáadása

1. Az a <a href="https://portal.azure.com" target="_blank">Azure-portálon</a>, nyissa meg a Logic Apps alkalmazást a Logic App tervezőben.

2. Adja hozzá a feltételt, amelyet a helyen. 

   Lépések között feltétel hozzáadásához húzza az egérmutatót a nyíl ahová adja hozzá a feltételt. Válassza ki a **pluszjel** (**+**), amely akkor jelenik meg, majd válassza a **feltétel hozzáadása**. Példa:

   ![Lépések között feltétel hozzáadása](./media/logic-apps-control-flow-conditional-statement/add-condition.png)

   Ha hozzá szeretne adni egy feltétel, a logikai alkalmazás alján a munkafolyamat végén válassza **+ új lépés** > **feltétel hozzáadása**.

3. A **feltétel**, a feltétel létrehozása. 

   1. A bal oldali panelen adja meg az adatok vagy az összehasonlítani kívánt mező.

      Az a **dinamikus tartalom hozzáadása az** listában kiválaszthatja a Logic Apps alkalmazást mezők meglévő.

   2. A középső listában válassza ki a végrehajtandó műveletet. 
   3. A megfelelő mezőben adja meg a feltételként egy érték vagy mező.

   Példa:

   ![Alapszintű módban feltétel szerkesztése](./media/logic-apps-control-flow-conditional-statement/edit-condition-basic-mode.png)

   A teljes feltétel a következő:

   ![Teljes feltétel](./media/logic-apps-control-flow-conditional-statement/edit-condition-basic-mode-2.png)

   > [!TIP]
   > Speciális feltétel létrehozása vagy kifejezések használata, válassza a **speciális módban szerkesztése**. Kifejezések által definiált is használhatja a [Munkafolyamatdefiníciós nyelve](../logic-apps/logic-apps-workflow-definition-language.md).
   > 
   > Példa:
   >
   > ![A kód feltétel szerkesztése](./media/logic-apps-control-flow-conditional-statement/edit-condition-advanced-mode.png)

5. A **Ha Igen** és **IF nem**, vegye fel a feltétel teljesülését vizsgálja alapján végrehajtásához szükséges lépéseket. Példa:

   ![Igen, és nincsenek elérési utak feltételei](./media/logic-apps-control-flow-conditional-statement/condition-yes-no-path.png)

   > [!TIP]
   > A meglévő műveletek áthúzhatja a **Ha Igen** és **IF nem** elérési utak.

6. Mentse a logikai alkalmazást.

Most már a logic app csak küld mail az RSS-hírcsatorna új elemeinek felel meg a feltételnek.

## <a name="json-definition"></a>JSON-definícióból

Most, hogy a létrehozott logikai alkalmazás egy feltételes utasítás használatával, a magas szintű kód megadása a feltételes utasítás mögött vizsgáljuk meg.

``` json
"actions": {
  "myConditionName": {
    "type": "If",
    "expression": "@contains(triggerBody()?['summary'], 'Microsoft')",
    "actions": {
      "Send_an_email": {
        "inputs": { },
        "runAfter": {}
      }
    },
    "runAfter": {}
  }
},
```

## <a name="get-support"></a>Támogatás kérése

* A kérdéseivel látogasson el az [Azure Logic Apps fórumára](https://social.msdn.microsoft.com/Forums/en-US/home?forum=azurelogicapps).
* Küldje el, vagy szavazna funkciók és javaslatok, látogasson el a [Azure Logic Apps felhasználói visszajelzési webhelyet](http://aka.ms/logicapps-wish).

## <a name="next-steps"></a>További lépések

* [Futtatás (kapcsoló utasítások) eltérő értékek alapján](../logic-apps/logic-apps-control-flow-switch-statement.md)
* [Futtassa, és ismételje meg a (hurkok)](../logic-apps/logic-apps-control-flow-loops.md)
* [Futtatás vagy egyesítési párhuzamos lépéseket (ágak)](../logic-apps/logic-apps-control-flow-branches.md)
* [Futtassa a csoportosított (hatókör) állapota alapján](../logic-apps/logic-apps-control-flow-run-steps-group-scopes.md)