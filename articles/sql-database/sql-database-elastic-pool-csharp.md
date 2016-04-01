<properties
    pageTitle="C# 資料庫開發：彈性資料庫集區 | Microsoft Azure"
    description="使用 C# 資料庫開發技術來建立 Azure SQL Database 彈性資料庫集區，讓您可以跨多個資料庫共用資源。"
    services="sql-database"
    keywords="c# database,sql development"
    documentationCenter=""
    authors="stevestein"
    manager="jeffreyg"
    editor=""/>

<tags
    ms.service="sql-database"
    ms.devlang="NA"
    ms.topic="article"
    ms.tgt_pltfrm="powershell"
    ms.workload="data-management"
    ms.date="12/01/2015"
    ms.author="sstein"/>

# C&#x23; 資料庫開發：為 SQL 資料庫建立和設定彈性資料庫集區

> [AZURE.SELECTOR]
- [Azure 入口網站](sql-database-elastic-pool-portal.md)
- [C#](sql-database-elastic-pool-csharp.md)
- [PowerShell](sql-database-elastic-pool-powershell.md)


本文將說明如何建立 [彈性資料庫集區](sql-database-elastic-pool.md) 從使用 C# 應用程式資料庫的 SQL 資料庫開發技巧。

> [AZURE.NOTE] 彈性資料庫集區目前的預覽版，且僅能搭配 SQL Database V12 伺服器。 如果您有 SQL Database V11 伺服器可以 [使用 PowerShell 升級至 V12 並建立集區](sql-database-upgrade-server.md) 一次。

這些範例使用 [Azure SQL Database Library for.NET](https://www.nuget.org/packages/Microsoft.Azure.Management.Sql)。
為了清楚起見，將個別程式碼片段分別列出，範例主控台應用程式會將所有命令整合在本文底端的區段中。

Azure SQL Database Library for.NET 提供 [Azure 資源管理員](resource-group-overview.md)-api，它會包裝 [資源管理員為基礎 SQL Database REST API](https://msdn.microsoft.com/library/azure/mt163571.aspx)。 此用戶端程式庫遵循資源管理員式用戶端程式庫的常見模式。 資源管理員需要資源群組，並且向 [Azure Active Directory](https://msdn.microsoft.com/library/azure/mt168838.aspx) (AAD)。

<br>

> [AZURE.NOTE] SQL Database Library for.NET 目前為預覽狀態。

<br>

如果您沒有 Azure 訂用帳戶，只需按一下 **免費試用版** 頂端的這個頁面，然後再回來這篇文章。 以及一份免費的 Visual Studio，請參閱 [Visual Studio 下載](https://www.visualstudio.com/downloads/download-visual-studio-vs) 頁面。

## 安裝必要的程式庫

取得必要的管理程式庫安裝下列封裝使用 [封裝管理員主控台](http://docs.nuget.org/Consume/Package-Manager-Console) SQL 開發 ︰

    Install-Package Microsoft.Azure.Management.Sql –Pre
    Install-Package Microsoft.Azure.Management.Resources –Pre
    Install-Package Microsoft.Azure.Common.Authentication –Pre


## 使用 Azure Active Directory 設定驗證

在您開始以 C# 進行 SQL 開發之前，您必須在 Azure 入口網站中完成一些工作。 首先，請設定必要的驗證以讓您的應用程式能夠存取 REST API。

 [Azure 資源管理員 REST Api](https://msdn.microsoft.com/library/azure/dn948464.aspx) 使用 Azure Active Directory 進行驗證，而不是舊版的 Azure 服務管理 REST Api 所使用的憑證。

若要根據目前的使用者驗證您的用戶端應用程式，您必須先在與訂用帳戶 (已在其下建立 Azure 資源) 相關聯的 AAD 網域中註冊您的應用程式。 如果您的 Azure 訂用帳戶是以 Microsoft 帳戶而不是工作或學校帳戶建立的，您便已經有預設 AAD 網域。 註冊應用程式可以透過程式 [傳統入口網站](https://manage.windowsazure.com/)。

若要建立新的應用程式並且在正確的 Active Directory 中註冊，請執行下列動作：

1. 捲動尋找左側功能表 **Active Directory** 服務，並將它開啟。

    ![C# SQL 資料庫開發：Active Directory 設定][1]

2. 選取要驗證您的應用程式，然後按一下它的目錄的 **名稱**。

    ![選取目錄。][4]

3. 在 [目錄] 頁面中，按一下 [ **應用程式**。

    ![按一下 [應用程式]。][5]

4. 按一下 [ **新增** 建立新的應用程式。

    ![按一下 [加入] 按鈕：建立 C# 應用程式。][6]

5. 選取 **加入我的組織正在開發的應用程式**。

5. 提供 **名稱** 應用程式，並選取 **原生用戶端應用程式**。

    ![新增應用程式][7]

6. 提供 **重新導向 URI**。 它不需要是實際的端點，只要是有效的 URI 即可。

    ![新增應用程式][8]

7. 完成建立應用程式中，按一下 **設定**, ，並複製 **用戶端識別碼** （您將程式碼中需要用戶端識別碼）。

    ![取得用戶端識別碼][9]


1. 在頁面底部按一下 **新增應用程式**。
1. 選取 **Microsoft 應用程式**。
1. 選取 **Azure 服務管理 API**, ，然後完成精靈。
2. 選取 API 之後，您現在必須授與存取這個 API，藉由選取所需的特定權限 **存取 Azure 服務管理 （預覽）**。

    ![設定權限][2]

2. 按一下 [ **儲存**。



### 識別網域名稱

您的程式碼需要網域名稱。 可以輕易地識別正確的網域名稱的方式是：

1. 移至 [Azure 入口網站](https://portal.azure.com)。
2. 將滑鼠停留在右上角的名稱，並記下出現在快顯視窗的網域。 取代 **domain.onmicrosoft.com** 您帳戶的值與下面程式碼片段。

    ![識別網域名稱][3]



**其他 AAD 資源**  

使用 Azure Active Directory 驗證可以找到中的其他資訊 [此有用的部落格文章](http://www.cloudidentity.com/blog/2013/09/12/active-directory-authentication-library-adal-v1-for-net-general-availability/)。


### 擷取目前使用者的存取權杖

用戶端應用程式必須擷取目前使用者的應用程式存取權杖。 使用者第一次執行程式碼時，系統會提示使用者輸入其使用者認證，產生的權杖會在本機快取。 後續執行會從快取擷取權杖，並且在權杖過期時僅提示使用者登入。


    /// <summary>
    /// Prompts for user credentials when first run or if the cached credentials have expired.
    /// </summary>
    /// <returns>The access token from AAD.</returns>
    private static AuthenticationResult GetAccessToken()
    {
        AuthenticationContext authContext = new AuthenticationContext
            ("https://login.windows.net/" /* AAD URI */
                + "domain.onmicrosoft.com" /* Tenant ID or AAD domain */);

        AuthenticationResult token = authContext.AcquireToken
            ("https://management.azure.com/"/* the Azure Resource Management endpoint */,
                "aa00a0a0-a0a0-0000-0a00-a0a00000a0aa" /* application client ID from AAD*/,
        new Uri("urn:ietf:wg:oauth:2.0:oob") /* redirect URI */,
        PromptBehavior.Auto /* with Auto user will not be prompted if an unexpired token is cached */);

        return token;
    }



> [AZURE.NOTE] 這篇文章中的範例使用每個 API 要求的同步表單，並封鎖，直到呼叫基礎服務上的其餘部分完成。 有可用的非同步方法。



## 建立資源群組

使用資源管理員時，必須在資源群組中建立所有資源。 資源群組是保留應用程式相關資源的容器。 若要建立彈性資料庫集區，您需要一個在現有資源群組中的 Azure SQL Database 伺服器。 請執行以下 C# 程式碼來建立資源群組：


    // Create a resource management client
    ResourceManagementClient resourceClient = new ResourceManagementClient(new TokenCloudCredentials("XXXXXXXX-XXXX-XXXX-XXXX-XXXXXXXXXXXX" /*subscription id*/, token.AccessToken ));

    // Resource group parameters
    ResourceGroup resourceGroupParameters = new ResourceGroup()
    {
        Location = "South Central US"
    };

    //Create a resource group
    var resourceGroupResult = resourceClient.ResourceGroups.CreateOrUpdate("resourcegroup-name", resourceGroupParameters);



## 建立伺服器

彈性資料庫集區包含在 Azure SQL Database 伺服器內，因此下一步就是建立伺服器。 伺服器名稱在全域的所有 Azure SQL Server 中必須是唯一的，因此如果伺服器名稱已被採用，您會收到錯誤。 另外值得注意的是，此命令可能需要數分鐘才能完成。 為了讓應用程式能夠連線到伺服器，您必須也在伺服器上建立防火牆規則，以允許從用戶端 IP 位址進行存取。


    // Create a SQL Database management client
    SqlManagementClient sqlClient = new SqlManagementClient(new TokenCloudCredentials("XXXXXXXX-XXXX-XXXX-XXXX-XXXXXXXXXXXX" /* Subscription id*/, token.AccessToken));

    // Create a server
    ServerCreateOrUpdateParameters serverParameters = new ServerCreateOrUpdateParameters()
    {
        Location = "South Central US",
        Properties = new ServerCreateOrUpdateProperties()
        {
            AdministratorLogin = "ServerAdmin",
            AdministratorLoginPassword = "P@ssword1",
            Version = "12.0"
        }
    };

    var serverResult = sqlClient.Servers.CreateOrUpdate("resourcegroup-name", "server-name", serverParameters);




## 設定伺服器防火牆規則以允許存取伺服器

伺服器預設並沒有防火牆規則，因此無法從任何位置連線到伺服器。 若要連接到伺服器，或任何資料庫的伺服器上， [防火牆規則](sql-database-firewall-configure.md) 必須定義，可讓您存取用戶端 IP 位址。

下列範例會建立一個允許從任何 IP 位址存取伺服器的伺服器防火牆規則。 建議您建立適當的 SQL 登入和密碼以保護您的資料庫，不要依賴防火牆規則做為防禦入侵的主要防衛措施。 如需詳細資訊，請參閱 [管理資料庫和登入 Azure SQL Database 中的](sql-database-manage-logins.md)。


    // Create a firewall rule on the server to allow TDS connection
    FirewallRuleCreateOrUpdateParameters firewallParameters = new FirewallRuleCreateOrUpdateParameters()
    {
        Properties = new FirewallRuleCreateOrUpdateProperties()
        {
            StartIpAddress = "0.0.0.0",
            EndIpAddress = "255.255.255.255"
        }
    };

    var firewallResult = sqlClient.FirewallRules.CreateOrUpdate("resourcegroup-name", "server-name", "FirewallRule1", firewallParameters);




若要允許其他 Azure 服務存取伺服器，則新增防火牆規則並且將 tartIpAddress 和 EndIpAddress 都設為 0.0.0.0。 請注意，這可讓 Azure 流量從 *任何* Azure 訂閱，才能存取伺服器。


## 建立資料庫

下列範例會建立一個新的「基本」資料庫；如果伺服器上已經有相同名稱的資料庫存在，則會更新現有的資料庫。

        // Create a database

        // Retrieve the server on which the database will be created
        Server currentServer = sqlClient.Servers.Get("resourcegroup-name", "server-name").Server;

        // Create a database: configure create or update parameters and properties explicitly
        DatabaseCreateOrUpdateParameters newDatabaseParameters = new DatabaseCreateOrUpdateParameters()
        {
            Location = currentServer.Location,
            Properties = new DatabaseCreateOrUpdateProperties()
            {
                Edition = "Basic",
                RequestedServiceObjectiveName = "Basic",
                MaxSizeBytes = 2147483648,
                Collation = "SQL_Latin1_General_CP1_CI_AS"
            }
        };

        var dbResponse = sqlClient.Databases.CreateOrUpdate("resourcegroup-name", "server-name", "Database1", newDatabaseParameters);



## 建立彈性資料庫集區

下列範例會建立一個新的彈性資料庫集區：



    // Create elastic pool: configure create or update parameters and properties explicitly
    ElasticPoolCreateOrUpdateParameters newPoolParameters = new ElasticPoolCreateOrUpdateParameters()
    {
        Location = "South Central US",
        Properties = new ElasticPoolCreateOrUpdateProperties()
        {
            Edition = "Standard",
            Dtu = 100,  // alternatively set StorageMB, if both are specified they must agree based on the eDTU:storage ratio of the edition
            DatabaseDtuMin = 0,
            DatabaseDtuMax = 100
         }
    };

    // Create the pool
    var newPoolResponse = sqlClient.ElasticPools.CreateOrUpdate("resourcegroup-name", "server-name", "ElasticPool1", newPoolParameters);


## 更新彈性資料庫集區

下列範例會更新現有彈性資料庫集區的效能特性：

    // Retrieve existing pool properties
    var currentPool = sqlClient.ElasticPools.Get("resourcegroup-name", "server-name", "ElasticPool1").ElasticPool;

    // Configure create or update parameters with existing property values, override those to be changed.
    ElasticPoolCreateOrUpdateParameters updatePoolParameters = new ElasticPoolCreateOrUpdateParameters()
    {
        Location = currentPool.Location,
        Properties = new ElasticPoolCreateOrUpdateProperties()
        {
            Edition = currentPool.Properties.Edition,
            DatabaseDtuMax = 50, /* Setting DatabaseDtuMax to 50 limits the eDTUs that any one database can consume */
            DatabaseDtuMin = 10, /* Setting DatabaseDtuMin above 0 limits the number of databases that can be stored in the pool */
            Dtu = (int)currentPool.Properties.Dtu,
            StorageMB = currentPool.Properties.StorageMB,  /* For a Standard pool there is 1 GB of storage per eDTU. */
        }
    };

    newPoolResponse = sqlClient.ElasticPools.CreateOrUpdate("resourcegroup-name", "server-name", "ElasticPool1", newPoolParameters);



## 將現有資料庫移入彈性資料庫集區

*建立集區後您也可以使用 TRANSACT-SQL 將現有的資料庫移入和移出集區。 如需詳細資訊，請參閱 [彈性資料庫集區參考 TRANSACT-SQL](sql-database-elastic-pool-reference.md#Transact-SQL)。*

下列範例會將現有的 Azure SQL 資料庫移到集區中：


    // Update database service objective to add the database to a pool

    // Retrieve current database properties
    currentDatabase = sqlClient.Databases.Get("resourcegroup-name", "server-name", "Database1").Database;

    // Configure create or update parameters with existing property values, override those to be changed.
    DatabaseCreateOrUpdateParameters updatePooledDbParameters = new DatabaseCreateOrUpdateParameters()
    {
        Location = currentDatabase.Location,
        Properties = new DatabaseCreateOrUpdateProperties()
        {
            Edition = "Standard",
            RequestedServiceObjectiveName = "ElasticPool",
            ElasticPoolName = "ElasticPool1",
            MaxSizeBytes = currentDatabase.Properties.MaxSizeBytes,
            Collation = currentDatabase.Properties.Collation,
        }
    };

    // Update the database
    var dbUpdateResponse = sqlClient.Databases.CreateOrUpdate("resourcegroup-name", "server-name", "Database1", updatePooledDbParameters);




## 在彈性資料庫集區中建立新的資料庫

*建立集區後您也可以使用 TRANSACT-SQL 建立新的彈性資料庫集區中。 如需詳細資訊，請參閱 [彈性資料庫集區參考 TRANSACT-SQL](sql-database-elastic-pool-reference.md#Transact-SQL)。*

下列範例會直接在集區中建立一個新的資料庫：


    // Create a new database in the pool

    // Create a database: configure create or update parameters and properties explicitly
    DatabaseCreateOrUpdateParameters newPooledDatabaseParameters = new DatabaseCreateOrUpdateParameters()
    {
        Location = currentServer.Location,
        Properties = new DatabaseCreateOrUpdateProperties()
        {
            Edition = "Standard",
            RequestedServiceObjectiveName = "ElasticPool",
            ElasticPoolName = "ElasticPool1",
            MaxSizeBytes = 268435456000, // 250 GB,
            Collation = "SQL_Latin1_General_CP1_CI_AS"
        }
    };

    var poolDbResponse = sqlClient.Databases.CreateOrUpdate("resourcegroup-name", "server-name", "Database2", newPooledDatabaseParameters);



## 列出彈性資料庫集區中的所有資料庫

下列範例會列出集區中的所有資料庫：

    //List databases in the elastic pool
    DatabaseListResponse dbListInPool = sqlClient.ElasticPools.ListDatabases("resourcegroup-name", "server-name", "ElasticPool1");
    Console.WriteLine("Databases in Elastic Pool {0}", "server-name.ElasticPool1");
    foreach (Database db in dbListInPool)
    {
        Console.WriteLine("  Database {0}", db.Name);
    }




## 範例主控台應用程式


    using Microsoft.Azure;
    using Microsoft.Azure.Management.Resources;
    using Microsoft.Azure.Management.Resources.Models;
    using Microsoft.Azure.Management.Sql;
    using Microsoft.Azure.Management.Sql.Models;
    using Microsoft.IdentityModel.Clients.ActiveDirectory;
    using System;
    using System.Security;

    namespace AzureSqlDatabaseRestApiExamples
    {
    class Program
    {
        /// <summary>
        /// Prompts for user credentials when first run or if the cached credentials have expired.
        /// </summary>
        /// <returns>The access token from AAD.</returns>
        private static AuthenticationResult GetAccessToken()
        {
            AuthenticationContext authContext = new AuthenticationContext
                ("https://login.windows.net/" /* AAD URI */
                + "domain.onmicrosoft.com" /* Tenant ID or AAD domain */);

            AuthenticationResult token = authContext.AcquireToken
                ("https://management.azure.com/"/* the Azure Resource Management endpoint */,
                "XXXXXXXX-XXXX-XXXX-XXXX-XXXXXXXXXXXX" /* application client ID from AAD*/,
                new Uri("urn:ietf:wg:oauth:2.0:oob") /* redirect URI */,
                PromptBehavior.Auto /* with Auto user will not be prompted if an unexpired token is cached */);

            return token;
        }

        private static AuthenticationResult GetAccessTokenUsingUserCredentials(UserCredential userCredential)
        {
            AuthenticationContext authContext = new AuthenticationContext
                ("https://login.windows.net/" /* AAD URI */
                + "YOU.onmicrosoft.com" /* Tenant ID or AAD domain */);

            AuthenticationResult token = authContext.AcquireToken(
                "https://management.azure.com/"/* the Azure Resource Management endpoint */,
                "XXXXXXXX-XXXX-XXXX-XXXX-XXXXXXXXXXXX" /* application client ID from AAD*/,
                userCredential);

            return token;
        }
        private static SecureString convertToSecureString(string secret)
        {
            var secureStr = new SecureString();
            if (secret.Length > 0)
            {
                foreach (var c in secret.ToCharArray()) secureStr.AppendChar(c);
            }
            return secureStr;
        }

        static void Main(string[] args)
        {
            var token = GetAccessToken();

            // Who am I?
            Console.WriteLine("Identity is {0} {1}", token.UserInfo.GivenName, token.UserInfo.FamilyName);
            Console.WriteLine("Token expires on {0}", token.ExpiresOn);
            Console.WriteLine("");

            // Create a resource management client
            ResourceManagementClient resourceClient = new ResourceManagementClient(new TokenCloudCredentials("XXXXXXXX-XXXX-XXXX-XXXX-XXXXXXXXXXXX" /*subscription id*/, token.AccessToken));

            // Resource group parameters
            ResourceGroup resourceGroupParameters = new ResourceGroup()
            {
                Location = "South Central US"
            };

            //Create a resource group
            var resourceGroupResult = resourceClient.ResourceGroups.CreateOrUpdate("resourcegroup-name", resourceGroupParameters);

            Console.WriteLine("Resource group {0} create or update completed with status code {1} ", resourceGroupResult.ResourceGroup.Name, resourceGroupResult.StatusCode);

            //create a SQL Database management client
            TokenCloudCredentials tokenCredentials = new TokenCloudCredentials("XXXXXXXX-XXXX-XXXX-XXXX-XXXXXXXXXXXX" /* Subscription id*/, token.AccessToken);

            SqlManagementClient sqlClient = new SqlManagementClient(tokenCredentials);

            // Create a server
            ServerCreateOrUpdateParameters serverParameters = new ServerCreateOrUpdateParameters()
            {
                Location = "South Central US",
                Properties = new ServerCreateOrUpdateProperties()
                {
                    AdministratorLogin = "ServerAdmin",
                    AdministratorLoginPassword = "P@ssword1",
                    Version = "12.0"
                }
            };

            var serverResult = sqlClient.Servers.CreateOrUpdate("resourcegroup-name", "server-name", serverParameters);

            var serverGetResult = sqlClient.Servers.Get("resourcegroup-name", "server-name");


            Console.WriteLine("Server {0} create or update completed with status code {1}", serverResult.Server.Name, serverResult.StatusCode);

            // Create a firewall rule on the server to allow TDS connection

            FirewallRuleCreateOrUpdateParameters firewallParameters = new FirewallRuleCreateOrUpdateParameters()
            {
                Properties = new FirewallRuleCreateOrUpdateProperties()
                {
                    StartIpAddress = "0.0.0.0",
                    EndIpAddress = "255.255.255.255"
                }
            };

            var firewallResult = sqlClient.FirewallRules.CreateOrUpdate("resourcegroup-name", "server-name", "FirewallRule1", firewallParameters);

            Console.WriteLine("Firewall rule {0} create or update completed with status code {1}", firewallResult.FirewallRule.Name, firewallResult.StatusCode);


            var dbResponse = sqlClient.Databases.CreateOrUpdate("resourcegroup-name", "server-name", "Database1", newDatabaseParameters);

            Console.WriteLine("Database {0} create or update completed with status code {1}. Service Objective {2} ", dbResponse.Database.Name, dbResponse.StatusCode, dbResponse.Database.Properties.ServiceObjective);


           // Create an elastic database pool
            var newPoolResponse = sqlClient.ElasticPools.CreateOrUpdate("resourcegroup-name", "server-name", "ElasticPool1", newPoolParameters);

            Console.WriteLine("Elastic pool {0} create or update completed with status code {1}.", newPoolResponse.ElasticPool.Name, newPoolResponse.StatusCode);

            // Update pool: retrieve existing pool properties
            var currentPool = sqlClient.ElasticPools.Get("resourcegroup-name", "server-name", "ElasticPool1").ElasticPool;

            // Update pool: configure create or update parameters with existing property values, override those to be changed.
            ElasticPoolCreateOrUpdateParameters updatePoolParameters = new ElasticPoolCreateOrUpdateParameters()
            {
                Location = currentPool.Location,
                Properties = new ElasticPoolCreateOrUpdateProperties()
                {
                    Edition = currentPool.Properties.Edition,
                    DatabaseDtuMax = 50, /* Setting DatabaseDtuMax to 50 limits the eDTUs that any one database can consume */
                    DatabaseDtuMin = 10, /* Setting DatabaseDtuMin above 0 limits the number of databases that can be stored in the pool */
                    Dtu = (int)currentPool.Properties.Dtu,
                    StorageMB = currentPool.Properties.StorageMB,  /* For a Standard pool there is 1 GB of storage per eDTU. */
                }
            };
            newPoolResponse = sqlClient.ElasticPools.CreateOrUpdate("resourcegroup-name", "server-name", "ElasticPool1", newPoolParameters);

            Console.WriteLine("Elastic pool {0} create or update completed with status code {1}.", newPoolResponse.ElasticPool.Name, newPoolResponse.StatusCode);

            // Update a databases service objective to add the database to a pool

            // Update database: retrieve current database properties
            currentDatabase = sqlClient.Databases.Get("resourcegroup-name", "server-name", "Database1").Database;

            // Update database: configure create or update parameters with existing property values, override those to be changed.
            DatabaseCreateOrUpdateParameters updatePooledDbParameters = new DatabaseCreateOrUpdateParameters()
            {
                Location = currentDatabase.Location,
                Properties = new DatabaseCreateOrUpdateProperties()
                {
                    Edition = "Standard",
                    RequestedServiceObjectiveName = "ElasticPool",
                    ElasticPoolName = "ElasticPool1",
                    MaxSizeBytes = currentDatabase.Properties.MaxSizeBytes,
                    Collation = currentDatabase.Properties.Collation,
                }
            };


            // Create a new database in the pool

            // Create a database: configure create or update parameters and properties explicitly
            DatabaseCreateOrUpdateParameters newPooledDatabaseParameters = new DatabaseCreateOrUpdateParameters()
            {
                Location = currentServer.Location,
                Properties = new DatabaseCreateOrUpdateProperties()
                {
                    Edition = "Standard",
                    RequestedServiceObjectiveName = "ElasticPool",
                    ElasticPoolName = "ElasticPool1",
                    MaxSizeBytes = 268435456000, // 250 GB,
                    Collation = "SQL_Latin1_General_CP1_CI_AS"
                }
            };

            var poolDbResponse = sqlClient.Databases.CreateOrUpdate("resourcegroup-name", "server-name", "Database2", newPooledDatabaseParameters);

            Console.WriteLine("Database {0} create or update completed with status code {1}. Service Objective: {2}({3}) ", poolDbResponse.Database.Name, poolDbResponse.StatusCode, poolDbResponse.Database.Properties.ServiceObjective, poolDbResponse.Database.Properties.ElasticPoolName);
      }
    }







## 其他資源


[SQL Database](https://azure.microsoft.com/documentation/services/sql-database/)

[Azure 資源管理 API](https://msdn.microsoft.com/library/azure/dn948464.aspx)

[彈性資料庫集區參考](sql-database-elastic-pool-reference.md)。


<!--Image references-->
[1]: ./media/sql-database-elastic-pool-csharp/aad.png
[2]: ./media/sql-database-elastic-pool-csharp/permissions.png
[3]: ./media/sql-database-elastic-pool-csharp/getdomain.png
[4]: ./media/sql-database-elastic-pool-csharp/aad2.png
[5]: ./media/sql-database-elastic-pool-csharp/aad-applications.png
[6]: ./media/sql-database-elastic-pool-csharp/add.png
[7]: ./media/sql-database-elastic-pool-csharp/add-application.png
[8]: ./media/sql-database-elastic-pool-csharp/add-application2.png
[9]: ./media/sql-database-elastic-pool-csharp/clientid.png


