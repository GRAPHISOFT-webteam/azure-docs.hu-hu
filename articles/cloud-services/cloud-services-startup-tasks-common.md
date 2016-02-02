<properties 
pageTitle="雲端服務的常見啟動工作 | Microsoft Azure" 
description="提供一些常見的啟動工作範例，做為您在雲端服務 Web 角色或背景工作角色中執行的參考。" 
services="cloud-services" 
documentationCenter="" 
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


# 常見的雲端服務啟動工作

本文提供一些常見的啟動工作範例，做為您在雲端服務中執行的參考。 您可以利用啟動工作，在角色啟動之前執行作業。 您可能想要執行的作業包括安裝元件、註冊 COM 元件、設定登錄機碼，或啟動長時間執行的處理序。

請參閱 [這篇文章](cloud-services-startup-tasks.md) 瞭解啟動工作的運作方式，以及如何明確建立的項目，定義啟動工作。

此處的許多工作都使用了以下項目：
>[AZURE.NOTE] 啟動工作不適用於虛擬機器，只適用於雲端服務 Web 和背景工作角色。


## 定義角色啟動前的環境變數

您可以定義整個角色的環境變數加入至服務定義檔中的角色定義的 [執行階段] 項目。

```xml
<ServiceDefinition name="MyService" xmlns="http://schemas.microsoft.com/ServiceHosting/2008/10/ServiceDefinition">
    <WebRole name="WebRole1">
        ...
        <Runtime>
            <Environment>
                <Variable name="MyEnvironmentVariable" value="MyVariableValue" />
            </Environment>
        </Runtime>
    </WebRole>
</ServiceDefinition>
```

如果您需要針對特定工作，不是由其他工作共用，定義的環境變數，您可以使用 [工作] 項目內的 [環境] 項目。

```xml
<ServiceDefinition name="MyService" xmlns="http://schemas.microsoft.com/ServiceHosting/2008/10/ServiceDefinition">
    <WebRole name="WebRole1">
        ...
        <Startup>
            <Task commandLine="Startup.cmd" executionContext="limited" taskType="simple">
                <Environment>
                    <Variable name="MyEnvironmentVariable" value="MyVariableValue" />
                </Environment>
            </Task>
        </Startup>
    </WebRole>
</ServiceDefinition>
```

變數也可以使用 [有效的 Azure XPath 值](https://msdn.microsoft.com/library/azure/hh404006.aspx) 參考部署相關的項目。 而不是使用 `值` 屬性，定義 [RoleInstanceValue] 的子項目。

```xml
<Variable name="PathToStartupStorage">
    <RoleInstanceValue xpath="/RoleEnvironment/CurrentInstance/LocalResources/LocalResource[@name='StartupLocalStorage']/@path" />
</Variable>
```


## 使用 AppCmd.exe 設定 IIS 啟動

[AppCmd.exe](https://technet.microsoft.com/library/jj635852.aspx) 命令列工具可用來管理在 Azure 上啟動的 IIS 設定。 *AppCmd.exe* 提供方便的命令列，可存取 Azure 上啟動工作所使用的組態設定。 使用 *AppCmd.exe*，即可針對應用程式和網站，新增、修改或移除網站設定。

不過，使用 *AppCmd.exe* 做為啟動工作時，需要注意幾件事情：

- 啟動工作可以在重新開機之間執行多次。 例如，角色回收時就可能會發生這種情形。
- 部分 *AppCmd.exe* 動作如果執行超過一次，就可能會產生錯誤。 若嘗試將區段加入 *Web.config* 兩次，就可能會產生錯誤。
- 若啟動工作傳回非零的結束代碼或 **errorlevel**，就會執行失敗。 如果 *AppCmd.exe* 產生錯誤，就會發生這種情形。

基於上述原因，呼叫 *AppCmd.exe* 之後，最好檢查 **errorlevel**，如果您是使用 *.cmd* 檔案包裝 *AppCmd.exe* 的呼叫，可讓這項檢查作業更容易進行。 如果偵測到已知的 **errorlevel** 回應，可以予以忽略，否則就必須回傳。 以下利用範例進一步說明。

所傳回的 errorlevel *AppCmd.exe* 會列在 winerror.h 檔案中，而且也會出現在 [MSDN](https://msdn.microsoft.com/library/windows/desktop/ms681382.aspx)。

### 範例

此範例會在 *Web.config* 檔案中，加入 JSON 的 compression 區段和壓縮項目，以及錯誤處理和記錄。

[ServiceDefinition.csdef] 檔案的相關區段如下所示，其中包括設定 [executionContext](https://msdn.microsoft.com/library/azure/gg557552.aspx#Task) 屬性設定為 `提高權限` 讓 *AppCmd.exe* 足夠的權限設定中的變更 *Web.config* 檔案:

```xml
<ServiceDefinition name="MyService" xmlns="http://schemas.microsoft.com/ServiceHosting/2008/10/ServiceDefinition">
    <WebRole name="WebRole1">
        ...
        <Startup>
            <Task commandLine="Startup.cmd" executionContext="elevated" taskType="simple" />
        </Startup>
    </WebRole>
</ServiceDefinition>
```

Startup.cmd 批次檔會使用 AppCmd.exe，在 *Web.config* 檔案中加入 JSON 的 compression 區段和壓縮項目。 預期的 **errorlevel** = 183 會使用 VERIFY.EXE 命令列程式設為零。 非預期的 errorlevel 會記錄至 StartupErrorLog.txt。

    REM   *** Add a compression section to the Web.config file. ***
    %windir%\system32\inetsrv\appcmd set config /section:urlCompression /doDynamicCompression:True /commit:apphost >> "%TEMP%\StartupLog.txt" 2>&1
    
    REM   ERRORLEVEL 183 occurs when trying to add a section that already exists. This error is expected if this
    REM   batch file were executed twice. This can occur and must be accounted for in a Azure startup
    REM   task. To handle this situation, set the ERRORLEVEL to zero by using the Verify command. The Verify
    REM   command will safely set the ERRORLEVEL to zero.
    IF %ERRORLEVEL% EQU 183 DO VERIFY > NUL
    
    REM   If the ERRORLEVEL is not zero at this point, some other error occurred.
    IF %ERRORLEVEL% NEQ 0 (
        ECHO Error adding a compression section to the Web.config file. >> "%TEMP%\StartupLog.txt" 2>&1
        GOTO ErrorExit
    )
    
    REM   *** Add compression for json. ***
    %windir%\system32\inetsrv\appcmd set config  -section:system.webServer/httpCompression /+"dynamicTypes.[mimeType='application/json; charset=utf-8',enabled='True']" /commit:apphost >> "%TEMP%\StartupLog.txt" 2>&1
    IF %ERRORLEVEL% EQU 183 VERIFY > NUL
    IF %ERRORLEVEL% NEQ 0 (
        ECHO Error adding the JSON compression type to the Web.config file. >> "%TEMP%\StartupLog.txt" 2>&1
        GOTO ErrorExit
    )
    
    REM   *** Exit batch file. ***
    EXIT /b 0
    
    REM   *** Log error and exit ***
    :ErrorExit
    REM   Report the date, time, and ERRORLEVEL of the error.
    DATE /T >> "%TEMP%\StartupLog.txt" 2>&1
    TIME /T >> "%TEMP%\StartupLog.txt" 2>&1
    ECHO An error occurred during startup. ERRORLEVEL = %ERRORLEVEL% >> "%TEMP%\StartupLog.txt" 2>&1
    EXIT %ERRORLEVEL%

## 新增防火牆規則

Azure 實際上擁有兩個防火牆。 第一道防火牆會控制虛擬機器與外界之間的連結。 這會受到 [ServiceDefinition.csdef] 檔案中的 [端點] 項目。

第二道防火牆會控制虛擬機器與該虛擬機器中處理序之間的連結。 這由控制 `netsh advfirewall 防火牆` 命令列工具，是本文的重點。

Azure 會針對在角色內啟動的處理序建立防火牆規則。 例如，在您啟動服務或程式時，Azure 會自動建立必要的防火牆規則，藉此允許該服務與網際網路通訊。 不過，如果您建立的服務是由角色外部的處理序啟動 (例如 COM+ 服務，或是使用 Windows 排程器啟動的程式)，您就必須手動建立防火牆規則以允許存取該服務。 您可以使用啟動工作建立這些防火牆規則。

啟動工作，會建立防火牆規則必須具有 [executionContext ][task] 的 **提高權限**。 將下列啟動工作加入至 [ServiceDefinition.csdef] 檔案。

```xml
<ServiceDefinition name="MyService" xmlns="http://schemas.microsoft.com/ServiceHosting/2008/10/ServiceDefinition">
    <WebRole name="WebRole1">
        ...
        <Startup>
            <Task commandLine="AddFirewallRules.cmd" executionContext="elevated" taskType="simple" />
        </Startup>
    </WebRole>
</ServiceDefinition>
```

若要新增防火牆規則，您必須使用適當 `netsh advfirewall 防火牆` 啟動批次檔中的命令。 在此範例中，啟動工作的 TCP 連接埠 80 需要具備高安全性與加密功能。

    REM   Add a firewall rule in a startup task.
    
    REM   Add an inbound rule requiring security and encryption for TCP port 80 traffic.
    netsh advfirewall firewall add rule name="Require Encryption for Inbound TCP/80" protocol=TCP dir=in localport=80 security=authdynenc action=allow >> "%TEMP%\StartupLog.txt" 2>&1
    
    REM   If an error occurred, return the errorlevel.
    EXIT /B %errorlevel%

## 封鎖特定的 IP 位址

您可以透過修改 IIS **web.config** 檔，並建立命令檔來解除鎖定 **ApplicationHost.config** 檔案的 **ipSecurity** 區段，藉此限制 Azure Web 角色只能存取一組指定的 IP 位址。

首先，建立要在角色啟動時執行的命令檔，以解除鎖定 **ApplicationHost.config** 檔案的 **ipSecurity** 區段。 在 Web 角色的根層級建立名為 **startup** 的新資料夾，然後在此資料夾中建立名為 **startup.cmd** 的批次檔。 將此檔案的屬性設定為**一律複製**，以確實部署此檔案。

將下列啟動工作加入至 [ServiceDefinition.csdef] 檔案。

```xml
<ServiceDefinition name="MyService" xmlns="http://schemas.microsoft.com/ServiceHosting/2008/10/ServiceDefinition">
    <WebRole name="WebRole1">
        ...
        <Startup>
            <Task commandLine="startup.cmd" executionContext="elevated" />
        </Startup>
    </WebRole>
</ServiceDefinition>
```

將此命令加入 **startup.cmd** 檔案:

    %windir%\system32\inetsrv\AppCmd.exe unlock config -section:system.webServer/security/ipSecurity

這可讓 **startup.cmd** 批次檔在 Web 角色每次初始化時一併執行，以確保將必要的 **ipSecurity** 區段解除鎖定。

最後，修改 [system.webServer 區段](http://www.iis.net/configreference/system.webserver/security/ipsecurity#005) 您的 web 角色 **web.config** 檔案以新增一份 IP 位址被授與存取，如下列範例所示:

此範例組態**允許**所有 IP 存取伺服器 (另外定義的 2 個 IP 除外)。

```xml
<system.webServer>
    <security>
    
    <ipSecurity>
        
        <add allowed="false" ipAddress="192.168.100.1" subnetMask="255.255.0.0" />
        <add allowed="false" ipAddress="192.168.100.2" subnetMask="255.255.0.0" />
    </ipSecurity>
    </security>
</system.webServer>
```

此範例組態**拒絕**所有 IP 存取伺服器 (另外定義的 2 個 IP 除外)。

```xml
<system.webServer>
    <security>
    
    <ipSecurity allowUnlisted="false">
        
        <add allowed="true" ipAddress="192.168.100.1" subnetMask="255.255.0.0" />
        <add allowed="true" ipAddress="192.168.100.2" subnetMask="255.255.0.0" />
    </ipSecurity>
    </security>
</system.webServer>
```

## 建立 PowerShell 啟動工作

Windows PowerShell 指令碼不能呼叫直接從 [ServiceDefinition.csdef] 檔案，但它們可以啟動批次檔內叫用的。

根據預設，PowerShell 不會執行未簽署的指令碼。 除非簽署指令碼，否則需要將 Windows PowerShell 設為執行未簽署的指令碼。 若要執行未簽署的指令碼，**ExecutionPolicy** 必須設為 **Unrestricted**。 您使用的 **ExecutionPolicy** 設定需取決於 Windows PowerShell 的版本。

    REM   Run an unsigned PowerShell script and log the output
    PowerShell -ExecutionPolicy Unrestricted .\startup.ps1 >> "%TEMP%\StartupLog.txt" 2>&1
    
    REM   If an error occurred, return the errorlevel.
    EXIT /B %errorlevel%

如果您使用的客體 OS 是執行 PowerShell 2.0 或 1.0，可以強制執行第 2 版，如果無法這麼做，請改用第 1 版。

    REM   Attempt to set the execution policy by using PowerShell version 2.0 syntax.
    PowerShell -Version 2.0 -ExecutionPolicy Unrestricted .\startup.ps1 >> "%TEMP%\StartupLog.txt" 2>&1
    
    REM   If PowerShell version 2.0 isn't available. Set the execution policy by using the PowerShell
    IF %ERRORLEVEL% EQU -393216 (
    
       PowerShell -Command "Set-ExecutionPolicy Unrestricted" >> "%TEMP%\StartupLog.txt" 2>&1
       PowerShell .\startup.ps1 >> "%TEMP%\StartupLog.txt" 2>&1
    )
    
    REM   If an error occurred, return the errorlevel.
    EXIT /B %errorlevel%

## 透過啟動工作在本機儲存體中建立檔案

您可以使用本機儲存體資源，儲存啟動工作所建立的檔案，以便日後供您的應用程式存取。

若要建立本機儲存體資源，將 [LocalResources] 區段加入至 [ServiceDefinition.csdef] 檔案，然後加入 [LocalStorage] 子元素。 將本機儲存體資源命名為不重複的名稱，然後為啟動工作提供適當的大小。

若要在啟動工作中使用本機儲存體資源，您需要建立環境變數來參考本機儲存體資源的位置。 接著，啟動工作和應用程式就能對本機儲存體資源讀取及寫入檔案。

**ServiceDefinition.csdef** 檔案的相關區段如下：

```xml
<ServiceDefinition name="MyService" xmlns="http://schemas.microsoft.com/ServiceHosting/2008/10/ServiceDefinition">
    <WebRole name="WebRole1">
        ...

        <LocalResources>
          <LocalStorage name="StartupLocalStorage" sizeInMB="5"/>
        </LocalResources>

        ...

        <Runtime>
            <Environment>
                <Variable name="PathToStartupStorage">
                    <RoleInstanceValue xpath="/RoleEnvironment/CurrentInstance/LocalResources/LocalResource[@name='StartupLocalStorage']/@path" />
                </Variable>
            </Environment>
        </Runtime>

        ...

        <Startup>
          <Task commandLine="Startup.cmd" executionContext="limited" taskType="simple" />
        </Startup>
    </WebRole>
</ServiceDefinition>
```

在範例中，此 **Startup.cmd** 批次檔使用 **PathToStartupStorage** 環境變數，在本機儲存體位置上建立 **MyTest.txt** 檔案。

    REM   Create a simple text file.
    
    ECHO This text will go into the MyTest.txt file which will be in the    >  "%PathToStartupStorage%\MyTest.txt"
    ECHO path pointed to by the PathToStartupStorage environment variable.  >> "%PathToStartupStorage%\MyTest.txt"
    ECHO The contents of the PathToStartupStorage environment variable is   >> "%PathToStartupStorage%\MyTest.txt"
    ECHO "%PathToStartupStorage%".                                          >> "%PathToStartupStorage%\MyTest.txt"
    
    REM   Exit the batch file with ERRORLEVEL 0.
    
    EXIT /b 0

您也可以使用 Azure sdk 存取本機儲存體 [GetLocalResource](https://msdn.microsoft.com/library/azure/microsoft.windowsazure.serviceruntime.roleenvironment.getlocalresource.aspx) 方法。 標準檔案讀取和寫入作業會隨即讀取和寫入本機儲存體資源的內容。

```csharp
string localStoragePath = Microsoft.WindowsAzure.ServiceRuntime.RoleEnvironment.GetLocalResource("StartupLocalStorage").RootPath;

string fileContent = System.IO.File.ReadAllText(System.IO.Path.Combine(localStoragePath, "MyTest.txt"));
```


## 區分在模擬器和雲端中執行的差異

當啟動工作在雲端中運作時，您可以讓啟動工作執行有別於在計算模擬器中運作時的步驟。 例如，在模擬器中執行時，您可能只想使用 SQL 資料的全新複本。 或者，您可能想針對雲端執行效能最佳化作業，而這是在模擬器中執行時不需要的作業。

這項功能在計算模擬器和雲端上執行不同的動作可以透過在 [ServiceDefinition.csdef] 檔案中，建立環境變數，則啟動工作在測試環境變數。

若要建立環境變數，將 [變數] / [RoleInstanceValue] 項目，並建立的 XPath 值 `/RoleEnvironment/Deployment/@emulated`。 值 **%computeemulatorrunning%** 環境變數會 `"true"` 運算模擬器上執行時和 `"false"` 雲端上執行時。


```xml
<ServiceDefinition name="MyService" xmlns="http://schemas.microsoft.com/ServiceHosting/2008/10/ServiceDefinition">
    <WebRole name="WebRole1">

        ...

        <Runtime>
            <Environment>
                <Variable name="ComputeEmulatorRunning">
                    <RoleInstanceValue xpath="/RoleEnvironment/Deployment/@emulated" />
                </Variable>
            </Environment>
        </Runtime>

    </WebRole>
</ServiceDefinition>
```

現在可以使用 **%ComputeEmulatorRunning%** 環境變數執行任何工作，並根據角色是在雲端或模擬器中執行，而執行不同的動作。 以下是檢查該環境變數的 .cmd 命令介面指令碼。

    REM   Check if this task is running on the compute emulator.
    
    IF "%ComputeEmulatorRunning%" == "true" (
        REM   This task is running on the compute emulator. Perform tasks that must be run only in the compute emulator.
    
    ) ELSE (
        REM   This task is running on the cloud. Perform tasks that must be run only in the cloud.
    
    )

## 偵測工作是否已在執行

角色可能會未經重新開機便進行回收，導致您的啟動工作再執行一次。 標幟可指出工作是否已在裝載的 VM 上執行。 可能有些工作即便多次執行也沒關係。 不過，在某些情況下，您必須避免工作執行一次以上。

若要偵測工作是否已在執行，最簡單的方式，是在工作成功執行時於 **%TEMP%** 資料夾中建立檔案，並在工作開始時尋找這個檔案。 以下是可為您執行此作業的範例 cmd Shell 指令碼。

    REM   If Task1_Success.txt exists, then Application 1 is already installed.
    IF EXIST "%RoleRoot%\Task1_Success.txt" (
      ECHO Application 1 is already installed. Exiting. >> "%TEMP%\StartupLog.txt" 2>&1
      GOTO Finish
    )
    
    REM   Run your real exe task
    ECHO Running XYZ >> "%TEMP%\StartupLog.txt" 2>&1
    "%PathToApp1Install%\setup.exe" >> "%TEMP%\StartupLog.txt" 2>&1
    
    IF %ERRORLEVEL% EQU 0 (
      REM   The application installed without error. Create a file to indicate that the task
      REM   does not need to be run again.
    
      ECHO This line will create a file to indicate that Application 1 installed correctly. > "%RoleRoot%\Task1_Success.txt"
    
    ) ELSE (
      REM   An error occurred. Log the error and exit with the error code.
    
      DATE /T >> "%TEMP%\StartupLog.txt" 2>&1
      TIME /T >> "%TEMP%\StartupLog.txt" 2>&1
      ECHO  An error occurred running task 1. Errorlevel = %ERRORLEVEL%. >> "%TEMP%\StartupLog.txt" 2>&1
    
      EXIT %ERRORLEVEL%
    )
    
    :Finish
    
    REM   Exit normally.
    EXIT /B 0

## 工作最佳作法

為 Web 或背景工作角色設定工作時，應該遵循的最佳作法如下。

### 務必記錄啟動活動

Visual Studio 並未提供可逐步執行批次檔的偵錯工具，因此最好盡可能取得越多批次檔作業上的資料。記錄批次檔的輸出 (包括 **stdout** 和 **stderr**)，在您嘗試偵錯及修正批次檔時，這些資料可以提供重要資訊。若要記錄都 **stdout** 和 **stderr** 至 StartupLog.txt 檔案的目錄中所指的 **%TEMP%** 環境變數，將文字加入 `>>"%temp%\\startuplog.txt"2 > & 1` 至您想要記錄的特定一行的結尾。例如，執行 **%PathToApp1Install%** 目錄中的 setup.exe 時：

    "%PathToApp1Install%\setup.exe" >> "%TEMP%\StartupLog.txt" 2>&1

如果您想要啟動工作的記錄所有的輸出，而不會增加 `>>"%temp%\startuplog.txt"2 > & 1`每一行的結尾，需要兩個啟動批次檔案。第一個批次檔會藉由重新導向呼叫第二個批次檔案，以記錄第二個批次檔的所有活動。重新導向必須順利執行，才能如實完成記錄。

以下示範如何從啟動批次檔重新導向所有的輸出。 在此範例中，ServerDefinition.csdef 檔案會建立啟動工作，以呼叫 Startup1.cmd。 Startup1.cmd 呼叫 Startup2.cmd，將所有輸出重新都導向至 %temp%\\startuplog.txt。

ServiceDefinition.cmd：

```xml
<Startup>
    <Task commandLine="Startup1.cmd" executionContext="limited" taskType="simple" />
</Startup>
```

Startup1.cmd：

    REM   Startup1.cmd calls the main startup batch file, Startup2.cmd, redirecting all output
    REM   to the StartupLog.txt log file.
    
    REM   Log the startup date and time.
    ECHO Startup1.cmd: >> "%TEMP%\StartupLog.txt" 2>&1
    ECHO Current date and time: >> "%TEMP%\StartupLog.txt" 2>&1
    DATE /T >> "%TEMP%\StartupLog.txt" 2>&1
    TIME /T >> "%TEMP%\StartupLog.txt" 2>&1
    ECHO Starting up Startup2.cmd. >> "%TEMP%\StartupLog.txt" 2>&1
    
    REM   Call the Startup2.cmd batch file, redirecting all output to the StartupLog.txt log file.
    START /B /WAIT Startup2.cmd >> "%TEMP%\StartupLog.txt" 2>&1
    
    REM   Log the completion of Startup1.cmd.
    ECHO Returned to Startup1.cmd. >> "%TEMP%\StartupLog.txt" 2>&1
    
    IF ERRORLEVEL EQU 0 (
       REM   No errors occurred. Exit Startup1.cmd normally.
       EXIT /B 0
    ) ELSE (
       REM   Log the error.
       ECHO An error occurred. The ERRORLEVEL = %ERRORLEVEL%.  >> "%TEMP%\StartupLog.txt" 2>&1
       EXIT %ERRORLEVEL%
    )

Startup2.cmd：

    REM   This is the batch file where the startup steps should be performed. Because of the
    REM   way Startup2.cmd was called, all commands and their outputs will be stored in the
    REM   StartupLog.txt file in the directory pointed to by the TEMP environment variable.
    
    REM   If an error occurs, the following command will pass the ERRORLEVEL back to the
    REM   calling batch file.
    EXIT /B %ERRORLEVEL%

### 正確設定啟動工作的 executionContext

正確設定啟動工作的權限。 有時候，即使角色是以正常的權限執行，啟動工作也必須以較高的權限執行。

[ExecutionContext ][task] 屬性來設定啟動工作的權限等級。 使用 `executionContext = 「 有限 」` 表示啟動工作將會有相同的權限層級角色。 使用 `executionContext = 」 特殊權限的 「` 表示啟動工作將具備系統管理員權限，可讓啟動工作來執行系統管理員工作，而系統管理員權限授予您的角色。

需要提高權限的啟動工作範例，是使用 **AppCmd.exe** 設定 IIS 的啟動工作。 **AppCmd.exe** 需要 `executionContext = 」 特殊權限的 「`。

### 使用正確的 taskType

[TaskType ][task] 屬性會決定將執行的方式啟動工作。 此屬性有三個值：**simple**、**background** 和 **foreground**。 background 和 foreground 工作會以非同步方式啟動，而 simple 工作會以同步方式執行，一次一個。

利用 **simple** 啟動工作後，您就可以設定工作發生的順序，亦即工作在 ServiceDefinition.csdef 檔案中列出的順序。 如果 **simple** 工作以非零的結束代碼做為結尾，則啟動程序會隨即停止，而角色也將不會啟動。

**background** 啟動工作和 **foreground** 啟動工作之間的差異，在於 **foreground** 工作會讓角色不斷執行，直到 **foreground** 工作結束為止。 換句話說，如果 **foreground** 工作停止回應或當機，那麼在 **foreground** 工作強制關閉前，都不會回收角色。 基於這個理由，除非需要使用 **foreground** 工作的功能，否則建議您將 **background** 工作用於非同步的啟動工作。

### 以 EXIT /B 0 結束批次檔

如果每個 simple 啟動工作的 **errorlevel** 是零，角色才會啟動。 並非所有的程式設定 **errorlevel** (結束代碼) 正確，因此批次檔結尾必須 `EXIT /B 0` 如果所有項目是否正確執行。

遺漏 `EXIT /B 0` 啟動批次結尾檔案的常見原因是的不會啟動角色。

### 啟動工作預計會執行一次以上

並非所有角色回收都會重新開機，但所有角色回收都會執行全部的啟動工作。 換句話說，啟動工作必須能夠在重新開機之間，順利地執行多次。 就此討論 [上方](#detect-that-your-task-has-already-run)。

### 使用本機儲存體儲存必須在角色中存取的檔案

如果您想要在啟動工作期間複製或建立檔案，然後供您的角色存取，則該檔案必須放在本機儲存體中。 請參閱 [區段](#create-files-in-local-storage-from-a-startup-task) 上方。

## 後續步驟

檢閱定域機組 [服務模型和封裝](cloud-services-model-and-package.md)

深入了解如何 [工作](cloud-services-startup-tasks.md) 運作。

[建立及部署](cloud-services-how-to-create-deploy-portal.md) 雲端服務封裝。



[servicedefinition.csdef]: cloud-services-model-and-package.md#csdef 
[task]: https://msdn.microsoft.com/library/azure/gg557552.aspx#Task 
[runtime]: https://msdn.microsoft.com/library/azure/gg557552.aspx#Runtime 
[startup]: https://msdn.microsoft.com/library/azure/gg557552.aspx#Startup 
[runtime]: https://msdn.microsoft.com/library/azure/gg557552.aspx#Runtime 
[environment]: https://msdn.microsoft.com/library/azure/gg557552.aspx#Environment 
[variable]: https://msdn.microsoft.com/library/azure/gg557552.aspx#Variable 
[roleinstancevalue]: https://msdn.microsoft.com/library/azure/gg557552.aspx#RoleInstanceValue 
[roleenvironment]: https://msdn.microsoft.com/library/azure/microsoft.windowsazure.serviceruntime.roleenvironment.aspx 
[endpoints]: https://msdn.microsoft.com/library/azure/gg557552.aspx#Endpoints 
[localstorage]: https://msdn.microsoft.com/library/azure/gg557552.aspx#LocalStorage 
[localresources]: https://msdn.microsoft.com/library/azure/gg557552.aspx#LocalResources 
[roleinstancevalue]: https://msdn.microsoft.com/library/azure/gg557552.aspx#RoleInstanceValue 

