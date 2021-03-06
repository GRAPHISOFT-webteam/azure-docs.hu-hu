---
title: "Másodlagos Azure-régióba replikált Azure-beli virtuális gépek feladatátvétele és feladat-visszavétele az Azure Site Recoveryvel (előzetes verzió)"
description: "Megtudhatja, hogyan végezhet feladatátvételt és feladat-visszavételt az Azure-beli virtuális gépeken másodlagos Azure-régióra az Azure Site Recoveryvel"
services: site-recovery
author: rayne-wiselman
manager: carmonm
ms.service: site-recovery
ms.topic: tutorial
ms.date: 02/07/2018
ms.author: raynew
ms.custom: mvc
ms.openlocfilehash: b45d1287444d200727550a81ce72a19a417fe510
ms.sourcegitcommit: 059dae3d8a0e716adc95ad2296843a45745a415d
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/09/2018
---
# <a name="fail-over-and-fail-back-azure-vms-between-azure-regions-preview"></a>Azure-beli virtuális gépek feladatátvétele és feladat-visszavétele Azure-régiók között (előzetes verzió)

Az [Azure Site Recovery](site-recovery-overview.md) szolgáltatás a helyszíni számítógépek és az Azure-beli virtuális gépek replikálásának, feladatátvételének és feladat-visszavételének kezelésével és irányításával járul hozzá a vészhelyreállítási stratégia megvalósításához.

Ez az oktatóanyag leírja, hogyan végezhet feladatátvételt egyetlen Azure-beli virtuális gépen egy másodlagos Azure-régióra. A feladatátvétel után visszaadja a feladatokat az elsődleges régióra, amint az elérhetővé válik. Eben az oktatóanyagban az alábbiakkal fog megismerkedni:

> [!div class="checklist"]
> * Az Azure-beli virtuális gép feladatátvétele
> * A másodlagos Azure-beli virtuális gép ismételt védelme, hogy az elsődleges régióba replikálódjon
> * A másodlagos virtuális gép feladat-visszavétele
> * Az elsődleges virtuális gép ismételt védelme a másodlagos régióban

## <a name="prerequisites"></a>Előfeltételek

- Végezzen [vészhelyreállítási próbát](azure-to-azure-tutorial-dr-drill.md) annak ellenőrzéséhez, hogy minden a várt módon működik-e.
- Ellenőrizze a virtuális gép tulajdonságait a feladatátvételi teszt futtatása előtt. A virtuális gépnek meg kell felelnie az [Azure-követelményeknek](site-recovery-support-matrix-to-azure.md#failed-over-azure-vm-requirements).

## <a name="run-a-failover-to-the-secondary-region"></a>Feladatátvétel futtatása a másodlagos régióba

1. A **Replikált elemek** területen válassza ki azt a virtuális gépet, amelyen feladatátvételt szeretne végezni > **Feladatátvétel**

   ![Feladatátvétel](./media/azure-to-azure-tutorial-failover-failback/failover.png)

2. A **Feladatátvétel** területen válassza ki azt a **Helyreállítási pontot**, amelyre a feladatátvételt végezni szeretné. Az alábbi lehetőségek egyikét használhatja:

   * **Legújabb** (alapértelmezett): Ez a lehetőség feldolgozza a Site Recovery szolgáltatásban lévő összes adatot, és a legalacsonyabb helyreállítási időkorlátot (RPO) biztosítja.
   * **Legutóbb feldolgozott**: Ez a lehetőség a virtuális gép feladatátvételét a Site Recovery által feldolgozott legutóbbi helyreállítási pontra végzi.
   * **Egyéni**: Akkor használja ezt a lehetőséget, ha egy adott helyreállítási pontra szeretné végezni a feladatátvételt. Ez a lehetőség feladatátvételi teszt végrehajtásához hasznos.

3. Válassza a **Gép leállítása a feladatátvétel megkezdése előtt** lehetőséget, ha azt szeretné, hogy a Site Recovery megkísérelje leállítani a forrás virtuális gépeket a feladatátvétel indítása előtt. A feladatátvételi akkor is folytatódik, ha a leállítás meghiúsul.

4. A feladatátvételi folyamatot a **Feladatok** lapon követheti nyomon.

5. A feladatátvétel után a virtuális gépre való bejelentkezéssel ellenőrizze a virtuális gépet. Ha a virtuális gép egy másik helyreállítási pontjára szeretne ugrani, akkor a **Helyreállítási pont módosítása** lehetőséget használhatja.

6. Ha elégedett a feladatátviteli virtuális géppel, **véglegesítheti** a feladatátvételt.
   A véglegesítés törli a szolgáltatással elérhető összes helyreállítási pontot. A **Helyreállítási pont módosítása** lehetőség ezután nem érhető el.

## <a name="reprotect-the-secondary-vm"></a>A másodlagos virtuális gép ismételt védelme

A virtuális gép feladatátvétele után ismét meg kell védenie azt, hogy az visszareplikálódjon az elsődleges régióba.

1. Győződjön meg arról, hogy a virtuális gép a **Feladatátvétel véglegesítve** állapotban van, és ellenőrizze, hogy az elsődleges régió elérhető-e, és létre tud-e hozni új erőforrásokat az elsődleges régióban, valamint el tudja-e érni azokat.
2. A **Tároló** > **Replikált elemek** területen kattintson a jobb gombbal arra a virtuális gépre, amelyen feladatátvételt végzett, és válassza az **Ismételt védelem** parancsot.

   ![Kattintson a jobb gombbal az ismételt védelemhez](./media/azure-to-azure-tutorial-failover-failback/reprotect.png)

2. Figyelje meg, hogy a védelem iránya (a másodlagostól az elsődleges régióba) már be van jelölve.
3. Tekintse át az **Erőforráscsoport, a Hálózat, a Tárolás és a Rendelkezésre állási csoportok** adatait. Az összes megjelölt (új) erőforrás az ismételt védelmi művelet részeként jön létre.
4. Kattintson az **OK** gombra az ismételt védelmi feladat elindításához. Ez a feladat feltölti a célhelyet a legújabb adatokkal. Ezután replikálja az eltéréseket az elsődleges régióba. A virtuális gép most védett állapotban van.

## <a name="fail-back-to-the-primary-region"></a>Feladat-visszavétel az elsődleges régióba

A virtuális gépek ismételt védelme után szükség szerint végezhet feladat-visszavételt az elsődleges régióba. Ehhez kövesse a [feladatátvételi](#run-a-failover) utasításokat.
