1.  登入線上 [Microsoft Azure Preview 入口網站](https://portal.azure.com/)。
2.  在 Jumpbar，依序按一下 [**新增**]、[**資料 + 儲存體**]、[**Azure DocumentDB**]。

    ![若要建立資料庫時，反白顯示 ](media/documentdb-create-dbaccount/ca1.png)

3. 在 [新增 DocumentDB 帳戶]**** 刀鋒視窗中，指定想要的 DocumentDB 帳戶組態。

    ![[新增 DocumentDB] 刀鋒視窗的螢幕擷取畫面](media/documentdb-create-dbaccount/ca3.png)

    - 在 [識別碼]**** 方塊中，輸入用來識別 DocumentDB 帳戶的名稱。 驗證 [識別碼]**** 時，[識別碼]**** 方塊中會出現綠色的核取記號。 [識別碼]**** 值會變成 URI 中的主機名稱。 此 [識別碼]**** 只能包含小寫字母、數字及 '-' 字元，且長度必須為 3 到 50 個字元。 請注意，*documents.azure.com* 會附加至您選擇的端點名稱後面，產生的結果將成為您的 DocumentDB 帳戶端點。

    - [**帳戶層**] 透鏡已被鎖定，因為 DocumentDB 僅支援單一標準帳戶層。如需詳細資訊，請參閱 [DocumentDB 定價](http://go.microsoft.com/fwlink/p/?LinkID=402317&clcid=0x409)。

    - 在 [訂用帳戶]**** 中，選取您要用於 DocumentDB 帳戶的 Azure 訂用帳戶。 如果您的帳戶只有一個訂用帳戶，預設會選取該帳戶。

    - 在 [資源群組]**** 中，選取或建立 DocumentDB 帳戶的資源群組。 依預設會建立新的資源群組。 不過，您可以選擇現有的資源群組，以便在其中加入您的 DocumentDB 帳戶。 如需詳細資訊，請參閱 [使用 Azure Preview 入口網站來管理您的 Azure 資源](resource-group-portal.md)。

    - 使用 [位置]**** 指定將代管您的 DocumentDB 帳戶的地理位置。

4.  設定新的 DocumentDB 帳戶選項之後，按一下 [建立]****。 建立 DocumentDB 帳戶可能需要數分鐘。 若要檢查狀態，您可以在「開始面板」上監視進度。  
    ![「開始面板」上 ](media/documentdb-create-dbaccount/ca4.png)

    或者，您也可以從 [通知] 中樞監視進度。

    ![快速建立資料庫 - 通知中樞的螢幕擷取畫面，顯示正在建立 DocumentDB 帳戶](media/documentdb-create-dbaccount/ca5.png)

    ![通知中樞的螢幕擷取畫面，顯示已成功建立 DocumentDB 帳戶並部署到資源群組 - 線上資料庫建立者通知](media/documentdb-create-dbaccount/ca6.png)

5.  建立 DocumentDB 帳戶之後，即可在線上入口網站中使用預設設定。 請注意，DocumentDB 帳戶的預設一致性會設定為 [工作階段]****。 您可以藉由按一下 [DocumentDB 帳戶]**** 刀鋒視窗上的 [預設一致性]**** 磚，調整預設一致性設定。

    ![[資源群組]  刀鋒視窗的螢幕擷取畫面 - 開始進行應用程式開發](media/documentdb-create-dbaccount/ca7.png)


[how to: create a documentdb account]: #Howto 
[next steps]: #NextSteps 
[documentdb-manage]: ../articles/documentdb/documentdb-manage.md 

