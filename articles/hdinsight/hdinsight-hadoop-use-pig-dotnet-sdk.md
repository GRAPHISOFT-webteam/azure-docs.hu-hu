<properties
   pageTitle="在 HDInsight 中搭配使用 Hadoop Pig 與 .NET | Microsoft Azure"
   description="了解如何使用 .NET SDK for Hadoop 將 Pig 工作提交至 HDInsight 上的 Hadoop。"
   services="hdinsight"
   documentationCenter=".net"
   authors="Blackmist"
   manager="paulettm"
   editor="cgronlun"
   tags="azure-portal"/>

<tags
   ms.service="hdinsight"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="big-data"
   ms.date="12/04/2015"
   ms.author="larryfr"/>


# 在 HDInsight 中使用 .NET SDK for Hadoop 執行 Pig 工作

[AZURE.INCLUDE [pig-selector](../../includes/hdinsight-selector-use-pig.md)]

本文件提供使用 .NET SDK for Hadoop 將 Pig 工作提交至 HDInsight 叢集上的 Hadoop 的範例。

HDInsight .NET SDK 提供 .NET 用戶端程式庫，讓您輕鬆地從 .NET 使用 HDInsight 叢集。 Pig 可讓您透過建立一系列資料轉換的模型，來建立 MapReduce 作業。 您將學習如何使用基本 C# 應用程式將 Pig 工作提交至 HDInsight 叢集。

[AZURE.INCLUDE [azure-portal](../../includes/hdinsight-azure-portal.md)]

* [使用.NET SDK 在 HDInsight 中 hadoop 執行 Pig 工作](hdinsight-hadoop-use-pig-dotnet-sdk-v1.md)

## <a id="prereq"></a>必要條件

若要完成這篇文章中的步驟，您需要下列項目。

* Azure HDInsight (HDInsight 上的 Hadoop) 叢集 (Windows 或 Linux 型)

* Visual Studio 2012、2013 或 2015

## <a id="certificate"></a>建立管理憑證

若要向 Azure HDInsight 驗證應用程式，您必須建立自我簽署憑證、將它安裝在您的開發工作站上，同時將它上傳至您的 Azure 訂閱。

如需有關如何執行這項操作的指示，請參閱 [建立自我簽署的憑證](http://go.microsoft.com/fwlink/?LinkId=511138)。
> [AZURE.NOTE] 建立憑證時，請一定要記下您使用的易記名稱，供稍後使用。

## <a id="subscriptionid"></a>尋找訂閱識別碼

每個 Azure 訂閱都是透過 GUID 值 (稱為訂閱識別碼) 予以識別。 請使用下列步驟來尋找此值。

1. 請瀏覽 [Azure 入口網站 ][preview-portal]。

2. 從入口網站的左側列選取 [瀏覽全部]____，然後從 [瀏覽]____ 刀鋒視窗選取 [訂用帳戶]____。

3. 在 [訂用帳戶]____ 刀鋒視窗所呈現的資訊中，找出您要使用的訂用帳戶，並記下 [訂用帳戶 ID]**** 欄中的值。

儲存訂閱識別碼，供稍後使用。

## <a id="create"></a>建立應用程式

1. 開啟 Visual Studio 2012 或 2013
2. 從 [**檔案**] 功能表中，選取 [**新增**]，然後選取 [**專案**]。
3. 對於新的專案，輸入或選取下列值。

   <table>
    <tr>
    <th>屬性</th>
    <th>值</th>
    </tr>
    <tr>
    <th>類別</th>
    <th>範本/Visual C#/Windows</th>
    </tr>
    <tr>
    <th>範本</th>
    <th>主控台應用程式</th>
    </tr>
    <tr>
    <th>名稱</th>
    <th>SubmitPigJob</th>
    </tr>
    </table>
4. 按一下 [確定]**** 以建立專案。
5. 從 [**工具**] 功能表中，選取 [**程式庫封裝管理員**] 或 [**Nuget 封裝管理員**]，然後選取 [**封裝管理員主控台**]。
6. 在主控台中執行下列命令，以安裝 .NET SDK 封裝。

        Install-Package Microsoft.Azure.Management.HDInsight.Job -Pre

7. 在 [方案總管] 中，按兩下 **Program.cs** 加以開啟。 將現有程式碼取代為下者。

     using System;
     using Microsoft.Azure.Management.HDInsight.Job;
     using Microsoft.Azure.Management.HDInsight.Job.Models;
     using Hyak.Common;
    
     namespace HDInsightSubmitPigJobsDotNet
     {
         class Program
         {
             static void Main(string[] args)
             {
                 var ExistingClusterName = "<HDInsightClusterName>";
                 var ExistingClusterUri = ExistingClusterName + ".azurehdinsight.net";
                 var ExistingClusterUsername = "<HDInsightClusterHttpUsername>";
                 var ExistingClusterPassword = "<HDInsightClusterHttpUserPassword>";
    
                 // The Pig Latin statements to run
                 string queryString = "LOGS = LOAD 'wasb:///example/data/sample.log';" +
                     "LEVELS = foreach LOGS generate REGEX_EXTRACT($0, '(TRACE|DEBUG|INFO|WARN|ERROR|FATAL)', 1)  as LOGLEVEL;" +
                     "FILTEREDLEVELS = FILTER LEVELS by LOGLEVEL is not null;" +
                     "GROUPEDLEVELS = GROUP FILTEREDLEVELS by LOGLEVEL;" +
                     "FREQUENCIES = foreach GROUPEDLEVELS generate group as LOGLEVEL, COUNT(FILTEREDLEVELS.LOGLEVEL) as COUNT;" +
                     "RESULT = order FREQUENCIES by COUNT desc;" +
                     "DUMP RESULT;";
    
    
                 HDInsightJobManagementClient _hdiJobManagementClient;
                 var clusterCredentials = new BasicAuthenticationCloudCredentials { Username = ExistingClusterUsername, Password = ExistingClusterPassword };
                 _hdiJobManagementClient = new HDInsightJobManagementClient(ExistingClusterUri, clusterCredentials);
    
                 // Define the Pig job
                 var parameters = new PigJobSubmissionParameters()
                 {
                     UserName = ExistingClusterUsername,
                     Query = queryString,
                 };
    
                 System.Console.WriteLine("Submitting the Sqoop job to the cluster...");
                 var response = _hdiJobManagementClient.JobManagement.SubmitPigJob(parameters);
                 System.Console.WriteLine("Validating that the response is as expected...");
                 System.Console.WriteLine("Response status code is " + response.StatusCode);
                 System.Console.WriteLine("Validating the response object...");
                 System.Console.WriteLine("JobId is " + response.JobSubmissionJsonResponse.Id);
                 Console.WriteLine("Press ENTER to continue ...");
                 Console.ReadLine();
             }
         }
     }

7. 按 **F5** 啟動應用程式。
8. 按 **ENTER** 結束應用程式。

## <a id="summary"></a>摘要

如您所見，.NET SDK for Hadoop 可讓您建立 .NET 應用程式，以將 Pig 工作提交至 HDInsight 叢集、監視工作狀態，以及擷取輸出。

## <a id="nextsteps"></a>後續步驟

如需 HDInsight 中 Pig 的一般資訊。

* [搭配使用 Pig 與 HDInsight 上的 Hadoop](hdinsight-use-pig.md)

如需您可以在 HDInsight 上使用 Hadoop 之其他方式的詳細資訊。

* [使用 Hive 與 HDInsight 上的 Hadoop](hdinsight-use-hive.md)

* [搭配使用 MapReduce 與 HDInsight 上的 Hadoop](hdinsight-use-mapreduce.md)

[preview-portal]: https://portal.azure.com/ 

