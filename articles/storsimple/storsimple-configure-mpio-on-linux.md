<properties 
   pageTitle="在 StorSimple Linux 主機上設定 MPIO | Microsoft Azure"
   description="在連線到執行 CentOS 6.6 之 Linux 主機的 StorSimple 上設定 MPIO"
   services="storsimple"
   documentationCenter="NA"
   authors="alkohli"
   manager="adinah"
   editor="tysonn" />
<tags 
   ms.service="storsimple"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="12/02/2015"
   ms.author="alkohli" />


# 在執行 CentOS 的 StorSimple 主機上設定 MPIO

本文說明在 Centos 6.6 主機伺服器上設定多重路徑 IO (MPIO) 所需的步驟。 主機伺服器會連線到您的 Microsoft Azure StorSimple 裝置，以透過 iSCSI 啟動器取得高可用性。 文中詳細描述多重路徑裝置的自動探索，以及 StorSimple 磁碟區特有的設定。

此程序適用於 StorSimple 8000 系列裝置的所有模型。
>[AZURE.NOTE] 此程序無法用於 StorSimple 虛擬裝置。 如需詳細資訊，請參閱〈如何設定虛擬裝置的主機伺服器〉。

## 關於多重路徑

多重路徑功能可讓您在主機伺服器與儲存體裝置之間設定多個 I/O 路徑。 這些 I/O 路徑是可包含不同纜線、開關、網路介面及控制站的實體 SAN 連線。 多重路徑功能會彙總 I/O 路徑，以設定與所有彙總路徑相關聯的新裝置。

多重路徑有雙重目的：

- **高可用性**︰如果 I/O 路徑的任何元素 (例如纜線、開關、網路介面或控制站) 失敗，它會提供替代路徑。

- **負載平衡**︰視您的儲存體裝置的組態而定，它可透過偵測 I/O 路徑上的負載及動態重新平衡這些負載來改善效能。


### 關於多重路徑元件

Linux 中的多重路徑是由核心元件和下列的使用者空間元件所組成。

- **核心**︰主要元件是 *device-mapper*，可重新路由傳送 I/O 及支援路徑和路徑群組的容錯移轉。

1. **使用者空間**：這些是 *multipath-tools*，可藉由指示 device-mapper 多重路徑模組該怎麼做來管理多重路徑的裝置。 這些工具包括：

    - **Multipath**︰列出及設定多重路徑的裝置。

    - **Multipathd**︰可執行多重路徑及監視路徑的精靈。

    - **Devmap-name**︰提供有意義的裝置名稱給 udev for devmaps。

    - **Kpartx**︰將線性 devmaps 對應至裝置資料分割，讓多重路徑對應可分割。

    - **Multipath.conf**︰多重路徑精靈的組態檔，用來覆寫內建組態資料表。

### 關於 multipath.conf 組態檔

組態檔 `/etc/multipath.conf` 許多多重路徑功能可讓使用者可設定。  `多重路徑` 命令與核心服務精靈 `multipathd` 使用此檔案中的資訊。 只有在多重路徑裝置的設定期間才會參考此檔案。 請確定已進行所有變更，然後再執行 `多重路徑` 命令。 如果您之後修改該檔案，則必須停止後重新啟動 multipathd，變更才會生效。

multipath.conf 有五個區段：

- **系統層級的預設值** *(defaults)*：您可以覆寫系統層級預設值。

1. **列入封鎖清單的裝置** *(blacklist)*：您可以指定不受 device-mapper 控制的裝置清單。

1. **封鎖清單例外狀況** *(blacklist_exceptions)*：您可以識別要被視為多重路徑裝置的特定裝置，即使這些裝置已列入封鎖清單。

1. **儲存體控制站特定設定** *(devices)*：您可以指定將套用到裝置的組態設定 (具有廠商和產品資訊)。

1. **裝置特定設定** *(multipaths)*：您可以使用本節來微調個別 LUN 的組態設定。

## 在連線到 Linux 主機的 StorSimple 上設定多重路徑

可以設定連線到 Linux 主機的 StorSimple 裝置，以取得高可用性和負載平衡。 例如，如果 Linux 主機有兩個連接到 SAN 的介面，而裝置有兩個連接到 SAN 的介面，以致這些介面位於相同的子網路上，則會有 4 個路徑可用。 不過，如果裝置瀚主機介面上的每個 DATA 介面位於不同的 IP 子網路上 (且不可路由傳送)，則只有 2 路徑可用。 您可以設定多重路徑，以便自動探索所有可用的路徑、選擇這些路徑的負載平衡演算法、套用僅限 StorSimple 磁碟區的特定組態設定，然後啟用和驗證多重路徑。

下列程序描述有兩個網路介面的 StorSimple 裝置連接到有兩個網路介面的主機時，要如何設定多重路徑。

## 必要條件

本節將詳細說明 CentOS 伺服器和 StorSimple 裝置的設定必要條件。

### 在 CentOS 主機上

1. 確定 CentOS 主機已啟用 2 個網路介面。 輸入：

 `ifconfig`

 下列範例顯示兩個網路介面時的輸出 (`eth0` 和 `eth1`) 出現在主應用程式。

     [root@centosSS ~]# ifconfig
     eth0  Link encap:Ethernet  HWaddr 00:15:5D:A2:33:41  
     inet addr:10.126.162.65  Bcast:10.126.163.255  Mask:255.255.252.0
     inet6 addr: 2001:4898:4010:3012:215:5dff:fea2:3341/64 Scope:Global
     inet6 addr: fe80::215:5dff:fea2:3341/64 Scope:Link
     UP BROADCAST RUNNING MULTICAST  MTU:1500  Metric:1
     RX packets:36536 errors:0 dropped:0 overruns:0 frame:0
     TX packets:6312 errors:0 dropped:0 overruns:0 carrier:0
     collisions:0 txqueuelen:1000 
     RX bytes:13994127 (13.3 MiB)  TX bytes:645654 (630.5 KiB)
    
     eth1  Link encap:Ethernet  HWaddr 00:15:5D:A2:33:42  
     inet addr:10.126.162.66  Bcast:10.126.163.255  Mask:255.255.252.0
     inet6 addr: 2001:4898:4010:3012:215:5dff:fea2:3342/64 Scope:Global
     inet6 addr: fe80::215:5dff:fea2:3342/64 Scope:Link
     UP BROADCAST RUNNING MULTICAST  MTU:1500  Metric:1
     RX packets:25962 errors:0 dropped:0 overruns:0 frame:0
     TX packets:11 errors:0 dropped:0 overruns:0 carrier:0
     collisions:0 txqueuelen:1000 
     RX bytes:2597350 (2.4 MiB)  TX bytes:754 (754.0 b)
    
     loLink encap:Local Loopback  
     inet addr:127.0.0.1  Mask:255.0.0.0
     inet6 addr: ::1/128 Scope:Host
     UP LOOPBACK RUNNING  MTU:65536  Metric:1
     RX packets:12 errors:0 dropped:0 overruns:0 frame:0
     TX packets:12 errors:0 dropped:0 overruns:0 carrier:0
     collisions:0 txqueuelen:0 
     RX bytes:720 (720.0 b)  TX bytes:720 (720.0 b)

1. 在 CentOS 伺服器上安裝 *iSCSI-initiator-utils*。 執行下列步驟以安裝 *iSCSI-initiator-utils*。

    1. 以登入 `根` 到 CentOS 主機。

    1. 安裝 *iSCSI-initiator-utils*。 輸入：

        `yum 安裝 iscsi 啟動器期間`

    1. 在成功安裝 *iSCSI-Initiator-utils* 之後，啟動 iSCSI 服務。 輸入：

        `服務 iscsid 入門`

        在情況下， `iscsid` 可能無法實際啟動和 `-強制` 可能需要的選項

    1. 若要確保 iSCSI 啟動器會在開機時啟用，請使用 `chkconfig` 命令，以啟用服務。

        `chkconfig iscsi 上`

    1. 若要驗證該服務是否正確設定，請執行以下命令：

        `chkconfig-清單 |grep iscsi`

        下方顯示一項範例輸出。

            iscsi   0:off   1:off   2:on3:on4:on5:on6:off
            iscsid  0:off   1:off   2:on3:on4:on5:on6:off

        在上述範例中，您可以看到 iSCSI 環境將在開機時於執行層級 2、 3、 4 和 5 執行。

1. 安裝 *device-mapper-multipath*。 輸入：

    `yum 安裝裝置的對應程式-多重路徑`

    將會開始安裝。 在系統提示您進行確認時，輸入 **Y**。



### 在 StorSimple 裝置上

您的 StorSimple 裝置應該︰

- 至少有兩個介面已啟用 iSCSI。 若要確認這兩個介面已在 StorSimple 裝置上啟用 iSCSI，請在 StorSimple 裝置的 Azure 傳統入口網站中執行下列步驟︰

    1. 登入 StorSimple 裝置的傳統入口網站。

    1. 選取 StorSimple Manager 服務，按一下 [裝置]****，然後選擇該特定 StorSimple 裝置。 按一下 [設定]**** 並驗證網路介面設定。 下面顯示的螢幕擷取畫面包含已啟用 iSCSI 的兩個網路介面。 以下 DATA 2 和 DATA 3 兩個 10 GbE 介面都已啟用 iSCSI。

        ![MPIO StorSimple DATA 2 設定](./media/storsimple-configure-mpio-on-linux/IC761347.png)

        ![MPIO StorSimple DATA 3 設定](./media/storsimple-configure-mpio-on-linux/IC761348.png)

        在 [設定]**** 頁面中

        1. 確定這兩個網路介面都已啟用 iSCSI。 [啟用 iSCSI]**** 欄位應設定為 [是]****。
        2. 確定網路介面的速度相同，兩者都應該是 1 GbE 或 10 GbE。
        3. 請記下已啟用 iSCSI 的介面的 IPv4 位址，並加以儲存供稍後用於主機上。

- 應可從 CentOS 伺服器存取 StorSimple 裝置上的 iSCSI 介面。

    若要確認這點，您需要在主機伺服器上提供 StorSimple 已啟用 iSCSI 的網路介面的 IP 位址。 使用的命令以及 DATA2 (10.126.162.25) 和 DATA3 (10.126.162.26) 的對應輸出如下所示：

        [root@centosSS ~]# iscsiadm -m discovery -t sendtargets -p 10.126.162.25:3260
        10.126.162.25:3260,1 iqn.1991-05.com.microsoft:storsimple8100-shx0991003g44mt-target
        10.126.162.26:3260,1 iqn.1991-05.com.microsoft:storsimple8100-shx0991003g44mt-target



### 硬體組態

建議您連接不同路徑上的兩個 iSCSI 網路介面，以獲得備援。 下圖顯示可取得高可用性以及使 CentOS 伺服器與 StorSimple 裝置的多重路徑達到平衡負載的建議硬體組態。

![連線至 Linux 主機之 StorSimple 的 MPIO 硬體設定](./media/storsimple-configure-mpio-on-linux/MPIOHardwareConfigurationStorSimpleToLinuxHost2M.png)

如上圖所示：

- StorSimple 裝置處於具有兩個控制器的主動-被動組態中。

- 兩個 SAN 開關會連接到您的裝置控制器。

- 兩個 iSCSI 啟動器會在 StorSimple 裝置上啟用。

- CentOS 主機已啟用 2 個網路介面。

如果主機和資料介面均可路由傳送，則上述組態將會在您的裝置與主機之間產生 4 個不同的路徑。
>[AZURE.IMPORTANT] 
>
>-我們建議您不要混合使用 1 GbE 與 10 GbE 網路介面的多重路徑。 使用兩個網路介面時，這兩個介面的類型應要完全相同。
>開啟您的 StorSimple 裝置 DATA0、 DATA1、 DATA4 和 DATA5 是 1 GbE 介面而 DATA2 和 DATA3 是 10 GbE 網路介面。 |

## 組態步驟

多重路徑的設定步驟涉及設定自動探索的可用路徑、指定要使用的負載平衡演算法、啟用多重路徑及最後驗證組態。 下列各節會詳細討論這些步驟。

### 步驟 1：為自動探索設定多重路徑

系統可以自動探索及設定多重路徑支援的裝置。

1. 初始化 `/etc/multipath.conf` 檔案。 輸入：

     `複製 mpathconf-啟用`

    上述命令會建立 `sample/etc/multipath.conf` 檔案。

1. 啟動多重路徑服務。 輸入：

    `複製服務 multipathd 入門`

    您會看到下列輸出︰

    `啟動 multipathd 精靈:`

1. 啟用多重路徑的自動探索。 輸入：

    `mpathconf-find_multipaths y`

    這將會修改預設值區段您 `multipath.conf` ，如下所示:

        defaults {
        find_multipaths yes
        user_friendly_names yes
        path_grouping_policy multibus
        }


### 步驟 2：為 StorSimple 磁碟區設定多重路徑

根據預設，所有裝置都會列入 multipath.conf 檔案的封鎖清單並遭到略過。 您必須建立封鎖清單例外狀況，允許 StorSimple 裝置中的磁碟區啟動多重路徑。

1. 編輯 `/etc/mulitpath.conf` 檔案。 輸入：

    `vi /etc/multipath.conf`

1. 找出 multipath.conf 檔案中的 blacklist_exceptions 區段。 您的 StorSimple 裝置必須列為本節中的封鎖清單例外狀況。 您可以在此檔案中取消註解相關行，如下所示修改該檔案 (僅使用您所用裝置的特定模型)︰

        blacklist_exceptions {
            device {
                       vendor  "MSFT"
                       product "STORSIMPLE 8100*"
            }
            device {
                       vendor  "MSFT"
                       product "STORSIMPLE 8600*"
            }
           }


### 步驟 3：設定循環配置資源多重路徑

此負載平衡演算法會以平衡且循環配置資源的方式，使用作用中控制器的所有可用多重路徑。

1. 編輯 `/etc/multipath.conf` 檔案。 輸入：

    `vi /etc/multipath.conf`

1. 在 `預設` 區段中，將 `path_grouping_policy` 至 `multibus`。  `Path_grouping_policy` 指定群組原則套用到未指定 multipaths 的預設路徑。 defaults 區段會如下所示。

        defaults {
                user_friendly_names yes
                path_grouping_policy multibus
        }




> [AZURE.NOTE] 
> 最常見的值的 `path_grouping_policy` 包括:

> - failover = 每一個優先順序群組 1 個路徑
> - multibus = 1 個優先順序群組中的所有有效路徑

### 步驟 4：啟用多重路徑

1. 重新啟動 `multipathd` 服務精靈。 輸入：

    `重新啟動服務 multipathd`

1. 輸出應如下所示：

        [root@centosSS ~]# service multipathd start
        Starting multipathd daemon:  [OK]





### 步驟 5：驗證多重路徑

1. 如下所示，先確定 iSCSI 連接是以 StorSimple 裝置建立︰

    1. 探索您的 StorSimple 裝置。 輸入：

        `iscsiadm-m 探索-t sendtargets-p < 裝置上的網路介面的 IP 位址 >: < iSCSI 連接埠上的 StorSimple 裝置 >`

        當 DATA0 的 IP 位址為 10.126.162.25 且連接埠 3260 在 StorSimple 裝置上開啟以便處理輸出 iSCSI 流量時，輸出會如下所示︰

            10.126.162.25:3260,1 iqn.1991-05.com.microsoft:storsimple8100-shx0991003g00dv-target
            10.126.162.26:3260,1 iqn.1991-05.com.microsoft:storsimple8100-shx0991003g00dv-target

        複製您的 StorSimple 裝置的 IQN `iqn.1991 05.com.microsoft:storsimple8100 shx0991003g00dv-目標`, ，上述輸出中。

    1. 使用目標 IQN 連接到此裝置。 StorSimple 裝置在此是 iSCSI 目標。 輸入：

        `iscsiadm-m 節點-登入-T < iSCSI 目標 IQN >`

        下列範例顯示輸出結果與目標 IQN 的 `iqn.1991 05.com.microsoft:storsimple8100 shx0991003g00dv-目標`。 此輸出指出您已順利連接到您的裝置上已啟用 iSCSI 的兩個網路介面。

            Logging in to [iface: eth0, target: iqn.1991-05.com.microsoft:storsimple8100-shx0991003g00dv-target, portal: 10.126.162.25,3260] (multiple)
            Logging in to [iface: eth1, target: iqn.1991-05.com.microsoft:storsimple8100-shx0991003g00dv-target, portal: 10.126.162.25,3260] (multiple)
            Logging in to [iface: eth0, target: iqn.1991-05.com.microsoft:storsimple8100-shx0991003g00dv-target, portal: 10.126.162.26,3260] (multiple)
            Logging in to [iface: eth1, target: iqn.1991-05.com.microsoft:storsimple8100-shx0991003g00dv-target, portal: 10.126.162.26,3260] (multiple)
            Login to [iface: eth0, target: iqn.1991-05.com.microsoft:storsimple8100-shx0991003g00dv-target, portal: 10.126.162.25,3260] successful.
            Login to [iface: eth1, target: iqn.1991-05.com.microsoft:storsimple8100-shx0991003g00dv-target, portal: 10.126.162.25,3260] successful.
            Login to [iface: eth0, target: iqn.1991-05.com.microsoft:storsimple8100-shx0991003g00dv-target, portal: 10.126.162.26,3260] successful.
                Login to [iface: eth1, target: iqn.1991-05.com.microsoft:storsimple8100-shx0991003g00dv-target, portal: 10.126.162.26,3260] successful.

        如果您在此只看到一個主機介面和兩個路徑，您必須在主機上針對 iSCSI 啟用這兩個介面。 您可以依照 [詳細 Linux 文件中的指示](https://access.redhat.com/documentation/Red_Hat_Enterprise_Linux/5/html/Online_Storage_Reconfiguration_Guide/iscsioffloadmain.html)。

    1. 磁碟區會從 StorSimple 裝置公開至 CentOS 伺服器。 如需詳細資訊，請參閱 [步驟 6: 建立磁碟區](storsimple-deployment-walkthrough.md#step-6-create-a-volume) 透過 StorSimple 裝置上的 Azure 傳統入口網站。

    1. 驗證可用的路徑。 輸入：

        `多重路徑 – l`

        下列範例顯示透過兩個可用路徑連接到單一主機網路介面的 StorSimple 裝置上兩個網路介面的輸出。

            mpathb (36486fd20cc081f8dcd3fccb992d45a68) dm-3 MSFT,STORSIMPLE 8100
            size=100G features='0' hwhandler='0' wp=rw
            `-+- policy='round-robin 0' prio=0 status=active
              |- 7:0:0:1 sdc 8:32 active undef running
              `- 6:0:0:1 sdd 8:48 active undef running

        下列範例顯示透過四個可用路徑連接到兩個主機網路介面的 StorSimple 裝置上兩個網路介面的輸出。

            mpathb (36486fd27a23feba1b096226f11420f6b) dm-2 MSFT,STORSIMPLE 8100
            size=100G features='0' hwhandler='0' wp=rw
            `-+- policy='round-robin 0' prio=0 status=active
              |- 17:0:0:0 sdb 8:16 active undef running
              |- 15:0:0:0 sdd 8:48 active undef running
              |- 14:0:0:0 sdc 8:32 active undef running
              `- 16:0:0:0 sde 8:64 active undef running

        設定路徑之後，請參閱主機作業系統 (Centos 6.6) 的特定指示來掛接及格式化這個磁碟區。


## 多重路徑疑難排解

如果您在多重路徑設定期間遇到任何問題，請參閱本節所提供的一些實用秘訣。

問： 我看不到在變更 `multipath.conf` 生效的檔案。

答： 如果您所做的任何變更 `multipath.conf` 檔案中，您必須重新啟動多重路徑服務。 輸入以下命令：

    service multipathd restart

問： 我已在 StorSimple 裝置上啟用兩個網路介面以及在主機上啟用兩個網路介面。 當我列出可用的路徑時，我只看到兩個路徑。 我預計看見四個可用的路徑。

答： 確定這兩個路徑位於相同的子網路上並可路由傳送。 如果網路介面位於不同的 vLAN 上且不可路由傳送，您只會看到兩個路徑。 確認這點的唯一方法就是確定您可以從 StorSimple 裝置上的網路介面存取這兩個主機介面。 您必須 [連絡 Microsoft 支援服務](storsimple-contact-microsoft-support.md) ，這項驗證只能透過支援工作階段。

問： 當我列出可用的路徑時，我看不到任何輸出。

答： 一般而言，看不到任何多重路徑路徑建議多重服務精靈發生問題，並最有可能是任何在這裡的問題出在 `multipath.conf` 檔案。

此外，也值得檢查您可以在連接到目標後實際看到某些磁碟，因為多重路徑清單沒有回應也可能表示您沒有任何磁碟。

- 使用下列命令來重新掃描 SCSI 匯流排：

    `$ 重新掃描 scsi bus.sh `(sg3_utils 封裝的一部份)

- 輸入下列命令：

    `$ dmesg |grep sd *`

- 或

    `$ fdisk – l`

    這些將傳回最近新增磁碟的詳細資料。

- 若要判斷它是否為 StorSimple 磁碟，請使用下列命令︰

    `貓 /sys/區塊/< 磁碟 >/裝置/模型`

    這會傳回一個字串，判斷它是否為 StorSimple 磁碟。

有一個比較不可能的可能原因也可能是 iscsid pid 過時。 使用下列命令從 iSCSI 工作階段登出：

    iscsiadm -m node --logout -p <Target_IP>

對 ISCSI 目標 (也就是您的 StorSimple 裝置) 上所有連接的網路介面重複此命令。 從所有 iSCSI 工作階段登出後，請使用 iSCSI 目標 IQN 來重新建立 iSCSI 工作階段。 輸入以下命令：

    iscsiadm -m node --login -T <TARGET_IQN>

問： 我不確定我的裝置是否已列入允許清單。

答： 若要驗證您的裝置是否已列入允許清單，請使用下列疑難排解互動式命令︰

    multipathd –k
    multipathd> show devices
    available block devices:
    ram0 devnode blacklisted, unmonitored
    ram1 devnode blacklisted, unmonitored
    ram2 devnode blacklisted, unmonitored
    ram3 devnode blacklisted, unmonitored
    ram4 devnode blacklisted, unmonitored
    ram5 devnode blacklisted, unmonitored
    ram6 devnode blacklisted, unmonitored
    ram7 devnode blacklisted, unmonitored
    ram8 devnode blacklisted, unmonitored
    ram9 devnode blacklisted, unmonitored
    ram10 devnode blacklisted, unmonitored
    ram11 devnode blacklisted, unmonitored
    ram12 devnode blacklisted, unmonitored
    ram13 devnode blacklisted, unmonitored
    ram14 devnode blacklisted, unmonitored
    ram15 devnode blacklisted, unmonitored
    loop0 devnode blacklisted, unmonitored
    loop1 devnode blacklisted, unmonitored
    loop2 devnode blacklisted, unmonitored
    loop3 devnode blacklisted, unmonitored
    loop4 devnode blacklisted, unmonitored
    loop5 devnode blacklisted, unmonitored
    loop6 devnode blacklisted, unmonitored
    loop7 devnode blacklisted, unmonitored
    sr0 devnode blacklisted, unmonitored
    sda devnode whitelisted, monitored
    dm-0 devnode blacklisted, unmonitored
    dm-1 devnode blacklisted, unmonitored
    dm-2 devnode blacklisted, unmonitored
    sdb devnode whitelisted, monitored
    sdc devnode whitelisted, monitored
    dm-3 devnode blacklisted, unmonitored

如需詳細資訊，請移至 [使用多重路徑的互動式命令進行疑難排解](http://www.centos.org/docs/5/html/5.1/DM_Multipath/multipath_config_confirm.html)。

## 有用的命令清單

| 類型| 命令| 說明|
|---|---|---|
| **iSCSI**| `服務 iscsid 入門`| 啟動 iSCSI 服務|
| | `服務 iscsid 停止`| 停止 iSCSI 服務|
| | `重新啟動服務 iscsid`| 重新啟動 iSCSI 服務|
| | `iscsiadm-m 探索-t sendtargets-p < TARGET_IP >`| 探索指定的位址上的可用目標|
| | `iscsiadm-m 節點-登入-T < TARGET_IQN >`| 登入 iSCSI 目標|
| | `iscsiadm-m 節點-登出-p < Target_IP >`| 登出 iSCSI 目標|
| | `貓 /etc/iscsi/initiatorname.iscsi`| 列印的 iSCSI 啟動器名稱|
| | `iscsiadm – m 工作階段 – s < 工作階段識別碼 >-P 3`| 檢查的 iSCSI 工作階段和在主機上探索到的磁碟區的狀態|
| | `iscsi – m 工作階段`| 顯示所有主機與 StorSimple 裝置之間建立 iSCSI 工作階段|
| | | |
| **多重路徑**| `服務 multipathd 入門`| 啟動精靈多重路徑|
| | `服務 multipathd 停止`| 停止多重路徑精靈|
| | `重新啟動服務 multipathd`| 重新啟動服務精靈多重路徑|
| | `上的 chkconfig multipathd` </br> 或者 </br> `mpathconf – with_chkconfig y`| 啟用多重路徑為在開機時啟動精靈|
| | `multipathd – k`| 啟動互動式主控台進行疑難排解|
| | `多重路徑 – l`| 多重路徑清單的連線和裝置|
| | `mpathconf-啟用`| 建立範例 mulitpath.conf 檔案中的 `/etc/mulitpath.conf`|
| | | |

## 後續步驟

當您在 Linux 主機上設定 MPIO 時，您可能也需要參考下列 CentoS 6.6 文件︰

- [設定 MPIO CentOS 上](http://www.centos.org/docs/5/html/5.1/DM_Multipath/setup_procedure.html)
- [Linux 訓練指南](http://linux-training.be/files/books/LinuxAdm.pdf)






