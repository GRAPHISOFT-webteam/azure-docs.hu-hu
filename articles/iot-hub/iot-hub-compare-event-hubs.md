<properties
 pageTitle="比較 Azure IoT 中樞與 Azure 事件中樞 |Microsoft Azure"
 description="透過反白顯示功能差異和使用案例來比較 Azure IoT 中樞和 Azure 事件中樞服務。"
 services="iot-hub"
 documentationCenter=""
 authors="fsautomata"
 manager="timlt"
 editor=""/>

<tags
 ms.service="iot-hub"
 ms.devlang="na"
 ms.topic="article"
 ms.tgt_pltfrm="na"
 ms.workload="na"
 ms.date="10/02/2015"
 ms.author="elioda"/>

# IoT 中樞和事件中樞的比較

Azure IoT 中心的主要使用案例之一是從裝置收集遙測。 基於這個理由，IoT 中心通常比較 [事件中樞][], ，因為它是雲端的大規模、 事件和遙測輸入提供低延遲且高可靠性事件處理服務。

不過，這些服務有許多差異，將在以下章節詳述。

| 領域 | IoT 中心 | 事件中樞 |
| ---- | ------- | ---------- |
| 通訊模式 | 裝置到雲端事件輸入和雲端到裝置傳訊。 | 僅限於事件輸入 (通常視為裝置到雲端案例)。 |
| 安全性 | 每個裝置的身分識別與可撤銷的存取控制。 See [IoT Hub Developer Guide - Security]. | 事件中樞全 [共用存取原則][Event Hub - security], ，有限撤銷支援使用 [發行者原則][Event Hub publisher policies]。 在 IoT 解決方案的內容中，通常需要實作自訂解決方案來支援每個裝置的認證以及防詐騙措施。 |
| 調整 | IoT 中樞會最佳化以支援數百萬同時連接的裝置。 | 事件中心可支援更多限制的同時連線數目 ︰ 最多 5000 個 AMQP 連線，為每個 [服務匯流排配額][]。 另一方面，事件中樞可讓您指定每個傳送訊息的分割。 |
| 裝置 SDK | IoT 中心提供 [裝置 Sdk][Azure IoT Hub SDKs] 大型各種不同的平台和語言 | .NET、C 都支援事件中樞，且事件中樞提供 AMQP 和 HTTP 傳送介面。 |

總而言之，即使唯一的使用案例是裝置到雲端遙測輸入，IoT 中樞仍會提供專為 IoT 裝置連線所設計的服務，並持續擴充具備 IoT 功能之案例的價值主張。 事件中樞的設計對象是資料中心案例之間及其內部的大規模事件輸入。

合併使用 IoT 中心和事件中樞的情況並不常見，除非前者處理裝置到雲端通訊，而後者處理後期事件輸入至及時處理引擎。

## 後續步驟

遵循下列連結以深入了解 Azure IoT 中樞：

- [開始使用 IoT 中心 (教學課程)][lnk-get-started]
- [何謂 Azure IoT 中心？][]

[Event Hubs]: ../event-hubs/event-hubs-what-is-event-hubs.md
[IoT Hub Developer Guide - Security]: iot-hub-devguide.md#security
[Event Hub - security]: ../event-hubs/event-hubs-authentication-and-security-model-overview.md
[Event Hub publisher policies]: ../event-hubs/event-hubs-overview.md#common-publisher-tasks
[Service Bus Quotas]: ../service-bus/service-bus-quotas.md
[Azure IoT Hub SDKs]: https://github.com/Azure/azure-iot-sdks/blob/master/readme.md
[lnk-get-started]: iot-hub-csharp-csharp-getstarted.md
[What is Azure IoT Hub?]: iot-hub-what-is-iot-hub.md


