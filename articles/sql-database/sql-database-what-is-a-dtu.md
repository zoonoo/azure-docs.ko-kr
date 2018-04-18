---
title: 'SQL Database: DTU란? | Microsoft Docs'
description: Azure SQL Database 트랜잭션 단위가 무엇인지 이해합니다.
keywords: 데이터베이스 옵션, 데이터베이스 성능
services: sql-database
author: CarlRabeler
manager: craigg
ms.service: sql-database
ms.custom: DBs & servers
ms.topic: article
ms.date: 04/01/2018
ms.author: carlrab
ms.openlocfilehash: 178eba46e0d128c8d93f2ba664a4a0916889fbbd
ms.sourcegitcommit: 3a4ebcb58192f5bf7969482393090cb356294399
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/06/2018
---
# <a name="database-transaction-units-dtus-and-elastic-database-transaction-units-edtus"></a>DTU(데이터베이스 트랜잭션 단위) 및 eDTU(탄력적 데이터베이스 트랜잭션 단위)
이 문서에서는 DTU(데이터베이스 트랜잭션 단위) 및 eDTU(탄력적 데이터베이스 트랜잭션 단위)와 최대 DTU 또는 eDTU를 적중하는 경우 발생하는 상황에 대해 설명합니다.  

## <a name="what-are-database-transaction-units-dtus"></a>DTU(데이터베이스 트랜잭션 단위)란?
[서비스 계층](sql-database-single-database-resources.md) 내 특정 성능 수준에서 단일 Azure SQL Database의 경우 Microsoft는 해당 데이터베이스(Azure 클라우드의 다른 데이터베이스와는 무관)에 대해 특정 수준의 리소스를 보장하며 예측 가능한 성능 수준을 제공합니다. 이 리소스의 양은 DTU(데이터베이스 트랜잭션 단위)의 수로 계산되며 계산, 저장소 및 IO 리소스를 번들로 묶은 측정값입니다. 이러한 리소스 간의 비율은 원래 실제 OLTP 워크로드에 맞게 디자인된 [OLTP 벤치마크 워크로드](sql-database-benchmark-overview.md)에 따라 결정됩니다. 워크로드가 이러한 리소스의 양을 초과하면 처리량이 제한되어 성능이 느려지고 시간이 초과됩니다. 워크로드에서 사용되는 리소스는 Azure 클라우드의 다른 SQL Database에 사용할 수 있는 리소스에 영향을 주지 않고, 다른 워크로드에서 사용되는 리소스는 사용자의 SQL Database에 사용할 수 있는 리소스에 영향을 주지 않습니다.

![경계 상자](./media/sql-database-what-is-a-dtu/bounding-box.png)

DTU는 다른 성능 수준 및 서비스 계층에서 Azure SQL Database 간의 리소스의 상대 크기를 이해하는 데 가장 유용합니다. 예를 들어 데이터베이스의 성능 수준을 증가시켜 DTU를 두 배로 높일 경우 해당 데이터베이스에 사용할 수 있는 리소스 집합이 동일하게 두 배로 높아집니다. 예를 들어 1750 DTU를 사용하는 프리미엄 P11 데이터베이스는 5개의 DTU를 사용하는 기본 데이터베이스보다 350배 더 많은 DTU 계산 기능을 제공합니다.  

워크로드의 리소스(DTU) 소비에 대해 더 자세히 이해하려면 [Azure SQL Database Query Performance Insight](sql-database-query-performance.md)를 사용하여 다음을 수행합니다.

- 향상된 성능을 위해 잠재적으로 조정될 수 있는 CPU/기간/실행 횟수별 최상위 쿼리를 식별합니다. 예를 들어 IO를 많이 사용하는 쿼리는 [메모리 내 최적화 기술](sql-database-in-memory.md)을 사용하여 특정 서비스 계층 및 성능 수준에서 사용 가능한 메모리를 보다 효율적으로 사용할 수 있는 이점이 있습니다.
- 쿼리에 대한 세부 정보로 드릴다운하고, 해당 텍스트 및 리소스 사용률에 대한 기록을 확인합니다.
- [SQL Database Advisor](sql-database-advisor.md)에서 수행한 작업을 표시하는 성능 조정 권장 사항에 액세스합니다.

응용 프로그램의 작동 중지를 최소로 하여 언제든지 [서비스 계층을 변경](sql-database-service-tiers.md) 할 수 있습니다(일반적으로 4초 이하 평균임). 많은 업무와 앱에서, 특히 사용 패턴이 비교적 예측 가능한 경우 데이터베이스를 만들고 성능을 확장하거나 축소할 수 있으면 충분합니다. 하지만 사용 패턴을 예측할 수 없는 경우 비용과 비즈니스 모델을 관리하기 어려워질 수 있습니다. 이 시나리오에서는 풀의 여러 데이터베이스 간에 공유되는 특정 수의 eDTU로 탄력적 풀을 사용합니다.

![SQL Database 소개: 계층 및 수준별 단일 데이터베이스 DTU](./media/sql-database-what-is-a-dtu/single_db_dtus.png)

## <a name="what-are-elastic-database-transaction-units-edtus"></a>eDTU(탄력적 데이터베이스 트랜잭션 단위)란?
필요 여부에 관계없이 항상 사용할 수 있는 전용 리소스(DTU) 집합을 SQL Database에 제공하는 대신 해당 데이터베이스 간의 리소스 풀을 공유하는 SQL Database 서버의 [탄력적 풀](sql-database-elastic-pool.md)에 데이터베이스를 배치할 수 있습니다. eDTU(Elastic Database 트랜잭션 단위)로 측정되는 탄력적 풀의 공유 리소스입니다. 탄력적 풀은 매우 다양하고 예측할 수 없는 사용 패턴을 지닌 여러 데이터베이스에 대한 성능 목표를 관리하기 위한 간단하고 비용 효율적인 솔루션을 제공합니다. 탄력적 풀에서 풀의 모든 리소스를 사용하는 데이터베이스는 없으며 또한 탄력적 풀의 데이터베이스에 언제나 최소한의 리소스는 사용할 수 있음을 보장할 수 있습니다. 

![SQL Database 소개: 계층 및 수준별 eDTU](./media/sql-database-what-is-a-dtu/sqldb_elastic_pools.png)

풀은 집합 가격에 대한 eDTU 수 집합이 제공됩니다. 탄력적 풀에 속한 개별 데이터베이스는 구성된 경계 내에서 자동 크기 조정할 수 있습니다. 부하가 높은 경우 데이터베이스에서 eDTU를 더 많이 사용하여 수요를 충족시킬 수 있지만, 부하가 낮은 경우에는 부하가 없어 eDTU를 전혀 사용하지 않을 때까지 데이터베이스에서 eDTU를 더 적게 사용합니다. 데이터베이스가 아니라 전체 풀에 리소스를 프로비전하면 관리 작업을 간소화하고 예측 가능한 풀 예산을 확보할 수 있습니다.

추가 eDTU는 데이터베이스 가동 중지 시간 없이 풀의 데이터베이스에 영향을 주지 않은 채 기존 풀에 추가할 수 있습니다. 마찬가지로 더 이상 필요하지 않은 추가 eDTU는 언제든지 기존 풀에서 제거할 수 있습니다. 풀에서 데이터베이스를 추가하거나 제거하거나 다른 데이터베이스의 eDTU를 확보할 수 있도록 높은 부하에서 데이터베이스가 사용할 수 있는 eDTU 양을 제한할 수 있습니다. 데이터베이스에서 예측 가능한 방식으로 리소스를 사용하는 경우 풀에서 이동할 수 있으며 예측 가능한 양의 리소스가 필요한 단일 데이터베이스로 구성할 수 있습니다.

## <a name="how-can-i-determine-the-number-of-dtus-needed-by-my-workload"></a>내 워크로드에 필요한 DTU의 수를 결정하려면 어떻게 해야 하나요?
기존 온-프레미스 또는 SQL Server 가상 머신 워크로드를 Azure SQL Database에 마이그레이션하려는 경우 [DTU 계산기](http://dtucalculator.azurewebsites.net/) 를 사용하여 필요한 DTU의 수를 대략적으로 계산할 수 있습니다. 기존 Azure SQL Database 워크로드의 경우 워크로드를 최적화하는 방법에 대해 깊이 이해하기 위해 [SQL Database Query Performance Insight](sql-database-query-performance.md) 를 사용하여 데이터베이스 리소스 사용(DTU)을 이해할 수 있습니다. [sys.dm_db_ resource_stats](https://msdn.microsoft.com/library/dn800981.aspx) DMV를 사용하여 지난 1시간 동안 리소스 소비 정보를 얻을 수 있습니다. 또는 카탈로그 뷰 [sys.resource_stats](http://msdn.microsoft.com/library/dn269979.aspx)는 충실도가 평균 5분으로 더 낮지만 지난 14일 동안 동일한 데이터를 가져오도록 쿼리할 수 있습니다.

## <a name="how-do-i-know-if-i-could-benefit-from-an-elastic-pool-of-resources"></a>리소스의 탄력적 풀의 이점이 있다면 어떻게 알 수 있나요?
풀은 특정 사용 패턴을 가진 많은 데이터베이스에 적합합니다. 주어진 데이터 베이스에 대해, 이 패턴은 상대적으로 사용률 급증이 드물고 평균 사용률이 낮음으로 규정됩니다. SQL Database는 기존 SQL Database 서버에서 데이터베이스의 기록 리소스 사용률을 자동으로 평가하고 Azure Portal의 적절한 풀 구성을 권장합니다. 자세한 내용은 [탄력적 풀을 사용해야 하는 경우](sql-database-elastic-pool.md)를 참조하세요.

## <a name="what-happens-when-i-hit-my-maximum-dtus"></a>내 최대 DTU에 도달한 경우 어떻게 되나요?
선택한 서비스 계층/성능 수준에 허용되는 최대 한도까지 데이터베이스 워크로드를 실행하는 데 필요한 리소스를 제공하도록 성능 수준이 보정 및 제어됩니다. 워크로드가 CPU/데이터 IO/로그 IO 한도 중 하나에서 한도에 도달할 경우 최대 허용 수준에서 계속 리소스를 수신하지만 쿼리의 대기 시간이 증가할 가능성이 큽니다. 속도 저하가 너무 심해서 쿼리 시간 초과가 시작되지 않으면 이러한 한도에 오류가 발생하지는 않지만 워크로드에서 작업 속도가 느려집니다. 허용되는 최대 동시 사용자 세션/요청(작업자 스레드) 제한에 도달하면 명시적 오류가 표시됩니다. CPU, 메모리, 데이터 IO 및 트랜잭션 로그 IO 이외의 리소스 제한에 대한 자세한 내용은 [Azure SQL Database 리소스 제한]( sql-database-dtu-resource-limits.md#what-happens-when-database-and-elastic-pool-resource-limits-are-reached)을 참조하세요.

## <a name="next-steps"></a>다음 단계
* 단일 데이터베이스 및 탄력적 풀에 대해 사용할 수 있는 DTU 및 eDTU와 CPU, 메모리, 데이터 IO, 트랜잭션 로그 IO 이외의 리소스 제한에 대한 자세한 내용은 [서비스 계층](sql-database-service-tiers.md)을 참조하세요.
* [SQL Database Query Performance Insight](sql-database-query-performance.md) 를 참조하여 DTU 소비를 이해합니다.
* DTU 혼합을 결정하는 데 사용되는 OLTP 벤치마크 워크로드의 방법론을 이해하려면 [SQL Database 벤치마크 개요](sql-database-benchmark-overview.md) 를 참조하세요.
