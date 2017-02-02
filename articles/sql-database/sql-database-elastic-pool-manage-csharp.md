---
title: "C#으로 탄력적 풀 모니터링 및 관리 | Microsoft Docs"
description: "C# 데이터베이스 개발 기술을 사용하여 Azure SQL Database 탄력적 풀을 관리합니다."
services: sql-database
documentationcenter: 
author: stevestein
manager: jhubbard
editor: 
ms.assetid: f6e6ff3b-6b60-43c1-afe9-575991e38237
ms.service: sql-database
ms.custom: multiple databases
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: csharp
ms.workload: data-management
ms.date: 10/04/2016
ms.author: sstein
translationtype: Human Translation
ms.sourcegitcommit: 145cdc5b686692b44d2c3593a128689a56812610
ms.openlocfilehash: a6d5b0ccb1b160be4f8f3c09814202634759c8c6


---
# <a name="monitor-and-manage-an-elastic-pool-with-cx23"></a>C&#x23;으로 탄력적 풀 모니터링 및 관리
> [!div class="op_single_selector"]
> * [Azure Portal](sql-database-elastic-pool-manage-portal.md)
> * [PowerShell](sql-database-elastic-pool-manage-powershell.md)
> * [C#](sql-database-elastic-pool-manage-csharp.md)
> * [T-SQL](sql-database-elastic-pool-manage-tsql.md)
> 
> 

C&#x23;을 사용하여 [탄력적 풀](sql-database-elastic-pool.md)을 관리하는 방법을 알아봅니다. 

> [!NOTE]
> SQL Database의 여러 새로운 기능은 [Azure Resource Manager 배포 모델](../azure-resource-manager/resource-group-overview.md)을 사용할 때 지원되므로 최신 **.NET용 SQL Database 관리 라이브러리([문서](https://msdn.microsoft.com/library/azure/mt349017.aspx) | [NuGet 패키지](https://www.nuget.org/packages/Microsoft.Azure.Management.Sql))**를 사용해야 합니다. 이전 [클래식 배포 모델 기반 라이브러리](https://www.nuget.org/packages/Microsoft.WindowsAzure.Management.Sql)가 이전 버전과만 호환되므로 최신 Resource Manager 기반 라이브러리를 사용하는 것이 좋습니다.
> 
> 

이 문서의 단계를 완료하려면 다음 항목이 필요합니다.

* 탄력적 풀(관리하려는 풀). 풀을 만들려면 [C#으로 탄력적 풀 만들기](sql-database-elastic-pool-create-csharp.md)를 참조하세요.
* 있습니다. Visual Studio의 무료 버전은 [Visual Studio 다운로드](https://www.visualstudio.com/downloads/download-visual-studio-vs) 페이지를 참조하세요.

## <a name="move-a-database-into-an-elastic-pool"></a>탄력적 풀로 데이터베이스 이동
풀 안팎에서 독립 실행형 데이터베이스를 이동할 수 있습니다.  

    // Retrieve current database properties.

    currentDatabase = sqlClient.Databases.Get("resourcegroup-name", "server-name", "Database1").Database;

    // Configure create or update parameters with existing property values, override those to be changed.
    DatabaseCreateOrUpdateParameters updatePooledDbParameters = new DatabaseCreateOrUpdateParameters()
    {
        Location = currentDatabase.Location,
        Properties = new DatabaseCreateOrUpdateProperties()
        {
            Edition = "Standard",
            RequestedServiceObjectiveName = "ElasticPool",
            ElasticPoolName = "ElasticPool1",
            MaxSizeBytes = currentDatabase.Properties.MaxSizeBytes,
            Collation = currentDatabase.Properties.Collation,
        }
    };

    // Update the database.
    var dbUpdateResponse = sqlClient.Databases.CreateOrUpdate("resourcegroup-name", "server-name", "Database1", updatePooledDbParameters);

## <a name="list-databases-in-an-elastic-pool"></a>탄력적 풀에 데이터베이스 나열
풀에 있는 모든 데이터베이스를 검색하려면 [ListDatabases](https://msdn.microsoft.com/library/microsoft.azure.management.sql.elasticpooloperationsextensions.listdatabases) 메서드를 호출합니다.

    //List databases in the elastic pool
    DatabaseListResponse dbListInPool = sqlClient.ElasticPools.ListDatabases("resourcegroup-name", "server-name", "ElasticPool1");
    Console.WriteLine("Databases in Elastic Pool {0}", "server-name.ElasticPool1");
    foreach (Database db in dbListInPool)
    {
        Console.WriteLine("  Database {0}", db.Name);
    }

## <a name="change-performance-settings-of-a-pool"></a>풀의 성능 설정 변경
기존 풀 속성을 검색합니다. 값을 수정하고 CreateOrUpdate 메서드를 실행합니다.

    var currentPool = sqlClient.ElasticPools.Get("resourcegroup-name", "server-name", "ElasticPool1").ElasticPool;

    // Configure create or update parameters with existing property values, override those to be changed.
    ElasticPoolCreateOrUpdateParameters updatePoolParameters = new ElasticPoolCreateOrUpdateParameters()
    {
        Location = currentPool.Location,
        Properties = new ElasticPoolCreateOrUpdateProperties()
        {
            Edition = currentPool.Properties.Edition,
            DatabaseDtuMax = 50, /* Setting DatabaseDtuMax to 50 limits the eDTUs that any one database can consume */
            DatabaseDtuMin = 10, /* Setting DatabaseDtuMin above 0 limits the number of databases that can be stored in the pool */
            Dtu = (int)currentPool.Properties.Dtu,
            StorageMB = currentPool.Properties.StorageMB,  /* For a Standard pool there is 1 GB of storage per eDTU. */
        }
    };

    newPoolResponse = sqlClient.ElasticPools.CreateOrUpdate("resourcegroup-name", "server-name", "ElasticPool1", newPoolParameters);


## <a name="latency-of-elastic-pool-operations"></a>탄력적 풀 작업의 대기 시간
* 데이터베이스당 최소 eDTU 또는 데이터베이스당 최대 eDTU를 변경하는 작업은 일반적으로 5분 이내에 완료됩니다.
* 풀 크기를 변경하는 시간(eDTU)은 풀에 있는 모든 데이터베이스의 총 크기에 따라 달라집니다. 변경 시간은 100GB당 평균 90분 이하입니다. 예를 들어 풀에 있는 모든 데이터베이스의 총 공간이 200GB일 경우, 풀당 풀 eDTU를 변경하는 예상 대기 시간은 3시간 이하입니다.

## <a name="additional-resources"></a>추가 리소스
* [SQL Database 클라이언트 응용 프로그램의 SQL 오류 코드: 데이터베이스 연결 오류 및 기타 문제](sql-database-develop-error-messages.md)
* [SQL 데이터베이스](https://azure.microsoft.com/documentation/services/sql-database/)
* [Azure 리소스 관리 API](https://msdn.microsoft.com/library/azure/dn948464.aspx)
* [C#을 사용하여 새 탄력적 풀 만들기](sql-database-elastic-pool-create-csharp.md)
* [탄력적 풀을 사용해야 하는 경우](sql-database-elastic-pool-guidance.md)
* [Azure SQL 데이터베이스를 사용하여 규모 확장](sql-database-elastic-scale-introduction.md)참조: 탄력적 데이터베이스 도구를 사용하여 규모를 확장하거나 데이터를 이동하거나 쿼리 또는 트랜잭션을 만듭니다.




<!--HONumber=Dec16_HO2-->


