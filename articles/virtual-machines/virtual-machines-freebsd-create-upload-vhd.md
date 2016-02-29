<properties
   pageTitle="建立及上傳 FreeBSD VM 映像 | Microsoft Azure"
   description="了解如何建立及上傳包含 FreeBSD 作業系統的虛擬硬碟 (VHD)，以建立 Azure 虛擬機器。"
   services="virtual-machines"
   documentationCenter=""
   authors="KylieLiang"
   manager="timlt"
   editor=""
   tags="azure-service-management"/>

<tags
   ms.service="virtual-machines"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="vm-linux"
   ms.workload="infrastructure-services"
   ms.date="05/19/2015"
   ms.author="kyliel"/>

# 建立並上傳 FreeBSD VHD 到 Azure

本文說明如何建立及上傳包含 FreeBSD 作業系統的虛擬硬碟 (VHD)，以便使用它做為您自己的映像，在 Azure 中建立虛擬機器 (VM)。

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-classic-include.md)] 資源管理員模型。


##先決條件##
本文假設您具有下列項目：

- **Azure 訂用帳戶** -如果您沒有帳戶，您可以建立帳戶，只需要幾分鐘的時間。 如果您有 MSDN 訂閱，請參閱 [MSDN 訂戶的 Azure 權益](http://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/)。 否則，請參閱 [建立免費試用帳戶](http://azure.microsoft.com/pricing/free-trial/)。  

- **Azure PowerShell 工具** -您已安裝且設定為使用您的訂閱 Microsoft Azure PowerShell 模組。 若要下載此模組，請參閱 [Azure 下載](http://azure.microsoft.com/downloads/)。 這裡有安裝和設定模組的教學課程。 您將使用 [Azure 下載](http://azure.microsoft.com/downloads/) cmdlet 來上傳 VHD。

- **安裝在.vhd 檔案中的 FreeBSD 作業系統**  -您已安裝支援的 FreeBSD 作業系統虛擬硬碟。 有多項工具可用來建立 .vhd 檔案，例如，您可以使用虛擬化解決方案 (例如 Hyper-V) 來建立 .vhd 檔案並安裝作業系統。 如需指示，請參閱 [安裝 HYPER-V 角色和設定虛擬機器](http://technet.microsoft.com/library/hh846766.aspx)。

> [AZURE.NOTE] Azure 不支援較新的 VHDX 格式。 您可以將磁碟轉換為 VHD 格式使用 HYPER-V 管理員或 cmdlet [轉換 vhd](https://technet.microsoft.com/library/hh848454.aspx)。

這項工作包含下列五個步驟。

## 步驟 1：準備要上傳的映像 ##

針對 hyper-v 上的 FreeBSD 安裝，本教學課程可 [這裡](http://blogs.msdn.com/b/kylie/archive/2014/12/25/running-freebsd-on-hyper-v.aspx)。

從安裝 FreeBSD 作業系統的虛擬機器，完成下列程序：

1. **啟用 DHCP**

        # echo 'ifconfig_hn0="SYNCDHCP"' >> /etc/rc.conf
        # service netif restart

2. **啟用 SSH**

    從光碟安裝之後，預設會啟用 SSH。 如果未啟用，或您直接使用 FreeBSD VHD，輸入：

        # echo 'sshd_enable="YES"' >> /etc/rc.conf
        # ssh-keygen -t dsa -f /etc/ssh/ssh_host_dsa_key
        # ssh-keygen -t rsa -f /etc/ssh/ssh_host_rsa_key
        # service sshd restart

3. **設定序列主控台**

        # echo 'console="comconsole vidconsole"' >> /boot/loader.conf
        # echo 'comconsole_speed="115200"' >> /boot/loader.conf

4. **安裝 sudo**

    Azure 中已停用根帳戶，您必須利用未授權的使用者 sudo 從較高權限執行命令。

        # pkg install sudo

5. Azure 代理程式的必要條件

    5.1 **安裝 python**

        # pkg install python27 py27-asn1
        # ln -s /usr/local/bin/python2.7 /usr/bin/python

    5.2 **安裝 wget**

        # pkg install wget

6. **安裝 Azure 代理程式**

    最新版的 Azure 代理程式一律位於 [github](https://github.com/Azure/WALinuxAgent/releases)。 2.0.10 版和更新版本正式支援 FreeBSD 10 和更新的版本。

        # wget https://raw.githubusercontent.com/Azure/WALinuxAgent/WALinuxAgent-2.0.10/waagent --no-check-certificate
        # mv waagent /usr/sbin
        # chmod 755 /usr/sbin/waagent
        # /usr/sbin/waagent -install

    **重要**: 安裝之後，請再次確認它正在執行。

        # service –e | grep waagent
        /etc/rc.d/waagent
        # cat /var/log/waagent.log

    現在您可以 **關閉** 您的 VM。 您也可以在關機之前執行步驟 7，但這是選擇性的。

7. 取消佈建是選擇性的。 它會嘗試清理系統，使其適合重新佈建。

    下列命令也會刪除最後佈建的使用者帳戶和相關聯的資料。

        # waagent –deprovision+user

## 步驟 2：在 Azure 中建立儲存體帳戶 ##

必須要有 Azure 中的儲存體帳戶才能上傳 .vhd 檔案，以在 Azure 中用來建立虛擬機器。 您可以使用 Azure 傳統入口網站來建立儲存體帳戶。

1. 登入 Azure 傳統入口網站。

2. 在命令列中，按一下 [ **新增**。

3. 按一下 [ **Data Services** > **儲存體** > **快速建立**。

    ![快速建立儲存體帳戶](./media/virtual-machines-freebsd-create-upload-vhd/Storage-quick-create.png)

4. 依照下列方式填入欄位：

    - 在 **URL**, ，輸入要在 URL 中使用的儲存體帳戶的子網域名稱。 此項目可以包含 3 至 24 個小寫字母與數字。 此名稱會成為 URL 內用來為訂閱的 Blob、「佇列」或「資料表」資源定址的主機名稱。

    - 選擇 **位置或同質群組** 儲存體帳戶。 同質群組可讓您將雲端服務和儲存體放在相同的資料中心。

    - 決定是否要使用 **地理區域複寫** 的儲存體帳戶。 依預設會開啟異地複寫。 此選項可讓您免費將資料複寫至次要位置，使您在主要位置發生重大錯誤時，可將儲存體容錯移轉至該位置。 次要位置會自動指派，且無法變更。 如果您因為法律規定或組織原則而需要更充分掌控您以雲端為基礎的儲存體所在的位置，您可以關閉地理複寫。 但請注意，如果您後續又開啟異地複寫，在您將現有的資料複寫至次要位置時，將會產生一次性的資料傳輸費用。 不含異地複寫的儲存服務會有相對的折扣。 管理儲存體帳戶的地理區域複寫的更多詳細資料可以在這裡找到: [建立、 管理或刪除儲存體帳戶](../storage-create-storage-account/#replication-options)。

    ![輸入儲存體帳戶詳細資料](./media/virtual-machines-freebsd-create-upload-vhd/Storage-create-account.png)


5. 按一下 [ **建立儲存體帳戶**。 帳戶現在會出現在 **儲存體**。

    ![已成功建立儲存體帳戶](./media/virtual-machines-freebsd-create-upload-vhd/Storagenewaccount.png)

6. 接下來，為您上傳的 VHD 建立容器。 按一下 [儲存體帳戶名稱，然後按一下 **容器**。

    ![儲存體帳戶詳細資訊](./media/virtual-machines-freebsd-create-upload-vhd/storageaccount_detail.png)

7. 按一下 [ **建立容器**。

    ![儲存體帳戶詳細資訊](./media/virtual-machines-freebsd-create-upload-vhd/storageaccount_container.png)

8. 型別 a **名稱** 容器和選取 **存取** 原則。

    ![容器名稱](./media/virtual-machines-freebsd-create-upload-vhd/storageaccount_containervalues.png)

    > [AZURE.NOTE] 根據預設，容器屬私人性質，只能由帳戶擁有者可以存取。 若要允許對容器中 Blob 的公開讀取存取，但不允許存取容器屬性和中繼資料，請使用 [公用 Blob] 選項。 若要允許對容器及 Blob 的完整公開讀取存取，請使用 [公開容器] 選項。

## 步驟 3：準備 Microsoft Azure 的連線 ##

您必須先在電腦與 Azure 中的訂閱之間建立安全連線，才能上傳 .vhd 檔案。 您可以使用 Microsoft Azure Active Directory 方法或憑證方法來達到此目的。

###使用 Microsoft Azure AD 方法

1. 開啟 Azure PowerShell 主控台。

2. 輸入以下命令：  
    `Add-AzureAccount`

    這個命令會開啟登入視窗，您可以用您的工作或學校帳戶登入。

    ![PowerShell Window](./media/virtual-machines-freebsd-create-upload-vhd/add_azureaccount.png)

3. Azure 會驗證並儲存認證資訊，然後關閉視窗。

###使用憑證方法

1. 開啟 Azure PowerShell 主控台。

2. 輸入：
    `Get-AzurePublishSettingsFile`。

3. 隨即開啟瀏覽器視窗，並提示您下載 .publishsettings 檔案。 它包含您 Microsoft Azure 訂閱的資訊和憑證。

    ![瀏覽器下載頁面](./media/virtual-machines-freebsd-create-upload-vhd/Browser_download_GetPublishSettingsFile.png)

3. 儲存 .publishsettings 檔案。

4. 輸入：
    `Import-AzurePublishSettingsFile <PathToFile>`

    其中 `<PathToFile>` 是 .publishsettings 檔案的完整路徑。

   如需詳細資訊，請參閱 [開始使用 Microsoft Azure Cmdlet](http://msdn.microsoft.com/library/windowsazure/jj554332.aspx)

   如需有關安裝和設定 PowerShell 的詳細資訊，請參閱 [如何安裝和設定 Microsoft Azure PowerShell](../install-configure-powershell.md)。

## 步驟 4：上傳 .vhd 檔案 ##

在上傳 .vhd 檔案時，您可以將 .vhd 檔案放在 Blob 儲存體中的任一處。 在下列命令範例中， **BlobStorageURL** 是您在步驟 2 中建立的儲存體帳戶的 URL **YourImagesFolder** 是您要儲存映像的 blob 儲存體中的容器。 **VHDName** 是出現在 Azure 傳統入口網站，來識別虛擬硬碟的標籤。 **PathToVHDFile** 是完整路徑和.vhd 檔案的名稱。


1. 從您在上一個步驟使用的 Azure PowerShell 視窗中，輸入：

        Add-AzureVhd -Destination "<BlobStorageURL>/<YourImagesFolder>/<VHDName>.vhd" -LocalFilePath <PathToVHDFile>        

## 步驟 5：使用上傳的 VHD 建立 VM ##
上傳 .vhd 之後，您可以將其新增為與訂閱相關之自訂映像清單中的映像，並使用此自訂映像建立虛擬機器。

1. 從您在上一個步驟使用的 Azure PowerShell 視窗中，輸入：

        Add-AzureVMImage -ImageName <Your Image's Name> -MediaLocation <location of the VHD> -OS <Type of the OS on the VHD>

    **重要**: 請使用 Linux 作為作業系統現在因為目前的 Azure PowerShell 版本只接受"Linux"或"Windows"做為參數的型別。

2. 完成上述步驟之後，當您選擇，會列出新的映像 **映像** Azure 傳統入口網站] 索引標籤。  

    ![新增映像](./media/virtual-machines-freebsd-create-upload-vhd/addfreebsdimage.png)

3. 從組件庫中建立虛擬機器。 這個新映像現在是可在 **我的映像**。 選取新的映像，並瀏覽提示來設定主機名稱、密碼/SSH 金鑰等項目。

    ![自訂映像](./media/virtual-machines-freebsd-create-upload-vhd/createfreebsdimageinazure.png)

4. 佈建完成後，您會看到 FreeBSD VM 在 Azure 中執行。

    ![azure 中的 freebsd 映像](./media/virtual-machines-freebsd-create-upload-vhd/freebsdimageinazure.png)

