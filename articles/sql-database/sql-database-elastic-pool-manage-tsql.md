---
title: "T-SQL: Azure SQL Database 탄력적 풀 관리 | Microsoft Docs"
description: "T-SQL을 사용하여 Azure SQL Database 탄력적 풀을 관리합니다."
services: sql-database
documentationcenter: 
author: srinia
manager: jhubbard
editor: 
ms.assetid: 4e288e17-bc3e-4255-9fbe-0a2ac0dbd7dd
ms.service: sql-database
ms.custom: multiple databases
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: data-management
ms.date: 05/27/2016
ms.author: srinia
translationtype: Human Translation
ms.sourcegitcommit: 6453cca9f876e6c363fbed463263c0f9684a3e70
ms.openlocfilehash: 11b1a6dd9ecaaa3abaf088b1fed73aae783252f5
ms.lasthandoff: 02/08/2017


---
# <a name="monitor-and-manage-an-elastic-pool-with-transact-sql"></a>Transact-SQL로 탄력적 풀 모니터링 및 관리
이 항목에서는 Transact-SQL을 사용하여 확장 가능한 [탄력적 풀](sql-database-elastic-pool.md)을 관리하는 방법을 보여 줍니다.  [Azure Portal](https://portal.azure.com/), [PowerShell](sql-database-elastic-pool-manage-powershell.md), REST API 또는 [C#][C#을 사용하여 탄력적 풀 만들기 및 관리](sql-database-elastic-pool-manage-csharp.md)을 사용하여 Azure 탄력적 풀을 만들고 관리할 수도 있습니다. [Transact-SQL](sql-database-elastic-pool-manage-tsql.md)을 사용하여 탄력적 풀을 만들고 여기에 데이터베이스를 넣거나 뺄 수도 있습니다.


[데이터베이스 만들기(Azure SQL Database)](https://msdn.microsoft.com/library/dn268335.aspx) 및 [데이터베이스 변경(Azure SQL Database)](https://msdn.microsoft.com/library/mt574871.aspx) 명령을 사용하여 데이터베이스를 만들고 탄력적 풀의 내부 및 외부로 이동시킵니다. 탄력적 풀은 이러한 명령을 사용하기 전에 있어야 합니다. 이러한 명령은 데이터베이스에만 적용됩니다. 새 풀 만들기 및 풀 속성(예: 최소 및 최대 eDTU)의 설정은 T-SQL 명령으로 변경될 수 없습니다.

## <a name="create-a-pooled-database-in-an-elastic-pool"></a>탄력적 풀에 풀링된 데이터베이스 만들기
SERVICE_OBJECTIVE 옵션과 함께 데이터베이스 만들기 명령을 사용합니다.   

    CREATE DATABASE db1 ( SERVICE_OBJECTIVE = ELASTIC_POOL (name = [S3M100] ));
    -- Create a database named db1 in an elastic named S3M100.

탄력적 풀에 있는 모든 데이터베이스는 탄력적 풀의 서비스 계층(기본, 표준, 프리미엄)을 상속합니다. 

## <a name="move-a-database-between-elastic-pools"></a>탄력적 풀 간에 데이터베이스 이동
수정으로 데이터베이스 변경 명령을 사용하고 SERVICE\_OBJECTIVE 옵션을 ELASTIC\_POOL로 설정합니다. 이름을 대상 풀의 이름으로 설정합니다.

    ALTER DATABASE db1 MODIFY ( SERVICE_OBJECTIVE = ELASTIC_POOL (name = [PM125] ));
    -- Move the database named db1 to an elastic named P1M125  

## <a name="move-a-database-into-an-elastic-pool"></a>탄력적 풀로 데이터베이스 이동
수정으로 데이터베이스 변경 명령을 사용하고 SERVICE\_OBJECTIVE 옵션을 ELASTIC_POOL로 설정합니다. 이름을 대상 풀의 이름으로 설정합니다.

    ALTER DATABASE db1 MODIFY ( SERVICE_OBJECTIVE = ELASTIC_POOL (name = [S3100] ));
    -- Move the database named db1 to an elastic named S3100.

## <a name="move-a-database-out-of-an-elastic-pool"></a>탄력적 풀 외부로 데이터베이스 이동
데이터베이스 변경 명령을 사용하고 성능 수준(예: S0 또는 S1) 중 하나에 SERVICE_OBJECTIVE를 설정합니다.

    ALTER DATABASE db1 MODIFY ( SERVICE_OBJECTIVE = 'S1');
    -- Changes the database into a stand-alone database with the service objective S1.

## <a name="list-databases-in-an-elastic-pool"></a>탄력적 풀에 데이터베이스 나열
[sys.database\_service \_objectives view](https://msdn.microsoft.com/library/mt712619)를 사용하여 탄력적 풀의 모든 데이터베이스를 나열합니다. 마스터 데이터베이스에 로그인하여 뷰를 쿼리합니다.

    SELECT d.name, slo.*  
    FROM sys.databases d 
    JOIN sys.database_service_objectives slo  
    ON d.database_id = slo.database_id
    WHERE elastic_pool_name = 'MyElasticPool'; 

## <a name="get-resource-usage-data-for-an-elastic-pool"></a>탄력적 풀에 대한 리소스 사용량 데이터 가져오기
[sys.elastic\_pool \_resource \_stats view](https://msdn.microsoft.com/library/mt280062.aspx)를 사용하여 논리 서버에서 탄력적 풀의 리소스 사용 통계를 검사합니다. 마스터 데이터베이스에 로그인하여 뷰를 쿼리합니다.

    SELECT * FROM sys.elastic_pool_resource_stats 
    WHERE elastic_pool_name = 'MyElasticPool'
    ORDER BY end_time DESC;

## <a name="get-resource-usage-for-a-pooled-database"></a>풀링된 데이터베이스에 대한 리소스 사용량 가져오기
[sys.dm\_ db\_ resource\_stats view](https://msdn.microsoft.com/library/dn800981.aspx) 또는 [sys.resource \_stats view](https://msdn.microsoft.com/library/dn269979.aspx)를 사용하여 탄력적 풀에서 데이터베이스의 리소스 사용 통계를 검사합니다. 이 프로세스는 단일 데이터베이스에 대한 리소스 사용량을 쿼리하는 경우와 비슷합니다.

## <a name="next-steps"></a>다음 단계
탄력적 풀을 만든 후에 탄력적 작업을 만들어 풀에 있는 탄력적 데이터베이스를 관리할 수 있습니다. 탄력적 작업을 통해 개수에 관계없이 풀에 있는 데이터베이스에 대해 T-SQL 스크립트를 실행할 수 있습니다. 자세한 내용은 [탄력적 데이터베이스 작업 개요](sql-database-elastic-jobs-overview.md)를 참조하세요. 

[Azure SQL 데이터베이스를 사용하여 규모 확장](sql-database-elastic-scale-introduction.md)참조: 탄력적 데이터베이스 도구를 사용하여 규모를 확장하거나 데이터를 이동하거나 쿼리 또는 트랜잭션을 만듭니다.


