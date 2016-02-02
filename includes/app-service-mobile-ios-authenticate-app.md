1. 在 Mac 上，開啟 Xcode 中的 **QSTodoListViewController.m**，並加入下列方法。 如果您不使用 Facebook 作為識別提供者，請將 _facebook_ 變更為 _microsoftaccount_、_twitter_、_google_ 或 _windowsazureactivedirectory_。

     - (void) loginAndGetData
     {
         MSClient *client = self.todoService.client;
         if (client.currentUser != nil) {
             return;
         }
    
         [client loginWithProvider:@"facebook" controller:self animated:YES completion:^(MSUser *user, NSError *error) {
             [self refresh];
         }];
     }

2. 取代 `[自動重新整理]` 中 `viewDidLoad` 取代為下列:

        [self loginAndGetData];

3. 按下  **執行** 以啟動應用程式，然後登入。 當您登入時，應該能夠檢視待辦事項清單並進行更新。





