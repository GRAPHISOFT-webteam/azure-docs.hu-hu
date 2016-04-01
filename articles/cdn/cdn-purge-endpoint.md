<properties 
    pageTitle="清除 Azure CDN 端點" 
    description="了解如何清除 CDN 端點的所有快取內容" 
    services="cdn" 
    documentationCenter=".NET" 
    authors="camsoper" 
    manager="dwrede" 
    editor=""/>

<tags 
    ms.service="cdn" 
    ms.workload="tbd" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="12/03/2015" 
    ms.author="casoper"/>
    
# 清除 Azure CDN 端點

## 概觀 

在資產的存留時間 (TTL) 到期前，Azure CDN 邊緣節點都會為資產進行快取。  資產的 TTL 到期之後，當用戶端從邊緣節點要求資產時，邊緣節點會建立資產新的更新的複本以服務用戶端的要求並儲存重新整理快取。

有時您可能想要從所有邊緣節點清除快取的內容，並強制它們全部擷取新的更新的資產。  可能是因為您的 Web 應用程式更新，或快速更新包含不正確資訊的資產。

本教學會逐步引導您清除端點的所有邊緣節點的資產。

## 逐步介紹

1. 在 [Azure 入口網站](http://portal.azure.com), ，瀏覽至包含您想要清除之端點的 CDN 設定檔。

2. 在 [CDN 設定檔] 刀鋒視窗中，按一下 [清除] 按鈕。
    
    ![CDN 設定檔刀鋒視窗](./media/cdn-purge-endpoint/cdn-profile-blade.png)
    
    會開啟 [清除] 刀鋒視窗。
    
    ![CDN 清除刀鋒視窗](./media/cdn-purge-endpoint/cdn-purge-blade.png)
    
3. 在 [清除] 刀鋒視窗中，從 [URL] 下拉式清單中選取希望清除的服務位址。

    ![清除表單](./media/cdn-purge-endpoint/cdn-purge-form.png)
    
    > [AZURE.NOTE] 您也可以取得 [清除] 刀鋒視窗按一下 **清除** CDN 端點] 分頁上的按鈕。  在此情況下， **URL** 欄位會預先填入該特定端點的服務位址。
    
4. 選取您希望從邊緣節點清除的資產。  如果您想要清除所有資產，按一下 [ **清除所有** 核取方塊。  否則，請輸入您想要清除每個資產的完整路徑 (例如， */pictures/kitten.png*) 中 **路徑** 文字方塊。

    > [AZURE.TIP] 多個 **路徑** 文字方塊輸入文字，讓您建立多個資產的清單之後，會出現。  按一下刪節號 (...) 按鈕可以將資產從清單刪除。
    >
    > 路徑必須為相對 URL。  星號 (*) 可做為萬用字元。  
    
5. 按一下 [ **清除** ] 按鈕。

    ![清除按鈕](./media/cdn-purge-endpoint/cdn-purge-button.png)
    

## 另請參閱
[Azure CDN REST API 參考資料 - 清除或預先載入端點](https://msdn.microsoft.com/library/mt634451.aspx)


