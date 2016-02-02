<properties
    pageTitle="在 Mac OS X (Yosemite) 上搭配 TinyTDS 使用 Ruby 連接到 SQL Database"
    description="提供您可在 Mac OS X (Yosemite) 上執行以連接到 Azure SQL Database 的 Ruby 程式碼範例。"
    services="sql-database"
    documentationCenter=""
    authors="ajlam"
    manager="jeffreyg"
    editor=""/>


<tags
    ms.service="sql-database"
    ms.workload="sql-database"
    ms.tgt_pltfrm="na"
    ms.devlang="ruby"
    ms.topic="article"
    ms.date="12/08/2015"
    ms.author="andrela"/>



# 在 Mac OS X (Yosemite) 上使用 Ruby 連接到 SQL Database

[AZURE.INCLUDE [sql-database-develop-includes-selector-language-platform-depth](../../includes/sql-database-develop-includes-selector-language-platform-depth.md)]

本主題提供可在執行 Yosemite 的 Mac 電腦上執行，以連接到 Azure SQL Database 資料庫的 Ruby 程式碼範例。

## 必要條件

### 安裝必要的模組

開啟您的終端機，並安裝下列：

**1) Homebrew**：從您的終端機執行下列命令： 這會在您的電腦上下載 Homebrew 封裝管理員。

    ruby -e "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/master/install)"

**2) FreeTDS：**從您的終端機執行下列命令。 這會在您的電腦上安裝 FreeTDS，TinyTDS 才能運作。

    brew install FreeTDS

**3) TinyTDS：**從您的終端機執行下列命令。 這會在您的電腦上安裝 TinyTDS。

    gem install tiny_tds

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

[Tinytds:: Result](https://github.com/rails-sqlserver/tiny_tds) 函數用來擷取對 SQL Database 設定從查詢的結果。 此函數會接受查詢，並傳回結果集。 結果集可使用 [result.each do |row|](https://github.com/rails-sqlserver/tiny_tds).

    require 'tiny_tds'  
    print 'test'     
    client = TinyTds::Client.new username: 'yourusername@yourserver', password: 'yourpassword',
    host: 'yourserver.database.windows.net', port: 1433,
    database: 'AdventureWorks', azure:true
    results = client.execute("select * from SalesLT.Product")
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



