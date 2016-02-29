<properties
   pageTitle="Azure Active Directory 稽核報告事件 | Microsoft Azure"
   description="可供檢視以及從 Azure Active Directory 下載的稽核事件"
   services="active-directory"
   documentationCenter=""
   authors="kenhoff"
   manager="mbaldwin"
   editor=""/>

<tags
   ms.service="active-directory"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="identity"
   ms.date="12/07/2015"
   ms.author="kenhoff"/>

# Azure Active Directory 稽核報告事件

*這份文件屬於 [Azure Active Directory 報告指南](active-directory-reporting-guide.md)。*

Azure Active Directory 稽核報告可協助客戶識別其 Azure Active Directory 中發生的特殊權限動作。 特殊權限動作包括提高權限變更 (例如，角色建立或密碼重設)、原則設定變更 (例如密碼原則) 或目錄設定變更 (例如，網域同盟設定變更)。 報告會提供的稽核記錄包括事件名稱、執行動作的執行者、受變更影響的目標資源，以及日期和時間 (UTC)。 客戶可透過其 Azure Active Directory 擷取稽核事件清單 [Azure 管理入口網站](https://manage.windowsazure.com/), 所述，在 [檢視存取和使用方式報告](active-directory-view-access-usage-reports.md)。


## 稽核報告事件清單
<!--- audit event descriptions should be in the past tense --->

事件                               | 事件說明
------------------------------------ | -------------------------------------------------------------------------------------------------------------------------------------------------------------------------
**使用者事件**                      |
新增使用者                             | 將使用者新增至目錄。
刪除使用者                          | 刪除目錄中的使用者。
設定授權屬性               | 設定目錄中使用者的授權屬性。
重設使用者密碼                  | 重設目錄中使用者的密碼。
變更使用者密碼                 | 變更目錄中使用者的密碼。
變更使用者授權                  | 變更指派給目錄中使用者的授權。 若要查看哪些授權已更新，請查看此事件之前或之後的「更新使用者」事件。
更新使用者                          | 更新目錄中的使用者。 [請參閱下文](#quotupdate-userquot-attributes) 可更新的屬性。
設定強制變更使用者密碼       | 設定可強制使用者在登入時變更其密碼的屬性。
**群組事件**                     |
新增群組                            | 在目錄中建立群組。
更新群組                         | 更新目錄中的群組。
刪除群組                         | 從目錄刪除群組。
將成員加入群組                  | 將成員加入目錄中的群組。
從群組移除成員             | 將成員從目錄中的群組移除。
**應用程式事件**               |
新增服務主體                | 將服務主體新增至目錄。
移除服務主體             | 移除目錄中的服務主體
新增服務主體認證    | 將認證新增至服務主體。
移除服務主體認證 | 移除服務主體的認證。
新增委派項目                 | 建立 [OAuth2PermissionGrant](https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/entity-and-complex-type-reference#OAuth2PermissionGrantEntity) 目錄中。
設定委派項目                 | 更新 [OAuth2PermissionGrant](https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/entity-and-complex-type-reference#OAuth2PermissionGrantEntity) 目錄中。
移除委派項目              | 刪除 [OAuth2PermissionGrant](https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/entity-and-complex-type-reference#OAuth2PermissionGrantEntity) 目錄中。
**角色事件**                      |
將角色成員新增至角色              | 將使用者新增至目錄角色。
移除角色的角色成員         | 從目錄角色移除使用者。
設定公司連絡人資訊      | 設定公司層級的連絡人喜好設定。 這包括用於行銷的電子郵件地址，以及 Microsoft Online Services 的相關技術通知。
**B2B 事件**                       |
已上傳的 Batch 邀請。              | 系統管理員已上傳的檔案中，包含將傳送給合作夥伴使用者的邀請。
已處理的 Batch 邀請。             | 已處理包含合作夥伴使用者邀請的檔案。
邀請外部使用者。                | 已邀請外部使用者至目錄。
兌換外部使用者的邀請。         | 外部使用者已兌換其至目錄的邀請。
新增外部使用者至群組。          | 已將目錄中群組的成員資格指派給外部使用者。
指派外部使用者至應用程式。 | 已將應用程式的直接存取權指派給外部使用者。
建立熱門租用戶               | 已透過邀請兌換在 Azure AD 中建立新的租用戶。
建立熱門使用者。                 | 已透過邀請兌換在 Azure AD 中為現有的租用戶建立使用者。
**目錄事件**                 |
將夥伴新增至公司               | 將夥伴新增至目錄。
移除公司的夥伴          | 移除目錄中的夥伴。
將網域新增至公司                | 將網域新增至目錄。
移除公司的網域           | 移除目錄中的網域。
更新網站                        | 更新目錄上的網域。
設定網域驗證            | 變更公司的預設網域設定。
設定網域的同盟設定    | 更新網域的同盟設定。
驗證網域                        | 驗證目錄上的網域。
驗證電子郵件驗證的網域         | 使用電子郵件驗證來驗證目錄上的網域。
對公司設定 DirSyncEnabled 旗標   | 設定可針對 Azure AD Sync 啟用目錄的屬性。
設定密碼原則                  | 設定使用者密碼的長度和字元限制。
設定公司資訊              | 更新公司層級資訊。 請參閱 [Get-msolcompanyinformation](https://msdn.microsoft.com/library/azure/dn194126.aspx) PowerShell cmdlet，如需詳細資訊。

<!---

仍然需要描述事件的清單:

還原應用程式
設定字串驗證原則
升級至夥伴的租用戶

--->

## 稽核報告保留
Azure AD 稽核報告中的事件會保留 180 天。 在報表上保留的相關資訊，請參閱 [Azure Active Directory 報告保留原則](active-directory-reporting-retention.md)。

若是對於儲存其稽核事件較長的保留時間感興趣的客戶，報告 API 可用來定期將稽核事件提取至不同的資料存放區。 請參閱 [開始使用報告 API](active-directory-reporting-api-getting-started.md) 如需詳細資訊。

## 每個稽核事件包含的屬性

屬性      | 說明
------------- | --------------------------------------------------------------
日期和時間 | 稽核事件發生的日期和時間
執行者         | 執行此動作的使用者或服務主體
動作        | 已執行的動作
目標        | 被執行此動作的使用者或服務主體


## 「更新使用者」屬性
「更新使用者」稽核事件包含有關哪些使用者屬性已更新的其他資訊。 每個屬性的舊值和新值都包含在內。

屬性                       | 說明
------------------------------- | -------------------------------------------------------------------------------------------------------------------------------------------------------
AccountEnabled                  | 允許使用者登入。
AssignedLicense                 | 指派給使用者的所有授權。
AssignedPlan                    | 從指派給使用者的授權產生的服務方案。
LicenseAssignmentDetail         | 指派給使用者的授權詳細資訊。 例如，如果涉及以群組為基礎的授權，這會包含授與授權的群組。
行動                          | 使用者的行動電話。
OtherMail                       | 使用者的備用電子郵件地址。
OtherMobile                     | 使用者的備用行動電話。
StrongAuthenticationMethod      | 使用者針對多因素驗證所設定的驗證方法清單，例如來自行動應用程式的語音電話、簡訊或驗證碼。
StrongAuthenticationRequirement | 是否對此使用者強制使用、啟用或停用多因素驗證。
StrongAuthenticationUserDetails | 使用者用於多因素驗證和密碼重設驗證的電話號碼、備用電話號碼和電子郵件地址。
TelephoneNumber                 | 使用者的電話號碼。

稽核記錄是許多標準規定的必要控制項。 對於使用 Azure Active Directory 稽核報告以符合其標準規定的客戶，建議客戶提交一份本說明主題的副本 (連同客戶匯出的稽核報告副本)，協助說明報告的詳細資料。 如果稽核人員想要了解 Azure 目前符合的法規，稽核人員導向至 [相容性頁面](http://azure.microsoft.com/support/trust-center/compliance/) 的 Microsoft Azure 信任中心。

