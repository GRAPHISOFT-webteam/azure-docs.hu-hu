<properties 
    pageTitle="使用彈性資料庫工具和資料列層級安全性的多租用戶應用程式" 
    description="了解如何搭配資料列層級安全性使用彈性資料庫工具建置應用程式，且讓此應用程式在 Azure SQL Database 上具有可支援多租用戶分區的高度可擴充資料層。" 
    metaKeywords="azure sql database elastic tools multi tenant row level security rls" 
    services="sql-database" documentationCenter=""  
    manager="jeffreyg" 
    authors="tmullaney"/>

<tags 
    ms.service="sql-database" 
    ms.workload="sql-database" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="11/03/2015" 
    ms.author="thmullan;torsteng;sidneyh" />


# 使用彈性資料庫工具和資料列層級安全性的多租用戶應用程式

[彈性資料庫工具](sql-database-elastic-scale-get-started.md) 和 [資料列層級安全性 (RLS)](https://msdn.microsoft.com/library/dn765131) 提供一組強大的功能，能以具彈性且有效率地調整多租用戶應用程式與 Azure SQL Database 的資料層。 本文說明如何運用這些技術，透過 **ADO.NET SqlClient** 和/或 **Entity Framework** 建置應用程式，且讓此應用程式具有可支援多租用戶分區的高度可擴充資料層。

* ****彈性資料庫工具可讓開發人員藉由使用 .NET 程式庫和 Azure 服務範本的一套業界標準分區化作法，向外延展應用程式的資料層。 使用「彈性資料庫用戶端程式庫」管理分區，有助於自動化及簡化許多通常與分區化相關的基礎結構工作。

* **資料列層級安全性**可讓開發人員使用安全性原則來篩選掉一些資料列 (這些資料列不屬於執行查詢的租用戶)，藉此在同一個資料庫中儲存多個租用戶的資料。 在資料庫內 (而不是在應用程式內) 集中存取邏輯與 RLS，可簡化維護流程並降低發生錯誤的風險 (因為應用程式的程式碼基底會成長)。 RLS 需要最新 [Azure SQL Database update (V12)](sql-database-preview-whats-new.md)。

搭配使用這些功能時，由於在同一個分區資料庫中儲存多租用戶的資料可節省成本並提高效率，進而使應用程式受益。 在此同時，應用程式仍會為「高階」租用戶彈性地提供隔離的單一租用戶分區，因為這些租用戶需要更嚴格的效能保證，畢竟多租用戶分區並不保證能在租用戶間平均分配資源。

簡單地說，彈性資料庫用戶端程式庫的 [資料相依路由](sql-database-elastic-scale-data-dependent-routing.md) Api 會自動將租用戶連接到正確的分區資料庫含有他們的分區化索引鍵 (通常為"TenantId")。 一旦連接之後，在資料庫內的 RLS 安全性原則可確保租用戶只能存取含有其 TenantId 的資料列。 此原則會假設所有資料表都包含一個會指出哪些資料列屬於哪個租用戶的 TenantId 資料行。

![部落格應用程式架構][1]

## 下載範例專案

### 必要條件

* 使用 Visual Studio (2012 或更新版本)
* 建立三個 Azure SQL Database
* 下載範例專案: [的 Azure SQL-多租用戶分區彈性資料庫工具](http://go.microsoft.com/?linkid=9888163)
  * 在 **Program.cs** 的開頭填入您的資料庫資訊

此專案會擴充所描述的 [Entity Framework 整合 Azure SQL 的彈性資料庫工具](sql-database-elastic-scale-use-entity-framework-applications-visual-studio.md) 透過支援多租用戶分區資料庫。 如上圖所示，這麼做可以建立具有四個租用戶與兩個多租用戶分區資料庫的簡單主控台應用程式，以用於建立部落格和文章。

建置並執行應用程式。 這會啟動彈性資料庫工具的分區對應管理員，並執行下列測試：

1. 使用 Entity Framework 和 LINQ，建立新的部落格，然後顯示每個租用戶的所有部落格
2. 使用 ADO.NET SqlClient，顯示某個租用戶的所有部落格
3. 嘗試插入錯誤的租用戶部落格，以確認是否擲回錯誤

請注意，因為分區資料庫中尚未啟用 RLS，所以這些測試都會顯現出一個問題：租用戶能夠查看不屬於自己的部落格，且應用程式無法阻止插入錯誤的租用戶部落格。 本文的其餘部分會說明，如何藉由 RLS 強制執行租用戶隔離來解決這些問題。 有兩個步驟：

1. **應用程式層**: 修改應用程式程式碼永遠 SESSION_CONTEXT 中開啟連接之後設定目前的 TenantId。 範例專案已經完成此步驟。
2. **資料層**: 根據儲存在 SESSION_CONTEXT TenantId 的篩選資料列的每個分區資料庫中建立 RLS 安全性原則。 您需要對每個分區資料庫執行這個動作，否則將不會篩選多租用戶分區中的資料列。


## 步驟 1) 應用程式層: SESSION_CONTEXT 中設定的 TenantId

在使用彈性資料庫用戶端程式庫的資料依存路由 API 連接到分區資料庫後，應用程式仍需告訴資料庫哪個 TenantId 使用該連接，然後 RLS 安全性原則才能篩選掉屬於其他租用戶的資料列。 建議的方式來傳遞此資訊是儲存在該連接目前的 TenantId [SESSION_CONTEXT](https://msdn.microsoft.com/library/mt590806.aspx)。 (請注意: 您也可以使用 [CONTEXT_INFO](https://msdn.microsoft.com/library/ms180125.aspx), ，但 SESSION_CONTEXT 是更好的選項很容易使用，因為根據預設，會傳回 NULL，而且支援索引鍵 / 值組。)

### Entity Framework

對於使用 Entity Framework 應用程式，最簡單的作法是設定中所述的 ElasticScaleContext 覆寫中 SESSION_CONTEXT [使用 EF DbContext 資料相依路由](sql-database-elastic-scale-use-entity-framework-applications-visual-studio.md/#data-dependent-routing-using-ef-dbcontext)。 後再傳回代理透過資料相依路由連接，只要建立並執行設定 'TenantId' 的 SqlCommand 來指定該連接之 shardingKey SESSION_CONTEXT 中。 如此一來，您只需要一次設定 SESSION_CONTEXT 撰寫程式碼。

```
// ElasticScaleContext.cs 
// ... 
// C'tor for data dependent routing. This call will open a validated connection routed to the proper 
// shard by the shard map manager. Note that the base class c'tor call will fail for an open connection 
// if migrations need to be done and SQL credentials are used. This is the reason for the  
// separation of c'tors into the DDR case (this c'tor) and the internal c'tor for new shards. 
public ElasticScaleContext(ShardMap shardMap, T shardingKey, string connectionStr)
    : base(OpenDDRConnection(shardMap, shardingKey, connectionStr), true /* contextOwnsConnection */)
{
}

public static SqlConnection OpenDDRConnection(ShardMap shardMap, T shardingKey, string connectionStr)
{
    // No initialization
    Database.SetInitializer<ElasticScaleContext<T>>(null);

    // Ask shard map to broker a validated connection for the given key
    SqlConnection conn = null;
    try
    {
        conn = shardMap.OpenConnectionForKey(shardingKey, connectionStr, ConnectionOptions.Validate);

        // Set TenantId in SESSION_CONTEXT to shardingKey to enable Row-Level Security filtering
        SqlCommand cmd = conn.CreateCommand();
        cmd.CommandText = @"exec sp_set_session_context @key=N'TenantId', @value=@shardingKey";
        cmd.Parameters.AddWithValue("@shardingKey", shardingKey);
        cmd.ExecuteNonQuery();

        return conn;
    }
    catch (Exception)
    {
        if (conn != null)
        {
            conn.Dispose();
        }

        throw;
    }
} 
// ... 
```

現在 SESSION_CONTEXT 會自動設定與指定的 TenantId 叫用 ElasticScaleContext 時:

```
// Program.cs 
SqlDatabaseUtils.SqlRetryPolicy.ExecuteAction(() => 
{   
    using (var db = new ElasticScaleContext<int>(sharding.ShardMap, tenantId, connStrBldr.ConnectionString))   
    {     
        var query = from b in db.Blogs
                    orderby b.Name
                    select b;

        Console.WriteLine("All blogs for TenantId {0}:", tenantId);     
        foreach (var item in query)     
        {       
            Console.WriteLine(item.Name);     
        }   
    } 
}); 
```

### ADO.NET SqlClient

對於使用 ADO.NET SqlClient 的應用程式，建議的方法是建立 shardmap.openconnectionforkey () 傳回連接之前自動設定為正確的 TenantId SESSION_CONTEXT 中的 'TenantId' 周圍的包裝函式。 若要確保 SESSION_CONTEXT 一定會設定，您應該只開啟使用此包裝函式的連線。

```
// Program.cs
// ...

// Wrapper function for ShardMap.OpenConnectionForKey() that automatically sets SESSION_CONTEXT with the correct
// tenantId before returning a connection. As a best practice, you should only open connections using this 
// method to ensure that SESSION_CONTEXT is always set before executing a query.
public static SqlConnection OpenConnectionForTenant(ShardMap shardMap, int tenantId, string connectionStr)
{
    SqlConnection conn = null;
    try
    {
        // Ask shard map to broker a validated connection for the given key
        conn = shardMap.OpenConnectionForKey(tenantId, connectionStr, ConnectionOptions.Validate);

        // Set TenantId in SESSION_CONTEXT to shardingKey to enable Row-Level Security filtering
        SqlCommand cmd = conn.CreateCommand();
        cmd.CommandText = @"exec sp_set_session_context @key=N'TenantId', @value=@shardingKey";
        cmd.Parameters.AddWithValue("@shardingKey", tenantId);
        cmd.ExecuteNonQuery();

        return conn;
    }
    catch (Exception)
    {
        if (conn != null)
        {
            conn.Dispose();
        }

        throw;
    }
}

// ...

// Example query via ADO.NET SqlClient
// If row-level security is enabled, only Tenant 4's blogs will be listed
SqlDatabaseUtils.SqlRetryPolicy.ExecuteAction(() =>
{
    using (SqlConnection conn = OpenConnectionForTenant(sharding.ShardMap, tenantId4, connStrBldr.ConnectionString))
    {
        SqlCommand cmd = conn.CreateCommand();
        cmd.CommandText = @"SELECT * FROM Blogs";

        Console.WriteLine("--\nAll blogs for TenantId {0} (using ADO.NET SqlClient):", tenantId4);
        SqlDataReader reader = cmd.ExecuteReader();
        while (reader.Read())
        {
            Console.WriteLine("{0}", reader["Name"]);
        }
    }
});
```

## 步驟 2) 資料層: 建立資料列層級安全性原則

### 建立安全性原則，每個租用戶可以存取的資料列篩選

現在，應用程式設定與目前的 TenantId SESSION_CONTEXT 查詢之前，RLS 安全性原則可以篩選查詢，並排除具有不同 TenantId 的資料列。

在 T-SQL 中已實作 RLS: 使用者定義函式定義存取邏輯和安全性原則將此函式繫結至任意數目的資料表。 此專案中，函式將只是檢查應用程式 (而非其他的 SQL 使用者) 連接到資料庫，而且 'TenantId' 儲存 SESSION_CONTEXT 中符合指定的資料列的 TenantId。 篩選述詞可讓符合這些條件的資料列通過 SELECT、UPDATE 和 DELETE 查詢的篩選；而封鎖述詞會避免違反這些條件的資料列進行 INSERT 或 UPDATE。 如果尚未設定 SESSION_CONTEXT，它會傳回 NULL，而且看不見或無法插入任何資料列。

若要啟用 RLS，，請使用 Visual Studio (SSDT)、 SSMS 或包含在專案中的 PowerShell 指令碼的所有分區上執行以下 T-SQL (或者，如果您使用 [彈性資料庫工作](sql-database-elastic-jobs-overview.md), ，您可用它來自動執行所有分區上此 T-SQL):

```
CREATE SCHEMA rls -- separate schema to organize RLS objects 
GO

CREATE FUNCTION rls.fn_tenantAccessPredicate(@TenantId int)     
    RETURNS TABLE     
    WITH SCHEMABINDING
AS
    RETURN SELECT 1 AS fn_accessResult          
        WHERE DATABASE_PRINCIPAL_ID() = DATABASE_PRINCIPAL_ID('dbo') -- the user in your application’s connection string (dbo is only for demo purposes!)         
        AND CAST(SESSION_CONTEXT(N'TenantId') AS int) = @TenantId
GO

CREATE SECURITY POLICY rls.tenantAccessPolicy
    ADD FILTER PREDICATE rls.fn_tenantAccessPredicate(TenantId) ON dbo.Blogs,
    ADD BLOCK PREDICATE rls.fn_tenantAccessPredicate(TenantId) ON dbo.Blogs,
    ADD FILTER PREDICATE rls.fn_tenantAccessPredicate(TenantId) ON dbo.Posts,
    ADD BLOCK PREDICATE rls.fn_tenantAccessPredicate(TenantId) ON dbo.Posts
GO 
```

> [AZURE.TIP] 對於需要數百個資料表上加入述詞的複雜專案，您可以使用協助程式預存程序，會自動產生的結構描述中的所有資料表上加入述詞的安全性原則。 請參閱 [將資料列層級安全性套用至所有資料表: 協助程式指令碼 (部落格)](http://blogs.msdn.com/b/sqlsecurity/archive/2015/03/31/apply-row-level-security-to-all-tables-helper-script)。  

現在如果您執行範例應用程式一次，租用戶會看到它們屬於資料列。 此外，應用程式無法插入屬於租用戶，不是一個目前連接到分區資料庫，而且也無法更新具有不同 TenantId 的可見資料列的資料列。 如果應用程式嘗試執行這兩項作業，就會引發 DbUpdateException。

如果您是在之後加入新的資料表，您只要「變更」安全性原則，並在新的資料表上加入篩選和封鎖述詞即可：

```
ALTER SECURITY POLICY rls.tenantAccessPolicy     
    ADD FILTER PREDICATE rls.fn_tenantAccessPredicate(TenantId) ON dbo.MyNewTable,
    ADD BLOCK PREDICATE rls.fn_tenantAccessPredicate(TenantId) ON dbo.MyNewTable
GO 
```

### 加入預設條件約束來為插入自動填入 TenantId

您可以在每個資料表上放置預設條件約束，以在插入資料列時，以 SESSION_CONTEXT 中目前儲存的值自動填入 TenantId。 例如：

```
-- Create default constraints to auto-populate TenantId with the value of SESSION_CONTEXT for inserts 
ALTER TABLE Blogs     
    ADD CONSTRAINT df_TenantId_Blogs      
    DEFAULT CAST(SESSION_CONTEXT(N'TenantId') AS int) FOR TenantId 
GO

ALTER TABLE Posts     
    ADD CONSTRAINT df_TenantId_Posts      
    DEFAULT CAST(SESSION_CONTEXT(N'TenantId') AS int) FOR TenantId 
GO 
```

現在應用程式不需要在插入資料列時指定 TenantId：

```
SqlDatabaseUtils.SqlRetryPolicy.ExecuteAction(() => 
{   
    using (var db = new ElasticScaleContext<int>(sharding.ShardMap, tenantId, connStrBldr.ConnectionString))
    {
        var blog = new Blog { Name = name }; // default constraint sets TenantId automatically     
        db.Blogs.Add(blog);     
        db.SaveChanges();   
    } 
}); 
```

> [AZURE.NOTE] 如果您需要針對 Entity Framework 專案使用預設條件約束，建議您「不要」在 EF 資料模型中包含 TenantId 資料行。 這是因為 Entity Framework 查詢會自動提供預設值會覆寫預設建立的條件約束在 T-SQL 中使用 SESSION_CONTEXT。 若要使用範例專案中的預設條件約束，舉例來說，您可以從 DataClasses.cs 移除 TenantId (並在 Package Manager Console 中執行 Add-Migration)，然後使用 T-SQL 確保欄位只存在於資料庫資料表中。 如此一來，EF 就不會在插入資料時，自動提供不正確的預設值。 

### (選擇性) 啟用「進階使用者」來存取所有資料列

有些應用程式可能需要建立一個能夠存取所有資料列的「進階使用者」，比方說，為了跨所有分區上的所有租用戶來產生報告，或在牽涉到資料庫之間移動租用戶資料列的分區上執行分割/合併作業。 為了達成此目的，您應該在每個分區資料庫中建立新的 SQL 使用者 (在本例中為 "superuser")。 然後使用新的述詞函式修改安全性原則，允許此使用者存取所有資料列：

```
-- New predicate function that adds superuser logic
CREATE FUNCTION rls.fn_tenantAccessPredicateWithSuperUser(@TenantId int)
    RETURNS TABLE
    WITH SCHEMABINDING
AS
    RETURN SELECT 1 AS fn_accessResult 
        WHERE 
        (
            DATABASE_PRINCIPAL_ID() = DATABASE_PRINCIPAL_ID('dbo') -- note, should not be dbo!
            AND CAST(SESSION_CONTEXT(N'TenantId') AS int) = @TenantId
        ) 
        OR
        (
            DATABASE_PRINCIPAL_ID() = DATABASE_PRINCIPAL_ID('superuser')
        )
GO

-- Atomically swap in the new predicate function on each table
ALTER SECURITY POLICY rls.tenantAccessPolicy
    ALTER FILTER PREDICATE rls.fn_tenantAccessPredicateWithSuperUser(TenantId) ON dbo.Blogs,
    ALTER BLOCK PREDICATE rls.fn_tenantAccessPredicateWithSuperUser(TenantId) ON dbo.Blogs,
    ALTER FILTER PREDICATE rls.fn_tenantAccessPredicateWithSuperUser(TenantId) ON dbo.Posts,
    ALTER BLOCK PREDICATE rls.fn_tenantAccessPredicateWithSuperUser(TenantId) ON dbo.Posts
GO
```


### 維護

* **加入新分區**: 您必須執行的 T-SQL 指令碼來啟用 RLS，所有新分區上的，否則不會篩選對這些分區的查詢。

* **加入新的資料表**: 您必須將篩選和封鎖的述詞加入所有分區上的安全性原則，每當建立新的資料表時，否則不會篩選對新資料表的查詢。 自動化此程序會使用 DDL 觸發程序中所述 [將資料列層級安全性套用至新建立的資料表 (部落格) 自動](http://blogs.msdn.com/b/sqlsecurity/archive/2015/05/22/apply-row-level-security-automatically-to-newly-created-tables.aspx)。


## 摘要

您可以將彈性資料庫工具與資料列層級安全性搭配使用，以支援多租用戶和單一租用戶的分區，藉此向外延展應用程式的資料層。 多租用戶分區可以用來更有效率地儲存資料 (特別是在有大量租用戶，卻只有些許資料列的情況)，而單一租用戶分區則可以用來支援高階租用戶，因為這類租用戶有更嚴格的效能和隔離需求。 如需詳細資訊，請參閱 [彈性資料庫工具文件地圖](sql-database-elastic-scale-documentation-map.md) 或 [資料列層級安全性參考](https://msdn.microsoft.com/library/dn765131) MSDN 上。


[AZURE.INCLUDE [elastic-scale-include](../../includes/elastic-scale-include.md)]









[1]: ./media/sql-database-elastic-tools-multi-tenant-row-level-security/blogging-app.png 

