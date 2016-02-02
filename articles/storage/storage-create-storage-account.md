<properties
    pageTitle="如何建立、管理或刪除儲存體帳戶 | Microsoft Azure"
    description="在 Azure 入口網站中建立新的儲存體帳戶、管理存取金鑰，或刪除儲存體帳戶。"
    services="storage"
    documentationCenter=""
    authors="robinsh"
    manager="carmonm"
    editor=""/>

<tags
    ms.service="storage"
    ms.workload="storage"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="get-started-article"
    ms.date="12/04/2015" 
    ms.author="robinsh"/>



# 關於 Azure 儲存體帳戶

[AZURE.INCLUDE [storage-selector-portal-create-storage-account](../../includes/storage-selector-portal-create-storage-account.md)]

## 概觀

Azure 儲存體帳戶可讓您存取 Azure 儲存體中的 Azure Blob、佇列、資料表和檔案服務。 您的儲存體帳戶提供您 Azure 儲存體資料物件的唯一命名空間。 根據預設，您帳戶中的資料只有帳戶擁有者 (也就是您) 可以使用。

儲存體帳戶分為兩種類型：

- 標準儲存體帳戶包含 Blob、資料表、佇列和檔案儲存體。
- 進階儲存體帳戶目前僅支援 Azure 虛擬機器磁碟。 請參閱 [高階儲存體: Azure 虛擬機器工作負載的高效能儲存體](storage-premium-storage-preview-portal.md) 的高階儲存體的深入概觀。

## 儲存體帳戶計費

我們會根據您的儲存體帳戶對 Azure 儲存體使用量計費。 儲存體成本以四項因素為基礎：儲存體容量、複寫配置、儲存體交易和資料輸出。

- 儲存體容量是指您用於儲存資料的儲存體帳戶配額。 若只是儲存資料，則成本是由您所儲存的資料量和複寫資料的方式來決定。
- 複寫會決定您的資料同時維護了多少複本，以及在哪些位置。
- 交易是指對 Azure 儲存體進行的所有讀取和寫入作業。
- 出口流量是指傳出 Azure 地區的資料。 當您儲存體帳戶中的資料受不同地區中執行的應用程式存取時，不論該應用程式是雲端服務還是其他某類應用程式，您都要負擔出口流量的費用。 (若為 Azure 服務，您可以採取步驟，將資料和服務群組在相同的資料中心，以減少或消除出口流量費用。)

[Azure 定價](http://azure.microsoft.com/pricing/details/#storage) 頁面會提供儲存體容量、 複寫和交易的詳細定價資訊。  [資料傳輸定價詳細資料](http://azure.microsoft.com/pricing/details/data-transfers/) 頁面會提供資料輸出的詳細定價資訊。

如需有關儲存體帳戶容量和效能目標的詳細資訊，請參閱 [Azure 儲存體延展性和效能目標](http://msdn.microsoft.com/library/azure/dn249410.aspx)。
> [AZURE.NOTE] 當您建立 Azure 虛擬機器時，如果您在部署位置中沒有儲存體帳戶，則會在該位置自動建立儲存體帳戶。 因此，您無須依照下方的步驟為虛擬機器磁碟建立儲存體帳戶。 儲存體帳戶名稱將以虛擬機器名稱為基礎。 請參閱 [Azure 虛擬機器文件](http://azure.microsoft.com/documentation/services/virtual-machines/) 如需詳細資訊。

## 儲存體帳戶端點

每個儲存在 Azure 儲存體中的物件都有一個唯一 URL 位址。 儲存體帳戶名稱會構成該位址的子網域。 子網域和每個服務的特定網域名稱的組合，會構成儲存體帳戶的*端點*。

例如，如果您的儲存體帳戶名為 *mystorageaccount*，則儲存體帳戶的預設端點將是：

- Blob 服務: http://*mystorageaccount*。 windows.net

- 表格服務: http://*mystorageaccount*。.table.core.windows.net

- 佇列服務: http://*mystorageaccount*。 queue.core.windows.net

- 檔案服務: http://*mystorageaccount*。 windows.net

用以存取儲存體帳戶中某物件的 URL，可藉由在端點後附加該物件在儲存體帳戶中的位置來建置。 例如，blob 位址格式可能如下: http://*mystorageaccount*.blob.core.windows.net/*mycontainer*/*myblob*。

您也可以設定與儲存體帳戶搭配使用的自訂網域名稱。 請參閱 [Azure 儲存體帳戶中設定自訂網域名稱的 blob 資料](storage-custom-domain-name.md) 如需詳細資訊。

## 建立儲存體帳戶

1. 登入 [Azure 入口網站](portal.azure.com)。

2. 在 [中樞] 功能表上，選取 [新增]**** -> [資料+儲存體]**** -> [儲存體帳戶]****。

3. 選取部署模型：[資源管理員]**** 或 [傳統]****。 [資源管理員]**** 是建議的部署模型。 如需詳細資訊，請參閱 [瞭解資源管理員部署和傳統部署](../resource-manager-deployment-model.md)。

4. 輸入儲存體帳戶的名稱。
    > [AZURE.NOTE] 儲存體帳戶名稱必須介於 3 到 24 個字元的長度，而且只能包含數字和小寫字母。
    >  
    > 儲存體帳戶名稱必須在 Azure 中是獨一無二的。 Azure 入口網站會指出您選取的儲存體帳戶名稱是否已使用。

    請參閱 [儲存體帳戶端點](#storage-account-endpoints) 下面詳細瞭解儲存體帳戶名稱將會用來解析 Azure 儲存體中的物件。

5. 指定要建立的儲存體帳戶類型。 儲存體帳戶類型會決定複寫該儲存體帳戶的方式，以及該帳戶屬於標準儲存體帳戶還是進階儲存體帳戶。

    預設儲存體帳戶類型是 **Standard-RAGRS**，它是具有讀取存取異地備援複寫的標準儲存體帳戶，。 這種類型的儲存體帳戶會從主要區域複寫到數百哩之外的次要地區，並為次要位置提供讀取權限。

    如需 Azure 儲存體複寫選項的詳細資訊，請參閱 [Azure 儲存體複寫](storage-redundancy.md)。 如需標準和進階儲存體帳戶的詳細資訊，請參閱 [儲存體簡介](storage-introduction.md) 和 [高階儲存體: Azure 虛擬機器工作負載的高效能儲存體](storage-premium-storage-preview-portal.md)

6. 指出您是否希望針對儲存體帳戶啟用診斷。 診斷包括儲存體分析記錄和度量。

7. 如果您有一個以上的 Azure 訂用帳戶，則會顯示 [訂用帳戶]**** 欄位。 選取您要在其中建立新儲存體帳戶的訂用帳戶。

8. 指定新的資源群組，或選取現有的資源群組。 如需資源群組的詳細資訊，請參閱 [使用 Azure 入口網站來管理 Azure 資源](../resource-group-portal.md)。

9. 選取儲存體帳戶的地理位置。

10. 按一下 [建立]**** 建立儲存體帳戶。

## 管理儲存體存取金鑰

當您建立儲存體帳戶時，Azure 會產生兩個 512 位元的儲存體存取金鑰，作為存取儲存體帳戶時的驗證憑藉。 透過提供這兩個儲存體存取金鑰，Azure 讓您可重新產生金鑰，同時又不需中斷儲存體服務或對該服務的存取。
> [AZURE.NOTE] 建議您避免將儲存體存取金鑰透露給其他任何人。 若要允許存取儲存體資源但不要公開您的存取金鑰，您可以使用「共用存取簽章」**。 共用存取簽章可在您定義的間隔期間內，使用您所指定的權限，來存取帳戶中的資源。 請參閱 [共用的存取簽章教學課程](storage-dotnet-shared-access-signature-part-1.md) 如需詳細資訊。

### 檢視並複製儲存體存取金鑰

在 [Azure 入口網站](portal.azure.com), ，瀏覽至儲存體帳戶，然後按一下 **金鑰** 圖示以檢視、 複製和重新產生您的帳戶存取金鑰。 [存取金鑰]**** 刀鋒視窗也包含使用您主要與次要金鑰的預先設定連接字串，讓您可以複製以在應用程式中使用。

### 重新產生儲存體存取金鑰

建議您定期變更儲存體帳戶的存取金鑰，保護儲存體連線的安全。 指派了兩個存取金鑰，因此您可以在重新產生一個存取金鑰的同時，使用另一個存取金鑰維持儲存體帳戶連線。
> [AZURE.WARNING] 重新產生存取金鑰會影響虛擬機器、媒體服務，以及任何相依於儲存體帳戶的應用程式。 所有使用存取金鑰來存取儲存體帳戶的用戶端，都必須更新為使用新的金鑰。

**虛擬機器** - 如果您的儲存體帳戶中有任何虛擬機器在執行中，您必須在重新產生存取金鑰之後，重新部署所有的虛擬機器。 若要避免重新部署，請在重新產生存取金鑰之前關閉虛擬機器。

**媒體服務** - 如果您有媒體服務相依於儲存體帳戶，您必須在重新產生金鑰之後，將存取金鑰與媒體服務重新同步。

**應用程式** - 如果您有 Web 應用程式或雲端服務使用儲存體帳戶，除非您變換金鑰，否則會在重新產生金鑰後失去連線。

以下是替換儲存體存取金鑰的程序：

1. 更新應用程式程式碼中的連接字串，以參考儲存體帳戶的次要存取金鑰。

2. 重新產生儲存體帳戶的主要存取金鑰。 按一下 [存取金鑰]**** 刀鋒視窗上的 [重新產生 Key1]****，然後按一下 [是]**** 確認您要重新產生新的金鑰。

3. 更新程式碼中的連接字串，以參考新的主要存取金鑰。

4. 以同樣的方式重新產生次要存取金鑰。

## 刪除儲存體帳戶

若要移除您不再使用的儲存體帳戶，瀏覽至儲存體帳戶中 [Azure 入口網站](portal.azure.com), ，然後按一下 **刪除**。 刪除儲存體帳戶會刪除整個帳戶，包括帳戶中的所有資料。
> [AZURE.WARNING] 您無法還原已刪除的儲存體帳戶，也無法擷取刪除之前所包含的任何內容。 請務必先備份您想要儲存的任何資料，再刪除帳戶。 這也適用於帳戶中的任何資源 - 一旦刪除 Blob、資料表、佇列或檔案，就是永久刪除。

## 後續步驟

- 若要深入了解 Azure 儲存體，請參閱 Azure 儲存體文件 [Azure.com](http://azure.microsoft.com/documentation/services/storage/)。
- 請瀏覽 [Azure 儲存體團隊部落格](http://blogs.msdn.com/b/windowsazurestorage/)。
- [使用 AzCopy 命令列公用程式傳輸資料](storage-use-azcopy)




