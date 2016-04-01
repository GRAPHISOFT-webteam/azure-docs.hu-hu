<properties
    pageTitle="使用 PowerShell 部署和管理 Windows Server/用戶端的備份 | Microsoft Azure"
    description="了解如何使用 PowerShell 部署和管理 Azure 備份"
    services="backup"
    documentationCenter=""
    authors="aashishr"
    manager="shreeshd"
    editor=""/>

<tags
    ms.service="backup"
    ms.workload="storage-backup-recovery"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="11/20/2015"
    ms.author="aashishr"; "jimpark"/>


# 使用 PowerShell 部署和管理 Windows Server/Windows 用戶端的 Azure 備份
本文說明如何使用 PowerShell 設定 Windows Server 或 Windows 用戶端上的 Azure 備份，以及管理備份和還原。

[AZURE.INCLUDE [arm-getting-setup-powershell](../../includes/arm-getting-setup-powershell.md)]

## 設定和註冊
開始：

1. [下載最新的 PowerShell](https://github.com/Azure/azure-powershell/releases) (所需的最低版本是 ︰ 1.0.0)
2. 啟用 Azure 備份指令程式，藉由切換至 *AzureResourceManager* 模式使用 **Switch-azuremode** 指令程式 ︰

```
PS C:\> Switch-AzureMode AzureResourceManager
```

PowerShell 可以自動化下列設定和註冊工作：

- 建立備份保存庫
- 安裝 Azure 備份代理程式
- 向 Azure 備份服務進行註冊
- 網路設定
- 加密設定

### 建立備份保存庫

> [AZURE.WARNING] 第一次使用 Azure Backup 的客戶，您需要註冊 Azure 備份提供者來搭配您的訂閱。 這可以透過執行下列命令來完成：Register-AzureProvider -ProviderNamespace "Microsoft.Backup"

您可以建立新的備份保存庫使用 **新增 AzureRMBackupVault** 指令程式。 備份保存庫是 ARM 資源，因此您必須將它放在資源群組內。 在提高權限的 Azure PowerShell 主控台中，執行下列命令：

```
PS C:\> New-AzureResourceGroup –Name “test-rg” -Region “West US”
PS C:\> $backupvault = New-AzureRMBackupVault –ResourceGroupName “test-rg” –Name “test-vault” –Region “West US” –Storage GeoRedundant
```

您可以取得一份在給定的訂閱使用的所有備份保存庫 **Get AzureRMBackupVault** 指令程式。


### 安裝 Azure 備份代理程式
在安裝 Azure 備份代理程式之前，您必須在 Windows Server 上下載並提供安裝程式。 您可以取得最新版本的安裝程式從 [Microsoft 下載中心](http://aka.ms/azurebackup_agent) 或從備份保存庫儀表板] 頁面。 安裝程式儲存至容易存取的位置，例如 * C:\Downloads\*。

若要安裝代理程式，請在已提升權限的 PowerShell 主控台中執行下列命令：

```
PS C:\> MARSAgentInstaller.exe /q
```

這會以所有預設選項安裝代理程式。 安裝作業會在背景中進行幾分鐘。 如果您未指定 */nu* 選項則 **Windows Update** 檢查是否有任何更新安裝結束時會開啟視窗。 安裝之後，代理程式會顯示在已安裝的程式清單中。

若要查看已安裝的程式清單，請移至 **控制台** > **程式** > **程式和功能**。

![已安裝代理程式](./media/backup-client-automation/installed-agent-listing.png)

#### 安裝選項

若要查看所有可透過命令列執行的選項，請使用下列命令：

```
PS C:\> MARSAgentInstaller.exe /?
```

可用的選項包括：

| 選項 | 詳細資料 | 預設值 |
| ---- | ----- | ----- |
| /q | 無訊息安裝 | - |
| /p: 「 位置 」 | Azure 備份代理程式的安裝資料夾的路徑。 | C:\Program Files\Microsoft Azure Recovery Services 代理程式 |
| /s: 「 位置 」 | Azure 備份代理程式的快取資料夾的路徑。 | C:\Program Files\Microsoft Azure Recovery Services Agent\Scratch |
| /m | 參加 Microsoft update | - |
| /nu | 不檢查更新安裝完成之後 | - |
| /d | 解除安裝 Microsoft Azure 復原服務代理程式 | - |
| /ph | Proxy 主機位址 | - |
| /po | Proxy 主機連接埠號碼 | - |
| /pu | Proxy 主機使用者名稱 | - |
| /pw | Proxy 密碼 | - |


### 向 Azure 備份服務進行註冊
您可以向 Azure 備份服務之前，您必須確定 [必要條件](backup-configure-vault.md) 符合。 您必須：

- 具備有效的 Azure 訂用帳戶
- 具備備份保存庫

若要下載的保存庫認證，請執行 **Get AzureRMBackupVaultCredentials** 在方便的位置在像是 Azure PowerShell 主控台和存放區中的指令程式 * C:\Downloads\*。

```
PS C:\> $credspath = "C:\"
PS C:\> $credsfilename = Get-AzureRMBackupVaultCredentials -Vault $backupvault -TargetLocation $credspath
PS C:\> $credsfilename
f5303a0b-fae4-4cdb-b44d-0e4c032dde26_backuprg_backuprn_2015-08-11--06-22-35.VaultCredentials
```

向保存庫註冊電腦是使用 [Start-obregistration](https://technet.microsoft.com/library/hh770398%28v=wps.630%29.aspx) 指令程式 ︰

```
PS C:\> $cred = $credspath + $credsfilename
PS C:\> Start-OBRegistration -VaultCredentials $cred -Confirm:$false

CertThumbprint      : 7a2ef2caa2e74b6ed1222a5e89288ddad438df2
SubscriptionID      : ef4ab577-c2c0-43e4-af80-af49f485f3d1
ServiceResourceName : test-vault
Region              : West US

Machine registration succeeded.
```

> [AZURE.IMPORTANT] 請勿使用相對路徑來指定保存庫認證檔。 您必須提供絕對路徑做為 Cmdlet 的輸入。

### 網路設定
若 Windows 電腦是透過 Proxy 伺服器連線到網際網路，您也可以提供 Proxy 設定給代理程式。 本範例未使用 Proxy 伺服器，因此會明確地清除任何 Proxy 相關資訊。

您也可以針對給定的一組當週天數，使用 [```work hour bandwidth```] 和 [```non-work hour bandwidth```] 的選項來控制頻寬使用情形。

設定 proxy 和頻寬的詳細資料使用 [Set-obmachinesetting](https://technet.microsoft.com/library/hh770409%28v=wps.630%29.aspx) 指令程式 ︰

```
PS C:\> Set-OBMachineSetting -NoProxy
Server properties updated successfully.

PS C:\> Set-OBMachineSetting -NoThrottle
Server properties updated successfully.
```

### 加密設定
傳送至 Azure 備份的備份資料會進行加密來保護資料的機密性。 加密複雜密碼是在還原時用來解密資料的「密碼」。

```
PS C:\> ConvertTo-SecureString -String "Complex!123_STRING" -AsPlainText -Force | Set-OBMachineSetting
Server properties updated successfully
```

> [AZURE.IMPORTANT] 安全一旦設定之後，保留複雜密碼資訊。 若沒有此複雜密碼，您將無法從 Azure 還原資料。

## 備份檔案和資料夾
Windows Server 和用戶端的所有 Azure 備份都經由原則來掌管。 原則包含三個部分：

1. A **備份排程** ，指定何時需要採取和與服務同步處理備份。
2. A **保留排程** 可指定多久保留在 Azure 中的復原點。
3. A **檔案包含/排除規格** ，指出要備份的項目。

本文件中要說明如何將備份自動化，因此我們假設還未設定任何選項。 首先建立新的備份原則使用 [New-obpolicy](https://technet.microsoft.com/library/hh770416.aspx) 指令程式，並使用它。

```
PS C:\> $newpolicy = New-OBPolicy
```

此時，原則是空的，需要使用其他 Cmdlet 來定義要包含或排除的項目、執行備份的時機，以及儲存備份的位置。

### 設定備份排程
原則 3 部分的第一個是備份排程，這建立使用 [New-obschedule](https://technet.microsoft.com/library/hh770401) 指令程式。 備份排程會定義何時需要進行備份。 建立排程時，您需要指定 2 個輸入參數：

- **一周天數** 應該執行備份。 您可以只選一天或選擇一週的每天都執行備份工作，或任意選取要一週的哪幾天。
- **每日時間** 要執行備份。 您可以定義多達一天 3 個不同時段來觸發備份。

例如，您可以設定每週六和日下午 4 點執行備份原則。

```
PS C:\> $sched = New-OBSchedule -DaysofWeek Saturday, Sunday -TimesofDay 16:00
```

備份排程需要與原則相關聯，這可透過使用 [Set-obschedule](https://technet.microsoft.com/library/hh770407) 指令程式。

```
PS C:> Set-OBSchedule -Policy $newpolicy -Schedule $sched
BackupSchedule : 4:00 PM Saturday, Sunday, Every 1 week(s) DsList : PolicyName : RetentionPolicy : State : New PolicyState : Valid
```
### 設定保留原則
保留原則會定義所建立備份工作的復原點保留時間長度。 當建立新的保留原則使用 [New-obretentionpolicy](https://technet.microsoft.com/library/hh770425) 指令程式，您可以指定的備份復原點需要使用 Azure 備份保留的天數。 下例將保留原則設為 7 天。

```
PS C:\> $retentionpolicy = New-OBRetentionPolicy -RetentionDays 7
```

保留原則必須與主要原則使用此指令程式相關聯 [Set-obretentionpolicy](https://technet.microsoft.com/library/hh770405):

```
PS C:\> Set-OBRetentionPolicy -Policy $newpolicy -RetentionPolicy $retentionpolicy

BackupSchedule  : 4:00 PM
                  Saturday, Sunday,
                  Every 1 week(s)
DsList          :
PolicyName      :
RetentionPolicy : Retention Days : 7

                  WeeklyLTRSchedule :
                  Weekly schedule is not set

                  MonthlyLTRSchedule :
                  Monthly schedule is not set

                  YearlyLTRSchedule :
                  Yearly schedule is not set

State           : New
PolicyState     : Valid
```
### 包含及排除要備份的檔案
```OBFileSpec``` 物件會定義備份中要包含與排除的檔案。 此組規則可劃分出電腦上要保護的檔案和資料夾。 您可以設定所需數量的檔案包含或排除規則，並建立其與原則的關聯。 建立新的 OBFileSpec 物件時，您可以：

- 指定要包含的檔案和資料夾
- 指定要排除的檔案和資料夾
- 指定要遞迴備份資料夾中的資料，或是否僅備份指定資料夾中最上層的檔案。

後者可利用在 New-OBFileSpec 命令中使用 -NonRecursive 旗標來達成。

在下例中，我們會備份磁碟區 C: 和 D:，並排除 Windows 資料夾和任何暫存資料夾中的作業系統二進位檔。 若要這麼做我們將建立兩個檔案使用的規格 [New-obfilespec](https://technet.microsoft.com/library/hh770408) cmdlet-一個包含和一個排除。 一旦建立檔案規格之後，它們與原則使用相關聯 [Add-obfilespec](https://technet.microsoft.com/library/hh770424) 指令程式。

```
PS C:\> $inclusions = New-OBFileSpec -FileSpec @("C:\", "D:\")

PS C:\> $exclusions = New-OBFileSpec -FileSpec @("C:\windows", "C:\temp") -Exclude

PS C:\> Add-OBFileSpec -Policy $newpolicy -FileSpec $inclusions

BackupSchedule  : 4:00 PM
                  Saturday, Sunday,
                  Every 1 week(s)
DsList          : {DataSource
                  DatasourceId:0
                  Name:C:\
                  FileSpec:FileSpec
                  FileSpec:C:\
                  IsExclude:False
                  IsRecursive:True

                  , DataSource
                  DatasourceId:0
                  Name:D:\
                  FileSpec:FileSpec
                  FileSpec:D:\
                  IsExclude:False
                  IsRecursive:True

                  }
PolicyName      :
RetentionPolicy : Retention Days : 7

                  WeeklyLTRSchedule :
                  Weekly schedule is not set

                  MonthlyLTRSchedule :
                  Monthly schedule is not set

                  YearlyLTRSchedule :
                  Yearly schedule is not set

State           : New
PolicyState     : Valid


PS C:\> Add-OBFileSpec -Policy $newpolicy -FileSpec $exclusions

BackupSchedule  : 4:00 PM
                  Saturday, Sunday,
                  Every 1 week(s)
DsList          : {DataSource
                  DatasourceId:0
                  Name:C:\
                  FileSpec:FileSpec
                  FileSpec:C:\
                  IsExclude:False
                  IsRecursive:True
                  ,FileSpec
                  FileSpec:C:\windows
                  IsExclude:True
                  IsRecursive:True
                  ,FileSpec
                  FileSpec:C:\temp
                  IsExclude:True
                  IsRecursive:True

                  , DataSource
                  DatasourceId:0
                  Name:D:\
                  FileSpec:FileSpec
                  FileSpec:D:\
                  IsExclude:False
                  IsRecursive:True

                  }
PolicyName      :
RetentionPolicy : Retention Days : 7

                  WeeklyLTRSchedule :
                  Weekly schedule is not set

                  MonthlyLTRSchedule :
                  Monthly schedule is not set

                  YearlyLTRSchedule :
                  Yearly schedule is not set

State           : New
PolicyState     : Valid
```

### 套用原則
現在原則物件已完成，且具有關聯的備份排程、保留原則及包含/排除的檔案清單。 此原則現在已經過認可，適合用於 Azure 備份。 新建立的原則套用之前請確定沒有任何現有的備份原則，使用與伺服器相關聯 [Remove-obpolicy](https://technet.microsoft.com/library/hh770415) 指令程式。 移除原則時，系統會提示確認。 若要略過確認，Cmdlet 中請使用 ```-Confirm:$false```。

```
PS C:> Get-OBPolicy | Remove-OBPolicy
Microsoft Azure Backup Are you sure you want to remove this backup policy? This will delete all the backed up data. [Y] Yes [A] Yes to All [N] No [L] No to All [S] Suspend [?] Help (default is "Y"):
```

認可原則物件已完成使用 [Set-obpolicy](https://technet.microsoft.com/library/hh770421) 指令程式。 系統將提示您進行確認。 若要略過確認，Cmdlet 中請使用 ```-Confirm:$false```。

```
PS C:> Set-OBPolicy -Policy $newpolicy
Microsoft Azure Backup Do you want to save this backup policy ? [Y] Yes [A] Yes to All [N] No [L] No to All [S] Suspend [?] Help (default is "Y"):
BackupSchedule : 4:00 PM Saturday, Sunday, Every 1 week(s)
DsList : {DataSource
         DatasourceId:4508156004108672185
         Name:C:\
         FileSpec:FileSpec
         FileSpec:C:\
         IsExclude:False
         IsRecursive:True,

         FileSpec
         FileSpec:C:\windows
         IsExclude:True
         IsRecursive:True,

         FileSpec
         FileSpec:C:\temp
         IsExclude:True
         IsRecursive:True,

         DataSource
         DatasourceId:4508156005178868542
         Name:D:\
         FileSpec:FileSpec
         FileSpec:D:\
         IsExclude:False
         IsRecursive:True
    }
PolicyName : c2eb6568-8a06-49f4-a20e-3019ae411bac
RetentionPolicy : Retention Days : 7
              WeeklyLTRSchedule :
              Weekly schedule is not set

              MonthlyLTRSchedule :
              Monthly schedule is not set

              YearlyLTRSchedule :
              Yearly schedule is not set
State : Existing PolicyState : Valid
```

您可以檢視現有的備份原則使用 [Get-obpolicy](https://technet.microsoft.com/library/hh770406) 指令程式。 您可以向下鑽研進一步使用 [Get-obschedule](https://technet.microsoft.com/library/hh770423) 指令程式的備份排程和 [Get-obretentionpolicy](https://technet.microsoft.com/library/hh770427) cmdlet 保留原則

```
PS C:> Get-OBPolicy | Get-OBSchedule
SchedulePolicyName : 71944081-9950-4f7e-841d-32f0a0a1359a
ScheduleRunDays : {Saturday, Sunday}
ScheduleRunTimes : {16:00:00}
State : Existing

PS C:> Get-OBPolicy | Get-OBRetentionPolicy
RetentionDays : 7
RetentionPolicyName : ca3574ec-8331-46fd-a605-c01743a5265e
State : Existing

PS C:> Get-OBPolicy | Get-OBFileSpec
FileName : *
FilePath : \?\Volume{b835d359-a1dd-11e2-be72-2016d8d89f0f}\
FileSpec : D:\
IsExclude : False
IsRecursive : True

FileName : *
FilePath : \?\Volume{cdd41007-a22f-11e2-be6c-806e6f6e6963}\
FileSpec : C:\
IsExclude : False
IsRecursive : True

FileName : *
FilePath : \?\Volume{cdd41007-a22f-11e2-be6c-806e6f6e6963}\windows
FileSpec : C:\windows
IsExclude : True
IsRecursive : True

FileName : *
FilePath : \?\Volume{cdd41007-a22f-11e2-be6c-806e6f6e6963}\temp
FileSpec : C:\temp
IsExclude : True
IsRecursive : True
```

### 執行臨機操作備份
設定備份原則之後，即會根據排程進行備份。 也可以使用觸發臨機操作備份是 [Start-obbackup](https://technet.microsoft.com/library/hh770426) 指令程式 ︰

```
PS C:> Get-OBPolicy | Start-OBBackup
Taking snapshot of volumes...
Preparing storage...
Estimating size of backup items...
Estimating size of backup items...
Transferring data...
Verifying backup...
Job completed.
The backup operation completed successfully.
```

## 從 Azure 備份還原資料
本節將引導您逐步完成自動化從 Azure 備份復原資料。 此工作涉及下列步驟：

1. 挑選來源磁碟區
2. 選擇要還原的備份點
3. 選擇要還原的項目
4. 觸發還原程序

### 挑選來源磁碟區
若要從 Azure 備份還原項目，您必須先識別項目的來源。 我們在 Windows Server 或 Windows 用戶端的內容中執行命令，則已經識別電腦。 找出來源的下一步是識別包含它的磁碟區。 磁碟區或備份這台電腦可以藉由執行擷取的來源清單 [Get-obrecoverablesource](https://technet.microsoft.com/library/hh770410) 指令程式。 此命令會傳回這部伺服器/用戶端備份的所有來源陣列。

```
PS C:> $source = Get-OBRecoverableSource
PS C:> $source
FriendlyName : C:\
RecoverySourceName : C:\
ServerName : myserver.microsoft.com

FriendlyName : D:\
RecoverySourceName : D:\
ServerName : myserver.microsoft.com
```

### 選擇要還原的備份點
要擷取備份點清單，請執行 [Get-obrecoverableitem](https://technet.microsoft.com/library/hh770399.aspx) cmdlet 搭配適當的參數。 在範例中，我們會選擇來源磁碟區的最新備份點 *d:* 並用它來還原特定檔案。

```
PS C:> $rps = Get-OBRecoverableItem -Source $source[1]
IsDir : False
ItemNameFriendly : D:\
ItemNameGuid : \?\Volume{b835d359-a1dd-11e2-be72-2016d8d89f0f}\
LocalMountPoint : D:\
MountPointName : D:\
Name : D:\
PointInTime : 18-Jun-15 6:41:52 AM
ServerName : myserver.microsoft.com
ItemSize :
ItemLastModifiedTime :

IsDir : False
ItemNameFriendly : D:\
ItemNameGuid : \?\Volume{b835d359-a1dd-11e2-be72-2016d8d89f0f}\
LocalMountPoint : D:\
MountPointName : D:\
Name : D:\
PointInTime : 17-Jun-15 6:31:31 AM
ServerName : myserver.microsoft.com
ItemSize :
ItemLastModifiedTime :
```
物件 ```$rps``` 是備份點陣列。 第一個元素是最新備份點，且第 N 個元素是最舊的備份點。 為了選擇最新的備份點，我們使用 ```$rps[0]```。

### 選擇要還原的項目
若要識別的正確檔案或資料夾還原，遞迴地使用 [Get-obrecoverableitem](https://technet.microsoft.com/library/hh770399.aspx) 指令程式。 如此一來，可單獨使用 ```Get-OBRecoverableItem``` 來瀏覽資料夾的階層。

在此範例中，如果我們想要還原檔案 *finances.xls* 我們可以使用物件參考 ```$filesFolders[1]```。

```
PS C:> $filesFolders = Get-OBRecoverableItem $rps[0]
PS C:> $filesFolders
IsDir : True
ItemNameFriendly : D:\MyData\
ItemNameGuid : \?\Volume{b835d359-a1dd-11e2-be72-2016d8d89f0f}\MyData\
LocalMountPoint : D:\
MountPointName : D:\
Name : MyData
PointInTime : 18-Jun-15 6:41:52 AM
ServerName : myserver.microsoft.com
ItemSize :
ItemLastModifiedTime : 15-Jun-15 8:49:29 AM

PS C:> $filesFolders = Get-OBRecoverableItem $filesFolders[0]
PS C:> $filesFolders
IsDir : False
ItemNameFriendly : D:\MyData\screenshot.oxps
ItemNameGuid : \?\Volume{b835d359-a1dd-11e2-be72-2016d8d89f0f}\MyData\screenshot.oxps
LocalMountPoint : D:\
MountPointName : D:\
Name : screenshot.oxps
PointInTime : 18-Jun-15 6:41:52 AM
ServerName : myserver.microsoft.com
ItemSize : 228313
ItemLastModifiedTime : 21-Jun-14 6:45:09 AM

IsDir : False
ItemNameFriendly : D:\MyData\finances.xls
ItemNameGuid : \?\Volume{b835d359-a1dd-11e2-be72-2016d8d89f0f}\MyData\finances.xls
LocalMountPoint : D:\
MountPointName : D:\
Name : finances.xls
PointInTime : 18-Jun-15 6:41:52 AM
ServerName : myserver.microsoft.com
ItemSize : 96256
ItemLastModifiedTime : 21-Jun-14 6:43:02 AM
```

您也可以使用 ```Get-OBRecoverableItem``` Cmdlet 來搜尋要還原的項目。 在範例中，搜尋 *finances.xls* 我們無法取得檔案控制代碼執行下列命令 ︰

```
PS C:\> $item = Get-OBRecoverableItem -RecoveryPoint $rps[0] -Location "D:\MyData" -SearchString "finance*"
```

### 觸發還原程序
為了觸發還原程序，我們首先需要指定復原選項。 這可以經由使用 [New-obrecoveryoption](https://technet.microsoft.com/library/hh770417.aspx) 指令程式。 例如，假設我們想要將檔案還原到 *C:\temp*。 我們也假設我們想要略過目的地資料夾的檔案已存在於 *C:\temp*。 為了建立此復原選項，使用下列命令：

```
PS C:\> $recovery_option = New-OBRecoveryOption -DestinationPath "C:\temp" -OverwriteType Skip
```

立即觸發還原使用 [Start-obrecovery](https://technet.microsoft.com/library/hh770402.aspx) 命令對所選 ```$item``` 的輸出從 ```Get-OBRecoverableItem``` 指令程式 ︰

```
PS C:\> Start-OBRecovery -RecoverableItem $item -RecoveryOption $recover_option
Estimating size of backup items...
Estimating size of backup items...
Estimating size of backup items...
Estimating size of backup items...
Job completed.
The recovery operation completed successfully.
```


## 解除安裝 Azure 備份代理程式
使用下列命令即可解除安裝 Azure 備份代理程式：

```
PS C:\> .\MARSAgentInstaller.exe /d /q
```

若要從電腦解除安裝代理程式二進位檔，請考量下列後果：

- 這會從電腦移除檔案篩選器，並停止追蹤變更。
- 會從電腦移除所有原則資訊，但服務中會繼續保存這些原則資訊。
- 會移除所有備份排程，且不再進行任何備份。

不過，Azure 中儲存的資料會留著，根據您所設定的保留原則設定予以保留。 較舊的時間點會自動過時。

## 遠端管理
關於 Azure 備份代理程式、原則和資料來源的所有管理工作，皆可透過 PowerShell 遠端完成。 要遠端管理的電腦必須經過正確準備。

依預設，WinRM 服務會設定為手動啟動。 必須將啟動類型設定為 *自動* 並應該啟動該服務。 若要確認 WinRM 服務正在執行，狀態] 屬性的值應該是 *執行*。

```
PS C:\> Get-Service WinRM

Status   Name               DisplayName
------   ----               -----------
Running  winrm              Windows Remote Management (WS-Manag...
```

應該將 PowerShell 設定為可以遠端執行。

```
PS C:\> Enable-PSRemoting -force
WinRM is already set up to receive requests on this computer.
WinRM has been updated for remote management.
WinRM firewall exception enabled.

PS C:\> Set-ExecutionPolicy unrestricted -force
```

現在可以遠端管理電腦 - 從代理程式的安裝開始。 例如，下列指令碼會將代理程式複製到遠端電腦並進行安裝。

```
PS C:\> $dloc = "\\REMOTESERVER01\c$\Windows\Temp"
PS C:\> $agent = "\\REMOTESERVER01\c$\Windows\Temp\MARSAgentInstaller.exe"
PS C:\> $args = "/q"
PS C:\> Copy-Item "C:\Downloads\MARSAgentInstaller.exe" -Destination $dloc - force

PS C:\> $s = New-PSSession -ComputerName REMOTESERVER01
PS C:\> Invoke-Command -Session $s -Script { param($d, $a) Start-Process -FilePath $d $a -Wait } -ArgumentList $agent $args
```

## 後續步驟
如需 Windows Server/用戶端的 Azure 備份詳細資訊，請參閱

- [Azure 備份的簡介](backup-configure-vault.md)
- [備份 Windows 伺服器](backup-azure-backup-windows-server.md)


