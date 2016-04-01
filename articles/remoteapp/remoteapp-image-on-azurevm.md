<properties 
    pageTitle="建立以 Azure VM 為基礎的 Azure RemoteApp 映像 | Microsoft Azure"
    description="了解如何開始使用 Azure 虛擬機器來建立 Azure RemoteApp 映像。" 
    services="remoteapp" 
    documentationCenter="" 
    authors="lizap" 
    manager="mbaldwin" />

<tags 
    ms.service="remoteapp" 
    ms.workload="compute" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="09/02/2015" 
    ms.author="elizapo" />



# 建立以 Azure 虛擬機器為基礎的 Azure RemoteApp 映像

您可以從 Azure 虛擬機器建立 Azure RemoteApp 映像 (其中保存您在集合中共用的應用程式)。 您也可以使用我們已新增至 Azure VM 映像資源庫的虛擬機器映像，其符合所有 Azure RemoteApp 映像需求。如果您想要的話，您可以將該 VM 映像做為您自己 VM 的起點使用。 只需在映像庫中尋找「Windows Server 遠端桌面工作階段主機」。

根據 Azure VM 建立您的專屬映像共有兩個步驟：建立映像，然後將它從 Azure VM 映像庫上傳至 Azure RemoteApp。

## 建立以 Azure VM 為基礎的自訂映像

使用這些步驟來建立以 Azure VM 為基礎的映像。

1. 建立 Azure 虛擬機器。 您可以使用「Windows Server 遠端桌面工作階段主機」或來自 Azure 虛擬機器映像庫的「Windows Server 遠端桌面工作階段主機與 Microsoft Office 365 ProPlus」映像。 此映像符合所有的 Azure RemoteApp 範本映像需求。 

    如需詳細資訊，請參閱 [建立執行 Windows 的 VM](virtual-machines-windows-tutorial.md)。

2. 連接至 VM，並安裝和設定您想要透過 RemoteApp 共用的應用程式。 請務必執行您應用程式所需的任何其他 Windows 設定。 

    如需詳細資訊，請參閱 [如何登入虛擬機器執行 Windows Server 的](virtual-machines-log-on-windows-server.md)。 

3. 如果您打算使用 Windows Server 遠端桌面工作階段主機映像之一，它包含了可確保您的 VM 符合 RemoteApp 先決條件的驗證指令碼。 若要執行指令碼，連按兩下 **ValidateRemoteAppImage** 在桌面上。 在繼續進行下一個步驟之前，請確定已修正指令碼所報告的所有錯誤。

4. SYSPREP 一般化和擷取映像。 請參閱 [如何擷取 Windows 虛擬機器作為範本使用](../virtual-machines-capture-image-windows-server.md) 如需相關指示。

 

## 將映像匯入 Azure RemoteApp 映像庫

使用下列步驟將新的映像匯入 Azure RemoteApp：

1. 在 **範本映像** ] 索引標籤 ︰
    - 如果您沒有現有的映像，請按一下 **上傳或匯入範本映像**。 
    - 如果您已經有一個以上的映像，按一下 [ **+** 新增新的映像。

2. 選取 **從您的虛擬機器匯入映像** 程式庫，然後再按一下 **下一步**。

3. 在下一個頁面上，從清單中選取自訂映像，並確認您已遵循建立映像時的所列步驟進行。 按一下 [ **下一步**。
4. 輸入新 RemoteApp 映像的名稱，並挑選一個位置，然後按一下核取記號以開始匯入程序。

> [AZURE.NOTE] 您可以從 Azure 虛擬機器支援的 Azure RemoteApp 支援的任何 Azure 位置的任何 Azure 位置，以匯入映像。 視位置而定，匯入可能需要多達 25 分鐘的時間。

現在您已經準備好要建立新的集合，方法是 [定域機組](remoteapp-create-cloud-deployment.md) 集合或 [混合式](remoteapp-create-hybrid-deployment.md), ，取決於您的需求。
 

