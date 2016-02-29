<properties
    pageTitle="Azure 通知中樞 - 常見問題集 (FAQ)"
    description="在通知中樞上設計/實作解決方案的常見問題集"
    services="notification-hubs"
    documentationCenter="mobile"
    authors="wesmc7777"
    manager="dwrede"
    editor="" />

<tags
    ms.service="notification-hubs"
    ms.workload="mobile"
    ms.tgt_pltfrm="mobile-multiple"
    ms.devlang="multiple"
    ms.topic="article"
    ms.date="11/25/2015" 
    ms.author="wesmc" />

#Azure 通知中樞 - 常見問題集 (FAQ)

##一般
###1.   「通知中樞」的定價為何？
通知中樞 」 提供三種層次: *免費*, ，*基本* & *標準* 層。 這裡有更多詳細資料 [Notification Hubs Pricing]。 定價方式依訂用帳戶層級收費，而且是以推播次數為基礎，因此您有多少命名空間或通知中樞並不重要。
「免費」層次是針對開發用途所提供，而且不提供 SLA 保證。 「基本」與「標準」層次是針對生產用途提供，而且下列主要功能僅針對「標準」層次啟用：

- *豐富遙測* -基本 」 層次不允許匯出遙測或註冊資料。 如果您需要匯出遙測資料以供離線檢視及分析的功能，則您必須移至「標準」層次。
- *多租用戶* -如果您正在建立行動應用程式使用通知中心以支援多個租用戶，則您必須考慮移至 「 標準層次。 這樣可讓您針對應用程式在「通知中樞」命名空間層級設定推播通知服務 (PNS) 認證，而且您可以接著將租用戶隔離，在此通用命名空間下為其提供個別中樞。 這樣可以簡化維護程序，同時保留 SAS 金鑰以從針對每個租用戶隔離的通知中樞傳送及接收通知，確保不會發生跨租用戶重疊的情況。
- *排定推送* -您可以排程排入佇列並傳送推播通知。
- *大量匯入* -您可以匯入大量的註冊。

###2.   什麼是 SLA？
對於「基本」與「標準」通知中樞層次，我們保證已正確設定的應用程式至少有 99.9% 的時間可以傳送通知或執行註冊管理作業 (對於部署在「基本」或「標準」通知中樞層次內的通知中樞)。 若要深入了解我們的 SLA，請瀏覽這裡的 SLA 頁面 [Notification Hubs SLA]。 請注意，沒有任何 SLA 保證「平台通知服務」與裝置之間不會發生延遲，因為「通知中樞」依賴外部平台提供者為裝置提供通知。

###3.   客戶如何使用通知中樞？
我們有一些使用「通知中樞」的客戶，其中比較著名的有：

* 索契 2014 – 100 個以上的興趣群組、3 百萬部以上的裝置、1.5 億則以上的通知 (在 2 週內傳送)。 [CaseStudy - Sochi]
* -Skanska [CaseStudy - Skanska]
* Seattle-Times [CaseStudy - Seattle Times]
* -Mural.ly [CaseStudy - Mural.ly]
* -7Digital [CaseStudy - 7Digital]
* Bing 應用程式 – 1 千萬部以上的裝置、每日傳送 3 百萬則通知

###4. 如何升級或降級我的通知中樞來變更服務層級？
移至 [Azure Classic Portal], 、 按一下服務匯流排，然後按一下您的命名空間上您的通知中心。 在 [調整] 索引標籤下，您可以變更通知中樞服務層級。

##設計與開發
###1.   支援哪些服務端平台？
我們提供 .NET、Java、PHP、Python、Node.js 的 SDK 與範例，因此您可以使用上述任一平台設定應用程式後端以便與「通知中樞」通訊。 通知中樞 API 是以 REST 介面為基礎，因此您可以選擇直接與其通訊。 這裡有更多詳細資料 [NH - REST APIs]

###2.   支援哪些裝置平台？
我們支援傳送通知到 Apple iOS、Android、Windows Universal 與 Windows Phone、Kindle、Android China (由 Baidu 開發)、Xamarin (iOS 與 Android)、Chrome Apps 平台。 針對可從這些平台-逐步快速入門教學課程 [NH - Getting Started Tutorials]

###3.   是否支援簡訊/電子郵件/Web 通知？
「通知中樞」主要設計來傳送通知給使用上述平台的行動裝置應用程式。 我們不提供傳送電子郵件或簡訊的功能，但是您可以將提供這些功能的協力廠商平台與「通知中樞」整合，以使用「Azure 行動服務」來傳送原生推播通知。 我們也不提供現成可用的瀏覽器中推播通知。 客戶可以選擇使用 SignalR 來實作此功能。 我們也提供教學課程，說明如何將推播通知傳送到可在 Google Chrome 瀏覽器上運作的 Chrome 應用程式。 請參閱 [Chrome Apps tutorial]

###4.   「Azure 行動服務」與「Azure 通知中樞」之間的關係為何以及何時使用？
如果您有現有的行動裝置應用程式後端，而且您只想要新增傳送推播通知的功能，則您必須使用「Azure 通知中樞」。 如果您想要從頭開始設定您的行動裝置應用程式後端，則應該考慮使用「Azure 行動服務」。 「Azure 行動服務」會自動為您佈建「通知中樞」，讓您能夠輕鬆地從行動裝置應用程式後端傳送推播通知。 「Azure 行動服務」的定價包括「通知中樞」的基本費率，因此您只需要在超過包含的推播數時才需要付費。 這裡有更多詳細資料 [Mobile Services Pricing]

###5.   可以支援多少裝置？
在「基本」與「標準」層次中，我們不會針對可接收通知的使用中裝置數目強制任何限制。 如需詳細資訊，請參閱: [Notification Hubs Pricing]

###6.   我可以傳送多少推播通知？
我們的客戶每天使用「Azure 通知中樞」傳送數百萬則推播通知。 您不需要為了調整「通知中樞」規模而執行任何額外動作。 我們會自動根據通知數目相應增加透過系統傳送的通知數目。 請注意，定價確實會受提供的推播通知數目影響。

###7.   通知到達我的裝置需要多久的時間？
在一般使用情節中，「Azure 通知中樞」能在 1 分鐘處理至少 1 百萬個傳送作業，能處理的連入負載數目大致上差不多，而且在本質上並不難處理。 此速率可能視標記數目與連入傳送作業本質而異。在此期間，我們能計算每個平台的目標數，並根據已註冊的標記/標記運算式將訊息路由傳送到對應的推播通知服務。 從這裡開始，傳送通知到裝置是推播通知服務 (PNS) 的責任。 PNS 不為傳送通知提供任何 SLA 保證，不過一般而言，絕大部分的訊息會在傳送到我們平台之後的數分鐘 (< 10 分鐘) 內傳送到裝置。 可能有少數異常情況會花比較多的時間。 「Azure 通知中樞」也有一個原則，可捨棄無法在 30 分鐘內傳送到 PNS 的任何通知。 發生此延遲的原因有數種，最常見的原因是 PNS 正在為您的應用程式進行節流處理。

###8.   是否有任何延遲保證？
因為推播通知是由外部平台特定「推播通知服務」所傳送的這個本質，我們不提供延遲保證。 一般而言，大部分的通知會在數分鐘內傳送到目標裝置。

###9.   設計具有命名空間與「通知中樞」的解決方案時，必須考量哪些事項？
*行動應用程式/環境:*
每個環境的每個行動裝置應用程式都應該有一個「通知中樞」。 在多組織用戶共享情節中，每個租用戶都應該有特別的中樞。
因為這可能導致問題傳送通知時，您永遠必須共用相同的通知中樞，測試與生產環境之間。 例如 Apple 提供 「 沙箱 」 與 「 實際執行推播端點有不同的認證。 如果一開始使用 Apple 沙箱憑證設定中樞，接著重新設定為使用 Apple 實際執行憑證，則舊的裝置權杖會成為無效且無法搭配新的憑證使用，並導致推播失敗。 建議您最好將實際執行與測試環境分開，並針對不同的環境使用不同的中樞。

*PNS 認證:*
當行動裝置應用程式向平台的開發人員入口網站 (例如 Apple 或 Google 等) 註冊時，您會取得應用程式識別碼與安全性權杖，應用程式後端必須將其提供給平台的「推播通知」服務，才能傳送推播通知給裝置。 您必須在「通知中樞」中設定這些安全性權杖，其形式可以是憑證 (例如，對於 Apple iOS 或 Windows Phone) 或安全性金鑰 (Google Android、Windows 等)。 此動作通常是在通知中樞層級完成，但在多組織用戶共享情節中，這也可以在命名空間層級完成。

*命名空間:*
命名空間也可以用於部署分組。  在多組織用戶共享情節中，它也可以用來代表相同應用程式之所有租用戶的所有通知中樞。

*地理分散:*
地理分散並不一定重大的推播通知的情況下。 請注意，最終會將推播通知傳送到裝置的各種推播通知服務 (例如 APNS 與 GCM 等) 也不會平均分散。 不過，如果您的應用程式是提供給全球各地的使用者使用，則您可以在不同的命名空間中建立數個中樞，以利用全球不同 Azure 區域中「通知中樞」服務的可用性優點。 請注意，這樣會增加管理成本 (特別是有關註冊方面)，因此建議您只有在確實需要時才這樣做。

###10.  應該從應用程式後端註冊或直接從裝置註冊？
當您必須在建立註冊之前執行用戶端驗證，或您有必須由應用程式後端根據某些應用程式邏輯建立或修改的標記時，從應用程式後端註冊非常實用。 詳細的指引，請參閱 [Backend Registration guidance] (& s) [Backend Registration guidance - 2]

###11.  資訊安全模型是什麼？
「Azure 通知中樞」使用共用存取簽章 (SAS) 型資訊安全模型。 您可以在根命名空間層級或更細微的通知中樞層級使用 SAS 權杖。 您可以使用不同的授權規則設定這些 SAS 權杖，例如傳送訊息權限與接聽通知權限等。這裡有更多詳細資料 [NH Security model]

###12.  如何處理通知中的機密承載？
通知是由平台推播通知服務 (PNS) 傳送給裝置。 當傳送者將通知傳送給「Azure 通知中樞」時，我們會接著處理通知並將它傳送給個別的 PNS。 傳送者與「Azure 通知中樞」之間的連線以及「Azure 通知中樞」與 PNS 之間的連線都使用 HTTPS。 「Azure 通知中樞」不會以任何方式記錄訊息的承載。
若要傳送機密承載，建議您使用「安全推播」模式，其中傳送者會傳送 'ping' 通知與訊息識別碼給裝置 (不含機密承載)，而當裝置上的應用程式收到此承載時，它會直接呼叫安全應用程式後端 API 以擷取訊息詳細資料。 這裡是實作模式的教學課程 [NH - Secure Push tutorial]

##作業
###1.   災害復原 (DR) 是什麼？
我們在我們這邊 (通知中樞名稱與連線字串等) 提供中繼資料災害復原。 萬一發生 DR，會遺失的其中一個項目是註冊資料，因此您必須有適當的解決方案能夠在新的中樞重新填入此資料。

- *步驟 1* 位在不同的 DC 中建立次要通知中樞。 您可以在發生 DR 事件時建立此通知中樞，或一開始就建立。 它並不是很重要，因為 NH 佈建是快速的程序，大約只需要數秒的時間。 一開始就建立通知中樞將也能避免 DR 事件影響我們的管理功能，因此建議您這樣做。

- *步驟 2* -只來自主要通知中樞註冊至次要通知中樞。 建議您不要嘗試在兩個中樞上維護註冊，而應該在收到註冊時進行同步，因為註冊會由 PNS 判定為到期的固有本質，第一種方式無法正確運作。 當我們收到有關已到期或無效註冊的 PNS 回應時，「通知中樞」會將它們清除。  

建議您使用會執行下列任一動作的應用程式後端：

- 在本身維護該組註冊，以便它可以在發生 DR 時執行大量插入作業以將資料插入到次要通知中樞，或

- 定期從主要中樞傾印註冊做為備份，然後執行大量插入作業以將資料插入到次要 NH。

(適用於 Standard 階層的註冊匯出/匯入功能如下所述: [Registrations Export/Import])

若您沒有後端，則當應用程式在裝置上啟動時，它們將會在次要 NH 中執行新註冊，而最後次要 NH 將會有所有已註冊的使用中裝置，但缺點是裝置上的應用程式未開啟期間將不會收到通知。

###2.   是否有任何稽核記錄功能？
所有通知中樞管理作業都移至作業記錄會公開這些 [Azure Classic Portal]。

##監視和疑難排解
###1.   可用的疑難排解功能有哪些？
「Azure 通知中樞」提供數個功能，可進行常見的疑難排解，特別是在已捨棄通知的最常見情節中。 請參閱下列疑難排解白皮書-在詳細資料 [NH - troubleshooting]

###2.   可用的遙測功能有哪些？
Azure 通知中心可讓檢視中的遙測資料 [Azure Classic Portal]。 可從可用的度量的詳細資料- [NH - Metrics]。
請注意，成功的通知只表示通知已傳送到外部推播通知服務 (例如 Apple 的 APNS 與 Google 的 GCM 等)，接著由 PNS 負責將通知傳送給裝置，但 PNS 並未將那些度量公開給我們。  
它也提供以程式設計方式匯出遙測的功能 (在「標準」層次中)。 請參閱此範例的詳細資料- [NH - Metrics sample]

[Azure Classic Portal]: https://manage.windowsazure.com
[Notification Hubs Pricing]: http://azure.microsoft.com/pricing/details/notification-hubs/
[Notification Hubs SLA]: http://azure.microsoft.com/support/legal/sla/
[CaseStudy - Sochi]: https://customers.microsoft.com/Pages/CustomerStory.aspx?recid=7942
[CaseStudy - Skanska]: https://customers.microsoft.com/Pages/CustomerStory.aspx?recid=5847
[CaseStudy - Seattle Times]: https://customers.microsoft.com/Pages/CustomerStory.aspx?recid=8354
[CaseStudy - Mural.ly]: https://customers.microsoft.com/Pages/CustomerStory.aspx?recid=11592
[CaseStudy - 7Digital]: https://customers.microsoft.com/Pages/CustomerStory.aspx?recid=3684
[NH - REST APIs]: https://msdn.microsoft.com/library/azure/dn530746.aspx
[NH - Getting Started Tutorials]: http://azure.microsoft.com/documentation/articles/notification-hubs-ios-get-started/
[Chrome Apps tutorial]: http://azure.microsoft.com/documentation/articles/notification-hubs-chrome-get-started/
[Mobile Services Pricing]: http://azure.microsoft.com/pricing/details/mobile-services/
[Backend Registration guidance]: https://msdn.microsoft.com/library/azure/dn743807.aspx
[Backend Registration guidance - 2]: https://msdn.microsoft.com/library/azure/dn530747.aspx
[NH Security model]: https://msdn.microsoft.com/library/azure/dn495373.aspx
[NH - Secure Push tutorial]: http://azure.microsoft.com/documentation/articles/notification-hubs-aspnet-backend-ios-secure-push/
[NH - troubleshooting]: http://azure.microsoft.com/documentation/articles/notification-hubs-diagnosing/
[NH - Metrics]: https://msdn.microsoft.com/library/dn458822.aspx
[NH - Metrics sample]: https://github.com/Azure/azure-notificationhubs-samples/tree/master/FetchNHTelemetryInExcel
[Registrations Export/Import]: https://msdn.microsoft.com/library/dn790624.aspx

