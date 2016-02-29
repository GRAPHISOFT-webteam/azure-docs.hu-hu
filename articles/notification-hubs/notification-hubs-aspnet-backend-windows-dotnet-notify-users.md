<properties
    pageTitle="Azure 通知中樞透過 .NET 後端通知使用者"
    description="了解如何在 Azure 中傳送安全的推播通知。 程式碼範例是以 C# 撰寫並使用 .NET API。"
    documentationCenter="windows"
    authors="wesmc7777"
    manager="dwrede"
    services="notification-hubs"
    editor=""/>

<tags
    ms.service="notification-hubs"
    ms.workload="mobile"
    ms.tgt_pltfrm="mobile-windows"
    ms.devlang="dotnet"
    ms.topic="article"
    ms.date="11/09/2015"
    ms.author="wesmc"/>

#Azure 通知中樞透過 .NET 後端通知使用者

[AZURE.INCLUDE [notification-hubs-selector-aspnet-backend-notify-users](../../includes/notification-hubs-selector-aspnet-backend-notify-users.md)]


##概觀

Azure 中的推播通知支援可讓您存取易於使用、多重平台的大規模推播基礎結構，而大幅簡化消費者和企業應用程式在行動平台上的推播通知實作。 本教學課程將示範如何使用 Azure 通知中心，來將推播通知傳送到特定裝置上的特定應用程式使用者。 ASP.NET WebAPI 後端是用來驗證用戶端。 使用驗證的用戶端使用者，後端就會自動將標記新增通知註冊。 後端會傳送此標記，以產生特定使用者的通知。 如需有關使用應用程式後端註冊通知的詳細資訊，請參閱指引主題 [從應用程式後端註冊](http://msdn.microsoft.com/library/dn743807.aspx)。 本教學課程為基礎的通知中樞和專案中建立 [開始使用通知中心] 教學課程。

本教學課程也是 [安全推播] 教學課程的必要條件。 完成本教學課程中的步驟之後，您可以前往 [安全推播] 教學課程，示範如何修改程式碼，在本教學課程以安全的方式傳送推播通知。





## 開始之前

我們非常重視您的意見反應。 如果您對於完成此主題有任何困難，或者有改進此內容的建議，非常歡迎您在本頁底部提供意見反應。

完成本教學課程中的程式碼可以在 GitHub 上找到 [這裡](https://github.com/Azure/azure-notificationhubs-samples/tree/master/dotnet/NotifyUsers)。 



##先決條件

在開始本教學課程之前，您必須已完成下列行動服務教學課程：

+ [開始使用通知中心]<br/>建立您的通知中心並保留應用程式名稱並註冊以接收通知，在本教學課程。 本教學課程假設您已完成這些步驟。 如果沒有，請依照 [開始使用通知中心 (Windows 市集)](notification-hubs-windows-store-dotnet-get-started.md); 尤其是區段 [註冊 Windows 市集應用程式](notification-hubs-windows-store-dotnet-get-started.md#register-your-app-for-the-windows-store) 和 [設定通知中心](notification-hubs-windows-store-dotnet-get-started.md#configure-your-notification-hub)。 特別是，確定您已輸入 **封裝 SID** 和 **用戶端密碼** 中的值在網站中， **設定** 的通知中心] 索引標籤。 此組態程序一節中所述 [設定通知中心](notification-hubs-windows-store-dotnet-get-started.md#configure-your-notification-hub)。 這是重要步驟：如果入口網站上的認證與您為所選應用程式名稱指定的認證不符，則推播通知將無法順利進行。




> [AZURE.NOTE] 如果您使用行動服務作為後端服務，請參閱 [行動服務版本](../mobile-services-javascript-backend-windows-store-dotnet-push-notifications-app-users.md) 本教學課程。




[AZURE.INCLUDE [notification-hubs-aspnet-backend-notifyusers](../../includes/notification-hubs-aspnet-backend-notifyusers.md)]

## 更新用戶端專案的程式碼

本節中，您可以更新您已完成的專案中的程式碼 [開始使用通知中心] 教學課程。 這應該已經與市集相關聯，並已針對您的通知中樞進行設定。 在本節中，您將加入程式碼以呼叫新的 WebAPI 後端，並使用它來註冊和傳送通知。

1. 在 Visual Studio 中開啟方案，您建立的 [開始使用通知中心] 教學課程。

2. 在 [方案總管] 中，以滑鼠右鍵按一下 **(Windows 8.1)** 專案，然後按一下 **管理 NuGet 封裝**。

3. 在左側，按一下 [ **線上**。

4. 在 **搜尋** 方塊中，輸入 **Http 用戶端**。

5. 在 [結果] 清單中，按一下 [ **Microsoft HTTP 用戶端程式庫**, ，然後按一下 [ **安裝**。 完成安裝。

6. 在 [NuGet **搜尋** 方塊中，輸入 **Json.net**。 安裝 **Json.NET** 封裝，然後關閉 [NuGet Package Manager] 視窗。

7. 重複上述步驟 **(Windows Phone 8.1)** 專案，以安裝 **JSON.NET** Windows Phone 專案的 NuGet 封裝。


8. 在 [方案總管中 **(Windows 8.1)** 專案中，按兩下 **MainPage.xaml** 在 Visual Studio 編輯器中開啟它。

9. 在 **MainPage.xaml** XML 程式碼，取代 `<Grid>` 區段取代為下列程式碼。 這個程式碼加入使用者用來進行驗證的使用者名稱和密碼文字方塊。 它也會加入通知訊息的文字方塊，以及應接收通知的使用者名稱標記：

        <Grid>
            <Grid.RowDefinitions>
                <RowDefinition Height="Auto"/>
                <RowDefinition Height="*"/>
            </Grid.RowDefinitions>

            <TextBlock Grid.Row="0" Text="Notify Users" HorizontalAlignment="Center" FontSize="48"/>

            <StackPanel Grid.Row="1" VerticalAlignment="Center">
                <Grid>
                    <Grid.RowDefinitions>
                        <RowDefinition Height="Auto"/>
                        <RowDefinition Height="Auto"/>
                        <RowDefinition Height="Auto"/>
                        <RowDefinition Height="Auto"/>
                        <RowDefinition Height="Auto"/>
                        <RowDefinition Height="Auto"/>
                        <RowDefinition Height="Auto"/>
                        <RowDefinition Height="Auto"/>
                        <RowDefinition Height="Auto"/>
                        <RowDefinition Height="Auto"/>
                        <RowDefinition Height="Auto"/>
                    </Grid.RowDefinitions>
                    <Grid.ColumnDefinitions>
                        <ColumnDefinition></ColumnDefinition>
                        <ColumnDefinition></ColumnDefinition>
                        <ColumnDefinition></ColumnDefinition>
                    </Grid.ColumnDefinitions>
                    <TextBlock Grid.Row="0" Grid.ColumnSpan="3" Text="Username" FontSize="24" Margin="20,0,20,0"/>
                    <TextBox Name="UsernameTextBox" Grid.Row="1" Grid.ColumnSpan="3" Margin="20,0,20,0"/>
                    <TextBlock Grid.Row="2" Grid.ColumnSpan="3" Text="Password" FontSize="24" Margin="20,0,20,0" />
                    <PasswordBox Name="PasswordTextBox" Grid.Row="3" Grid.ColumnSpan="3" Margin="20,0,20,0"/>

                    <Button Grid.Row="4" Grid.ColumnSpan="3" HorizontalAlignment="Center" VerticalAlignment="Center"
                                Content="1. Login and register" Click="LoginAndRegisterClick" Margin="0,0,0,20"/>

                    <ToggleButton Name="toggleWNS" Grid.Row="5" Grid.Column="0" HorizontalAlignment="Right" Content="WNS" IsChecked="True" />
                    <ToggleButton Name="toggleGCM" Grid.Row="5" Grid.Column="1" HorizontalAlignment="Center" Content="GCM" />
                    <ToggleButton Name="toggleAPNS" Grid.Row="5" Grid.Column="2" HorizontalAlignment="Left" Content="APNS" />

                    <TextBlock Grid.Row="6" Grid.ColumnSpan="3" Text="Username Tag To Send To" FontSize="24" Margin="20,0,20,0"/>
                    <TextBox Name="ToUserTagTextBox" Grid.Row="7" Grid.ColumnSpan="3" Margin="20,0,20,0" TextWrapping="Wrap" />
                    <TextBlock Grid.Row="8" Grid.ColumnSpan="3" Text="Enter Notification Message" FontSize="24" Margin="20,0,20,0"/>
                    <TextBox Name="NotificationMessageTextBox" Grid.Row="9" Grid.ColumnSpan="3" Margin="20,0,20,0" TextWrapping="Wrap" />
                    <Button Grid.Row="10" Grid.ColumnSpan="3" HorizontalAlignment="Center" Content="2. Send push" Click="PushClick" Name="SendPushButton" />
                </Grid>
            </StackPanel>
        </Grid>


10. 在 [方案總管中 **(Windows Phone 8.1)** 專案中，開啟 **MainPage.xaml** 並將 Windows Phone 8.1 `<Grid>` 與上述相同的程式碼區段。 介面看起來應該會如下所示。

    ![][13]

11. 在 [方案總管] 中，開啟 **MainPage.xaml.cs** 檔案 **(Windows 8.1)** 和 **(Windows Phone 8.1)** 專案。 在這兩個檔案頂端加入下列 `using` 陳述式：

        using System.Net.Http;
        using Windows.Storage;
        using System.Net.Http.Headers;
        using Windows.Networking.PushNotifications;
        using Windows.UI.Popups;
        using System.Threading.Tasks;

12. 在 **MainPage.xaml.cs** 的 **(Windows 8.1)** 和 **(Windows Phone 8.1)** 專案內加入下列成員加入 `MainPage` 類別。 請務必使用先前取得的實際後端端點來取代 `<Enter Your Backend Endpoint>`： 例如，`http://mybackend.azurewebsites.net`。

        private static string BACKEND_ENDPOINT = "<Enter Your Backend Endpoint>";



13. 將下列程式碼新增至 MainPage 類別中 **MainPage.xaml.cs** 的 **(Windows 8.1)** 和 **(Windows Phone 8.1)** 專案。

     `PushClick` 方法是 click 處理常式 **傳送推播** ] 按鈕。 它會呼叫後端以觸發所有裝置的通知，而所有裝置都具有符合 `to_tag` 參數的使用者名稱標記。 通知訊息會以要求主體的 JSON 內容形式傳送。

     `LoginAndRegisterClick` 方法是 click 處理常式 **登入並註冊** ] 按鈕。 它會在本機儲存體中儲存基本驗證權杖 (請注意，這代表驗證結構描述使用的任何權杖)，然後使用 `RegisterClient` 以使用後端來註冊通知。


        private async void PushClick(object sender, RoutedEventArgs e)
        {
            if (toggleWNS.IsChecked.Value)
            {
                await sendPush("wns", ToUserTagTextBox.Text, this.NotificationMessageTextBox.Text);
            }
            if (toggleGCM.IsChecked.Value)
            {
                await sendPush("gcm", ToUserTagTextBox.Text, this.NotificationMessageTextBox.Text);
            }
            if (toggleAPNS.IsChecked.Value)
            {
                await sendPush("apns", ToUserTagTextBox.Text, this.NotificationMessageTextBox.Text);

            }
        }

        private async Task sendPush(string pns, string userTag, string message)
        {
            var POST_URL = BACKEND_ENDPOINT + "/api/notifications?pns=" +
                pns + "&to_tag=" + userTag;

            using (var httpClient = new HttpClient())
            {
                var settings = ApplicationData.Current.LocalSettings.Values;
                httpClient.DefaultRequestHeaders.Authorization = new AuthenticationHeaderValue("Basic", (string)settings["AuthenticationToken"]);

                try
                {
                    await httpClient.PostAsync(POST_URL, new StringContent("\"" + message + "\"",
                        System.Text.Encoding.UTF8, "application/json"));
                }
                catch (Exception ex)
                {
                    MessageDialog alert = new MessageDialog(ex.Message, "Failed to send " + pns + " message");
                    alert.ShowAsync();
                }
            }
        }

        private async void LoginAndRegisterClick(object sender, RoutedEventArgs e)
        {
            SetAuthenticationTokenInLocalStorage();

            var channel = await PushNotificationChannelManager.CreatePushNotificationChannelForApplicationAsync();

            // The "username:<user name>" tag gets automatically added by the message handler in the backend.
            // The tag passed here can be whatever other tags you may want to use.
            try
            {
                // The device handle used will be different depending on the device and PNS. 
                // Windows devices use the channel uri as the PNS handle.
                await new RegisterClient(BACKEND_ENDPOINT).RegisterAsync(channel.Uri, new string[] { "myTag" });

                var dialog = new MessageDialog("Registered as: " + UsernameTextBox.Text);
                dialog.Commands.Add(new UICommand("OK"));
                await dialog.ShowAsync();
                SendPushButton.IsEnabled = true;
            }
            catch (Exception ex)
            {
                MessageDialog alert = new MessageDialog(ex.Message, "Failed to register with RegisterClient");
                alert.ShowAsync();
            }
        }

        private void SetAuthenticationTokenInLocalStorage()
        {
            string username = UsernameTextBox.Text;
            string password = PasswordTextBox.Password;

            var token = Convert.ToBase64String(System.Text.Encoding.UTF8.GetBytes(username + ":" + password));
            ApplicationData.Current.LocalSettings.Values["AuthenticationToken"] = token;
        }



14. 在 [方案總管在 **共用** 專案中，開啟 **App.xaml.cs** 檔案。 在 `OnLaunched()` 事件處理常式中，尋找對 `InitNotificationsAsync()` 的呼叫。 取消註解或刪除對 `InitNotificationsAsync()` 的呼叫。 上面加入的按鈕處理常式會初始化通知註冊。


        protected override void OnLaunched(LaunchActivatedEventArgs e)
        {
            //InitNotificationsAsync();


15. 在 [方案總管] 中，以滑鼠右鍵按一下 **共用** 專案，然後按一下 **新增**, ，然後按一下 [ **類別**。 將類別 **RegisterClient.cs**, ，然後按一下 [ **確定** 以產生類別。

    為了註冊推播通知，此類別會包裝連絡應用程式後端所需的 REST 呼叫。 它也會在本機儲存 *registrationIds* 中所述的通知中心所建立 [從應用程式後端註冊](http://msdn.microsoft.com/library/dn743807.aspx)。 請注意它會使用授權權杖儲存在本機儲存體中，當您按一下 **登入並註冊** ] 按鈕。


16. 在 RegisterClient.cs 檔案開頭加入下列 `using` 陳述式：

        using Windows.Storage;
        using System.Net;
        using System.Net.Http;
        using System.Net.Http.Headers;
        using Newtonsoft.Json;
        using System.Threading.Tasks;
        using System.Linq;

17. 在 `RegisterClient` 類別定義中加入下列程式碼。

        private string POST_URL;

        private class DeviceRegistration
        {
            public string Platform { get; set; }
            public string Handle { get; set; }
            public string[] Tags { get; set; }
        }

        public RegisterClient(string backendEndpoint)
        {
            POST_URL = backendEndpoint + "/api/register";
        }

        public async Task RegisterAsync(string handle, IEnumerable<string> tags)
        {
            var regId = await RetrieveRegistrationIdOrRequestNewOneAsync();

            var deviceRegistration = new DeviceRegistration
            {
                Platform = "wns",
                Handle = handle,
                Tags = tags.ToArray<string>()
            };

            var statusCode = await UpdateRegistrationAsync(regId, deviceRegistration);

            if (statusCode == HttpStatusCode.Gone)
            {
                // regId is expired, deleting from local storage & recreating
                var settings = ApplicationData.Current.LocalSettings.Values;
                settings.Remove("__NHRegistrationId");
                regId = await RetrieveRegistrationIdOrRequestNewOneAsync();
                statusCode = await UpdateRegistrationAsync(regId, deviceRegistration);
            }

            if (statusCode != HttpStatusCode.Accepted)
            {
                // log or throw
                throw new System.Net.WebException(statusCode.ToString());
            }
        }

        private async Task<HttpStatusCode> UpdateRegistrationAsync(string regId, DeviceRegistration deviceRegistration)
        {
            using (var httpClient = new HttpClient())
            {
                var settings = ApplicationData.Current.LocalSettings.Values;
                httpClient.DefaultRequestHeaders.Authorization = new AuthenticationHeaderValue("Basic", (string) settings["AuthenticationToken"]);

                var putUri = POST_URL + "/" + regId;

                string json = JsonConvert.SerializeObject(deviceRegistration);
                                var response = await httpClient.PutAsync(putUri, new StringContent(json, Encoding.UTF8, "application/json"));
                return response.StatusCode;
            }
        }

        private async Task<string> RetrieveRegistrationIdOrRequestNewOneAsync()
        {
            var settings = ApplicationData.Current.LocalSettings.Values;
            if (!settings.ContainsKey("__NHRegistrationId"))
            {
                using (var httpClient = new HttpClient())
                {
                    httpClient.DefaultRequestHeaders.Authorization = new AuthenticationHeaderValue("Basic", (string)settings["AuthenticationToken"]);

                    var response = await httpClient.PostAsync(POST_URL, new StringContent(""));
                    if (response.IsSuccessStatusCode)
                    {
                        string regId = await response.Content.ReadAsStringAsync();
                        regId = regId.Substring(1, regId.Length - 2);
                        settings.Add("__NHRegistrationId", regId);
                    }
                    else
                    {
                        throw new System.Net.WebException(response.StatusCode.ToString());
                    }
                }
            }
            return (string)settings["__NHRegistrationId"];

        }

18. 儲存您的所有變更。


## 測試應用程式

1. 在 Windows 8.1 和 Windows Phone 8.1 上啟動應用程式。 對於 Windows Phone 8.1，您可以在模擬器或實際裝置中執行執行個體。

2. 在應用程式的 Windows 8.1 執行個體中，輸入 **Username** 和 **密碼** 下列畫面所示。 它應該與您在 Windows Phone 上輸入的使用者名稱和密碼不同。


3. 按一下 [ **登入並註冊** ，並確認您已登入顯示的對話方塊。 這也會啟用 **傳送推播** ] 按鈕。

    ![][14]

4. 在 Windows Phone 8.1 執行個體中，輸入使用者名稱字串中都 **Username** 和 **密碼** 欄位然後按一下 [ **登入和註冊**。
5. 接著在 **收件者使用者名稱標記** 欄位中，輸入在 Windows 8.1 上註冊的使用者名稱。 輸入通知訊息，然後按一下 [ **傳送推播**。

    ![][16]

6. 只有已經使用相符使用者名稱標記所註冊的裝置才會收到通知訊息。

    ![][15]

## 後續步驟

* 如果您想要按興趣群組分隔使用者，請參閱 [使用通知中心傳送即時新聞]。
* 若要深入了解如何使用通知中心，請參閱 [通知中心指引]。



[9]: ./media/notification-hubs-aspnet-backend-windows-dotnet-notify-users/notification-hubs-secure-push9.png
[10]: ./media/notification-hubs-aspnet-backend-windows-dotnet-notify-users/notification-hubs-secure-push10.png
[11]: ./media/notification-hubs-aspnet-backend-windows-dotnet-notify-users/notification-hubs-secure-push11.png
[12]: ./media/notification-hubs-aspnet-backend-windows-dotnet-notify-users/notification-hubs-secure-push12.png
[13]: ./media/notification-hubs-aspnet-backend-windows-dotnet-notify-users/notification-hubs-wp-ui.png
[14]: ./media/notification-hubs-aspnet-backend-windows-dotnet-notify-users/notification-hubs-windows-instance-username.png
[15]: ./media/notification-hubs-aspnet-backend-windows-dotnet-notify-users/notification-hubs-notification-received.png
[16]: ./media/notification-hubs-aspnet-backend-windows-dotnet-notify-users/notification-hubs-wp-send-message.png



<!-- URLs. -->
[Get started with Notification Hubs]: notification-hubs-windows-store-dotnet-get-started.md
[Secure Push]: notification-hubs-aspnet-backend-windows-dotnet-secure-push.md
[Use Notification Hubs to send breaking news]: notification-hubs-windows-store-dotnet-send-breaking-news.md
[Notification Hubs Guidance]: http://msdn.microsoft.com/library/jj927170.aspx

