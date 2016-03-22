<properties 
    pageTitle="탄력적 데이터베이스 풀 만들기(PowerShell) | Microsoft Azure" 
    description="PowerShell을 사용하여 여러 데이터베이스를 관리하도록 탄력적 데이터베이스 풀을 만들어 Azure SQL 데이터베이스 리소스를 확장하는 방법에 대해 알아봅니다." 
	services="sql-database" 
    documentationCenter="" 
    authors="stevestein" 
    manager="jhubbard" 
    editor=""/>

<tags
    ms.service="sql-database"
    ms.devlang="NA"
    ms.topic="get-started-article"
    ms.tgt_pltfrm="powershell"
    ms.workload="data-management" 
    ms.date="03/15/2016"
    ms.author="sstein"/>

# 탄력적 데이터베이스 풀 만들기(PowerShell) 

> [AZURE.SELECTOR]
- [Azure 포털](sql-database-elastic-pool-create-portal.md)
- [PowerShell](sql-database-elastic-pool-create-powershell.md)
- [C#](sql-database-elastic-pool-create-csharp.md)


PowerShell cmdlet을 사용하여 [탄력적 데이터베이스 풀](sql-database-elastic-pool.md)을 만드는 방법을 알아봅니다.

> [AZURE.NOTE] 탄력적 데이터베이스 풀은 현재 미리 보기 상태이며, SQL 데이터베이스 V12 서버에서만 사용할 수 있습니다. SQL 데이터베이스 V11 서버가 있는 경우 한 단계에서 [PowerShell을 사용하여 V12로 업그레이드 및 풀 만들기](sql-database-upgrade-server-portal.md)를 할 수 있습니다.


Azure PowerShell 1.0 이상을 실행해야 합니다. 자세한 내용은 [Azure PowerShell을 설치 및 구성하는 방법](../powershell-install-configure.md)을 참조하세요.



## 탄력적 데이터베이스 풀 생성

[New-AzureRmSqlElasticPool](https://msdn.microsoft.com/library/azure/mt619378.aspx) cmdlet을 사용하여 탄력적 데이터베이스 풀을 만듭니다.

	New-AzureRmSqlElasticPool -ResourceGroupName "resourcegroup1" -ServerName "server1" -ElasticPoolName "elasticpool1" -Edition "Standard" -Dtu 400 -DatabaseDtuMin 10 -DatabaseDtuMax 100


## 탄력적 데이터베이스 풀에 새 탄력적 데이터베이스 만들기

풀 내에서 직접 새 데이터베이스를 만들려면 [New-AzureRmSqlDatabase](https://msdn.microsoft.com/library/azure/mt619339.aspx) cmdlet을 사용하고 **ElasticPoolName** 매개 변수를 설정합니다.


	New-AzureRmSqlDatabase -ResourceGroupName "resourcegroup1" -ServerName "server1" -DatabaseName "database1" -ElasticPoolName "elasticpool1"



## 탄력적 데이터베이스 풀에 독립 실행형 데이터베이스 이동

기존 데이터베이스를 풀로 이동하려면 [Set-AzureRmSqlDatabase](https://msdn.microsoft.com/library/azure/mt619433.aspx) cmdlet을 사용하고 **ElasticPoolName** 매개 변수를 설정합니다.

	Set-AzureRmSqlDatabase -ResourceGroupName "resourcegroup1" -ServerName "server1" -DatabaseName "database1" -ElasticPoolName "elasticpool1"



## 탄력적 데이터베이스 풀 PowerShell 예제 만들기

이 스크립트는 새 서버를 만들어서 사용자 이름 및 암호를 묻는 경우 새 서버에 (Azure 자격 증명이 아닌) 관리자 로그인 및 관리자 암호를 입력합니다.

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


## 탄력적 데이터베이스 참조

탄력적 데이터베이스 및 탄력적 데이터베이스 풀에 대한 자세한 내용은 [탄력적 데이터베이스 참조](sql-database-elastic-pool-reference.md)를 참조하세요.

<!---HONumber=AcomDC_0316_2016-->