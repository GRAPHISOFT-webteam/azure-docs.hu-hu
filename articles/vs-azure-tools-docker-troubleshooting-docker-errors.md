<properties
   pageTitle="使用 Visual Studio 疑難排解 Windows 上的 Docker 用戶端錯誤 | Microsoft Azure"
   description="疑難排解使用 Visual Studio 在 Windows 上建立及部署 Web 應用程式到 Docker 時您會遇到的問題。"
   services="visual-studio-online"
   documentationCenter="na"
   authors="TomArcher"
   manager="douge"
   editor="" />
<tags
   ms.service="multiple"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="multiple"
   ms.date="08/20/2015"
   ms.author="tarcher" />


# 疑難排解 Docker 錯誤

設定應用程式 Docker 容器的所有設定之後，您應該確定設定和路徑正確。 Visual Studio 在 [發佈] 對話方塊中提供 [驗證] 按鈕來協助您執行這項操作。

本主題將協助您診斷和修正或解決在 Docker 中裝載 Visual Studio 應用程式時會遇到的最常見的問題。 之後遇到的更多問題會加入本主題。

## 嘗試在 [發佈 Web] 對話方塊中驗證您的 Docker 主機連接時，收到失敗的訊息

以下是這個問題的某些可能解決方案。

- 在 **連線** ] 索引標籤 **發行** ] 對話方塊中，請確定 **伺服器 Url** 正確無誤，尾端 `: < port_number >` 上 **伺服器 URL** 是 Docker 精靈正在接聽的連接埠。

- 在 [發佈]**** 對話方塊的 [連接]**** 索引標籤中，展開 [Docker 進階選項]**** 區段，並確定已指定了正確的 [驗證]**** 選項。
  - 如果伺服器上的 Docker 精靈會設定為使用 TLS 安全性則 Windows Docker 命令列介面 (docker.exe) 將會尋找用戶端金鑰 (key.pem) 和憑證 (cert.pem) 預設為在 `< %userprofile%> \.docker` 資料夾。如果這些項目中沒有顯示，將必須使用 OpenSSL 來產生。如需有關設定 Docker TLS 的詳細資訊，請參閱 [保護 Docker 精靈使用 HTTPS 通訊端](https://docs.docker.com/articles/https/)。

    其中一種確保 Docker 是正確的 Windows 用戶端向 Linux 伺服器是預覽文字方塊的內容複製到新的命令視窗，然後變更 `< 命令 >` 到 「 資訊 」，如下列所示:

    ```
    // This example assumes the Docker daemon is configured to use the default port
    // of 2376 to listen for connections.docker.

    --tls -H tcp://contoso.cloudapp.net:2376 info
    ```

    作為將用戶端憑證和金鑰檔複製到 .docker 資料夾的替代方案，您可以藉由加入下列參數來變更 [驗證]**** 選項：

    ```
    --tls --tlscert=C:\mycert\cert.pem --tlskey=C:\mycert\key.pem
    ```
- 確定 Docker 主機電腦上的 Docker 精靈 1.6 版或更新版本。

## 在 Docker 資料夾中使用您自己的憑證而沒有用戶端憑證時的逾時錯誤

如果您選擇在 Visual Studio 中建立 Docker 主機時，使用您自己的憑證 (也就是您清除 **自動產生 Docker 憑證** 中核取方塊 **Microsoft Azure 上建立虛擬機器** 對話方塊)，您必須將用戶端憑證和金鑰檔案 (cert.pem 和 key.pem) 複製到 Docker 資料夾 (`< %userprofile%> \.docker`)。否則，發佈您的專案時，會在一小時內收到逾時錯誤，並且發佈作業會失敗。

## 需要 PowerShell 3.0 才能發佈至 Docker 容器

如果作業系統是 Windows 7 或 Windows Server 2008，您必須安裝 PowerShell 3.0 才能發佈至 Docker 容器。 PowerShell 3.0 隨附於 [Windows Management Framework 3.0](https://www.microsoft.com/en-us/download/details.aspx?id=34595)。 您必須在安裝完成後重新啟動系統。

做為替代的解決方法，您可以升級到已具備 PowerShell 3.0 的 Windows 8.1 或 Windows 10。

## PowerShell 視窗不會自動關閉

建立 VM 之後，有時候 PowerShell 視窗不會自動關閉。 關閉此視窗也會關閉 Visual Studio。 因為視窗不會影響任何 Visual Studio 或 Docker 工具功能，請保留它開啟直到完成您的工作。

## 常見問題集

問：如何在 Azure 中使用 Visual Studio 工具建立新的啟用 Docker 的 Linux 機器？

答: 請參閱 [在 Docker 中裝載 Web 應用程式](vs-azure-tools-docker-hosting-web-apps-in-docker.md) 如需如何執行這項操作。

問: 哪些 Visual Studio 專案範本都支援發行至 Linux Docker 容器?

答: visual Studio 目前支援的 C# 主控台應用程式 (封裝) 和 C# ASP.NET 5 Preview 網站範本，包括:

- 空白

- Web API

- Web 應用程式

問: 如何將我的 ASP.NET 5 web 或主控台專案發行至 Docker 從命令列使用 MSBUILD?

答: 請使用下列 MSBuild 命令:

    `msbuild <projectname.xproj> /p:deployOnBuild=true;publishProfile=<profilename>`

問: 如何將我的 ASP.NET 5 web 或主控台專案發行至 Docker 從命令列使用 PowerShell?

答: 請使用下列 PowerShell 命令:

```
.\contoso-Docker-publish.ps1 -packOutput $env:USERPROFILE\AppData\Local\Temp\PublishTemp -pubxmlFile .\contoso-Docker.pubxml
```

問: 我已安裝 docker 有自己的 Linux 伺服器，如何指定在 **Web 發行** 對話方塊?

答: 請參閱章節 **提供自訂的 Docker 主機** 主題中的 [在 Docker 中裝載 Web 應用程式](vs-azure-tools-docker-hosting-web-apps-in-docker.md)。

問: 我使用我自己的 Linux 伺服器已安裝 docker。 如何產生金鑰和憑證以設定使用 TLS 的驗證？

答: 一種方法是在伺服器上使用 OpenSSL 來為 CA、 伺服器和用戶端產生必要的憑證和金鑰。然後您可以使用協力廠商軟體建立 SSH/SFTP 連接，然後將憑證複製到本機的 Windows 開發電腦。根據預設，Docker (CLI) 會嘗試使用憑證位於 `< userprofile > \.docker` 資料夾。

另一個選項是下載 OpenSSL for Windows 並產生需要的憑證和金鑰，然後上傳 CA、伺服器憑證和金鑰到 Linux 機器。 如需有關建立 Docker 的安全連線的詳細資訊，請參閱 [保護 Docker 精靈使用 HTTPS 通訊端](https://docs.docker.com/articles/https/)。





