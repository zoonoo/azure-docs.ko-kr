<properties
    pageTitle="C#으로 탄력적 데이터베이스 풀 만들기 | Microsoft Azure"
    description="C# 데이터베이스 개발 기술을 사용하여 Azure SQL 데이터베이스에 확장성 있는 탄력적 데이터베이스 풀을 만들면 여러 데이터베이스에서 리소스를 공유할 수 있습니다."
    services="sql-database"
    documentationCenter=""
    authors="stevestein"
    manager="jhubbard"
    editor=""/>

<tags
    ms.service="sql-database"
    ms.devlang="NA"
    ms.topic="get-started-article"
    ms.tgt_pltfrm="csharp"
    ms.workload="data-management"
    ms.date="08/18/2016"
    ms.author="sstein"/>

# C&#x23;을 사용한 새 탄력적 데이터베이스 풀 만들기

> [AZURE.SELECTOR]
- [Azure 포털](sql-database-elastic-pool-create-portal.md)
- [PowerShell](sql-database-elastic-pool-create-powershell.md)
- [C#](sql-database-elastic-pool-create-csharp.md)


C#을 사용하여 [탄력적 데이터베이스 풀](sql-database-elastic-pool.md)을 만드는 방법을 알아봅니다.

일반적인 오류 코드는 [SQL 데이터베이스 클라이언트 응용 프로그램의 SQL 오류 코드: 데이터베이스 연결 오류 및 기타 문제](sql-database-develop-error-messages.md)를 참조하세요.

예제는 [.NET용 SQL 데이터베이스 라이브러리](https://msdn.microsoft.com/library/azure/mt349017.aspx)를 사용하므로 아직 설치되지 않은 경우 계속하기 전에 이 라이브러리를 설치합니다. Visual Studio의 [패키지 관리자 콘솔](http://docs.nuget.org/Consume/Package-Manager-Console)(**도구** > **NuGet 패키지 관리자** > **패키지 관리자 콘솔**)에서 다음 명령을 실행하여 이 라이브러리를 설치할 수 있습니다.

    Install-Package Microsoft.Azure.Management.Sql –Pre

## 풀 만들기

[Azure Active Directory](sql-database-client-id-keys.md)의 값을 사용하여 [SqlManagementClient](https://msdn.microsoft.com/library/microsoft.azure.management.sql.sqlmanagementclient) 인스턴스를 만듭니다. [ElasticPoolCreateOrUpdateParameters](https://msdn.microsoft.com/library/microsoft.azure.management.sql.models.elasticpoolcreateorupdateparameters) 인스턴스를 만들고 [CreateOrUpdate](https://msdn.microsoft.com/library/microsoft.azure.management.sql.databaseoperationsextensions.createorupdate) 메서드를 호출합니다. 풀, 최소 및 최대 DTU당 eDTU 값은 서비스 계층 값(기본, 표준 또는 프리미엄)에 의해 제한됩니다. [탄력적 풀 및 탄력적 데이터베이스에 대한 eDTU 및 저장소 제한](sql-database-elastic-pool.md#eDTU-and-storage-limits-for-elastic-pools-and-elastic-databases)을 참조하세요.


    ElasticPoolCreateOrUpdateParameters newPoolParameters = new ElasticPoolCreateOrUpdateParameters()
    {
        Location = "South Central US",
        Properties = new ElasticPoolCreateOrUpdateProperties()
        {
            Edition = "Standard",
            Dtu = 400,
            DatabaseDtuMin = 0,
            DatabaseDtuMax = 100
         }
    };

    // Create the pool
    var newPoolResponse = sqlClient.ElasticPools.CreateOrUpdate("resourcegroup-name", "server-name", "ElasticPool1", newPoolParameters);

## 풀에 데이터베이스 만들기

[DataBaseCreateorUpdateProperties](https://msdn.microsoft.com/library/microsoft.azure.management.sql.models.databasecreateorupdateproperties) 인스턴스를 만들고 새 데이터베이스의 속성을 설정합니다. 그런 다음 리소스 그룹, 서버 이름 및 새 데이터베이스 이름을 사용하여 CreateOrUpdate 메서드를 호출합니다.

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

기존 데이터베이스를 풀으로 이동하려면 [데이터베이스를 탄력적 풀로 이동](sql-database-elastic-pool-manage-csharp.md#Move-a-database-into-an-elastic-pool)을 참조하세요.

## 예제: C&#x23;을 사용하여 풀 만들기

이 예제에서는 Azure 리소스 그룹, Azure SQL Server 및 탄력적 풀을 만듭니다.
 

이 예제를 실행하려면 다음 라이브러리가 필요합니다. Visual Studio의 [패키지 관리자 콘솔](http://docs.nuget.org/Consume/Package-Manager-Console)(**도구** > **NuGet 패키지 관리자** > **패키지 관리자 콘솔**)에서 다음 명령을 실행하여 설치할 수 있습니다.

    Install-Package Microsoft.Azure.Management.Sql –Pre
    Install-Package Microsoft.Azure.Management.ResourceManager –Pre
    Install-Package Microsoft.Azure.Common.Authentication –Pre

콘솔 앱을 만들고 Program.cs의 내용을 다음으로 바꿉니다. 필요한 클라이언트 ID 및 관련 값을 가져오려면 [앱 등록 및 SQL 데이터베이스에 앱을 연결하는 데 필요한 클라이언트 값 가져오기](sql-database-client-id-keys.md) 문서를 사용하여 네이티브 앱을 만듭니다.

    using Microsoft.Azure;
    using Microsoft.Azure.Management.ResourceManager;
    using Microsoft.Azure.Management.ResourceManager.Models;
    using Microsoft.Azure.Management.Sql;
    using Microsoft.Azure.Management.Sql.Models;
    using Microsoft.IdentityModel.Clients.ActiveDirectory;
    using System;
    
    namespace SqlDbElasticPoolsSample
    {
    class Program
    {
        // authentication variables
        static string subscriptionId = "<your Azure subscription>";
        static string clientId = "<your client id>";
        static string redirectUri = "<your redirect URI>";
        static string domainName = "<i.e. microsoft.onmicrosoft.com>";
        static AuthenticationResult token;

        // resource group variables
        static string datacenterLocation = "<location>";
        static string resourceGroupName = "<resource group name>";

        // server variables
        static string serverName = "<server name>";
        static string adminLogin = "<server admin>";
        static string adminPassword = "<server password (store it securely!)>";
        static string serverVersion = "12.0";

        // pool variables
        static string elasticPoolName = "<pool name>";
        static string poolEdition = "Standard";
        static int poolDtus = 400;
        static int databaseMinDtus = 0;
        static int databaseMaxDtus = 100;


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
            Console.WriteLine("Creating elastic database pool... ");
            ElasticPoolCreateOrUpdateResponse epool = CreateElasticDatabasePool();
            Console.WriteLine("Creation of pool " + epool.ElasticPool.Name + ": " + epool.Status.ToString());


            // keep the console open until Enter is pressed!
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
            var newPoolResponse = sqlClient.ElasticPools.CreateOrUpdate(resourceGroupName, serverName, elasticPoolName, newPoolParameters);
            return newPoolResponse;
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

  

## 다음 단계

- [풀 관리](sql-database-elastic-pool-manage-csharp.md)
- [탄력적 작업 만들기](sql-database-elastic-jobs-overview.md) 탄력적 작업은 풀의 데이터베이스 개수에 관계없이 T-SQL 스크립트를 실행할 수 있습니다.
- [Azure SQL 데이터베이스를 사용하여 규모 확장](sql-database-elastic-scale-introduction.md): 탄력적 데이터베이스 도구를 사용하여 확장합니다.

## 추가 리소스

- [SQL 데이터베이스](https://azure.microsoft.com/documentation/services/sql-database/)
- [Azure 리소스 관리 API](https://msdn.microsoft.com/library/azure/dn948464.aspx)

<!---HONumber=AcomDC_0824_2016-->