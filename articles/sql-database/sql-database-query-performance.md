<properties 
   pageTitle="Azure SQL Database 查詢效能深入解析" 
   description="查詢效能監視可識別 Azure SQL Database 的大部分 CPU 取用的查詢。" 
   services="sql-database" 
   documentationCenter="" 
   authors="stevestein" 
   manager="jeffreyg" 
   editor="monicar"/>

<tags
   ms.service="sql-database"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="data-management" 
   ms.date="12/02/2015"
   ms.author="sstein"/>

# Azure SQL Database 查詢效能深入解析


管理和調整關聯式資料庫效能是一項具挑戰性的工作，需要投入大量的專業知識和時間。 「查詢效能深入解析」提供了下列各項，讓您得以花費較少的時間來疑難排解資料庫效能：

- 更深入的資料庫資源 (DTU) 取用分析。 
- 排名最前面的 CPU 取用查詢，進行微調有可能會改善效能。 
- 能夠向下鑽研查詢的詳細資料。
​

## 必要條件

- 「查詢效能深入解析」僅適用於 Azure SQL Database V12。
- 查詢效能深入解析要求 [查詢存放區](https://msdn.microsoft.com/library/dn817826.aspx) 在您的資料庫上執行。 如果查詢存放區還未執行，入口網站將會提示您開啟它。

 
## 權限

下列 [角色型存取控制](role-based-access-control-configure.md) 權限，才能使用查詢效能深入資訊 ︰ 

- **讀取器**, ，**擁有者**, ，**參與者**, ，**SQL DB 參與者** 或 **SQL Server 參與者** 不需要檢視最上層的資源取用查詢和圖表權限。 
- **擁有者**, ，**參與者**, ，**SQL DB 參與者** 或 **SQL Server 參與者** 權限，才能檢視查詢文字。



## 使用查詢效能深入解析

「查詢效能深入解析」很容易使用︰

- 檢閱排名最前面的資源取用查詢清單。 
- 選取個別查詢以檢視其詳細資料。
- 按一下 [ **設定** 來自訂資料的顯示方式，或顯示不同的時間期間。



> [AZURE.NOTE] 需要 SQL Database 以提供查詢效能深入資訊的查詢存放區擷取了好幾個小時的資料。 如果在某段時間內資料庫沒有任何作用中的活動或查詢存放區，則在顯示該期間時圖表會是空的。 如果查詢存放區不在執行中，您可隨時加以啟用。   



## 檢閱排名最前面的 CPU 取用查詢

在 [入口網站](https://portal.azure.com) 執行下列動作 ︰

1. 瀏覽至 SQL 資料庫，然後按一下 [ **查詢效能深入解析**。 

    ![查詢效能深入解析][1]

    排名最前面的查詢檢視隨即開啟並列出排名最前面的 CPU 取用查詢。

1. 按一下以取得詳細資料圖表周圍。<br>第一行會顯示資料庫的整體 DTU %橫條圖顯示選取之查詢所耗用的 CPU %。 選取或清除圖表要包含或排除的個別查詢。

    ![排名最前面的查詢][2]

1. （選擇性） 按一下 **編輯圖表** 來自訂 CPU 耗用量資料的顯示方式，或顯示不同的時間期間。

## 檢視個別查詢詳細資料

若要檢視查詢詳細資料︰

1. 按一下排名最前面的查詢清單中的任何查詢。<br>詳細資料檢視會隨即開啟，而查詢的 CPU 取用量會根據時間來細分。
3. 按一下以取得詳細資料圖表周圍。<br>第一行整體 DTU %，且長條是選取的查詢所耗用的 CPU %。
4. 檢閱此資料以查看詳細的度量，包括執行查詢的每個間隔的持續時間、執行次數，以及資源使用率百分比。
    
    ![查詢詳細資料][3]

1. （選擇性） 按一下 **設定** 來自訂 CPU 耗用量資料的顯示方式，或顯示不同的時間期間。




## 摘要

「查詢效能深入解析」可協助您了解您的查詢工作負載的影響，以及其與資料庫資源取用量的關係。 使用此功能，您將了解排名最前面的取用查詢，並且在發生問題前輕鬆地找出要修正的項目。 按一下 [ **查詢效能深入解析** 磚以查看取用查詢的最佳資源 (CPU) 的資料庫刀鋒視窗。 




## 後續步驟

資料庫工作負載會動態地持續變更。 監視您的查詢並繼續進行微調，以改善效能。 

簽出 [索引顧問](sql-database-index-advisor.md) 改善您的 SQL database 效能的其他建議。

<!--Image references-->
[1]: ./media/sql-database-query-performance/tile.png
[2]: ./media/sql-database-query-performance/top-queries.png
[3]: ./media/sql-database-query-performance/query-details.png





