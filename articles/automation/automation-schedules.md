<properties 
   pageTitle="Azure 自動化中的排程 | Microsoft Azure"
   description="自動化排程是用來排程讓 Azure 自動化中的 Runbook 自動啟動。  本文說明如何建立排程。"
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
   ms.date="10/26/2015"
   ms.author="bwren" />

# Azure 自動化中的排程

自動化排程是用來排程 Runbook 以自動執行。  這可以是在單一日期和時間執行一次 Runbook。  或也可以是重複執行的排程，以啟動 Runbook 多次。  排程通常不會從 Runbook 存取。

>[AZURE.NOTE]  排程目前不支援 Azure 自動化 DSC 組態。

## Windows PowerShell Cmdlet

下表中的 Cmdlet 是用來在  Azure 自動化中透過 Windows PowerShell 建立和管理變數。 其隨附於 [Azure PowerShell 模組](../powershell-install-configure.md)。

|Cmdlet|說明|
|:---|:---|
|[Get-AzureAutomationSchedule](http://msdn.microsoft.com/library/dn690274.aspx)|擷取排程。|
|[New-AzureAutomationSchedule](http://msdn.microsoft.com/library/dn690271.aspx)|建立新排程。|
|[Remove-AzureAutomationSchedule](http://msdn.microsoft.com/library/dn690279.aspx)|移除排程。|
|[Set-AzureAutomationSchedule](http://msdn.microsoft.com/library/dn690270.aspx)|設定現有排程的屬性。|
|[Get-AzureAutomationScheduledRunbook](http://msdn.microsoft.com/library/dn913778.aspx)|擷取排程的 Runbook。|
|[Register-AzureAutomationScheduledRunbook](http://msdn.microsoft.com/library/dn690265.aspx)|將 Runbook 與排程相關聯。|
|[Unregister-AzureAutomationScheduledRunbook](http://msdn.microsoft.com/library/dn690273.aspx)|從排程分離 Runbook。|

## 建立新排程

### 使用 Azure 入口網站建立新排程


1. 從您的自動化帳戶，按一下 [ **資產** 視窗的頂端。
1. 在視窗的底端，按一下 [ **加入設定**。
1. 按一下 [ **加入排程**。
1. 完成精靈，然後按一下核取方塊以儲存新變數。

### 使用 Azure 預覽入口網站建立新排程

1. 從您的自動化帳戶，按一下 [ **資產** 部分，以開啟 **資產** 刀鋒視窗。
1. 按一下 [ **排程** 部分，以開啟 **排程** 刀鋒視窗。
1. 按一下 [ **加入排程** 刀鋒視窗的頂端。
1. 完成表單，然後按一下 [ **建立** 以儲存新的排程。

### 使用 Windows PowerShell 建立新排程

 [New-azureautomationschedule](http://msdn.microsoft.com/library/dn690271.aspx) 指令程式建立新排程，以及設定現有排程的值。  下列範例 Windows PowerShell 命令會建立名為「我的每日排程」的新排程，該排程將從明天中午開始，並每天發動一次持續一年：

    $automationAccountName = "MyAutomationAccount"
    $scheduleName = "My Daily Schedule"
    $startTime = (Get-Date).Date.AddDays(1).AddHours(12)
    $expiryTime = $startTime.AddYears(1)
    
    New-AzureAutomationSchedule –AutomationAccountName $automationAccountName –Name $scheduleName –StartTime $startTime –ExpiryTime $expiryTime –DayInterval 1


## 另請參閱
- [在 Azure 自動化中排程 Runbook](automation-scheduling-a-runbook.md)
 


