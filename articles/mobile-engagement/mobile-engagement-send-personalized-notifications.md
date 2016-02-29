<properties 
    pageTitle="使用 Azure Mobile Engagement 傳送個人化通知" 
    description="如何藉由在通知中包含像是其名稱的使用者設定檔資訊，傳送個人化通知"        
    services="mobile-engagement" 
    documentationCenter="mobile" 
    authors="piyushjo" 
    manager="dwrede" 
    editor="" />

<tags 
    ms.service="mobile-engagement" 
    ms.workload="mobile" 
    ms.tgt_pltfrm="all" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="12/07/2015" 
    ms.author="piyushjo" />

#包括使用者名稱來個人化通知

在您讓通知對應用程式使用者更有吸引力的過程中，您應該考慮個人化。 其中一個強力的方法包含選擇性地使用應用程式使用者名稱做為通知的地址，使其更加個人化。 請注意這裡，您在將使用者名稱新增至通知時應該小心謹慎，因為如果過度使用這項策略，反而可能會讓部分應用程式使用者感到不悅。 您也應該確保您會讓使用者在完全同意的情況下，選擇在行動應用程式中提供這些個人詳細資料，再開始使用這個方法。 

技術上來說，您可以使用 Azure Mobile engagement 完成個人化通知，方法是依照下列步驟，我們將會使用在通知中包含使用者名稱的案例。 您會使用應用程式資訊或標記的概念，其值可以由 SDK 傳遞，該 SDK 是在行動應用程式中或透過 API 整合。 然後可以使用這些應用程式資訊或標記：

1. 根據應用程式資訊的值將通知的目標設為特定使用者，或 
2. 做為通知中的預留位置，在將通知傳送給該裝置時，會以裝置/使用者的特定值取代。 

> [AZURE.IMPORTANT] 請注意，傳送通知的速度將會看到減少因為這項額外處理的應用程式資訊的值取代每個通知。 

##在 Mobile Engagement 入口網站註冊應用程式資訊

1) 您開始在 Azure 入口網站中註冊應用程式資訊或標籤。 移至 **設定** ]-> [ **Tag (App-info)** 這個。  

![][1]  

2) 按一下 [ **新標記 (應用程式資訊)** 並提供名稱為 *user_name* 和做為型別 *字串* 按一下 **提交**。 

![][2]

3) 最後，您會看到類似下列的登錄此應用程式資訊:

![][3]

##從用戶端 SDK 傳送應用程式資訊

我們在這裡使用 Windows Universal 應用程式範例，但是對於其他的 SDK 也有對等方法存在。 

假設您在行動應用程式中有一種方法，在驗證使用者之後從使用者取得像是其名稱的設定檔資訊，您會在這裡呼叫 `SendAppInfo` 方法並且填入 `user_name` 應用程式資訊的值，您稍早註冊至 Mobile Engagement 服務後端。 

    Dictionary<object, object> appInfo = new Dictionary<object, object>();
    appInfo.Add("user_name", str);
    EngagementAgent.Instance.SendAppInfo(appInfo); 

##傳送個人化通知

現在您已經全部設定為傳送通知使用此 **user_name**。 

1) 移至 Mobile Engagement 入口網站上 **到達** 索引標籤來建立通知，您可以使用此預留位置通知標題或本文中的任何位置以下列格式。 

![][4]  

> [AZURE.NOTE] 任何使用者的未設定 user_name 應用程式資訊，不會收到任何通知。 

2) 當 Mobile Engagement 會選取裝置來傳送此通知，則它會查看此應用程式資訊，並取代預留位置的值。  
例如，如果我們將 `str = "Scott"` 使用者的裝置註冊會收到相關聯的應用程式資訊 **user_name = SCOTT** 針對這位使用者，而且此使用者會看到的應用程式的推播通知，以下列格式。 

![][5]  

<!-- Images. -->
[1]: ./media/mobile-engagement-send-personalized-notifications/app-info.png
[2]: ./media/mobile-engagement-send-personalized-notifications/create-app-info.png
[3]: ./media/mobile-engagement-send-personalized-notifications/app-info-user-name.png
[4]: ./media/mobile-engagement-send-personalized-notifications/personal-notification.png
[5]: ./media/mobile-engagement-send-personalized-notifications/notification.png


