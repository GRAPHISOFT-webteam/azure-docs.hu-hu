---
title: "Az Azure PowerShell-példák az Azure Data Factory |} Microsoft Docs"
description: "Az Azure PowerShell-példák - parancsfájlokat, amelyek segítségével hozhat létre és kezelhet az adat-előállítók."
services: data-factory
author: spelluru
manager: douglaslMS
editor: 
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/16/2018
ms.author: douglasl
ms.openlocfilehash: 25d9f63eb6c8afee9bc8ca4771ff51d98bc2d09b
ms.sourcegitcommit: 782d5955e1bec50a17d9366a8e2bf583559dca9e
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/02/2018
---
# <a name="azure-powershell-samples-for-azure-data-factory"></a>Az Azure Data Factory Azure PowerShell-minták

A következő táblázat az Azure Data Factory Azure PowerShell-mintaparancsfájlok hivatkozásokat tartalmaz.

> [!NOTE]
> Ez a cikk a Data Factory 2. verziójára vonatkozik, amely jelenleg előzetes verzióban érhető el. A Data Factory szolgáltatásnak, amely általánosan elérhető (GA), 1 verziójának használatakor lásd [adat-előállító version1 minták](v1/data-factory-samples.md).

| |  |
|---|---|
|**Adatok másolása**||
|[Blobok mappából másolja az Azure Blob Storage tárolóban egy másik mappába](scripts/copy-azure-blob-powershell.md?toc=%2fpowershell%2fmodule%2ftoc.json)| A PowerShell parancsfájl blobok az Azure Blob Storage egy mappából egy másik mappájába másolja az azonos Blob-tároló. |
|[A helyszíni SQL Server az Azure Blob Storage adatok másolása](scripts/hybrid-copy-powershell.md?toc=%2fpowershell%2fmodule%2ftoc.json)| A PowerShell parancsfájl másol adatokat a helyszíni SQL Server-adatbázis egy Azure blobtárolóba. |
|[A tömeges másolás](scripts/bulk-copy-powershell.md?toc=%2fpowershell%2fmodule%2ftoc.json)| A PowerShell-parancsfájlpélda egy Azure SQL data warehouse adatainak másolása Azure SQL adatbázis több táblából. |
|[Növekményes másolása](scripts/incremental-copy-powershell.md?toc=%2fpowershell%2fmodule%2ftoc.json)| A PowerShell-parancsfájlpélda betölti csak új vagy frissített rekordokat egy forrás adattárból e fogadó adattárba után a forrásból származó adatokat a fogadó kezdeti teljes másolata. |
|**Adatok átalakítása**||
|[Adatok használata Spark-fürt](scripts/transform-data-spark-powershell.md?toc=%2fpowershell%2fmodule%2ftoc.json)| A PowerShell parancsfájl átalakítja az adatokat a program futtatása Spark-fürtön. |
|**Az Azure-bA növekedési és shift SSIS-csomagok**||
|[Azure-SSIS integrációs futásidejű létrehozása](scripts/deploy-azure-ssis-integration-runtime-powershell.md?toc=%2fpowershell%2fmodule%2ftoc.json)| A PowerShell parancsfájl rendelkezések egy Azure-SSIS integrációs futásidejű, amely az SQL Server Integration Services (SSIS) csomag fut az Azure-ban. |



