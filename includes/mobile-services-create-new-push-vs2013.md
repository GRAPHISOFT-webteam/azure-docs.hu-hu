下列步驟將向 Windows 市集註冊您的應用程式、設定您的行動服務以啟用推播通知，以及將您的程式碼加入至您的應用程式以對通知中心註冊裝置通道。 Visual Studio 2013 使用您提供的認證連接至 Azure 與 Windows 市集。 

1. 在 Visual Studio 2013，開啟 [方案總管] 中，以滑鼠右鍵按一下 Windows 市集應用程式專案，按一下 **新增** 然後 **推播通知...**。 

    ![Visual Studio 2013 中的 [加入推播通知精靈]](./media/mobile-services-create-new-push-vs2013/mobile-add-push-notifications-vs2013.png)

    這樣會啟動 [加入推播通知] 精靈。

2. 按一下 [ **下一步**, 、 登入您的 Windows 市集帳戶，然後中提供名稱 **保留新名稱** 按一下 **保留**。

    這樣會建立新的應用程式註冊。

3. 按一下新的登錄中 **應用程式名稱** 清單，然後按一下 **下一步**。

4. 在 **選取服務** 頁面上，按一下您的行動服務的名稱，然後按一下 [ **下一步** 和 **完成**。 

    您的行動服務所使用的通知中心已透過 Windows 通知服務 (WNS) 註冊更新。 您現在可以使用 Azure 通知中心，透過 WNS 從行動服務傳送通知至您的應用程式。 

    >[AZURE.NOTE]本教學課程示範由行動服務後端傳送通知。 您可以使用相同的通知中心註冊，來從任何後端服務傳送通知。 如需詳細資訊，請參閱 [通知中心概觀](http://msdn.microsoft.com/library/azure/jj927170.aspx)。

5. 當您完成精靈，新 **推入安裝程式已接近完成** Visual Studio 中開啟頁面。 此頁面提供與本教學課程相異的設定行動服務專案傳送通知之替代方法的詳細資料。 

    [加入推播通知精靈] 加入至您的通用 Windows 應用程式解決方案之程式碼為平台特定。 在本節中，您將會透過分享行動服務用戶端程式碼移除此備援，如此能使通用應用程式更容易維護。  

<!-- URLs. -->
[Get started with Mobile Services]: /develop/mobile/tutorials/get-started/
[Get started with data]: /develop/mobile/tutorials/get-started-with-data-dotnet/
