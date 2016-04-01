<properties 
   pageTitle="在 Azure 自動化中啟動 Runbook"
   description="摘要說明可以用來在 Azure 自動化中啟動 Runbook 的不同的方法，並提供使用 Azure 入口網站和 Windows PowerShell 的詳細資訊。"
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
   ms.date="11/13/2015"
   ms.author="bwren;sngun"/>

# 在 Azure 自動化中啟動 Runbook

下表將協助您判斷在 Azure 自動化中啟動 Runbook 的方法，最適合您的特定案例。    這篇文章包含有關使用 Azure 入口網站和 Windows PowerShell 啟動 Runbook 的詳細資料。  其他方法的詳細資訊在其他文件中提供，您可以從下列連結來存取。

<table>
 <tr>
  <td>方法</td>
  <td>特性</td>
 </tr>
 <tr>           
  <td><a href="#starting-a-runbook-with-the-azure-portal">Azure 入口網站</a></td>
  <td>
   <ul>
    <li>互動式使用者介面的最簡單方法。</li>
    <li>提供簡單參數值的表單。</li>
    <li>輕鬆追蹤工作狀態。</li>
    <li>使用 Azure 登入資訊驗證存取。</li>
   </ul>
  </td>
 </tr>
 <tr>
  <td><a href="https://msdn.microsoft.com/library/dn690259.aspx">Windows PowerShell</a></td>
  <td>
   <ul>
     <li>使用 Windows PowerShell Cmdlet 從命令列呼叫。</li>
     <li>可以包含在具有多個步驟的自動化解決方案中。</li>
     <li>使用憑證或 OAuth 使用者主體 / 服務驗證要求主體。</li>
     <li>提供簡單和複雜的參數值。</li>
     <li>追蹤工作狀態。</li>
     <li>支援 PowerShell Cmdlet 所需的用戶端。</li>
   </ul>
  </td>
 </tr>
 <tr>
  <td><a href="http://msdn.microsoft.com/library/azure/mt163849.aspx">Azure 自動化 API</a></td>
  <td>
   <ul>
    <li>最有彈性的方法，但也最複雜。</li>
    <li>從任何可提出 HTTP 要求的自訂程式碼呼叫。</li>
    <li>使用憑證或 OAuth 使用者主體/服務主體來驗證要求。</li>
    <li>提供簡單和複雜的參數值。</li>
    <li>追蹤工作狀態。</li>
   </ul>
  </td>
 </tr>
 <tr>
  <td><a href="http://azure.microsoft.com/documentation/articles/automation-webhooks/">Webhook</a></td>
  <td>
   <ul>
    <li>從單一 HTTP 要求啟動 Runbook。</li>
    <li>在 URL 中使用安全性權杖進行驗證。</li>
    <li>用戶端無法覆寫建立 Webhook 時指定的參數值。  Runbook 可以定義填入了 HTTP 要求詳細資料的單一參數。</li>
    <li>無法透過 Webhook URL 追蹤工作狀態。</li>
   </ul>
  </td>
 </tr>
 <tr>
  <td><a href="http://azure.microsoft.com/documentation/articles/automation-webhooks/">回應 Azure 警示</a></td>
  <td>
   <ul>
    <li>啟動 Runbook 以回應 Azure 警示。</li>
    <li>設定 Runbook 的 Webhook 以及警示的連結。</li>
    <li>在 URL 中使用安全性權杖進行驗證。</li>
    <li>目前僅支援度量的警示。</li>
   </ul>
  </td>
 </tr>
 <tr>
  <td><a href="http://azure.microsoft.com/documentation/articles/automation-scheduling-a-runbook">排程</a></td>
  <td>
   <ul>
    <li>每小時、每天或每週排程，自動啟動 Runbook。</li>
    <li>透過 Azure 入口網站、PowerShell Cmdlet 或 Azure API 操縱排程。</li>
    <li>提供參數值來搭配排程使用。</li>
   </ul>
  </td>
 </tr>
 <tr>
  <td><a href="http://azure.microsoft.com/documentation/articles/automation-child-runbooks/">從另一個 Runbook</a></td>
  <td>
   <ul>
    <li>在另一個 Runbook 中使用 Runbook 作為活動</li>
    <li>對多個 Runbook 使用的功能很有用。</li>
    <li>提供參數值給子 Runbook，並在父 Runbook 中使用輸出。</li>
   </ul>
  </td>
 </tr>
</table>
<br>



## 使用 Azure 入口網站啟動 Runbook

1. 在 Azure 入口網站中，選取 **自動化** ，然後按一下自動化帳戶的名稱。
1. 選取 **Runbook** ] 索引標籤。
1. 選取 runbook，然後再按 **啟動**。
1. 如果 Runbook 有參數，系統會提示您提供每個參數的文字方塊的值。 請參閱 [Runbook 參數](#Runbook-parameters) 下方以進一步參數的詳細資訊。
1. 請選取 **檢視作業** 旁 **起始** runbook 訊息，或選取 **工作** 的 runbook，以檢視 runbook 工作的狀態] 索引標籤。

## 使用 Azure 預覽入口網站啟動 Runbook

1. 從您的自動化帳戶，按一下 [ **Runbook** 部分，以開啟 **Runbook** 刀鋒視窗。
1. 按一下 runbook 以開啟其 **Runbook** 刀鋒視窗。
2. 按一下 [ **啟動**。
1. 如果 Runbook 沒有參數，系統會提示您確認您是否想要啟動它。  如果 runbook 有參數， **啟動 Runbook** 刀鋒視窗會開啟，因此您可以提供參數值。 請參閱 [Runbook 參數](#Runbook-parameters) 下方以進一步參數的詳細資訊。
3.  **工作** ] 分頁隨即開啟，讓您可以追蹤工作的狀態。


## 使用 Windows PowerShell 啟動 Runbook

您可以使用 [Start-azureautomationrunbook](http://msdn.microsoft.com/library/azure/dn690259.aspx) 使用 Windows PowerShell 啟動 runbook。 下列範例程式碼會啟動名稱為 Test-Runbook 的 Runbook。

    Start-AzureAutomationRunbook –AutomationAccountName "MyAutomationAccount" –Name "Test-Runbook"

Start-AzureAutomationRunbook 會傳回工作物件，一旦啟動 Runbook，您即可用來追蹤其狀態。 然後，您可以使用此工作物件搭配 [Get-azureautomationjob](http://msdn.microsoft.com/library/azure/dn690263.aspx) 來判斷作業的狀態和 [Get-azureautomationjoboutput](http://msdn.microsoft.com/library/azure/dn690268.aspx) 來取得其輸出。 下列範例程式碼會啟動名稱為 Test-Runbook 的 Runbook，等到它完成，然後顯示其輸出。

    $job = Start-AzureAutomationRunbook –AutomationAccountName "MyAutomationAccount" –Name "Test-Runbook"
    
    $doLoop = $true
    While ($doLoop) {
       $job = Get-AzureAutomationJob –AutomationAccountName "MyAutomationAccount" -Id $job.Id
       $status = $job.Status
       $doLoop = (($status -ne "Completed") -and ($status -ne "Failed") -and ($status -ne "Suspended") -and ($status -ne "Stopped") 
    }
    
    Get-AzureAutomationJobOutput –AutomationAccountName "MyAutomationAccount" -Id $job.Id –Stream Output

如果 runbook 需要參數，則您必須提供其作為 [雜湊表](http://technet.microsoft.com/library/hh847780.aspx) 其中 hashtable 的索引鍵符合參數名稱，而值是參數值。 下列範例顯示如何啟動 Runbook 具有名為 FirstName 和 LastName 的兩個參數、名為 RepeatCount 的整數，和名為 Show 的布林值參數。 如需有關參數的詳細資訊，請參閱 [Runbook 參數](#Runbook-parameters) 下方。

    $params = @{"FirstName"="Joe";"LastName"="Smith";"RepeatCount"=2;"Show"=$true}
    Start-AzureAutomationRunbook –AutomationAccountName "MyAutomationAccount" –Name "Test-Runbook" –Parameters $params

## Runbook 參數

使用 Azure 管理入口網站或 Windows PowerShell 啟動 Runbook 時，指示會透過 Azure 自動化 Web 服務傳送。 此服務不支援具有複雜資料型別的參數。 如果您需要提供複雜參數的值，則您必須內嵌呼叫它從另一個 runbook 中所述 [Azure 自動化中的子系 runbook](automation-child-runbooks.md)。

Azure 自動化 Web 服務會為特定資料型別的參數提供特殊功能，如下列小節所述。

### 具名的值

如果參數是資料型別 [object]，則您可以使用下列 JSON 格式來傳送具名值的清單 ︰ *{"Name1": Value1，"Name2": Value2，"Name3": Value3}*。 這些值必須是簡單型別。 Runbook 將會收到參數 [PSCustomObject](http://msdn.microsoft.com/library/azure/system.management.automation.pscustomobject(v=vs.85).aspx) 與每個具名值相對應的屬性。

請考慮可接受稱為 user 的參數的下列測試 Runbook。

    Workflow Test-Parameters
    {
       param ( 
          [Parameter(Mandatory=$true)][object]$user
       )
        if ($user.Show) {
            foreach ($i in 1..$user.RepeatCount) {
                $user.FirstName
                $user.LastName
            }
        } 
    }

下列文字可用於 user 參數。

    {"FirstName":"Joe","LastName":"Smith","RepeatCount":2,"Show":true}

這會導致下列輸出。

    Joe
    Smith
    Joe
    Smith

### 陣列

如果參數是陣列，例如 [array] 或 [string []]，您可以使用下列 JSON 格式來傳送值的清單 ︰ *[Value1，Value2，Value3]*。 這些值必須是簡單型別。

請考慮可接受參數，呼叫下列測試 runbook *使用者*。

    Workflow Test-Parameters
    {
       param ( 
          [Parameter(Mandatory=$true)][array]$user
       )
        if ($user[3]) {
            foreach ($i in 1..$user[2]) {
                $ user[0]
                $ user[1]
            }
        } 
    }

下列文字可用於 user 參數。

    ["Joe","Smith",2,true]

這會導致下列輸出。

    Joe
    Smith
    Joe
    Smith

### 認證

如果參數是資料型別 **PSCredential**, ，您就可以提供 Azure 自動化名稱 [認證資產](automation-credentials.md)。 Runbook 將會使用您所指定的名稱擷取認證。

請考慮可接受稱為 credential 的參數的下列測試 Runbook。

    Workflow Test-Parameters
    {
       param ( 
          [Parameter(Mandatory=$true)][PSCredential]$credential
       )
       $credential.UserName
    }

下列文字可用於使用者參數假設有認證資產 *My Credential*。

    My Credential

假設認證中的使用者名稱是 *jsmith*, ，這會導致下列輸出。

    jsmith

## 相關文章

- [Azure 自動化中的子 Runbook](automation-child-runbooks.md) 

