---
title: "Hogyan kell konfigurációs fájlokat az Azure Service Fabric paraméterezni |} Microsoft Docs"
description: "Bemutatja, hogyan kell konfigurációs fájlokat a Service Fabric paraméterezni"
documentationcenter: .net
author: mikkelhegn
manager: msfussell
editor: 
ms.service: service-fabric
ms.devlang: dotNet
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 12/06/2017
ms.author: mikhegn
ms.openlocfilehash: 1e7d59ecb231440711b8ed3dc0b27a2b105890c4
ms.sourcegitcommit: 384d2ec82214e8af0fc4891f9f840fb7cf89ef59
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/16/2018
---
# <a name="how-to-parameterize-configuration-files-in-service-fabric"></a>Hogyan kell konfigurációs fájlokat a Service Fabric paraméterezni

Ez a cikk bemutatja, hogyan parametrizálja a Service Fabric konfigurációs fájlt.

## <a name="procedure-for-parameterizing-configuration-files"></a>A konfigurációs fájlok paraméterezése eljárás

Ebben a példában bírálja felül a paraméterek használatát az alkalmazások központi telepítésének konfigurációs érték.

1. Nyissa meg a Config\Settings.xml fájlt.
1. Egy konfigurációs paraméter állította a következő XML-fájlok hozzáadása:

    ```xml
      <Section Name="MyConfigSection">
        <Parameter Name="CacheSize" Value="25" />
      </Section>
    ```

1. Mentse és zárja be a fájlt.
1. Nyissa meg az `ApplicationManifest.xml` fájlt.
1. Adja hozzá a `ConfigOverride` elem, a konfigurációs csomagot, a szakasz és a paraméter hivatkozik.

      ```xml
        <ConfigOverrides>
          <ConfigOverride Name="Config">
              <Settings>
                <Section Name="MyConfigSection">
                    <Parameter Name="CacheSize" Value="[Stateless1_CacheSize]" />
                </Section>
              </Settings>
          </ConfigOverride>
        </ConfigOverrides>
      ```

1. Továbbra is a ApplicationManifest.xml fájlban, akkor adja meg a paraméter a `Parameters` elem

    ```xml
      <Parameters>
        <Parameter Name="Stateless1_CacheSize" />
      </Parameters>
    ```

1. És adja meg a`DefaultValue`

    ```xml
      <Parameters>
        <Parameter Name="Stateless1_CacheSize" DefaultValue="80" />
      </Parameters>
    ```

> [!NOTE]
> Abban az esetben, ahol hozzáadhat egy ConfigOverride a Service Fabric mindig úgy dönt, az alkalmazás paramétereit vagy az alkalmazás jegyzékében meghatározott alapértelmezett érték.
>
>

A Visual Studio használatával a Cloud.xml az alkalmazás közzétételéhez közzétételi profil, ha a szolgáltatás 80-as port használatára van konfigurálva. Az alkalmazás központi telepítése a MyWebAPI_PortNumber paraméter megadása nélkül, ha a szolgáltatás a 8080-as portot használ.

## <a name="next-steps"></a>További lépések
Ebben a cikkben ismertetett alapfogalmakat némelyike kapcsolatos további tudnivalókért tekintse meg a [több környezetek cikkek alkalmazásokat kezeléséhez](service-fabric-manage-multiple-environment-app-configuration.md).

Más elérhető a Visual Studio alkalmazás-felügyeleti képességekkel kapcsolatos információkért lásd: [kezelése a Service Fabric-alkalmazások, a Visual Studio](service-fabric-manage-application-in-visual-studio.md).