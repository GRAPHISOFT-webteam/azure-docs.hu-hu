
<properties
   pageTitle="開始設定網際網路面向的負載平衡器 | Microsoft Azure"
   description="為您的虛擬機器或雲端服務設定第一個網際網路面向的負載平衡器集合。 "
   services="load-balancer"
   documentationCenter="na"
   authors="joaoma"
   manager="adinah"
   editor="tysonn" />
<tags
   ms.service="load-balancer"
   ms.devlang="na"
   ms.topic="hero-article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="11/19/2015"
   ms.author="joaoma" />

# 開始設定網際網路面向的負載平衡器

> [AZURE.SELECTOR]
- [Azure 的傳統步驟](load-balancer-internet-getstarted.md)
- [資源管理員 Powershell 步驟](load-balancer-arm-powershell.md)

Microsoft Azure 中的負載平衡服務可處理所有租用戶類型 (IaaS 或 PaaS) 和各種支援的作業系統 (Windows 或任何以 Linux 為基礎的作業系統)。


## 為虛擬機器設定網際網路面向的負載平衡器

為了使雲端服務的各虛擬機器的網際網路流量達到負載平衡，您必須建立負載平衡的集合。 此程序假設您已建立虛擬機器，而且全都在相同的雲端服務內。

**設定虛擬機器的負載平衡集合**

1. 在 Azure 入口網站中，按一下 [ **虛擬機器**, ，然後按一下 [虛擬機器中的負載平衡集的名稱。
2.  按一下 [ **端點**, ，然後按一下 [ **新增**。

4.  在 **將端點加入至虛擬機器** 頁面上，按一下向右箭號。

4.  在 **指定端點的詳細資料** 頁面 ︰
    - 在 **名稱**, ，輸入端點的名稱，或從常見通訊協定的預先定義端點的清單中選取名稱。
    -  在 **通訊協定**, ，視需要選取端點，TCP 或 UDP 型別所需的通訊協定。
    -  在 **公用連接埠和私人連接埠**, ，輸入您想將虛擬機器使用，視需要的連接埠號碼。 您可以在虛擬機器上使用私人連接埠和防火牆規則，以適合應用程式的方式來重新導向流量。 私人連接埠可與公用連接埠相同。 例如，對於 Web (HTTP) 端點的流量，您可以將連接埠 80 同時指派給公用和私人連接埠。

5.  選取 **建立負載平衡集**, ，然後按一下向右箭號。

6.  在 **設定負載平衡集** 頁面上，輸入負載平衡集名稱，然後指派 Azure 負載平衡器探查行為的值。
負載平衡器會使用探查，來判斷負載平衡集合中的虛擬機器是否可用於接收連入流量。

7.  按一下核取記號以建立負載平衡端點。 您會看到 **是** 中 **負載平衡集名稱** 資料行的 **端點** 的虛擬機器] 頁面。

8.  在入口網站中，按一下 [ **虛擬機器**, ，按一下 [負載平衡集合中的其他虛擬機器的名稱，按一下 **端點**, ，然後按一下 [ **新增**。

9.  在 **將端點加入至虛擬機器** 頁面上，按一下 **將端點加入至現有的負載平衡集**, 選取負載平衡集的名稱，然後按一下向右箭號。

10. 在 **指定端點的詳細資料** 頁面上，輸入端點的名稱，然後按一下 [核取記號。
針對負載平衡集合中的其他虛擬機器，重複步驟 8-10。

您也可以使用下列 Windows PowerShell Cmdlet 來設定端點：

- Add-AzureEndpoint

[Add-AzureEndpoint](https://msdn.microsoft.com/library/windowsazure/dn495300)

- Get-AzureEndpoint

[Get-AzureEndpoint](https://msdn.microsoft.com/library/windowsazure/dn495158)

- Remove-AzureEndpoint

[Remove-AzureEndpoint](https://msdn.microsoft.com/library/windowsazure/dn495161)


## 為雲端服務設定網際網路面向的負載平衡器


雲端服務是使用負載平衡器自動設定，並可透過服務模型加以自訂。

您可以利用 Azure SDK for .NET 2.5 來更新雲端服務。 在進行雲端服務的端點設定 [服務定義](https://msdn.microsoft.com/library/azure/gg557553.aspx).csdef 檔案。

下列範例示範如何設定雲端部署的 servicedefinition.csdef 檔案：

檢查雲端部署所產生之 .csdef 檔的 snipet，您可以看到已設定為在連接埠 10000、10001 和 10002 上使用連接埠 HTTP 的外部端點。


    <ServiceDefinition name=“Tenant“>
    <WorkerRole name=“FERole” vmsize=“Small“>
    <Endpoints>
        <InputEndpoint name=“FE_External_Http” protocol=“http” port=“10000“ />
        <InputEndpoint name=“FE_External_Tcp“  protocol=“tcp“  port=“10001“ />
        <InputEndpoint name=“FE_External_Udp“  protocol=“udp“  port=“10002“ />

        <InputEndpointname=“HTTP_Probe” protocol=“http” port=“80” loadBalancerProbe=“MyProbe“ />

        <InstanceInputEndpoint name=“InstanceEP” protocol=“tcp” localPort=“80“>
           <AllocatePublicPortFrom>
              <FixedPortRange min=“10110” max=“10120“  />
           </AllocatePublicPortFrom>
        </InstanceInputEndpoint>
        <InternalEndpoint name=“FE_InternalEP_Tcp” protocol=“tcp“ />
    </Endpoints>
    </WorkerRole>
    </ServiceDefinition>




### 檢查雲端服務的負載平衡器健全狀態


以下是健全狀態探查的範例：

        <LoadBalancerProbes>
        <LoadBalancerProbe name=“MyProbe” protocol=“http” path=“Probe.aspx” intervalInSeconds=“5” timeoutInSeconds=“100“ />
        </LoadBalancerProbes>

負載平衡器會結合端點的資訊和探查，來建立 URL http://{DIP VM}:80/Probe.aspx 可以用來查詢服務的健全狀況的表單中的資訊。

該服務偵測到來自相同 IP 位址的定期探查。 這是來自虛擬機器執行所在之節點主機的健全狀況探查要求。
服務必須以負載平衡器的 HTTP 200 狀態碼回應，假設服務的狀況良好。 任何其他 HTTP 狀態碼 (例如 503) 都會直接讓虛擬機器脫離循環。

探查定義也會控制探查的頻率。 在上述案例中，負載平衡器每隔 5 秒探查端點一次。 如果長達 10 秒 (兩個探查間隔) 未收到正面回應，則會認為探查已關閉，而虛擬機器會脫離循環。 同樣地，如果服務已脫離循環並收到正面回應，則會立即將服務放回循環。 如果服務在狀況良好和狀況不良之間變動，則負載平衡器可以決定延遲將循環重新帶回服務，直到有探查數次的狀況皆為良好為止。

請檢查服務定義結構描述 [健全狀況探查](https://msdn.microsoft.com/library/azure/jj151530.aspx) 如需詳細資訊。

## 使用 PowerShell 設定負載平衡器

建立虛擬機器之後，您可以使用 PowerShell cmdlet，將負載平衡器新增至相同雲端服務內的虛擬機器。

在下列範例中，您會將稱為 "webfarm" 的負載平衡器新增至雲端服務端點 "mycloudservice" (或 mycloudservice.cloudapp.net) 和名為 myVM 的虛擬機器。 負載平衡器會接收連接埠 80 上的流量，並使用 HTTP 負載平衡連接埠 8080 上虛擬機器之間的網路流量。

    Get-AzureVM -ServiceName "mycloudservice" -Name "MyVM" | Add-AzureEndpoint -Name "HttpIn" -Protocol "tcp" -PublicPort 80 -LocalPort 8080 -LBSetName "WebFarm" -ProbePort 80 -ProbeProtocol "http" -ProbePath '/' | Update-AzureVM


## 後續步驟

[開始設定內部負載平衡器](load-balancer-internal-getstarted.md)

[設定負載平衡器分配模式](load-balancer-distribution-mode.md)

[設定負載平衡器的閒置 TCP 逾時設定](load-balancer-tcp-idle-timeout.md)


