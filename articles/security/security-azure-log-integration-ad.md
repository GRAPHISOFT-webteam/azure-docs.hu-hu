---
title: "Vizsgálati naplóit Azure Active Directory integrálása az Azure napló |} Microsoft Docs"
description: "Megtudhatja, hogyan telepítse az Azure napló integrációs szolgáltatást, és integrálhatja a naplók az Azure felügyeleti naplók"
services: security
documentationcenter: na
author: Barclayn
manager: MBaldwin
editor: TomShinder
ms.assetid: 
ms.service: security
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ums.workload: na
ms.date: 02/16/2018
ms.author: barclayn
ms.custom: azlog
ms.openlocfilehash: 0f45f43a0296a7d90a68b0526f805ea50a1ce6c6
ms.sourcegitcommit: d87b039e13a5f8df1ee9d82a727e6bc04715c341
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/21/2018
---
# <a name="integrate-azure-active-directory-audit-logs"></a>Integrálása az Azure Active Directory-naplók

Az Azure Active Directory (Azure AD) naplózási események segítségével azonosíthatja a privilegizált műveletekhez, hogy megtörtént az Azure Active Directoryban. Láthatja, hogy milyen típusú eseményeket vizsgálatával nyomon követett [Azure Active Directory auditnaplójának jelentési eseményei](/active-directory/active-directory-reporting-audit-events#list-of-audit-report-events.md).


> [!NOTE]
> A cikkben ismertetett-megkezdése előtt át kell néznie a [Ismerkedés](security-azure-log-integration-get-started.md) cikk és a lépéseket van.

## <a name="steps-to-integrate-azure-active-directory-audit-logs"></a>Azure Active Directory integrálásának lépései naplók

1. Nyissa meg a parancssort, és futtassa a parancsot:

   ``cd c:\Program Files\Microsoft Azure Log Integration``

2. Futtassa ezt a parancsot: 
 
   ``azlog createazureid``

   Ez a parancs kéri az Azure bejelentkezési. A parancs majd hoz létre egy Azure Active Directory szolgáltatás egyszerű az Azure AD-bérlőkkel, amely az Azure-előfizetéseket, amelyben a bejelentkezett felhasználó nem rendszergazda, a társadminisztrátorának vagy a tulajdonos tárolni. A parancs sikertelen lesz, ha a bejelentkezett felhasználó csak a Vendég felhasználó az Azure AD-bérlő. Hitelesítés az Azure-bA az Azure AD segítségével történik. Az Azure AD identity arra, hogy az Azure-előfizetések olvasási hozzáférést egy egyszerű Azure napló integrációs szolgáltatás létrehozásakor létrejön.

3. A következő parancsot adja meg a bérlő azonosítójával. A parancs futtatásához a bérlői rendszergazda szerepkör tagjának lennie kell.

   ``Azlog.exe authorizedirectoryreader tenantId``

   Példa:

   ``AZLOG.exe authorizedirectoryreader ba2c0000-d24b-4f4e-92b1-48c4469999``

4. Ellenőrizze a következő mappák győződjön meg arról, hogy az Azure Active Directory JSON naplófájlok bennük jönnek létre:

   * **C:\Users\azlog\AzureActiveDirectoryJson**
   * **C:\Users\azlog\AzureActiveDirectoryJsonLD**

A következő videó bemutatja a cikkben szereplő lépéseket:

> [!VIDEO https://channel9.msdn.com/Blogs/Azure-Security-Videos/Azure-Log-Integration-Videos-Azure-AD-Integration/player]


> [!NOTE]
> A JSON-fájlokban szereplő információk üzembe a biztonsági információk és eseménykezelő rendszer (SIEM) kapcsolatos tudnivalókat forduljon a SIEM gyártójához.

Közösségi támogatás érhető el a [Azure napló integrációs MSDN fórumon](https://social.msdn.microsoft.com/Forums/office/home?forum=AzureLogIntegration). Ezen a fórumon lehetővé teszi, hogy a személyek Azure napló integráció közösségi egymással kérdéseket, válaszokat, tippeket és trükkök támogatásához. Ezenkívül az Azure napló integrációs csoport ezen a fórumon figyeli, és amikor azt is segít.

Is megnyithatja a [támogatási kérelem](../azure-supportability/how-to-create-azure-support-request.md). Válassza ki **napló integrációs** a szolgáltatást, amelynek támogatási kérelmet.

## <a name="next-steps"></a>További lépések
Azure napló integrációs kapcsolatos további információkért lásd:

* [A Microsoft Azure napló integráció az Azure-naplók](https://www.microsoft.com/download/details.aspx?id=53324): A letöltőközpontból weblap, amely részleteit, rendszerkövetelmények és telepítési utasításokat az Azure napló integráció.
* [Bevezetés az Azure napló integrációs](security-azure-log-integration-overview.md): Ez a cikk bemutatja a Azure napló integrációs, annak főbb funkcióit és annak működéséről.
* [Az Azure napló integrációs gyakran ismételt kérdések](security-azure-log-integration-faq.md): Ez a cikk Azure napló integrációs kapcsolatos kérdésekre ad választ.
* [Az Azure Diagnostics és az Azure új szolgáltatásai naplók](https://azure.microsoft.com/blog/new-features-for-azure-diagnostics-and-azure-audit-logs/): Ebben a blogbejegyzésben bemutatja Azure vizsgálati naplók és egyéb szolgáltatásokat, amelyek segítenek betekintést nyerhet az Azure-erőforrások működésére.
