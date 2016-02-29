<properties
    pageTitle=" 開始使用 Azure 傳統入口網站來傳遞點播內容 | Microsoft Azure"
    description="本教學課程將逐步引導您使用 Azure 傳統入口網站，藉由 Azure 媒體服務來實作點播視訊 (VoD) 內容傳遞應用程式。"
    services="media-services"
    documentationCenter=""
    authors="Juliako"
    manager="dwrede"
    editor=""/>

<tags
    ms.service="media-services"
    ms.workload="media"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="get-started-article"
    ms.date="10/05/2015"
    ms.author="juliako"/>


# 開始使用 Azure 傳統入口網站來傳遞點播內容


[AZURE.INCLUDE [media-services-selector-get-started](../../includes/media-services-selector-get-started.md)]


本教學課程將逐步引導您透過 Azure 傳統入口網站實作基本的點播視訊 (VoD) 內容傳遞應用程式。

> [AZURE.NOTE] 若要完成本教學課程，您需要 Azure 帳戶。 如果您沒有帳戶，只需要幾分鐘的時間就可以建立免費試用帳戶。 如需詳細資料，請參閱 <a href="http://www.windowsazure.com/pricing/free-trial/?WT.mc_id=A8A8397B5" target="_blank">Azure 免費試用</a>。

本教學課程內容包括以下工作：

1.  建立 Azure 媒體服務帳戶。
2.  設定串流端點。
1.  上傳視訊檔案。
1.  將來源檔案編碼為一組調適性位元速率 MP4 檔案。
1.  發佈資產並取得串流和漸進式下載 URL。  
1.  播放您的內容。


## 建立 Azure 媒體服務帳戶

1. 在 [Azure 傳統入口網站](https://manage.windowsazure.com/), ，按一下 [ **新增**, ，按一下 [ **媒體服務**, ，然後按一下 [ **快速建立**。

    ![Media Services Quick Create](./media/media-services-portal-get-started/wams-QuickCreate.png)

2. 在 **名稱**, ，輸入新帳戶的名稱。 媒體服務帳戶名稱為全部小寫且不含空格的數字或字母，且長度是 3 到 24 個字元。

3. 在 **區域**, ，選取將用來儲存您的媒體服務帳戶之中繼資料記錄的地理區域。 只有可用的媒體服務區域才會出現在下拉式清單方塊中。

4. 在 **儲存體帳戶**, ，選取儲存體帳戶提供媒體內容從媒體服務帳戶的 blob 儲存體。 您可以選取與媒體服務帳戶相同地理區域中的現有儲存體帳戶，也可以建立新的儲存體帳戶。 新的儲存體帳戶會建立於相同的區域中。

5. 如果您在建立新的儲存體帳戶， **新的儲存體帳戶名稱**, ，輸入儲存體帳戶的名稱。 儲存體帳戶名稱的規則會與媒體服務帳戶相同。

6. 按一下 [ **快速建立** 表單底部。

    您可以在視窗底端的訊息區域監視程序的狀態。

    成功建立帳戶之後，狀態會變更為作用中。

    在頁面底部 **管理金鑰** ] 按鈕會出現。 當您按一下此按鈕時，會顯示對話方塊，其中具有媒體服務帳戶名稱和主要和次要金鑰。 您將需要帳戶名稱和主要金鑰資訊，以便以程式設計方式存取媒體服務帳戶。

    ![Media Services Page](./media/media-services-portal-get-started/wams-mediaservices-page.png)

    當您按兩下帳戶名稱時，預設會顯示 [快速入門] 頁面。 此頁面可讓您執行一些在入口網站的其他頁面也可以執行的管理工作。 例如，您可以從此頁面上傳視訊檔案，也可以從 [內容] 頁面上傳視訊檔案。


## 使用入口網站設定串流端點

使用 Azure 媒體服務時，其中一個最常見案例是提供調適性位元速率串流給您的用戶端。 使用調適性位元速率串流，用戶端可以在視訊顯示時，根據目前網路頻寬、CPU 使用率以及其他因素，切換成較高或較低的位元速率串流。 媒體服務支援下列調適性位元速率串流技術：HTTP 即時串流 (HLS)、Smooth Streaming、MPEG DASH 和 HDS (僅適用於 Adobe PrimeTime/Access 授權)。

媒體服務提供動態封裝，這讓您以媒體服務支援的串流格式 (MPEG DASH, 、HLS、Smooth Streaming、HDS) 提供調適性位元速率 MP4 或 Smooth Streaming 編碼內容，而不必重新封裝成這些串流格式。

若要利用動態封裝，您需要執行下列動作：

- 將您的夾層 (來源) 檔編碼為一組調適性位元速率 MP4 檔案或調適性位元速率 Smooth Streaming 檔案 (編碼步驟稍後示範於本教學課程中)。  
- 取得至少一個串流單元 *串流端點* 從中您打算傳遞內容。

使用動態封裝，您只需要以單一儲存格式儲存及播放檔案，媒體服務會根據來自用戶端的要求建置及傳遞適當的回應。

若要變更串流保留單元數目，請執行下列動作：

1. 在 [Azure 傳統入口網站](https://manage.windowsazure.com/), ，按一下 [ **媒體服務**。 接著，按一下媒體服務的名稱。

2. 選取 [串流端點] 頁面。 然後，按一下您要修改的串流端點。

3. 若要指定串流單元數目，請選取 **延展** 索引標籤，然後移動 **保留容量** 滑桿。

    ![Scale page](./media/media-services-portal-get-started/media-services-origin-scale.png)

4. 按一下 [ **儲存** ] 按鈕以儲存您的變更。

    配置任何新的單元需要大約 20 分鐘的時間才能完成。

    >[AZURE.NOTE] 目前，從任何正值的串流處理單元為 none，可以停用串流長達一小時。
    >
    > 計算成本時會使用 24 小時內指定的最大單元數。 如需定價詳細資料的相關資訊，請參閱 [媒體服務定價詳細資料](http://go.microsoft.com/fwlink/?LinkId=275107)。

## 上傳內容


1. 在 [Azure 傳統入口網站](http://go.microsoft.com/fwlink/?LinkID=256666&clcid=0x409), ，按一下 [ **媒體服務** ，然後按一下媒體服務帳戶名稱。
2. 選取 [內容] 頁面。
3. 按一下 [ **上載** 頁面上，或在入口網站底部的按鈕。
4. 在 **上傳內容** ] 對話方塊中，瀏覽至想要的資產檔案。 按一下檔案，然後按一下 [ **開啟** 或按下 Enter。

    ![] UploadContentDialog[] uploadcontent

5. 在 **上傳內容** 對話方塊方塊中，按一下核取按鈕以接受 **檔案** 和 **內容名稱**。
6. 上傳隨即開始，而您可以在入口網站底部追蹤進度。  

    ![] JobStatus[狀態]

上傳完成之後，您將會看到新資產列在 [內容] 清單中。 依照慣例的名稱包含"**-來源**」 附加在結尾，以協助追蹤新的內容作為編碼工作的來源內容。

![] ContentPage[] contentpage

如果檔案大小值未不會更新上傳程序停止之後，選取 **同步處理中繼資料** ] 按鈕。 這樣會以儲存體中的實際檔案大小同步處理資產檔案大小，並重新整理 [內容] 頁面上的值。


## 編碼內容

### 概觀
若要透過網際網路傳遞數位視訊，您必須壓縮媒體。 媒體服務提供媒體編碼程式，可讓您指定要如何為您的內容編碼 (例如，要使用的轉碼器、檔案格式、解析度及位元速率)。

使用 Azure 媒體服務時，其中一個最常見案例是提供調適性位元速率串流給您的用戶端。 使用調適性位元速率串流，用戶端可以在視訊顯示時，根據目前網路頻寬、CPU 使用率以及其他因素，切換成較高或較低的位元速率串流。 媒體服務支援下列調適性位元速率串流技術：HTTP 即時串流 (HLS)、Smooth Streaming、MPEG DASH 和 HDS (僅適用於 Adobe PrimeTime/Access 授權)。

媒體服務提供動態封裝，這讓您以媒體服務支援的串流格式 (MPEG DASH, 、HLS、Smooth Streaming 或 HDS) 提供調適性位元速率 MP4 或 Smooth Streaming 編碼內容，而不必重新封裝成這些串流格式。

若要利用動態封裝，您需要執行下列動作：

- 將您的夾層 (來源) 檔編碼為一組調適性位元速率 MP4 檔案或調適性位元速率 Smooth Streaming 檔案 (編碼步驟稍後示範於本教學課程中)。
- 為您計畫從該處傳遞內容的串流端點取得至少一個隨選串流單元。 如需詳細資訊，請參閱 [如何調整隨選串流保留單元](media-services-manage-origins.md#scale_streaming_endpoints/)。

使用動態封裝，您只需要以單一儲存格式儲存及播放檔案，媒體服務會根據來自用戶端的要求建置及傳遞適當的回應。

請注意，除了能夠使用動態封裝功能，隨選串流保留單元提供您專用傳出功能，此功能可以以 200 Mbps 為增量來購買。 依預設，隨選串流是以共用執行個體模型來設定，可與其他所有使用者共用伺服器資源 (例如，運算或流出容量)。 若要改進隨選串流輸送量，建議您購買隨選串流保留單元。

### 編碼

本章說明當您要使用 Azure 傳統入口網站來透過 Azure 媒體編碼器為您的內容編碼時，可以採取的步驟。

1.  選取您想要編碼的檔案。
    如果此檔案類型，支援編碼 **程序** 底部的 [內容] 頁面將會啟用按鈕。
4. 在 **程序** 對話方塊中，選取 * * Azure 媒體編碼器 * * 處理器。
5. 選擇其中一個 **編碼組態**。

    ![] Process2[] process2

     [Azure Media Encoder 的工作預設字串](https://msdn.microsoft.com/library/azure/dn619392.aspx) 主題說明何謂各 **調適性串流 (動態封裝)**, ，**漸進式下載預設**, ，**調適性串流的傳統 Prsests**  類別表示。  

     **其他** 後續所述的設定。

    + **使用 PlayReady 內容保護進行編碼**。 此預設格式會產生以 PlayReady 內容保護編碼的資產。  

        依預設會使用媒體服務 PlayReady 授權服務。 若要指定其他可讓用戶端從中取得授權以播放 PlayReady 加密內容的服務，請使用 REST 或媒體服務 .NET SDK API。 如需詳細資訊，請參閱 [使用靜態加密來保護您的內容]() ，並設定 **licenseAcquisitionUrl** Media Encryptor 預設中的屬性。 或者，您可以使用動態加密，然後設定 **PlayReadyLicenseAcquisitionUrl** 屬性中所述 [使用 PlayReady 動態加密和授權傳遞服務](http://go.microsoft.com/fwlink/?LinkId=507720 )。
    + **在 (透過 Flash/Silverlight) 的 PC/Mac 上播放**。 此預設會產生具有下列特性的 Smooth Streaming 資產：使用 AAC 編碼的 44.1 kHz 16 位元/樣本立體音訊 CBR (96 kbps)，以及使用 H.264 主要設定檔編碼的 720p 視訊 CBR (6 位元速率，範圍從 3400 kbps 到 400 kbps)，與兩秒 GOP。
    + **透過 HTML5 播放 (IE/Chrome/Safari)**。 此預設會產生具有下列特性的單一 MP4 檔案：使用 AAC 編碼的 44.1 kHz 16 位元/樣本立體音訊 CBR (128 kbps)，以及使用 H.264 主要設定檔編碼的 720p 視訊 CBR (4500 kbps)。
    + **在 iOS 裝置和 PC/Mac 上的播放**。 此預設格式會產生特性與 Smooth Streaming 資產 (如先前所述) 相同、但是其格式可以用來將 Apple HLS 資料流傳遞至 iOS 裝置的資產。

5. 接著，輸入想要的好記輸出內容名稱或接受預設值。 然後，按一下核取按鈕以啟動編碼作業，而您可以在入口網站底部追蹤進度。
6. 選取 **確定**。

    編碼完成之後，[內容] 頁面會包含編碼的檔案。

    若要檢視編碼工作的進度，請切換至 **工作** 頁面。  

    如果編碼完成之後不會不更新檔案大小值，選取 **同步處理中繼資料** ] 按鈕。 這樣會以儲存體中的實際檔案大小同步處理輸出資產檔案大小，並重新整理 [內容] 頁面上的值。


## 發佈內容

### 概觀

若要提供使用者可用來串流或下載內容的 url，您需要藉由建立定位器來 「 發行 」 您的資產。 定位器提供對於資產中包含之檔案的存取。 媒體服務支援兩種類型的定位器：OnDemandOrigin 定位器，用於串流媒體 (例如，MPEG DASH、HLS 或 Smooth Streaming) 和存取簽章 (SAS) 定位器，用來下載媒體檔案。

當您使用 Azure 傳統入口網站發佈您的資產時，系統會為您建立定位器，並提供 OnDemand 式的 URL (如果您的資產包含 .ism 檔案) 或 SAS URL。

SAS URL 具有下列格式。

    {blob container name}/{asset name}/{file name}/{SAS signature}

資料流 URL 的格式如下，還可以用它來播放 Smooth Streaming 資產。

    {streaming endpoint name-media services account name}.streaming.mediaservices.windows.net/{locator ID}/{filename}.ism/Manifest

若要建置 HLS 串流 URL，請將 (格式=m3u8-aapl) 附加至 URL。

    {streaming endpoint name-media services account name}.streaming.mediaservices.windows.net/{locator ID}/{filename}.ism/Manifest(format=m3u8-aapl)

若要建立 MPEG DASH 串流 URL，將附加 (格式 = mpd-時間-csf) 的 url。

    {streaming endpoint name-media services account name}.streaming.mediaservices.windows.net/{locator ID}/{filename}.ism/Manifest(format=mpd-time-csf)


定位器有到期日。 使用入口網站來發佈您的資產時，建立的定位器會在 100 年後失效。

>[AZURE.NOTE] 如果您使用入口網站建立的定位器 2015 年 3 月之前，會建立定位器 2 年到期日。  

若要更新定位器的到期日，請使用 [REST](http://msdn.microsoft.com/library/azure/hh974308.aspx#update_a_locator ) 或 [.NET](http://go.microsoft.com/fwlink/?LinkID=533259) Api。 請注意，當您更新 SAS 定位器的到期日，URL 也會隨之變更。

### Publish

若要使用入口網站發佈資產，請執行下列作業：

1. 選取資產。
2. 然後，按一下 [發佈] 按鈕。

 ![] PublishedContent[] publishedcontent


## 從入口網站播放內容

Azure 傳統入口網站提供內容播放程式，可讓您用來測試視訊。

按一下想要的視訊，然後按一下 **播放** 入口網站底部的按鈕。

適用一些考量事項：

- 確定已發佈視訊。
-  **媒體服務內容播放程式** 會從預設串流端點播放。 如果您想要從非預設串流端點播放，請使用其他播放程式。 例如， [Azure 媒體服務播放器](http://amsplayer.azurewebsites.net/azuremediaplayer.html)。


![] AMSPlayer[] AMSPlayer



##後續步驟：媒體服務學習路徑

[AZURE.INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

##提供意見反應

[AZURE.INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]

## 尋找其他內容嗎？

如果本主題未包含您預期的內容、缺少部分內容，或者提供了一些其他不符合您需求的方式，請在下方提供您使用 Disqus 執行緒的意見反應給我們。

### 其他資源
- <a href="http://channel9.msdn.com/Shows/Azure-Friday/Azure-Media-Services-101-Get-your-video-online-now-">Azure 媒體服務 101 - 立即在線上取得您的影片！</a>
- <a href="http://channel9.msdn.com/Shows/Azure-Friday/Azure-Media-Services-102-Dynamic-Packaging-and-Mobile-Devices">Azure 媒體服務 102 - 動態封裝和行動裝置</a>


<!-- Anchors. -->


<!-- URLs. -->
[Azure Classic Portal]: http://manage.windowsazure.com/


<!-- Images -->
[portaloverview]: ./media/media-services-portal-get-started/media-services-content-page.png
[publishedcontent]: ./media/media-services-portal-get-started/media-services-upload-content-published.png
[uploadcontent]: ./media/media-services-portal-get-started/UploadContent.png
[status]: ./media/media-services-portal-get-started/Status.png
[encoder]: ./media/media-services-manage-content/EncoderDialog2.png
[branding]: ./media/branding-reporting.png
[contentpage]: ./media/media-services-portal-get-started/media-services-content-page.png
[process]: ./media/media-services-manage-content/media-services-process-video.png
[process2]: ./media/media-services-portal-get-started/media-services-process-video2.png
[encrypt]: ./media/media-services-manage-content/media-services-encrypt-content.png
[AMSPlayer]: ./media/media-services-portal-get-started/media-services-portal-player.png

