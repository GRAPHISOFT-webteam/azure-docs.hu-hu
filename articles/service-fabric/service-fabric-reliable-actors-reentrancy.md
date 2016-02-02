<properties
   pageTitle="Reliable Actor 重新進入"
   description="Service Fabric Reliable Actor 重新進入簡介。"
   services="service-fabric"
   documentationCenter=".net"
   authors="jessebenson"
   manager="timlt"
   editor="vturecek"/>

<tags
   ms.service="service-fabric"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="NA"
   ms.date="11/14/2015"
   ms.author="amanbha"/>



# Reliable Actor 重新進入

預設的網狀架構動作項目允許邏輯呼叫以內容為基礎的重新進入。 這允許位於相同的呼叫內容鏈結的動作項目可重新進入。 例如，如果動作項目 A 傳送訊息給動作項目 B，而動作項目 B 又將訊息傳送給動作項目 C。作為處理訊息的一部分，如果動作項目 C 呼叫動作項目 A，此訊息將允許且會變成可重新進入。 任何其他屬於不同呼叫內容的訊息將從動作項目 A 上封鎖，直到其處理完畢。

動作項目想要禁止邏輯呼叫內容為基礎的重新進入可以停用它以裝飾動作項目類別 `ReentrantAttribute(ReentrancyMode.Disallowed)`。

```csharp
public enum ReentrancyMode
{
    LogicalCallContext = 1,
    Disallowed = 2
}
```

下列程式碼會示範動作項目類別的重新進入模式設定為 `ReentrancyMode.Disallowed`。 在此情況下，如果動作項目傳送可重新進入訊息給另一個動作項目類型的例外狀況 `FabricException` 就會擲回。

```csharp
[Reentrant(ReentrancyMode.Disallowed)]
class VoicemailBoxActor : StatefulActor<VoicemailBox>, IVoicemailBoxActor
{
    ...
}
```





