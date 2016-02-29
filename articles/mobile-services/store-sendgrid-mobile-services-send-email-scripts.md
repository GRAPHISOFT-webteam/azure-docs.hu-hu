<properties
    pageTitle="使用 SendGrid 傳送電子郵件 | Microsoft Azure"
    description="了解如何使用 SendGrid 服務，從 Azure 行動服務應用程式傳送電子郵件。"
    services="mobile-services"
    documentationCenter=""
    authors="Erikre"
    manager="sendgrid"
    editor=""/>


<tags 
    ms.service="mobile-services" 
    ms.workload="mobile" 
    ms.tgt_pltfrm="na" 
    ms.devlang="multiple" 
    ms.topic="article" 
    ms.date="11/30/2015" 
    ms.author="Erikre"/>


# 使用 SendGrid 從行動服務傳送電子郵件

[AZURE.INCLUDE [mobile-service-note-mobile-apps](../../includes/mobile-services-note-mobile-apps.md)]

&nbsp;


本主題示範如何將電子郵件功能新增至您的行動服務。 在本教學課程中，您將新增伺服器端程式碼以使用 SendGrid 傳送電子郵件。 完成後，行動服務就會在每次插入記錄時傳送電子郵件。

SendGrid 是 [雲端架構電子郵件服務]，能提供可靠的 [交易式電子郵件傳遞]、擴充性和即時分析，以及有彈性的 API 來輕鬆進行自訂整合。 如需詳細資訊，請參閱 <http://sendgrid.com>。

本教學課程將逐步引導您完成下列啟用電子郵件功能的基本步驟：

1. [建立 SendGrid 帳戶]
2. [新增指令碼來傳送電子郵件]
3. [插入資料以接收電子郵件]

本教學課程會以行動服務快速入門為基礎。 在開始本教學課程之前，您必須先完成 [開始使用行動服務]。

## <a name="sign-up"></a>建立新的 SendGrid 帳戶

[AZURE.INCLUDE [sendgrid-sign-up](../../includes/sendgrid-sign-up.md)]

## <a name="add-script"></a>註冊傳送電子郵件的新指令碼

1. 登入 [Azure 傳統入口網站] 中，按一下 **行動電話服務**, ，然後按一下您的行動服務。

2. 在 Azure 傳統入口網站中，按一下 [ **資料** 標籤，然後按一下 **TodoItem** 資料表。

    ![][1]

3. 在 **todoitem**, ，按一下 [ **指令碼** 索引標籤並選取 **插入**。

    ![][2]

    這會顯示函式中發生插入時所叫用 **TodoItem** 資料表。

4. 使用下列程式碼來取代插入函數：

        var SendGrid = require('sendgrid').SendGrid;

        function insert(item, user, request) {
            request.execute({
                success: function() {
                    // After the record has been inserted, send the response immediately to the client
                    request.respond();
                    // Send the email in the background
                    sendEmail(item);
                }
            });

            function sendEmail(item) {
                var sendgrid = new SendGrid('**username**', '**password**');

                sendgrid.send({
                    to: '**email-address**',
                    from: '**from-address**',
                    subject: 'New to-do item',
                    text: 'A new to-do was added: ' + item.text
                }, function(success, message) {
                    // If the email failed to send, log it as an error so we can investigate
                    if (!success) {
                        console.error(message);
                    }
                });
            }
        }

5. 以正確的值取代上述指令碼中的預留位置：

    - **_使用者名稱_ 和 _密碼_**: 的 SendGrid 認證您中識別 [建立 SendGrid 帳戶]。

    - **_電子郵件地址_**: 若要傳送電子郵件地址。 在實際的應用程式中，您可以使用資料表來儲存和擷取電子郵件地址。 在測試應用程式時，請只使用您自己的電子郵件地址。

    - **_從位址_**: 源自電子郵件地址。 請考慮使用屬於貴組織的已登錄網域位址。

     > [AZURE.NOTE] 如果您沒有註冊的網域，您可以改為使用您的行動服務，格式的網域 *@ 通知_您行動服務_.azure-mobile.net*。 不過，傳送至您行動服務網域的訊息將會被忽略。

6. 按一下 [ **儲存** ] 按鈕。 您現在已設定指令碼來傳送電子郵件每次一筆記錄插入 **TodoItem** 資料表。

## <a name="insert-data"></a>插入測試資料以接收電子郵件

1. 在用戶端應用程式專案中，執行快速入門應用程式。

    本主題示範的是 Windows 市集版本的快速入門。

2. 在應用程式中鍵入文字 **Insert a TodoItem**, ，然後按一下 [ **儲存**。

    ![][3]

3. 請注意，您會收到一封電子郵件，如下方的通知所示。

    ![][4]

    恭喜，您已經成功地將您的行動服務設定為使用 SendGrid 傳送電子郵件。

## <a name="nextsteps"> </a>後續步驟

現在您已經了解將 SendGrid 電子郵件服務與行動服務搭配使用有多麼簡單，
接下來請參考下列連結來深入了解 SendGrid。

-   SendGrid API 文件：
    <https://sendgrid.com/docs>
-   Azure 客戶的 SendGrid 特別優惠：
    <https://sendgrid.com/windowsazure.html>

<!-- Anchors. -->
[Create a SendGrid account]: #sign-up
[Add a script to send email]: #add-script
[Insert data to receive email]: #insert-data

<!-- Images. -->
[1]: ./media/store-sendgrid-mobile-services-send-email-scripts/mobile-portal-data-tables.png
[2]: ./media/store-sendgrid-mobile-services-send-email-scripts/mobile-insert-script-push2.png
[3]: ./media/store-sendgrid-mobile-services-send-email-scripts/mobile-quickstart-push1.png
[4]: ./media/store-sendgrid-mobile-services-send-email-scripts/mobile-receive-email.png

<!-- URLs. -->
[Get started with Mobile Services]: /develop/mobile/tutorials/get-started
[sign up page]: https://sendgrid.com/windowsazure.html
[Multiple User Credentials page]: https://sendgrid.com/credentials
[Azure classic portal]: https://manage.windowsazure.com/
[cloud-based email service]: https://sendgrid.com/email-solutions
[transactional email delivery]: https://sendgrid.com/transactional-email



