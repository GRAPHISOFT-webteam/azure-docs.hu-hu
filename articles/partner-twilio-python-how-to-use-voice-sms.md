<properties
    pageTitle="如何使用 Twilio for Voice and SMS (PHP) | Microsoft Azure"
    description="了解如何在 Azure 上使用 Twilio API 服務撥打電話及傳送簡訊。 程式碼範例以 PHP 撰寫。"
    services=""
    documentationCenter="python"
    authors="devinrader"
    manager="twilio"
    editor=""/>

<tags
    ms.service="multiple"
    ms.workload="na"
    ms.tgt_pltfrm="na"
    ms.devlang="python" 
    ms.topic="article"
    ms.date="02/19/2015"
    ms.author="MicrosoftHelp@twilio.com"/>





# 如何在 PHP 中透過 Twilio 使用語音和簡訊功能
本指南示範如何在 Azure 上透過 Twilio API 服務執行常見的程式設計工作。 涵蓋的案例包括打電話和傳送簡訊 (SMS)。 如需 Twilio 和應用程式中使用語音和 SMS 的詳細資訊，請參閱 [下一步](#NextSteps) 一節。

## <a id="WhatIs"></a>什麼是 Twilio？
Twilio 正在形塑商業環境的未來，可讓開發人員將語音、VoIP 和訊息傳送內嵌到應用程式中。 它們將雲端、全球化環境中所需的整個基礎結構虛擬化，透過 Twilio 通訊 API 平台來揭露基礎結構。 輕鬆就可建立和擴充應用程式。 享受隨收隨付定價的彈性和雲端可靠性的好處。

**Twilio 語音** 可讓應用程式撥打和接聽電話。 **Twilio SMS** 可讓您的應用程式收發簡訊。 **Twilio 用戶端** 可讓您從任何電話、 平板電腦或瀏覽器的 VoIP 電話，且支援 WebRTC。

## <a id="Pricing"></a>Twilio 定價和特別優惠

升級 Twilio 帳戶的 Azure 客戶，可特別獲贈價值 $10 的 Twilio 點數。 此 Twilio 點數可用來折抵任何 Twilio 使用量 ($10 點數相當於最多傳送 1,000 則簡訊，或最多接收 1000 分鐘的撥入語音，視電話號碼所在地點或通話目的地而定)。 請至 [ahoy.twilio.com/azure] 兌換 Twilio 點數來開始使用。

Twilio 是隨收隨付的服務。 不需要設定費，隨時都可結清帳戶。 您可以找到更多詳細資料，在 [Twilio 定價] [twilio_pricing]。

## <a id="Concepts"></a>概念
Twilio API 是一套為應用程式提供語音和簡訊功能的 RESTful API。 用戶端程式庫有多種語言。如需清單，請參閱 [Twilio API 程式庫] [twilio_libraries]。

Twilio API 的兩大重點是 Twilio 動詞和 Twilio 標記語言 (TwiML)。

### <a id="Verbs"></a>Twilio 動詞
API 採用 Twilio 動詞。例如， **& l t;Y & gt;** 動詞指示 Twilio 在通話中用語音傳遞訊息上呼叫。

以下是 Twilio 動詞清單。 請透過 [Twilio Markup Language documentation][http://www.twilio.com/docs/api/twiml] 了解其他動詞和功能。

* **& l t; 撥 & gt;**: 接通另一支電話。
* **& l t;收集 & gt;**: 收集電話按鍵上輸入的數字。
* **& l t;掛斷 & gt;**: 結束通話。
* **& l t;播放 & gt;**: 播放音訊檔案。
* **& l t;暫停 & gt;**: 靜候指定的秒數。
* **& l t;記錄和 gt;**: 錄製來電者的語音並傳回含有錄音的檔案 URL。
* **& l t;重新導向 & gt;**: 將通話或簡訊的控制權移轉至 TwiML 不同的 URL。
* **& l t;拒絕 & gt;**: 拒絕 Twilio 號碼的來電而不計費
* **& l t;Y & gt;**: 將文字轉換成語音的呼叫。
* **& l t;Sms & gt;**: 傳送簡訊。

### <a id="TwiML"></a>TwiML
TwiML 是以 Twilio 動詞為基礎的一組 XML 指令，可指示 Twilio 如何處理來電或簡訊。

例如，下列 TwiML 會將轉換文字 **Hello World** 成語音。

    <?xml version="1.0" encoding="UTF-8" ?>
    <Response>
       <Say>Hello World</Say>
    </Response>

當應用程式呼叫 Twilio API 時，其中一個 API 參數是傳回 TwiML 回應的 URL。 在開發用途上，您可以使用 Twilio 提供的 URL 來提供應用程式所使用的 TwiML 回應。 您也可以裝載您自己的 Url 來產生 TwiML 回應，以及另一個選項是使用 **TwiMLResponse** 物件。

如需 Twilio 動詞命令、 其屬性和 TwiML 的詳細資訊，請參閱 [TwiML] [twiml]。 如需 Twilio API 的詳細資訊，請參閱 [Twilio API] [twilio_api]。

## <a id="CreateAccount"></a>建立 Twilio 帳戶
準備取得 Twilio 帳戶時，註冊 [試用 Twilio] [try_twilio]。 您可以先使用免費帳戶，稍後再升級帳戶。

註冊 Twilio 帳戶時，您會收到帳戶識別碼和驗證權杖。 兩者皆為呼叫 Twilio API 所需。 為了防止未經授權存取您的帳戶，您妥善保管驗證權杖。 您的帳戶識別碼和驗證權杖會在可檢視 [Twilio 帳戶頁面] [twilio_account], ，欄位中 **帳戶 SID** 和 **AUTH TOKEN**, 分別。

## <a id="create_app"></a>建立 PHP 應用程式
使用 Twilio 服務且執行於 Azure 的 PHP 應用程式，與其他使用 Twilio 服務的 PHP 應用程式並無不同。 雖然 Twilio 服務是以 REST 為基礎，並且可以數種方式從 PHP 撥打，本文將著重在如何使用 Twilio 服務與 [GitHub PHP 的 Twilio 程式庫][twilio_php]。 如需有關如何使用適用於 PHP 的 Twilio 程式庫的詳細資訊，請參閱 [http://readthedocs.org/docs/twilio-php/en/latest/index.html][twilio_lib_docs]。

建置和部署至 Azure Twilio/PHP 應用程式的詳細的指示位於 [如何在 Azure 上的 PHP 應用程式中進行通話使用 Twilio][howto_phonecall_php]。

## <a id="configure_app"></a>設定應用程式以使用 Twilio 程式庫
您可以透過兩種方式設定應用程式，以使用適用於 PHP 的 Twilio 程式庫：

1. 下載適用於從 GitHub 的 PHP 的 Twilio 程式庫 ([https://github.com/twilio/twilio-php][twilio_php])，並新增 **服務** 目錄新增至您的應用程式。

    -或-

2. 以 PEAR 封裝的形式，安裝適用於 PHP 的 Twilio 程式庫。 您可以使用下列命令進行此安裝：

        $ pear channel-discover twilio.github.com/pear
        $ pear install twilio/Services_Twilio

一旦您安裝適用於 PHP 的 Twilio 程式庫，您就可以新增 **require_once** 陳述式參考的程式庫 PHP 檔案頂端:

        require_once 'Services/Twilio.php';

如需詳細資訊，請參閱 [https://github.com/twilio/twilio-php/blob/master/README.md][twilio_github_readme]。

## <a id="howto_make_call"></a>如何: 撥出電話
下圖顯示如何進行傳出呼叫使用 **Services_Twilio** 類別。 此程式碼也使用 Twilio 提供的網站來傳回 Twilio 標記語言 (TwiML) 回應。 換成您的值 **從** 和 **至** 電話號碼，並確保您確認 **從** 電話號碼 Twilio 帳戶執行程式碼之前。

    // Include the Twilio PHP library.
    require_once 'Services/Twilio.php';

    // Library version.
    $version = "2010-04-01";

    // Set your account ID and authentication token.
    $sid = "your_twilio_account_sid";
    $token = "your_twilio_authentication_token";

    // The number of the phone initiating the the call.
    // (Must be previously validated with Twilio.)
    $from_number = "NNNNNNNNNNN";

    // The number of the phone receiving call.
    $to_number = "NNNNNNNNNNN";

    // Use the Twilio-provided site for the TwiML response.
    $url = "http://twimlets.com/message";

    // The phone message text.
    $message = "Hello world.";

    // Create the call client.
    $client = new Services_Twilio($sid, $token, $version);

    //Make the call.
    try
    {
        $call = $client->account->calls->create(
            $from_number,
            $to_number,
            $url.'?Message='.urlencode($message)
        );
    }
    catch (Exception $e)
    {
        echo 'Error: ' . $e->getMessage();
    }

如前所述，此程式碼使用 Twilio 提供的網站來傳回 TwiML 回應。 您可以改用您自己的網站提供 TwiML 回應;如需詳細資訊，請參閱 [如何提供 TwiML 回應從您自己的網站](#howto_provide_twiml_responses)。


- **請注意**: 若要疑難排解 SSL 憑證驗證錯誤，請參閱 [http://readthedocs.org/docs/twilio-php/en/latest/usage/rest.html][ssl_validation]


## <a id="howto_send_sms"></a>如何: 傳送簡訊
下圖顯示如何傳送 SMS 訊息使用 **Services_Twilio** 類別。  **從** 數目由 Twilio 提供給試用帳戶來傳送 SMS 訊息。  **到** 數目，必須驗證 Twilio 帳戶執行程式碼之前。

    // Include the Twilio PHP library.
    require_once 'Services/Twilio.php';

    // Library version.
    $version = "2010-04-01";

    // Set your account ID and authentication token.
    $sid = "your_twilio_account_sid";
    $token = "your_twilio_authentication_token";


    $from_number = "NNNNNNNNNNN"; // With trial account, texts can only be sent from your Twilio number.
    $to_number = "NNNNNNNNNNN";
    $message = "Hello world.";

    // Create the call client.
    $client = new Services_Twilio($sid, $token, $version);

    // Send the SMS message.
    try
    {
        $client->account->sms_messages->create($from_number, $to_number, $message);
    }
    catch (Exception $e)
    {
        echo 'Error: ' . $e->getMessage();
    }

## <a id="howto_provide_twiml_responses"></a>如何: 從您自己的網站提供 TwiML 回應
當您的應用程式開始呼叫 Twilio API 時，Twilio 會將要求傳送至 URL，然後應該會傳回 TwiML 回應。 前述範例使用 Twilio 提供的 URL [http://twimlets.com/message][twimlet_message_url]。 (雖然 TwiML 是設計給 Twilio 使用的，但您也可以在瀏覽器中檢視 TwiML。 例如，按一下 [http://twimlets.com/message][twimlet_message_url] 可查看空白 `<Response>` 元素; 另一個範例中，按一下 [http://twimlets.com/message?Message%5B0%5D=Hello%20World][twimlet_message_url_hello_world] 查看 `<Response>` 包含項目 `<Say>` 項目。)

除了使用 Twilio 提供的 URL 以外，您也可以建立自己的網站來傳回 HTTP 回應。 您可以使用任何語言建立會傳回 XML 回應的網站；本主題假設您將使用 PHP 建立 TwiML。

下列 PHP 頁面會產生 TwiML 回應，顯示在 **Hello World** 的呼叫。

    <?php
        header("content-type: text/xml");
        echo "<?xml version=\"1.0\" encoding=\"UTF-8\"?>\n";
    ?>
    <Response>
        <Say>Hello world.</Say>
    </Response>

從以上範例中可知，TwiML 回應只是一份 XML 文件。 適用於 PHP 的 Twilio 程式庫包含可為您產生 TwiML 的類別。 下列範例會產生回應，如上所示，但會使用 **Services\_Twilio\_Twiml** 適用於 PHP 的 Twilio 程式庫中的類別:

    require_once('Services/Twilio.php');

    $response = new Services_Twilio_Twiml();
    $response->say("Hello world.");
    print $response;

如需 TwiML 的詳細資訊，請參閱 [https://www.twilio.com/docs/api/twiml][twiml_reference]。

設定 PHP 頁面最多提供 TwiML 回應之後，請使用 PHP 頁面的 URL 作為傳遞到  `Services_Twilio->account->calls->create`  方法。 比方說，如果您有 Web 應用程式，名為 **MyTwiML** 部署至 Azure 代管的服務，而 PHP 頁面的名稱是 **mytwiml.php**, ，可以將 URL 傳遞到  **services_twilio->account->calls->create]-> [呼叫]-> [建立**  如下列範例所示:

    require_once 'Services/Twilio.php';

    $sid = "your_twilio_account_sid";
    $token = "your_twilio_authentication_token";
    $from_number = "NNNNNNNNNNN";
    $to_number = "NNNNNNNNNNN";
    $url = "http://<your_hosted_service>.cloudapp.net/MyTwiML/mytwiml.php";

    // The phone message text.
    $message = "Hello world.";

    $client = new Services_Twilio($sid, $token, "2010-04-01");

    try
    {
        $call = $client->account->calls->create(
            $from_number,
            $to_number,
            $url.'?Message='.urlencode($message)
        );
    }
    catch (Exception $e)
    {
        echo 'Error: ' . $e->getMessage();
    }

如需有關在 Azure 中使用 Twilio 的詳細資訊，請參閱 [如何在 Azure 上的 PHP 應用程式中進行通話使用 Twilio][howto_phonecall_php]。

## <a id="AdditionalServices"></a>如何: 使用其他 Twilio 服務
除了此處所示的範例以外，Twilio 還提供網頁式 API，方便您從 Azure 應用程式中充份利用其他 Twilio 功能。 完整的詳細資訊，請參閱 [Twilio API 文件] [twilio_api_documentation]。

## <a id="NextSteps"></a>後續步驟
了解基本的 Twilio 服務之後，請參考下列連結以取得更多資訊：

* [Twilio 安全性方針] [twilio_security_guidelines]
* [Twilio 作法指南與範例程式碼] [twilio_howtos]
* [Twilio 快速入門教學課程][twilio_quickstarts]
* [GitHub 上的 Twilio] [twilio_on_github]
* [洽詢 Twilio 支援] [twilio_support]

[twilio_php]: https://github.com/twilio/twilio-php
[twilio_lib_docs]: http://readthedocs.org/docs/twilio-php/en/latest/index.html
[twilio_github_readme]: https://github.com/twilio/twilio-php/blob/master/README.md
[ssl_validation]: http://readthedocs.org/docs/twilio-php/en/latest/usage/rest.html

[howto_phonecall_php]: http://windowsazure.com/documentation/articles/partner-twilio-php-make-phone-call
[twimlet_message_url]: http://twimlets.com/message
[twimlet_message_url_hello_world]: http://twimlets.com/message?Message%5B0%5D=Hello%20World
[twiml_reference]: https://www.twilio.com/docs/api/twiml
[twilio_pricing]: http://www.twilio.com/pricing

[twilio_libraries]: https://www.twilio.com/docs/libraries
[twiml]: http://www.twilio.com/docs/api/twiml
[twilio_api]: http://www.twilio.com/api
[try_twilio]: https://www.twilio.com/try-twilio
[twilio_account]:  https://www.twilio.com/user/account
[verify_phone]: https://www.twilio.com/user/account/phone-numbers/verified#
[twilio_api_documentation]: http://www.twilio.com/api
[twilio_security_guidelines]: http://www.twilio.com/docs/security
[twilio_howtos]: http://www.twilio.com/docs/howto
[twilio_on_github]: https://github.com/twilio
[twilio_support]: http://www.twilio.com/help/contact
[twilio_quickstarts]: http://www.twilio.com/docs/quickstart

