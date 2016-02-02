<properties
   pageTitle="了解 Service Fabric 應用程式 RunAs 安全性原則 |Microsoft Azure"
   description="如何以系統和本機安全性帳戶執行 Service Fabric 應用程式的概觀，包括應用程式在啟動前需要執行某些特殊權限動作的 SetupEntry 點"
   services="service-fabric"
   documentationCenter=".net"
   authors="msfussell"
   manager="timlt"
   editor="bscholl"/>

<tags
   ms.service="service-fabric"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="NA"
   ms.date="11/24/2015"
   ms.author="mfussell"/>


# RunAs：使用不同的安全性權限執行 Service Fabric 應用程式

Service Fabric 能夠保護在叢集中以不同的使用者帳戶執行 (稱為 "RunAs") 的應用程式。 此外，也可保護具有此使用者帳戶的應用程式所使用的資源，例如檔案、目錄和憑證。

根據預設，Service Fabric 應用程式會在用以執行 Fabric.exe 程序的帳戶之下執行。 此外，它也能夠以本機使用者帳戶 (指定於應用程式的資訊清單) 執行應用程式。 RunAs 支援的帳戶類型為 **LocalUser**、**NetworkService**、**LocalService** 和 **LocalSystem**。
> [AZURE.NOTE] 可使用 Active Directory 的 Windows Server 部署支援網域帳戶。 

您可以定義和建立使用者群組，然後將一或多個使用者新增至此群組以便一起管理。 如果不同的服務進入點有多個使用者，而且他們需要有可在群組層級取得的某些常見權限，這是特別有用。

## 設定 SetupEntryPoint 的 RunAs 原則

中所述 [應用程式模型](service-fabric-application-model.md)  **SetupEntryPoint** 是相同的認證與 Service Fabric 執行特殊權限的進入點 (通常 *網路* 帳戶) 之前的任何其他進入點。 **EntryPoint** 指定的可執行檔通常是長時間執行的服務主機，所以有個別的安裝進入點可避免以高度權限執行服務主機執行檔延長的時間期間。 **EntryPoint** 指定的可執行檔是在 **SetupEntryPoint** 成功結束之後執行。 如果曾經終止或當機，產生的程序會監視並重新啟動 (以 **SetupEntryPoint** 再次開始)。

以下簡單的範例服務資訊清單顯示服務的 SetupEntryPoint 和主要 EntryPoint。

~~~
<?xml version="1.0" encoding="utf-8" ?>
<ServiceManifest Name="MyServiceManifest" Version="SvcManifestVersion1" xmlns="http://schemas.microsoft.com/2011/01/fabric" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance">
  <Description>範例服務資訊清單</Description>
  <ServiceTypes>
    <StatelessServiceType ServiceTypeName="MyServiceType" />
  </ServiceTypes>
  <CodePackage Name="Code" Version="1.0.0">
    <SetupEntryPoint>
      <ExeHost>
        <Program>Setupentrypoint</Program>
      </ExeHost>
    </SetupEntryPoint>
    <EntryPoint>
      <ExeHost>
        <Program>MyServiceHost.exe</Program>
      </ExeHost>
    </EntryPoint>
  </CodePackage>
  <ConfigPackage Name="Config" Version="1.0.0" />
</ServiceManifest>
~~~

### 設定 RunAs 原則

設定服務來擁有 SetupEntryPoint，您可以變更下執行應用程式資訊清單中的安全性權限。 下列範例示範如何將服務設定成以系統管理員帳戶權限執行。

~~~
<?xml version="1.0" encoding="utf-8"?>
<ApplicationManifest xmlns:xsd="http://www.w3.org/2001/XMLSchema" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" ApplicationTypeName="MyApplicationType" ApplicationTypeVersion="1.0.0" xmlns="http://schemas.microsoft.com/2011/01/fabric">
   <ServiceManifestImport>
      <ServiceManifestRef ServiceManifestName="MyServiceTypePkg" ServiceManifestVersion="1.0.0" />
      <ConfigOverrides />
      <Policies>
         <RunAsPolicy CodePackageRef="Code" UserRef="SetupAdminUser" EntryPointType="Setup" />
      </Policies>
   </ServiceManifestImport>
   <Principals>
      <Users>
         <User Name="SetupAdminUser">
            <MemberOf>
               <SystemGroup Name="Administrators" />
            </MemberOf>
         </User>
      </Users>
   </Principals>
</ApplicationManifest>
~~~

首先，以使用者名稱 (例如 SetupAdminUser) 建立 **Principals** 區段。 這表示使用者是 Administrators 系統群組的成員。

接著在 **ServiceManifestImport** 區段下設定原則，以將此主體套用至 **SetupEntryPoint**。 這會告知 Service Fabric，執行 MySetup.bat 檔案執行時，它應該是具有系統管理員權限的 RunAs。 假設您尚未**將原則套用至主要進入點，則 MyServiceHost.exe 中的程式碼將會以系統 NetworkService 帳戶 (這是預設帳戶，其所有服務進入點都是 RunAs 帳戶) 執行。

讓我們現在將 MySetup.bat 檔案加入至 Visual Studio 專案，以便測試系統管理員權限。 在 Visual Studio 中，以滑鼠右鍵按一下服務專案並加入名為 MySetup.bat 的新檔案。 接下來就必須確保這個檔案包含在服務封裝內 (這並非預設)。 若要確保 MySetup.bat 檔案已包含在封裝內，請選取此檔案，以滑鼠右鍵按一下以取得內容功能表，選擇 [屬性] 並在 [屬性] 對話方塊中確定 [複製到輸出目錄]**** 設為 [有更新時才複製]****。 如以下螢幕擷取畫面所示。

![Visual Studio CopyToOutput for SetupEntryPoint 批次檔][image1]

現在開啟 MySetup.bat 檔案並加入下列命令。

~~~
REM 設定系統環境變數。 這需要系統管理員權限
setx m TestVariable"MyValue"
回應系統 TestVariable 設 > test.txt
echo %testvariable%>> test.txt

REM 若要刪除此系統變數我們
REM REG delete"HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Control\Session Manager\Environment"/v TestVariable /f
~~~

接下來建置解決方案並部署至本機開發叢集。 一旦啟動服務，如在 Service Fabric 總管中所見，您可以看到 MySetup.bat 成功的方式有兩種。 開啟 PowerShell 命令提示字元並輸入

~~~
PS C:\[Environment]::GetEnvironmentVariable("TestVariable","Machine")
MyValue
~~~

接著，在 Service Fabric 總管中記下部署和啟動服務的節點名稱 (例如節點 1)，然後瀏覽至應用程式執行個體工作資料夾，尋找可顯示 **TestVariable** 值的 out.txt 檔案。 例如，如果此服務已部署至節點 2，您可以移至 MyApplicationType 的這個路徑

~~~
C:\SfDevCluster\Data\_App\Node.2\MyApplicationType_App\work\out.txt
~~~

## 從 SetupEntryPoint 啟動 PowerShell 命令

若要從 **SetupEntryPoint** 執行Power Shell，您可以在指向 PowerShell 檔案的批次檔中執行 PowerShell.exe。 先將 PowerShell 檔案加入至服務專案 (例如 MySetup.ps1)。 請記得設定 [有更新時才複製]** 屬性，讓這個檔案也包含在服務封裝內。 下列範例顯示的範例批次檔可啟動名為 MySetup.ps1 的 PowerShell 檔案，以設定名為 *TestVariable* 的系統環境變數。

MySetup.bat 可啟動 PowerShell 檔案。

~~~
略過 powershell.exe ExecutionPolicy-「.\MySetup.ps1 」 命令
~~~

在 PowerShell 檔案中，加入以下項目來設定系統環境變數。

```
[Environment]::GetEnvironmentVariable("TestVariable","Machine") > out.txt
```

## 將 RunAs 原則套用到服務

在上述步驟中，您會看到如何將 RunAs 原則套用到 SetupEntryPoint。 讓我們深入了解如何建立可當作服務原則套用的不同主體。

### 建立本機使用者群組

您可以定義和建立使用者群組，然後將一或多個使用者新增至此群組。 如果不同的服務進入點有多個使用者，而且他們需要有可在群組層級取得的某些常見權限，這是特別有用。 下列範例顯示名為 **LocalAdminGroup** 且具有系統管理員權限的本機群組。 Customer1 和 Customer2 這兩個使用者會成為此本機群組的成員。

~~~
<Principals>
 <Groups>
   <Group Name="LocalAdminGroup">
     <Membership>
       <SystemGroup Name="Administrators"/>
     </Membership>
   </Group>
 </Groups>
  <Users>
     <User Name="Customer1">
        <MemberOf>
           <Group NameRef="LocalAdminGroup" />
        </MemberOf>
     </User>
    <User Name="Customer2">
      <MemberOf>
        <Group NameRef="LocalAdminGroup" />
      </MemberOf>
    </User>
  </Users>
</Principals>
~~~

### 建立本機使用者

您可以建立一個本機使用者，以便用來保護應用程式內的服務。 在應用程式資訊清單的 [主體] 區段中指定 LocalUser 帳戶類型時，Service Fabric 會在應用程式部署所在的電腦上建立本機使用者帳戶。 根據預設，這些帳戶沒有如應用程式資訊清單中指定的相同名稱 (例如下列範例中的 "Customer3")，但是會以動態方式產生名稱並具有隨機密碼。

~~~
<Principals>
  <Users>
     <User Name="Customer3" AccountType="LocalUser" />
  </Users>
</Principals>
~~~



## 將原則指派給服務程式碼封裝

**ServiceManifestImport** 的 **RunAsPolicy** 區段可指定 Principals 區段中應該用來執行程式碼封裝的帳戶，並可讓服務資訊清單中的程式碼封裝與 Principals 區段中的帳戶產生關聯。 您可以為 Setup 或 Main 進入點指定此原則，或指定 [全部] 將此原則套用到兩者。 下列範例顯示套用不同的原則。

~~~
<Policies>
<RunAsPolicy CodePackageRef="Code" UserRef="LocalAdmin" EntryPointType="Setup"/>
<RunAsPolicy CodePackageRef="Code" UserRef="Customer3" EntryPointType="Main"/>
</Policies>
~~~

如果未指定 **EntryPointType**，則預設值會設定為 EntryPointType ="Main"。 當您想要以系統帳戶執行某些高權限設定作業時，指定 **SetupEntryPoint** 特別有用，然而實際服務程式碼可以在較低權限的帳戶下執行。

### 將預設原則套用到所有服務程式碼封裝

**DefaultRunAsPolicy** 區段用來針對未定義特定 **RunAsPolicy** 的所有程式碼封裝，指定預設使用者帳戶。 如果在應用程式所用的服務資訊清單中指定的大部分程式碼封裝必須以相同的 RunAs 使用者執行，則應用程式可以只定義該使用者帳戶的預設 RunAs 原則，而不需針對每個程式碼封裝指定 **RunAsPolicy**。 舉例來說，下列範例指定如果程式碼封裝未指定 **RunAsPolicy**，則此程式碼封裝應該以 Principals 區段中指定的 MyDefaultAccount 執行。

~~~
<Policies>
  <DefaultRunAsPolicy UserRef="MyDefaultAccount"/>
</Policies>
~~~

## 為 http 和 https 端點指派 SecurityAccessPolicy

如果您將 RunAs 原則套用到服務，而服務資訊清單宣告具有 http 通訊協定的端點資源，您必須指定 **SecurityAccessPolicy** 以確保配置給這些端點的連接埠，都已針對用以執行服務的 RunAs 使用者帳戶正確列入存取控制清單 (ACL) 中。 否則，http.sys 無法存取服務，而您從用戶端呼叫時會失敗。 下列範例將 Customer3 帳戶套用到名為 *ServiceEndpointName* 的端點，並給予完整的存取權限。

~~~
<Policies>
   <RunAsPolicy CodePackageRef="Code" UserRef="Customer1" />
   
   <SecurityAccessPolicy ResourceRef="EndpointName" PrincipalRef="Customer1" />
</Policies>
~~~

對於 https 端點，您必須透過已在應用程式資訊清單的 certificates 區段中定義憑證的 **EndpointBindingPolicy**，額外指出要傳回給用戶端的憑證名稱。

~~~
<Policies>
   <RunAsPolicy CodePackageRef="Code" UserRef="Customer1" />
  
   <SecurityAccessPolicy ResourceRef="EndpointName" PrincipalRef="Customer1" />
  
  <EndpointBindingPolicy EndpointRef="EndpointName" CertificateRef="Cert1" />
< / 原則
~~~


## 完整的應用程式資訊清單範例

下列應用程式資訊清單顯示上述許多不同的設定。

~~~
<?xml version="1.0" encoding="utf-8"?>
<ApplicationManifest xmlns:xsd="http://www.w3.org/2001/XMLSchema" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" ApplicationTypeName="Application3Type" ApplicationTypeVersion="1.0.0" xmlns="http://schemas.microsoft.com/2011/01/fabric">
   <Parameters>
      <Parameter Name="Stateless1_InstanceCount" DefaultValue="-1" />
   </Parameters>
   <ServiceManifestImport>
      <ServiceManifestRef ServiceManifestName="Stateless1Pkg" ServiceManifestVersion="1.0.0" />
      <ConfigOverrides />
      <Policies>
         <RunAsPolicy CodePackageRef="Code" UserRef="Customer1" />
         <RunAsPolicy CodePackageRef="Code" UserRef="LocalAdmin" EntryPointType="Setup" />
        
         <SecurityAccessPolicy ResourceRef="EndpointName" PrincipalRef="Customer1" />
        
        <EndpointBindingPolicy EndpointRef="EndpointName" CertificateRef="Cert1" />
     </Policies>
   </ServiceManifestImport>
   <DefaultServices>
      <Service Name="Stateless1">
         <StatelessService ServiceTypeName="Stateless1Type" InstanceCount="[Stateless1_InstanceCount]">
            <SingletonPartition />
         </StatelessService>
      </Service>
   </DefaultServices>
   <Principals>
      <Groups>
         <Group Name="LocalAdminGroup">
            <Membership>
               <SystemGroup Name="Administrators" />
            </Membership>
         </Group>
      </Groups>
      <Users>
         <User Name="LocalAdmin">
            <MemberOf>
               <Group NameRef="LocalAdminGroup" />
            </MemberOf>
         </User>
         
         <User Name="Customer1" />
         <User Name="MyDefaultAccount" AccountType="NetworkService" />
      </Users>
   </Principals>
   <Policies>
      <DefaultRunAsPolicy UserRef="LocalAdmin" />
   </Policies>
   <Certificates>
     <EndpointCertificate Name="Cert1" X509FindValue="FF EE E0 TT JJ DD JJ EE EE XX 23 4T 66 "/>
  </Certificates>
</ApplicationManifest>
~~~



## 後續步驟

* [了解應用程式模型](service-fabric-application-model.md)
* [在服務資訊清單中指定的資源](service-fabric-service-manifest-resources.md)
* [部署應用程式](service-fabric-deploy-remove-applications.md)


[environment]: :SetEnvironmentVariable("TestVariable", "MyValue", "Machine""
[image1]: ./media/service-fabric-application-runas-security/copy-to-output.png 

