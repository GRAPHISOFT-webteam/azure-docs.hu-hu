已註冊新的插入指令檔，該指令檔會在插入新 Todo 項目時產生 SAS。

0. 如果您尚未建立儲存體帳戶，請參閱 [如何建立儲存體帳戶](../storage/storage-create-storage-account.md)。

1. 在 [Azure 傳統入口網站](https://manage.windowsazure.com/), ，按一下 [ **儲存體**, 、 儲存體帳戶，然後按一下 [ **管理金鑰**。 

2. 請記下的 **儲存體帳戶名稱** 和 **便捷鍵**。

    ![](./media/mobile-services-configure-blob-storage/mobile-blob-storage-account-keys.png)

3. 在您的行動服務中，按一下 [ **設定** 索引標籤上，向下捲動至 **應用程式設定** 輸入 **名稱** 和 **值** 您從儲存體帳戶中，取得下列各項的配對，然後按一下 [ **儲存**。

    + `STORAGE_ACCOUNT_NAME`
    + `STORAGE_ACCOUNT_ACCESS_KEY`

    ![](./media/mobile-services-configure-blob-storage/mobile-blob-storage-app-settings.png)

    儲存體帳戶存取金鑰會以加密方式儲存在應用程式設定中。 您可以在執行期間從任何伺服器指令碼存取此金鑰。 如需詳細資訊，請參閱 [應用程式設定]。

4. 在 [設定] 索引標籤，確定 [動態結構描述](http://msdn.microsoft.com/library/windowsazure/b6bb7d2d-35ae-47eb-a03f-6ee393e170f7) 已啟用。 您需要啟用動態結構描述，才能將新資料行加入 TodoItem 資料表。 請避免在任何實際執行服務中啟用動態結構描述。

4. 按一下 [ **資料** 標籤，然後按一下 **TodoItem** 資料表。 

5.  在 **todoitem**, ，按一下 [ **指令碼** 索引標籤並選取 **插入**, ，以下列程式碼，來取代 insert 函數，然後按一下 [ **儲存**:

        var azure = require('azure');
        var qs = require('querystring');
        var appSettings = require('mobileservice-config').appSettings;
        
        function insert(item, user, request) {
            // Get storage account settings from app settings. 
            var accountName = appSettings.STORAGE_ACCOUNT_NAME;
            var accountKey = appSettings.STORAGE_ACCOUNT_ACCESS_KEY;
            var host = accountName + '.blob.core.windows.net';
        
            if ((typeof item.containerName !== "undefined") && (
            item.containerName !== null)) {
                // Set the BLOB store container name on the item, which must be lowercase.
                item.containerName = item.containerName.toLowerCase();
        
                // If it does not already exist, create the container 
                // with public read access for blobs.        
                var blobService = azure.createBlobService(accountName, accountKey, host);
                blobService.createContainerIfNotExists(item.containerName, {
                    publicAccessLevel: 'blob'
                }, function(error) {
                    if (!error) {
        
                        // Provide write access to the container for the next 5 mins.        
                        var sharedAccessPolicy = {
                            AccessPolicy: {
                                Permissions: azure.Constants.BlobConstants.SharedAccessPermissions.WRITE,
                                Expiry: new Date(new Date().getTime() + 5 * 60 * 1000)
                            }
                        };
        
                        // Generate the upload URL with SAS for the new image.
                        var sasQueryUrl = 
                        blobService.generateSharedAccessSignature(item.containerName, 
                        item.resourceName, sharedAccessPolicy);
        
                        // Set the query string.
                        item.sasQueryString = qs.stringify(sasQueryUrl.queryString);
        
                        // Set the full path on the new new item, 
                        // which is used for data binding on the client. 
                        item.imageUri = sasQueryUrl.baseUrl + sasQueryUrl.path;
        
                    } else {
                        console.error(error);
                    }
                    request.execute();
                });
            } else {
                request.execute();
            }
        }

    這會以新指令碼取代 TodoItem 資料表發生插入時所叫用的函數。 這個新指令碼會對插入產生新的 SAS (有效期間為 5 分鐘)，以及將產生的 SAS 值指派給所傳回項目的 `sasQueryString` 屬性。 `imageUri` 屬性也會設定為新 BLOB 的資源路徑，以便於繫結期間在用戶端 UI 中顯示映像。

    >[AZURE.NOTE] 此程式碼會為個別 blob 建立 SAS。 如果您需要將多個 blob 上傳至容器，使用相同的 SAS，可以改為呼叫 [method<](http://go.microsoft.com/fwlink/?LinkId=390455)</a> 空白的 blob 資源名稱，就像這樣: 
    >                 
    >     blobService.generateSharedAccessSignature(containerName, '', sharedAccessPolicy);

接著，您將會更新快速入門應用程式，以使用插入時產生的 SAS 來新增映像上傳功能。
 
<!-- Anchors. -->

<!-- Images. -->

<!-- URLs. -->
[App settings]: http://msdn.microsoft.com/library/windowsazure/b6bb7d2d-35ae-47eb-a03f-6ee393e170f7

