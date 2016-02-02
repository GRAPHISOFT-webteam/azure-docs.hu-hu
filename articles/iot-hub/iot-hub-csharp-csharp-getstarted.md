<properties
    pageTitle="開始使用 Azure IoT 中樞 | Microsoft Azure"
    description="請遵循此教學課程以開始搭配 C# 使用 Azure IoT 中心。"
    services="iot-hub"
    documentationCenter=".net"
    authors="dominicbetts"
    manager="timlt"
    editor=""/>

<tags
     ms.service="iot-hub"
     ms.devlang="dotnet"
     ms.topic="hero-article"
     ms.tgt_pltfrm="na"
     ms.workload="na"
     ms.date="12/14/2015"
     ms.author="dobett"/>


# 教學課程：開始使用 Azure IoT 中樞

## 簡介

Azure IoT 中樞是一項完全受管理的服務，可在數百萬個 IoT 裝置和一個解決方案後端之間啟用可靠且安全的雙向通訊。 IoT 專案所面臨的其中一個最大挑戰，就是如何可靠且安全地將裝置連線至解決方案後端。 若要解決這個問題，IoT 中樞：

- 提供可靠的裝置到雲端和雲端到裝置的超大規模傳訊。
- 使用每一裝置的安全性認證和存取控制來啟用安全通訊。
- 包括適用於最受歡迎的語言和平台的裝置程式庫。

本教學課程說明如何：

- 使用 Azure 入口網站來建立 IoT 中樞。
- 在您的 IoT 中樞中建立裝置身分識別。
- 建立模擬裝置，該裝置會傳送遙測到您的雲端後端，並從您的雲端後端接收命令。

在本教學課程結尾處，您將會有三個 Windows 主控台應用程式：

* **CreateDeviceIdentity**，這會建立裝置身分識別和相關聯的安全性金鑰，來連線您的模擬裝置。
* **ReadDeviceToCloudMessages**，其中顯示模擬的裝置所傳送的遙測。
* **SimulatedDevice**，這會使用先前建立的裝置身分識別連接到您的 IoT 中樞，並每秒傳送遙測訊息。

> [AZURE.NOTE] 

若要完成此教學課程，您需要下列項目：

+ Microsoft Visual Studio 2015。

+ 使用中的 Azure 帳戶。

## 建立 IoT 中樞

您需要為您的模擬裝置建立 IoT 中樞以供連接。 下列步驟顯示如何使用 Azure 入口網站完成這項工作。

1. 

2. 在 Jumpbar 中，按一下 [新增]****，然後按一下 [物聯網]****，再按一下 [Azure IoT 中樞]****。

    ![][1]

3. 在 [IoT 中樞]**** 刀鋒視窗中，選擇 IoT 中樞的組態。

    ![][2]

    * 在 [名稱]**** 方塊中，輸入 IoT 中樞的名稱。 如果**名稱**有效且可用，[名稱]**** 方塊中會出現綠色的核取記號。
    * 選取一個**價格和級別層**。 本教學課程不需要特定層。
    * 在**資源群組**中，建立新的資源群組，或選取現有的資源群組。
    * 在 [位置]**** 中，選取要裝載您的 IoT 中樞的位置。

4. 選擇好 IoT 中樞組態選項時，按一下 [建立]****。 Azure 建立您的 IoT 中樞可能需要幾分鐘的時間。 若要檢查狀態，您可以在「開始面板」或 [通知] 面板中監視進度。

    ![][3]

5. 成功建立 IoT 中樞時，請開啟新 IoT 中樞的刀鋒視窗，記下 **Hostname**，然後按一下 [金鑰]**** 圖示。

    ![][4]

6. 按一下 [iothubowner]**** 原則，然後複製並記下 **iothubowner** 刀鋒視窗中的連接字串。

    ![][5]

您現在已經建立 IoT 中樞，因此您已具有完成本教學課程的其餘部分所需的主機名稱和連接字串。

[AZURE.INCLUDE [iot-hub-get-started-cloud-csharp](../../includes/iot-hub-get-started-cloud-csharp.md)]


[AZURE.INCLUDE [iot-hub-get-started-device-csharp](../../includes/iot-hub-get-started-device-csharp.md)]

## 執行應用程式

現在您已經準備好執行應用程式。

1.  在 Visual Studio 的 [方案總管] 中以滑鼠右鍵按一下您的方案，然後按一下 [設定啟始專案]****。 選取 [多個啟始專案]****，然後同時針對 **ReadDeviceToCloudMessages** 和 **SimulatedDevice** 專案選取 [啟動]**** 做為 [動作]****。

    ![][41]

2.  按下 **F5** 來啟動這兩個執行的應用程式。 來自 **SimulatedDevice** 應用程式的主控台輸出會顯示模擬的裝置傳送給您的 IoT 中樞的訊息，而來自 **ReadDeviceToCloudMessages** 應用程式的主控台輸出則會顯示您的 IoT 中樞接收的訊息。

    ![][42]

## 後續步驟

在本教學課程中，您在入口網站中設定了新的 IoT 中樞，然後在中樞的身分識別登錄中建立了裝置身分識別。 您會將此裝置身分識別用於模擬的裝置，該裝置會將裝置對雲端訊息傳送至中樞，並建立另一個應用程式，以顯示中樞所接收的訊息。 您可以利用下列教學課程繼續探索 IoT 中樞功能和其他 IoT 案例：

- 
- 
- 

您可以在下列文章中進一步了解 IoT 中樞：

* 
* 
* 
* 
* 




[1]: ./media/iot-hub-csharp-csharp-getstarted/create-iot-hub1.png 
[2]: ./media/iot-hub-csharp-csharp-getstarted/create-iot-hub2.png 
[3]: ./media/iot-hub-csharp-csharp-getstarted/create-iot-hub3.png 
[4]: ./media/iot-hub-csharp-csharp-getstarted/create-iot-hub4.png 
[5]: ./media/iot-hub-csharp-csharp-getstarted/create-iot-hub5.png 
[41]: ./media/iot-hub-csharp-csharp-getstarted/run-apps1.png 
[42]: ./media/iot-hub-csharp-csharp-getstarted/run-apps2.png 
[lnk-c2d-tutorial]: iot-hub-csharp-csharp-c2d.md 
[lnk-process-d2c-tutorial]: iot-hub-csharp-csharp-process-d2c.md 
[lnk-upload-tutorial]: iot-hub-csharp-csharp-file-upload.md 
[lnk-hub-overview]: iot-hub-what-is-iot-hub.md 
[lnk-hub-guidance]: iot-hub-guidance.md 
[lnk-hub-dev-guide]: iot-hub-devguide.md 
[lnk-supported-devices]: https://github.com/Azure/azure-iot-sdks/blob/master/doc/tested_configurations.md 
[lnk-dev-center]: http://www.azure.com/develop/iot 
[lnk-hub-sdks]: iot-hub-sdks-summary.md 
[lnk-free-trial]: http://azure.microsoft.com/pricing/free-trial/ 
[lnk-resource-groups]: resource-group-portal.md 
[lnk-portal]: https://portal.azure.com/ 

