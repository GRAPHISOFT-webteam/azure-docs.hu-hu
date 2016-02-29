<properties
   pageTitle="使用多個服務組態設定 Azure 專案"
   description="了解如何透過變更 ServiceDefinition.csdef 和 ServiceConfiguration.cscfg 檔案來設定 Azure 雲端服務專案。"
   services="visual-studio-online"
   documentationCenter="na"
   authors="TomArcher"
   manager="douge"
   editor="" />
<tags
   ms.service="multiple"
   ms.devlang="multiple"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="09/29/2015"
   ms.author="tarcher" />

# 使用多個服務組態設定 Azure 專案

Azure 雲端服務專案包含兩個組態檔：ServiceDefinition.csdef 和 ServiceConfiguration.cscfg。 這些檔案會與您的 Azure 雲端服務應用程式一起封裝並部署至 Azure。

-  **ServiceDefinition.csdef** 檔案包含的中繼資料所需的 Azure 環境針對雲端服務應用程式，包括包含哪些角色的需求。 這個檔案也包含套用至所有執行個體的組態設定。 在執行階段可以使用 Azure 服務裝載執行階段 API 來讀取這些組態設定。 當您的服務在 Azure 中執行時，無法更新此檔案。

-  **ServiceConfiguration.cscfg** 檔案服務定義檔中定義的組態設定會設定值，並指定要針對每個角色執行的執行個體的數目。 當您的雲端服務在 Azure 中執行時，可以更新此檔案。

Azure Tools for Microsoft Visual Studio 提供的屬性頁面可供您設定這些檔案中儲存的組態設定。 若要存取屬性頁中，按兩下 [方案總管] 中的 Azure 雲端服務專案底下的角色參考，或以滑鼠右鍵按一下角色參考並選擇 [ **屬性**, ，如下圖所示。

![VS_Solution_Explorer_Roles_Properties](./media/vs-azure-tools-multiple-services-project-configurations/IC784076.png)

服務定義檔和服務組態檔的基礎結構描述的相關資訊，請參閱 [結構描述參考](https://msdn.microsoft.com/library/azure/dd179398.aspx)。 如需服務組態的詳細資訊，請參閱 [如何設定雲端服務](cloud-services-how-to-configure.md)。

## 設定角色屬性

下列各節指出 Web 角色和背景工作角色的屬性頁很類似，但有一些差異。

從 **快取** ] 頁面上，您可以設定 Azure 快取服務。

### 組態頁面

在 **組態** ] 頁面上，您可以設定這些屬性:

**執行個體**

設定 **執行個體** count 屬性，服務應該執行這個角色的執行個體的數目。

設定 **VM 大小** 屬性 **超小型**, ，**小型**, ，**媒體**, ，**大**, ，或 **超大型**。  如需詳細資訊，請參閱 [雲端服務的大小](cloud-services-sizes-specs.md)。

**啟動動作** (只有 Web 角色)

設定此屬性，可指定當您開始偵錯時，Visual Studio 應針對 HTTP 端點或 HTTPS 端點或兩者啟動網頁瀏覽器。

只有在您已為角色定義 HTTPS 端點時，才可使用 HTTPS 端點選項。 您可以定義 HTTPS 端點上 **端點** 屬性頁。

如果您已新增 HTTPS 端點，則預設會啟用 HTTPS 端點選項，而在您開始偵錯時，Visual Studio 會為此端點啟動瀏覽器 (除了為 HTTP 端點啟動瀏覽器以外)。 這是假設兩個啟動選項都已啟用。

**診斷**

Web 角色預設會啟用診斷。 Azure 雲端服務專案和儲存體帳戶已設為使用本機儲存體模擬器。 當您準備好要部署至 Azure 時，您可以按一下產生器] 按鈕 (**...**) 更新定域機組中使用 Azure 儲存體的儲存體帳戶。 您可以隨選方式或在自動排程的間隔，將診斷資料傳輸至儲存體帳戶。 如需 Azure 診斷的詳細資訊，請參閱 [在 Azure 雲端服務和虛擬機器中啟用診斷](cloud-services-dotnet-diagnostics.md)。

## 設定頁面

在 **設定** ] 頁面上，您可以新增您的服務組態設定。 組態設定是名稱-值組。 角色中執行的程式碼可以讀取您在執行階段使用類別所提供的組態設定的值 [Azure Managed 程式庫](http://go.microsoft.com/fwlink?LinkID=171026)。 具體來說， [GetConfigurationSettingValue](https://msdn.microsoft.com/library/azure/microsoft.windowsazure.serviceruntime.roleenvironment.getconfigurationsettingvalue.aspx) 方法會傳回具名的組態設定，在執行階段值。

### 設定儲存體帳戶的連接字串

連接字串是一個組態設定，可為儲存體模擬器或 Azure 儲存體帳戶提供連接和驗證資訊。 每當您的程式碼必須從在角色中執行的程式碼存取 Azure 儲存體服務資料 (也就是 Blob、佇列或資料表資料) 時，您必須定義該儲存體帳戶的連接字串。

指向 Azure 儲存體帳戶的連接字串必須使用已定義的格式。 如需有關如何建立連接字串資訊，請參閱 [設定 Azure 儲存體連接字串](storage-configure-connection-string.md)。

當您準備好要針對 Azure 儲存體服務測試您的服務，或當您準備好要將您的雲端服務部署至 Azure 時，您可以變更任何連接字串的值以指向您的 Azure 儲存體帳戶。 按一下 (**...**)，請選取 **輸入儲存體帳戶認證**。 輸入您的帳戶資訊，包含您的帳戶名稱和帳戶金鑰。 在 **儲存體帳戶連接字串** 對話方塊中，您也可能表示您是否想要使用預設的 HTTPS 端點 (預設選項)、 預設 HTTP 端點或自訂端點。 您可能決定要使用自訂端點，如果您已經註冊您的服務的自訂網域名稱中所述 [Azure 儲存體帳戶中設定自訂網域名稱的 blob 資料](storage-custom-domain-name.md)。

>[AZURE.IMPORTANT] 您必須修改成指向 Azure 儲存體帳戶，才能部署您的服務連接字串。 無法執行這項操作，可能會導致您的角色無法啟動，或在初始化中、忙碌中和停止中狀態間循環。

## 端點頁面

背景工作角色可以有任意數目的 HTTP、HTTPS 或 TCP 端點。 端點可以是可供外部用戶端使用的輸入端點，或是可供在服務中執行的其他角色使用的內部端點。

- 若要讓 HTTP 端點可供外部用戶端和網頁瀏覽器使用，請將端點類型變更為輸入，並指定名稱和公用連接埠號碼。

- 若要讓外部用戶端和網頁瀏覽器使用 HTTPS 端點，將端點類型變更為 **輸入**, ，並指定名稱、 公用連接埠編號，以及管理憑證的名稱。

    請注意，您可以指定管理憑證之前，您必須定義憑證上 **憑證** 屬性頁。

- 若要讓端點可供雲端服務中的其他角色用於內部存取，請將端點類型變更為內部，並指定此端點的名稱和可能的私人連接埠。

## 本機儲存體頁面

您可以使用 **本機儲存體** 保留一或多個角色的本機儲存體資源的屬性頁。 本機儲存體資源是執行中角色執行個體所在之 Azure 虛擬機器的檔案系統中的保留目錄。 如需如何使用本機儲存體資源的詳細資訊，請參閱 [設定本機儲存體資源](cloud-services-configure-local-storage-resources.md)。

## 憑證頁面

在 **憑證** ] 頁面上，您可以將憑證與您的角色。 您加入的憑證可以用來設定 HTTPS 端點上 **端點** 屬性頁。

 **憑證** 屬性頁會將憑證的相關資訊加入至您的服務組態。 請注意您的憑證未隨附您的服務。您必須將您的憑證上載分別透過 Azure [Azure 管理入口網站](http://go.microsoft.com/fwlink/?LinkID=213885)。

若要讓憑證與角色產生關聯，請提供憑證的名稱。 您可以使用此名稱來參照憑證上設定 HTTPS 端點時 **端點** 屬性頁。 接下來，指定憑證存放區是 **本機** 或 **目前使用者** 和存放區的名稱。 最後，輸入憑證的指紋。 如果憑證在目前使用者 \ 個人 (我的) 存放區中，您可從填入的清單中選取憑證，以輸入憑證的指紋。 如果憑證位於任何其他位置，請以手動方式輸入指紋值。

當您從憑證存放區新增憑證時，所有中繼憑證都會自動新增至您的組態設定。 這些中繼憑證也必須上傳至 Azure，以便針對 SSL 正確設定您的服務。

只在您的服務在雲端執行時，與您的服務相關聯的管理憑證才會套用至您的服務。 當您的服務在本機開發環境中執行時，它會使用由計算模擬器所管理的標準憑證。

## 建立 Azure 雲端服務專案

若要設定套用至整個 Azure 雲端服務專案的設定，請先開啟該專案節點的捷徑功能表，然後選擇 [屬性] 以開啟其屬性頁面。 下表顯示這些屬性頁面。

|屬性頁面|說明|
|---|---|
|應用程式|在此頁面，您可以顯示此雲端服務專案使用的 Azure Tools 版本相關資訊，而且可以升級至目前版本的工具。|
|建置事件|在此頁面，您可以設定建置前和建置後事件。|
|開發|在此頁面，您可以指定組建組態指示以及執行任何建置後事件的條件。|
|Web|在此頁面，您可以設定 Web 伺服器的相關設定。|

