<properties 
    pageTitle="DocumentDB (NoSQL 資料庫) 上的 SQL 查詢 | Microsoft Azure" 
    description="了解如何使用 SQL 查詢陳述式來查詢 DocumentDB (NoSQL 資料庫)。 做為 JSON 查詢語言，SQL 查詢可以用於巨量資料分析。" 
    keywords="sql query, sql queries, sql syntax, json query language, database concepts and sql queries"
    services="documentdb" 
    documentationCenter="" 
    authors="arramac" 
    manager="jhubbard" 
    editor="monicar"/>

<tags 
    ms.service="documentdb" 
    ms.workload="data-services" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="12/14/2015" 
    ms.author="arramac"/>

# DocumentDB 中的 SQL 查詢
Microsoft Azure DocumentDB 支援使用 SQL (結構化查詢語言) 做為 JSON 查詢語言來查詢文件。 DocumentDB 確實是無結構描述。 由於它是直接在資料庫引擎內使用 JSON 資料模型，因此它提供不需明確的結構描述或建立次要索引，即可自動編製 JSON 文件索引的功能。 

在為 DocumentDB 設計查詢語言時，我們有兩個謹記的目標：

-   我們想要支援 SQL，而不是發明新的查詢語言。 SQL 是一種最熟悉且熱門的查詢語言。 DocumentDB SQL 提供一個正式的程式設計模型，可在 JSON 文件上進行豐富的查詢。
-   由於 JSON 文件資料庫可以直接在資料庫引擎中執行 JavaScript，因此，我們想要使用 JavaScript 的程式設計模型做為查詢語言的基礎。 DocumentDB SQL 是以 JavaScript 的類型系統、運算式評估和函數叫用為基礎。 這除了其他功能之外，還進而提供自然程式設計模型來進行關聯式投射、跨 JSON 文件的階層式導覽、自我聯結、空間查詢，以及叫用完全以 JavaScript 撰寫的使用者定義函式 (UDF)。 

我們相信這些功能的重點是減少應用程式與資料庫之間的摩擦，而且對開發人員的生產力而言十分重要。

我們建議從觀看下列影片中，在影片中 Aravind Ramachandran 顯示 DocumentDB 的查詢功能，並瀏覽入門我們 [查詢遊樂場](http://www.documentdb.com/sql/demo), ，其中您可以試試 DocumentDB 並對我們的資料集執行 SQL 查詢。

> [AZURE.VIDEO dataexposedqueryingdocumentdb]

接著再回到本文，我們將會從 SQL 查詢教學課程開始，帶領您演練一些簡易的 JSON 文件和 SQL 命令。

## 開始使用 DocumentDB 中的 SQL 命令
為了查看 DocumentDB SQL 如何運作，讓我們從一些簡單的 JSON 文件開始著手，然後對其逐步執行一些簡單查詢。 請考慮有關兩個家族的這兩份 JSON 文件。 請注意，使用 DocumentDB，我們不需要明確地建立任何結構描述或次要索引。 我們只需要將 JSON 文件插入到 DocumentDB 集合中，再接著進行查詢即可。 
這裡有 Andersen 一家的簡單 JSON 文件，包括這一家的父母、孩子 (以及寵物)、地址及註冊資訊。 這份文件包含字串、數字、布林值、陣列和巢狀屬性。 

**文件**  

    {
        "id": "AndersenFamily",
        "lastName": "Andersen",
        "parents": [
           { "firstName": "Thomas" },
           { "firstName": "Mary Kay"}
        ],
        "children": [
           {
               "firstName": "Henriette Thaulow", "gender": "female", "grade": 5,
               "pets": [{ "givenName": "Fluffy" }]
           }
        ],
        "address": { "state": "WA", "county": "King", "city": "seattle" },
        "creationDate": 1431620472,
        "isRegistered": true
    }


以下是第二份具有些微差異的文件：使用 `givenName` 和 `familyName` 來取代 `firstName` 和 `lastName`。

**文件**  

    {
        "id": "WakefieldFamily",
        "parents": [
            { "familyName": "Wakefield", "givenName": "Robin" },
            { "familyName": "Miller", "givenName": "Ben" }
        ],
        "children": [
            {
                "familyName": "Merriam", 
                "givenName": "Jesse", 
                "gender": "female", "grade": 1,
                "pets": [
                    { "givenName": "Goofy" },
                    { "givenName": "Shadow" }
                ]
            },
            { 
                "familyName": "Miller", 
                 "givenName": "Lisa", 
                 "gender": "female", 
                 "grade": 8 }
        ],
        "address": { "state": "NY", "county": "Manhattan", "city": "NY" },
        "creationDate": 1431620462,
        "isRegistered": false
    }



現在，讓我們嘗試對此資料執行一些查詢，以了解 DocumentDB SQL 的一些重要部分。 例如，下列查詢將會傳回 id 欄位符合 `AndersenFamily` 的文件。 因為它是 `SELECT *`，所以查詢的輸出是完整 JSON 文件：

**查詢**

    SELECT * 
    FROM Families f 
    WHERE f.id = "AndersenFamily"

**結果**

    [{
        "id": "AndersenFamily",
        "lastName": "Andersen",
        "parents": [
           { "firstName": "Thomas" },
           { "firstName": "Mary Kay"}
        ],
        "children": [
           {
               "firstName": "Henriette Thaulow", "gender": "female", "grade": 5,
               "pets": [{ "givenName": "Fluffy" }]
           }
        ],
        "address": { "state": "WA", "county": "King", "city": "seattle" },
        "creationDate": 1431620472,
        "isRegistered": true
    }]


現在，請考慮我們需要以不同形式來重新格式化 JSON 輸出的情況。 當地址所在城市的名稱與省/市的名稱相同時，此查詢會投射具有兩個所選欄位 (Name 和 City) 的新 JSON 物件。 在此情況下，"NY, NY" 相符。

**查詢**   

    SELECT {"Name":f.id, "City":f.address.city} AS Family 
    FROM Families f 
    WHERE f.address.city = f.address.state

**結果**

    [{
        "Family": {
            "Name": "WakefieldFamily", 
            "City": "NY"
        }
    }]


下一個查詢會傳回家族中所有指定小孩的名稱，該家族的 id 符合依據居住城市所排序的 `WakefieldFamily`。

**查詢**

    SELECT c.givenName 
    FROM Families f 
    JOIN c IN f.children 
    WHERE f.id = 'WakefieldFamily'
    ORDER BY f.address.city ASC

**結果**

    [
      { "givenName": "Jesse" }, 
      { "givenName": "Lisa"}
    ]


我們想要透過目前所看到的範例來指出 DocumentDB 查詢語言的一些重要部分：  
 
-   因為 DocumentDB SQL 處理 JSON 值，所以它處理樹狀形式的實體，而不是資料列和資料行。 因此，此語言可讓您參考樹狀目錄中任意深度的節點 (例如 `Node1.Node2.Node3…..Nodem`)，該節點與參考 `<table>.<column>` 之兩個部分參考的關聯式 SQL 類似。   
-   結構化查詢語言可處理無結構描述資料。 因此，需要動態繫結類型系統。 相同的運算式可能會對不同的文件產生不同的類型。 查詢的結果會是有效的 JSON 值，但不保證會是固定的結構描述。  
-   DocumentDB 只支援嚴謹的 JSON 文件。 這表示類型系統和運算式只能處理 JSON 類型。 請參閱 [JSON 規格](http://www.json.org/) 如需詳細資訊。  
-   DocumentDB 集合是 JSON 文件的無結構描述容器。 集合中文件內及跨文件之資料實體中的關係，會由內含項目以隱含方式擷取，而不是由主索引鍵和外部索引鍵關係擷取。 這是本文稍後討論之文件內聯結中值得指出的重要部分。

## DocumentDB 索引編製

在進入 DocumentDB SQL 語法之前，有必要先探索 DocumentDB 的索引設計。 

資料庫索引的目的是要在使用最少資源 (例如 CPU、輸入/輸出) 的情況下，以各種方式提供查詢，同時兼顧良好的輸送量和低延遲。 通常，選擇適用於資料庫查詢的正確索引需要經過相當的規劃和實驗。 此方式對資料不符合嚴謹結構描述的無結構描述資料庫而言是種挑戰，而且發展十分迅速。 

因此，我們在設計 DocumentDB 索引子系統時，設定了下列目標：

-   在不需要結構描述的情況下，對文件編製索引：索引子系統不需要任何結構描述資訊，或提出任何文件結構描述的相關假設。 

-   支援有效率、豐富階層式及關聯式查詢：索引可有效率地支援 DocumentDB 查詢語言，包括支援階層式和關聯式投射。

-   支援在面對持續大量寫入時進行一致查詢：在面對持續大量寫入時，針對具有一致查詢的高寫入輸送量工作負載，會以累加、有效率及線上方式更新索引。 一致的索引更新對於提供一致性層級的查詢十分重要，在此層級中是由使用者設定文件服務。

-   支援多重租用：由於是使用保留型模型進行跨租用戶的資源控管，因此會在為每一複本配置的系統資源 (CPU、記憶體、輸入/輸出) 預算內執行索引更新。 

-   儲存體效率：基於成本效益，索引的磁碟儲存體額外負荷有所限制且可預測。 這十分重要，因為 DocumentDB 允許開發人員在索引額外負荷與查詢效能之間做出成本取捨。  

請參閱 [DocumentDB 範例](https://github.com/Azure/azure-documentdb-net) msdn 的範例示範如何設定集合的索引編製原則。 現在，讓我們深入討論 DocumentDB SQL 語法。


## DocumentDB SQL 查詢的基本概念
根據 ANSI-SQL 標準，每個查詢都會包含 SELECT 子句以及選擇性的 FROM 和 WHERE 子句。 針對每個查詢，通常都會列舉 FROM 子句中的來源。 接著，會對來源套用 WHERE 子句中的篩選，以擷取 JSON 文件的子集。 最後，使用 SELECT 子句來投射選取清單中所要求的 JSON 值。
    
    SELECT [TOP <top_expression>] <select_list> 
    [FROM <from_specification>] 
    [WHERE <filter_condition>]
    [ORDER BY <sort_specification]    


## FROM 子句
除非稍後會在查詢中篩選或投射來源，否則 `FROM <from_specification>` 子句為選用子句。 此子句的目的是指定查詢必須操作的資料來源。 整個集合通常是來源，但是您可以改為指定集合的子集。 

類似 `SELECT * FROM Families` 的查詢指出整個 Families 集合是要列舉的來源。 可使用特殊識別碼 ROOT 來代表此集合，而不使用集合名稱。 
下列清單包含會針對每個查詢強制執行的規則：

- 集合可以進行別名處理，例如 `SELECT f.id FROM Families AS f` 或只是 `SELECT f.id FROM Families f`。 這裡 `f` 相當於 `Families`。 `AS` 是選擇性的關鍵字，別名的識別碼。

-   請注意，進行別名處理之後，無法繫結原始來源。 例如，`SELECT Families.id FROM Families f` 在語句構造上無效，因為無法再解析識別碼 "Families"。

-   所有需要參照的屬性都必須是完整的。 如果未遵循嚴謹的結構描述，則會強制執行以避免任何模糊的繫結。 因此，`SELECT id FROM Families f` 在語句構造上無效，因為不會繫結屬性 `id`。
    
### 子文件
來源也可以減少為更小的子集。 例如，若只要列舉每個文件中的樹狀子目錄，則子根目錄可能會變成來源 (如下列範例所示)。

**查詢**

    SELECT * 
    FROM Families.children

**結果**  

    [
      [
        {
            "firstName": "Henriette Thaulow",
            "gender": "female",
            "grade": 5,
            "pets": [
              {
                  "givenName": "Fluffy"
              }
            ]
        }
      ],
      [
        {
            "familyName": "Merriam",
            "givenName": "Jesse",
            "gender": "female",
            "grade": 1
        },
        {
            "familyName": "Miller",
            "givenName": "Lisa",
            "gender": "female",
            "grade": 8
        }
      ]
    ]

雖然上面的範例使用陣列做為來源，但是也可以使用物件做為來源 (如下列範例所示)。 任何可在來源中找到的有效 JSON 值 (非 undefined)，都會被考量納入查詢的結果中。 如果有些家族沒有 `address.state` 值，則會在查詢結果中予以排除。

**查詢**

    SELECT * 
    FROM Families.address.state

**結果**

    [
      "WA", 
      "NY"
    ]


## WHERE 子句
WHERE 子句 (**`WHERE <filter_condition>`**) 是選擇性的。 它會指定條件，而且來源所提供的 JSON 文件必須滿足這些條件才能併入為結果的一部分。 任何 JSON 文件都必須將指定的條件評估為 "true"，才能視為結果。 索引層使用 WHERE 子句，來判斷可為結果一部分的來源文件的絕對最小子集。 

下列查詢會要求包含名稱屬性且其值為 `AndersenFamily` 的文件。 沒有名稱屬性或值不符合 `AndersenFamily` 的任何其他文件都會予以排除。 

**查詢**

    SELECT f.address
    FROM Families f 
    WHERE f.id = "AndersenFamily"

**結果**

    [{
      "address": {
        "state": "WA", 
        "county": "King", 
        "city": "seattle"
      }
    }]


前一個範例已顯示簡單的相等查詢。 DocumentDB SQL 也支援各種純量運算式。 最常用的是二元和一元運算式。 來源 JSON 物件中的屬性參考也是有效的運算式。 

下列是目前支援的二元運算式，而且可以用於查詢中 (如下列範例所示)：  
<table>
<tr>
<td>算術</td> 
<td>+、-、*、/、%</td>
</tr>
<tr>
<td>位元</td>    
<td>|, &, ^, <<, >>>>> （填滿零的向右移位） </td>
</tr>
<tr>
<td>邏輯</td>
<td>AND、OR、NOT</td>
</tr>
<tr>
<td>比較</td> 
<td>=、 ！ =、 （& s) lt;，（& s) gt;，（& s) lt; =、 & gt; =、 <></td>
</tr>
<tr>
<td>String</td> 
<td>|| (串連)</td>
</tr>
</table>  

讓我們了解一些使用二元運算子的查詢。

    SELECT * 
    FROM Families.children[0] c
    WHERE c.grade % 2 = 1     -- matching grades == 5, 1
    
    SELECT * 
    FROM Families.children[0] c
    WHERE c.grade ^ 4 = 1    -- matching grades == 5
    
    SELECT *
    FROM Families.children[0] c
    WHERE c.grade >= 5     -- matching grades == 5


一元運算子 +、-、~ 及 NOT 也是支援的運算子，可在查詢內使用 (如下列範例所示)：

    SELECT *
    FROM Families.children[0] c
    WHERE NOT(c.grade = 5)  -- matching grades == 1
    
    SELECT *
    FROM Families.children[0] c
    WHERE (-c.grade = -5)  -- matching grades == 5



除了二元和一元運算子之外，還允許屬性參照。 例如，`SELECT * FROM Families f WHERE f.isRegistered` 所傳回的 JSON 文件包含屬性 `isRegistered` 且屬性值等於 JSON `true` 值。 任何其他值 (false、null、Undefined、`<number>`、`<string>`、`<object>`、`<array>` 等) 則會導致從結果中排除來源文件。 

### 相等和比較運算子
下表顯示 DocumentDB SQL 中任何兩個 JSON 類型之間的相等比較結果。
<table style = "width:300px">
   <tbody>
      <tr>
         <td valign="top">
            <strong>Op</strong>
         </td>
         <td valign="top">
            <strong>未定義</strong>
         </td>
         <td valign="top">
            <strong>Null</strong>
         </td>
         <td valign="top">
            <strong>布林值</strong>
         </td>
         <td valign="top">
            <strong>數字</strong>
         </td>
         <td valign="top">
            <strong>字串</strong>
         </td>
         <td valign="top">
            <strong>物件</strong>
         </td>
         <td valign="top">
            <strong>陣列</strong>
         </td>
      </tr>
      <tr>
         <td valign="top">
            <strong>未定義<strong>
         </td>
         <td valign="top">
            Undefined
         </td>
         <td valign="top">
            Undefined
         </td>
         <td valign="top">
            Undefined
         </td>
         <td valign="top">
            Undefined
         </td>
         <td valign="top">
            Undefined
         </td>
         <td valign="top">
            Undefined
         </td>
         <td valign="top">
            Undefined
         </td>
      </tr>
      <tr>
         <td valign="top">
            <strong>Null<strong>
         </td>
         <td valign="top">
            Undefined
         </td>
         <td valign="top">
            <strong>[確定]</strong>
         </td>
         <td valign="top">
            Undefined
         </td>
         <td valign="top">
            Undefined
         </td>
         <td valign="top">
            Undefined
         </td>
         <td valign="top">
            Undefined
         </td>
         <td valign="top">
            Undefined
         </td>
      </tr>
      <tr>
         <td valign="top">
            <strong>布林值<strong>
         </td>
         <td valign="top">
            Undefined
         </td>
         <td valign="top">
            Undefined
         </td>
         <td valign="top">
            <strong>[確定]</strong>
         </td>
         <td valign="top">
            Undefined
         </td>
         <td valign="top">
            Undefined
         </td>
         <td valign="top">
            Undefined
         </td>
         <td valign="top">
            Undefined
         </td>
      </tr>
      <tr>
         <td valign="top">
            <strong>數字<strong>
         </td>
         <td valign="top">
            Undefined
         </td>
         <td valign="top">
            Undefined
         </td>
         <td valign="top">
            Undefined
         </td>
         <td valign="top">
            <strong>[確定]</strong>
         </td>
         <td valign="top">
            Undefined
         </td>
         <td valign="top">
            Undefined
         </td>
         <td valign="top">
            Undefined
         </td>
      </tr>
      <tr>
         <td valign="top">
            <strong>字串<strong>
         </td>
         <td valign="top">
            Undefined
         </td>
         <td valign="top">
            Undefined
         </td>
         <td valign="top">
            Undefined
         </td>
         <td valign="top">
            Undefined
         </td>
         <td valign="top">
            <strong>[確定]</strong>
         </td>
         <td valign="top">
            Undefined
         </td>
         <td valign="top">
            Undefined
         </td>
      </tr>
      <tr>
         <td valign="top">
            <strong>物件<strong>
         </td>
         <td valign="top">
            Undefined
         </td>
         <td valign="top">
            Undefined
         </td>
         <td valign="top">
            Undefined
         </td>
         <td valign="top">
            Undefined
         </td>
         <td valign="top">
            Undefined
         </td>
         <td valign="top">
            <strong>[確定]</strong>
         </td>
         <td valign="top">
            Undefined
         </td>
      </tr>
      <tr>
         <td valign="top">
            <strong>陣列<strong>
         </td>
         <td valign="top">
            Undefined
         </td>
         <td valign="top">
            Undefined
         </td>
         <td valign="top">
            Undefined
         </td>
         <td valign="top">
            Undefined
         </td>
         <td valign="top">
            Undefined
         </td>
         <td valign="top">
            Undefined
         </td>
         <td valign="top">
            <strong>[確定]</strong>
         </td>
      </tr>
   </tbody>
</table>

其他比較運算子 (例如 >、>=、!=、< 及 <=) 則適用下列規則：   

-   不同類型的比較會導致 Undefined。
-   兩個物件或兩個陣列之間的比較會導致 Undefined。   

如果篩選中純量運算式的結果是 Undefined，則不會將對應的文件併入結果中，因為 Undefined 邏輯上不會等於 "true"。

### BETWEEN 關鍵字
您也可以使用 BETWEEN 關鍵字來表示像對 ANSI SQL 中的值範圍執行查詢。 BETWEEN 可用於字串或數字。

例如，此查詢會傳回其長子的成績介於 1-5 (兩者皆含) 之間的所有家族文件。 

    SELECT *
    FROM Families.children[0] c
    WHERE c.grade BETWEEN 1 AND 5

不同於 ANSI SQL，您也可以在 FROM 子句中使用 BETWEEN 子句，如下列範例所示 。

    SELECT (c.grade BETWEEN 0 AND 10)
    FROM Families.children[0] c

如需更快速的查詢執行時間，請記得針對經過 BETWEEN 子句篩選的任何數值屬性/路徑，建立使用範圍索引類型的檢索原則。 

在 DocumentDB 和 ANSI SQL 中使用 BETWEEN 的主要差異在於，您可以表示針對混合類型屬性的範圍查詢，比方說，在某些文件中 「 成績 」 可能是數字 (5) ，而在其他文件中可能會是字串 ("grade4")。 在這些情況下 (像在 JavaScript 中)，這兩種不同類型之間的比較會產生 「 未定義 」，並略過文件。

### 邏輯 (AND、OR 和 NOT) 運算子
邏輯運算子的運算對象是布林值。 下表顯示這些運算子的邏輯真值表。

或|True|False|Undefined
---|---|---|---
True|True|True|True
False|True|False|Undefined
Undefined|True|Undefined|Undefined

AND|True|False|Undefined
---|---|---|---
True|True|False|Undefined
False|False|False|False
Undefined|Undefined|False|Undefined

NOT|  |
---|---
True|False
False|True
Undefined|Undefined

### IN 關鍵字
IN 關鍵字可用來檢查指定的值是否符合清單中的任何值。 例如，此查詢會傳回所有的家族文件，其中 id 是 "WakefieldFamily" 或 "AndersenFamily" 其中一個。 
 
    SELECT *
    FROM Families 
    WHERE Families.id IN ('AndersenFamily', 'WakefieldFamily')

這個範例會傳回其狀態是任何一個指定值的所有文件。

    SELECT *
    FROM Families 
    WHERE Families.address.state IN ("NY", "WA", "CA", "PA", "OH", "OR", "MI", "WI", "MN", "FL")

IN 就相當於鏈結多個 OR 子句，不過可提供使用單一索引，因為 DocumentDB 支援較高 [限制](documentdb-limits.md) IN 子句中指定的引數的數目。  

### 三元 (?) 和聯合 (??) 運算子
三元和聯合運算子可用來建立條件運算式，與熱門程式設計語言 (如 C# 和 JavaScript) 類似。 

快速建構新的 JSON 屬性時，三元 (?) 運算子可以說非常好用。 比方說，您現在可以撰寫查詢將類別層級分類為一般人可判讀的格式 (例如初級/中級/進階)，如下所示。
 
     SELECT (c.grade < 5)? "elementary": "other" AS gradeLevel 
     FROM Families.children[0] c

您也可以巢狀運算子的呼叫，如下方查詢所示。
 
    SELECT (c.grade < 5)? "elementary": ((c.grade < 9)? "junior": "high")  AS gradeLevel 
    FROM Families.children[0] c

和其他查詢運算子一樣，如果任何文件中的條件運算式遺漏了參考屬性，或如果要比較的類型不同，則查詢結果會將這些文件排除在外。

Coalesce （？） 運算子可用來有效率地 （也稱為檢查有屬性 定義） 文件中。 針對半結構化或混合類型的資料執行查詢時，這非常有用。 例如，此查詢會傳回 "lastName" (如果存在) 或 "surname" (如果不存在)。

    SELECT f.lastName ?? f.surname AS familyName
    FROM Families f

### 加上引號的屬性存取子
您也可以使用加上引號的屬性運算子 `[]` 存取屬性。 例如，`SELECT c.grade` 和 `SELECT c["grade"]` 是相等的。 當您需要逸出包含空格、特殊字元的屬性，或剛好要共用和 SQL 關鍵字或保留字相同的名稱時，此語法很有用。

    SELECT f["lastName"]
    FROM Families f
    WHERE f["id"] = "AndersenFamily"


## SELECT 子句
SELECT 子句 (**`SELECT <select_list>`**) 是必要的並指定值將會從查詢中擷取，就像在 ANSI-SQL 中。 在來源文件上篩選出來的子集會傳遞給投射階段，而在此階段中，會擷取指定的 JSON 值並建構新的 JSON 物件 (針對每個傳遞給它的輸入)。 

下列範例示範一般的 SELECT 查詢。 

**查詢**

    SELECT f.address
    FROM Families f 
    WHERE f.id = "AndersenFamily"

**結果**

    [{
      "address": {
        "state": "WA", 
        "county": "King", 
        "city": "seattle"
      }
    }]


### 巢狀屬性
在下列範例中，我們將投射兩個巢狀屬性 `f.address.state` 和 `f.address.city`。

**查詢**

    SELECT f.address.state, f.address.city
    FROM Families f 
    WHERE f.id = "AndersenFamily"

**結果**

    [{
      "state": "WA", 
      "city": "seattle"
    }]


投射也支援 JSON 運算式 (如下列範例所示)。

**查詢**

    SELECT { "state": f.address.state, "city": f.address.city, "name": f.id }
    FROM Families f 
    WHERE f.id = "AndersenFamily"

**結果**

    [{
      "$1": {
        "state": "WA", 
        "city": "seattle", 
        "name": "AndersenFamily"
      }
    }]


讓我們在這裡查看 `$1` 角色。 `SELECT` 子句需要建立 JSON 物件，且因為未提供任何索引鍵，所以我們將使用以 `$1` 開頭的隱含引數變數名稱。 例如，此查詢會傳回兩個隱含引數變數，名稱為 `$1` 和 `$2`。

**查詢**

    SELECT { "state": f.address.state, "city": f.address.city }, 
           { "name": f.id }
    FROM Families f 
    WHERE f.id = "AndersenFamily"

**結果**

    [{
      "$1": {
        "state": "WA", 
        "city": "seattle"
      }, 
      "$2": {
        "name": "AndersenFamily"
      }
    }]


### 別名
現在，讓我們使用值的明確別名處理來擴充上面的範例。 AS 是用來加上別名的關鍵字。 請注意，它在將第二個值投射為 `NameInfo` 時是選用項目 (如下所示)。 

如果查詢具有兩個同名的屬性，則必須使用別名處理來重新命名一或兩個屬性，使其在投射的結果中變得更為明確。

**查詢**

    SELECT 
           { "state": f.address.state, "city": f.address.city } AS AddressInfo, 
           { "name": f.id } NameInfo
    FROM Families f 
    WHERE f.id = "AndersenFamily"

**結果**

    [{
      "AddressInfo": {
        "state": "WA", 
        "city": "seattle"
      }, 
      "NameInfo": {
        "name": "AndersenFamily"
      }
    }]


### 純量運算式
除了屬性參考之外，SELECT 子句也支援純量運算式 (例如常數、算術運算式、邏輯運算式等)。例如，以下是簡單 "Hello World" 查詢。

**查詢**

    SELECT "Hello World"

**結果**

    [{
      "$1": "Hello World"
    }]


以下是使用純量運算式的較複雜範例。

**查詢**

    SELECT ((2 + 11 % 7)-2)/3   

**結果**

    [{
      "$1": 1.33333
    }]


在下列範例中，純量運算式結果是布林值。

**查詢**

    SELECT f.address.city = f.address.state AS AreFromSameCityState
    FROM Families f 

**結果**

    [
      {
        "AreFromSameCityState": false
      }, 
      {
        "AreFromSameCityState": true
      }
    ]


### 物件和陣列建立
DocumentDB SQL 的另一個重要功能是建立陣列/物件。 在前一個範例中，請注意，我們已建立新的 JSON 物件。 同樣地，您也可以建構陣列 (如下列範例所示)。

**查詢**

    SELECT [f.address.city, f.address.state] AS CityState 
    FROM Families f 

**結果**  

    [
      {
        "CityState": [
          "seattle", 
          "WA"
        ]
      }, 
      {
        "CityState": [
          "NY", 
          "NY"
        ]
      }
    ]

### VALUE 關鍵字
 **值** 關鍵字提供可傳回 JSON 值的方法。 例如，下面顯示的查詢會傳回純量 `"Hello World"` 而非 `{$1: "Hello World"}`。

**查詢**

    SELECT VALUE "Hello World"

**結果**

    [
      "Hello World"
    ]


下列查詢會在結果中傳回不含 `"address"` 標籤的 JSON 值。

**查詢**

    SELECT VALUE f.address
    FROM Families f 

**結果**  

    [
      {
        "state": "WA", 
        "county": "King", 
        "city": "seattle"
      }, 
      {
        "state": "NY", 
        "county": "Manhattan", 
        "city": "NY"
      }
    ]

下列範例會延伸此程式碼，以示範如何傳回 JSON 基本值 (JSON 樹狀目錄的分葉層級)。 

**查詢**

    SELECT VALUE f.address.state
    FROM Families f 

**結果**

    [
      "WA",
      "NY"
    ]


###* 運算子
支援使用特殊運算子 (*) 來依原樣投射文件。 使用時，它必須是唯一投射的欄位。 查詢類似 `SELECT * FROM Families f` 有效， `SELECT VALUE * FROM Families f ` 和  `SELECT *, f.id FROM Families f ` 無效。

**查詢**

    SELECT * 
    FROM Families f 
    WHERE f.id = "AndersenFamily"

**結果**

    [{
        "id": "AndersenFamily",
        "lastName": "Andersen",
        "parents": [
           { "firstName": "Thomas" },
           { "firstName": "Mary Kay"}
        ],
        "children": [
           {
               "firstName": "Henriette Thaulow", "gender": "female", "grade": 5,
               "pets": [{ "givenName": "Fluffy" }]
           }
        ],
        "address": { "state": "WA", "county": "King", "city": "seattle" },
        "creationDate": 1431620472,
        "isRegistered": true
    }]

###TOP 運算子
TOP 關鍵字可以用來限制來自查詢的值數目。 當 TOP 與 ORDER BY 子句一起使用時，結果集會限制於前 N 個已排序的值。否則，它會以未定義的順序傳回前 N 個結果。 最佳做法是在 SELECT 陳述式中，一律搭配 TOP 子句來使用 ORDER BY 子句。 這是唯一能如預期般指出哪些資料列會受到 TOP 影響的方式。 


**查詢**

    SELECT TOP 1 * 
    FROM Families f 

**結果**

    [{
        "id": "AndersenFamily",
        "lastName": "Andersen",
        "parents": [
           { "firstName": "Thomas" },
           { "firstName": "Mary Kay"}
        ],
        "children": [
           {
               "firstName": "Henriette Thaulow", "gender": "female", "grade": 5,
               "pets": [{ "givenName": "Fluffy" }]
           }
        ],
        "address": { "state": "WA", "county": "King", "city": "seattle" },
        "creationDate": 1431620472,
        "isRegistered": true
    }]

TOP 可以與常數值 (如上所示) 或使用參數化查詢的變數值搭配使用 。 如需詳細資訊，請參閱下方的參數化查詢。

## ORDER BY 子句
像是在 ANSI SQL 中，您可以在查詢時包含選擇性的 Order By 子句。 子句可以包含選擇性 ASC/DESC 引數，利用它來指定擷取結果時必須依循的順序。 Order by 更深入了解，請參閱 [DocumentDB Order By 逐步解說](documentdb-orderby.md)。

例如，以下是依據居住城市名稱的順序擷取家族的查詢。

**查詢**

    SELECT f.id, f.address.city
    FROM Families f 
    ORDER BY f.address.city
    
**結果**
    
    [
      {
        "id": "WakefieldFamily",
        "city": "NY"
      },
      {
        "id": "AndersenFamily",
        "city": "Seattle"   
      }
    ]

以下是依據建立日期擷取家族的查詢，儲存為代表 epoch 時間的數字，也就是，自 1970 年 1 月 1 日之後經過的時間 (以秒為單位)。

**查詢**

    SELECT f.id, f.creationDate
    FROM Families f 
    ORDER BY f.creationDate DESC
    
**結果**
    
    [
      {
        "id": "WakefieldFamily",
        "creationDate": 1431620462
      },
      {
        "id": "AndersenFamily",
        "creationDate": 1431620472  
      }
    ]
    
## 進階資料庫概念和 SQL 查詢
### 反覆運算
已加入新的建構，透過 **IN** 關鍵字在 DocumentDB SQL，以支援反覆運算 JSON 陣列。 FROM 來源支援反覆運算。 讓我們從下列範例開始：

**查詢**

    SELECT * 
    FROM Families.children

**結果**  

    [
      [
        {
          "firstName": "Henriette Thaulow", 
          "gender": "female", 
          "grade": 5, 
          "pets": [{ "givenName": "Fluffy"}]
        }
      ], 
      [
        {
            "familyName": "Merriam", 
            "givenName": "Jesse", 
            "gender": "female", 
            "grade": 1
        }, 
        {
            "familyName": "Miller", 
            "givenName": "Lisa", 
            "gender": "female", 
            "grade": 8
        }
      ]
    ]

現在，讓我們查看另一個查詢，以執行反覆運算集合中的子系。 請注意輸出陣列中的差異。 此範例會分割 `children`，並將結果簡維成單一陣列。  

**查詢**

    SELECT * 
    FROM c IN Families.children

**結果**  

    [
      {
          "firstName": "Henriette Thaulow",
          "gender": "female",
          "grade": 5,
          "pets": [{ "givenName": "Fluffy" }]
      },
      {
          "familyName": "Merriam",
          "givenName": "Jesse",
          "gender": "female",
          "grade": 1
      },
      {
          "familyName": "Miller",
          "givenName": "Lisa",
          "gender": "female",
          "grade": 8
      }
    ]

這可以進一步用來篩選陣列的每個個別項目 (如下列範例所示)。

**查詢**

    SELECT c.givenName
    FROM c IN Families.children
    WHERE c.grade = 8

**結果**  

    [{
      "givenName": "Lisa"
    }]

### 聯結
在關聯式資料庫中，跨資料表的聯結需求極為重要。 就設計正規化結構描述而言，邏輯上需要它。 與此相反的是，DocumentDB 會處理無結構描述文件的反正規化資料模型。 這在邏輯上等同於「自我聯結」。

支援的語言的語法是 < from_source1 > 聯結 < from_source2 > 聯結...加入 < from_sourceN >。 整體而言，這會傳回一組 **N**-tuple (具有 tuple **N** 值)。 每個 Tuple 所擁有的值，都是將所有集合別名在其個別集合上反覆運算所產生。 換句話說，這是參與聯結之集的完整交叉乘積。

下列範例示範 JOIN 子句的運作方式。 在下列範例中，結果是空的，因為來源中每個文件與空集合的交叉乘積是空的。

**查詢**

    SELECT f.id
    FROM Families f
    JOIN f.NonExistent

**結果**  

    [{
    }]


在下列範例中，聯結會介於文件根目錄與 `children` 子根目錄之間。 它是兩個 JSON 物件之間的交叉乘積。 子系是陣列的事實不會影響 JOIN，因為我們是處理為子陣列的單一根目錄。 因此，結果只會包含兩個結果，因為每個含有陣列之文件的交叉乘積都只會產生一個文件。

**查詢**

    SELECT f.id
    FROM Families f
    JOIN f.children
 
**結果**

    [
      {
        "id": "AndersenFamily"
      }, 
      {
        "id": "WakefieldFamily"
      }
    ]


下列範例示範較傳統的聯結：

**查詢**

    SELECT f.id
    FROM Families f
    JOIN c IN f.children 

**結果**

    [
      {
        "id": "AndersenFamily"
      }, 
      {
        "id": "WakefieldFamily"
      }, 
      {
        "id": "WakefieldFamily"
      }
    ]



請注意第一件事是 `from_source` 的 **加入** 子句是迭代器。 因此，此案例中的流程如下：  

-   展開每個子項目 **c** 陣列中。
-   套用文件的根目錄的交叉乘積 **f** 每個子項目與 **c** ，所壓平合併的第一個步驟中。
-   最後，投射根物件 **f** name 屬性本身。 

第一份文件 (`AndersenFamily`) 只包含一個子項目，因此結果集只會包含與此文件相對應的單一物件。 第二份文件 (`WakefieldFamily`) 包含兩個子系。 因此，交叉乘積會產生每個子系的個別物件，進而導致兩個物件 (一個對應至此文件的子系一個)。 請注意，這兩個文件中的根欄位會相同，就像您在交叉乘積中預期地一樣。

JOIN 的實際作用是透過圖形中很難投射的交叉乘積來形成 Tuple。 此外，如下面範例所示，您可以根據 Tuple 的組合進行篩選，讓使用者選擇全部 Tuple 都符合的條件。

**查詢**

    SELECT 
        f.id AS familyName,
        c.givenName AS childGivenName,
        c.firstName AS childFirstName,
        p.givenName AS petName 
    FROM Families f 
    JOIN c IN f.children 
    JOIN p IN c.pets
 
**結果**

    [
      {
        "familyName": "AndersenFamily", 
        "childFirstName": "Henriette Thaulow", 
        "petName": "Fluffy"
      }, 
      {
        "familyName": "WakefieldFamily", 
        "childGivenName": "Jesse", 
        "petName": "Goofy"
      }, 
      {
       "familyName": "WakefieldFamily", 
       "childGivenName": "Jesse", 
       "petName": "Shadow"
      }
    ]



此範例是上述範例的自然延伸，用來執行雙重聯結。 因此，交叉乘積可被視為下列虛擬程式碼。

    for-each(Family f in Families)
    {   
        for-each(Child c in f.children)
        {
            for-each(Pet p in c.pets)
            {
                return (Tuple(f.id AS familyName, 
                  c.givenName AS childGivenName, 
                  c.firstName AS childFirstName,
                  p.givenName AS petName));
            }
        }
    }

`AndersenFamily` 有一個小孩養了一隻寵物。 因此，交叉乘積會產生一個資料列 (1*1*1) 從這一系列。 不過，WakefieldFamily 有兩個小孩，但只有一個小孩 "Jesse" 養了多隻寵物。 而 Jesse 擁有 2 隻寵物。 因此，交叉乘積會產生 1*1*2 = 2 的這一系列資料列。

在下一個範例中，對 `pet` 有一個額外的篩選。 這會排除寵物名稱不是 "Shadow" 的所有 Tuple。 請注意，我們可以從陣列建置 Tuple、根據 Tuple 的任何元素進行篩選，以及投射元素的任何組合。 

**查詢**

    SELECT 
        f.id AS familyName,
        c.givenName AS childGivenName,
        c.firstName AS childFirstName,
        p.givenName AS petName 
    FROM Families f 
    JOIN c IN f.children 
    JOIN p IN c.pets
    WHERE p.givenName = "Shadow"

**結果**

    [
      {
       "familyName": "WakefieldFamily", 
       "childGivenName": "Jesse", 
       "petName": "Shadow"
      }
    ]


## JavaScript 整合
DocumentDB 提供一個程式設計模型，以根據預存程序和觸發程序，直接對集合執行 JavaScript 型應用程式邏輯。 這允許：

-   藉由直接在資料庫引擎內深入整合 JavaScript 執行階段，對集合中的文件執行高效能交易式 CRUD 操作和查詢。 
-   將控制流程、變數範圍限制、指派以及例外狀況處理基本類型與資料庫交易的整合自然模型化。 如需 JavaScript 整合之 DocumentDB 支援的詳細資料，請參閱 JavaScript 伺服器端程式設計文件。

###使用者定義函數 (UDF)
除了本文中已定義的類型之外，DocumentDB SQL 還支援使用者定義函式 (UDF)。 特別的是，如果開發人員可以傳入零個或多個引數並傳回單一引數結果，則支援純量 UDF。 系統會檢查所有這些引數是否為合法的 JSON 值。  

DocumentDB SQL 語法已延伸，可支援使用這些使用者定義函式的自訂應用程式邏輯。 您可以向 DocumentDB 註冊 UDF，然後在 SQL 查詢中參照它。 實際上，UDF 是特別設計來透過查詢進行叫用。 這項選擇的必然結果，就是 UDF 無法存取其他 JavaScript 類型 (預存程序和觸發程序) 擁有的內容物件。 因為查詢是以唯讀形式執行，所以可以在主要或次要複本上執行。 因此，與其他 JavaScript 類型不同，UDF 是設計成在次要複本上執行。

以下是如何在 DocumentDB 資料庫中註冊 UDF 的範例 (特別是在文件集合下)。

   
       UserDefinedFunction regexMatchUdf = new UserDefinedFunction
       {
           Id = "REGEX_MATCH",
           Body = @"function (input, pattern) { 
                       return input.match(pattern) !== null;
                   };",
       };
       
       UserDefinedFunction createdUdf = client.CreateUserDefinedFunctionAsync(
           collectionSelfLink/* link of the parent collection*/, 
           regexMatchUdf).Result;  
                                                                             
上述範例會建立名為 `REGEX_MATCH` 的 UDF。 它接受兩個 JSON 字串值 `input` 和 `pattern`，並使用 JavaScript 的 string.match() 函式來檢查第一個項目是否符合第二個項目中指定的模式。


我們現在可以在投射的查詢中使用此 UDF。 必須限定 Udf，以區分大小寫的前置詞"udf 」。 當從查詢中呼叫。 

>[AZURE.NOTE] 3/17/2015 年以前，DocumentDB 支援無需 UDF 「 udf 」。 前置詞，例如 SELECT regex_match （）。 這種呼叫模式已被取代。  

**查詢**

    SELECT udf.REGEX_MATCH(Families.address.city, ".*eattle")
    FROM Families

**結果**

    [
      {
        "$1": true
      }, 
      {
        "$1": false
      }
    ]

UDF 也可用篩選內所示在下列範例中，也限定 「 udf 」。 前置詞 ︰

**查詢**

    SELECT Families.id, Families.address.city
    FROM Families
    WHERE udf.REGEX_MATCH(Families.address.city, ".*eattle")

**結果**

    [{
        "id": "AndersenFamily",
        "city": "Seattle"
    }]


在本質上，UDF 是有效的純量運算式，而且可以用於投射和篩選。 

為了擴展 UDF 的能力，請查看另一個含有條件式邏輯的範例：

       UserDefinedFunction seaLevelUdf = new UserDefinedFunction()
       {
           Id = "SEALEVEL",
           Body = @"function(city) {
                switch (city) {
                    case 'seattle':
                        return 520;
                    case 'NY':
                        return 410;
                    case 'Chicago':
                        return 673;
                    default:
                        return -1;
                    }"
            };

            UserDefinedFunction createdUdf = await client.CreateUserDefinedFunctionAsync(collection.SelfLink, seaLevelUdf);
    
    
以下是執行 UDF 的範例。

**查詢**

    SELECT f.address.city, udf.SEALEVEL(f.address.city) AS seaLevel
    FROM Families f 

**結果**

     [
      {
        "city": "seattle", 
        "seaLevel": 520
      }, 
      {
        "city": "NY", 
        "seaLevel": 410
      }
    ]


如同上述範例所示，UDF 可將 JavaScript 語言的強大功能與 DocumentDB SQL 整合來提供可進行程式設計的豐富介面，以在內建 JavaScript 執行階段功能的協助下，執行複雜的程序性、條件式邏輯。

在處理 UDF 的目前階段 (WHERE 子句或 SELECT 子句) 中，DocumentDB SQL 針對來源中的每個文件提供 UDF 的引數。 結果會緊密整合至整體執行管線。 如果 JSON 值中沒有 UDF 參數所參照的屬性，則會將參數視為 undefined，因此，而整個略過 UDF 叫用。 同樣地，如果 UDF 的結果為未定義，便不會被納入結果中。 

簡言之，UDF 是在進行查詢時執行複雜商務邏輯的不錯工具。

### 運算子評估
DocumentDB 憑藉著為 JSON 資料庫，來描繪 JavaScript 運算子和其評估語意。 雖然 DocumentDB 嘗試保留 JSON 支援方面的 JavaScript 語意，但是在部分執行個體中，作業評估還是會偏離。

與傳統 SQL 不同，在 DocumentDB SQL 中，除非從資料庫實際擷取值，否則通常不知道值的類型。 為了有效率地執行查詢，大部分的運算子都有嚴謹的類型需求。 

與 JavaScript 不同，DocumentDB SQL 不會執行隱含轉換。 例如，類似 `SELECT * FROM Person p WHERE p.Age = 21` 的查詢會針對包含 Age 屬性且值為 21 的文件進行比對。 其中 Age 屬性符合字串 "21" 或
符合其他可能無限變化 (如 "021"、"21.0"、"0021"、"00021" 等) 的任何其他文件都將不會進行比對。 
這與 JavaScript 形成對比，在 JavaScript 中，會將字串值隱含地轉換成數字 (根據運算子，例如：==)。 這項選擇對 DocumentDB SQL 中有效率的索引比對十分重要。 

## 參數化 SQL 查詢
DocumentDB 支援查詢使用以類似 @ 標記法表示的參數。 參數化 SQL 提供使用者輸入的健全處理和逸出，防止透過 SQL 插入式意外洩露資料。 

比方說，您可以撰寫查詢，將姓氏和地址 (州) 做為參數，然後根據使用者輸入，使用各種不同姓氏和地址 (州) 的值執行查詢。

    SELECT * 
    FROM Families f
    WHERE f.lastName = @lastName AND f.address.state = @addressState

您接著可以將這項要求傳送至 DocumentDB 做為參數化 JSON 查詢，如下所示。

    {      
        "query": "SELECT * FROM Families f WHERE f.lastName = @lastName AND f.address.state = @addressState",     
        "parameters": [          
            {"name": "@lastName", "value": "Wakefield"},         
            {"name": "@addressState", "value": "NY"},           
        ] 
    }

您可以使用參數化查詢來設定 TOP 的引數，如下所示。

    {      
        "query": "SELECT TOP @n * FROM Families",     
        "parameters": [          
            {"name": "@n", "value": 10},         
        ] 
    }

參數值可以是任何有效的 JSON (字串、數字、布林值、null，甚至是陣列或巢狀 JSON)。 而且因為 DocumentDB 屬於無結構描述，系統不會針對任何類型驗證參數。

##內建函數
DocumentDB 也支援一般作業的數個內建函數，這些函數可用於查詢內，例如使用者定義函數 (UDF)。

<table>
<tr>
<td>數學函數</td> 
<td>ABS、CEILING、EXP、FLOOR、LOG、LOG10、POWER、ROUND、SIGN、SQRT、SQUARE、TRUNC、ACOS、ASIN、ATAN、ATN2、COS、COT、DEGREES、PI、RADIANS、SIN 和 TAN</td>
</tr>
<tr>
<td>類型檢查函數</td>    
<td>IS_ARRAY、IS_BOOL、IS_NULL、IS_NUMBER、IS_OBJECT、IS_STRING、IS_DEFINED 和 IS_PRIMITIVE</td>
</tr>
<tr>
<td>字串函數</td>   
<td>CONCAT、CONTAINS、ENDSWITH、INDEX_OF、LEFT、LENGTH、LOWER、LTRIM、REPLACE、REPLICATE、REVERSE、RIGHT、RTRIM、STARTSWITH、SUBSTRING 和 UPPER</td>
</tr>
<tr>
<td>陣列函數</td>    
<td>ARRAY_CONCAT、ARRAY_CONTAINS、ARRAY_LENGTH 和 ARRAY_SLICE</td>
</tr>
<tr>
<td>空間函數</td>  
<td>ST_DISTANCE、ST_WITHIN、ST_ISVALID 和 ST_ISVALIDDETAILED</td>
</tr>
</table>  

如果您目前使用的使用者定義函數 (UDF) 已提供內建函數，您應該使用相對應的內建函數，因為這樣會加快執行速度並更有效率。 

### 數學函數
每個數學函數都會執行計算，通常以提供做為引數的輸入值為基礎，並且會傳回數值。 以下是支援的內建數學函數資料表。

<table>
<tr>
<td><strong>使用量</strong></td>
<td><strong>說明</strong></td>
</tr>
<tr>
<td><a href="https://msdn.microsoft.com/library/azure/dn782250.aspx#bk_abs">ABS (num_expr)</a></td> 
<td>傳回指定之數值運算式的絕對 (正) 值。</td>
</tr>
<tr>
<td><a href="https://msdn.microsoft.com/library/azure/dn782250.aspx#bk_ceiling">CEILING (num_expr)</a></td> 
<td>傳回大於或等於指定之數值運算式的最小整數值。</td>
</tr>
<tr>
<td><a href="https://msdn.microsoft.com/library/azure/dn782250.aspx#bk_floor">FLOOR (num_expr)</a></td> 
<td>傳回小於或等於指定之數值運算式的最大整數。</td>
</tr>
<tr>
<td><a href="https://msdn.microsoft.com/library/azure/dn782250.aspx#bk_exp">EXP (num_expr)</a></td> 
<td>傳回指定之數值運算式的指數。</td>
</tr>
<tr>
<td><a href="https://msdn.microsoft.com/library/azure/dn782250.aspx#bk_log">LOG (num_expr [,base])</a></td> 
<td>傳回指定之數值運算式的自然對數，或使用指定之基底的對數</td>
</tr>
<tr>
<td><a href="https://msdn.microsoft.com/library/azure/dn782250.aspx#bk_log10">LOG10 (num_expr)</a></td> 
<td>傳回指定之數值運算式的以 10 為基底的對數值。</td>
</tr>
<tr>
<td><a href="https://msdn.microsoft.com/library/azure/dn782250.aspx#bk_round">ROUND (num_expr)</a></td> 
<td>傳回數值，四捨五入到最接近的整數值。</td>
</tr>
<tr>
<td><a href="https://msdn.microsoft.com/library/azure/dn782250.aspx#bk_trunc">TRUNC (num_expr)</a></td> 
<td>傳回數值，截斷至最接近的整數值。</td>
</tr>
<tr>
<td><a href="https://msdn.microsoft.com/library/azure/dn782250.aspx#bk_sqrt">SQRT (num_expr)</a></td>   
<td>傳回指定之數值運算式的平方根。</td>
</tr>
<tr>
<td><a href="https://msdn.microsoft.com/library/azure/dn782250.aspx#bk_square">SQUARE (num_expr)</a></td>   
<td>傳回指定之數值運算式的平方。</td>
</tr>
<tr>
<td><a href="https://msdn.microsoft.com/library/azure/dn782250.aspx#bk_power">POWER (num_expr, num_expr)</a></td>   
<td>傳回指定之數值運算式的乘冪給指定的值。</td>
</tr>
<tr>
<td><a href="https://msdn.microsoft.com/library/azure/dn782250.aspx#bk_sign">SIGN (num_expr)</a></td>   
<td>傳回指定之數值運算式的正負號值 (-1、0、1)。</td>
</tr>
<tr>
<tr>
<td><a href="https://msdn.microsoft.com/library/azure/dn782250.aspx#bk_acos">ACOS (num_expr)</a></td>   
<td>傳回角度，以弧度為單位，它的餘弦是指定的數值運算式；也稱為反餘弦值。</td>
</tr>
<tr>
<td><a href="https://msdn.microsoft.com/library/azure/dn782250.aspx#bk_asin">ASIN (num_expr)</a></td>   
<td>傳回角度，以弧度為單位，其正弦函數是指定的數值運算式。 這也稱為反正弦值。</td>
</tr>
<tr>
<td><a href="https://msdn.microsoft.com/library/azure/dn782250.aspx#bk_atan">ATAN (num_expr)</a></td>   
<td>傳回角度，以弧度為單位，其正切函數是指定的數值運算式。 這也稱為反正切值。</td>
</tr>
<tr>
<td><a href="https://msdn.microsoft.com/library/azure/dn782250.aspx#bk_atn2">ATN2 (num_expr)</a></td>   
<td>傳回角度，以弧度為單位，正 x 軸和從原點 (y、x) 點的切線之間，其中 x 和 y 是兩個指定之浮點運算式的值。</td>
</tr>
<tr>
<td><a href="https://msdn.microsoft.com/library/azure/dn782250.aspx#bk_cos">COS (num_expr)</a></td> 
<td>在指定運算式中傳回指定角度的三角餘弦函數，以弧度為單位。</td>
</tr>
<tr>
<td><a href="https://msdn.microsoft.com/library/azure/dn782250.aspx#bk_cot">COT (num_expr)</a></td> 
<td>在指定的數值運算式中傳回指定角度的三角餘切函數，以弧度為單位。</td>
</tr>
<tr>
<td><a href="https://msdn.microsoft.com/library/azure/dn782250.aspx#bk_degrees">DEGREES (num_expr)</a></td> 
<td>針對以弧度指定的角度，傳回對應的角度 (以度為單位)。</td>
</tr>
<tr>
<td><a href="https://msdn.microsoft.com/library/azure/dn782250.aspx#bk_pi">PI ()</a></td>   
<td>傳回 PI 的常數值。</td>
</tr>
<tr>
<td><a href="https://msdn.microsoft.com/library/azure/dn782250.aspx#bk_radians">RADIANS (num_expr)</a></td> 
<td>當您輸入數值運算式 (以度為單位) 時，傳回弧度。</td>
</tr>
<tr>
<td><a href="https://msdn.microsoft.com/library/azure/dn782250.aspx#bk_sin">SIN (num_expr)</a></td> 
<td>在指定運算式中傳回指定角度的三角正弦函數 (以弧度為單位)。</td>
</tr>
<tr>
<td><a href="https://msdn.microsoft.com/library/azure/dn782250.aspx#bk_tan">TAN (num_expr)</a></td> 
<td>在指定運算式中傳回輸入運算式的正切函數。</td>
</tr>

</table> 

例如，您現在可以執行下列的查詢：

**查詢**

    SELECT VALUE ABS(-4)

**結果**

    [4]

相較於 ANSI SQL，DocumentDB 函數的主要差異在於它們的設計用途是有效搭配使用無結構描述資料和混合的結構描述資料。 例如，如果您有一個文件的 [大小] 屬性已遺失，或具有非數值的值 (例如 "unknown")，該文件就會被略過而不會傳回錯誤。

### 類型檢查函數
類型檢查函數可讓您檢查 SQL 查詢中的運算式類型。 類型檢查函數可在屬性類型為變數或未知時，用來快速判斷文件中的屬性類型。 以下是支援的內建類型檢查函數資料表。

<table>
<tr>
  <td><strong>使用量</strong></td>
  <td><strong>說明</strong></td>
</tr>
<tr>
  <td><a href="https://msdn.microsoft.com/library/azure/dn782250.aspx#bk_is_array">IS_ARRAY (expr)</a></td>
  <td>傳回布林值，表示值的類型是否為陣列。</td>
</tr>
<tr>
  <td><a href="https://msdn.microsoft.com/library/azure/dn782250.aspx#bk_is_bool">IS_BOOL (expr)</a></td>
  <td>傳回布林值，表示值的類型是否為布林值。</td>
</tr>
<tr>
  <td><a href="https://msdn.microsoft.com/library/azure/dn782250.aspx#bk_is_null">IS_NULL (expr)</a></td>
  <td>傳回布林值，表示值的類型是否為 null。</td>
</tr>
<tr>
  <td><a href="https://msdn.microsoft.com/library/azure/dn782250.aspx#bk_is_number">IS_NUMBER (expr)</a></td>
  <td>傳回布林值，表示值的類型是否為數字。</td>
</tr>
<tr>
  <td><a href="https://msdn.microsoft.com/library/azure/dn782250.aspx#bk_is_object">IS_OBJECT (expr)</a></td>
  <td>傳回布林值，表示值的類型是否為 JSON 物件。</td>
</tr>
<tr>
  <td><a href="https://msdn.microsoft.com/library/azure/dn782250.aspx#bk_is_string">IS_STRING (expr)</a></td>
  <td>傳回布林值，表示值的類型是否為字串。</td>
</tr>
<tr>
  <td><a href="https://msdn.microsoft.com/library/azure/dn782250.aspx#bk_is_defined">IS_DEFINED (expr)</a></td>
  <td>傳回布林值，表示屬性是否已經指派值。</td>
</tr>
<tr>
  <td><a href="https://msdn.microsoft.com/library/azure/dn782250.aspx#bk_is_primitive">IS_PRIMITIVE (expr)</a></td>
  <td>傳回布林值，表示值的類型是字串、數字、布林值或 Null。</td>
</tr>

</table>

藉由使用這些函數，您現在可以執行下列查詢：

**查詢**

    SELECT VALUE IS_NUMBER(-4)

**結果**

    [true]

### 字串函數
下列純量函數會對字串輸入值執行作業，並傳回字串、數值或布林值。 以下是內建字串函數的資料表：

使用量|說明
---|---
[LENGTH (str_expr)](https://msdn.microsoft.com/library/azure/dn782250.aspx#bk_length)|傳回指定字串運算式的字元數目
[CONCAT (str_expr, str_expr [, str_expr])](https://msdn.microsoft.com/library/azure/dn782250.aspx#bk_concat)|傳回字串，該字串是串連兩個或多個字串值的結果。
[SUBSTRING (str_expr, num_expr, num_expr)](https://msdn.microsoft.com/library/azure/dn782250.aspx#bk_substring)|傳回字串運算式的一部分。
[STARTSWITH (str_expr, str_expr)](https://msdn.microsoft.com/library/azure/dn782250.aspx#bk_startswith)|傳回布林值，表示第一個字串運算式是否以第二個結束字串運算式做為結束
[ENDSWITH (str_expr, str_expr)](https://msdn.microsoft.com/library/azure/dn782250.aspx#bk_endswith)|傳回布林值，表示第一個字串運算式是否以第二個結束字串運算式做為結束
[CONTAINS (str_expr, str_expr)](https://msdn.microsoft.com/library/azure/dn782250.aspx#bk_contains)|傳回布林值，表示第一個字串運算式是否包含第二個字串運算式。
[INDEX_OF (str_expr, str_expr)](https://msdn.microsoft.com/library/azure/dn782250.aspx#bk_index_of)|傳回第一個指定的字串運算式中，第二個字串運算式第一次出現的開始位置，或者如果找不到字串，則為 -1。
[LEFT (str_expr, num_expr)](https://msdn.microsoft.com/library/azure/dn782250.aspx#bk_left)|傳回具有指定字元數目的字串左側部分。
[RIGHT (str_expr, num_expr)](https://msdn.microsoft.com/library/azure/dn782250.aspx#bk_right)|傳回具有指定字元數目的字串右側部分。
[LTRIM (str_expr)](https://msdn.microsoft.com/library/azure/dn782250.aspx#bk_ltrim)|傳回移除開頭空白之後的字串運算式。
[RTRIM (str_expr)](https://msdn.microsoft.com/library/azure/dn782250.aspx#bk_rtrim)|傳回截斷所有結尾空白之後的字串運算式。
[LOWER (str_expr)](https://msdn.microsoft.com/library/azure/dn782250.aspx#bk_lower)|傳回將大寫字元資料轉換成小寫之後的字串運算式。
[UPPER (str_expr)](https://msdn.microsoft.com/library/azure/dn782250.aspx#bk_upper)|傳回將小寫字元資料轉換成大寫之後的字串運算式。
[REPLACE (str_expr, str_expr, str_expr)](https://msdn.microsoft.com/library/azure/dn782250.aspx#bk_replace)|使用其他字串值取代指定的字串值的所有項目。
[REPLICATE (str_expr, num_expr)](https://msdn.microsoft.com/library/azure/dn782250.aspx#bk_replicate)|將字串值重複指定的次數。
[REVERSE (str_expr)](https://msdn.microsoft.com/library/azure/dn782250.aspx#bk_reverse)|傳回反向順序的字串值。

藉由使用這些函數，您現在可以執行下列查詢。 例如，您可以傳回大寫的家族名稱，如下所示：

**查詢**

    SELECT VALUE UPPER(Families.id)
    FROM Families

**結果**

    [
        "WAKEFIELDFAMILY", 
        "ANDERSENFAMILY"
    ]

或串連字串，如下列範例所示：

**查詢**

    SELECT Families.id, CONCAT(Families.address.city, ",", Families.address.state) AS location
    FROM Families

**結果**

    [{
      "id": "WakefieldFamily",
      "location": "NY,NY"
    },
    {
      "id": "AndersenFamily",
      "location": "seattle,WA"
    }]


字串函數也可用於 WHERE 子句中以篩選結果，例如下列範例：

**查詢**

    SELECT Families.id, Families.address.city
    FROM Families
    WHERE STARTSWITH(Families.id, "Wakefield")

**結果**

    [{
      "id": "WakefieldFamily",
      "city": "NY"
    }]

### 陣列函數
下列純量函數會對陣列輸入值執行作業，並傳回數值、布林值或陣列值。 以下是內建陣列函數的資料表：

使用量|說明
---|---
[ARRAY_LENGTH (arr_expr)](https://msdn.microsoft.com/library/azure/dn782250.aspx#bk_array_length)|傳回指定陣列運算式的元素數目。
[ARRAY_CONCAT (arr_expr, arr_expr [, arr_expr])](https://msdn.microsoft.com/library/azure/dn782250.aspx#bk_array_concat)|傳回串連兩個或多個陣列值之結果的陣列。
[ARRAY_CONTAINS (arr_expr, expr)](https://msdn.microsoft.com/library/azure/dn782250.aspx#bk_array_contains)|傳回布林值，表示陣列是否包含指定值。
[ARRAY_SLICE (arr_expr, num_expr [, num_expr])](https://msdn.microsoft.com/library/azure/dn782250.aspx#bk_array_slice)|傳回陣列運算式的一部分。

陣列函數可用來管理 JSON 中的陣列。 例如，以下是傳回其中一位父母是 "Robin Wakefield" 之所有文件的查詢。 

**查詢**

    SELECT Families.id 
    FROM Families 
    WHERE ARRAY_CONTAINS(Families.parents, { givenName: "Robin", familyName: "Wakefield" })

**結果**

    [{
      "id": "WakefieldFamily"
    }]

以下是另一個例子，會使用 ARRAY_LENGTH 取得每個家族的子女數目。

**查詢**

    SELECT Families.id, ARRAY_LENGTH(Families.children) AS numberOfChildren
    FROM Families 

**結果**

    [{
      "id": "WakefieldFamily",
      "numberOfChildren": 2
    },
    {
      "id": "AndersenFamily",
      "numberOfChildren": 1
    }]

### 空間函數

DocumentDB 支援下列開放地理空間協會 (OGC) 的內建函數，以用於查詢地理空間。 如需有關 DocumentDB 中的地理空間支援的詳細資訊，請參閱 [使用 Azure DocumentDB 中的地理空間資料](documentdb-geospatial.md)。 

<table>
<tr>
  <td><strong>使用量</strong></td>
  <td><strong>說明</strong></td>
</tr>
<tr>
  <td>ST_DISTANCE (point_expr、point_expr)</td>
  <td>傳回兩個 GeoJSON 點運算式之間的距離。</td>
</tr>
<tr>
  <td>ST_WITHIN (point_expr、polygon_expr)</td>
  <td>傳回布林運算式，指出第一個引數中指定的 GeoJSON 點是否位在第二個引數的 GeoJSON 多邊形內。</td>
</tr>
<tr>
  <td>ST_ISVALID</td>
  <td>傳回布林值，指出指定的 GeoJSON 點或多邊形運算式是否有效。</td>
</tr>
<tr>
  <td>ST_ISVALIDDETAILED</td>
  <td>如果指定的 GeoJSON 點或多邊形運算式是有效的，就會傳回包含布林值的 JSON 值；但如果是無效的，就會額外加上做為字串值的原因。</td>
</tr>
</table>

空間函數可以用來對空間資料的執行鄰近性查詢。 例如，以下查詢使用 ST_DISTANCE 內建函數傳回所有家族文件，且這些文件在 30 公里指定位置內。 

**查詢**

    SELECT f.id 
    FROM Families f 
    WHERE ST_DISTANCE(f.location, {'type': 'Point', 'coordinates':[31.9, -4.8]}) < 30000

**結果**

    [{
      "id": "WakefieldFamily"
    }]

如果您將空間索引編製包含在索引編製原則中，則「距離查詢」將會透過索引獲得有效利用。 如需空間索引編製的詳細資料，請參閱下面的章節。 如果您沒有指定路徑的空間索引，仍然可以透過指定 `x-ms-documentdb-query-enable-scan` 要求標頭 (且值設定為 "true") 執行空間查詢。 在.NET 中，這可以經由傳遞選擇性 **FeedOptions** 引數來使用查詢 [EnableScanInQuery](https://msdn.microsoft.com/library/microsoft.azure.documents.client.feedoptions.enablescaninquery.aspx#P:Microsoft.Azure.Documents.Client.FeedOptions.EnableScanInQuery) 設為 true。 

ST_WITHIN 可用來檢查點是否在多邊形內。 多邊形常用來表示邊界，例如郵遞區號、州省邊界或自然構成物。 此外，如果您將空間索引編製包含在索引編製原則中，則「距離內」查詢將會透過索引獲得有效利用。 

ST_WITHIN 中的多邊形引數只可以包含單一環狀，也就是多邊形本身不能有漏洞。 檢查 [DocumentDB 限制](documentdb-limits.md) 點 ST_WITHIN 查詢多邊形內所允許的最大數目。

**查詢**

    SELECT * 
    FROM Families f 
    WHERE ST_WITHIN(f.location, {
        'type':'Polygon', 
        'coordinates': [[[31.8, -5], [32, -5], [32, -4.7], [31.8, -4.7], [31.8, -5]]]
    })

**結果**

    [{
      "id": "WakefieldFamily",
    }]
    
>[AZURE.NOTE] 類似於如何不相符的型別運作，在 DocumentDB 查詢中，如果是引數格式不正確或無效，則會評估為在指定的位置值 **未定義** 和查詢結果中略過評估文件。 如果您的查詢沒有傳回任何結果，請執行 ST_ISVALIDDETAILED 來偵錯，了解空間類型無效的原因。     

ST_ISVALID 和 ST_ISVALIDDETAILED 可用來檢查空間物件是否有效。 例如，下列查詢以超出範圍的緯度值 (-132.8)，檢查點的有效性。 ST_ISVALID 只會傳回布林值，而 ST_ISVALIDDETAILED 會傳回布林和字串，字串中包含被視為無效的原因。

**查詢**

    SELECT ST_ISVALID({ "type": "Point", "coordinates": [31.9, -132.8] })

**結果**

    [{
      "$1": false
    }]

這些函數也可以用來驗證多邊形。 例如，這裡我們使用 ST_ISVALIDDETAILED 驗證不封閉的多邊形。 

**查詢**

    SELECT ST_ISVALIDDETAILED({ "type": "Polygon", "coordinates": [[ 
        [ 31.8, -5 ], [ 31.8, -4.7 ], [ 32, -4.7 ], [ 32, -5 ] 
        ]]})

**結果**

    [{
       "$1": { 
          "valid": false, 
          "reason": "The Polygon input is not valid because the start and end points of the ring number 1 are not the same. Each ring of a polygon must have the same start and end points." 
        }
    }]
    
這樣會包裝空間函式以及 DocumentDB 的 SQL 語法。 現在讓我們看看 LINQ 查詢如何運作，以及它如何與我們到目前為止所見到的語法互動。

## LINQ 到 DocumentDB SQL
LINQ 是一種 .NET 程式設計模型，此模型會將運算表示為對物件串流的查詢。 DocumentDB 提供用戶端程式庫，以透過促進 JSON 與 .NET 物件之間的轉換，以及從小部分的 LINQ 查詢對應至 DocumentDB 查詢，來與 LINQ 互動。 

下圖顯示使用 DocumentDB 支援 LINQ 查詢的架構。  使用 DocumentDB 用戶端，開發人員可以建立 **IQueryable** 直接查詢 DocumentDB 查詢提供者，接著將 LINQ 查詢轉譯成 DocumentDB 查詢的物件。 然後，將查詢傳遞給 DocumentDB 伺服器，以擷取一組具有 JSON 格式的結果。 傳回的結果會在用戶端進行還原序列化，變成 .NET 物件的串流。

![使用 DocumentDB 支援 LINQ 查詢的架構。][1]
 


### .NET 和 JSON 對應
.NET 物件與 JSON 文件之間的對應是自然的，亦即每個資料成員欄位都會對應至 JSON 物件，其中欄位名稱對應至物件的「索引鍵」部分，而「值」部分則遞迴地對應至物件的值部分。 請思考一下下列範例。 建立的 Family 物件對應至 JSON 文件 (如下所示)。 而反之亦然，JSON 文件會對應回 .NET 物件。

**C# 類別**

    public class Family
    {
        [JsonProperty(PropertyName="id")]
        public string Id;
        public Parent[] parents;
        public Child[] children;
        public bool isRegistered;
    };
    
    public struct Parent
    {
        public string familyName;
        public string givenName;
    };
    
    public class Child
    {
        public string familyName;
        public string givenName;
        public string gender;
        public int grade;
        public List<Pet> pets;
    };
    
    public class Pet
    {
        public string givenName;
    };
    
    public class Address
    {
        public string state;
        public string county;
        public string city;
    };
    
    // Create a Family object.
    Parent mother = new Parent { familyName= "Wakefield", givenName="Robin" };
    Parent father = new Parent { familyName = "Miller", givenName = "Ben" };
    Child child = new Child { familyName="Merriam", givenName="Jesse", gender="female", grade=1 };
    Pet pet = new Pet { givenName = "Fluffy" };
    Address address = new Address { state = "NY", county = "Manhattan", city = "NY" };
    Family family = new Family { Id = "WakefieldFamily", parents = new Parent [] { mother, father}, children = new Child[] { child }, isRegistered = false };


**JSON**  

    {
        "id": "WakefieldFamily",
        "parents": [
            { "familyName": "Wakefield", "givenName": "Robin" },
            { "familyName": "Miller", "givenName": "Ben" }
        ],
        "children": [
            {
                "familyName": "Merriam", 
                "givenName": "Jesse", 
                "gender": "female", 
                "grade": 1,
                "pets": [
                    { "givenName": "Goofy" },
                    { "givenName": "Shadow" }
                ]
            },
            { 
              "familyName": "Miller", 
              "givenName": "Lisa", 
              "gender": "female", 
              "grade": 8 
            }
        ],
        "address": { "state": "NY", "county": "Manhattan", "city": "NY" },
        "isRegistered": false
    };



### LINQ 至 SQL 轉譯
DocumentDB 查詢提供者執行從 LINQ 查詢到 DocumentDB SQL 查詢的最佳對應。 在下列描述中，我們假設讀者對 LINQ 有基本的認識。

首先，針對類型系統，我們支援所有 JSON 基本類型：數值類型、布林值、字串及 null。 只支援這些 JSON 類型。 下列是支援的純量運算式。

-   常數值：這些包括評估查詢時之基本資料類型的常數值。

-   屬性/陣列索引運算式：這些運算式參照物件或陣列項目的屬性。

        family.Id;
        family.children[0].familyName;
        family.children[0].grade;
        family.children[n].grade; //n is an int variable

-   算術運算式：這些包括數值和布林值的一般算術運算式。 如需完整清單，請參閱 SQL 規格。

        2 * family.children[0].grade;
        x + y;

-   字串比較運算式：這些包括比較字串值與某個常數字串值。  
 
        mother.familyName == "Smith";
        child.givenName == s; //s is a string variable

-   物件/陣列建立運算式：這些運算式傳回複合值類型或匿名類型的物件，或是這類物件的陣列。 這些值可以是巢狀值。

        new Parent { familyName = "Smith", givenName = "Joe" };
        new { first = 1, second = 2 }; //an anonymous type with 2 fields              
        new int[] { 3, child.grade, 5 };

### 支援的 LINQ 運算子清單
以下是隨附於 DocumentDB.NET SDK 之 LINQ 提供者中支援的 LINQ 運算子清單。

-   **選取**︰ 投影轉譯成 SQL SELECT 包括物件建構
-   **其中**︰ 篩選轉譯成 SQL WHERE，並支援之間轉譯 & &、 | | 和 ！ 以 SQL 運算子
-   **SelectMany**︰ 可讓復原的 SQL JOIN 子句的陣列。 可用來鏈結/巢串運算式來篩選陣列元素
-   **OrderBy 和 OrderByDescending**︰ 將資料轉譯為 ORDER BY 遞增/遞減 ︰
-   **CompareTo**︰ 將資料轉譯為範圍比較。 通常用於字串，因為字串在 .NET 中是無法比較的
-   **採取**︰ 將資料轉譯為 SQL TOP 來限制從查詢結果
-   **數學函式**︰ 支援從轉譯。NET 的 Abs、 Acos、 Asin、 Atan、 Ceiling Cos、 Exp、 Floor、 記錄、 Log10、 Pow、 循、 號、 Sin、 Sqrt、 Tan、 對等 SQL 的內建函式的截斷。
-   **字串函式**︰ 支援從轉譯。NET 的 Concat、 Contains、 EndsWith、 IndexOf、 計數、 ToLower、 TrimStart、 取代、 反向、 TrimEnd、 StartsWith、 子字串、 對等 SQL 的內建函式的 ToUpper。
-   **陣列函數**︰ 支援從轉譯。NET 的 Concat、 Contains 和對等 SQL 的內建函式的計數。
-   **地理空間擴充程式函式**︰ 支援虛設常式方法 IsValid 和 IsValidDetailed 內的距離，對等 SQL 的內建函式的轉譯。
-   **使用者定義函式擴充函式**︰ 從虛設常式方法 UserDefinedFunctionProvider.Invoke 支援轉譯對應使用者定義函式。
-   **其他**︰ 支援聯合和條件式運算子的轉譯。 可根據內容將 Contains 轉譯為字串 CONTAINS、ARRAY_CONTAINS 或 SQL IN。

### SQL 查詢運算子
以下一些範例說明如何將一些標準 LINQ 查詢運算子往下轉譯為 DocumentDB 查詢。

#### Select 運算子
語法為 `input.Select(x => f(x))`，其中 `f` 是純量運算式。

**LINQ Lambda 運算式**

    input.Select(family => family.parents[0].familyName);

**SQL** 

    SELECT VALUE f.parents[0].familyName
    FROM Families f



**LINQ Lambda 運算式**

    input.Select(family => family.children[0].grade + c); // c is an int variable


**SQL** 

    SELECT VALUE f.children[0].grade + c
    FROM Families f 



**LINQ Lambda 運算式**

    input.Select(family => new
    {
        name = family.children[0].familyName,
        grade = family.children[0].grade + 3
    });


**SQL** 

    SELECT VALUE {"name":f.children[0].familyName, 
                  "grade": f.children[0].grade + 3 }
    FROM Families f



#### SelectMany 運算子
語法為 `input.SelectMany(x => f(x))`，其中 `f` 是傳回集合類型的純量運算式。

**LINQ Lambda 運算式**

    input.SelectMany(family => family.children);

**SQL** 

    SELECT VALUE child
    FROM child IN Families.children



#### Where 運算子
語法為 `input.Where(x => f(x))`，其中 `f` 是傳回布林值的純量運算式。

**LINQ Lambda 運算式**

    input.Where(family=> family.parents[0].familyName == "Smith");

**SQL** 

    SELECT *
    FROM Families f
    WHERE f.parents[0].familyName = "Smith" 



**LINQ Lambda 運算式**

    input.Where(
        family => family.parents[0].familyName == "Smith" && 
        family.children[0].grade < 3);

**SQL** 

    SELECT *
    FROM Families f
    WHERE f.parents[0].familyName = "Smith"
    AND f.children[0].grade < 3


### 複合 SQL 查詢
您可以包含上述的運算子，以形成功能更強大的查詢。 由於 DocumentDB 支援巢狀集合，因此這個組合可以是串連或巢狀形式。

#### 串連 

語法為 `input(.|.SelectMany())(.Select()|.Where())*`。 串連查詢的開頭可以是選用的 `SelectMany` 查詢，其後接著多個 `Select` 或 `Where` 運算子。


**LINQ Lambda 運算式**

    input.Select(family=>family.parents[0])
        .Where(familyName == "Smith");

**SQL**

    SELECT *
    FROM Families f
    WHERE f.parents[0].familyName = "Smith"



**LINQ Lambda 運算式**

    input.Where(family => family.children[0].grade > 3)
        .Select(family => family.parents[0].familyName);

**SQL** 

    SELECT VALUE f.parents[0].familyName
    FROM Families f
    WHERE f.children[0].grade > 3



**LINQ Lambda 運算式**

    input.Select(family => new { grade=family.children[0].grade}).
        Where(anon=> anon.grade < 3);
            
**SQL** 

    SELECT *
    FROM Families f
    WHERE ({grade: f.children[0].grade}.grade > 3)



**LINQ Lambda 運算式**

    input.SelectMany(family => family.parents)
        .Where(parent => parents.familyName == "Smith");

**SQL** 

    SELECT *
    FROM p IN Families.parents
    WHERE p.familyName = "Smith"



#### 巢狀

語法為 `input.SelectMany(x=>x.Q())`，其中 Q 是 `Select`、`SelectMany` 或 `Where` 運算子。

在巢狀查詢中，會將內部查詢套用至外部集合的每個項目。 其中一個重要功能是內部查詢可以參照外部集合中項目的欄位 (例如自我聯結)。

**LINQ Lambda 運算式**

    input.SelectMany(family=> 
        family.parents.Select(p => p.familyName));

**SQL** 

    SELECT VALUE p.familyName
    FROM Families f
    JOIN p IN f.parents


**LINQ Lambda 運算式**

    input.SelectMany(family => 
        family.children.Where(child => child.familyName == "Jeff"));
            
**SQL** 

    SELECT *
    FROM Families f
    JOIN c IN f.children
    WHERE c.familyName = "Jeff"



**LINQ Lambda 運算式**
            
    input.SelectMany(family => family.children.Where(
        child => child.familyName == family.parents[0].familyName));

**SQL** 

    SELECT *
    FROM Families f
    JOIN c IN f.children
    WHERE c.familyName = f.parents[0].familyName


## 執行 SQL 查詢
DocumentDB 公開資源的方式是透過 REST API，此 API 可供任何能發出 HTTP/HTTPS 要求的語言呼叫。 此外，DocumentDB 還為數種熱門語言 (例如 .NET、Node.js、JavaScript 和 Python) 提供程式設計程式庫。 REST API 和各種程式庫都支援透過 SQL 進行查詢。 .NET SDK 除了 SQL 之外，也支援 LINQ 查詢。

下列範例顯示如何建立查詢，並針對 DocumentDB 資料庫帳戶提交它。

### REST API
DocumentDB 提供透過 HTTP 的開放 RESTful 程式設計模型。 可以使用 Azure 訂用帳戶佈建資料庫帳戶。 DocumentDB 資源模型包含某個資料庫帳戶，每個都是可利用邏輯和穩定 URI 來定址的資源集合。 在本文件中，一組資源稱為摘要。 資料庫帳戶包含一組資料庫，而每個資料庫都包含多個集合，且各集合因此會包含文件、UDF 和其他資源類型。

與這些資源的基本互動模型是透過具有其標準解譯的 HTTP 動詞命令 GET、PUT、POST 和 DELETE。 POST 動詞命令用於建立新的資源、執行預存程序或發出 DocumentDB 查詢。 查詢一律是唯讀作業，而且沒有任何副作用。

下列範例示範針對集合進行之 DocumentDB 查詢的 POST，此集合包含我們到目前為止檢閱過的兩個範例文件。 查詢具有 JSON 名稱屬性的簡單篩選。 請注意 `x-ms-documentdb-isquery` 和內容類型的用法：`application/query+json` 標頭表示該作業是一項查詢。


**要求**

    POST https://<REST URI>/docs HTTP/1.1
    ...
    x-ms-documentdb-isquery: True
    Content-Type: application/query+json

    {      
        "query": "SELECT * FROM Families f WHERE f.id = @familyId",     
        "parameters": [          
            {"name": "@familyId", "value": "AndersenFamily"}         
        ] 
    }
    

**結果**

    HTTP/1.1 200 Ok
    x-ms-activity-id: 8b4678fa-a947-47d3-8dd3-549a40da6eed
    x-ms-item-count: 1
    x-ms-request-charge: 0.32
    
    <indented for readability, results highlighted>
    
    {  
       "_rid":"u1NXANcKogE=",
       "Documents":[  
          {  
             "id":"AndersenFamily",
             "lastName":"Andersen",
             "parents":[  
                {  
                   "firstName":"Thomas"
                },
                {  
                   "firstName":"Mary Kay"
                }
             ],
             "children":[  
                {  
                   "firstName":"Henriette Thaulow",
                   "gender":"female",
                   "grade":5,
                   "pets":[  
                      {  
                         "givenName":"Fluffy"
                      }
                   ]
                }
             ],
             "address":{  
                "state":"WA",
                "county":"King",
                "city":"seattle"
             },
             "_rid":"u1NXANcKogEcAAAAAAAAAA==",
             "_ts":1407691744,
             "_self":"dbs\/u1NXAA==\/colls\/u1NXANcKogE=\/docs\/u1NXANcKogEcAAAAAAAAAA==\/",
             "_etag":"00002b00-0000-0000-0000-53e7abe00000",
             "_attachments":"_attachments\/"
          }
       ],
       "count":1
    }


第二個範例顯示透過聯結傳回多個結果的較複雜查詢。

**要求**

    POST https://<REST URI>/docs HTTP/1.1
    ...
    x-ms-documentdb-isquery: True
    Content-Type: application/query+json
    
    {      
        "query": "SELECT 
                     f.id AS familyName, 
                     c.givenName AS childGivenName, 
                     c.firstName AS childFirstName, 
                     p.givenName AS petName 
                  FROM Families f 
                  JOIN c IN f.children 
                  JOIN p in c.pets",     
        "parameters": [] 
    }


**結果**

    HTTP/1.1 200 Ok
    x-ms-activity-id: 568f34e3-5695-44d3-9b7d-62f8b83e509d
    x-ms-item-count: 1
    x-ms-request-charge: 7.84
    
    <indented for readability, results highlighted>
    
    {  
       "_rid":"u1NXANcKogE=",
       "Documents":[  
          {  
             "familyName":"AndersenFamily",
             "childFirstName":"Henriette Thaulow",
             "petName":"Fluffy"
          },
          {  
             "familyName":"WakefieldFamily",
             "childGivenName":"Jesse",
             "petName":"Goofy"
          },
          {  
             "familyName":"WakefieldFamily",
             "childGivenName":"Jesse",
             "petName":"Shadow"
          }
       ],
       "count":3
    }


如果查詢的結果無法放入結果的單一頁面內，則 REST API 會透過 `x-ms-continuation-token` 傳回接續 Token。 用戶端可以透過在後續結果中包括標頭，以將結果分頁。 每頁的結果數目也可以透過 `x-ms-max-item-count` 數字標頭來控制。

若要管理查詢的資料一致性原則，請使用 `x-ms-consistency-level` 標頭 (例如，所有 REST API 要求)。 針對工作階段一致性，也需要在查詢要求中回應最新的 `x-ms-session-token` Cookie 標頭。 請注意，所查詢集合的索引原則也可能會影響查詢結果的一致性。 運用預設索引原則設定，集合的索引一律會具有最新文件內容，而且查詢結果會符合針對資料所選擇的一致性。 如果編索引原則放寬為 Lazy，則查詢可能會傳回過時的結果。 如需詳細資訊，請參閱 [DocumentDB 一致性層級] [consistency-levels]。

如果集合上所設定的索引原則無法支援指定的查詢，則 DocumentDB 伺服器會傳回 400「不正確的要求」。 針對範圍查詢 (針對雜湊 (相等) 查閱所設定的路徑) 以及明確地排除不進行編製索引的路徑，會傳回此訊息。 `x-ms-documentdb-query-enable-scan` 標頭可以指定成允許查詢在無法使用索引時執行掃描。

### C# (.NET) SDK
.NET SDK 支援 LINQ 和 SQL 查詢。 下列範例顯示如何執行稍早在本文件中介紹的簡單篩選查詢。


    foreach (var family in client.CreateDocumentQuery(collectionLink, 
        "SELECT * FROM Families f WHERE f.id = \"AndersenFamily\""))
    {
        Console.WriteLine("\tRead {0} from SQL", family);
    }
    
    SqlQuerySpec query = new SqlQuerySpec("SELECT * FROM Families f WHERE f.id = @familyId");
    query.Parameters = new SqlParameterCollection();
    query.Parameters.Add(new SqlParameter("@familyId", "AndersenFamily"));

    foreach (var family in client.CreateDocumentQuery(collectionLink, query))
    {
        Console.WriteLine("\tRead {0} from parameterized SQL", family);
    }

    foreach (var family in (
        from f in client.CreateDocumentQuery(collectionLink)
        where f.Id == "AndersenFamily"
        select f))
    {
        Console.WriteLine("\tRead {0} from LINQ query", family);
    }
    
    foreach (var family in client.CreateDocumentQuery(collectionLink)
        .Where(f => f.Id == "AndersenFamily")
        .Select(f => f))
    {
        Console.WriteLine("\tRead {0} from LINQ lambda", family);
    }


此範例會比較每個文件內的兩個屬性是否相等，以及使用匿名投射。 


    foreach (var family in client.CreateDocumentQuery(collectionLink,
        @"SELECT {""Name"": f.id, ""City"":f.address.city} AS Family 
        FROM Families f 
        WHERE f.address.city = f.address.state"))
    {
        Console.WriteLine("\tRead {0} from SQL", family);
    }
    
    foreach (var family in (
        from f in client.CreateDocumentQuery<Family>(collectionLink)
        where f.address.city == f.address.state
        select new { Name = f.Id, City = f.address.city }))
    {
        Console.WriteLine("\tRead {0} from LINQ query", family);
    }
    
    foreach (var family in
        client.CreateDocumentQuery<Family>(collectionLink)
        .Where(f => f.address.city == f.address.state)
        .Select(f => new { Name = f.Id, City = f.address.city }))
    {
        Console.WriteLine("\tRead {0} from LINQ lambda", family);
    }


下一個範例顯示聯結 (透過 LINQ SelectMany 表示)。


    foreach (var pet in client.CreateDocumentQuery(collectionLink,
          @"SELECT p
            FROM Families f 
                 JOIN c IN f.children 
                 JOIN p in c.pets 
            WHERE p.givenName = ""Shadow"""))
    {
        Console.WriteLine("\tRead {0} from SQL", pet);
    }
    
    // Equivalent in Lambda expressions
    foreach (var pet in
        client.CreateDocumentQuery<Family>(collectionLink)
        .SelectMany(f => f.children)
        .SelectMany(c => c.pets)
        .Where(p => p.givenName == "Shadow"))
    {
        Console.WriteLine("\tRead {0} from LINQ lambda", pet);
    }



.NET 用戶端會在 foreach 區塊中自動逐一查看查詢結果的所有頁面 (如上所示)。 .NET SDK 中也提供 REST API 小節所介紹的查詢選項，方法是在 CreateDocumentQuery 方法中使用 `FeedOptions` 和 `FeedResponse` 類別。 頁數可以透過 `MaxItemCount` 設定來控制。 

開發人員可以明確地控制分頁，藉由建立 `IDocumentQueryable` 使用 `IQueryable` 物件，然後藉由讀取` ResponseContinuationToken` 值，並將它們傳回為 `RequestContinuationToken` 中 `FeedOptions`。 `EnableScanInQuery` 可以將設定的索引原則無法支援查詢時啟用掃描。

請參閱 [DocumentDB.NET 範例](https://github.com/Azure/azure-documentdb-net) 更多包含查詢的範例。 

### JavaScript 伺服器端 API 
DocumentDB 提供一個程式設計模型，以使用預存程序和觸發程序，直接對集合執行 JavaScript 型應用程式邏輯。 在集合層級註冊的 JavaScript 邏輯接著可以對給定集合之文件的作業發出資料庫作業。 這些作業會包裝在環境 ACID 交易中。

下列範例示範如何在 JavaScript 伺服器 API 中使用 queryDocuments，從預存程序和觸發程序內發出查詢。


    function businessLogic(name, author) {
        var context = getContext();
        var collectionManager = context.getCollection();
        var collectionLink = collectionManager.getSelfLink()
    
        // create a new document.
        collectionManager.createDocument(collectionLink,
            { name: name, author: author },
            function (err, documentCreated) {
                if (err) throw new Error(err.message);
    
                // filter documents by author
                var filterQuery = "SELECT * from root r WHERE r.author = 'George R.'";
                collectionManager.queryDocuments(collectionLink,
                    filterQuery,
                    function (err, matchingDocuments) {
                        if (err) throw new Error(err.message);
    context.getResponse().setBody(matchingDocuments.length);
    
                        // Replace the author name for all documents that satisfied the query.
                        for (var i = 0; i < matchingDocuments.length; i++) {
                            matchingDocuments[i].author = "George R. R. Martin";
                            // we don't need to execute a callback because they are in parallel
                            collectionManager.replaceDocument(matchingDocuments[i]._self,
                                matchingDocuments[i]);
                        }
                    })
            });
    }


##參考
1.  [Azure DocumentDB 簡介][introduction]
2.  [DocumentDB SQL 規格](http://go.microsoft.com/fwlink/p/?LinkID=510612)
3.  [DocumentDB .NET 範例](https://github.com/Azure/azure-documentdb-net)
4.  [DocumentDB 一致性層級][consistency-levels]
5.  ANSI SQL 2011 [http://www.iso.org/iso/iso_catalogue/catalogue_tc/catalogue_detail.htm?csnumber=53681](http://www.iso.org/iso/iso_catalogue/catalogue_tc/catalogue_detail.htm?csnumber=53681)
6.  JSON [http://json.org/](http://json.org/)
7.  Javascript 規格 [http://www.ecma-international.org/publications/standards/Ecma-262.htm](http://www.ecma-international.org/publications/standards/Ecma-262.htm) 
8.  LINQ [http://msdn.microsoft.com/library/bb308959.aspx](http://msdn.microsoft.com/library/bb308959.aspx) 
9.  大型資料庫的查詢評估技術 [http://dl.acm.org/citation.cfm?id=152611](http://dl.acm.org/citation.cfm?id=152611)
10. 平行關聯式資料庫系統中的查詢處理 (IEEE Computer Society Press，1994 年)
11. Lu, Ooi, Tan, 平行關聯式資料庫系統中的查詢處理 (IEEE Computer Society Press，1994 年)。
12. Christopher Olston、Benjamin Reed、Utkarsh Srivastava、Ravi Kumar、Andrew Tomkins：Pig Latin：資料處理的 Not-So-Foreign 語言，SIGMOD 2008。
13.     G. Graefe。 The Cascades framework for query optimization. IEEE Data Eng. Bull., 18(3): 1995.


[1]: ./media/documentdb-sql-query/sql-query1.png
[introduction]: documentdb-introduction.md
[consistency-levels]: documentdb-consistency-levels.md
 


