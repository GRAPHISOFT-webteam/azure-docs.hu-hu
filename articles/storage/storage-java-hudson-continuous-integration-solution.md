<properties
    pageTitle="如何使用 Hudson 與 Azure Blob 服務 | Microsoft Azure"
    description="說明如何使用 Hudson 與 Azure Blob 儲存體作為組建成品的存放庫。"
    services="storage"
    documentationCenter="java"
    authors="rmcmurray"
    manager="wpickett"
    editor="jimbe"/>

<tags
    ms.service="storage"
    ms.workload="storage"
    ms.tgt_pltfrm="na"
    ms.devlang="Java"
    ms.topic="article"
    ms.date="10/12/2015" 
    ms.author="robmcm"/>


# 使用 Azure 儲存體與 Hudson 連續整合解決方案

## 概觀

下列資訊說明如何使用 Azure Blob 服務，作為 Hudson 連續整合 (CI) 解決方案所建立的組建成品儲存機制，或作為在組建程序中要使用之可下載檔案的來源。 您會發現這很實用的其中一種情況就是，當您在敏捷式開發環境中編寫程式碼 (使用 Java 或其他語言) 時，組建是根據連續整合來執行，而您需要一個存放庫來存放組建成品，以便能夠將這些成品分享給其他組織成員或客戶，或是維護封存等等。 另一種情況是當組建工作本身需要其他檔案時，例如，要隨組建輸入一起下載的相依性項目。

在本教學課程中，您將使用由 Microsoft 所提供適用於 Hudson CI 的 Azure 儲存體外掛程式。

## Hudson 簡介

Hudson 提供軟體專案的連續整合，方法是允許開發人員輕易整合其程式碼變更，並可自動和頻繁地產生組建，進而提高開發人員的產能。 組建會分版本存在，而組建成品可以上傳至各種存放庫。 本文將示範如何使用 Azure Blob 儲存體作為組建成品的存放庫。 其中也示範如何從 Azure Blob 儲存體下載相依性。

有關 Hudson 的詳細資訊，請參閱 [認識 Hudson []][]。

## 使用 Blob 服務的優點

使用 Blob 服務來裝載您敏捷式開發組建成品的優點包括：

- 組建成品和/或可下載相依性項目的高可用性。
- Hudson CI 解決方案上傳組建成品時的效能。
- 在您的客戶和合作夥伴下載組建成品時提供良好的效能。
- 提供使用者存取原則控制，可以選擇匿名存取、期限型共用存取簽章存取、私用存取等。

## 必要條件

您將需要下列項目來使用 Blob 服務與 Hudson CI 解決方案：

- Hudson 連續整合解決方案。

    如果您目前沒有 Hudson CI 解決方案，您可以使用下列技巧來執行 Hudson CI 解決方案：

    1. 在已啟用 Java 的機器上，下載 Hudson WAR，從 <http://hudson-ci.org/>。
    2. 在命令提示字元中，會開啟至包含 Hudson WAR 的資料夾，並執行 Hudson WAR。 例如，如果您已下載 3.1.2 版：

        `java 的 jar hudson 3.1.2.war`

    3. 在瀏覽器中，開啟 `http://localhost:8080/`。 這將會開啟 Hudson 儀表板。

    4. 第一次使用 Hudson 時，請在 `http://localhost:8080/` 中完成初始設定。

    5. 完成初始設定、 取消中的 Hudson WAR 執行個體，啟動 Hudson WAR，並重新開啟 Hudson 儀表板之後 `http://localhost:8080/ /`, ，可用來安裝和設定 Azure 儲存體外掛程式。

        雖然典型的 Hudson CI 解決方案可設定作為服務執行，但在本教學課程中，您可以直接在命令列中執行 Hudson war。

- 一個 Azure 帳戶。您可以註冊 Azure 帳戶，請造訪 <http://www.azure.com>。

- 一個 Azure 儲存體帳戶。 如果您還沒有儲存體帳戶，您可以建立一個使用中的步驟 [如何建立儲存體帳戶 []][]。

- 建議學習 Hudson CI 解決方案，但這不是必須的，因為下列內容會使用基本範例來說明使用 Blob 服務作為 Hudson CI 組建成品的儲存機制時的所需步驟。

## 如何使用 Blob 服務與 Hudson CI

若要使用 Blob 服務與 Hudson，您必須安裝 Azure 儲存體外掛程式，設定此外掛程式使用您的儲存體帳戶，然後建立可將組建成品上傳至儲存體帳戶的建置後動作。 這些步驟將於下列各節中說明。

## 如何安裝 Azure 儲存體外掛程式

1. 在 Hudson 儀表板中，按一下 [Manage Hudson]****。
2. 在**管理 Hudson** 頁面中，按一下 [**管理外掛程式**]。
3. 按一下 [Available]**** 索引標籤。
4. 按一下 [其他]****。
5. 在 [**構件上傳程式**] 區段中，選取 [**Microsoft Azure 儲存體外掛程式**]。
6. 按一下 [Install]****。
7. 在安裝完成後，請重新啟動 Hudson。

## 如何設定 Azure 儲存體外掛程式來使用您的儲存體帳戶

1. 在 Hudson 儀表板中，按一下 [Manage Hudson]****。
2. 在 [**管理 Hudson**] 頁面中，按一下 [**設定系統**]。
3. 在 [Microsoft Azure Storage Account Configuration]**** 區段中：

    a. 輸入您儲存體帳戶名稱，您可以從取得 [Azure 入口網站](portal.azure.com)。

    b. 輸入儲存體帳戶金鑰，也可行，或是從 [Azure 入口網站](portal.azure.com)。

    c. 如果您使用的是公用 Azure 雲端，請在 [Blob Service Endpoint URL]**** 使用預設值。 如果您使用不同的 Azure 雲端，使用的端點中所指定 [Azure 入口網站](portal.azure.com) 儲存體帳戶。

    d. 按一下 [Validate storage credentials]**** 以驗證您的儲存體帳戶。

    e. [選用] 如果您有其他可提供 Hudson CI 使用的儲存體帳戶，請按一下 [Add more storage accounts]****。

    f. 按一下 [Save]**** 儲存您的設定。

## 如何建立會將您的組建成品上傳至您儲存體帳戶的建置後動作

為了方便說明，首先，我們需要建立一個會建立數個檔案的工作，然後新增一個會將這些檔案上傳至您儲存體帳戶的建置後動作。

1. 在 Hudson 儀表板中，按一下 [新增工作]****。
2. 將此工作命名為 **MyJob**，按一下 [Build a free-style software job]****，然後按一下 [確定]****。
3. 在工作組態的 [Build]**** 區段中，按一下 [Add build step]**** 並選擇 [Execute Windows batch command]****。
4. 在 [Command]**** 中，使用下列命令：

        md text
        cd text
        echo Hello Azure Storage from Hudson > hello.txt
        date /t > date.txt
        time /t >> date.txt

5. 在工作組態的 [Post-build Actions]**** 區段中，按一下 [Upload artifacts to Microsoft Azure Blob storage]****。
6. 在 [儲存體帳戶名稱]**** 中，選取要使用的儲存體帳戶。
7. 在 [Container Name]**** 中，指定容器名稱。 (如果上傳組建成品時該容器尚未存在，將會建立該容器。) 您可以使用環境變數，就這個範例而言，請輸入 **${JOB_NAME}** 作為容器名稱。

    **秘訣**

    在您為 [Execute Windows batch command]**** 輸入指令碼的 [命令]**** 區段下方是個連結，可連結到 Hudson 認可的環境變數。 按一下該連結即可了解各環境變數名稱和描述。 請注意，含有特殊字元的環境變數 (例如 **BUILD_URL** 環境變數) 不能當做容器名稱或共同虛擬路徑。

8. 在此範例中，請按一下 [Make new container public by default]****。 (如果您想要使用私用容器，則需要建立共用存取簽章來允許存取。 這已超出本文的範圍。 您可以進一步了解共用的存取簽章在 [建立共用存取簽章](http://go.microsoft.com/fwlink/?LinkId=279889).)
9. [選擇性] 如果您要在上傳組建成品之前清除容器的內容，請按一下 [Clean container before uploading]**** (若不想清除容器的內容，請維持不核取)。
10. 在 [List of Artifacts to upload]****，輸入 **text/*.txt**。
11. 如 **已上傳的成品的共同虛擬路徑**, ，輸入 **${BUILD\_ID} / ${BUILD\_NUMBER}**。
12. 按一下 [Save]**** 儲存您的設定。
13. 在 Hudson 儀表板中，按一下 [Build Now]**** 以執行 **MyJob**。 檢查主控台輸出中的狀態。 當建置後動作開始上傳組建成品時，主控台輸出將會包含 Azure 儲存體的狀態訊息。
14. 順利完成作業時，您就可以開啟公用 Blob 來檢查組建成品。

    a. 登入 [Azure 入口網站](portal.azure.com)。

    b. 按一下 [儲存體]****。

    c. 按一下您在 Hudson 中使用的儲存體帳戶名稱。

    d. 按一下 [容器]****。

    e. 按一下名為 **myjob** 的容器，這是您在建立 Hudson 工作時所指定工作名稱的小寫版本。 在 Azure 儲存體中，容器名稱和 Blob 名稱皆為小寫 (並且區分大小寫)。 在名為 **myjob** 之容器的 Blob 清單中，您應該會看到 **hello.txt** 和 **date.txt**。 請複製這些項目中任何一項的 URL，然後在瀏覽器中開啟它。 您會看到文字檔已上傳作為組建成品。

每個工作只能建立一個將成品上傳至 Azure Blob 儲存體的建置後動作。 請注意，將成品上傳至 Azure Blob 儲存體的單一建置後動作可以在 [要上傳的成品清單]**** 內，使用分號作為分隔符號來指定不同檔案 (包含萬用字元) 和檔案路徑。 例如，若 Hudson 組建產生 JAR 檔和 TXT 檔，在工作區的 **建置** 資料夾，而您想要上傳至 Azure Blob 儲存體兩者，請使用下列 **要上傳--成品** 值: **build/\*.jar;build/\*.txt**。 您也可以使用雙冒號語法來指定要在 Blob 名稱內使用的路徑。 比方說，如果您想要使用上傳 Jar **二進位檔** 在 blob 路徑和使用上傳的 TXT 檔案 **通知** 在 blob 路徑中，使用下列 **要上傳--成品** 值: **build/\*.jar::binaries;build/\*.txt::notices**。

## 如何建立從 Azure Blob 儲存體下載的組建步驟

下列步驟示範如何設定從 Azure Blob 儲存體下載項目的組建步驟。 如果您要在組建中加入項目 (例如您保留在 Azure Blob 儲存體中的 JAR)，這會很有用。

1. 在工作組態的 [Build]**** 區段中，按一下 [Add build step]****，並選擇 [Download from Azure Blob storage]****。
2. 在 [儲存體帳戶名稱]**** 中，選取要使用的儲存體帳戶。
3. 在 [容器名稱]**** 中，指定您要下載的 Blob 所在之容器的名稱。 您可以使用環境變數。
4. 在 [Blob 名稱]**** 中，指定 Blob 名稱。 您可以使用環境變數。 另外，您也可以在指定 Blob 名稱的開頭字母之後，使用星號作為萬用字元。 例如， **專案 *** 指定名稱開頭的所有 blob **專案**。
5. [選擇性] 針對**下載路徑**，請於 Hudson 機器上指定要從 Azure Blob 儲存體下載檔案的目標路徑。 也可以使用環境變數 (如果未提供 [下載路徑]**** 的值，則 Azure Blob 儲存體中的檔案會下載至工作的工作區)。

如果要從 Azure Blob 儲存體下載其他項目，您可以建立其他組建步驟。

執行組建之後，您可以檢查組建記錄主控台輸出，或查看下載位置，了解是否已成功下載您想要的 Blob。

## Blob 服務所使用的元件

以下提供 Blob 服務元件的概觀。

- **儲存體帳戶**：一律透過儲存體帳戶來存取 Azure 儲存體。 這是存取 blob 用的最高等級的命名空間。 帳戶可以包含不限數目的容器，只要它們的大小總計低於 100 TB 即可。
- **容器**：容器提供一組 Blob 的群組。 所有 Blob 都必須放在容器中。 一個帳戶可以包含的容器不限數量。 容器可以儲存無限制的 Blob。
- **Blob**：任何類型和大小的檔案。 Azure 儲存中可以儲存兩種 Blob：區塊 Blob 和分頁 Blob。 大部分檔案都是區塊 Blob。 單一區塊 Blob 的大小上限為 200 GB。 本教學課程使用區塊 Blob。 分頁 Blob (另一種 Blob 類型) 的大小上限為 1 TB，當檔案中的位元組範圍經常修改時，分頁 Blob 的效率較高。 如需 blob 的詳細資訊，請參閱 [了解區塊 Blob 和分頁 Blob](http://msdn.microsoft.com/library/azure/ee691964.aspx)。
- **URL 格式**：可利用下列 URL 格式來定址 Blob：

    `http://storageaccount.blob.core.windows.net/container_name/blob_name`

    (上述格式適用於公用 Azure 雲端。 如果您使用不同的 Azure 雲端，使用中的端點 [Azure 入口網站](portal.azure.com) 來判斷您的 URL 端點。)

    在上述格式中，`storageaccount` 代表您儲存體帳戶的名稱，`container_name` 代表您容器的名稱，而 `blob_name` 則代表您 Blob 的名稱。 容器名稱中可以有多個路徑，這些路徑彼此以正斜線 **/** 分隔。 在本教學課程的範例容器名稱為 **MyJob**, ，和 **${BUILD\_ID} / ${BUILD\_NUMBER}** 所使用的共同虛擬路徑，產生的 blob url 格式如下:

    `http://example.blob.core.windows.net/myjob/2014-05-01_11-56-22/1/hello.txt`

## 後續步驟


[how to create a storage account]: http://go.microsoft.com/fwlink/?LinkId=279823 
[meet hudson]: http://wiki.eclipse.org/Hudson-ci/Meet_Hudson 

