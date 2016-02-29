<properties
   pageTitle="Multi-Geo 說明文件 | Microsoft Azure"
   description="了解如何在美國中南部 (SCUS) Azure 區域以外的 Azure 區域中建立工作區並發佈 Web 服務。"
   services="machine-learning"
   documentationCenter=""
   authors="tedway"
   manager="paulettm"
   editor="rmca14"
   tags=""/>

<tags
   ms.service="machine-learning"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="11/23/2015"
   ms.author="tedway; neerajkh"/>

# Multi-Geo 說明文件

本文將說明如何在其他 Azure 區域中建立工作區及發佈 Web 服務。

## 建立工作區

1. 登入 Azure 傳統入口網站。

2.  按一下 [ **+ 新增** > **DATA SERVICES** > **機器學習** > **快速建立**。  在 **位置** 選取另一個區域，例如 **東南亞**。
![多個地理區域的說明圖 1][] 1
3. 選取工作區]，然後按一下 **登入 ML Studio**。
![多個地理區域的說明圖 2][] 2

4. 您現在已於其他地區中建立工作區，您可如同任何其他工作區一樣使用。 若要切換工作區，請查看您螢幕的右上方。 按一下下拉式清單，選取 [區域]，然後選取 [工作區]。 一切都是本機工作區的區域。比方說，所有您從工作區建立的 web 服務會在工作區位於相同的區域。
![多個地理區域的說明圖 3][] 3

## 從 [資源庫] 開啟實驗

如果從 [資源庫] 開啟實驗，您也可以選取要複製實驗的目標區域。

![多個地理區域的說明圖 4][] 4a

## Web 服務管理

若要以程式設計方式管理 web 服務，例如重新定型，使用特定地區的地址: **https://asiasoutheast.management.azureml.net**

### 注意事項

1.  您只能複製屬於相同區域工作區之間的實驗。 在未來，我們將啟用跨多個區域工作區之間實驗的複製。
2.  區域選取器一次只會顯示一個區域的工作區。 在未來，您可查看您可同時存取所有區域之工作區的完整清單。  
3.  免費工作區或 [來賓存取] (匿名) 工作區會在美國中南部建立並裝載。 在未來，您可以在您選擇的區域中建立 [免費/來賓存取] 工作區。  
4.  從位於東南亞的工作區部署的 Web 服務也會裝載於東南亞。 在未來，於單一區域中建立實驗，以及將產生的 Web 服務端點部署到不同區域，將會更有彈性。  

## 詳細資訊

在詢問問題 [Azure Machine Learning 論壇](https://social.msdn.microsoft.com/Forums/azure/home?forum=MachineLearning)。

<!--Image references-->
[1]: ./media/machine-learning-multi-geo/multi-geo_1.png
[2]: ./media/machine-learning-multi-geo/multi-geo_2.png
[3]: ./media/machine-learning-multi-geo/multi-geo_3.png
[4a]: ./media/machine-learning-multi-geo/multi-geo_4a.png

