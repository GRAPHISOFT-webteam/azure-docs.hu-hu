<properties
   pageTitle="資源管理員和服務管理 (傳統) 部署模式 | Azure"
   description="了解資源管理員與傳統部署模型之間的差異"
   services="virtual-network"
   documentationCenter=""
   authors="telmosampaio"
   manager="carolz"
   editor=""
   tags="azure-resource-manager,azure-service-management"/>

<tags
   ms.service="virtual-network"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="12/07/2015"
   ms.author="telmos"/>


# Azure 部署模型

Azure 平台正在轉換。 不論您是 Azure 新手或已使用它數年，務必了解我們在此轉換期間對此平台所做的一些重要變更。

所有 Azure 資源都支援下列一或兩個部署模型：

- **資源管理員︰**這是 Azure 資源的最新部署模型。 大部分較新的資源都已支援這種部署模型，最後所有資源都會提供支援。

- **傳統︰**現在大部分現有的 Azure 資源都支援此模型。 加入至 Azure 的新資源將不支援此模型。

每個 Azure 資源的文件會詳述可用以建立該資源的服務模型。

## 為何這有關係？

有所關係的原因如下︰

- 您在這兩種模型下使用的 Azure 平台功能會有所不同。
- 個別 Azure 資源功能或行為在這兩種模型下會有所不同，或只存在於其中一種模型。 例如，平衡使用傳統部署模型所建立之虛擬機器的流量負載是「隱含的」**，因為虛擬機器是 Azure 雲端服務的成員，而雲端服務內的虛擬機器會自動平衡負載。 使用資源管理員建立的虛擬機器不是雲端服務的成員，而必須明確**建立個別的 Azure 負載平衡器資源，以平衡多部虛擬機器的流量負載。
- 在這兩種模型中，建立、設定及管理 Azure 資源的方式有所不同。
- 使用一種部署模型所建立的資源，不一定能與使用不同部署模型建立的資源交互操作。 例如，使用一種部署模型所建立的 Azure 虛擬機器只能連接至使用相同部署模型建立的 Azure 虛擬網路。

每個部署模型的基礎就是每個資源的應用程式設計介面 (API)。  開發人員可以撰寫程式碼，直接**與這些 API 互動。

不過，IT 專業人員通常會在網頁瀏覽器中使用圖形化入口網站、在 Windows 電腦上使用 Azure PowerShell Cmdlet，或在 Windows、OS X 或 Linux 電腦上使用 Azure 命令列介面 (CLI)，藉此與這些 API 間接**互動。 IT 專業人員所使用的這三種間接方法都會直接與 API 互動。 這表示 Azure 平台或資源引進新功能時，一律可先透過 API 直接取得，而在 API 可供使用後透過間接方法取得新資源和功能的支援。

下列各節說明如何透過三種間接方法，使用不同的部署模型來設定 Azure 資源。

## 入口網站

Azure 有兩個入口網站︰

- 它用來建立及設定可支援傳統部署模型的較舊 Azure 資源。 您無法使用它來建立或設定僅支援資源管理員的資源。
- 它可以用來建立及設定某些 Azure 資源。 您最終能使用它來建立和設定所有的 Azure 資源。 對於支援兩種部署模型的某些資源，此入口網站可用來建立及設定使用任何一種部署模型的資源。

某些資源和功能只可以在其中一個入口網站中建立及設定。 某些資源或功能還不能在任一個入口網站中建立或設定，而只能透過 PowerShell、CLI 或兩者進行設定。 每個 Azure 資源的文件會詳述可用以建立該資源的方法。

## PowerShell

某些資源和功能只可以在使用 PowerShell 或 CLI 加以建立及設定。 視資源而定，使用資源管理員 PowerShell Cmdlet 時，您可能會有兩個選項可供建立及設定 Azure 資源︰

- **僅限 PowerShell Cmdlet︰**您可以使用每個資源的 Cmdlet，個別建立及設定每個 Azure 資源。 從命令列執行這項操作，或在 PowerShell 指令碼中包含您可儲存和設定版本的多個命令。

- **具有 Azure 資源管理員範本的 PowerShell Cmdlet︰**您可以使用 PowerShell，透過 Azure 資源管理員範本建立 Azure 資源。 範本可予以儲存及設定版本。

## CLI

您可以從使用 CLI 的 Windows、OS X 或 Linux 電腦建立及設定 Azure 資源。

## 後續步驟

- 
- 
- 




