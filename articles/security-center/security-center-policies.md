<properties
   pageTitle="開始使用 Azure 資訊安全中心 | Microsoft Azure"
   description="本文件可協助您在「Azure 資訊安全中心」設定安全性原則。"
   services="security-center"
   documentationCenter="na"
   authors="YuriDio"
   manager="swadhwa"
   editor=""/>

<tags
   ms.service="security-center"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="12/16/2015"
   ms.author="yurid"/>


# 在 Azure 資訊安全中心設定安全性原則

這份文件透過帶領您完成執行這項工作的必要步驟，協助您設定 Azure 資訊安全中心的安全性原則。
> [AZURE.NOTE] 本文中的資訊適用於 Azure 資訊安全中心的預覽版本。

## 什麼是 Azure 資訊安全中心？

Azure 資訊安全中心利用加強對 Azure 資源的能見度及安全性控制權，以預防、偵測並回應威脅。 它提供您訂用帳戶之間的整合式安全性監視和原則管理，協助您偵測可能會忽略的威脅，且適用於廣泛的安全性解決方案生態系統。

## 什麼是安全性原則？

安全性原則定義了針對指定之訂用帳戶內的資源建議的一組控制項。 在「Azure 資訊安全中心」中，您可以根據公司安全性需求，以及每個訂用帳戶中應用程式的類型或資料的敏感性，為您的 Azure 訂用帳戶定義原則。

例如，用於開發或測試的資源與用於實際執行應用程式的資源，兩者的安全性需求可能不同。 同樣地，具有 PII (個人識別資訊) 這類規範資料的應用程式可能會需要較高層級的安全性。 「Azure 資訊安全中心」中啟用的安全性原則將會決定安全性建議與監視，以協助您識別可能的弱點並減輕威脅。

## 設定安全性原則

安全性原則是針對每個訂用帳戶設定的。 若要修改安全性原則，您必須是該訂用帳戶的「擁有者」或「參與者」。 請依照下列步驟在「Azure 資訊安全中心」設定安全性原則：

1. 按一下 Azure 資訊安全中心儀表板中的 [安全性原則]**** 磚。

2. 在於右側開啟的 [安全性原則 - 定義每個訂用帳戶的原則]**** 刀鋒視窗中，選取您想要啟用安全性原則的訂用帳戶。

    ![啟用資料收集](./media/security-center-policies/security-center-policies-fig0.png)

3. **安全性原則**  該訂用帳戶的刀鋒視窗會開啟一組選項類似如下所示:

    ![啟用資料收集](./media/security-center-policies/security-center-policies-fig1.png)

4. 確定 [從虛擬機器收集資料]**** 選項為 [開啟]****。 此選項可啟用現有資源與新資源的自動記錄收集。

5. 如果尚未設定儲存體帳戶，當您開啟 [安全性原則]**** 時，可能會看到類似下圖的警告：

    ![儲存體選擇](./media/security-center-policies/security-center-policies-fig2.png)

6. 如果您看到這個警告，請按一下此選項並選取區域，如下圖所示：

    ![儲存體選擇](./media/security-center-policies/security-center-policies-fig3.png)

7. 針對每個有虛擬機器在其中執行的區域，請選擇儲存體帳戶，以儲存從這些虛擬機器收集到的資料。 這可讓您針對隱私權和資料主權的考量，輕鬆將資料保留在相同的地理區域。 您決定將使用哪一個區域之後，請選取該區域，然後選取儲存體帳戶。

8. 在 [選擇儲存體帳戶]**** 刀鋒視窗中，按一下 [確定]****。
    > [AZURE.NOTE] 如果想要的話，您也可以從不同區域的虛擬機器，將資料彙總到一個集中的儲存體帳戶。 請參閱 [Azure 安全性中心常見問題集](security-center-faq.md) 如需詳細資訊

9. 在 [安全性原則]**** 刀鋒視窗中按一下 [開啟]****，啟用您想要在此訂用帳戶使用的安全性建議。 請使用下表做為參考，以了解每個選項會執行的動作：

   | 原則| 狀態為開啟時|
|----- |-----|
| 系統更新| 根據為該虛擬機器設定的服務，每隔 12 小時從 Windows Update 或 WSUS 抓取可用的更新清單，並建議在您的 Windows 虛擬機器上安裝遺漏的更新。|
| 基準規則| 每隔 12 小時分析一次所有支援的虛擬機器，以識別任何可能造成虛擬機器更容易受到攻擊的作業系統設定，並建議進行設定變更來處理這些弱點。請參閱 [清單的建議基準](https://gallery.technet.microsoft.com/Azure-Security-Center-a789e335) 如需有關受監視的特定設定。|
| 反惡意程式碼| 建議為所有 Windows 虛擬機器佈建反惡意程式碼，以協助識別和移除病毒、間諜軟體及其他惡意軟體。|
| 端點上的存取控制清單| 建議 [存取控制清單](virtual-machines-set-up-endpoints.md) (ACL) 設定來限制存取傳統虛擬機器端點。這通常是用來確保只有連線到公司網路的使用者才能存取虛擬機器。|
| 子網路與網路介面上的網路安全性群組| 建議 [網路安全性群組](virtual-networks-nsg.md) (Nsg) 設定來控制傳入和傳出流量子網路及網路資源管理員的虛擬機器的介面。除非另有指定，否則所有虛擬機器網路介面都會繼承為子網路設定的 NSG。除了檢查是否已設定 NSG 之外，還會評估「輸入安全性規則」來識別允許「任何」連入流量的規則。|
| Web 應用程式防火牆| 建議您在資源管理員的虛擬機器上佈建 Web 應用程式防火牆時: [執行個體層級公用 IP](virtual-networks-instance-level-public-ip.md) (ILPIP) 會使用和相關聯的 NSG 輸入安全性規則都設定為允許存取連接埠 80/443。使用負載平衡 IP (VIP)，而且相關聯的負載平衡以及傳入的 NAT 規則都設定為允許存取連接埠 80/443 (如需詳細資訊，請參閱 [Azure 資源管理員的負載平衡器支援](load-balancer-arm.md))|
| SQL 稽核| 建議針對法規遵循、進階偵測及調查用途，啟用 Azure SQL 伺服器與資料庫的存取稽核。|
| SQL 透明資料加密| 建議為您的 Azure SQL 資料庫、關聯的備份及交易記錄檔啟用待用期加密，讓您的資料即使遭到入侵也無法被讀取。|

10. 完成所有選項的設定之後，按一下 [儲存]**** 以認可這些變更。

> [AZURE.NOTE] 如果啟用資料收集，將會安裝監視代理程式。 如果您不想現在從這個位置開啟資料收集，可以稍後從健康情況和建議檢視開啟。 您也可以針對訂用帳戶或只針對選取的 VM 啟用這項功能。 請參閱 [Azure 安全性中心常見問題集](security-center-faq.md) 要深入了解支援的 Vm。 

## 後續步驟

在本文件中，您已了解如何在「Azure 資訊安全中心」設定安全性原則。 若要深入了解「Azure 資訊安全中心」，請參閱下列主題：

- [Azure 資訊安全中心的安全性健全狀況監視](security-center-monitoring.md) – 了解如何監視您的 Azure 資源的健全狀況
- [管理與 Azure 資訊安全中心的安全性警示回應](security-center-managing-and-responding-alerts.md) -了解如何管理和回應安全性警示
- [Azure 安全性中心常見問題集](security-center-faq.md) – 尋找使用服務的相關的常見問題集
- [Azure 安全性部落格](http://blogs.msdn.com/b/azuresecurity/) – 尋找關於 Azure 安全性與相容性部落格文章





