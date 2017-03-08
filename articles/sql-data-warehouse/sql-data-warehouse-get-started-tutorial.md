---
title: "Azure SQL Data Warehouse – Első lépéseket ismertető oktatóanyag | Microsoft Docs"
description: "Ez az oktatóanyag az Azure SQL Data Warehouse üzembe helyezését és adatokkal való feltöltését mutatja be. Emellett megismerkedhet a méretezés, a felfüggesztetés és a finomhangolás alapjaival is."
services: sql-data-warehouse
documentationcenter: NA
author: hirokib
manager: johnmac
editor: barbkess
ms.assetid: 52DFC191-E094-4B04-893F-B64D5828A900
ms.service: sql-data-warehouse
ms.devlang: NA
ms.topic: hero-article
ms.tgt_pltfrm: NA
ms.workload: data-services
ms.date: 01/26/2017
ms.author: elbutter;barbkess
translationtype: Human Translation
ms.sourcegitcommit: 2c88c1abd2af7a1ca041cd5003fd1f848e1b311c
ms.openlocfilehash: 12f72e76ee991dfb701637847f2e406cd0f8c449
ms.lasthandoff: 02/03/2017


---
# <a name="get-started-with-sql-data-warehouse"></a>Bevezetés az SQL Data Warehouse használatába

Ez az oktatóanyag az Azure SQL Data Warehouse üzembe helyezését és adatokkal való feltöltését mutatja be. Emellett megismerkedhet a méretezés, a felfüggesztetés és a finomhangolás alapjaival is. Az oktatóanyag elvégzése után készen áll majd az adattárház lekérdezésére és vizsgálatára.

**Az oktatóanyag áttekintésének becsült ideje:** Ez egy példakódot is tartalmazó átfogó oktatóanyag, amelynek az elvégzése kb. 30 percet vesz igénybe, ha az előfeltételeket már teljesítette. 

## <a name="prerequisites"></a>Előfeltételek

Ez az oktatóanyag azt feltételezi, hogy már ismeri az SQL Data Warehouse-zal kapcsolatos alapvető fogalmakat. A bevezetésért lásd: [Mi az az SQL Data Warehouse?](sql-data-warehouse-overview-what-is.md) 

### <a name="sign-up-for-microsoft-azure"></a>Regisztráció a Microsoft Azure-ban
Ha még nem rendelkezik Microsoft Azure-fiókkal, először regisztrálnia kell egyet a szolgáltatás használatához. Ha már rendelkezik fiókkal, ezt a lépést kihagyhatja. 

1. Nyissa meg a fiókoldalakat: [https://azure.microsoft.com/account/](https://azure.microsoft.com/account/)
2. Hozzon létre egy ingyenes Azure-fiókot, vagy vásároljon egy fiókot.
3. Kövesse az utasításokat

### <a name="install-appropriate-sql-client-drivers-and-tools"></a>A megfelelő SQL-ügyfélillesztők és -ügyféleszközök telepítése

A legtöbb SQL-ügyféleszköz képes csatlakozni az SQL Data Warehouse-hoz a JDBC, az ODBC vagy az ADO.NET használatával. Az SQL Data Warehouse által támogatott T-SQL-szolgáltatások széles köre miatt lehetséges, hogy egyes ügyfélalkalmazások nem teljes mértékben kompatibilisek az SQL Data Warehouse-zal.

Ha Windows operációs rendszert használ, javasoljuk a [Visual Studio] vagy az [SQL Server Management Studio] használatát.

[!INCLUDE [Create a new logical server](../../includes/sql-data-warehouse-create-logical-server.md)] 

[!INCLUDE [SQL Database create server](../../includes/sql-database-create-new-server-firewall-portal.md)]

## <a name="create-a-sql-data-warehouse"></a>SQL Data Warehouse létrehozása

Az SQL Data Warehouse egy nagymértékben párhuzamos feldolgozáshoz kialakított speciális típusú adatbázis. Az adatbázis több csomópontra van elosztva, és párhuzamosan dolgozza fel a lekérdezéseket. Az összes csomópont tevékenységét az SQL Data Warehouse vezérlő csomópontja vezényli. Maguk a csomópontok SQL Database használatával kezelik az adatokat.  

> [!NOTE]
> Egy SQL Data Warehouse létrehozása egy új számlázható szolgáltatás létrejöttét eredményezheti.  További információ: [SQL Data Warehouse díjszabása](https://azure.microsoft.com/pricing/details/sql-data-warehouse/).
>

### <a name="create-a-data-warehouse"></a>Adattárház létrehozása

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com).
2. Kattintson a **New** > **Databases** > **SQL Data Warehouse** (Új > Adatbázisok > SQL Data Warehouse) elemre.

    ![NewBlade](../../includes/media/sql-data-warehouse-create-dw/blade-click-new.png)
    ![SelectDW](../../includes/media/sql-data-warehouse-create-dw/blade-select-dw.png)

3. Adja meg az üzembe helyezés részleteit

    **Database name** (Adatbázis neve): Tetszés szerint bármit megadhat. Ha több adattárházzal is rendelkezik, akkor javasoljuk, hogy a nevek tartalmazzák a régiót, a környezetet vagy hasonló részleteket (például: *mydw-westus-1-test*).

    **Subscription** (Előfizetés): Az Ön Azure-előfizetése

    **Erőforráscsoport**: Hozzon létre egy erőforráscsoportot, vagy használjon egy meglévőt.
    > [!NOTE]
    > Az erőforráscsoportok hasznosak az erőforrások adminisztrációja, például a hozzáférés-vezérlés körének meghatározása és a sablonalapú üzembe helyezés során. Tudjon meg többet az Azure erőforráscsoportjairól és az ajánlott eljárásokról [itt](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview#resource-groups).

    **Forrás**: Üres adatbázis

    **Kiszolgáló**: Válassza ki az [Előfeltételek] szakaszban létrehozott kiszolgálót.

    **Rendezés**: Hagyja meg az alapértelmezett SQL_Latin1_General_CP1_CI_AS beállítást.

    **Teljesítmény kiválasztása**: Azt javasoljuk, hogy kezdje a standard 400DWU beállítással.

4. Válassza a **Rögzítés az irányítópulton**
    ![Rögzítés az irányítópulton](./media/sql-data-warehouse-get-started-tutorial/pin-to-dashboard.png) lehetőséget.

5. Dőljön hátra, és várjon, amíg az adattárház üzembe helyezése megtörténik! Ez a folyamat szokványos esetben több percig is eltarthat. A portál értesíti, amint az adattárház használatra kész. 

## <a name="connect-to-sql-data-warehouse"></a>Connect to SQL Data Warehouse

Az oktatóanyagban az SQL Server Management Studio (SSMS) segítségével csatlakozunk az adattárházhoz. A következő támogatott összekötőkön keresztül is csatlakozhat az SQL Data Warehouse-hoz: ADO.NET, JDBC, ODBC és PHP. Ne feledje, hogy a Microsoft által nem támogatott eszközök esetében a funkcionalitás korlátozott lehet.


### <a name="get-connection-information"></a>Kapcsolatadatok lekérése

Az adattárházhoz való kapcsolódáshoz az [Előfeltételek] szakaszban létrehozott logikai SQL-kiszolgálón keresztül kell csatlakoznia.

1. Válassza ki az adattárházat az irányítópulton vagy keresse meg az erőforrások között.

    ![SQL Data Warehouse irányítópult](./media/sql-data-warehouse-get-started-tutorial/sql-dw-dashboard.png)

2. Keresse meg a logikai SQL-kiszolgáló teljes nevét.

    ![Kiszolgálónév kiválasztása](./media/sql-data-warehouse-get-started-tutorial/select-server.png)

3. Nyissa meg az SSMS-t, és az Object Explorer használatával csatlakozzon ehhez a kiszolgálóhoz az [Előfeltételek] szakaszban létrehozott kiszolgálói rendszergazdai hitelesítő adatok használatával.

    ![Csatlakozás SSMS segítségével](./media/sql-data-warehouse-get-started-tutorial/ssms-connect.png)

Ha minden jól megy, mostanra kapcsolódnia kell a logikai SQL-kiszolgálóhoz. Miután kiszolgálói rendszergazdaként jelentkezett be, a kiszolgáló által futtatott bármelyik adatbázishoz kapcsolódhat, beleértve a master adatbázist. 

Csak egyetlen kiszolgálói rendszergazdai fiók létezik, és az összes felhasználó közül ez rendelkezik a legtöbb jogosultsággal. Legyen óvatos, és csak kevesekkel tudassa a rendszergazdai jelszót a szervezetben. 

Emellett rendelkezhet egy Azure Active Directory-rendszergazdai fiókkal is. Ennek részleteit itt nem ismertetjük. Ha többet szeretne megtudni az Azure Active Directory-alapú hitelesítéssel kapcsolatban, lásd: [Azure AD-hitelesítés](https://docs.microsoft.com/azure/sql-database/sql-database-aad-authentication).

Ezután a további bejelentkezések és felhasználók létrehozásával ismerkedünk meg.


## <a name="create-a-database-user"></a>Adatbázis-felhasználó létrehozása

Ebben a lépésben egy felhasználói fiókot hozhat létre az adattárház eléréséhez. Megmutatjuk azt is, hogyan engedélyezheti a felhasználó számára a nagy mennyiségű memória- és processzor-erőforrást igénylő lekérdezések futtatását.

### <a name="notes-about-resource-classes-for-allocating-resources-to-queries"></a>Az erőforrásosztályokkal kapcsolatos megjegyzések az erőforrások lekérdezésekhez való kiosztásához

- Az adatok biztonsága érdekében a kiszolgálói rendszergazdát ne használja a lekérdezések éles adatbázisokon való futtatásához. Az összes közül ez a felhasználó rendelkezik a legtöbb jogosultsággal, így ha ezzel hajt végre műveleteket a felhasználói adatokon, veszélyeztetheti az adatokat. Továbbá, mivel a kiszolgálói rendszergazda felügyeleti tevékenységeket hivatott végezni, a működéséhez kis mennyiségű memória- és processzor-erőforrást használ csak. 

- Az SQL Data Warehouse előre meghatározott adatbázis-szerepköröket, úgynevezett erőforrásosztályokat alkalmaz a különböző mennyiségű memória, processzor-erőforrás és egyidejű hely lefoglalásához az egyes felhasználók számára. Az egyes felhasználók kicsi, közepes, nagy vagy extra nagy erőforrásosztályokba tartozhatnak. Az adott falhasználó erőforrásosztálya határozza meg, hogy a felhasználó milyen erőforrásokkal rendelkezik a lekérdezések és betöltési műveletek futtatásához.

- Az adattömörítés optimalizálásához általában nagy vagy extra nagy erőforrást szükséges lefoglalni a felhasználó számára. További információkat az erőforrásosztályokról [itt](./sql-data-warehouse-develop-concurrency.md#resource-classes) talál.

### <a name="create-an-account-that-can-control-a-database"></a>Az adatbázisok vezérlésére alkalmas fiók létrehozása

Mivel jelenleg kiszolgálói rendszergazdaként van bejelentkezve, rendelkezik megfelelő jogosultsággal a bejelentkezések és a felhasználók létrehozásához.

1. Az SSMS vagy más lekérdezésügyfél használatával nyisson egy új lekérdezést a **masteren**.

    ![Új lekérdezés a Master adatbázison](./media/sql-data-warehouse-get-started-tutorial/query-on-server.png)

    ![Új lekérdezés a Master1 adatbázison](./media/sql-data-warehouse-get-started-tutorial/query-on-master.png)

2. Futtassa a következő T-SQL parancsot a lekérdezésablakban, és hozzon létre egy bejelentkezést MedRCLogin és egy felhasználót LoadingUser néven. Ez a bejelentkezés képes kapcsolódni a logikai SQL-kiszolgálóhoz.

    ```sql
    CREATE LOGIN MedRCLogin WITH PASSWORD = 'a123reallySTRONGpassword!';
    CREATE USER LoadingUser FOR LOGIN MedRCLogin;
    ```

3. Most az *SQL Data Warehouse-adatbázis* lekérdezéséhez hozzon létre egy adatbázis-felhasználót azon bejelentkezés alapján, amelyet az adatbázishoz való hozzáféréshez és az azon való tevékenységek elvégzéséhez hozott létre.

    ```sql
    CREATE USER LoadingUser FOR LOGIN MedRCLogin;
    ```

4. Adjon az adatbázis-felhasználónak felügyeleti jogosultságot az NYT nevű adatbázishoz. 

    ```sql
    GRANT CONTROL ON DATABASE::[NYT] to LoadingUser;
    ```
    > [!NOTE]
    > Ha az adatbázis nevében található kötőjel, mindenképp foglalja a nevet szögletes zárójelek közé. 
    >

### <a name="give-the-user-medium-resource-allocations"></a>Közepes erőforrás-mennyiség lefoglalása a felhasználó számára

1. A következő T-SQL parancs futtatásával tegye a felhasználót a mediumrc nevű közepes erőforrásosztály tagjává. 

    ```sql
    EXEC sp_addrolemember 'mediumrc', 'LoadingUser';
    ```
    > [!NOTE]
    > Az egyidejűségre és az erőforrásosztályokra vonatkozó további információkért kattintson [ide](sql-data-warehouse-develop-concurrency.md#resource-classes). 
    >

2. Csatlakozás a logikai kiszolgálóhoz az új hitelesítő adatokkal

    ![Bejelentkezés az új bejelentkezési adatokkal](./media/sql-data-warehouse-get-started-tutorial/new-login.png)


## <a name="load-data-from-azure-blob-storage"></a>Adatok betöltése az Azure Blob Storage-ből

Most már készen áll az adatok betöltésére az adattárházba. Ez a lépés bemutatja, hogyan töltheti be a New York-i taxik adatait egy nyilvános Azure tárolóblobból. 

- Az adatok az SQL Data Warehouse-ba való betöltésének gyakori módja, ha először áthelyezi az adatokat az Azure Blob Storage-be, majd eztán tölti be azokat az adattárházba. Hogy könnyebben átlássa a betöltés folyamatát, a New York-i taxik adatait már eleve egy nyilvános Azure tárolóblobban tároljuk. 

- Ha később szeretné megismerni az adatok Azure Blob Storage-be való áthelyezésének vagy a forrásból közvetlenül az SQL Data Warehouse-ba való betöltésének a módját, olvassa el a [betöltés áttekintését](sql-data-warehouse-overview-load.md).


### <a name="define-external-data"></a>Külső adatok meghatározása

1. Hozzon létre egy főkulcsot. Adatbázisonként csak egyszer kell főkulcsot létrehoznia. 

    ```sql
    CREATE MASTER KEY;
    ```

2. Határozza meg a helyet az Azure blobban, ahol a taxik adatai találhatók.  

    ```sql
    CREATE EXTERNAL DATA SOURCE NYTPublic
    WITH
    (
        TYPE = Hadoop,
        LOCATION = 'wasbs://2013@nytpublic.blob.core.windows.net/'
    );
    ```

3. Határozza meg a külső fájlformátumokat.

    A ```CREATE EXTERNAL FILE FORMAT``` parancs használatával adhatja meg a külső adatokat tartalmazó fájlok formátumát. Ezek egy vagy több karakter, az úgynevezett elválasztó karakterek használatával vannak elválasztva. Bemutatási célokból a taxik adatait itt tömörítetlen adatokként és GZIP formátumban tömörített adatokként is tároljuk.

    A következő T-SQL parancsok futtatásával határozhatja meg a két különböző, a tömörítetlen és a tömörített formátumot.

    ```sql
    CREATE EXTERNAL FILE FORMAT uncompressedcsv
    WITH (
        FORMAT_TYPE = DELIMITEDTEXT,
        FORMAT_OPTIONS ( 
            FIELD_TERMINATOR = ',',
            STRING_DELIMITER = '',
            DATE_FORMAT = '',
            USE_TYPE_DEFAULT = False
        )
    );

    CREATE EXTERNAL FILE FORMAT compressedcsv
    WITH ( 
        FORMAT_TYPE = DELIMITEDTEXT,
        FORMAT_OPTIONS ( FIELD_TERMINATOR = '|',
            STRING_DELIMITER = '',
        DATE_FORMAT = '',
            USE_TYPE_DEFAULT = False
        ),
        DATA_COMPRESSION = 'org.apache.hadoop.io.compress.GzipCodec'
    );
    ```

4.  Hozzon létre egy sémát a külső fájlformátum számára. 

    ```sql
    CREATE SCHEMA ext;
    ```
5. Hozza létre a külső táblákat. Ezek a táblák az Azure Blob Storage-ben tárolt adatokra hivatkoznak. A következő T-SQL parancsok futtatásával hozzon létre több külső táblát, amelyek mind a külső adatforrásban korábban meghatározott Azure-blobra mutatnak.

```sql
    CREATE EXTERNAL TABLE [ext].[Date] 
    (
        [DateID] int NOT NULL,
        [Date] datetime NULL,
        [DateBKey] char(10) COLLATE SQL_Latin1_General_CP1_CI_AS NULL,
        [DayOfMonth] varchar(2) COLLATE SQL_Latin1_General_CP1_CI_AS NULL,
        [DaySuffix] varchar(4) COLLATE SQL_Latin1_General_CP1_CI_AS NULL,
        [DayName] varchar(9) COLLATE SQL_Latin1_General_CP1_CI_AS NULL,
        [DayOfWeek] char(1) COLLATE SQL_Latin1_General_CP1_CI_AS NULL,
        [DayOfWeekInMonth] varchar(2) COLLATE SQL_Latin1_General_CP1_CI_AS NULL,
        [DayOfWeekInYear] varchar(2) COLLATE SQL_Latin1_General_CP1_CI_AS NULL,
        [DayOfQuarter] varchar(3) COLLATE SQL_Latin1_General_CP1_CI_AS NULL,
        [DayOfYear] varchar(3) COLLATE SQL_Latin1_General_CP1_CI_AS NULL,
        [WeekOfMonth] varchar(1) COLLATE SQL_Latin1_General_CP1_CI_AS NULL,
        [WeekOfQuarter] varchar(2) COLLATE SQL_Latin1_General_CP1_CI_AS NULL,
        [WeekOfYear] varchar(2) COLLATE SQL_Latin1_General_CP1_CI_AS NULL,
        [Month] varchar(2) COLLATE SQL_Latin1_General_CP1_CI_AS NULL,
        [MonthName] varchar(9) COLLATE SQL_Latin1_General_CP1_CI_AS NULL,
        [MonthOfQuarter] varchar(2) COLLATE SQL_Latin1_General_CP1_CI_AS NULL,
        [Quarter] char(1) COLLATE SQL_Latin1_General_CP1_CI_AS NULL,
        [QuarterName] varchar(9) COLLATE SQL_Latin1_General_CP1_CI_AS NULL,
        [Year] char(4) COLLATE SQL_Latin1_General_CP1_CI_AS NULL,
        [YearName] char(7) COLLATE SQL_Latin1_General_CP1_CI_AS NULL,
        [MonthYear] char(10) COLLATE SQL_Latin1_General_CP1_CI_AS NULL,
        [MMYYYY] char(6) COLLATE SQL_Latin1_General_CP1_CI_AS NULL,
        [FirstDayOfMonth] date NULL,
        [LastDayOfMonth] date NULL,
        [FirstDayOfQuarter] date NULL,
        [LastDayOfQuarter] date NULL,
        [FirstDayOfYear] date NULL,
        [LastDayOfYear] date NULL,
        [IsHolidayUSA] bit NULL,
        [IsWeekday] bit NULL,
        [HolidayUSA] varchar(50) COLLATE SQL_Latin1_General_CP1_CI_AS NULL
    )
    WITH
    (
        LOCATION = 'Date',
        DATA_SOURCE = NYTPublic,
        FILE_FORMAT = uncompressedcsv,
        REJECT_TYPE = value,
        REJECT_VALUE = 0
    );
    
    CREATE EXTERNAL TABLE [ext].[Geography]
    (
        [GeographyID] int NOT NULL,
        [ZipCodeBKey] varchar(10) COLLATE SQL_Latin1_General_CP1_CI_AS NOT NULL,
        [County] varchar(50) COLLATE SQL_Latin1_General_CP1_CI_AS NULL,
        [City] varchar(50) COLLATE SQL_Latin1_General_CP1_CI_AS NULL,
        [State] varchar(50) COLLATE SQL_Latin1_General_CP1_CI_AS NULL,
        [Country] varchar(50) COLLATE SQL_Latin1_General_CP1_CI_AS NULL,
        [ZipCode] varchar(50) COLLATE SQL_Latin1_General_CP1_CI_AS NULL
    )
    WITH
    (
        LOCATION = 'Geography',
        DATA_SOURCE = NYTPublic,
        FILE_FORMAT = uncompressedcsv,
        REJECT_TYPE = value,
        REJECT_VALUE = 0 
    );
        
    
    CREATE EXTERNAL TABLE [ext].[HackneyLicense]
    (
        [HackneyLicenseID] int NOT NULL,
        [HackneyLicenseBKey] varchar(50) COLLATE SQL_Latin1_General_CP1_CI_AS NOT NULL,
        [HackneyLicenseCode] varchar(50) COLLATE SQL_Latin1_General_CP1_CI_AS NULL
    )
    WITH
    (
        LOCATION = 'HackneyLicense',
        DATA_SOURCE = NYTPublic,
        FILE_FORMAT = uncompressedcsv,
        REJECT_TYPE = value,
        REJECT_VALUE = 0
    )
    ;
        
    
    CREATE EXTERNAL TABLE [ext].[Medallion]
    (
        [MedallionID] int NOT NULL,
        [MedallionBKey] varchar(50) COLLATE SQL_Latin1_General_CP1_CI_AS NOT NULL,
        [MedallionCode] varchar(50) COLLATE SQL_Latin1_General_CP1_CI_AS NULL
    )
    WITH
    (
        LOCATION = 'Medallion',
        DATA_SOURCE = NYTPublic,
        FILE_FORMAT = uncompressedcsv,
        REJECT_TYPE = value,
        REJECT_VALUE = 0
    )
    ;
        
    CREATE EXTERNAL TABLE [ext].[Time]
    (
        [TimeID] int NOT NULL,
        [TimeBKey] varchar(8) COLLATE SQL_Latin1_General_CP1_CI_AS NOT NULL,
        [HourNumber] tinyint NOT NULL,
        [MinuteNumber] tinyint NOT NULL,
        [SecondNumber] tinyint NOT NULL,
        [TimeInSecond] int NOT NULL,
        [HourlyBucket] varchar(15) COLLATE SQL_Latin1_General_CP1_CI_AS NOT NULL,
        [DayTimeBucketGroupKey] int NOT NULL,
        [DayTimeBucket] varchar(100) COLLATE SQL_Latin1_General_CP1_CI_AS NOT NULL
    )
    WITH
    (
        LOCATION = 'Time',
        DATA_SOURCE = NYTPublic,
        FILE_FORMAT = uncompressedcsv,
        REJECT_TYPE = value,
        REJECT_VALUE = 0
    )
    ;
    
    
    CREATE EXTERNAL TABLE [ext].[Trip]
    (
        [DateID] int NOT NULL,
        [MedallionID] int NOT NULL,
        [HackneyLicenseID] int NOT NULL,
        [PickupTimeID] int NOT NULL,
        [DropoffTimeID] int NOT NULL,
        [PickupGeographyID] int NULL,
        [DropoffGeographyID] int NULL,
        [PickupLatitude] float NULL,
        [PickupLongitude] float NULL,
        [PickupLatLong] varchar(50) COLLATE SQL_Latin1_General_CP1_CI_AS NULL,
        [DropoffLatitude] float NULL,
        [DropoffLongitude] float NULL,
        [DropoffLatLong] varchar(50) COLLATE SQL_Latin1_General_CP1_CI_AS NULL,
        [PassengerCount] int NULL,
        [TripDurationSeconds] int NULL,
        [TripDistanceMiles] float NULL,
        [PaymentType] varchar(50) COLLATE SQL_Latin1_General_CP1_CI_AS NULL,
        [FareAmount] money NULL,
        [SurchargeAmount] money NULL,
        [TaxAmount] money NULL,
        [TipAmount] money NULL,
        [TollsAmount] money NULL,
        [TotalAmount] money NULL
    )
    WITH
    (
        LOCATION = 'Trip2013',
        DATA_SOURCE = NYTPublic,
        FILE_FORMAT = compressedcsv,
        REJECT_TYPE = value,
        REJECT_VALUE = 0
    )
    ;
    
    CREATE EXTERNAL TABLE [ext].[Weather]
    (
        [DateID] int NOT NULL,
        [GeographyID] int NOT NULL,
        [PrecipitationInches] float NOT NULL,
        [AvgTemperatureFahrenheit] float NOT NULL
    )
    WITH
    (
        LOCATION = 'Weather2013',
        DATA_SOURCE = NYTPublic,
        FILE_FORMAT = uncompressedcsv,
        REJECT_TYPE = value,
        REJECT_VALUE = 0
    )
    ;
    ```

### Import the data from Azure blob storage.

SQL Data Warehouse supports a key statement called CREATE TABLE AS SELECT (CTAS). This statement creates a new table based on the results of a select statement. The new table has the same columns and data types as the results of the select statement.  This is an elegant way to import data from Azure blob storage into SQL Data Warehouse.

1. Run this script to import your data.

    ```sql
    CREATE TABLE [dbo].[Date]
    WITH
    ( 
        DISTRIBUTION = ROUND_ROBIN,
        CLUSTERED COLUMNSTORE INDEX
    )
    AS SELECT * FROM [ext].[Date]
    OPTION (LABEL = 'CTAS : Load [dbo].[Date]')
    ;
    
    CREATE TABLE [dbo].[Geography]
    WITH
    ( 
        DISTRIBUTION = ROUND_ROBIN,
        CLUSTERED COLUMNSTORE INDEX
    )
    AS
    SELECT * FROM [ext].[Geography]
    OPTION (LABEL = 'CTAS : Load [dbo].[Geography]')
    ;
    
    CREATE TABLE [dbo].[HackneyLicense]
    WITH
    ( 
        DISTRIBUTION = ROUND_ROBIN,
        CLUSTERED COLUMNSTORE INDEX
    )
    AS SELECT * FROM [ext].[HackneyLicense]
    OPTION (LABEL = 'CTAS : Load [dbo].[HackneyLicense]')
    ;
    
    CREATE TABLE [dbo].[Medallion]
    WITH
    (
        DISTRIBUTION = ROUND_ROBIN,
        CLUSTERED COLUMNSTORE INDEX
    )
    AS SELECT * FROM [ext].[Medallion]
    OPTION (LABEL = 'CTAS : Load [dbo].[Medallion]')
    ;
    
    CREATE TABLE [dbo].[Time]
    WITH
    (
        DISTRIBUTION = ROUND_ROBIN,
        CLUSTERED COLUMNSTORE INDEX
    )
    AS SELECT * FROM [ext].[Time]
    OPTION (LABEL = 'CTAS : Load [dbo].[Time]')
    ;
    
    CREATE TABLE [dbo].[Weather]
    WITH
    ( 
        DISTRIBUTION = ROUND_ROBIN,
        CLUSTERED COLUMNSTORE INDEX
    )
    AS SELECT * FROM [ext].[Weather]
    OPTION (LABEL = 'CTAS : Load [dbo].[Weather]')
    ;
    
    CREATE TABLE [dbo].[Trip]
    WITH
    (
        DISTRIBUTION = ROUND_ROBIN,
        CLUSTERED COLUMNSTORE INDEX
    )
    AS SELECT * FROM [ext].[Trip]
    OPTION (LABEL = 'CTAS : Load [dbo].[Trip]')
    ;
    ```

2. View your data as it loads.

   You’re loading several GBs of data and compressing it into highly performant clustered columnstore indexes. Run the following query that uses a dynamic management views (DMVs) to show the status of the load. After starting the query, grab a coffee and a snack while SQL Data Warehouse does some heavy lifting.
    
    ```sql
    SELECT
        r.command,
        s.request_id,
        r.status,
        count(distinct input_name) as nbr_files,
        sum(s.bytes_processed)/1024/1024 as gb_processed
    FROM 
        sys.dm_pdw_exec_requests r
        INNER JOIN sys.dm_pdw_dms_external_work s
        ON r.request_id = s.request_id
    WHERE
        r.[label] = 'CTAS : Load [dbo].[Date]' OR
        r.[label] = 'CTAS : Load [dbo].[Geography]' OR
        r.[label] = 'CTAS : Load [dbo].[HackneyLicense]' OR
        r.[label] = 'CTAS : Load [dbo].[Medallion]' OR
        r.[label] = 'CTAS : Load [dbo].[Time]' OR
        r.[label] = 'CTAS : Load [dbo].[Weather]' OR
        r.[label] = 'CTAS : Load [dbo].[Trip]'
    GROUP BY
        r.command,
        s.request_id,
        r.status
    ORDER BY
        nbr_files desc, 
        gb_processed desc;
    ```

3. View all system queries.

    ```sql
    SELECT * FROM sys.dm_pdw_exec_requests;
    ```

4. Enjoy seeing your data nicely loaded into your Azure SQL Data Warehouse.

    ![See Data Loaded](./media/sql-data-warehouse-get-started-tutorial/see-data-loaded.png)


## Improve query performance

There are several ways to improve query performance and to achieve the high-speed performance that SQL Data Warehouse is designed to provide.  

### See the effect of scaling on query performance 

One way to improve query performance is to scale resources by changing the DWU service level for your data warehouse. Each service level costs more, but you can scale back or pause resources at any time. 

In this step, you compare performance at two different DWU settings.

First, let's scale the sizing down to 100 DWU so we can get an idea of how one compute node might perform on its own.

1. Go to the portal and select your SQL Data Warehouse.

2. Select scale in the SQL Data Warehouse blade. 

    ![Scale DW From portal](./media/sql-data-warehouse-get-started-tutorial/scale-dw.png)

3. Scale down the performance bar to 100 DWU and hit save.

    ![Scale and save](./media/sql-data-warehouse-get-started-tutorial/scale-and-save.png)

4. Wait for your scale operation to finish.

    > [!NOTE]
    > Queries cannot run while changing the scale. Scaling **kills** your currently running queries. You can restart them when the operation is finished.
    >
    
5. Do a scan operation on the trip data, selecting the top million entries for all the columns. If you're eager to move on quickly, feel free to select fewer rows. Take note of the time it takes to run this operation.

    ```sql
    SELECT TOP(1000000) * FROM dbo.[Trip]
    ```
6. Scale your data warehouse back to 400 DWU. Remember, each 100 DWU is adding another compute node to your Azure SQL Data Warehouse.

7. Run the query again! You should notice a significant difference. 

> [!NOTE]
> Since SQL Data Warehouse uses massively parallel processing. Queries that scan or perform analytic functions on millions of rows experience the true power of
> Azure SQL Data Warehouse.
>

### See the effect of statistics on query performance

1. Run a query that joins the Date table with the Trip table

    ```sql
    SELECT TOP (1000000) 
        dt.[DayOfWeek],
        tr.[MedallionID],
        tr.[HackneyLicenseID],
        tr.[PickupTimeID],
        tr.[DropoffTimeID],
        tr.[PickupGeographyID],
        tr.[DropoffGeographyID],
        tr.[PickupLatitude],
        tr.[PickupLongitude],
        tr.[PickupLatLong],
        tr.[DropoffLatitude],
        tr.[DropoffLongitude],
        tr.[DropoffLatLong],
        tr.[PassengerCount],
        tr.[TripDurationSeconds],
        tr.[TripDistanceMiles],
        tr.[PaymentType],
        tr.[FareAmount],
        tr.[SurchargeAmount],
        tr.[TaxAmount],
        tr.[TipAmount],
        tr.[TollsAmount],
        tr.[TotalAmount]
    FROM [dbo].[Trip] as tr
        JOIN dbo.[Date] as dt
        ON  tr.DateID = dt.DateID
    ```

    This query takes a while because SQL Data Warehouse has to shuffle data before it can perform the join. Joins do not have to shuffle data if they are designed to join data in the same way it is distributed. That's a deeper subject. 

2. Statistics make a difference. 
3. Run this statement to create statistics on the join columns.

    ```sql
    CREATE STATISTICS [dbo.Date DateID stats] ON dbo.Date (DateID);
    CREATE STATISTICS [dbo.Trip DateID stats] ON dbo.Trip (DateID);
    ```

    > [!NOTE]
    > SQL DW does not automatically manage statistics for you. Statistics are important for query
    > performance and it is highly recommended you create and update statistics.
    > 
    > **You gain the most benefit by having statistics on columns involved in joins, columns
    > used in the WHERE clause and columns found in GROUP BY.**
    >

3. Run the query from Prerequisites again and observe any performance differences. While the differences in query performance will not be as drastic as scaling up, you should notice a  speed-up. 

## Next steps

You're now ready to query and explore. Check out our best practices or tips.

If you're done exploring for the day, make sure to pause your instance! In production, you can experience enormous 
savings by pausing and scaling to meet your business needs.

![Pause](./media/sql-data-warehouse-get-started-tutorial/pause.png)

## Useful readings

[Concurrency and Workload Management][]

[Best practices for Azure SQL Data Warehouse][]

[Query Monitoring][]

[Top 10 Best Practices for Building a Large Scale Relational Data Warehouse][]

[Migrating Data to Azure SQL Data Warehouse][]

[Concurrency and Workload Management]: sql-data-warehouse-develop-concurrency.md#change-a-user-resource-class-example
[Best practices for Azure SQL Data Warehouse]: sql-data-warehouse-best-practices.md#hash-distribute-large-tables
[Query Monitoring]: sql-data-warehouse-manage-monitor.md
[Top 10 Best Practices for Building a Large Scale Relational Data Warehouse]: https://blogs.msdn.microsoft.com/sqlcat/2013/09/16/top-10-best-practices-for-building-a-large-scale-relational-data-warehouse/
[Migrating Data to Azure SQL Data Warehouse]: https://blogs.msdn.microsoft.com/sqlcat/2016/08/18/migrating-data-to-azure-sql-data-warehouse-in-practice/



[!INCLUDE [Additional Resources](../../includes/sql-data-warehouse-article-footer.md)]

<!-- Internal Links -->
[Prerequisites]: sql-data-warehouse-get-started-tutorial.md#prerequisites

<!--Other Web references-->
[Visual Studio]: https://www.visualstudio.com/
[SQL Server Management Studio]: https://msdn.microsoft.com/en-us/library/mt238290.aspx

