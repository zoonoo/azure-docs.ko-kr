<properties 
	pageTitle="PowerShell을 사용하여 Azure SQL Server를 V12로 업그레이드" 
	description="PowerShell을 사용하여 Azure SQL Server를 V12로 업그레이드합니다." 
	services="sql-database" 
	documentationCenter="" 
	authors="stevestein" 
	manager="jeffreyg" 
	editor=""/>

<tags 
	ms.service="sql-database" 
	ms.workload="data-management" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="09/05/2015" 
	ms.author="sstein"/>

# PowerShell을 사용하여 Azure SQL Server를 V12로 업그레이드
 

이 문서에서는 가격 책정 계층 및 탄력적 풀 권장 사항을 사용하여 SQL 데이터베이스 서버를 V12로 업그레이드하는 방법을 보여 줍니다.



## 필수 조건 

PowerShell을 사용하여 서버를 V12로 업그레이드하려면 Azure PowerShell이 설치 및 실행되고 있어야 하며, 버전에 따라 Azure 리소스 관리자 PowerShell Cmdlet에 액세스하기 위해 리소스 관리자 모드로 전환해야 할 수도 있습니다.

[Microsoft 웹 플랫폼 설치 관리자](http://go.microsoft.com/fwlink/p/?linkid=320376&clcid=0x409)를 실행하여 Azure PowerShell 모듈을 다운로드하고 설치할 수 있습니다. 자세한 내용은 [Azure PowerShell을 설치 및 구성하는 방법](../powershell-install-configure.md)을 참조하세요.

cmdlets를 만들고 관리할 수 있는 Azure SQL 데이터 베이스는 Azure 리소스 관리자 모듈에 있습니다. Azure PowerShell을 시작하면 Azure 모듈에 있는 cmdlet을 기본적으로 가져옵니다. Azure 리소스 관리자 모듈로 전환하려면 **Switch-azuremode** cmdlet을 사용합니다.

	Switch-AzureMode -Name AzureResourceManager

'The Switch-azuremode cmdlet는 더이상 사용 되지 않으며 향후 릴리스에서 제거 될 예정.' 라는 경고가 발생 하는 경우 무시할 수 있으며 다음 섹션으로 이동할 수 있습니다.

자세한 내용은 [리소스 관리자에서 Windows PowerShell 사용](../powershell-azure-resource-manager.md)을 참조하세요.



## 자격 증명을 구성합니다.

Azure 구독에 대한 PowerShell cmdlet을 실행 하려면 먼저 Azure 계정에 대한 액세스를 설정 해야 합니다. 다음을 실행하면 자격 증명을 입력할 수 있는 로그인 화면이 나타납니다. Azure 포털에 로그인할 때 사용한 것과 동일한 메일과 암호를 사용합니다.

	Add-AzureAccount

로그인에 성공하면 액세스 권한이 있는 Azure 구독으로 로그인한 ID를 포함한 일부 정보가 화면에 표시됩니다.


## Azure 구독 선택

사용하고 싶은 구독을 선택하려면 구독 ID(**-SubscriptionId**) 또는 구독 이름(**-SubscriptionName**)이 필요합니다. 이는 이전 단계에서 복사하거나, 또는 구독이 여러 개일 경우 **Get-AzureSubscription** cmdlet을 실행하고 결과 집합에서 원하는 구독 정보를 복사할 수 있습니다.

현재 구독을 설정 하 여 구독 정보로 다음 cmdlet을 실행 합니다.

	Select-AzureSubscription -SubscriptionId 4cac86b0-1e56-bbbb-aaaa-000000000000

다음 명령은 위에서 방금 선택한 구독을 실행합니다.

## 권장 사항 가져오기

서버 업그레이드에 대한 권장 사항을 가져오려면 다음 cmdlet을 실행합니다.

    $hint = Get-AzureSqlServerUpgradeHint -ResourceGroupName “resourcegroup1” -ServerName “server1” 

자세한 내용은 [Azure SQL 데이터베이스 탄력적 데이터베이스 풀 권장 사항](sql-database-elastic-pool-portal.md#elastic-database-pool-pricing-tier-recommendations) 및 [Azure SQL 데이터베이스 가격 책정 계층 권장 사항](sql-database-service-tier-advisor.md)을 참조하세요.



## 업그레이드 시작

서버 업그레이드를 시작하려면 다음 cmdlet을 실행합니다.

    Start-AzureSqlServerUpgrade -ResourceGroupName “resourcegroup1” -ServerName “server1” -ServerVersion 12.0 -DatabaseCollection $hint.Databases -ElasticPoolCollection $hint.ElasticPools  


이 명령을 실행하면 업그레이드 프로세스가 시작됩니다. 권장 사항의 출력을 사용자 지정한 다음 편집된 권장 사항을 이 cmdlet에 제공할 수 있습니다.


## Azure SQL Server 업그레이드


    # Adding the account
    #
    Add-AzureAccount
    
    # Switch mode
    #
    Switch-AzureMode -Name AzureResourceManager

    # Setting the variables
    #
    $SubscriptionName = 'YOUR_SUBSCRIPTION' 
    $ResourceGroupName = 'YOUR_RESOURCE_GROUP' 
    $ServerName = 'YOUR_SERVER' 
    
    # Selecting the right subscription 
    # 
    Select-AzureSubscription $SubscriptionName 
    
    # Getting the upgrade recommendations 
    #
    $hint = Get-AzureSqlServerUpgradeHint -ResourceGroupName $ResourceGroupName -ServerName $ServerName 
    
    # Starting the upgrade process 
    #
    Start-AzureSqlServerUpgrade -ResourceGroupName $ResourceGroupName -ServerName $ServerName -ServerVersion 12.0 -DatabaseCollection $hint.Databases -ElasticPoolCollection $hint.ElasticPools  


## 사용자 지정 업그레이드 매핑

권장 사항이 해당 서버 및 비즈니스 사례에 적합하지 않은 경우 데이터베이스 업그레이드 방법을 선택하고 단일 또는 탄력적 데이터베이스에 매핑할 수 있습니다.

데이터베이스를 탄력적 데이터베이스 풀로 업그레이드:

    $elasticPool = New-Object -TypeName Microsoft.Azure.Management.Sql.Models.UpgradeRecommendedElasticPoolProperties
    $elasticPool.DatabaseDtuMax = 100  
    $elasticPool.DatabaseDtuMin = 0  
    $elasticPool.Dtu = 800
    $elasticPool.Edition = "Standard"  
    $elasticPool.DatabaseCollection = ("DB1")  
    $elasticPool.Name = "elasticpool_1"  


데이터베이스를 단일 데이터베이스로 업그레이드:

    $databaseMap = New-Object -TypeName Microsoft.Azure.Management.Sql.Models.UpgradeDatabaseProperties  
    $databaseMap.Name = "DB2"
    $databaseMap.TargetEdition = "Standard"
    $databaseMap.TargetServiceLevelObjective = "S0"
    Start-AzureSqlServerUpgrade –ResourceGroupName resourcegroup1 –ServerName server1 -Version 12.0 -DatabaseCollection($databaseMap) -ElasticPoolCollection ($elasticPool)
    




## 관련 정보

- [Azure SQL 데이터베이스 리소스 관리자 Cmdlet](https://msdn.microsoft.com/library/mt163521.aspx)
- [Azure SQL 데이터베이스 서비스 관리자 Cmdlet](https://msdn.microsoft.com/library/dn546726.aspx)
 

<!---HONumber=Sept15_HO3-->