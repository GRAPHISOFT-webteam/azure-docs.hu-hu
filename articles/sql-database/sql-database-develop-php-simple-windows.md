<properties
    pageTitle="在 Windows 上使用 PHP 連接到 SQL Database"
    description="提供可從 Windows 用戶端連接到 Azure SQL Database 的範例 PHP 程式，並提供用戶端所需之必要軟體元件的連結。"
    services="sql-database"
    documentationCenter=""
    authors="meet-bhagdev"
    manager="jeffreyg"
    editor=""/>


<tags
    ms.service="sql-database"
    ms.workload="data-management"
    ms.tgt_pltfrm="na"
    ms.devlang="php"
    ms.topic="article"
    ms.date="12/08/2015"
    ms.author="meetb"/>


# 在 Windows 上使用 PHP 連接到 SQL Database


[AZURE.INCLUDE [sql-database-develop-includes-selector-language-platform-depth](../../includes/sql-database-develop-includes-selector-language-platform-depth.md)]


本主題說明如何從以 PHP 撰寫並在 Windows 上執行的用戶端應用程式，連接到 Azure SQL Database。


[AZURE.INCLUDE [sql-database-develop-includes-prerequisites-php-windows](../../includes/sql-database-develop-includes-prerequisites-php-windows.md)]

### SQL Database

請參閱 [入門頁面](sql-database-get-started.md) 以了解如何建立範例資料庫。  請務必遵循該指南以建立 **AdventureWorks 資料庫範本**。 只有如下所示的範例適用於 **AdventureWorks 結構描述**。


## 步驟 1：取得連線詳細資料

[AZURE.INCLUDE [sql-database-include-connection-string-details-20-portalshots](../../includes/sql-database-include-connection-string-details-20-portalshots.md)]


## 步驟 2 ︰ 連接


這 **OpenConnection** 函式會在所有後續函式的頂端附近呼叫。


    function OpenConnection()
    {
        try
        {
            $serverName = "tcp:myserver.database.windows.net,1433";
            $connectionOptions = array("Database"=>"AdventureWorks",
                "Uid"=>"MyUser", "PWD"=>"MyPassword");
            $conn = sqlsrv_connect($serverName, $connectionOptions);
            if($conn == false)
                die(FormatErrors(sqlsrv_errors()));
        }
        catch(Exception $e)
        {
            echo("Error!");
        }
    }


## 步驟 3 ︰ 執行查詢

 [Sqlsrv_query （)](http://php.net/manual/en/function.sqlsrv-query.php) 函式可用來擷取對 SQL Database 設定從查詢的結果。 此函式基本上會接受任何查詢與連線物件，並傳回結果集，這可以藉由使用反覆 [sqlsrv_fetch_array （)](http://php.net/manual/en/function.sqlsrv-fetch-array.php)。

    function ReadData()
    {
        try
        {
            $conn = OpenConnection();
            $tsql = "SELECT [CompanyName] FROM SalesLT.Customer";
            $getProducts = sqlsrv_query($conn, $tsql);
            if ($getProducts == FALSE)
                die(FormatErrors(sqlsrv_errors()));
            $productCount = 0;
            while($row = sqlsrv_fetch_array($getProducts, SQLSRV_FETCH_ASSOC))
            {
                echo($row['CompanyName']);
                echo("<br/>");
                $productCount++;
            }
            sqlsrv_free_stmt($getProducts);
            sqlsrv_close($conn);
        }
        catch(Exception $e)
        {
            echo("Error!");
        }
    }


## 步驟 4 ︰ 插入資料列

在您將了解如何執行此範例 [插入](https://msdn.microsoft.com/library/ms174335.aspx) 陳述式，將保護您的應用程式的參數傳遞 [SQL 資料隱碼](https://technet.microsoft.com/library/ms161953(v=sql.105).aspx) 弱點，以及擷取自動產生 [主索引鍵](https://msdn.microsoft.com/library/ms179610.aspx) 值。  


    function InsertData()
    {
        try
        {
            $conn = OpenConnection();

            $tsql = "INSERT SalesLT.Product (Name, ProductNumber, StandardCost, ListPrice, SellStartDate) OUTPUT            INSERTED.ProductID VALUES ('SQL Server 1', 'SQL Server 2', 0, 0, getdate())";
            //Insert query
            $insertReview = sqlsrv_query($conn, $tsql);
            if($insertReview == FALSE)
                die(FormatErrors( sqlsrv_errors()));
            echo "Product Key inserted is :";
            while($row = sqlsrv_fetch_array($insertReview, SQLSRV_FETCH_ASSOC))
            {   
                echo($row['ProductID']);
            }
            sqlsrv_free_stmt($insertReview);
            sqlsrv_close($conn);
        }
        catch(Exception $e)
        {
            echo("Error!");
        }
    }

## 步驟 5 ︰ 回復交易


這個程式碼範例示範如何使用交易，您將：

-開始交易

-插入一列資料，更新另一列資料

-確認您的交易中插入與更新是否成功，如果其中一項沒有成功就復原交易


    function Transactions()
    {
        try
        {
            $conn = OpenConnection();

            if (sqlsrv_begin_transaction($conn) == FALSE)
                die(FormatErrors(sqlsrv_errors()));

            $tsql1 = "INSERT INTO SalesLT.SalesOrderDetail (SalesOrderID,OrderQty,ProductID,UnitPrice)
            VALUES (71774, 22, 709, 33)";
            $stmt1 = sqlsrv_query($conn, $tsql1);

            /* Set up and execute the second query. */
            $tsql2 = "UPDATE SalesLT.SalesOrderDetail SET OrderQty = (OrderQty + 1) WHERE ProductID = 709";
            $stmt2 = sqlsrv_query( $conn, $tsql2);

            /* If both queries were successful, commit the transaction. */
            /* Otherwise, rollback the transaction. */
            if($stmt1 && $stmt2)
            {
                   sqlsrv_commit($conn);
                   echo("Transaction was commited");
            }
            else
            {
                sqlsrv_rollback($conn);
                echo "Transaction was rolled back.\n";
            }
            /* Free statement and connection resources. */
            sqlsrv_free_stmt( $stmt1);
            sqlsrv_free_stmt( $stmt2);
        }
        catch(Exception $e)
        {
            echo("Error!");
        }
    }


## 後續步驟


如需 PHP 安裝和使用方式的詳細資訊，請參閱 [使用 PHP 存取 SQL Server Database](http://technet.microsoft.com/library/cc793139.aspx)。


