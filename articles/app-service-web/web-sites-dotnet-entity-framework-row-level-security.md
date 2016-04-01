<properties
    pageTitle="教學課程：使用 Entity Framework 和資料列層級安全性的 Web 應用程式搭配多租用戶資料庫"
    description="了解如何使用 Entity Framework 和資料列層級安全性，搭配多租用戶 SQL Database 後端來開發 ASP.NET MVC 5 Web 應用程式。"
  metaKeywords="azure asp.net mvc entity framework multi tenant row level security rls sql database"
    services="app-service\web"
    documentationCenter=".net"
    manager="jeffreyg"
  authors="tmullaney"/>

<tags
    ms.service="app-service-web"
    ms.workload="web"
    ms.tgt_pltfrm="na"
    ms.devlang="dotnet"
    ms.topic="article"
    ms.date="10/30/2015"
    ms.author="thmullan"/>

# 教學課程：使用 Entity Framework 和資料列層級安全性的 Web 應用程式搭配多租用戶資料庫

本教學課程示範如何建置多租用戶 web 應用程式 」[共用的資料庫、 共用的結構描述](https://msdn.microsoft.com/library/aa479086.aspx)「 租用戶模型，使用 Entity Framework 和 [資料列層級安全性](https://msdn.microsoft.com/library/dn765131.aspx)。 在此模型中，單一資料庫包含許多租用戶的資料，而且每個資料表中的每個資料列都有一個相關聯的「租用戶識別碼」。 資料列層級安全性 (RLS) 是 Azure SQL Database 的新功能，用來防止租用戶存取彼此的資料。 這只需要對應用程式進行一次微幅的變更即可。 RLS 將租用戶存取邏輯集中在資料庫本身之內，簡化應用程式碼並降低租用戶之間不慎洩露資料的風險。

舉個簡單的連絡人管理員應用程式，從 [使用驗證和 SQL DB 建立 ASP.NET 的 MVP 應用程式並部署至 Azure App Service](web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database.md)。 現在，此應用程式允許所有使用者 (租用戶) 看見所有連絡人：

![啟用 RLS 之前的連絡人管理員應用程式](./media/web-sites-dotnet-entity-framework-row-level-security/ContactManagerApp-Before.png)

只要經過少數的微幅變更，我們就能支援多租用戶，讓使用者能夠看見屬於他們的連絡人。

## 步驟 1：在應用程式中加入「攔截器」類別來設定 SESSION_CONTEXT

我們需要進行一項應用程式變更。 因為所有應用程式使用者使用相同的連接字串 (也就是相同的 SQL 登入) 連線到資料庫，RLS 原則目前無法知道應該篩選哪一個使用者。 這種方法在 Web 應用程式中很常見，因為可讓連接共用更有效率，但這表示我們需要另一種方式來識別資料庫內目前的應用程式使用者。 解決方法是將應用程式設定的索引鍵 / 值組中目前的使用者識別碼 [SESSION_CONTEXT](https://msdn.microsoft.com/library/mt590806) 它執行查詢之前。 SESSION_CONTEXT 是工作階段範圍的機碼值存放區，RLS 原則會使用其中儲存的 UserId 來識別目前的使用者。 *注意：SESSION_CONTEXT 目前是 Azure SQL Database 的預覽功能。*

我們將加入 [攔截器](https://msdn.microsoft.com/data/dn469464.aspx), ，在 Entity Framework (EF) 6，以自動在 SESSION_CONTEXT EF 執行每個查詢之前，附加在前面的 T-SQL 陳述式設定目前的使用者識別碼的新功能。

1.  在 Visual Studio 中開啟 ContactManager 專案。
2.  在 [方案總管] 中，以滑鼠右鍵按一下 [模型] 資料夾，然後選擇 [新增] -> [類別]。
3.  將新類別命名為 "SessionContextInterceptor.cs"，按一下 [加入]。
4.  使用下列程式碼取代 SessionContextInterceptor.cs 的內容。

```
using System;
using System.Collections.Generic;
using System.Linq;
using System.Web;
using System.Data.Common;
using System.Data.SqlClient;
using System.Data.Entity;
using System.Data.Entity.Infrastructure.Interception;
using Microsoft.AspNet.Identity;

namespace ContactManager.Models
{
    public class SessionContextInterceptor : IDbCommandInterceptor
    {
        private void SetSessionContext(DbCommand command)
        {
            try
            {
                var userId = System.Web.HttpContext.Current.User.Identity.GetUserId();
                if (userId != null)
                {
                    // Set SESSION_CONTEXT to current UserId before executing queries
                    var sql = "EXEC sp_set_session_context @key=N'UserId', @value=@UserId;";

                    command.CommandText = sql + command.CommandText;
                    command.Parameters.Insert(0, new SqlParameter("@UserId", userId));
                }
            }
            catch (System.NullReferenceException)
            {
                // If no user is logged in, leave SESSION_CONTEXT null (all rows will be filtered)
            }
        }
        public void NonQueryExecuting(DbCommand command, DbCommandInterceptionContext<int> interceptionContext)
        {
            this.SetSessionContext(command);
        }
        public void NonQueryExecuted(DbCommand command, DbCommandInterceptionContext<int> interceptionContext)
        {

        }
        public void ReaderExecuting(DbCommand command, DbCommandInterceptionContext<DbDataReader> interceptionContext)
        {
            this.SetSessionContext(command);
        }
        public void ReaderExecuted(DbCommand command, DbCommandInterceptionContext<DbDataReader> interceptionContext)
        {

        }
        public void ScalarExecuting(DbCommand command, DbCommandInterceptionContext<object> interceptionContext)
        {
            this.SetSessionContext(command);
        }
        public void ScalarExecuted(DbCommand command, DbCommandInterceptionContext<object> interceptionContext)
        {

        }
    }

    public class SessionContextConfiguration : DbConfiguration
    {
        public SessionContextConfiguration()
        {
            AddInterceptor(new SessionContextInterceptor());
        }
    }
}
```

這是唯一需要的應用程式變更。 接著開始建置並發佈應用程式。

## 步驟 2：將 UserId 資料行加入至資料庫結構描述

接下來，我們需要將 UserId 資料行加入至 Contacts 資料表，使每個資料列與使用者 (租用戶) 相關聯。 我們將直接在資料庫中變更結構描述，所以 EF 資料模型中不必包含此欄位。

使用 SQL Server Management Studio 或 Visual Studio，直接連接到資料庫，然後執行下列 T-SQL：

```
ALTER TABLE Contacts ADD UserId nvarchar(128)
    DEFAULT CAST(SESSION_CONTEXT(N'UserId') AS nvarchar(128))
```

這會將 UserId 資料行加入至 Contacts 資料表。 我們使用 nvarchar (128) 資料類型來比對儲存在 AspNetUsers 資料表中的 UserId，我們也建立 DEFAULT 條件約束，自動將新插入的資料列的 UserId 設定為目前儲存在 SESSION_CONTEXT 中的 UserId。

現在，資料表如下所示：

![SSMS Contacts 資料表](./media/web-sites-dotnet-entity-framework-row-level-security/SSMS-Contacts.png)

建立新的連絡人時，將會自動指派正確的 UserId 給他們。 但為了示範，我們還是指派幾個現有的連絡人給現有的使用者。

如果您已在應用程式建立一些使用者 (例如使用本機、Google 或 Facebook 帳戶)，就可以在 AspNetUsers 資料表中看見他們。 在以下的螢幕擷取畫面中，目前為止只有一個使用者。

![SSMS AspNetUsers 資料表](./media/web-sites-dotnet-entity-framework-row-level-security/SSMS-AspNetUsers.png)

複製 user1@contoso.com 的識別碼，並將它貼到下列 T-SQL 陳述式。 執行此陳述式，將三個連絡人與此 UserId 相關聯。

```
UPDATE Contacts SET UserId = '19bc9b0d-28dd-4510-bd5e-d6b6d445f511'
WHERE ContactId IN (1, 2, 5)
```

## 步驟 3：在資料庫中建立資料列層級安全性原則

最後一個步驟是建立安全性原則，使用 SESSION_CONTEXT 中的 UserId 自動篩選查詢所傳回的結果。

在仍然連接資料庫的情況下，執行下列 T-SQL：

```
CREATE SCHEMA Security
go

CREATE FUNCTION Security.userAccessPredicate(@UserId nvarchar(128))
    RETURNS TABLE
    WITH SCHEMABINDING
AS
    RETURN SELECT 1 AS accessResult
    WHERE @UserId = CAST(SESSION_CONTEXT(N'UserId') AS nvarchar(128))
go

CREATE SECURITY POLICY Security.userSecurityPolicy
    ADD FILTER PREDICATE Security.userAccessPredicate(UserId) ON dbo.Contacts,
    ADD BLOCK PREDICATE Security.userAccessPredicate(UserId) ON dbo.Contacts
go

```

此程式碼會執行三個動作。 首先，建立新的結構描述，當作集中管理和限制 RLS 物件存取權的最佳作法。 接著，建立述詞函數，當資料列的 UserId 符合 SESSION_CONTEXT 中的 UserId 時，將傳回 '1'。 最後，建立安全性原則，在 Contacts 資料表上加入此函數作為篩選和封鎖述詞。 篩選述詞可讓查詢只傳回屬於目前使用者的資料列，而封鎖述詞充當保護措施，防止應用程式不慎插入錯誤使用者的資料列。 *注意 ︰ 區塊述詞目前的 Azure SQL Database 的預覽功能。*

現在，執行應用程式，並以 user1@contoso.com 身分登入。 這位使用者現在只會看到我們稍早指派給此 UserId 的連絡人：

![啟用 RLS 之前的連絡人管理員應用程式](./media/web-sites-dotnet-entity-framework-row-level-security/ContactManagerApp-After.png)

若要進一步驗證，請嘗試註冊新的使用者。 因為尚未指派連絡人給他們，他們看不到任何連絡人。 如果他們建立新的連絡人，則會指派給他們，也只有他們才能看見這個連絡人。

## 後續步驟

就這麼簡單！ 這個簡單的連絡人管理員 Web 應用程式已轉換成多租用戶應用程式，每個使用者都有自己的連絡人清單。 由於使用資料列層級安全性，我們已避免在應用程式碼中強制執行租用戶存取邏輯的複雜性。 這種透明度可讓應用程式專注於處理目前的實際商務問題，而隨著應用程式的程式碼基底不斷成長，也能降低意外洩漏資料的風險。

本教學課程只是稍微示範一下 RLS 的功能。 比方說，存取邏輯可以更複雜或更精細，而 SESSION_CONTEXT 中也不僅止於只能儲存目前的 UserId 而已。 它也可讓 [整合彈性資料庫工具用戶端程式庫的 RLS](../sql-database/sql-database-elastic-tools-multi-tenant-row-level-security.md) 支援向外擴充資料層中的多租用戶分區。

除了這些可能性，我們也正在努力讓 RLS 更臻完美。 如果您有任何疑問、構想或期望，請留下您的意見。 歡迎提供意見反應！


