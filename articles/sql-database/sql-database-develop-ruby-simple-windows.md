<properties
    pageTitle="使用 Ruby 和 Windows 上的 TinyTDS 連接到 SQL Database"
    description="提供您可在 Windows 上執行以連接到 Azure SQL Database 的 Ruby 程式碼範例。"
    services="sql-database"
    documentationCenter=""
    authors="meet-bhagdev"
    manager="jeffreyg"
    editor=""/>


<tags
    ms.service="sql-database"
    ms.workload="sql-database"
    ms.tgt_pltfrm="na"
    ms.devlang="ruby"
    ms.topic="article"
    ms.date="12/08/2015"
    ms.author="mebha"/>



# 在 Windows 上使用 Ruby 連接到 SQL Database

[AZURE.INCLUDE [sql-database-develop-includes-selector-language-platform-depth](../../includes/sql-database-develop-includes-selector-language-platform-depth.md)]

本主題提供可在執行 Windows 8.1 的 Windows 電腦上執行，以連接到 Azure SQL Database 資料庫的 Ruby 程式碼範例。

## 必要條件

### 安裝必要的模組

開啟您的終端機，並安裝下列：

**1) Ruby：**如果您的電腦沒有 Ruby，請安裝。 若是新的 Ruby 使用者，我們建議您使用 Ruby 2.1.X 安裝程式。 這些安裝程式提供相容且更新的穩定語言和廣泛封裝清單 (gem)。 [移 Ruby 下載頁面](http://rubyinstaller.org/downloads/) 並且下載適當的 2.1.x 安裝程式。 舉例來說，如果您是使用 64 位元電腦，請下載 **Ruby 2.1.6 (x64)** 安裝程式。
<br/><br/>安裝程式下載後，執行下列作業:


- 按兩下檔案以啟動安裝程式。

- 選取您的語言，並同意接受這些條款。

- 在安裝設定畫面上，選取 [將 Ruby 可執行檔新增至您的路徑]** 和 [讓 .rb 和 .rbw 檔案與此 Ruby 安裝產生關聯]** 旁的核取方塊。


**2) DevKit:** 下載 DevKit 從 [RubyInstaller 頁面](http://rubyinstaller.org/downloads/)

下載完成之後，執行下列作業：


- 按兩下檔案。 系統會詢問您解壓縮檔案的位置。

- 按一下 "..." 按鈕，然後選取 "C:\DevKit"。 您可能必須藉由按一下 [新增資料夾]，先建立此資料夾。

- 按一下 [確定]，然後按一下 [解壓縮]，以解壓縮檔案。


現在開啟命令列提示字元並輸入下列命令：

    > chdir C:\DevKit
    > ruby dk.rb init
    > ruby dk.rb install

您現在有功能完整的 Ruby 和 RubyGems！


**3) TinyTDS：**瀏覽至 C:\DevKit 並且從您的終端機執行下列命令： 這會在您的電腦上安裝 TinyTDS。

    gem inst tiny_tds --pre

### SQL Database

請參閱 [入門頁面](sql-database-get-started.md) 以了解如何建立範例資料庫。 請務必遵循該指南以建立 **AdventureWorks 資料庫範本**。 以下所示的範例僅適用於 **AdventureWorks 結構描述**。


## 步驟 1：取得連線詳細資料

[AZURE.INCLUDE [sql-database-include-connection-string-details-20-portalshots](../../includes/sql-database-include-connection-string-details-20-portalshots.md)]

## 步驟 2: 連接

[Tinytds:: Client](https://github.com/rails-sqlserver/tiny_tds) 函式用來連接到 SQL 資料庫。

    require 'tiny_tds'
    client = TinyTds::Client.new username: 'yourusername@yourserver', password: 'yourpassword',
    host: 'yourserver.database.windows.net', port: 1433,
    database: 'AdventureWorks', azure:true

## 步驟 3: 執行查詢

複製以下程式碼並貼到空白檔案中。 稱它為 test.rb。 然後執行它，方法是從命令提示字元輸入下列命令：

    ruby test.rb

在程式碼範例中， [tinytds:: Result](https://github.com/rails-sqlserver/tiny_tds) 函數用來擷取對 SQL Database 設定從查詢的結果。 此函數會接受查詢，並傳回結果集。 結果集可使用 [result.each do |row|](https://github.com/rails-sqlserver/tiny_tds).

    require 'tiny_tds'  
    print 'test'     
    client = TinyTds::Client.new username: 'yourusername@yourserver', password: 'yourpassword',
    host: 'yourserver.database.windows.net', port: 1433,
    database: 'AdventureWorks', azure:true
    results = client.execute("SELECT c.CustomerID, c.CompanyName,COUNT(soh.SalesOrderID) AS OrderCount FROM SalesLT.Customer AS c LEFT OUTER JOIN SalesLT.SalesOrderHeader AS soh ON c.CustomerID = soh.CustomerID GROUP BY c.CustomerID, c.CompanyName ORDER BY OrderCount DESC")
    results.each do |row|
    puts row
    end

## 步驟 4: 插入資料列

在您將了解如何執行此範例 [插入](https://msdn.microsoft.com/library/ms174335.aspx) 陳述式，將保護您的應用程式的參數傳遞 [SQL 資料隱碼](https://technet.microsoft.com/library/ms161953(v=sql.105).aspx) 弱點，以及擷取自動產生 [主索引鍵](https://msdn.microsoft.com/library/ms179610.aspx) 值。

若要搭配使用 TinyTDS 和 Azure，建議您執行多個 `設定` 陳述式來變更目前工作階段處理特定資訊的方式。 建議 `設定` 陳述式所提供的程式碼範例。 例如， `SET ANSI_NULL_DFLT_ON` 可讓新的資料行允許 null 值，即使未明確指定資料行的 null 屬性狀態建立。

若要配合 Microsoft SQL Server [datetime](http://msdn.microsoft.com/library/ms187819.aspx) 格式，請使用 [strftime](http://ruby-doc.org/core-2.2.0/Time.html#method-i-strftime) 函數轉換成相對應的日期時間格式。

    require 'tiny_tds'
    client = TinyTds::Client.new username: 'yourusername@yourserver', password: 'yourpassword',
    host: 'yourserver.database.windows.net', port: 1433,
    database: 'AdventureWorks', azure:true
    results = client.execute("SET ANSI_NULLS ON")
    results = client.execute("SET CURSOR_CLOSE_ON_COMMIT OFF")
    results = client.execute("SET ANSI_NULL_DFLT_ON ON")
    results = client.execute("SET IMPLICIT_TRANSACTIONS OFF")
    results = client.execute("SET ANSI_PADDING ON")
    results = client.execute("SET QUOTED_IDENTIFIER ON")
    results = client.execute("SET ANSI_WARNINGS ON")
    results = client.execute("SET CONCAT_NULL_YIELDS_NULL ON")
    require 'date'
    t = Time.now
    curr_date = t.strftime("%Y-%m-%d %H:%M:%S.%L")
    results = client.execute("INSERT SalesLT.Product (Name, ProductNumber, StandardCost, ListPrice, SellStartDate)
    OUTPUT INSERTED.ProductID VALUES ('SQL Server Express New', 'SQLEXPRESS New', 0, 0, '#{curr_date}' )")
    results.each do |row|
    puts row
    end



