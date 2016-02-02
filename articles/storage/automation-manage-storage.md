<properties
    pageTitle="使用 Azure 自動化管理 Azure 儲存體"
    description="了解如何使用 Azure 自動化服務大規模地管理 Azure 儲存體。"
    services="storage, automation"
    documentationCenter=""
    authors="jodoglevy"
    manager="eamono"
    editor=""/>

<tags
    ms.service="storage"
    ms.workload="storage"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="11/03/2015"
    ms.author="jolevy"/>




# 使用 Azure 自動化管理 Azure 儲存體

本指南將為您介紹 Azure 自動化服務，以及如何使用它來簡化 Azure 儲存體 Blob、資料表及佇列的管理。


## 什麼是 Azure 自動化？

[Azure 自動化](http://azure.microsoft.com/services/automation/) 是一項 Azure 服務，可簡化雲端管理透過程序自動化。 透過 Azure 自動化，長時間執行、手動、容易發生錯誤和經常重複的工作都可以自動化，以提高可靠性、效率，並為您的組織縮短創造價值時程。

Azure 自動化提供非常可靠且高度可用的工作流程執行引擎，可隨著組織的成長根據您的需求進行調整。 在 Azure 自動化中，程序可透過手動方式、經由協力廠商系統，或依照排程的間隔啟動，讓工作精準地在需要時執行。

將您的雲端管理工作交由「Azure 自動化」自動執行，以降低營運負擔並釋出 IT/開發維運人力，使其專注於能夠為企業創造價值的工作上。


## Azure 自動化為何有助於管理 Azure 儲存體？

可透過 PowerShell cmdlet，可在 Azure 自動化中管理 azure 儲存體 [Azure PowerShell 工具](https://msdn.microsoft.com/library/azure/jj156055.aspx)。 Azure 自動化的這些 儲存體 PowerShell Cmdlet 都是內建的，以便您在服務內執行所有 Blob、資料表及佇列管理工作。 您也可以將 Azure 自動化中的這些 Cmdlet 與其他 Azure 服務的 Cmdlet 搭配，以透過 Azure 服務和協力廠商系統自動執行複雜的工作。

[Azure 自動化 runbook 資源庫](http://azure.microsoft.com/blog/2014/10/07/introducing-the-azure-automation-runbook-gallery/) 含有多種產品團隊和社群 runbook，以便開始使用自動化管理 Azure 儲存體、 其他 Azure 服務，以及第 3 個廠商系統。 資源庫 Runbook 包括：

 * [移除超過 X 天的 Azure 儲存體 blob](https://gallery.technet.microsoft.com/scriptcenter/Remove-Storage-Blobs-that-aae4b761)
 * [從 Azure 儲存體的 blob 下載到 Azure 自動化](https://gallery.technet.microsoft.com/scriptcenter/a-Blob-from-Azure-Storage-6bc13745)
 * [在 Azure 雲端服務中建立 Azure VM 資料磁碟的複本](https://gallery.technet.microsoft.com/scriptcenter/Make-copies-of-Azure-VM-065a6394)


## 後續步驟

了解 Azure 自動化的基本概念以及如何用它來管理 Azure 儲存體 Blob、資料表及佇列之後，請參考下列連結，以深入了解 Azure 自動化。

請參閱 Azure 自動化 [快速入門教學課程](../automation-create-runbook-from-samples.md)






