<properties 
    pageTitle="DocumentDB Python SDK | Microsoft Azure" 
    description="了解所有 Python SDK 相關資訊，包括 發行日期、停用日期及 DocumentDB Python SDK 每個版本之間的變更。" 
    services="documentdb" 
    documentationCenter="python" 
    authors="ryancrawcour" 
    manager="jhubbard" 
    editor="cgronlun"/>

<tags 
    ms.service="documentdb" 
    ms.workload="data-services" 
    ms.tgt_pltfrm="na" 
    ms.devlang="python" 
    ms.topic="article" 
    ms.date="11/16/2015" 
    ms.author="ryancraw"/>

# DocumentDB SDK

> [AZURE.SELECTOR]
- [.NET SDK](documentdb-sdk-dotnet.md)
- [Node.js SDK](documentdb-sdk-node.md)
- [Java SDK](documentdb-sdk-java.md)
- [Python SDK](documentdb-sdk-python.md)

##DocumentDB Python SDK

<table>
<tr><td>**下載**</td><td>[PyPI](https://pypi.python.org/pypi/pydocumentdb)</td></tr>
<tr><td>**參與**</td><td>[GitHub](https://github.com/Azure/azure-documentdb-python)</td></tr>
<tr><td>**文件**</td><td>[Python SDK Reference Documentation](http://azure.github.io/azure-documentdb-python/)</td></tr>
<tr><td>**開始**</td><td>[Get started with the Python SDK](documentdb-python-application.md)</td></tr>
<tr><td>**目前支援的平台**</td><td>[Python 2.7](https://www.python.org/download/releases/2.7/)</td></tr>
</table></br>

## 版本資訊

### <a name="1.4.2"/>[1.4.2](https://pypi.python.org/pypi/pydocumentdb/1.4.2)
- 實作 Upsert。 已新增新的 UpsertXXX 方法以支援 Upsert 功能。
- 實作以識別碼為基礎的路由。 不需變更公用 API，所有變更皆為內部變更。

### <a name="1.2.0"/>[1.2.0](https://pypi.python.org/pypi/pydocumentdb/1.2.0)
- 支援地理空間索引。
- 驗證所有資源的識別碼屬性。 資源的識別碼不能包含 ?、/、#、\ 等字元，或在結尾處使用空格。
- 將新標頭「索引轉換進度」加至 ResourceResponse。

### <a name="1.1.0"/>[1.1.0](https://pypi.python.org/pypi/pydocumentdb/1.1.0)
- 實作 V2 索引原則

### <a name="1.0.1"/>[1.0.1](https://pypi.python.org/pypi/pydocumentdb/1.0.1)
- 支援 Proxy 連線

### <a name="1.0.0"/>[1.0.0](https://pypi.python.org/pypi/pydocumentdb/1.0.0)
- GA SDK

## 發行和停用日期
Microsoft 提供通知的最少 **12 個月** 之前淘汰 SDK 從容地轉換到較新/支援的版本。

新功能和功能與最佳化只能加入至目前的 SDK，因此它會建議您一律升級為最新 SDK 版本早越好。 

使用已停用之 SDK 的任何 DocumentDB 要求都將被服務拒絕。

> [AZURE.WARNING]
所有版本的 Azure DocumentDB SDK for Python 之前的版本, **1.0.0** 將淘汰上 **2016 年 2 月 29 日**。 

<br/>

| 版本 | 發行日期 | 停用日期 
| ---     | ---          | ---
| [1.4.2](#1.4.2) | 2015 年 10 月 6日日 |---
| [1.4.1](#1.4.1) | 2015 年 10 月 6日日 |---
| [1.2.0](#1.2.0) | 2015 年 8 月 6日日 |---
| [1.1.0](#1.1.0) | 2015 年 7 月 09 |---
| [1.0.1](#1.0.1) | 2015 年 5 月 25 日 |---
| [1.0.0](#1.0.0) | 2015 年 4 月 7日日 |---
| 0.9.4-prelease | 2015 年 1 月 14日日 | 2016 年 2 月 29日日
| 0.9.3-prelease | 2014 年 12 月 09 | 2016 年 2 月 29日日
| 0.9.2-prelease | 2014 年 11 月 25日日 | 2016 年 2 月 29日日
| 0.9.1-prelease | 2014 年 9 月 23日日 | 2016 年 2 月 29日日
| 0.9.0-prelease | 2014 年 8 月 21 日 | 2016 年 2 月 29日日

## 常見問題集
[AZURE.INCLUDE [documentdb-sdk-faq](../../includes/documentdb-sdk-faq.md)]

## 另請參閱

若要深入了解 DocumentDB，請參閱 [Microsoft Azure DocumentDB](https://azure.microsoft.com/services/documentdb/) 服務頁面。 

