<properties 
    pageTitle="DocumentDB Java SDK | Microsoft Azure" 
    description="了解所有 Java SDK 相關資訊，包括 發行日期、停用日期及 DocumentDB Java SDK 每個版本之間的變更。" 
    services="documentdb" 
    documentationCenter="java" 
    authors="ryancrawcour" 
    manager="jhubbard" 
    editor="cgronlun"/>

<tags 
    ms.service="documentdb" 
    ms.workload="data-services" 
    ms.tgt_pltfrm="na" 
    ms.devlang="java" 
    ms.topic="article" 
    ms.date="12/07/2015" 
    ms.author="ryancraw"/>

# DocumentDB SDK

> [AZURE.SELECTOR]
- [.NET SDK](documentdb-sdk-dotnet.md)
- [Node.js SDK](documentdb-sdk-node.md)
- [Java SDK](documentdb-sdk-java.md)
- [Python SDK](documentdb-sdk-python.md)

##DocumentDB Java SDK

<table>
<tr><td>**下載**</td><td>[Maven](http://mvnrepository.com/artifact/com.microsoft.azure/azure-documentdb)</td></tr>
<tr><td>**參與**</td><td>[GitHub](https://github.com/Azure/azure-documentdb-java/)</td></tr>
<tr><td>**文件**</td><td>[Java SDK Reference Documentation](http://azure.github.io/azure-documentdb-java/)</td></tr>
<tr><td>**開始**</td><td>[Get started with the Java SDK](documentdb-java-application.md)</td></tr>
<tr><td>**目前支援的執行階段**</td><td>[JDK 7](http://www.oracle.com/technetwork/java/javase/downloads/jdk7-downloads-1880260.html)</td></tr>
</table></br>

## 版本資訊

### <a name="1.5.0"/>[1.5.0](http://mvnrepository.com/artifact/com.microsoft.azure/azure-documentdb/1.5.0)
- 新增「雜湊和範圍」分割區解析程式來協助將應用程式跨多個分割區分區。

### <a name="1.4.0"/>[1.4.0](http://mvnrepository.com/artifact/com.microsoft.azure/azure-documentdb/1.4.0)
- 實作 Upsert。 已新增新的 upsertXXX 方法以支援 Upsert 功能。
- 實作以識別碼為基礎的路由。 不需變更公用 API，所有變更皆為內部變更。

### <a name="1.3.0"/>1.3.0
- 已略過版本以配合其他 SDK 的版本號碼

### <a name="1.2.0"/>[1.2.0](http://mvnrepository.com/artifact/com.microsoft.azure/azure-documentdb/1.2.0)
- 支援地理空間索引
- 驗證所有資源的識別碼屬性。 資源的識別碼不能包含 ?、/、#、\ 等字元，或在結尾處使用空格。
- 將新標頭「索引轉換進度」加至 ResourceResponse。

### <a name="1.1.0"/>[1.1.0](http://mvnrepository.com/artifact/com.microsoft.azure/azure-documentdb/1.1.0)
- 實作 V2 索引原則

### <a name="1.0.0"/>[1.0.0](http://mvnrepository.com/artifact/com.microsoft.azure/azure-documentdb/1.0.0)
- GA SDK

## 發行和停用日期
Microsoft 提供通知的最少 **12 個月** 之前淘汰 SDK 從容地轉換到較新/支援的版本。

新功能和功能與最佳化只能加入至目前的 SDK，因此它會建議您一律升級為最新 SDK 版本早越好。 

使用已停用之 SDK 的任何 DocumentDB 要求都將被服務拒絕。

> [AZURE.WARNING]
之前的版本中的所有版本的 Azure DocumentDB SDK for Java **1.0.0** 將淘汰上 **2016 年 2 月 29 日**。 

<br/>

| 版本 | 發行日期 | 停用日期 
| ---     | ---          | ---
| [1.5.0](#1.5.0) | 2015 年 12 月 4日日 |---
| [1.4.0](#1.4.0) | 2015 年 10 月 5日日 |---
| [1.3.0](#1.3.0) | 2015 年 10 月 5日日 |---
| [1.2.0](#1.2.0) | 2015 年 8 月 5日日 |---
| [1.1.0](#1.1.0) | 2015 年 7 月 09 |---
| [1.0.1](#1.0.1) | 2015 年 5 月 12 日 |---
| [1.0.0](#1.0.0) | 2015 年 4 月 7日日 |---
| 0.9.5-prelease | 2015 年 3 月 09 | 2016 年 2 月 29日日
| 0.9.4-prelease | 2015 年 2 月 17日日 | 2016 年 2 月 29日日
| 0.9.3-prelease | 2015 年 1 月 13日日 | 2016 年 2 月 29日日
| 0.9.2-prelease | 2014 年 12 月 19日日 | 2016 年 2 月 29日日
| 0.9.1-prelease | 2014 年 12 月 19日日 | 2016 年 2 月 29日日
| 0.9.0-prelease | 2014 年 12 月 10日日 | 2016 年 2 月 29日日

## 常見問題集
[AZURE.INCLUDE [documentdb-sdk-faq](../../includes/documentdb-sdk-faq.md)]

## 另請參閱

若要深入了解 DocumentDB，請參閱 [Microsoft Azure DocumentDB](https://azure.microsoft.com/services/documentdb/) 服務頁面。 

