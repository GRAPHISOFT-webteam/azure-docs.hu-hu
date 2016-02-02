<properties
    pageTitle="在 Eclipse 中顯示 Azure Libraries for Java 封裝的 Javadoc 內容"
    description="如何在 Eclipse 中顯示 Azure Libraries 的 Javadoc 內容。"
    services=""
    documentationCenter="java"
    authors="rmcmurray"
    manager="wpickett"
    editor=""/>

<tags
    ms.service="multiple"
    ms.workload="na"
    ms.tgt_pltfrm="multiple"
    ms.devlang="Java"
    ms.topic="article"
    ms.date="11/30/2015" 
    ms.author="robmcm"/>




# 在 Eclipse 中顯示 Azure Libraries for Java 封裝的 Javadoc 內容

Javadoc 內容與 Azure Libraries for Java 產生關聯時，即可在 Eclipse 環境中檢視 Azure Libraries for Java 的 Javadoc 內容。 下列步驟示範該如何在 Eclipse 中使用此功能。

此程序假設您已將 Azure Library for Java 新增至您的組建路徑。

## 在 Eclipse 中顯示 Azure Libraries for Java 的 Javadoc 內容

* 於 Eclipse 的 [專案總管] 中，在您專案的 [**所參考的資料庫**] 區段中，開啟 Azure library for Java JAR 的內容功能表。 例如，**microsoft-windowsazure-api-0.1.0.jar** (視您所安裝的版本而定，版本號碼可能會有所不同)。
* 按一下 [內容]****。
* 在 [**內容**] 對話方塊的左側窗格中，按一下 [**Javadoc 位置**]。 隨即顯示 [**Javadoc 位置**] 對話方塊。
* 您可以指定 **Javadoc URL**或**封存檔案中的 Javadoc**。
    * 
    * 若選擇使用**封存檔案中的 Javadoc**，可以指定外部檔案或工作區檔案。
    選擇後視需要進行瀏覽/驗證。
    ![][ic553487]
* *選擇性步驟*：按一下 [**驗證**]。 這裡可能會顯示 Javadoc JAR 可能發生的問題。
* 按一下 [確定]****。

與程式庫產生關聯後，Javadoc 內容隨即會顯示在 Eclipse 整合式開發環境 (IDE) 中。

![][ic553488]

## 另請參閱














[azure java developer center]: http://go.microsoft.com/fwlink/?LinkID=699547 
[azure toolkit for eclipse]: http://go.microsoft.com/fwlink/?LinkID=699529 
[creating a hello world application for azure in eclipse]: http://go.microsoft.com/fwlink/?LinkID=699533 
[installing the azure toolkit for eclipse]: http://go.microsoft.com/fwlink/?LinkId=699546 
[ic553487]: ./media/azure-toolkit-for-eclipse-displaying-javadoc-content-for-azure-libraries/ic553487.png 
[ic553488]: ./media/azure-toolkit-for-eclipse-displaying-javadoc-content-for-azure-libraries/ic553488.png 

