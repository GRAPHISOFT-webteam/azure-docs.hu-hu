<properties 
    pageTitle="如何管理媒體服務帳戶中的串流端點" 
    description="本主題說明如何使用 Azure 傳統入口網站管理串流端點。" 
    services="media-services" 
    documentationCenter="" 
    authors="Juliako" 
    writer="juliako" 
    manager="dwrede" 
    editor=""/>

<tags 
    ms.service="media-services" 
    ms.workload="media" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="12/09/2015"
    ms.author="juliako"/>



# <a id="managemediaservicesorigins"></a>如何管理串流端點到媒體服務帳戶

> [AZURE.SELECTOR]
- [Portal](media-services-manage-origins.md)
- [Java](https://github.com/southworkscom/azure-sdk-for-media-services-java-samples)



在 Microsoft Azure 媒體服務中，「串流端點」****代表可以直接將內容傳遞給用戶端播放程式應用程式，或傳遞給內容傳遞網路 (CDN) 進行進一步發佈的串流服務。 媒體服務也提供順暢的 Azure CDN 整合。 來自 StreamingEndpoint 服務的輸出資料流可以是即時資料流，也可以是媒體服務帳戶中的隨選視訊資產。

此外，您可以藉由調整縮放單位 (也稱為串流單位)，控制串流端點服務處理成長頻寬需求的產能。 建議您為生產環境中的應用程式配置一個或多個縮放單位。 縮放單位提供您可以以 200 Mbps 和其他功能的遞增量購買哪些功能，其中包含兩個專用的流出容量: [動態封裝](media-services-dynamic-packaging-overview.md), ，CDN 整合，以及進階的組態。

請注意，只有 StreamingEndpoint 處於執行中狀態時，才會向您收取費用。

本主題簡介串流端點提供的主要功能。 本主題也會示範如何使用 Azure 傳統入口網站來管理串流端點。


## 新增及刪除串流端點

您可以利用 .NET SDK、 REST API 或 Azure 傳統入口網站來新增或移除串流端點。

若要使用 Azure 傳統入口網站來新增或移除串流端點，以執行下列作業：

1. 在 [Azure 傳統入口網站](https://manage.windowsazure.com/), ，按一下 [ **媒體服務**。 接著，按一下媒體服務的名稱。
2. 選取 [串流端點]**** 頁面。
3. 按一下頁面底部的 [新增] 或 [刪除] 按鈕。 請注意，預設串流端點不可刪除。
4. 按一下 [啟動] 按鈕以啟動串流端點。
5. 按一下串流端點的名稱加以設定。

![串流端點頁面][streaming-endpoint]


根據預設，您可以建立兩個串流端點。 如果您需要更多，請參閱 [配額和限制](media-services-quotas-and-limitations.md)。

## <a id="scale_streaming_endpoints"></a>調整串流端點

串流單位提供您可以以 200 Mbps 為增量購買的專用流出容量和目前包含的其他功能 [動態封裝功能](media-services-dynamic-packaging-overview.md)。 根據預設，串流被設定哪些伺服器資源 (例如，運算、 流出容量等) 的共用執行個體模型 與所有其他使用者共用。 若要改善串流的輸送量，建議購買串流單位。

您可以利用 .NET SDK、REST API 或 Azure 傳統入口網站進行調整。

若要利用入口網站來變更串流單位數目，請執行下列動作：

1. 若要指定串流單元數目，請選取 [調整] 索引標籤，然後移動 [reserved capacity]**** 滑動軸。

    ![Scale page](./media/media-services-manage-origins/media-services-origin-scale.png)

4. 按 [儲存] 以儲存您的變更。

    任何新的串流單位配置需要大約 20 分鐘的時間才能完成。

    >[AZURE.NOTE] 目前，串流單位從任何正數值到無，都可能停用隨選串流長達一小時。

    >[AZURE.NOTE] 計算成本時會使用 24 小時內指定的最大單元數。 如需定價詳細資料的相關資訊，請參閱 [媒體服務定價詳細資料](http://go.microsoft.com/fwlink/?LinkId=275107)。

## <a id="configure_streaming_endpoints"></a>設定串流端點

當您至少擁有一個調整單位時，串流端點可讓您設定下列屬性：

- 存取控制
- 自訂主機名稱
- 快取控制
- 跨站台存取原則

如需這些屬性的詳細資訊，請參閱 [StreamingEndpoint](https://msdn.microsoft.com/library/azure/dn783468.aspx)。

您可以利用 .NET SDK、REST API 或 Azure 傳統入口網站設定這些屬性。

若要利用入口網站來變更串流單位數目，請執行下列動作：

1. 選取您想要設定的串流端點。
1. 選取 [設定] 索引標籤。

隨時顯示簡要的欄位說明。

![Configure origin][configure-origin]


1. 設定會在 HTTP 回應的 cache control 標頭中指定的快取期間上限。 此值將不會覆寫在 Blob 內容上明確設定的快取值上限。

2. 指定能夠連接到已發佈之串流端點的 IP 位址。 若未指定 IP 位址，則任何 IP 位址都可連接。

3. 為 Akamai 簽章標頭驗證指定組態。

4. 您可以指定 Adobe Flash 用戶端的跨網域存取原則 (如需詳細資訊，請參閱 [跨網域原則檔案規格](http://www.adobe.com/devnet/articles/crossdomain_policy_file_spec.html)。 以及 Microsoft Silverlight 用戶端的用戶端存取原則 (如需詳細資訊，請參閱 [讓服務可跨網域界限](https://msdn.microsoft.com/library/cc197955(v=vs.95).aspx)。

5. 您也可以按一下 [設定]**** 按鈕，設定自訂主機名稱。 如需詳細資訊，請參閱 **CustomHostNames** 屬性 [StreamingEndpont](https://msdn.microsoft.com/library/dn783468.aspx) 主題。


## <a id="enable_cdn"></a>啟用 Azure CDN 整合

您可以為串流端點指定 Azure CDN 整合 (預設為停用)。

若要將 Azure CDN 整合設定成 true：

- 串流端點必須至少是一個串流 (調整) 單位。 如果以後想要將調整單位設定為 0，請先停用 CDN 整合。 根據預設，當您建立新的串流端點時也會自動設定一個串流單位。

- 串流端點必須處於停止狀態。 啟用 CDN 之後，您可以啟動串流端點。

您可能需要等候 90 分鐘的時間，才能啟用 Azure CDN 整合。 它需要兩個小時才能讓變更跨所有 CDN POP 生效。


在所有 Azure 資料中心啟用 CDN 整合：美國西部、美國東部、北歐、西歐、日本西部、日本東部、東南亞和東亞。

一旦啟用後，就會停用下列組態：[自訂主機名稱]**** 和 [存取控制]****。

![串流端點啟用 CDN][streaming-endpoint-enable-cdn]


### 其他考量

- 對串流端點啟用 CDN 時，用戶端無法直接從來源要求內容。 如果您需要能力以測試您的內容是否具有 CDN，您可以建立另一個未啟用 CDN 的串流端點。
- 您的串流端點主機名稱在啟用 CDN 之後維持不變。 您在啟用 CDN 之後不需要對您的媒體服務工作流程進行任何變更。 例如，如果您的串流端點主機名稱是 strasbourg.streaming.mediaservices.windows.net，則在啟用 CDN 之後，會使用完全相同的主機名稱。
- 對於新的串流端點，您可以藉由建立新的端點來啟用 CDN；對於現有的串流端點，您必須先停止端點，然後再啟用 CDN。


如需詳細資訊，請參閱 [宣佈 Azure 媒體服務整合 Azure CDN (內容傳遞網路) 與](http://azure.microsoft.com/blog/2015/03/17/announcing-azure-media-services-integration-with-azure-cdn-content-delivery-network/)。


## 媒體服務學習路徑

[AZURE.INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

## 提供意見反應

[AZURE.INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]


[streaming-endpoint-enable-cdn]: ./media/media-services-manage-origins/media-services-origins-enable-cdn.png 
[streaming-endpoint]: ./media/media-services-manage-origins/media-services-origins-page.png 
[configure-origin]: ./media/media-services-manage-origins/media-services-origins-configure.png 
[configure-origin-configure-custom-host-names]: ./media/media-services-manage-origins/media-services-configure-custom-host-names.png 

