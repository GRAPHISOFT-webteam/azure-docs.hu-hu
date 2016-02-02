<properties
    pageTitle="使用負載平衡集將 MySQL 叢集化 | Microsoft Azure"
    description="在 Azure 上安裝以傳統部署模型建立的負載平衡、高可用性 Linux MySQL 叢集"
    services="virtual-machines"
    documentationCenter=""
    authors="bureado"
    manager="timlt"
    editor=""
    tags="azure-service-management"/>

<tags
    ms.service="virtual-machines"
    ms.workload="infrastructure-services"
    ms.tgt_pltfrm="vm-linux"
    ms.devlang="na"
    ms.topic="article"
    ms.date="04/14/2015"
    ms.author="jparrel"/>


# 在 Linux 上使用負載平衡集合將 MySQL 叢集化

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-classic-include.md)] 資源管理員模型。


本文旨在瀏覽與說明要在 Microsoft Azure 上部署高度可用 Linux 架構服務的其他可用方法，首先從 MySQL Server 高可用性開始。 影片，找到說明此方法可用於 [Channel 9](http://channel9.msdn.com/Blogs/Open/Load-balancing-highly-available-Linux-services-on-Windows-Azure-OpenLDAP-and-MySQL)。

我們會依據 DRBD、Corosync 和 Pacemaker，說明無共用、兩個節點、單一主要的 MySQL 高可用性解決方案。 一次只會有一個節點執行 MySQL。 也限制一次只會有一個節點從 DRBD 資源讀取和寫入。

因為我們會使用 Microsoft Azure 的負載平衡集合，來提供循環配置資源功能和 VIP 的端點偵測、移除及正常復原，所以不需要 VIP 解決方案 (如 LVS)。 VIP 是一個可全域路由的 IPv4 位址，會在我們第一次建立雲端服務時由 Microsoft Azure 指定。

還有其他可能的架構，可供 MySQL 使用，包括 NBD 叢集、 Percona 和 Galera 以及數個中介軟體解決方案，包括至少一個可作為 VM 上 [VM Depot](http://vmdepot.msopentech.com)。 只要這些解決方案可以在單點傳送與多點傳送上複寫或廣播，且不仰賴共用儲存體或多個網路介面，那麼應不難在 Microsoft Azure 上部署這些案例。

當然，您可以將這些叢集架構以類似的方式延伸到其他產品，如 PostgreSQL 和 OpenLDAP。 例如，已透過多個主要 OpenLDAP 成功測試使用無共用的負載平衡程序，您可以在我們的第 9 頻道 (Channel 9) 部落格上觀看此測試。

## 準備就緒

您將需要具備至少能夠建立兩部 (2) VM 之有效訂閱的 Microsoft Azure 帳戶 (在此範例中，使用 XS)、網路和子網路、同質群組和可用性設定組，以及能夠在相同區域建立新的 VHD 作為雲端服務，並將他們連接至 Linux VM 的能力。

### 測試環境

- Ubuntu 13.10
  - DRBD
  - MySQL Server
  - Corosync 和 Pacemaker

### 同質群組

建立解決方案的同質群組，方法是透過登入 Azure 傳統入口網站，向下捲動到 [設定]，然後建立新的同質群組。 並將稍後建立的配置資源指派給此同質群組。

### 網路

建立新網路，並在該網路內建立一個子網路。 我們選擇內部只有一個 /24 子網路的 10.10.10.0/24 網路。

### 虛擬機器

建立使用背書的 Ubuntu 映像庫映像，並呼叫第一個 Ubuntu 13.10 VM `hadb01`。 在此程序中會建立一個名為 hadb 的新雲端服務。 我們這麼稱呼它是因為當我們加入更多資源時，此名稱可說明服務將具備的共用、負載平衡性質。 建立 `hadb01` 是順利和已完成使用入口網站。 系統會自動建立 SSH 的端點，並選取我們所建立的網路。 我們也會選擇為 VM 建立新的可用性設定組。

第一個 VM 建立後 (技術上來說，建立雲端服務時)，我們繼續建立第二個 VM `hadb02`。 第二個 vm 我們也會使用 Ubuntu 13.10 VM 從資源庫使用入口網站，但我們將選擇使用現有的雲端服務， `hadb.cloudapp.net`, ，而不是建立一個新。 系統應會自動選取網路和可用性設定組。 並建立 SSH 端點。

在建立這兩個 Vm 之後，我們要記下的 SSH 連接埠 `hadb01` (TCP 22) 及 `hadb02` (由 Azure 自動指派)

### 連接的儲存體

我們將新磁碟連接至這兩個 VM，並在此程序中建立一個新的 5 GB 磁碟。 這些磁碟將在 VHD 容器中託管，並供我們的主要作業系統磁碟使用。 建立及連接磁碟後就不需要為我們重新啟動 Linux，因為核心將會看到新裝置 (通常是 `/開發人員/sdc`, ，您可以檢查 `dmesg` 輸出)

我們繼續每個 VM 上建立新的資料分割使用 `cfdisk` (主要，Linux 磁碟分割)，並寫入新資料分割資料表。 **請勿在此磁碟分割上建立檔案系統**。

## 設定叢集

在這兩個 Ubuntu VM 中，我們必須使用 APT 來安裝 Corosync、Pacemaker 和 DRBD。 使用 `apt get`:

    sudo apt-get install corosync pacemaker drbd8-utils.

**此時請勿安裝 MySQL**。 Debian 和 Ubuntu 安裝指令碼會初始化上的 MySQL 資料目錄 `/var/lib/mysql`, ，但因為 DRBD 檔案系統，就會取代此目錄，我們必須稍後再執行此作業。

現在我們也應該驗證 (使用 `來 ifconfig`) 這兩個 Vm 是否使用中 10.10.10.0/24 子網路的位址，以及他們可以依照名稱 ping 彼此。 如果需要，您也可以使用 `ssh-keygen` 和 `ssh-複製-識別碼` 來確定這兩個 Vm 可以透過 SSH 彼此通訊而不需要密碼。

### 設定 DRBD

我們將建立使用基礎 DRBD 資源 `/dev/sdc1` 產生的資料分割 `/dev/drbd1` 資源能夠使用 ext3 格式化以及主要和次要節點使用。 若要這樣做，請開啟 `/etc/drbd.d/r0.res` 並複製下列資源定義。 在這兩個 VM 中執行下列動作：

    resource r0 {
      on `hadb01` {
        device  /dev/drbd1;
        disk   /dev/sdc1;
        address  10.10.10.4:7789;
        meta-disk internal;
      }
      on `hadb02` {
        device  /dev/drbd1;
        disk   /dev/sdc1;
        address  10.10.10.5:7789;
        meta-disk internal;
      }
    }

然後，使用來初始化資源 `drbdadm` 兩部 vm:

    sudo drbdadm -c /etc/drbd.conf role r0
    sudo drbdadm up r0

最後，在主要 (`hadb01`) 強制執行 DRBD 資源的擁有權 (主要):

    sudo drbdadm primary --force r0

如果您檢查內容的程序/drbd (`sudo cat/proc/drbd`) 在這兩個 Vm，您應該會看到 `主要/次要` 上 `hadb01` 和 `Secondary/Primary` 上 `hadb02`, ，此時的解決方案與一致。 客戶可免費透過 10.10.10.0/24 網路同步處理這個 5 GB 磁碟。

在同步處理磁碟後您可以建立檔案系統上 `hadb01`。 基於測試目的，我們會使用 ext2，但下列指示將可建立 ext3 檔案系統：

    mkfs.ext3 /dev/drbd1

### 掛接 DRBD 資源

在 `hadb01` 我們現在可以開始掛接 DRBD 資源。 Debian 及其衍生版本會使用 `/var/lib/mysql` 做為 MySQL 的資料目錄。 我們尚未安裝 MySQL，因此我們將建立此目錄並掛接 DRBD 資源。 在 `hadb01`:

    sudo mkdir /var/lib/mysql
    sudo mount /dev/drbd1 /var/lib/mysql

## 設定 MySQL

您現在可以準備上安裝 MySQL `hadb01`:

    sudo apt-get install mysql-server

如 `hadb02`, ，有兩個選項。 您可以現在安裝 mysql-server，這將會建立 /var/lib/mysql，並在其中填入新的資料目錄，然後繼續移除內容。 在 `hadb02`:

    sudo apt-get install mysql-server
    sudo service mysql stop
    sudo rm –rf /var/lib/mysql/*

第二個選擇是容錯移轉至 `hadb02` ，然後安裝那裡 mysql-server (安裝指令碼會注意到現有安裝，並不會對它)

在 `hadb01`:

    sudo drbdadm secondary –force r0

在 `hadb02`:

    sudo drbdadm primary –force r0
    sudo apt-get install mysql-server

如果您不打算現在容錯移轉 DRBD，雖然第一個選擇可以說是較為繁瑣，但相對是比較容易的。 安裝完成後，您可以開始使用 MySQL 資料庫。 在 `hadb02` (或 drbd 為作用中伺服器的任一部):

    mysql –u root –p
    CREATE DATABASE azureha;
    CREATE TABLE things ( id SERIAL, name VARCHAR(255) );
    INSERT INTO things VALUES (1, "Yet another entity");
    GRANT ALL ON things.\* TO root;

**警告**：最後一個陳述式會有效地停用此資料表中根使用者的驗證。 這應由您的生產等級 GRANT 陳述式取代，這裡僅是為了說明的目的才包括此陳述式。

如果您想要在 VM 外進行查詢，您還必須啟用 MySQL 的網路功能，這正是本指南的目的。 在兩個 Vm 上開啟 `/etc/mysql/my.cnf` 並瀏覽至 `繫結位址`, ，將它從 127.0.0.1 變更為 0.0.0.0。 儲存檔案之後, 發出 `sudo service mysql 重新啟動` 上目前的主要複本。

### 建立 MySQL 負載平衡集合

我們將會回到入口網站，並瀏覽至 `hadb01` VM，然後結束點。 我們將建立新端點，從下拉式清單中選擇 [MySQL (TCP 3306)]，然後核取 [Create new load balanced set] ** 方塊。 將此負載平衡端點取名 `lb mysql`。 除了將時間降低為 5 (秒數，最小值) 以外，我們將維持大部分的選項不變。

建立端點之後，請移至 `hadb02`, ，端點，並建立新的端點，但我們將選擇 `lb mysql`, ，然後從下拉式功能表中選取 [MySQL。 在此步驟中，您也可以使用 Azure CLI。

此時，我們已具備手動叢集作業所需的所有項目。

### 測試負載平衡集合

您可以使用任何 MySQL 用戶端與應用程式 (例如以 Azure 網站身分執行的 phpMyAdmin) 從機器外部執行測試，在此案例中，我們使用在另一部 Linux 機器上的 MySQL 的命令列工具：

    mysql azureha –u root –h hadb.cloudapp.net –e "select * from things;"

### 手動容錯移轉

您現在可以透過關閉 MySQL、切換 DRBD 的主要 VM，然後重新啟動 MySQL 來模擬容錯移轉。

在 hadb01 上：

    service mysql stop && umount /var/lib/mysql ; drbdadm secondary r0

接著，在 hadb02 上：

    drbdadm primary r0 ; mount /dev/drbd1 /var/lib/mysql && service mysql start

手動容錯移轉後，您可以重複遠端查詢，它應可正常運作。

## 設定 Corosync

Corosync 是需要 Pacemaker 才能運作的基礎叢集基礎結構。 若是 Heartbeat v1 和 v2 使用者 (及其他方法，如 Ultramonkey)，Corosync 是 CRM 功能的分割，而 Pacemaker 會維持類似 Hearbeat 的功能。

Corosync 在 Azure 上的主要限制是，Corosync 偏好的通訊順序為多點傳送大於廣播大於單點傳送，但 Microsoft Azure 網路僅支援單點傳送。

還好，Corosync 具備有效的單點傳送模式，唯一的真正限制是，因為所有節點彼此之間不會*神奇地自動*進行通訊，您必須在組態檔中定義節點，包括其 IP 位址。 我們可以使用 Corosync 範例檔案進行單點傳送，只需變更繫結位址、 節點清單和記錄目錄 (Ubuntu 會使用 `/var/log/corosync` 而範例檔案會使用 `/var/log/cluster`) 及啟用仲裁工具。

* * 請注意 `transport: udpu` 指示詞和手動定義的 IP 位址的節點 * *。

在 `/etc/corosync/corosync.conf` 這兩個節點:

    totem {
      version: 2
      crypto_cipher: none
      crypto_hash: none
      interface {
        ringnumber: 0
        bindnetaddr: 10.10.10.0
        mcastport: 5405
        ttl: 1
      }
      transport: udpu
    }
    
    logging {
      fileline: off
      to_logfile: yes
      to_syslog: yes
      logfile: /var/log/corosync/corosync.log
      debug: off
      timestamp: on
      logger_subsys {
        subsys: QUORUM
        debug: off
        }
      }
    
    nodelist {
      node {
        ring0_addr: 10.10.10.4
        nodeid: 1
      }
    
      node {
        ring0_addr: 10.10.10.5
        nodeid: 2
      }
    }
    
    quorum {
      provider: corosync_votequorum
    }

我們會複製這兩部 VM 上的組態檔，並在這兩個節點上啟動 Corosync：

    sudo service start corosync

在啟動服務不久後，系統應會在目前環中建立叢集，並組成仲裁。 我們可以透過審查記錄來檢查此功能，或者：

    sudo corosync-quorumtool –l

接著應有與下圖類似的輸出：

![corosync-quorumtool -l sample output](media/virtual-machines-linux-mysql-cluster/image001.png)

## 設定 Pacemaker

Pacemaker 會使用叢集來監視資源，定義在主要故障時將這些資源切換到次要。 在所有其他選擇中，您可從一組可用指令碼或從 LSB (如 init) 指令碼來定義資源。

我們要 Pacemaker「擁有」DRBD 資源、掛接點及 MySQL 服務。 如果 Pacemaker 可以在主要發生問題時，依正確順序來開啟與關閉 DRBD、掛接/取消掛接 DRBD 及啟動/停止 MySQL，則我們的安裝便已完成。

首次安裝 Pacemaker 時，您的組態應十分簡單，如下所示：

    node $id="1" hadb01
      attributes standby="off"
    node $id="2" hadb02
      attributes standby="off"

執行檢查 `sudo crm 設定顯示`。 現在，建立檔案 (例如 `/tmp/cluster.conf`) 使用下列資源:

    primitive drbd_mysql ocf:linbit:drbd \
          params drbd_resource="r0" \
          op monitor interval="29s" role="Master" \
          op monitor interval="31s" role="Slave"
    
    ms ms_drbd_mysql drbd_mysql \
          meta master-max="1" master-node-max="1" \
            clone-max="2" clone-node-max="1" \
            notify="true"
    
    primitive fs_mysql ocf:heartbeat:Filesystem \
          params device="/dev/drbd/by-res/r0" \
          directory="/var/lib/mysql" fstype="ext3"
    
    primitive mysqld lsb:mysql
    
    group mysql fs_mysql mysqld
    
    colocation mysql_on_drbd \
           inf: mysql ms_drbd_mysql:Master
    
    order mysql_after_drbd \
           inf: ms_drbd_mysql:promote mysql:start
    
    property stonith-enabled=false
    
    property no-quorum-policy=ignore

現在，將檔案載入組態 (您只需要在一個節點上執行此動作)：

    sudo crm configure
      load update /tmp/cluster.conf
      commit
      exit

另外，請確定開機時會啟動兩個節點的 Pacemaker：

    sudo update-rc.d pacemaker defaults

經過幾秒鐘，並使用 `sudo crm_mon – L`, ，確認其中一個節點已成為叢集的主要，而且正在執行的所有資源。 您可以使用掛接和 PS 來檢查資源是否正在執行。

下列螢幕擷取畫面顯示 `crm_mon` 且其中一個節點已停止 (使用 CONTROL-C 結束)

![crm_mon node stopped](media/virtual-machines-linux-mysql-cluster/image002.png)

此快照說明兩個節點 (一個主要和一個從屬)：

![crm_mon operational master/slave](media/virtual-machines-linux-mysql-cluster/image003.png)

## 測試

我們已準備好開始自動容錯移轉模擬。 作法有二：軟式和硬式。 軟式方法使用叢集的關機功能: `crm_standby-U ' uname-n'-v 上`。 在主要 VM 上使用此選項，從屬 VM 便會取代主要 VM。 請務必將此選項設回關閉 (否則 crm_mon 將通知您某個節點正在待命中)

硬式方法是透過入口網站將主要 VM (hadb01) 關機，或變更 VM 上的執行層級 (例如，終止、關機)，然後發出主要 VM 出現故障的訊號來協助 Corosync 和 Pacemaker。 我們可以測試這個方法 (在維護期間非常有用)，但我們也可以透過凍結 VM 來強制執行此案例。

## STONITH

它應該能透過 Azure CLI 發出 VM 關機命令，來代替可控制實體裝置的 STONITH 指令碼。 您可以使用 `/usr/lib/stonith/plugins/external/ssh` 作為基礎，並在叢集的組態中啟用 STONITH。 系統應會全域安裝 Azure CLI，並載入叢集使用者的發佈設定/設定檔。

範例程式碼上有可用資源 [GitHub](https://github.com/bureado/aztonith)。 您需要變更叢集的組態新增至下列 `sudo crm 設定`:

    primitive st-azure stonith:external/azure \
      params hostlist="hadb01 hadb02" \
      clone fencing st-azure \
      property stonith-enabled=true \
      commit

**注意：**此指令碼不會執行使用/停用檢查。 原始的 SSH 資源會有 15 個 Ping 檢查，但 Azure VM 的復原時間可能會有較多的變數。

## 限制

套用下列限制：

- 將 DRBD 作為資源 Pacemaker 會使用管理的 linbit DRBD 資源指令碼 `下的 drbdadm` 關閉一個節點，即使節點只進入待命時。 因為主要 VM 在開始遭到寫入時，從屬 VM 並不會同步處理 DRBD 資源，所以這不是理想的作法。 如果主要 VM 不是順利關機，從屬 VM 會接收較舊的檔案系統狀態。 可能解決此問題的方法有兩種：
  - 強制執行 `drbdadm up r0` 在所有叢集節點中透過本機 (未叢集化) 看門狗，或者，
  - 編輯 linbit DRBD 指令碼確保 `下` 中不會呼叫， `/usr/lib/ocf/resource.d/linbit/drbd`。
- 負載平衡器至少需要 5 秒的時間進行回應，因此應用程式應為叢集感知且應可容許逾時；其他架構也可提供協助，例如應用程式內部佇列、查詢中繼軟體等。
- 若要確保寫入作業會以正常的步調結束，且會儘可能頻繁地將快取清除到磁碟以減少記憶體損失，MySQL 調整是有必要的。
- VM 互連中的寫入效能將會取決於虛擬開關，因為虛擬開關是 DRBD 用來複寫裝置的機制。





