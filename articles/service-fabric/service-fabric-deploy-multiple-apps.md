<properties
   pageTitle="使用 MongoDB 部署 Node.js 應用程式 | Microsoft Azure"
   description="如何封裝多個應用程式以部署至 Azure Service Fabric 叢集的逐步解說"
   services="service-fabric"
   documentationCenter=".net"
   authors="bmscholl"
   manager=""
   editor=""/>

<tags
   ms.service="service-fabric"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="NA"
   ms.date="11/17/2015"
   ms.author="bscholl"/>


# 部署多個自訂應用程式

這篇文章說明如何封裝及部署多個 Service Fabric 應用程式使用 Service Fabric 封裝工具，可在 http://aka.ms/servicefabricpacktool 的預覽版本。

如手動建立 Service Fabric 封裝，請參閱文章 [部署現有的應用程式在 Azure Service Fabric](service-fabric-deploy-existing-app.md)。

雖然本逐步解說示範的是如何部署使用 MongoDB 做為資料存放區並具有 Node.js 前端的應用程式，但是您可以將這些步驟套用到任何與另一個應用程式具有相依性的應用程式。   

## 封裝 Node.js 應用程式

本文假設 Service Fabric 叢集中的節點上未安裝 Node.js。 因此，您需要在封裝之前，先將 node.exe 新增至您節點應用程式的根目錄。 Node.js 應用程式 (使用 Express Web 架構和 Jade 樣板化引擎) 的目錄結構看起來應該與以下類似：

```
|-- NodeApplication
    |-- bin
        |-- www
    |-- node_modules
        |-- .bin
        |-- express
        |-- jade
        |-- etc.
    |-- public
        |-- images
        |-- etc.
    |-- routes
        |-- index.js
        |-- users.js
    |-- views
        |-- index.jade
        |-- etc.
    |-- app.js
    |-- package.json
    |-- node.exe
```

在下一個步驟中，您將為 Node.js 應用程式建立應用程式封裝。 以下程式碼會建立包含 Node.js 應用程式的 Service Fabric 應用程式封裝。

```
.\ServiceFabricAppPackageUtil.exe /source:'[yourdirectory]\MyNodeApplication' /target:'[yourtargetdirectory] /appname:NodeService /exe:'node.exe' /ma:'bin/www' /AppType:NodeAppType
```

以下是所使用之參數的描述：

- **來源/**: 指向應該一起封裝的應用程式的目錄
- **/target**: 定義的目錄應該在其中建立封裝。 這個目錄必須是與目標目錄不同的目錄。
- **/appname**: 定義現有的應用程式的應用程式名稱。 請務必了解這會轉譯成資訊清單中的「服務名稱」，而不是轉譯成 Service Fabric 應用程式名稱。
- **/exe**: 定義服務網狀架構應該在此情況下啟動的可執行檔 `node.exe`
- **/ma**: 定義用來啟動可執行檔的引數。 如未安裝 Node.js，需要啟動 Node.js web 伺服器執行 Service Fabric `node.exe bin/www`。  `/ma:'bin/www'` 會告訴封裝工具使用 `bin/ma` 做為 node.exe 引數
- **/ AppType**: 定義 Service Fabric 應用程式的型別名稱。 如果您

如果您瀏覽至 /target 參數中指定的目錄，您可以看到工具已建立可完整運作的 Service Fabric 封裝，如以下所示：

```
|--[yourtargetdirectory]
    |-- NodeApplication
        |-- C
              |-- bin
              |-- data
              |-- node_modules
              |-- public
              |-- routes
              |-- views
              |-- app.js
              |-- package.json
              |-- node.exe
        |-- config
              |--Settings.xml
        |-- ServiceManifest.xml
    |-- ApplicationManifest.xml
```
所產生的 ServiceManifest.xml 現在有個描述應該如何啟動 Node.js Web 伺服器的區段，如以下程式碼片段所示：

```xml
<CodePackage Name="C" Version="1.0">
    <EntryPoint>
        <ExeHost>
            <Program>node.exe</Program>
            <Arguments>'bin/www'</Arguments>
            <WorkingFolder>CodePackage</WorkingFolder>
        </ExeHost>
    </EntryPoint>
</CodePackage>
```
在此範例中，Node.js Web 伺服器會接聽通訊埠 3000，所以您需要更新 ServiceManifest.xml 中的端點資訊，如以下所示。   

```xml
<Resources>
      <Endpoints>
        <Endpoint Name="NodeServiceEndpoint" Protocol="http" Port="3000" Type="Input" />
      </Endpoints>
</Resources>
```
既然您已封裝 Node.js 應用程式，您可以繼續封裝 MongoDB。 如先前所提到的，您現在進行的步驟並非 Node.js 和 MongoDB 專用的步驟，事實上它們適用於所有要封裝在一起以做為一個 Service Fabric 應用程式的應用程式。  

為了封裝 MongoDB，您會想要確定您封裝 mongod.exe 和 mongo.exe。 這兩個二進位檔都位於 MongoDB 安裝目錄的 `bin` 目錄中。 目錄結構類似於下方的結構。

```
|-- MongoDB
    |-- bin
        |-- mongod.exe
        |-- mongo.exe
        |-- etc.
```
Service Fabric 需要使用類似於下方的命令來啟動 MongoDB，因此封裝 MongoDB 時，您需要使用 `/ma` 參數。

```
mongod.exe --dbpath [path to data]
```
> [AZURE.NOTE] 不會被資料保留在節點失敗的情況下，萬一 MongoDB 資料目錄放在節點本機目錄。 您應該使用永久性儲存體或實作 MongoDB ReplicaSet 以防止資料遺失。  

在 PowerShell 或「命令殼層」中，我們會使用下列參數來執行封裝工具：

```
.\ServiceFabricAppPackageUtil.exe /source: [yourdirectory]\MongoDB' /target:'[yourtargetdirectory]' /appname:MongoDB /exe:'bin\mongod.exe' /ma:'--dbpath [path to data]' /AppType:NodeAppType
```

為了將 MongoDB 新增至您的 Service Fabric 應用程式封裝，您必須確定 /target 參數指向已經包含應用程式資訊清單及 Node.js 應用程式的同一個目錄，並確定您使用相同的 ApplicationType 名稱。

讓我們瀏覽至該目錄並檢查已建立的工具。

```
|--[yourtargetdirectory]
    |-- MyNodeApplication
    |-- MongoDB
        |-- C
            |--bin
                |-- mongod.exe
                |-- mongo.exe
                |-- etc.
        |-- config
            |--Settings.xml
        |-- ServiceManifest.xml
    |-- ApplicationManifest.xml
```
如您所見，工具已將新資料夾 [MongoDB] 新增至包含 MongoDB 二進位檔的目錄。 如果您開啟 `ApplicationManifest.xml` 檔案，您將可以看到封裝現在包含 Node.js 應用程式和 MongoDB。 以下程式碼會顯示應用程式資訊清單的內容。

```xml
<ApplicationManifest xmlns:xsd="http://www.w3.org/2001/XMLSchema" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" ApplicationTypeName="MyNodeApp" ApplicationTypeVersion="1.0" xmlns="http://schemas.microsoft.com/2011/01/fabric">
   <ServiceManifestImport>
      <ServiceManifestRef ServiceManifestName="MongoDB" ServiceManifestVersion="1.0" />
   </ServiceManifestImport>
   <ServiceManifestImport>
      <ServiceManifestRef ServiceManifestName="NodeService" ServiceManifestVersion="1.0" />
   </ServiceManifestImport>
   <DefaultServices>
      <Service Name="MongoDBService">
         <StatelessService ServiceTypeName="MongoDB">
            <SingletonPartition />
         </StatelessService>
      </Service>
      <Service Name="NodeServiceService">
         <StatelessService ServiceTypeName="NodeService">
            <SingletonPartition />
         </StatelessService>
      </Service>
   </DefaultServices>
</ApplicationManifest>  
```

最後一個步驟是要使用以下 PowerShell 指令碼，將應用程式發佈至本機 Service Fabric 叢集：

```
Connect-ServiceFabricCluster localhost:19000

Write-Host 'Copying application package...'
Copy-ServiceFabricApplicationPackage -ApplicationPackagePath '[yourtargetdirectory]' -ImageStoreConnectionString 'file:C:\SfDevCluster\Data\ImageStore' -ApplicationPackagePathInImageStore 'Store\NodeAppType'

Write-Host 'Registering application type...'
Register-ServiceFabricApplicationType -ApplicationPathInImageStore 'Store\NodeAppType'

New-ServiceFabricApplication -ApplicationName 'fabric:/NodeApp' -ApplicationTypeName 'NodeAppType' -ApplicationTypeVersion 1.0  
```

一旦應用程式成功發行到本機叢集，您可以存取我們已經進入 Node.js 應用程式，例如 http://localhost:3000/ 的服務資訊清單中的連接埠上的 Node.js 應用程式。

在本教學課程中，您已看到如何輕鬆地將兩個現有應用程式封裝成一個 Service Fabric 應用程式並將其部署到 Service Fabric，以便讓它能夠從一些 Service Fabric 功能 (例如高可用性和健康情況系統整合) 獲益。

## 後續步驟

了解如何 [封裝單一應用程式手動](service-fabric-deploy-existing-app.md)。

