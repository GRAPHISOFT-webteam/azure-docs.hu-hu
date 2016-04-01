<properties
    pageTitle="使用 Azure SQL Database 來為備份及還原進行疑難排解"
    description="了解如何使用 Azure SQL Database 中的備份和複本，從錯誤和中斷情況復原雲端資料庫。"
    services="sql-database"
    documentationCenter=""
    authors="dalechen"
    manager="msmets"
    editor=""/>

<tags
    ms.service="sql-database"
    ms.workload="data-management"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="12/10/2015"
    ms.author="daleche"/>

# 將資料庫還原至先前的時間點、還原已刪除的資料庫，或從資料中心中斷情況復原

SQL Database 會保留資料庫的複本，因此您可以從中斷情況與使用者錯誤復原。 可用的選項取決於資料庫服務層和您選擇的選項。 請參閱 [業務持續性概觀](sql-database-business-continuity.md) 詳細資料和設計考量。

##若要將資料庫還原至先前的時間點
1.  在 [Azure 入口網站](https://azure.microsoft.com/), ，按一下 [ **SQL 資料庫**。
2.  從清單中選取您的資料庫，然後按一下 [ **還原**。
3.  輸入新資料庫的名稱、 選擇的日期和時間、 還原，然後按一下 **建立。**
4.  視需要調整應用程式以參考新的資料庫。 請參閱 [從使用者錯誤復原資料庫](sql-database-user-error-recovery.md)。

##若要還原不小心刪除的資料庫
1.  在 [Azure 入口網站](https://azure.microsoft.com/), ，按一下 [ **SQL 伺服器**。
2.  從清單中選取裝載資料庫的伺服器。
3.  在 [伺服器] 分頁中，向下捲動並按一下 [ **已刪除資料庫**。
4.  選取要還原，，然後按一下資料庫 **建立**。
5.  視需要調整應用程式以參考新的資料庫。 請參閱 [從使用者錯誤復原資料庫](sql-database-user-error-recovery.md)。

##若要從區域資料中心中斷情況復原
使用 Standard 和 Premium 資料庫時，如果您設定地理複寫的次要資料庫，您可以使用這些次要資料庫復原。 如此一來，還原資料庫時比較不容易遺失資料。 請參閱 [從中斷復原 Azure SQL database](sql-database-disaster-recovery.md) 如需詳細資訊。

Azure 也能為不同區域中 (異地備援備份) 的每個資料庫提供備份。 您可以從這些備份 (稱為異地還原) 建立新的資料庫，但如果您單獨採用此方法，很可能會發生資料遺失。

**若要使用異地還原復原資料庫：**

- 在 [Azure 入口網站](https://azure.microsoft.com/), ，按一下 [ **新增**, ，按一下 **資料和儲存體**, ，按一下 **SQL 資料庫**, ，然後選取 **備份** 做為資料庫來源。 請參閱 [從中斷復原 Azure SQL database](sql-database-disaster-recovery.md) 如需詳細資訊。


