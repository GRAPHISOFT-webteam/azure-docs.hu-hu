<properties 
    pageTitle="深入了解：Azure AD 密碼管理 | Microsoft Azure" 
    description="Azure AD 密碼管理的進階主題，包括密碼回寫的運作方式、密碼回寫安全性、密碼重設入口網站的運作方式，以及密碼重設使用哪些資料。" 
    services="active-directory" 
    documentationCenter="" 
    authors="asteen" 
    manager="kbrint" 
    editor="billmath"/>

<tags 
    ms.service="active-directory" 
    ms.workload="identity" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="11/16/2015" 
    ms.author="asteen"/>

# 深入了解密碼管理
如果您已部署密碼管理，或只是想要在部署之前先深入了解其運作方式的技術細節，本節可讓您大致了解這項服務背後的技術概念。 我們將探討以下內容：

* [**密碼回寫概觀**](#password-writeback-overview)
  - [密碼回寫的運作方式](#how-password-writeback-works)
  - [密碼回寫的支援案例](#scenarios-supported-for-password-writeback)
  - [密碼回寫的安全性模型](#password-writeback-security-model)
* [**密碼重設入口網站的運作方式**](#how-does-the-password-reset-portal-work)
  - [密碼重設使用哪些資料？](#what-data-is-used-by-password-reset)
  - [如何存取密碼為使用者重設資料](#how-to-access-password-reset-data-for-your-users)

## 密碼回寫概觀
密碼回寫是 [Azure Active Directory Connect](active-directory-aadconnect) 可啟用及目前 Azure Active Directory Premium 訂閱者所使用的元件。 如需詳細資訊，請參閱 [Azure Active Directory 版本](active-directory-editions.md)。 

密碼回寫可讓您設定雲端租用戶以在內部部署 Active Directory 中將密碼回寫給您。  此服務讓您不需設定和管理複雜的內部部署自助式密碼重設解決方案，並且會提供便利的雲端方式供您的使用者重設其內部部署密碼，無論他們身處何處。  請繼續閱讀以下內容，以了解密碼回寫的某些重要功能：

- **零延遲的意見反應。**密碼回寫是一項同步作業。  如果使用者的密碼不符合原則，或因為任何原因而無法重設或變更，他們會立即收到通知。
- **支援使用 AD FS 或其他同盟技術的使用者密碼重設。**在使用密碼回寫後，只要同盟的使用者帳戶同步處理到您的 Azure AD 租用戶，他們就能夠從雲端管理自己的內部部署 AD 密碼。
- **支援使用密碼雜湊同步的使用者密碼重設。**當密碼重設服務偵測到同步處理的使用者帳戶已啟用密碼雜湊同步處理時，我們會同時重設此帳戶的內部部署密碼和雲端密碼。
- **支援從存取面板和 Office 365 變更密碼。**當已同盟或密碼同步處理的使用者過來變更已過期或尚未過期的密碼時，我們會將這些密碼回寫到您的本機 AD 環境。
- **支援密碼時通知系統管理員重設它們從寫** [**Azure 管理入口網站**](https://manage.windowsazure.com)。  每當系統管理員重設使用者的密碼在 [Azure 管理入口網站](https://manage.windowsazure.com), ，如果該使用者為同盟或密碼同步處理，我們將在您的本機 AD，以及系統管理員所選取的密碼。  Office 系統管理入口網站目前不支援這項作業。
- **會強制執行您在內部部署 AD 密碼原則。**當使用者重設其密碼時，我們會確保它符合您的內部部署 AD 原則，然後再認可到該目錄。  這當中包括歷程記錄、複雜度、使用期限、密碼篩選和您在本機 AD 中定義的任何其他密碼限制。
- **不需要任何輸入的防火牆規則。**密碼回寫會以 Azure 服務匯流排轉送做為基礎通訊管道，這表示您不必在防火牆上開啟任何輸入連接埠，這項功能就能正常運作。
- **不支援存在於內部部署 Active Directory 中的受保護群組內的使用者帳戶。**如需受保護群組的詳細資訊，請參閱 [Active Directory 中受保護的帳戶和群組](https://technet.microsoft.com/library/dn535499.aspx)。

### 密碼回寫的運作方式
密碼回寫有三個主要元件：

- 密碼重設雲端服務 (這也會整合至 Azure AD 的密碼變更頁面)
- 租用戶特定的 Azure 服務匯流排轉送
- 內部部屬密碼重設端點

其組合方式如下圖所示：

  ![][001]

當已同盟或密碼雜湊同步處理的使用者過來重設或變更其雲端密碼時，會發生下列情況：

1.  我們會檢查看看使用者擁有哪一種密碼。  如果我們看到密碼是在內部部署中進行管理，便會確保回寫服務已啟動並正在執行。  如果情況正是如此，我們會讓使用者繼續進行，如果不是，我們會告訴使用者不能在此處重設其密碼。
2.  接下來，使用者通過適當的驗證關卡，並到達重設密碼畫面。
3.  使用者選取新的密碼，並加以確認。
4.  按一下送出，我們便會以回寫設定程序期間所建立的對稱金鑰加密純文字密碼。
5.  在加密密碼後，我們會將它放入裝載中，透過 HTTPS 通道傳送到您租用戶特定的服務匯流排轉送 (我們也會在回寫設定過程中為您設定此轉送)。  此轉送受到只有您的內部部署安裝才會知道的隨機產生密碼所保護。
6.  一旦訊息抵達服務匯流排，密碼重設端點就會自動甦醒，並看到它有擱置中的重設要求。
7.  服務接著會使用雲端錨點屬性來尋找提出要求的使用者。  此查閱若要成功，使用者物件必須存在於 AD 連接器空間、必須連結至對應的 MV 物件，而且必須連結至對應的 AAD 連接器物件。 最後，為了讓同步處理找到此使用者帳戶，AD 連接器物件與 MV 的連結上必須有同步處理規則 `Microsoft.InfromADUserAccountEnabled.xxx`。  之所以需要這樣，是因為當呼叫是來自雲端時，同步處理引擎會使用 cloudAnchor 屬性來查閱 AAD 連接器空間物件，然後順著連結回到 MV 物件，接著再順著連結回到 AD 物件。 因為相同使用者可能有多個 AD 物件 (多樹系)，同步處理引擎需仰賴 `Microsoft.InfromADUserAccountEnabled.xxx` 連結來選出正確的物件。
8.  一旦找到使用者帳戶，我們會嘗試直接在適當的 AD 樹系中重設密碼。
9.  如果密碼設定作業成功，我們會告訴使用者他們的密碼已修改完成，可以放心繼續進行其他工作。
10. 如果密碼設定作業失敗，我們會對使用者傳回錯誤，請他們再試一次。  作業可能因各種原因而失敗，例如服務已關閉、所選密碼不符合組織原則、在本機 AD 中找不到使用者等等。  其中的許多原因會有特定的訊息，讓使用者知道該怎麼做來解決問題。

### 密碼回寫的支援案例
下表描述同步處理功能的各個版本所支援的案例。  一般情況下，強烈建議您安裝最新版的 [Azure AD Connect](active-directory-aadconnect.md#download-azure-ad-connect) 如果您想要使用密碼回寫。

  ![][002]

### 密碼回寫的安全性模型
密碼回寫服務極為安全、穩固。  為了確保您的資訊受到保護，我們啟用了 4 層式的安全性模型，詳細情形如下所述。

- **租用戶特定的服務匯流排轉送** – 當您設定服務，我們會設定租用戶特定的服務匯流排轉送隨機產生的強式密碼，Microsoft 永遠無法存取受保護。
- **鎖定的密碼編譯強式密碼加密金鑰** – 建立服務匯流排轉送後，我們會建立強式對稱金鑰，用來加密的密碼，因為它來自網路傳輸。  此金鑰只會存在於您在雲端的公司密碼存放區中，並受到嚴密鎖定和稽核，情形就和目錄中的任何密碼一樣。
- **業界標準的 TLS** – 當密碼重設或變更作業發生在雲端中，我們對純文字密碼，並以公開金鑰予以加密。  然後將它放入 HTTPS 訊息中，使用 Microsoft 的 SSL 憑證透過加密通道傳送到您的服務匯流排轉送中。  該訊息抵達服務匯流排後，您的內部部署代理程式便會甦醒、使用先前產生的強式密碼向服務匯流排驗證、擷取加密的訊息、使用我們產生的私密金鑰加以解密，然後嘗試透過 AD DS SetPassword API 設定密碼。  這個步驟能讓我們在雲端上強制執行 AD 內部部屬密碼原則 (複雜度、使用期限、歷程記錄、篩選等)。
- **訊息到期原則** – 最後，如果因為某些原因訊息停留在服務匯流排因為在內部部署服務已關閉，它將會逾時，在數分鐘後移除，以便更進一步地提升安全性。

## 密碼重設入口網站的運作方式
當使用者瀏覽到密碼重設入口網站時，便會啟動一項工作流程，以判斷該使用者帳戶是否有效、使用者屬於哪個組織，該使用者密碼的管理位置，以及使用者是否有獲得功能的使用授權。  請看完下列步驟，以了解密碼重設頁面背後的邏輯。

1.  使用者在無法直接存取帳戶] 連結或降低 [https://passwordreset.microsoftonline.com](https://passwordreset.microsoftonline.com)。
2.  使用者輸入使用者識別碼並通過文字驗證。
3.  Azure AD 執行下列動作，驗證使用者是否能夠使用這項功能：
    - 檢查使用者是否已啟用這項功能，並已獲得 Azure AD 授權。
        - 如果使用者未啟用這項功能或未獲得授權，便會要求使用者連絡其系統管理員來重設其密碼。
    - 檢查使用者是否已按照系統管理員原則在其帳戶上定義正確的查問資料。
        - 如果原則只需要一項查問，則表示使用者必定已針對系統管理員原則所啟用的至少一項查問定義適當的資料。
          - 如果使用者未進行設定，便會建議使用者連絡其系統管理員來重設其密碼。
        - 如果原則需要兩項查問，則表示使用者必定已針對系統管理員原則所啟用的至少兩項查問定義適當的資料。
          - 如果使用者未進行設定，便會建議使用者連絡其系統管理員來重設其密碼。
    - 檢查使用者的密碼是否在內部部署進行管理 (已同盟或密碼雜湊同步處理)。
       - 如果在內部部署中有部署回寫和管理使用者的密碼，則允許使用者進行驗證及重設其密碼。
       - 如果在內部部署中未部署回寫但有管理使用者的密碼，則會要求使用者連絡其系統管理員來重設其密碼。
4.  如果判斷使用者能夠成功重設其密碼，則會引導使用者完成重設程序。

深入了解如何部署密碼回寫: [開始使用: Azure AD 密碼管理](active-directory-passwords-getting-started.md)。

### 密碼重設使用哪些資料？
下表概述密碼重設期間會在何處以及如何使用這些資料，並且說明其設計可如何協助您決定您的組織適合使用哪些驗證選項。 下表也會說明您代表使用者從不會驗證這些資料的輸入路徑提供資料時的所有格式需求。

> [AZURE.NOTE] 辦公室電話不會顯示註冊入口網站中，因為使用者目前無法編輯此屬性的目錄中。

<table>
          <tbody><tr>
            <td>
              <p>
                <strong>連絡方法名稱</strong>
              </p>
            </td>
            <td>
              <p>
                <strong>Azure Active Directory 資料元素</strong>
              </p>
            </td>
            <td>
              <p>
                <strong>使用位置/可供設定位置</strong>
              </p>
            </td>
            <td>
              <p>
                <strong>格式需求</strong>
              </p>
            </td>
          </tr>
          <tr>
            <td>
              <p>辦公室電話</p>
            </td>
            <td>
              <p>PhoneNumber</p>
              <p>例如 Set-MsolUser -UserPrincipalName JWarner@contoso.com -PhoneNumber "+1 1234567890x1234"</p>
            </td>
            <td>
              <p>使用位置：</p>
              <p>密碼重設入口網站</p>
              <p>可供設定位置：</p>
              <p>PhoneNumber 可從 PowerShell、DirSync、Azure 管理入口網站和 Office 系統管理入口網站進行設定</p>
            </td>
            <td>
              <p>+ccc xxxyyyzzzz (例如 +1 1234567890)</p>
              <ul>
                <li class="unordered">
                                        Must provide a country code<br><br></li>
              </ul>
              <ul>
                <li class="unordered">
                                        Must provide an area code (where applicable)<br><br></li>
              </ul>
              <ul>
                <li class="unordered">
                                        Must have provide a + in front of the country code<br><br></li>
              </ul>
              <ul>
                <li class="unordered">
                                        Must have a space between country code and the rest of the number<br><br></li>
              </ul>
              <ul>
                <li class="unordered">
                                        Extensions are not supported, if you have any extensions specified, we will strip it from the number before dispatching the phone call.<br><br></li>
              </ul>
            </td>
          </tr>
          <tr>
            <td>
              <p>行動電話</p>
            </td>
            <td>
              <p>AuthenticationPhone</p>
              <p>或</p>
              <p>MobilePhone</p>
              <p>(如果已有資料，就會使用驗證電話，否則會回復到行動電話欄位)。</p>
              <p>例如 Set-MsolUser -UserPrincipalName JWarner@contoso.com -MobilePhone "+1 1234567890x1234"</p>
            </td>
            <td>
              <p>使用位置：</p>
              <p>密碼重設入口網站</p>
              <p>註冊入口網站</p>
              <p>可供設定位置： </p>
              <p>AuthenticationPhone 可從密碼重設註冊入口網站或 MFA 註冊入口網站進行設定。</p>
              <p>MobilePhone 可從 PowerShell、DirSync、Azure 管理入口網站和 Office 系統管理入口網站進行設定</p>
            </td>
            <td>
              <p>+ccc xxxyyyzzzz (例如 +1 1234567890)</p>
              <ul>
                <li class="unordered">
                                        Must provide a country code.<br><br></li>
              </ul>
              <ul>
                <li class="unordered">
                                        Must provide an area code (where applicable).<br><br></li>
              </ul>
              <ul>
                <li class="unordered">
                                        Must have provide a + in front of the country code.<br><br></li>
              </ul>
              <ul>
                <li class="unordered">
                                        Must have a space between country code and the rest of the number.<br><br></li>
              </ul>
              <ul>
                <li class="unordered">
                                        Extensions are not supported, if you have any extensions specified, we ignore it when dispatching the phone call.<br><br></li>
              </ul>
            </td>
          </tr>
          <tr>
            <td>
              <p>替代電子郵件</p>
            </td>
            <td>
              <p>AuthenticationEmail</p>
              <p>或</p>
              <p>AlternateEmailAddresses[0] </p>
              <p>(如果已有資料，就會使用驗證電子郵件，否則會回復到替代電子郵件欄位)。</p>
              <p>注意：替代電子郵件欄位在目錄中會指定為字串陣列。  我們會使用此陣列中的第一個項目。</p>
              <p>例如 Set-MsolUser -UserPrincipalName JWarner@contoso.com -AlternateEmailAddresses "email@live.com"</p>
            </td>
            <td>
              <p>使用位置：</p>
              <p>密碼重設入口網站</p>
              <p>註冊入口網站</p>
              <p>可供設定位置： </p>
              <p>AuthenticationEmail 可從密碼重設註冊入口網站或 MFA 註冊入口網站進行設定。</p>
              <p>AlternateEmail 可從 PowerShell、Azure 管理入口網站和 Office 系統管理入口網站進行設定</p>
            </td>
            <td>
              <p>
                <a href="mailto:user@domain.com">user@domain.com</a> 或甲斐@黒川.日本</p>
              <ul>
                <li class="unordered">
                                        Emails should follow standard formatting as per .<br><br></li>
              </ul>
              <ul>
                <li class="unordered">
                                        Unicode emails are supported.<br><br></li>
              </ul>
            </td>
          </tr>
          <tr>
            <td>
              <p>安全性問題和答案</p>
            </td>
            <td>
              <p>無法在目錄中直接修改。</p>
            </td>
            <td>
              <p>使用位置：</p>
              <p>密碼重設入口網站</p>
              <p>註冊入口網站 </p>
              <p>可供設定位置： </p>
              <p>若要設定安全性問題，唯一的方法是透過 Azure 管理入口網站。</p>
              <p>若要設定指定使用者的安全性問題答案，唯一的方法是透過註冊入口網站。</p>
            </td>
            <td>
              <p>安全性問題的字元數上限和下限分別是 200 個字元和 3 個字元</p>
              <p>答案的字元數上限和下限分別是 40 個字元和 3 個字元</p>
            </td>
          </tr>
        </tbody></table>

###如何存取密碼為使用者重設資料
####可透過同步處理設定的資料
下列欄位可以從内部部署同步處理：

* 行動電話
* 辦公室電話

####可使用 Azure AD PowerShell 設定的資料
下列欄位可使用 Azure AD PowerShell 和圖形 API 存取：

* 替代電子郵件
* 行動電話
* 辦公室電話
* 驗證電話
* 驗證電子郵件

####只能使用註冊 UI 設定的資料
下列欄位才能夠存取透過 SSPR 註冊 UI (https://aka.ms/ssprsetup):

* 安全性問題和答案

####當使用者註冊時會發生什麼事？
當使用者註冊時，註冊頁面將會看到 **一律** 設定下列欄位:

* 驗證電話
* 驗證電子郵件
* 安全性問題和答案

如果您已經提供的值 **行動電話** 或 **備用電子郵件**, ，使用者可以立即使用這些重設其密碼，即使它們尚未註冊此服務。  此外，使用者會在第一次註冊時看到這些值，並可隨意加以修改。  不過，已成功註冊之後，這些值將會保存在 **驗證電話** 和 **驗證電子郵件** 欄位中分別。

這在解除封鎖大量使用者使其得以使用 SSPR，同時允許使用者在整個註冊程序中驗證這項資訊，是很有用的方式。

####使用 PowerShell 設定密碼重設資料
您可使用 Azure AD PowerShell 設定下列欄位的值。

* 替代電子郵件
* 行動電話
* 辦公室電話

若要開始，您必須先以 [下載並安裝 Azure AD PowerShell 模組](https://msdn.microsoft.com/library/azure/jj151815.aspx#bkmk_installmodule)。  一旦安裝好，即可依照下列步驟設定每一個欄位。

#####替代電子郵件
```
Connect-MsolService
Set-MsolUser -UserPrincipalName user@domain.com -AlternateEmailAddresses @("email@domain.com")
```

#####行動電話
```
Connect-MsolService
Set-MsolUser -UserPrincipalName user@domain.com -MobilePhone "+1 1234567890"
```

#####辦公室電話
```
Connect-MsolService
Set-MsolUser -UserPrincipalName user@domain.com -PhoneNumber "+1 1234567890"
```

####使用 PowerShell 讀取密碼重設資料
您可以使用 Azure AD PowerShell 閱讀下列欄位的值。

* 替代電子郵件
* 行動電話
* 辦公室電話
* 驗證電話
* 驗證電子郵件

若要開始，您必須先以 [下載並安裝 Azure AD PowerShell 模組](https://msdn.microsoft.com/library/azure/jj151815.aspx#bkmk_installmodule)。  一旦安裝好，即可依照下列步驟設定每一個欄位。

#####替代電子郵件
```
Connect-MsolService
Get-MsolUser -UserPrincipalName user@domain.com | select AlternateEmailAddresses
```

#####行動電話
```
Connect-MsolService
Get-MsolUser -UserPrincipalName user@domain.com | select MobilePhone
```

#####辦公室電話
```
Connect-MsolService
Get-MsolUser -UserPrincipalName user@domain.com | select PhoneNumber
```

#####驗證電話
```
Connect-MsolService
Get-MsolUser -UserPrincipalName user@domain.com | select -Expand StrongAuthenticationUserDetails | select PhoneNumber
```

#####驗證電子郵件
```
Connect-MsolService
Get-MsolUser -UserPrincipalName user@domain.com | select -Expand StrongAuthenticationUserDetails | select Email
```

## 連結至密碼重設文件
以下是所有 Azure AD 密碼重設文件頁面的連結： 

* [**您自己的密碼重設**](active-directory-passwords-update-your-own-password.md) -深入了解如何重設或變更您自己的密碼為系統的使用者
* [**它的運作方式**](active-directory-passwords-how-it-works.md) -了解六個不同元件服務，以及每個未
* [**開始使用**](active-directory-passwords-getting-started.md) -了解如何讓使用者重設及變更雲端或內部部署密碼
* [**自訂**](active-directory-passwords-customize.md) -了解如何自訂外觀和行為的服務，貴組織的需求
* [**最佳作法**](active-directory-passwords-best-practices.md) -了解如何快速部署且有效管理組織中的密碼
* [**深入**](active-directory-passwords-get-insights.md) -了解整合式報告功能
* [**常見問題集**](active-directory-passwords-faq.md) -取得常見問題的解答
* [**疑難排解**](active-directory-passwords-troubleshoot.md) -了解如何快速移難排解服務的問題



[001]: ./media/active-directory-passwords-learn-more/001.jpg "Image_001.jpg"
[002]: ./media/active-directory-passwords-learn-more/002.jpg "Image_002.jpg"

