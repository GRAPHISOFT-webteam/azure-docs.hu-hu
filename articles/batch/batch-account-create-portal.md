<properties
    pageTitle="建立 Azure Batch 帳戶 | Microsoft Azure"
    description="了解如何在 Azure 入口網站中建立 Azure Batch 帳戶，以在雲端中執行大規模的平行工作負載"
    services="batch"
    documentationCenter=""
    authors="dlepow"
    manager="timlt"
    editor=""/>

<tags
    ms.service="batch"
    ms.workload="big-compute"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="11/10/2015"
    ms.author="danlep"/>




# 在 Azure 入口網站中建立和管理 Azure Batch 帳戶

> [AZURE.SELECTOR]
- [Azure portal](batch-account-create-portal.md)
- [Batch Management .NET](batch-management-dotnet.md)


本文將說明如何使用 [Azure 入口網站](https://portal.azure.com) 來建立和管理 Azure 批次帳戶和設定，例如帳戶金鑰。 您需要 Batch 帳戶 URL 和相關聯的存取金鑰以驗證所有 Batch API 要求。 將 Batch 工作負載的所有 Batch 資源 (例如集區、工作 (job) 和工作 (task)) 與特定 Batch 帳戶產生關聯。
>[AZURE.NOTE] 目前預覽入口網站支援 Batch 帳戶管理和檢視一些帳戶資源的功能。 完整的 Batch 功能可以透過 Batch API 讓開發人員使用。

## 建立批次帳戶：

1. 登入 [Azure 入口網站](https://portal.azure.com)。

2. 按一下 [**新增**] > [**計算**] > [**Batch 服務**]。

    ![Marketplace 中的批次][marketplace_portal]

3. 檢閱資訊，然後按一下 [**建立**]。

4. 在 [**新 Batch 帳戶**] 刀鋒視窗中，輸入下列資訊：

    a. 在 [**帳戶名稱**] 中，輸入要在批次帳戶 URL 中使用的唯一名稱。
    >[AZURE.NOTE] Batch 帳戶名稱對 Azure 必須是唯一的，介於 3 到 24 個字元，並只能使用小寫字母和數字。

    b. 如果您有多個訂用帳戶，請按一下 [**訂用帳戶**] 以選取將建立帳戶的可用訂用帳戶。

    c. 按一下 [**資源群組**]，為帳戶選取現有的資源群組，或建立一個新群組。

    d. 在 [**位置**] 中，選取 Batch可用的 Azure 區域。

    ![建立批次帳戶：][account_portal]

5. 按一下 [**建立**] 以完成帳戶建立。

## 管理存取金鑰和帳戶設定

建立帳戶之後，您可以在入口網站找到它，以便管理存取金鑰、授權使用者和其他設定。

Batch 帳戶 URL 會出現在 [**Essentials**] 中。它是下列形式的 URL `https://<account_name>.&lt;region&gt;.batch.azure.com`。

若要查看和管理存取金鑰，按一下金鑰圖示。

![Batch 帳戶金鑰][account_keys]

## 其他關於 Batch 帳戶的注意事項

* 其他的方式來建立和管理 「 批次帳戶包括 [批次 PowerShell cmdlet](batch-powershell-cmdlets-get-started.md) 和 [批次管理.NET 程式庫](http://www.nuget.org/packages/Microsoft.Azure.Management.Batch/)。

* Azure 不會針對 Batch 帳戶向您收取費用。 您只取得需支付您的 Azure 運算資源和其他服務使用您的工作負載執行時 (請參閱 [批次定價](https://azure.microsoft.com/pricing/details/batch/))。

* 您可以在單一 Batch 帳戶中執行多個 Batch 工作負載，或在不同 Azure 區域的 Batch 帳戶之間散佈工作負載。

* 如果您執行數個大型的批次工作負載，請注意，某些 [批次服務配額和限制](batch-quota-limit.md) 套用到您的 Azure 訂閱，而且每個批次帳戶。 目前 Batch 帳戶的配額會出現在預覽入口網站的帳戶內容。

## 後續步驟

* 請參閱 [Azure 批次功能概觀](batch-api-basics.md) 若要深入了解批次概念。

* 開始開發第一個應用程式與 [批次.NET 用戶端程式庫](batch-dotnet-get-started.md)。


[marketplace_portal]: ./media/batch-account-create-portal/marketplace_batch.PNG 
[account_portal]: ./media/batch-account-create-portal/batch_acct_portal.png 
[account_keys]: ./media/batch-account-create-portal/account_keys.PNG 

