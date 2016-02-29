<properties
    pageTitle="利用 PHP 重試邏輯連接到 SQL Database | Microsoft Azure"
    description="提供可從具有暫時性錯誤處理的 Windows 用戶端連接到 Azure SQL Database 的範例 PHP 程式，並提供用戶端所需之必要軟體元件的連結。"
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


# 在具有暫時性錯誤處理的 Windows 上使用 PHP 連接到 SQL Database


[AZURE.INCLUDE [sql-database-develop-includes-selector-language-platform-depth](../../includes/sql-database-develop-includes-selector-language-platform-depth.md)]


本主題說明如何從以 PHP 撰寫並在 Windows 上執行的用戶端應用程式，連接到 Azure SQL Database。


[AZURE.INCLUDE [sql-database-develop-includes-prerequisites-php-windows](../../includes/sql-database-develop-includes-prerequisites-php-windows.md)]

### SQL Database

請參閱 [入門頁面](sql-database-get-started.md) 以了解如何建立範例資料庫。  請務必遵循該指南以建立 **AdventureWorks 資料庫範本**。 只有如下所示的範例適用於 **AdventureWorks 結構描述**。


## 步驟 1：取得連線詳細資料

[AZURE.INCLUDE [sql-database-include-connection-string-details-20-portalshots](../../includes/sql-database-include-connection-string-details-20-portalshots.md)]

## 步驟 2: 連接並查詢

示範程式經過設計，會使嘗試連接期間的暫時性錯誤導致重試。 但是，查詢命令期間的暫時性錯誤會導致程式捨棄連線並建立新的連線，然後再重試查詢命令。 我們不建議也不反對這種設計選擇。 示範程式會說明一些您可使用的設計彈性。

<br>程式碼範例中大部分的內容都是攔截例外狀況的邏輯。 此 Program.cs 檔案的簡短版本可這 [這裡](sql-database-develop-php-simple-windows.md)。
<br>Main 方法在 Program.cs 中。 呼叫堆疊的執行，如下所示:
* Main 呼叫 ConnectAndQuery。
* ConnectAndQuery 呼叫 EstablishConnection。
* EstablishConnection 呼叫 IssueQueryCommand。

 [Sqlsrv_query ()](http://php.net/manual/en/function.sqlsrv-query.php) 函式可用來擷取對 SQL Database 設定從查詢的結果。 此函式基本上會接受任何查詢與連線物件，並傳回結果集，這可以藉由使用反覆 [sqlsrv_fetch_array ()](http://php.net/manual/en/function.sqlsrv-fetch-array.php)。

    <?php
        // Variables to tune the retry logic.  
        $connectionTimeoutSeconds = 30;  // Default of 15 seconds is too short over the Internet, sometimes.
        $maxCountTriesConnectAndQuery = 3;  // You can adjust the various retry count values.
        $secondsBetweenRetries = 4;  // Simple retry strategy.
        $errNo = 0;
        $serverName = "tcp:yourdatabase.database.windows.net,1433";
        $connectionOptions = array("Database"=>"AdventureWorks",
           "Uid"=>"yourusername", "PWD"=>"yourpassword", "LoginTimeout" => $connectionTimeoutSeconds);
        $conn;
        $errorArr = array();
        for ($cc = 1; $cc <= $maxCountTriesConnectAndQuery; $cc++)
        {
            $errorArr = array();
            $ctr = 0;
            // [A.2] Connect, which proceeds to issue a query command.
            $conn = sqlsrv_connect($serverName, $connectionOptions);  
            if( $conn == true)
            {
                echo "Connection was established";
                echo "<br>";

                $tsql = "SELECT [CompanyName] FROM SalesLT.Customer";
                $getProducts = sqlsrv_query($conn, $tsql);
                if ($getProducts == FALSE)
                    die(FormatErrors(sqlsrv_errors()));
                $productCount = 0;
                $ctr = 0;
                while($row = sqlsrv_fetch_array($getProducts, SQLSRV_FETCH_ASSOC))
                {   
                    $ctr++;
                    echo($row['CompanyName']);
                    echo("<br/>");
                    $productCount++;
                    if($ctr>10)
                        break;
                }
                sqlsrv_free_stmt($getProducts);
                break;
            }
            // Adds any the error codes from the SQL Exception to an array.
            else {  
                if( ($errors = sqlsrv_errors() ) != null) {
                    foreach( $errors as $error ) {
                        $errorArr[$ctr] = $error['code'];
                        $ctr = $ctr + 1;
                    }
                }
                $isTransientError = TRUE;
                // [A.4] Check whether sqlExc.Number is on the whitelist of transients.
                $isTransientError = IsTransientStatic($errorArr);
                if ($isTransientError == TRUE)  // Is a static persistent error...
                {
                    echo("Persistent error suffered, SqlException.Number==". $errorArr[0].". Program Will terminate.");
                    echo "<br>";
                    // [A.5] Either the connection attempt or the query command attempt suffered a persistent SqlException.
                    // Break the loop, let the hopeless program end.
                    exit(0);
                }
                // [A.6] The SqlException identified a transient error from an attempt to issue a query command.
                // So let this method reloop and try again. However, we recommend that the new query
                // attempt should start at the beginning and establish a new connection.
                if ($cc >= $maxCountTriesConnectAndQuery)
                {
                    echo "Transient errors suffered in too many retries - " . $cc ." Program will terminate.";
                    echo "<br>";
                    exit(0);
                }
                echo("Transient error encountered.  SqlException.Number==". $errorArr[0]. " . Program might retry by itself.");  
                echo "<br>";
                echo $cc . " Attempts so far. Might retry.";
                echo "<br>";
                // A very simple retry strategy, a brief pause before looping. This could be changed to exponential if you want.
                sleep(1*$secondsBetweenRetries);
            }
            // [A.3] All has gone well, so let the program end.
        }
        function IsTransientStatic($errorArr) {
            $arrayOfTransientErrorNumbers = array(4060, 10928, 10929, 40197, 40501, 40613);
            $result = array_intersect($arrayOfTransientErrorNumber, $errorArr);
            $count = count($result);
            if($count >= 0) //change to > 0 later.
                return TRUE;
        }
    ?>

## 後續步驟

如需 PHP 安裝和使用方式的詳細資訊，請參閱 [使用 PHP 存取 SQL Server Database](http://technet.microsoft.com/library/cc793139.aspx)。

