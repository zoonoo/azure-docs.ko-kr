<properties 
    pageTitle="PowerShell을 사용하여 Azure SQL 데이터베이스의 서비스 계층 및 성능 수준 변경" 
    description="Azure SQL 데이터베이스의 서비스 계층 및 성능 수준 변경에서는 PowerShell을 사용하여 SQL 데이터베이스 규모 확장 또는 축소 방법을 보여 줍니다. PowerShell을 사용하여 Azure SQL 데이터베이스의 가격 책정 계층 변경." 
	services="sql-database"
	documentationCenter=""
	authors="stevestein"
	manager="jeffreyg"
	editor=""/>

<tags
	ms.service="sql-database"
	ms.devlang="NA"
	ms.date="09/10/2015"
	ms.author="sstein"
	ms.workload="data-management"
	ms.topic="article"
	ms.tgt_pltfrm="NA"/>


# PowerShell을 사용하여 SQL 데이터베이스의 서비스 계층 및 성능 수준(가격 책정 계층) 변경

**단일 데이터베이스**

> [AZURE.SELECTOR]
- [Azure Preview Portal](sql-database-scale-up.md)
- [PowerShell](sql-database-scale-up-powershell.md)


이 문서에서는 PowerShell을 사용하여 SQL 데이터베이스의 서비스 계층 및 성능 수준을 변경하는 방법을 보여 줍니다.

[SQL 데이터베이스 Web/Business 데이터베이스를 새 서비스 계층으로 업그레이드](sql-database-upgrade-new-service-tiers.md) 및 [Azure SQL 데이터베이스 서비스 계층 및 성능 수준](sql-database-service-tiers.md)의 정보를 사용하여 Azure SQL 데이터베이스에 대해 적절한 서비스 계층 및 성능 수준을 결정합니다.

> [AZURE.IMPORTANT]SQL 데이터베이스의 서비스 계층 및 성능 수준 변경은 온라인 작업입니다. 즉, 데이터베이스가 온라인 상태이 고 전체 작업 중에 가동 중지 시간 없이 사용할 수 있습니다.

- 데이터베이스를 다운그레이드하려면 데이터베이스가 대상 서비스 계층의 최대 허용 크기보다 작아야 합니다. 
- [표준 지역에서 복제](https://msdn.microsoft.com/library/azure/dn758204.aspx) 또는 [활성 지역 복제](https://msdn.microsoft.com/library/azure/dn741339.aspx)를 사용할 수 있는 데이터베이스를 업그레이드하는 경우에는 주 데이터베이스를 업그레이드하기 전에 먼저 해당 보조 데이터베이스를 원하는 성능 계층으로 업그레이드해야 합니다.
- 프리미엄 서비스 계층에서 다운그레이드하는 경우는 먼저 모든 지역에서 복제 관계를 종료해야 합니다. [연속 복사 관계 종료](https://msdn.microsoft.com/library/azure/dn741323.aspx) 항목에 설명된 단계에 따라 주 데이터베이스와 활성 보조 데이터베이스 간의 복제 프로세스를 중지할 수 있습니다.
- 복원 서비스는 여러 서비스 계층에서 서로 다르게 제공됩니다. 다운그레이드하는 경우 지정 시간으로 복원하는 기능을 잃게 되거나 백업 보존 기간이 단축될 수 있습니다. 자세한 내용은 [Azure SQL 데이터베이스 백업 및 복원](https://msdn.microsoft.com/library/azure/jj650016.aspx)을 참조하세요.
- 24시간 내에 최대 4개의 개별 데이터베이스 변경(서비스 계층 또는 성능 수준)을 수행할 수 있습니다.
- 데이터베이스의 새로운 속성은 변경이 완료될 때까지 적용되지 않습니다.



**이 문서를 완료하려면 다음이 필요합니다.**

- Azure 구독. Azure 구독이 필요할 경우 이 페이지 위쪽에서 **무료 평가판**을 클릭하고 되돌아와 이 문서를 완료합니다.
- Azure SQL 데이터베이스입니다. SQL 데이터베이스가 없는 경우 [첫 Azure SQL 데이터베이스 만들기](sql-database-get-started.md) 문서의 단계에 따라 만듭니다.
- Azure PowerShell. [Microsoft 웹 플랫폼 설치 관리자](http://go.microsoft.com/fwlink/p/?linkid=320376&clcid=0x409)를 실행하여 Azure PowerShell 모듈을 다운로드하고 설치할 수 있습니다. 자세한 내용은 [Azure PowerShell을 설치 및 구성하는 방법](powershell-install-configure.md)을 참조하세요.

Azure SQL 데이터베이스의 서비스 계층을 변경하는 Cmdlet은 Azure 리소스 관리자 모듈에 있습니다. Azure PowerShell을 시작하면 Azure 모듈에 있는 cmdlet을 기본적으로 가져옵니다. Azure 리소스 관리자 모듈로 전환하려면 Switch-AzureMode cmdlet을 사용합니다.

	Switch-AzureMode -Name AzureResourceManager

**Switch-azuremode**를 실행하고 경고: *Switch-AzureMode cmdlet은 더 이상 사용되지 않으며 향후 릴리스에서 제거될 예정입니다.*가 표시되는 경우 다음 단계로 이동하여 자격 증명을 구성합니다.

자세한 내용은 [리소스 관리자에서 Windows PowerShell 사용](powershell-azure-resource-manager.md)을 참조하세요.

## 자격 증명 구성 및 구독 선택

먼저 Azure 계정에 액세스 권한을 설정해야 하므로 PowerShell을 시작하고 다음 cmdlet을 실행합니다. 로그인 화면에서 Azure 포털에 로그인할 때 사용한 것과 동일한 메일과 암호를 입력합니다.

	Add-AzureAccount

로그인에 성공하면 액세스 권한이 있는 Azure 구독으로 로그인한 ID를 포함한 일부 정보가 화면에 표시됩니다.


### Azure 구독 선택

구독을 선택하려면 구독 ID 또는 구독 이름(**-SubscriptionName**)이 필요합니다. 이전 단계에 표시된 정보에서 구독 ID를 복사하거나, 구독이 여러 개이고 세부 정보가 필요한 경우 **Get-AzureSubscription** cmdlet을 실행하고 결과 집합에서 원하는 구독 정보를 복사할 수 있습니다. 구독을 설정한 후 다음 cmdlet을 실행합니다.

	$SubscriptionId = "4cac86b0-1e56-bbbb-aaaa-000000000000"
    Select-AzureSubscription -SubscriptionId $SubscriptionId

성공적으로 **Select-azuresubscription**을 실행한 후 PowerShell 프롬프트로 돌아갑니다. 둘 이상의 구독이 있는 경우 **Get-azuresubscription**을 실행하고 **IsCurrent: True** 표시를 사용하려는 구독을 확인할 수 있습니다.


 


## SQL 데이터베이스의 서비스 계층 및 성능 수준 변경

**Set-AzureSqlDatabase** Cmdlet을 실행하고 **-RequestedServiceObjectiveName**을 원하는 가격 책정 계층의 성능 수준(예를 들어 *S0*, *S1*, *S2*, *S3*, *P1*, *P2*, ...)으로 설정합니다.

    $ResourceGroupName = "resourceGroupName"
    
    $ServerName = "serverName"
    $DatabaseName = "databaseName"

    $NewEdition = "Standard"
    $NewPricingTier = "S2"

    $ScaleRequest = Set-AzureSqlDatabase -DatabaseName $DatabaseName -ServerName $ServerName -ResourceGroupName $ResourceGroupName -Edition $NewEdition -RequestedServiceObjectiveName $NewPricingTier


  

   


## SQL 데이터베이스의 서비스 계층 및 성능 수준을 변경하는 샘플 PowerShell 스크립트

    
	Switch-AzureMode -Name AzureResourceManager
    
    $SubscriptionId = "4cac86b0-1e56-bbbb-aaaa-000000000000"
    
    $ResourceGroupName = "resourceGroupName"
    $Location = "Japan West"
    
    $ServerName = "serverName"
    $DatabaseName = "databaseName"
    
    $NewEdition = "Standard"
    $NewPricingTier = "S2"
    
    Add-AzureAccount
    Select-AzureSubscription -SubscriptionId $SubscriptionId
    
    $ScaleRequest = Set-AzureSqlDatabase -DatabaseName $DatabaseName -ServerName $ServerName -ResourceGroupName $ResourceGroupName -Edition $NewEdition -RequestedServiceObjectiveName $NewPricingTier
    
    $ScaleRequest
    
        


## 다음 단계

- [규모 확장 및 감축](sql-database-elastic-scale-get-started.md)
- [SSMS를 사용하여 SQL 데이터베이스에 연결 및 쿼리하기](sql-database-connect-query-ssms.md)
- [Azure SQL 데이터베이스 내보내기](sql-database-export-powershell.md)

## 추가 리소스

- [비즈니스 연속성 개요](sql-database-business-continuity.md)
- [SQL 데이터베이스 설명서](https://azure.microsoft.com/documentation/services/sql-database/)

<!---HONumber=Oct15_HO1-->