<properties
    pageTitle="如何透過 Ubuntu-Docker VM 映像快速使用 Docker"
    description="說明並示範如何直接從 Azure 映像庫快速使用 Docker on Ubuntu Server"
    services="virtual-machines"
    documentationCenter=""
    authors="squillace"
    manager="timlt"
    editor="tysonn"/>

<tags
    ms.service="virtual-machines"
    ms.devlang="na"
    ms.topic="article"
    ms.tgt_pltfrm="vm-linux"
    ms.workload="infrastructure"
    ms.date="10/04/2015"
    ms.author="rasquill"/>

# 如何快速地開始使用 Azure Marketplace 中的 Docker

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-classic-include.md)] 資源管理員模型。
 

若要開始使用最快的方法 [Docker] 是移至 Azure Marketplace，並建立 VM，使用 **Docker on Ubuntu Server** 所建立的映像範本 [Canonical] 搭配 [MSOpenTech]。 這會建立 Ubuntu Server VM，並自動安裝 [Docker VM 擴充程式](virtual-machines-docker-vm-extension.md) 連同 **最新** Docker 引擎預先安裝在 Azure 上並執行。  

您可以立即使用 SSH 連接到 VM，並直接開始使用 Docker 執行工作，無須執行其他動作。

> [AZURE.NOTE]Azure Marketplace 範本所建立的 VM 並未裝載遠端 docker 用戶端所管理的 Docker 遠端 API。 若要啟用從遠端控制此 VM 上的 Docker 主機，請參閱 [使用 HTTPS 執行 Docker](https://docs.docker.com/articles/https/) 或依照 [使用 Docker VM 延伸模組，從 Azure 傳統入口網站](virtual-machines-docker-with-portal.md) 或 [使用 Docker VM 延伸模組，從 Azure CLI](virtual-machines-docker-with-xplat-cli-install.md)。 
<!-- -->
如果您想要自動化從 Windows Azure 的 Docker VM，您可以 [安裝 Docker 工具箱](https://docs.docker.com/installation/windows/) 或取得 Docker.exe [從 Chocolatey](https://chocolatey.org/packages/docker)。

## 登入入口網站

這部分很簡單，除非您沒有 Azure 帳戶。 [太容易，取得一個免費](http://azure.microsoft.com/pricing/free-trial/)！

## 使用 Docker 映像從 Canonical 和 MSOpenTech 建立 VM

1. 現在，您用以登入，按一下 **新增** 左上角來建立新的 VM 映像。 您可能會立即在橫幅中看到適當的映像：

> ![在橫幅中選擇 Docker Ubuntu 映像](./media/virtual-machines-docker-ubuntu-quickstart/CreateNewDockerBanner.png)

2. 但如果沒有，請在映像庫中加以搜尋：

> ![在映像庫中尋找映像](./media/virtual-machines-docker-ubuntu-quickstart/DockerOnUbuntuServerMSOpenTech.png)

3. 提供使用者名稱和密碼執行個體或內容 **.pub** 檔案 （ssh rsa 格式），以使用憑證啟用 SSH。 (下圖顯示指定使用者名稱和密碼的組合。)然後按下 **建立** 底部。

> ![設定 VM 執行個體](./media/virtual-machines-docker-ubuntu-quickstart/CreateVMDockerUbuntuPwd.png)

4. 等待作業開始執行。 此作業不需要太多時間。

> ![在入口網站中執行的 Docker 映像](./media/virtual-machines-docker-ubuntu-quickstart/DockerUbuntuRunning.png)

## 使用 SSH 連線，即可開始使用

現在好戲正要上演。 您可以使用 SSH 立即連接到 VM：

> ![使用 SSH 連線](./media/virtual-machines-docker-ubuntu-quickstart/SSHToDockerUbuntu.png)

並且開始發出 Docker 命令，切記，此 Azure VM 上的預設組態需要 **`sudo`**:

> ![提取映像](./media/virtual-machines-docker-ubuntu-quickstart/DockerPullSmallImages.png)

<!--Every topic should have next steps and links to the next logical set of content to keep the customer engaged-->
## 後續步驟

你想要開始使用 [Docker]！

<!--Anchors-->
[Log on to the Portal]: #logon
[Create a VM with the Docker Image from Canonical and MSOpenTech]: #createvm
[Connect with SSH and Have Fun]: #havingfun
[Next steps]: #next-steps


[Docker]: https://www.docker.com/
[BusyBox]: http://en.wikipedia.org/wiki/BusyBox
[Docker scratch image]: https://docs.docker.com/articles/baseimages/#creating-a-simple-base-image-using-scratch
[Canonical]: http://www.canonical.com/
[MSOpenTech]: http://msopentech.com/
 


