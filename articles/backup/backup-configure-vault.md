<properties
    pageTitle="設定 Azure 備份服務以準備備份 Windows Server | Microsoft Azure"
    description="本教學課程讓您了解如何使用 Microsoft Azure 雲端產品中的備份服務，將 Windows Server 備份至雲端。"
    services="backup"
    documentationCenter=""
    authors="Jim-Parker"
    manager="jwhit"
    editor=""/>

<tags
    ms.service="backup 」
    ms.workload= 「 儲存體的備份復原的 」
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="英雄文章 」
    ms.date="11/26/2015 」
    ms.author="jimpark 」。「 aashishr 」 / >

# 準備環境以備份 Windows 電腦

本文將引導您進行使用 Azure Backup 來備份 Windows 伺服器的準備步驟。 若要將 Windows Server 或 Windows 用戶端備份至 Azure，您需要 Azure 帳戶。 如果您沒有帳戶，只需要幾分鐘的時間就可以建立免費試用帳戶。 如需詳細資料，請參閱 [Azure 免費試用](https://azure.microsoft.com/pricing/free-trial/)。
>[AZURE.NOTE] 您之前需要建立或取得 X.509 v3 憑證，才能註冊備份伺服器。 這些憑證仍受到支援，但現在為了簡化向伺服器註冊 Azure 保存庫的作業，您可以直接從 [快速啟動] 頁面產生保存庫認證。

## 開始之前
若要將檔案和資料從 Windows Server 備份至 Azure，您必須先：

- **建立備份保存庫** — 建立保存庫中的 [Azure Backup 管理入口網站](http://manage.windowsazure.com)。
- **下載保存庫認證** — 從 Azure 備份保存庫的儀表板] 頁面上，下載保存庫認證將用來登錄到備份保存庫的 Windows 電腦。
- **安裝 Azure 備份代理程式，並註冊伺服器** — 從 [儀表板] 頁面中，按一下連結以下載 [Azure 備份代理程式](http://aka.ms/azurebackup_agent)。 安裝代理程式，然後使用保存庫認證來將伺服器註冊到備份保存庫。

[AZURE.INCLUDE [backup-create-vault-wgif](../../includes/backup-create-vault-wgif.md)]

[AZURE.INCLUDE [backup-download-credentials](../../includes/backup-download-credentials.md)]

[AZURE.INCLUDE [backup-install-agent](../../includes/backup-install-agent.md)]

## 後續步驟
- [備份 Windows Server 或 Windows 用戶端](backup-azure-backup-windows-server.md)
- [管理 Windows Server 或 Windows 用戶端](backup-azure-manage-windows-server.md)
- [從 Azure 還原 Windows Server 或 Windows 用戶端](backup-azure-restore-windows-server.md)
- [Azure 備份常見問題集](backup-azure-backup-faq.md)
- [Azure 備份論壇](http://go.microsoft.com/fwlink/p/?LinkId=290933)

