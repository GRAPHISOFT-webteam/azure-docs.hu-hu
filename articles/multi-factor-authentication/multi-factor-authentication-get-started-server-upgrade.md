<properties 
    pageTitle="將 PhoneFactor Agent 升級為 Azure Multi-Factor Authentication Server" 
    description="本文件說明如何開始使用 Azure MFA Server，以及如何從舊版的 PhoneFactor Agent 升級。" 
    services="multi-factor-authentication" 
    documentationCenter="" 
    authors="billmath" 
    manager="stevenpo" 
    editor="curtland"/>

<tags 
    ms.service="multi-factor-authentication" 
    ms.workload="identity" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="11/19/2015" 
    ms.author="billmath"/>

# 將 PhoneFactor Agent 升級為 Azure Multi-Factor Authentication Server

若要將 PhoneFactor Agent v5.x 或更舊版本升級為 Azure Multi-Factor Authentication Server，您需要先解除安裝 PhoneFactor Agent 和附屬元件，接著才能安裝 Multi-Factor Authentication Server 和附屬元件。 

## 將 PhoneFactor Agent 升級為 Azure Multi-Factor Authentication Server
<ol>
<li>首先，備份 PhoneFactor 資料檔案。 預設安裝位置是 C:\Program Files\PhoneFactor\Data\Phonefactor.pfdata。


<li>如果已安裝使用者入口網站：</li>
<ol>
<li>瀏覽至安裝資料夾，然後備份 web.config 檔案。 預設安裝位置是 C:\inetpub\wwwroot\PhoneFactor。</li>


<li>如果您已在入口網站中加入自訂佈景主題，請備份 C:\inetpub\wwwroot\PhoneFactor\App_Themes 目錄下的自訂資料夾。</li>


<li>透過 PhoneFactor Agent 解除安裝使用者入口網站 (唯有安裝在與 PhoneFactor Agent 相同的伺服器時才適用) 或透過 Windows [程式和功能]。</li></ol>




<li>如果已安裝 Mobile App Web 服務:
<ol>
<li>請移至安裝資料夾，並將備份 web.config 檔案。 預設安裝位置是 C:\inetpub\wwwroot\PhoneFactorPhoneAppWebService。</li>
<li>解除安裝 Mobile App Web 服務，透過 Windows 程式和功能。</li></ol>

<li>如果已安裝 Web 服務 SDK，請在透過 PhoneFactor Agent 或透過 Windows 程式和功能解除安裝。

<li>解除安裝 PhoneFactor 代理程式，透過 Windows 程式和功能。

<li>安裝多重要素驗證伺服器。 請注意，系統會從先前 PhoneFactor Agent 安裝的登錄接續安裝路徑，因此它應該會安裝在相同位置 (如 C:\Program Files\PhoneFactor)。 新安裝將會有不同的預設安裝路徑 (如 C:\Program Files\Multi-Factor Authentication Server)。 先前 PhoneFactor Agent 所留下的資料檔案應該會在安裝期間升級，因此在安裝新的 Multi-Factor Authentication Server 之後，使用者和設定應該會留在原地。

<li>出現提示時，啟用多因素驗證伺服器，並確定它指派給正確的複寫群組。

<li>如果先前已安裝 Web 服務 SDK，請安裝新的 Web 服務 SDK，透過 Multi-factor Authentication Server 使用者介面。 請注意，預設虛擬目錄名稱現在是 "MultiFactorAuthWebServiceSdk"，而非 "PhoneFactorWebServiceSdk"。 如果您想要使用先前的名稱，必須在安裝期間變更虛擬目錄的名稱。 否則，如果您允許安裝使用新預設名稱，必須變更所有參考 Web 服務 SDK 之應用程式中的 URL (如使用者入口網站和 Mobile App Web 服務)，使其指向正確位置。

<li>如果使用者入口網站之前已安裝在 PhoneFactor Agent Server 上，安裝新多因素驗證使用者入口網站透過 Multi-factor Authentication Server 使用者介面。 請注意，預設虛擬目錄名稱現在是 "MultiFactorAuth"，而非 "PhoneFactor"。 如果您想要使用先前的名稱，必須在安裝期間變更虛擬目錄的名稱。 否則，如果您允許安裝使用新預設名稱，應該要在 Multi-Factor Authentication Server 中按一下 [使用者入口網站] 圖示，然後更新 [設定] 索引標籤中的使用者入口網站 URL。 

<li>如果使用者入口網站和/或 Mobile App Web 服務之前已安裝在 PhoneFactor Agent 不同的伺服器上:
<ol>
<li>移至安裝位置 (例如 C:\Program Files\PhoneFactor)，並將適當的安裝到其他伺服器。 使用者入口網站和 Mobile App Web 服務備有 32 位元和 64 位元安裝程式。 它們分別稱為 MultiFactorAuthenticationUserPortalSetupXX.msi 和 MultiFactorAuthenticationMobileAppWebServiceSetupXX.msi 分別。</li>
<li>若要安裝使用者入口網站網頁伺服器上，開啟以系統管理員命令提示字元並執行 MultiFactorAuthenticationUserPortalSetupXX.msi。 請注意，預設虛擬目錄名稱現在是 "MultiFactorAuth"，而非 "PhoneFactor"。 如果您想要使用先前的名稱，必須在安裝期間變更虛擬目錄的名稱。 否則，如果您允許安裝使用新預設名稱，應該要在 Multi-Factor Authentication Server 中按一下 [使用者入口網站] 圖示，然後更新 [設定] 索引標籤中的使用者入口網站 URL。 現有的使用者必須瞭解新的 URL。</li>
<li>移至使用者入口網站安裝位置 (例如 C:\inetpub\wwwroot\MultiFactorAuth) 並編輯 web.config 檔案。 在將 web.config 檔案升級為新 web.config 檔案之前備份的原始檔案中，複製 appSettings 和 applicationSettings 區段中的值。 在安裝 Web 服務 SDK 時，如果新的預設虛擬目錄名稱已保留，請變更 applicationSettings 區段中的 URL，以指向正確位置。 如果先前 web.config 檔案中的其他預設值已變更，將這些相同變更套用到新的 web.config 檔案。</li>
<li>若要安裝 Mobile App Web 服務的網頁伺服器上，開啟以系統管理員命令提示字元並執行 MultiFactorAuthenticationMobileAppWebServiceSetupXX.msi。 請注意，預設虛擬目錄名稱現在是 "MultiFactorAuthMobileAppWebService"，而非 "PhoneFactorPhoneAppWebService"。 如果您想要使用先前的名稱，必須在安裝期間變更虛擬目錄的名稱。 您也許會想要選擇較短的名稱，以便使用者在行動裝置上輸入。 否則，如果您允許安裝使用新的預設名稱，您應該按一下 Multi-factor Authentication Server 的行動應用程式圖示，並更新 Mobile App Web 服務 URL。</li>
<li>前往 Mobile App Web 服務安裝位置 (例如 C:\inetpub\wwwroot\MultiFactorAuthMobileAppWebService) 並編輯 web.config 檔案。 在將 web.config 檔案升級為新 web.config 檔案之前備份的原始檔案中，複製 appSettings 和 applicationSettings 區段中的值。 在安裝 Web 服務 SDK 時，如果新的預設虛擬目錄名稱已保留，請變更 applicationSettings 區段中的 URL，以指向正確位置。 如果先前 web.config 檔案中的其他預設值已變更，將這些相同變更套用到新的 web.config 檔案。</li></ol>


 


 
