<properties 
    pageTitle="如何使用 Twilio for Voice and SMS (Java) | Microsoft Azure" 
    description="了解如何在 Azure 上使用 Twilio API 服務撥打電話及傳送簡訊。 程式碼範例以 Java 撰寫。" 
    services="" 
    documentationCenter="java" 
    authors="devinrader" 
    manager="twilio" 
    editor="mollybos"/>

<tags 
    ms.service="multiple" 
    ms.workload="na" 
    ms.tgt_pltfrm="na" 
    ms.devlang="Java" 
    ms.topic="article" 
    ms.date="11/25/2014" 
    ms.author="microsofthelp@twilio.com"/>

# 如何在 Java 中透過 Twilio 使用語音和簡訊功能

本指南示範如何在 Azure 上透過 Twilio API 服務執行常見的程式設計工作。 涵蓋的案例包括打電話和傳送簡訊 (SMS)。 如需 Twilio 和應用程式中使用語音和 SMS 的詳細資訊，請參閱 [下一步](#NextSteps) 一節。

## <a id="WhatIs"></a>什麼是 Twilio？
Twilio 是一種電話語音 Web 服務 API，能夠讓您使用現有的 Web 語言和技術建立語音和 SMS 應用程式。 Twilio 算是協力廠商服務 (並非 Azure 功能，也並非 Microsoft 產品)。

**Twilio 語音** 可讓應用程式撥打和接聽電話。 **Twilio SMS** 可讓應用程式撰寫和接收 SMS 訊息。 **Twilio 用戶端** 可讓應用程式中啟用語音通訊使用現有的網際網路連線，包括行動連線。

## <a id="Pricing"></a>Twilio 定價和特別優惠
Twilio 定價的相關資訊，還有在 [Twilio 定價] [twilio_pricing]。 Azure 客戶享有 [特別優惠][special_offer]: 免費 1000年簡訊或接聽 1000 分鐘。 若要註冊獲得這項優惠或取得詳細資訊，請造訪 [http://ahoy.twilio.com/azure][special_offer]。  

## <a id="Concepts"></a>概念
Twilio API 是一套為應用程式提供語音和簡訊功能的 RESTful API。 用戶端程式庫有多種語言。如需清單，請參閱 [Twilio API 程式庫] [twilio_libraries]。

Twilio API 的兩大重點是 Twilio 動詞和 Twilio 標記語言 (TwiML)。

### <a id="Verbs"></a>Twilio 動詞
API 採用 Twilio 動詞。例如， **& l t;Y & gt;** 動詞指示 Twilio 在通話中用語音傳遞訊息上呼叫。 

以下是 Twilio 動詞清單。

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
當您準備取得 Twilio 帳戶時時，請在註冊 [試用 Twilio] [try_twilio]。 您可以先使用免費帳戶，稍後再升級帳戶。

註冊 Twilio 帳戶時，您會收到帳戶識別碼和驗證權杖。 兩者皆為呼叫 Twilio API 所需。 為了防止未經授權存取您的帳戶，您妥善保管驗證權杖。 您的帳戶識別碼和驗證權杖會在可檢視 [Twilio 帳戶頁面] [twilio_account], ，欄位中 **帳戶 SID** 和 **AUTH TOKEN**, 分別。

## <a id="create_app"></a>建立 Java 應用程式
1. 取得 Twilio JAR 並將它加到您的 Java 組建路徑和 WAR 部署組件。 在 [https://github.com/twilio/twilio-java][twilio_java], ，您可以下載 GitHub 來源及建立您自己的 JAR，或下載預先建置的 JAR (含或不含相依性)。
2. 確定 JDK 的 **cacerts** 金鑰存放區包含 Equifax 安全憑證授權單位憑證有 MD5 指紋 67:CB:9 D: C0:13:24:8A:82:9B:B2:17:1E:D1:1B:EC:D4 (序號是 35:DE:F4:CF 和 SHA1 指紋是 D2:32:09:AD:23:D3:14:23:21:74:E4:0 D: 7F:9 D: 62:13:97:86:63:3A)。 這是憑證授權單位 (CA) 憑證 [https://api.twilio.com][twilio_api_service] 服務會使用 Twilio Api 時受到呼叫。 如需確定 JDK 的 **cacerts** 金鑰存放區包含正確 CA 憑證，請參閱 [新增憑證至 Java CA 憑證存放區][add_ca_cert]。

有提供在 java 中使用 Twilio 用戶端程式庫的詳細的指示 [如何在 Azure 上的 Java 應用程式中進行通話使用 Twilio][howto_phonecall_java]。

## <a id="configure_app"></a>設定應用程式以使用 Twilio 程式庫
在您的程式碼中，您可以加入 **匯入** 陳述式的 Twilio 封裝或想要使用您的應用程式類別的原始程式檔的頂端。 

對於 Java 原始程式檔：

    import com.twilio.*;
    import com.twilio.sdk.*;
    import com.twilio.sdk.resource.factory.*;
    import com.twilio.sdk.resource.instance.*;

對於 Java 伺服器頁面 (JSP) 原始程式檔：

    import="com.twilio.*"
    import="com.twilio.sdk.*"
    import="com.twilio.sdk.resource.factory.*"
    import="com.twilio.sdk.resource.instance.*"
您要使用的 Twilio 封裝或類別根據您 **匯入** 陳述式可能不同。

## <a id="howto_make_call"></a>如何: 撥出電話
下圖顯示如何進行傳出呼叫使用 **CallFactory** 類別。 此程式碼也使用 Twilio 提供的網站來傳回 Twilio 標記語言 (TwiML) 回應。 換成您的值 **從** 和 **至** 電話號碼，並確保您確認 **從** 電話號碼 Twilio 帳戶執行程式碼之前。

    // Use your account SID and authentication token instead
    // of the placeholders shown here.
    String accountSID = "your_twilio_account";
    String authToken = "your_twilio_authentication_token";

    // Create an instance of the Twilio client.
    TwilioRestClient client;
    client = new TwilioRestClient(accountSID, authToken);

    // Retrieve the account, used later to create an instance of the CallFactory.
    Account account = client.getAccount();

    // Use the Twilio-provided site for the TwiML response.
    String Url="http://twimlets.com/message";
    Url = Url + "?Message%5B0%5D=Hello%20World";

    // Place the call From, To and URL values into a hash map. 
    HashMap<String, String> params = new HashMap<String, String>();
    params.put("From", "NNNNNNNNNN"); // Use your own value for the second parameter.
    params.put("To", "NNNNNNNNNN");   // Use your own value for the second parameter.
    params.put("Url", Url);

    // Create an instance of the CallFactory class.
    CallFactory callFactory = account.getCallFactory();

    // Make the call.
    Call call = callFactory.create(params);

如需有關傳遞至參數 **CallFactory.create** 方法，請參閱 [http://www.twilio.com/docs/api/rest/making-calls][twilio_rest_making_calls]。

如前所述，此程式碼使用 Twilio 提供的網站來傳回 TwiML 回應。 您可以改用您自己的網站提供 TwiML 回應;如需詳細資訊，請參閱 [如何在 Azure 上的 Java 應用程式中提供 TwiML 回應](#howto_provide_twiml_responses)。

## <a id="howto_send_sms"></a>如何: 傳送簡訊
下圖顯示如何傳送 SMS 訊息使用 **SmsFactory** 類別。  **從** 號 **4155992671**, ，由 Twilio 提供給試用帳戶來傳送 SMS 訊息。  **到** 數目，必須驗證 Twilio 帳戶執行程式碼之前。

    // Use your account SID and authentication token instead
    // of the placeholders shown here.
    String accountSID = "your_twilio_account";
    String authToken = "your_twilio_authentication_token";

    // Create an instance of the Twilio client.
    TwilioRestClient client;
    client = new TwilioRestClient(accountSID, authToken);

    // Retrieve the account, used later to create an instance of the SmsFactory.
    Account account = client.getAccount();

    // Send an SMS message.
    MessageFactory messageFactory = account.getMessageFactory();
    
    List<NameValuePair> params = new ArrayList<NameValuePair>();
    params.add(new BasicNameValuePair("To", "+14159352345")); // Replace with a valid phone number for your account.
    params.add(new BasicNameValuePair("From", "+14158141829")); // Replace with a valid phone number for your account.
    params.add(new BasicNameValuePair("Body", "Where's Wallace?"));
    
    Message sms = messageFactory.create(params);
        
如需有關傳遞至參數 **SmsFactory.create** 方法，請參閱 [http://www.twilio.com/docs/api/rest/sending-sms][twilio_rest_sending_sms]。

## <a id="howto_provide_twiml_responses"></a>如何: 從您自己的網站提供 TwiML 回應
當您的應用程式開始呼叫 Twilio api 時，例如透過 **CallFactory.create** 方法時，Twilio 會將要求傳送至預期會傳回 TwiML 回應的 URL。 前述範例使用 Twilio 提供的 URL [http://twimlets.com/message][twimlet_message_url]。 (雖然 TwiML 是針對供 Web 服務使用而設計，但您可以在瀏覽器中檢視 TwiML。 例如，按一下 [ [http://twimlets.com/message][twimlet_message_url] 可查看空白 **& l t;回應 & gt;** 項目。另一個範例中，按一下 [http://twimlets.com/message?Message%5B0%5D=Hello%20World][twimlet_message_url_hello_world] 查看 **& lt;回應 & gt;** 包含項目 **& l t;Y & gt;** 項目。)

除了依賴 Twilio 提供的 URL，您也可以建立自己的 URL 網站來傳回 HTTP 回應。 您可以使用任何語言建立會傳回 HTTP 回應的網站；本主題假設您將該 URL 裝載在 JSP 頁面中。

下列 JSP 頁面結果中顯示的 TwiML 回應 **Hello World** 的呼叫。

    <%@ page contentType="text/xml" %>
    <Response> 
        <Say>Hello World</Say>
    </Response>

下列 JSP 頁面將引起 TwiML 有以下回應：說出一些文字、停頓數次，然後說出 Twilio API 版本和 Azure 角色名稱資訊。


    <%@ page contentType="text/xml" %>
    <Response> 
        <Say>Hello from Azure</Say>
        <Pause></Pause>
        <Say>The Twilio API version is <%= request.getParameter("ApiVersion") %>.</Say>
        <Say>The Azure role name is <%= System.getenv("RoleName") %>.</Say>
        <Pause></Pause>
        <Say>Good bye.</Say>
    </Response>

 **ApiVersion** 參數會出現在 Twilio 語音要求 (而非 SMS 要求)。 若要查看 Twilio 語音和 SMS 要求的可用要求參數，請參閱 <https://www.twilio.com/docs/api/twiml/twilio_request> 和 <https://www.twilio.com/docs/api/twiml/sms/twilio_request>, 分別。  **RoleName** 環境變數可供使用 Azure 部署的一部分。 (如果您想要新增自訂環境變數，以便他們可以從 **System.getenv**, ，環境變數一節，請參閱 [其他角色組態設定][misc_role_config_settings]。)

設定 JSP 頁面最多提供 TwiML 回應之後，請使用 JSP 頁面的 URL 作為傳遞到 **CallFactory.create** 方法。 比方說，如果您有 Web 應用程式，名為 MyTwiML 部署到 Azure 代管的服務，和 JSP 頁面的名稱是 mytwiml.jsp，可以將 URL 傳遞到 **CallFactory.create** 如下列所示:

    // Place the call From, To and URL values into a hash map. 
    HashMap<String, String> params = new HashMap<String, String>();
    params.put("From", "NNNNNNNNNN");
    params.put("To", "NNNNNNNNNN");
    params.put("Url", "http://<your_hosted_service>.cloudapp.net/MyTwiML/mytwiml.jsp");

    CallFactory callFactory = account.getCallFactory();
    Call call = callFactory.create(params);

進行 TwiML 回應的另一個選項是透過 **TwiMLResponse** 類別，可在 **twimlresponse** 封裝。

如需使用 Java 的 Azure 中使用 Twilio 的詳細資訊，請參閱 [如何在 Azure 上的 Java 應用程式中進行通話使用 Twilio][howto_phonecall_java]。

## <a id="AdditionalServices"></a>如何: 使用其他 Twilio 服務
除了此處所示的範例以外，Twilio 還提供網頁式 API，方便您從 Azure 應用程式中充份利用其他 Twilio 功能。 完整的詳細資訊，請參閱 [Twilio API 文件] [twilio_api_documentation]。

## <a id="NextSteps"></a>後續步驟
了解基本的 Twilio 服務之後，請參考下列連結以取得更多資訊：

* [Twilio 安全性方針] [twilio_security_guidelines]
* [Twilio 作法與範例程式碼] [twilio_howtos]
* [Twilio 快速入門教學課程][twilio_quickstarts] 
* [GitHub 上的 Twilio] [twilio_on_github]
* [洽詢 Twilio 支援] [twilio_support]

[twilio_java]: https://github.com/twilio/twilio-java
[twilio_api_service]: https://api.twilio.com
[add_ca_cert]: java-add-certificate-ca-store.md
[howto_phonecall_java]: partner-twilio-java-phone-call-example.md
[misc_role_config_settings]: http://msdn.microsoft.com/library/windowsazure/hh690945.aspx
[twimlet_message_url]: http://twimlets.com/message
[twimlet_message_url_hello_world]: http://twimlets.com/message?Message%5B0%5D=Hello%20World
[twilio_rest_making_calls]: http://www.twilio.com/docs/api/rest/making-calls
[twilio_rest_sending_sms]: http://www.twilio.com/docs/api/rest/sending-sms
[twilio_pricing]: http://www.twilio.com/pricing
[special_offer]: http://ahoy.twilio.com/azure
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

