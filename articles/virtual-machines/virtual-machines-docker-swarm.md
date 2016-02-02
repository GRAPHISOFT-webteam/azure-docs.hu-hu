<properties
   pageTitle = 「 開始在 Azure 上搭配 swarm 使用 docker 」
   描述 ="說明如何使用 Docker VM 延伸模組建立一組 Vm，以及使用 swarm 來建立 Docker 叢集 」。
   服務 = 「 虛擬機器 」
   documentationCenter ="虛擬機器 」
   作者 ="squillace"
   管理員 ="timlt"
   editor="tysonn"/>

<tags
   ms.service= 「 虛擬機器 」
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm= 「 vm linux 」
   ms.workload="infrastructure 」
   ms.date="09/22/2015 」
   ms.author="rasquill"/ >

# 如何搭配swarm 使用 docker

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-classic-include.md)] 資源管理員模型。


本主題將示範一個非常簡單的方式來使用 [docker](https://www.docker.com/) 與 [swarm](https://github.com/docker/swarm) Azure 上建立由 swarm 管理的叢集。 它會在 Azure 中建立四個虛擬機器，一個用來做為 swarm 管理員，其餘三個則做為 docker 主機叢集的一部分。 當您完成時，可以使用 swarm 查看叢集，然後開始在其上使用 docker。 此外，本主題中的 Azure CLI 呼叫會使用服務管理 (asm) 模式。
> [AZURE.NOTE] 這是舊版軟體，因此，請查看以往的更新記錄，以取得在 Azure 上使用此功能，為 Docker 容器建立平衡且受控制的大型叢集相關資訊，以及檢查 docker swarm 文件來探索它的所有功能。

> 此外，本主題使用 swarm 使用 docker 和 Azure CLI *沒有* 使用 **docker-machine-** 以顯示不同的工具如何一起運作，但是保持獨立。 **docker-machine-** 有 **-swarm** 參數可讓您使用 **docker-machine-** 直接將節點新增到 swarm。 如需範例，請參閱 [docker-machine-](https://github.com/docker/machine) 文件。 如果您錯過 **docker-machine-** Vm 上執行的 Azure，請參閱 [如何搭配 Azure 使用 docker-machine](virtual-machines-docker-machine.md)。

## 使用 Azure 虛擬機器建立 docker 主機

本主題會建立四個 VM，但您可以使用任何您想要的數目。 呼叫下列 *< 密碼 >* 您所選擇的密碼來取代。

    azure vm docker create swarm-master -l "East US" -e 22 $imagename ops <password>
    azure vm docker create swarm-node-1 -l "East US" -e 22 $imagename ops <password>
    azure vm docker create swarm-node-2 -l "East US" -e 22 $imagename ops <password>
    azure vm docker create swarm-node-3 -l "East US" -e 22 $imagename ops <password>

當您完成時，應該能夠使用 **azure vm list** 來查看您的 Azure VM：

    $ azure vm list | grep "swarm-[mn]"
    data:    swarm-master     ReadyRole           East US       swarm-master.cloudapp.net                               100.78.186.65
    data:    swarm-node-1     ReadyRole           East US       swarm-node-1.cloudapp.net                               100.66.72.126
    data:    swarm-node-2     ReadyRole           East US       swarm-node-2.cloudapp.net                               100.72.18.47  
    data:    swarm-node-3     ReadyRole           East US       swarm-node-3.cloudapp.net                               100.78.24.68  

## 在 swarm 主要 VM 上安裝 swarm

本主題使用 [容器模型的安裝來自 docker swarm 文件](https://github.com/docker/swarm#1---docker-image) -但是，您也可以 SSH 到 **swarm 主要**。 在此模型中，會下載 **swarm** 以做為執行 swarm 的 docker 容器。 在後續內容中，我們會*從膝上型電腦使用 docker 遠端*執行此步驟以連接到 **swarm-master** VM，並告知它使用叢集識別碼建立命令 **swarm create**。 叢集識別碼是 **swarm** 探索 swarm 群組成員的方式。 (您也可以複製儲存機制並自行建置它，這樣做可讓您擁有完整控制權且能夠進行偵錯)。

    $ docker --tls -H tcp://swarm-master.cloudapp.net:2376 run --rm swarm create
    Unable to find image 'swarm:latest' locally
    511136ea3c5a: Pull complete
    a8bbe4db330c: Pull complete
    9dfb95669acc: Pull complete
    0b3950daf974: Pull complete
    633f3d9a9685: Pull complete
    bba5f98a0414: Pull complete
    defbc1ab4462: Pull complete
    92d78d321ff2: Pull complete
    swarm:latest: The image you are pulling has been verified. Important: image verification is a tech preview feature and should not be relied on to provide security.
    Status: Downloaded newer image for swarm:latest
    36731c17189fd8f450c395db8437befd

最後一行是叢集識別碼；請將它複製到某處，因為當您將節點 VM 加入 swarm 主機以建立 "swarm" 時將再次用到它。 在此範例中，叢集識別碼是 **36731c17189fd8f450c395db8437befd**。
> [AZURE.NOTE] 只是要先聲明，我們正使用本機 docker 安裝來連接到 Azure 中的 **swarm-master** VM，並指示 **swarm-master** 下載、安裝及執行 **create** 命令，其會傳回我們稍後要基於探索目的而使用的叢集識別碼。

> 若要確認這一點，執行 `docker-tcp: / /`*< 主機名稱 >* ` 映像` 上列出的容器處理程序 **swarm 主要** 電腦和用於比較的另一個節點上 (因為我們已與上一個 swarm 命令，所以 **--rm** 交換器，容器已移除它完成，因此使用 **docker ps-a** 不會傳回任何項目)。:


        $ docker --tls -H tcp://swarm-master.cloudapp.net:2376 images
        REPOSITORY          TAG                 IMAGE ID            CREATED             VIRTUAL SIZE
        swarm               latest              92d78d321ff2        11 days ago         7.19 MB
        $ docker --tls -H tcp://swarm-node-1.cloudapp.net:2376 images
        REPOSITORY          TAG                 IMAGE ID            CREATED             VIRTUAL SIZE
        $

<P />
> 如果您熟悉 **docker**，就知道其他節點不會包含任何項目，因為並未下載並執行任何映像。

## 將節點 VM 加入我們的 docker 叢集

針對每個節點，使用 Azure CLI 列出端點資訊。 我們將在以下針對 **swarm-node-1** docker 主機執行此動作，以取得該節點的 docker 連接埠。

    $ azure vm endpoint list swarm-node-1
    info:    Executing command vm endpoint list
    + Getting virtual machines
    data:    Name    Protocol  Public Port  Private Port  Virtual IP      EnableDirectServerReturn  Load Balanced
    data:    ------  --------  -----------  ------------  --------------  ------------------------  -------------
    data:    docker  tcp       2376         2376          138.91.112.194  false                     No
    data:    ssh     tcp       22           22            138.91.112.194  false                     No
    info:    vm endpoint list command OK

使用 **docker** 和 `-H` 選項來指向節點 VM，在 docker 用戶端會將該節點加入您要藉由傳遞叢集識別碼和節點的 docker 連接埠建立的 swarm (後者使用 **--addr**):

    $ docker --tls -H tcp://swarm-node-1.cloudapp.net:2376 run -d swarm join --addr=138.91.112.194:2376 token://36731c17189fd8f450c395db8437befd
    Unable to find image 'swarm:latest' locally
    511136ea3c5a: Pull complete
    a8bbe4db330c: Pull complete
    9dfb95669acc: Pull complete
    0b3950daf974: Pull complete
    633f3d9a9685: Pull complete
    bba5f98a0414: Pull complete
    defbc1ab4462: Pull complete
    92d78d321ff2: Pull complete
    swarm:latest: The image you are pulling has been verified. Important: image verification is a tech preview feature and should not be relied on to provide security.
    Status: Downloaded newer image for swarm:latest
    bbf88f61300bf876c6202d4cf886874b363cd7e2899345ac34dc8ab10c7ae924

看起來不錯。 若要確認 **swarm** 正在 **swarm-node-1** 上執行，可輸入：

    $ docker --tls -H tcp://swarm-node-1.cloudapp.net:2376 ps -a
        CONTAINER ID        IMAGE               COMMAND                CREATED             STATUS              PORTS               NAMES
        bbf88f61300b        swarm:latest        "/swarm join --addr=   13 seconds ago      Up 12 seconds       2375/tcp            angry_mclean

針對叢集中的所有其他節點重複執行。 在本例中，我們會針對 **swarm-node-2** 和 **swarm-node-3** 執行此動作。

## 開始管理 swarm 叢集

    $ docker --tls -H tcp://swarm-master.cloudapp.net:2376 run -d -p 2375:2375 swarm manage token://36731c17189fd8f450c395db8437befd
    d7e87c2c147ade438cb4b663bda0ee20981d4818770958f5d317d6aebdcaedd5

然後您可以列出叢集中的節點：

    ralph@local:~$ docker --tls -H tcp://swarm-master.cloudapp.net:2376 run --rm swarm list token://73f8bc512e94195210fad6e9cd58986f
    54.149.104.203:2375
    54.187.164.89:2375
    92.222.76.190:2375

## 後續步驟

在您的 swarm 上執行動作。 若要尋找靈感，請參閱 [https://github.com/docker/swarm/](https://github.com/docker/swarm/), ，或許就 [視訊](https://www.youtube.com/watch?v=EC25ARhZ5bI)。




[docker-machine-azure]: virtual-machines-docker-machine.md 

