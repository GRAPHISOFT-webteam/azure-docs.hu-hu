本節中，我們將修改資料庫的模型加入名為的新時間戳記欄位 **CompleteDate**。 此欄位將記錄前次完成的 Todo 項目時間。 Entity Framework 將會更新資料庫，根據我們模型變更，使用預設資料庫初始設定式類別衍生自 [DropCreateDatabaseIfModelChanges](http://go.microsoft.com/fwlink/?LinkId=394621)。 

1. 在 Visual studio 方案總管] 中展開 **App_Start** todolist 服務專案資料夾中的。 開啟 WebApiConfig.cs 檔案。

2. 在 WebApiConfig.cs 檔案中，注意您的預設資料庫初始設定式類別是衍生自 `DropCreateDatabaseIfModelChanges` 類別。 這意味對模型的任何變更都會導致資料表捨棄，並重新建立以容納新模型。 因此資料表的資料將流失，並且將重新植入資料表。 修改資料庫初始設定式的 Seed 方法，以便如下所示將種子資料儲存至 WebApiConfig.cs 檔案。

    >[AZURE.NOTE] 當使用預設資料庫初始設定式時，Entity Framework 會卸除並 Code First 模型定義中偵測到資料模型變更時重新建立資料庫。 若要進行此資料模型變更，並保有資料庫的現有資料，必須使用 Code First Migrations。 如需詳細資訊，請參閱 [如何使用 Code First Migrations 更新資料模型](../articles/mobile-services-dotnet-backend-how-to-use-code-first-migrations.md)。

        List<TodoItem> todoItems = new List<TodoItem>
        {
          new TodoItem { Id = "1", Text = "First seed item", Complete = false },
          new TodoItem { Id = "2", Text = "Second seed item", Complete = false },
        };
     

3. 在 Visual studio 方案總管] 中展開 **DataObjects** todolist 服務專案資料夾中的。 開啟 TodoItem.cs 檔案，並更新 TodoItem 類別以加入 CompleteDate 欄位，如下所示。 接著儲存 TodoItem.cs 檔案。

        public class TodoItem : EntityData
        {
          public string Text { get; set; }
          public bool Complete { get; set; }
          public System.DateTime? CompleteDate { get; set; }
        }

4. 在 Visual studio 方案總管] 中展開 **控制器** todolist 服務專案資料夾中的。 開啟 TodoItemController.cs 檔案並更新 `PatchTodoItem` 方法，使它將設定 **CompleteDate** 時 **完成** 屬性從 false 變更為 true。 接著儲存 TodoItemController.cs 檔案。

        public Task<TodoItem> PatchTodoItem(string id, Delta<TodoItem> patch)
        {
            // If complete is being set to true and was false, set CompleteDate
            if ((patch.GetEntity().Complete == true) &&
                (GetTodoItem(id).Queryable.Single().Complete == false))
            {
                patch.TrySetPropertyValue("CompleteDate", System.DateTime.Now);
            }
            return UpdateAsync(id, patch);
        }


5. 重新建置 todolist .NET 後端服務專案並確認未發生建置錯誤。 

接下來，您將會更新用戶端應用程式，以顯示新 **CompleteDate** 資料。
