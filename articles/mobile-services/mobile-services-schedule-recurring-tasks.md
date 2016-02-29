<properties
    pageTitle="在 JavaScript 後端行動服務的後端工作排程 | Microsoft Azure"
    description="使用 Azure 行動服務排程器來定義依排程執行的 JavaScript 後端作業。"
    services="mobile-services"
    documentationCenter=""
    authors="ggailey777"
    manager="dwrede"
    editor=""/>

<tags
    ms.service="mobile-services"
    ms.workload="mobile"
    ms.tgt_pltfrm="mobile-multiple"
    ms.devlang="multiple"
    ms.topic="article"
    ms.date="12/07/2015"
    ms.author="glenga"/>

# 在行動服務中為週期性工作排程

[AZURE.INCLUDE [mobile-service-note-mobile-apps](../../includes/mobile-services-note-mobile-apps.md)]

&nbsp;


> [AZURE.SELECTOR]
- [.NET 後端](mobile-services-dotnet-backend-schedule-recurring-tasks.md)
- [Javascript 後端](mobile-services-schedule-recurring-tasks.md)

本主題將示範如何在 Azure 傳統入口網站中使用作業排程器功能，以根據您所定義的排程來定義要執行的伺服器指令碼。 在此情況下，指令碼會定期向遠端服務 (在此案例中為 Twitter) 查詢，並將結果儲存在新資料表中。 可排定的其他一些定期工作包括：

+ 封存老舊或重複的資料記錄。
+ 要求或儲存外部資料，例如推文、RSS 項目和位置資訊。
+ 處理或調整儲存影像的大小。

本教學課程將為您示範如何使用工作排程器，來建立向 Twitter 要求推文資料並在新的 Updates 資料表中儲存推文的排定工作。

##<a name="get-oauth-credentials"></a>註冊以取得 Twitter v1.1 API 的存取權與儲存認證

[AZURE.INCLUDE [mobile-services-register-twitter-access](../../includes/mobile-services-register-twitter-access.md)]

##<a name="create-table"></a>建立新的 Updates 資料表

接下來，您必須建立要儲存推文的新資料表。

2. 在 [Azure 傳統入口網站]，按一下 [ **資料** 您的行動服務] 索引標籤，然後按一下 [ **+ 建立**。

3. 在 **資料表名稱** 類型 _更新_, ，然後按一下核取按鈕。

##<a name="add-job"></a>建立新的排定工作

現在，您可以建立可存取 Twitter 並將推文資料儲存於全新 Updates 資料表中的排定工作。

2. 按一下 [ **排程器** 索引標籤，然後按一下 [ **+ 建立**。

    >[AZURE.NOTE]當您執行您的行動服務 <em>免費</em> 層，您才能夠一次執行一個排定的工作。 在付費層中，您一次可以執行多達十個排定工作。

3. 在 [排程器] 對話方塊中，輸入 _getUpdates_ 的 **工作名稱**, 、 設定排程間隔和單位，然後按一下核取按鈕。

    這會建立名為的新工作 **getUpdates**。

4. 按一下您剛才建立的新工作中，按一下 **指令碼** ] 索引標籤上，並將預留位置函數 **getUpdates** 為下列程式碼:

        var updatesTable = tables.getTable('Updates');
        var request = require('request');
        var twitterUrl = "https://api.twitter.com/1.1/search/tweets.json?q=%23mobileservices&result_type=recent";

        // Get the service configuration module.
        var config = require('mobileservice-config');

        // Get the stored Twitter consumer key and secret.
        var consumerKey = config.twitterConsumerKey,
            consumerSecret = config.twitterConsumerSecret
        // Get the Twitter access token from app settings.
        var accessToken= config.appSettings.TWITTER_ACCESS_TOKEN,
            accessTokenSecret = config.appSettings.TWITTER_ACCESS_TOKEN_SECRET;

        function getUpdates() {
            // Check what is the last tweet we stored when the job last ran
            // and ask Twitter to only give us more recent tweets
            appendLastTweetId(
                twitterUrl,
                function twitterUrlReady(url){
                    // Create a new request with OAuth credentials.
                    request.get({
                        url: url,
                        oauth: {
                            consumer_key: consumerKey,
                            consumer_secret: consumerSecret,
                            token: accessToken,
                            token_secret: accessTokenSecret
                        }},
                        function (error, response, body) {
                        if (!error && response.statusCode == 200) {
                            var results = JSON.parse(body).statuses;
                            if(results){
                                console.log('Fetched ' + results.length + ' new results from Twitter');
                                results.forEach(function (tweet){
                                    if(!filterOutTweet(tweet)){
                                        var update = {
                                            twitterId: tweet.id,
                                            text: tweet.text,
                                            author: tweet.user.screen_name,
                                            date: tweet.created_at
                                        };
                                        updatesTable.insert(update);
                                    }
                                });
                            }
                        } else {
                            console.error('Could not contact Twitter');
                        }
                    });

                });
         }
        // Find the largest (most recent) tweet ID we have already stored
        // (if we have stored any) and ask Twitter to only return more
        // recent ones
        function appendLastTweetId(url, callback){
            updatesTable
            .orderByDescending('twitterId')
            .read({success: function readUpdates(updates){
                if(updates.length){
                    callback(url + '&since_id=' + (updates[0].twitterId + 1));
                } else {
                    callback(url);
                }
            }});
        }

        function filterOutTweet(tweet){
            // Remove retweets and replies
            return (tweet.text.indexOf('RT') === 0 || tweet.to_user_id);
        }


    此指令碼會使用儲存的認證來呼叫 Twitter 查詢 API，以要求包含雜湊標記 `#mobileservices` 的最新推文。 在重複的推文和回覆被儲存於資料表之前，系統會先將它們從結果中移除。

    >[AZURE.NOTE]這個範例假設只有少數資料列插入資料表，在每次排定執行。 倘若一個迴圈會在免費層上執行插入許多資料列，則您可能會沒有足夠的連線。 在此情況下，您應該以批次方式執行插入。 如需詳細資訊，請參閱[作法：執行大量插入](mobile-services-how-to-use-server-scripts.md#bulk-inserts)。

6. 按一下 [ **執行一次** 測試指令碼。

    當工作在排程器中維持停用狀態時，這麼做會儲存並執行工作。

7. 按一下 [上一頁] 按鈕，再按一下 **資料**, ，按一下 [ **更新** 資料表中，按一下 **瀏覽**, ，並驗證 Twitter 資料已插入至資料表。

8. 按一下 [上一頁] 按鈕，再按一下 **排程器**, ，請選取 **getUpdates**, ，然後按一下 [ **啟用**。

    這會讓工作在指定的排程上執行，在此案例中為每個小時。

恭喜！您已順利地在行動服務上建立新的排定工作。 系統會依排程執行此工作，直到您停用或修改此工作為止。

## <a name="nextsteps"> </a>另請參閱

* [行動服務伺服器指令碼參考]
  <br/>進一步了解註冊和使用伺服器指令碼。

<!-- Anchors. -->
[Register for Twitter access and store credentials]: #get-oauth-credentials
[Create the new Updates table]: #create-table
[Create a new scheduled job]: #add-job
[Next steps]: #next-steps

<!-- Images. -->

<!-- URLs. -->
[Mobile Services server script reference]: http://go.microsoft.com/fwlink/?LinkId=262293
[WindowsAzure.com]: http://www.windowsazure.com/
[Azure classic portal]: https://manage.windowsazure.com/
[Register your apps for Twitter login with Mobile Services]: /develop/mobile/how-to-guides/register-for-twitter-authentication
[Twitter Developers]: http://go.microsoft.com/fwlink/p/?LinkId=268300
[App settings]: http://msdn.microsoft.com/library/windowsazure/b6bb7d2d-35ae-47eb-a03f-6ee393e170f7

