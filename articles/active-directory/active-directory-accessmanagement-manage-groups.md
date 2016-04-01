<properties

    pageTitle="Managing security groups in Azure Active Directory | Microsoft Azure"
    description="How to create and manage security groups to manage Azure resource access using Azure Active Directory."
    services="active-directory"
    documentationCenter=""
    authors="curtand"
    manager="stevenpo"
    editor=""/>

<tags
    ms.service="active-directory"
    ms.workload="identity"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="11/17/2015"
    ms.author="curtand"/>


#在 Azure Active Directory 中管理安全性群組

Azure Active Directory (Azure AD) 的其中一項主要功能是管理資源的存取權。 這些資源可以是目錄的一部分，例如透過目錄中的角色或目錄外部的資源 (例如 SaaS 應用程式、Azure 服務以及 SharePoint 網站或內部部署資源) 管理物件的權限。 資源擁有者可以指派群組存取資源，透過這種方式，授與該群組成員存取資源。 群組的擁有者就可以管理群組的成員資格。 實際上，資源擁有者是委派權限給群組的擁有者，以指派使用者存取其資源。


##如何建立及管理安全性群組嗎

**在 Azure 入口網站中建立群組**

1. 在 Azure 入口網站中，按一下 [ **Active Directory**, ，然後按一下您的組織目錄的名稱。
2. 按一下 [ **群組** ] 索引標籤。
3. 在 [群組] 頁面中，按一下 [ **加入群組**。
4. 在 **加入群組** ] 視窗中，指定名稱和群組的描述。
5. 您可以使用 Office 365 帳戶入口網站、Windows Intune 帳戶入口網站或 Azure 入口網站完成這項工作 (視貴組織所訂閱的服務而定)。 如需有關使用入口網站來管理您的 Azure Active Directory 的詳細資訊，請參閱 [管理 Azure AD 目錄](active-directory-administer)。

## 如何指派或移除安全性群組中的使用者

**將成員加入 Azure 入口網站的群組**

1. 在 Azure 入口網站中，按一下 [ **Active Directory**, ，然後按一下您的組織目錄的名稱。
2. 按一下 [ **群組** ] 索引標籤。
3. 在 **群組** 頁面上，按一下您想要將成員加入群組的名稱。 根據預設，這會顯示 **成員** 所選取群組] 索引標籤。
4. 在該群組的頁面上，按一下 [ **新增成員**。
5. 在 **新增成員** 頁面上，按一下使用者或群組，您想要加入此群組的成員身分，並確定此名稱會加入至 [選取] 窗格中的名稱。


**移除 Azure 入口網站的群組成員**

1. 在 Azure 入口網站中，按一下 [ **Active Directory**, ，然後按一下您的組織目錄的名稱。
2. 按一下 [ **群組** ] 索引標籤。
3. 在 [群組] 頁面上，按一下您想要從其中移除成員的群組的名稱。
4. 在該群組的頁面上，按一下 [ **成員** ] 索引標籤。
5. 在該群組的頁面上，按一下您要從此群組移除，然後按一下成員名稱 **移除**。
6. 確認您想要從群組移除此成員，即可 **是** 以回答動作確認問題。


## 如何使用規則動態管理安全性群組的成員

**若要啟用特定群組的動態成員資格，請執行下列步驟：**

1. 在 Azure 網站中，在 **群組** 索引標籤上，選取您想要編輯的群組，然後在此群組 **設定** 索引標籤上，設定 **啟用動態成員資格** 切換至 **是**。
2. 您現在可以為將控制此群組的動態成員資格如何運作的群組，設定一個簡單的單一規則。 請確定 **將使用者加入其中** 已核取選項按鈕，然後再從下拉式功能表 （例如，department、 jobTitle 等），選取一個使用者屬性
3. 接著，選取一個條件 (不等於、等於、開頭不是、開頭為、不包含、包含、不符合、符合)，最後指定所選使用者屬性的值。

例如，如果某個群組指派給 SaaS 應用程式 (如需詳細資訊，請參閱「在 Azure AD 中將群組存取權指派給 SaaS 應用程式」)，而且您透過設定一個規則，藉以新增設定為 Equals(-eq)Sales Rep 的 jobTitle 的使用者，以啟用此群組的動態成員資格，則在您的 Azure AD 目錄中，其職稱設定為 Sales Rep 的所有使用者將可以存取此 SaaS 應用程式。

## 其他資訊

這些文章提供有關 Azure Active Directory 的其他資訊。

* [使用 Azure Active Directory 群組來管理資源的存取權](active-directory-manage-groups.md)

* [什麼是 Azure Active Directory？](active-directory-whatis.md)

* [整合內部部署身分識別與 Azure Active Directory](active-directory-aadconnect.md)


