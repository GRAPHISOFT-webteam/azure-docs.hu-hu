<properties
   pageTitle="設定 Service Fabric 叢集支援的安全連線 | Microsoft Azure"
   description="了解如何使用 Visual Studio 來設定 Azure Service Fabric 叢集支援的安全連線。"
   services="service-fabric"
   documentationCenter="na"
   authors="cawaMS"
   manager="paulyuk"
   editor="tglee" />

<tags
   ms.service="multiple"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="multiple"
   ms.date="10/08/2015"
   ms.author="cawaMS" />

# 從 Visual Studio 設定對 Service Fabric 叢集的安全連線

深入了解如何使用 Visual Studio 來安全地存取已設定存取控制原則的 Service Fabric 叢集。

## 叢集連線類型

Azure Service Fabric 叢集所支援的連線兩種類型 ︰ **不安全** 連線和 **x509 憑證為基礎** 安全連線。 (如 Service Fabric 叢集裝載在內部， **Windows** 和 **dSTS** 也支援驗證。)建立叢集時，您必須設定叢集連線類型。 建立之後，即無法變更連線類型。

「Visual Studio Service Fabric 工具」支援所有用於連線到叢集來進行發行的驗證類型。 請參閱 [從 Azure 入口網站設定 Service Fabric 叢集](service-fabric-cluster-creation-via-portal.md) 如需如何設定安全的 Service Fabric 叢集的指示。

## 在發行設定檔中設定叢集連接

如果您要發行從 Visual Studio 中，Service Fabric 專案 **發行** 對話方塊可讓您選擇的 Azure Service Fabric 叢集，方法是按一下 **選取** 按鈕 **連接端點** 一節。 您可以登入您的 Azure 帳戶，然後選取您訂用帳戶底下現有的叢集。

![[發行] 對話方塊可讓使用者設定 Service Fabric 連線][publishdialog]

 **選取 Service Fabric 叢集** 對話方塊會自動驗證叢集的連線。 如果通過驗證，即表示您的系統已安裝正確的憑證，可安全地連線到叢集，否則即表示您的叢集不安全。 驗證失敗的原因可能是網路問題，或是您的系統尚未正確設定來連線到安全的叢集。

![[選取 Service Fabric 叢集] 對話方塊可讓使用者選擇現有的或建立新的 Service Fabric 叢集連線來設定連線][selectsfcluster]

### 連線到安全的叢集

1.  請確定您可以存取目的地叢集所信任的其中一個用戶端憑證。 憑證通常是以「個人資訊交換」(.pfx) 檔案的形式共用。 請參閱 [從 Azure 入口網站設定 Service Fabric 叢集](service-fabric-cluster-creation-via-portal.md) 如何設定授與存取權的用戶端的伺服器。

2.  安裝受信任的憑證。 若要這樣做，請按兩下.pfx 檔案，或使用 PowerShell 指令碼 Import-PfxCertificate 來匯入憑證。 若要將憑證安裝 **Cert: \LocalMachine\My** 位置。 匯入憑證時，可以接受所有預設設定。

3.  選擇 **發佈...** 命令來開啟專案的捷徑功能表上 **發行 Azure 應用程式** ] 對話方塊中，然後選取目標叢集。 此工具會自動解析連線，並將安全連線參數儲存在發行設定檔中。

4.  [選擇性]：您可以編輯發行設定檔以指定安全的叢集連線。

    由於您正手動編輯「發行設定檔」XML 檔案以指定憑證資訊，因此請務必記下憑證存放區名稱、存放區位置，以及憑證指紋。 您將必須為憑證的存放區名稱和存放區位置提供這些值。 請參閱 [How to ︰ 擷取憑證的指紋](https://msdn.microsoft.com/library/ms734695(v=vs.110).aspx) 如需詳細資訊。

     *ClusterConnectionParameters* 參數可讓您指定連接至 Service Fabric 叢集時要使用的 PowerShell 參數。 有效的參數是 Connect-ServiceFabricCluster Cmdlet 所接受的任何參數。 請參閱 [Connect-servicefabriccluster](https://msdn.microsoft.com/library/mt125938.aspx) 可用參數的清單。

    如果要發行至遠端叢集，您需要指定該特定叢集的適當參數。 以下是連線到非安全叢集的範例：

    `<ClusterConnectionParameters ConnectionEndpoint="mycluster.westus.cloudapp.azure.com:19000" />`

    以下是連線到 x509 憑證型安全叢集的範例：

    ```
    <ClusterConnectionParameters
    ConnectionEndpoint="mycluster.westus.cloudapp.azure.com:19000"
    X509Credential="true"
    ServerCertThumbprint="0123456789012345678901234567890123456789"
    FindType="FindByThumbprint"
    FindValue="9876543210987654321098765432109876543210"
    StoreLocation="CurrentUser"
    StoreName="My" />
    ```

5.  編輯任何其他必要的設定，例如升級參數和應用程式參數的檔案位置，並發行您的應用程式從 **發行的 Service Fabric 應用程式** Visual Studio 中的對話方塊。

## 後續步驟
如需有關如何存取 Service Fabric 叢集的詳細資訊，請參閱 [使用 Service Fabric 總管視覺化叢集](service-fabric-visualizing-your-cluster.md)。

<!--Image references-->
[publishdialog]:./media/service-fabric-visualstudio-configure-secure-connections/publishdialog.png
[selectsfcluster]:./media/service-fabric-visualstudio-configure-secure-connections/selectsfcluster.png

