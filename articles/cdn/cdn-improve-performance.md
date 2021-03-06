---
title: "Fájlok tömörítése Azure CDN szolgáltatás használata a jobb teljesítmény érdekében |} Microsoft Docs"
description: "Útmutató a fájl átviteli sebesség növelése és az Azure CDN szolgáltatás használata a fájlok tömörítésével-betöltési teljesítményének javítása."
services: cdn
documentationcenter: 
author: dksimpson
manager: akucer
editor: 
ms.assetid: af1cddff-78d8-476b-a9d0-8c2164e4de5d
ms.service: cdn
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/09/2018
ms.author: mazha
ms.openlocfilehash: 743d1db803cdb58ae8fa37430ccffa10ca003f93
ms.sourcegitcommit: b32d6948033e7f85e3362e13347a664c0aaa04c1
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/13/2018
---
# <a name="improve-performance-by-compressing-files-in-azure-cdn"></a>A jobb teljesítmény érdekében az Azure CDN fájlok tömörítése
Fájl tömörítése a fájl adatátviteli sebesség növelése és a fájlméret csökkentésével, mielőtt a kiszolgáló-betöltési teljesítményének javítása egyszerű és hatékony módszer. A fájltömörítés sávszélesség-költségek csökkentése, és gyorsabban környezetet biztosítson a felhasználók számára.

Ahhoz, hogy fájltömörítés két módja van:

- A forrás kiszolgálón tömörítésének engedélyezéséhez. Ebben az esetben a CDN a tömörített fájlok mentén továbbítja, és azokat kérő ügyfeleknek továbbítja azokat.
- Közvetlenül a CDN peremhálózati kiszolgálóinak tömörítésének engedélyezéséhez. Ebben az esetben a CDN tömöríti a fájlokat, és végfelhasználók számára, akkor is, ha a forráskiszolgáló nem tömöríti szolgál.

> [!IMPORTANT]
> CDN konfigurációs módosításokat a hálózaton belüli propagálásához hosszabb időt is igénybe vehet. A **Akamai Azure CDN** -profilok propagálása általában fejeződik be, egy perc alatt.  A **Azure CDN Verizon** -profilok propagálása általában befejezi 90 percen belül. Ha először a CDN-végpont tömörítési beállítása, várja meg, 1 – 2 óra, mielőtt a tömörítési beállítások propagálása a POP való biztosításához.
> 
> 

## <a name="enabling-compression"></a>Tömörítés engedélyezése
A standard és prémium szintű CDN rétegek funkcionalitása azonos tömörítést, de a felhasználói felület eltér. Standard és prémium szintű CDN-rétegek közötti különbségekről további információkért lásd: [Azure CDN áttekintése](cdn-overview.md).

### <a name="standard-tier"></a>Standard csomag
> [!NOTE]
> Ez a szakasz vonatkozik **Azure CDN Standard verizon** és **Azure CDN Standard Akamai** profilok.
> 
> 

1. A CDN-profil lapon válassza ki a kezelni kívánt CDN-végpont.
   
    ![CDN-profil végpontok](./media/cdn-file-compression/cdn-endpoints.png)
   
    A CDN-végpont lap nyílik meg.
2. Válassza ki **tömörítés**.

    ![CDN-tömörítés kiválasztása](./media/cdn-file-compression/cdn-compress-select-std.png)
   
    A tömörítés lap nyílik meg.
3. Válassza ki **a** kapcsolja be a tömörítést.
   
    ![CDN tömörítésének beállításai](./media/cdn-file-compression/cdn-compress-standard.png)
4. Használja az alapértelmezett MIME-típusok, vagy módosítsa a listát hozzáadásával vagy eltávolításával a MIME-típusok.
   
   > [!TIP]
   > Bár lehetséges, nem ajánlott tömörítés alkalmazása tömörített formátumban. Például ZIP, MP3, MP4 vagy JPG.
   > 
 
5. A módosítások elvégzése után válassza ki a **mentése**.

### <a name="premium-tier"></a>Premium szintű csomag
> [!NOTE]
> Ez a szakasz csak a vonatkozik **verizon Azure CDN Premium** profilok.
> 

1. A CDN-profil lapon válassza ki a **kezelése**.
   
    ![CDN kezelése kiválasztása](./media/cdn-file-compression/cdn-manage-btn.png)
   
    Megnyitja a CDN-felügyeleti portálon.
2. Vigye a **HTTP nagy** lapra, és vigye a **gyorsítótár beállításainak** menü. Válassza ki **tömörítés**.

    ![CDN-tömörítés kiválasztása](./media/cdn-file-compression/cdn-compress-select.png)
   
    A tömörítési beállítások jelennek meg.
   
    ![CDN tömörítésének beállításai](./media/cdn-file-compression/cdn-compress-files.png)
3. Tömörítés engedélyezése kiválasztásával **tömörítés engedélyezve**. A MIME-típusok tömörítendő (szóközök nélkül) vesszővel tagolt lista formájában adja meg a **fájltípusok** mezőbe.
   
   > [!TIP]
   > Bár lehetséges, nem ajánlott tömörítés alkalmazása tömörített formátumban. Például ZIP, MP3, MP4 vagy JPG.
   > 
    
4. A módosítások elvégzése után válassza ki a **frissítés**.

## <a name="compression-rules"></a>Tömörítés szabályok

### <a name="azure-cdn-from-verizon-profiles-both-standard-and-premium-tiers"></a>(Standard és a prémium szintű rétegek) profilból Verizon Azure CDN

A **Azure CDN Verizon** profilok, csak a megfelelő fájlok tömörítése. Jogosult tömörítési, egy fájlt kell:
- Lehet nagyobb, mint 128 bájt
- 1 MB-nál kisebb lehet
 
A profilok az alábbi tömörítési kódolások támogatja:
- a gzip (GNU zip)
- DEFLATE
- bzip2
- brotli 
 
Ha a kérelem több tömörítési típust támogatja, akkor ezeket a tömörítési típusok élveznek brotli tömörítést.

Ha egy eszköz kérelmet megad brotli tömörítési (`Accept-Encoding: br` HTTP-fejléc) és a gyorsítótár-tévesztései, Azure CDN kérelem eredményez az eszköz brotli tömörítési végez az eredeti kiszolgálóra. Ezt követően a tömörített fájl kiszolgált közvetlenül a gyorsítótárból.

### <a name="azure-cdn-from-akamai-profiles"></a>A profilok Akamai Azure CDN

A **Akamai Azure CDN** profilok, fájlok kerülnek a tömörítést. Azonban a MIME-típust, amelyet egy fájlnak kell lennie [tömörítési konfigurált](#enabling-compression).

Ezeket a profilokat támogatja a gzip tömörítési kódolást csak. Ha egy profil végpontjának a gzip-kódolású fájlt kér, mindig kért a forrásból, függetlenül az ügyfélkérés. 

## <a name="compression-behavior-tables"></a>Tömörítés viselkedés táblák
Az alábbi táblázatok bemutatják az Azure CDN tömörítési viselkedés minden forgatókönyvhöz:

### <a name="compression-is-disabled-or-file-is-ineligible-for-compression"></a>Tömörítés le van tiltva, vagy a fájl nem tömörítési
| Ügyfél által kért formátum (keresztül Accept-Encoding fejlécnek) | Gyorsítótárazott fájlformátum | Az ügyfél CDN válasz | Megjegyzések&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;|
| --- | --- | --- | --- |
| Tömörített |Tömörített |Tömörített | |
| Tömörített |Az Uncompressed |Az Uncompressed | |
| Tömörített |Nem gyorsítótárazott |Tömörített és tömörítetlen |A forrás válasz határozza meg, hogy CDN végez-e a tömörítést. |
| Az Uncompressed |Tömörített |Az Uncompressed | |
| Az Uncompressed |Az Uncompressed |Az Uncompressed | |
| Az Uncompressed |Nem gyorsítótárazott |Az Uncompressed | |

### <a name="compression-is-enabled-and-file-is-eligible-for-compression"></a>Tömörítését engedélyezték, és a fájl nem jogosult az tömörítés
| Ügyfél által kért formátum (keresztül Accept-Encoding fejlécnek) | Gyorsítótárazott fájlformátum | Az ügyfél CDN válasz | Megjegyzések |
| --- | --- | --- | --- |
| Tömörített |Tömörített |Tömörített |CDN transcodes támogatott formátumok közötti. |
| Tömörített |Az Uncompressed |Tömörített |CDN hajt végre a tömörítést. |
| Tömörített |Nem gyorsítótárazott |Tömörített |A CDN a tömörítési hajt végre, ha a forrás visszaküldi a tömörítetlen. <br/>**Verizon Azure CDN** átadja az első kérésre tömörítetlen fájl és majd tömöríti és gyorsítótárazza a További kérelmeknél fájlt. <br/>A Cache-Control rendelkező fájlok: Soha ne tömörített no-cache fejlécet. |
| Az Uncompressed |Tömörített |Az Uncompressed |A CDN egy kitömörítés hajt végre. |
| Az Uncompressed |Az Uncompressed |Az Uncompressed | |
| Az Uncompressed |Nem gyorsítótárazott |Az Uncompressed | |

## <a name="media-services-cdn-compression"></a>Media Services-CDN tömörítés
A Media Services CDN streaming engedélyezett végpontot, tömörítés alapértelmezés szerint engedélyezve van a következő MIME-típusok: 
- application/vnd.ms-sstr+xml 
- alkalmazás/dash + xml
- application/vnd.apple.mpegurl
- alkalmazás/f4m + xml 

## <a name="see-also"></a>Lásd még
* [CDN-fájltömörítés hibaelhárítása](cdn-troubleshoot-compression.md)    

