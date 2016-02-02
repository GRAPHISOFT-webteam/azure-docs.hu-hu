<properties
    pageTitle="步驟 3：建立新的 Machine Learning 實驗 | Microsoft Azure"
    description="開發預測解決方案逐步解說的步驟 3：在 Azure Machine Learning Studio 中建立新的定型實驗。"
    services="machine-learning"
    documentationCenter=""
    authors="garyericson"
    manager="paulettm"
    editor="cgronlun"/>

<tags
    ms.service="machine-learning"
    ms.workload="data-services"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="09/08/2015" 
    ms.author="garye"/>



# 逐步解說步驟 3：建立新的 Azure Machine Learning 實驗

這是逐步解說的第三個步驟 [開發預測方案使用 Azure ML](machine-learning-walkthrough-develop-predictive-solution.md)


1.  [建立 Machine Learning 工作區](machine-learning-walkthrough-1-create-ml-workspace.md)
2.  [上傳現有資料](machine-learning-walkthrough-2-upload-data.md)
3.  **建立新實驗**
4.  [訓練及評估模型](machine-learning-walkthrough-4-train-and-evaluate-models.md)
5.  [部署 web 服務](machine-learning-walkthrough-5-publish-web-service.md)
6.  [存取 web 服務](machine-learning-walkthrough-6-access-web-service.md)

----------


我們需要在 ML Studio 中建立新的實驗，並讓它使用我們上傳的資料集。

1.  在 ML Studio 中，按一下視窗底部的 [+新增]****。
2.  選取 [實驗]****，然後選取 [空白實驗]。 選取畫布頂端的預設實驗名稱，然後將它重新命名為有意義的名稱。
    > [AZURE.TIP] 在 [**屬性**] 窗格中為實驗填入**摘要**和**描述**是不錯的作法。 這些屬性會提供記錄實驗的機會，以便稍後查看它的人員能了解您的目標和方法。

3.  在實驗畫布左側的模組調色盤中，展開 [Saved Datasets]****。
4.  尋找您在**我的資料集**下建立的資料集並將它拖曳到畫布上。 您也可以在調色盤上方的 [搜尋]**** 方塊中輸入名稱，以尋找資料集。

## 準備資料

您可以按一下資料集的輸出連接埠，然後選取 [**檢視結果**]，來檢視整個資料集的前 100 列資料和部分統計資訊。 請注意，ML Studio 已識別每個資料行的資料類型。 它也會為資料行指定一般標題，因為資料檔案並未隨附資料行標題。

資料行標題並非必要，但這些標題可讓您更容易使用模型中的資料。 此外，當我們最終在 Web 服務中發佈此模型時，標題將有助於服務的使用者識別資料行。

我們可以新增使用的資料行標題 [中繼資料編輯器 ][metadata-editor] 模組。
[中繼資料編輯器 ][metadata-editor] 模組用來變更資料集相關聯的中繼資料。 透過這種方式，可提供資料行標題更易記的名稱。 若要這樣做，我們將引導 [中繼資料編輯器 ][metadata-editor] 對所有資料行，然後提供要加入資料行的名稱清單。

1.  在模組調色盤的 [搜尋]**** 方塊中，輸入 "metadata"。 您會看到 [中繼資料編輯器 ][metadata-editor] 模組清單中。
2.  按一下並拖曳 [中繼資料編輯器 ][metadata-editor] 模組拖曳到畫布上，把它放在資料集下方。
3.  連接至資料集 [中繼資料編輯器 ][metadata-editor]: 按一下資料集的輸出連接埠、 拖曳到的輸入連接埠 [中繼資料編輯器 ][metadata-editor], ，然後放開滑鼠按鈕。 即使您在畫布上移動資料集或模組，這兩者仍會保持連線。
4.  使用 [中繼資料編輯器 ][metadata-editor] 仍處於選取狀態，在 **屬性** 窗格右邊的畫布上，按一下 [ **啟動資料行選取器**。
5.  在 [**選取資料行**] 對話方塊中，將 [**開始於**] 欄位設定為 [所有資料行]。
6.  下方的資料列 **開始於** 可讓您包含或排除特定資料行 [中繼資料編輯器 ][metadata-editor] 修改。 由於我們想要修改所有資料行，因此請按一下資料列右側的減號 ("-") 刪除這個資料列。 對話方塊應該看起來像這樣:
    ![已選取所有資料行的資料行選取器][4]
7.  按一下 [**確定**] 核取記號。
8.  回到 [**屬性**] 窗格，找到 [**新增資料行名稱**] 參數。 在這個欄位中，輸入資料集中 21 個資料行的名稱清單，並以逗號加以分隔，且依資料行順序排序。 您可以從 UCI 網站上的資料集文件中取得資料行名稱，或為求簡便，您可以複製並貼上下列內容：


    支票帳戶的狀態、 持續時間，在幾個月、 信用記錄、 用途、 信用額度數量、 存款帳戶/債券、 存在就業自、 一篇速率可處置的收入、 個人狀態和性別、 其他 debtors，自目前居住地、 屬性、 年來，其他一篇計劃，外罩，現有的信用額度，工作，許多人提供維護、 電話、 外部的背景工作的存留期間的百分比表示信用風險

[屬性] 窗格將如下所示：

![Properties for Metadata Editor][1]
> [AZURE.TIP] 如果您想要確認資料行標題，執行實驗 (按一下 **執行** 實驗畫布下方)，按一下輸出連接埠 [中繼資料編輯器 ][metadata-editor] 模組，然後選取 **檢視結果**。 您可以用相同方式檢視任何模組的輸出，以檢視資料在實驗中的執行進度。

實驗目前看起來如下：

![Adding Metadata Editor][2]

## 建立訓練和測試資料集

實驗的下一個步驟，是產生將用來訓練及測試模型的個別資料集。 若要這樣做，我們使用 [分割 ][split] 模組。

1.  尋找 [分割 ][split] 模組，將它拖曳到畫布上，並將它連接到最後一個 [中繼資料編輯器 ][metadata-editor] 模組。
2.  根據預設，分割率會是 0.5，並且會設定 [Randomized split]**** 參數。 這表示資料有一半會透過一個連接埠的輸出的隨機 [分割 ][split] 模組，並另一半。 您可以調整這些參數和 [隨機種子]**** 參數，以變更訓練與評分資料之間的分割。 在此範例中，我們會將其保留為原狀。
    > [AZURE.TIP] 分割率基本上會決定有多少資料會透過左側輸出埠來輸出。 例如，如果您將分割率設為 0.7，則會有 70% 的資料透過左側連接埠輸出，30% 透過右側連接埠輸出。  

我們可以使用的輸出 [分割 ][split] 模組，但要讓我們選擇以左側的輸出作為訓練資料，右側輸出作為評分資料。

如 UCI 網站上所說明，將高信用風險誤判為低風險的成本，會比將低信用風險誤判為高風險的成本多出 5 倍。 為了說明這一點，我們將產生新資料集以反映此成本函數。 在此新資料集中，每個高風險範例都會複寫 5 次，而每個低風險範例則不複寫。

我們可以使用 R 程式碼來執行此複寫：

1.  找出並拖曳 [執行 R 指令碼 ][execute-r-script] 模組拖曳到實驗畫布，然後連接的左側的輸出連接埠 [分割 ][split] 模組的第一個輸入連接埠 ("Dataset1") [執行 R 指令碼 ][execute-r-script] 模組。
2.  在 [**屬性**] 窗格中，刪除 [**R 指令碼**] 參數中的預設文字，然後輸入此指令碼：

        dataset1 <- maml.mapInputPort(1)
        data.set<-dataset1[dataset1[,21]==1,]
        pos<-dataset1[dataset1[,21]==2,]
        for (i in 1:5) data.set<-rbind(data.set,pos)
        maml.mapOutputPort("data.set")



我們要做此一相同複寫作業的每個輸出 [分割 ][split] 模組使訓練和評分資料具有相同的成本調整。

1.  以滑鼠右鍵按一下 [執行 R 指令碼 ][execute-r-script] 模組，然後選取 **複製**。
2.  以滑鼠右鍵按一下實驗畫布，然後選取 [貼上]****。
3.  第一個輸入連接埠連接這個 [執行 R 指令碼 ][execute-r-script] 模組到右輸出連接埠 [分割 ][split] 模組。

> [AZURE.TIP] 「執行 R 指令碼」模組的複本會包含與原始模組相同的指令碼。 當您在畫布上複製並貼上模組時，複本將會保留原本的所有屬性。  
>
實驗此時看起來會如下所示：

![Adding Split module and R scripts][3]

如需有關您的實驗中使用 R 指令碼的詳細資訊，請參閱 [擴展您的經驗，透過 R](machine-learning-extend-your-experiment-with-r.md)。

* * 下一步: [訓練及評估模型](machine-learning-walkthrough-4-train-and-evaluate-models.md)* *




[1]: ./media/machine-learning-walkthrough-3-create-new-experiment/create1.png 
[2]: ./media/machine-learning-walkthrough-3-create-new-experiment/create2.png 
[3]: ./media/machine-learning-walkthrough-3-create-new-experiment/create3.png 
[4]: ./media/machine-learning-walkthrough-3-create-new-experiment/columnselector.png 
[execute-r-script]: https://msdn.microsoft.com/library/azure/30806023-392b-42e0-94d6-6b775a6e0fd5/ 
[metadata-editor]: https://msdn.microsoft.com/library/azure/370b6676-c11c-486f-bf73-35349f842a66/ 
[split]: https://msdn.microsoft.com/library/azure/70530644-c97a-4ab6-85f7-88bf30a8be5f/ 

