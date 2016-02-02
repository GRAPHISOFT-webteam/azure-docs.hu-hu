<properties
    pageTitle="登入 Azure 中的 Linux VM | Microsoft Azure"
    description="了解如何登入執行 Linux 的 Azure 虛擬機器，方法是使用安全殼層 (SSH) 用戶端。"
    services="virtual-machines"
    documentationCenter=""
    authors="squillace"
    manager="timlt"
    editor=""
    tags="azure-service-management"/>

<tags
    ms.service="virtual-machines"
    ms.workload="infrastructure-services"
    ms.tgt_pltfrm="vm-linux"
    ms.devlang="na"
    ms.topic="article"
    ms.date="12/08/2015"
    ms.author="rasquill"/>



# 如何登入執行 Linux 的虛擬機器

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-classic-include.md)] [資源管理員部署模型](virtual-machines-linux-tutorial-portal-rm.md)。

您必須在要用來登入虛擬機器的電腦上安裝 SSH 用戶端。 有許多 SSH 用戶端程式可供選擇。 下面是一些可能的選項：

- 在執行 Windows 作業系統的電腦上，您可能會想要使用 PuTTY 之類的 SSH 用戶端。 如需詳細資訊，請參閱 [PuTTY 下載頁面](http://www.chiark.greenend.org.uk/~sgtatham/putty/download.html)。
- 若為執行 Linux 作業系統的虛擬機器，您可使用安全殼層 (SSH) 用戶端登入；目前未依預設安裝此用戶端的配送極為少見。 請參閱 [如何使用 SSH](virtual-machines-linux-use-ssh-key.md) 如需 Linux 有點詳細資訊。


>[AZURE.NOTE] 如需詳細的需求和疑難排解秘訣，請參閱 [連接到 Azure 虛擬機器透過 RDP 或 SSH](http://go.microsoft.com/fwlink/p/?LinkId=398294)。

此程序說明如何使用 PuTTY 程式來存取虛擬機器。

1. 尋找 **主機名稱** 和 **連接埠資訊** 從 [管理入口網站](http://manage.windowsazure.com)。 您可以從虛擬機器的儀表板中找到您需要的資訊。 請按一下虛擬機器名稱，然後在儀表板的 [Quick Glance]**** 區段中尋找 [SSH Details]****。

    ![取得 SSH 詳細資料](./media/virtual-machines-linux-how-to-log-on/sshdetails.png)

2. 開啟 PuTTY 程式。

3. 輸入您從儀表板收集的主機名稱和連接埠資訊，然後按一下 [開啟]****。

    ![開啟 PuTTY](./media/virtual-machines-linux-how-to-log-on/putty.png)

4. 使用您在建立機器時指定的帳戶登入虛擬機器。 如需有關如何使用使用者名稱和密碼建立虛擬機器的詳細資訊，請參閱 [建立執行 Linux 的虛擬機器](virtual-machines-linux-tutorial.md)。

    ![登入虛擬機器](./media/virtual-machines-linux-how-to-log-on/sshlogin.png)

>[AZURE.NOTE] VMAccess 擴充功能可以在您忘記 SSH 金鑰或密碼時幫助您重設。 如果忘記使用者名稱，您可以使用擴充功能，以 sudo 權限建立新的使用者名稱。 如需指示，請參閱 [如何重設密碼或 SSH 為 Linux 虛擬機器]。

您現在可以開始使用虛擬機器，就如同操作任何其他伺服器一樣。



[how to reset a password or ssh for linux virtual machines]: http://go.microsoft.com/fwlink/p/?LinkId=512138 

