<properties
    pageTitle="什麼是 PowerApps Enterprise 及如何開始使用 | Microsoft Azure"
    description="開始使用 PowerApps Enterprise 並建立 App Service 環境"
    services=""
    suite="powerapps"
    documentationCenter="" 
    authors="linhtranms"
    manager="dwrede"
    editor=""/>

<tags
   ms.service="powerapps"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na" 
   ms.date="11/29/2015"
   ms.author="litran"/>

# 什麼是 Microsoft PowerApps Enterprise？

Microsoft PowerApps Enterprise 是新的 Microsoft Azure 服務。 PowerApps Enterprise 讓公司內的商務使用者也能建立行動應用程式，且可讓 IT 管理員嚴格管理這些應用程式。 

透過具有功能區和 Excel 公式的 Office 樣式介面，商務使用者可以建立下列用途的應用程式：

- 使用折線圖、圓形圖和橫條圖來顯示資料，就如同在 Excel 中一樣。
- 建立包括按鈕、插入文字和格式化日期等功能的使用者介面。
- 加入多重選擇控制項，包括清單方塊、下拉式清單和選項按鈕。
- 上傳影像、拍照和播放音訊/視訊檔案。
- 使用「後端」系統來顯示和更新資訊，例如 Excel 和 SQL Server。
- 將預先建置的 App Service 連接器加入您的應用程式，以連接到 PaaS 應用程式，例如 Twitter 和 SharePoint。

IT 管理員可以管理由公司內的商務使用者所建立的應用程式，包括：

- 管理這些應用程式和管理使用者對這些應用程式的存取權。
- 建立 API 和不同資料來源的連接。 
- 管理使用者對 API 的存取權和這些資料來源的連接。 

## 如何開始使用？

首先，決定是否需要建立新的 Azure Active Directory (Azure AD) 租用戶。 如果您已經有 AD 租用戶，然後只要啟用 PowerApps 企業在 Azure 入口網站中，將您的 Api 和連線，並開始管理 (開始 *步驟 2* 本主題中)。 

如果您沒有 AD 租用戶，請在 Azure 入口網站中啟用 PowerApps Enterprise，加入您的 API 和連接，然後開始管理。 

本主題列出詳細資訊。

## 步驟 1：建立新的或使用現有的 Azure AD 租用戶

若要開始使用 PowerApps Enterprise，您需要 Azure Active Directory (Azure AD) 租用戶。 租用戶是專用的 Azure AD 服務執行個體。 

當您的組織或公司註冊 Microsoft Azure 雲端服務時，例如 Microsoft Intune 或 Office 365，您的組織會自動收到和擁有 AD 租用戶。 每個 Azure AD 租用戶彼此不同且分開。 

使用下列步驟來判斷是否已有租用戶或如何建立新的租用戶。

#### 具有現有的 Office 365 訂用帳戶
如果您有現有的 Office 365 訂用帳戶 (或 Microsoft Dynamic CRM Online、Enterprise Mobility Suite 或其他 Microsoft 服務)，您就有 Azure Active Directory 的免費訂用帳戶。 您可以使用 Azure AD 來建立和管理使用者及群組帳戶。 如果您無法登入 Azure 入口網站，您可能需要啟動訂用帳戶。 若要這樣做，請移至 [Azure 傳統入口網站](https://manage.windowsazure.com/), ，並完成註冊處理一次。 使用這些 [步驟](https://technet.microsoft.com/library/dn832618.aspx) 來存取 Azure AD 租用戶。 

#### 具有與 Microsoft 帳戶相關聯的現有 Azure 訂用帳戶
如果您先前已使用個別的 Microsoft 帳戶 (hotmail 或 live) 註冊 Azure 訂用帳戶，則您已經有租用戶！ 在 [Azure 傳統入口網站](https://manage.windowsazure.com/), ，**預設租用戶** 下方會列出 **所有項目** 歲以下 **Active Directory**。 您可以視需要任意使用此租用戶，但您可能會想要建立組織系統管理員帳戶。

若要這樣做，請使用下列步驟。 或者，您可能會想要建立新的租用戶，並按照類似的程序在該租用戶中建立系統管理員。

1.  登入 [Azure 傳統入口網站](https://manage.windowsazure.com/) 與個別帳戶。
2.  選取 **Active Directory**「 在左側的功能表列中。 
3.  選取 **預設目錄** 中可用的目錄清單。
4.  選取 **使用者** 頂端的索引標籤。 在 [源自] 資料行中只有一個以 [Microsoft 帳戶] 列出的使用者。
5.  選取 **新增使用者** 底部。 
6.  在 **新增使用者表單**, ，輸入下列詳細資料:  
    
    屬性 |描述
--- | ---
使用者類型 |您的組織中的新使用者
使用者名稱 |選擇此系統管理員的使用者名稱
名字 / 顯示名字 |輸入您的值
角色 |全域管理員
替代電子郵件地址 |輸入您的值
選擇性 |啟用多因素驗證  
    
    選取 **建立** 按鈕以完成並顯示暫時密碼。

完成時，為新的管理使用者記錄此暫時密碼。 若要變更暫時密碼，登入 [https://login.microsoftonline.com](https://login.microsoftonline.com) 利用這個新的使用者帳戶和變更密碼。 您也可以使用替代電子郵件，直接將密碼傳送給使用者。


#### 具有與組織帳戶相關聯的現有 Azure 訂用帳戶
如果您先前已使用組織帳戶註冊 Azure 訂用帳戶，則您已經有租用戶！ 在 [Azure 傳統入口網站](https://manage.windowsazure.com/), ，租用戶下方會列出 **所有項目** 並同時在 [ **Active Directory**。 您可以視需要任意使用此租用戶。 您也可以建立新的租用戶使用 **新增** 功能表底部的工作列中。

#### 以上皆無並想要從頭開始
如果上述都無法套用到您，然後移至 [https://account.windowsazure.com/organization](https://account.windowsazure.com/organization) 來註冊 Azure 以新的組織。 註冊後，您就會有自己的 Azure AD 租用戶和您選擇的網域名稱。 在 [Azure 傳統入口網站](https://manage.windowsazure.com/), ，您可以看到在租用戶 **Active Directory** 左功能表中。

## 步驟 2：建立新的或使用現有的 Azure 訂用帳戶
既然您已經有 AD 租用戶，您可以建立新的或使用現有的 Azure 訂用帳戶。 Azure AD 訂用帳戶包含數個版本。 對於 PowerApps Enterprise，您可以使用免費版。 不過，如果您需要使用 AAD Proxy 建立內部部署資料的混合式連線，您需要 Basic 或 Premium 版本。 

[Azure Active Directory 版本](../active-directory/active-directory-editions.md) 列出更多的功能。 


## 步驟 3：以您的 Azure 工作訂用帳戶註冊 PowerApps Enterprise
> [AZURE.NOTE] 以下步驟需要系統管理員登入 Azure 入口網站，並提交一個要求的訂閱。 

既然您已經有 AD 租用戶和 Azure 訂用帳戶，您的工作訂用帳戶管理員就可以註冊 PowerApps Enterprise。 管理員也可以在公司內新增使用者來「管理」PowerApps (包括指定使用者的權限)，並管理已發佈至您的 Azure 訂用帳戶的 PowerApps。 

不含申請 PowerApps 企業，您會看到沒有存取分頁當您移到 [Azure 入口網站](https://portal.azure.com/) 和 PowerApps 瀏覽。  註冊您的公司， **訂用帳戶管理員** 可以移至 [PowerApps](http://go.microsoft.com/fwlink/p/?LinkId=716848) 連絡我們深入了解定價和註冊程序。 

![][4]  

一旦您完成註冊程序，且準備使用 PowerApps Enterprise，您就可以： 

- 新增您公司內的使用者，並使用 [角色型存取控制](../role-based-access-control-configure.md), ，讓這些使用者 PowerApps 系統管理員角色，以存取 PowerApps 企業入口網站。
- 建立專用的 App Service 環境來裝載您 PowerApps。
- 建立 API 和連接，以便在專用的 App Service 環境內執行。
- 除了 PowerApps 中建立的應用程式，您還可以將其他應用程式加入至 App Service 環境，包括 Web 應用程式、行動應用程式、API 應用程式和邏輯應用程式。

在下列範例中，Contoso 公司已註冊 PowerApps。 在這個新 **PowerApps** 刀鋒視窗中，您可以看到應用程式使用此 app service 環境建立不同類型的摘要。 在 **管理 Api**, ，您可以看到 Microsoft 建立的 Api (受管理的 Microsoft) 的摘要與 Contoso 建立 Api (管理的 IT):  

![範例公司 PowerApps 刀鋒視窗][3]  


## 步驟 4：建立 App Service 環境
建立 App Service 環境來裝載 PowerApps API 和連接，以及行動應用程式、Web 應用程式、 API 應用程式和邏輯應用程式。 

App Service 環境是隔離和專用的環境，可安全地執行您的所有應用程式。 計算資源專屬於 App Service 環境，完全只用來執行您的應用程式。 當您註冊 PowerApps Enterprise 時，將有一個專用的 App Service 環境用來裝載您的應用程式所使用的 API 和連接。 此 App Service 環境是「特殊」類型的 App Service 環境。 具體而言： 

- 您可以自由使用此 App Service 環境。 它繫結至您的公司，而不是訂用帳戶。
- 您可以設定 API 和連接供您在 PowerApps 中建立的應用程式使用。 但是，您也可以將 Web 應用程式、行動應用程式、邏輯應用程式和 API 應用程式加入至這個相同的 App Service 環境。 
- 計費固定且納入 PowerApps Enterprise 中。  
- 自動會管理您的規模。 您不需要監視環境來決定是否需要額外的計算資源。

一般的 Azure App Service 環境有不同的功能。 請參閱 [App Service 環境簡介](../app-service-app-service-environment-intro.md) 的這些詳細資料。

#### 開始使用的需求

- Azure 公司訂用帳戶
- 訂用帳戶管理員，您公司內 [PowerApps 公司註冊](powerapps-get-started-azure-portal.md) 企業。
- 您已使用 PowerApps 管理員 (PowerApps 的「擁有者」) 或訂用帳戶管理員登入 Azure 入口網站。

### 建立 App Service 環境
> [AZURE.NOTE] 如果看不到要建立 app service 環境的選項，然後是已建立您的租用戶。 若要檢視詳細資料，請選取 **設定** 開啟 app service 環境。

1. 在 [Azure 入口網站](https://portal.azure.com/), 、 使用工作帳戶登入。 例如，使用登入 */users/yourusername*@*YourCompany*。 com。 當您這樣做時，將會自動登入您的公司訂用帳戶。
 
2. 選取 **瀏覽** 在工作列上:  
![瀏覽 PowerApps][1]
  
3. 在清單中，您可以捲動以尋找 PowerApps 或輸入 *powerapps*:  
![搜尋 PowerApps][2]  

4. 在 **PowerApps** 分頁中，選取 **若要開始建立 App Service 環境** 或選取 **App Service 環境** 下 *設定*:  
![][5]

    > [AZURE.NOTE] 如果您按一下 **若要開始建立 App Service 環境**, ，您會看到一個額外的分頁，App Service 環境的相關詳細資料。 直接按一下該刀鋒視窗上的 [建立] 連結，以開啟 [建立] 刀鋒視窗。 

5. 接下來，輸入名稱，選取您想要使用的訂用帳戶，選取或建立新的資源群組，然後選取虛擬網路。 **請注意** ，選擇虛擬網路之後，它就無法變更:  
![][6]  
如需如何使用 app service 環境的虛擬網路，請參閱 [如何建立 App Service 環境](../app-service-web-how-to-create-an-app-service-environment.md)。 

6. 選取 **新增** 完成建立 app service 環境。 

> [AZURE.TIP] 在建立使用 PowerApps app service 環境時，不會提示設定計算資源集區。 自動會處理此步驟。

請記住，您也可以將 Web 應用程式、行動應用程式和 API 應用程式加入至這個 App Service 環境。 事實上，您可以在此環境中加入 App Service 環境支援的任何項目。

### 加入管理員來管理 App Service 環境

若要存取 App Service 環境、建立 API、連接和其他資源，加入的使用者必須具有「擁有者」角色。

1. 選取您剛才建立的 App Service 環境。
2. 在 Essentials 中，選取 **資源群組** 屬性。 這樣會開啟包含 app service 環境的資源群組:  
![][7]
3. 選取要管理的權限的 RBAC 圖示:  
![][8]  
    新增使用者及指派角色就像使用 [角色型存取控制](https://azure.microsoft.com/documentation/articles/role-based-access-control-configure) Azure 內。

> [AZURE.NOTE] 目前，您無法提供 app service 環境的 RBAC 權限。 您可以在父資源群組層級提供 RBAC 權限。

## 摘要和後續步驟
您的公司現在已註冊 PowerApps 且具有 App Service 環境。 接下來，您可以加入可供您的應用程式使用的 API 和連接。 

- [監視 PowerApps 應用程式](powerapps-manage-monitor-usage.md)
- [開發適用於 PowerApps 的 API](powerapps-develop-api.md)
- [加入新的 API、加入連接和授與使用者存取權](powerapps-manage-api-connection-user-access.md)
- [更新現有的 API 及其屬性](powerapps-configure-apis.md)


[1]: ./media/powerapps-get-started-azure-portal/browseall.png
[2]: ./media/powerapps-get-started-azure-portal/allresources.png
[3]: ./media/powerapps-get-started-azure-portal/powerappsblade.png
[4]: ./media/powerapps-get-started-azure-portal/noaccess.png
[5]: ./media/powerapps-get-started-azure-portal/createase.png
[6]: ./media/powerapps-get-started-azure-portal/aseproperties.png
[7]: ./media/powerapps-get-started-azure-portal/aseessentials.png
[8]: ./media/powerapps-get-started-azure-portal/resourcegrouprbac.png


