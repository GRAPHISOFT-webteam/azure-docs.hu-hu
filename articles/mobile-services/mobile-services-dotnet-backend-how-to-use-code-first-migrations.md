<properties
    pageTitle="如何對 .NET 後端行動服務進行資料模型變更"
    description="本主題說明資料模型的初始設定式，以及如何在 .NET 後端行動服務中進行資料模型變更。"
    services="mobile-services"
    documentationCenter=""
    authors="ggailey777"
    writer="glenga"
    manager="dwrede"
    editor=""/>

<tags
    ms.service="mobile-services"
    ms.workload="mobile"
    ms.tgt_pltfrm="NA"
    ms.devlang="multiple"
    ms.topic="article"
    ms.date="12/07/2015"
    ms.author="glenga"/>

# 如何對 .NET 後端行動服務進行資料模型變更

[AZURE.INCLUDE [mobile-service-note-mobile-apps](../../includes/mobile-services-note-mobile-apps.md)]

&nbsp;


本主題說明如何使用 Entity Framework Code First 移轉對現有的 Azure SQL Database 進行資料模型變更，以避免遺失現有的資料。 此程序假設您已將 .NET 後端專案發佈至 Azure、您的資料庫中目前已有資料，且遠端與本機資料模型仍保持同步。 本主題同時說明 Azure 行動服務實作的預設 Code First 初始設定式，開發期間將會用到這些初始設定式。 當您不必保有現有的資料時，這些初始設定式可讓您輕鬆地進行結構描述變更，而不需要使用 Code First 移轉。

>[AZURE.NOTE]用來在 SQL 資料庫中的資料表前置詞的結構描述名稱是在 web.config 檔案中的 MS_MobileServiceName 應用程式設定所定義的。 當您從入口網站下載入門專案後，此值已設為行動服務名稱。 當您的結構描述名稱符合行動服務時，多個行動服務即可安全地共用同一個資料庫執行個體。

請注意在 .NET 後端專案中不支援自動移轉。

## 更新資料模型

當您將功能加入您的.NET 後端行動服務時，您會加入新的控制器，以公開您的 API 中的新端點。 您可以將新的 API 建立為自訂控制器或資料表控制器。 A [TableController<TEntity>] 公開 （expose) 資料型別繼承自 [EntityData]。 若要啟用要保存到資料庫的資料，此資料類型必須也加入為新的資料模型 [DbSet<T>] 中 [DbContext]。 若要深入了解 Entity Framework 中的 Code First，請參閱 [Code first 建立模型](https://msdn.microsoft.com/data/ee712907#codefirst)。

Visual Studio 可讓您輕鬆地建立新的資料表控制器，向用戶端應用程式公開新的資料類型。 如需詳細資訊，請參閱 [如何使用控制器來存取行動服務中的資料](https://msdn.microsoft.com/library/windows/apps/xaml/dn614132.aspx)。

## 資料模型初始設定式

行動服務在 .NET 後端行動服務專案中，提供兩種資料模型初始設定式基底類別。 這兩個初始設定式卸除並重新建立資料庫中的資料表，當 Entity Framework 偵測到資料模型變更，在您 [DbContext]。 這些初始設定式設計成不論行動服務在本機電腦上執行，或裝載於 Azure，都能使用。

>[AZURE.NOTE]當您發佈.NET 後端行動服務時，直到發生資料存取作業，將不會執行初始設定式。 這表示對於新發佈的服務而言，直到用戶端要求資料存取作業 (如查詢)，才會建立用於儲存的資料表。
>
>您也可以執行資料存取作業使用的內建的 API 說明功能，從存取 **試試看** 啟動頁面上的連結。 如需有關使用 API 頁面來測試您的行動服務，請參閱測試行動服務專案的本機環境中的章節 [將行動服務新增至現有的應用程式](mobile-services-dotnet-backend-windows-universal-dotnet-get-started-data.md#test-the-service-locally)。

這兩個初始設定式會從資料庫中，刪除行動服務所用結構描述中的所有資料表、檢視、函式和程序。

+ **ClearDatabaseSchemaIfModelChanges** <br/> 只有在 Code First 偵測到資料模型變更時，才會刪除結構描述物件。 從下載的.NET 後端專案中的預設初始設定式 [Azure classic portal] 繼承自這個基底類別。

+ **ClearDatabaseSchemaAlways**: <br/> 每次存取資料模型都會刪除結構描述物件。 這個基底類別可用來重設資料庫，而不需要進行資料模型變更。

在本機電腦上執行時，可以使用其他 Code First 資料模型初始設定式。 但是，由於使用者沒有可捨棄資料庫的權限，因此嘗試捨棄資料庫的初始設定式在 Azure 中會失敗，這是件好事。

您在行動服務的本機開發期間可繼續使用初始設定式，因此 .NET 後端教學課程假設您使用初始設定式。 但如果您要進行資料模型變更，並保有資料庫中現有的資料，則必須使用 Code First 移轉。

>[AZURE.IMPORTANT]當開發及測試您的行動服務專案，對即時 Azure 服務，您一律應該使用專用的行動服務執行個體測試。 切勿對目前實際執行或由用戶端應用程式使用中的行動服務進行開發或測試。

在下載的快速入門專案中，Code First 初始設定式會在 WebApiConfig.cs 檔中定義。 覆寫 **種子** 方法，將開頭幾列資料加入至新的資料表。 如需植入資料的範例，請參閱 [Seeding data in migrations]。

## <a name="migrations"></a>啟用 Code First 移轉

「Code First 移轉」會使用快照方法，產生在執行時會對資料庫進行結構描述變更的程式碼。 透過「移轉」，您將可對資料模型進行增量變更，並保有資料庫中現有的資料。

>[AZURE.NOTE]如果您已將.NET 後端行動服務專案發佈至 Azure，您的 SQL Database 資料表結構描述不符的專案目前的資料模型，您必須使用初始設定式、 手動、 卸除的資料表或將結構描述和資料模型保持同步，再嘗試使用 Code First 移轉進行發佈。

下列步驟會開啟「移轉」，並且在專案、本機資料庫和 Azure 中套用資料模型變更。

1. 在 Visual Studio [方案總管] 中，請在行動服務專案上按一下滑鼠右鍵，然後按一下 [ **設定為啟始專案**。

2. 從 **工具** ] 功能表上，依序展開 **NuGet 封裝管理員**, ，然後按一下 [ **Package Manager Console**。

    這會顯示 [Package Manager Console]，以供您管理 Code First 移轉。

3. 在 [Package Manager Console] 中，輸入下列命令：

        PM> Enable-Migrations

    這會為您的專案開啟「Code First 移轉」。

4. 在主控台中，執行下列命令：

        PM> Add-Migration Initial

    這會建立名為的新移轉 *初始*。 移轉程式碼會儲存在 Migrations 專案資料夾中。

5. 展開 App_Start 資料夾，開啟 WebApiConfig.cs 專案檔案，然後新增下列 **使用** 陳述式 ︰

        using System.Data.Entity.Migrations;
        using todolistService.Migrations;

    在上述程式碼中，您必須將 _todolistService_ 字串取代為您的專案，即已下載的快速入門專案的命名空間 <em>mobile & #95 服務 & #95; 名稱</em>服務。

6. 在相同的程式碼檔案中，標記為註解來呼叫 **Database.SetInitializer** 方法和其後新增下列程式碼 ︰

        var migrator = new DbMigrator(new Configuration());
        migrator.Update();

    這會停用捨棄並重新建立資料庫的預設 Code First 資料庫初始設定式，並將其取代為要套用最新移轉的明確要求。 此時，只要存取資料，任何資料模型變更都將導致 InvalidOperationException，除非您已建立資料模型的移轉。 接下來，您的服務必須使用「Code First 移轉」，將資料模型變更移轉至資料庫。

7.  按 F5，在本機電腦上啟動行動服務專案。

    此時，資料庫會與資料模型同步。 如果您提供種子資料，您可以確認它即可 **試試看**, ，**GET 資料表 todoitem**, ，然後 **試試看** 和 **傳送**。 如需詳細資訊，請參閱 [Seeding data in migrations]。

8.   現在，請對資料模型進行變更 (例如，將新的 UserId 屬性新增至 TodoItem 類型、重新建置專案)，然後在封裝管理員中執行下列命令：

        PM> Add-Migration NewUserId

    This creates a new migration named *NewUserId*. A new code file, which implements this change, is added in the Migrations folder

9.  再按一次 F5，在本機電腦上重新啟動行動服務專案。

    移轉會套用至資料庫，且資料庫會再次與資料模型同步。 如果您提供種子資料，您可以確認它即可 **試試看**, ，**GET 資料表 todoitem**, ，然後 **試試看** 和 **傳送**。 如需詳細資訊，請參閱 [Seeding data in migrations]。

10. 將行動服務重新發佈至 Azure，然後執行用戶端應用程式以存取資料，並驗證資料確實載入且未發生錯誤。

13. （選擇性）在 [Azure classic portal], ，選取您的行動服務，按一下 **設定** > **SQL Database**。 這會導覽至您行動服務資料庫的 SQL Database 頁面。

14. （選擇性）按一下 [ **管理**, ，登入您的 SQL 資料庫伺服器，然後按一下 [ **設計** 和驗證結構描述變更，已在 Azure 中進行。


## 使用 Code First 移轉而不使用初始設定式
您在 .NET 後端專案使用 Code First 移轉之前，您應該先執行資料模型初始設定式。 如果您不使用初始設定式，則在嘗試套用移轉時會發生錯誤。 如果您選擇不使用其中一個預先定義的資料模型初始設定式，請確定移轉設定為使用 Migrations\Configuration.cs 檔案中的 EntityTableSqlGenerator 做為 SqlGenerator，如下列範例所示：

    public Configuration()
    {
        AutomaticMigrationsEnabled = false;
        SetSqlGenerator("System.Data.SqlClient", new EntityTableSqlGenerator());
    }

##<a name="seeding"></a>在移轉中植入資料

您可以讓「移轉」在移轉執行時新增種子資料至資料庫。  **組態** 類別具有 **種子** 方法，您可以覆寫來插入或更新資料。 在啟用「移轉」時，Configuration.cs 程式碼檔案會新增至 Migrations 資料夾。 下列範例示範如何覆寫 [Seed] 方法，以資料植入 **TodoItems** 資料表。  [Seed] 移轉至最新版本後呼叫方法。

###植入新資料表

下列程式碼植入 **TodoItems** 新的資料列的資料表 ︰

        List<TodoItem> todoItems = new List<TodoItem>
        {
            new TodoItem { Id = "1", Text = "First item", Complete = false },
            new TodoItem { Id = "2", Text = "Second item", Complete = false },
        };

        foreach (TodoItem todoItem in todoItems)
        {
            context.Set<TodoItem>().Add(todoItem);
        }
        base.Seed(context);

###在資料表中植入新資料行

下列程式碼只會植入 UserId 資料行：

        context.TodoItems.AddOrUpdate(
            t => t.UserId,
                new TodoItem { UserId = 1 },
                new TodoItem { UserId = 1 },
                new TodoItem { UserId = 2 }
            );
        base.Seed(context);

此程式碼呼叫 [AddOrUpdate] 協助程式擴充方法，以將種子資料加入至新的 UserId 資料行。 使用 [AddOrUpdate], ，不會建立重複的資料列。

<!-- Anchors -->
[Migrations]: #migrations
[Seeding data in migrations]: #seeding

<!-- Images -->
[0]: ./media/mobile-services-dotnet-backend-how-to-use-code-first-migrations/navagate-to-sql-database.png
[1]: ./media/mobile-services-dotnet-backend-how-to-use-code-first-migrations/manage-sql-database.png
[2]: ./media/mobile-services-dotnet-backend-how-to-use-code-first-migrations/sql-database-drop-tables.png

<!-- URLs -->
[DropCreateDatabaseIfModelChanges]: http://msdn.microsoft.com/library/gg679604(v=vs.113).aspx
[Seed]: http://msdn.microsoft.com/library/hh829453(v=vs.113).aspx
[Azure classic portal]: https://manage.windowsazure.com/
[DbContext]: http://msdn.microsoft.com/library/system.data.entity.dbcontext(v=vs.113).aspx
[AddOrUpdate]: http://msdn.microsoft.com/library/system.data.entity.migrations.idbsetextensions.addorupdate(v=vs.103).aspx
[TableController<TEntity>]: https://msdn.microsoft.com/library/azure/dn643359.aspx
[EntityData]: https://msdn.microsoft.com/library/azure/microsoft.windowsazure.mobile.service.entitydata.aspx
[DbSet<T>]: https://msdn.microsoft.com/library/azure/gg696460.aspx


