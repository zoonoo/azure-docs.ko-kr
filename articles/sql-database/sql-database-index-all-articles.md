<properties
    pageTitle="SQL 데이터베이스 서비스에 대한 모든 항목 | Microsoft Azure"
    description="http://azure.microsoft.com/documentation/articles/에 있는 SQL 데이터베이스라는 Azure 서비스에 대한 모든 항목, 제목 및 설명에 대한 테이블입니다."
    services="sql-database"
    documentationCenter=""
    authors="MightyPen"
    manager="jhubbard"
    editor="MightyPen"/>

<tags
    ms.service="sql-database"
    ms.workload="sql-database"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="10/05/2016"
    ms.author="genemi"/>



# <a name="all-topics-for-azure-sql-database-service"></a>Azure SQL 데이터베이스 서비스에 대한 모든 항목

이 항목에서는 Azure의 **SQL 데이터베이스** 서비스에 직접 적용되는 모든 항목을 나열합니다. 이 웹 페이지에서 **Ctrl+F**를 사용해 키워드를 검색하여 현재 관심 있는 항목을 찾을 수 있습니다.




## <a name="new"></a>새로 만들기

| &nbsp; | 제목 | 설명 |
| --: | :-- | :-- |
| 1 | [Daxko/CSI에서 Azure를 사용하여 개발 주기를 가속화하고 고객 서비스 및 성능 개선](sql-database-implementation-daxko.md) | Daxko/CSI에서 SQL 데이터베이스를 사용하여 개발 주기를 가속화하고 고객 서비스 및 성능을 개선하는 방법을 알아봅니다. |
| 2 | [Azure를 통한 GEP의 글로벌 고객 접근 및 효율성 개선](sql-database-implementation-gep.md) | GEP이 SQL 데이터베이스를 사용하여 더 많은 글로벌 고객에게 도달하고 보다 높은 효율성을 달성하는 방법을 알아봅니다. |
| 3 | [Azure를 사용하여 SnelStart는 매월 1,000개의 새 Azure SQL 데이터베이스 규모로 비즈니스 서비스를 빠르게 확장했습니다.](sql-database-implementation-snelstart.md) | 어떻게 SnelStart가 Azure를 사용하여 매월 1,000개의 새 Azure SQL 데이터베이스 규모로 비즈니스 서비스를 빠르게 확장했는지 알아봅니다. |
| 4 | [Umbraco는 Azure SQL Database를 사용하여 클라우드의 수천 개의 테넌트를 위해 서비스를 신속하게 프로비전하고 확장합니다.](sql-database-implementation-umbraco.md) | 어떻게 Umbraco가 SQL 데이터베이스를 사용하여 클라우드의 수천 개의 테넌트를 위해 서비스를 신속하게 프로비전하고 확장하는지 알아봅니다. |
| 5 | [PowerShell을 사용하여 Azure SQL Database 관리](sql-database-manage-powershell.md) | PowerShell을 사용한 Azure SQL 데이터베이스 관리. |
| 6 | [SQL 데이터베이스 및 SQL 데이터 웨어하우스를 사용한 Azure AD MFA에 대한 SSMS 지원](sql-database-ssms-mfa-authentication.md) | SQL 데이터베이스 및 SQL 데이터 웨어하우스용 SSMS에서 Multi-Factor Authentication 사용 |
| 7 | [DTU(데이터베이스 트랜잭션 단위) 및 eDTU(Elastic Database 트랜잭션 단위) 설명](sql-database-what-is-a-dtu.md) | Azure SQL Database 트랜잭션 단위가 무엇인지 이해합니다. |


## <a name="updated-articles,-sql-database"></a>업데이트된 문서, SQL Database

이 섹션에서는 최근에 대규모로 수행되거나 중요하게 업데이트된 문서가 목록으로 표시됩니다. 업데이트된 각 문서에 대해 추가된 markdown 텍스트의 대략적인 코드 조각이 표시됩니다. 날짜 범위 **2016-08-22**부터 **2016-10-05**까지 문서가 업데이트되었습니다.

| &nbsp; | 문서 | 업데이트된 텍스트, 코드 조각 | 업데이트 시기 |
| --: | :-- | :-- | :-- |
| 8 | [PowerShell을 사용하여 Azure SQL 데이터베이스 관리](sql-database-command-line-tools.md) | New-AzureRmResourceGroup(https://msdn.microsoft.com/library/azure/mt759837.aspx) cmdlet으로 SQL Database 및 관련 Azure 리소스에 대한 리소스 그룹을 만듭니다. * $resourceGroupName = "resourcegroup1" $resourceGroupLocation = "northcentralus" New-AzureRmResourceGroup -Name $resourceGroupName -Location $resourceGroupLocation * 자세한 내용은 Azure Resource Manager로 Azure PowerShell 사용(../powershell-azure-resource-manager.md)을 참조하세요. 샘플 스크립트는 SQL 데이터베이스 PowerShell 스크립트 만들기(sql-database-get-started-powershell.md create-a-sql-database-powershell-script)를 참조하세요. ** SQL Database 서버 만들기** New-AzureRmSqlServer(https://msdn.microsoft.com/library/azure/mt603715.aspx) cmdlet으로 SQL Database 서버 만들기. *server1* 을 서버의 이름으로 바꿉니다. 서버 이름은 모든 Azure SQL Database 서버 간에 고유해야 합니다. 이미 사용한 서버 이름인 경우 오류가 발생합니다. 이 명령을 완료하는 데는 몇 분 정도 걸릴 수 있습니다. 리소스 | 2016-09-14 |
| 9 | [PowerShell을 사용하여 Azure SQL 데이터베이스 복사](sql-database-copy-powershell.md) |   SQL 데이터베이스 원본(복사할 기존 데이터베이스)  --  $sourceDbName = "db1"  $sourceDbServerName = "server1"  $sourceDbResourceGroupName = "rg1"  SQL 데이터베이스 복사(새로 만들 데이터베이스)  --  $copyDbName = "db1_copy"  $copyDbServerName = "server2"  $copyDbResourceGroupName = "rg2"  같은 서버에 데이터베이스 복사  --  New-AzureRmSqlDatabaseCopy -ResourceGroupName $sourceDbResourceGroupName -ServerName $sourceDbServerName -DatabaseName $sourceDbName -CopyDatabaseName $copyDbName  다른 서버에 데이터베이스 복사  --  New-AzureRmSqlDatabaseCopy -ResourceGroupName $sourceDbResourceGroupName -ServerName $sourceDbServerName -DatabaseName $sourceDbName -CopyResourceGroupName $copyDbResourceGroupName -CopyServerName $copyDbServerName -CopyDatabaseName $copyDbName  Elastic Database 풀에 데이터베이스 복사  --  $poolName = "pool1"  New-AzureRmSqlDatabaseCopy -ResourceGroupName $sourceDbResourceGroupName -ServerName $sourceDbServerName -DatabaseName $sourceDbName -CopyReso | 2016-09-08 |
| 10 | [C#을 사용한 탄력적 데이터베이스 풀 만들기](sql-database-elastic-pool-create-csharp.md) |   Console.WriteLine("Server firewall...");  FirewallRuleGetResponse fwr = CreateOrUpdateFirewallRule(_sqlMgmtClient, _resourceGroupName, _serverName, _firewallRuleName, _startIpAddress, _endIpAddress);  Console.WriteLine("Server firewall: " + fwr.FirewallRule.Id);  Console.WriteLine("Elastic pool...");  ElasticPoolCreateOrUpdateResponse epr = CreateOrUpdateElasticDatabasePool(_sqlMgmtClient, _resourceGroupName, _serverName, _poolName, _poolEdition, _poolDtus, _databaseMinDtus, _databaseMaxDtus);  Console.WriteLine("Elastic pool: " + epr.ElasticPool.Id);  Console.WriteLine("Database...");  DatabaseCreateOrUpdateResponse dbr = CreateOrUpdateDatabase(_sqlMgmtClient, _resourceGroupName, _serverName, _databaseName, _poolName);  Console.WriteLine("Database: " + dbr.Database.Id);  Console.WriteLine("계속하려면 아무 키나 누르십시오...");  Console.ReadKey();  }  static ResourceGroup CreateOrUpdateResourceGroup(ResourceManagementClient resourceMgmtClient, string subscriptionId, string resourceGroupNa | 2016-09-14 |
| 11 | [탄력적 데이터베이스 풀에 적합한 데이터베이스를 식별하기 위한 PowerShell 스크립트](sql-database-elastic-pool-database-assessment-powershell.md) | ** Elastic Database 풀 후보의 경우 마스터와 이미 풀에 있는 데이터베이스를 제외합니다. 아래의 제외된 목록에 데이터베이스를 필요한 만큼 추가할 수 있음** $ListOfDBs = Get-AzureRmSqlDatabase -ServerName $servername.Split('.') 0  -ResourceGroupName $ResourceGroupName  /  Where-Object {$_.DatabaseName -notin ("master") -and $_.CurrentServiceLevelObjectiveName -notin ("ElasticPool") -and $_.CurrentServiceObjectiveName -notin ("ElasticPool")} $outputConnectionString = "Data Source=$outputServerName;Integrated Security=false;Initial Catalog=$outputdatabaseName;User Id=$outputDBUsername;Password=$outputDBpassword" $destinationTableName = "resource_stats_output" ** 메트릭 컬렉션의 출력 데이터베이스에 테이블 만들기** $sql = " IF  NOT EXISTS (SELECT * FROM sys.objects WHERE object_id = OBJECT_ID(N'$($destinationTableName)') AND type in (N'U')) BEGIN Create Table $($destinationTableName) (database_name varchar(128), slo varchar(20), end_time datetime, avg_cpu float, avg_ | 2016-09-29 |
| 12 | [SQL Database 자습서: Azure Portal을 사용하여 빠르게 SQL Database 만들기](sql-database-get-started.md) |   ! 새 sql 데이터베이스 1(./media/sql-database-get-started/sql-database-new-database-1.png) 3. **SQL Database** 를 클릭하여 SQL Database 블레이드를 엽니다. 이 블레이드의 콘텐츠는 구독 및 기존 개체(예: 기존 서버)의 수에 따라 달라집니다.  ! 새 sql 데이터베이스 2(./media/sql-database-get-started/sql-database-new-database-2.png) 4. **데이터베이스 이름** 텍스트 상자에 첫 번째 데이터베이스에 대한 이름(예: "my-database")을 입력합니다. 녹색 확인 표시가 유효한 이름을 제공한 것을 나타냅니다.  ! 새 sql 데이터베이스 3(./media/sql-database-get-started/sql-database-new-database-3.png) 5. 구독이 여러 개 있는 경우 구독을 선택합니다. 6. **리소스 그룹** 아래에서 **새로 만들기**를 클릭하고 첫 번째 리소스 그룹의 이름(예: "my-resource-group")을 제공합니다. 녹색 확인 표시가 유효한 이름을 제공한 것을 나타냅니다.  ! 새 sql 데이터베이스 4(./media/sql-database-get-started/sql-database-new-database-4.png) 7. **원본 선택* 아래에서 | 2016-09-08 |
| 13 | [SQL 데이터베이스 시도: C#을 사용하여 .NET용 SQL 데이터베이스 라이브러리로 SQL 데이터베이스 만들기](sql-database-get-started-csharp.md) | ** 리소스에 액세스하는 서비스 주체 만들기** 다음 PowerShell 스크립트는 AD(Active Directory) 응용 프로그램 및 C 앱을 인증해야 하는 서비스 주체를 만듭니다. 스크립트는 이전 C 샘플에 필요한 값을 출력합니다. 자세한 내용은 Azure PowerShell을 사용하여 리소스에 액세스하는 서비스 주체 만들기(../resource-group-authenticate-service-principal.md)를 참조하세요.  Azure에 로그인합니다.  Add-AzureRmAccount  구독이 여러 개 있는 경우 주석을 제거하고 사용하려는 구독을 설정합니다.  $subscriptionId = "{xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx}"  Set-AzureRmContext -SubscriptionId $subscriptionId  새 AAD 앱에 이러한 값을 제공합니다.  $appName은 앱의 표시 이름이며, 디렉터리에서 고유해야 합니다.  $uri가 실제 uri일 필요는 없습니다.  $secret은 사용자가 만드는 암호입니다.  $appName = "{app-name}"  $uri = "http://{app-name}"  $secret = "{app-password}"  AAD 앱 만들기  $azureAdApplication = New-AzureRmADApp | 2016-09-23 |
| 14 | [Azure 포털을 사용하여 Azure SQL 데이터베이스 관리](sql-database-manage-portal.md) | 데이터베이스 설정을 보거나 업데이트하려면 SQL 데이터베이스 블레이드에서 원하는 설정을 클릭합니다. SQL 데이터베이스 설정(./media/sql-database-manage-portal/settings.png) ** SQL 데이터베이스 정규화된 서버 이름을 찾는 방법** 데이터베이스 서버 이름을 보려면 **SQL 데이터베이스** 블레이드에서 **개요**를 클릭하고 서버 이름을 기록해 둡니다. SQL 데이터베이스 설정(./media/sql-database-manage-portal/server-name.png) ** 내 SQL Server 및 데이터베이스에 대한 액세스를 제어하는 방화벽 규칙을 관리하는 방법** 방화벽 규칙을 보거나, 만들거나, 업데이트하려면 **SQL 데이터베이스** 블레이드에서 **Set server firewall**(서버 방화벽 설정)을 클릭합니다. 자세한 내용은 Azure Portal을 사용하여 Azure SQL Database 서버 수준 방화벽 규칙 구성(sql-database-configure-firewall-settings.md)을 참조하세요. ! 방화벽 규칙(./media/sql-database-manage-portal/sql-database-firewall.png) ** SQL 데이터베이스 서비스 계층 또는 성능 수준을 변경하는 방법** SQL 데이터베이스의 서비스 계층 또는 성능 수준을 업데이트하려면 **가격 책정 계층( | 2016-09-20 |





## <a name="get-started"></a>시작

| &nbsp; | 제목 | 설명 |
| --: | :-- | :-- |
| 15 | [Azure SQL 데이터베이스에서 격리되고 효율적인 다중 테넌트 앱 빌드](sql-database-build-multi-tenant-apps.md) | SQL 데이터베이스에서 다중 테넌트 앱을 빌드하는 방법을 알아보세요. |
| 16 | [SQL Server Management Studio를 사용하여 SQL 데이터베이스에 연결하고 샘플 T-SQL 쿼리를 실행합니다.](sql-database-connect-query-ssms.md) | SQL Server Management Studio(SSMS)를 사용하여 SQL 데이터베이스에 연결하는 방법을 알아봅니다. 그런 다음 Transact-SQL(T-SQL)를 사용하여 샘플 쿼리를 실행합니다. |
| 17 | [.NET(C#)을 사용하여 SQL 데이터베이스에 연결](sql-database-develop-dotnet-simple.md) | 이 빠른 시작에 포함된 샘플 코드를 사용하여 C#으로 최신 응용 프로그램을 개발하고 Azure SQL 데이터베이스로 클라우드에서 강력한 관계형 데이터베이스를 통해 지원할 수 있습니다. |
| 18 | [Azure 포털로 새 탄력적 데이터베이스 풀 만들기](sql-database-elastic-pool-create-portal.md) | 여러 데이터베이스 간에 공유되는 관리 및 리소스에 대한 SQL 데이터베이스 구성에 확장성 있는 탄력적 데이터베이스 풀을 추가하는 방법입니다. |
| 19 | [Azure SQL 데이터베이스 자습서 탐색](sql-database-explore-tutorials.md) | SQL 데이터베이스 기능에 대해 알아보기 |
| 20 | [SQL Database 자습서: Azure Portal을 사용하여 빠르게 SQL Database 만들기](sql-database-get-started.md) | SQL Database 논리 서버, 서버 방화벽 규칙, SQL Database 및 샘플 데이터를 설정하는 방법을 알아봅니다. 또한 클라이언트 도구를 사용하여 연결하고 사용자를 구성하며 데이터베이스 방화벽 규칙을 설정하는 방법을 알아봅니다. |
| 21 | [Azure SQL 데이터베이스의 보안 유지 및 보호](sql-database-helps-secures-and-protects.md) | SQL 데이터베이스가 보안 및 보호를 지원하는 방법을 알아봅니다. |
| 22 | [Azure SQL Database 학습 및 적응](sql-database-learn-and-adapt.md) | Azure SQL 데이터베이스가 학습하고 적응하는 방법으 알아봅니다. |
| 23 | [클라우드 SQL Server 옵션 선택: Azure SQL(PaaS) 데이터베이스 또는 Azure VM의 SQL Server(IaaS)](sql-database-paas-vs-sql-server-iaas.md) | 다음 중 어떤 클라우드 SQL Server 옵션이 응용 프로그램에 맞는지 알아봅니다. Azure SQL (PaaS) 데이터베이스 또는 Azure 가상 컴퓨터 클라우드의 SQL Server. |
| 24 | [즉석에서 Azure SQL 데이터베이스 확장](sql-database-scale-on-the-fly.md) | 즉석에서 SQL 데이터베이스를 확장하는 방법을 알아봅니다. |
| 25 | [Azure SQL 데이터베이스 솔루션 빠른 시작 탐색](sql-database-solution-quick-starts.md) | Azure SQL 데이터베이스 솔루션에 대한 자세한 정보 |
| 26 | [사용자 환경에서 작동하는 Azure SQL 데이터베이스](sql-database-works-in-your-environment.md) | SQL 데이터베이스가 도움이 되고 보안을 유지하며 보호하는 방법을 알아봅니다. |



## <a name="build-an-app"></a>앱 빌드

| &nbsp; | 제목 | 설명 |
| --: | :-- | :-- |
| 27 | [SQL 연결 오류와 일시적 SQL 데이터베이스 오류의 문제 해결, 진단 및 예방](sql-database-connectivity-issues.md) | SQL 연결 오류 또는 Azure SQL 데이터베이스의 일시적 오류를 해결, 진단 및 방지하는 방법을 알아봅니다.  |
| 28 | [Visual Studio를 사용하여 SQL 데이터베이스에 연결](sql-database-connect-query.md) | C#에 프로그램을 작성하여 SQL 데이터베이스에 쿼리하고 연결합니다. IP 주소, 연결 문자열, 보안 로그인 및 무료 Visual Studio에 대한 정보입니다. |
| 29 | [ADO.NET 4.5 및 SQL 데이터베이스 V12에 대한 1433 이외의 포트](sql-database-develop-direct-route-ports-adonet-v12.md) | ADO.NET에서 Azure SQL 데이터베이스 V12로 클라이언트 연결이 프록시를 무시하고 데이터베이스와 직접 상호 작용하는 경우가 있습니다. 1433 이외의 포트가 중요해집니다. |
| 30 | [SQL 데이터베이스 클라이언트 응용 프로그램의 SQL 오류 코드: 데이터베이스 연결 오류 및 기타 문제](sql-database-develop-error-messages.md) | 일반적인 데이터베이스 연결 오류, 데이터베이스 복사 문제 및 일반적인 오류와 같은 SQL 데이터베이스 클라이언트 응용 프로그램에 대한 SQL 오류 코드에 대해 알아봅니다.  |
| 31 | [Windows에서 PHP를 사용하여 SQL 데이터베이스에 연결](sql-database-develop-php-simple.md) | Windows 클라이언트에서 Azure SQL 데이터베이스에 연결하는 샘플 PHP 프로그램을 제시하고, 클라이언트에 필요한 필수 소프트웨어 구성 요소의 링크를 제공합니다. |
| 32 | [SQL 데이터베이스 시도: C#을 사용하여 .NET용 SQL 데이터베이스 라이브러리로 SQL 데이터베이스 만들기](sql-database-get-started-csharp.md) | SQL 및 C# 앱 개발을 위해 SQL 데이터베이스를 시도하고 .NET용 SQL 데이터베이스 라이브러리를 사용하여 C#으로 Azure SQL 데이터베이스를 만듭니다. |
| 33 | [Azure SQL 데이터베이스의 JSON 기능 시작](sql-database-json-features.md) | Azure SQL 데이터베이스에서는 JSON(JavaScript Object Notation) 표기법으로 데이터 구문 분석, 쿼리 및 서식 지정을 수행할 수 있습니다. |
| 34 | [Azure SQL 데이터베이스에 대한 연결 문제 해결](sql-database-troubleshoot-common-connection-issues.md) | Azure SQL 데이터베이스에 대한 일반적인 연결 오류를 확인 및 해결하는 단계 |
| 35 | [SQL 데이터베이스에 연결할 때 “현재 서버의 데이터베이스를 사용할 수 없습니다” 오류](sql-database-troubleshoot-connection.md) | 응용 프로그램이 SQL 데이터베이스에 연결할 때 현재 서버의 데이터베이스를 사용할 수 없습니다 오류를 해결합니다. |



## <a name="develop"></a>개발

| &nbsp; | 제목 | 설명 |
| --: | :-- | :-- |
| 36 | [코드에서 SQL Database에 액세스하는 응용 프로그램을 인증하는 데 필요한 값 가져오기](sql-database-client-id-keys.md) | 코드에서 SQL Database에 액세스하기 위한 서비스 사용자를 만듭니다. |
| 37 | [Windows에서 Java JDBC를 사용하여 SQL 데이터베이스에 연결합니다.](sql-database-develop-java-simple.md) | Azure SQL 데이터베이스에 연결하는 데 사용할 수 있는 Java 코드 샘플을 제시합니다. 이 샘플에서는 JDBC를 사용하며, Windows 클라이언트 컴퓨터에서 실행됩니다. |
| 38 | [Node.js를 사용하여 SQL 데이터베이스에 연결](sql-database-develop-nodejs-simple.md) | Azure SQL 데이터베이스에 연결하는 데 사용할 수 있는 Node.js 코드 샘플을 제시합니다. |
| 39 | [SQL 데이터베이스 개발 개요](sql-database-develop-overview.md) | 사용 가능한 연결 라이브러리 및 SQL 데이터베이스에 연결하는 응용 프로그램에 대한 모범 사례를 알아봅니다. |
| 40 | [Python을 사용하여 SQL 데이터베이스에 연결](sql-database-develop-python-simple.md) | Azure SQL 데이터베이스에 연결하는 데 사용할 수 있는 Python 코드 샘플을 제시합니다. |
| 41 | [Ruby를 사용하여 SQL 데이터베이스에 연결](sql-database-develop-ruby-simple.md) | Azure SQL 데이터베이스에 연결하기 위해 실행할 수 있는 Ruby 코드 샘플을 제공합니다. |
| 42 | [Azure SQL 데이터베이스의 임시 테이블 시작](sql-database-temporal-tables.md) | Azure SQL 데이터베이스에서 임시 테이블을 사용하여 시작하는 방법을 알아봅니다. |



## <a name="performance-and-scale"></a>성능 및 확장

| &nbsp; | 제목 | 설명 |
| --: | :-- | :-- |
| 43 | [SQL 데이터베이스 관리자](sql-database-advisor.md) | Azure SQL 데이터베이스 관리자는 현재 쿼리 성능을 향상시킬 수 있는 기존 SQL 데이터베이스에 대한 권장 사항을 제공합니다. |
| 44 | [Azure Portal을 사용하는 SQL Database 관리자](sql-database-advisor-portal.md) | Azure 포털의 Azure SQL 데이터베이스 관리자를 사용하여 현재 쿼리 성능을 향상시킬 수 있는 기존 SQL 데이터베이스에 대한 권장 사항을 검토하고 구현할 수 있습니다. |
| 45 | [Azure SQL 데이터베이스 벤치마크 개요](sql-database-benchmark-overview.md) | 이 항목은 Azure SQL 데이터베이스의 성능 측정에 사용된 Azure SQL 데이터베이스 벤치마크에 대해 설명합니다. |
| 46 | [탄력적 데이터베이스 풀을 사용해야 하는 경우](sql-database-elastic-pool-guidance.md) | 탄력적 데이터베이스 풀은 탄력적 데이터베이스 그룹에서 공유하는 가용 리소스의 컬렉션입니다. 이 문서는 탄력적 데이터베이스 풀을 사용하여 데이터베이스의 그룹에 대한 적합성을 평가하는데 도움이 되는 지침을 제공합니다. |
| 47 | [탄력적 데이터베이스 도구 시작하기](sql-database-elastic-scale-get-started.md) | 쉽게 실행할 수 있는 샘플 앱을 포함하여 Azure SQL 데이터베이스의 탄력적 데이터베이스 도구 기능에 대해 기본적으로 설명합니다. |
| 48 | [SQL 데이터베이스 FAQ](sql-database-faq.md) | 클라우드 데이터베이스 및 Azure SQL 데이터베이스, Microsoft 관계형 데이터베이스 관리 시스템(RDBMS) 및 클라우드에서 서비스로 데이터베이스에 대해 고객이 궁금해하는 일반적인 질문에 대한 답변입니다. |
| 49 | [SQL 데이터베이스에서 메모리 내 시작(미리 보기)](sql-database-in-memory.md) | SQL 메모리 내 기술은 트랜잭션 및 분석 작업의 성능을 크게 향상시킵니다. 이러한 기술을 활용하는 방법을 알아봅니다. |
| 50 | [메모리 내 OLTP(미리 보기)를 사용하여 SQL 데이터베이스에서 응용 프로그램의 성능 향상](sql-database-in-memory-oltp-migration.md) | 메모리 내 OLTP를 채택하여 기존 SQL 데이터베이스의 트랜잭션 성능을 향상합니다. |
| 51 | [메모리 내 OLTP 저장소 모니터링](sql-database-in-memory-oltp-monitoring.md) | XTP 메모리 내 저장소 사용, 용량을 예측 및 모니터링합니다. 41823 용량 오류를 해결합니다. |
| 52 | [Azure SQL 데이터베이스에서 쿼리 저장소 운영](sql-database-operate-query-store.md) | Azure SQL 데이터베이스에서 쿼리 저장소를 운영하는 방법 알아보기 |
| 53 | [SQL 데이터베이스 성능 Insight](sql-database-performance.md) | Azure SQL 데이터베이스는 현재 쿼리 성능을 향상시킬 수 있는 영역을 식별하는 데 도움이 되는 성능 도구를 제공합니다. |
| 54 | [Azure SQL Database 및 단일 데이터베이스의 성능](sql-database-performance-guidance.md) | 이 문서는 응용 프로그램에 대해 선택할 서비스 계층을 결정하는 데 도움이 될 수 있습니다. 또한 Azure SQL Database를 활용하도록 응용 프로그램을 튜닝하는 방법도 권고합니다. |
| 55 | [Azure SQL 데이터베이스 쿼리 성능 Insight](sql-database-query-performance.md) | 쿼리 성능 모니터링은 Azure SQL 데이터베이스에 대한 대부분의 CPU 사용 쿼리를 식별합니다. |
| 56 | [SQL 데이터베이스의 서비스 계층 및 성능 수준(가격 책정 계층) 변경](sql-database-scale-up.md) | Azure SQL 데이터베이스의 서비스 계층 및 성능 수준 변경에서는 SQL 데이터베이스 규모 확장 또는 축소 방법을 보여 줍니다. Azure SQL 데이터베이스의 가격 책정 계층 변경 |
| 57 | [Azure SQL 데이터베이스에서 데이터베이스 성능 모니터링](sql-database-single-database-monitor.md) | Azure 도구 및 동적 관리 뷰를 사용하여 데이터베이스를 모니터링하기 위한 옵션에 대해 알아봅니다. |
| 58 | [SQL 데이터베이스 성능 튜닝 팁](sql-database-troubleshoot-performance.md) | 평가 및 개선을 통한 Azure SQL 데이터베이스의 성능 튜닝 관련 팁. |
| 59 | [SQL 데이터베이스 응용 프로그램 성능을 개선하기 위해 일괄 처리를 사용하는 방법](sql-database-use-batching-to-improve-performance.md) | 이 문서는 데이터베이스 작업을 일괄 처리하면 Azure SQL 데이터베이스 응용 프로그램의 속도와 확장성이 매우 향상된다는 증거를 제공합니다. 이러한 일괄 처리 기법은 SQL Server 데이터베이스에 적용되지만 이 문서는 Azure에 중점을 두었습니다. |



## <a name="tools-for-scaling-out"></a>규모 확장용 도구

| &nbsp; | 제목 | 설명 |
| --: | :-- | :-- |
| 60 | [다중 테넌트 SaaS 응용 프로그램 및 Azure SQL 데이터베이스에 대한 디자인 패턴](sql-database-design-patterns-multi-tenancy-saas-applications.md) | 이 문서에서는 클라우드 환경에서 실행되는 다중 테넌트 데이터베이스 응용 프로그램에 대해 고려해야 하는 요구 사항과 일반적인 데이터 아키텍처 패턴을 설명하고 이러한 패턴과 관련된 다양한 장단점을 설명합니다. 그리고 Azure SQL Database가 탄력적 풀 및 탄력적 도구를 통해 다른 기능도 동일하게 제공하면서 이러한 요구 사항의 처리를 지원하는 방법도 설명합니다. |
| 61 | [확장하기 위해 기존 데이터베이스 마이그레이션](sql-database-elastic-convert-to-use-elastic-tools.md) | 분할된 데이터베이스 맵 관리자를 만들어 탄력적 데이터베이스 도구를 사용하기 위해 분할된 데이터베이스 변환 |
| 62 | [확장성 있는 클라우드 데이터베이스 빌드](sql-database-elastic-database-client-library.md) | 탄력적 데이터베이스 클라이언트 라이브러리와 함께 확장성 있는 .NET 데이터베이스 앱 빌드 |
| 63 | [분할된 맵 관리자에 대한 성능 카운터](sql-database-elastic-database-perf-counters.md) | ShardMapManager 클래스 및 데이터 종속 라우팅 성능 카운터 |
| 64 | [탄력적 데이터베이스 도구를 사용하여 분할된 데이터베이스 추가하기](sql-database-elastic-scale-add-a-shard.md) | 이 문서에서는 탄력적인 확장 API를 사용하여 새 분할된 데이터베이스를 분할된 데이터베이스 집합에 추가하는 방법을 설명합니다. |
| 65 | [분할/병합 서비스 배포](sql-database-elastic-scale-configure-deploy-split-and-merge.md) | 탄력적 데이터베이스 도구를 사용하는 분할 및 병합 |
| 66 | [데이터 종속 라우팅](sql-database-elastic-scale-data-dependent-routing.md) | Azure SQL 데이터베이스의 탄력적 데이터베이스 기능인 데이터 종속 라우팅을 위해 .NET 앱에서 ShardMapManager 클래스를 사용하는 방법 |
| 67 | [탄력적 데이터베이스 도구 FAQ](sql-database-elastic-scale-faq.md) | Azure SQL 데이터베이스의 탄력적인 확장에 대한 질문과 대답을 제공합니다. |
| 68 | [탄력적 데이터베이스 도구 용어집](sql-database-elastic-scale-glossary.md) | 탄력적 데이터베이스 도구에 쓰이는 용어 설명. |
| 69 | [Azure SQL 데이터베이스를 사용하여 확장](sql-database-elastic-scale-introduction.md) | SaaS(Software as a Service) 개발자는 이러한 도구를 사용하여 클라우드에서 확장 가능한 탄력적 데이터베이스를 쉽게 만들 수 있습니다. |
| 70 | [탄력적 데이터베이스 클라이언트 라이브러리 액세스에 사용되는 자격 증명](sql-database-elastic-scale-manage-credentials.md) | 탄력적 데이터베이스 확장 앱에 대해 올바른 수준의 자격 증명(관리자부터 읽기 전용까지)을 설정하는 방법입니다. |
| 71 | [다중 분할된 데이터베이스 쿼리](sql-database-elastic-scale-multishard-querying.md) | 분할된 데이터베이스간의 쿼리를 실행할 때는 탄력적 데이터베이스 클라이언트 라이브러리를 사용합니다. |
| 72 | [확장된 클라우드 데이터베이스 간 데이터 이동](sql-database-elastic-scale-overview-split-and-merge.md) | 탄력적 데이터베이스 API를 사용하여 자체 호스팅되는 서비스를 통해 분할된 데이터베이스를 조작하고 데이터를 이동하는 방법에 대해 설명합니다. |
| 73 | [분할된 데이터베이스 맵 관리자를 사용하여 데이터베이스 확장](sql-database-elastic-scale-shard-map-management.md) | ShardMapManager 및 .NET용 탄력적 데이터베이스를 사용하는 방법 |
| 74 | [분할-병합 보안 구성](sql-database-elastic-scale-split-merge-security-configuration.md) | 암호화에 대한 409 인증서를 설정 |
| 75 | [최신 탄력적 데이터베이스 클라이언트 라이브러리를 사용하도록 앱 업그레이드](sql-database-elastic-scale-upgrade-client-library.md) | Nuget을 사용하여 앱 및 라이브러리 업그레이드 |
| 76 | [엔터티 프레임 작업과 함께 탄력적 데이터베이스 클라이언트 라이브러리](sql-database-elastic-scale-use-entity-framework-applications-visual-studio.md) | 데이터베이스 코딩을 위해 탄력적 데이터베이스 클라이언트 라이브러리 및 Entity Framework 사용 |
| 77 | [Dapper과 함께 탄력적 데이터베이스 클라이언트 라이브러리 사용](sql-database-elastic-scale-working-with-dapper.md) | Dapper과 함께 탄력적 데이터베이스 클라이언트 라이브러리 사용 |
| 78 | [탄력적 데이터베이스 도구 및 행 수준 보안을 제공하는 다중 테넌트 응용 프로그램](sql-database-elastic-tools-multi-tenant-row-level-security.md) | 탄력적 데이터베이스 도구와 행 수준 보안을 함께 사용하여 다중 테넌트 분할된 데이터베이스를 지원하고 Azure SQL 데이터베이스의 데이터 계층을 유연하게 확장할 수 있는 응용 프로그램 구축 방법에 대해 알아보세요. |



## <a name="elastic-jobs"></a>탄력적 작업

| &nbsp; | 제목 | 설명 |
| --: | :-- | :-- |
| 79 | [탄력적 작업을 사용하여 규모가 확장된 Azure SQL 데이터베이스 만들기 및 관리(미리 보기)](sql-database-elastic-jobs-create-and-manage.md) | 탄력적 데이터베이스 작업 만들기 및 관리 과정을 단계별로 안내합니다. |
| 80 | [탄력적 데이터베이스 작업 시작](sql-database-elastic-jobs-getting-started.md) | 탄력적 데이터베이스 작업을 사용하는 방법 |
| 81 | [규모가 확장된 클라우드 데이터베이스 관리](sql-database-elastic-jobs-overview.md) | 탄력적 데이터베이스 작업 서비스를 설명합니다. |
| 82 | [PowerShell을 사용하여 SQL 데이터베이스 탄력적 데이터베이스 작업 만들기 및 관리(미리 보기)](sql-database-elastic-jobs-powershell.md) | Azure SQL 데이터베이스 풀을 관리하는데 사용되는 PowerShell |
| 83 | [탄력적 데이터베이스 작업 설치 개요](sql-database-elastic-jobs-service-installation.md) | 탄력적 작업 기능의 설치에 대한 단계별 안내 |
| 84 | [탄력적 데이터베이스 작업 구성 요소 제거](sql-database-elastic-jobs-uninstall.md) | 탄력적 데이터베이스 작업 도구를 제거하는 방법 |



## <a name="elastic-pools"></a>탄력적 풀

| &nbsp; | 제목 | 설명 |
| --: | :-- | :-- |
| 85 | [Azure 탄력적 풀이란?](sql-database-elastic-pool.md) | 풀을 사용하여 수백 또는 수천 개의 데이터베이스를 관리합니다. 성능 단위 집합에 대한 하나의 가격을 풀에 분배할 수 있습니다. 데이터베이스를 내/외부로 자유롭게 이동합니다. |
| 86 | [C#을 사용한 탄력적 데이터베이스 풀 만들기](sql-database-elastic-pool-create-csharp.md) | C# 데이터베이스 개발 기술을 사용하여 Azure SQL 데이터베이스에 확장성 있는 탄력적 데이터베이스 풀을 만들면 여러 데이터베이스에서 리소스를 공유할 수 있습니다. |
| 87 | [PowerShell을 사용하여 새 탄력적 데이터베이스 풀 만들기](sql-database-elastic-pool-create-powershell.md) | PowerShell을 사용하여 여러 데이터베이스를 관리하도록 확장성 있는 탄력적 데이터베이스 풀을 만들어 Azure SQL 데이터베이스 리소스를 규모 확장하는 방법에 대해 알아봅니다. |
| 88 | [탄력적 데이터베이스 풀에 적합한 데이터베이스를 식별하기 위한 PowerShell 스크립트](sql-database-elastic-pool-database-assessment-powershell.md) | 탄력적 데이터베이스 풀은 탄력적 데이터베이스 그룹에서 공유하는 가용 리소스의 컬렉션입니다. 이 문서는 탄력적 데이터베이스 풀을 사용하여 데이터베이스의 그룹에 대한 적합성을 평가하는데 유용한 Powershell 스크립트를 제공합니다. |
| 89 | [C#으로 탄력적 데이터베이스 풀 모니터링 및 관리](sql-database-elastic-pool-manage-csharp.md) | C# 데이터베이스 개발 기술을 사용하여 Azure SQL 데이터베이스 탄력적 데이터베이스 풀을 관리합니다. |
| 90 | [Azure 포털을 사용하여 탄력적 데이터베이스 풀 모니터링 및 관리](sql-database-elastic-pool-manage-portal.md) | Azure 포털 및 SQL 데이터베이스의 기본 제공 인텔리전스를 사용하여 성능을 최적화하고 비용을 관리하기 위해 확장성 있는 탄력적 데이터베이스 풀을 관리, 모니터링, 적절히 조정하는 방법에 대해 알아봅니다. |
| 91 | [PowerShell로 탄력적 데이터베이스 풀 모니터링 및 관리](sql-database-elastic-pool-manage-powershell.md) | PowerShell을 사용하여 탄력적 데이터베이스 풀을 관리하는 방법을 알아봅니다. |
| 92 | [Transact-SQL로 탄력적 데이터베이스 풀 모니터링 및 관리](sql-database-elastic-pool-manage-tsql.md) | T-SQL을 사용하여 탄력적 풀에 Azure SQL 데이터베이스를 만듭니다. 또는 T-SQL을 사용하여 풀 내부 및 외부로 데이터베이스를 이동합니다. |
| 93 | [탄력적 데이터베이스 풀 대금 청구 및 가격 정보](sql-database-elastic-pool-price.md) | 탄력적 데이터베이스 풀 관련 가격 정보입니다. |



## <a name="elastic-query"></a>탄력적 쿼리

| &nbsp; | 제목 | 설명 |
| --: | :-- | :-- |
| 94 | [확장된 클라우드 데이터베이스에서 보고(미리 보기)](sql-database-elastic-query-getting-started.md) | 데이터베이스 간 쿼리 사용 방법 |
| 95 | [데이터베이스 간 쿼리 시작(수직 분할)(미리 보기)](sql-database-elastic-query-getting-started-vertical.md) | 수직 분할 데이터베이스에서 탄력적 데이터베이스 쿼리를 사용하는 방법 |
| 96 | [확장된 클라우드 데이터베이스에서 보고(미리 보기)](sql-database-elastic-query-horizontal-partitioning.md) | 행 분할에서 탄력적 쿼리를 설정하는 방법 |
| 97 | [Azure SQL 탄력적 데이터베이스 쿼리 개요(미리 보기)](sql-database-elastic-query-overview.md) | 탄력적 쿼리 기능 개요 |
| 98 | [여러 스키마를 사용하여 클라우드 데이터베이스에서 쿼리(미리 보기)](sql-database-elastic-query-vertical-partitioning.md) | 수직 분할을 통해 데이터베이스 간 쿼리를 설정하는 방법 |



## <a name="elastic-transaction"></a>탄력적 트랜잭션

| &nbsp; | 제목 | 설명 |
| --: | :-- | :-- |
| 99 | [클라우드 데이터베이스의 분산 트랜잭션](sql-database-elastic-transactions-overview.md) | Azure SQL 데이터베이스와 탄력적 데이터베이스 트랜잭션 개요 |



## <a name="backup-and-recovery"></a>백업 및 복구

| &nbsp; | 제목 | 설명 |
| --: | :-- | :-- |
| 100 | [SQL Database 백업](sql-database-automated-backups.md) | Azure SQL Database를 이전 지정 시간으로 롤백하거나 지리적 영역(최대 35일)의 새 데이터베이스에 데이터베이스를 복사할 수 있도록 하는 SQL Database 기본 제공 데이터베이스 백업에 대해 알아봅니다. |
| 101 | [Azure SQL 데이터베이스의 비즈니스 연속성 개요](sql-database-business-continuity.md) | Azure SQL 데이터베이스에서 클라우드 무중단 업무 방식 및 데이터베이스 복구를 지원하고 중요 업무용 클라우드 응용 프로그램을 계속해서 실행할 수 있도록 하는 방법을 알아봅니다. |
| 102 | [Azure SQL 데이터베이스 백업에서 단일 테이블을 복원하는 방법](sql-database-cloud-migrate-restore-single-table-azure-backup.md) | Azure SQL 데이터베이스 백업에서 단일 테이블을 복원하는 방법을 알아봅니다. |
| 103 | [SQL 데이터베이스에서 활성 지역 복제를 사용하여 클라우드 재해 복구를 위한 응용 프로그램 설계](sql-database-designing-cloud-solutions-for-disaster-recovery.md) | Azure SQL 데이터베이스의 앱 데이터 백업에 대한 지역에서 복제를 사용하여 무중단 업무 방식 계획에 대한 클라우드 재해 복구 솔루션을 디자인하는 방법을 알아봅니다. |
| 104 | [Azure SQL 데이터베이스 복원 또는 보조 데이터베이스에 대한 장애 조치](sql-database-disaster-recovery.md) | Azure SQL 데이터베이스 활성 지역 복제 및 지역 복원 기능을 사용하여 하위 지역 데이터 센터 중단 또는 오류로부터 데이터베이스를 복구하는 방법에 대해 알아봅니다. |
| 105 | [재해 복구 훈련 수행](sql-database-disaster-recovery-drills.md) | Azure SQL 데이터베이스를 사용하여 중요 업무용 비즈니스 응용 프로그램이 오류 및 중단에 대한 복원력을 유지하도록 만드는 방법에 대한 지침과 모범 사례에 대해 알아봅니다. |
| 106 | [SQL 데이터베이스의 탄력적 풀을 사용하는 응용 프로그램에 대한 재해 복구 전략](sql-database-disaster-recovery-strategies-for-applications-with-elastic-pool.md) | 올바른 장애 조치 패턴을 선택하여 재해 복구를 위한 클라우드 솔루션을 설계하는 방법에 대해 알아봅니다. |
| 107 | [RecoveryManager 클래스를 사용하여 분할된 데이터베이스 맵 문제 해결](sql-database-elastic-database-recovery-manager.md) | RecoveryManager 클래스를 사용하여 분할된 데이터베이스 맵의 문제 해결 |
| 108 | [BACPAC 파일을 가져와 Azure SQL 데이터베이스 만들기](sql-database-import.md) | 기존 BACPAC 파일을 가져와 Azure SQL 데이터베이스를 만듭니다. |
| 109 | [Azure 포털을 사용하여 이전 시점으로 Azure SQL 데이터베이스 복원](sql-database-point-in-time-restore-portal.md) | 이전 시점으로 Azure SQL 데이터베이스를 복원합니다. |
| 110 | [PowerShell을 사용하여 이전 시점으로 Azure SQL 데이터베이스 복원](sql-database-point-in-time-restore-powershell.md) | 이전 시점으로 Azure SQL 데이터베이스를 복원합니다. |
| 111 | [복구된 Azure SQL 데이터베이스 마무리](sql-database-recovered-finalize.md) | 특정 시점 복원, Microsoft Azure SQL 데이터베이스, 데이터베이스 복원, 데이터베이스 복구, Azure 클래식 포털, Azure 클래식 포털 |
| 112 | [자동화된 데이터베이스 백업을 사용하여 Azure SQL 데이터베이스 복구](sql-database-recovery-using-backups.md) | Azure SQL 데이터베이스를 이전 시점(최대 35일)에 롤백할 수 있는 특정 시점 복원에 대해 알아봅니다. |
| 113 | [Azure 포털을 사용하여 삭제된 Azure SQL 데이터베이스 복원](sql-database-restore-deleted-database-portal.md) | 삭제된 Azure SQL 데이터베이스를 복원합니다(Azure 포털). |
| 114 | [PowerShell을 사용하여 삭제된 Azure SQL 데이터베이스 복원](sql-database-restore-deleted-database-powershell.md) | 삭제된 Azure SQL 데이터베이스를 복원합니다(PowerShell). |
| 115 | [이전 시점으로 데이터베이스 복원, 삭제된 데이터베이스 복원 또는 데이터 센터 가동 중단에서 복구](sql-database-troubleshoot-backup-and-restore.md) | Azure SQL 데이터베이스에서 백업 및 복제본을 사용하여 오류 및 가동 중단으로부터 클라우드 데이터베이스를 복원하는 방법에 대해 알아보세요. |



## <a name="migrate"></a>마이그레이션

| &nbsp; | 제목 | 설명 |
| --: | :-- | :-- |
| 116 | [SqlPackage를 사용하여 SQL Server 데이터베이스를 BACPAC 파일로 내보내기](sql-database-cloud-migrate-compatible-export-bacpac-sqlpackage.md) | Microsoft Azure SQL 데이터베이스, 데이터베이스 마이그레이션, 데이터베이스 내보내기, BACPAC 파일 내보내기, sqlpackage |
| 117 | [SQL Server Management Studio를 사용하여 SQL Server 데이터베이스를 BACPAC 파일로 내보내기](sql-database-cloud-migrate-compatible-export-bacpac-ssms.md) | Microsoft Azure SQL 데이터베이스, 데이터베이스 마이그레이션, 데이터베이스 내보내기, BACPAC 파일 내보내기, 데이터 계층 응용 프로그램 내보내기 마법사 |
| 118 | [SqlPackage를 사용하여 BACPAC 파일에서 SQL 데이터베이스로 가져오기](sql-database-cloud-migrate-compatible-import-bacpac-sqlpackage.md) | Microsoft Azure SQL 데이터베이스, 데이터베이스 마이그레이션, 데이터베이스 가져오기, BACPAC 파일 가져오기, sqlpackage |
| 119 | [SSMS를 사용하여 BACPAC에서 SQL 데이터베이스로 가져오기](sql-database-cloud-migrate-compatible-import-bacpac-ssms.md) | Microsoft Azure SQL 데이터베이스, 데이터베이스 배포, 데이터베이스 마이그레이션, 데이터베이스 가져오기, 데이터베이스 내보내기, 마이그레이션 마법사 |
| 120 | [Microsoft Azure 데이터베이스에 데이터베이스 배포 마법사를 사용하여 SQL Server 데이터베이스를 SQL 데이터베이스로 마이그레이션](sql-database-cloud-migrate-compatible-using-ssms-migration-wizard.md) | Microsoft Azure SQL 데이터베이스, 데이터베이스 마이그레이션, Microsoft Azure 데이터베이스 마법사 |
| 121 | [트랜잭션 복제를 사용하여 Azure SQL Server 데이터베이스를 SQL 데이터베이스로 마이그레이션](sql-database-cloud-migrate-compatible-using-transactional-replication.md) | Microsoft Azure SQL 데이터베이스, 데이터베이스 마이그레이션, 데이터베이스 가져오기, 트랜잭션 복제 |
| 122 | [SqlPackage.exe를 사용하여 SQL 데이터베이스 호환성 확인](sql-database-cloud-migrate-determine-compatibility-sqlpackage.md) | Microsoft Azure SQL 데이터베이스, 데이터베이스 마이그레이션, SQL 데이터베이스 호환성, SqlPackage |
| 123 | [Azure SQL 데이터베이스로 마이그레이션하기 전에 SQL Server Management Studio를 사용하여 SQL 데이터베이스 호환성 확인](sql-database-cloud-migrate-determine-compatibility-ssms.md) | Microsoft Azure SQL 데이터베이스, 데이터베이스 마이그레이션, SQL 데이터베이스 호환성, 데이터 계층 응용 프로그램 내보내기 마법사 |
| 124 | [Azure SQL 데이터베이스로 마이그레이션하기 전에 SQL Azure 마이그레이션 마법사를 사용하여 SQL Server 데이터베이스 호환성 문제 해결](sql-database-cloud-migrate-fix-compatibility-issues.md) | Microsoft Azure SQL 데이터베이스, 데이터베이스 마이그레이션, 호환성, SQL Azure 마이그레이션 마법사 |
| 125 | [Visual Studio용 SQL Server Data Tools를 사용하여 Azure SQL 데이터베이스로 SQL Server 데이터베이스 마이그레이션](sql-database-cloud-migrate-fix-compatibility-issues-ssdt.md) | Microsoft Azure SQL 데이터베이스, 데이터베이스 마이그레이션, 호환성, SQL Azure 마이그레이션 마법사, SSDT |
| 126 | [SQL 데이터베이스로 마이그레이션하기 전에 SQL Server Management Studio를 사용하여 SQL Server 데이터베이스 호환성 문제 해결](sql-database-cloud-migrate-fix-compatibility-issues-ssms.md) | Microsoft Azure SQL 데이터베이스, 데이터베이스 마이그레이션, 호환성, SQL Azure 마이그레이션 마법사 |
| 127 | [PowerShell을 사용하여 Azure SQL 데이터베이스를 BACPAC 파일에 보관](sql-database-export-powershell.md) | PowerShell을 사용하여 Azure SQL 데이터베이스를 BACPAC 파일에 보관 |
| 128 | [PowerShell을 사용하여 BACPAC 파일을 가져와 Azure SQL 데이터베이스 만들기](sql-database-import-powershell.md) | PowerShell을 사용하여 BACPAC 파일을 가져와 Azure SQL 데이터베이스 만들기 |
| 129 | [CSV에서 Azure SQL 데이터 웨어하우스로 데이터 로드(플랫 파일)](sql-database-load-from-csv-with-bcp.md) | 작은 데이터 크기의 경우 bcp를 사용하여 Azure SQL 데이터베이스로 데이터를 가져옵니다. |
| 130 | [서버 간, 구독 간, Azure 내부 및 외부로 데이터베이스 이동](sql-database-troubleshoot-moving-data.md) | Azure SQL 데이터베이스에서 데이터 및 데이터베이스를 복사, 이동 및 마이그레이션하는 빠른 단계입니다. |



## <a name="move-data-in-and-out"></a>데이터 입출력 이동

| &nbsp; | 제목 | 설명 |
| --: | :-- | :-- |
| 131 | [클라우드에서 SQL 데이터베이스로 SQL Server 데이터베이스 마이그레이션](sql-database-cloud-migrate.md) | 클라우드에서 Azure SQL 데이터베이스로 온-프레미스 SQL Server 데이터베이스를 마이그레이션하는 방법을 알아봅니다. 데이터베이스 마이그레이션 도구를 사용하여 데이터베이스 마이그레이션 전에 호환성을 테스트합니다. |
| 132 | [Azure SQL 데이터베이스 복사](sql-database-copy.md) | Azure SQL 데이터베이스 사본 만들기 |
| 133 | [Azure 포털을 사용하여 Azure SQL 데이터베이스를 BACPAC 파일에 보관](sql-database-export.md) | Azure Portal을 사용하여 Azure SQL 데이터베이스를 BACPAC 파일에 보관 |



## <a name="security"></a>보안

| &nbsp; | 제목 | 설명 |
| --: | :-- | :-- |
| 134 | [Azure Active Directory 인증을 사용하여 SQL 데이터베이스 또는 SQL 데이터 웨어하우스에 연결](sql-database-aad-authentication.md) | Azure Active Directory 인증을 사용하여 SQL 데이터베이스에 연결하는 방법을 확인합니다. |
| 135 | [상시 암호화 - SQL 데이터베이스의 중요한 데이터 보호 및 Windows 인증서 저장소에 암호화 키 저장](sql-database-always-encrypted.md) | 몇 분 만에 SQL 데이터베이스의 중요한 데이터를 보호합니다. |
| 136 | [상시 암호화 - SQL 데이터베이스의 중요한 데이터 보호 및 Azure 주요 자격 증명 모음에 암호화 키 저장](sql-database-always-encrypted-azure-key-vault.md) | 몇 분 만에 SQL 데이터베이스의 중요한 데이터를 보호합니다. |
| 137 | [SQL Database - 하위 클라이언트 지원 및 감사에 대한 IP 끝점 변경](sql-database-auditing-and-dynamic-data-masking-downlevel-clients.md) | SQL 데이터베이스 하위 클라이언트 지원 및 감사에 대한 IP 끝점 변경에 대해 알아봅니다. |
| 138 | [PowerShell을 사용하여 Azure SQL 데이터베이스 서버 수준 방화벽 규칙 구성](sql-database-configure-firewall-settings-powershell.md) | Azure SQL 데이터베이스에 액세스하는 IP 주소에 대한 방화벽을 구성하는 방법을 알아봅니다. |
| 139 | [REST API를 사용하여 Azure SQL 데이터베이스 서버 수준 방화벽 규칙 구성](sql-database-configure-firewall-settings-rest.md) | Azure SQL 데이터베이스에 액세스하는 IP 주소에 대한 방화벽을 구성하는 방법을 알아봅니다. |
| 140 | [T-SQL을 사용하여 Azure SQL 데이터베이스 서버 수준 및 데이터베이스 수준 방화벽 규칙 구성](sql-database-configure-firewall-settings-tsql.md) | Azure SQL 데이터베이스에 액세스하는 IP 주소에 대한 방화벽을 구성하는 방법을 알아봅니다. |
| 141 | [SQL 데이터베이스 동적 데이터 마스킹 시작(Azure 포털)](sql-database-dynamic-data-masking-get-started.md) | Azure 포털에서 SQL 데이터베이스 동적 데이터 마스킹을 시작하는 방법 |
| 142 | [SQL 데이터베이스 동적 데이터 마스킹 시작(Azure 클래식 포털)](sql-database-dynamic-data-masking-get-started-portal.md) | Azure 클래식 포털에서 SQL 데이터베이스 동적 데이터 마스킹을 시작하는 방법 |
| 143 | [Azure SQL Database 방화벽 규칙 구성 \- 개요](sql-database-firewall-configure.md) | 서버 수준 및 데이터베이스 수준 방화벽 규칙으로 SQL 데이터베이스 방화벽을 구성하여 액세스를 관리하는 방법에 대해 알아봅니다. |
| 144 | [SQL 데이터베이스 자습서 - 데이터베이스에 액세스하고 관리하도록 SQL 데이터베이스 사용자 계정 만들기](sql-database-get-started-security.md) | 데이터베이스에 액세스하고 관리하기 위해 사용자 계정을 만드는 방법에 대해 알아봅니다. |
| 145 | [SQL 데이터베이스 인증 및 권한 부여: 액세스 부여](sql-database-manage-logins.md) | SQL 데이터베이스 보안 관리, 특히 서버 수준 보안 주체 계정을 통해 데이터베이스 액세스 및 로그인 보안을 관리하는 방법에 대해 알아봅니다. |
| 146 | [Azure SQL 데이터베이스 보안 지침 및 제한 사항](sql-database-security-guidelines.md) | Microsoft Azure SQL 데이터베이스 지침 및 보안과 관련된 제한 사항을 알아봅니다. |
| 147 | [SQL 데이터베이스 위협 감지 시작](sql-database-threat-detection-get-started.md) | Azure 포털에서 SQL 데이터베이스 위협 감지를 시작하는 방법 |
| 148 | [Azure SQL 데이터베이스에서 관리자 암호 재설정과 같은 일반 관리 작업을 수행하는 방법](sql-database-troubleshoot-permissions.md) | SQL 데이터베이스의 일반 관리 작업을 수행하는 방법을 설명합니다. 예: 관리자 암호 재설정, 액세스 부여 및 제거. |



## <a name="manage-your-database"></a>데이터베이스 관리

| &nbsp; | 제목 | 설명 |
| --: | :-- | :-- |
| 149 | [SQL 데이터베이스 감사 시작](sql-database-auditing-get-started.md) | SQL 데이터베이스 감사 시작 |
| 150 | [Azure 포털을 사용하여 Azure SQL 데이터베이스 서버 수준 방화벽 규칙 구성](sql-database-configure-firewall-settings.md) | Azure SQL 서버에 액세스하는 IP 주소에 방화벽을 구성하는 방법을 알아봅니다. |
| 151 | [Azure 포털을 사용하여 Azure SQL 데이터베이스 복사](sql-database-copy-portal.md) | Azure SQL 데이터베이스 사본 만들기 |
| 152 | [Azure 자동화를 사용하여 Azure SQL 데이터베이스 관리](sql-database-manage-automation.md) | Azure 자동화 서비스를 사용하여 대규모 Azure SQL 데이터베이스를 관리하는 방법을 알아봅니다. |
| 153 | [개요: SQL 데이터베이스 관리 도구](sql-database-manage-overview.md) | Azure SQL 데이터베이스 관리용 도구 및 옵션 비교 |
| 154 | [동적 관리 뷰를 사용하여 Azure SQL 데이터베이스 모니터링](sql-database-monitoring-with-dmvs.md) | 동적 관리 뷰를 사용하여 Microsoft Azure SQL 데이터베이스를 모니터링하여 일반적인 성능 문제를 감지 및 진단하는 방법에 대해 알아봅니다. |
| 155 | [SQL 데이터베이스 보안 설정](sql-database-security.md) | 인증, 권한 부여, 연결 보안, 암호화 및 규정 준수와 관련하여 클라우드 및 SQL Server 온-프레미스 간의 차이점을 포함하여 Azure SQL 데이터베이스 및 SQL Server 보안에 대해 알아봅니다. |



## <a name="extended-events"></a>확장 이벤트

| &nbsp; | 제목 | 설명 |
| --: | :-- | :-- |
| 156 | [SQL 데이터베이스의 확장 이벤트에 대한 이벤트 파일 대상 코드](sql-database-xevent-code-event-file.md) | Azure SQL 데이터베이스에서 확장 이벤트의 이벤트 파일 대상을 보여주는 2단계 코드 샘플에 대해 PowerShell 및 Transact-SQL을 제공합니다. Azure 저장소는 이 시나리오의 필수 부분입니다. |
| 157 | [SQL 데이터베이스의 확장 이벤트에 대한 링 버퍼 대상 코드](sql-database-xevent-code-ring-buffer.md) | Azure SQL 데이터베이스에서 링 버퍼 대상을 사용하여 편리하고 빨라진 Transact-SQL 코드 샘플을 제공합니다. |
| 158 | [SQL 데이터베이스의 확장 이벤트](sql-database-xevent-db-diff-from-svr.md) | Azure SQL 데이터베이스의 확장 이벤트(XEvent)와 Microsoft SQL Server의 이벤트 세션 간 차이점에 대해 설명합니다. |



## <a name="geo-replication"></a>지역에서 복제

| &nbsp; | 제목 | 설명 |
| --: | :-- | :-- |
| 159 | [Azure 포털에서 Azure SQL 데이터베이스에 대해 계획 또는 계획되지 않은 장애 조치 시작](sql-database-geo-replication-failover-portal.md) | Azure 포털을 사용하여 Azure SQL 데이터베이스에 대해 계획 또는 계획되지 않은 장애 조치 시작 |
| 160 | [PowerShell로 Azure SQL 데이터베이스에 대해 계획 또는 계획되지 않은 장애 조치 시작](sql-database-geo-replication-failover-powershell.md) | PowerShell을 사용하여 Azure SQL 데이터베이스에 대해 계획 또는 계획되지 않은 장애 조치 시작 |
| 161 | [Transact-SQL로 Azure SQL 데이터베이스에 대해 계획 또는 계획되지 않은 장애 조치 시작](sql-database-geo-replication-failover-transact-sql.md) | Transact-SQL을 사용하여 Azure SQL 데이터베이스에 대해 계획 또는 계획되지 않은 장애 조치 시작 |
| 162 | [개요: SQL 데이터베이스 활성 지역 복제](sql-database-geo-replication-overview.md) | 활성 지역 복제를 사용하면 Azure 데이터 센터 중에서 데이터베이스의 복제본을 4개 설정할 수 있습니다. |
| 163 | [Azure 포털로 Azure SQL 데이터베이스에 대한 지역에서 복제 구성](sql-database-geo-replication-portal.md) | Azure 포털을 사용하여 Azure SQL 데이터베이스에 대한 지역에서 복제 구성 |
| 164 | [PowerShell로 Azure SQL 데이터베이스에 대한 지역에서 복제 구성](sql-database-geo-replication-powershell.md) | PowerShell을 사용하여 Azure SQL 데이터베이스에 대한 활성 지역 복제 구성 |
| 165 | [재해 복구 후에 Azure SQL 데이터베이스 보안을 관리하는 방법](sql-database-geo-replication-security-config.md) | 이 항목에서는 데이터베이스 복원 또는 장애 조치(failover) 후 보안을 관리하기 위한 보안 고려 사항에 대해 설명합니다. |
| 166 | [TRANSACT-SQL로 Azure SQL 데이터베이스에 대한 지역에서 복제 구성](sql-database-geo-replication-transact-sql.md) | TRANSACT-SQL을 사용하여 Azure SQL 데이터베이스에 대한 지역에서 복제 구성 |
| 167 | [Azure 포털을 사용하여 지역 중복 백업에서 Azure SQL 데이터베이스 지역 복원](sql-database-geo-restore-portal.md) | 지역 중복 백업에서 Azure SQL 데이터베이스 지역 복원(Azure 포털) |
| 168 | [PowerShell을 사용하여 지역 중복 백업에서 Azure SQL 데이터베이스 복원](sql-database-geo-restore-powershell.md) | 지역 중복 백업에서 새 서버로 Azure SQL 데이터베이스를 복원합니다. |



## <a name="upgrade"></a>업그레이드

| &nbsp; | 제목 | 설명 |
| --: | :-- | :-- |
| 169 | [Azure SQL 데이터베이스의 호환성 수준 130을 통해 개선된 쿼리 성능](sql-database-compatibility-level-query-performance-130.md) | Azure SQL 데이터베이스 또는 Microsoft SQL Server의 데이터베이스에 적합한 호환성 수준을 판단하는 단계와 도구 |
| 170 | [SQL 데이터베이스 활성 지역 복제를 사용하여 클라우드 응용 프로그램의 롤링 업그레이드 관리](sql-database-manage-application-rolling-upgrade.md) | Azure SQL 데이터베이스 지역에서 복제를 사용하여 클라우드 응용 프로그램의 온라인 업그레이드를 지원하는 방법을 알아봅니다. |
| 171 | [Azure 포털을 사용하여 Azure SQL 데이터베이스 V12로 업그레이드](sql-database-upgrade-server-portal.md) | Web 및 Business 데이터베이스를 업그레이드하는 방법을 포함하여 Azure SQL 데이터베이스 V12로 업그레이드하는 방법을 설명하며 Azure 포털을 사용하여 V11 서버 업그레이드를 통해 데이터베이스를 탄력적 데이터베이스 풀로 직접 마이그레이션하는 방법을 설명합니다. |
| 172 | [PowerShell을 사용하여 Azure SQL 데이터베이스 V12로 업그레이드](sql-database-upgrade-server-powershell.md) | Web 및 Business 데이터베이스를 업그레이드하는 방법을 포함하여 Azure SQL 데이터베이스 V12로 업그레이드하는 방법을 설명하며 PowerShell을 사용하여 V11 서버 업그레이드를 통해 데이터베이스를 탄력적 데이터베이스 풀로 직접 마이그레이션하는 방법을 설명합니다. |
| 173 | [SQL 데이터베이스 V12로 업그레이드 계획 및 준비](sql-database-v12-plan-prepare-upgrade.md) | 이 항목에서는 Azure SQL 데이터베이스 버전 V12로 업그레이드하는 과정의 준비 및 제한 사항에 대해 설명합니다. |
| 174 | [SQL 데이터베이스 V12의 새로운 기능](sql-database-v12-whats-new.md) | 클라우드에서 Azure SQL 데이터베이스를 사용하는 비즈니스 시스템이 이제 V12 버전으로 업그레이드하면 이익이 되는지 이유를 설명합니다. |
| 175 | [Web 및 Business 버전 지원 종료 FAQ](sql-database-web-business-sunset-faq.md) | Azure SQL Web 및 Business 데이터베이스가 사용되지 않는 시기를 확인하고 새 서비스 계층의 특징 및 기능에 대해 알아보십시오. |



## <a name="tools"></a>도구

| &nbsp; | 제목 | 설명 |
| --: | :-- | :-- |
| 176 | [PowerShell을 사용하여 Azure SQL 데이터베이스 관리](sql-database-command-line-tools.md) | PowerShell을 사용한 Azure SQL 데이터베이스 관리. |
| 177 | [SQL 데이터베이스 자습서: Azure SQL 데이터베이스에 Excel 연결 및 보고서 만들기](sql-database-connect-excel.md) | Microsoft Excel을 클라우드의 Azure SQL 데이터베이스에 연결하는 방법을 알아봅니다. 보고 및 데이터 탐색을 위해 Excel로 데이터를 가져옵니다. |
| 178 | [PowerShell cmdlet을 사용하여 SQL 데이터베이스 만들기 및 일반적인 데이터베이스 설치 작업 수행하기](sql-database-get-started-powershell.md) | PowerShell을 사용하여 SQL 데이터베이스를 만드는 방법에 대해 알아봅니다. PowerShell cmdlet을 통해 일반적인 데이터베이스 설치 작업을 관리할 수 있습니다. |
| 179 | [Azure SQL 데이터 동기화 시작(미리 보기)](sql-database-get-started-sql-data-sync.md) | 이 자습서는 Azure SQL 데이터 동기화를 시작하도록 도와줍니다. |
| 180 | [SQL Server Management Studio를 사용하여 Azure SQL 데이터베이스 관리](sql-database-manage-azure-ssms.md) | SQL Server Management Studio를 사용하여 SQL 데이터베이스 서버 및 데이터베이스를 관리하는 방법에 대해 알아봅니다. |
| 181 | [Azure 포털을 사용하여 Azure SQL 데이터베이스 관리](sql-database-manage-portal.md) | Azure 포털을 사용하여 클라우드의 관계형 데이터베이스를 관리하는 방법을 알아보세요. |
| 182 | [PowerShell을 사용하여 SQL 데이터베이스의 서비스 계층 및 성능 수준(가격 책정 계층) 변경](sql-database-scale-up-powershell.md) | Azure SQL 데이터베이스의 서비스 계층 및 성능 수준 변경에서는 PowerShell을 사용하여 SQL 데이터베이스 규모 확장 또는 축소 방법을 보여 줍니다. PowerShell을 사용하여 Azure SQL 데이터베이스의 가격 책정 계층 변경. |
| 183 | [Azure RemoteApp에서 SQL Server Management Studio를 사용하여Azure SQL 데이터베이스에 연결](sql-database-ssms-remoteapp.md) | 이 자습서에서는 SQL 데이터베이스에 연결할 때 보안 및 성능을 위해 Azure RemoteApp에서 SQL Server Management Studio를 사용하는 방법을 알아봅니다. |



## <a name="reference"></a>참조

| &nbsp; | 제목 | 설명 |
| --: | :-- | :-- |
| 184 | [SQL 데이터베이스 및 SQL Server용 연결 라이브러리](sql-database-libraries.md) | 클라이언트 프로그램에서 Azure SQL 데이터베이스 또는 Microsoft SQL Server에 연결하는 데 사용할 수 있는 각 드라이버의 최소 버전 번호를 나열합니다. Microsoft가 아니라 커뮤니티에서 릴리즈된 드라이버에 대한 버전 정보 링크가 제공됩니다. |
| 185 | [Azure SQL 데이터베이스 리소스 제한](sql-database-resource-limits.md) | 이 페이지에서는 Azure SQL 데이터베이스에 대한 몇 가지 일반적인 리소스 제한을 설명합니다. |
| 186 | [Azure SQL 데이터베이스 TRANSACT-SQL의 차이점](sql-database-transact-sql-information.md) | Azure SQL 데이터베이스에서 완전히 지원되지 않는 TRANSACT-SQL 문 |



## <a name="miscellaneous"></a>기타

| &nbsp; | 제목 | 설명 |
| --: | :-- | :-- |
| 187 | [PowerShell을 사용하여 Azure SQL 데이터베이스 복사](sql-database-copy-powershell.md) | PowerShell을 사용하여 Azure SQL 데이터베이스 사본 만들기 |
| 188 | [Transact-SQL을 사용하여 Azure SQL 데이터베이스 복사](sql-database-copy-transact-sql.md) | Transact-SQL을 사용하여 Azure SQL 데이터베이스 사본 만들기 |
| 189 | [Azure SQL 데이터베이스 일반 제한 사항 및 지침](sql-database-general-limitations.md) | 이 페이지는 Azure SQL 데이터베이스의 일반 제한 사항 일부와 상호 운용성 및 지원 영역을 설명합니다. |
| 190 | [SQL 데이터베이스 가격 책정 계층 권장 사항](sql-database-service-tier-advisor.md) | Azure 포털에서 가격 책정 계층을 변경할 때 기존 Azure SQL 데이터베이스의 작업을 실행하는 데 가장 적합한 계층을 추천해 주는 가격 책정 계층 권장 사항이 제공됩니다. 가격 책정 계층은 SQL 데이터베이스의 서비스 계층 및 성능 수준을 설명합니다. |


&nbsp;

<hr/>

<a name="extras_append"></a>

## <a name="extras"></a>추가 항목

<a name="extra_related_articles"></a>

### <a name="related-articles-from-other-azure-services"></a>다른 Azure 서비스의 관련 문서

- [Azure에서 Azure 앱 서비스 앱 백업](../app-service-web/web-sites-backup.md)

<a name="extra_documentation_tools"></a>

### <a name="documentation-tools"></a>설명서 도구

- [Azure SQL 데이터베이스에 대해 발표된 업데이트](http://azure.microsoft.com/updates/?service=sql-database)

- [필터를 사용하여 Microsoft Azure의 모든 설명서 유형을 검색합니다](http://azure.microsoft.com/search/documentation/)

<a name="extra_learning_path_graphics"></a>

### <a name="learning-path-graphics"></a>학습 경로 그래픽

- [모든 Microsoft Azure 서비스에 대한 학습 경로 그래픽](http://azure.microsoft.com/documentation/learning-paths/)

- [학습 경로: Azure SQL 데이터베이스 알아보기](http://azure.microsoft.com/documentation/learning-paths/sql-database-training-learn-sql-database/)

- [학습 경로: SQL 데이터베이스 탄력적인 확장](http://azure.microsoft.com/documentation/learning-paths/sql-database-elastic-scale/)


<!--
AzIxAA.ExtraAppend.sql-database.txt
C:\_MainW\VS15\AzureIndexAllArticles\AzureIndexAllArticles\In-Out\In\

This bullet link is improperly disallowed by publishing automation due to presence of string 'docuXXmentation/arXXticles':
- [Search SQL Database documentation, with filters](http://azure.microsoft.com/docuXXmentation/arXXticles/?service=sql-database)
-->





<!--HONumber=Oct16_HO2-->


