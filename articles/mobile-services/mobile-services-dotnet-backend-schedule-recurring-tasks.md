<properties
    pageTitle="在 .NET 後端行動服務的後端工作排程 | Microsoft Azure"
    description="使用 Azure 行動服務排程器來定義依排程執行的 .NET 後端作業。"
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

本教學課程將逐步引導您瞭解如何使用工作排程器，來建立向 Twitter 要求推文資料並在新的 Updates 資料表中儲存推文的排定工作。

>[AZURE.NOTE]本教學課程會使用第三方 LINQ to Twitter 程式庫來簡化對 Twitter v1.1 OAuth 2.0 存取。 。 您必須下載並安裝 LINQ to Twitter NuGet 封裝，才能完成本教學課程。 如需詳細資訊，請參閱 [LINQ to Twitter CodePlex 專案中的]。

##<a name="get-oauth-credentials"></a>註冊以取得 Twitter v1.1 API 的存取權與儲存認證

[AZURE.INCLUDE [mobile-services-register-twitter-access](../../includes/mobile-services-register-twitter-access.md)]

&nbsp;&nbsp;7. 在 Visual Studio 的 [方案總管] 中，開啟行動服務專案的 web.config 檔案，找出 `MS_TwitterConsumerKey` 和 `MS_TwitterConsumerSecret` 應用程式設定，並以您在入口網站中設定的 Twitter 取用者金鑰值和取用者密碼值取代這些金鑰的值。

&nbsp;&nbsp;8. 在相同的區段中新增下列應用程式設定，並將預留位置取代為您在入口網站中設為應用程式設定的 Twitter 存取權杖和存取權杖密碼值：

    <add key="TWITTER_ACCESS_TOKEN" value="**your_access_token**" />
    <add key="TWITTER_ACCESS_TOKEN_SECRET" value="**your_access_token_secret**" />

行動服務在執行於本機電腦時會使用這些儲存的設定，讓您能夠先測試排定工作，再加以發佈。 在 Azure 中執行時，行動服務會改用在入口網站中設定的值，並忽略這些專案設定。

##<a name="install-linq2twitter"></a>下載並安裝 LINQ to Twitter 程式庫

1. 在 **方案總管] 中** 在 Visual Studio 中，以滑鼠右鍵按一下專案名稱，然後 **管理 NuGet 封裝**。

2. 在左窗格中，選取 **線上** 類別、 搜尋 `linq2twitter`, ，按一下 [ **安裝** 上 **linqtotwitter** 封裝，然後閱讀並接受授權合約。

    ![][1]

    這會將 Linq to Twitter 程式庫新增至您的行動服務專案。

接下來，您必須建立要儲存推文的新資料表。

##<a name="create-table"></a>建立新的 Updates 資料表

1. 在 Visual Studio [方案總管] 中以滑鼠右鍵按一下 DataObjects 資料夾中，展開 **新增**, ，按一下 [ **類別**,   ，型別 `Updates` 的 **名稱**, ，然後按一下 [ **新增**。

    這會為 Updates 類別建立新的專案檔案。

2. 以滑鼠右鍵按一下 **參考** > **加入參考...** > **Framework** 下 **組件**, ，然後檢查 **System.ComponentModel.DataAnnotations** 按一下 **確定**。

    ![][7]

    這會加入新的組件參考。

2. 在此新類別中新增下列 **使用** 陳述式:

        using Microsoft.WindowsAzure.Mobile.Service;
        using System.ComponentModel.DataAnnotations;

3. 取代 **更新** 類別定義以下列程式碼:

        public class Updates
        {
            [Key]
            public int UpdateId { get; set; }
            public long TweetId { get; set; }
            public string Text { get; set; }
            public string Author { get; set; }
            public DateTime Date { get; set; }
        }

4. 展開 [模型] 資料夾，開啟資料模型內容檔案 (名為 *service_name*Context.cs)，並新增下列屬性的傳回型別 **DbSet**:

        public DbSet<Updates> Updates { get; set; }

    服務會使用 Updates 資料表 (在第一次存取 DbSet 時建立於資料庫中) 儲存推文資料。

    >[AZURE.NOTE] 當使用預設資料庫初始設定式時，Entity Framework 會卸除並 Code First 模型定義中偵測到資料模型變更時重新建立資料庫。 若要進行此資料模型變更，並保有資料庫的現有資料，必須使用 Code First Migrations。 無法針對 Azure 中的 SQL Database 使用預設的初始設定式。 如需詳細資訊，請參閱 [如何使用 Code First Migrations 更新資料模型](mobile-services-dotnet-backend-use-code-first-migrations.md)。

接下來，請建立可存取 Twitter 並將推文資料儲存於全新 Updates 資料表中的排定工作。

##<a name="add-job"></a>建立新的排定工作

1. 展開 ScheduledJobs 資料夾，並開啟 SampleJob.cs 專案檔案。

    這個類別繼承自 **ScheduledJob**, ，代表可依排程，在 Azure 傳統入口網站，固定排程或依需求執行工作。

2. 以下列程式碼取代 SampleJob.cs 的內容：

        using System;
        using System.Linq;
        using System.Threading;
        using System.Threading.Tasks;
        using System.Web.Http;
        using Microsoft.WindowsAzure.Mobile.Service;
        using Microsoft.WindowsAzure.Mobile.Service.ScheduledJobs;
        using LinqToTwitter;
        using todolistService.Models;
        using todolistService.DataObjects;

        namespace todolistService
        {
            // A simple scheduled job which can be invoked manually by submitting an HTTP
            // POST request to the path "/jobs/sample".
            public class SampleJob : ScheduledJob
            {
                private todolistContext context;
                private string accessToken;
                private string accessTokenSecret;

                protected override void Initialize(ScheduledJobDescriptor scheduledJobDescriptor,
                    CancellationToken cancellationToken)
                {
                    base.Initialize(scheduledJobDescriptor, cancellationToken);

                    // Create a new context with the supplied schema name.
                    context = new todolistContext();
                }

                public async override Task ExecuteAsync()
                {
                    // Try to get the stored Twitter access token from app settings.
                    if (!(Services.Settings.TryGetValue("TWITTER_ACCESS_TOKEN", out accessToken) |
                    Services.Settings.TryGetValue("TWITTER_ACCESS_TOKEN_SECRET", out accessTokenSecret)))
                    {
                        Services.Log.Error("Could not retrieve Twitter access credentials.");
                    }

                    // Create a new authorizer to access Twitter v1.1 APIs
                    // using single-user OAUth 2.0 credentials.
                    MvcAuthorizer auth = new MvcAuthorizer();
                    SingleUserInMemoryCredentialStore store =
                        new SingleUserInMemoryCredentialStore()
                    {
                        ConsumerKey = Services.Settings.TwitterConsumerKey,
                        ConsumerSecret = Services.Settings.TwitterConsumerSecret,
                        OAuthToken = accessToken,
                        OAuthTokenSecret = accessTokenSecret
                    };

                    // Set the credentials for the authorizer.
                    auth.CredentialStore = store;

                    // Create a new LINQ to Twitter context.
                    TwitterContext twitter = new TwitterContext(auth);

                    // Get the ID of the most recent stored tweet.
                    long lastTweetId = 0;
                    if (context.Updates.Count() > 0)
                    {
                        lastTweetId = (from u in context.Updates
                                       orderby u.TweetId descending
                                       select u).Take(1).SingleOrDefault()
                                                    .TweetId;
                    }

                    // Execute a search that returns a filtered result.
                    var response = await (from s in twitter.Search
                                          where s.Type == SearchType.Search
                                          && s.Query == "%23mobileservices"
                                          && s.SinceID == Convert.ToUInt64(lastTweetId + 1)
                                          && s.ResultType == ResultType.Recent
                                          select s).SingleOrDefaultAsync();

                    // Remove retweets and replies and log the number of tweets.
                    var filteredTweets = response.Statuses
                        .Where(t => !t.Text.StartsWith("RT") && t.InReplyToUserID == 0);
                    Services.Log.Info("Fetched " + filteredTweets.Count()
                        + " new tweets from Twitter.");

                    // Store new tweets in the Updates table.
                    foreach (Status tweet in filteredTweets)
                    {
                        Updates newTweet =
                            new Updates
                            {
                                TweetId = Convert.ToInt64(tweet.StatusID),
                                Text = tweet.Text,
                                Author = tweet.User.Name,
                                Date = tweet.CreatedAt
                            };

                        context.Updates.Add(newTweet);
                    }

                    await context.SaveChangesAsync();
                }
                protected override void Dispose(bool disposing)
                {
                    base.Dispose(disposing);
                    if (disposing)
                    {
                        context.Dispose();
                    }
                }
            }
        }

    在上述程式碼中，您必須將字串 _todolistService_ 和 _todolistContext_ 命名空間和 DbContext，下載的專案，這是 *mobile & #95 服務 & #95; 名稱*服務和 *mobile & #95 服務 & #95; 名稱*個別內容。

    在上述程式碼中， **ExecuteAsync** 覆寫方法會呼叫 Twitter 查詢 API，使用預存的認證以要求包含雜湊標記的最新推文 `#mobileservices`。 在重複的推文和回覆被儲存於資料表之前，系統會先將它們從結果中移除。

##<a name="run-job-locally"></a>在本機測試排定工作

排程工作可先在本機進行測試，再發佈至 Azure，以及在入口網站中註冊。

1. 在 Visual Studio 中，將行動服務專案設為起始專案，然後按 F5。

    這會啟動行動服務專案，以及顯示歡迎使用頁面的新瀏覽器視窗。

2. 按一下 [ **試試看**, ，然後按一下 [ **POST 工作 / {jobName}**。

    ![][8]

4. 按一下 [ **試試看**, ，型別 `Sample` 為 **{jobName}** 參數值，然後按一下 [ **傳送**。

    ![][9]

    這會將新的 POST 要求傳送至範例工作端點。 在本機服務中， **ExecuteAsync** 方法會啟動。 您可以在這個方法中設定中斷點，對程式碼進行偵錯。

3. 在 [伺服器總管] 中，展開 **資料連接**, ，**MSTableConnectionString**, ，和 **資料表**，以滑鼠右鍵按一下 **更新** 按一下 **顯示資料表資料**。

    新的推文會以資料列的形式輸入資料表中。

##<a name="register-job"></a>發佈服務及註冊新工作

工作必須註冊在 **排程器** ] 索引標籤上，讓行動服務可以執行在您定義的排程。

3. 將行動服務專案重新發佈至 Azure。

4. 在 [Azure 傳統入口網站]，按一下 [行動服務，然後按一下您的應用程式。

2. 按一下 [ **排程器** 索引標籤，然後按一下 [ **+ 建立**。

    >[AZURE.NOTE]當您執行您的行動服務 <em>免費</em> 層，您才能夠一次執行一個排定的工作。 在付費層中，您一次可以執行多達十個排定工作。

3. 在 [排程器] 對話方塊中，輸入 _範例_ 的 **工作名稱**, 、 設定排程間隔和單位，然後按一下核取按鈕。

    ![][4]

    這會建立名為的新工作 **範例**。

4. 按一下新的工作，您剛建立的然後按一下 [ **執行一次** 測試指令碼。

    這會執行在排程器中仍處於停用狀態的工作。 在此頁面上，您可以隨時啟用工作及變更其排程。

    >[AZURE.NOTE]POST 要求仍然可以用於啟動排程的工作。 However, the authorization defaults to user, which means that the request must include the application key in the header.

4. (選擇性)在 [Azure 傳統入口網站]，按一下 [管理您的行動服務相關聯的資料庫。

    ![][6]

5. 在 Azure 傳統入口網站中執行查詢，以檢視應用程式所作的變更。 您的查詢會與下列查詢相類似，但將以您的行動服務名稱做為結構描述名稱，而非 `todolist`。

        SELECT * FROM [todolist].[Updates]

恭喜！您已順利地在行動服務上建立新的排定工作。 系統會依排程執行此工作，直到您停用或修改此工作為止。

<!-- Anchors. -->
[Register for Twitter access and store credentials]: #get-oauth-credentials
[Download and install the LINQ to Twitter library]: #install-linq2twitter
[Create the new Updates table]: #create-table
[Create a new scheduled job]: #add-job
[Test the scheduled job locally]: #run-job-locally
[Publish the service and register the job]: #register-job
[Next steps]: #next-steps

<!-- Images. -->
[1]: ./media/mobile-services-dotnet-backend-schedule-recurring-tasks/add-linq2twitter-nuget-package.png
[2]: ./media/mobile-services-dotnet-backend-schedule-recurring-tasks/mobile-services-selection.png
[3]: ./media/mobile-services-dotnet-backend-schedule-recurring-tasks/mobile-schedule-new-job-cli.png
[4]: ./media/mobile-services-dotnet-backend-schedule-recurring-tasks/create-new-job.png
[5]: ./media/mobile-services-dotnet-backend-schedule-recurring-tasks/sample-job-run-once.png
[6]: ./media/mobile-services-dotnet-backend-schedule-recurring-tasks/manage-sql-azure-database.png
[7]: ./media/mobile-services-dotnet-backend-schedule-recurring-tasks/add-component-model-reference.png
[8]: ./media/mobile-services-dotnet-backend-schedule-recurring-tasks/mobile-service-start-page.png
[9]: ./media/mobile-services-dotnet-backend-schedule-recurring-tasks/mobile-service-try-this-out.png

<!-- URLs. -->
[Azure classic portal]: https://manage.windowsazure.com/
[Register your apps for Twitter login with Mobile Services]: mobile-services-how-to-register-twitter-authentication.md
[Twitter Developers]: http://go.microsoft.com/fwlink/p/?LinkId=268300
[App settings]: http://msdn.microsoft.com/library/windowsazure/b6bb7d2d-35ae-47eb-a03f-6ee393e170f7
[LINQ to Twitter CodePlex project]: http://linqtotwitter.codeplex.com/
