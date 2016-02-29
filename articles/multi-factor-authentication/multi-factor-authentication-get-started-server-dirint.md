<properties 
    pageTitle="Azure Multi-Factor Authentication 和 Active Directory 之間的目錄整合" 
    description="此 Azure Multi-Factor Authentication 頁面說明如何整合 Azure Multi-Factor Authentication Server 與 Active Directory，讓您可以同步處理目錄。" 
    services="multi-factor-authentication" 
    documentationCenter="" 
    authors="billmath" 
    manager="stevenpo" 
    editor="curtand"/>

<tags 
    ms.service="multi-factor-authentication" 
    ms.workload="identity" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="11/19/2015" 
    ms.author="billmath"/>

# Azure MFA Server 與 Active Directory 之間的目錄整合

[目錄整合] 區段可讓您將伺服器設定為與 Active Directory 或另一個 LDAP 目錄整合。  它可讓您設定屬性以比對目錄結構描述，並設定自動同步處理使用者。 

## Settings
根據預設，Azure Multi-Factor Authentication Server 會設定為從 Active Directory 匯入或同步處理使用者。  此索引標籤可讓您覆寫預設行為，並繫結至不同的 LDAP 目錄、ADAM 目錄或特定 Active Directory 網域控制站。  它也支援使用 LDAP 驗證來代理 LDAP 或 LDAP 繫結做為 RADIUS 目標、IIS 驗證的預先驗證，或使用者入口網站的主要驗證。  下表描述個別設定。

![設定](./media/multi-factor-authentication-get-started-server-dirint/dirint.png)

| 功能 | 說明 |
| ------- | ----------- |
| 使用 Active Directory | 選取 [使用 Active Directory] 選項，以使用 Active Directory 來匯入和同步處理。  這是預設設定。 <br>注意: 電腦必須加入網域，您必須使用 Active Directory 整合才能正常運作的網域帳戶登入。 |
| 包含受信任的網域 | 核取 [包含受信任的網域] 核取方塊，讓代理程式嘗試連線至目前網域所信任的網域、樹系中的另一個網域，或涉及樹系信任的網域。  當不從任何受信任的網域匯入或同步處理使用者時，請取消選取此核取方塊，以改善效能。  預設為核取。 |
| 使用特定 LDAP 組態 | 選取 [使用 LDAP] 選項，以使用指定的 LDAP 設定來匯入和同步處理。 注意：當選取 [使用 LDAP] 時，使用者介面會將參考從 Active Directory 變更為 LDAP。 |
| [編輯] 按鈕 | [編輯] 按鈕允許修改目前的 LDAP 組態設定。 |
| 使用屬性範圍查詢 | 指出是否應該使用屬性範圍查詢。  屬性範圍查詢可以根據另一個記錄屬性中的項目，有效率地在目錄中搜尋合格的記錄。  Azure Multi-Factor Authentication Server 可以使用屬性範圍查詢，有效率地查詢安全性群組成員的使用者。   <br>注意: 在某些情況下，屬性範圍查詢，都有支援，但不應使用。  比方說，當安全性群組包含來自多個網域的成員時，Active Directory 的屬性範圍查詢可能會有問題。  在此情況下，就不應該核取此核取方塊。 |

下表描述 LDAP 組態設定。

| 功能 | 說明 |
| ------- | ----------- |
| 伺服器 | 輸入執行 LDAP 目錄之伺服器的主機名稱或 IP 位址。  也可以指定備份伺服器 (以分號隔開)。 <br>注意: 繫結類型為 SSL 時，完整主機名稱時通常需要。 |
| 基準 DN | 輸入作為所有目錄查詢起點之基準目錄物件的辨別名稱。  例如，dc=abc,dc=com。 |
| 繫結類型 - 查詢 | 選取繫結時可用的適當繫結類型，以搜尋 LDAP 目錄。  這用於匯入、同步處理和使用者名稱解析。 <br><br>  匿名-將執行匿名繫結。  不會使用繫結 DN 和繫結密碼。  只有當 LDAP 目錄允許匿名繫結，而且權限允許查詢適當的記錄和屬性時，這才有作用。  <br><br> 簡單-繫結 DN 和繫結密碼將會傳遞以純文字的繫結至 LDAP 目錄。  這只能用於測試目的，以確認可連上伺服器且繫結帳戶具有適當的存取權。  安裝適當的憑證之後，建議改用 SSL。  <br><br> 繫結至 LDAP 目錄使用 SSL 加密 SSL-繫結 DN 和繫結密碼。  這需要在本機安裝 LDAP 目錄所信任的憑證。  <br><br> Windows-繫結使用者名稱和繫結密碼將用來安全地連線到 Active Directory 網域控制站或 ADAM 目錄。  如果 [繫結使用者名稱] 空白，將使用已登入使用者帳戶進行繫結。 |
| 繫結類型 - 驗證 | 選取適當的繫結類型供執行 LDAP 繫結驗證時使用。  請參閱＜繫結類型 - 查詢＞下的繫結類型描述。  比方說，這可將匿名繫結用於查詢，而將 SSL 繫結用於保護 LDAP 繫結驗證。 |
| 繫結 DN 或繫結使用者名稱 | 輸入帳戶的使用者記錄辨別名稱，以在繫結至 LDAP 目錄時使用。<br><br>只有在 [繫結類型] 為 [簡單] 或 [SSL] 時，才會使用繫結辨別名稱。  <br><br>輸入要繫結類型為 Windows，繫結至 LDAP 目錄時使用的 Windows 帳戶的使用者名稱。  如果空白，將使用已登入的使用者帳戶進行繫結。 |
| 繫結密碼 | 輸入用來繫結至 LDAP 目錄之繫結 DN 或使用者名稱的繫結密碼。  若要設定 Multi-Factor Auth Server AdSync 服務的密碼，必須啟用同步處理，而且此服務必須在本機電腦上執行。  此密碼會儲存在用於執行 Multi-Factor Auth Server AdSync Service 的帳戶下的 [Windows 儲存的使用者和密碼] 中。  此密碼也會儲存在用於執行 Multi-Factor Auth Server 使用者介面的帳戶下，以及用於執行 Multi-Factor Auth Server Service 的帳戶下。  <br><br> 注意: 由於密碼只會儲存在本機伺服器的 Windows 預存使用者名稱和密碼，此步驟中將需要在每個需要存取密碼的 Multi-factor Auth Server 上執行。 |
| 查詢大小限制 | 指定目錄搜尋將傳回的使用者數目上限的大小限制。  此限制應該符合 LDAP 目錄上的設定。  對於不支援分頁的大型搜尋，匯入和同步處理將會嘗試以批次方式擷取使用者。  如果此處指定的大小限制大於 LDAP 目錄上設定的限制，可能會遺漏部分使用者。 |
| 測試按鈕 | 按一下 [測試] 按鈕，測試 LDAP 伺服器繫結。  <br><br> 注意: 使用 LDAP] 選項並不需要選取，才能測試繫結。  這樣可以在使用 LDAP 設定之前先測試繫結。 |

## 篩選器
篩選可讓您在執行目錄搜尋時，設定準則來限定記錄。  您可以設定篩選以限定想要同步處理的物件範圍。  

![篩選器](./media/multi-factor-authentication-get-started-server-dirint/dirint2.png)

Azure Multi-Factor Authentication 具有下列三個選項。

- **容器篩選** -指定用來限定容器記錄時執行目錄搜尋的篩選準則。  對於 Active Directory 和 ADAM，通常會使用 (|(objectClass=organizationalUnit)(objectClass=container))。  對於其他 LDAP 目錄，應該根據目錄結構描述，使用可限定每一種容器物件的篩選準則。  <br>注意: 如果空白，((Objectclass 將會使用預設值。

- **安全性群組篩選器** -指定用來限定安全性群組記錄時執行目錄搜尋的篩選準則。  對於 Active Directory 和 ADAM，通常會使用 (&(objectCategory=group)(groupType:1.2.840.113556.1.4.804:=-2147483648))。  對於其他 LDAP 目錄，應該根據目錄結構描述，使用可限定每一種安全性群組物件的篩選準則。  <br>注意: 如果空白，(&(objectCategory=group) (groupType:1.2.840.113556.1.4.804:=-2147483648)) 將預設情況下使用。

- **使用者篩選** -指定用來限定使用者記錄時執行目錄搜尋的篩選準則。  對於 Active Directory 和 ADAM，通常會使用 (&(objectClass=user)(objectCategory=person))。  對於其他 LDAP 目錄，應該根據目錄結構描述，使用 (objectClass = inetOrgPerson) 或類似的準則。 <br>注意: 如果空白、 (& (Objectcategory 將預設情況下使用。

## 屬性
可視需要來自訂特定目錄的屬性。  這可讓您新增自訂屬性，並微調到只同步處理您需要的屬性。  每個屬性欄位的值應該是目錄結構描述中所定義之屬性的名稱。  如需詳細資訊，請參閱下表。

![屬性](./media/multi-factor-authentication-get-started-server-dirint/dirint3.png)

| 功能 | 說明 |
| ------- | ----------- |
| 唯一識別碼 | 輸入屬性的屬性名稱，做為容器、安全性群組和使用者記錄的唯一識別碼。  在 Active Directory 中，這通常是 objectGUID。  在其他 LDAP 實作中，它可能是 entryUUID 或類似的項目。  預設值是 objectGUID。 |
| ...[選取屬性] 按鈕 | 每個屬性欄位的 [...] 按鈕旁邊會顯示選取的屬性] 對話方塊，可讓您從清單中選取屬性。 <br><br>選取 [屬性] 對話方塊。<br><br>注意: 屬性可以手動輸入，並不需要符合屬性清單中的屬性。 |
| 唯一識別碼類型 | 選取唯一識別碼屬性的類型。  在 Active Directory 中，objectGUID 屬性為 GUID 類型。  在其他 LDAP 實作中，它可能是 ASCII 位元組陣列或字串類型。  預設值是 GUID。 <br><br>注意: 務必正確設定，因為同步處理項目會參考其唯一識別碼和唯一識別碼類型用來直接在目錄中尋找的物件。  當目錄實際上將值儲存為 ASCII 字元的位元組陣列時，設定為字串會導致同步處理無法正確運作。 |
| 辨別名稱 | 輸入屬性的屬性名稱，此屬性包含每一筆記錄的辨別名稱。  在 Active Directory 中，這通常是 distinguishedName。  在其他 LDAP 實作中，它可能是 entryDN 或類似的項目。  預設值是 distinguishedName。 <br><br>注意: 如果只包含辨別的名稱屬性不存在，可能會使用 adspath 屬性。  「 LDAP: / /<server>/"路徑的部分將自動刪除，只保留的辨別的名稱的物件。 |
| 容器名稱 | 輸入屬性的屬性名稱，此屬性包含容器記錄中的名稱。  從 Active Directory 匯入或新增同步處理項目時，這個屬性的值會顯示在容器階層中。  預設值是 name。 <br><br>注意: 如果不同容器使用不同的屬性做為名稱，多個容器名稱可以指定屬性中以分號分隔。  在容器物件上找到的第一個容器名稱屬性將會用來顯示其名稱。 |
| 安全性群組名稱 | 輸入屬性的屬性名稱，此屬性包含安全性群組記錄中的名稱。  從 Active Directory 匯入或新增同步處理項目時，這個屬性的值會顯示在安全性群組清單中。  預設值是 name。 |
| 使用者 | 下列屬性用於搜尋、顯示、匯入和同步處理目錄中的使用者資訊。 |
| 使用者名稱 | 輸入屬性的屬性名稱，此屬性包含使用者記錄中的使用者名稱。  這個屬性的值將用於做為 Multi-Factor Auth Server 使用者名稱。  可以指定第二個屬性做為第一個屬性的備用。  只有當第一個屬性不包含使用者的值時，才會使用第二個屬性。  預設值是 userPrincipalName 和 sAMAccountName。 |
| 名字 | 輸入屬性的屬性名稱，此屬性包含使用者記錄中的名字。  預設值是 givenName。 |
| 姓氏 | 輸入屬性的屬性名稱，此屬性包含使用者記錄中的姓氏。  預設值是 sn。 |
| 電子郵件地址 | 輸入屬性的屬性名稱，此屬性包含使用者記錄中的電子郵件地址。  電子郵件地址將用來傳送歡迎及更新電子郵件給使用者。  預設值是 mail。 |
| 使用者群組 | 輸入屬性的屬性名稱，此屬性包含使用者記錄中的使用者群組。  使用者群組可在 Multi-Factor Auth Server 管理入口網站中用來篩選代理程式和報告中的使用者。 |
| 說明 | 輸入屬性的屬性名稱，此屬性包含使用者記錄中的描述。  描述只用於搜尋。  預設值是 description。 |
| 語音通話語言 | 輸入屬性的屬性名稱，此屬性包含用於使用者語音通話的語言簡稱。 |
| 簡訊語言 | 輸入屬性的屬性名稱，此屬性包含用於使用者簡訊的語言簡稱。 |
| 電話應用程式語言 | 輸入屬性的屬性名稱，此屬性包含用於使用者電話應用程式簡訊的語言簡稱。 |
| OATH 權杖語言 | 輸入屬性的屬性名稱，此屬性包含用於使用者 OATH 權杖簡訊的語言簡稱。 |
| 電話 | 下列屬性用來匯入或同步處理使用者電話號碼。  如果未指定電話類型的屬性名稱，則從 Active Directory 匯入或新增同步處理項目時，將無法使用電話類型。 |
| Business | 輸入屬性的屬性名稱，此屬性包含使用者記錄中的商務電話號碼。  預設值是 telephoneNumber。 |
| Home | 輸入屬性的屬性名稱，此屬性包含使用者記錄中的住家電話號碼。  預設值是 homePhone。 |
| 呼叫器 | 輸入屬性的屬性名稱，此屬性包含使用者記錄中的呼叫器號碼。  預設值是 pager。 |
| 行動 | 輸入屬性的屬性名稱，此屬性包含使用者記錄中的行動電話號碼。  預設值是 mobile。 |
| 傳真 | 輸入屬性的屬性名稱，此屬性包含使用者記錄中的傳真號碼。  預設值是 facsimileTelephoneNumber。 |
| IP 電話 | 輸入屬性的屬性名稱，此屬性包含使用者記錄中的 IP 電話號碼。  預設值是 ipPhone。 |
| 自訂 | 輸入屬性的屬性名稱，此屬性包含使用者記錄中的 |
|  | 自訂電話號碼。  預設值為空白。 |
| 分機 | 輸入屬性的屬性名稱，此屬性包含使用者記錄中的分機電話號碼。  分機欄位的值只做為主要電話號碼的分機。  預設值為空白。 <br><br>注意: 如果未指定分機屬性，延伸可以是 [電話] 屬性的一部分。  分機的前面應該加上 'x' 才能剖析。  例如，555-123-4567 x890 會形成電話號碼 555-123-4567 和分機 890。 |
| [還原預設值] 按鈕 | 按一下 [還原預設值] 按鈕，可將所有屬性還原為預設值。  在一般 Active Directory 或 ADAM 結構描述中，預設值應該可以正確運作。 |

若要編輯屬性，只要按一下 [屬性] 索引標籤上的 [編輯] 按鈕。  這會顯示一個視窗讓您編輯屬性。

![編輯屬性](./media/multi-factor-authentication-get-started-server-dirint/dirint4.png)

## 同步處理
同步處理可讓 Azure Multi-factor 使用者資料庫與 Active Directory 或另一個輕量型目錄存取通訊協定輕量型目錄存取通訊協定 (LDAP) 目錄中的使用者同步處理。  此程序類似於從 Active Directory 手動匯入使用者，但會定期輪詢 Active Directory 使用者和安全性群組是否有需要處理的變更。  它也支援停用或移除已從容器或安全性群組中移除的使用者，以及移除已從 Active Directory 中刪除的使用者。

Multi-Factor Auth ADSync 服務是會定期輪詢 Active Directory 的 Windows 服務。  這並不會與 Azure AD Sync 或 Azure AD Connect 混淆。  Multi-factor Auth ADSync，雖然根據類似的程式碼基底，旨在說明 Azure 多因素驗證伺服器。  它會安裝成「已停止」狀態，在設定為執行時才由 Multi-Factor Auth Server 服務啟動。  如果您有多伺服器 Multi-Factor Auth Server 組態，Multi-Factor Auth ADSync 只能在單一伺服器上執行。

Multi-Factor Auth ADSync 服務使用 Microsoft 所提供的 DirSync LDAP 伺服器擴充功能，有效率地輪詢變更。  此 DirSync 控制呼叫端必須具有「目錄變更」權限和 DS-Replication-Get-Changes 擴充控制存取權限。  根據預設，這些權限指派給網域控制站上的 Administrator 和 LocalSystem 帳戶。  根據預設，Multi-Factor Auth AdSync 服務設定為以 LocalSystem 身分執行。  因此，在網域控制站上執行此服務最簡單。  如果您將此服務設定為一律執行完整同步處理，則此服務可以用權限較少的帳戶執行。  這比較沒有效率，但需要的帳戶權限較少。

如果設定為使用 LDAP，且 LDAP 目錄支援 DirSync 控制，則輪詢使用者和安全性群組的變更時，就如同輪詢 Active Directory 的變更一樣。  如果 LDAP 目錄不支援 DirSync 控制，將會在每個週期執行完整同步處理。

![同步處理](./media/multi-factor-authentication-get-started-server-dirint/dirint5.png)

如需 [同步處理] 索引標籤上每個設定的其他資訊，請參閱下表。

| 功能 | 說明 |
| ------- | ----------- |
| 啟用與 Active Directory 同步處理 | 核取時，Multi-Factor Auth Server 服務將開始定期輪詢 Active Directory 的變更。 <br><br>請注意: 必須加入至少一個同步處理項目，並立即同步處理之前必須先執行 Multi-factor Auth Server 服務會開始處理變更。 |
| 同步處理頻率 | 指定 Multi-Factor Auth Server 服務在輪詢和處理變更之間等候的時間間隔。 <br><br> 注意: 指定的間隔是每個週期開始之間的時間。  如果處理變更的時間超過間隔，服務將立即再次輪詢。 |
| 移除已不在 Active Directory 中的使用者 | 核取時，Multi-Factor Auth Server 服務會處理 Active Directory 刪除的使用者標記，並移除相關的 Multi-Factor Auth Server 使用者。 |
| 永遠執行完整同步處理 | 核取時，Multi-Factor Auth Server 服務將一律執行完整同步處理。  取消核取時，Multi-Factor Auth Server 服務只會查詢已變更的使用者來執行增量同步處理。  預設為核取。 <br><br> 注意: 若未選取，增量同步處理只能用於在目錄支援 DirSync 控制且用來繫結至目錄的帳戶具有適當的權限執行 DirSync 增量查詢時。  如果帳戶沒有適當的權限，或有多個網域涉及同步處理，則建議執行完整同步處理。 |
| 超過 X 個使用者將停用或移除時，需要系統管理員核准 | 同步處理項目可以設定為停用或移除不再是項目的容器或安全性群組成員的使用者。  以防萬一，當停用或移除的使用者數目超過臨界值時，可能需要系統管理員核准。  核取時，超過指定的臨界值就需要核准。  預設值為 5，範圍從 1 到 999。 <br><br> 核准之前會先傳送電子郵件通知給系統管理員。 電子郵件通知提供指示來檢閱及核准停用和移除使用者。  Multi-Factor Auth Server 使用者介面啟動時會提示要求核准。 |

 **立即同步處理** 按鈕可讓您執行完整同步處理指定的同步處理項目。  每當加入、修改、移除或重新排列同步處理項目時，就需要完整同步處理。  也需要如此，Multi-Factor Auth AdSync 服務才能運作，因為它會設定服務輪詢增量變更的起始點。  如果同步處理項目已變更，但尚未執行完整同步處理，當您瀏覽至另一個區段或關閉使用者介面時，將會提示您「立即同步處理」。

 **移除** ] 按鈕可讓系統管理員從 Multi-factor Auth Server 同步處理項目清單中刪除一個或多個同步處理項目。

>[AZURE.WARNING]同步處理項目記錄已移除之後，就無法復原。 如果不小心刪除，則必須重新加入同步處理項目記錄。

同步處理項目已從 Multi-Factor Auth Server 中移除。  Multi-Factor Auth Server 服務將不再處理這些同步處理項目。 

[上移] 和 [下移] 按鈕可讓系統管理員變更同步處理項目的順序。  順序很重要，因為相同的使用者可能是多個同步處理項目 (例如容器和安全性群組) 的成員。  在同步處理期間套用至使用者的設定，將來自使用者相關聯的清單中的第一個同步處理項目。  因此，同步處理項目應該依優先順序放置。

>[AZURE.TIP]移除同步處理項目之後應該執行完整同步處理。  排序同步處理項目之後應該執行完整同步處理。  按一下 [立即同步處理] 按鈕以執行完整同步處理。

## Multi-Factor Auth Server
可以設定其他 Multi-Factor Auth Server 做為備份 RADIUS Proxy、LDAP Proxy 或是用於 IIS 驗證。 所有代理程式會共用同步處理設定。 但其中只有一個代理程式會執行 Multi-Factor Auth Server 服務。 此索引標籤可讓您選取應該啟用同步處理的 Multi-Factor Auth Server。

![Multi-Factor-Auth Server](./media/multi-factor-authentication-get-started-server-dirint/dirint6.png)
