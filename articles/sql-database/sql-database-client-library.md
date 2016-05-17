<properties 
   pageTitle="C#을 사용하여 Azure SQL 데이터베이스 만들기 및 관리" 
   description="이 문서에서는 .NET용 Azure SQL 데이터베이스 라이브러리를 사용하여 C#으로 Azure SQL 데이터베이스를 만들고 관리하는 방법을 보여 줍니다." 
   services="sql-database" 
   documentationCenter="" 
   authors="stevestein" 
   manager="jhubbard" 
   editor=""/>

<tags
   ms.service="sql-database"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="powershell"
   ms.workload="data-management" 
   ms.date="05/03/2016"
   ms.author="sstein"/>

# C&#x23;을 사용하여 SQL 데이터베이스 만들기 및 관리

> [AZURE.SELECTOR]
- [Azure 포털](sql-database-elastic-pool-create-portal.md)
- [C#](sql-database-client-library.md)
- [PowerShell](sql-database-elastic-pool-powershell.md)


## 개요

이 문서에서는 [.NET용 Azure SQL 데이터베이스 라이브러리](https://www.nuget.org/packages/Microsoft.Azure.Management.Sql)를 사용하여 C#으로 많은 Azure SQL 데이터베이스 관리 작업을 수행하는 명령을 제공합니다.

개별 코드 조각은 명확성을 위해 세분화되었으며 샘플 콘솔 응용 프로그램은 이 문서의 하단에 있는 섹션에서 모든 명령을 합칩니다.

.NET용 Azure SQL 데이터베이스 라이브러리는 [리소스 관리자 기반 SQL 데이터베이스 REST API](https://msdn.microsoft.com/library/azure/mt163571.aspx)를 래핑하는 [Azure 리소스 관리자](../resource-group-overview.md) 기반 API를 제공합니다. 이 클라이언트 라이브러리는 리소스 관리자 기반 클라이언트 라이브러리의 일반적인 패턴을 따릅니다. 리소스 관리자는 리소스 그룹을 필요로 하며 AAD([Azure Active Directory](https://msdn.microsoft.com/library/azure/mt168838.aspx))로 인증합니다.

<br>

> [AZURE.NOTE] .NET용 Azure SQL 데이터베이스 라이브러리는 현재 미리 보기 상태입니다.

<br>

Azure 구독이 없는 경우 이 페이지의 맨 위에서 **무료 평가판**을 클릭하고 이 문서로 돌아오면 됩니다. Visual Studio의 무료 버전은 [Visual Studio 다운로드](https://www.visualstudio.com/downloads/download-visual-studio-vs) 페이지를 참조하세요.

## 필요한 라이브러리 설치

[패키지 관리자 콘솔](http://docs.nuget.org/Consume/Package-Manager-Console)을 사용해 다음의 패키지를 설치하고 필요한 관리자 라이브러리를 가져옵니다.

    PM> Install-Package Microsoft.Azure.Management.Sql –Pre
    PM> Install-Package Microsoft.Azure.Management.Resources –Pre
    PM> Install-Package Microsoft.Azure.Common.Authentication –Pre


## Azure Active Directory로 인증 구성

먼저 필요한 인증을 설정하여 REST API에 액세스할 응용 프로그램을 사용해야 합니다.

[Azure 리소스 관리자 REST API](https://msdn.microsoft.com/library/azure/dn948464.aspx)는 이전의 Azure 서비스 관리 REST API에서 사용된 인증서가 아닌 Azure Active Directory를 사용하여 인증합니다.

현재 사용자에 기반을 두고 클라이언트 응용 프로그램을 인증하려면 먼저 Azure 리소스가 생성한 구독과 관련된 AAD 도메인에 응용 프로그램을 등록해야 합니다. 회사 또는 학교 계정이 아닌 Microsoft 계정으로 Azure 구독을 생성한 경우 이미 기본 AAD 도메인을 가지고 있습니다. 응용 프로그램은 [클래식 포털](https://manage.windowsazure.com/)에서 등록할 수 있습니다.

새 응용 프로그램을 만들고 올바른 active directory에 등록하려면 다음을 수행합니다.

1. 왼쪽의 메뉴를 스크롤하여 **Active Directory** 서비스를 찾아 엽니다.

    ![AAD][1]

2. 디렉터리를 선택하여 응용 프로그램을 인증하고 해당 **이름**을 클릭합니다.

    ![디렉터리][4]

3. 디렉터리 페이지에서 **응용 프로그램**을 클릭합니다.

    ![응용 프로그램][5]

4. **추가**를 클릭하여 새 응용 프로그램을 만듭니다.

    ![응용 프로그램 추가][6]

5. **내 조직에서 개발 중인 응용 프로그램 추가**를 선택합니다.

5. 앱의 **이름**을 입력하고 **네이티브 클라이언트 응용 프로그램**을 선택합니다.

    ![응용 프로그램 추가][7]

6. **리디렉션 URI**를 입력합니다. 실제 끝점일 필요는 없으며 유효한 URI이면 됩니다.

    ![응용 프로그램 추가][8]

7. 앱 만들기를 완료하고 **구성**을 클릭한 후 **클라이언트 ID**를 복사합니다(코드에 클라이언트 ID가 필요함).

    ![클라이언트 ID 가져오기][9]


1. 페이지 맨 아래에서 **응용 프로그램 추가**를 클릭합니다.
1. **Microsoft 앱**을 선택합니다.
1. **Azure 서비스 관리 API**를 선택하고 마법사를 완료합니다.
2. API를 선택한 후 **Azure 서비스 관리(미리 보기) 액세스**를 선택하여 이 API에 액세스하는 데 필요한 특정 권한을 부여해야 합니다.

    ![권한][2]

2. **저장**을 클릭합니다.



### 도메인 이름을 식별합니다.

코드에 대한 도메인 이름이 필요합니다. 적절한 도메인 이름을 식별하는 간단한 방법은 다음과 같습니다.

1. [Azure 포털](https://portal.azure.com)로 이동합니다.
2. 오른쪽 위에 있는 이름 위로 마우스를 이동하고 팝업 창에 나타나는 도메인을 참고합니다.

    ![도메인 이름 식별][3]





**추가 AAD 리소스**

인증을 위한 Azure Active Directory 사용에 대한 추가 정보는 [이 유용한 블로그 게시물](http://www.cloudidentity.com/blog/2013/09/12/active-directory-authentication-library-adal-v1-for-net-general-availability/)에서 찾을 수 있습니다.


### 현재 사용자에 대한 액세스 토큰 검색 

클라이언트 응용 프로그램은 현재 사용자에 대한 응용 프로그램 액세스 토큰을 검색해야 합니다. 코드가 사용자에 의해 처음으로 실행될 때 사용자 자격 증명을 입력하라는 메시지가 표시되며 결과적으로 토큰이 로컬에서 캐시됩니다. 후속 실행 때에는 캐시에서 토큰을 검색하며 토큰이 만료되었을 때 사용자에게 로그인하라는 메시지만 표시합니다.


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

사용자의 조작이 필요없는 자동화 스크립트를 만들려면 사용자 대신 서비스 주체에 기반하여 인증할 수 있습니다. 이 접근 방식은 자격 증명 개체의 생성 및 제출을 필요로 합니다.




> [AZURE.NOTE] 이 문서의 예제는 각 API요청의 동기 양식을 사용하며 REST가 완료되어 기본 서비스를 호출할 때까지 차단합니다. 비동기 메서드를 사용할 수 있습니다.



## 리소스 그룹 만들기

리소스 관리자를 사용하여 모든 리소스를 리소스 그룹에 생성해야 합니다. 리소스 그룹은 응용 프로그램에 관련된 리소스를 보유하는 컨테이너입니다. 데이터베이스 서버는 Azure SQL 데이터베이스로 기존의 리소스 그룹에 먼저 만들어야 하며 그 후에 서버에 생성된 데이터베이스에 만듭니다. 그런 다음 응용 프로그램이 T-SQL을 제출하기 위해 TDS를 사용하여 서버 또는 데이터베이스에 연결하기 전에 클라이언트 IP 주소에서 액세스를 열기 위한 방화벽 규칙 또한 서버에 만들어야 합니다.


    // Create a resource management client 
    ResourceManagementClient resourceClient = new ResourceManagementClient(new TokenCloudCredentials("XXXXXXXX-XXXX-XXXX-XXXX-XXXXXXXXXXXX" /*subscription id*/, token.AccessToken ));
    
    // Resource group parameters
    ResourceGroup resourceGroupParameters = new ResourceGroup()
    {
        Location = "South Central US"
    };
    
    //Create a resource group
    var resourceGroupResult = resourceClient.ResourceGroups.CreateOrUpdate("resourcegroup-name", resourceGroupParameters);



## 서버 만들기 

SQL 데이터베이스는 서버에 포함되어 있습니다. 서버 이름은 모든 Azure SQL 서버에서 전역적으로 고유해야 하며 서버 이름이 이미 사용 중인 경우 오류가 발생합니다. 또한 이 명령을 완료하는 데 몇 분 정도 걸릴 수 있다는 점도 유의해야 합니다.


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




## 서버에 대한 액세스를 허용할 수 있도록 서버 방화벽 규칙 생성

기본적으로 모든 위치에서 서버로 연결할 수는 없습니다. TDS를 사용해 서버에 연결하고 T-SQL을 서버 또는 서버의 데이터베이스에 제출하려면 클라이언트 IP 주소에서 액세스를 허용하는 [방화벽 규칙](https://msdn.microsoft.com/library/azure/ee621782.aspx)이 정의되어야 합니다.

다음의 예제는 모든 IP 주소에서 서버에 대한 액세스를 여는 규칙을 만듭니다. 데이터베이스의 보안을 위해 적절한 SQL 로그인 및 암호를 만들고 지침에 대한 기본 보호로서 방화벽 규칙을 사용하지 않을 것을 권장합니다.


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




다른 Azure 서비스의 서버 액세스를 허용하려면 방화벽 규칙을 추가하고 StartIpAddress와 EndIpAddress를 모두 0.0.0.0으로 설정합니다. 이렇게 하면 *모든* Azure 구독에서 Azure 트래픽이 서버에 액세스할 수 있습니다.


## 데이터베이스 만들기

다음의 명령은 서버에 같은 이름의 데이터베이스가 존재하지 않을 경우 새 기본 데이터베이스를 만듭니다. 같은 이름의 데이터베이스가 존재하면 업데이트됩니다.

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



## 데이터베이스 업데이트 

데이터베이스를 업데이트하려면(예: 서비스 계층 및 성능 수준 변경) 위의 데이터베이스 생성 또는 업데이트와 같이 **Databases.CreateOrUpdate** 메서드를 호출합니다. **Edition** 및 **RequestedServiceObjectiveName** 속성을 원하는 서비스 계층 및 성능 수준으로 설정합니다. Edition을 **Premium**으로 변경하거나 Premium에서 변경하는 경우 데이터베이스 크기에 따라 업데이트하는 데 시간이 걸릴 수 있습니다.

다음은 SQL 데이터베이스를 표준 (S0) 수준으로 업데이트합니다.

    // Retrieve current database properties 
    var currentDatabase = sqlClient.Databases.Get("resourecegroup-name", "server-name", "Database1").Database;

    // Configure create or update parameters with existing property values, override those to be changed.
    DatabaseCreateOrUpdateParameters updateDatabaseParameters = new DatabaseCreateOrUpdateParameters()
    {
        Location = currentDatabase.Location,
        Properties = new DatabaseCreateOrUpdateProperties()
        {
            Edition = "Standard",
            RequestedServiceObjectiveName = "S0", // alternatively set the RequestedServiceObjectiveId
            MaxSizeBytes = currentDatabase.Properties.MaxSizeBytes,
            Collation = currentDatabase.Properties.Collation
        }
     };

    // Update the database
    dbResponse = sqlClient.Databases.CreateOrUpdate("resourcegroup-name", "server-name", "Database1", updateDatabaseParameters);


## 서버에 있는 모든 데이터베이스 나열하기

서버에 있는 모든 데이터베이스를 나열하려면 서버 및 리소스 그룹 이름을 Database.List 메서드로 전달합니다.

    // List databases on the server
    DatabaseListResponse dbListOnServer = sqlClient.Databases.List("resourcegroup-name", "server-name");
    Console.WriteLine("Databases on Server {0}", "server-name");
    foreach (Database db in dbListOnServer)
    {
        Console.WriteLine("  Database {0}, Service Objective {1}", db.Name, db.Properties.ServiceObjective);
    }



## 탄력적 데이터베이스 풀 생성

서버에 새로운 풀을 만듭니다.



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


## 탄력적 데이터베이스 풀 업데이트

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



## 기존 데이터베이스를 탄력적 데이터베이스 풀 내로 이동

*풀을 만든 후에 기존 데이터베이스를 풀 내부 및 외부로 이동하는 데 Transact-SQL을 사용할 수 있습니다. 자세한 내용은 [Transact-SQL로 탄력적 데이터베이스 풀 모니터링 및 관리](sql-database-elastic-pool-manage-tsql.md)를 참조하세요.*

탄력적 데이터베이스를 풀 내로 이동합니다.

    
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
    
    


## 탄력적 데이터베이스 풀 내에 새 데이터베이스 생성

*풀을 만든 후에 풀에서 탄력적인 새 데이터베이스를 만드는 데 Transact-SQL을 사용할 수 있습니다. 자세한 내용은 [Transact-SQL로 탄력적 데이터베이스 풀 모니터링 및 관리](sql-database-elastic-pool-manage-tsql.md)를 참조하세요.*

풀 내에 직접 새 데이터베이스를 만듭니다.

    
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



## 탄력적 데이터베이스 풀 내에 있는 모든 데이터베이스 나열

풀 내에 있는 모든 데이터베이스를 나열합니다.

    //List databases in the elastic pool
    DatabaseListResponse dbListInPool = sqlClient.ElasticPools.ListDatabases("resourcegroup-name", "server-name", "ElasticPool1");
    Console.WriteLine("Databases in Elastic Pool {0}", "server-name.ElasticPool1");
    foreach (Database db in dbListInPool)
    {
        Console.WriteLine("  Database {0}", db.Name);
    }

## 서버 삭제

서버 (또한 서버의 모든 탄력적 데이터베이스 풀 및 데이터베이스)를 삭제하려면 다음의 코드를 실행합니다.

    var serverOperationResponse = sqlClient.Servers.Delete("resourcegroup-name", "server-name");


## 리소스 그룹 삭제

리소스 그룹을 삭제합니다.

    // Delete the resource group
    var resourceOperationResponse = resourceClient.ResourceGroups.Delete("resourcegroup-name");



## 샘플 콘솔 응용 프로그램


    using Microsoft.Azure;
    using Microsoft.Azure.Insights;
    using Microsoft.Azure.Insights.Models;
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

            Console.WriteLine("Database {0} create or update completed with status code {1}. Service Objective {2} ", dbResponse.Database.Name, dbResponse.StatusCode, dbResponse.Database.Properties.ServiceObjective);

            // ...
            // Update database: retrieve current database properties 
            var currentDatabase = sqlClient.Databases.Get("resourcegroup-name", "server-name", "Database1").Database;

            // Update database: configure create or update parameters with existing property values, override those to be changed.
            DatabaseCreateOrUpdateParameters updateDatabaseParameters = new DatabaseCreateOrUpdateParameters()
            {
                Location = currentDatabase.Location,
                Properties = new DatabaseCreateOrUpdateProperties()
                {
                    Edition = "Standard",
                    RequestedServiceObjectiveName = "S0", // alternatively set the RequestedServiceObjectiveId
                    MaxSizeBytes = currentDatabase.Properties.MaxSizeBytes,
                    Collation = currentDatabase.Properties.Collation
                }
            };

            // Update the database
            dbResponse = sqlClient.Databases.CreateOrUpdate("resourcegroup-name", "server-name", "Database1", updateDatabaseParameters);

            Console.WriteLine("Database {0} create or update completed with status code {1}. Service Objective: {2} ", dbResponse.Database.Name, dbResponse.StatusCode, dbResponse.Database.Properties.ServiceObjective);

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

            // Update database service objective to add the database to a pool

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

            // Update the database
            var dbUpdateResponse = sqlClient.Databases.CreateOrUpdate("resourcegroup-name", "server-name", "Database1", updatePooledDbParameters);

            Console.WriteLine("Database {0} create or update completed with status code {1}. Service Objective: {2}({3}) ", dbUpdateResponse.Database.Name, dbUpdateResponse.StatusCode, dbUpdateResponse.Database.Properties.ServiceObjective, dbUpdateResponse.Database.Properties.ElasticPoolName);

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

            // List databases on the server
            DatabaseListResponse dbListOnServer = sqlClient.Databases.List("resourcegroup-name", "server-name");
            Console.WriteLine("Databases on Server {0}", "server-name");
            foreach (Database db in dbListOnServer)
            {
                Console.WriteLine("  Database {0}, Service Objective {1}", db.Name, db.Properties.ServiceObjective);
            }

            // List all servers, pools and databases in the resource group
            ServerListResponse serverList = new ServerListResponse();
            ElasticPoolListResponse poolList = new ElasticPoolListResponse();
            DatabaseListResponse dbListInPool = new DatabaseListResponse();

            Console.WriteLine("Servers in resource group {0}", "resourcegroup-name");
            serverList = sqlClient.Servers.List("resourcegroup-name");
            foreach (Server server in serverList)
            {
                Console.WriteLine("  Server '{0}' location: {1}", server.Name, server.Location);
                poolList = sqlClient.ElasticPools.List("resourcegroup-name", server.Name);
                foreach (ElasticPool pool in poolList)
                {
                    Console.WriteLine("    Elastic Pool '{0}' edition: {1} eDTU: {2} storage GB: {3} database eDTU min: {4} database eDTU max: {5}", pool.Name, pool.Properties.Edition, pool.Properties.Dtu, (pool.Properties.StorageMB/1024), pool.Properties.DatabaseDtuMin, pool.Properties.DatabaseDtuMax);
                    dbListInPool = sqlClient.ElasticPools.ListDatabases("resourcegroup-name", server.Name, pool.Name);
                    foreach(Database db in dbListInPool)
                    {
                        Console.WriteLine("      Database '{0}'", db.Name);                       
                    }
                }
            }

            // Metrics

            var endTime = String.Format(DateTime.Now.ToUniversalTime().ToString("s")) + "Z"; // as UTC in sortable time format yyyy-mm-ddThh:mm:ssZ
            var duration = TimeSpan.FromHours(2);
            var startTime = String.Format(DateTime.Now.Subtract(duration).ToUniversalTime().ToString("s")) + "Z";  // as UTC in sortable time format yyyy-mm-ddThh:mm:ssZ

            Console.WriteLine("");
            Console.WriteLine("Elastic pool metrics for 'ElasticPool1'");

            ElasticPoolMetricDefinitions poolMetricDefinition = new ElasticPoolMetricDefinitions();
            poolMetricDefinition = sqlClient.ElasticPools.ListMetricDefinitions("resourcegroup-name", "server-name", "ElasticPool1");

            Console.WriteLine("  Metric definitions: ");
            foreach (Microsoft.Azure.Management.Sql.Models.MetricDefinition metricDefinition in poolMetricDefinition.MetricDefinitions)
            {
                Console.WriteLine("    '{0}' unit: {1} aggregation type: {2}", metricDefinition.Name.LocalizedValue, metricDefinition.Unit, metricDefinition.PrimaryAggregationType);
            }
            Console.WriteLine("  Metric values: ");
            ElasticPoolMetrics elasticPoolMetrics = new ElasticPoolMetrics();
            elasticPoolMetrics = sqlClient.ElasticPools.ListMetrics("resourcegroup-name", "server-name", "ElasticPool1", "name.value eq 'dtu_consumption_percent'", "PT5M", startTime, endTime);
            foreach (Microsoft.Azure.Management.Sql.Models.Metric metric in elasticPoolMetrics.Metrics)
            {
                Console.WriteLine("    '{0}' unit: {1} time grain: {2} start time: {3} end time: {4} values: {5}", metric.Name.LocalizedValue, metric.Unit, metric.TimeGrain, metric.StartTime, metric.EndTime, metric.Values.Count);
                foreach (Value metricValue in metric.Values)
                {
                    Console.WriteLine("      Timestamp: {0} average: {1} minimum: {2} maximum {3} total {4}", metricValue.Timestamp, metricValue.Average, metricValue.Maximum, metricValue.Minimum, metricValue.Total);
                }
            }

            // List database metrics
            Console.WriteLine("");
            Console.WriteLine("Database metrics for 'Database1'");
            Console.WriteLine("  Metric definitions"); 

            Microsoft.Azure.Insights.InsightsClient insightsClient = new InsightsClient(tokenCredentials);

            Microsoft.Azure.Insights.Models.MetricDefinitionListResponse metricDefinitionListResponse = insightsClient.MetricDefinitionOperations.GetMetricDefinitions("subscriptions/XXXXXXXX-XXXX-XXXX-XXXX-XXXXXXXXXXXX/resourcegroups/resourcegroup-name/providers/microsoft.sql/servers/server-name/databases/Database1/", "");
            foreach (Microsoft.Azure.Insights.Models.MetricDefinition metricDefinition in metricDefinitionListResponse.MetricDefinitionCollection.Value)
            {
                Console.WriteLine("    {0} unit: {1} aggregation: {2}" , metricDefinition.Name.LocalizedValue, metricDefinition.Unit, metricDefinition.PrimaryAggregationType);
            }
            var resourceURI = "subscriptions/XXXXXXXX-XXXX-XXXX-XXXX-XXXXXXXXXXXX/resourcegroups/resourcegroup-name/providers/microsoft.sql/servers/server-name/databases/Database1/";
            var filter = "(name.value eq 'dtu_consumption_percent') and startTime eq " + startTime + " and endTime eq " + endTime + " and timeGrain eq duration'PT5M'";

            Console.WriteLine("  Metric values");
            MetricListResponse mlr = insightsClient.MetricOperations.GetMetrics(resourceURI,filter);
            foreach (Microsoft.Azure.Insights.Models.Metric metric in mlr.MetricCollection.Value)
            {
                Console.WriteLine("    {0}", metric.Name.LocalizedValue);
                foreach (MetricValue metricValue in metric.MetricValues)
                {
                    Console.WriteLine("      Timestamp: {0} minimum: {1} maximum: {2} average: {3}", metricValue.Timestamp, metricValue.Minimum, metricValue.Maximum, metricValue.Average);
                }
            }

            Console.WriteLine("");
            Console.WriteLine("Press any key to delete the server and resource group, which will also delete the databases and the elastic pool.");
            Console.ReadKey();

            // Delete the server which deletes the databases and then the elastic pool
            var serverOperationResponse = sqlClient.Servers.Delete("resourcegroup-name", "server-name");
            Console.WriteLine("");
            Console.WriteLine("Server {0} delete completed with status code {1}.", "server-name", serverOperationResponse.StatusCode);

            // Delete the resource group
            var resourceOperationResponse = resourceClient.ResourceGroups.Delete("resourcegroup-name");
            Console.WriteLine("");
            Console.WriteLine("Resource {0} delete completed with status code {1}.", "resourcegroup-name", resourceOperationResponse.StatusCode);

            Console.WriteLine("");
            Console.WriteLine("Execution complete.  Press any key to continue.");
            Console.ReadKey();
        }
    }
    }





## 추가 리소스

[SQL 데이터베이스](https://azure.microsoft.com/documentation/services/sql-database/)

[Azure 리소스 관리 API](https://msdn.microsoft.com/library/azure/dn948464.aspx)

<!--Image references-->
[1]: ./media/sql-database-client-library/aad.png
[2]: ./media/sql-database-client-library/permissions.png
[3]: ./media/sql-database-client-library/getdomain.png
[4]: ./media/sql-database-client-library/aad2.png
[5]: ./media/sql-database-client-library/aad-applications.png
[6]: ./media/sql-database-client-library/add.png
[7]: ./media/sql-database-client-library/add-application.png
[8]: ./media/sql-database-client-library/add-application2.png
[9]: ./media/sql-database-client-library/clientid.png

<!---HONumber=AcomDC_0504_2016-->