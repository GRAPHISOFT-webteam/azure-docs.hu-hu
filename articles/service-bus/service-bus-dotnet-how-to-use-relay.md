<properties
    pageTitle="如何使用服務匯流排轉送搭配 .NET | Microsoft Azure"
    description="了解如何使用 Azure 服務匯流排轉送服務連接主控於相異位置的兩個應用程式。"
    services="service-bus"
    documentationCenter=".net"
    authors="sethmanheim"
    manager="timlt"
    editor=""/>

<tags
    ms.service="service-bus"
    ms.workload="tbd"
    ms.tgt_pltfrm="na"
    ms.devlang="dotnet"
    ms.topic="get-started-article"
    ms.date="10/07/2015"
    ms.author="sethm"/>



# 如何使用 Azure 服務匯流排轉送服務

本文說明如何使用服務匯流排轉送服務。 這些範例均以 C# 撰寫，並使用 Windows Communication Foundation (WCF) API 以及包含在服務匯流排組件 (Microsoft Azure .NET SDK 的一部分) 中的擴充功能。 如需服務匯流排轉送的詳細資訊，請參閱 [服務匯流排轉送傳訊](service-bus-relay-overview.md) 概觀。

[AZURE.INCLUDE [create-account-note](../../includes/create-account-note.md)]

## 什麼是服務匯流排轉送

[服務匯流排 * 轉送 * 服務](service-bus-relay-overview.md) 可讓您建置在 Azure 資料中心和您自己的內部部署企業環境中執行的混合式應用程式。 服務匯流排轉送，以協助這讓您以安全地公開位於企業網路到公用雲端，而不需開啟防火牆連線或要求對企業的進行侵入式變更的 Windows Communication Foundation (WCF) 服務
網路基礎結構。

![轉送概念](./media/service-bus-dotnet-how-to-use-relay/sb-relay-01.png)

服務匯流排轉送可讓您代管位於現有企業環境內的 WCF 服務。 您可以再委派接聽連入工作階段和要求這些服務匯流排的 WCF 服務
Azure 內部執行的服務。 這可讓您將這些服務公開給在 Azure 中執行的應用程式程式碼，或是給行動工作者或外部網路合作夥伴環境。 服務匯流排可讓您安全地控制可以
存取這些服務精細的層級。 它提供了功能強大及安全的方式，來公開應用程式功能及現有企業解決方案的資料，並從雲端加以利用。

本文章將示範如何使用服務匯流排轉送來建立 WCF Web 服務，使用可在兩端之間實作安全交談的 TCP 通道繫結來加以公開。

## 建立服務命名空間

若要開始在 Azure 中使用服務匯流排轉送，您必須先
建立服務命名空間。 命名空間提供範圍
容器，供您的應用程式內定址服務匯流排資源。

建立服務命名空間：

1.  登入 [Azure 傳統入口網站 []][]。

2.  在入口網站的左方瀏覽窗格中，按一下
    **服務匯流排**。

3.  在入口網站的下方窗格中，按一下 [建立]****。

    ![](./media/service-bus-dotnet-how-to-use-relay/sb-queues-13.png)

4.  在 [加入新的命名空間]**** 對話方塊中，輸入命名空間名稱。
    系統會立即檢查此名稱是否可用。

    ![](./media/service-bus-dotnet-how-to-use-relay/sb-queues-04.png)

5.  確定命名空間名稱可用之後，請選擇
    您的命名空間應裝載在哪個國家或地區 (請
    確實使用您部署運算資源所在的相同
    國家/地區)。
    > [AZURE.IMPORTANT] 請挑選您想要選擇來部署應用程式的*相同區域*。 這樣可以獲得最佳效能。

6.  讓對話方塊中的其他欄位保留其預設值 ([傳訊]**** 和 [標準層]****)，然後按一下核取記號。 此時系統會建立並啟用命名空間。 系統為帳戶提供資源時，您可能需要等幾分鐘。

    ![](./media/service-bus-dotnet-how-to-use-relay/getting-started-multi-tier-27.png)

    您建立的命名空間就會出現在入口網站中，稍待片刻就會生效。 等到狀態變成 [作用中]**** 之後再繼續。

## 取得命名空間的預設管理認證

若要在新的命名空間上執行管理作業 (例如建立轉送連線)，您必須為命名空間設定共用存取簽章 (SAS) 授權規則。 如需有關 SAS 的詳細資訊，請參閱 [服務匯流排 [] 使用共用存取簽章驗證][]。

1.  在左的導覽窗格中，按一下 [ **服務匯流排** 節點至
    顯示可用的命名空間清單。
    ![](./media/service-bus-dotnet-how-to-use-relay/sb-queues-13.png)

2.  連按兩下您剛從顯示的清單建立的命名空間的名稱。
    ![](./media/service-bus-dotnet-how-to-use-relay/sb-queues-09.png)

3.  按一下頁面頂端的 [設定]**** 索引標籤。

4.  佈建完服務匯流排命名空間後，預設會建立一個 **KeyName** 設為 **RootManageSharedAccessKey** 的 **SharedAccessAuthorizationRule**。 此頁面會顯示該金鑰以及預設規則的主要和次要金鑰。

## 取得服務匯流排 NuGet 封裝

要取得服務匯流排 API，並對應用程式進行設定，以使用所有服務匯流排相依性的最簡單方法，便是使用服務匯流排 NuGet 封裝。 NuGet Visual Studio 擴充功能可讓您輕易地安裝和更新 Visual Studio 和 Visual Studio Express 中的程式庫和工具。 服務匯流排 NuGet 封裝是最簡單的方式
若要取得服務匯流排 API，並設定您的應用程式使用所有服務匯流排相依性。

若要在應用程式中安裝 NuGet 封裝，請執行下列動作：

1.  在 [方案總管] 中，以滑鼠右鍵按一下 [喜好設定]****，然後按一下 [Manage NuGet Packages]****。
2.  搜尋 「 服務匯流排 」 並選取 * * Microsoft Azure
    服務匯流排 * * 項目。 按一下 [安裝]**** 完成安裝作業，然後關閉下列對話方塊。

    ![](./media/service-bus-dotnet-how-to-use-relay/getting-started-multi-tier-13.png)


## 使用服務匯流排來公開及取用具有 TCP 的 SOAP Web 服務

若要將現有的 WCF SOAP Web 服務公開給外部使用，您必須對服務繫結和位址進行變更。 這可能需要變更組態檔，或可能需要變更程式碼，視您如何設定和配置 WCF 服務而定。 請注意，WCF 可讓您對相同的服務有多個網路端點，因此您可以保留現有的內部端點，並同時新增用於外部存取的服務匯流排端點。

在本工作中，您將建立一個簡單的 WCF 服務，並為它新增服務匯流排接聽程式。 本練習假設您對 Visual Studio 有一定程度的了解，因此將不會逐步解說完成建立專案的所有詳細資料。 而是會將重點放在程式碼。

開始下面的步驟之前，請完成下列設定環境的程序：

1.  在 Visual Studio 中，建立解決方案中包含兩個專案 ("Client" 和 "Service") 的主控台應用程式。
2.  對兩個專案新增 Microsoft Azure 服務匯流排 NuGet 封裝。 這會將所有必要組件參照新增至您的專案。

### 如何建立服務

首先建立服務本身。 任何 WCF 服務都包含至少三個獨特部分：

-   說明會交換哪些訊息以及會叫用哪些作業的合約定義。
-   所述合約的實作。
-   代管 WCF 服務並公開多個端點的主機。

本節中的程式碼範例將逐一說明這些元件。

此合約會定義一個單一作業，即可新增兩個數字並傳回結果的 `AddNumbers`。 `IProblemSolverChannel` 介面可讓用戶端更輕鬆地管理 Proxy 存留期。 建立此類介面被視為是最佳做法。 最好能夠將此合約定義置於個別檔案中，以便您可以分別從 "Client" 和 "Service" 專案中參照該檔案，但您也可以將此程式碼複製到這兩個專案內。

```
using System.ServiceModel;

[ServiceContract(Namespace = "urn:ps")]
interface IProblemSolver
{
    [OperationContract]
    int AddNumbers(int a, int b);
}

interface IProblemSolverChannel : IProblemSolver, IClientChannel {}
```

合約準備好了以後，實作便微不足道。

```
class ProblemSolver : IProblemSolver
{
    public int AddNumbers(int a, int b)
    {
        return a + b;
    }
}
```

### 以程式設計方式設定服務主機

在準備好合約和實作之後，您便可以開始代管服務。 代管會發生在
[System.ServiceModel.ServiceHost](https://msdn.microsoft.com/library/azure/system.servicemodel.servicehost.aspx) 物件負責管理的服務執行個體並代管接聽訊息的端點。 下列程式碼將設定包含一般本機端點和服務匯流排端點的服務，以緊密地說明內部和外部端點的外觀。 使用您的命名空間名稱來取代字串 *namespace*，並使用上述設定步驟中所取得的 SAS 金鑰來取代 *yourKey*。

```
ServiceHost sh = new ServiceHost(typeof(ProblemSolver));

sh.AddServiceEndpoint(
   typeof (IProblemSolver), new NetTcpBinding(),
   "net.tcp://localhost:9358/solver");

sh.AddServiceEndpoint(
   typeof(IProblemSolver), new NetTcpRelayBinding(),
   ServiceBusEnvironment.CreateServiceUri("sb", "namespace", "solver"))
    .Behaviors.Add(new TransportClientEndpointBehavior {
          TokenProvider = TokenProvider.CreateSharedAccessSignatureTokenProvider("RootManageSharedAccessKey", "yourKey")});

sh.Open();

Console.WriteLine("Press ENTER to close");
Console.ReadLine();

sh.Close();
```

在此範例中，您將建立相同合約實作的兩個端點。 一個是本機，一個透過服務匯流排投射。 它們之間的主要差異是繫結; [NetTcpBinding](https://msdn.microsoft.com/library/azure/system.servicemodel.nettcpbinding.aspx) 本機和 [NetTcpRelayBinding](https://msdn.microsoft.com/library/azure/microsoft.servicebus.nettcprelaybinding.aspx) 服務匯流排端點和位址。 本機端點會包含具有獨特連接埠的本機網路位址。 服務匯流排端點會包含字串所組成的端點位址 `sb`, ，您的命名空間名稱及路徑"solver"。 這會產生 URI `sb://[serviceNamespace].servicebus.windows.net/solver`, ，指出服務端點為服務匯流排 TCP 端點，具有完整外部 DNS 名稱。 如果您的程式碼取代預留位置，如前文所述到 `Main` 函式的 **服務** 應用程式，您將會有一個功能性服務。 如果您想要服務專門接聽服務匯流排，請移除本機端點宣告。

### 在 App.config 檔案中設定服務主機

您也可以使用 App.config 檔案來設定主機。 此案例中的服務裝載程式碼會出現在下一個範例中。

```
ServiceHost sh = new ServiceHost(typeof(ProblemSolver));
sh.Open();
Console.WriteLine("Press ENTER to close");
Console.ReadLine();
sh.Close();
```

端點定義移入 App.config 檔案。 請注意，NuGet 封裝已在 App.config 檔案中新增許多定義，這些都是服務匯流排的必要組態擴充功能。 下列範例 (與上述程式碼完全相同) 應會出現在 **system.serviceModel** 元素的正下方。 這個程式碼範例假設您的專案 C# 命名空間名稱為 **Service**。
使用您的服務匯流排服務命名空間和金鑰來取代預留位置。

```
<services>
    <service name="Service.ProblemSolver">
        <endpoint contract="Service.IProblemSolver"
                  binding="netTcpBinding"
                  address="net.tcp://localhost:9358/solver"/>
        <endpoint contract="Service.IProblemSolver"
                  binding="netTcpRelayBinding"
                  address="sb://namespace.servicebus.windows.net/solver"
                  behaviorConfiguration="sbTokenProvider"/>
    </service>
</services>
<behaviors>
    <endpointBehaviors>
        <behavior name="sbTokenProvider">
            <transportClientEndpointBehavior>
                <tokenProvider>
                    <sharedAccessSignature keyName="RootManageSharedAccessKey" key="yourKey" />
                </tokenProvider>
            </transportClientEndpointBehavior>
        </behavior>
    </endpointBehaviors>
</behaviors>
```

在進行這些變更之後，此服務便會和以前一樣啟動，但會多了兩個即時端點：一個在本機，一個在雲端接聽。

### 建立用戶端

#### 以程式設計方式設定用戶端

若要取用此服務，您可以建構 WCF 用戶端使用 [ChannelFactory](https://msdn.microsoft.com/library/system.servicemodel.channelfactory.aspx) 物件。 服務匯流排會使用權杖為基礎的安全性
實作使用 SAS 模型。  [TokenProvider](https://msdn.microsoft.com/library/azure/microsoft.servicebus.tokenprovider.aspx) 類別代表安全性權杖提供者可傳回部分知名權杖提供者的內建原廠方法。 以下範例使用 [CreateSharedAccessSignatureTokenProvider](https://msdn.microsoft.com/library/azure/microsoft.servicebus.tokenprovider.createsharedaccesssignaturetokenprovider.aspx) 方法以處理適當 SAS 權杖的擷取。 如上一節所述，將從入口網站取得這些名稱和金鑰。

首先，將 `IProblemSolver` 合約程式碼從服務中參照或複製到您的用戶端專案。

然後，取代中的程式碼 `Main` 的用戶端上，再次使用您的服務匯流排命名空間和 SAS 金鑰來取代預留位置文字的方法。

```
var cf = new ChannelFactory<IProblemSolverChannel>(
    new NetTcpRelayBinding(),
    new EndpointAddress(ServiceBusEnvironment.CreateServiceUri("sb", "namespace", "solver")));

cf.Endpoint.Behaviors.Add(new TransportClientEndpointBehavior
            { TokenProvider = TokenProvider.CreateSharedAccessSignatureTokenProvider("RootManageSharedAccessKey","yourKey") });

using (var ch = cf.CreateChannel())
{
    Console.WriteLine(ch.AddNumbers(4, 5));
}
```

您現在可以建置用戶端和服務、執行它們 (先執行服務)，然後用戶端將呼叫此服務並列印 **9**。 您可以在不同機器上 (即使是在不同網路上) 執行用戶端和伺服器，通訊仍然可以運作。 您也可以在雲端或在本機上執行用戶端程式碼。

#### 在 App.config 檔案中設定用戶端

下列程式碼示範如何使用 App.config 檔案設定用戶端。

```
var cf = new ChannelFactory<IProblemSolverChannel>("solver");
using (var ch = cf.CreateChannel())
{
    Console.WriteLine(ch.AddNumbers(4, 5));
}
```

端點定義移入 App.config 檔案。 下列範例 (與上述程式碼相同) 應會出現在 **system.serviceModel** 元素的正下方。 這裡，
您必須使用您的服務匯流排命名空間和 SAS 金鑰來取代預留位置。

```
<client>
    <endpoint name="solver" contract="Service.IProblemSolver"
              binding="netTcpRelayBinding"
              address="sb://namespace.servicebus.windows.net/solver"
              behaviorConfiguration="sbTokenProvider"/>
</client>
<behaviors>
    <endpointBehaviors>
        <behavior name="sbTokenProvider">
            <transportClientEndpointBehavior>
                <tokenProvider>
                    <sharedAccessSignature keyName="RootManageSharedAccessKey" key="yourKey" />
                </tokenProvider>
            </transportClientEndpointBehavior>
        </behavior>
    </endpointBehaviors>
</behaviors>
```

## 後續步驟

了解基本的服務匯流排轉送服務之後，請參考下列連結以取得更多資訊。

- [Azure 服務匯流排架構概觀](fundamentals-service-bus-hybrid-solutions.md)
- [如何使用服務匯流排轉送服務](service-bus-dotnet-how-to-use-relay.md)
- 下載服務匯流排範例從 [Azure 範例 []][] 或參閱 [服務匯流排的概觀範例 []][]。


[create a service namespace]: #create_namespace 
[obtain the default management credentials for the namespace]: #obtain_credentials 
[get the service bus nuget package]: #get_nuget_package 
[how to: use service bus to expose and consume a soap web service  with tcp]: #how_soap 
[azure classic portal]: http://manage.windowsazure.com 
[shared access signature authentication with service bus]: service-bus-shared-access-signature-authentication.md 
[azure samples]: https://code.msdn.microsoft.com/site/search?query=service%20bus&f%5B0%5D.Value=service%20bus&f%5B0%5D.Type=SearchText&ac=2 
[overview of service bus samples]: service-bus-samples.md 

