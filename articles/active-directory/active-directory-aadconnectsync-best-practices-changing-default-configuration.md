<properties
    pageTitle="Azure AD Connect 同步處理：變更預設組態的最佳作法 | Microsoft Azure"
    description="提供變更 Azure AD Connect 同步處理預設組態的最佳作法。"
    services="active-directory"
    documentationCenter=""
    authors="andkjell"
    manager="stevenpo"
    editor=""/>

<tags
    ms.service="active-directory"
    ms.workload="identity"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="11/11/2015"
    ms.author="markusvi;andkjell"/>


# Azure AD Connect 同步處理：變更預設組態的最佳作法

本主題的目的旨在說明支援及不支援的 Azure AD Connect 同步處理變更。

Azure AD Connect 所建立的組態適用於大部分同步內部部署 Active Directory 與 Azure AD 的「現狀」環境。 不過，在某些情況下，組態必須套用某些變更以滿足特定需要或需求。

## 服務帳戶的變更
Azure AD Connect 同步處理會使用安裝精靈所建立的服務帳戶執行。 這個服務帳戶會存放同步處理所使用的資料庫加密金鑰。 它是使用 127 個字元長的密碼所建立的，而且密碼已設定為永不到期。

- 它是 **不支援** 變更或重設服務帳戶的密碼。 這樣做將會損毀加密金鑰，服務將無法存取資料庫且將無法啟動。

## 排程器的變更
Azure AD Connect 同步處理已設定為每 3 小時同步處理身分識別資料。 安裝期間會建立一個使用服務帳戶執行的排定工作，此帳戶會具備操作同步處理伺服器的權限。

- 它是 **不支援** 來變更排程工作。 不知道服務帳戶的密碼。 請參閱 [變更服務帳戶](#changes-to-the-service-account)
- 它是 **不支援** 來同步處理頻率高於預設 3 小時。

## 同步處理規則的變更

安裝精靈所提供的組態應該適用於最常見的案例。 萬一您需要對組態進行變更，則您必須遵循這些規則，以便仍能具備支援的組態。

- 唯一支援現成可用同步處理規則的變更是將它停用。 任何其他變更可能會在升級期間遺失。
- 如果您需要對現成可用的規則進行任何其他變更，則請複製該規則，然後停用原始的規則。 同步處理規則編輯器將提示您，並協助您達成此目標。
- 使用同步處理規則編輯器，匯出您的自訂同步處理規則。 這提供可用來在災害復原情況下輕鬆地重建它們的 PowerShell 指令碼。

>[AZURE.WARNING] 全新的同步處理規則具有指紋。 如果您變更這些規則，將不再符合憑證指紋，而未來當您嘗試套用新版的 Azure AD Connect 時可能會遇到問題。 僅利用本文所述的方式進行變更。

### 刪除不必要的同步處理規則
請勿刪除現成可用的同步處理規則。此規則將會在下次升級期間重新建立。

在某些情況下，安裝精靈所產生的組態將不適用於您的拓撲。 例如，如果您具備帳戶與資源樹系拓撲，但已在具備 Exchange 結構描述的帳戶樹系中擴充該結構描述，則系統將針對帳戶樹系以及資源樹系建立適用於 Exchange 的規則。 在此情況下，我們需要停用適用於 Exchange 的同步處理規則。

![已停用同步處理規則](./media/active-directory-aadconnectsync-best-practices-changing-default-configuration/exchangedisabledrule.png)

在上圖中，安裝精靈已在帳戶樹系中找到舊的 Exchange 2003 結構描述。 這是在 Fabrikam 的環境中引進資源樹系之前新增的。 若要確保不會同步處理任何來自舊的 Exchange 實作的屬性，就必須以所述的方式停用同步處理規則。

### 變更現成可用的規則
如果您需要對現成可用的規則進行變更，則您應該複製該現成可用的規則，然後停用原始的規則。 接著對複製的規則進行變更。 同步處理規則編輯器將協助您完成此動作。 當您開啟現成可用的規則時，即會顯示此對話方塊：

![對現成可用的規則發出警告](./media/active-directory-aadconnectsync-best-practices-changing-default-configuration/warningoutofboxrule.png)

選取 **是** 來建立規則的複本。 隨即會開啟複製的規則。

![複製的規則](./media/active-directory-aadconnectsync-best-practices-changing-default-configuration/clonedrule.png)

在這個複製的規則上，對範圍、聯結和轉換進行任何必要變更。

### 請勿傳送屬性
有兩種方式可防止傳送屬性。 第一個安裝精靈] 中，且可讓您 [移除選取的屬性](active-directory-aadconnect-get-started-custom.md#azure-ad-app-and-attribute-filtering)。 如果您先前不曾同步處理該屬性，則適用這個選項。 不過，如果您已開始同步處理這個屬性，且稍後利用這個功能來移除它，則同步處理引擎將停止管理屬性，而現有的值將會留在 Azure AD 中。

如果您想要移除屬性的值並確定未來將不會傳送它，就需要改為建立自訂規則。

在 Fabrikam，我們在同步處理到雲端的屬性中發現了一些不應該存在的屬性。 我們想要確定這些屬性會從 Azure AD 中移除。

![擴充屬性](./media/active-directory-aadconnectsync-best-practices-changing-default-configuration/badextensionattribute.png)

- 建立新的輸入同步處理規則，並填入描述
![說明](./media/active-directory-aadconnectsync-best-practices-changing-default-configuration/syncruledescription.png)
- 建立類型的屬性流程 **運算式** 和與來源 **AuthoritativeNull**。 常值 **AuthoritativeNull** 表示此值必須空白 MV 中，即使較低的優先順序同步處理規則會填入值。
![擴充屬性](./media/active-directory-aadconnectsync-best-practices-changing-default-configuration/syncruletransformations.png)
- 儲存同步處理規則。 啟動 **同步處理服務**, ，找到的連接器，再選取 **執行**, ，和 **完整同步處理**。 這將會重新計算所有屬性流程。
- 請確認預期的變更會藉由搜尋連接器空間即將匯出。
![分段刪除](./media/active-directory-aadconnectsync-best-practices-changing-default-configuration/deletetobeexported.png)

## 後續步驟
深入了解 [Azure AD Connect 同步處理](active-directory-aadconnectsync-whatis.md) 組態。

深入了解 [整合內部部署身分識別與 Azure Active Directory](active-directory-aadconnect.md)。


