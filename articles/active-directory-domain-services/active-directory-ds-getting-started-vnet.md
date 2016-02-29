<properties
    pageTitle="Azure Active Directory 網域服務預覽：開始使用 | Microsoft Azure"
    description="開始使用 Azure Active Directory 網域服務"
    services="active-directory-ds"
    documentationCenter=""
    authors="mahesh-unnikrishnan"
    manager="udayh"
    editor="curtand"/>

<tags
    ms.service="active-directory-ds"
    ms.workload="identity"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="11/09/2015"
    ms.author="maheshu"/>

# Azure AD 網域服務 *(預覽)* -快速入門

## 選取 Azure 虛擬網路的指導方針
選取要與 Azure AD 網域服務搭配使用的虛擬網路時，請記住下列指導方針：

- 確定您選取的虛擬網路位於 Azure AD 網域服務所支援的區域中。 目前支援的 Azure 區域的清單位於 [區域頁面](https://azure.microsoft.com/regions/#services)。
- 如果您打算使用現有的虛擬網路，請確定它是區域虛擬網路。 使用舊版同質群組機制的虛擬網路不能與 Azure AD 網域服務搭配使用。 您必須 [將繼承的虛擬網路移轉至區域虛擬網路](../virtual-networks-migrate-to-regional-vnet.md)。
- 選取目前裝載/將裝載需要存取 Azure AD 網域服務之虛擬機器的虛擬網路。 您稍後便無法將網域服務移動到其他虛擬網路。
- 使用 Azure 資源管理員建立的虛擬網路不支援 Azure AD 網域服務。


## 步驟 2：建立 Azure 虛擬網路
下一個組態步驟是建立您想要啟用 Azure AD 網域服務的 Azure 虛擬網路。 如果您現在已經有慣用的虛擬網路，就可以略過此步驟。

> [AZURE.NOTE] 請確定您建立或選擇使用 Azure AD 網域服務與 Azure 虛擬網路屬於 Azure AD 網域服務支援的 Azure 區域。 如需可以使用 Azure AD 網域服務所在的 Azure 區域的清單，請參閱 [區域](active-directory-ds-regions.md) 頁面。

您必須記下虛擬網路的名稱，如此一來，當您在後續設定步驟中啟用 Azure AD 網域服務時，就能選取正確的虛擬網路。

執行下列組態步驟，以建立您想要啟用 Azure AD 網域服務的 Azure 虛擬網路。

1. 瀏覽至 **Azure 管理入口網站** ([https://manage.windowsazure.com](https://manage.windowsazure.com))。
2. 選取 **網路** 在左窗格中的節點。
3. 按一下 [ **新增** 在頁面底部的工作窗格。

    ![虛擬網路節點](./media/active-directory-domain-services-getting-started/virtual-networks.png)

4. 在 **網路服務** 節點中，選取 **虛擬網路**。
5. 按一下 [ **快速建立** 才能建立虛擬網路。

    ![虛擬網路 - 快速建立](./media/active-directory-domain-services-getting-started/virtual-network-quickcreate.png)

6. 指定 **名稱** 虛擬網路。 您也可以選擇設定 **位址空間** 或 **最大 VM 計數** 對此網路。 您現在可以讓 DNS 伺服器設定保留為 [無]。 此設定將在您啟用 Azure AD 網域服務之後更新。
7. 請確定您選取支援的 Azure 地區中 **位置** 下拉式清單。 如需可以使用 Azure AD 網域服務所在的 Azure 區域的清單，請參閱 [區域](active-directory-ds-regions.md) 頁面。 這個步驟非常重要。 如果您在 Azure AD 網域服務不支援的 Azure 區域中選取虛擬網路，將無法啟用該虛擬網路中的服務。
8. 按一下 [ **建立虛擬網路** ] 按鈕以建立虛擬網路。

    ![建立適用於 Azure AD 網域服務的虛擬網路。](./media/active-directory-domain-services-getting-started/create-vnet.png)

---
[**下一個步驟-啟用 Azure AD 網域服務。**](active-directory-ds-getting-started-enableaadds.md)

