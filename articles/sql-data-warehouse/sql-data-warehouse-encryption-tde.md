<properties 
    pageTitle="開始使用 SQL 資料倉儲中的透明資料加密 (TDE) | Microsoft Azure" 
    description="開始使用 SQL 資料倉儲中的透明資料加密 (TDE)" 
    services="sql-data-warehouse" 
    documentationCenter="" 
    authors="twounder" 
    manager="barbkess" 
    editor=""/>

<tags 
    ms.service="sql-data-warehouse" 
    ms.workload="data-management" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="11/20/2015" 
    ms.author="twounder"/>
 
# 開始使用 SQL 資料倉儲中的透明資料加密 (TDE)
> [AZURE.SELECTOR]
- [Azure 傳統入口網站](sql-data-warehouse-encryption-tde.md)
- [TSQL](sql-data-warehouse-encryption-tde-tsql.md)

Azure SQL 資料倉儲透明資料加密 (TDE) 可在不需變更應用程式的情況下，對靜止的資料庫、相關聯的備份和交易記錄檔執行即時加密和解密，協助防止惡意活動的威脅。

TDE 會使用稱為資料庫加密金鑰的對稱金鑰來加密整個資料庫的儲存體。 在 SQL Database 中，資料庫加密金鑰是由內建伺服器憑證保護。 內建伺服器憑證對每個 SQL Database 伺服器都是唯一的。 Microsoft 至少每 90 天會自動替換這些憑證。 如需 TDE 的一般說明，請參閱 [透明資料加密 (TDE)]。

##啟用加密

若要啟用 SQL 資料倉儲的 TDE，請遵循下列步驟：

1. 開啟中的資料庫 [Azure 傳統入口網站](https://portal.azure.com)
2. 在 [資料庫] 刀鋒視窗中，按一下 [ **設定** 按鈕 
3. 選取 **透明資料加密** 選項
![][1] 
4. 選取 **上** 設定
![][2] 
5. 選取 **儲存**
![][3]  

##停用加密

若要停用 SQL 資料倉儲的 TDE，請遵循下列步驟：

1. 開啟中的資料庫 [Azure 傳統入口網站](https://portal.azure.com)
2. 在 [資料庫] 刀鋒視窗中，按一下 [ **設定** 按鈕 
3. 選取 **透明資料加密** 選項
![][1] 
4. 選取 **關閉** 設定
![][4] 
5. 選取 **儲存**
![][5]  




<!--Anchors-->
[Transparent Data Encryption (TDE)]: https://msdn.microsoft.com/library/bb934049.aspx


<!--Image references-->
[1]: ./media/sql-data-warehouse-security-tde/sql-data-warehouse-security-tde-portal-settings.png
[2]: ./media/sql-data-warehouse-security-tde/sql-data-warehouse-security-tde-portal-settings-on.png
[3]: ./media/sql-data-warehouse-security-tde/sql-data-warehouse-security-tde-portal-settings-save.png
[4]: ./media/sql-data-warehouse-security-tde/sql-data-warehouse-security-tde-portal-settings-off.png
[5]: ./media/sql-data-warehouse-security-tde/sql-data-warehouse-security-tde-portal-settings-save2.png

<!--Link references-->

