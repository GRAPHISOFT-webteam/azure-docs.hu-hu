<properties 
    pageTitle="開始在 Xamarin Android 中使用行動應用程式的驗證" 
    description="了解如何使用行動應用程式透過眾多識別提供者驗證 Xamarin Android 應用程式使用者，包括 AAD、Google、Facebook、Twitter 和 Microsoft。" 
    services="app-service\mobile" 
    documentationCenter="xamarin" 
    authors="mattchenderson" 
    manager="dwrede" 
    editor=""/>

<tags 
    ms.service="app-service-mobile" 
    ms.workload="mobile" 
    ms.tgt_pltfrm="mobile-xamarin-android" 
    ms.devlang="dotnet" 
    ms.topic="article" 
    ms.date="12/07/2015" 
    ms.author="mahender"/>


# 將驗證新增至 Xamarin.Android 應用程式

[AZURE.INCLUDE [app-service-mobile-selector-get-started-users](../../includes/app-service-mobile-selector-get-started-users.md)]
&nbsp;  
[AZURE.INCLUDE [app-service-mobile-note-mobile-services](../../includes/app-service-mobile-note-mobile-services.md)]

本主題說明如何從用戶端應用程式驗證行動應用程式的使用者。 在本教學課程中，您會使用 Azure 行動應用程式所支援的身分識別提供者將驗證新增至快速入門專案。 在行動應用程式中成功驗證並授權之後，就會顯示使用者識別碼值。

本教學課程以行動應用程式快速入門為基礎。 您還必須先完成教學課程 [建立 Xamarin.Android 應用程式]。 如果您不要使用下載的快速入門伺服器專案，必須將驗證擴充套件新增至您的專案。 如需伺服器擴充功能套件的詳細資訊，請參閱 [Azure 行動應用程式使用.NET 後端伺服器 SDK](app-service-mobile-dotnet-backend-how-to-use-server-sdk.md)。

## <a name="register"></a>註冊應用程式進行驗證，並設定應用程式服務

[AZURE.INCLUDE [app-service-mobile-register-authentication](../../includes/app-service-mobile-register-authentication.md)]

## <a name="permissions"></a>限制已驗證的使用者權限

[AZURE.INCLUDE [app-service-mobile-restrict-permissions-dotnet-backend](../../includes/app-service-mobile-restrict-permissions-dotnet-backend.md)]

在 Visual Studio 或 Xamarin Studio 中，在裝置或模擬器上執行用戶端專案。 確認在應用程式啟動後，發生狀態代碼 401 (未經授權) 的未處理例外狀況。 這是因為應用程式嘗試以未驗證的使用者身分存取您的行動應用程式程式碼。 *TodoItem* 資料表現在需要驗證。

接下來，您將會更新用戶端應用程式，利用已驗證的使用者身分來要求行動應用程式後端的資源。

## <a name="add-authentication"></a>將驗證新增至應用程式

應用程式已更新，要求使用者在資料顯示前點選 [登入]**** 按鈕並驗證。

1. 將下列程式碼加入 **TodoActivity** 類別：

     // Define a authenticated user.
     private MobileServiceUser user;
     private async Task<bool> Authenticate()
     {
             var success = false;
             try
             {
                 // Sign in with Facebook login using a server-managed flow.
                 user = await client.LoginAsync(this,
                     MobileServiceAuthenticationProvider.Facebook);
                 CreateAndShowDialog(string.Format("you are now logged in - {0}",
                     user.UserId), "Logged in!");
    
                 success = true;
             }
             catch (Exception ex)
             {
                 CreateAndShowDialog(ex, "Authentication failed");
             }
             return success;
     }
    
     [Java.Interop.Export()]
     public async void LoginUser(View view)
     {
         // Load data only after authentication succeeds.
         if (await Authenticate())
         {
             //Hide the button after authentication succeeds. 
             FindViewById<Button>(Resource.Id.buttonLoginUser).Visibility = ViewStates.Gone;
    
             // Load the data.
             OnRefreshItemsSelected();
         }
     }

 這會建立一個新方法以驗證使用者，以及建立新 [登入]**** 按鈕的方法處理常式。 上述範例程式碼中的使用者是使用 Facebook 登入進行驗證。 對話方塊會在驗證後用來顯示使用者識別碼。
 > [AZURE.NOTE] 如果您使用的身分識別提供者不是 Facebook，請將傳遞給上述 **LoginAsync** 的值變更為下列其中之一：_MicrosoftAccount_、_Twitter_、_Google_ 或 _WindowsAzureActiveDirectory_。

3. 在 **OnCreate** 方法中，刪除或註解下列程式碼行：

        OnRefreshItemsSelected ();

4. 在 Activity_To_Do.axml 檔案中，在現有的*AddItem* 按鈕之前加入下列 *LoginUser* 按鈕定義：

        <Button
            android:id="@+id/buttonLoginUser"
            android:layout_width="wrap_content"
            android:layout_height="wrap_content"
            android:onClick="LoginUser"
            android:text="@string/login_button_text" />

5. 將下列元素新增到 Strings.xml 資源檔：

        <string name="login_button_text">Sign in</string> 

6. 在 Visual Studio 或 Xamarin Studio 中，在裝置或模擬器上執行用戶端專案，並使用您選擇的身分識別提供者登入。

    當您成功登入後，應用程式將會顯示您的登入識別碼以及 todo 項目的清單，您可以對資料進行更新。




[create a xamarin.android app]: app-service-mobile-xamarin-android-get-started.md 

