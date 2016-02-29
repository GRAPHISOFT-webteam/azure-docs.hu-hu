<properties 
    pageTitle="媒體服務 REST API 概觀 | Microsoft Azure" 
    description="媒體服務 REST API 概觀" 
    services="media-services" 
    documentationCenter="" 
    authors="Juliako" 
    manager="dwrede" 
    editor=""/>

<tags 
    ms.service="media-services" 
    ms.workload="media" 
    ms.tgt_pltfrm="na" 
    ms.devlang="dotnet" 
    ms.topic="article" 
    ms.date="12/05/2015"
    ms.author="juliako"/>


# 媒體服務 REST API 概觀 

[AZURE.INCLUDE [media-services-selector-setup](../../includes/media-services-selector-setup.md)]

Microsoft Azure 媒體服務會接受以 OData 為基礎的 HTTP 要求，而且可以使用詳細資訊 JSON 或 atom+pub 回應。 由於媒體服務符合 Azure 設計指導方針，因此在連線到媒體服務時，每個用戶端都必須使用一組必要的 HTTP 標頭，以及一組可以使用的選擇性標頭。 下列章節描述建立要求及接收來自媒體服務的回應時可以使用的標頭和 HTTP 指令動詞。


## 媒體服務支援的標準 HTTP 要求標頭

您對媒體服務每次呼叫，有一組必須在要求中包含的必要標頭，以及一組可能會想要包含的選擇性標頭。 下表列出必要標頭：


標頭|類型|值
---|---|---
Authorization|Bearer|Bearer 是唯一接受的授權機制。 此值也必須包含 ACS 所提供的存取權杖。
x-ms-version|十進位|2.11
DataServiceVersion|十進位|3.0
MaxDataServiceVersion|十進位|3.0



>[AZURE.NOTE] 因為媒體服務會使用 OData 來公開 (expose) 透過其他 Api 其基礎資產中繼資料儲存機制，DataServiceVersion 和 MaxDataServiceVersion 的標頭應該包含在任何要求;然而，如果不支援的話，然後目前媒體服務假設使用中的 DataServiceVersion 值為 3.0。

以下是一組選擇性標頭：

標頭|類型|值
---|---|---
Date|RFC 1123 日期|要求的時間戳記
Accept|內容類型|如下所示的回應要求內容類型：<p> -應用程式/json; odata = 詳細資訊<p> -應用程式/atom + xml<p> 回應可能會有不同的內容類型，如 blob 擷取，成功的回應會在其中包含 blob 串流做為裝載。
Accept-Encoding|Gzip、deflate|GZIP 和 DEFLATE 編碼 (適用時)。 注意：若是大型資源，媒體服務可能會忽略此標頭，並傳回未壓縮的資料。
Accept-Language|"en"、"es" 等。|指定回應的慣用語言。
Accept-Charset|字元集類型，如 "UTF-8"|預設值為 UTF-8。
X-HTTP-Method|HTTP 方法|可讓不支援 PUT 或 DELETE 等 HTTP 方法的用戶端或防火牆，透過 GET 呼叫通道傳送使用這些方法。
Content-Type|內容類型|PUT 或 POST 要求中的要求主體內容類型。
client-request-id|String|呼叫端定義的值，識別指定的要求。 如果指定，回應訊息中將包含此值以做為對應要求的方式。 <p><p>**重要**<p>值應該處於 2096b (2 k)。

## 媒體服務支援的標準 HTTP 回應標頭

以下是一組可能會根據您所要求的資源，以及您要執行的動作而傳回給您的標頭。


標頭|類型|值
---|---|---
request-id|String|目前作業的唯一識別碼，由服務產生。
client-request-id|String|在原始要求中，呼叫者所指定的識別碼 (如果有的話)。
Date|RFC 1123 日期|處理要求的日期。
Content-Type|視情況而異|回應主體的內容類型。
Content-Encoding|視情況而異|Gzip 或 deflate (視情況)。


## 媒體服務支援的標準 HTTP 指令動詞

以下是進行 HTTP 要求時，可以使用的 HTTP 指令動詞完整清單：


指令動詞|說明
---|---
GET|傳回物件的目前值。
POST|根據提供的資料建立物件或提交命令。
PUT|取代物件，或建立具名的物件 (如果適用的話)。
刪除|刪除物件。
MERGE|以具名屬性變更來更新現有的物件。
HEAD|傳回 GET 回應的物件中繼資料。


## 探索媒體服務模型

若要讓使用者更容易找到媒體服務實體，可以使用 $metadata 作業。 它可讓您擷取所有有效的實體類型、實體屬性、關聯、函式、動作等等。 下列範例顯示如何建構 URI: https://media.windows.net/API/$ 中繼資料。

如果您想要在瀏覽器檢視中繼資料，或是未在要求中包含 x-ms-version 標頭，您應該將 "?api-version=2.x" 附加到 URI 的結尾。



##媒體服務學習路徑

[AZURE.INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

##提供意見反應

[AZURE.INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]

  
  [Azure Classic Portal]: http://manage.windowsazure.com/



 

