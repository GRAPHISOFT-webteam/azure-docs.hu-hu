---
title: "Egy felhasználó vagy csoport-hozzárendelés eltávolítása egy vállalati alkalmazás Azure Active Directoryban |} Microsoft Docs"
description: "A hozzáférés hozzárendelése egy felhasználóhoz vagy csoporthoz eltávolítása a vállalati alkalmazások az Azure Active Directoryban"
services: active-directory
documentationcenter: 
author: MarkusVi
manager: mtillman
editor: 
ms.assetid: 7b2d365b-ae92-477f-9702-353cc6acc5ea
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/14/2018
ms.author: markvi
ms.reviewer: asteen
ms.custom: it-pro
ms.openlocfilehash: 084ffcbe473290a8734b1c8b8847b517dac4f6b6
ms.sourcegitcommit: d87b039e13a5f8df1ee9d82a727e6bc04715c341
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/21/2018
---
# <a name="remove-a-user-or-group-assignment-from-an-enterprise-app-in-azure-active-directory"></a>Egy felhasználó vagy csoport-hozzárendelés eltávolítása egy vállalati alkalmazás Azure Active Directoryban
Nem lett hozzárendelve hozzáférés a vállalati alkalmazások az Azure Active Directory (Azure AD) egyik felhasználó vagy csoport eltávolítása. A vállalati alkalmazások kezelésére a megfelelő engedélyekkel kell rendelkeznie, és a címtár globális rendszergazdának kell lennie.

> [!NOTE]
> A Microsoft Applications (például az Office 365-alkalmazásokhoz) a PowerShell használatával távolítsa el a felhasználók vállalati alkalmazások számára.

## <a name="how-do-i-remove-a-user-or-group-assignment-to-an-enterprise-app-in-the-azure-portal"></a>Hogyan távolíthatom egy felhasználó vagy csoport-hozzárendelés vállalati alkalmazások számára az Azure-portálon?
1. Jelentkezzen be a [Azure-portálon](https://portal.azure.com) egy olyan fiókkal, amely a címtár globális rendszergazdája.
2. Válassza ki **további szolgáltatások**, adja meg **Azure Active Directory** a szövegmezőbe, majd válassza ki azt a **Enter**.
3. Az a **Azure Active Directory - *directoryname***  (Ez azt jelenti, hogy az Azure AD lapon a kezelt könyvtár) oldalon válassza ki, **vállalati alkalmazások**.

    ![Vállalati alkalmazások megnyitásakor](./media/active-directory-coreapps-remove-assignment-user-azure-portal/open-enterprise-apps.png)
4. Az a **vállalati alkalmazások** lapon jelölje be **összes alkalmazás**. Kezelheti az alkalmazások listáját láthatja.
5. Az a **vállalati alkalmazások – összes alkalmazás** lapján válasszon ki egy alkalmazást.
6. Az a ***appname*** oldalon (Ez azt jelenti, hogy a lap a címben a kijelölt alkalmazás nevét), válassza ki, **felhasználók és csoportok**.

    ![Felhasználók vagy csoportok kiválasztása](./media/active-directory-coreapps-remove-assignment-user-azure-portal/remove-app-users.png)
7. Az a ***appname*** **-felhasználó & csoport-hozzárendelés** lapon válasszon egyet a további felhasználókat vagy csoportokat, és válassza ki a **eltávolítása** parancsot. Erősítse meg a döntést, a parancssorba.

    ![A Remove parancsot kiválasztása](./media/active-directory-coreapps-remove-assignment-user-azure-portal/remove-users.png)

## <a name="how-do-i-remove-a-user-or-group-assignment-to-an-enterprise-app-using-powershell"></a>Hogyan el egy felhasználó vagy csoport-hozzárendelés vállalati alkalmazásokhoz PowerShell használatával?
1. Nyisson meg egy rendszergazda jogú Windows PowerShell-parancssort.

    >[!NOTE] 
    > Telepítenie kell a AzureAD modul (a parancs `Install-Module -Name AzureAD`). Ha a NuGet-modulok vagy az új Azure Active Directory V2 PowerShell modul telepítésére kéri, írja be az I, és nyomja le az ENTER.

2. Futtatás `Connect-AzureAD` , és jelentkezzen be egy globális rendszergazdai felhasználói fiókkal.
3. Használja a következő parancsfájl egy felhasználó és a szerepkör hozzárendelése egy alkalmazás:

    ```powershell
    # Store the proper parameters
    $user = get-azureaduser -ObjectId <objectId>
    $spo = Get-AzureADServicePrincipal -ObjectId <objectId>

    #Get the ID of role assignment 
    $assignments = Get-AzureADServiceAppRoleAssignment -ObjectId $spo.ObjectId | Where {$_.PrincipalDisplayName -eq $user.DisplayName}

    #if you run the following, it will show you what is assigned what
    $assignments | Select *

    #To remove the App role assignment run the following command.
    Remove-AzureADServiceAppRoleAssignment -ObjectId $spo.ObjectId -AppRoleAssignmentId $assignments[assignment #].ObjectId
    ``` 
## <a name="next-steps"></a>További lépések

- [Összes saját csoportok](active-directory-groups-view-azure-portal.md)
- [Egy felhasználó vagy csoport hozzárendelése egy vállalati alkalmazás](active-directory-coreapps-assign-user-azure-portal.md)
- [Tiltsa le a felhasználói bejelentkezéseket a vállalati alkalmazás](active-directory-coreapps-disable-app-azure-portal.md)
- [Módosítja a nevét, vagy egy vállalati alkalmazás embléma](active-directory-coreapps-change-app-logo-user-azure-portal.md)
