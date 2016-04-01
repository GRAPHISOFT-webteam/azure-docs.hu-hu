<properties 
    pageTitle="如何在 Azure RemoteApp 中重新導向 QuickBooks？ | Microsoft Azure" 
    description="了解如何在 Azure RemoteApp 中對 USB 裝置使用重新導向。" 
    services="remoteapp" 
    documentationCenter="" 
    authors="lizap" 
    manager="mbaldwin" />

<tags 
    ms.service="remoteapp" 
    ms.workload="compute" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="11/04/2015" 
    ms.author="elizapo" />



# 如何在 Azure RemoteApp 中重新導向 QuickBooks？

裝置重新導向可讓使用者在 Azure RemoteApp 中透過應用程式使用連接到電腦或平板電腦的 USB 裝置。 例如，如果您透過 Azure RemoteApp 共用 Skype，您的使用者必須能夠使用其裝置相機。

繼續執行其他步驟之前，請確定您已閱讀中的 USB 重新導向資訊 [Azure RemoteApp 中使用重新導向](remoteapp-redirection.md)。 不過，建議使用 nusbdevicestoredirect: * 不適用於 USB 網路攝影機，而且可能不適用於某些 USB 印表機或 [multifunctional 的 USB 裝置。 基於設計和安全性理由，Azure RemoteApp 系統管理員必須先依裝置類別 GUID 或依裝置執行個體識別碼啟用重新導向，您的使用者才能使用這些裝置。

雖然本文討論 web 相機重新導向，您可以使用類似的方法來重新導向 USB 印表機以及其他 USB [multifunctional 裝置不會重新導向的 **nusbdevicestoredirect:*** 命令。

## USB 裝置的重新導向選項
如同遠端桌面服務可用的機制一樣，Azure RemoteApp 會使用非常類似的機制重新導向 USB 裝置。 基礎技術可讓您選擇正確的重新導向方法提供的裝置，若要取得這兩者的最高層級和 RemoteFX USB 裝置重新導向使用 **usbdevicestoredirect:s:** 命令。 此命令有四個元素：

| 處理順序 | 參數           | 說明                                                                                                                |
|------------------|---------------------|----------------------------------------------------------------------------------------------------------------------------|
| 1                | *                   | 選取高層級重新導向未挑選的所有裝置。 附註：根據設計，* 不適用於 USB 網路攝影機。  |
|                  | {Device class GUID} | 選取所有符合指定之裝置安裝類別的裝置。                                                           |
|                  | USB\InstanceID      | 選取針對指定之執行個體識別碼指定的 USB 裝置                                                                  |
| 2                | -USB\Instance ID    | 移除指定之裝置的重新導向設定。                                                                 |

## 使用裝置類別 GUID 重新導向 USB 裝置
尋找可用於重新導向的裝置類別 GUID 的方式有兩種。 

第一個選項是使用 [System-Defined 裝置安裝類別提供給供應商](https://msdn.microsoft.com/library/windows/hardware/ff553426.aspx)。 選擇最符合連接到本機電腦之裝置的類別。 若為數位相機，這可能是 [影像裝置] 類別或 [視訊擷取裝置] 類別。 您必須以裝置類別進行一些實驗，以尋找適用於本機連接 USB 裝置 (在我們的案例中為網路攝影機) 的正確類別 GUID。

比較好的方法或第二個選項，就是遵循下列步驟來尋找特定裝置類別 GUID：

1. 開啟 [裝置管理員]，找出此裝置會被重新導向，以滑鼠右鍵按一下，然後再開啟屬性。
![開啟裝置管理員](./media/remoteapp-usbredir/ra-devicemanager.png)
2. 在 **詳細資料** 索引標籤上，選擇屬性 **類別 Guid**。 它會出現的值是裝置的該類型類別 GUID。
![數位相機內容](./media/remoteapp-usbredir/ra-classguid.png)
3. 使用類別 Guid 值來重新導向相符的裝置。

例如：

        Set-AzureRemoteAppCollection -CollectionName <collection name> -CustomRdpProperty "nusbdevicestoredirect:s:<Class Guid value>"

您可以在相同 Cmdlet 中合併多個裝置重新導向。 例如：若要重新導向本機儲存體和 USB 網路攝影機，Cmdlet 會如下所示：

        Set-AzureRemoteAppCollection -CollectionName <collection name> -CustomRdpProperty "drivestoredirect:s:*`nusbdevicestoredirect:s:<Class Guid value>"

當您依類別 GUID 設定裝置重新導向時，指定的集合中符合該類別 GUID 的所有裝置都會重新導向。 比方說，如果本機網路上有多部具有相同 USB 網路攝影機的電腦，您可以執行單一 Cmdlet 來重新導向所有網路攝影機。

## 使用裝置執行個體識別碼重新導向 USB 裝置

如果您想要更細微的控制而且想要控制每個裝置的重新導向，您可以使用 **USB\InstanceID** 重新導向參數。

此方法最困難的部分是尋找 USB 裝置執行個體識別碼。 您需要有電腦和特定 USB 裝置的存取權。 接著，遵循下列步驟：

1. 中所述，啟用遠端桌面工作階段中的裝置重新導向 [如何使用我的裝置和資源的遠端桌面工作階段中？](http://windows.microsoft.com/en-us/windows7/How-can-I-use-my-devices-and-resources-in-a-Remote-Desktop-session)
2. 開啟 [遠端桌面連線，然後按一下 **顯示選項**。
3. 按一下 [ **存** RDP 檔案來儲存目前的連接設定。  
    ![將設定儲存為 RDP 檔案](./media/remoteapp-usbredir/ra-saveasrdp.png)
4. 選擇檔案名稱和位置 (例如 “MyConnection.rdp” 和 “This PC\Documents”)，然後儲存檔案。
5. 使用文字編輯器開啟 MyConnection.rdp 檔案，並尋找您要重新導向之裝置的執行個體識別碼。

現在，在下列 Cmdlet 中使用執行個體識別碼：

    Set-AzureRemoteAppCollection -CollectionName <collection name> -CustomRdpProperty "nusbdevicestoredirect:s: USB\<Device InstanceID value>"



### 幫我們來協助您 
您知道除了評比這篇文章以及在下面留言以外，您可以變更文件本身嗎？ 有所遺漏？ 有所錯誤？ 我是否撰寫了令人混淆的內容？ 向上捲動，然後按一下 [ **GitHub 上的編輯** 若要變更-供檢閱，這些會變成給我們，接著，我們登入它們，就會看到您的變更和改進這裡。

