<properties
   pageTitle="Azure 自動化中的 Runbook 執行"
   description="描述如何處理 Azure 自動化中的 Runbook 的詳細資料。"
   services="automation"
   documentationCenter=""
   authors="bwren"
   manager="stevenka"
   editor="tysonn" />
<tags
   ms.service="automation"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="11/10/2015"
   ms.author="bwren" />


# Azure 自動化中的 Runbook 執行

當您在 Azure 自動化中啟動 Runbook 時即會建立一個工作。 工作是 Runbook 的單一執行個體。 會指派 Azure 自動化背景工作，以執行每項工作。 雖然背景工作是由多個 Azure 帳戶共用，來自不同自動化帳戶的工作會彼此隔離。 您對將服務您的要求工作的背景工作沒有控制權。 單一 Runbook 一次可以執行多個工作。 當您在 Azure 入口網站中檢視 Runbook 的清單時，它會列出已針對每一個 Runbook 啟動的上一個工作的狀態。 您可以檢視每一個 Runbook工作的清單，以追蹤每個 Runbook 的狀態。 如需不同的工作狀態的說明，請參閱 [工作狀態](#job-statuses)。

下圖顯示的 runbook 工作的生命週期 [圖形化 runbook](automation-runbook-types.md#graphical-runbooks) 和 [PowerShell 工作流程 runbook](automation-runbook-types.md#powershell-workflow-runbooks)。

![工作狀態 -PowerShell 工作流程](./media/automation-runbook-execution/job-statuses.png)

下圖顯示的 runbook 工作的生命週期 [PowerShell runbook](automation-runbook-types.md#powershell-runbooks)。

![工作狀態 - PowerShell 指令碼](./media/automation-runbook-execution/job-statuses-script.png)


藉由對您的 Azure 訂用帳戶進行連接，您的工作將可以存取您的 Azure 資源。 如果可從公用雲端存取這些資源，他們將只可以存取您的資料中心中的資源。

## 工作狀態

下表描述工作可能會有不同的狀態。

| Status| 說明|
|:---|:---|
| Completed| 工作已成功完成。|
| Failed| 如 [圖形化和 PowerShell 工作流程的 runbook](automation-runbook-types.md), ，runbook 無法編譯。如 [PowerShell 指令碼 runbook](automation-runbook-types.md), 、 runbook 無法啟動或工作時發生例外狀況。|
| 處理失敗，正在等候資源| 工作失敗，因為它達到 [公平分享](#fairshare) 三次上限，且每次從相同的檢查點或啟動 runbook 開始。|
| 已排入佇列| 工作正在等候取得自動化背景工作中的資源，以便可啟動。|
| 啟動中| 工作已指派給背景工作，並且系統正在進行啟動。|
| 繼續中| 工作暫停後，系統正在繼續工作。|
| 執行中| 工作正在執行。|
| 執行中，正在等候資源| 工作已卸載，因為它達到 [公平分享](#fairshare) 限制。工作會很快地從其上一個檢查點繼續。|
| 已停止| 工作完成之前已由使用者停止。|
| 停止中| 系統正在停止工作。|
| 暫止| 工作已由使用者、系統或 Runbook 中的命令暫停。已暫停的工作可以再次啟動，並從其上一個檢查點，或從 Runbook 的開頭繼續 (若無檢查點)。僅在例外狀況時，系統會暫停 Runbook。根據預設，ErrorActionPreference 會設定為 [**繼續**]，代表工作會在發生錯誤時繼續執行。如果此喜好設定變數設定為 [**停止**]，則工作將會在發生錯誤時暫停。適用於 [圖形化和 PowerShell 工作流程的 runbook](automation-runbook-types.md) 只。|
| 暫停中| 因使用者要求，系統正在嘗試暫停工作。Runbook 必須達到其下一個檢查點才能暫停。如果其已通過其上一個檢查點，則在可以暫停之前會先完成工作。適用於 [圖形化和 PowerShell 工作流程的 runbook](automation-runbook-types.md) 只。|

## 使用 Azure 管理入口網站來檢視工作狀態

### 自動化儀表板

自動化儀表板會針對特定的自動化帳戶顯示所有 Runbook 的摘要。 其也包含帳戶的使用量概觀。 針對經過指定的天數或小時數輸入每個狀態的所有 Runbook，摘要圖表會顯示其工作總數。 您可以在圖表的右上角選取時間範圍。 圖表的時間軸會根據您選取的時間範圍類型進行變更。 您可以選擇是否要在螢幕頂端按一下以顯示特定狀態的列。

您可以使用下列步驟來顯示自動化儀表板。

1. 在 Azure 管理入口網站中，選取 [**自動化**]，然後按一下自動化帳戶的名稱。
1. 選取 [**儀表板**] 索引標籤。

### Runbook 儀表板

Runbook 儀表板會顯示單一 Runbook 的摘要。 針對經過指定的天數或小時數輸入每個狀態的 Runbook，摘要圖表會顯示其工作總數。 您可以在圖表的右上角選取時間範圍。 圖表的時間軸會根據您選取的時間範圍類型進行變更。 您可以選擇是否要在螢幕頂端按一下以顯示特定狀態的列。

您可以使用下列步驟來顯示 Runbook 儀表板。

1. 在 Azure 管理入口網站中，選取 [**自動化**]，然後按一下自動化帳戶的名稱。
1. 按一下 Runbook 的名稱。
1. 選取 [**儀表板**] 索引標籤。

### 工作摘要

您可以針對特定 Runbook 及其最新狀態，檢視所有已建立工作的清單。 您可以依工作狀態和上次工作變更的日期範圍來篩選此清單。 按一下工作的名稱以檢視其詳細的資訊及其輸出。 工作的詳細檢視包含提供給該工作的 Runbook 參數值。

您可以使用下列步驟來檢視 Runbook 工作。

1. 在 Azure 管理入口網站中，選取 [**自動化**]，然後按一下自動化帳戶的名稱。
1. 按一下 Runbook 的名稱。
1. 選取 [**工作**] 索引標籤。
1. 按一下工作的 [**已建立工作**] 資料行以檢視其詳細資料和輸出。

## 使用 Windows PowerShell 擷取工作狀態

您可以使用 [Get-azureautomationjob](http://msdn.microsoft.com/library/azure/dn690263.aspx) 擷取 runbook，並將特定工作的詳細資料所建立的作業。 如果您使用 Windows PowerShell 啟動 runbook [Start-azureautomationrunbook](http://msdn.microsoft.com/library/azure/dn690259.aspx), ，則它會傳回產生的工作。 使用 [Get-azureautomationjob](http://msdn.microsoft.com/library/azure/dn690263.aspx)輸出以取得工作的輸出。

下列範例命令會針對範例 Runbook 擷取上一個工作並顯示其狀態，該值會提供給 Runbook 參數，以及來自工作的輸出。

    $job = (Get-AzureAutomationJob –AutomationAccountName "MyAutomationAccount" –Name "Test-Runbook" | sort LastModifiedDate –desc)[0]
    $job.Status
    $job.JobParameters
    Get-AzureAutomationJobOutput –AutomationAccountName "MyAutomationAccount" -Id $job.Id –Stream Output

## 公平共用

若要在雲端中的所有 runbook 之間共用資源，Azure 自動化將之後暫時卸除任何工作執行了 3 小時。 [圖形](automation-runbook-types.md#graphical-runbooks) 和 [PowerShell 工作流程](automation-runbook-types.md#powershell-workflow-runbooks) runbook 將會繼續從其最後一個 [檢查點](http://technet.microsoft.com/library/dn469257.aspx#bk_Checkpoints)。 在此期間，工作的顯示狀態為「執行中，正在等候資源」。 如果 Runbook 沒有檢查點，或在卸載工作之前工作未到達第一個檢查點，則會從頭重新啟動。 [PowerShell](automation-runbook-types.md#powershell-runbooks) runbook 會一律從頭開始重新啟動因為它們不支援檢查點。

如果 Runbook 從相同檢查點或從 Runbook 的開頭重新啟動連續三次，它將被終止，狀態為「失敗，正在等候資源」。 這是為了防止 Runbook 無限期地執行而不會完成，因為它們在未重新卸載的情況下，無法進入下一個檢查點。 在此情況下，您會收到下列失敗例外狀況。

* 作業無法繼續執行，因為它已經由相同的檢查點重複收回。 請確定您的 Runbook 不會執行長時間作業，而未保存其 state.*

建立 Runbook 時，您應該確定在兩個檢查點之間執行的任何活動的時間不會超過 3 小時。 您可能需要將檢查點加入至 runbook，以確保它不會不到達此 3 小時的限制或中斷長時間執行的作業。 例如，您的 Runbook 可能在大型 SQL 資料庫上執行重新索引。 如果此單一作業未在公平共用的限制內完成，則將會卸載工作並且從頭開始重新啟動。 在此情況下，您應該將重新索引作業分成多個步驟，例如一次重新索引一個資料表，然後在每個作業之後插入檢查點，讓工作可以在最後一個作業完成後繼續。



## 相關文章

- [在 Azure 自動化中啟動 runbook](automation-starting-a-runbook.md)





