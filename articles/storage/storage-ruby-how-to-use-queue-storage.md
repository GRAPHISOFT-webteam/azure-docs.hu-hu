<properties 
    pageTitle="如何使用拼音的佇列儲存體 | Microsoft Azure" 
    description="了解如何使用 Azure 佇列服務來建立和刪除佇列，以及插入、取得和刪除訊息。 範例以 Ruby 撰寫。" 
    services="storage" 
    documentationCenter="ruby" 
    authors="tfitzmac" 
    manager="wpickett" 
    editor=""/>

<tags 
    ms.service="storage" 
    ms.workload="storage" 
    ms.tgt_pltfrm="na" 
    ms.devlang="ruby" 
    ms.topic="article" 
    ms.date="09/23/2015" 
    ms.author="tomfitz"/>


# 如何使用 Ruby 的佇列儲存體

[AZURE.INCLUDE [storage-selector-queue-include](../../includes/storage-selector-queue-include.md)]

## 概觀

本指南說明如何使用 Microsoft 執行一般案例
。 這些範例是以 Ruby Azure API 撰寫的。
涵蓋的案例包括 **插入**, ，**查看**, ，**取得**,，
和 **刪除** 佇列訊息，以及 **建立和刪除
佇列**。

[AZURE.INCLUDE [storage-queue-concepts-include](../../includes/storage-queue-concepts-include.md)]

[AZURE.INCLUDE [storage-create-account-include](../../includes/storage-create-account-include.md)]

## 建立 Ruby 應用程式

建立 Ruby 應用程式。 如需指示， 
請參閱 [Azure 上建立 Ruby 應用程式](/develop/ruby/tutorials/web-app-with-linux-vm/)。

## 設定您的應用程式以存取儲存體

若要使用 Azure 儲存體，您需要下載並使用 Ruby azure 套件，這包含一組便利程式庫，能與儲存體 REST 服務通訊。

### 使用 RubyGems 來取得套件

1. 使用命令列介面，例如 **PowerShell** (Windows)、 **終端機** (Mac) 或 **Bash** (Unix)。

2. 在命令視窗中鍵入 "gem install azure" 以安裝 Gem 和相依性。

### 匯入封裝

使用您偏好的文字編輯器，將以下內容新增至您打算使用儲存體的 Ruby 檔案頂端：

    require "azure"

## 設定 Azure 儲存體連接

Azure 模組會讀取環境變數 **AZURE\_STORAGE\_ACCOUNT** 和 **AZURE\_STORAGE\_ACCESS_KEY** 
以取得連接到 Azure 儲存體帳戶所需的資訊。 如果未設定這些環境變數， 
您必須指定帳戶資訊，才能使用 **azure:: queueservice** 為下列程式碼:

    Azure.config.storage_account_name = "<your azure storage account>"
    Azure.config.storage_access_key = "<your Azure storage access key>"

若要取得這些值，請執行下列動作：

1. 登入 [Azure 入口網站](portal.azure.com)。
2. 瀏覽到您要使用的儲存體帳戶
3. 按一下 [ **管理金鑰** 底部的 [瀏覽] 窗格。
4. 在快顯對話方塊中，您將會看到儲存體帳戶名稱、主要存取金鑰和次要存取金鑰。 如需存取金鑰，您可以選取主要存取金鑰或次要存取金鑰。

## 作法：建立佇列

下列程式碼會建立 **azure:: queueservice** 物件，可讓您能夠使用佇列。

    azure_queue_service = Azure::QueueService.new

使用 **create_queue ()** 方法來建立具有指定名稱的佇列。

    begin
      azure_queue_service.create_queue("test-queue")
    rescue
      puts $!
    end

## 作法：將訊息插入佇列中

若要將訊息插入佇列中，使用 **create_message ()** 方法來建立新的訊息，並將它新增至佇列。

    azure_queue_service.create_message("test-queue", "test message")

## 作法：預覽下一個訊息

您可以查看在前面的佇列訊息，而它從佇列中移除藉由呼叫 **peek\_messages ()** 方法。 根據預設， **peek\_messages ()** 會查看單一訊息。 您也可以指定所要查看的訊息數。

    result = azure_queue_service.peek_messages("test-queue",
      {:number_of_messages => 10})

## 作法：清除下一個佇列訊息

您可以使用兩個步驟將訊息從佇列中移除。

1. 當您呼叫 **list\_messages ()**, ，您取得佇列中的下一個訊息的預設值。 您也可以指定您要取得的訊息數。 從傳回的訊息 **list\_messages ()** 會從此佇列讀取訊息的任何其他程式碼是不可見。 您可以傳入以秒為單位的可見性逾時，作為參數。

2. 若要完成從佇列移除訊息，您還必須呼叫 **delete_message ()**。

這個移除訊息的兩步驟程序可確保您的程式碼因為硬體或軟體故障而無法處理訊息時，另一個程式碼的執行個體可以取得相同訊息並再試一次。 您的程式碼呼叫 **delete\_message()** 處理完訊息之後。

    messages = azure_queue_service.list_messages("test-queue", 30)
    azure_queue_service.delete_message("test-queue", 
      messages[0].id, messages[0].pop_receipt)

## 作法：變更佇列訊息的內容

您可以在佇列中就地變更訊息內容。 以下程式碼使用 **update_message ()** 方法來更新訊息。 此方法會傳回一個 Tuple (其中包含佇列訊息的 pop receipt) 和一個 UTC 日期時間值 (代表訊息將會顯示在佇列上的時間)。

    message = azure_queue_service.list_messages("test-queue", 30)
    pop_receipt, time_next_visible = azure_queue_service.update_message(
      "test-queue", message.id, message.pop_receipt, "updated test message", 
      30)

## 作法：清除佇列訊息的其他選項

自訂從佇列中擷取訊息的方法有兩種。

1. 您可以取得一批訊息。

2. 您可以設定較長或較短的可見度逾時，讓您的程式碼有較長或較短的時間可以完全處理每個訊息。

下列程式碼範例使用 **list\_messages ()** 方法一次呼叫中取得 15 個訊息。 接著，它會列出每個訊息，並加以刪除。 它也會將可見度逾時設定為每個訊息五分鐘。

    azure_queue_service.list_messages("test-queue", 300
      {:number_of_messages => 15}).each do |m|
      puts m.message_text
      azure_queue_service.delete_message("test-queue", m.id, m.pop_receipt)
    end

## 作法：取得佇列長度

您可以取得佇列中的估計訊息數目。  **Get\_queue\_metadata ()** 方法會要求佇列服務傳回約略的訊息計數和佇列的中繼資料。

    message_count, metadata = azure_queue_service.get_queue_metadata(
      "test-queue")

## 作法：刪除佇列

若要刪除佇列及其內含的所有訊息，請呼叫 **delete\_queue** 佇列物件上的方法。

    azure_queue_service.delete_queue("test-queue")

## 後續步驟

了解佇列儲存體的基礎概念之後，請參考下列連結以了解有關更複雜的儲存工作。

- 請瀏覽 [Azure 儲存體團隊部落格](http://blogs.msdn.com/b/windowsazurestorage/)
- 請瀏覽 [Azure SDK for Ruby](https://github.com/WindowsAzure/azure-sdk-for-ruby) GitHub 上的儲存機制

要比較本文所討論的 Azure 佇列服務和 Azure 服務匯流排佇列中所討論 [如何使用服務匯流排佇列](/develop/ruby/how-to-guides/service-bus-queues/) 文件，請參閱 [Azure 佇列和 Azure 服務匯流排佇列-比較和對照](http://msdn.microsoft.com/library/azure/hh767287.aspx)
 

