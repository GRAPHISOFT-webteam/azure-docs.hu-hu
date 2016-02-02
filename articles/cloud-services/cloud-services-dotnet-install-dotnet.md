<properties
   pageTitle="在雲端服務角色上安裝 .NET"
   description="本文說明如何在雲端服務 Web 和背景工作角色上手動安裝 .NET Framework。"
   services="cloud-services"
   documentationCenter=".net"
   authors="sbtron"
   manager="timlt"
   editor=""/>

<tags
   ms.service="cloud-services"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="12/11/2015"
   ms.author="saurabh"/>


# 在雲端服務角色上安裝 .NET

本文說明如何在雲端服務 Web 和背景工作角色上安裝 .NET framework。 您可以依照下列步驟在 Azure 客體 OS 系列 4 上安裝.NET framework 4.5.2 或 .NET 4.6 。 如需客體作業系統的最新版本，請參閱 [Azure 客體 OS 版本與 SDK 相容性比較表](cloud-services-guestos-update-matrix.md)。

在您的 web 和背景工作角色上安裝.NET 的程序牽涉到包括.NET 安裝程式套件一部分定域機組專案並啟動安裝程式角色的啟動工作的一部分。

## 將 .NET 安裝程式加入至專案

1. 針對您想要安裝的 .NET framework 下載 Web 安裝程式
    - [.NET 4.5.2 web 安裝程式](http://go.microsoft.com/fwlink/p/?LinkId=397703)
    - [.NET 4.6 web 安裝程式](http://go.microsoft.com/fwlink/?LinkId=528259)
    - [4.6.1 在.NET web 安裝程式](http://go.microsoft.com/fwlink/?LinkId=671729)
2. 若是 Web 角色
  1. 在 [**方案總管**] 中，於雲端服務專案中的 [**角色**] 下，以滑鼠右鍵按一下您的角色，然後依序選取 [**新增 > 新增資料夾**]。 建立名為 *bin* 的資料夾
  2. 在 **bin** 資料夾上按一下滑鼠右鍵，並依序選取 [**新增 > 現有項目**]。 選取 .NET 安裝程式，並將它加入至 bin 資料夾。
3. 若是背景工作角色
  1. 以滑鼠右鍵按一下您的角色，然後依序選取 [**新增 > 現有項目**]。 選取 .NET 安裝程式，並將它加入至角色。

以此方式新增至角色內容資料夾的檔案會自動加入至雲端服務封裝，並部署至虛擬機器上的一致位置。 為雲端服務中的所有 Web 和背景工作角色重複此程序，以便所有角色都有安裝程式副本。

![安裝程式檔案的角色內容][1]

## 定義角色的啟動工作

啟動工作可讓您在啟動角色之前執行作業。 將 .NET Framework 做為啟動工作的一部分安裝，可確保在執行任何應用程式程式碼之前已安裝好 Framework。 如需有關啟動工作，請參閱: [在 Azure 中執行啟動工作](https://msdn.microsoft.com/library/azure/hh180155.aspx)。

1. 將下列內容加入所有角色中 **WebRole** 或 **WorkerRole** 節點底下的 *ServiceDefinition.csdef* 檔案：

    ```xml
     <LocalResources>
      <LocalStorage name="NETFXInstall" sizeInMB="1024" cleanOnRoleRecycle="false" />
    </LocalResources>
    <Startup>
      <Task commandLine="install.cmd" executionContext="elevated" taskType="simple">
        <Environment>
          <Variable name="PathToNETFXInstall">
            <RoleInstanceValue xpath="/RoleEnvironment/CurrentInstance/LocalResources/LocalResource[@name='NETFXInstall']/@path" />
          </Variable>
        </Environment>
      </Task>
    </Startup>
    ```

    上述組態會使用系統管理員權限來執行主控台命令 *install.cmd*，以便安裝 .NET Framework。 此設定也會建立名稱為 *NETFXInstall* 的 LocalStorage。 啟動指令碼會將暫存資料夾設定為使用此本機儲存資源，如此即可從此資源下載並及安裝 .NET Framework 安裝程式。 請務必將此資源的大小設定為至少 1024 MB，以確保架構的安裝正確。 如需詳細資訊，請參閱: [使用本機儲存體來儲存檔案，在啟動期間](https://msdn.microsoft.com/library/azure/hh974419.aspx)

2. 建立 **install.cmd**，並在該角色上按一下右鍵，依序選取 [新增] > [現有項目...]****，將其新增至所有角色。 因此，所有角色現在應該都有 .NET 安裝程式檔案，以及 install.cmd 檔案。

    ![所有檔案的角色內容][2]
    > [AZURE.NOTE] 使用如 [記事本] 之類的簡單文字編輯器來建立這個檔案。 如果使用 Visual Studio 來建立文字檔案，然後將它重新命名為 '.cmd'，則此檔案可能仍會包含 UTF-8 位元順序標記，並在執行第一行的指令碼時出現錯誤。 如果您要使用 Visual Studio 來建立檔案，請在檔案的第一行保留加入 REM (備註)，以便在執行時將它忽略。 

3. 將下列指令碼加入 **install.cmd** 檔案：

    ```
    REM Set the value of netfx to install appropriate .NET Framework. 
    REM ***** To install .NET 4.5.2 set the variable netfx to "NDP452" *****
    REM ***** To install .NET 4.6 set the variable netfx to "NDP46" *****
    REM ***** To install .NET 4.6.1 set the variable netfx to "NDP461" *****
    set netfx="NDP46"


    REM ***** Needed to correctly install .NET 4.6.1, otherwise you may see an out of disk space error *****
    set TMP=%PathToNETFXInstall%
    set TEMP=%PathToNETFXInstall%


    REM ***** Setup .NET filenames and registry keys *****
    if %netfx%=="NDP461" goto NDP461
    if %netfx%=="NDP46" goto NDP46
        set netfxinstallfile="NDP452-KB2901954-Web.exe"
        set netfxregkey="0x5cbf5"
        goto logtimestamp

    :NDP46
    set netfxinstallfile="NDP46-KB3045560-Web.exe"
    set netfxregkey="0x60051"
    goto logtimestamp

    :NDP461
    set netfxinstallfile="NDP461-KB3102438-Web.exe"
    set netfxregkey="0x6041f"

    :logtimestamp
    REM ***** Setup LogFile with timestamp *****
    set timehour=%time:~0,2%
    set timestamp=%date:~-4,4%%date:~-10,2%%date:~-7,2%-%timehour: =0%%time:~3,2%
    md "%PathToNETFXInstall%\log"
    set startuptasklog="%PathToNETFXInstall%log\startuptasklog-%timestamp%.txt"
    set netfxinstallerlog="%PathToNETFXInstall%log\NetFXInstallerLog-%timestamp%"

    echo Logfile generated at: %startuptasklog% >> %startuptasklog%
    echo TMP set to: %TMP% >> %startuptasklog%
    echo TEMP set to: %TEMP% >> %startuptasklog%

    REM ***** Check if .NET is installed *****
    echo Checking if .NET (%netfx%) is installed >> %startuptasklog%
    reg query "HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\NET Framework Setup\NDP\v4\Full" /v Release | Find %netfxregkey%
    if %ERRORLEVEL%== 0 goto end

    REM ***** Installing .NET *****
    echo Installing .NET: start /wait %~dp0%netfxinstallfile% /q /serialdownload /log %netfxinstallerlog% >> %startuptasklog%
    start /wait %~dp0%netfxinstallfile% /q /serialdownload /log %netfxinstallerlog% >> %startuptasklog% 2>>&1

    :end
    echo install.cmd completed: %date:~-4,4%%date:~-10,2%%date:~-7,2%-%timehour: =0%%time:~3,2% >> %startuptasklog%
    ```

    > [AZURE.IMPORTANT] 更新指令碼中 *netfx* 變數的值，以符合您想要安裝的架構版本。 若要安裝 .NET 4.5.2，*netfx* 變數應設定為 *"NDP452"*、若要安裝 .NET 4.6，則 *netfx* 變數應設定為 *"NDP46"*，而若要安裝 .NET 4.6.1，則 *netfx* 變數應設定為 *"NDP461"*

    安裝指令碼會透過查詢登錄來檢查指定的 .NET framework 版本是否已在電腦上安裝。 如果未安裝該 .NET 版本，則 .Net Web 安裝程式便會啟動。 為協助疑難排解任何問題，該指令碼會將所有活動記錄到名為 *startuptasklog-(currentdatetime).txt* (儲存於 *InstallLogs* 本機儲存體) 的檔案。



## 設定診斷，將啟動工作記錄檔傳輸到 Blob 儲存體 (選擇性)

您可以選擇性地設定 Azure 診斷，將啟動指令碼或 .NET 安裝程式所產生的任何記錄檔傳輸到 Blob 儲存體。 透過這種方法，您可以從 Blob 儲存體直接下載記錄檔，而無需遠端桌面到角色，即可檢視記錄檔。

若要設定診斷，請開啟 *diagnostics.wadcfgx*，並在**目錄**節點下加入下列內容：

```xml 
<DataSources>
 <DirectoryConfiguration containerName="netfx-install">
  <LocalResource name="NETFXInstall" relativePath="log"/>
 </DirectoryConfiguration>
</DataSources>
```

這樣會將 Azure 診斷設定為將 *NETFXInstall* 資源下 *log* 目錄中的所有檔案，傳輸到 *netfx-install* Blob 容器中的診斷儲存體帳戶。

## 部署您的服務

部署服務時，啟動工作將會執行並安裝 .NET Framework (如果尚未安裝)。 安裝 Framework 時，您的角色將會處於忙碌狀態，而且甚至可能會重新啟動 (如果 Framework 安裝有此要求)。


## 其他資源

- [安裝.NET Framework]][]
- [如何: 判斷的.NET Framework 版本為 ][]
- [疑難排解.NET Framework 安裝]][]



[how to: determine which .net framework versions are installed]: https://msdn.microsoft.com/library/hh925568.aspx 
[installing the .net framework]: https://msdn.microsoft.com/library/5a4x27ek.aspx 
[troubleshooting .net framework installations]: https://msdn.microsoft.com/library/hh925569.aspx 
[1]: ./media/cloud-services-dotnet-install-dotnet/rolecontentwithinstallerfiles.png 
[2]: ./media/cloud-services-dotnet-install-dotnet/rolecontentwithallfiles.png 

