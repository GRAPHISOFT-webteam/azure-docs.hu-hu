<properties
   pageTitle="Azure 特殊權限身分識別管理：角色"
   description="了解要針對具備 Azure 特殊權限身分識別管理擴充功能的特殊權限身分識別使用哪些角色。"
   services="active-directory"
   documentationCenter=""
   authors="IHenkel"
   manager="stevenpo"
   editor=""/>

<tags
   ms.service="na"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="identity"
   ms.date="09/21/2015"
   ms.author="inhenk"/>

# Azure 特殊權限身分識別管理：角色

<!-- **PLACEHOLDER: Need description of how this works. Azure PIM uses roles from MSODS objects.**-->

## 來自 Azure Active Directory、Office 365 和其他來源的角色

Azure PIM 會使用下列角色做為預設的管理員角色。

- 全域管理員
- 計費管理員
- 服務管理員
- 使用者管理員
- 密碼管理員

如需有關從 Office 365 角色的詳細資訊，請 Exchange Online、 Sharepoint Online 和商務用 Skype 按一下這裡。[在 Office 365 中的系統管理員角色指派](https://support.office.com/en-us/article/Assigning-admin-roles-in-Office-365-eac4d046-1afd-4f1a-85fc-8219c79e1504?ui=en-US&rs=en-US&ad=US)

<!--**PLACEHOLDER: The above article may not be the one we want since PIM gets roles from places other that Office 365**-->


<!-- ## The PIM Security Administrator Role **PLACEHOLDER: Need description of the Security Administrator role.**-->

## 使用者角色和登入
> [AZURE.NOTE]為了讓使用者能夠登入 Azure PIM，它們必須在 azure 擁有的授權。

## 將授權指派給 Azure AD 中的使用者

> [AZURE.NOTE] 授權選項才會出現授權實際結束此訂閱。

1. 全域管理員帳戶或共同管理員帳戶，登入 [http://manage.windowsazure.com] (http://manage.windowsazure.com)。
2. 按一下 [ **所有項目** 在主功能表中。
3. 選取您想要使用的目錄，且該目錄會含有與它相關聯的授權。
4. 按一下 [ **授權**。 隨即會出現可用的授權清單。
5. 按一下包含您要散發之授權的授權方案。
6. 按一下 [ **指派使用者**。
7. 選取您想要指派授權的使用者。
8. 按一下 [ **指派** ] 按鈕。  使用者現在可以登入 Azure。

<!--Every topic should have next steps and links to the next logical set of content to keep the customer engaged-->
## 後續步驟
[AZURE.INCLUDE [active-directory-privileged-identity-management-toc](../../includes/active-directory-privileged-identity-management-toc.md)]


