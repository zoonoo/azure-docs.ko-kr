<properties 
   pageTitle="C#을 사용하여 Azure SQL 데이터베이스 만들기" 
   description="이 문서에서는 .NET용 Azure SQL 데이터베이스 라이브러리를 사용하여 C#으로 Azure SQL 데이터베이스를 만드는 방법을 보여 줍니다." 
   services="sql-database" 
   documentationCenter="" 
   authors="stevestein" 
   manager="jeffreyg" 
   editor=""/>

<tags
   ms.service="sql-database"
   ms.devlang="NA"
   ms.topic="get-started-article"
   ms.tgt_pltfrm="powershell"
   ms.workload="data-management" 
   ms.date="09/01/2015"
   ms.author="sstein"/>

# C&#x23;으로 SQL 데이터베이스 만들기

**단일 데이터베이스**

> [AZURE.SELECTOR]
- [Azure Preview Portal](sql-database-get-started.md)
- [C#](sql-database-get-started-csharp.md)
- [PowerShell](sql-database-get-started-powershell.md)



이 문서는 [.NET용 Azure SQL 데이터베이스 라이브러리](https://www.nuget.org/packages/Microsoft.Azure.Management.Sql)를 사용하여 C#으로 Azure SQL 데이터베이스를 만드는 명령을 제공합니다.

이 문서에서는 단일 데이터베이스를 만드는 방법을 보여 주며 탄력적 데이터베이스 생성에 대한 내용은 [탄력적 데이터베이스 풀 생성](sql-database-elastic-pool-portal.md)을 참조하세요.

개별 코드 조각은 명확성을 위해 세분화되었으며 샘플 콘솔 응용 프로그램은 이 문서의 하단에 있는 섹션에서 모든 명령을 합칩니다.

.NET용 Azure SQL 데이터베이스 라이브러리는 [리소스 관리자 기반 SQL 데이터베이스 REST API](https://msdn.microsoft.com/library/azure/mt163571.aspx)를 래핑하는 [Azure 리소스 관리자](resource-group-overview.md) 기반 API를 제공합니다. 이 클라이언트 라이브러리는 리소스 관리자 기반 클라이언트 라이브러리의 일반적인 패턴을 따릅니다. 리소스 관리자는 리소스 그룹을 필요로 하며 AAD([Azure Active Directory](https://msdn.microsoft.com/library/azure/mt168838.aspx))로 인증합니다.

<br>

> [AZURE.NOTE].NET용 Azure SQL 데이터베이스 라이브러리는 현재 미리 보기 상태입니다.

<br>

이 문서의 단계를 완료하려면 다음이 필요합니다.

- Azure 구독. Azure 구독이 필요할 경우 이 페이지 위쪽에서 **무료 평가판**을 클릭하고 되돌아와 이 문서를 완료합니다.
- 있습니다. Visual Studio의 무료 버전은 [Visual Studio 다운로드](https://www.visualstudio.com/downloads/download-visual-studio-vs) 페이지를 참조하세요.


## 필요한 라이브러리 설치

[패키지 관리자 콘솔](http://docs.nuget.org/Consume/Package-Manager-Console)을 사용해 다음의 패키지를 설치하고 필요한 관리자 라이브러리를 가져옵니다.

    PM> Install-Package Microsoft.Azure.Management.Sql –Pre
    PM> Install-Package Microsoft.Azure.Management.Resources –Pre
    PM> Install-Package Microsoft.Azure.Common.Authentication –Pre


## Azure Active Directory로 인증 구성

먼저 필요한 인증을 설정하여 REST API에 액세스할 클라이언트 응용 프로그램을 사용해야 합니다.

[Azure 리소스 관리자 REST API](https://msdn.microsoft.com/library/azure/dn948464.aspx)는 인증을 위해 Azure Active Directory를 사용합니다.

현재 사용자에 기반을 두고 클라이언트 응용 프로그램을 인증하려면 먼저 Azure 리소스가 생성한 구독과 관련된 AAD 도메인에 응용 프로그램을 등록해야 합니다. 회사 또는 학교 계정이 아닌 Microsoft 계정으로 Azure 구독을 생성한 경우 이미 기본 AAD 도메인을 가지고 있습니다. 응용 프로그램은 [Azure 포털](https://manage.windowsazure.com/)에서 등록할 수 있습니다.

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

1. [Azure Preview 포털](https://portal.azure.com)로 이동합니다.
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
            ("https://login.windows.net/" + domainName /* Tenant ID or AAD domain */);

        AuthenticationResult token = authContext.AcquireToken
            ("https://management.azure.com/"/* the Azure Resource Management endpoint */,
                clientId,
        new Uri(clientAppUri) /* redirect URI */,
        PromptBehavior.Auto /* with Auto user will not be prompted if an unexpired token is cached */);

        return token;
    }



> [AZURE.NOTE]이 문서의 예제는 각 API요청의 동기 양식을 사용하며 REST가 완료되어 기본 서비스를 호출할 때까지 차단합니다. 비동기 메서드를 사용할 수 있습니다.



## 리소스 그룹 만들기

리소스 관리자를 사용하여 모든 리소스를 리소스 그룹에 생성해야 합니다. 리소스 그룹은 응용 프로그램에 관련된 리소스를 보유하는 컨테이너입니다. 데이터베이스 서버는 Azure SQL 데이터베이스로 기존의 리소스 그룹에 만들어야 하며 그 후에 서버에 생성된 데이터베이스에 만듭니다. 그런 다음 응용 프로그램이 서버 또는 데이터베이스에 연결하기 전에 클라이언트 IP 주소에서 액세스를 열기 위한 방화벽 규칙 또한 서버에 만들어야 합니다.


    // Create a resource management client 
    ResourceManagementClient resourceClient = new ResourceManagementClient(new TokenCloudCredentials("XXXXXXXX-XXXX-XXXX-XXXX-XXXXXXXXXXXX" /*subscription id*/, token.AccessToken ));
    
    // Resource group parameters
    ResourceGroup resourceGroupParameters = new ResourceGroup()
    {
        Location = "South Central US"
    };
    
    //Create a resource group
    var resourceGroupResult = resourceClient.ResourceGroups.CreateOrUpdate("ResourceGroupName", resourceGroupParameters);



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
            AdministratorLoginPassword = securelyStoredPassword,
            Version = "12.0"
        }
    };

    var serverResult = sqlClient.Servers.CreateOrUpdate("ResourceGroupName", "ServerName", serverParameters);




## 서버에 대한 액세스를 허용할 수 있도록 서버 방화벽 규칙 생성

기본적으로 모든 위치에서 서버로 연결할 수는 없습니다. 서버 또는 서버의 데이터베이스에 연결하려면 클라이언트 IP 주소에서 액세스를 허용하는 [방화벽 규칙](https://msdn.microsoft.com/library/azure/ee621782.aspx)을 정의해야 합니다.

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

    var firewallResult = sqlClient.FirewallRules.CreateOrUpdate("ResourceGroupName", "ServerName", "FirewallRuleName", firewallParameters);




다른 Azure 서비스의 서버 액세스를 허용하려면 방화벽 규칙을 추가하고 StartIpAddress와 EndIpAddress를 모두 0.0.0.0으로 설정합니다. 이렇게 하면 *모든* Azure 구독에서 Azure 트래픽이 서버에 액세스할 수 있습니다.


## 데이터베이스 만들기

다음의 명령은 서버에 같은 이름의 데이터베이스가 존재하지 않을 경우 새 기본 데이터베이스를 만듭니다. 같은 이름의 데이터베이스가 존재하면 업데이트됩니다.

        // Create a database

        // Retrieve the server on which the database will be created
        Server currentServer = sqlClient.Servers.Get("ResourceGroupName", "ServerName").Server;
 
        // Create a database: configure create or update parameters and properties explicitly
        DatabaseCreateOrUpdateParameters newDatabaseParameters = new DatabaseCreateOrUpdateParameters()
        {
            Location = currentServer.Location,
            Properties = new DatabaseCreateOrUpdateProperties()
            {
                Edition = "Standard",
                RequestedServiceObjectiveName = "S0",
            }
        };

        var dbResponse = sqlClient.Databases.CreateOrUpdate("ResourceGroupNname", "ServerName", "Database1", newDatabaseParameters);






## 샘플 콘솔 응용 프로그램


    using Microsoft.Azure;
    using Microsoft.Azure.Management.Resources;
    using Microsoft.Azure.Management.Resources.Models;
    using Microsoft.Azure.Management.Sql;
    using Microsoft.Azure.Management.Sql.Models;
    using Microsoft.IdentityModel.Clients.ActiveDirectory;
    using System;
    
    
    namespace CreateSqlDatabase
    {
     class Program
     {

        #region user variables
        
        // Azure subscription id
        private static string subscriptionId = "XXXXXXXX-XXXX-XXXX-XXXX-XXXXXXXXXXXX";
     
        
        // application client ID from Azure Active Directory (AAD)
        private static string clientAppUri = "http://app1";
        private static string clientId = "XXXXXXXX-XXXX-XXXX-XXXX-XXXXXXXXXXXX";

        private static string resourcegroupName = "rg1";

        private static string dataCenterLocation = "Japan West";

        private static string databaseName = "newDatabaseName";
        private static string databaseEdition = "Standard";
        private static string databasePerformanceLevel = "S0";

        private static string serverName = "serverName";
        private static string serverAdmin = "admin";
        private static string serverAdminPassword = securelyStoredPassword;
        private static string serverVersion = "12.0";

        private static string firewallRuleName = "rule1";
        private static string firewallRuleStartIp = "0.0.0.0";
        private static string firewallRuleEndIp = "255.255.255.255";



        private static string domainName = "microsoft.onmicrosoft.com";

        private static string serverLocation = "Japan West";

        #endregion


        static void Main(string[] args)
        {
            var token = GetAccessToken();

            // Who am I?
            Console.WriteLine("Identity: {0} {1}", token.UserInfo.GivenName, token.UserInfo.FamilyName);
            Console.WriteLine("Token expires on {0}", token.ExpiresOn);
            Console.WriteLine("");

            // Create a resource management client 
            ResourceManagementClient resourceClient = new ResourceManagementClient(new TokenCloudCredentials(subscriptionId, token.AccessToken));

            // Resource group parameters
            ResourceGroup resourceGroupParameters = new ResourceGroup()
            {
                Location = dataCenterLocation
            };

            //Create a resource group
            var resourceGroupResult = resourceClient.ResourceGroups.CreateOrUpdate(resourcegroupName, resourceGroupParameters);

            Console.WriteLine("Resource group {0} create or update completed with status code {1} ", resourceGroupResult.ResourceGroup.Name, resourceGroupResult.StatusCode);

            //create a SQL Database management client
            TokenCloudCredentials tokenCredentials = new TokenCloudCredentials(subscriptionId, token.AccessToken);

            SqlManagementClient sqlClient = new SqlManagementClient(tokenCredentials);

            // Create a server
            ServerCreateOrUpdateParameters serverParameters = new ServerCreateOrUpdateParameters()
            {
                Location = serverLocation,
                Properties = new ServerCreateOrUpdateProperties()
                {
                    AdministratorLogin = serverAdmin,
                    AdministratorLoginPassword = serverAdminPassword,
                    Version = serverVersion
                }
            };

            var serverResult = sqlClient.Servers.CreateOrUpdate(resourcegroupName, serverName, serverParameters);

            var serverGetResult = sqlClient.Servers.Get(resourcegroupName, serverName);


            Console.WriteLine("Server {0} create or update completed with status code {1}", serverResult.Server.Name, serverResult.StatusCode);

            // Create a firewall rule on the server to allow TDS connection 

            FirewallRuleCreateOrUpdateParameters firewallParameters = new FirewallRuleCreateOrUpdateParameters()
            {
                Properties = new FirewallRuleCreateOrUpdateProperties()
                {
                    StartIpAddress = firewallRuleStartIp,
                    EndIpAddress = firewallRuleEndIp
                }
            };

            var firewallResult = sqlClient.FirewallRules.CreateOrUpdate(resourcegroupName, serverName, firewallRuleName, firewallParameters);

            Console.WriteLine("Firewall rule {0} create or update completed with status code: {1}", firewallResult.FirewallRule.Name, firewallResult.StatusCode);

            // Create a database

            // Retrieve the server on which the database will be created
            Server currentServer = sqlClient.Servers.Get(resourcegroupName, serverName).Server;

            // Create a database
            DatabaseCreateOrUpdateParameters newDatabaseParameters = new DatabaseCreateOrUpdateParameters()
            {
                Location = currentServer.Location,
                Properties = new DatabaseCreateOrUpdateProperties()
                {
                    Edition = databaseEdition,
                    RequestedServiceObjectiveName = databasePerformanceLevel,
                }
            };

            var dbResponse = sqlClient.Databases.CreateOrUpdate(resourcegroupName, serverName, databaseName, newDatabaseParameters);

            Console.WriteLine("Database {0} created with status code: {1}. Service Objective: {2} ", dbResponse.Database.Name, dbResponse.StatusCode, dbResponse.Database.Properties.ServiceObjective);
           
            Console.WriteLine(System.Environment.NewLine + "Press any key to exit.");
            Console.ReadKey();
        }

        /// <summary>
        /// Prompts for user credentials when first run or if the cached credentials have expired.
        /// </summary>
        /// <returns>The access token from AAD.</returns>
        private static AuthenticationResult GetAccessToken()
        {
            AuthenticationContext authContext = new AuthenticationContext
                ("https://login.windows.net/" + domainName /* Tenant ID or AAD domain */);

            AuthenticationResult token = authContext.AcquireToken
                ("https://management.azure.com/"/* the Azure Resource Management endpoint */,
                    clientId,
            new Uri(clientAppUri) /* redirect URI */,
            PromptBehavior.Auto /* with Auto user will not be prompted if an unexpired token is cached */);

            return token;
        }
      }
    }


## 다음 단계

- [C#을 사용하여 SQL 데이터베이스에 연결 및 쿼리하기](sql-database-connect-query.md)
- [SQL Server Management Studio(SSMS)로 연결](sql-database-connect-to-database.md)

## 추가 리소스

- [SQL 데이터베이스](https://azure.microsoft.com/documentation/services/sql-database/)





<!--Image references-->
[1]: ./media/sql-database-get-started-csharp/aad.png
[2]: ./media/sql-database-get-started-csharp/permissions.png
[3]: ./media/sql-database-get-started-csharp/getdomain.png
[4]: ./media/sql-database-get-started-csharp/aad2.png
[5]: ./media/sql-database-get-started-csharp/aad-applications.png
[6]: ./media/sql-database-get-started-csharp/add.png
[7]: ./media/sql-database-get-started-csharp/add-application.png
[8]: ./media/sql-database-get-started-csharp/add-application2.png
[9]: ./media/sql-database-get-started-csharp/clientid.png

<!---HONumber=Nov15_HO2-->