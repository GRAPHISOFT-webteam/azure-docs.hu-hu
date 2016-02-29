<properties
   pageTitle="搭配使用 Azure 串流分析與 SQL 資料倉儲 | Microsoft Azure"
   description="搭配使用 Azure 串流分析與 SQL 資料倉儲以便開發解決方案的秘訣。"
   services="sql-data-warehouse"
   documentationCenter="NA"
   authors="sahaj08"
   manager="barbkess"
   editor=""/>

<tags
   ms.service="sql-data-warehouse"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="data-services"
   ms.date="09/30/2015"
   ms.author="sahajs;twounder"/>

# 搭配使用 Azure 串流分析與 SQL 資料倉儲

Azure 串流分析是完全受管理的服務，可用來對雲端中的串流資料進行低延遲、高可用性、可延展的複雜事件處理。 您可以藉由讀取了基本概念 [Azure 串流分析簡介][]。 然後，您就可以了解如何利用串流分析建立端對端解決方案，依照下列 [開始使用 Azure 串流分析][] 教學課程。

在本文中，您將學習如何使用 Azure SQL 資料倉儲資料庫做為串流分析工作的輸出接收器。

## 先決條件

首先，請完成下列步驟 [開始使用 Azure 串流分析][] 教學課程。  

1. 建立事件中樞輸入
2. 設定並啟動事件產生器應用程式
3. 佈建資料流分析工作
4. 指定工作輸入和查詢

接著，建立 Azure SQL 資料倉儲資料庫

## 指定工作輸出：Azure SQL 資料倉儲資料庫

### 步驟 1

在資料流分析工作按一下 **輸出** 上方的頁面上，然後按一下 [從 **加入輸出**。

### 步驟 2

選取 SQL Database，然後按一下 [下一步]。

![][add-output]

### 步驟 3
在下一頁輸入下列值：

- *輸出別名*: 輸入此工作輸出的易記名稱。
- *訂閱*:
    - 如果 SQL 資料倉儲資料庫是在與此資料流分析工作相同的訂用帳戶中，則請選取 [使用目前訂用帳戶的 SQL Database]。
    - 如果您的資料庫是在不同的訂用帳戶中，請選取 [使用其他訂用帳戶的 SQL Database]。
- *資料庫*: 指定目的地資料庫的名稱。
- *伺服器名稱*: 指定您指定的資料庫伺服器名稱。 您可以使用 Azure 傳統入口網站進行搜尋。

![][server-name]

- *使用者名稱*: 指定擁有資料庫的寫入權限的帳戶的使用者名稱。
- *密碼*: 提供指定的使用者帳戶的密碼。
- *資料表*: 指定資料庫中的目標資料表的名稱。

![][add-database] 

### 步驟 4

按一下核取按鈕以新增此工作輸出，並確認串流分析可成功連接到資料庫。

![][test-connection]

成功連接到資料庫時，您將會在入口網站的底部看到通知。 您可以按一下底部的 [測試連線]，以測試資料庫的連線。

## 後續步驟

如需整合的概觀，請參閱 [SQL 資料倉儲整合概觀][]。

如需更多開發秘訣，請參閱 [SQL 資料倉儲開發概觀][]。

<!--Image references-->

[add-output]: ./media/sql-data-warehouse-integrate-azure-stream-analytics/add-output.png
[server-name]: ./media/sql-data-warehouse-integrate-azure-stream-analytics/dw-server-name.png
[add-database]: ./media/sql-data-warehouse-integrate-azure-stream-analytics/add-database.png
[test-connection]: ./media/sql-data-warehouse-integrate-azure-stream-analytics/test-connection.png

<!--Article references-->

[Introduction to Azure Stream Analytics]: stream-analytics-introductiond.md
[Get started using Azure Stream Analytics]: stream-analytics-get-started.md
[SQL Data Warehouse development overview]:  sql-data-warehouse-overview-develop.md
[SQL Data Warehouse integration overview]:  sql-data-warehouse-overview-integrate.md

<!--MSDN references-->

<!--Other Web references-->
[Azure Stream Analytics documentation]: http://azure.microsoft.com/documentation/services/stream-analytics/


