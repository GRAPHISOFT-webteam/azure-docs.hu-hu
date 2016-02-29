<properties
   pageTitle="如何連接到資料來源"
   description="操作說明文章著重在說明如何連接到使用 Azure 資料目錄找到的資料來源。"
   services="data-catalog"
   documentationCenter=""
   authors="steelanddata"
   manager="NA"
   editor=""
   tags=""/>
<tags
   ms.service="data-catalog"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="data-catalog"
   ms.date="11/10/2015"
   ms.author="maroche"/>


# 如何連接到資料來源

## 簡介
**Microsoft Azure 資料目錄** 是完全受管理的雲端服務，可作為的註冊系統和探索企業資料來源系統。 換句話說， **Azure 資料目錄** 的重點，協助使用者探索、 了解，以及使用資料來源，並可協助組織從現有的資料獲得更多價值。 這個背景的重點就在於使用資料，也就是使用者探索資料來源並了解其用途，然後連接到資料來源將使用其資料。

##資料來源位置
資料來源在註冊期間， **Azure 資料目錄** 收到有關資料來源的中繼資料。 此中繼資料包含資料來源位置的詳細資料。 位置的詳細資料會因資料來源而異，但永遠會包含連接所需的資訊。 例如，SQL Server 資料表的位置包含伺服器名稱、資料庫名稱、結構描述名稱和資料表名稱，而 SQL Server Reporting Services 報表的位置包含伺服器名稱和報表的路徑。 其他資料來源類型的位置，則會反映結構與來源系統的功能。

##整合式用戶端工具
連接到資料來源的最簡單方式是使用 「 在中開啟。...」 在功能表 **Azure 資料目錄** 入口網站。 這個功能表會顯示一份選項清單，可供您連接到所選取的資料資產。
使用預設的並排顯示檢視時，此功能表位於每個磚上。

 ![從資料資產磚以 Excel 開啟 SQL Server 資料表](./media/data-catalog-how-to-connect/data-catalog-how-to-connect1.png)

使用清單檢視時，此功能表位於入口網站視窗頂端的搜尋列中。

 ![從搜尋列以報告管理員開啟 SQL Server Reporting Services 報表](./media/data-catalog-how-to-connect/data-catalog-how-to-connect2.png)

##您的資料與工具
功能表中可用的選項取決於目前選取的資料資產的類型。 當然，並非所有可能的工具將會包含在 「 在中開啟。...」 功能表上，但它並又容易連接至資料來源使用任何用戶端工具。 在 [選取資料資產時 **Azure 資料目錄** 入口網站的完整位置會顯示在 [屬性] 窗格中。

 ![SQL Server 資料表的連接資訊](./media/data-catalog-how-to-connect/data-catalog-how-to-connect3.png)

連接資訊的詳細資料會因資料來源的類型而異，但是入口網站中所包含的資訊提供您所需的一切，讓您可以使用任何用戶端工具連接到資料來源。 使用者可以將他們發現使用的資料來源的連線詳細資料複製 **Azure 資料目錄**, ，好讓他們能夠使用其選擇的工具中的資料。

##連接和資料來源的權限
雖然 **Azure 資料目錄** 可讓資料來源設定為可探索，存取資料本身會保持在資料來源擁有者或系統管理員控制之下。 探索資料來源中的 **Azure 資料目錄** 並不提供使用者存取資料來源本身的任何權限。

若要讓使用者更輕鬆地探索資料來源，但不要有存取其資料的權限，使用者可以在加註資料來源時於 [要求存取] 屬性中提供資訊。 這裡所提供的資訊會和入口網站中的資料來源位置資訊一起呈現，包括連結的程序或取得資料來源存取權的聯絡點。

 ![具有所提供之要求存取指示的連接資訊](./media/data-catalog-how-to-connect/data-catalog-how-to-connect4.png)

##摘要
註冊資料來源與 **Azure 資料目錄** 將從資料來源的結構化和描述性中繼資料複製到目錄服務，讓該資料成為可探索。 資料來源註冊，並後發現，使用者可以連線到資料來源從 **Azure 資料目錄** 入口網站 」 中開啟。..."」 功能表或使用所選擇的資料的工具。

