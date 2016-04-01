<properties 
    pageTitle="使用 Data Factory SDK 來建立、監視及管理 Azure Data Factory" 
    description="了解如何使用 Data Factory .NET SDK，以程式設計方式建立、監視和管理 Azure Data Factory。" 
    services="data-factory" 
    documentationCenter="" 
    authors="spelluru" 
    manager="jhubbard" 
    editor="monicar"/>

<tags 
    ms.service="data-factory" 
    ms.workload="data-services" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="10/06/2015" 
    ms.author="spelluru"/>

# 使用 Data Factory .NET SDK 來建立、監視及管理 Azure Data Factory
## 概觀
您可以使用 Data Factory .NET SDK，以程式設計方式建立、監視及管理 Azure Data Factory 本文包含指導您建立範例 .NET 主控台應用程式的逐步解說，此應用程式將會建立並監視 Data Factory。 請參閱 [Data Factory 類別庫參考][adf-class-library-reference] Data Factory.NET SDK 的詳細資料。 



## 必要條件

- Visual Studio 2012 或 2013
- 下載並安裝 [Azure.NET SDK][azure-developer-center]
- 下載並安裝適用於 Azure Data Factory 的 NuGet 封裝 逐步解說中包含相關指示。

## 逐步介紹
1. 使用 Visual Studio 2012 或 2013 來建立 C# .NET 主控台應用程式。
    <ol type="a">
        <li>啟動 <b>Visual Studio 2012</b> 或 <b>Visual Studio 2013</b>。</li>
        <li>按一下 [ <b>檔案</b>, ，指向 [ <b>新增</b>, ，然後按一下 <b>專案</b>。</li> 
        <li>展開 <b>範本</b>, ，然後選取 <b>Visual C#</b>。 在此逐步解說中，您使用的是 C#，但您可以使用任何 .NET 語言。</li> 
        <li>選取 <b>主控台應用程式</b> 從右邊的專案類型清單。</li>
        <li>輸入 <b>DataFactoryAPITestApp</b> 的 <b>名稱</b>。</li> 
        <li>選取 <b>C:\ADFGetStarted</b> 的 <b>位置</b>。</li>
        <li>按一下 [ <b>確定</b> 以建立專案。</li>
    </ol>
2. 按一下 [<b>工具</b>]，指向 [<b>NuGet 封裝管理員</b>]，然後按一下 [<b>封裝管理員主控台</b>]。
3.  在 [Package Manager Console]<b></b> 中，逐一執行下列命令。</b>。 

        Install-Package Microsoft.Azure.Management.DataFactories
        Install-Package Microsoft.IdentityModel.Clients.ActiveDirectory
6. 新增下列 **appSetttings** 區段 **App.config** 檔案。 Helper 方法會使用這些 ︰ **GetAuthorizationHeader**。 

    值取代 **SubscriptionId** 和 **ActiveDirectoryTenantId** 與您的 Azure 訂用帳戶和租用戶識別碼。 您可以取得這些值執行 **Get-azureaccount** 從 Azure PowerShell （您可能需要先使用 Add-azureaccount 登入）。
 
        <appSettings>
            <!--CSM Prod related values-->
            <add key="ActiveDirectoryEndpoint" value="https://login.windows.net/" />
            <add key="ResourceManagerEndpoint" value="https://management.azure.com/" />
            <add key="WindowsManagementUri" value="https://management.core.windows.net/" />
            <add key="AdfClientId" value="1950a258-227b-4e31-a9cf-717495945fc2" />
            <add key="RedirectUri" value="urn:ietf:wg:oauth:2.0:oob" />
            <!--Make sure to write your own tenenat id and subscription ID here-->
            <add key="SubscriptionId" value="your subscription ID" />
            <add key="ActiveDirectoryTenantId" value="your tenant ID" />
        </appSettings>
6. 新增下列 **使用** 原始程式檔 (Program.cs) 專案中的陳述式。

        using System.Threading;
        using System.Configuration;
        using System.Collections.ObjectModel;
        
        using Microsoft.Azure.Management.DataFactories;
        using Microsoft.Azure.Management.DataFactories.Models;
        using Microsoft.Azure.Management.DataFactories.Common.Models;
        
        using Microsoft.IdentityModel.Clients.ActiveDirectory;
        using Microsoft.Azure;
6. 加入下列程式碼所建立的執行個體  **DataPipelineManagementClient** 類別 **Main** 方法。 您將使用此物件來建立 Data Factory、連結的服務、輸入和輸出資料集，以及管線。 您也將使用此物件來監視執行階段的資料集配量。    

        // create data factory management client
        string resourceGroupName = "resourcegroupname";
        string dataFactoryName = "APITutorialFactorySP";

        TokenCloudCredentials aadTokenCredentials =
            new TokenCloudCredentials(
                ConfigurationManager.AppSettings["SubscriptionId"],
                GetAuthorizationHeader());

        Uri resourceManagerUri = new Uri(ConfigurationManager.AppSettings["ResourceManagerEndpoint"]);

        DataFactoryManagementClient client = new DataFactoryManagementClient(aadTokenCredentials, resourceManagerUri);

    > [AZURE.NOTE] 取代 **resourcegroupname** 與 Azure 資源群組的名稱。 您可以建立資源群組使用 [New-azureresourcegroup](https://msdn.microsoft.com/library/Dn654594.aspx) 指令程式。

7. 新增下列程式碼會建立 **資料 factory** 至 **Main** 方法。

        // create a data factory
        Console.WriteLine("Creating a data factory");
        client.DataFactories.CreateOrUpdate(resourceGroupName,
            new DataFactoryCreateOrUpdateParameters()
            {
                DataFactory = new DataFactory()
                {
                    Name = dataFactoryName,
                    Location = "westus",
                    Properties = new DataFactoryProperties() { }
                }
            }
        );

8. 新增下列程式碼會建立 **連結服務** 至 **Main** 方法。 

    > [AZURE.NOTE] 使用 **帳戶名稱** 和 **帳戶金鑰** 您 Azure 儲存體帳戶的 **ConnectionString**。 

        // create a linked service
        Console.WriteLine("Creating a linked service");
        client.LinkedServices.CreateOrUpdate(resourceGroupName, dataFactoryName,
            new LinkedServiceCreateOrUpdateParameters()
            {
                LinkedService = new LinkedService()
                {
                    Name = "LinkedService-AzureStorage",
                    Properties = new LinkedServiceProperties
                    (
                        new AzureStorageLinkedService("DefaultEndpointsProtocol=https;AccountName=<account name>;AccountKey=<account key>")
                    )
                }
            }
        );
9. 新增下列程式碼會建立 **輸入和輸出資料集** 至 **Main** 方法。 

    請注意， **FolderPath** 輸入的 blob 設為 **adftutorial /** 其中 **adftutorial** 是 blob 儲存體中容器的名稱。 如果此容器不存在於 Azure blob 儲存體中，以下列名稱建立容器 ︰ **adftutorial** 將文字檔上傳至容器。
    
    請注意，輸出 blob 的 FolderPath 設為 ︰ **adftutorial/apifactoryoutput / {Slice}** 其中 **配量** 動態計算的值根據 **SliceStart** （開始日期時間的每個配量）。  

 
        // create input and output datasets
        Console.WriteLine("Creating input and output datasets");
        string Dataset_Source = "DatasetBlobSource";
        string Dataset_Destination = "DatasetBlobDestination";

        client.Datasets.CreateOrUpdate(resourceGroupName, dataFactoryName,
            new DatasetCreateOrUpdateParameters()
            {
                Dataset = new Dataset()
                {
                    Name = Dataset_Source,
                    Properties = new DatasetProperties()
                    {
                        LinkedServiceName = "LinkedService-AzureStorage",
                        TypeProperties = new AzureBlobDataset()
                        {
                            FolderPath = "adftutorial/",
                            FileName = "emp.txt"
                        },
                        External = true,
                        Availability = new Availability()
                        {
                            Frequency = SchedulePeriod.Hour,
                            Interval = 1,
                        },

                        Policy = new Policy()
                        {
                            Validation = new ValidationPolicy()
                            {
                                MinimumRows = 1
                            }
                        }
                    }
                }
            });

        client.Datasets.CreateOrUpdate(resourceGroupName, dataFactoryName,
            new DatasetCreateOrUpdateParameters()
            {
                Dataset = new Dataset()
                {
                    Name = Dataset_Destination,
                    Properties = new DatasetProperties()
                    {

                        LinkedServiceName = "LinkedService-AzureStorage",
                        TypeProperties = new AzureBlobDataset()
                        {
                            FolderPath = "adftutorial/apifactoryoutput/{Slice}",
                            PartitionedBy = new Collection<Partition>()
                            {
                                new Partition()
                                {
                                    Name = "Slice",
                                    Value = new DateTimePartitionValue()
                                    {
                                        Date = "SliceStart",
                                        Format = "yyyyMMdd-HH"
                                    }
                                }
                            }
                        },

                        Availability = new Availability()
                        {
                            Frequency = SchedulePeriod.Hour,
                            Interval = 1,
                        },
                    }
                }
            });

11. 加入下列程式碼的 **會建立並啟用管線** 至 **Main** 方法。 此管線有 **CopyActivity** 採用 **BlobSource** 做為來源和 **BlobSink** 為接收器。

            // create a pipeline
        Console.WriteLine("Creating a pipeline");
        DateTime PipelineActivePeriodStartTime = new DateTime(2014, 8, 9, 0, 0, 0, 0, DateTimeKind.Utc);
        DateTime PipelineActivePeriodEndTime = PipelineActivePeriodStartTime.AddMinutes(60);
        string PipelineName = "PipelineBlobSample";

        client.Pipelines.CreateOrUpdate(resourceGroupName, dataFactoryName,
            new PipelineCreateOrUpdateParameters()
            {
                Pipeline = new Pipeline()
                {
                    Name = PipelineName,
                    Properties = new PipelineProperties()
                    {
                        Description = "Demo Pipeline for data transfer between blobs",

                        // Initial value for pipeline's active period. With this, you won't need to set slice status
                        Start = PipelineActivePeriodStartTime,
                        End = PipelineActivePeriodEndTime,

                        Activities = new List<Activity>()
                        {                                
                            new Activity()
                            {   
                                Name = "BlobToBlob",
                                Inputs = new List<ActivityInput>()
                                {
                                    new ActivityInput() {
                                        Name = Dataset_Source
                                    }
                                },
                                Outputs = new List<ActivityOutput>()
                                {
                                    new ActivityOutput()
                                    {
                                        Name = Dataset_Destination
                                    }
                                },
                                TypeProperties = new CopyActivity()
                                {
                                    Source = new BlobSource(),
                                    Sink = new BlobSink()
                                    {
                                        WriteBatchSize = 10000,
                                        WriteBatchTimeout = TimeSpan.FromMinutes(10)
                                    }
                                }
                            }

                        },
                    }
                }
            });

    

12. 新增下列 helper 方法使用 **Main** 方法 **程式** 類別。 此方法會顯示一個對話方塊，讓您提供方塊 **使用者名稱** 和 **密碼** 您用來登入 Azure 傳統入口網站。 
 
        public static string GetAuthorizationHeader()
        {
            AuthenticationResult result = null;
            var thread = new Thread(() =>
            {
                try
                {
                    var context = new AuthenticationContext(ConfigurationManager.AppSettings["ActiveDirectoryEndpoint"] + ConfigurationManager.AppSettings["ActiveDirectoryTenantId"]);

                    result = context.AcquireToken(
                        resource: ConfigurationManager.AppSettings["WindowsManagementUri"],
                        clientId: ConfigurationManager.AppSettings["AdfClientId"],
                        redirectUri: new Uri(ConfigurationManager.AppSettings["RedirectUri"]),
                        promptBehavior: PromptBehavior.Always);
                }
                catch (Exception threadEx)
                {
                    Console.WriteLine(threadEx.Message);
                }
            });

            thread.SetApartmentState(ApartmentState.STA);
            thread.Name = "AcquireTokenThread";
            thread.Start();
            thread.Join();

            if (result != null)
            {
                return result.AccessToken;
            }

            throw new InvalidOperationException("Failed to acquire token");
        }  
 
13. 加入下列程式碼以 **Main** 方法來取得輸出資料集的資料配量的狀態。 在此範例中只預期有配量。   
 
        // Pulling status within a timeout threshold
        DateTime start = DateTime.Now;
        bool done = false;

        while (DateTime.Now - start < TimeSpan.FromMinutes(5) && !done)
        {
            Console.WriteLine("Pulling the slice status");
            // wait before the next status check
            Thread.Sleep(1000 * 12);

            var datalistResponse = client.DataSlices.List(resourceGroupName, dataFactoryName, Dataset_Destination,
                new DataSliceListParameters()
                {
                    DataSliceRangeStartTime = PipelineActivePeriodStartTime.ConvertToISO8601DateTimeString(),
                    DataSliceRangeEndTime = PipelineActivePeriodEndTime.ConvertToISO8601DateTimeString()
                });

            foreach (DataSlice slice in datalistResponse.DataSlices)
            {
                if (slice.State == DataSliceState.Failed || slice.State == DataSliceState.Ready)
                {
                    Console.WriteLine("Slice execution is done with status: {0}", slice.State);
                    done = true;
                    break;
                }
                else
                {
                    Console.WriteLine("Slice status is: {0}", slice.State);
                }
            }
        }

14. **（選擇性）** 加入下列程式碼，取得執行資料配量配量的詳細資料 **Main** 方法。

        Console.WriteLine("Getting run details of a data slice");

        // give it a few minutes for the output slice to be ready
        Console.WriteLine("\nGive it a few minutes for the output slice to be ready and press any key.");
        Console.ReadKey();

        var datasliceRunListResponse = client.DataSliceRuns.List(
                resourceGroupName,
                dataFactoryName,
                Dataset_Destination,
                new DataSliceRunListParameters()
                {
                    DataSliceStartTime = PipelineActivePeriodStartTime.ConvertToISO8601DateTimeString()
                }
            );
        
        foreach (DataSliceRun run in datasliceRunListResponse.DataSliceRuns)
        {
            Console.WriteLine("Status: \t\t{0}", run.Status);
            Console.WriteLine("DataSliceStart: \t{0}", run.DataSliceStart);
            Console.WriteLine("DataSliceEnd: \t\t{0}", run.DataSliceEnd);
            Console.WriteLine("ActivityId: \t\t{0}", run.ActivityName);
            Console.WriteLine("ProcessingStartTime: \t{0}", run.ProcessingStartTime);
            Console.WriteLine("ProcessingEndTime: \t{0}", run.ProcessingEndTime);
            Console.WriteLine("ErrorMessage: \t{0}", run.ErrorMessage);
        }

        Console.WriteLine("\nPress any key to exit.");
        Console.ReadKey();

15. 在 [方案總管] 中，展開專案 (**DataFactoryAPITestApp**)，以滑鼠右鍵按一下 **參考**, ，然後按一下 **加入參考**。 選取核取方塊 **System.Configuration** 組件，然後按一下 [ **確定**。 
16. 建置主控台應用程式。 按一下 [ **建置** 功能表，然後按一下 [ **建置方案**。 
16. 確認您 Azure Blob 儲存體之 adftutorial 容器中至少有一個檔案。 如果沒有，請在「記事本」中以下列內容建立 Emp.txt 檔案，然後將它上傳至 adftutorial 容器。

        John, Doe
        Jane, Doe
     
17. 執行範例時，即可 **偵錯** ]-> [ **開始偵錯** ] 功能表上。 當您看到 **取得執行資料配量的詳細資料**, ，等待幾分鐘，再按 **ENTER**。 
18. 使用 Azure 入口網站確認 data factory: **APITutorialFactory** 建立使用下列成品 ︰ 
    - 連結服務 ︰ **LinkedService_AzureStorage** 
    - 資料集 ︰ **DatasetBlobSource** 和 **DatasetBlobDestination**。
    - 管線 ︰ **PipelineBlobSample** 
18. 確認輸出檔案已建立在 **apifactoryoutput** 資料夾中的 **adftutorial** 容器。



> [AZURE.NOTE] 上述範例程式碼會啟動對話方塊，供您輸入 Azure 認證。 如果您需要登入以程式設計的方式而不使用對話方塊中，請參閱 [驗證服務主體與 Azure 資源管理員](resource-group-authenticate-service-principal.md#authenticate-service-principal-with-certificate---powershell)。 


[data-factory-introduction]: data-factory-introduction.md
[adf-getstarted]: data-factory-get-started.md
[adf-tutorial]: data-factory-tutorial.md
[use-custom-activities]: data-factory-use-custom-activities.md
[developer-reference]: http://go.microsoft.com/fwlink/?LinkId=516908
 
[adf-class-library-reference]: http://go.microsoft.com/fwlink/?LinkID=521877
[azure-developer-center]: http://azure.microsoft.com/downloads/
 


