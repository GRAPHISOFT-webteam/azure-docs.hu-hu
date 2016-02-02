<properties 
pageTitle="處理雲端服務生命週期事件 | Microsoft Azure" 
description="了解如何在 .NET 中使用雲端服務角色的生命週期方法" 
services="cloud-services" 
documentationCenter=".net" 
authors="Thraka" 
manager="timlt" 
editor=""/>
<tags 
ms.service="cloud-services" 
ms.workload="tbd" 
ms.tgt_pltfrm="na" 
ms.devlang="na" 
ms.topic="article" 
ms.date="12/07/2015" 
ms.author="adegeo"/>


# 在 .NET 中自訂 Web 或背景工作角色的生命週期

當您建立背景工作角色時，擴充 [RoleEntryPoint](https://msdn.microsoft.com/library/azure/microsoft.windowsazure.serviceruntime.roleentrypoint.aspx) 類別可提供讓您覆寫這些方法可讓您回應生命週期事件。 若是 Web 角色，此類別是選擇性的，因此您可以使用它來回應生命週期事件。

## 擴充 RoleEntryPoint 類別

[RoleEntryPoint](https://msdn.microsoft.com/library/azure/microsoft.windowsazure.serviceruntime.roleentrypoint.aspx) 類別包含 Azure 呼叫的方法時它 **啟動**, ，**執行**, ，或 **停止** web 或背景工作角色。 您可以選擇性地覆寫這些方法來管理角色初始化、角色關機順序或角色的執行緒。

擴充 **RoleEntryPoint** 時，您應該注意下列方法的行為：

-   [OnStart](https://msdn.microsoft.com/library/azure/microsoft.windowsazure.serviceruntime.roleentrypoint.onstart.aspx) 和 [OnStop](https://msdn.microsoft.com/library/azure/microsoft.windowsazure.serviceruntime.roleentrypoint.onstop.aspx) 方法會傳回布林值，因此很可能會傳回 **false** 從這些方法。

     如果您的程式碼傳回 **false**，會突然終止角色處理序，而不會執行您既有的任何關機順序。 一般而言，您應該避免從 **OnStart** 方法傳回 **false**。

-   **RoleEntryPoint** 方法多載中未能攔截的任何例外狀況，都會被視為未處理的例外狀況。

     其中一個生命週期方法內發生的例外狀況，則 Azure 會引發 [UnhandledException](https://msdn.microsoft.com/library/system.appdomain.unhandledexception.aspx) 事件，然後按一下 [處理序已終止。 您的角色離線之後，Azure 將重新啟動該角色。 當發生未處理的例外狀況時， [停止](https://msdn.microsoft.com/library/azure/microsoft.windowsazure.serviceruntime.roleenvironment.stopping.aspx) 不會引發事件，而 **OnStop** 不會呼叫方法。

如果您的角色無法啟動，或在初始化、忙碌和停止狀態之間循環，每次角色重新啟動時，您的程式碼可能會在其中一個生命週期事件內擲回未處理的例外狀況。 在此案例中，使用 [UnhandledException](https://msdn.microsoft.com/library/system.appdomain.unhandledexception.aspx) 事件，以判斷造成例外狀況，並適當地處理。 您的角色也可能會傳回從 [執行](https://msdn.microsoft.com/library/azure/microsoft.windowsazure.serviceruntime.roleentrypoint.run.aspx) 方法，導致角色重新啟動。 如需部署狀態的詳細資訊，請參閱 [常見的問題導致角色回收](https://msdn.microsoft.com/library/azure/gg465402.aspx)。
> [AZURE.NOTE] 如果您使用 [Azure Tools for Microsoft Visual Studio](https://msdn.microsoft.com/library/azure/ee405484.aspx) 來開發應用程式，角色專案範本會自動擴充 **RoleEntryPoint** ，WebRole.cs 及 WorkerRole.cs 檔案中的類別。

## OnStart 方法

當 Azure 將您的角色執行個體連線時，會呼叫 **OnStart** 方法。 OnStart 程式碼執行時，角色執行個體會標示為 **Busy**，而且負載平衡器不會將任何外部流量導向至該執行個體。 您可以覆寫此方法執行初始化工作，例如實作事件處理常式及啟動 [Azure 診斷](cloud-services-how-to-monitor.md)。

如果 **OnStart** 傳回 **true**，便會成功地初始化執行個體，而且 Azure 會呼叫 **RoleEntryPoint.Run** 方法。 如果 **OnStart** 傳回 **false**，角色會立即終止，而不執行任何計劃的關機順序。

下列程式碼範例示範如何覆寫 **OnStart** 方法。 當角色執行個體啟動，並設定將記錄資料傳輸至儲存體帳戶時，此方法會設定並啟動診斷監視器：

```csharp
public override bool OnStart()
{
    var config = DiagnosticMonitor.GetDefaultInitialConfiguration();

    config.DiagnosticInfrastructureLogs.ScheduledTransferLogLevelFilter = LogLevel.Error;
    config.DiagnosticInfrastructureLogs.ScheduledTransferPeriod = TimeSpan.FromMinutes(5);

    DiagnosticMonitor.Start("DiagnosticsConnectionString", config);

    return true;
}
```

## OnStop 方法

在 Azure 將角色執行個體離線之後，以及處理序結束之前，會呼叫 **OnStop** 方法。 您可以覆寫此方法以呼叫讓您的角色執行個體正常關機所需的程式碼。
> [AZURE.IMPORTANT] 因為使用者起始關機以外的原因呼叫使用 **OnStop** 方法執行的程式碼時，其完成時間有限。 經過這個時間之後，處理序便會終止，因此您必須確定 **OnStop** 方法中的該程式碼可以快速執行，或容許完成之前不執行。 系統會在引發 **Stopping** 事件之後呼叫 **OnStop** 方法。


## Run 方法

您可以覆寫 **Run** 方法，針對您的角色執行個體實作長時間執行的執行緒。

不需要覆寫 **Run** 方法；預設實作會啟動永久處於睡眠狀態的執行緒。 如果您覆寫 **Run** 方法，您的程式碼應該會無限期地封鎖。 如果傳回 **Run** 方法，就會自動正常回收角色；換句話說，Azure 會引發 **Stopping** 事件並呼叫 **OnStop** 方法，讓您的關機順序可以在角色離線之前執行。


### 實作 Web 角色的 ASP.NET 生命週期方法

除了提供 **RoleEntryPoint** 類別提供的方法之外，您還可以使用 ASP.NET 生命週期方法管理 Web 角色的初始化及關機順序。 如果您要將現有的 ASP.NET 應用程式移植至 Azure，這對於相容性可能很有幫助。 系統會從 **RoleEntryPoint** 方法內呼叫 ASP.NET 生命週期方法。  **Application\_Start** 方法之後呼叫 **RoleEntryPoint.OnStart** 方法完成。  **Application\_End** 方法之前，呼叫 **Application_end** 方法呼叫。

## 後續步驟

了解如何 [建立雲端服務封裝](cloud-services-model-and-package.md)。




