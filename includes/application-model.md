# 運算

Azure 可讓您部署和監視在 Microsoft 資料中心內執行的
應用程式程式碼。 當您建立應用程式
並在 Azure 上執行時，程式碼和組態合起來
稱為 Azure 託管服務。 託管服務可輕鬆
管理、擴大和縮小、重新設定，以及使用新版的應用程式程式碼
進行更新。 本文的重點在於 Azure
託管的服務應用程式模型。<a id="compare" name="compare"></a>

## 目錄<a id="_GoBack" name="_GoBack"></a>

-   [Azure 應用程式模型優點][]
-   [託管服務核心概念][]
-   [託管服務設計考量][]
-   [設計可調整的應用程式][]
-   [託管服務的定義和組態][]
-   [服務定義檔][]
-   [服務組態檔][]
-   [建立和部署託管服務][]
-   [參考][]

## <a id="benefits"> </a>Azure 應用程式模型優點

當您將應用程式部署為託管服務時，Azure
會建立一或多個虛擬機器 (VM) (其中包含您的
應用程式程式碼)，並且在其中一個 Azure 資料中心內的
實體機器上啟動 VM。 當用戶端要求您的裝載的
應用程式進入資料中心時，負載平衡器會將這些
要求平均分配給 VＭ。 雖然您的應用程式裝載於 
Azure 中，但它可取得三個主要優勢：

-   **高可用性。** 高可用性意味著 Azure 可確保
    您的應用程式會盡可能執行，而且能夠
    回應用戶端要求。 如果您的應用程式終止 (例如，
    因為未處理的例外狀況)，則 Azure 會偵測到
    此狀況，而且會自動重新啟動您的應用程式。 如果
    您的應用程式執行所在的機器遇到
    硬體故障，則 Azure 會也偵測到此狀況，並且
    會自動在另一部運作中的實體機器上建立新的 VM
    並從該處執行您的程式碼。 注意 ︰ 為了讓您的應用程式
    取用 99.95% 的 Microsoft 服務等級協定，您
    必須有至少兩個 VM 正在執行您的應用程式程式碼。 此
    可讓一個 VM 能在 Azure 將您的程式碼從故障的 VM 移至
    良好的新 VM 時處理用戶端要求。

-   **延展性。** Azure 可讓您以動態的方式輕鬆
    變更執行您的應用程式程式碼的 VM 數目，以處理
    加諸於您的應用程式上的實際負載。 這可讓您
    將您的應用程式調整為您的客戶加諸於其上的工作負載，
    而且只要在您有需要時支付您所需的
    VM。 當您想要變更 VM 的數目時，Azure
    會在幾分鐘內回應，這樣就能夠視需要動態變更
    正在執行的 VM 數目。

-   **可管理性。** 因為 Azure 是一項平台即服務
    (PaaS) 供應項目，所以它會管理必要的基礎結構 (硬體本身、
    電力和網路)，讓這些機器維持
    運作。 Azure 也會管理平台，確保
    作業系統保持最新狀態並具有正確的修補程式和
    安全性更新以及元件更新，例如 .NET
    Framework 和 Internet Information Server。 因為所有的 VM 都是
    執行 Windows Server 2008，所以 Azure 提供了額外的
    功能，例如診斷監視、遠端桌面支援、
    防火牆，以及憑證存放區設定。 這些功能全都
    免費提供。 事實上，當您在 Azure 中
    執行您的應用程式時，Windows Server 2008 作業
    系統 (OS) 授權就已包含在內。 因為所有 VM 都是執行
    Windows Server 2008，所以任何在 Windows Server 2008 上執行的程式碼
    在 Azure 中執行時的狀況也不錯。

## <a id="concepts"> </a>託管服務核心概念

當您的應用程式部署成為 Azure 中的託管服務時，
它會以一個或多個 *角色。* A *角色* 其實就是指應用程式
設定和組態。 您可以為應用程式定義一或多個
角色，每個角色都有自己的應用程式檔案集和
組態。 對於應用程式中的每個角色，您可以指定
數字的 Vm，或 *角色執行個體*, ，以執行。 下圖顯示兩個
簡單範例，說明使用角色和角色執行個體模型化為託管服務
的應用程式。

##### 圖 1：單一角色有三個執行個體 (VM) 在 Azure 資料中心內執行

![image][0]

##### 圖 2：兩個角色各有兩個執行個體 (VM) 在 Azure 資料中心內執行

![image][1]

角色執行個體通常會處理透過所謂的 *輸入端點* 進入
透過所謂的資料中心 *輸入的端點*。 單一角色
可以有 0 個或更多輸入端點。 每個端點均表示一個通訊協定
(HTTP、HTTPS 或 TCP) 和一個連接埠。 通常會將角色設定為
有兩個輸入端點 ︰ HTTP 接聽連接埠 80 和 443
在連接埠 443。 下圖顯示兩個不同角色的範例
而這兩個角色有不同的輸入端點可將用戶端要求導向至他們。

![image][2]

當您在 Azure 中建立託管服務時，該託管服務會被指派
可公開定址的 IP 位址，以便用戶端進行存取。 在
建立託管服務時，您也必須選取可對應至該 IP 位置的
URL 前置詞。 此前置詞必須是唯一的，因為您
基本上會保留 *前置詞*。 cloudapp.net URL，讓其他人
具有該 URL。 用戶端會使用 URL 與您的角色執行個體
通訊。 通常，您不會散佈或發佈 Azure
*前置詞*。 cloudapp.net URL。 但是，您會向您所選的 DNS 註冊機構
購買 DNS 名稱並設定您的 DNS 名稱，以將用戶端要求
重新導向至 Azure URL。 如需詳細資訊，請參閱
[在 Azure 中設定自訂網域名稱][]。

## <a id="considerations"> </a>託管服務設計考量

設計要在雲端環境中執行的應用程式時，有
幾個需要考量之處，例如延遲、
高可用性和延展性。

決定要將應用程式程式碼放在何處是在 Azure 中執行
託管服務時的重要考量。 當您 [顯示選項] 並檢視所選取的憑證時，它
會將應用程式部署至最接近您用戶端的資料中心，
以減少延遲並獲得最佳效能。
不過，如果您有一些關於您的資料及其所在位置的司法或法律考量，
您可能會選擇比較接近您的公司或您的資料的
資料中心。 全球有六個資料中心
能夠託管您的應用程式程式碼。 下表顯示
可用的位置：

<table border="2" cellspacing="0" cellpadding="5" style="border: 2px solid #000000;">
<tbody>
<tr>
<td style="width: 100px;">
**國家/區域**

</td>
<td style="width: 200px;">
**子區域**

</td>
</tr>
<tr>
<td>
美國

</td>
<td>
中南部和中北部

</td>
</tr>
<tr>
<td>
歐洲

</td>
<td>
北歐和西歐

</td>
</tr>
<tr>
<td>
亞洲

</td>
<td>
東南亞和東亞

</td>
</tr>
</tbody>
</table>
建立託管服務時，您會選取一個子區域，表示
您想在其中執行程式碼的位置。

若要達到高可用性和延展性，您應用程式的資料
一定要保留在可供多個角色執行個體存取的
中央儲存機制中。 為了達成此目標，Azure 提供
數個儲存選項，例如 Blob、資料表和 SQL Database。 請參閱
 [在 Azure 中的資料儲存方案][] 如需詳細的文件
有關這些儲存技術的詳細資訊。 下圖顯示
Azure 資料中心內的負載平衡器如何將
用戶端要求分配給不同的角色執行個體，而這些角色執行個體全都可以存取
相同的資料儲存區。

![image][3]

通常，您想要在同一個資料中心找到您的應用程式程式碼
和您的資料，因為這可讓應用程式程式碼存取資料時的
延遲性很低 (效能較佳)。 此外，當資料在
同一個資料中心內移動時，您不需支付頻寬
費用。

## <a id="scale"> </a>設計可調整的應用程式

有時候，您可能想要採用單一應用程式 (例如簡易
網站) 並將它託管於 Azure 中。 但是，您的
應用程式通常會由數個一起運作的角色組成。 如需
在下圖中，網站角色有兩個執行個體，
訂單處理角色有三個執行個體，而報表產生器角色有
一個執行個體。 這些角色會一起運作，而
所有角色的程式碼都會封裝在一起，並以一個單位
的方式部署至 Azure。

![image][4]

將應用程式分割成不同的角色 (每個角色都在自己的角色執行
個體 (也就是 VM) 上執行) 的主要原因在於要獨立
延展角色。 例如，在假期期間，許多
客戶可能會向貴公司購買產品，因此您可能
想要增加執行您的網站角色的角色執行個體數目，
以及執行訂單處理角色的角色執行個體
。 假期之後，您可能會收到大量退貨的產品，
因此您可能需要很多網站執行個體，但是較少的訂單
處理執行個體。 在一年的其他時候，您可能只需要
少數網站和訂單處理執行個體。 這麼一來，您
可能只需要一個報表產生器執行個體。 Azure 中
以角色為基礎的部署彈性可讓您輕鬆地調整您的
應用程式，以符合您的商務需求的。

託管服務內的角色執行個體通常
會彼此通訊。 例如，網站角色會接受
客戶的訂單，然後將訂單處理作業卸載至訂單
處理角色執行個體。 將工作表單從一組角色執行個體傳遞至
另一組執行個體的最佳方式就是使用 Azure
所提供的佇列技術 (不是佇列服務就是
服務匯流排佇列)。 使用佇列是整個過程中十分重要的
一環。 佇列可讓託管服務獨立調整其角色
，進而讓您在工作負載與成本之間取得平衡。 如果
佇列中的訊息數目會隨著時間增加，則您可以調升
訂單處理角色執行個體的數目。 如果佇列中的
訊息數目隨著時間減少，則您可以調降
訂單處理角色執行個體的數目。 如此一來，您只要支付
處理實際工作量所需的執行個體即可。

佇列也帶來可靠性。 調降訂單處理角色執行個體的
數目時，Azure 會決定要終止哪些執行
個體。 它可能會決定終止處理佇列訊息
過程中的執行個體。 不過，因為訊息
處理並未順利完成，所以另一個挑選該訊息並加以處理的
訂單處理角色執行個體會再次看到此
訊息。 因為佇列訊息的可見性，所以保證最終會
處理訊息。 佇列也可以當作負載
平衡器，有效地將其訊息分配給要求訊息的所有
角色執行個體。

對於網站角色執行個體，您可以監控傳入的流量，
並決定要調升或調降其數目。 Auch die Eigenschaften
佇列可讓您將網站角色執行個體的數目與
訂單處理角色執行個體的數目分開調整。 此功能非常
強大，並讓您有很大的彈性。 當然，如果您的
應用程式包含其他角色，您可以新增其他
佇列做為兩者之間的通訊管道，以充分利用
相同的調整和成本優勢。

## <a id="defandcfg"> </a>託管服務的定義和組態

將託管服務部署至 Azure 時，您還必須有
服務定義檔和服務組態檔。 這兩個
檔案都是 XML 檔案，可讓您以宣告方式指定
託管服務的部署選項。 服務定義檔
描述所有構成託管服務的角色以及其
通訊方式。 服務組態檔描述每個角色的
執行個體數目以及用來設定每個角色執行個體的
設定。

## <a id="def"> </a>服務定義檔

如我先前所述，服務定義 (CSDEF) 檔是 XML
檔案，用以描述構成整個應用程式的各種
重新接收。 如需此 XML 檔案的完整結構描述，請至：
[] http://msdn.microsoft.com/library/windowsazure/ee758711.aspx[].
CSDEF 檔案包含您希望在應用程式中的每個角色的 WebRole
或 WorkerRole 元素。 將角色部署為 Web 角色 (使用
WebRole 元素) 表示程式碼將會在包含 Windows Server 2008 和
Internet Information Server (IIS) 的角色執行個體上執行。
將角色部署為背景工作角色 (使用 WorkerRole 元素) 表示
角色執行個體上會有 Windows Server 2008 (將不會
安裝 IIS)。

您當然可以建立及部署使用一些其他機制的背景工作角色，
以接聽連入的 Web 要求 (例如，您的程式碼
可建立並使用 .NET HttpListener)。 因為角色執行個體
全都執行 Windows Server 2008，所以您的程式碼可以執行任何作業，
而在 Windows Server 2008 上執行的應用程式通常可用這些作業。
2008.

針對每個角色，您可以指出想要該角色的執行個體使用的
VM 大小。 下表顯示現今可用的各種 VM 大小
和每個 VM 的屬性：

<table border="2" cellspacing="0" cellpadding="5" style="border: 2px solid #000000;">
<tbody>
<tr align="left" valign="top">
<td>
**VM 大小**

</td>
<td>
**CPU**

</td>
<td>
**RAM**

</td>
<td>
**磁碟**

</td>
<td>
**尖峰
網路 I/O**

</td>
</tr>
<tr align="left" valign="top">
<td>
**超小型**

</td>
<td>
1 x 1.0 GHz

</td>
<td>
768 MB

</td>
<td>
20GB

</td>
<td>
\~5 Mbps

</td>
</tr>
<tr align="left" valign="top">
<td>
**小型**

</td>
<td>
1 x 1.6 GHz

</td>
<td>
1.75 GB

</td>
<td>
225GB

</td>
<td>
\~100 Mbps

</td>
</tr>
<tr align="left" valign="top">
<td>
**中型**

</td>
<td>
2 x 1.6 GHz

</td>
<td>
3.5 GB

</td>
<td>
490GB

</td>
<td>
\~200 Mbps

</td>
</tr>
<tr align="left" valign="top">
<td>
**大型**

</td>
<td>
4 x 1.6 GHz

</td>
<td>
7 GB

</td>
<td>
1TB

</td>
<td>
\~400 Mbps

</td>
</tr>
<tr align="left" valign="top">
<td>
**超大型**

</td>
<td>
8 x 1.6 GHz

</td>
<td>
14 GB

</td>
<td>
2TB

</td>
<td>
\~800 Mbps

</td>
</tr>
</tbody>
</table>
您每小時都需支付您做為角色執行個體使用的每個 VM，您
還需支付角色執行個體在資料中心外部傳送的
任何資料。 進入資料中心的資料則不需計費。 如需
詳細資訊，請參閱 [Azure 定價] []。 一般而言，最好
使用許多小型角色執行個體，而不是使用一些大型
執行個體，您的應用程式失敗才比較容易從失敗中復原。 在
您擁有的角色執行個體越少，其中一個執行個體的失敗對於
整體應用程式的負面影響就越大。 此外，如前所述，
您必須為每個角色部署至少兩個執行個體，才能取得
Microsoft 提供的 99.95%服務等級協定。

服務定義 (CSDEF) 檔也可供您指定您的應用程式中各角色的
許多相關屬性。 以下是一些
可供您使用的實用項目：

-   **憑證**。 您可以使用憑證來加密資料或者
    您的 Web 服務支援 SSL。 所有憑證都需要上傳
    至 Azure。 如需詳細資訊，請參閱 [管理憑證
    在 Azure 中][]。 此 XML 設定會將先前上傳的
    憑證安裝到角色執行個體的憑證存放區，以供
    您的應用程式程式碼使用。

-   **組態設定名稱**。 適用於您希望應用程式
    在角色執行個體上執行時讀取的應用程式。 組態
    設定的實際值已設定於服務
    組態 (CSCFG) 檔中，不需重新部署您的程式碼，即可
    隨時進行更新。 事實上，您可以此方式
    撰寫應用程式，以偵測變更的組態
    值，而不會發生停機。

-   **輸入端點**。 您在此指定任何 HTTP、HTTPS 或 TCP
    端點 (含連接埠)，而您想要透過 *前置詞*.cloadapp.net URL 對
    透過您 *前置詞*。 cloadapp.net URL。 當 Azure 部署您的
    角色時，它會自動在角色執行個體上設定
    防火牆。

-   **內部端點**。 您在此指定任何 HTTP 或 TCP 端點，
    而您想要對部署為應用程式一部分的其他角色執行個體
    公開這些端點。 內部端點可讓您應用程式內的
    所有角色執行個體彼此通訊，但您應用程式外部
    的任何角色執行個體均無法存取。

-   **匯入模組**。 這些模組會在您的角色執行個體上選擇性
    安裝有用的元件。 元件包括診斷監視、
    遠端桌面和 Azure Connect (可讓您的角色
    執行個體透過安全通道存取內部部署的資源)。

-   **本機儲存體**。 這會在角色執行個體上配置一個子目錄，
    以供您的應用程式使用。 如需詳細說明，請參閱
    在 [在 Azure 中的資料儲存方案][] 文件。

-   **啟動工作**。 啟動工作可讓您在
    角色執行個體啟動時於其上安裝必要的元件。 這類工作
    可視需要提升為以系統管理員的身分執行。

## <a id="cfg"> </a>服務組態檔

服務組態 (CSCFG) 檔是 XML 檔案，用以描述
不需重新部署應用程式即可變更的設定。 Auch die Eigenschaften
如需 XML 檔案的完整結構描述，請至：
[http://msdn.microsoft.com/library/windowsazure/ee758710.aspx][]。
CSCFG 檔案會針對您應用程式中的每個角色各包含
重新接收。 以下是一些您可以在 CSCFG 檔案中指定的項目
檔案的路徑：

-   **作業系統版本**。 此屬性可讓您選取作業
    系統 (OS) 版本，以便用於執行您的應用程式程式碼的所有
    角色執行個體。 此作業系統稱為 *客體 OS*, ，而且每個
    新版本都包含客體作業系統發行時可用的最新安全性
    修補程式和更新。 如果您將
    osVersion 屬性值設定為 "\*"，則 Azure 會在有新的
    客體作業系統版本可用時，自動更新每個角色執行個體上的
    客體作業系統。 不過，您可以選擇不要自動
    更新，方法是選取特定客體作業系統版本。 例如，
    將 osVersion 屬性的值設定為
    "WA-GUEST-OS-2.8\_201109-01" 會導致所有角色執行個體取得
    此網頁上所描述的功能：
    [http://msdn.microsoft.com/library/hh560567.aspx][]。 如需
    客體作業系統版本的相關資訊，請參閱 [Managing Upgrades to the
    Azure 客體作業系統]。

-   **執行個體**。 此元素的值表示您要佈建的角色執行個體
    數目，以便針對特定角色執行
    。 因為您可以將新的 CSCFG 檔案上傳至 Azure
    (不需重新部署您的應用程式)，所以很簡單就可以
    變更此元素的值並上傳新的 CSCFG 檔案，
    以便動態增加或減少執行您的應用程式程式碼的
    角色執行個體數目。 這可讓您輕鬆地調升或調降
    您的應用程式，以符合實際的工作量需求，同時還能
    控制執行角色執行個體所需支付的費用。

-   **組態設定值**。 此元素表示
    設定值 (定義於 CSDEF 檔案中)。 您的角色可以在執行時
    讀取這些值。 這些組態設定值
    通常用於對 SQL Database 或 Azure 儲存體的 
    連接字串，但可用於您想要的任何用途。

## <a id="hostedservices"> </a>建立和部署託管服務

建立託管的服務時，您必須先移至 [Azure Management Portal] 並藉由指定佈建託管的服務
DNS 前置詞以及您最終希望程式碼執行所在的資料中心。
在中。 然後在開發環境中，您會建立服務
定義 (CSDEF) 檔，建置應用程式程式碼，並將這些檔案
全都封裝 (壓縮) 到服務套件 (CSPKG) 檔案。 您還必須
準備您的服務組態 (CSCFG) 檔。 若要部署您的角色，
您可利用 Azure 服務管理 API 來上傳 CSPKG 和
CSCFG 檔案。 一旦部署，Azure 就會在資料中心
佈建角色執行個體 (根據組態資料)，
從封裝中擷取您的應用程式程式碼，將它複製到角色
執行個體，並啟動這些執行個體。 現在，您的程式碼已啟動並執行。

下圖顯示您在開發電腦上建立的 CSPKG 和 CSCFG
檔案。 CSPKG 檔案包含 CSDEF 檔案以及
兩個角色的程式碼。 利用 Azure 服務管理 API 上傳 CSPKG
和 CSCFG 檔案之後，Azure 會在資料中心
建立角色執行個體。 在此範例中，CSCFG 檔案指出
Azure 應該建立角色 \#1 的三個執行個體和
角色 \#2 的兩個執行個體。

![image][5]

如需有關部署、升級和重新設定角色的詳細資訊，
角色，請參閱 [部署和更新 Azure 應用程式][]
發行項。<a id="Ref" name="Ref"></a>

## <a id="references"> </a>參考

-   [建立 Azure 託管服務][]

-   [在 Azure 中管理託管服務][]

-   [將應用程式移轉到 Azure][]

-   [設定 Azure 應用程式][]

<div style="width: 700px; border-top: solid; margin-top: 5px; padding-top: 5px; border-top-width: 1px;">

<p>作者：Jeffrey Richter (Wintellect)</p>

</div>

  [Azure Application Model Benefits]: #benefits
  [Hosted Service Core Concepts]: #concepts
  [Hosted Service Design Considerations]: #considerations
  [Designing your Application for Scale]: #scale
  [Hosted Service Definition and Configuration]: #defandcfg
  [The Service Definition File]: #def
  [The Service Configuration File]: #cfg
  [Creating and Deploying a Hosted Service]: #hostedservices
  [References]: #references
  [0]: ./media/application-model/application-model-3.jpg
  [1]: ./media/application-model/application-model-4.jpg
  [2]: ./media/application-model/application-model-5.jpg
  [Configuring a Custom Domain Name in Azure]: http://www.windowsazure.com/develop/net/common-tasks/custom-dns/
  [Data Storage Offerings in Azure]: http://www.windowsazure.com/develop/net/fundamentals/cloud-storage/
  [3]: ./media/application-model/application-model-6.jpg
  [4]: ./media/application-model/application-model-7.jpg
  
  [Azure Pricing]: http://www.windowsazure.com/pricing/calculator/
  [Managing Certificates in Azure]: http://msdn.microsoft.com/library/windowsazure/gg981929.aspx
  [http://msdn.microsoft.com/library/windowsazure/ee758710.aspx]: http://msdn.microsoft.com/library/windowsazure/ee758710.aspx
  [http://msdn.microsoft.com/library/hh560567.aspx]: http://msdn.microsoft.com/library/hh560567.aspx
  [Managing Upgrades to the Azure Guests OS]: http://msdn.microsoft.com/library/ee924680.aspx
  [Azure Management Portal]: http://manage.windowsazure.com/
  [5]: ./media/application-model/application-model-8.jpg
  [Deploying and Updating Azure Applications]: http://www.windowsazure.com/develop/net/fundamentals/deploying-applications/
  [Creating a Hosted Service for Azure]: http://msdn.microsoft.com/library/gg432967.aspx
  [Managing Hosted Services in Azure]: http://msdn.microsoft.com/library/gg433038.aspx
  [Migrating Applications to Azure]: http://msdn.microsoft.com/library/gg186051.aspx
  [Configuring an Azure Application]: http://msdn.microsoft.com/library/windowsazure/ee405486.aspx


