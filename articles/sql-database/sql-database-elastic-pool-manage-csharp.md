<properties
    pageTitle="C#으로 탄력적 데이터베이스 풀 모니터링 및 관리 | Microsoft Azure"
    description="C# 데이터베이스 개발 기술을 사용하여 Azure SQL 데이터베이스 탄력적 데이터베이스 풀을 관리합니다."
    services="sql-database"
    documentationCenter=""
    authors="srinia"
    manager="jhubbard"
    editor=""/>

<tags
    ms.service="sql-database"
    ms.devlang="NA"
    ms.topic="article"
    ms.tgt_pltfrm="csharp"
    ms.workload="data-management"
    ms.date="07/05/2016"
    ms.author="srinia"/>

# C&#x23;으로 탄력적 데이터베이스 풀 모니터링 및 관리 

> [AZURE.SELECTOR]
- [Azure 포털](sql-database-elastic-pool-manage-portal.md)
- [PowerShell](sql-database-elastic-pool-manage-powershell.md)
- [C#](sql-database-elastic-pool-manage-csharp.md)
- [T-SQL](sql-database-elastic-pool-manage-tsql.md)


C&#x23;을 사용하여 [탄력적 데이터베이스 풀](sql-database-elastic-pool.md)을 관리하는 방법을 알아봅니다.

일반적인 오류 코드는 [SQL 데이터베이스 클라이언트 응용 프로그램의 SQL 오류 코드: 데이터베이스 연결 오류 및 기타 문제](sql-database-develop-error-messages.md)를 참조하세요.

아래 예제는 [.NET용 SQL 데이터베이스 라이브러리](https://msdn.microsoft.com/library/azure/mt349017.aspx)를 사용하므로 아직 설치되지 않은 경우 계속하기 전에 이 라이브러리를 설치해야 합니다. Visual Studio의 [패키지 관리자 콘솔](http://docs.nuget.org/Consume/Package-Manager-Console)(**도구** > **NuGet 패키지 관리자** > **패키지 관리자 콘솔**)에서 다음 명령을 실행하여 이 라이브러리를 설치할 수 있습니다.

    PM> Install-Package Microsoft.Azure.Management.Sql –Pre


## 탄력적 풀로 데이터베이스 이동

풀 안팎에서 독립 실행형 데이터베이스를 이동할 수 있습니다.

    // Retrieve current database properties.

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

    // Update the database.
    var dbUpdateResponse = sqlClient.Databases.CreateOrUpdate("resourcegroup-name", "server-name", "Database1", updatePooledDbParameters);

## 탄력적 풀에 데이터베이스 나열

풀에 있는 모든 데이터베이스를 검색하려면 [ListDatabases](https://msdn.microsoft.com/library/microsoft.azure.management.sql.elasticpooloperationsextensions.listdatabases) 메서드를 호출합니다.

    //List databases in the elastic pool
    DatabaseListResponse dbListInPool = sqlClient.ElasticPools.ListDatabases("resourcegroup-name", "server-name", "ElasticPool1");
    Console.WriteLine("Databases in Elastic Pool {0}", "server-name.ElasticPool1");
    foreach (Database db in dbListInPool)
    {
        Console.WriteLine("  Database {0}", db.Name);
    }

## 풀의 성능 설정 변경

기존 풀 속성을 검색합니다. 값을 수정하고 CreateOrUpdate 메서드를 실행합니다.

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


## 탄력적 풀 작업의 대기 시간

- 데이터베이스당 최소 eDTU 또는 데이터베이스당 최대 eDTU를 변경하는 작업은 일반적으로 5분 이내에 완료됩니다.
- 풀당 eDTU를 변경하는 작업은 풀에 있는 모든 데이터베이스에서 사용한 공간의 전체 크기에 따라 달라집니다. 변경 시간은 100GB당 평균 90분 이하입니다. 예를 들어 풀에 있는 모든 데이터베이스에서 사용 중인 총 공간이 200GB일 경우, 풀당 풀 eDTU를 변경하는 예상 대기 시간은 3시간 이하입니다.


## 풀 C&#x23; 예제 관리

이 예제를 실행하려면 다음 라이브러리가 필요합니다. Visual Studio의 [패키지 관리자 콘솔](http://docs.nuget.org/Consume/Package-Manager-Console)(**도구** > **NuGet 패키지 관리자** > **패키지 관리자 콘솔**)에서 다음 명령을 실행하여 설치할 수 있습니다.

    PM> Install-Package Microsoft.Azure.Management.Sql –Pre
    PM> Install-Package Microsoft.Azure.Management.Resources –Pre
    PM> Install-Package Microsoft.Azure.Common.Authentication –Pre

콘솔 앱을 만들고 Program.cs의 내용을 다음으로 바꿉니다. 필요한 클라이언트 ID 및 관련 값을 가져오려면 [앱 등록 및 SQL 데이터베이스에 앱을 연결하는 데 필요한 클라이언트 값 가져오기](sql-database-client-id-keys.md)를 참조하세요.

    using Microsoft.Azure;
    using Microsoft.Azure.Management.Resources;
    using Microsoft.Azure.Management.Resources.Models;
    using Microsoft.Azure.Management.Sql;
    using Microsoft.Azure.Management.Sql.Models;
    using Microsoft.IdentityModel.Clients.ActiveDirectory;
    using System;
    
    namespace SqlDbElasticPoolsSample
    {
    class Program
    {
        // pool variables
        static string poolName = "elasticPool1";
        static string poolEdition = "Standard";
        static int poolDtus = 400;
        static int databaseMinDtus = 0;
        static int databaseMaxDtus = 100;

        // authentication variables
        static string subscriptionId = "<your Azure subscription id>";
        static string clientId = "<your client id>";
        static string redirectUri = "<your redirect URI>";
        static string domainName = "<i.e. microsoft.onmicrosoft.com>";
        static AuthenticationResult token;

        // resource group variables
        static string datacenterLocation = "Japan West";
        static string resourceGroupName = "<resource group name>";

        // server variables
        static string serverName = "<server name>";
        static string adminLogin = "<server admin>";
        static string adminPassword = "<server admin password>";
        static string serverVersion = "12.0";

        // database variables
        static string databaseName = "<database name>";


        static void Main(string[] args)
        {
            // Sign in to Azure
            token = GetAccessToken();
            Console.WriteLine("Logged in as: " + token.UserInfo.DisplayableId);

            // Create a resource group
            ResourceGroup rg = CreateResourceGroup();
            Console.WriteLine(rg.Name + " created.");

            // Create a server
            Console.WriteLine("Creating server... ");
            ServerGetResponse srvr = CreateServer();
            Console.WriteLine("Creation of server " + srvr.Server.Name + ": " + srvr.StatusCode.ToString());

            // Create a pool
            Console.WriteLine("Creating elastic database pool with 400 pool eDTUs... ");
            ElasticPoolCreateOrUpdateResponse epool = CreateElasticDatabasePool();
            Console.WriteLine("Creation of pool " + epool.ElasticPool.Name + ": " + epool.Status.ToString());

            // Open the portal so we can see our operations in action
            string portalPage = @"https://portal.azure.com/#resource/subscriptions/"
                + subscriptionId
                + @"/resourceGroups/"
                + resourceGroupName
                + @"/providers/Microsoft.Sql/servers/"
                + serverName
                + @"/elasticPools/"
                + poolName ;
            System.Diagnostics.Process.Start(portalPage);


            // Pause the console until Enter is pressed.
            Console.WriteLine("Press Enter to update the pool to 1200 pool eDTUs.");
            Console.ReadLine();

            // Update the pool
            Console.WriteLine("Updating elastic database pool to 1200 pool eDTUs...");
            ElasticPoolCreateOrUpdateResponse epool2 = UpdateElasticDatabasePool();
            Console.WriteLine("Update of pool " + epool2.ElasticPool.Name + ": " + epool2.Status.ToString());

            // Create a new database in the pool
            Console.WriteLine("Creating a new database in the pool... ");
            DatabaseCreateOrUpdateResponse db = CreateNewDatabaseInPool();
            Console.WriteLine("Creation of elastic database " + db.Database.Name + ": " + db.Status.ToString());

            // Move an existing database into pool
            Console.WriteLine("Creating a new database, stand-alone, not yet in the pool... ");
            DatabaseCreateOrUpdateResponse db2 = CreateStandAloneDatabase();
            Console.WriteLine("Creation of stand-alone database " + db2.Database.Name + ": " + db2.Status.ToString());

            // Pause the console until Enter is pressed.
            Console.WriteLine("Press Enter to move our existing db into the pool.");
            Console.ReadLine();

            Console.WriteLine("Moving stand-alone database into the pool... ");
            DatabaseCreateOrUpdateResponse db3 = MoveExistingDatabaseIntoPool();
            Console.WriteLine("Moving stand-alone database " + db3.Database.Name + ": " + db3.Status.ToString());

            // Pause the console until Enter is pressed.
            Console.WriteLine("Press Enter to list all databases in {0}.", poolName);
            Console.ReadLine();

            // List all databases in a pool
            DatabaseListResponse dbs = GetDbsInPool();
            Console.WriteLine("Databases in Elastic Pool {0}", poolName);
            foreach (Database db4 in dbs)
            {
                Console.WriteLine("- " + db4.Name);
            }

            // Pause the console until Enter is pressed.
            Console.WriteLine("Press Enter to exit.");
            Console.ReadLine();
        }


        static ElasticPoolCreateOrUpdateResponse CreateElasticDatabasePool()
        {
            // Create a SQL Database management client
            SqlManagementClient sqlClient = new SqlManagementClient(new TokenCloudCredentials(subscriptionId, token.AccessToken));

            // Create elastic pool: configure create or update parameters and properties explicitly
            ElasticPoolCreateOrUpdateParameters newPoolParameters = new ElasticPoolCreateOrUpdateParameters()
            {
                Location = datacenterLocation,
                Properties = new ElasticPoolCreateOrUpdateProperties()
                {
                    Edition = poolEdition,
                    Dtu = poolDtus, 
                    DatabaseDtuMin = databaseMinDtus,
                    DatabaseDtuMax = databaseMaxDtus
                }
            };

            // Create the pool
            var newPoolResponse = sqlClient.ElasticPools.CreateOrUpdate(resourceGroupName, serverName, poolName, newPoolParameters);
            return newPoolResponse;
        }

        static ElasticPoolCreateOrUpdateResponse UpdateElasticDatabasePool()
        {
            int newPoolDtus = 1200;
            int newDatabaseMinDtus = 10;
            int newDatabaseMaxDtus = 50;

            // Create a SQL Database management client
            SqlManagementClient sqlClient = new SqlManagementClient(new TokenCloudCredentials(subscriptionId, token.AccessToken));

            // Retrieve existing pool
            var currentPool = sqlClient.ElasticPools.Get(resourceGroupName, serverName, poolName).ElasticPool;

            // Configure create or update parameters with existing property values, override those to be changed.
            ElasticPoolCreateOrUpdateParameters updatePoolParameters = new ElasticPoolCreateOrUpdateParameters()
            {
                Location = currentPool.Location,
                Properties = new ElasticPoolCreateOrUpdateProperties()
                {
                    Edition = currentPool.Properties.Edition,
                    DatabaseDtuMax = newDatabaseMaxDtus,
                    DatabaseDtuMin = newDatabaseMinDtus,
                    Dtu = newPoolDtus
                }
            };
            var updatePoolResponse = sqlClient.ElasticPools.CreateOrUpdate(resourceGroupName, serverName, poolName, updatePoolParameters);
            return updatePoolResponse;
        }

        static DatabaseCreateOrUpdateResponse CreateNewDatabaseInPool()
        {
            databaseName = "newDbInThePool";

            // Create a SQL Database management client
            SqlManagementClient sqlClient = new SqlManagementClient(new TokenCloudCredentials(subscriptionId, token.AccessToken));

            // Retrieve current pool
            var currentPool = sqlClient.ElasticPools.Get(resourceGroupName, serverName, poolName).ElasticPool;

            // Create a database
            DatabaseCreateOrUpdateParameters databaseParameters = new DatabaseCreateOrUpdateParameters()
            {
                Location = currentPool.Location,
                Properties = new DatabaseCreateOrUpdateProperties()
                {
                    CreateMode = "Default",
                    Edition = currentPool.Properties.Edition,
                    ElasticPoolName = poolName
                }
            };

            var dbResult = sqlClient.Databases.CreateOrUpdate(resourceGroupName, serverName, databaseName, databaseParameters);
            return dbResult;
        }

        static DatabaseCreateOrUpdateResponse MoveExistingDatabaseIntoPool()
        {
            databaseName = "standaloneDB";

            // Create a SQL Database management client
            SqlManagementClient sqlClient = new SqlManagementClient(new TokenCloudCredentials(subscriptionId, token.AccessToken));

            // Retrieve current database
            var currentDatabase = sqlClient.Databases.Get(resourceGroupName, serverName, databaseName).Database;

            // Retrieve current pool
            var currentPool = sqlClient.ElasticPools.Get(resourceGroupName, serverName, poolName).ElasticPool;

            // Configure create or update parameters with existing property values, override those to be changed.
            DatabaseCreateOrUpdateParameters updatePooledDbParameters = new DatabaseCreateOrUpdateParameters()
            {
                Location = currentDatabase.Location,
                Properties = new DatabaseCreateOrUpdateProperties()
                {
                    Edition = currentPool.Properties.Edition,
                    RequestedServiceObjectiveName = "ElasticPool",
                    ElasticPoolName = currentPool.Name,
                    MaxSizeBytes = currentDatabase.Properties.MaxSizeBytes,
                    Collation = currentDatabase.Properties.Collation,
                }
            };
            // Update the database
            var dbUpdateResponse = sqlClient.Databases.CreateOrUpdate(resourceGroupName, serverName, databaseName, updatePooledDbParameters);
            return dbUpdateResponse;
        }

        static DatabaseListResponse GetDbsInPool()
        {
            // Create a SQL Database management client
            SqlManagementClient sqlClient = new SqlManagementClient(new TokenCloudCredentials(subscriptionId, token.AccessToken));

            //List databases in the elastic pool
            DatabaseListResponse dbListInPool = sqlClient.ElasticPools.ListDatabases(resourceGroupName, serverName, poolName);
            return dbListInPool;
        }
     

        static ServerGetResponse CreateServer()
        {
            // Create a SQL Database management client
            SqlManagementClient sqlClient = new SqlManagementClient(new TokenCloudCredentials(subscriptionId, token.AccessToken));

            // Create a server
            ServerCreateOrUpdateParameters serverParameters = new ServerCreateOrUpdateParameters()
            {
                Location = datacenterLocation,
                Properties = new ServerCreateOrUpdateProperties()
                {
                    AdministratorLogin = adminLogin,
                    AdministratorLoginPassword = adminPassword,
                    Version = serverVersion
                }
            };
            var serverResult = sqlClient.Servers.CreateOrUpdate(resourceGroupName, serverName, serverParameters);
            return serverResult;
        }

        static DatabaseCreateOrUpdateResponse CreateStandAloneDatabase()
        {
            databaseName = "standaloneDB";
            // Create a SQL Database management client
            SqlManagementClient sqlClient = new SqlManagementClient(new TokenCloudCredentials(subscriptionId, token.AccessToken));

            // Create a database
            DatabaseCreateOrUpdateParameters databaseParameters = new DatabaseCreateOrUpdateParameters()
            {
                Location = datacenterLocation,
                Properties = new DatabaseCreateOrUpdateProperties()
                {
                    Edition = "Standard",
                    RequestedServiceObjectiveName = "S0",
                    CreateMode = "Default"
                }
            };

            var dbResult = sqlClient.Databases.CreateOrUpdate(resourceGroupName,serverName, databaseName, databaseParameters);
            return dbResult;
        }

        static ResourceGroup CreateResourceGroup()
        {
            Microsoft.Rest.TokenCredentials creds = new Microsoft.Rest.TokenCredentials(token.AccessToken);
            // Create a resource management client 
            ResourceManagementClient resourceClient = new ResourceManagementClient(creds);

            // Resource group parameters
            ResourceGroup resourceGroupParameters = new ResourceGroup()
            {
                Location = datacenterLocation,
            };

            //Create a resource group
            resourceClient.SubscriptionId = subscriptionId;
            var resourceGroupResult = resourceClient.ResourceGroups.CreateOrUpdate(resourceGroupName, resourceGroupParameters);
            return resourceGroupResult;
        }

        private static AuthenticationResult GetAccessToken()
        {
            AuthenticationContext authContext = new AuthenticationContext
                ("https://login.windows.net/" + domainName /* Tenant ID or AAD domain */);

            AuthenticationResult token = authContext.AcquireToken
                ("https://management.azure.com/"/* the Azure Resource Management endpoint */,
                    clientId,
            new Uri(redirectUri) /* redirect URI */,
            PromptBehavior.Auto /* with Auto user will not be prompted if an unexpired token is cached */);

            return token;
        }
    }
    }

## 추가 리소스

- [SQL 데이터베이스](https://azure.microsoft.com/documentation/services/sql-database/)
- [Azure 리소스 관리 API](https://msdn.microsoft.com/library/azure/dn948464.aspx)
- [C#을 사용한 새 탄력적 데이터베이스 풀 만들기](sql-database-elastic-pool-create-csharp.md)
- [탄력적 데이터베이스 풀을 사용해야 하는 경우](sql-database-elastic-pool-guidance.md)
- [Azure SQL 데이터베이스를 사용하여 규모 확장](sql-database-elastic-scale-introduction.md) 참조: 탄력적 데이터베이스 도구를 사용하여 규모를 확장하거나 데이터를 이동하거나 쿼리 또는 트랜잭션을 만듭니다.

<!---HONumber=AcomDC_0706_2016-->