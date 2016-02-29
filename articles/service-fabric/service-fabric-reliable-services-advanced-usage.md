<properties
   pageTitle="可靠的服務程式設計模型進階用法"
   description="深入了解 Service Fabric 可靠服務程式設計模型的進階用法，以在服務中增加彈性。"
   services="Service-Fabric"
   documentationCenter=".net"
   authors="jessebenson"
   manager="timlt"
   editor="masnider"/>

<tags
   ms.service="Service-Fabric"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="NA"
   ms.date="11/17/2015"
   ms.author="jesseb"/>

# 可靠服務程式設計模型的進階用法
Service Fabric 可簡化撰寫和管理可靠的無狀態與具狀態服務。 本指南會討論到「可靠服務」程式設計模型的進階用法，以在您的服務中取得更多控制權和彈性。 在之前閱讀這份指南，讓您熟悉 [可靠服務程式設計模型](service-fabric-reliable-services-introduction.md)。

## 無狀態服務基底類別
StatelessService 基底類別提供 RunAsync() 和 CreateServiceInstanceListeners()，這對大部分的無狀態服務來說已經足夠。 StatelessServiceBase 類別以 StatelessService 為基礎，並公開其他服務生命週期事件。 如果您需要額外的控制或彈性，您可以從 StatelessServiceBase 衍生。 請參閱開發人員參考文件 [StatelessService](https://msdn.microsoft.com/library/azure/microsoft.servicefabric.services.statelessservice.aspx) 和 [StatelessServiceBase](https://msdn.microsoft.com/library/azure/microsoft.servicefabric.services.statelessservicebase.aspx) 如需詳細資訊。

- `void OnInitialize(StatelessServiceInitializiationParameters)`
    OnInitialize 是 Service Fabric 呼叫的第一個方法。 其提供服務初始化資訊，例如服務名稱、資料分割識別碼、執行個體識別碼和程式碼封裝資訊。 這裡不應執行任何複雜的處理。 冗長的初始化應該透過 OnOpenAsync 執行。

- `Task OnOpenAsync(IStatelessServicePartition, CancellationToken)`
    使用無狀態服務執行個體時，會呼叫 OnOpenAsync。 這個階段可以啟動擴充服務的初始化作業。

- `Task OnCloseAsync(CancellationToken)`
    無狀態服務執行個體要將正常關機時，會呼叫 OnCloseAsync。 這可能在升級服務程式碼、因負載平衡而移動服務執行個體或偵測到暫時性失敗的時侯發生。 OnCloseAsync 可以用來安全地關閉任何資源、停止任何背景處理、完成外部狀態儲存或關閉現有的連接。

- `void OnAbort()`
    無狀態服務執行個體已強制關閉時，會呼叫 OnAbort。 這個一般會在於節點上偵測到永久錯誤，或因內部失敗而 Service Fabric 無法可靠地管理服務執行個體生命週期時呼叫。

## 具狀態服務基底類別
StatefulService 基底類別對大部分的具狀態服務應已足夠。 類似無狀態服務，StatefulServiceBase 類別以 StatefulService 為基礎，並公開其他服務生命週期事件。 此外，其可讓您提供自訂的穩定狀態提供者，並選擇性地在次要複本上支援通訊接聽程式。 如果您需要額外的控制或彈性，您可以從 StatefulServiceBase 衍生。 請參閱開發人員參考文件 [StatefulService](https://msdn.microsoft.com/library/azure/microsoft.servicefabric.services.statefulservice.aspx) 和 [StatefulServiceBase](https://msdn.microsoft.com/library/azure/microsoft.servicefabric.services.statefulservicebase.aspx) 如需詳細資訊。

- `Task OnChangeRoleAsync(ReplicaRole, CancellationToken)`
    具狀態服務變更角色，例如為主要或次要資料庫時，會呼叫 OnChangeRoleAsync。 其提供主要複本寫入狀態 (允許建立和寫入可靠集合)，並提供次要複本讀取狀態 (只能讀取現有可靠集合)。 您可以啟動或更新背景作業以回應角色變更，例如在次要複本上執行唯讀驗證、 產生報告或資料採礦。

- `IStateProviderReplica CreateStateProviderReplica()`
    可設定狀態的服務會預期有可靠的狀態提供者。 StatefulService 使用 ReliableStateManager 類別，並可提供可靠的集合 (例如字典和佇列)。 您可以覆寫此方法，方法是透過傳遞 ReliableStateManagerConfiguration 給其建構函式來設定 ReliableStateManager 類別。 這可以讓您提供自訂狀態序列化程式，指定資料可能已遺失時會發生什麼狀況，以及設定覆寫器/狀態提供者。

StatefulServiceBase 也會提供與 StatelessServiceBase 相同的四個生命週期事件，並具有相同的語意和使用案例：

- `void OnInitialize(StatefulServiceInitializiationParameters)`
- `Task OnOpenAsync(IStatefulServicePartition, CancellationToken)`
- `Task OnCloseAsync(CancellationToken)`
- `void OnAbort()`

## 後續步驟
如需更多與 Service Fabric 相關的進階主題，請參閱下列文件。

- [設定具狀態可靠的服務](service-fabric-reliable-services-configuration.md)

- [Service Fabric 健康狀態簡介](service-fabric-health-introduction.md)

- [使用系統健康情況報告進行疑難排解](service-fabric-understand-and-troubleshoot-with-system-health-reports.md)

- [位置限制概觀](service-fabric-placement-constraint.md)

