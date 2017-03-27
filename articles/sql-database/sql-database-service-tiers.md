---
title: "SQL Database 성능: 서비스 계층 | Microsoft Docs"
description: "SQL Database 서비스 계층을 비교합니다."
keywords: "데이터베이스 옵션, 데이터베이스 성능"
services: sql-database
documentationcenter: 
author: janeng
manager: jhubbard
editor: 
ms.assetid: f5c5c596-cd1e-451f-92a7-b70d4916e974
ms.service: sql-database
ms.custom: overview
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: data-management
wms.date: 03/06/2017
ms.author: janeng
translationtype: Human Translation
ms.sourcegitcommit: 8a531f70f0d9e173d6ea9fb72b9c997f73c23244
ms.openlocfilehash: 4307797b3961d8efef4045590e340268f0ad226d
ms.lasthandoff: 03/10/2017


---
# <a name="sql-database-options-and-performance-understand-whats-available-in-each-service-tier"></a>SQL 데이터베이스 옵션 및 성능: 각 서비스 계층에서 사용할 수 있는 것 이해

[Azure SQL Database](sql-database-technical-overview.md)는 네 가지 서비스 계층, 즉 **Basic**, **Standard**, **Premium** 및 **Premium RS**를 제공합니다. 각 서비스 계층은 여러 워크로드를 다룰 여러 성능 수준을 제공합니다. 더 높은 성능 수준은 더욱 높은 처리량을 제공하도록 설계된 추가 리소스를 제공합니다. 가동 중지 시간 없이 서비스 계층 및 성능 수준을 동적으로 변경할 수 있습니다. 기본, 표준 및 프리미엄 서비스 계층은 모두 가동 시간 SLA가 99.99%이고 유연한 비즈니스 연속성 옵션, 보안 기능 및 시간당 대금 청구 기능을 제공합니다. Premium RS 계층은 SLA는 감소하더라도 Premium 계층과 동일한 수준의 성능, 보안 기능 및 비즈니스 연속성 기능을 제공합니다.

> [!IMPORTANT]
> Premium RS 데이터베이스는 Premium 또는 Standard 데이터베이스보다 낮은 수의 중복 복사본으로 실행됩니다. 따라서 서비스 오류가 발생하면 최대 5분 간격으로 백업에서 데이터베이스를 복구할 수 있어야 합니다.
>

특정 [성능 수준](sql-database-service-tiers.md#single-database-service-tiers-and-performance-levels)의 서비스 계층 내에서 전용 리소스를 사용하여 단일 데이터베이스를 만들 수 있습니다. 또 여러 데이터베이스 간에 리소스를 공유하는 [탄력적 풀](sql-database-service-tiers.md#elastic-pool-service-tiers-and-performance-in-edtus)에서 데이터베이스를 만들 수도 있습니다. 단일 데이터베이스에 사용할 수 있는 리소스는 DTU(데이터베이스 트랜잭션 단위)의 측면에서 표현되고 탄력적 풀에 사용할 수 있는 리소스는 eDTU(탄력적 데이터베이스 트랜잭션 단위)의 측면에서 표현됩니다. DTU 및 eDTU에 대한 자세한 내용은 [DTU란?](sql-database-what-is-a-dtu.md)을 참조하세요. 

## <a name="choosing-a-service-tier"></a>서비스 계층 선택
다음 표에서 다양한 응용 프로그램 워크로드에 가장 적합한 계층의 예제를 제공합니다.

| 서비스 계층 | 대상 워크로드 |
| :--- | --- |
| **Basic** | 일반적으로 지정된 시기에 활성 작업 한 개를 지원하는 작은 데이터베이스에 가장 적합합니다. 예를 들어 개발 또는 시험, 또는 자주 사용하지 않는 소규모 응용 프로그램에 사용되는 데이터베이스가 이에 해당합니다. |
| **Standard** |중저 IO 성능 요구 사항의 클라우드 응용 프로그램에 적합한 옵션으로, 여러 개의 동시 쿼리를 지원합니다. 예를 들어 작업 그룹 또는 웹 응용 프로그램이 이 계층에 적합한 예입니다. |
| **Premium** | 높은 IO 성능 요구 사항의 높은 트랜잭션 볼륨용으로 설계되었으며, 많은 동시 사용자를 지원합니다. 예를 들어 업무 필수 응용 프로그램을 지원하는 데이터베이스가 이 계층에 적합합니다. |
| **Premium RS** | IO 집약적 작업을 진행하지만 최고 가용성을 보장할 필요는 없는 경우를 위해 설계되었습니다. 예제에는 데이터베이스가 레코드 시스템이 아닌 고성능 작업 또는 분석 작업 테스트가 포함됩니다. |
|||

먼저 지정된 양의 전용 리소스를 사용하여 단일 데이터베이스를 실행하려고 하는지 아니면 데이터베이스 그룹에서 리소스 풀을 공유하려고 하는지를 결정합니다. [탄력적 풀 고려 사항](sql-database-elastic-pool-guidance.md)을 검토합니다. 서비스 계층을 결정하려면 필요한 최소 데이터베이스 기능을 결정하고 시작합니다.

| **서비스 계층 기능** | **Basic** | **Standard** | **Premium** | **Premium RS**|
| :-- | --: | --: | --: | --: |
| 개별 데이터베이스 최대 크기 | 2 GB | 250GB | 4TB*  | 500GB  |
| 탄력적 풀의 최대 총 저장소 | 117GB | 1200GB | 750GB | 750GB |
| 풀당 최대 데이터베이스 수 | 400  | 400 | 50 | 50 |
| 데이터베이스 백업 보존 기간 | 7 일 | 35일 | 35일 | 35일 |
||||||

> [!IMPORTANT]
> P11 및 P15 성능 수준을 사용하는 고객은 추가 비용 없이 최대 4TB의 포함된 저장소를 사용할 수 있습니다. 이 4TB 옵션은 미국 동부2, 미국 서부, 유럽 서부, 동남 아시아, 일본 동부, 오스트레일리아 동부, 캐나다 중부 및 캐나다 동부에서 현재 공개 미리 보기 상태로 제공됩니다. 현재 제한 사항에 대해서는 [현재 4TB 제한 사항](sql-database-service-tiers.md#current-limitations-of-p11-and-p15-databases-with-4-tb-maxsize)을 참조하세요.
>

최소 서비스 계층을 결정하면 데이터베이스에 대한 성능 수준을 결정할 준비가 되었습니다(DTU의 수). 표준 S2 및 S3 성능 수준이 좋은 시작점인 경우가 많습니다. 높은 CPU 또는 IO 요구 사항의 데이터베이스의 경우 프리미엄 성능 수준이 적합한 시작점입니다. 프리미엄은 더 많은 CPU를 제공하고 높은 표준 성능 수준에 비해 10배 이상의 IO에서 시작합니다.

## <a name="single-database-service-tiers-and-performance-levels"></a>단일 데이터베이스 서비스 계층 및 성능 수준
단일 데이터베이스의 경우 각 서비스 계층 내에는 여러 성능 수준이 있습니다. [Azure Portal](sql-database-manage-single-databases-portal.md), [PowerShell](sql-database-manage-single-databases-powershell.md), [Transact-SQL](sql-database-manage-single-databases-tsql.md), C# 및 REST API를 사용하여 워크로드 요구에 가장 잘 맞는 수준을 선택할 수 있는 유연성이 있습니다. 

호스팅된 데이터베이스 수에 관계 없이, 데이터베이스는 보장된 리소스 집합을 가져오며 데이터베이스의 예상되는 성능 특징은 영향을 받지 않습니다.

[!INCLUDE [SQL DB service tiers table](../../includes/sql-database-service-tiers-table.md)]

> [!NOTE]
> 이 서비스 계층 테이블에서 다른 모든 행에 대한 자세한 설명은 [서비스 계층 기능 및 제한](sql-database-performance-guidance.md#service-tier-capabilities-and-limits)을 참조하세요.
> 

## <a name="scaling-up-or-scaling-down-a-single-database"></a>단일 데이터베이스 확장 및 축소

처음으로 서비스 계층 및 성능 수준을 선택한 후에 단일 데이터베이스를 실제 환경에 따라 동적으로 확장 또는 축소할 수 있습니다. 규모를 확장 또는 축소해야 하는 경우는 [Azure Portal](sql-database-manage-single-databases-portal.md), [PowerShell](sql-database-manage-single-databases-powershell.md), [Transact-SQL](sql-database-manage-single-databases-tsql.md), C# 및 REST API를 사용하여 Azure Portal에서 데이터베이스의 계층을 간편하게 변경할 수 있습니다. 

> [!VIDEO https://channel9.msdn.com/Blogs/Azure/Azure-SQL-Database-dynamically-scale-up-or-scale-down/player]
>

서비스 계층 및/또는 데이터베이스의 성능 수준을 변경하게 되면 새 성능 수준에서 원본 데이터베이스의 복제본을 만들고 연결을 복제본으로 전환합니다. 이 프로세스 중에 데이터가 손실되지는 않지만 복제본으로 전환할 경우 잠깐 동안 데이터베이스에 대한 연결이 비활성화되므로 비행의 일부 트랜잭션이 롤백될 수 있습니다. 하지만 평균 4초 이내 또 사례의 99% 이상에서 30초 미만으로 이 창이 달라집니다. 현재 연결에서 비행의 트랜잭션 다수를 사용할 수 없는 경우 이 창을 더 이상 유지할 수 없습니다.  

전체 확장 프로세스 기간은 변경 전후 데이터베이스의 크기 및 서비스 계층에 따라 달라집니다. 예를 들어 표준 서비스 계층 내에서 변경되고 있는 250GB 데이터베이스는 6시간 내에 완료되어야 합니다. 프리미엄 서비스 계층 내의 성능 수준을 변경하고 있는 동일한 크기의 데이터베이스의 경우 3시간 이내에 완료되어야 합니다.

* 데이터베이스를 다운그레이드하려면 데이터베이스가 대상 서비스 계층의 최대 허용 크기보다 작아야 합니다. 
* [지역에서 복제](sql-database-geo-replication-portal.md) 를 사용할 수 있는 데이터베이스를 업그레이드하는 경우에는 주 데이터베이스를 업그레이드하기 전에 먼저 해당 보조 데이터베이스를 원하는 성능 계층으로 업그레이드해야 합니다.
* 프리미엄 서비스 계층에서 다운그레이드하는 경우는 먼저 모든 지역에서 복제 관계를 종료해야 합니다. [가동 중단에서 복구](sql-database-disaster-recovery.md) 토픽에 설명된 단계에 따라 주 데이터베이스와 활성 보조 데이터베이스 간의 복제 프로세스를 중지할 수 있습니다.
* 복원 서비스는 여러 서비스 계층에서 서로 다르게 제공됩니다. 다운그레이드하는 경우 지정 시간으로 복원하는 기능을 잃게 되거나 백업 보존 기간이 단축될 수 있습니다. 자세한 내용은 [Azure SQL 데이터베이스 백업 및 복원](sql-database-business-continuity.md)을 참조하세요.
* 데이터베이스의 새로운 속성은 변경이 완료될 때까지 적용되지 않습니다.

> [!IMPORTANT]
> 자세한 단계는 [Azure Portal에서 단일 데이터베이스 관리](sql-database-manage-single-databases-portal.md), [PowerShell을 사용하여 단일 데이터베이스 관리](sql-database-manage-single-databases-powershell.md) 또는 [Transact-SQL을 사용하여 단일 데이터베이스 관리](sql-database-manage-single-databases-tsql.md)를 참조하세요.
>

## <a name="elastic-pool-service-tiers-and-performance-in-edtus"></a>탄력적 풀 서비스 계층 및 eDTU의 성능

풀을 사용하여 데이터베이스가 풀 내의 각 데이터베이스에 특정 성능 수준을 할당할 필요 없이 eDTU 리소스를 공유하고 사용할 수 있습니다. 예를 들어 표준 풀의 단일 데이터베이스에서 eDTU를 0개 사용하다가 풀을 구성할 때 설정한 최대 데이터베이스 eDTU 사용으로 전환할 수 있습니다. 풀을 사용하여 다양한 워크로드를 가진 복수의 데이터베이스에서 전체 풀에 사용 가능한 eDTU 리소스를 효율적으로 사용할 수 있습니다. 자세한 내용은 [탄력적 풀의 가격 및 성능 고려 사항](sql-database-elastic-pool-guidance.md) 을 참조하세요.

다음 표에서는 풀 서비스 계층의 특성을 설명합니다.

[!INCLUDE [SQL DB service tiers table for elastic pools](../../includes/sql-database-service-tiers-table-elastic-pools.md)]

또한 풀 내의 각 데이터베이스는 해당 계층에 대한 단일 데이터베이스 특성을 준수합니다. 예를 들어 Basic 풀의 최대 세션 수 한계는 풀당 4800 ~ 28800개이지만, Basic 풀 내 개별 데이터베이스의 데이터베이스 세션 한계는 300개입니다.

## <a name="scaling-up-or-scaling-down-an-elastic-pool"></a>탄력적 풀 확장 및 축소

처음으로 서비스 계층 및 성능 수준을 선택한 후에 탄력적 풀을 실제 환경에 따라 동적으로 확장 또는 축소할 수 있습니다. 

* 데이터베이스당 최소 eDTU 또는 데이터베이스당 최대 eDTU를 변경하는 작업은 일반적으로&5;분 이내에 완료됩니다.
* 풀 크기를 변경하는 시간(eDTU)은 풀에 있는 모든 데이터베이스의 총 크기에 따라 달라집니다. 변경 시간은 100GB당 평균 90분 이하입니다. 예를 들어 풀에 있는 모든 데이터베이스의 총 공간이 200GB일 경우, 풀당 풀 eDTU를 변경하는 예상 대기 시간은 3시간 이하입니다.

자세한 단계에 대해서는 [Azure Portal에서 탄력적 풀 관리](sql-database-elastic-pool-manage-portal.md), [Powershell을 사용하여 탄력적 풀 관리](sql-database-elastic-pool-manage-powershell.md), [Transact-SQL을 사용하여 탄력적 풀 관리](sql-database-elastic-pool-manage-tsql.md) 또는 [C#을 사용하여 탄력적 풀 관리](sql-database-elastic-pool-manage-csharp.md)를 참조하세요.

## <a name="creating-or-upgrading-to-4tb"></a>만들기 또는 4TB로 업그레이드

다음 섹션에서는 4TB 옵션에 대한 구현 세부 사항에 대해 설명합니다.

### <a name="creating-in-the-azure-portal"></a>Azure Portal에서 만들기

P11/P15를 만들 때 1TB의 기본 저장소 옵션이 미리 선택되어 있습니다. 지원되는 하위 지역 중 하나에 있는 데이터베이스의 경우 저장소 최대 크기를 4TB로 늘릴 수 있습니다. 다른 모든 하위 지역에서는 저장소 슬라이더를 변경할 수 없습니다. 4TB의 포함된 저장소를 선택하면 가격이 변경되지 않습니다.

### <a name="creating-using-powershell-or-transact-sql"></a>PowerShell 또는 Transact-SQL을 사용하여 만들기

P11/P15 데이터베이스를 만들 때 최대 크기 값을 1TB(기본값) 또는 4TB로 설정할 수 있습니다. 값 '1024GB' 및 '4096GB'도 허용됩니다. 4TB의 최대 크기 옵션을 선택하면 데이터베이스는 지원되지 않는 하위 지역에서 프로비전된 경우에도 create 명령이 오류를 나타내며 실패합니다.

### <a name="upgrading-to-4tb"></a>4TB로 업그레이드 

지원되는 하위 지역 중 하나에 있는 기존 P11 및 P15 데이터베이스의 경우 저장소 최대 크기를 4TB로 늘릴 수 있습니다. 이 작업은 Azure Portal, PowerShell 또는 Transact-SQL로 수행할 수 있습니다. 다음 예제에서는 ALTER DATABASE 명령을 사용하여 변경되는 최대 크기를 보여 줍니다.

```ALTER DATABASE <DatabaseName> MODIFY (MAXSIZE = 4096 GB);
```

Upgrading an existing P11 or P15 database can only be performed by a server-level principal login or by members of the dbmanager database role. 
If executed in a supported region the configuration will be updated immediately. This can be checked using the [SELECT DATABASEPROPERTYEX](https://msdn.microsoft.com/library/ms186823.aspx) or by inspecting the database size in the Azure portal. The database will remain online during the upgrade process. However, you will not be able to utilize the full 4 TB of storage until the actual database files have been upgraded to the new maxsize. The length of time required depends upon on the size of the database being upgraded.  

### Error messages
When creating or upgrading an P11/P15 database in an unsupported region, the create or upgrade operation will fail with the following error message: **P11 and P15 database with up to 4TB of storage are available in US East 2, West US, South East Asia, West Europe, Canada East, Canada Central, Japan East, and Australia East.**

## Current limitations of P11 and P15 databases with 4 TB maxsize

- When creating or updating a P11 or P15 database, you can only chose between 1 TB and 4 TB maxsize. Intermediate storage sizes are not currently supported.
- The 4 TB database maxsize cannot be changed to 1 TB even if the actual storage used is below 1 TB. Thus, you cannot downgrade a P11-4TB/P15-4TB to a P11-1TB/P15-1TB or a lower performance tier (e.g., to P1-P6) until we are providing additional storage options for the rest of the performance tiers. This restriction also applies to the restore and copy scenarios including point-in-time, geo-restore, long-term-backup-retention, and database copy. Once a database is configured with the 4 TB option, all restore operations of this database must be into a P11/P15 with 4 TB maxsize.
- For Active Geo-Replication scenarios:
   - Setting up a geo-replication relationship: If the primary database is P11 or P15, the secondary(ies) must also be P11 or P15; lower performance tiers will be rejected as secondaries since they are not capable of supporting 4 TB.
   - Upgrading the primary database in a geo-replication relationship: Changing the maxsize to 4 TB on a primary database will trigger the same change on the secondary database. Both upgrades must be successful for the change on the primary to take effect. Region limitations for the 4TB option apply (see above). If the secondary is in a region that does not support 4 TB, the primary will not be upgraded.
- Using the Import/Export service for loading P11-4TB/P15-4TB databases is not supported. Use SqlPackage.exe to [import](sql-database-import-sqlpackage.md) and [export](sql-database-export-sqlpackage.md) data.

## Next steps

* Learn the details of [elastic pools](sql-database-elastic-pool-guidance.md) and [price and performance considerations for elastic pools](sql-database-elastic-pool-guidance.md).
* Learn how to [Monitor, manage, and resize elastic pools](sql-database-elastic-pool-manage-portal.md) and [Monitor the performance of single databases](sql-database-single-database-monitor.md).
* Now that you know about the SQL Database tiers, try them out with a [free account](https://azure.microsoft.com/pricing/free-trial/) and learn [how to create your first SQL database](sql-database-get-started.md).
* For migration scenarios, use the [DTU Calculator](http://dtucalculator.azurewebsites.net/) to approximate the number of DTUs needed. 


