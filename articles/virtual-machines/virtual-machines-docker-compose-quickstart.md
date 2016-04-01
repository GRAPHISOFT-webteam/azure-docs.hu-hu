<properties
   pageTitle="虛擬機器上的 Docker 和 Compose | Microsoft Azure"
   description="在 Azure 虛擬機器上使用 Compose 和 Docker 的快速簡介"
   services="virtual-machines"
   documentationCenter=""
   authors="dlepow"
   manager="timlt"
   editor=""
   tags="azure-service-management"/>

<tags
   ms.service="virtual-machines"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="vm-linux"
   ms.workload="infrastructure-services"
   ms.date="11/16/2015"
   ms.author="danlep"/>

# 在 Azure 虛擬機器上開始使用 Docker 和 Compose 定義並執行多容器應用程式

本文將說明如何開始使用 Docker 和 [撰寫](http://github.com/docker/compose) 來定義並在 Azure 中的 Linux 虛擬機器上執行複雜的應用程式。 使用 Compose (後續 *Fig*)，您將多個 Docker 容器所組成的應用程式定義使用簡單的文字檔。 然後您可以透過可進行所有操作以便在 VM 上執行的單一命令，啟動您的應用程式。 例如，本文將說明如何藉由後端 MariaDB SQL 資料庫快速設定 WordPress 部落格，但您也可以使用 Compose 來設定更複雜的應用程式。

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-classic-include.md)] [資源管理員模型](https://azure.microsoft.com/documentation/templates/docker-wordpress-mysql/)。


如果您熟悉 Docker 和容器，請參閱 [Docker 高層級白板](http://azure.microsoft.com/documentation/videos/docker-high-level-whiteboard/)。

## 步驟 1：設定 Linux VM 做為 Docker 主機

您可以使用 Azure Markeplace 中的各種 Azure 程序和可用的映像建立 Linux VM，並將其設定為 Docker 主機。 例如，請參閱 [使用 Docker VM 擴充程式從 Azure 命令列介面](virtual-machines-docker-with-xplat-cli.md) 快速的程序，建立 Ubuntu VM 使用 Docker VM 擴充程式。 當您使用 Docker VM 延伸模組時，您的 VM 會自動設為 Docker 主機。 該文章中的範例會示範如何使用 [適用於 Mac、 Linux 和 Windows Azure 命令列介面](../xplat-cli-install.md) (Azure CLI) 來建立 VM 的服務管理模式。

## 步驟 2：安裝 Compose

Linux VM 和 Docker 執行之後，請使用 SSH 從用戶端電腦連線到此 VM。 如果您要安裝 [撰寫](https://github.com/docker/compose/blob/882dc673ce84b0b29cd59b6815cb93f74a6c4134/docs/install.md) 執行下列兩個命令。

>[AZURE.TIP] 如果您使用 Docker VM 延伸模組來建立 VM 時，為您已安裝 Compose。 請略過這些命令，並移至步驟 3。 只有在 VM 上自行安裝 Docker 時才需要安裝 Compose。

```
$ curl -L https://github.com/docker/compose/releases/download/1.1.0/docker-compose-`uname -s`-`uname -m` > /usr/local/bin/docker-compose

$ chmod +x /usr/local/bin/docker-compose
```
>[AZURE.NOTE]如果您收到 「 拒絕存取 」 錯誤，VM 的 /usr/local/bin 目錄可能無法寫入，因此您必須以超級使用者的身分安裝 Compose。 依序執行 `sudo -i`、以上兩個命令，然後再執行 `exit`。

若要測試 Compose 的安裝，請執行下列命令。

```
$ docker-compose --version
```

您將看到類似 `docker-compose 1.4.1` 的輸出內容。


## 步驟 3：建立 docker-compose.yml 組態檔

接下來，請建立 `docker-compose.yml` 檔案，這只是一個文字組態檔，用來定義要在此 VM 上執行的 Docker 容器。  此檔案會指定要在每個容器上執行的映像 (或可能是來自 Dockerfile 的組建)、所需的環境變數和相依性、連接埠，容器之間的連結等等。 如需有關 yml 檔案語法的詳細資訊，請參閱 [docker compose.yml 參考](http://docs.docker.com/compose/yml/)。

在 VM 上建立工作目錄，並使用您慣用的文字編輯器建立 `docker-compose.yml`。 若要嘗試簡單的範例，請將下列文字複製到檔案。 這項設定使用的映像 [DockerHub 登錄](https://registry.hub.docker.com/_/wordpress/) 安裝 WordPress （開放原始碼部落格和內容管理系統） 和連結的後端 MariaDB 資料庫。

 ```
 wordpress:
  image: wordpress
  links:
    - db:mysql
  ports:
    - 8080:80

db:
  image: mariadb
  environment:
    MYSQL_ROOT_PASSWORD: <your password>

```

## 步驟 4 ︰ 開始撰寫容器

在您 VM 的工作目錄中，執行以下命令。

```
$ docker-compose up -d

```

如此會啟動 `docker-compose.yml` 中指定的 Docker 容器。 您會看到類似以下的輸出：

```
Creating wordpress_db_1...
Creating wordpress_wordpress_1...
```

>[AZURE.NOTE] 請務必使用 **-d** 選項在啟動時，讓容器在背景持續執行。

若要確認容器是否已啟動，請輸入 `docker-compose ps`。 您應該會看到如下的內容：

```
Name             Command             State              Ports
-------------------------------------------------------------------------
wordpress_db_1     /docker-           Up                 3306/tcp
             entrypoint.sh
             mysqld
wordpress_wordpr   /entrypoint.sh     Up                 0.0.0.0:8080->80
ess_1              apache2-for ...                       /tcp
```

您現在可以在 VM 上瀏覽至 `http://localhost:8080`，以直接連線到 WordPress。 如果您想要透過網際網路連線至 VM，請先在將公用連接埠 80 對應至私用連接埠 8080 的 VM 上設定 HTTP 端點。 例如，在 Azure 服務管理部署中，執行下列 Azure CLI 命令：

```
$ azure vm endpoint create <machine-name> 80 8080

```

您現在應該會看到 WordPress 起始畫面，您可以在其中完成安裝，然後開始使用此應用程式。

![WordPress 起始畫面][wordpress_start]


## 後續步驟

* 簽出 [Compose CLI 參考](http://docs.docker.com/compose/reference/) 和 [使用者指南](http://docs.docker.com/compose/) 建置和部署多容器應用程式的相關範例。
* 使用 Azure 資源管理員範本，或是您自己或其中一個由 [社群](http://azure.microsoft.com/documentation/templates/), 、 部署與 Docker 及使用 Compose 設定的應用程式的 Azure VM。 例如， [部署 WordPress 部落格和 Docker](https://azure.microsoft.com/documentation/templates/docker-wordpress-mysql/) 範本使用 Docker 和 Compose 來快速部署 WordPress Ubuntu VM 上的 MySQL 後端。
* 嘗試整合 Docker Compose 與 [Docker Swarm](virtual-machines-docker-swarm.md) 叢集。 請參閱 []
[Docker Compose/Swarm 整合](https://github.com/docker/compose/blob/master/SWARM.md) 案例。

<!--Image references-->

[wordpress_start]: ./media/virtual-machines-docker-compose-quickstart/WordPress.png


