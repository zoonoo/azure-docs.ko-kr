---
title: 탄력적 데이터베이스 풀 관리(PowerShell) | Microsoft Docs
description: PowerShell을 사용하여 탄력적 데이터베이스 풀을 관리하는 방법을 알아봅니다.
services: sql-database
documentationcenter: ''
author: srinia
manager: jhubbard
editor: ''

ms.service: sql-database
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: powershell
ms.workload: data-management
ms.date: 06/22/2016
ms.author: srinia

---
# PowerShell로 탄력적 데이터베이스 풀 모니터링 및 관리
> [!div class="op_single_selector"]
> * [Azure 포털](sql-database-elastic-pool-manage-portal.md)
> * [PowerShell](sql-database-elastic-pool-manage-powershell.md)
> * [C#](sql-database-elastic-pool-manage-csharp.md)
> * [T-SQL](sql-database-elastic-pool-manage-tsql.md)
> 
> 

PowerShell cmdlet을 사용하여 [탄력적 데이터베이스 풀](sql-database-elastic-pool.md)을 관리합니다.

일반적인 오류 코드는 [SQL 데이터베이스 클라이언트 응용 프로그램의 SQL 오류 코드: 데이터베이스 연결 오류 및 기타 문제](sql-database-develop-error-messages.md)를 참조하세요.

풀에 대한 값은 [eDTU 및 저장소 제한](sql-database-elastic-pool.md#eDTU-and-storage-limits-for-elastic-pools-and-elastic-databases)에서 찾을 수 있습니다.

## 필수 조건
* Azure PowerShell 1.0 이상. 자세한 내용은 [Azure PowerShell을 설치 및 구성하는 방법](../powershell-install-configure.md)을 참조하세요.
* 탄력적 데이터베이스 풀은 SQL 데이터베이스 V12 서버에서만 사용할 수 있습니다. SQL 데이터베이스 V11 서버가 있는 경우 [PowerShell을 사용하여 한 번에 V12로 업그레이드하고 풀을 만듭니다](sql-database-upgrade-server-portal.md).

## 탄력적 풀로 데이터베이스 이동
[Set-AzureRmSqlDatabase](https://msdn.microsoft.com/library/azure/mt619433.aspx)를 사용하여 데이터베이스를 풀 안팎으로 이동할 수 있습니다.

    Set-AzureRmSqlDatabase -ResourceGroupName "resourcegroup1" -ServerName "server1" -DatabaseName "database1" -ElasticPoolName "elasticpool1"

## 풀의 성능 설정 변경
성능이 저하되는 경우 규모 증가에 맞게 풀의 설정을 변경할 수 있습니다. [Set-AzureRmSqlElasticPool](https://msdn.microsoft.com/library/azure/mt603511.aspx) cmdlet을 사용합니다. -Dtu 매개 변수를 풀당 eDTU로 설정합니다. 가능한 값은 [eDTU 및 저장소 제한](sql-database-elastic-pool.md#eDTU-and-storage-limits-for-elastic-pools-and-elastic-databases)을 참조하세요.

    Set-AzureRmSqlElasticPool –ResourceGroupName “resourcegroup1” –ServerName “server1” –ElasticPoolName “elasticpool1” –Dtu 1200 –DatabaseDtuMax 100 –DatabaseDtuMin 50 


## 풀 관련 작업 상태 확인
풀을 만드는 데 시간이 걸릴 수 있습니다. [Get-AzureRmSqlElasticPoolActivity](https://msdn.microsoft.com/library/azure/mt603812.aspx) cmdlet을 사용하여 생성 및 업데이트를 포함한 풀 작업의 상태를 추적합니다.

    Get-AzureRmSqlElasticPoolActivity –ResourceGroupName “resourcegroup1” –ServerName “server1” –ElasticPoolName “elasticpool1” 


## 탄력적 데이터베이스를 풀 내외부로 이동하는 작업의 상태 확인
데이터베이스를 이동하는 데 시간이 걸릴 수 있습니다. [Get-AzureRmSqlDatabaseActivity](https://msdn.microsoft.com/library/azure/mt603687.aspx) cmdlet을 사용하여 이동 상태를 추적합니다.

    Get-AzureRmSqlDatabaseActivity -ResourceGroupName "resourcegroup1" -ServerName "server1" -DatabaseName "database1" -ElasticPoolName "elasticpool1"

## 풀에 대한 리소스 사용 데이터 가져오기
원본 풀 한도의 백분율로 검색할 수 있는 메트릭:

| 메트릭 이름 | 설명 |
|:--- |:--- |
| cpu\_percent |풀 한도의 백분율로 평균 계산 사용률 |
| physical\_data\_read\_percent |풀 한도에 따른 백분율로 평균 I/O 사용률 |
| log\_write\_percent |풀 한도의 백분율로 평균 쓰기 리소스 사용률 |
| DTU\_consumption\_percent |풀에 대한 eDTU 한도의 백분율로 평균 eDTU 사용률 |
| storage\_percent |풀의 저장소 한도의 백분율로 평균 저장소 사용률 |
| workers\_percent |풀의 한도에 따른 백분율로 최대 동시 작업자(요청) |
| sessions\_percent |풀의 한도에 따른 백분율로 최대 동시 세션 |
| eDTU\_limit |이 간격 중에 이 탄력적 풀에 대한 현재 최대 탄력적 풀 DTU 설정 |
| storage\_limit |이 간격 중에 이 탄력적 풀에 대한 현재 최대 탄력적 풀 저장소 제한 설정(MB) |
| eDTU\_used |이 간격에 풀에서 사용한 평균 eDTU |
| storage\_used |이 간격에 풀에서 사용한 평균 저장소(바이트) |

**메트릭 세분성/보존 기간:**

* 데이터는 5분의 세분성으로 반환됩니다.
* 데이터 보존 기간은 35일입니다.

이 cmdlet 및 API는 한 번의 호출로 검색할 수 있는 행의 수를 1000행(5분의 세분성에서 약 3일치 데이터)으로 제한합니다. 하지만 이 명령을 서로 다른 시작/종료 시간 간격으로 여러 번 호출하여 더 많은 데이터를 검색할 수 있습니다.

메트릭을 검색하려면:

    $metrics = (Get-AzureRmMetric -ResourceId /subscriptions/<subscriptionId>/resourceGroups/FabrikamData01/providers/Microsoft.Sql/servers/fabrikamsqldb02/elasticPools/franchisepool -TimeGrain ([TimeSpan]::FromMinutes(5)) -StartTime "4/18/2015" -EndTime "4/21/2015")  


## 탄력적 데이터베이스에 대한 리소스 사용량 데이터 가져오기
이 API는 다음과 같은 의미 체계 차이를 제외하고 독립 실행형 데이터베이스의 리소스 사용률을 모니터링하는 데 사용되는 현재(V12) API와 동일합니다.

이 API의 경우 메트릭이 해당 풀에 대한 최대 eDTU(또는 CPU, IO 등 기반 메트릭에 대해 동등한 최대값) 집합당 백분율로 표현됩니다. 예를 들어, 이 메트릭에서 50% 사용률은 특정 리소스 사용률이 상위 풀의 리소스에 대한 데이터베이스당 최대값 한도의 50%임을 나타냅니다.

메트릭을 검색하려면:

    $metrics = (Get-AzureRmMetric -ResourceId /subscriptions/<subscriptionId>/resourceGroups/FabrikamData01/providers/Microsoft.Sql/servers/fabrikamsqldb02/databases/myDB -TimeGrain ([TimeSpan]::FromMinutes(5)) -StartTime "4/18/2015" -EndTime "4/21/2015") 

## 리소스 풀에 경고 추가
리소스에 경고 규칙을 추가하여 리소스가 설정한 사용률 임계값에 도달할 경우 [URL 끝점](https://msdn.microsoft.com/library/mt718036.aspx)에 전자 메일 알림 또는 경고 문자열을 보낼 수 있습니다. Add-AzureRmMetricAlertRule cmdlet을 사용합니다.

> [!IMPORTANT]
> 탄력적 풀에 대한 리소스 사용률 모니터링은 20분 이상 지연됩니다. 탄력적 풀에 대한 경고를 30분 미만으로 설정하는 것은 현재 지원되지 않습니다. 탄력적 풀에 대해 30분 미만의 기간으로 설정한 경고(PowerShell API에서 "-WindowSize"라는 매개 변수 사용)는 트리거되지 않을 수 있습니다. 탄력적 풀에 대해 정의하는 모든 경고는 30분 이상의 기간(WindowSize)을 사용해야 합니다.
> 
> 

이 예제에서는 풀의 eDTU 소비가 특정 임계값을 초과할 때 알림을 받기 위해 경고를 추가합니다.

    # Set up your resource ID configurations
    $subscriptionId = '<Azure subscription id>'      # Azure subscription ID
    $location =  '<location'                         # Azure region
    $resourceGroupName = '<resource group name>'     # Resource Group
    $serverName = '<server name>'                    # server name
    $poolName = '<elastic pool name>'                # pool name 

    #$Target Resource ID
    $ResourceID = '/subscriptions/' + $subscriptionId + '/resourceGroups/' +$resourceGroupName + '/providers/Microsoft.Sql/servers/' + $serverName + '/elasticpools/' + $poolName

    # Create an email action
    $actionEmail = New-AzureRmAlertRuleEmail -SendToServiceOwners -CustomEmail JohnDoe@contoso.com

    # create a unique rule name
    $alertName = $poolName + "- DTU consumption rule"

    # Create an alert rule for DTU_consumption_percent
    Add-AzureRMMetricAlertRule -Name $alertName -Location $location -ResourceGroup $resourceGroupName -TargetResourceId $ResourceID -MetricName "DTU_consumption_percent"  -Operator GreaterThan -Threshold 80 -TimeAggregationOperator Average -WindowSize 00:60:00 -Actions $actionEmail 

## 풀 내의 모든 데이터베이스에 경고 추가
탄력적 풀의 모든 데이터베이스에 경고 규칙을 추가하여 리소스가 경고에 의해 설정된 사용률 임계값에 도달할 경우 [URL 끝점](https://msdn.microsoft.com/library/mt718036.aspx)에 전자 메일 알림 또는 경고 문자열을 보낼 수 있습니다.

> [!IMPORTANT]
> 탄력적 풀에 대한 리소스 사용률 모니터링은 20분 이상 지연됩니다. 탄력적 풀에 대한 경고를 30분 미만으로 설정하는 것은 현재 지원되지 않습니다. 탄력적 풀에 대해 30분 미만의 기간으로 설정한 경고(PowerShell API에서 "-WindowSize"라는 매개 변수 사용)는 트리거되지 않을 수 있습니다. 탄력적 풀에 대해 정의하는 모든 경고는 30분 이상의 기간(WindowSize)을 사용해야 합니다.
> 
> 

이 예제에서는 해당 데이터베이스의 DTU 사용량이 지정된 특정 임계값을 초과할 경우 알림을 받기 위해 풀의 각 데이터베이스에 경고를 추가합니다.

    # Set up your resource ID configurations
    $subscriptionId = '<Azure subscription id>'      # Azure subscription ID
    $location = '<location'                          # Azure region
    $resourceGroupName = '<resource group name>'     # Resource Group
    $serverName = '<server name>'                    # server name
    $poolName = '<elastic pool name>'                # pool name 

    # Get the list of databases in this pool.
    $dbList = Get-AzureRmSqlElasticPoolDatabase -ResourceGroupName $resourceGroupName -ServerName $serverName -ElasticPoolName $poolName

    # Create an email action
    $actionEmail = New-AzureRmAlertRuleEmail -SendToServiceOwners -CustomEmail JohnDoe@contoso.com

    # Get resource usage metrics for a database in an elastic database for the specified time interval.
    foreach ($db in $dbList)
    {
    $dbResourceId = '/subscriptions/' + $subscriptionId + '/resourceGroups/' + $resourceGroupName + '/providers/Microsoft.Sql/servers/' + $serverName + '/databases/' + $db.DatabaseName

    # create a unique rule name
    $alertName = $db.DatabaseName + "- DTU consumption rule"

    # Create an alert rule for DTU_consumption_percent
    Add-AzureRMMetricAlertRule -Name $alertName  -Location $location -ResourceGroup $resourceGroupName -TargetResourceId $dbResourceId -MetricName "dtu_consumption_percent"  -Operator GreaterThan -Threshold 80 -TimeAggregationOperator Average -WindowSize 00:60:00 -Actions $actionEmail

    # drop the alert rule
    #Remove-AzureRmAlertRule -ResourceGroup $resourceGroupName -Name $alertName
    } 



## 구독에서 여러 풀에 걸친 리소스 사용 데이터 수집 및 모니터링
구독에 데이터베이스가 많은 경우 각 탄력적 풀을 개별적으로 모니터링하는 작업은 번거롭습니다. 대신, 리소스 사용량을 모니터링하고 분석하기 위해 여러 풀 및 해당 데이터베이스에서 리소스 사용 데이터를 수집하기 위해 SQL 데이터베이스 PowerShell cmdlet 및 T-SQL 쿼리를 결합할 수 있습니다. 이러한 일련의 Powershell 스크립트의 [샘플 구현](https://github.com/Microsoft/sql-server-samples/tree/master/samples/manage/azure-sql-db-elastic-pools)은 작동 내용 및 사용 방법에 대한 설명서와 함께 GitHub SQL Server 샘플 리포지토리에 나와 있습니다.

이 샘플 구현을 사용하려면 아래에 나열된 다음 단계를 따릅니다.

1. [스크립트 및 설명서](https://github.com/Microsoft/sql-server-samples/tree/master/samples/manage/azure-sql-db-elastic-pools)를 다운로드합니다.
2. 사용자 환경에 대한 스크립트를 수정합니다. 탄력적 풀이 호스팅되는 하나 이상의 서버를 지정합니다.
3. 수집된 메트릭을 저장할 원격 분석 데이터베이스를 지정합니다.
4. 스크립트의 실행 기간을 지정하는 스크립트를 사용자 지정합니다.

높은 수준에서 스크립트는 다음을 수행합니다.

* 지정된 Azure 구독(또는 지정된 서버 목록)에 모든 서버를 열거합니다.
* 각 서버에 대한 백그라운드 작업을 실행합니다. 작업은 일정한 간격으로 루프에서 실행되고 서버에서 모든 풀에 대한 원격 분석 데이터를 수집합니다. 그러면 지정된 원격 분석 데이터베이스에 수집된 데이터를 로드합니다.
* 각 풀에서 데이터베이스의 목록을 열거하여 데이터베이스 리소스 사용 데이터를 수집합니다. 그러면 원격 분석 데이터베이스에 수집된 데이터를 로드합니다.

탄력적 풀 및 해당 데이터베이스의 상태를 모니터링하도록 원격 분석 데이터베이스에서 수집된 메트릭을 분석할 수 있습니다. 또한 스크립트는 원격 분석 데이터베이스에 미리 정의된 TVF(테이블 값 함수)를 설치하여 지정된 기간 동안 메트릭을 집계하도록 도움을 줍니다. 예를 들어 TVF의 결과는 "주어진 기간에 최대 eDTU 사용률을 포함한 상위 N개의 탄력적 풀 창"을 표시하는 데 사용할 수 있습니다. 필요에 따라 Excel 또는 Power BI와 같은 분석 도구를 사용하여 수집된 데이터를 쿼리하고 분석합니다.

## 예: 풀 및 해당 데이터베이스에 대한 리소스 사용 메트릭 검색
이 예에서는 지정된 탄력적 풀 및 모든 데이터베이스에 대한 사용 메트릭을 검색합니다. 수집된 데이터는 형식이 지정되어 .csv 형식 파일에 기록됩니다. 이 파일은 Excel에서 탐색할 수 있습니다.

    $subscriptionId = '<Azure subscription id>'          # Azure subscription ID
    $resourceGroupName = '<resource group name>'             # Resource Group
    $serverName = <server name>                              # server name
    $poolName = <elastic pool name>                          # pool name

    # Login to Azure account and select the subscription.
    Login-AzureRmAccount
    Set-AzureRmContext -SubscriptionId $subscriptionId

    # Get resource usage metrics for an elastic pool for the specified time interval.
    $startTime = '4/27/2016 00:00:00'  # start time in UTC
    $endTime = '4/27/2016 01:00:00'    # end time in UTC

    # Construct the pool resource ID and retrive pool metrics at 5 minute granularity.
    $poolResourceId = '/subscriptions/' + $subscriptionId + '/resourceGroups/' + $resourceGroupName + '/providers/Microsoft.Sql/servers/' + $serverName + '/elasticPools/' + $poolName
    $poolMetrics = (Get-AzureRmMetric -ResourceId $poolResourceId -TimeGrain ([TimeSpan]::FromMinutes(5)) -StartTime $startTime -EndTime $endTime) 

    # Get the list of databases in this pool.
    $dbList = Get-AzureRmSqlElasticPoolDatabase -ResourceGroupName $resourceGroupName -ServerName $serverName -ElasticPoolName $poolName

    # Get resource usage metrics for a database in an elastic database for the specified time interval.
    $dbMetrics = @()
    foreach ($db in $dbList)
    {
        $dbResourceId = '/subscriptions/' + $subscriptionId + '/resourceGroups/' + $resourceGroupName + '/providers/Microsoft.Sql/servers/' + $serverName + '/databases/' + $db.DatabaseName
        $dbMetrics = $dbMetrics + (Get-AzureRmMetric -ResourceId $dbResourceId -TimeGrain ([TimeSpan]::FromMinutes(5)) -StartTime $startTime -EndTime $endTime)
    }

    #Optionally you can format the metrics and output as .csv file using the following script block.
    $command = {
    param($metricList, $outputFile)

    # Format metrics into a table.
    $table = @()
    foreach($metric in $metricList) { 
      foreach($metricValue in $metric.MetricValues) {
        $sx = New-Object PSObject -Property @{
            Timestamp = $metricValue.Timestamp.ToString()
            MetricName = $metric.Name; 
            Average = $metricValue.Average;
            ResourceID = $metric.ResourceId 
          }
          $table = $table += $sx
      }
    }

    # Output the metrics into a .csv file.
    write-output $table | Export-csv -Path $outputFile -Append -NoTypeInformation
    }

    # Format and output pool metrics
    Invoke-Command -ScriptBlock $command -ArgumentList $poolMetrics,c:\temp\poolmetrics.csv

    # Format and output database metrics
    Invoke-Command -ScriptBlock $command -ArgumentList $dbMetrics,c:\temp\dbmetrics.csv



## 탄력적 풀 작업의 대기 시간
* 데이터베이스당 최소 eDTU 또는 데이터베이스당 최대 eDTU를 변경하는 작업은 일반적으로 5분 이내에 완료됩니다.
* 풀당 eDTU를 변경하는 작업은 풀에 있는 모든 데이터베이스에서 사용한 공간의 전체 크기에 따라 달라집니다. 변경 시간은 100GB당 평균 90분 이하입니다. 예를 들어 풀에 있는 모든 데이터베이스에서 사용 중인 총 공간이 200GB일 경우, 풀당 풀 eDTU를 변경하는 예상 대기 시간은 3시간 이하입니다.

## V11에서 V12 서버로 마이그레이션
PowerShell cmdlet을 사용하여 V11 또는 기타 V12 이전 버전에서 Azure SQL 데이터베이스 V12로의 업그레이드를 시작, 중지 또는 모니터링할 수 있습니다.

* [PowerShell을 사용하여 SQL 데이터베이스 V12로 업그레이드 준비](sql-database-upgrade-server-powershell.md)

이러한 PowerShell cmdlet에 대한 참조 설명서는 다음을 참조하세요.

* [Get AzureRMSqlServerUpgrade](https://msdn.microsoft.com/library/azure/mt603582.aspx)
* [Start-AzureRMSqlServerUpgrade](https://msdn.microsoft.com/library/azure/mt619403.aspx)
* [Stop-AzureRMSqlServerUpgrade](https://msdn.microsoft.com/library/azure/mt603589.aspx)

Stop- cmdlet은 취소를 의미하는 것으로, 일시 중지가 아닙니다. 처음부터 다시 시작하는 것 외에 업그레이드를 다시 시작할 수 있는 방법은 없습니다. Stop- cmdlet은 해당하는 모든 리소스를 정리하고 해제합니다.

## 다음 단계
* [탄력적 작업 만들기](sql-database-elastic-jobs-overview.md) 탄력적 작업은 풀의 데이터베이스 개수에 관계없이 T-SQL 스크립트를 실행할 수 있습니다.
* [Azure SQL 데이터베이스를 사용하여 규모 확장](sql-database-elastic-scale-introduction.md) 참조: 탄력적 데이터베이스 도구를 사용하여 규모를 확장하거나 데이터를 이동하거나 쿼리 또는 트랜잭션을 만듭니다.

<!---HONumber=AcomDC_0706_2016-->