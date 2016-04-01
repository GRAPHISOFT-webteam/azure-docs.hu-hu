<properties
    pageTitle="在 App Service 環境中建立 Web 應用程式"
    description="了解如何在 App Service 環境中建立 Web 應用程式和 App Service 方案"
    services="app-service"
    documentationCenter=""
    authors="ccompy"
    manager="stefsch"
    editor=""/>

<tags
    ms.service="app-service"
    ms.workload="web"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="get-started-article" 
    ms.date="10/26/2015"
    ms.author="ccompy"/>

# 在 App Service 環境中建立 Web 應用程式

## 概觀

本教學課程示範如何建立 web 應用程式和應用程式服務計劃中 [App Service 環境](app-service-app-service-environment-intro.md) (ASE)。 

> [AZURE.NOTE] 如果您想要了解如何建立 web 應用程式，但不必在 App Service 環境，請參閱 [建立.NET web 應用程式](web-sites-dotnet-get-started.md) 或其中一個相關的教學課程的其他語言和架構。

## 必要條件

本教學課程假設您已建立 App Service 環境。 如果尚未執行此作業，請參閱 [建立 App Service 環境](app-service-web-how-to-create-an-app-service-environment.md)。 

## 建立 Web 應用程式

1. 在 [Azure 入口網站](https://portal.azure.com/), ，按一下 [ **新增 > Web + 行動 > Web 應用程式**。 

    ![][1]

2. 選取您的訂閱。  

    如果您有多個訂用帳戶，請注意，若要在您的 App Service 環境中建立應用程式，必須使用您在建立環境時所使用訂用帳戶來建立。 

3. 選取或建立資源群組。

    *資源群組* 可讓您管理相關的 Azure 資源當做一個單位，有助於建立時 *角色型存取控制* (RBAC) 規則針對您的應用程式。 如需詳細資訊，請參閱 [管理您的 Azure 資源][ResourceGroups]。 

4. 選取或建立 App Service 方案。

    *應用程式服務計劃* 是受管理的 web 應用程式。  當您選取價格時，支付的價格會套用到 App Service 方案，而非個別的應用程式。 若要相應增加 Web 應用程式的執行個體數目，您可相應增加 App Service 方案的執行個體，這會影響該方案中的所有 Web 應用程式。  方案中的某些功能 (例如網站位置或 VNET 整合) 也有數量限制。  如需詳細資訊，請參閱 [Azure App Service 方案概觀](../app-service/azure-web-sites-web-hosting-plans-in-depth-overview.md)

    您可以藉由查看方案名稱下加註的位置，來識別 ASE 中的 App Service 方案。  

    ![][5]

    如果您想要使用已存在於 App Service 環境中的 App Service 方案，請選取該方案。 如果您想要建立新的應用程式服務方案，請參閱本教學課程的下一節 [在 App Service 環境中建立應用程式服務方案](#createplan)。

5. 輸入您的 web 應用程式的名稱，然後按一下 **建立**。 

    您的 Web 應用程式名稱在 Azure App Service 中必須是唯一的。  這表示如果您要建立名為「thisismywebapp」的 Web 應用程式，則目前在 Azure App Service 中就不能有任何其他名為「thisismywebapp」的 Web 應用程式。  

    在 ASE 中，Web 應用程式的 URL 是：

    [*sitename*]。[*的 App Service 環境名稱*]。.p.azurewebsites.net

    而不是

    [*sitename*]。 azurewebsites.net

## <a name="createplan"></a> 建立 App Service 方案

當您在 App Service 環境中建立 App Service 方案時，您的背景工作角色選擇會因為在 ASE 中沒有共用的背景工作角色而有所不同。  您必須使用的背景工作角色也就是由系統管理員配置給 ASE 的背景工作角色。  這代表在建立新的方案時，配置給 ASE 背景工作集區的背景工作角色數目，必須超過該背景工作集區中所有方案的執行個體總數。  如果您的 ASE 背景工作集區中沒有足夠的背景工作角色來建立方案，則您需要與 ASE 系統管理員合作來新增背景工作角色。

而 App Service 環境所裝載的 App Service 方案還有另一項差異，那就是缺少價格選取項目。  當您有 App Service 環境時，您會支付系統所使用的計算資源費用，但該環境中的方案不會有附加的費用。  當您建立 App Service 方案時，您通常會選取決定費率的價格方案。  App Service 環境基本上是您可以在其中建立內容的私人位置。  您可支付環境費用，而非裝載您的內容。

下列指示說明如何在您依照本教學課程的上一節所說明來建立 Web 應用程式時，建立 App Service 方案。

1. 按一下 [ **新建** 計劃選擇 UI 中，並提供您計劃的名稱，就像平常一樣 ASE 之外。

2. 選取您想要從位置選擇器中使用的 ASE。

    因為 App Service 環境基本上是專用部署位置，所以它會顯示在 [位置] 下。 

    ![][2]

    在位置選擇器中選取 ASE 之後，App Service 方案建立 UI 將會更新。  位置現在會顯示 ASE 系統的位置及其所在區域，而背景工作集區選擇器會取代價格方案選擇器。  

    ![][3]

### 選取背景工作集區

通常在 Azure App Service 中和 App Service 環境以外的地方，專用價格方案通常會有 3 種計算大小可供選擇。  同樣地，對於 ASE 您最多可以定義 3 個背景工作集區，並指定用於該背景工作集區的計算大小。  這是什麼意思 ASE 的租用戶為改用選取與您的應用程式服務計劃的運算大小的定價方案時，您可以選取所謂 *背景工作集區*。  

背景工作角色集區選取 UI 會在名稱下方顯示該背景工作集區角色使用的運算大小。  可用數量是指有多少運算執行個體可使用於該集區。  總計集區實際上可能有超過這個數字的執行個體，但這個值只是指未使用的數量。  如果您需要調整，請參閱 App Service 環境以加入更多計算資源 [設定 App Service 環境](app-service-web-configure-an-app-service-environment.md)。

![][4]

在此範例中，您只會看到兩個可用的背景工作集區。 這是因為 ASE 系統管理員只會將主機配置到這兩個背景工作角色集區中。  第三個集區會在有 VM 配置到其中時出現。   

## 建立 Web 應用程式之後

在 ASE 中執行 Web 應用程式及管理 App Service 方案時，有幾件事需要列入考量。  

如先前所述，ASE 的擁有者必須負責系統大小，因此他們也必須負責確保有足夠的容量來裝載所需的 App Service 方案。 如果沒有可用的背景工作角色，您將無法建立您的 App Service 方案。  也就無法相應增加您的 Web 應用程式。  如果您需要更多執行個體，您必須讓您的 App Service 環境的系統管理員新增更多的背景工作。

當您建立 Web 應用程式和 App Service 方案之後，最好能夠相應增加該方案的內容。  在 ASE 中，您一定需要至少 2 個 App Service 方案執行個體，才能為您的應用程式提供容錯功能。  在 ASE 中調整 App Service 方案的方式，就跟一般透過 App Service 方案 UI 來進行的方式相同。  如需有關調整 [如何在 App Service 環境中調整 web 應用程式](app-service-web-scale-a-web-app-in-an-app-service-environment.md)

<!--Image references-->
[1]: ./media/app-service-web-how-to-create-a-web-app-in-an-ase/createaspnewwebapp.png
[2]: ./media/app-service-web-how-to-create-a-web-app-in-an-ase/createasplocation.png
[3]: ./media/app-service-web-how-to-create-a-web-app-in-an-ase/createaspselected.png
[4]: ./media/app-service-web-how-to-create-a-web-app-in-an-ase/createaspworkerpool.png
[5]: ./media/app-service-web-how-to-create-a-web-app-in-an-ase/selectaspinase.png

<!--Links-->
[WhatisASE]: http://azure.microsoft.com/documentation/articles/app-service-app-service-environment-intro/
[Appserviceplans]: http://azure.microsoft.com/documentation/articles/azure-web-sites-web-hosting-plans-in-depth-overview/
[HowtoCreateASE]: http://azure.microsoft.com/documentation/articles/app-service-web-how-to-create-an-app-service-environment/
[HowtoScale]: http://azure.microsoft.com/documentation/articles/app-service-web-scale-a-web-app-in-an-app-service-environment
[HowtoConfigureASE]: http://azure.microsoft.com/documentation/articles/app-service-web-configure-an-app-service-environment
[ResourceGroups]: http://azure.microsoft.com/documentation/articles/resource-group-portal/
[AzurePowershell]: http://azure.microsoft.com/documentation/articles/powershell-install-configure/


