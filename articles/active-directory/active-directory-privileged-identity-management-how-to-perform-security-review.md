<properties
   pageTitle="Azure 特殊權限身分識別管理：如何執行安全性檢閱"
   description="了解如何將角色新增到具備 Azure 特殊權限身分識別管理擴充功能的特殊權限身分識別。"
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


# Azure 特殊權限身分識別管理：如何執行安全性檢閱

## 執行安全性檢閱

它是很容易就能一次檢閱特殊權限的存取 [已啟動安全性檢閱](active-directory-privileged-identity-management-how-to-start-security-review.md)。

## 針對檢閱者：核准或拒絕存取

### 「我」檢閱

1. 在 PIM 主功能表中，按一下 [檢閱系統管理存取權]****。 隨即會出現安全性檢閱清單。
2. 在清單中，選取您要變更存取權的 [使用者]****。 附註：存取權將會實際變更。 此程序只會針對變更角色存取權的使用者建置檢查清單。 至少選取一位使用者之後，即會啟用 [核准存取]**** 和 [拒絕存取]**** 按鈕。
3. 按一下 [  **核准存取** 或 **拒絕存取** 您已選取的使用者。 隨即會在入口網站的主功能表中出現一則通知，而檢閱清單將會消失。 關閉 [檢閱 Azure AD 角色]**** 刀鋒視窗。

### 自我檢閱

1. 使用者將會收到一封電子郵件，指出他們應該檢閱其存取權。 電子郵件將包含登入入口網站的連結。
2. 之後，使用者可以核准或拒絕他們自己的存取上的 [  **核准存取** 或 **拒絕存取** 按鈕。 他們的名稱將會從清單中消失。

## 針對檢閱管理員：管理安全性檢閱

## 完成或停止檢閱

1. 返回 PIM 儀表板。
2. 在 [安全性檢閱]**** 清單中，按一下您想要完成的安全性檢閱。 隨即會出現安全性檢閱的詳細資料刀鋒視窗。
3. 按一下 [停止檢閱]**** 以完成或停止檢閱。 這將會封存檢閱，而該刀鋒視窗將會消失。

## 匯出檢閱

您可以匯出檢閱，來與 Excel 或可使用 CSV 檔案的其他程式搭配使用。

1. 返回 PIM 儀表板。
2. 按一下儀表板的 [安全性檢閱]**** 區段。 隨即會出現 [安全性檢閱]**** 刀鋒視窗。
3. 按一下您想要匯出的安全性檢閱。 隨即會出現安全性檢閱的詳細資料刀鋒視窗。
4. 按一下 [匯出]**** 按鈕。 隨即會開始下載 CSV 檔案。

## 刪除檢閱

> AZURE.WARNING [發生刪除動作之前，您將不會收到警告，因此，請確定您「想要」**實際刪除該檢閱。]

1. 返回 PIM 儀表板。
2. 按一下儀表板的 [安全性檢閱]**** 區段。 隨即會出現 [安全性檢閱]**** 刀鋒視窗。
3. 按一下您想要刪除的安全性檢閱。 隨即會出現安全性檢閱的詳細資料刀鋒視窗。
4. 按一下 [刪除]**** 按鈕。


## 後續步驟

[AZURE.INCLUDE [active-directory-privileged-identity-management-toc](../../includes/active-directory-privileged-identity-management-toc.md)]





