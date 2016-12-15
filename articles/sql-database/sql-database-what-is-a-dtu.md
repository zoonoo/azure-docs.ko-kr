---
title: "SQL Database: DTU란? | Microsoft Docs"
description: "Azure SQL Database 트랜잭션 단위가 무엇인지 이해합니다."
keywords: "데이터베이스 옵션, 데이터베이스 성능"
services: sql-database
documentationcenter: 
author: CarlRabeler
manager: jhubbard
editor: CarlRabeler
ms.assetid: 89e3e9ce-2eeb-4949-b40f-6fc3bf520538
ms.service: sql-database
ms.custom: overview
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: NA
ms.date: 09/06/2016
ms.author: carlrab
translationtype: Human Translation
ms.sourcegitcommit: 4edae4ef772dc68b1f0efa9e758f26277dd35c44
ms.openlocfilehash: bbc9bef4039b5c898c44a0e39b78b7b28c225c32


---
# <a name="explaining-database-transaction-units-dtus-and-elastic-database-transaction-units-edtus"></a>DTU(데이터베이스 트랜잭션 단위) 및 eDTU(탄력적 데이터베이스 트랜잭션 단위) 설명
이 문서에서는 DTU(데이터베이스 트랜잭션 단위) 및 eDTU(탄력적 데이터베이스 트랜잭션 단위)와 최대 DTU 또는 eDTU를 적중하는 경우 발생하는 상황에 대해 설명합니다.  

## <a name="what-are-database-transaction-units-dtus"></a>DTU(데이터베이스 트랜잭션 단위)란?
DTU는 [독립 실행형 데이터베이스 서비스 계층](sql-database-service-tiers.md#single-database-service-tiers-and-performance-levels)내에서 특정 성능 수준으로 독립 실행형 Azure SQL Database에 사용 가능하도록 보장된 리소스의 측정 단위입니다. DTU는 실제 OLTP 워크로드에 일반적으로 설계된 OLTP 벤치마크 워크로드에 의해 결정되는 비율로 CPU, 메모리 및 데이터 I/O와 트랜잭션 로그 I/O의 혼합된 측정치입니다. 데이터베이스의 성능 수준을 증가시켜 DTU를 두 배로 높일 경우 해당 데이터베이스에 사용할 수 있는 리소스 집합이 동일하게 2배로 높아집니다. 예를 들어 1750 DTU를 사용하는 프리미엄 P11 데이터베이스는 5개의 DTU를 사용하는기본 데이터베이스보다 350배 더 많은 DTU 계산 기능을 제공합니다. DTU 혼합을 결정하는 데 사용되는 OLTP 벤치마크 워크로드의 방법론을 이해하려면 [SQL Database 벤치마크 개요](sql-database-benchmark-overview.md)를 참조하세요.

![SQL Database 소개: 계층 및 수준별 단일 데이터베이스 DTU](./media/sql-database-what-is-a-dtu/single_db_dtus.png)

응용 프로그램의 작동 중지를 최소로 하여 언제든지 [서비스 계층을 변경](sql-database-scale-up.md) 할 수 있습니다(일반적으로 4초 이하 평균임). 많은 업무와 앱에서, 특히 사용 패턴이 비교적 예측 가능한 경우 데이터베이스를 만들고 단일 데이터베이스 성능을 확장하거나 축소할 수 있으면 충분합니다. 하지만 사용 패턴을 예측할 수 없는 경우 비용과 비즈니스 모델을 관리하기 어려워질 수 있습니다. 이 시나리오에서는 많은 특정 eDTU와 함께 탄력적 풀을 사용합니다.

## <a name="what-are-elastic-database-transaction-units-edtus"></a>eDTU(탄력적 데이터베이스 트랜잭션 단위)란?
eDTU는 [탄력적 풀](sql-database-elastic-pool.md)이라는 Azure SQL Server의 데이터베이스 집합 간에 공유될 수 있는 리소스 집합의 측정 단위(DTU)입니다. 탄력적 풀은 매우 다양하고 예측할 수 없는 사용 패턴을 지닌 여러 데이터베이스에 대한 성능 목표를 관리하기 위한 간단하고 비용 효율적인 솔루션을 제공합니다. 자세한 내용은 [탄력적 풀 및 서비스 계층](sql-database-service-tiers.md#elastic-database-pool-service-tiers-and-performance-in-edtus) 을 참조하세요.

![SQL Database 소개: 계층 및 수준별 eDTU](./media/sql-database-what-is-a-dtu/sqldb_elastic_pools.png)

풀은 집합 가격에 대한 eDTU 수 집합이 제공됩니다. 풀 내에서 개별 데이터베이스는 집합 매개 변수 내에서 자동 크기 조정에 유연성이 제공됩니다. 부하가 높은 상태에서 데이터베이스는 더 많은 eDTU를 사용하여 수요를 충족할 수 있습니다. 낮은 부하량에서 데이터베이스는 적게 사용하고 부하가 없는 데이터베이스는 eDTU를 사용하지 않습니다. 단일 데이터베이스가 아닌 전체 풀에 대한 리소스 프로비저닝은 관리 작업을 간소화합니다. 또한 풀에 대한 예측 가능한 예산이 있습니다.

데이터베이스 가동 중단 또는 탄력적 풀에 있는 데이터베이스에 미치는 영향 없이 추가 eDTU를 기존 풀에 추가할 수 있습니다. 마찬가지로, 추가 eDTU가 더 이상 필요하지 않은 경우에는 언제든지 기존 풀에서 제거할 수 있습니다. 풀에 데이터베이스를 추가하거나 뺄 수 있습니다. 데이터베이스가 예측 가능한 방식으로 리소스를 사용하는 경우 리소스를 이동합니다.

## <a name="how-can-i-determine-the-number-of-dtus-needed-by-my-workload"></a>내 워크로드에 필요한 DTU의 수를 결정하려면 어떻게 해야 하나요?
기존 온-프레미스 또는 SQL Server 가상 컴퓨터 워크로드를 Azure SQL Database에 마이그레이션하려는 경우 [DTU 계산기](http://dtucalculator.azurewebsites.net/) 를 사용하여 필요한 DTU의 수를 대략적으로 계산할 수 있습니다. 기존 Azure SQL Database 워크로드의 경우 워크로드를 최적화하는 방법에 대해 깊이 이해하기 위해 [SQL Database Query Performance Insight](sql-database-query-performance.md) 를 사용하여 데이터베이스 리소스 사용(DTU)을 이해할 수 있습니다. [sys.dm_db_ resource_stats](https://msdn.microsoft.com/library/dn800981.aspx) DMV를 사용하여 지난 1시간 동안 리소스 소비 정보를 얻을 수 있습니다. 또는 카탈로그 뷰 [sys.resource_stats](http://msdn.microsoft.com/library/dn269979.aspx)는 충실도가 평균 5분으로 더 낮지만 지난 14일 동안 동일한 데이터를 가져오도록 쿼리할 수 있습니다.

## <a name="how-do-i-know-if-i-could-benefit-from-an-elastic-pool-of-resources"></a>리소스의 탄력적 풀의 이점이 있다면 어떻게 알 수 있나요?
풀은 특정 사용 패턴을 가진 많은 데이터베이스에 적합합니다. 주어진 데이터 베이스에 대해, 이 패턴은 상대적으로 사용률 급증이 드물고 평균 사용률이 낮음으로 규정됩니다. SQL 데이터베이스는 기존 SQL 데이터베이스 서버에서 데이터베이스의 기록 리소스 사용률을 자동으로 평가하고 Azure 포털의 적절한 풀 구성을 권장합니다. 자세한 내용은 [Elastic Database 풀을 사용해야 하는 경우](sql-database-elastic-pool-guidance.md)

## <a name="what-happens-when-i-hit-my-maximum-dtus"></a>내 최대 DTU에 도달한 경우 어떻게 되나요?
선택한 서비스 계층/성능 수준에 허용되는 최대 한도까지 데이터베이스 워크로드를 실행하는 데 필요한 리소스를 제공하도록 성능 수준이 보정 및 제어됩니다. 워크로드가 CPU/데이터 IO/로그 IO 한도 중 하나에서 한도에 도달할 경우 최대 허용 수준에서 계속 리소스를 수신하지만 쿼리의 대기 시간이 증가할 가능성이 큽니다. 속도 저하가 너무 심해서 쿼리 시간 초과가 시작되지 않으면 이러한 한도에 오류가 발생하지는 않지만 워크로드에서 작업 속도가 느려집니다. 허용되는 최대 동시 사용자 세션/요청(작업자 스레드) 제한에 도달하면 명시적 오류가 표시됩니다. 메모리, 데이터 I/O 및 트랜잭션 로그 I/O가 아닌 CPU 리소스의 제한 사항에 대한 자세한 내용은 [Azure SQL Database 리소스 제한](sql-database-resource-limits.md) 을 참조하세요.

## <a name="next-steps"></a>다음 단계
* 독립 실행형 데이터베이스 및 탄력적 풀에 사용할 수 있는 DTU 및 eDTU는 [서비스 계층](sql-database-service-tiers.md) 을 참조하세요.
* 메모리, 데이터 I/O 및 트랜잭션 로그 I/O가 아닌 CPU 리소스의 제한 사항에 대한 자세한 내용은 [Azure SQL Database 리소스 제한](sql-database-resource-limits.md) 을 참조하세요.
* [SQL Database Query Performance Insight](sql-database-query-performance.md) 를 참조하여 DTU 소비를 이해합니다.
* DTU 혼합을 결정하는 데 사용되는 OLTP 벤치마크 워크로드의 방법론을 이해하려면 [SQL Database 벤치마크 개요](sql-database-benchmark-overview.md) 를 참조하세요.



<!--HONumber=Dec16_HO1-->


