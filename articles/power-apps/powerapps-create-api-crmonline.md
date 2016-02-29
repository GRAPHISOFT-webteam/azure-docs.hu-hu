<properties
    pageTitle="新增 Dynamics CRM Online API 至 PowerApps Enterprise | Microsoft Azure"
    description="在您組織的應用程式服務環境中建立或設定新的 Dynamics CRM Online API"
    services=""
    suite="powerapps"
    documentationCenter=""
    authors="schabungbam"
    manager="dwrede"
    editor=""/>

<tags
   ms.service="powerapps"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="11/25/2015"
   ms.author="sameerch"/>

# 在您組織的應用程式服務環境中建立新的 Dynamics CRM Online API

## 在 Azure 入口網站中建立 API

1. 在 [Azure 入口網站](https://portal.azure.com), 、 使用工作帳戶登入。 例如，使用登入 */users/yourusername*@*YourCompany*。 com。 當您這樣做時，將會自動登入您的公司訂用帳戶。

2. 選取 **瀏覽** 在工作列上:  
![][1]

3. 在清單中，您可以捲動以尋找 PowerApps 或輸入 *powerapps*:  
![][2]  

4. 在 **PowerApps**, ，請選取 **管理 Api**:  
![瀏覽至已註冊的 api][3]

5. 在 **管理 Api**, ，請選取 **新增** 新增新的 API:  
![新增 API][4]

6. 輸入描述性 **名稱** 為您的 API。  

7. 在 **來源**, ，請選取 **可用 Api** 選取預先建置的 Api，並選取 **Dynamics CRM Online**:
![選取 Dynamics CRM Online API][5]

8. 選取 **設定-設定必要的設定**:
![設定 Dynamics CRM Online API 設定][6]

9. 輸入 **用戶端識別碼** 和 **應用程式金鑰** Dynamics CRM Online Azure Active Directory (AAD) 應用程式。  如果您還沒有這些值，請參閱本主題中的＜註冊 AAD 應用程式以搭配 PowerApps 使用＞一節，建立您需要的金鑰與密碼值。  

    > [AZURE.IMPORTANT] 儲存 **重新導向 URL**。 您在本主題的後半部可能需要此值。

10. 選取 **確定** 完成的步驟。

完成時，您的應用程式服務環境中會新增新的 Dynamics CRM Online API。

## 註冊 AAD 應用程式以搭配 PowerApps Dynamics CRM Online API 使用

1. 開啟 [Azure 入口網站](https://portal.azure.com)。

2. 選取 **瀏覽** ，然後選取 **Active Directory**:  

    > [AZURE.NOTE] 這會在 Azure 傳統入口網站中開啟 Active Directory。  

3. 選取您的組織的租用戶名稱:  
![啟動 Azure Active Directory][7]

4. 選取 **應用程式** 索引標籤，然後選取 **新增**:  
![AAD 租用戶應用程式][8]

5. 在 **新增應用程式**:  

    a) 輸入 **名稱** 應用程式。  
    b) 保留應用程式輸入做為 **Web**。  
    c) 選取 **下一步**。

    ![加入 AAD 應用程式 - 應用程式資訊][9]

6. 在 **應用程式屬性**:  

    a) 輸入 **登入 URL** 應用程式。  因為您要為 PowerApps 與 AAD 進行驗證，將 [登入 url _https://login.windows.net_。  
    b) 輸入有效的 **應用程式識別碼 URI** 應用程式。  
    c) 選取 **確定**。  

    ![加入 AAD 應用程式 - 應用程式屬性][10]

7. 完成後，系統會將您重新導向至新的 AAD 應用程式。 選取 **設定**:  
![Contoso AAD 應用程式][11]

8. 設定 **回覆 URL** 下 _OAuth 2_ 區段 (在本主題中) 在 Azure 入口網站中加入新的 Dynamics CRM Online API 時收到的重新導向 url:  
![Contoso AAD 應用程式設定][12]

9. 選取 **儲存**。

Azure Active Directory 應用程式便建立好了。 您可以在 Azure 入口網站的 Dynamics CRM Online API 組態中使用此應用程式。

## 摘要和後續步驟
在本主題中，您已將 Dynamics CRM Online API 新增至 PowersApps Enterprise。 接下來，請授與使用者此 API 的存取權，讓使用者能夠將此 API 新增至其應用程式：

[新增連接並授與使用者存取權](powerapps-manage-api-connection-user-access.md)

<!-- References -->

[1]: ./media/powerapps-create-api-crmonline/browseall.png
[2]: ./media/powerapps-create-api-crmonline/allresources.png
[3]: ./media/powerapps-create-api-crmonline/browse-to-registered-apis.PNG
[4]: ./media/powerapps-create-api-crmonline/add-api.PNG
[5]: ./media/powerapps-create-api-crmonline/select-crmonline-api.PNG
[6]: ./media/powerapps-create-api-crmonline/configure-crmonline-settings.PNG
[7]: ./media/powerapps-create-api-crmonline/launch-aad.PNG
[8]: ./media/powerapps-create-api-crmonline/aad-tenant-applications.PNG
[9]: ./media/powerapps-create-api-crmonline/aad-tenant-applications-add-appinfo.PNG
[10]: ./media/powerapps-create-api-crmonline/aad-tenant-applications-add-app-properties.PNG
[11]: ./media/powerapps-create-api-crmonline/contoso-aad-app.PNG
[12]: ./media/powerapps-create-api-crmonline/contoso-aad-app-configure.PNG

