* 請依照下列步驟，在 [在伺服器上安裝用戶端 SSL 簽署身分識別](https://developer.apple.com/library/ios/documentation/IDEs/Conceptual/AppDistributionGuide/ConfiguringPushNotifications/ConfiguringPushNotifications.html#//apple_ref/doc/uid/TP40012582-CH32-SW15) 匯出至.p12 檔案的上一個步驟中下載的憑證。

* 在 Azure 傳統入口網站中，按一下 [行動服務]**** > 您的應用程式 > [推送]**** 索引標籤 > [Apple 推送通知設定]**** > "[上傳]****。 上傳 .p12 檔案，並確定已選取正確的 [**模式**] (不是 [沙箱] 就是 [實際執行]，這對應於您產生的用戶端 SSL 憑證為 [開發] 或 [配送])。 您的行動服務現在已設定成在 iOS 上使用推播通知！





