<properties 
 pageTitle="排程器輸出驗證" 
 description="" 
 services="scheduler" 
 documentationCenter=".NET" 
 authors="krisragh" 
 manager="dwrede" 
 editor=""/>
<tags 
 ms.service="scheduler" 
 ms.workload="infrastructure-services" 
 ms.tgt_pltfrm="na" 
 ms.devlang="dotnet" 
 ms.topic="article" 
 ms.date="12/04/2015" 
 ms.author="krisragh"/>


# 排程器輸出驗證

排程器工作可能需要對外呼叫需要驗證的服務。 如此一來，被呼叫的服務可以判定排程器工作是否可以存取它的資源。 其中某些服務包括其他 Azure 服務、Salesforce.com、Facebook 和安全的自訂網站。

## 新增和移除驗證

將驗證新增至排程器工作很簡單 – 只將 JSON 子項目 `驗證` 至 `要求` 時建立或更新工作項目。 機密資料的一部分傳遞排程器服務在 PUT、 PATCH 或 POST 要求 – `驗證` 物件 – 永遠不會在回應中傳回。 在回應中，密碼資訊會設定為 null，或可能具有一個代表已驗證之實體的公用權杖。

若要移除驗證、 PUT 或 PATCH 工作明確地設定 `驗證` 物件為 null。 您將不會看到回應中傳回的任何驗證屬性。

目前，唯一支援的驗證類型是 `ClientCertificate` 模型 (適用於使用 SSL/TLS 用戶端憑證)， `基本` 模型 (適用於基本驗證)，而 `ActiveDirectoryOAuth` 模型 (適用於 Active Directory OAuth 驗證)。

## ClientCertificate 驗證的要求本文

新增驗證使用時 `ClientCertificate` 模型，在要求主體中指定下列其他元素。

| 元素| 說明|
|:---|:---|
| _ (父元素) 驗證 (_a)| 使用 SSL 用戶端憑證的驗證物件。|
| _type_| 必要。驗證類型。若是 SSL 用戶端憑證，值必須是 `ClientCertificate`。|
| _pfx_| 必要。Base64 編碼的 PFX 檔案內容。|
| _password_| 必要。存取 PFX 檔案的密碼。|


## ClientCertificate 驗證的回應本文

當傳送要求與驗證資訊時，回應包含下列驗證相關的元素。

| 元素| 說明|
|:--|:--|
| _ (父元素) 驗證 (_a)| 使用 SSL 用戶端憑證的驗證物件。|
| _type_| 驗證類型。SSL 用戶端憑證，這個值是 `ClientCertificate`。|
| _certificateThumbprint_| 憑證的指紋。|
| _certificateSubjectName_| 憑證的主旨辨別名稱。|
| _certificateExpiration_| 憑證的到期日|

## ClientCertificate 驗證的範例要求和回應

下列範例要求會提出 PUT 要求，其中包含 `ClientCertificate` 驗證。 要求如下：


    PUT https://management.core.windows.net/7e2dffb5-45b5-475a-91be-d3d9973c82d5/cloudservices/cs-brazilsouth-scheduler/resources/scheduler/~/JobCollections/testScheduler/jobs/testScheduler 
    x-ms-version: 2013-03-01
    User-Agent: Microsoft.WindowsAzure.Scheduler.SchedulerClient/3.0.0.0 AzurePowershell/v0.8.10
    Content-Type: application/json; charset=utf-8
    Host: management.core.windows.net
    Content-Length: 4013
    Expect: 100-continue
    
    {
      "action": {
        "type": "http",
        "request": {
          "uri": "https://management.core.windows.net/7e2dffb5-45b5-475a-91be-d3d9973c82d5/cloudservices/CS-NorthCentralUS-scheduler/resources/scheduler/~/JobCollections/testScheduler/jobs/test",
          "method": "GET",
          "headers": {
            "x-ms-version": "2013-03-01"
          },
          "authentication": {
            "type": "clientcertificate",
            "password": "test",
            "pfx": "long-pfx-key”
          }
        }
      },
      "recurrence": {
        "frequency": "minute",
        "interval": 1
      }
    }

一旦傳送此要求，回應如下：

    HTTP/1.1 201 Created
    Cache-Control: no-cache
    Pragma: no-cache
    Content-Length: 721
    Content-Type: application/json; charset=utf-8
    Expires: -1
    Server: 1.0.6198.153 (rd_rdfe_stable.141027-2149) Microsoft-HTTPAPI/2.0
    x-ms-servedbyregion: ussouth2
    X-AspNet-Version: 4.0.30319
    X-Powered-By: ASP.NET
    
    
    {
      "id": "testScheduler",
      "action": {
        "request": {
          "uri": "https:\/\/management.core.windows.net\/7e2dffb5-45b5-475a-91be-d3d9973c82d5\/cloudservices\/CS-NorthCentralUS-scheduler\/resources\/scheduler\/~\/JobCollections\/testScheduler\/jobs\/test",
          "method": "GET",
          "headers": {
            "x-ms-version": "2013-03-01"
          },
          "authentication": {
            "type": "ClientCertificate",
            "certificateThumbprint": "C1645E2AF6317D9FCF9C78FE23F9DE0DAFAD2AB5",
            "certificateExpiration": "2021-01-01T08:00:00Z",
            "certificateSubjectName": "CN=Scheduler Management"
          }
        },
        "type": "http"
      },
      "recurrence": {
        "frequency": "minute",
        "interval": 1
      },
      "state": "enabled",
      "status": {
        "nextExecutionTime": "2014-10-29T21:52:35.2108904Z",
        "executionCount": 0,
        "failureCount": 0,
        "faultedCount": 0
      }
    }

## 基本驗證的要求本文

新增驗證使用時 `基本` 模型，在要求主體中指定下列其他元素。

| 元素| 說明|
|:--|:--|
| _ (父元素) 驗證 (_a)| 使用基本驗證的驗證物件。|
| _type_| 必要。驗證類型。基本驗證，值必須是 `基本`。|
| _username_| 必要。要驗證的使用者名稱。|
| _password_| 必要。要驗證的密碼。|

## 基本驗證的回應本文

當傳送要求與驗證資訊時，回應包含下列驗證相關的元素。

| 元素| 說明|
|:--|:--|
| _ (父元素) 驗證 (_a)| 使用基本驗證的驗證物件。|
| _type_| 驗證類型。基本驗證，這個值是 `基本`。|
| _username_| 已驗證的使用者名稱。|

## 基本驗證的範例要求和回應

下列範例要求會提出 PUT 要求，其中包含 `基本` 驗證。 要求如下：

    PUT https://management.core.windows.net/7e2dffb5-45b5-475a-91be-d3d9973c82d5/cloudservices/cs-brazilsouth-scheduler/resources/scheduler/~/JobCollections/testScheduler/jobs/testScheduler 
    x-ms-version: 2013-03-01
    User-Agent: Microsoft.WindowsAzure.Scheduler.SchedulerClient/3.0.0.0 AzurePowershell/v0.8.10
    Content-Type: application/json; charset=utf-8
    Host: management.core.windows.net
    Expect: 100-continue
    
    {
      "action": {
        "type": "http",
        "request": {
          "uri": "https://management.core.windows.net/7e2dffb5-45b5-475a-91be-d3d9973c82d5/cloudservices/CS-NorthCentralUS-scheduler/resources/scheduler/~/JobCollections/testScheduler/jobs/test",
          "method": "GET",
          "headers": {
            "x-ms-version": "2013-03-01"
          },
        "authentication":{  
          "username":"user1",
          "password":"password",
          "type":"basic"
          }           
        }
      },
      "recurrence": {
        "frequency": "minute",
        "interval": 1
      }
    }

一旦傳送此要求，回應如下：

    HTTP/1.1 201 Created
    Cache-Control: no-cache
    Pragma: no-cache
    Content-Length: 721
    Content-Type: application/json; charset=utf-8
    Expires: -1
    Server: 1.0.6198.153 (rd_rdfe_stable.141027-2149) Microsoft-HTTPAPI/2.0
    x-ms-servedbyregion: ussouth2
    X-AspNet-Version: 4.0.30319
    X-Powered-By: ASP.NET
    
    {
      "id": "testScheduler",
      "action": {
        "request": {
          "uri": "https:\/\/management.core.windows.net\/7e2dffb5-45b5-475a-91be-d3d9973c82d5\/cloudservices\/CS-NorthCentralUS-scheduler\/resources\/scheduler\/~\/JobCollections\/testScheduler\/jobs\/test",
          "method": "GET",
          "headers": {
            "x-ms-version": "2013-03-01"
          },
          "authentication":{  
            "username":"user1",
            "type":"Basic"
          }
        },
        "type": "http"
      },
      "recurrence": {
        "frequency": "minute",
        "interval": 1
      },
      "state": "enabled",
      "status": {
        "nextExecutionTime": "2014-10-29T21:52:35.2108904Z",
        "executionCount": 0,
        "failureCount": 0,
        "faultedCount": 0
      }
    }

## ActiveDirectoryOAuth 驗證的要求本文

新增驗證使用時 `ActiveDirectoryOAuth` 模型，在要求主體中指定下列其他元素。

| 元素| 說明|
|:--|:--|
| _ (父元素) 驗證 (_a)| 使用 ActiveDirectoryOAuth 驗證的驗證物件。|
| _type_| 必要。驗證類型。ActiveDirectoryOAuth 驗證的值必須是 `ActiveDirectoryOAuth`。|
| _tenant_| 必要。Azure AD 租用戶的租用戶識別碼。|
| _audience_| 必要。這是設為 https://management.core.windows.net/。|
| _clientId_| 必要。提供 Azure AD 應用程式的用戶端識別碼。|
| _secret_| 必要。要求權杖之用戶端的密碼。|

### 判斷您的租用戶識別碼

您也可以執行 Azure AD 租用戶找到租用戶識別碼 `Get-azureaccount` Azure PowerShell 中。

## ActiveDirectoryOAuth 驗證的回應本文

當傳送要求與驗證資訊時，回應包含下列驗證相關的元素。

| 元素| 說明|
|:--|:--|
| _ (父元素) 驗證 (_a)| 使用 ActiveDirectoryOAuth 驗證的驗證物件。|
| _type_| 驗證類型。ActiveDirectoryOAuth 驗證，這個值是 `ActiveDirectoryOAuth`。|
| _tenant_| Azure AD 租用戶的租用戶識別碼。|
| _audience_| 這是設為 https://management.core.windows.net/。|
| _clientId_| Azure AD 應用程式的用戶端識別碼。|

## ActiveDirectoryOAuth 驗證的範例要求和回應

下列範例要求會提出 PUT 要求，其中包含 `ActiveDirectoryOAuth` 驗證。 要求如下：

    PUT https://management.core.windows.net/7e2dffb5-45b5-475a-91be-d3d9973c82d5/cloudservices/cs-brazilsouth-scheduler/resources/scheduler/~/JobCollections/testScheduler/jobs/testScheduler 
    x-ms-version: 2013-03-01
    User-Agent: Microsoft.WindowsAzure.Scheduler.SchedulerClient/3.0.0.0 AzurePowershell/v0.8.10
    Content-Type: application/json; charset=utf-8
    Host: management.core.windows.net
    Expect: 100-continue
    
    {
      "action": {
        "type": "http",
        "request": {
          "uri": "https://management.core.windows.net/7e2dffb5-45b5-475a-91be-d3d9973c82d5/cloudservices/CS-NorthCentralUS-scheduler/resources/scheduler/~/JobCollections/testScheduler/jobs/test",
          "method": "GET",
          "headers": {
            "x-ms-version": "2013-03-01"
          },
          "authentication":{  
            "tenant":"01234567-89ab-cdef-0123-456789abcdef",
            "audience":"https://management.core.windows.net/",
            "clientId":"8a14db88-4d1a-46c7-8429-20323727dfab",
            "secret": "&lt;secret-key&gt;",
            "type":"ActiveDirectoryOAuth"
          }                      
        }
      },
      "recurrence": {
        "frequency": "minute",
        "interval": 1
      }
    }

一旦傳送此要求，回應如下：

    HTTP/1.1 201 Created
    Cache-Control: no-cache
    Pragma: no-cache
    Content-Length: 721
    Content-Type: application/json; charset=utf-8
    Expires: -1
    Server: 1.0.6198.153 (rd_rdfe_stable.141027-2149) Microsoft-HTTPAPI/2.0
    x-ms-servedbyregion: ussouth2
    X-AspNet-Version: 4.0.30319
    X-Powered-By: ASP.NET
    
    
    {
      "id": "testScheduler",
      "action": {
        "request": {
          "uri": "https:\/\/management.core.windows.net\/7e2dffb5-45b5-475a-91be-d3d9973c82d5\/cloudservices\/CS-NorthCentralUS-scheduler\/resources\/scheduler\/~\/JobCollections\/testScheduler\/jobs\/test",
          "method": "GET",
          "headers": {
            "x-ms-version": "2013-03-01"
          },
          "authentication":{  
            "tenant":"01234567-89ab-cdef-0123-456789abcdef",
            "audience":"https://management.core.windows.net/",
            "clientId":"8a14db88-4d1a-46c7-8429-20323727dfab",
            "type":"ActiveDirectoryOAuth"
          }
        },
        "type": "http"
      },
      "recurrence": {
        "frequency": "minute",
        "interval": 1
      },
      "state": "enabled",
      "status": {
        "nextExecutionTime": "2014-10-29T21:52:35.2108904Z",
        "executionCount": 0,
        "failureCount": 0,
        "faultedCount": 0
      }
    }

## 另請參閱

 [排程器是什麼?](scheduler-intro.md)

 [Azure 排程器概念、 術語及實體階層](scheduler-concepts-terms.md)

 [開始使用 Azure 入口網站中的排程器](scheduler-get-started-portal.md)

 [計劃和計費，Azure 排程器中](scheduler-plans-billing.md)

 [Azure 排程器 REST API 參考](https://msdn.microsoft.com/library/dn528946)

 [Azure 排程器 PowerShell cmdlet 參考](scheduler-powershell-reference.md)

 [Azure 排程器高可用性和可靠性](scheduler-high-availability-reliability.md)

 [Azure 排程器限制、 預設值和錯誤碼](scheduler-limits-defaults-errors.md)











