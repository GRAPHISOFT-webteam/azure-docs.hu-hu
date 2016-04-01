<properties
 pageTitle="設定 Windows RDMA 叢集以執行 MPI 應用程式 | Microsoft Azure"
 description="了解如何建立具有 A8 或 A9 大小之 VM 的 Windows HPC Pack 叢集，以使用 Azure RDMA 網路執行 MPI 應用程式。"
 services="virtual-machines"
 documentationCenter=""
 authors="dlepow"
 manager="timlt"
 editor=""
 tags="azure-service-management,hpc-pack"/>
<tags
ms.service="virtual-machines"
 ms.devlang="na"
 ms.topic="article"
 ms.tgt_pltfrm="vm-windows"
 ms.workload="big-compute"
 ms.date="09/28/2015"
 ms.author="danlep"/>

# 使用 HPC Pack 和 A8 與 A9 執行個體設定 Windows RDMA 叢集，以執行 MPI 應用程式

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-classic-include.md)] 資源管理員模型。


本文將說明如何設定 Windows RDMA 叢集與 Azure 中 [Microsoft HPC Pack](https://technet.microsoft.com/library/cc514029) 和 [大小 A8 和 A9 計算 instensive 執行個體](virtual-machines-a8-a9-a10-a11-specs.md) 執行平行訊息傳遞介面 (MPI) 應用程式。 當您設定 A8 和 A9 大小的 Windows Server 執行個體以執行支援的 MPI 實作時，MPI 應用程式可透過低延遲、高輸送量網路，在運用遠端直接記憶體存取 (RDMA) 技術的 Azure 中進行有效率的通訊。

>[AZURE.NOTE] Azure 的 Windows RDMA 目前支援使用 Microsoft Network Direct 介面 A8 和 A9 執行個體之間通訊的 MPI 應用程式。
>
> Azure 也提供 A10 和 A11 大量運算執行個體，包含與 A8 和 A9 執行個體相同的處理能力，但不含 RDMA 後端網路的連接。 若要在 Azure 中執行 MPI 工作負載，使用 A8 和 A9 執行個體通常可獲得最佳效能。

如果您想要存取 Azure RDMA 網路，請參閱 Linux Vm 上執行 MPI 工作負載 [設定 Linux RDMA 叢集以執行 MPI 應用程式](virtual-machines-linux-cluster-rdma.md)。

## Windows HPC 叢集部署選項
Microsoft HPC Pack 是在 Azure 中建立 Windows Server 型 HPC 叢集時建議使用的工具。 在使用 A8 和 A9 執行個體時，HPC Pack 會是執行可存取 Azure 中 RDMA 網路之 Windows MPI 應用程式最有效的方式。 HPC Pack 包含 Windows 訊息傳遞介面之 Microsoft 實作的執行階段環境。

這篇文章介紹兩個叢集 A8 和 A9 部署案例
使用 Microsoft HPC Pack 的執行個體。

* 案例 1. 部署大量運算背景工作角色執行個體 (PaaS)

* 案例 2. 在大量運算 VM 中部署運算節點 (IaaS)

## 必要條件

* **檢閱 [背景資訊和考量](virtual-machines-a8-a9-a10-a11-specs.md)** 關於大量運算執行個體


* **Azure 訂用帳戶** -如果您沒有帳戶，您可以建立免費試用帳戶只需要幾分鐘的時間。 如需詳細資料，請參閱 [Azure 免費試用](http://azure.microsoft.com/pricing/free-trial/)。


* **核心配額** -您可能需要增加核心配額以部署 A8 或 A9 Vm 的叢集。 例如，如果您想要使用 HPC Pack 部署 8 個 A9 執行個體，將需要至少 128 個核心。 若要增加配額，請開啟 [線上客戶支援要求](http://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/) 不另外加收費用。

## 案例 1. 部署大量運算背景工作角色執行個體 (PaaS)


從現有的 HPC Pack 叢集，加入 Azure 背景工作角色執行個體 （Azure 節點） 的雲端中執行額外的計算資源
服務 (PaaS)。 這項功能，也稱為 「 高載至 Azure 」 從 HPC
組件支援各式各樣的背景工作角色執行個體的大小。 若要使用
計算密集型執行個體，只要指定的大小為 A8 或 A9 時
加入 Azure 節點。

以下是暴增至 A8 或 A9 Azure 執行個體中的步驟
現有的 （通常是內部） 叢集。 使用類似的程序
將背景工作角色執行個體加入至部署 HPC Pack 前端節點
在 Azure VM。

>[AZURE.NOTE] 若要使用 HPC Pack 擴充至 Azure 的教學課程，請參閱 [設定混合式 HPC pack 叢集](../cloud-services/cloud-services-setup-hybrid-hpcpack-cluster.md)。 請留意下列步驟中僅適用於 A8 和 A9 大小之 Azure 節點的考量。

![將量擴大到 Azure][burst]



4. **部署及設定 HPC Pack 2012 R2 前端節點**

    下載最新的 HPC Pack 安裝套件從 [Microsoft 下載中心](https://www.microsoft.com/download/details.aspx?id=49922)。 需求及準備 Azure 高載部署的指示，請參閱 [HPC Pack 快速入門指南](https://technet.microsoft.com/library/jj884144.aspx) 和 [使用 Microsoft HPC Pack 擴充至 Azure](https://technet.microsoft.com/library/gg481749.aspx)

5. **在 Azure 訂用帳戶中設定管理憑證**

    設定憑證以保護前端節點與 Azure 之間的連線。 選項及程序，請參閱 [案例，以設定 Azure 管理憑證的 HPC Pack](http://technet.microsoft.com/library/gg481759.aspx)。

6. **建立新的雲端服務和儲存體帳戶**

    使用 Azure 傳統入口網站建立雲端服務和儲存體帳戶，以在可使用大量運算執行個體的區域中進行部署。 (請勿將雲端服務和儲存體帳戶與用於其他部署的現有同質群組產生關聯。)

7. **建立 Azure 節點範本**

    使用 [HPC 叢集管理員] 中的 [建立節點範本精靈]。 如需步驟，請參閱 [建立 Azure 節點範本](http://technet.microsoft.com/library/gg481758.aspx#BKMK_Templ) 」 步驟來部署 Azure 節點使用 Microsoft HPC Pack 中 」。

    為了進行初始測試，建議您在範本中設定手動可用性原則。

8. **將節點新增至叢集**

    使用 [HPC 叢集管理員] 中的 [新增節點精靈]。 如需詳細資訊，請參閱 [加入 Azure 節點新增至 Windows HPC 叢集](http://technet.microsoft.com/library/gg481758.aspx#BKMK_Add)。

    指定節點的大小時，請選取 A8 或 A9。

9. **啟動 (佈建) 節點並使其上線執行工作**

    選取節點，並使用 **啟動** HPC 叢集管理員中的動作。 佈建完成後，請選取節點，並使用 **上線** HPC 叢集管理員中的動作。 節點已備妥而可執行工作。

10. **將工作提交至叢集**

    使用 HPC Pack 工作提交工具來執行叢集工作。 請參閱 [Microsoft HPC Pack ︰ 作業管理](http://technet.microsoft.com/library/jj899585.aspx)。

11. **停止 (解除佈建) 節點**

    當您在執行中的工作時，讓節點離線並使用 **停止** HPC 叢集管理員中的動作。


### 其他考量

* **Proxy 節點** -在每個高載至 Azure 的部署大量運算執行個體，HPC Pack 會自動部署最少 2 個額外大小 A8 執行個體做為 proxy 節點，在您指定除了 Azure 背景工作角色執行個體。 如需詳細資訊，請參閱 [設定 Azure Proxy 節點的數字](https://technet.microsoft.com/library/jj899633.aspx)。 Proxy 節點會使用配置給訂用帳戶的核心，並產生 Azure 背景工作角色執行個體的相關費用。

* **虛擬網路** -HPC Pack 目前不支援的點對站 VPN 組態進行 PaaS 部署。


## 案例 2. 在大量運算 VM 中部署運算節點 (IaaS)

在此案例中，您會在加入 Azure 虛擬網路之 Active Directory 網域中的 VM 上部署 HPC Pack 前端節點和叢集運算節點。
 [HPC Pack IaaS 部署
指令碼](virtual-machines-hpcpack-cluster-powershell-script.md)
會自動化此程序中，大部分動作，並提供彈性的部署選項
包括能夠指定叢集中的 A8 或 A9 VM 大小
節點。 下列步驟將引導您使用此自動化的部署
方法將程式碼部署至服務。 或者，您可以使用 Azure 快速入門範本，透過「資源管理員」部署模型來部署叢集。 測試部署，您可以手動部署 Active Directory
網域中，在前端節點 VM，計算節點 Vm 和其他部分
在 Azure 中的 HPC Pack 叢集基礎結構。 請參閱 [使用 Microsoft HPC Pack，在 Azure 中的 HPC 叢集選項](virtual-machines-hpcpack-cluster-options.md)。

![Azure VM 中的叢集][iaas]


1. **在用戶端電腦上執行 HPC Pack IaaS 部署指令碼，以建立叢集前端節點和運算節點 VM**

 下載 HPC Pack IaaS 部署指令碼套件從 [Microsoft 下載中心](https://www.microsoft.com/download/details.aspx?id=49922)。

 若要準備用戶端電腦，建立指令碼組態檔，然後執行指令碼，請參閱 < [使用 HPC Pack IaaS 部署指令碼建立 HPC 叢集](virtual-machines-hpcpack-cluster-powershell-script.md)。 若要部署大小為 A8 和 A9 的運算節點，請參閱本文稍後的其他考量。

2. **讓運算節點上線執行工作**

    選取節點，並使用 **上線** HPC 叢集管理員中的動作。 節點已備妥而可執行工作。

3. **將工作提交至叢集**

    連接到前端節點以提交工作，或設定內部部署電腦來執行這項操作。 如需資訊，請參閱 [提交工作至 HPC 叢集在 Azure 中](virtual-machines-hpcpack-cluster-submit-jobs.md)。

4. **讓節點離線並加以停止 (取消配置)**

    工作執行完成後，請使用 [HPC 叢集管理員] 讓節點離線。 然後，使用 Azure 管理工具將其關閉。

### 執行叢集部署指令碼的其他考量
* **虛擬網路** -指定新的虛擬網路的 A8 和 A9 執行個體可用的區域。


* **Windows Server 作業系統** -若要支援 RDMA 連線能力，指定在 Windows Server 2012 R2 或 Windows Server 2012 作業系統針對大小 A8 或 A9 計算節點 Vm。


* **雲端服務** -我們建議您部署主節點中有一個雲端服務和 A8 和 A9 計算不同的雲端服務中的節點。


* **前端節點大小** -A8 或 A9 大小新增計算節點 Vm 至少考慮的大小為 A4 （超大型） 的前端節點。


* **HpcVmDrivers 延伸模組** -部署指令碼會安裝 Azure VM 代理程式和 HpcVmDrivers 延伸模組部署 A8 或 A9 大小時，自動計算與 Windows Server 作業系統的節點。 HpcVmDrivers 會在運算節點 VM 上安裝驅動程式，使其可以連接到 RDMA 網路。 請參閱 [Azure VM 延伸模組與功能](virtual-machines-extensions-features.md)。


* **叢集網路組態** -部署指令碼會自動設定 HPC Pack 叢集拓撲 5 （企業網路上的所有節點）。 VM 中的所有 HPC Pack 叢集部署都需要此拓撲，包括大小為 A8 或 A9 的運算節點在內。 後續請勿變更此叢集網路拓撲。

## 在 A8 和 A9 執行個體上執行 MPI 應用程式

### 範例：在 HPC Pack 叢集上執行 mpipingpong

若要驗證的 HPC Pack 部署大量運算執行個體，執行 HPC Pack **mpipingpong** 命令在叢集上。 **mpipingpong**
傳送封包不斷地計算配對的節點之間的資料
延遲和輸送量的測量和統計資料啟用 RDMA 的
應用程式網路。 此範例顯示執行中的典型模式
MPI 工作 (在此情況下， **mpipingpong**) 使用叢集 **mpiexec**
命令。

這個範例假設您在 「 高載至 Azure 」 的 Azure 節點新增
組態 ([案例 1](#scenario-1.-deploy-compute-intensive-worker-role-instances-(PaaS)) 此文件中)。 如果您部署 HPC Pack 叢集上的 Azure Vm
您將需要修改命令語法來指定不同的節點
群組和設定網路流量導向至其他環境變數
RDMA 網路。


若要在叢集上執行 mpipingpong：


1. 在前端節點上或正確設定的用戶端電腦上，開啟 [命令] 視窗。

2. 若要估計有 4 個節點的 Azure 高載部署中的成對節點之間的延遲，請輸入下列提交工作的命令，使用小型封包和大量反覆項目執行 mpipingpong：

    ```
    job submit /nodegroup:azurenodes /numnodes:4 mpiexec -c 1 -affinity mpipingpong -p 1:100000 -op -s nul
    ```

    命令會傳回已提交之工作的 ID。

    如果您部署 HPC Pack 叢集部署在 Azure Vm 上，指定包含的節點群組計算節點 Vm 部署在單一雲端服務中，並修改 **mpiexec** 命令，如下所示 ︰

  ```
  job submit /nodegroup:vmcomputenodes /numnodes:4 mpiexec -c 1 -affinity -env MSMPI\_DISABLE\_SOCK 1 -env MSMPI\_PRECONNECT all -env MPICH\_NETMASK 172.16.0.0/255.255.0.0 mpipingpong -p 1:100000 -op -s nul
  ```

3. 工作完成後，若要檢視輸出 (在此案例中，是指工作之任務 1 的輸出)，請輸入下列命令

    ```
    task view <JobID>.1
    ```

    其中 & l t;*JobID*& gt; 已送出之工作的識別碼。

    輸出會包含如下的延遲結果。

    ![Ping pong 延遲][pingpong1]

4. 若要估計成對 Azure 高載節點之間的輸送量，請輸入下列命令以送出工作來執行 **mpipingpong** 具有大型封包和少量反覆項目 ︰

    ```
    job submit /nodegroup:azurenodes /numnodes:4 mpiexec -c 1 -affinity mpipingpong -p 4000000:1000 -op -s nul
    ```

    命令會傳回已提交之工作的 ID。

    在部署於 Azure VM 的 HPC Pack 叢集上，修改在步驟 2 中記下的命令。

5. 工作完成後，若要檢視輸出 (在此案例中，是指工作之任務 1 的輸出)，請輸入下列命令：

    ```
    task view &lt;JobID&gt;.1
    ```
    
  輸出會包含如下的輸送量結果。

  ![Ping pong 輸送量][pingpong2]


### MPI 應用程式考量


以下是在 Azure 上執行 MPI 應用程式的考量
執行個體。 部分僅適用於部署的 Azure 節點 （背景工作角色
執行個體在 「 高載至 Azure 」 組態中加入）。

* Azure 會定期重新佈建雲端服務中的背景工作角色執行個體，不另行通知 (例如為了維護系統，或是因為執行個體失敗)。 如果執行個體在執行 MPI 工作時重新佈建，執行個體將會遺失其所有的資料，並回復到第一次部署時的狀態，而這會導致 MPI 工作失敗。 您為單一 MPI 工作使用的節點愈多，工作執行的時間愈久，且其中一個執行個體就愈有可能在工作執行時重新佈建。 如果您在部署中將單一節點指定為檔案伺服器，您也應考量這一點。


* 您無需使用 A8 和 A9 執行個體來執行 Azure 中的 MPI 工作。 您可以使用 HPC Pack 支援的任何執行個體大小。 不過，建議使用 A8 和 A9 執行個體來執行較大規模、且對連接節點之網路的延遲和頻寬較為敏感的 MPI 工作。 如果您使用 A8 和 A9 以外的執行個體來執行對延遲和頻寬較敏感的 MPI 工作，建議您執行僅在少數節點上執行單一任務的小型工作。

* 部署至 Azure 執行個體的應用程式，會受限於應用程式的相關授權條款。 請向廠商確認任何商業應用程式在雲端中的執行授權或其他限制。 並非所有廠商都提供隨用隨付授權。


* Azure 執行個體需要進一步的安裝，才能存取內部部署節點、共用和授權伺服器。 例如，若要讓 Azure 節點能夠存取內部部署授權伺服器，您可以設定站對站 Azure 虛擬網路。


* 若要在 Azure 執行個體執行 MPI 應用程式，每個 MPI 應用程式的 Windows 防火牆的執行個體上執行註冊 **hpcfwutil** 命令。 如此，即可在防火牆動態指派的連接埠上進行 MPI 通訊。

    >[AZURE.NOTE] 高載至 Azure 的部署，您也可以設定防火牆例外狀況命令自動於所有新的 Azure 節點新增至您的叢集上執行。 當您執行之後 **hpcfwutil** 命令，並確認您的應用程式的運作方式，將 Azure 節點的啟動指令碼的命令。 如需詳細資訊，請參閱 [Azure 節點使用啟動指令碼](https://technet.microsoft.com/library/jj899632(v=ws.10).aspx)。



* HPC Pack 會使用 CCP_MPI_NETMASK 叢集環境變數，來指定 MPI 通訊可接受的位址範圍。 從 HPC Pack 2012 R2 開始，CCP_MPI_NETMASK 叢集環境變數只對加入網域的叢集運算節點之間的 MPI 通訊 (內部部署或在 Azure VM 中) 具有效用。 在「將量擴大到 Azure」組態中新增的節點會忽略此變數。


* MPI 工作無法在部署於不同雲端服務的 Azure 執行個體之間執行 (例如，在使用不同節點範本的「將量擴大到 Azure」部署之間，或部署在多個雲端服務中的 Azure VM 運算節點之間)。 如果您有多個以不同節點範本起始的 Azure 節點部署，MPI 工作就必須一組 Azure 節點上執行。


* 當您將 Azure 節點新增至您的叢集並使其上線時，HPC 工作排程器服務會立即嘗試在節點上啟動工作。 如果您的工作負載只有一部分可在 Azure 上執行，請確實更新或建立工作範本，以定義可在 Azure 上執行的工作類型。 例如，若要確保使用工作範本的已提交工作只會在 Azure 節點上執行，請在工作範本中新增 [節點群組] 屬性，並選取 AzureNodes 做為必要的值。 若要為您的 Azure 節點建立自訂群組，您可以使用 Add-HpcGroup HPC PowerShell Cmdlet。


## 後續步驟

* 如果您想要執行 Linux MPI 應用程式存取 Azure RDMA 網路，請參閱 [設定 Linux RDMA 叢集以執行 MPI 應用程式](virtual-machines-linux-cluster-rdma.md)。

<!--Image references-->
[burst]: ./media/virtual-machines-windows-hpcpack-cluster-rdma/burst.png
[iaas]: ./media/virtual-machines-windows-hpcpack-cluster-rdma/iaas.png
[pingpong1]: ./media/virtual-machines-windows-hpcpack-cluster-rdma/pingpong1.png
[pingpong2]: ./media/virtual-machines-windows-hpcpack-cluster-rdma/pingpong2.png


