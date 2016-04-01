<properties
    pageTitle="適用於 Eclipse 的 Azure 工具組的新功能"
    description="了解適用於 Eclipse 的 Azure 工具組中的最新功能。"
    services=""
    documentationCenter="java"
    authors="rmcmurray"
    manager="wpickett"
    editor=""/>

<tags
    ms.service="multiple"
    ms.workload="na"
    ms.tgt_pltfrm="multiple"
    ms.devlang="Java"
    ms.topic="article"
    ms.date="11/19/2015" 
    ms.author="robmcm"/>

<!-- Legacy MSDN URL = https://msdn.microsoft.com/library/azure/hh694270.aspx -->

# 適用於 Eclipse 的 Azure 工具組的新功能 #

## 適用於 Eclipse 的 Azure 工具組版本 ##

本文包含適用於 Eclipse 的 Azure 工具組的各種版本和最新更新的相關資訊。

### 2015 年 9 月 1 日 ###

適用於 Eclipse 的 Azure 工具組 - 2015 年 9 月版本包含下列增強功能：

* **祖魯文 OpenJDK 更新的支援**。 如需詳細資訊，請參閱 [Zulu OpenJDK Azul Systems 網頁][]。
* **更新 Tomcat 和 Jetty 發佈**。 已更新 Microsoft Azure 上所提供、可搭配適用於 Eclipse 的 Azure 工具組使用的 Jetty 和 Tomcat 散發套件  (這些散發套件可讓開發人員使用適用於 Eclipse 的 Azure 工具組建立快速開發與測試專案)。
* **支援自動更新 Tomcat 和 Jetty 參考**。 除了 Azure 上所提供之 Tomcat 和 Jetty 的特定版本外，開發人員現在還可以參考稱為「最新 (自動更新)」的散發套件，這個散發套件會在下一次回收角色執行個體時，自動更新為每個 Jetty 或 Tomcat 主要版本的最新散發套件  (回收程序會自動發生，但開發人員可以透過 Azure 入口網站手動觸發回收程序)。這項新功能，表示開發人員不需要重新部署應用程式才能夠有伺服器軟體更新。 (
*  這項功能目前僅供開發和測試目的或非任務關鍵應用程式使用，並不建議用於生產環境)。
* **Azure blob 的總管檢視佇列和 Azure 儲存體中的資料表**。 這可讓開發人員直接從 Eclipse IDE 中對其儲存體構件執行一組常見的工作。 例如：刪除、上傳或下載 blob。

### 2015 年 8 月 1 日 ###

適用於 Eclipse 的 Azure 工具組 - 2015 年 8 月版本包含下列增強功能：

* **Application Insights 檢測金鑰管理**。 這項更新可讓您直接從 Eclipse IDE 取得、建立及管理 Application Insights 檢測金鑰。
* **Microsoft JDBC Driver 4.1 for SQL Server**。 這項更新包含適用於 Microsoft SQL Server 之最新 JDBC 驅動程式的支援。
* **Azure SDK 2.7 版**。 這個最新的 Azure SDK 更新是工具組安裝在 Windows 上時的新必要條件  (請注意，在非 Windows 作業系統上則不需要此更新)。
* **支援祖魯文 OpenJDK v7 更新**。 如需詳細資訊，請參閱 [Zulu OpenJDK Azul Systems 網頁][]。

### 2015 年 5 月 1 日 ###

適用於 Eclipse 的 Azure 工具組 - 2015 年 5 月版本包含下列增強功能：

* **改善的伺服器選擇 UI**。 此版本簡化了在非 Windows 作業系統上使用工具組的方式。
* **Maven 專案的支援**。 此版本支援將 Maven 專案做為應用程式，工具組可部署到 Azure，並設定 Application Insights。
* **Azure SDK 2.6 版**。 這個最新的 Azure SDK 更新是工具組安裝在 Windows 上時的新必要條件  (請注意，在非 Windows 作業系統上則不需要此更新)。
* **部署升級，而不是重新發行**。 如果要在先前的版本已經上線時重新發佈部署專案，此工具組現在會使用 Azure 的部署升級功能，而不會像過去一樣先關閉先前的部署再從頭開始重新發佈。 這可讓您的雲端服務盡可能保持運作而不中斷，協助您即使是在更新期間也能達到高可用性，並加快重新發佈程序的速度。
* **支援的最新的祖魯文 OpenJDK v8 – 更新 40**。 如需詳細資訊，請參閱 [Zulu OpenJDK Azul Systems 網頁][]。

### 2015 年 3 月 9 日 ###

適用於 Eclipse 的 Azure 工具組 - 2015 年 3 月版本包含下列增強功能：

* **適用於 Mac、 Ubuntu 和其他 Linux 類別支援**。 這一版的適用於 Eclipse 的 Azure 工具組加入了 Mac OS 和數個 Unix 平台的支援，讓開發人員能夠藉由安裝此工具組，從執行非 Windows 作業系統的 Eclipse 建立和設定 Java 專案並將其發佈至 Azure 雲端服務 (PaaS)。

>[AZURE.NOTE] 這項功能處於預覽狀態，並不建議在實際執行環境中使用。 雖然此功能沒有客戶支援服務等級協定 (SLA)，但仍歡迎您提供相關意見反應。

* **新的 Application Insights 外掛程式**。 開發人員現在可以在 Azure 上使用 Application Insights 以設定自動伺服器遙測。
* **{1>ant 型命令列部署自動化**。 這項功能可讓開發人員在 Eclipse 外部使用 Ant 自動發佈較新版的部署。 第一次從 Eclipse 部署專案後，系統就會為專案自動設定預先產生的指令碼，後續的部署就可以使用指令碼完全自動進行部署 (只能透過命令列)。
* **更簡單、 更快速的部署在 Azure 上 tomcat 和 Jetty 可用性**。 開發人員現在可以直接參考 Azure 上提供的各種 Tomcat 或 Jetty 版本，而不必將 Java 伺服器上傳到其帳戶 (或透過工具組)，因此不需要針對快速入門案例上傳 Java 伺服器。
* **用於將 Java web 應用程式發行至 Azure 雲端服務的快顯方法**。 為了減少簡單開發和測試案例的學習曲線，開發人員現在可以更直接地將 Java 應用程式發佈至 Azure。 應用程式不需要經歷建立及設定 Azure 部署專案的程序，而是會使用 Tomcat v8 和 Zulu JVM (OpenJDK) 的預設執行個體進行部署。

### 2015 年 1 月 30 日 ###

適用於 Eclipse 的 Azure 工具組 - 2015 年 1 月版本包含下列增強功能：

* **支援 IBM® WebSphere® 應用程式伺服器 Liberty 核心**。 此版本將 IBM WebSphere Application Server Liberty Core 增加到支援的應用程式伺服器清單中，透過這些伺服器，工具組將能夠部署到 Azure。 這個最新的新增項目會擴充已包含各種版本之 Tomcat、Jetty、JBoss 和 GlassFish 的工具組目前現成支援的應用程式伺服器清單。
* **內含 Application Insights SDK**。 這個新發行的用戶端 API 程式庫 (v0.9.0) 現在是 Azure Libraries for Java 封裝的一部分。
* **套件更新 Azure libraries for Java**。 此更新包括 Azure Libraries for Java v0.7.0 和 Storage Client API v2.0.0 以及新發佈的 Application Insights SDK v0.9.0。

### 2014 年 11 月 12 日 ###

適用於 Eclipse 的 Azure 工具組 - 2014 年 11 月版本包含下列增強功能：

* **支援 Azure SDK 2.5**。 這個最新的 Azure SDK 更新是工具組的新必要條件。
* **祖魯文 OpenJDK v1.8、 v1.7 和 v1.6 封裝的更新版本支援**。 如需詳細資訊，請參閱 [Zulu OpenJDK Azul Systems 網頁][]。
* **支援雲端服務的新標準 D 大小**, ，以提供更高的效能和額外的記憶體資源。 如需詳細資訊，請參閱 [虛擬機器和雲端服務大小][]。

### 2014 年 10 月 17 日 ###

適用於 Eclipse 的 Azure 工具組 - 2014 年 10 月版本包含下列增強功能：

* **在 [發佈到雲端案例的效能改進**。 若使用者有多個訂用帳戶和儲存體帳戶，載入訂用帳戶資訊的速度變得更快了。
* **祖魯文 OpenJDK v1.8 封裝的更新版本支援**。 如需詳細資訊，請參閱 [Zulu OpenJDK Azul Systems 網頁][]。
* **支援淘汰舊版的第 3 個合作對象 Jdk**。 對於新的部署專案，已淘汰的 JDK 封裝將不會再顯示於下拉式功能表中。 參考已淘汰之 JDK 封裝的現有專案目前還是能夠繼續參考，但建議您升級這類專案使其依賴最新的 JDK 封裝。
* **更新的版本的 Package for Azure Libraries for Java 用戶端 API 程式庫**。 如需詳細資訊，請參閱 [Microsoft Azure 用戶端 API][]。
* **錯誤修正。** 此版本包含許多依據使用者報告和測試所做出的其他錯誤修正。

### 2014 年 8 月 5 日 ###

適用於 Eclipse 的 Azure 工具組 - 2014 年 8 月版本包含下列增強功能：

* **支援 Azure SDK 2.4。** 舊版 Eclipse 工具組無法使用這個新發佈的 SDK。
* **已更新 Zulu OpenJDK v1.6、v1.7 和 v1.8 封裝的版本。** 如需詳細資訊，請參閱 [Zulu OpenJDK Azul Systems 網頁][]。
* **已更新 Azure Libraries for Java 用戶端 API 程式庫的封裝版本。** 如需詳細資訊，請參閱 [Microsoft Azure 用戶端 API][]。
* **支援最新的發佈設定檔案格式。** 已加入 2.0 版發佈設定檔案格式的支援。
* **發佈到雲端功能背後的架構變更。** 此工具組現在會使用新發佈之適用於 Java 的 Microsoft Azure 用戶端 API 來支援其發佈到雲端功能。
* **錯誤修正。** 此版本包含許多使用者要求的錯誤修正。

### 2014 年 6 月 12 日 ###

適用於 Eclipse 的 Azure 工具組 - 2014 年 6 月版本是次要服務更新，可提供下列增強功能：

* **支援 Zulu OpenJDK 封裝 v1.8。** 如需詳細資訊，請參閱 [Zulu OpenJDK Azul Systems 網頁][]。
* **已更新 Zulu OpenJDK v1.6 和 1.7 封裝的版本。** 如需詳細資訊，請參閱 [Zulu OpenJDK Azul Systems 網頁][]。
* **已更新 Azure Libraries for Java 用戶端 API 程式庫的封裝版本。** 如需詳細資訊，請參閱 [Microsoft Azure 用戶端 API][]。
* **錯誤修正。** 此版本包含許多使用者要求的錯誤修正。

### 2014 年 4 月 4 日 ###

適用於 Eclipse 的 Azure 外掛程式 - 2014 年 4 月版本已發行。 這個更新需搭配屬於必要條件的 Azure SDK 2.3 版，而且在您安裝外掛程式時會自動下載此 SDK。 此更新包含 2014 年 2 月預覽以來所推出的新功能、錯誤修正，以及一些根據意見反應所開發出的使用性增強功能：

* **支援 Azure SDK 2.3 版本。** 適用於 Eclipse 的 Azure 外掛程式 - 2014 年 4 月版本需要 Azure SDK 2.3。 在使用新的外掛程式時，如果您還沒有 Azure SDK 2.3，系統會提示您允許安裝此 SDK。 請勿將 Azure SDK 2.3 用在舊版的外掛程式。
* **不需要完整部署封裝即可升級應用程式。** 在部署屬於專案一部分的 Java 應用程式時，外掛程式現在會自動將這些應用程式上傳到您選定的儲存體帳戶，以便您可以更新專案，並回收角色執行個體以部署最新的應用程式版本，而不需要重建並重新部署整個封裝。
* **Tomcat 8 現在是可辨識的應用程式伺服器。** 如果您選取的 Tomcat 8 安裝目錄中的機器上 **伺服器** ] 索引標籤的 **Azure 部署專案** ] 對話方塊中，外掛程式會立即自動偵測到它，並能夠部署以自動化方式，類似較舊版本的 Tomcat 已經在清單中的 Tomcat 8。
* **Azul Zulu OpenJDK 封裝更新：v1.7 更新 51 和 v1.6 更新 47。** 自這一版開始，有提供 Azul System 的 Zulu Open JDK v7 封裝更新 51。 此外，也開始提供 Zulu Open JDK v6 封裝，從更新 47 開始。 除了這些更新之外，先前已提供 Zulu Open JDK v7 封裝更新 45、更新 40 和更新 25。
* **支援 A8 和 A9 Microsoft Azure 虛擬機器大小。** 您現在可以將雲端服務部署到高記憶體的 A8 和 A9 虛擬機器大小。 如需這些 VM 大小的詳細資訊，請參閱 [虛擬機器和雲端服務大小][]。
* **針對啟用 SSL 的角色，自動從 HTTP 重新導向至 HTTPS。** 當您的雲端服務只包含 HTTPS 角色時，如果使用者的要求指定 HTTP，它會自動重新導向至 HTTPS。 您不需要建立個別角色來處理 HTTP 要求。
* **用於本機模擬的快速模擬器。** 在本機上偵錯應用程式時，現在會使用 Azure 快速模擬器做為模擬器。
* **Azure 已更名為 Microsoft Azure。** UI 畫面現在會反映 Azure 已更名的事實而不再稱為 Azure。

### 2014 年 2 月 6日 ###

適用於 Eclipse 的 Azure 外掛程式 - 2014 年 2 月預覽已發行。 此更新包含 2013 年 10 月預覽以來所推出的新功能、錯誤修正，以及一些根據意見反應所開發出的使用性增強功能：

* **支援 SSL 卸載。** 已將安全通訊端層 (SSL) 卸載新增為一項功能，讓您可以在 Azure 上的 Java 部署中輕鬆啟用超文字安全傳輸通訊協定 (HTTPS) 支援，而不必在 Java 應用程式伺服器中設定 SSL。 在工作階段親和性和/或驗證通訊案例中，這個功能特別有用。 例如，在使用此工具組已經支援的存取控制服務 (ACS) 篩選器時。 如需詳細資訊，請參閱 [SSL 卸載][] 和 [如何使用 SSL 卸載][]。
* **GlassFish 4 現在是可辨識的應用程式伺服器。** 如果您選取 GlassFish 4 安裝目錄中的機器上 **伺服器** ] 索引標籤的 **Azure 部署專案** ] 對話方塊中，外掛程式會立即自動偵測到它，並能夠部署 GlassFish OSE 4，以自動化方式，類似於 GlassFish OSE 3 版本已經在清單中。
* **Azul Zulu OpenJDK 封裝更新 45。** 自這一版開始，現在會提供 Azul System 的 Zulu (Open JDK v7 封裝) 更新 45，除此之外，先前已提供更新 40 和更新 25。
* **支援私人端點連接埠的「自動」功能。** 您可以將輸入端點和內部端點的私人連接埠設定為自動，以讓 Azure 自動為該端點指派連接埠。 先前您只能指派特定連接埠號碼。
* **支援在建立自我簽署憑證的 UI 中自訂憑證名稱 (CN)。** 先前會對所有新憑證使用相同的硬式編碼名稱，但現在您可以指定自有憑證名稱，以協助區分 Azure 入口網站中具有不同用途的多個憑證。
* **Azure 工具列 ︰** {1}azure 工具列已更新下列變更 ︰ 
    * ![][ic710876] 這個圖示已加入之 **新增 Azure 部署專案**。
    * ![][ic710877] 已加入這個圖示來做為建立自我簽署憑證對話方塊的捷徑。
* **支援 A5 Azure 虛擬機器大小。** 您現在可以將雲端服務部署到高記憶體的 A5 虛擬機器大小。 如需此 VM 大小的詳細資訊，請參閱 [虛擬機器和雲端服務大小][]。
* **支援 Microsoft Windows Server 2012 R2。** 您現在可以選取 Windows Server 2012 R2 做為雲端作業系統。

### 2013 年 10 月 22日 ###

適用於 Eclipse 的 Azure 外掛程式 - 2013 年 10 月預覽已發行。 此更新包含 2013 年 9 月預覽以來所推出的新功能、錯誤修正，以及一些根據意見反應所開發出的使用性增強功能：

* **支援 Azure SDK 2.2 版本。** 適用於 Eclipse 的 Azure 外掛程式 - 2013 年 10 月預覽支援 Azure SDK 2.2。 此外掛程式仍然可以使用 Azure SDK 2.1，而且如果您還沒有至少安裝 Azure SDK 2.1，則會自動安裝 Azure SDK 2.2。
* **Azul Zulu OpenJDK 封裝更新 40。** 正如 2013 年 9 月預覽所宣佈的，此外掛程式現在能直接在 Azure 上使用第三方提供的 JDK，而不需要您上傳自己的 JDK。 在 2013 年 10 月版本中，現在會提供 Azul System 的 Zulu (Open JDK v7 封裝) 更新 40，除此之外，原先已發佈了更新 25。
* **活動記錄檔中的雲端部署連結。** Azure 活動記錄檔中，當您部署的狀態為 [ **發佈**, ，您可以按一下 **發佈** ，因為它現在已經連結至您的部署; 然後您的瀏覽器中開啟您的部署。 (狀態 **發行** 先前標示為 **執行**。)
* **發佈時可以選擇目標 OS。**  **Publish to Azure** 對話方塊會包含新欄位， **目標 OS**, ，它會提供更容易找到的方法，讓您設定您的目標作業系統。
* **自動覆寫先前的部署。**  **Publish to Azure** 對話方塊會包含新的核取方塊， **覆寫先前的部署**。 如果選取此選項，就會在發佈新的部署時自動覆寫先前的部署。當您沒有先取消發佈先前的部署就將新部署發佈至相同位置時，您不會再遇到衝突問題。
* **Jetty 9 現在是可辨識的應用程式伺服器。** 如果您選取 Jetty 9 安裝目錄中的機器上 **伺服器** ] 索引標籤的 **Azure 部署專案** ] 對話方塊中，外掛程式會立即自動偵測到它，並能夠部署 Jetty 9 以自動化方式，已經在清單中較舊版本的 Jetty 類似。
* **從 [專案] 內容功能表新增角色。**  **Azure** 專案內容功能表現在包含新的功能表項目， **加入角色**, ，它會提供一種更快和更多的探索方法，讓您 Azure 專案中加入新的角色。
* **Azure Libraries for Java 程式庫封裝更新。** 這根據版本 0.4.6 [Microsoft Azure 用戶端 API][]。

### 2013 年 9 月 25 日 ###

適用於 Eclipse 的 Azure 外掛程式 - 2013 年 9 月預覽已發行。 此更新包含 2013 年 8 月預覽以來所推出的新功能、錯誤修正，以及一些根據意見反應所開發出的使用性增強功能：

* **能夠部署 Azure 上提供的 Azul Zulu OpenJDK 封裝。** 在指定要用於 Azure 部署的 JDK 時已加入新的選項。 使用此選項，您可以直接在 Azure 雲端上部署第三方 JDK 封裝，而不必上傳您自己的 JDK。 Azul Systems 提供了第一個這類封裝，其名稱為 Zulu，是以 OpenJDK 為基礎，而您現在可以使用此選項部署這個封裝。
* **Azure Libraries for Java 程式庫封裝更新。** 這根據版本 0.4.5 [Microsoft Azure 用戶端 API][]。

### 2013 年 8 月 1日 ###

適用於 Eclipse 的 Azure 外掛程式 - 2013 年 8 月預覽已發行。 這個更新需搭配屬於必要條件的 Azure SDK 2.1 版，而且在您安裝外掛程式時會自動下載此 SDK。 此更新包含 2013 年 7 月預覽以來所推出的新功能、錯誤修正，以及一些根據意見反應所開發出的使用性增強功能：

* **移除將本機 JDK 和本機應用程式伺服器納入成為部署封裝一部分的選項。** 若要將這些元件內嵌在封裝中，最好是在部署期間從雲端儲存體下載 JDK 和應用程式伺服器，因為下載這些項目會導致較小的部署封裝大小、更快速的部署時間以及更容易進行維護。 因此，我們移除了將 JDK 和應用程式伺服器納入部署封裝的選項。 已設定為將本機 JDK 和本機應用程式伺服器納入成為部署封裝一部分的現有專案，會自動轉換為將 JDK 和應用程式伺服器自動上傳到雲端儲存體。
* **支援 Azure SDK 2.1 版本。** 適用於 Eclipse 的 Azure 外掛程式 - 2013 年 8 月預覽需要 Azure SDK 2.1。 請勿將 2013 年 8 月預覽用於舊版 Azure SDK，此外，也請勿將 Azure SDK 2.1 用於舊版適用於 Eclipse 的 Azure 外掛程式。
* **支援 Eclipse Kepler 版本。** 與此相關的是，新的最小必要 Eclipse IDE 版本為 Indigo。 適用於 Eclipse 的 Azure 外掛程式不會再於 Helios 上進行正式測試。

### 2013 年 7 月 3 日 ###

適用於 Eclipse 的 Azure 外掛程式 - 2013 年 7 月預覽已發行。 此更新包含 2013 年 5 月預覽以來所推出的新功能、錯誤修正，以及一些根據意見反應所開發出的使用性增強功能：

* **能夠建立新的儲存體帳戶。** A **新增** ] 按鈕已加入至 **加入儲存體帳戶** ] 對話方塊。 這可讓您在 Eclipse 外掛程式內建立儲存體帳戶，而不需要您登入 Azure 管理入口網站  (您必須已經有 Azure 訂用帳戶才能使用此功能)。如需建立新的儲存體帳戶的詳細資訊，請參閱 [來建立新的儲存體帳戶][]。
* **儲存體帳戶有新的 [(自動)] 選項可供用來自動部署 JDK 和伺服器，以及用來進行快取。** 使用時 **自動上傳** 選項 JDK 和應用程式伺服器，您現在可以指定 **（自動）** 上載 JDK 時要使用的 URL 和儲存體帳戶和應用程式伺服器，或使用 Azure 快取時。 然後，這些功能會自動使用相同的儲存體帳戶與您在中選取一個 **Publish to Azure** ] 對話方塊。  [在 Eclipse 中建立 Azure 的 Hello World 應用程式][] 教學課程已更新為使用新 **（自動）** 選項。
* **能夠設定 Azure 服務端點。** 指定服務端點以決定應用程式要部署至全域 Azure 平台、由位於中國的 21Vianet 所運作的 Azure 或私人 Azure 平台，並由該平台進行管理。 如需詳細資訊，請參閱 [Azure 服務端點][]。
* **大型部署可以指定本機儲存資源。** 如果您的部署太大，預設的 approot 資料夾無法容納時，您現在可以指定本機儲存資源來做為 JDK 和應用程式伺服器的部署目的地。 如需詳細資訊，請參閱 [部署大型部署][]。
* **支援 A6 和 A7 Azure 虛擬機器大小。** 您現在可以將雲端服務部署到高記憶體的 A6 和 A7 虛擬機器大小。 如需這些大小的詳細資訊，請參閱 [虛擬機器和雲端服務大小][]。
* **Azure Libraries for Java 程式庫封裝更新。** 這根據版本 0.4.4 [Microsoft Azure 用戶端 API][]。

### 2013 年 5 月 1 日 ###

適用於 Eclipse 的 Azure 外掛程式 - 2013 年 5 月預覽已發行。 這個重大更新需搭配屬於必要條件的 Azure SDK 2.0 版，而且在您安裝外掛程式時會自動下載此 SDK。 此版本包含 2013 年 2 月預覽以來所推出的新功能、錯誤修正，以及一些根據意見反應所開發出的使用性增強功能：

* **自動上傳 JDK 和應用程式伺服器到 Azure 儲存體，以及從 Azure 儲存體進行部署。** 這個新選項會視需要將選取的 JDK 和應用程式伺服器自動上傳至指定的 Azure 儲存體帳戶，並從該處部署這些元件，而不必在部署封裝中內嵌或讓使用者屆時手動上傳。 這個普遍要求的功能可以大幅提高部署 JDK 和伺服器元件的便利性，對新手使用者來說更是如此。 使用這些選項的逐步解說，請參閱 [在 Eclipse 中建立 Azure 的 Hello World 應用程式][]。
* **集中式儲存體帳戶追蹤，以及能夠更輕鬆地參考儲存體帳戶 (透過下拉式控制項)。** 這適用於多個依賴儲存體的功能，例如 JDK 和伺服器元件部署，以及快取。 如需詳細資訊，請參閱 [Azure 儲存體帳戶清單][]。
* **簡化發佈到雲端精靈的遠端存取設定。** 您只需要輸入使用者名稱和密碼就能啟用遠端存取，或是將欄位空白以讓遠端存取保持停用。
* **Azure Libraries for Java 程式庫封裝更新。** 這根據版本 0.4.2 [Microsoft Azure 用戶端 API][]。
* **支援 Windows Server 2012 上的黏性工作階段。** 黏性工作階段先前只能在 Windows Server 2008 R2 上運作，但現在兩種雲端作業系統目標皆支援工作階段親和性。
* **改進封裝上傳效能。** 即使 JDK 和應用程式伺服器內嵌於部署封裝中，部署程序的上傳部分仍會比舊版快上近兩倍。

### 2013 年 2 月 8 日 ###

適用於 Eclipse 的 Azure 外掛程式 - 2013 年 2 月預覽已發行。 這個次要更新包含 2012 年 11 月預覽以來所推出的錯誤修正、根據意見反應所開發出的使用性增強功能，以及一些新增的功能：

* 支援從公用或私人 Azure blob 儲存體下載項目部署 JDK、應用程式伺服器和任意其他元件，而不必在部署至雲端時將它們納入部署封裝中。
* 能夠變更角色的使用者定義的元件會處理的順序，透過加入 **移** 和 **下移** 按鈕 **元件** 區段 **Azure 角色屬性**。
* 若要更新 **Azure libraries for Java 封裝** 程式庫，根據版本 0.4.0 [Microsoft Azure 用戶端 API][]。

### 2012 年 11 月 5 日 ###

適用於 Eclipse 的 Azure 外掛程式 - 2012 年 11 月預覽已發行。 這個重大更新包含 2012 年 9 月預覽以來所推出的一些新功能、其他錯誤修正，以及根據意見反應所開發出的使用性增強功能：

* 支援將 Microsoft Windows Server 2012 做為雲端作業系統。
* 支援 Memcached 用戶端的 Azure 共置快取。
* 加入 Apache Qpid JMS 用戶端程式庫以利用 Azure AMQP 型傳訊。
* 改良 **新的專案** 精靈結尾為使用者提供了可快速啟用幾個常見的重要功能，其專案中的新網頁 ︰ 自黏工作階段、 快取和遠端偵錯。
* 在計算模擬器中執行時會自動將角色執行個體減少為 1 個，以避免伺服器執行個體之間發生連接埠繫結衝突。

### 2012 年 9 月 28 日 ###

適用於 Eclipse 的 Azure 外掛程式 - 2012 年 9 月預覽已發行。 此服務更新包含 2012 年 8 月預覽以來所推出的一些其他錯誤修正，以及一些根據意見反應所開發出的現有功能使用性增強功能：

* 支援將 Microsoft Windows 8 和 Microsoft Windows Server 2012 做為開發作業系統，解決先前讓外掛程式無法在這些作業系統上正常運作的問題。
* 改善指定端點連接埠範圍的支援。
* 與包含空格的檔案路徑相關的錯誤修正。
* 改善角色內容功能表，以便更快速地存取角色特定的組態設定。
* 微幅調整 **發佈到雲端** 精靈和一些其他錯誤修正。

### 2012 年 8 月 28 日 ###

適用於 Eclipse 的 Azure 外掛程式 - 2012 年 8 月預覽已發行。 此服務更新包含 2012 年 7 月預覽以來所推出的其他錯誤修正，以及幾個根據意見反應所開發出的現有功能使用性增強功能：

* 在 [Azure 存取控制服務篩選器] 對話方塊內：
    * **內嵌的簽章憑證** 在應用程式的 WAR 檔案中，以簡化雲端部署。
    * **若要建立自我簽署的憑證的選項** 在 ACS 篩選 UI。 如需有關 Azure 存取控制服務篩選器的詳細資訊，請參閱 [Eclipse 搭配 Azure 存取控制服務使用的驗證 Web 使用者如何][]。
* 在 Azure 部署專案精靈 (也適用於角色的 [伺服器組態] 屬性頁) 內：
    * **自動探索 JDK 位置** 電腦上 （如有需要，您可以覆寫）。
    * **自動偵測伺服器類型的** 當您選取的應用程式伺服器安裝目錄。

### 2012 年 7 月 15 日 ###

適用於 Eclipse 的 Azure 外掛程式 - 2012 年 7 月預覽可解決 2012 年 6 月版本發行後，使用者所找到及 (或) 報告的許多最高優先順序錯誤。 這只是服務更新，因此未包含任何新功能。

### 2012 年 6 月 7日 ###

適用於 Eclipse 的 Azure 外掛程式 - 2012 年 6 月 CTP 已發行。 新功能包括：

* **新增 Azure 部署專案精靈]:** 可讓您選取 JDK、 Java 應用程式伺服器，以及直接在改進的精靈 UI 中的 Java 應用程式。 包含在立即可用的伺服器組態清單中以供您從中選擇的項目包括 Tomcat 6、Tomcat 7、GlassFish OSE 3、Jetty 7、Jetty 8、JBoss 6 和 JBoss 7 (獨立版)。 此外，您也可以自訂伺服器組態清單。 此 UI 改進功能可替代先前的主要方法，也就是拖放壓縮檔並複製到啟動指令碼。 該方法仍可正常運作，但可能只會用於更進階的案例。
* **伺服器組態角色屬性頁 ︰** 可讓您輕鬆地切換 Jdk、 Java 應用程式伺服器和您在建立專案後，您的部署與相關聯的應用程式。 如需詳細資訊，請參閱 [伺服器組態屬性][]。
* **& q u o t;發行至雲端告訴我們理由。精靈 ︰** 提供簡單的方法，將專案部署至 Azure，直接從 Eclipse，自動化先前手動困難的提取認證，登入 Azure 管理入口網站中上, 傳封裝等。如需如何直接將您的專案部署至 Azure 的範例，請參閱 [在 Eclipse 中建立 Azure 的 Hello World 應用程式][]。
* **Azure 工具列 ︰** Azure 工具列現已提供 Eclipse，其中包含叫用下列功能的按鈕 ︰
    * ![][ic710879] **Run in Azure Emulator**︰ 在模擬器中執行您的專案。
    * ![][ic710880] **Reset Azure Emulator**︰ 重設模擬器。
    * ![][ic710881] **Azure 建置雲端套件**︰ 編譯您的部署的封裝。
    * ![][ic710876] **新增 Azure 部署專案**︰ 建立新的 Azure 部署專案。
    * ![][ic710882] **Publish to Azure Cloud**︰ 將專案發佈到 Azure。
    * ![][ic710883] **Unpublish**︰ 刪除您的部署。
    * 許多 Azure 工具列按鈕中使用 [在 Eclipse 中建立 Azure 的 Hello World 應用程式][]。
* **Azure Libraries for Java:** 現已開放下載的一部分的單一 Package for Azure Libraries for Java 程式庫，在 Eclipse 中，隨附外掛程式安裝且包含所有必要相依性。 您只需要將一個程式庫參考加入 Java 專案中，不必再另外下載任何項目。 如需詳細資訊，請參閱 [安裝 Azure Toolkit for Eclipse][]。
* **Microsoft JDBC Driver 4.0 for SQL Server 在外掛程式安裝期間，您可以使用 ︰** 新外掛程式的安裝期間，您可以安裝最新版本的 Microsoft JDBC Driver for SQL Server。
* **Azure 存取控制服務篩選器可用在外掛程式安裝期間 ︰** 內含的 Eclipse 程式庫，此工具組，在這個新元件，可讓以順暢地利用 Azure 存取控制服務 (ACS) 驗證使用不同的身分識別提供者，例如 Google、 Live.com 和 yahoo ！ 的 Java web 應用程式。 您不需要自行撰寫驗證邏輯，只要設定幾個選項，然後讓篩選器執行讓使用者使用 ACS 進行登入的困難工作。 您可以完全專注於撰寫程式碼，以讓使用者根據要求物件內的篩選器傳回給應用程式的身分識別來存取資源。 如需使用 ACS 篩選器的教學課程，請參閱 [Eclipse 搭配 Azure 存取控制服務使用的驗證 Web 使用者如何][]。
* **Azure SDK 1.7 必要條件的自動偵測 ︰** 當您建立新的 Azure 部署專案時，Azure SDK 1.7 就會自動下載如果尚未安裝。
* **執行個體端點 ︰** 允許直接存取連接埠端點進行通訊的負載平衡的角色執行個體。 您可以加入執行個體端點，透過端點 UI，可透過 [端點屬性][] 頁面。 這有助於在具有多個執行個體部署的案例中，對雲端上執行的特定計算執行個體進行遠端偵錯和 JMX 診斷。 (執行個體端點也會列在 
* **元件 UI:** 輕鬆設定專案中的個別 Azure 角色與其他外部的資源，例如 Java 應用程式專案的專案相依性的進階使用者; 也可輕鬆描述其部署邏輯。 如需詳細資訊，請參閱 [元件屬性][]。
* **自動升級舊版的專案 ︰** 當您開啟的工作區具有舊版外掛程式所建立的 Azure 專案時，舊專案將會顯示在 Eclipse 中為關閉狀態，因為舊版的專案不是以新的版本相容。 如果您嘗試開啟其中一個舊專案，將會啟動升級精靈。 如果您同意升級時，新的專案，與 **_Upgraded** 附加到名稱時，會建立並自動更新為使用新的版本。 您可以視需要重新命名新專案。 升級過程中不會修改原始專案 (並且會保持關閉狀態)。

### 2011 年 12 月 10日 ###

適用於 Eclipse 的 Azure 外掛程式 - 2011 年 12 月 CTP 已發行。 新功能包括：

* **工作階段親和性 (& q u o t; 自黏工作階段 & q u o t;) 支援 ︰** 協助啟用可設定狀態，叢集 Java 應用程式只是單一的核取方塊。 如需詳細資訊，請參閱 [工作階段同質性][]。
* **預先製作的啟動指令碼範例 ︰** 的熱門 Java 伺服器 （Tomcat、 Jetty、 JBoss、 GlassFish），您可以只複製/貼上從您的專案範例目錄在啟動指令碼。
* **即時模擬器啟動輸出 ︰** 可以現在觀看所有步驟的執行啟動指令碼在專用的主控台視窗中，從顯示您的進度和失敗指令碼在 Azure 執行。
* **自動、 輕量級 java.exe 監視 ︰** java.exe 停止時執行，使用輕量型、 預先製作的指令碼，會自動包含在部署中，會強制角色回收。
* **遠端偵錯設定 UI 的 Java 應用程式 ︰** 可讓您輕鬆地啟用 Eclipse 的遠端偵錯工具存取您的 Java 應用程式在模擬器或 Azure 雲端執行，因此您可以逐步執行和偵錯即時 Java 程式碼。 如需詳細資訊，請參閱 [在 Eclipse 中偵錯 Azure 應用程式][]。
* **本機儲存體資源設定 UI:** 您不再需要直接操作 XML 來設定本機資源。 這項功能也可讓您在本機資源已透過環境變數 (可直接從啟動指令碼參考) 進行部署之後，存取本機資源的有效檔案路徑。 如需詳細資訊，請參閱 [本機儲存體屬性][]。
* **環境變數設定 UI:** 您不再需要設定環境變數，透過手動編輯組態 XML。 如需詳細資訊，請參閱 [環境變數內容][]。
* **SQL Azure 的 JDBC 驅動程式 ︰** 取得透過外掛程式安裝為完美的整合式 Eclipse 程式庫，讓您更輕鬆針對 SQL Azure 的程式設計。 
* **快速的內容功能表存取角色設定 UI**︰ 只要以滑鼠右鍵按一下 [角色] 資料夾，然後按一下 **屬性**。
* **自訂 Azure 專案和角色資料夾圖示 ︰** 更好的可見性和方便瀏覽您的工作區和專案中。

## 另請參閱 ##

[適用於 Eclipse 的 Azure 工具組][]

[安裝 Azure Toolkit for Eclipse][] 

[在 Eclipse 中建立適用於 Azure 的 Hello World 應用程式][]

如需有關如何使用 java 的 Azure 的詳細資訊，請參閱 [Azure Java 開發人員中心][]。

<!-- URL List -->

[Azul Systems web page for the Zulu OpenJDK]: http://go.microsoft.com/fwlink/?LinkId=402457
[Azure Java Developer Center]: http://go.microsoft.com/fwlink/?LinkID=699547
[Azure Service Endpoints]: http://go.microsoft.com/fwlink/?LinkID=699526
[Azure Storage Account List]: http://go.microsoft.com/fwlink/?LinkID=699528
[Azure Toolkit for Eclipse]: http://go.microsoft.com/fwlink/?LinkID=699529
[Components properties]: http://go.microsoft.com/fwlink/?LinkID=699525#components_properties
[Creating a Hello World Application for Azure in Eclipse]: http://go.microsoft.com/fwlink/?LinkID=699533
[Debugging Azure Applications in Eclipse]: http://go.microsoft.com/fwlink/?LinkID=699535
[Deploying Large Deployments]: http://go.microsoft.com/fwlink/?LinkID=699536
[Endpoints properties]: http://go.microsoft.com/fwlink/?LinkID=699525#endpoints_properties
[Environment variables properties]: http://go.microsoft.com/fwlink/?LinkID=699525#environment_variables_properties
[How to Authenticate Web Users with Azure Access Control Service Using Eclipse]: http://go.microsoft.com/fwlink/?LinkID=264703
[How to Use SSL Offloading]: http://go.microsoft.com/fwlink/?LinkID=699545
[Installing the Azure Toolkit for Eclipse]: http://go.microsoft.com/fwlink/?LinkId=699546
[Local storage properties]: http://go.microsoft.com/fwlink/?LinkID=699525#local_storage_properties
[Microsoft Azure Client API]: http://go.microsoft.com/fwlink/?LinkId=280397
[Server configuration properties]: http://go.microsoft.com/fwlink/?LinkID=699525#server_configuration_properties
[Session Affinity]: http://go.microsoft.com/fwlink/?LinkID=699548
[SSL Offloading]: http://go.microsoft.com/fwlink/?LinkID=699549
[To create a new storage account]: http://go.microsoft.com/fwlink/?LinkID=699528#create_new
[Virtual Machine and Cloud Service Sizes for Azure]: http://go.microsoft.com/fwlink/?LinkId=466520

<!-- IMG List -->

[ic710876]: ./media/azure-toolkit-for-eclipse-whats-new/ic710876.png
[ic710877]: ./media/azure-toolkit-for-eclipse-whats-new/ic710877.png
[ic710879]: ./media/azure-toolkit-for-eclipse-whats-new/ic710879.png
[ic710880]: ./media/azure-toolkit-for-eclipse-whats-new/ic710880.png
[ic710881]: ./media/azure-toolkit-for-eclipse-whats-new/ic710881.png
[ic710876]: ./media/azure-toolkit-for-eclipse-whats-new/ic710876.png
[ic710882]: ./media/azure-toolkit-for-eclipse-whats-new/ic710882.png
[ic710883]: ./media/azure-toolkit-for-eclipse-whats-new/ic710883.png


