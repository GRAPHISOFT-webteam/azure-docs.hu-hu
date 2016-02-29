<properties
    pageTitle="在 Ubuntu Linux 上搭配 Tedious 使用 Node.js 連接到 SQL Database"
    description="提供可用來連接到 Azure SQL Database 的 Node.js 程式碼範例。 這個範例使用 Tedious 驅動程式進行連接。"
    services="sql-database"
    documentationCenter=""
    authors="meet-bhagdev"
    manager="jeffreyg"
    editor=""/>


<tags
    ms.service="sql-database"
    ms.workload="data-management"
    ms.tgt_pltfrm="na"
    ms.devlang="nodejs"
    ms.topic="article"
    ms.date="12/08/2015"
    ms.author="meetb"/>


# 在 Ubuntu Linux 上搭配 Tedious 使用 Node.js 連接到 SQL Database


[AZURE.INCLUDE [sql-database-develop-includes-selector-language-platform-depth](../../includes/sql-database-develop-includes-selector-language-platform-depth.md)]


本主題提供可在 Ubuntu Linux 上執行的 Node.js 程式碼範例。 這個範例使用 Tedious 驅動程式連接到 Azure SQL Database。


## 先決條件


開啟您的終端機並安裝 **節點** 和 **npm**, ，除非您已經安裝在電腦上。


    sudo apt-get install node
    sudo apt-get install npm


您的電腦設定為使用之後 **節點** 和 **npm**, 、 瀏覽至目錄，您打算建立 Node.js 專案，並輸入下列命令。


    sudo npm init
    sudo npm install tedious


**npm init** 建立節點專案。 若要在專案建立期間保留預設值，請按 Enter 鍵直到專案建立為止。 現在您會看到 **package.json** 專案目錄中的檔案。


### SQL Database

請參閱 [入門頁面](sql-database-get-started.md) 以了解如何建立範例資料庫。  請務必遵循該指南以建立 **AdventureWorks 資料庫範本**。 只有如下所示的範例適用於 **AdventureWorks 結構描述**。

## 步驟 1：取得連線詳細資料

[AZURE.INCLUDE [sql-database-include-connection-string-details-20-portalshots](../../includes/sql-database-include-connection-string-details-20-portalshots.md)]

## 步驟 2：連接

 [新連線](http://pekim.github.io/tedious/api-connection.html) 函式用來連接到 SQL 資料庫。

    var Connection = require('tedious').Connection;
    var config = {
        userName: 'yourusername',
        password: 'yourpassword',
        server: 'yourserver.database.windows.net',
        // If you are on Microsoft Azure, you need this:
        options: {encrypt: true, database: 'AdventureWorks'}
    };
    var connection = new Connection(config);
    connection.on('connect', function(err) {
    // If no error, then good to proceed.
        console.log("Connected");
    });


## 步驟 3: 執行查詢


所有 SQL 陳述式會都執行使用 [new request ()](http://pekim.github.io/tedious/api-request.html) 函式。 如果陳述式會傳回資料列，例如 select 陳述式，您可以擷取這些使用 [request.on ()](http://pekim.github.io/tedious/api-request.html) 函式。 如果沒有資料列， [request.on ()](http://pekim.github.io/tedious/api-request.html) 函式會傳回空白清單。


    var Connection = require('tedious').Connection;
    var config = {
        userName: 'yourusername',
        password: 'yourpassword',
        server: 'yourserver.database.windows.net',
        // When you connect to Azure SQL Database, you need these next options.
        options: {encrypt: true, database: 'AdventureWorks'}
    };
    var connection = new Connection(config);
    connection.on('connect', function(err) {
        // If no error, then good to proceed.
        console.log("Connected");
        executeStatement();
    });

    var Request = require('tedious').Request;
    var TYPES = require('tedious').TYPES;

    function executeStatement() {
        request = new Request("SELECT c.CustomerID, c.CompanyName,COUNT(soh.SalesOrderID) AS OrderCount FROM SalesLT.Customer AS c LEFT OUTER JOIN SalesLT.SalesOrderHeader AS soh ON c.CustomerID = soh.CustomerID GROUP BY c.CustomerID, c.CompanyName ORDER BY OrderCount DESC;", function(err) {
        if (err) {
            console.log(err);}
        });
        var result = "";
        request.on('row', function(columns) {
            columns.forEach(function(column) {
              if (column.value === null) {
                console.log('NULL');
              } else {
                result+= column.value + " ";
              }
            });
            console.log(result);
            result ="";
        });

        request.on('done', function(rowCount, more) {
        console.log(rowCount + ' rows returned');
        });
        connection.execSql(request);
    }


## 步驟 4：插入資料列

在您將了解如何執行此範例 [插入](https://msdn.microsoft.com/library/ms174335.aspx) 陳述式，將保護您的應用程式的參數傳遞 [SQL 資料隱碼](https://technet.microsoft.com/library/ms161953(v=sql.105).aspx) 弱點，以及擷取自動產生 [主索引鍵](https://msdn.microsoft.com/library/ms179610.aspx) 值。  


    var Connection = require('tedious').Connection;
    var config = {
        userName: 'yourusername',
        password: 'yourpassword',
        server: 'yourserver.database.windows.net',
        // If you are on Azure SQL Database, you need these next options.
        options: {encrypt: true, database: 'AdventureWorks'}
    };
    var connection = new Connection(config);
    connection.on('connect', function(err) {
        // If no error, then good to proceed.
        console.log("Connected");
        executeStatement1();
    });

    var Request = require('tedious').Request
    var TYPES = require('tedious').TYPES;

    function executeStatement1() {
        request = new Request("INSERT SalesLT.Product (Name, ProductNumber, StandardCost, ListPrice, SellStartDate) OUTPUT INSERTED.ProductID VALUES (@Name, @Number, @Cost, @Price, CURRENT_TIMESTAMP);", function(err) {
         if (err) {
            console.log(err);}
        });
        request.addParameter('Name', TYPES.NVarChar,'SQL Server Express 2014');
        request.addParameter('Number', TYPES.NVarChar , 'SQLEXPRESS2014');
        request.addParameter('Cost', TYPES.Int, 11);
        request.addParameter('Price', TYPES.Int,11);
        request.on('row', function(columns) {
            columns.forEach(function(column) {
              if (column.value === null) {
                console.log('NULL');
              } else {
                console.log("Product id of inserted item is " + column.value);
              }
            });
        });     
        connection.execSql(request);
    }


## 後續步驟

如需詳細資訊，請參閱 [Node.js 開發人員中心](/develop/nodejs/)。

