<properties
    pageTitle="將 Web App 資源移動到另一個資源群組"
    description="描述您可以將 Web Apps 與 App Service 從一個資源群組移至另一個的案例。"
    services="app-service"
    documentationCenter=""
    authors="ZainRizvi"
    manager="wpickett"
    editor=""/>

<tags
    ms.service="app-service"
    ms.workload="web"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="10/29/2015"
    ms.author="zarizvi"/>
    
# 支援的移動組態

您可以將 Azure Web 應用程式資源使用移動 [ARM 移動資源 Api](../resource-group-move-resources.md)。

Azure Web Apps 目前支援下列移動案例：

* 將整個資源群組內容 (Web App、App Service 方案與憑證) 移動到另一個資源群組 
    * 注意：目的地資源群組不能包含此案例中的任何 Microsoft.Web 資源
* 將個別 Web 應用程式移動到不同的資源群組，同時也在其目前的 App Service 方案中裝載它們 (App Service 方案保留在舊的資源群組中)


