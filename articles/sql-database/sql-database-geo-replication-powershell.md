<properties 
    pageTitle="PowerShell을 사용하여 Azure SQL 데이터베이스에 대한 활성 지역 복제 구성 | Microsoft Azure" 
    description="PowerShell을 사용하여 Azure SQL 데이터베이스에 대한 지역에서 복제" 
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
    ms.date="04/27/2016"
    ms.author="sstein"/>

# PowerShell로 Azure SQL 데이터베이스에 대한 지역에서 복제 구성



> [AZURE.SELECTOR]
- [Azure 포털](sql-database-geo-replication-portal.md)
- [PowerShell](sql-database-geo-replication-powershell.md)
- [Transact-SQL](sql-database-geo-replication-transact-sql.md)


이 문서에서는 PowerShell을 사용하여 SQL 데이터베이스에 대한 지역에서 복제를 구성하는 방법을 보여 줍니다.

장애 조치(Failover)를 시작하려면 [Azure SQL 데이터베이스에 대해 계획 또는 계획되지 않은 장애 조치 시작](sql-database-geo-replication-failover-powershell.md)을 참조하세요.

>[AZURE.NOTE] 현재 활성 지역 복제(읽기 가능한 보조)는 모든 서비스 계층에 있는 모든 데이터베이스에 대해 사용 가능합니다. 2017년 4월, 읽을 수 없는 보조 유형은 사용 중지되며 기존의 읽을 수 없는 데이터베이스는 읽을 수 있는 보조로 자동으로 업그레이드됩니다.

동일하거나 다른 데이터 센터 위치(하위 지역)에 최대 4개의 읽기 기능한 보조 데이터베이스를 구성할 수 있습니다. 보조 데이터베이스를 데이터 센터 정전 또는 주 데이터베이스에 연결하지 못하는 경우 사용할 수 있습니다.

지역에서 복제를 구성하려면 다음이 필요합니다.

- Azure 구독. Azure 구독이 필요할 경우 이 페이지 위쪽에서 **무료 계정**을 클릭하고 되돌아와 이 문서를 완료합니다.
- Azure SQL 데이터베이스 - 다른 지역으로 복제하려는 주 데이터베이스입니다.
- Azure PowerShell 1.0 이상 [Azure PowerShell을 설치 및 구성하는 방법](../powershell-install-configure.md)에 따라 Azure PowerShell 모듈을 다운로드하여 설치할 수 있습니다.


## 자격 증명 구성 및 구독 선택

먼저 Azure 계정에 액세스 권한을 설정해야 하므로 PowerShell을 시작하고 다음 cmdlet을 실행합니다. 로그인 화면에서 Azure 포털에 로그인할 때 사용한 것과 동일한 메일과 암호를 입력합니다.


	Login-AzureRmAccount

로그인에 성공하면 액세스 권한이 있는 Azure 구독으로 로그인한 ID를 포함한 일부 정보가 화면에 표시됩니다.


### Azure 구독 선택

구독을 선택하려면 구독 ID가 필요합니다. 이전 단계에 표시된 정보에서 구독 ID를 복사하거나, 구독이 여러 개이고 세부 정보가 필요한 경우 **Get-AzureRmSubscription** cmdlet를 실행하고 결과 집합에서 원하는 구독 정보를 복사할 수 있습니다. 다음 cmdlet은 구독 ID를 사용하여 현재 구독을 설정합니다.

	Select-AzureRmSubscription -SubscriptionId 4cac86b0-1e56-bbbb-aaaa-000000000000

성공적으로 **Select-AzureRmSubscription**을 실행한 후 PowerShell 프롬프트로 돌아갑니다.


## 보조 데이터베이스 추가


다음 단계에서는 지역에서 복제 파트너 관계에 새 보조 데이터베이스를 만듭니다.
  
보조를 사용하려면 구독 소유자 또는 공동 소유자여야 합니다.

**New-AzureRmSqlDatabaseSecondary** cmdlet를 사용하여 파트너 서버의 보조 데이터베이스를 연결된 서버(주 데이터베이스)의 로컬 데이터베이스에 추가할 수 있습니다.

이 cmdlet는 **Start-AzureSqlDatabaseCopy**를 **–IsContinuous** 매개 변수로 바꿉니다. 다른 cmdlet에서 사용할 수 있는 **AzureRmSqlDatabaseSecondary** 개체를 출력하여 특정 복제 링크를 명확하게 식별합니다. 이 cmdlet은 보조 데이터베이스가 작성되고 완전히 시드되는 경우 반환합니다. 데이터베이스 크기에 따라 몇 분에서 몇 시간이 걸릴 수 있습니다.

보조 서버에서 복제된 데이터베이스는 주 서버의 데이터베이스와 동일한 이름을 포함하며 기본적으로 동일한 수준의 서비스입니다. 보조 데이터베이스는 읽을 수 있거나 읽을 수 없을 수 있으며 단일 데이터베이스 또는 탄력적 데이터베이스가 될 수 있습니다. 자세한 내용은 [New-AzureRMSqlDatabaseSecondary](https://msdn.microsoft.com/library/mt603689.aspx) 및 [서비스 계층](sql-database-service-tiers.md)을 참조하세요. 보조가 만들어지고 시드된 후 데이터는 주 데이터베이스에서 새로운 보조 데이터베이스로 복제되기 시작합니다. 아래 단계에서는 PowerShell을 사용하여 단일 데이터베이스 또는 탄력적 데이터베이스와 함께 읽을 수 있거나 읽을 수 없는 보조를 만들어 이 작업을 수행하는 방법을 설명합니다.

파트너 데이터베이스가 이미 있는 경우(예: 이전 지역에서 복제 관계를 종료한 결과) 명령이 실패합니다.



### 읽을 수 없는 보조(단일 데이터베이스) 추가

다음 명령은 리소스 그룹 "rg2"에서 서버 "srv2"의 데이터베이스 "mydb"에 대한 읽을 수 없는 보조를 만듭니다.

    $database = Get-AzureRmSqlDatabase –DatabaseName "mydb" -ResourceGroupName "rg1" -ServerName "srv1"
    $secondaryLink = $database | New-AzureRmSqlDatabaseSecondary –PartnerResourceGroupName "rg2" –PartnerServerName "srv2" -AllowConnections "No"



### 읽을 수 있는 보조(단일 데이터베이스) 추가

다음 명령은 리소스 그룹 "rg2"에서 서버 "srv2"의 데이터베이스 "mydb"에 대한 읽을 수 있는 보조를 만듭니다.

    $database = Get-AzureRmSqlDatabase –DatabaseName "mydb" -ResourceGroupName "rg1" -ServerName "srv1"
    $secondaryLink = $database | New-AzureRmSqlDatabaseSecondary –PartnerResourceGroupName "rg2" –PartnerServerName "srv2" -AllowConnections "All"




### 읽을 수 없는 보조(탄력적 데이터베이스) 추가

다음 명령은 리소스 그룹 "rg2"에서 서버 "srv2"의 탄력적 데이터베이스 풀 "ElasticPool1"에 있는 데이터베이스 "mydb"에 대한 읽을 수 없는 보조를 만듭니다.

    $database = Get-AzureRmSqlDatabase –DatabaseName "mydb" -ResourceGroupName "rg1" -ServerName "srv1"
    $secondaryLink = $database | New-AzureRmSqlDatabaseSecondary –PartnerResourceGroupName "rg2" –PartnerServerName "srv2" –SecondaryElasticPoolName "ElasticPool1" -AllowConnections "No"


### 읽을 수 있는 보조(탄력적 데이터베이스) 추가

다음 명령은 리소스 그룹 "rg2"에서 서버 "srv2"의 탄력적 데이터베이스 풀 "ElasticPool1"에 있는 데이터베이스 "mydb"에 대한 읽을 수 있는 보조를 만듭니다.

    $database = Get-AzureRmSqlDatabase –DatabaseName "mydb" -ResourceGroupName "rg1" -ServerName "srv1"
    $secondaryLink = $database | New-AzureRmSqlDatabaseSecondary –PartnerResourceGroupName "rg2" –PartnerServerName "srv2" –SecondaryElasticPoolName "ElasticPool1" -AllowConnections "All"





## 보조 데이터베이스 제거

**Remove-AzureRmSqlDatabaseSecondary** cmdlet를 사용하여 보조 데이터베이스와 주 데이터베이스 간의 복제 파트너 관계를 영구적으로 종료합니다. 관계가 종료된 후 보조 데이터베이스는 읽기-쓰기 데이터베이스가 됩니다. 보조 데이터베이스에 대한 연결이 끊어진 경우 명령이 성공하지만 연결이 복원된 후 보조는 읽기-쓰기가 됩니다. 자세한 내용은 [Remove-AzureRmSqlDatabaseSecondary](https://msdn.microsoft.com/library/mt603457.aspx) 및 [서비스 계층](sql-database-service-tiers.md)을 참조하세요.

이 cmdlet은 복제를 위한 Stop-AzureSqlDatabaseCopy를 대체합니다.

이와 같은 제거는 복제 링크를 제거하는 강제 종료와 동일하며, 종료하기 전에 완전히 복제되지 않은 이전의 보조를 독립 실행형 데이터베이스로 둡니다. 모든 링크 데이터가 이전의 주 데이터베이스와 이전의 보조 데이터베이스 모두에서 정리됩니다(있는 경우). 이 cmdlet은 복제 링크가 제거될 때 반환됩니다.


보조를 제거하려면 사용자는 RBAC에 따라 주 및 보조 데이터베이스에 대한 쓰기 권한이 있어야 합니다. 자세한 내용은 역할 기반 액세스 제어를 참조하세요.

다음은 리소스 그룹 "rg2"의 서버 "srv2"에 대한 데이터베이스 "mydb"의 복제 링크를 제거합니다.

    $database = Get-AzureRmSqlDatabase –DatabaseName "mydb" -ResourceGroupName "rg1" -ServerName "srv1"
    $secondaryLink = $database | Get-AzureRmSqlDatabaseReplicationLink –SecondaryResourceGroup "rg2" –PartnerServerName "srv2"
    $secondaryLink | Remove-AzureRmSqlDatabaseSecondary 


## 지역에서 복제 구성 및 상태 모니터링

모니터링 작업에는 지역에서 복제 구성의 모니터링 및 데이터 복제 상태 모니터링이 포함됩니다.

[Get-AzureRmSqlDatabaseReplicationLink](https://msdn.microsoft.com/library/mt619330.aspx)를 사용하여 sys.geo\_replication\_links 카탈로그 뷰에 표시되는 전달 복제 링크에 대한 정보를 검색할 수 있습니다.

다음 명령은 주 데이터베이스 "mydb" 및 리소스 그룹 "rg2"의 서버 "srv2"에 있는 보조 데이터베이스 간 복제 링크의 상태를 검색합니다.

    $database = Get-AzureRmSqlDatabase –DatabaseName "mydb" -ResourceGroupName "rg1" -ServerName "srv1"
    $secondaryLink = $database | Get-AzureRmSqlDatabaseReplicationLink –PartnerResourceGroup "rg2” –PartnerServerName "srv2”


  

## 다음 단계

- [Azure SQL 데이터베이스에 대해 계획 또는 계획되지 않은 장애 조치 시작](sql-database-geo-replication-failover-powershell.md)
- [재해 복구 연습](sql-database-disaster-recovery-drills.md)




## 추가 리소스

- [지역에서 복제를 위한 보안 구성](sql-database-geo-replication-security-config.md)
- [새 지역에서 복제 기능에 대한 주요 내용](https://azure.microsoft.com/blog/spotlight-on-new-capabilities-of-azure-sql-database-geo-replication/)
- [SQL 데이터베이스 BCDR FAQ](sql-database-bcdr-faq.md)
- [비즈니스 연속성 개요](sql-database-business-continuity.md)
- [활성 지역 복제](sql-database-geo-replication-overview.md)
- [클라우드 재해 복구를 위한 응용 프로그램 설계](sql-database-designing-cloud-solutions-for-disaster-recovery.md)
- [복구된 Azure SQL 데이터베이스 마무리](sql-database-recovered-finalize.md)

<!---HONumber=AcomDC_0504_2016-->