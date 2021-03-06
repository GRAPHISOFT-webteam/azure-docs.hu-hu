---
title: "Azure Application Insights Telemetria-adatmodell - nyomkövetési Telemetria |} Microsoft Docs"
description: "Application Insights – nyomkövetési telemetria tartozó adatmodell"
services: application-insights
documentationcenter: .net
author: SergeyKanzhelev
manager: carmonm
ms.service: application-insights
ms.workload: TBD
ms.tgt_pltfrm: ibiza
ms.devlang: multiple
ms.topic: article
ms.date: 04/25/2017
ms.author: mbullwin
ms.openlocfilehash: 0398774e21d89fd084e6929bc5e410697d2aafaa
ms.sourcegitcommit: e462e5cca2424ce36423f9eff3a0cf250ac146ad
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/01/2017
---
# <a name="trace-telemetry-application-insights-data-model"></a>Nyomkövetési telemetria: Application Insights adatmodell

Nyomkövetési telemetria (a [Application Insights](app-insights-overview.md)) jelöli `printf` stílus nyomkövetési utasítás szöveget keres. `Log4Net`, `NLog`, és más szöveges naplófájl-bejegyzéseket az ilyen típusú példányok van lefordítva. A nyomkövetés nem lehet mérési egy bővíthetőségi.

## <a name="message"></a>Üzenet

Nyomkövetési üzenet.

Maximális hossz: 32768 karakter

## <a name="severity-level"></a>Súlyossági szint

Nyomkövetési súlyossági szint. Az érték lehet `Verbose`, `Information`, `Warning`, `Error`, `Critical`.

## <a name="custom-properties"></a>Egyéni tulajdonságok

[!INCLUDE [application-insights-data-model-properties](../../includes/application-insights-data-model-properties.md)]

## <a name="next-steps"></a>Következő lépések

- [.NET nyomkövetési naplók megtekintése az Application Insights felfedezés](app-insights-asp-net-trace-logs.md).
- [Fedezze fel az Application Insights nyomkövetési naplók Java](app-insights-java-trace-logs.md).
- Lásd: [adatmodell](application-insights-data-model.md) Application Insights-típusok és az adatok modell.
- [Egyéni nyomkövetési telemetria írása](app-insights-api-custom-events-metrics.md#tracktrace)
- Tekintse meg [platformok](app-insights-platforms.md) Application Insights által támogatott.
