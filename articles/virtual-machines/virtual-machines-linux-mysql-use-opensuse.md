<properties
    pageTitle="在 Microsoft Azure 中的 OpenSUSE Linux VM 上安裝 MySQL"
    description="了解如何在 Azure 的虛擬機器上安裝 MySQL。"
    services="virtual-machines"
    documentationCenter=""
    authors="cynthn"
    manager="timlt"
    editor=""
    tags="azure-service-management"/>

<tags
    ms.service="virtual-machines"
    ms.workload="infrastructure-services"
    ms.tgt_pltfrm="vm-linux"
    ms.devlang="na"
    ms.topic="article"
    ms.date="08/31/2015"
    ms.author="cynthn"/>


# 在 Azure 中執行 OpenSUSE Linux 的虛擬機器上安裝 MySQL

[MySQL ][mysql] 是熱門開放原始碼 SQL 資料庫。 本教學課程會示範如何建立執行 OpenSUSE Linux 的虛擬機器，然後安裝 MySQL。

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-classic-include.md)] 資源管理員模型。


<br>


[AZURE.INCLUDE [free-trial-note](../../includes/free-trial-note.md)]

## 建立執行 OpenSUSE Linux 的虛擬機器

[AZURE.INCLUDE [create-and-configure-opensuse-vm-in-portal](../../includes/create-and-configure-opensuse-vm-in-portal.md)]

## 在虛擬機器上安裝和執行 MySQL

[AZURE.INCLUDE [install-and-run-mysql-on-opensuse-vm](../../includes/install-and-run-mysql-on-opensuse-vm.md)]

## 後續步驟

如需 MySQL 的詳細資訊，請參閱 [MySQL 文件 ][mysqldocs]。


[mysqldocs]: http://dev.mysql.com/doc/index-topic.html 
[mysql]: http://www.mysql.com 

