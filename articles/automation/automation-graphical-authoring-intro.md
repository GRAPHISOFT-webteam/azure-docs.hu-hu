<properties 
   pageTitle="Azure 自動化中的圖形化編寫"
   description="圖形化編寫可讓您建立 Azure 自動化的 Runbook，而不使用程式碼。   本文章提供圖形化編寫的簡介和開始建立圖形化 Runbook 所需的所有詳細資料。"
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
   ms.date="11/05/2015"
   ms.author="bwren" />

# Azure 自動化中的圖形化編寫

## 簡介

圖形化編寫可讓您為 Azure 自動化建立 Runbook，而沒有基礎的 Windows PowerShell 工作流程程式碼的複雜度。  您從 Cmdlet 和其他活動的程式庫中將活動加入至畫布，並將其連結在一起以形成工作流程。

本文章提供圖形化編寫的簡介及開始建立圖形化 Runbook 所需的概念。

## 圖形化 Runbook

Azure 自動化中的所有 Runbook 都是 Windows PowerShell 工作流程。  圖形化 Runbook 會產生由自動化背景工作執行的 PowerShell 程式碼，但是您無法檢視它，或直接修改它。  圖形化 Runbook 無法轉換成文字式Runbook，現有的文字式 Runbook 也無法匯入到圖形化編輯器。


## 圖形化編輯器概觀

您可以藉由建立或編輯圖形化 Runbook，在 Azure 預覽入口網站中開啟圖形化編輯器。

![圖形化工作區](media/automation-graphical-authoring-intro/graphical-editor.png)


下列各節說明圖形化編輯器中的控制項。


### 畫布
畫布是您設計 Runbook 的位置。  您會從程式庫控制項中的節點將活動加入至 Runbook，並以連結將它們連接來定義 Runbook 的邏輯。

### 程式庫控制項

程式庫控制項是您在其中選取 [活動](#activities) 新增至您的 runbook。  您會將它們加入畫布，在畫布中將它們連接到其他活動。  它包含下表所述的四個區段。

| 區段 | 說明 |
|:---|:---|
| Cmdlet | 包含可以在 Runbook 中使用的所有 Cmdlet。  Cmdlet 是依模組組織。  已在您的自動化帳戶中安裝的所有模組將可供使用。  |
| Runbook |  包含依標記組織的自動化帳戶中的 Runbook。  由於 runbook 可以有多個標記，它可能會列出這些 runbook 可以加入至畫布上，以當做子系 runbook 的多個標記。 目前正在編輯的 Runbook 會顯示，但不能加入至畫布，因為 Runbook 無法呼叫本身。
| Assets | 包含 [自動化資產](http://msdn.microsoft.com/library/dn939988.aspx) 中您可以在 runbook 中使用的自動化帳戶。  當您將資產加入至 Runbook 時，它會加入工作流程活動，取得所選資產。  如果是變數資產，您可以選取是否要加入活動以取得變數或設定變數。
| Runbook 控制項 | 包含可在目前的 Runbook 中使用的 Runbook 控制項活動。 A *聯合* 接受多個輸入，並等待，直到所有已完成，再繼續工作流程。 A *工作流程指令碼* 執行一個或多個 PowerShell 工作流程程式碼行。  您可以對您無法利用其他活動來達成的自訂程式碼或功能使用此活動。|

### 組態控制項

組態控制項是您為在畫布上選取的物件提供詳細資料的位置。此控制項中可使用的屬性將取決於所選的物件類型。  當您在組態控制項中選取一個選項時，即會開啟其他分頁以提供其他資訊。

### 測試控制項

第一次啟動圖形化編輯器時，不會顯示測試控制項。 開啟當您以互動方式 [測試圖形化 runbook](#graphical-runbook-procedures)。  

## 圖形化 Runbook 程序 

### 匯出和匯入圖形化 Runbook

您可以只匯出圖形化 Runbook 的已發行版本。  如果尚未發行 runbook，然後在 **匯出發行** 按鈕將會停用。  當您按一下 **匯出發行** runbook] 按鈕，下載到本機電腦。  檔案的名稱比對的 runbook 名稱 *graphrunbook* 延伸模組。

![匯出已發行](media/automation-graphical-authoring-intro/runbook-export.png)

您可以將圖形化 runbook 檔案匯入選取 **匯入** 選項新增 runbook 時。   當您選取要匯入檔案時，您可以保留相同 **名稱** 或提供新的。

![匯入 Runbook](media/automation-graphical-authoring-intro/runbook-import.png)


### 測試圖形化 Runbook

您可以在 Azure 預覽入口網站中測試 Runbook 的草稿版，同時保持 Runbook 已發行的版本不變，或可以在發行之前，測試新的 Runbook。 這可讓您驗證取代已發行的版本之前，Runbook 正常運作。 當您測試 Runbook 時，草稿 Runbook 會執行，而且它執行的任何動作都會完成。 不會建立工作歷程記錄，但是會在 [測試輸出] 窗格中顯示輸出。 

開啟 runbook 進行編輯，以開啟 runbook 的測試控制項，然後按一下 **測試窗格** ] 按鈕。

![測試窗格按鈕](media/automation-graphical-authoring-intro/runbook-edit-test-pane.png)

測試控制項將提示輸入任何輸入參數，而您可以按一下來啟動 runbook **啟動** ] 按鈕。

![測試控制項按鈕](media/automation-graphical-authoring-intro/runbook-test-start.png)

### 發行圖形化 Runbook

Azure 自動化中的每個 Runbook 有草稿和已發行的版本。 只可執行已發行版本，而且只可編輯草稿版本。 已發行版本不會受到草稿版本的任何變更影響。 草稿版本就緒可供使用時，您會將它發行，以草稿版本覆寫已發行版本。

您可以開啟 runbook 進行編輯，然後按一下發行圖形化 runbook **發行** ] 按鈕。

![發佈按鈕](media/automation-graphical-authoring-intro/runbook-edit-publish.png)

當尚未發行 runbook 時，它的狀態為 **新增**。  當發行時，它的狀態為 **發佈**。  如果您編輯 runbook 之後已發佈，且草稿和已發佈版本不同,，runbook 的狀態為 [ **中編輯**。

![Runbook 狀態](media/automation-graphical-authoring-intro/runbook-statuses.png) 

您也可以選擇還原成 Runbook 的已發行版本。  這樣會棄置上次發行 Runbook 後所進行的任何變更，並以已發行版本取代 Runbook 的草稿版本。

![還原成已發行按鈕](media/automation-graphical-authoring-intro/runbook-edit-revert-published.png)


## 活動

活動是 Runbook 的建置區塊。  活動可以是 PowerShell Cmdlet、子 Runbook 或工作流程活動。  您將活動加入 runbook 以滑鼠右鍵按一下程式庫控制項，然後選取 **加入至畫布**。  然後可以按一下並拖曳活動，將它放置在畫布上的任何位置。  活動在畫布上的位置不會以任何方式影響 Runbook 的作業。  您可以配置您的 Runbook，不過您會發現以視覺化方式檢視其作業最適合。 

![加入至畫布](media/automation-graphical-authoring-intro/add-to-canvas.png)

在組態分頁中的畫布上選取活動以設定其屬性和參數。  您可以變更 **標籤** 為成對您具的活動。  原始的 Cmdlet 仍在執行，您只要變更它將在圖形化編輯器中使用的顯示名稱。  標籤在 Runbook 內必須是唯一的。 

### 參數集

參數集會定義將接受特定 Cmdlet 的值的強制和選擇性參數。  所有的 Cmdlet 至少有一個參數集，而某些則有多個。  如果 Cmdlet 有多個參數集，然後您必須選取可以設定參數之前，將使用參數集。  您可以設定的參數將取決於您選擇的參數集。  您可以變更選取的活動所使用的參數集 **參數設定** 並選取其他設定。  在此情況下，您設定的任何參數值都會遺失。

在下列範例中，Get-AzureVM Cmdlet 有兩個參數集。  您無法設定參數值，直到您選取其中一個參數集。  ListAllVMs 參數集用於傳回所有虛擬機器，而且具有單一的選擇性參數。  GetVMByServiceand VMName 是用於指定您想要傳回的虛擬機器，而且具有一個強制和兩個選擇性參數。

![參數集](media/automation-graphical-authoring-intro/parameter-set.png)

#### 參數值

當您指定參數的值時，您會選取資料來源，以便判斷將如何指定值。  特定參數可使用的資料來源將取決於該參數的有效值。  例如，Null 不是不允許 null 值的參數可用的選項。

| 資料來源 | 說明 |
|:---|:---|
|常數值|輸入參數的值。  這只適用於下列資料型別：Int32、Int64、String、Boolean、DateTime、Switch。 |
|活動輸出|從優先於工作流程中的目前活動的活動輸出。  將列出所有有效的活動。  對參數值只選取要使用它的輸出的活動。  如果活動會輸出具有多個屬性的物件，您可以在選取活動之後輸入名稱屬性。|
|Runbook 輸入參數|選取 Runbook 的輸入參數做為活動參數的輸入。|  
|自動化變數資產|選取「自動化變數」做為輸入。|  
|自動化認證資產|選取「自動化認證」做為輸入。|  
|自動化憑證資產|選取「自動化憑證」做為輸入。|  
|自動化連線資產|選取「自動化連線」做為輸入。| 
|PowerShell 運算式|指定簡單 [PowerShell 運算式](#powershell-expressions)。  在活動和用於參數值的結果之前，將會評估運算式。  您可以使用變數來參照活動或 Runbook 的輸入參數的輸出。|
|空字串|空字串值。|
|Null|Null 值。|
|取消選取|清除先前設定的任何值。|


#### 選擇性的其他參數

所有的 Cmdlet 可選擇提供額外的參數。  這些是 PowerShell 一般參數或其他自訂參數。  您會看到一個文字方塊，您可以在其中使用 PowerShell 語法提供參數。  例如，若要使用 **Verbose** 一般參數，您會指定 **"-Verbose: $True"**。

### 工作流程指令碼控制項

工作流程指令碼控制項是一種特殊的活動，會接受 PowerShell 工作流程程式碼，以便提供可能無法使用的功能。  這不是完整的工作流程，但必須包含有效的 PowerShell 工作流程程式碼行。  它不能接受參數，但它可以對活動輸出和 Runbook 輸入參數使用變數。  活動的任何輸出會加入至資料匯流排中，除非它在加入至 Runbook 的輸出中沒有連出的連結。

例如，下列程式碼會執行使用稱為$NumberOfDays 的 Runbook 輸入變數的日期計算。  然後會將計算的日期時間傳送為輸出，供 Runbook 中後續的活動使用。

    $DateTimeNow = InlineScript{(Get-Date).ToUniversalTime()}
    $DateTimeStart = InlineScript{($using:DateTimeNow).AddDays(-$using:NumberOfDays)}
    $DateTimeStart


## 連結和工作流程

A **連結** 圖形化 runbook 中連接兩個活動。  就會在畫布上顯示為箭號，從來源活動指向目的地活動。  活動會以箭頭的方向執行，在來源活動完成之後，目的地活動就會啟動。  

### 建立連結

在兩個活動之間建立連結，方法是選取來源活動，然後按一下圖形底部的圓形。  將箭頭拖曳到目的地活動和版本。

![建立連結](media/automation-graphical-authoring-intro/create-link.png)

在 [組態] 分頁中選取連結來設定其屬性。  這包括下表中說明的連結類型。

| 連結類型 | 說明 |
|:---|:---|
| 管線 | 目的地活動會對從來源活動輸出的每一個物件執行一次。  如果來源活動不會產生任何輸出，則不會執行目的地活動。  從來源活動輸出以物件形式提供。  |
| 順序 | 目的地活動只會執行一次。  它會從來源活動接收物件的陣列。  從來源活動輸出以物件陣列形式提供。 |

### 啟動活動

圖形化 Runbook 會從沒有連入連結的任何活動開始。  這通常是只有一個活動，做為 Runbook 的啟動活動。  如果多個活動沒有連入的連結，Runbook 將以平行方式執行它們來開始。  然後，在每個完成時它會遵循連結以執行其他活動。

### 條件

當您指定連結的條件時，只有條件解析為 true 時才會執行目的地活動。  您通常會在條件中使用 $ActivityOutput 變數來擷取從來源活動輸出。  

針對管線連結，您會指定單一物件的條件，並且來源活動會對每個物件輸出評估條件。  然後目的地活動會對符合條件的每個物件執行。  例如，具有 Get-AzureVM 的來源活動，可以對條件式管線連結使用下列語法，僅擷取目前正在執行的虛擬機器。  

    $ActivityOutput['Get-AzureVM'].PowerState -eq 'Started'

針對順序連結，因為單一陣列會傳回包含從來源活動輸出的所有物件，因此只會評估一次條件。  因此，順序連結不能用於篩選 (如管線連結)，但只能判斷下一個活動是否會執行。  下列程式碼顯示評估 Get-AzureVM 的輸出的相同範例，以判斷正在執行的虛擬機器。  在此情況下，程式碼會逐步進行陣列中的每個物件，並且如果至少一個虛擬機器正在執行，即解析為 true。  目的地活動會負責剖析此資料。

    $test = $false
    $VMs = $ActivityOutput['Get-AzureVm']
    Foreach ($VM in VMs)
    {
        If ($VM.PowerState –eq 'Started')
            {
                $test = $true
            }
    }
    $test

使用條件式連結時，可從來源活動提供該分支中的其他活動使用的資料將由條件篩選。  如果活動是多個連結的來源，可用於每個分支中可用活動的資料取決於連接到該分支的連結中的條件。

例如下面的 Runbook 中的來源活動會取得所有虛擬機器。  它有兩個條件式連結和不含條件的連結。  第一個條件式連結會使用運算式 *$ActivityOutput [' Get-azurevm ']。PowerState-eq 'Started'* 來篩選目前正在執行的虛擬機器。  第二個使用運算式 *$ActivityOutput [' Get-azurevm ']。PowerState eq 'Stopped'* 來篩選目前已停止虛擬機器。  

![條件式連結範例](media/automation-graphical-authoring-intro/conditional-links.png)

遵循第一個連結並使用 Get-AzureVM 活動輸出的任何活動，將只會取得 Get-AzureVM 執行時啟動的虛擬機器。  遵循第二個連結的任何活動，將只會取得 Get-AzureVM 執行時已停止的虛擬機器。  遵循第三個連結的任何活動，將會取得所有虛擬機器，不論其執行狀態為何。

### 接合

接合是一個特殊的活動，將會等候直到所有內送的分支完成。  這可讓您以平行方式執行多個活動，並確保所有活動已完成再繼續。

雖然接合可以有無限的數量的連入連結，但這些連結只有一個可以是管線。  內送的順序連結數目不受限制。  您可以使用多個內送的管線連結建立接合，並儲存 Runbook，但是執行時將會失敗。

下列範例是 Runbook 的一個部分，其會啟動一組虛擬機器，同時下載修補程式以套用到這些機器。  接合用來確保兩個程序都已完成，才繼續 Runbook。

![接合](media/automation-graphical-authoring-intro/junction.png)

### 循環

循環是目的地活動連結回到其來源活動，或到最後連結回其來源的另一個活動。  圖形化編寫目前不允許循環。  如果您的 Runbook 有循環，它會適當地儲存，但是它執行時會收到錯誤。

![循環](media/automation-graphical-authoring-intro/cycle.png)

### 迴圈

迴圈是重複活動達指定的次數或不斷重複它，直到符合特定條件為止。  圖形化 Runbook 中目前不支援迴圈。   

### 在活動之間共用資料

是由連出連結活動輸出的任何資料寫入至 *databus* runbook。  Runbook 中的任何活動可以在資料匯流排上使用資料來填入參數值，或納入指令碼程式碼。  活動可以存取工作流程中任何先前的活動的輸出。     

資料如何寫入至資料匯流排取決於活動上的連結類型。  如 **管線**, ，資料會輸出為多個物件。  如 **順序** 連結，資料會輸出為陣列。  如果只有一個值，則會輸出為單一元素陣列。

您可以使用兩種方法之一來存取資料匯流排上的資料。  第一個使用 **活動輸出** 資料來源來填入另一個活動的參數。  如果輸出是物件，您可以指定單一屬性。

![活動輸出](media/automation-graphical-authoring-intro/activity-output-datasource.png)

您也可以擷取中活動的輸出 **PowerShell 運算式** 資料來源或從 **工作流程指令碼** 使用 ActivityOutput 變數的活動。  如果輸出是物件，您可以指定單一屬性。  ActivityOutput 變數使用下列語法。

    $ActivityOutput['Activity Label']
    $ActivityOutput['Activity Label'].PropertyName 

### 檢查點

設定的相同指示 [檢查點](automation-powershell-workflow/#checkpoints) 在 runbook 中適用於圖形化 runbook。  您可以在您要設定檢查點的位置加入 Checkpoint-Workflow Cmdlet 的活動。  然後，如果 Runbook 在不同的背景工作從這個檢查點開始，您應該使用 Add-AzureAccount 遵循此活動。 


## 向 Azure 資源驗證

Azure 自動化中的大部分 Runbook 將會需要向 Azure 資源驗證。  此驗證所使用的一般方法是使用 Add-azureaccount cmdlet 搭配 [認證資產](http://msdn.microsoft.com/library/dn940015.aspx) ，代表 Azure 的帳戶具有存取權的 Active Directory 使用者。  這會討論 [設定 Azure 自動化](automation-configuring.md)。

您可以在 Add-AzureAccount 活動之後，將認證資產加入至畫布，以將這項功能加入至圖形化 Runbook。  Add-AzureAccount 會對其輸入使用認證活動。  下列範例就將此說明。

![驗證活動](media/automation-graphical-authoring-intro/authentication-activities.png)

您必須在開始 Runbook 時，和每個檢查點之後進行驗證。  這表示在任何 Checkpoint-Workflow 活動之後加入額外的 Add-AzureAccount 活動。 因為您可以使用相同認證，因此不需要額外的認證活動 

![活動輸出](media/automation-graphical-authoring-intro/authentication-activity-output.png)

## Runbook 輸入和輸出

### Runbook 輸入

Runbook 可能需要使用者透過 Azure 預覽入口網站啟動 Runbook 時輸入或如果目前的 Runbook 為子系，則需要從另一個 Runbook 輸入。
例如，如果您有一個會建立虛擬機器的 Runbook，您可能需要在每次啟動 Runbook 時提供資訊，例如虛擬機器的名稱和其他屬性。  

您透過定義一或多個輸入參數來接受 Runbook 的輸入。  您會在每次啟動 Runbook 時提供值給這些參數。  使用 Azure 預覽入口網站啟動 Runbook 時，它會提示您為 Runbook 的每個輸入參數提供值。

您可以按一下來存取 runbook 的輸入的參數 **輸入和輸出** runbook 工具列上的按鈕。  

![Runbook 輸入輸出](media/automation-graphical-authoring-intro/runbook-edit-input-output.png) 

這會開啟 **輸入和輸出** 控制項，您可以在其中編輯現有的輸入的參數或按一下 [建立新 **加入輸入**。 

![加入輸入](media/automation-graphical-authoring-intro/runbook-edit-add-input.png)

下表中的屬性定義每個輸入參數。

|屬性|說明|
|:---|:---|
| 名稱 | 參數的唯一名稱。  這只能包含數字字元，而且不能包含空格。 |
| 說明 | 輸入參數的選擇性描述。  |
| 類型 | 對參數值預期的資料型別。  提示輸入時，Azure 預覽入口網站會對每個參數的資料型別提供適當的控制。 |
| 強制 | 指定是否必須提供參數的值。  如果您未對未定義預設值的每個強制參數提供值，則無法啟動 Runbook。 |
| 預設值 | 如果未提供其中一個的值，要對參數指定什麼值。  這可以是 Null 或特定值。 |


### Runbook 輸出

沒有連出連結的任何活動所建立的資料將會加入至 [runbook 的輸出](http://msdn.microsoft.com/library/azure/dn879148.aspx)。  輸出會隨著 Runbook 工作儲存，並且在 Runbook 用作子項時提供給父 Runbook 使用。  


## PowerShell 運算式

圖形化撰寫的優點之一是提供您以 PowerShell 的基本知識建立 Runbook 的能力。  目前，您需要知道雖然填入的特定 PowerShell 有點 [參數值](#activities) 和設定 [連結條件](#links-and-workflow)。  本節提供 PowerShell 運算式的快速簡介，供不熟悉的使用者參考。  PowerShell 的完整詳細資料位於 [使用 Windows PowerShell 撰寫指令碼](http://technet.microsoft.com/library/bb978526.aspx)。 


### PowerShell 運算式資料來源

您可以使用 PowerShell 運算式做為資料來源來填入值 [活動參數](#activities) 一些 PowerShell 程式碼的結果。  這可以是執行一些簡單函式的單行程式碼，或執行一些複雜邏輯的多行程式碼。  未指派給變數的任何命令輸出都會輸出到參數值。 

例如，下列命令會輸出目前的日期。 

    Get-Date

下列命令會從目前的日期建立字串並將它指派給變數。  然後將變數的內容傳送至輸出 

    $string = "The current date is " + (Get-Date)
    $string

下列命令會評估目前的日期並傳回表示當天是工作日或週末的字串。 

    $date = Get-Date
    if (($date.DayOfWeek = "Saturday") -or ($date.DayOfWeek = "Sunday")) { "Weekend" }
    else { "Weekday" }
    
 

### 活動輸出

若要在 Runbook 中使用上一個活動的輸出，請以下列語法使用 $ActivityOutput 變數。

    $ActivityOutput['Activity Label'].PropertyName

例如，您可能有一個活動，具有需要虛擬機器名稱的屬性，在此情況下您可以使用下列運算式。

    $ActivityOutput['Get-AzureVm'].Name

如果是需要虛擬機器物件的屬性而非只是單純的屬性，則您要使用下列語法傳回整個物件。

    $ActivityOutput['Get-AzureVm']

您也可以在更複雜的運算式中使用活動的輸出，例如串連文字到虛擬機器名稱的下列運算式。

    "The computer name is " + $ActivityOutput['Get-AzureVm'].Name


### 條件

使用 [比較運算子](https://technet.microsoft.com/library/hh847759.aspx) 來比較值，或判斷值是否符合指定的模式。  比較會傳回 $true 或 $false 的值。

例如，下列條件決定是否與活動的虛擬機器名稱 *Get-azurevm* 目前 *停止*。 

    $ActivityOutput["Get-AzureVM"].PowerState –eq "Stopped"

以下條件會檢查是否有相同的虛擬機器不是在任何狀態 *停止*。

    $ActivityOutput["Get-AzureVM"].PowerState –ne "Stopped"

您可以加入多個條件 [邏輯運算子](https://technet.microsoft.com/library/hh847789.aspx) 例如 **-和** 或 **-或**。  例如，下列條件會進行檢查前一個範例中相同的虛擬機器的狀態是否 *停止* 或 *停止*。

    ($ActivityOutput["Get-AzureVM"].PowerState –eq "Stopped") -or ($ActivityOutput["Get-AzureVM"].PowerState –eq "Stopping") 


### 雜湊表

[雜湊表](http://technet.microsoft.com/library/hh847780.aspx) 是名稱/值組，傳回一組值很有用。  某些活動的屬性可能是雜湊表而不是簡單值。  雜湊表也可能稱為字典。 

使用下列語法建立雜湊表。  雜湊表可以包含任意數目的項目，但是每個項目都由一個名稱和值定義。

    @{ <name> = <value>; [<name> = <value> ] ...}

例如，下列運算式建立要在活動參數的資料來源中使用的雜湊表，這個雜湊表的值要做為網際網路搜尋。

    $query = "Azure Automation"
    $count = 10
    $h = @{'q'=$query; 'lr'='lang_ja';  'count'=$Count}
    $h

下列範例會使用稱為活動的輸出 *取得 Twitter 連接* 填入雜湊表。

    @{'ApiKey'=$ActivityOutput['Get Twitter Connection'].ConsumerAPIKey;
      'ApiSecret'=$ActivityOutput['Get Twitter Connection'].ConsumerAPISecret;
      'AccessToken'=$ActivityOutput['Get Twitter Connection'].AccessToken;
      'AccessTokenSecret'=$ActivityOutput['Get Twitter Connection'].AccessTokenSecret}



## 相關文章

- [了解 Windows PowerShell 工作流程](automation-powershell-workflow.md)
- [自動化資產](http://msdn.microsoft.com/library/azure/dn939988.aspx)
- [運算子](https://technet.microsoft.com/library/hh847732.aspx)
 

