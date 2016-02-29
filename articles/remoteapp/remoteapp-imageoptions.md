<properties
    pageTitle="建立 Azure RemoteApp 映像 | Microsoft Azure"
    description="了解可用來建立 Azure RemoteApp 之映像的選項"
    services="remoteapp"
    documentationCenter=""
    authors="lizap"
    manager="mbaldwin" />

<tags
    ms.service="remoteapp"
    ms.workload="compute"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="12/05/2015"
    ms.author="elizapo" />



# 建立 Azure RemoteApp 映像

Azure RemoteApp 使用映像保留與使用者共用的應用程式。 若要建立使用您選擇的應用程式，Azure remoteapp，無論是雲端或混合式，首先建立映像與安裝這些應用程式。 接著，建立使用該映像的集合，並將使用者指派給集合，然後將應用程式發佈給那些使用者。

您有幾個選項可建立或使用映像。 基本 [需求](remoteapp-imagereqs.md) 的映像只是它執行 Windows Server 2012 R2 並已安裝遠端桌面工作階段主機 (RDSH) 角色。 如何達成就是有趣的地方。

如果是映像，則您有下列選擇：

- 您可以匯入並使用 [根據 Azure 虛擬機器的映像](remoteapp-image-on-azurevm.md)。 這適用於需要自訂設定的特定業務應用程式。 您可以自訂要用於應用程式的映像。
- 您可以 [建立並上傳自訂映像](remoteapp-create-custom-image.md)。 如果您已經有用於內部部署遠端桌面服務部署的映像，則這十分不錯。
- 您可以使用其中一個 [範本映像](remoteapp-images.md) RemoteApp 訂閱所包含。 這些映像是由 RemoteApp 小組所建立和維護，並且包含一些可提供給您使用者的標準應用程式 (如 Office 套件)。 請注意，只有 Office 365 Pro Plus 映像才能用於生產設定中。

不論在何處取得映像或建立它，請確定您了解 [應用程式需求](remoteapp-appreqs.md) ，確保您的應用程式在 RemoteApp 中順暢運作。 然後下, 一個步驟是建立 [定域機組](remoteapp-create-cloud-deployment.md) 或 [混合式](remoteapp-create-hybrid-deployment.md) 集合。

