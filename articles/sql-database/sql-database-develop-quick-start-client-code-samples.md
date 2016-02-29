<properties 
    pageTitle="SQL Database 的用戶端快速入門程式碼範例 | Microsoft Azure" 
    description="提供適用於 Linux 上的 Node.js、Mac OS 上的 Python、Java 與 Windows、企業程式庫，以及 Azure SQL Database 用戶端的程式碼範例與驅動程式。"
    services="sql-database" 
    documentationCenter="" 
    authors="MightyPen" 
    manager="jeffreyg" 
    editor=""/>


<tags 
    ms.service="sql-database" 
    ms.workload="data-management" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="10/29/2015" 
    ms.author="genemi"/>


# SQL Database 的用戶端快速入門程式碼範例


本主題提供可用來連接到 Azure SQL Database 之快速入門程式碼範例的連結：


- 簡短範例連接和查詢。
- 重試範例連接和查詢，但如果遇到的錯誤分類為自動重試 [*暫時性錯誤*](sql-database-develop-error-messages.md#bkmk_connection_errors) (例如連接逾時)。


這些範例涵蓋：


- 各種程式設計語言。
- 您的用戶端程式可在其上執行的 Windows、Linux 及 Mac OS 作業系統。
- 可供下載任何必要連接驅動程式的連結。
- 簡短的快速入門程式碼範例。
- 較長的範例，其中包含以自動重試邏輯形式處理的暫時性錯誤。
- 程式碼範例，將關聯式結果集轉換成物件導向格式。


> [AZURE.NOTE] 其他語言的程式碼範例會準備，並將新增到本主題的連結。


## Linux 上的用戶端


本節提供在 Linux 上執行之用戶端程式的程式碼範例主題連結。


| 語言 | 簡短的範例 | 重試範例 | 關聯式到物件 |
| :-- | :-- | :-- | :-- |
| Node.js | [Tedious](sql-database-develop-nodejs-simple-linux.md) | . | . |
| Python | [FreeTDS、pymssql](sql-database-develop-python-simple-unbutu-linux.md) | . | . |
| Ruby | [FreeTDS、TinyTDS](sql-database-develop-ruby-simple-linux.md) | . | . |


## Mac OS 上的用戶端


本節提供在 Mac OS 上執行之用戶端程式的程式碼範例主題連結。


| 語言 | 簡短的範例 | 重試範例 | 關聯式到物件 |
| :-- | :-- | :-- | :-- |
| Python | [pymssql](sql-database-develop-python-simple-mac-osx.md) | . | . |
| Ruby | [Homebrew<br/>FreeTDS、TinyTDS](sql-database-develop-ruby-simple-mac-osx.md) | . | . |


## Windows 上的用戶端


本節提供在 Windows 上執行之用戶端程式的程式碼範例主題連結。


| 語言 | 簡短的範例 | 重試範例 | 關聯式到物件 |
| :-- | :-- | :-- | :-- |
| C# | [ADO.NET](sql-database-develop-dotnet-simple.md) | [ADO.NET 自訂](sql-database-develop-csharp-retry-windows.md)<br/><br/>[企業程式庫](sql-database-develop-entlib-csharp-retry-windows.md) | Entity Framework |
| C++ | [ODBC 驅動程式](http://msdn.microsoft.com/library/azure/hh974312.aspx) | . | . |
| Java | [Java 撰寫的。 JDBC、JDK。 Insert、 Transaction、 Select。](sql-database-develop-java-simple-windows.md) | . | . |
| Node.js | [msnodesql](sql-database-develop-nodejs-simple-windows.md) | . | . |
| PHP | [ODBC](sql-database-develop-php-simple-windows.md) | [ODBC 自訂](sql-database-develop-php-retry-windows.md) | . |
| Python | [pymssql](sql-database-develop-python-simple-windows.md) | . | . |


## 另請參閱


- [適用於多種語言和平台的 SDK 與工具的下載項目](http://azure.microsoft.com/downloads/#cmd-line-tools)
- [SQL Database 和 SQL Server 的連線庫](sql-database-libraries.md)
- [暫時性錯誤的數字代碼的清單](sql-database-develop-error-messages.md#bkmk_connection_errors)<br/>(& s) nbsp;
- [Azure SQL Database 開發：使用說明主題](http://msdn.microsoft.com/library/azure/ee621787.aspx)
- [連接到 SQL Database：連結、最佳作法和設計方針](sql-database-connect-central-recommendations.md)
- [建立您的第一個 Azure SQL Database](sql-database-get-started.md)
- [Entity Framework 6 在此處，EF 7 在 GitHub 上](http://entityframework.codeplex.com/)


