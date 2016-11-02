<properties
    pageTitle="PowerShell을 사용하여 새 탄력적 데이터베이스 풀 만들기 | Microsoft Azure"
    description="PowerShell을 사용하여 여러 데이터베이스를 관리하도록 확장성 있는 탄력적 데이터베이스 풀을 만들어 Azure SQL 데이터베이스 리소스를 규모 확장하는 방법에 대해 알아봅니다."
	services="sql-database"
    documentationCenter=""
    authors="srinia"
    manager="jhubbard"
    editor=""/>

<tags
    ms.service="sql-database"
    ms.devlang="NA"
    ms.topic="get-started-article"
    ms.tgt_pltfrm="powershell"
    ms.workload="data-management"
    ms.date="05/27/2016"
    ms.author="srinia"/>

# PowerShell을 사용하여 새 탄력적 데이터베이스 풀 만들기

> [AZURE.SELECTOR]
- [Azure 포털](sql-database-elastic-pool-create-portal.md)
- [PowerShell](sql-database-elastic-pool-create-powershell.md)
- [C#](sql-database-elastic-pool-create-csharp.md)


PowerShell cmdlet을 사용하여 [탄력적 데이터베이스 풀](sql-database-elastic-pool.md)을 만드는 방법을 알아봅니다.

일반적인 오류 코드는 [SQL 데이터베이스 클라이언트 응용 프로그램의 SQL 오류 코드: 데이터베이스 연결 오류 및 기타 문제](sql-database-develop-error-messages.md)를 참조하세요.

> [AZURE.NOTE] 탄력적 풀은 현재 미리 보기 상태인 미국 중북부 및 인도 서부를 제외한 모든 Azure 지역에서 일반 공급(GA) 상태입니다. 이 지역에서도 탄력적 풀의 GA를 가능한 한 빨리 제공하겠습니다. 또한 탄력적 풀은 현재 [메모리 내 OLTP 또는 메모리 내 분석](sql-database-in-memory.md)을 사용하는 데이터베이스를 지원하지 않습니다.


Azure PowerShell 1.0 이상을 실행해야 합니다. 자세한 내용은 [Azure PowerShell을 설치 및 구성하는 방법](../powershell-install-configure.md)을 참조하세요.

## 새 풀 만들기

[New-AzureRmSqlElasticPool](https://msdn.microsoft.com/library/azure/mt619378.aspx) cmdlet은 새 풀을 만듭니다. 풀, 최소 및 최대 Dtu당 eDTU 값은 서비스 계층 값(기본, 표준 또는 프리미엄)에 의해 제한됩니다. [탄력적 풀 및 탄력적 데이터베이스에 대한 eDTU 및 저장소 제한](sql-database-elastic-pool.md#eDTU-and-storage-limits-for-elastic-pools-and-elastic-databases)을 참조하세요.

	New-AzureRmSqlElasticPool -ResourceGroupName "resourcegroup1" -ServerName "server1" -ElasticPoolName "elasticpool1" -Edition "Standard" -Dtu 400 -DatabaseDtuMin 10 -DatabaseDtuMax 100


## 풀에 새 탄력적 데이터베이스 만들기

[New-AzureRmSqlDatabase](https://msdn.microsoft.com/library/azure/mt619339.aspx) cmdlet을 사용하고 **ElasticPoolName** 매개 변수를 대상 풀로 설정합니다. 기존 데이터베이스를 풀으로 이동하려면 [데이터베이스를 탄력적 풀로 이동](sql-database-elastic-pool-manage-powershell.md#Move-a-database-into-an-elastic-pool)을 참조하세요.

	New-AzureRmSqlDatabase -ResourceGroupName "resourcegroup1" -ServerName "server1" -DatabaseName "database1" -ElasticPoolName "elasticpool1"

## 풀을 만들고 여러 새 데이터베이스로 채웁니다. 

풀에 많은 수의 데이터베이스를 만드는 작업은 한 번에 단일 데이터베이스만을 만들 수 있는 포털 또는 PowerShell cmdlet을 사용하는 경우와 같은 시간이 걸릴 수 있습니다. 새 풀로 만들기를 자동화하하려면 [CreateOrUpdateElasticPoolAndPopulate ](https://gist.github.com/billgib/d80c7687b17355d3c2ec8042323819ae)를 참조하세요.

## 예제: PowerShell을 사용하여 풀 만들기 

이 스크립트는 새 Azure 리소스 그룹 및 새 서버를 만듭니다. 대화 상자가 나타나면 (Azure 자격 증명 아닌) 새 서버에 대한 관리자 사용자 이름 및 암호를 제공합니다.

    $subscriptionId = '<your Azure subscription id>'
    $resourceGroupName = '<resource group name>'
    $location = '<datacenter location>'
    $serverName = '<server name>'
    $poolName = '<pool name>'
    $databaseName = '<database name>'

    Login-AzureRmAccount
    Set-AzureRmContext -SubscriptionId $subscriptionId

    New-AzureRmResourceGroup -Name $resourceGroupName -Location $location
    New-AzureRmSqlServer -ResourceGroupName $resourceGroupName -ServerName $serverName -Location $location -ServerVersion "12.0"
    New-AzureRmSqlServerFirewallRule -ResourceGroupName $resourceGroupName -ServerName $serverName -FirewallRuleName "rule1" -StartIpAddress "192.168.0.198" -EndIpAddress "192.168.0.199"

    New-AzureRmSqlElasticPool -ResourceGroupName $resourceGroupName -ServerName $serverName -ElasticPoolName $poolName -Edition "Standard" -Dtu 400 -DatabaseDtuMin 10 -DatabaseDtuMax 100

    New-AzureRmSqlDatabase -ResourceGroupName $resourceGroupName -ServerName $serverName -DatabaseName $databaseName -ElasticPoolName $poolName -MaxSizeBytes 10GB



## 다음 단계

- [풀 관리](sql-database-elastic-pool-manage-powershell.md)
- [탄력적 작업 만들기](sql-database-elastic-jobs-overview.md) 탄력적 작업은 풀의 데이터베이스 개수에 관계없이 T-SQL 스크립트를 실행할 수 있습니다.
- [Azure SQL 데이터베이스를 사용하여 규모 확장](sql-database-elastic-scale-introduction.md): 탄력적 데이터베이스 도구를 사용하여 확장합니다.

<!---HONumber=AcomDC_0907_2016-->