<properties
	pageTitle="풀에 적합한 단일 데이터베이스를 식별하는 Powershell 스크립트"
	description="탄력적 데이터베이스 풀은 탄력적 데이터베이스 그룹에서 공유하는 가용 리소스의 컬렉션입니다. 이 문서는 탄력적 데이터베이스 풀을 사용하여 데이터베이스의 그룹에 대한 적합성을 평가하는데 유용한 Powershell 스크립트를 제공합니다."
	services="sql-database"
	documentationCenter=""
	authors="stevestein"
	manager="jhubbard"
	editor=""/>

<tags
	ms.service="sql-database"
	ms.devlang="NA"
	ms.date="06/06/2016"
	ms.author="sstein"
	ms.workload="data-management"
	ms.topic="article"
	ms.tgt_pltfrm="NA"/>

# 탄력적 데이터베이스 풀에 적합한 데이터베이스를 식별하기 위한 PowerShell 스크립트

이 문서의 샘플 PowerShell 스크립트는 SQL 데이터베이스 서버의 사용자 데이터베이스에 대한 eDTU 집계 값을 예측합니다. 이 스크립트는 실행되는 동안 데이터를 수집하며, 일반적인 프로덕션 워크로드의 경우에는 하루 이상 이 스크립트를 실행해야 합니다. 이상적으로 데이터베이스의 일반적인 워크로드를 나타내는 지속 기간에 대해 해당 스크립트를 실행할 수 있습니다. 이렇게 해도 데이터베이스의 일반 및 최대 사용량을 나타내는 데이터를 캡처하는 데 충분합니다. 1주 또는 그 이상 스크립트를 실행하면 좀 더 정확한 예측값을 얻을 수 있습니다.

이 스크립트는 풀이 지원되지 않는 v11 서버에서 데이터베이스를 평가하고 풀이 지원되는 v12 서버로 마이그레이션하는 데 특히 유용합니다. v12 서버에서 SQL 데이터베이스에는 과거 사용량 원격 분석을 수행하고 가장 비용 효율적인 풀을 권장하는 기본 제공 인텔리전스 기능이 있습니다. 이 기능을 사용하는 방법에 대한 자세한 내용은 [탄력적 데이터베이스 풀 모니터링, 관리 및 크기 조정](sql-database-elastic-pool-manage-portal.md)을 참조하세요.

> [AZURE.IMPORTANT] 스크립트를 실행 하는 동안 PowerShell 창을 꼭 열어두어야 합니다. 필요한 시간 동안 스크립트를 실행할 때까지 PowerShell 창을 닫지 않도록 합니다.

## 필수 조건 

스크립트를 실행 하기 전에 다음을 설치 합니다.:

- 최신 [PowerShell 명령줄 도구](http://go.microsoft.com/?linkid=9811175&clcid=0x409)
- [SQL Server 2014 기능 팩](https://www.microsoft.com/download/details.aspx?id=42295)

### 스크립트 세부 정보

클라우드의 VM 또는 로컬 컴퓨터에서 스크립트를 실행할 수 있습니다. 로컬 컴퓨터에서 실행할 경우에 스크립트를 대상 데이터베이스에서 데이터를 다운로드 해야 하기 때문에 데이터 송신 요금이 발생할 수도 있습니다. 아래에서 데이터베이스의 수 및 스크립트를 실행 하는 기간에 따라 예상 데이터 볼륨을 예측 합니다. Azure 데이터 전송 비용은 [데이터 전송 가격 정보](https://azure.microsoft.com/pricing/details/data-transfers/)를 참조하세요.
       
 -     시간당 1 데이터베이스 = 38 (kb)
 -     하루 1 데이터베이스 = 900 KB
 -     주당 1 데이터베이스 = 6MB
 -     하루에 100 데이터베이스= 90MB
 -     주당 500 데이터베이스 = 3GB

스크립트는 표준 계층에 대한 현재 공개 미리 보기에 적합하지 않은 데이터베이스를 제외합니다. 대상 서버에서 추가 데이터베이스를 제외해야 할 경우 해당 조건에 맞게 스크립트를 변경합니다. 기본적으로 스크립트는 다음에 대한 정보를 컴파일하지 않습니다.

* 탄력적 데이터베이스(데이터베이스가 탄력적 풀에서 이미 포함되어 있음)
* 서버의 Master 데이터베이스

스크립트는 출력 데이터베이스 분석에 대한 중간 데이터를 저장 해야 합니다. 신규 또는 기존 데이터베이스를 사용할 수 있습니다. 필수적이지 않지만 도구를 실행할 때 출력 데이터베이스 분석 결과 영향을 방지하려면 다른 서버에 있어야 합니다. 출력 데이터베이스의 성능 수준은 S0 이상이어야 합니다. 많은 수의 데이터베이스에 대한 데이터를 긴 기간 동안 수집하는 경우 더 높은 성능 수준으로 출력 데이터베이스를 업그레이드 하는 것이 좋습니다.

스크립트는 대상 서버(탄력적 데이터베이스 풀 후보)에 연결하는 데 사용할 자격 증명을 전체 서버 이름 <*dbname*>**.database.windows.net**으로 제공해야 합니다. 스크립트는 한 번에 둘 이상의 서버에 분석을 지원하지 않습니다.

초기 매개 변수 집합에 대한 값을 제출한 후 Azure 계정에 로그온하라는 메시지가 표시됩니다. 출력 데이터베이스 서버가 아닌 대상 서버에 로그온 됩니다.
	
다음 경고가 뜬 경우에는, 경고를 무시하고 스크립트를 실행할 수 있습니다.

- 경고: Switch-azuremode cmdlet이 반대되었습니다.
- 경고: SQL Server 서비스 정보를 얻을 수 없습니다. 다음 오류로 인해 'Microsoft.Azure.Commands.Sql.dll’의 WMI에 연결 하지 못했습니다: RPC 서버를 사용할 수 없습니다.

스크립트가 완료되면 대상 서버의 모든 후보 데이터베이스를 포함하는 풀에 필요한 eDTU의 예상 수를 출력합니다. 이 예상 eDTU는 풀을 만들고 구성하는 데 사용할 수 있습니다. 풀을 만들고 데이터베이스를 풀로 이동한 후 며칠 동안 풀을 면밀히 모니터링하고 필요에 따라 풀 eDTU 구성을 조정해야 합니다. [탄력적 데이터베이스 풀 모니터링, 관리 및 크기 조정](sql-database-elastic-pool-manage-portal.md)을 참조하세요.


   [AZURE.INCLUDE [learn-about-deployment-models-classic-include](../../includes/learn-about-deployment-models-classic-include.md)
    
    param (
	[Parameter(Mandatory=$true)][string]$AzureSubscriptionName, # Azure Subscription name - can be found on the Azure portal: https://portal.azure.com/
	[Parameter(Mandatory=$true)][string]$ResourceGroupName, # Resource Group name - can be found on the Azure portal: https://portal.azure.com/
	[Parameter(Mandatory=$true)][string]$servername, # full server name like "abcdefg.database.windows.net"
	[Parameter(Mandatory=$true)][string]$username, # user name
	[Parameter(Mandatory=$true)][string]$serverPassword, # password
	[Parameter(Mandatory=$true)][string]$outputServerName, # metrics collection database for analysis. full server name like "zyxwvu.database.windows.net"
	[Parameter(Mandatory=$true)][string]$outputdatabaseName,
	[Parameter(Mandatory=$true)][string]$outputDBUsername,
	[Parameter(Mandatory=$true)][string]$outputDBpassword,
	[Parameter(Mandatory=$true)][int]$duration_minutes # How long to run. Recommend to run for the period of time when your typical workload is running. At least 10 mins.
	)
	
	Login-AzureRmAccount
	Select-AzureSubscription $AzureSubscriptionName
	
	$server = Get-AzureRmSqlServer -ServerName $servername.Split('.')[0] -ResourceGroupName $ResourceGroupName
	
	# Check version/upgrade status of the server
	$upgradestatus = Get-AzureRmSqlServerUpgrade -ServerName $servername.Split('.')[0] -ResourceGroupName $ResourceGroupName
	$version = ""
	if ([string]::IsNullOrWhiteSpace($server.ServerVersion)) 
	{
	$version = $upgradestatus.Status
	}
	else
	{
	$version = $server.ServerVersion
	}
	
	# For Elastic database pool candidates, we exclude master, and any databases that are already in a pool. You may add more databases to the excluded list below as needed
	$ListOfDBs = Get-AzureRmSqlDatabase -ServerName $servername.Split('.')[0] -ResourceGroupName $ResourceGroupName | Where-Object {$_.DatabaseName -notin ("master") -and $_.CurrentServiceLevelObjectiveName -notin ("ElasticPool") -and $_.CurrentServiceObjectiveName -notin ("ElasticPool")}
	
	$outputConnectionString = "Data Source=$outputServerName;Integrated Security=false;Initial Catalog=$outputdatabaseName;User Id=$outputDBUsername;Password=$outputDBpassword"
	$destinationTableName = "resource_stats_output"
	
	# Create a table in output database for metrics collection
	$sql = "
	IF  NOT EXISTS (SELECT * FROM sys.objects 
	WHERE object_id = OBJECT_ID(N'$($destinationTableName)') AND type in (N'U'))
	
	BEGIN
	Create Table $($destinationTableName) (database_name varchar(128), slo varchar(20), end_time datetime, avg_cpu float, avg_io float, avg_log float, db_size float);
	Create Clustered Index ci_endtime ON $($destinationTableName) (end_time);
	END
	TRUNCATE TABLE $($destinationTableName);
	"
	Invoke-Sqlcmd -ServerInstance $outputServerName -Database $outputdatabaseName -Username $outputDBUsername -Password $outputDBpassword -Query $sql -ConnectionTimeout 120 -QueryTimeout 120 
	
	# waittime (minutes) is interval between data collection queries in the loop below.
	$Waittime = 10
	$end_Time = [DateTime]::UtcNow
	$start_time = $end_time.AddMinutes(-$Waittime)
	$finish_time = $end_Time.AddMinutes($duration_minutes)
	
	While ($end_time -lt $finish_time)
	{
	Write-Host "Collecting metrics..." 
	foreach ($db in $ListOfDBs)
	{
	if ($version -in ("12.0", "Completed")) # for V12 databases 
	{
	$sql = "Declare @dbname varchar(128) = '$($db.DatabaseName)';"
	$sql += "Declare @SLO varchar(20) = '$($db.CurrentServiceObjectiveName)';"
	$sql+= "
	Declare @DTU_cap int, @db_size float;
	Select @DTU_cap = CASE @SLO 
	WHEN 'Basic' THEN 5
	WHEN 'S0' THEN 10
	WHEN 'S1' THEN 20
	WHEN 'S2' THEN 50
	WHEN 'S3' THEN 100
	WHEN 'P1' THEN 125
	WHEN 'P2' THEN 250
	WHEN 'P3' THEN 1000
	ELSE 50 -- assume Web/Business DBs
	END
	SELECT @db_size = SUM(reserved_page_count) * 8.0/1024/1024 FROM sys.dm_db_partition_stats
	SELECT @dbname as database_name, @SLO as SLO, dateadd(second, round(datediff(second, '2015-01-01', end_time) / 15.0, 0) * 15,'2015-01-01')
	as end_time, avg_cpu_percent * (@DTU_cap/100.0) AS avg_cpu, avg_data_io_percent * (@DTU_cap/100.0) AS avg_io, avg_log_write_percent * (@DTU_cap/100.0) AS avg_log, @db_size as db_size FROM sys.dm_db_resource_stats
	WHERE end_time > '$($start_time)' and end_time <= '$($end_time)';
	" 
	}
	else
	{
	$sql = "Declare @dbname varchar(128) = '$($db.DatabaseName)';"
	$sql += "Declare @SLO varchar(20) = '$($db.CurrentServiceObjectiveName)';"
	$sql+= "
	Declare @DTU_cap int, @db_size float;
	Select @DTU_cap = CASE @SLO 
	WHEN 'Basic' THEN 5
	WHEN 'S0' THEN 10
	WHEN 'S1' THEN 20
	WHEN 'S2' THEN 50
	WHEN 'P1' THEN 100
	WHEN 'P2' THEN 200
	WHEN 'P3' THEN 800
	ELSE 50 -- assume Web/Business DBs
	END
	SELECT @db_size = SUM(reserved_page_count) * 8.0/1024/1024 from sys.dm_db_partition_stats
	SELECT @dbname as database_name, @SLO as SLO, dateadd(second, round(datediff(second, '2015-01-01', end_time) / 15.0, 0) * 15,'2015-01-01')
	as end_time, avg_cpu_percent * (@DTU_cap/100.0) AS avg_cpu, avg_data_io_percent * (@DTU_cap/100.0) AS avg_io, avg_log_write_percent * (@DTU_cap/100.0) AS avg_log, @db_size as db_size FROM sys.dm_db_resource_stats
	WHERE end_time > '$($start_time)' and end_time <= '$($end_time)';
	" 
	}
	
	$result = Invoke-Sqlcmd -ServerInstance $servername -Database $db.DatabaseName -Username $username -Password $serverPassword -Query $sql -ConnectionTimeout 120 -QueryTimeout 3600 
	#bulk copy the metrics to output database
	$bulkCopy = new-object ("Data.SqlClient.SqlBulkCopy") $outputConnectionString 
	$bulkCopy.BulkCopyTimeout = 600
	$bulkCopy.DestinationTableName = "$destinationTableName";
	$bulkCopy.WriteToServer($result);
	
	}
	
	$start_time = $start_time.AddMinutes($Waittime)
	$end_time = $end_time.AddMinutes($Waittime)
	Write-Host $start_time
	Write-Host $end_time
	do {
	Start-Sleep 1
	   }
	until (([DateTime]::UtcNow) -ge $end_time)
	}
	
	Write-Host "Analyzing the collected metrics...."
	# Analysis query that does aggregation of the resource metrics to calculate pool size.
	$sql1 = 'Declare @DTU_Perf_99 as float, @DTU_Storage as float;
	WITH group_stats AS
	(
	SELECT end_time, SUM(db_size) AS avg_group_Storage, SUM(avg_cpu) AS avg_group_cpu, SUM(avg_io) AS avg_group_io,SUM(avg_log) AS avg_group_log
	FROM resource_stats_output 
	WHERE slo LIKE '
	
	$sql2 = '
	GROUP BY end_time
	)
	-- calculate aggregate storage and DTUs for all DBs in the group
	, group_DTU AS
	(
	SELECT end_time, avg_group_Storage, 
	(SELECT Max(v)
	   FROM (VALUES (avg_group_cpu), (avg_group_log), (avg_group_io)) AS value(v)) AS avg_group_DTU
	FROM group_stats
	)
	-- Get top 1 percent of the storage and DTU utilization samples.
	, top1_percent AS (
	SELECT TOP 1 PERCENT avg_group_Storage, avg_group_dtu FROM group_dtu ORDER BY [avg_group_DTU] DESC
	)
	
	-- Max and 99th percentile DTU for the given list of databases if converted into an elastic pool. Storage is increased by factor of 1.25 to accommodate for future growth. Currently storage limit of the pool is determined by the amount of DTUs based on 1GB/DTU.
	--SELECT MAX(avg_group_Storage)*1.25/1024.0 AS Group_Storage_DTU, MAX(avg_group_dtu) AS Group_Performance_DTU, MIN(avg_group_dtu) AS Group_Performance_DTU_99th_percentile FROM top1_percent;
	SELECT @DTU_Storage = MAX(avg_group_Storage)*1.25/1024.0, @DTU_Perf_99 = MIN(avg_group_dtu) FROM top1_percent;
	IF @DTU_Storage > @DTU_Perf_99 
	SELECT ''Total number of DTUs dominated by storage: '' + convert(varchar(100), @DTU_Storage)
	ELSE 
	SELECT ''Total number of DTUs dominated by resource consumption: '' + convert(varchar(100), @DTU_Perf_99)'
	
	#check if there are any web/biz edition dbs in the collected metrics
	$checkslo = "SELECT TOP 1 slo FROM resource_stats_output WHERE slo LIKE 'shared%'"
	$output = Invoke-Sqlcmd -ServerInstance $outputServerName -Database $outputdatabaseName -Username $outputDBUsername -Password $outputDBpassword -Query $checkslo -QueryTimeout 3600 | select -expand slo
	if ($output -like "Shared*")
	{
	write-host "`nWeb/Business edition:" -BackgroundColor Green -ForegroundColor Black
	$sql = $sql1 + "'Shared%'"  + $sql2
	$data = Invoke-Sqlcmd -ServerInstance $outputServerName -Database $outputdatabaseName -Username $outputDBUsername -Password $outputDBpassword -Query $sql -QueryTimeout 3600
	$data | %{'{0}' -f $_[0]}
	}
	
	#check if there are any basic edition dbs in the collected metrics
	$checkslo = "SELECT TOP 1 slo FROM resource_stats_output WHERE slo LIKE 'Basic%'"
	$output = Invoke-Sqlcmd -ServerInstance $outputServerName -Database $outputdatabaseName -Username $outputDBUsername -Password $outputDBpassword -Query $checkslo -QueryTimeout 3600 | select -expand slo
	if ($output -like "Basic*")
	{
	write-host "`nBasic edition:" -BackgroundColor Green -ForegroundColor Black
	$sql = $sql1 + "'Basic%'"  + $sql2
	$data = Invoke-Sqlcmd -ServerInstance $outputServerName -Database $outputdatabaseName -Username $outputDBUsername -Password $outputDBpassword -Query $sql -QueryTimeout 3600
	$data | %{'{0}' -f $_[0]} 
	}
	
	#check if there are any standard edition dbs in the collected metrics
	$checkslo = "SELECT TOP 1 slo FROM resource_stats_output WHERE slo LIKE 'S%' AND slo NOT LIKE 'Shared%'"
	$output = Invoke-Sqlcmd -ServerInstance $outputServerName -Database $outputdatabaseName -Username $outputDBUsername -Password $outputDBpassword -Query $checkslo -QueryTimeout 3600 | select -expand slo
	if ($output -like "S*")
	{
	write-host "`nStandard edition:" -BackgroundColor Green -ForegroundColor Black
	$sql = $sql1 + "'S%' AND slo NOT LIKE 'Shared%'"  + $sql2
	$data = Invoke-Sqlcmd -ServerInstance $outputServerName -Database $outputdatabaseName -Username $outputDBUsername -Password $outputDBpassword -Query $sql -QueryTimeout 3600
	$data | %{'{0}' -f $_[0]}
	}
	
	#check if there are any premium edition dbs in the collected metrics
	$checkslo = "SELECT TOP 1 slo FROM resource_stats_output WHERE slo LIKE 'P%'"
	$output = Invoke-Sqlcmd -ServerInstance $outputServerName -Database $outputdatabaseName -Username $outputDBUsername -Password $outputDBpassword -Query $checkslo -QueryTimeout 3600 | select -expand slo
	if ($output -like "P*")
	{
	write-host "`nPremium edition:" -BackgroundColor Green -ForegroundColor Black
	$sql = $sql1 + "'P%'"  + $sql2
	$data = Invoke-Sqlcmd -ServerInstance $outputServerName -Database $outputdatabaseName -Username $outputDBUsername -Password $outputDBpassword -Query $sql -QueryTimeout 3600
	$data | %{'{0}' -f $_[0]}
}

        

<!---HONumber=AcomDC_0608_2016-->