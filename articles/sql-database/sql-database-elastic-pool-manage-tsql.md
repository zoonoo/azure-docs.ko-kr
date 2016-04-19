<properties 
    pageTitle="T-SQL을 사용하여 탄력적 풀로 Azure SQL 데이터베이스 만들기 및 이동 | Microsoft Azure" 
    description="T-SQL을 사용하여 탄력적 풀에 Azure SQL 데이터베이스를 만듭니다. 또는 T-SQL을 사용하여 풀 내부 및 외부로 데이터베이스를 이동합니다." 
	services="sql-database" 
    documentationCenter="" 
    authors="sidneyh" 
    manager="jhubbard" 
    editor=""/>

<tags
    ms.service="sql-database"
    ms.devlang="NA"
    ms.topic="get-started-article"
    ms.tgt_pltfrm="NA"
    ms.workload="data-management" 
    ms.date="04/11/2016"
    ms.author="sidneyh"/>

# Transact-SQL로 탄력적 데이터베이스 풀 모니터링 및 관리  

> [AZURE.SELECTOR]
- [Azure 포털](sql-database-elastic-pool-manage-portal.md)
- [C#](sql-database-elastic-pool-manage-csharp.md)
- [PowerShell](sql-database-elastic-pool-manage-powershell.md)
- [T-SQL](sql-database-elastic-pool-manage-tsql.md)

[데이터베이스 만들기(Azure SQL 데이터베이스)](https://msdn.microsoft.com/library/dn268335.aspx) 및 [데이터베이스 변경(Azure SQL 데이터베이스)](https://msdn.microsoft.com/library/mt574871.aspx) 명령을 사용하여 데이터베이스를 만들고 탄력적 풀의 내부 및 외부로 이동시킵니다. 탄력적 풀은 이러한 명령을 사용하기 전에 있어야 합니다. 이러한 명령은 데이터베이스에만 적용됩니다. 새 풀 만들기 및 풀 속성(예: 최소 및 최대 eDTU)의 설정은 T-SQL 명령으로 변경될 수 없습니다.


> [AZURE.NOTE] 탄력적 데이터베이스 풀은 현재 미리 보기 상태이며, SQL 데이터베이스 V12 서버에서만 사용할 수 있습니다. SQL 데이터베이스 V11 서버가 있는 경우 한 단계에서 [PowerShell을 사용하여 V12로 업그레이드 및 풀 만들기](sql-database-upgrade-server-portal.md)를 할 수 있습니다.


## 탄력적 풀에 새 데이터베이스 생성
SERVICE\_OBJECTIVE 옵션과 함께 데이터베이스 만들기 명령을 사용합니다.

	CREATE DATABASE db1 ( SERVICE_OBJECTIVE = ELASTIC_POOL (name = [S3M100] ));
	-- Create a database named db1 in a pool named S3M100.

탄력적 풀에 있는 모든 데이터베이스는 탄력적 풀의 서비스 계층(기본, 표준, 프리미엄)을 상속합니다.


## 탄력적 풀 간에 데이터베이스 이동
수정으로 데이터베이스 변경 명령을 사용하고 SERVICE\_OBJECTIVE 옵션을 ELASTIC\_POOL로 설정합니다. 이름을 대상 풀의 이름으로 설정합니다.

	ALTER DATABASE db1 MODIFY ( SERVICE_OBJECTIVE = ELASTIC_POOL (name = [PM125] ));
	-- Move the database named db1 to a pool named P1M125  


## 탄력적 풀로 데이터베이스 이동 
수정으로 데이터베이스 변경 명령을 사용하고 SERVICE\_OBJECTIVE 옵션을 ELASTIC\_POOL로 설정합니다. 이름을 대상 풀의 이름으로 설정합니다.

	ALTER DATABASE db1 MODIFY ( SERVICE_OBJECTIVE = ELASTIC_POOL (name = [S3100] ));
	-- Move the database named db1 to a pool named S3100.

## 탄력적 풀 외부로 데이터베이스 이동
데이터베이스 변경 명령을 사용하고 성능 수준(S0, S1 등) 중 하나에 SERVICE\_OBJECTIVE를 설정합니다.

	ALTER DATABASE db1 MODIFY ( SERVICE_OBJECTIVE = 'S1');
	-- Changes the database into a stand-alone database with the service objective S1.

## 탄력적 풀에 데이터베이스 나열
[sys.database\_service \_objectives view](https://msdn.microsoft.com/library/mt712619)를 사용하여 탄력적 풀의 모든 데이터베이스를 나열합니다. 마스터 데이터베이스에 로그인하여 뷰를 쿼리합니다.

>[AZURE.NOTE] 현재 탄력적 풀의 데이터베이스에 대한 service\_objective\_column은 서비스 목표 문자열의 내부 토큰을 반환합니다. "ElasticPool" 문자열로 바뀝니다.
>

	SELECT d.name, slo.*  
	FROM sys.databases d 
	JOIN sys.database_service_objectives slo  
	ON d.database_id = slo.database_id
	WHERE elastic_pool_name = 'MyElasticPool'; 

## 탄력적 풀의 리소스 사용 모니터링

[sys.elastic\_pool \_resource \_stats view](https://msdn.microsoft.com/library/mt280062.aspx)를 사용하여 논리 서버에서 탄력적 풀의 리소스 사용 통계를 검사합니다. 마스터 데이터베이스에 로그인하여 뷰를 쿼리합니다.

	SELECT * FROM sys.elastic_pool_resource_stats 
	WHERE elastic_pool_name = 'MyElasticPool'
	ORDER BY end_time DESC;

## 탄력적 풀에서 데이터베이스의 리소스 사용 모니터링
[sys.dm \_resource\_stats view](https://msdn.microsoft.com/library/dn800981.aspx) 또는 [sys.resource \_stats view](https://msdn.microsoft.com/library/dn269979.aspx)를 사용하여 탄력적 풀에서 데이터베이스의 리소스 사용 통계를 검사합니다. 이 프로세스는 모든 단일 데이터베이스에 대한 리소스 사용량을 쿼리하는 경우와 비슷합니다.

## 탄력적 풀 작업의 대기 시간

- 데이터베이스당 보장된 eDTU 또는 데이터베이스당 최대 eDTU를 변경하는 작업은 일반적으로 5분 이내에 완료됩니다.
- 풀의 풀 저장소 한도를 변경하는 작업은 풀에 있는 모든 데이터베이스에서 사용 중인 공간의 전체 크기에 따라 달라집니다. 변경 시간은 100GB당 평균 90분 이하입니다. 예를 들어 풀에 있는 모든 데이터베이스에서 사용 중인 총 공간이 200GB일 경우, 풀 eDTU/저장소 용량 한도를 변경하는 예상 대기 시간은 3시간 이하입니다.

## 다음 단계

탄력적 데이터베이스 풀을 만든 후에 탄력적 작업을 만들어 풀에 있는 탄력적 데이터베이스를 관리할 수 있습니다. 탄력적 작업을 통해 개수에 관계없이 풀에 있는 데이터베이스에 대해 T-SQL 스크립트를 실행할 수 있습니다. 자세한 내용은 [탄력적 데이터베이스 작업 개요](sql-database-elastic-jobs-overview.md)를 참조하세요.

<!---HONumber=AcomDC_0413_2016-->