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
    ms.date="09/14/2016"
    ms.author="sstein"/>

# C&#x23;을 사용한 탄력적 데이터베이스 풀 만들기

> [AZURE.SELECTOR]
- [Azure 포털](sql-database-elastic-pool-create-portal.md)
- [PowerShell](sql-database-elastic-pool-create-powershell.md)
- [C#](sql-database-elastic-pool-create-csharp.md)


이 문서에서는 C#을 사용하여 [.NET용 Azure SQL Database 라이브러리](https://www.nuget.org/packages/Microsoft.Azure.Management.Sql)로 Azure SQL Elastic Database 풀을 만드는 방법을 설명합니다. 독립 실행형 SQL Database를 만들려면 [C#을 사용하여 .NET용 SQL Database 라이브러리로 SQL Database 만들기](sql-database-get-started-csharp.md)를 참조하세요.

.NET용 Azure SQL 데이터베이스 라이브러리는 [리소스 관리자 기반 SQL 데이터베이스 REST API](https://msdn.microsoft.com/library/azure/mt163571.aspx)를 래핑하는 [Azure 리소스 관리자](../resource-group-overview.md) 기반 API를 제공합니다.


> [AZURE.NOTE] .NET용 Azure SQL 데이터베이스 라이브러리는 현재 미리 보기 상태입니다.


이 문서의 단계를 완료하려면 다음이 필요합니다.

- Azure 구독. Azure 구독이 필요할 경우 이 페이지 위쪽에서 **무료 계정**을 클릭하고 되돌아와 이 문서를 완료합니다.
- 있습니다. Visual Studio의 무료 버전은 [Visual Studio 다운로드](https://www.visualstudio.com/downloads/download-visual-studio-vs) 페이지를 참조하세요.


## 콘솔 앱 만들기 및 필요한 라이브러리 설치

1. Visual Studio를 시작합니다.
2. **파일** > **새로 만들기** > **프로젝트**를 클릭합니다.
3. C# **콘솔 응용 프로그램**을 만들고 이름을 *SqlElasticPoolConsoleApp*으로 지정합니다.


C#으로 SQL Database를 만들려면 [패키지 관리자 콘솔](http://docs.nuget.org/Consume/Package-Manager-Console)을 사용하여 필요한 관리 라이브러리를 로드합니다.

1. **도구** > **NuGet 패키지 관리자** > **패키지 관리자 콘솔**을 클릭합니다.
2. `Install-Package Microsoft.Azure.Management.Sql –Pre`을 입력하여 [Microsoft Azure SQL 관리 라이브러리](https://www.nuget.org/packages/Microsoft.Azure.Management.Sql)를 설치합니다.
3. `Install-Package Microsoft.Azure.Management.ResourceManager –Pre`을 입력하여 [Microsoft Azure Resource Manager 라이브러리](https://www.nuget.org/packages/Microsoft.Azure.Management.ResourceManager)를 설치합니다.
4. `Install-Package Microsoft.Azure.Common.Authentication –Pre`을 입력하여 [Microsoft Azure 공용 인증 라이브러리](https://www.nuget.org/packages/Microsoft.Azure.Common.Authentication)를 설치합니다.



> [AZURE.NOTE] 이 문서의 예제는 각 API요청의 동기 양식을 사용하며 REST가 완료되어 기본 서비스를 호출할 때까지 차단합니다. 비동기 메서드를 사용할 수 있습니다.


## SQL Elastic Database 풀 만들기 - C# 예제

다음 샘플은 리소스 그룹, 서버, 방화벽 규칙, 탄력적 풀 및 풀의 SQL Database를 만듭니다. `_subscriptionId, _tenantId, _applicationId, and _applicationSecret` 변수를 가져오려면 [리소스에 액세스하는 서비스 주체 만들기](#create-a-service-principal-to-access-resources)를 참조하세요.

**Program.cs** 콘텐츠를 다음으로 바꾸고 `{variables}`를 앱 값(`{}`을 포함하지 않음)으로 업데이트합니다.


```
using Microsoft.Azure;
using Microsoft.Azure.Management.ResourceManager;
using Microsoft.Azure.Management.ResourceManager.Models;
using Microsoft.Azure.Management.Sql;
using Microsoft.Azure.Management.Sql.Models;
using Microsoft.IdentityModel.Clients.ActiveDirectory;
using System;

namespace SqlElasticPoolConsoleApp
{
    class Program
        {

        // For details about these four (4) values, see
        // https://azure.microsoft.com/documentation/articles/resource-group-authenticate-service-principal/
        static string _subscriptionId = "{xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx}";
        static string _tenantId = "{xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx}";
        static string _applicationId = "{xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx}";
        static string _applicationSecret = "{your-password}";

        // Create management clients for the Azure resources your app needs to work with.
        // This app works with Resource Groups, and Azure SQL Database.
        static ResourceManagementClient _resourceMgmtClient;
        static SqlManagementClient _sqlMgmtClient;

        // Authentication token
        static AuthenticationResult _token;

        // Azure resource variables
        static string _resourceGroupName = "{resource-group-name}";
        static string _resourceGrouplocation = "{Azure-region}";

        static string _serverlocation = _resourceGrouplocation;
        static string _serverName = "{server-name}";
        static string _serverAdmin = "{server-admin-login}";
        static string _serverAdminPassword = "{server-admin-password}";

        static string _firewallRuleName = "{firewall-rule-name}";
        static string _startIpAddress = "{0.0.0.0}";
        static string _endIpAddress = "{255.255.255.255}";

        static string _poolName = "{pool-name}";
        static string _poolEdition = "{Standard}";
        static int _poolDtus = {100};
        static int _databaseMinDtus = {10};
        static int _databaseMaxDtus = {100};

        static string _databaseName = "{elasticdbfromcsarticle}";



        static void Main(string[] args)
        {
            // Authenticate:
            _token = GetToken(_tenantId, _applicationId, _applicationSecret);
            Console.WriteLine("Token acquired. Expires on:" + _token.ExpiresOn);

            // Instantiate management clients:
            _resourceMgmtClient = new ResourceManagementClient(new Microsoft.Rest.TokenCredentials(_token.AccessToken));
            _sqlMgmtClient = new SqlManagementClient(new TokenCloudCredentials(_subscriptionId, _token.AccessToken));


            Console.WriteLine("Resource group...");
            ResourceGroup rg = CreateOrUpdateResourceGroup(_resourceMgmtClient, _subscriptionId, _resourceGroupName, _resourceGrouplocation);
            Console.WriteLine("Resource group: " + rg.Id);


            Console.WriteLine("Server...");
            ServerGetResponse sgr = CreateOrUpdateServer(_sqlMgmtClient, _resourceGroupName, _serverlocation, _serverName, _serverAdmin, _serverAdminPassword);
            Console.WriteLine("Server: " + sgr.Server.Id);

            Console.WriteLine("Server firewall...");
            FirewallRuleGetResponse fwr = CreateOrUpdateFirewallRule(_sqlMgmtClient, _resourceGroupName, _serverName, _firewallRuleName, _startIpAddress, _endIpAddress);
            Console.WriteLine("Server firewall: " + fwr.FirewallRule.Id);

            Console.WriteLine("Elastic pool...");
            ElasticPoolCreateOrUpdateResponse epr = CreateOrUpdateElasticDatabasePool(_sqlMgmtClient, _resourceGroupName, _serverName, _poolName, _poolEdition, _poolDtus, _databaseMinDtus, _databaseMaxDtus);
            Console.WriteLine("Elastic pool: " + epr.ElasticPool.Id);

            Console.WriteLine("Database...");
            DatabaseCreateOrUpdateResponse dbr = CreateOrUpdateDatabase(_sqlMgmtClient, _resourceGroupName, _serverName, _databaseName, _poolName);
            Console.WriteLine("Database: " + dbr.Database.Id);


            Console.WriteLine("Press any key to continue...");
            Console.ReadKey();
        }

        static ResourceGroup CreateOrUpdateResourceGroup(ResourceManagementClient resourceMgmtClient, string subscriptionId, string resourceGroupName, string resourceGroupLocation)
        {
            ResourceGroup resourceGroupParameters = new ResourceGroup()
            {
                Location = resourceGroupLocation,
            };
            resourceMgmtClient.SubscriptionId = subscriptionId;
            ResourceGroup resourceGroupResult = resourceMgmtClient.ResourceGroups.CreateOrUpdate(resourceGroupName, resourceGroupParameters);
            return resourceGroupResult;
        }

        static ServerGetResponse CreateOrUpdateServer(SqlManagementClient sqlMgmtClient, string resourceGroupName, string serverLocation, string serverName, string serverAdmin, string serverAdminPassword)
        {
            ServerCreateOrUpdateParameters serverParameters = new ServerCreateOrUpdateParameters()
            {
                Location = serverLocation,
                Properties = new ServerCreateOrUpdateProperties()
                {
                    AdministratorLogin = serverAdmin,
                    AdministratorLoginPassword = serverAdminPassword,
                    Version = "12.0"
                }
            };
            ServerGetResponse serverResult = sqlMgmtClient.Servers.CreateOrUpdate(resourceGroupName, serverName, serverParameters);
            return serverResult;
        }


        static FirewallRuleGetResponse CreateOrUpdateFirewallRule(SqlManagementClient sqlMgmtClient, string resourceGroupName, string serverName, string firewallRuleName, string startIpAddress, string endIpAddress)
        {
            FirewallRuleCreateOrUpdateParameters firewallParameters = new FirewallRuleCreateOrUpdateParameters()
            {
                Properties = new FirewallRuleCreateOrUpdateProperties()
                {
                    StartIpAddress = startIpAddress,
                    EndIpAddress = endIpAddress
                }
            };
            FirewallRuleGetResponse firewallResult = sqlMgmtClient.FirewallRules.CreateOrUpdate(resourceGroupName, serverName, firewallRuleName, firewallParameters);
            return firewallResult;
        }



        static ElasticPoolCreateOrUpdateResponse CreateOrUpdateElasticDatabasePool(SqlManagementClient sqlMgmtClient, string resourceGroupName, string serverName, string poolName, string poolEdition, int poolDtus, int databaseMinDtus, int databaseMaxDtus)
        {
            // Retrieve the server that will host this elastic pool
            Server currentServer = sqlMgmtClient.Servers.Get(resourceGroupName, serverName).Server;

            // Create elastic pool: configure create or update parameters and properties explicitly
            ElasticPoolCreateOrUpdateParameters newPoolParameters = new ElasticPoolCreateOrUpdateParameters()
            {
                Location = currentServer.Location,
                Properties = new ElasticPoolCreateOrUpdateProperties()
                {
                    Edition = poolEdition,
                    Dtu = poolDtus,
                    DatabaseDtuMin = databaseMinDtus,
                    DatabaseDtuMax = databaseMaxDtus
                }
            };

            // Create the pool
            var newPoolResponse = sqlMgmtClient.ElasticPools.CreateOrUpdate(resourceGroupName, serverName, poolName, newPoolParameters);
            return newPoolResponse;
        }




        static DatabaseCreateOrUpdateResponse CreateOrUpdateDatabase(SqlManagementClient sqlMgmtClient, string resourceGroupName, string serverName, string databaseName, string poolName)
        {
            // Retrieve the server that will host this database
            Server currentServer = sqlMgmtClient.Servers.Get(resourceGroupName, serverName).Server;

            // Create a database: configure create or update parameters and properties explicitly
            DatabaseCreateOrUpdateParameters newDatabaseParameters = new DatabaseCreateOrUpdateParameters()
            {
                Location = currentServer.Location,
                Properties = new DatabaseCreateOrUpdateProperties()
                {
                    CreateMode = DatabaseCreateMode.Default,
                    ElasticPoolName = poolName
                }
            };
            DatabaseCreateOrUpdateResponse dbResponse = sqlMgmtClient.Databases.CreateOrUpdate(resourceGroupName, serverName, databaseName, newDatabaseParameters);
            return dbResponse;
        }



        private static AuthenticationResult GetToken(string tenantId, string applicationId, string applicationSecret)
        {
            AuthenticationContext authContext = new AuthenticationContext("https://login.windows.net/" + tenantId);
            _token = authContext.AcquireToken("https://management.core.windows.net/", new ClientCredential(applicationId, applicationSecret));
            return _token;
        }
    }
}
```





## 리소스에 액세스하는 서비스 주체 만들기

다음 PowerShell 스크립트는 AD(Active Directory) 응용 프로그램 및 C# 앱을 인증해야 하는 서비스 주체를 만듭니다. 스크립트는 이전 C# 샘플에 필요한 값을 출력합니다. 자세한 내용은 [Azure PowerShell을 사용하여 리소스에 액세스하는 서비스 주체 만들기](../resource-group-authenticate-service-principal.md)를 참조하세요.

   
    # Sign in to Azure.
    Add-AzureRmAccount
    
    # If you have multiple subscriptions, uncomment and set to the subscription you want to work with.
    #$subscriptionId = "{xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx}"
    #Set-AzureRmContext -SubscriptionId $subscriptionId
    
    # Provide these values for your new AAD app.
    # $appName is the display name for your app, must be unique in your directory.
    # $uri does not need to be a real uri.
    # $secret is a password you create.
    
    $appName = "{app-name}"
    $uri = "http://{app-name}"
    $secret = "{app-password}"
    
    # Create a AAD app
    $azureAdApplication = New-AzureRmADApplication -DisplayName $appName -HomePage $Uri -IdentifierUris $Uri -Password $secret
    
    # Create a Service Principal for the app
    $svcprincipal = New-AzureRmADServicePrincipal -ApplicationId $azureAdApplication.ApplicationId
    
    # To avoid a PrincipalNotFound error, I pause here for 15 seconds.
    Start-Sleep -s 15
    
    # If you still get a PrincipalNotFound error, then rerun the following until successful. 
    $roleassignment = New-AzureRmRoleAssignment -RoleDefinitionName Contributor -ServicePrincipalName $azureAdApplication.ApplicationId.Guid
    
    
    # Output the values we need for our C# application to successfully authenticate
    
    Write-Output "Copy these values into the C# sample app"
    
    Write-Output "_subscriptionId:" (Get-AzureRmContext).Subscription.SubscriptionId
    Write-Output "_tenantId:" (Get-AzureRmContext).Tenant.TenantId
    Write-Output "_applicationId:" $azureAdApplication.ApplicationId.Guid
    Write-Output "_applicationSecret:" $secret


  

## 다음 단계

- [풀 관리](sql-database-elastic-pool-manage-csharp.md)
- [탄력적 작업 만들기](sql-database-elastic-jobs-overview.md) 탄력적 작업은 풀의 데이터베이스 개수에 관계없이 T-SQL 스크립트를 실행할 수 있습니다.
- [Azure SQL 데이터베이스를 사용하여 규모 확장](sql-database-elastic-scale-introduction.md): 탄력적 데이터베이스 도구를 사용하여 확장합니다.

## 추가 리소스

- [SQL 데이터베이스](https://azure.microsoft.com/documentation/services/sql-database/)
- [Azure 리소스 관리 API](https://msdn.microsoft.com/library/azure/dn948464.aspx)

<!---HONumber=AcomDC_0914_2016-->