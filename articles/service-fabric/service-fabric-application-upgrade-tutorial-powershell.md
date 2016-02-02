<properties
   pageTitle="使用 PowerShell 進行 Service Fabric 應用程式升級 | Microsoft Azure"
   description="本文會逐步解說使用 PowerShell 來部署 Service Fabric 應用程式、變更程式碼及執行升級的體驗。"
   services="service-fabric"
   documentationCenter=".net"
   authors="mani-ramaswamy"
   manager="timlt"
   editor=""/>

<tags
   ms.service="service-fabric"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="NA"
   ms.date="07/17/2015"
   ms.author="subramar"/>




# 使用 PowerShell 進行 Service Fabric 應用程式升級

最常使用和建議的升級方法是監視輪流升級。 Service Fabric 會根據健康狀態原則集，監視正在升級之應用程式的健康狀態。 當升級網域 (UD) 中的應用程式已升級時，Service Fabric 會評估應用程式健康狀態，並決定是否要繼續進行下一個升級網域，或根據健康狀態原則讓升級失敗。 可以使用受管理或原生 API、PowerShell 或 REST，執行監視應用程式升級。 如需使用 Visual Studio 升級的指示，請參閱 [都使用 Visual Studio 應用程式](service-fabric-application-upgrade-tutorial.md)。

Service Fabric 監視輪流升級可以讓應用程式系統管理員設定 Service Fabric 用來判斷應用程式健康狀態良好的健康狀態評估原則。 此外，它也可讓系統管理員設定當健康狀態評估失敗時採取的動作，例如自動回復。 本節會逐步解說使用 PowerShell 對其中一個 SDK 範例進行受監視的升級。

## 步驟 1：建置和部署視覺物件範例

若要執行這些步驟，請從 github 下載應用程式，然後依範例的讀我檔案所述，將 **webgl-utils.js** 和 **gl-matrix-min.js** 檔案新增至專案。 不這麼做的話，應用程式將無法運作。 將這些檔案新增至專案之後，在應用程式專案 **VisualObjectsApplication** 上按一下滑鼠右鍵，然後選取 Service Fabric 功能表項目中的 [發行] 命令來建置和發行應用程式，如以下所示。 請參閱 [服務網狀架構應用程式升級教學課程](service-fabric-application-tutorial.md) 如需詳細資訊。 或者，您也可以使用 PowerShell 來部署您的應用程式。
> [AZURE.NOTE] 在 PowerShell 中使用任何 Service Fabric 命令之前，必須先使用連線到叢集 `Connect-servicefabriccluster` 指令程式。 同樣地，它會假設已經在本機電腦上設定叢集。 請參閱 [設定 Service Fabric 開發環境](service-fabric-get-started.md)。

在 Visual Studio 中建置專案之後，使用者會使用 PowerShell 命令 **Copy-ServiceFabricApplicationPackage**，將應用程式封裝複製到 ImageStore，然後藉由使用 **Register-ServiceFabricApplicationPackage** Cmdlet，將應用程式註冊至 Service Fabric 執行階段，最後，使用 **New-ServiceFabricApplication** Cmdlet 來啟動應用程式的執行個體。 這三個步驟類似於在 Visual Studio 中使用 [部署] 功能表項目。

現在，您可以使用 [Service Fabric 總管來檢視叢集與應用程式](service-fabric-visualizing-your-cluster.md)。 應用程式有 web 服務，可以瀏覽至 Internet Explorer 中輸入 [http://localhost:8081/visualobjects](http://localhost:8081/visualobjects) 網址列中。 您應該會在畫面上看到一些浮動視覺物件四處移動。 此外，使用者可能會使用 **Get-ServiceFabricApplication** 檢查應用程式狀態。

## 步驟 2：更新視覺物件範例

您可能會注意到在步驟 1 中已部署的版本，視覺物件不會旋轉。 讓我們將這個應用程式升級到其中的視覺物件也會旋轉的版本。

選取 VisualObjects 解決方案內的 VisualObjects.ActorService 專案，然後開啟 StatefulVisualObjectActor.cs 檔案。 在該檔案內瀏覽至方法 `MoveObject`, ，並註解化 `這。State.Move()` 並取消註解 `這。State.Move(true)`。 這項變更會使得物件在服務升級後能夠旋轉。

我們也需要更新 **VisualObjects.ActorService** 專案的 *ServiceManifest.xml* 檔案 (在 [PackageRoot] 底下)。 請將 *ServiceManifest.xml* 檔案中的 *CodePackage* 和服務版本及對應的行更新成 2.0。
您可以使用 Visual Studio *編輯資訊清單檔案* 選項之後，您以滑鼠右鍵按一下方案，讓資訊清單檔案的變更-請參閱 [服務網狀架構應用程式升級教學課程](service-fabric-application-tutorial.md)。


進行變更之後，資訊清單應該會看起來如下 (醒目提示的部分即為所做的變更)：

```xml
<ServiceManifestName="VisualObjects.ActorService"Version="2.0"xmlns="http://schemas.microsoft.com/2011/01/fabric"xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance">

<CodePackageName="Code"Version="2.0">
```

現在，我們需要更新 *ApplicationManifest.xml* 檔案 (可以在 **VisualObjects** 解決方案底下的 **VisualObjects** 專案底下找到)，以使用版本 2.0 的 **VisualObjects.ActorService** 專案，並且將應用程式版本一併從 1.0.0.0 更新成 2.0.0.0。 現在，*ApplicationManifest.xml* 中的對應程式碼行應該會如下所示：

```xml
<ApplicationManifestxmlns:xsd="http://www.w3.org/2001/XMLSchema"xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"ApplicationTypeName="VisualObjects"ApplicationTypeVersion="2.0.0.0"xmlns="http://schemas.microsoft.com/2011/01/fabric">

 <ServiceManifestRefServiceManifestName="VisualObjects.ActorService"ServiceManifestVersion="2.0" />
```


現在建置專案，方法是只選取 **ActorService** 專案，然後以滑鼠右鍵按一下並選取 Visual Studio 中的組建 (如果您選取全部重新建置，您可能必須在其 *ServiceManifest.xml* 和 *ApplicationManifest.xml* 中更新其他專案的版本，因為程式碼已變更)。 現在，讓我們在 [VisualObjectsApplication]** 上按一下滑鼠右鍵，選取 [Service Fabric] 功能表，然後選擇 [封裝]，來封裝已更新的應用程式。 這應該會建立可部署的應用程式封裝。 更新的應用程式已準備好進行部署。


## 步驟 3: 決定健康原則，並升級參數

請讓您熟悉 [應用程式升級參數](service-fabric-application-upgrade-parameters.md) 和 [升級程序](service-fabric-application-upgrade.md) 以充分了解各種升級參數、 逾時和健康狀態準則套用。 對於此逐步解說，我們會將服務健康狀態評估準則保留為預設值 (和建議值)，這表示升級之後，所有服務和執行個體應該是_健康狀態良好_。 但是，讓我們將 *HealthCheckStableDuration* 增加為 60 秒 (如此一來，在升級繼續至下一個升級網域之前，至少有 20 秒的時間服務是健康狀態良好的)。 同時也要將 *UpgradeDomainTimeout* 設為 1200 秒，將 *UpgradeTimeout* 設為 3000 秒。 最後，我們也要將 *UpgradeFailureAction* 設為回復，以要求 Service Fabric 在升級期間遇到任何問題時，將應用程式回復為舊版。 因此，開始升級時我們指定的升級參數 (在步驟 4) 如下所示：

FailureAction = Rollback

HealthCheckStableDurationSec = 60

UpgradeDomainTimeoutSec = 1200

UpgradeTimeout = 3000


## 步驟 4：準備應用程式以進行升級

現在，應用程式已建置並且準備好進行升級。 如果您以系統管理員身分開啟 PowerShell 視窗並且輸入 **Get-ServiceFabricApplication**，它應該會讓您知道它是已部署之 **VisualObjects** 的應用程式類型 1.0.0.0。 應用程式封裝儲存在以下的相對路徑，您在其中解壓縮 Service Fabric SDK - *Samples\Services\Stateful\VisualObjects\VisualObjects\obj\x64\Debug*。 您應該會在該目錄中找到 "Package" 資料夾 - 這是應用程式封裝儲存的位置。 請檢查時間戳記以確保它是最新組建 (您也可能需要適當地修改路徑)。

現在讓我們將更新的應用程式封裝複製到 Service Fabric ImageStore (Service Fabric 在其中儲存應用程式封裝)。 參數 *ApplicationPackagePathInImageStore* 會通知 Service Fabric 可以在哪裡找到應用程式封裝。 我們有使用下列命令 (您可能需要再次適當地修改路徑)，以將更新的應用程式放在 「 VisualObjects\_V2 」。

```powershell
Copy-ServiceFabricApplicationPackage  -ApplicationPackagePath .\Samples\Services\Stateful\VisualObjects\VisualObjects\obj\x64\Debug\Package
-ImageStoreConnectionString fabric:ImageStore   -ApplicationPackagePathInImageStore "VisualObjects\_V2"
```

下一步是將此應用程式註冊至 Service Fabric，可以使用下列命令來執行：

```powershell
Register-ServiceFabricApplicationType -ApplicationPathInImageStore "VisualObjects\_V2"
```

如果上述命令不成功，很可能是您需要重新建置所有服務。 如步驟 2 中所述，您可能也必須更新您的 WebService 版本。

## 步驟 5：開始應用程式升級

現在，我們已經準備好使用下列命令開始應用程式升級：

```powershell
Start-ServiceFabricApplicationUpgrade -ApplicationName fabric:/VisualObjects -ApplicationTypeVersion 2.0.0.0 -HealthCheckStableDurationSec 60 -UpgradeDomainTimeoutSec 1200 -UpgradeTimeout 3000   -FailureAction Rollback -Monitored
```


請注意，應用程式名稱如 *ApplicationManifest.xml* 檔案中所述。 Service Fabric 會使用這個名稱來識別要升級哪一個應用程式。 如果您設定的逾時太短，您可能會遇到失敗訊息，指出此問題。 請參閱疑難排解章節，或增加逾時。

現在，應用程式升級會繼續，您可以使用 Service Fabric 總管或使用下列 PowerShell 命令進行監視：**Get-ServiceFabricApplicationUpgrade fabric:/VisualObjects**。

幾分鐘後，使用上述 PowerShell 命令的狀態應該會顯示已升級所有升級網域 (完成)。 而且，您應該會在瀏覽器視窗中發現該視覺物件現在已經開始旋轉！

您可能想要嘗試變更版本，從第 2 版移至第 3 版做為練習，或者甚至是從第 2 版移回第 1 版 (是的，您可以從 v2 升級至 v1)。 練習逾時和健康狀態原則，讓自己更熟練。 當您部署至 Azure 叢集時，所使用的參數將會不同於部署至本機叢集時使用的參數 - 建議保守地設定逾時。


## 後續步驟

[都您應用程式使用 Visual Studio](service-fabric-application-upgrade-tutorial.md) 逐步引導您完成使用 Visual Studio 的應用程式升級。

控制您的應用程式所使用的升級方式 [升級參數](service-fabric-application-upgrade-parameters.md)。

學習如何使用，讓應用程式升級相容 [資料序列化](service-fabric-application-upgrade-data-serialization.md)。

了解如何使用進階的功能，同時升級您的應用程式藉由參考 [進階主題](service-fabric-application-upgrade-advanced.md)。

藉由參考中的步驟修正應用程式升級的一般問題 [疑難排解應用程式升級 ](service-fabric-application-upgrade-troubleshooting.md)。






