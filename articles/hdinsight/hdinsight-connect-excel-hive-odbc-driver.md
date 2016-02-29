<properties
   pageTitle="使用 Hive ODBC 驅動程式將 Excel 連接到 Hadoop | Microsoft Azure"
   description="了解如何在 Excel 中設定和使用 Microsoft Hive ODBC 驅動程式來查詢 HDInsight 叢集中的資料。"
   services="hdinsight"
   documentationCenter=""
   authors="mumian"
   manager="paulettm"
   tags="azure-portal"
   editor="cgronlun"/>

<tags
   ms.service="hdinsight"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="big-data"
   ms.date="10/15/2015"
   ms.author="jgao"/>

#使用 Microsoft Hive ODBC 驅動程式將 Excel 連接到 Hadoop

[AZURE.INCLUDE [ODBC-JDBC-selector](../../includes/hdinsight-selector-odbc-jdbc.md)]

Microsoft 的巨量資料解決方案整合 Microsoft 商務智慧 (BI) 元件與 Azure HDInsight 所部署的 Apache Hadoop 叢集。 舉例來說，此整合可讓您使用 Microsoft Hive 開放式資料庫連接 (ODBC) 驅動程式，將 Excel 連接到 HDInsight 上 Hadoop 叢集的 Hive 資料倉儲。

您也可以從 Excel 使用 Microsoft Power Query for Excel，連接與 HDInsight 叢集和其他資料來源 (包括其他 (非 HDInsight) Hadoop 叢集) 相關聯的資料。 如需安裝和使用 Power Query 的資訊，請參閱 [Excel 連接到 HDInsight 使用 Power Query][hdinsight-power-query]。

> [AZURE.NOTE] 雖然您可以使用這份文件中的步驟與 Linux 或 Windows 為基礎的 HDInsight 叢集，Windows 就會是用戶端工作站需要有。

**必要條件**:

開始閱讀本文之前，您必須符合下列必要條件：

- **HDInsight 叢集**。 若要建立一個帳戶，請參閱 [開始使用 Azure HDInsight][hdinsight-get-started]。
- **工作站** 與 Office 2013 Professional Plus、 Office 365 Pro Plus、 Excel 2013 Standalone 或 Office 2010 Professional Plus。


##安裝 Microsoft Hive ODBC 驅動程式

下載並安裝 Microsoft Hive ODBC 驅動程式從 [下載中心][hive-odbc-driver-download]。

此驅動程式可以安裝在 32 位元或 64 位元版本的 Windows 7、Windows 8、Windows 10、Windows Server 2008 R2 和 Windows Server 2012，並且允許連接到 Azure HDInsight (1.6 版及更新版本) 與 Azure HDInsight Emulator (v.1.0.0.0 和更新版本)。 您應安裝與您要使用 ODBC 驅動程式的應用程式版本相符的版本。 本教學課程將會從 Office Excel 使用此驅動程式。

##建立 Hive ODBC 資料來源

下列步驟將說明如何建立 Hive ODBC 資料來源。

1. 從 Windows 8 或 Windows 10 中，按視窗鍵以開啟 [開始] 畫面，然後鍵入 **資料來源**。
2. 按一下 [ **設定 ODBC 資料來源 (32 位元)** 或 **設定 ODBC 資料來源 (64 位元)** Office 版本而定。 如果您使用 Windows 7，請選擇 **ODBC 資料來源 (32 位元)** 或 **ODBC 資料來源 (64 位元)** 從 **系統管理工具**。 這會啟動 **ODBC 資料來源管理員** ] 對話方塊。

    ![OBDC data source administrator][img-hdi-simbahiveodbc-datasource-admin]

3. 按一下 [使用者 dns **新增** 開啟 **建立新的資料來源** 精靈。
4. 選取 **Microsoft Hive ODBC 驅動程式**, ，然後按一下 [ **完成**。 這會啟動 **Microsoft Hive ODBC 驅動程式 DNS 設定** ] 對話方塊。

5. 輸入或選取下列值：

屬性|說明
---|---
資料來源名稱|為資料來源指定名稱
Host|Enter <HDInsightClusterName>。 n e t。 例如，myHDICluster.azurehdinsight.net
連接埠|使用 <strong>443</strong>. (此連接埠已從 563 變更為 443。)
資料庫|使用 <strong>預設值</strong>.
Hive 伺服器類型|選取 <strong>Hive 伺服器 2</strong>
機制|選取 <strong>Azure HDInsight 服務</strong>
HTTP 路徑|保留為空白。
使用者名稱|輸入 HDInsight 叢集使用者的使用者名稱。 這是在叢集佈建程序中所建立的使用者名稱。 如果您使用快速建立選項，預設使用者名稱是 <strong>admin</strong>.
密碼|輸入 HDInsight 叢集使用者的密碼。
</table>

要注意當您按一下的某些重要參數 **進階選項**:

參數|說明
---|---
使用原生查詢|選取此選項時，ODBC 驅動程式不會嘗試將 TSQL 轉換為 HiveQL。 只有在百分之百確定您所提交的是純 HiveQL 陳述式時，才應使用此選項。 連接到 SQL Server 或 Azure SQL Database 時，您應將其保留為未勾選。
每個區塊擷取的資料列|在擷取大量記錄時可能必須調整此參數，以確保最佳效能。
預設字串資料行長度、二進位資料行長度、十進位資料行小數位數|資料類型的長度和精確度可能會影響傳回資料的方式。 如果失去精確度且 (或) 發生截斷狀況，將會傳回不正確的資訊。


    ![Advanced options][img-HiveOdbc-DataSource-AdvancedOptions]

6. 按一下 [ **測試** 以測試資料來源。 當資料來源已正確設定時，它會顯示 *測試順利完成!*。
7. 按一下 [ **確定** 以關閉 [測試] 對話方塊。 新的資料來源應該現在會列示在 **ODBC 資料來源管理員**。
8. 按一下 [ **確定** 以結束精靈。

##從 HDInsight 叢集將資料匯入 Excel 中

下列步驟將說明，如何使用您在前述步驟中建立的 ODBC 資料來源，將資料從 Hive 資料表匯入 Excel 活頁簿中。

1. 在 Excel 中開啟新的或現有的活頁簿。
2. 從 **資料** 索引標籤上，按一下 [ **從其他資料來源**, ，然後按一下 [ **從資料連接精靈** 啟動 **資料連線精靈**。

    ![Open data connection wizard][img-hdi-simbahiveodbc.excel.dataconnection]

3. 選取 **ODBC DSN** 做為資料來源，然後按一下 **下一步**。
4. 從 ODBC 資料來源中，選取您在上一個步驟中建立的資料來源名稱，然後按一下 [ **下一步**。
5. 重新輸入在精靈中，叢集的密碼，然後按一下 **測試** 來視需要再次驗證組態。
6. 按一下 [ **確定** 以關閉 [測試] 對話方塊。
7. 按一下 [ **確定**。 等候 **選取資料庫及資料表** 對話方塊開啟。 這可能需要幾秒鐘的時間。
8. 選取您想要匯入，然後按一下資料表 **下一步**。  *Hivesampletable* 是 HDInsight 叢集隨附的範例 hive 資料表。  您可以選擇此資料表 (如果尚未建立)。 如需有關執行 Hive 查詢及建立 Hive 資料表，請參閱 [搭配 HDInsight 使用 Hive][hdinsight-use-hive]。
8. 按一下 [ **完成**。
9. 在 **匯入資料** ] 對話方塊中，您可以變更或指定的查詢。 若要這樣做，請按一下 **屬性**。 這可能需要幾秒鐘的時間。
10. 按一下 [ **定義** 索引標籤，然後再附加 **LIMIT 200** hive 控制檔中，選取陳述式中的 **命令文字** 文字方塊。 此修改會將傳回的記錄數限定為 200 個。

    ![Connection Properties][img-hdi-simbahiveodbc-excel-connectionproperties]

11. 按一下 [ **確定** 關閉連接屬性] 對話方塊。
12. 按一下 [ **確定** 關閉 **匯入資料** ] 對話方塊。  
13. 重新輸入密碼，然後再按一下 **確定**。 經過數秒後，資料即會匯入至 Excel。

##後續步驟

在本文中，您已了解到如何使用 Microsoft Hive ODBC 驅動程式將 HDInsight 服務中的資料擷取至 Excel。 同樣地，您也可以將 HDInsight 服務中的資料擷取至 SQL Database。 此外也可以將資料上傳至 HDInsight 服務。 若要深入了解，請參閱：

- [使用 HDInsight 分析班機延誤資料][hdinsight-analyze-flight-data]
- [將資料上傳到 HDInsight][hdinsight-upload-data]
- [搭配 HDInsight 使用 Sqoop] [hdinsight-use-sqoop]


[hdinsight-use-sqoop]: hdinsight-use-sqoop.md
[hdinsight-analyze-flight-data]: hdinsight-analyze-flight-delay-data.md
[hdinsight-use-hive]: hdinsight-use-hive.md
[hdinsight-upload-data]: hdinsight-upload-data.md
[hdinsight-power-query]: hdinsight-connect-excel-power-query.md
[hdinsight-get-started]: hdinsight-hadoop-tutorial-get-started-windows.md

[hive-odbc-driver-download]: http://go.microsoft.com/fwlink/?LinkID=286698

[img-hdi-simbahiveodbc-datasource-admin]: ./media/hdinsight-connect-excel-hive-ODBC-driver/HDI.SimbaHiveOdbc.DataSourceAdmin1.png
[img-HiveOdbc-DataSource-AdvancedOptions]: ./media/hdinsight-connect-excel-hive-ODBC-driver/HDI.HiveOdbc.DataSource.AdvancedOptions1.png
[img-hdi-simbahiveodbc-excel-connectionproperties]: ./media/hdinsight-connect-excel-hive-ODBC-driver/HDI.SimbaHiveODBC.Excel.ConnectionProperties1.png
[img-hdi-simbahiveodbc.excel.dataconnection]: ./media/hdinsight-connect-excel-hive-ODBC-driver/HDI.SimbaHiveOdbc.Excel.DataConnection1.png

