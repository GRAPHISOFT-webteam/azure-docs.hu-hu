---
title: "Az Azure tartalék virtuális gép példányok Windows szoftverek költségeit |} Microsoft Docs"
description: "Ismerje meg, melyik Windows szoftver mérőszámok nem része a fenntartott virtuális gép példány költségeket."
services: billing
documentationcenter: 
author: manish-shukla01
manager: manshuk
editor: 
tags: billing
ms.service: billing
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/03/2017
ms.author: manshuk
ms.openlocfilehash: a0bb559369877e1cc5333394102bfb85d3f0bb11
ms.sourcegitcommit: 059dae3d8a0e716adc95ad2296843a45745a415d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/09/2018
---
# <a name="windows-software-costs-not-included-with-reserved-instances"></a>A Windows szoftverek költségeit fenntartott példányok nem találhatók

Ha egy Azure hibrid használata juttatás nem rendelkezik a fenntartott példány virtuális gépeken, majd van szó, a a Windows szoftverek mérőszámok szerepel a következő szakaszban.

## <a name="windows-software-meters-not-included-in-reserved-instance-cost"></a>A Windows szoftverek mérőszámok nem foglalt példány költség szerepel

| Fogyasztásmérő azonosítója | A használati fájlban MeterName | Virtuális gép által használt |
| ------- | ------------------------| --- |
| e7e152ac-f29c-4cce-ad6e-026192c01ef2 | Windows-foglalás Svr kapacitásnövelés (1 mag) | B sorozat |
| cac255a2-9f0f-4c62-8bd6-f0fa449c5f76 | Windows-foglalás Svr kapacitásnövelés (2 mag) | B sorozat |
| 09756b58-3fb5-4390-976d-9ddd14f9ed18 | Windows-foglalás Svr kapacitásnövelés (4 mag) | B sorozat |
| e828cb37-5920-4dc7-b30f-664e4dbcb6c7 | Windows-foglalás Svr kapacitásnövelés (8 mag) | B sorozat |
| f65a06cf-c9c3-47a2-8104-f17a8542215a | Windows-foglalás Svr (1 mag) | B sorozat kivételével |
| b99d40ae-41fe-4d1d-842b-56d72f3d15ee | Windows-foglalás Svr (2 mag) | B sorozat kivételével |
| 1cb88381-0905-4843-9ba2-7914066aabe5 | Windows-foglalás Svr (4 mag) | B sorozat kivételével |
| 07d9e10d-3e3e-4672-ac30-87f58ec4b00a | Windows-foglalás Svr (6 mag) | B sorozat kivételével |
| 603f58d1-1e96-460b-a933-ce3775ac7e2e | Windows-foglalás Svr (8 mag) | B sorozat kivételével |
| 36aaadda-da86-484a-b465-c8b5ab292d71 | Windows-foglalás Svr (12 mag) | B sorozat kivételével |
| 02968a6b-1654-4495-ada6-13f378ba7172 | Windows-foglalás Svr (16 mag) | B sorozat kivételével |
| 175434d8-75f9-474b-9906-5d151b6bed84 | Windows-foglalás Svr (20 mag) | B sorozat kivételével |
| 77eb6dd0-88f5-4a16-ab39-05d1742efb25 | Windows-foglalás Svr (24 mag) | B sorozat kivételével |
| 0d5bdf46-b719-4b1f-a780-b9bdfffd0591 | Windows-foglalás Svr (32 mag) | B sorozat kivételével |
| f1214b5c-cc16-445f-be6c-a3bb75f8395a | Windows-foglalás Svr (40 mag) | B sorozat kivételével |
| 637b7c77-65ad-4486-9cc7-dc7b3e9a8731 | Windows-foglalás Svr (64 mag) | B sorozat kivételével |
| da612742-e7cc-4ca3-9334-0fb7234059cd | Windows-foglalás Svr (72 mag) | B sorozat kivételével |
| a485cb8c-069b-4cf3-9a8e-ddd84b323da2 | Windows-foglalás Svr (128 mag) | B sorozat kivételével |
| 904c5c71-1eb7-43a6-961c-d305a9681624 | Windows-foglalás Svr (256 mag) | B sorozat kivételével |
| 6fdab81b-4284-4df9-8939-c237cc7462fe | Windows-foglalás Svr (96 mag) | B sorozat kivételével |

Ezek a mérőszámok költségét Azure RateCard API-n keresztül érheti el. A díjszabás beszerzésére egy azure mérő információkért lásd: [ár és a metaadat-információ az Azure-előfizetés használt erőforrások lekérése](https://msdn.microsoft.com/library/azure/mt219004).

## <a name="next-steps"></a>További lépések
Tudhat meg többet a fenntartott virtuálisgép-példányok, tekintse meg a következő cikkekben talál.

- [A virtuális gépek fenntartott virtuális gép osztályt előre fizetés](../virtual-machines/windows/prepay-reserved-vm-instances.md)
- [Fenntartott virtuálisgép-példányok kezelése](billing-manage-reserved-vm-instance.md)
- [Kevesebbet költeni a virtuális gépek a fenntartott virtuálisgép-példányok](billing-save-compute-costs-reservations.md)
- [A fenntartott virtuális gép példány kedvezményeket alkalmazásának a módját ismertetése](billing-understand-vm-reservation-charges.md)
- [A használatalapú fizetéses előfizetésre fenntartott példány használatának megértéséhez](billing-understand-reserved-instance-usage.md)
- [A nagyvállalati beléptetés használata fenntartott példány ismertetése](billing-understand-reserved-instance-usage-ea.md)
