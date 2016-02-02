## 建立裝置識別

在本節中，您將建立 Windows 主控台應用程式，它會在 IoT 中樞的身分識別登錄中建立新的裝置身分識別。 裝置無法連線到 IoT 中樞，除非它在裝置身分識別登錄中具有項目。 請參閱 **裝置身分識別登錄** 區段 [IoT 中心開發人員指南 ][lnk-devguide-identity] 如需詳細資訊。 執行這個主控台應用程式時，它會產生唯一的裝置識別碼及金鑰，當裝置向 IoT 中樞傳送裝置對雲端訊息時，可以用來識別裝置本身。

1. 在 Visual Studio 中，將新的 Visual C# Windows 傳統桌面專案加入至目前的方案使用 **主控台應用程式** 專案範本。 將專案命名為 **CreateDeviceIdentity**。

    ![][10]

2. 在 [方案總管] 中，以滑鼠右鍵按一下 **CreateDeviceIdentity** 專案，然後按一下 [管理 NuGet 封裝]****。

3. 在 [NuGet 封裝管理員]**** 視窗中，請確認已選取 [包含發行前版本]**** 選項。 然後搜尋 **Microsoft Azure 裝置**，按一下 [安裝]**** 並接受使用規定。

    ![][11]

4. 這會下載、 安裝，並將參考加入 [Microsoft Azure 裝置 SDK ][lnk-nuget-device-sdk] NuGet 封裝。

4. 新增下列 `使用` 上方的陳述式 **Program.cs** 檔案:

        using Microsoft.Azure.Devices;
        using Microsoft.Azure.Devices.Common.Exceptions;

5. 將下列欄位加入至 **Program** 類別，並將預留位置的值替換為您在上一節中為 IoT 中樞所建立的連接字串 ：

        static RegistryManager registryManager;
        static string connectionString = "{iothub connection string}";

6. 將下列方法加入至 **Program** 類別：

        private async static Task AddDeviceAsync()
        {
            string deviceId = "myFirstDevice";
            Device device;
            try
            {
                device = await registryManager.AddDeviceAsync(new Device(deviceId));
            }
            catch (DeviceAlreadyExistsException)
            {
                device = await registryManager.GetDeviceAsync(deviceId);
            }
            Console.WriteLine("Generated device key: {0}", device.Authentication.SymmetricKey.PrimaryKey);
        }

    這個方法會建立具有識別碼 **myFirstDevice** 的新裝置身分識別 (如果該裝置識別碼已經存在登錄中，程式碼就只會擷取現有的裝置資訊)。 接著，應用程式會顯示該身分識別的主要金鑰。 您將在此模擬裝置中使用此金鑰，連線到您的 IoT 中樞。

7. 最後，將下列幾行加入至 **Main** 方法：

        registryManager = RegistryManager.CreateFromConnectionString(connectionString);
        AddDeviceAsync().Wait();
        Console.ReadLine();

8. 執行此應用程式，並記下裝置的金鑰。

    ![][12]

> [AZURE.NOTE] IoT 中樞身分識別登錄只會儲存裝置身分識別，以啟用對中樞的安全存取。 它會儲存裝置識別碼和金鑰，來做為安全性認證，以及啟用或停用旗標，讓您停用個別裝置的存取。 如果您的應用程式需要儲存其他裝置特定的中繼資料，它應該使用應用程式專用的存放區。 請參閱 [IoT 中心開發人員指南 ][lnk-devguide-identity] 如需詳細資訊。

## 接收裝置到雲端的訊息

在本節中，您將建立 Windows 主控台應用程式，以讀取來自 IoT 中樞的裝置到雲端訊息。 IoT 中心公開 [事件中心 ][lnk-event-hubs-overview]-可讓您讀取裝置對雲端訊息相容的端點。 為了簡單起見，本教學課程會建立的基本讀取器不適合用於高輸送量部署。  [處理裝置對雲端訊息 ][lnk-processd2c-tutorial] 教學課程說明如何處理大規模裝置對雲端訊息和 [開始使用事件中心 ][lnk-eventhubs-tutorial] 教學課程上提供進一步資訊的方式來處理訊息來自事件中心。

1. 在 Visual Studio 中，將新的 Visual C# Windows 傳統桌面專案加入至目前的方案使用 **主控台應用程式** 專案範本。 將專案命名為 **ReadDeviceToCloudMessages**。

    ![][10]

2. 在 [方案總管] 中，以滑鼠右鍵按一下 **ReadDeviceToCloudMessages** 專案，然後按一下 [管理 NuGet 封裝]****。

3. 在 [NuGet 封裝管理員]**** 視窗中，請確認已選取 [包含發行前版本]**** 選項。 然後搜尋 **WindowsAzure.ServiceBus**，按一下 [安裝]**** 並接受使用規定。

    這會下載、 安裝，並將參考加入 [Azure 服務匯流排 ][lnk-servicebus-nuget], ，與其所有相依性。

4. 新增下列 `使用` 上方的陳述式 **Program.cs** 檔案:

        using Microsoft.ServiceBus.Messaging;

5. 將下列欄位加入至 **Program** 類別，並將預留位置的值替換為您在*建立 IoT 中樞*一節中為 IoT 中樞所建立的連接字串：

        static string connectionString = "{iothub connection string}";
        static string iotHubD2cEndpoint = "messages/events";
        static EventHubClient eventHubClient;

6. 將下列方法加入至 **Program** 類別：

     private async static Task ReceiveMessagesFromDeviceAsync(string partition)
     {
         var eventHubReceiver = eventHubClient.GetDefaultConsumerGroup().CreateReceiver(partition, DateTime.Now);
         while (true)
         {
             EventData eventData = await eventHubReceiver.ReceiveAsync();
             if (eventData == null) continue;
    
             string data = Encoding.UTF8.GetString(eventData.GetBytes());
             Console.WriteLine(string.Format("Message received. Partition: {0} Data: '{1}'", partition, data));
         }
     }

 這個方法會使用 **EventHubReceiver** 執行個體接收來自所有 IoT 中樞裝置對雲端接收資料分割的訊息。 請注意如何傳遞 `Ticks` 參數，當您建立 **EventHubReceiver** 物件，使它只會收到它啟動後傳送的訊息。

7. 最後，將下列幾行加入至 **Main** 方法：

     Console.WriteLine("Receive messages\n");
     eventHubClient = EventHubClient.CreateFromConnectionString(connectionString, iotHubD2cEndpoint);
    
     var d2cPartitions = eventHubClient.GetRuntimeInformation().PartitionIds;
    
     foreach (string partition in d2cPartitions)
     {
        ReceiveMessagesFromDeviceAsync(partition);
     }  
     Console.ReadLine();







[lnk-eventhubs-tutorial]: event-hubs-csharp-ephcs-getstarted.md 
[lnk-devguide-identity]: iot-hub-devguide.md#identityregistry 
[lnk-servicebus-nuget]: https://www.nuget.org/packages/WindowsAzure.ServiceBus 
[lnk-event-hubs-overview]: event-hubs-overview.md 
[lnk-nuget-device-sdk]: https://www.nuget.org/packages/Microsoft.Azure.Devices/ 
[lnk-processd2c-tutorial]: iot-hub-csharp-csharp-process-d2c.md 
[10]: ./media/iot-hub-getstarted-cloud-csharp/create-identity-csharp1.png 
[11]: ./media/iot-hub-getstarted-cloud-csharp/create-identity-csharp2.png 
[12]: ./media/iot-hub-getstarted-cloud-csharp/create-identity-csharp3.png 

