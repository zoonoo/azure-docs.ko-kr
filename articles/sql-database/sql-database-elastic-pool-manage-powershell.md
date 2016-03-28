<properties 
    pageTitle="탄력적 데이터베이스 풀 관리(PowerShell) | Microsoft Azure" 
    description="PowerShell을 사용하여 탄력적 데이터베이스 풀을 관리하는 방법을 알아봅니다."  
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
    ms.date="03/15/2016"
    ms.author="sstein"/>

# 탄력적 데이터베이스 풀 모니터링 및 관리(PowerShell) 

> [AZURE.SELECTOR]
- [Azure 포털](sql-database-elastic-pool-manage-portal.md)
- [PowerShell](sql-database-elastic-pool-manage-powershell.md)
- [C#](sql-database-elastic-pool-manage-csharp.md)


PowerShell cmdlet을 사용하여 [탄력적 데이터베이스 풀](sql-database-elastic-pool.md)을 관리하는 방법을 알아봅니다.

> [AZURE.NOTE] 탄력적 데이터베이스 풀은 현재 미리 보기 상태이며, SQL 데이터베이스 V12 서버에서만 사용할 수 있습니다. SQL 데이터베이스 V11 서버가 있는 경우 한 단계에서 [PowerShell을 사용하여 V12로 업그레이드 및 풀 만들기](sql-database-upgrade-server-portal.md)를 할 수 있습니다.

Azure PowerShell 1.0 이상을 실행해야 합니다. 자세한 내용은 [Azure PowerShell을 설치 및 구성하는 방법](../powershell-install-configure.md)을 참조하세요.




## 탄력적 데이터베이스 풀에 새 탄력적 데이터베이스 만들기

풀 내에서 직접 새 데이터베이스를 만들려면 [New-AzureRmSqlDatabase](https://msdn.microsoft.com/library/azure/mt619339.aspx) cmdlet을 사용하고 **ElasticPoolName** 매개 변수를 설정합니다.


	New-AzureRmSqlDatabase -ResourceGroupName "resourcegroup1" -ServerName "server1" -DatabaseName "database1" -ElasticPoolName "elasticpool1"


## 독립 실행형 데이터베이스를 탄력적 데이터베이스 풀로 이동

기존 데이터베이스를 풀로 이동하려면 [Set-AzureRmSqlDatabase](https://msdn.microsoft.com/library/azure/mt619433.aspx) cmdlet을 사용하고 **ElasticPoolName** 매개 변수를 설정합니다.

	Set-AzureRmSqlDatabase -ResourceGroupName "resourcegroup1" -ServerName "server1" -DatabaseName "database1" -ElasticPoolName "elasticpool1"


## 탄력적 데이터베이스 풀의 성능 설정 변경

탄력적 데이터베이스 풀의 성능 설정을 변경하려면 [Set-AzureRmSqlElasticPool](https://msdn.microsoft.com/library/azure/mt603511.aspx) cmdlet을 사용합니다.

    Set-AzureRmSqlElasticPool –ResourceGroupName “resourcegroup1” –ServerName “server1” –ElasticPoolName “elasticpool1” –Dtu 1200 –DatabaseDtuMax 100 –DatabaseDtuMin 50 


## 탄력적 데이터베이스 풀 관련 작업 상태 확인

[Get-AzureRmSqlElasticPoolActivity](https://msdn.microsoft.com/library/azure/mt603812.aspx) cmdlet을 사용하여 생성 및 업데이트를 포함한 탄력적 데이터베이스 풀 작업의 상태를 추적할 수 있습니다.

	Get-AzureRmSqlElasticPoolActivity –ResourceGroupName “resourcegroup1” –ServerName “server1” –ElasticPoolName “elasticpool1” 


## 탄력적 데이터베이스를 탄력적 데이터베이스 풀 내외부로 이동하는 작업의 상태 확인

[Get-AzureRmSqlDatabaseActivity](https://msdn.microsoft.com/library/azure/mt603687.aspx) cmdlet을 사용하여 생성 및 업데이트를 포함한 탄력적 데이터베이스 작업의 상태를 추적할 수 있습니다.

	Get-AzureRmSqlDatabaseActivity -ResourceGroupName "resourcegroup1" -ServerName "server1" -DatabaseName "database1" -ElasticPoolName "elasticpool1"

## 탄력적 데이터베이스 풀에 대한 사용 현황 데이터 가져오기

원본 풀 한도의 백분율로 검색할 수 있는 메트릭:

* 평균 CPU 사용률 - cpu\_percent 
* 평균 IO 사용률 - data\_io\_percent 
* 평균 로그 사용률 - log\_write\_percent 
* 평균 메모리 사용률 - memory\_percent 
* 평균 eDTU 사용률(CPU/IO/로그 사용률의 최대값으로) – DTU\_percent 
* 최대 동시 사용자 요청 수(작업자) – max\_concurrent\_requests 
* 최대 동시 사용자 세션 수 – max\_concurrent\_sessions 
* 탄력적 풀의 총 저장소 크기 – storage\_in\_megabytes 


메트릭 세분성/보존 기간:

* 데이터는 5분의 세분성으로 반환됩니다.  
* 데이터 보존 기간은 14일입니다.  


이 cmdlet 및 API는 한 번의 호출로 검색할 수 있는 행의 수를 1000행(5분의 세분성에서 약 3일치 데이터)으로 제한합니다. 하지만 이 명령을 서로 다른 시작/종료 시간 간격으로 여러 번 호출하여 더 많은 데이터를 검색할 수 있습니다.


메트릭 검색:

	$metrics = (Get-Metrics -ResourceId /subscriptions/<subscriptionId>/resourceGroups/FabrikamData01/providers/Microsoft.Sql/servers/fabrikamsqldb02/elasticPools/franchisepool -TimeGrain ([TimeSpan]::FromMinutes(5)) -StartTime "4/18/2015" -EndTime "4/21/2015") 

호출을 반복하고 데이터를 추가하여 더 많은 날짜의 데이터 가져오기:

	$metrics = $metrics + (Get-Metrics -ResourceId /subscriptions/<subscriptionId>/resourceGroups/FabrikamData01/providers/Microsoft.Sql/servers/fabrikamsqldb02/elasticPools/franchisepool -TimeGrain ([TimeSpan]::FromMinutes(5)) -StartTime "4/21/2015" -EndTime "4/24/2015") 
 
테이블 형식 지정:

    $table = Format-MetricsAsTable $metrics 

CSV 파일로 내보내기:

    foreach($e in $table) { Export-csv -Path c:\temp\metrics.csv -input $e -Append -NoTypeInformation} 

## 탄력적 데이터베이스에 대한 리소스 사용 메트릭 가져오기

이 API는 다음과 같은 의미 체계 차이를 제외하고 독립 실행형 데이터베이스의 리소스 사용률을 모니터링하는 데 사용되는 현재(V12) API와 동일합니다.

* 이 API의 경우 메트릭이 해당 탄력적 데이터베이스 풀에 대한 databaseDtuMax(또는 CPU, IO 등 기반 메트릭에 대해 동등한 최대값) 집합당 백분율로 표현됩니다. 예를 들어 이 메트릭에서 50% 사용률은 특정 리소스 사용률이 부모 탄력적 데이터베이스 풀의 리소스에 대한 데이터베이스당 최대값 한도의 50%임을 나타냅니다. 

메트릭 가져오기:

    $metrics = (Get-Metrics -ResourceId /subscriptions/<subscriptionId>/resourceGroups/FabrikamData01/providers/Microsoft.Sql/servers/fabrikamsqldb02/databases/myDB -TimeGrain ([TimeSpan]::FromMinutes(5)) -StartTime "4/18/2015" -EndTime "4/21/2015") 

필요할 경우 호출을 반복하고 데이터를 추가하여 다른 날짜의 데이터 가져오기:

    $metrics = $metrics + (Get-Metrics -ResourceId /subscriptions/<subscriptionId>/resourceGroups/FabrikamData01/providers/Microsoft.Sql/servers/fabrikamsqldb02/databases/myDB -TimeGrain ([TimeSpan]::FromMinutes(5)) -StartTime "4/21/2015" -EndTime "4/24/2015") 

테이블 형식 지정:

    $table = Format-MetricsAsTable $metrics 

CSV 파일로 내보내기:

    foreach($e in $table) { Export-csv -Path c:\temp\metrics.csv -input $e -Append -NoTypeInformation}


## 탄력적 데이터베이스 풀 모니터링 및 관리(PowerShell 예제)


    $subscriptionId = '<Azure subscription id>'
    $resourceGroupName = '<resource group name>'
    $location = '<datacenter location>'
    $serverName = '<server name>'
    $poolName = '<pool name>'
    $databaseName = '<database name>'
    
    Login-AzureRmAccount
    Set-AzureRmContext -SubscriptionId $subscriptionId
    
    
    Set-AzureRmSqlElasticPool –ResourceGroupName $resourceGroupName –ServerName $serverName –ElasticPoolName $poolName –Dtu 1200 –DatabaseDtuMax 100 –DatabaseDtuMin 50 
    
    $poolResourceId = '/subscriptions/' + $subscriptionId + '/resourceGroups/' + $resourceGroupName + '/providers/Microsoft.Sql/servers/' + $serverName + '/elasticPools/' + $poolName
    $dbResourceId = '/subscriptions/' + $subscriptionId + '/resourceGroups/' + $resourceGroupName + '/providers/Microsoft.Sql/servers/' + $serverName + '/databases/' + $databaseName 
    $startTime1 = '2/10/2016'
    $endTime1 = '2/14/2016'
    $startTime2 = '2/14/2016'
    $endTime2 = '2/18/2016'
    
    
    
    $metrics = (Get-Metrics -ResourceId $poolResourceId -TimeGrain ([TimeSpan]::FromMinutes(5)) -StartTime $startTime1 -EndTime $endTime1) 
    $metrics
    
    $metrics = $metrics + (Get-Metrics -ResourceId $poolResourceId -TimeGrain ([TimeSpan]::FromMinutes(5)) -StartTime $startTime2 -EndTime $endTime2)
    $table = Format-MetricsAsTable $metrics
    foreach($e in $table) { Export-csv -Path c:\temp\metrics.csv -input $e -Append -NoTypeInformation}
    
    $metrics = (Get-Metrics -ResourceId $dbResourceId -TimeGrain ([TimeSpan]::FromMinutes(5)) -StartTime $startTime1 -EndTime $endTime1) 
    $metrics = $metrics + (Get-Metrics -ResourceId $dbResourceId -TimeGrain ([TimeSpan]::FromMinutes(5)) -StartTime $startTime2 -EndTime $endTime2)
    $table = Format-MetricsAsTable $metrics
    foreach($e in $table) { Export-csv -Path c:\temp\metrics.csv -input $e -Append -NoTypeInformation}

## 다음 단계

- [탄력적 작업 만들기](sql-database-elastic-jobs-overview.md) 탄력적 작업은 풀의 데이터베이스 개수에 관계없이 T-SQL 스크립트를 실행할 수 있습니다.


## 탄력적 데이터베이스 참조

API 및 오류 세부 정보를 포함하여 탄력적 데이터베이스 및 탄력적 데이터베이스 풀에 대한 자세한 내용은 [탄력적 데이터베이스 참조](sql-database-elastic-pool-reference.md)를 참조하세요.

<!---HONumber=AcomDC_0316_2016-->