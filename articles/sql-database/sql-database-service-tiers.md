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
ms.date: 02/09/2017
ms.author: janeng
translationtype: Human Translation
ms.sourcegitcommit: ae230c012a17eb73c8993a32197c844c6abaa2a4
ms.openlocfilehash: 920143756a0e0b369cf839681f9c4308f77a4af0


---
# <a name="sql-database-options-and-performance-understand-whats-available-in-each-service-tier"></a>SQL 데이터베이스 옵션 및 성능: 각 서비스 계층에서 사용할 수 있는 것 이해
[Azure SQL Database](sql-database-technical-overview.md)는 여러 성능 수준 즉, **기본**, **표준**, **프리미엄** 등의&3;가지 서비스 계층을 제공하여 여러 워크로드를 다룹니다. 더 높은 성능 수준은 더욱 높은 처리량을 제공하도록 설계된 증가된 리소스를 제공합니다. 가동 중지 시간 없이 [서비스 계층 및 성능 수준을 동적으로](sql-database-service-tiers.md) 변경할 수 있습니다. 기본, 표준 및 프리미엄 서비스 계층은 모두 가동 시간 SLA가 99.99%이고 유연한 비즈니스 연속성 옵션, 보안 기능 및 시간당 대금 청구 기능을 제공합니다. 

선택한 [성능 수준](sql-database-service-tiers.md#single-database-service-tiers-and-performance-levels)에서 전용 리소스를 사용하여 단일 데이터베이스를 만들 수 있습니다. 또 데이터베이스 간에서 리소스를 공유하는 [탄력적 풀](sql-database-service-tiers.md#elastic-pool-service-tiers-and-performance-in-edtus)에서 여러 데이터베이스를 관리할 수도 있습니다. 단일 데이터베이스에 사용할 수 있는 리소스는 DTU(데이터베이스 트랜잭션 단위)의 측면에서 표현되고 탄력적 풀에 사용할 수 있는 리소스는 탄력적인 DTU(eDTU)의 측면에서 표현됩니다. DTU 및 eDTU에 대한 자세한 내용은 [DTU란?](sql-database-what-is-a-dtu.md)을 참조하세요. 

두 경우 모두 서비스 계층은 **Basic**, **Standard** 및 **Premium**을 포함합니다. 

## <a name="choosing-a-service-tier"></a>서비스 계층 선택
다음 표에서 다양한 응용 프로그램 워크로드에 가장 적합한 계층의 예제를 제공합니다.

| 서비스 계층 | 대상 워크로드 |
| :--- | --- |
| **Basic** | 일반적으로 지정된 시기에 활성 작업 한 개를 지원하는 작은 데이터베이스에 가장 적합합니다. 예를 들어 개발 또는 시험, 또는 자주 사용하지 않는 소규모 응용 프로그램에 사용되는 데이터베이스가 이에 해당합니다. |
| **Standard** |중저 IO 성능 요구 사항의 클라우드 응용 프로그램에 적합한 옵션으로, 여러 개의 동시 쿼리를 지원합니다. 예를 들어 작업 그룹 또는 웹 응용 프로그램이 이 계층에 적합한 예입니다. |
| **Premium** | 높은 IO 성능 요구 사항의 높은 트랜잭션 볼륨용으로 설계되었으며, 많은 동시 사용자를 지원합니다. 예를 들어 업무 필수 응용 프로그램을 지원하는 데이터베이스가 이 계층에 적합합니다. |

먼저 단일 데이터베이스를 실행할지 또는 리소스를 공유하는 데이터베이스를 그룹화할지 결정합니다. [탄력적 풀 고려 사항](sql-database-elastic-pool-guidance.md)을 검토합니다. 서비스 계층을 결정하려면 필요한 최소 데이터베이스 기능을 결정하고 시작합니다.

* 개별 데이터베이스의 최대 데이터베이스 크기(고성능 수준에서 기본 2GB, 표준 250GB 및 프리미엄 500GB-1TB)
* 탄력적 풀의 최대 총 저장소(기본 117GB, 표준 1200GB 및 프리미엄 750GB)
* 풀당 데이터베이스 수(기본 400, 표준 400 및 프리미엄 50)
* 데이터베이스 백업 보존 기간(기본 7일, 표준 및 프리미엄 35일)

최소 서비스 계층을 결정하면 데이터베이스에 대한 성능 수준을 결정할 준비가 되었습니다(DTU의 수). 표준 S2 및 S3 성능 수준이 좋은 시작점인 경우가 많습니다. 높은 CPU 또는 IO 요구 사항의 데이터베이스의 경우 프리미엄 성능 수준이 적합한 시작점입니다. 프리미엄은 더 많은 CPU를 제공하고 높은 표준 성능 수준에 비해 10배 이상의 IO에서 시작합니다.

## <a name="single-database-service-tiers-and-performance-levels"></a>단일 데이터베이스 서비스 계층 및 성능 수준
단일 데이터베이스의 경우 각 서비스 계층 내에는 여러 성능 수준이 있습니다. 워크로드 요구에 가장 잘 맞는 수준을 선택할 수 있는 유연성이 있습니다. 규모를 확장 또는 축소해야 하는 경우는 데이터베이스의 계층을 간편하게 변경할 수 있습니다. 자세한 내용은 [데이터베이스 서비스 계층 및 성능 수준 변경](sql-database-service-tiers.md) 을 참조하세요.

호스팅된 데이터베이스 수에 관계 없이, 데이터베이스는 보장된 리소스 집합을 가져오며 데이터베이스의 예상되는 성능 특징은 영향을 받지 않습니다.

[!INCLUDE [SQL DB service tiers table](../../includes/sql-database-service-tiers-table.md)]

> [!NOTE]
> 이 서비스 계층 테이블에서 다른 모든 행에 대한 자세한 설명은 [서비스 계층 기능 및 제한](sql-database-performance-guidance.md#service-tier-capabilities-and-limits)을 참조하세요.
> 

## <a name="scaling-up-or-scaling-down-a-single-database"></a>단일 데이터베이스 확장 및 축소

처음으로 서비스 계층 및 성능 수준을 선택한 후에 단일 데이터베이스를 실제 환경에 따라 동적으로 확장 또는 축소할 수 있습니다. 

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
> 자세한 단계는 [Azure Portal을 사용하여 단일 데이터베이스 관리](sql-database-manage-single-databases-portal.md), [PowerShell을 사용하여 단일 데이터베이스 관리](sql-database-manage-single-databases-powershell.md) 또는 [Transact-SQL을 사용하여 단일 데이터베이스 관리](sql-database-manage-single-databases-tsql.md)를 참조하세요.
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

> [!IMPORTANT]
> 자세한 단계는 [Azure Portal을 사용하여 탄력적 풀 관리](sql-database-elastic-pool-manage-portal.md), [PowerShell을 사용하여 탄력적 풀 관리](sql-database-elastic-pool-manage-powershell.md), [Transact-SQL을 사용하여 탄력적 풀 관리](sql-database-elastic-pool-manage-tsql.md) 또는 [C#을 사용하여 탄력적 풀 관리](sql-database-elastic-pool-manage-csharp.md)를 참조하세요.
>

## <a name="next-steps"></a>다음 단계

* [탄력적 풀](sql-database-elastic-pool-guidance.md) 및 [탄력적 풀에 대한 가격 및 성능 고려 사항](sql-database-elastic-pool-guidance.md)에 대해 자세히 알아봅니다.
* [탄력적 풀을 모니터링, 관리 및 크기 조정](sql-database-elastic-pool-manage-portal.md)하고 [단일 데이터베이스의 성능을 모니터링](sql-database-single-database-monitor.md)하는 방법에 대해 알아봅니다.
* SQL Database 계층에 대해 알아 보았으면 [무료 계정](https://azure.microsoft.com/pricing/free-trial/)을 사용해보고 [첫 번째 SQL Database를 만드는 방법](sql-database-get-started.md)에 대해 알아보세요.
* 마이그레이션 시나리오의 경우 [DTU 계산기](http://dtucalculator.azurewebsites.net/)를 사용하여 필요한 DTU의 수를 대략적으로 예상합니다. 




<!--HONumber=Feb17_HO3-->


