---
title: "탄력적 풀이란? 여러 SQL Database 관리 - Azure | Microsoft Docs"
description: "탄력적 풀을 사용하여 수백 및 수천 개의 SQL Database를 관리하고 크기를 조정합니다. 필요한 경우 배포할 수는 리소스에 대한 단일 가격"
keywords: "여러 데이터베이스, 데이터베이스 리소스, 데이터베이스 성능"
services: sql-database
documentationcenter: 
author: ddove
manager: jhubbard
editor: 
ms.assetid: b46e7fdc-2238-4b3b-a944-8ab36c5bdb8e
ms.service: sql-database
ms.custom: DBs & servers
ms.devlang: NA
ms.date: 06/27/2017
ms.author: ddove
ms.workload: data-management
ms.topic: article
ms.tgt_pltfrm: NA
ms.translationtype: Human Translation
ms.sourcegitcommit: 857267f46f6a2d545fc402ebf3a12f21c62ecd21
ms.openlocfilehash: bd0661106a10506680e5c9c70c1fded3eaa999fd
ms.contentlocale: ko-kr
ms.lasthandoff: 06/28/2017


---

# <a name="elastic-pools-help-you-manage-and-scale-multiple-sql-databases"></a>탄력적 풀이 여러 SQL Database를 관리하고 크기를 조정하는 데 도움을 주는 방식

SQL Database 탄력적 풀은 사용 요구가 다양하고 예측하기 어려운 여러 데이터베이스를 관리하고 크기를 조정하기 위한 간단하고 비용 효과적인 솔루션입니다. 탄력적 풀에 있는 데이터베이스는 단일 Azure SQL Database 서버에 있으며 설정된 가격으로 설정된 수의 리소스(eDTU([Elastic Database 트랜잭션 단위](sql-database-what-is-a-dtu.md)))를 공유합니다. Azure SQL 데이터베이스의 탄력적 풀을 사용하면 SaaS 개발자가 각각의 데이터베이스에 성능 탄력성을 제공하는 동시에 규정된 예산 내의 데이터베이스 그룹에 가격 성능을 최적화할 수 있습니다.   

> [!NOTE]
> 탄력적 풀은 현재 미리 보기 상태인 인도 서부를 제외한 모든 Azure 지역에서 일반 공급(GA) 상태입니다.  이 영역에서 탄력적 풀의 GA는 가능한 한 빨리 수행될 예정입니다.
>

## <a name="overview-of-elastic-pools"></a>탄력적 풀의 개요 

SaaS 개발자는 여러 데이터베이스로 구성된 대규모 데이터 계층에 응용 프로그램을 작성합니다. 일반적인 응용 프로그램 패턴은 각 고객에 대해 단일 데이터베이스를 프로비전합니다. 하지만 다양한 고객은 종종 다양하고 예측할 수 없는 사용 패턴이 있으며 각 데이터베이스 사용자의 리소스 요구 사항을 예측하기 어렵습니다. 일반적으로 두 가지 옵션이 있습니다. 

- 최대 사용량에 따른 리소스 오버프로비저닝과 과다 지불 또는
- 최대 사용 기간 동안 성능 및 고객 만족은 저하되지만 비용을 절감하는 언더프로비저닝 

탄력적 풀은 데이터베이스가 필요한 성능 리소스를 필요할 때 얻도록 하여 이 문제를 해결합니다. 예측 가능한 예산 내에서 간단한 리소스 할당 메커니즘을 제공합니다. 탄력적 풀을 사용한 SaaS 응용 프로그램의 디자인 패턴에 대해 자세히 알아보려면 [Azure SQL 데이터베이스를 사용한 다중 테넌트 SaaS 응용 프로그램 디자인 패턴](sql-database-design-patterns-multi-tenancy-saas-applications.md)을 참조하세요.

> [!VIDEO https://channel9.msdn.com/Blogs/Azure/Elastic-databases-helps-SaaS-developers-tame-explosive-growth/player]
>

탄력적 풀을 사용하면 개발자가 예측할 수 없는 개별 데이터베이스의 사용 기간을 수용하기 위해 여러 데이터베이스에서 공유되는 풀에 대한 eDTU([Elastic Database 트랜잭션 단위](sql-database-what-is-a-dtu.md))를 구매할 수 있습니다. 풀에 대한 eDTU 요구 사항은 해당 데이터베이스의 집계 사용률에 의해 결정됩니다. 풀에 사용 가능한 eDTU 수는 개발자 예산에 따라 제어됩니다. 개발자는 풀에 데이터베이스를 추가하고 데이터베이스에 대한 최소 및 최대 eDTU를 설정하며 예산에 따라 풀의 eDTU를 설정합니다. 개발자는 풀을 사용하여 처음 시작부터 계속 성장하는 성숙한 비즈니스까지 해당 서비스를 원활하게 증가시킬 수 있습니다.

풀 내에서 개별 데이터베이스는 집합 매개 변수 내에서 자동 크기 조정에 유연성이 제공됩니다. 부하가 높은 상태에서 데이터베이스는 더 많은 eDTU를 사용하여 수요를 충족할 수 있습니다. 낮은 부하량에서 데이터베이스는 적게 사용하고 부하가 없는 데이터베이스는 eDTU를 사용하지 않습니다. 단일 데이터베이스가 아닌 전체 풀에 대한 리소스 프로비저닝은 관리 작업을 간소화합니다. 또한 풀에 대한 예측 가능한 예산이 있습니다. 새 eDTU 예약에 대한 추가 계산 리소스를 제공하기 위해 데이터베이스를 이동해야 한다는 점을 제외하면 데이터베이스 가동 중지 시간을 발생시키지 않고 추가 eDTU를 기존 풀에 추가할 수 있습니다. 마찬가지로, 추가 eDTU가 더 이상 필요하지 않은 경우에는 언제든지 기존 풀에서 제거할 수 있습니다. 풀에 데이터베이스를 추가하거나 뺄 수 있습니다. 데이터베이스가 예측 가능한 방식으로 리소스를 사용하는 경우 리소스를 이동합니다.

[Azure Portal](sql-database-elastic-pool-manage-portal.md), [PowerShell](sql-database-elastic-pool-manage-powershell.md), [Transact-SQL](sql-database-elastic-pool-manage-tsql.md), [C#](sql-database-elastic-pool-manage-csharp.md) 및 REST API를 사용하여 탄력적 풀을 만들고 관리할 수도 있습니다. 

## <a name="when-to-consider-a-pool"></a>풀을 고려하는 경우
풀은 특정 사용 패턴을 사용하여 많은 수의 데이터베이스에 적합합니다. 주어진 데이터 베이스에 대해, 이 패턴은 상대적으로 사용률 급증이 드물고 평균 사용률이 낮음으로 규정됩니다.

풀에 더 많은 데이터베이스를 추가할수록 절감이 커집니다. 응용 프로그램 사용 패턴에 따라 S3 데이터베이스 두 개 만큼 절감이 가능합니다.  

다음 섹션에서는 데이터베이스의 특정 컬렉션이 풀에 있는 것이 이득이 될 수 있는지 평가하는 방법을 이해할 수 있습니다. 예제에서는 표준 풀을 사용하지만 기본 및 프리미엄 풀에도 동일한 원칙이 적용됩니다.

### <a name="assessing-database-utilization-patterns"></a>데이터베이스 사용량 패턴 평가
다음 그림에는 대부분의 시간을 유휴 상태로 있지만 정기적으로 갑작스러운 활동이 증가하는 데이터베이스의 예가 나와있습니다. 다음은 풀에 적합한 사용률 패턴입니다.

   ![풀에 적합한 단일 데이터베이스](./media/sql-database-elastic-pool/one-database.png)

위에서 보여 주는 5분 주기에서 DB1은 최대 90DTU까지 도달하지만 전체 평균 사용량은 5DTU 미만입니다. 단일 데이터베이스에서 이 워크로드를 실행하려면 S3 성능 수준이 필요하지만 활동이 적은 기간 동안 리소스의 대부분을 사용하지 않게 됩니다.

풀을 사용하면 이러한 사용하지 않는 DTU를 여러 데이터베이스에서 공유할 수 있으므로 필요한 DTU 및 전체 비용을 줄일 수 있습니다.

앞의 예제에 기초하여, DB1같은 유사한 사용률 패턴의 추가 데이터베이스가 있다고 가정합니다. 아래 두 그림에서 4개의 데이터베이스 사용률 및 20개의 데이터베이스는 동일한 그래프에 계층화되어 시간에 따라 해당 사용률의 특성이 겹치지 않는다는 것을 설명합니다.

   ![풀에 적합한 사용 패턴을 가진 네 개의 데이터베이스](./media/sql-database-elastic-pool/four-databases.png)

  ![풀에 적합한 사용 패턴을 가진 20개의 데이터베이스](./media/sql-database-elastic-pool/twenty-databases.png)

전체 20개 데이터베이스의 총 DTU 사용률은 위 그림에서 검은색 선으로 표시되었습니다. 이는 DTU 사용률 집계가 100 DTU를 초과하지 않음을 보여 주며, 이 기간 동안 20개 데이터베이스가 100 eDTU를 공유할 수 있음을 나타냅니다. 이러한 결과 20X DTUs의 감소와 13x 가격 감소를 비교합니다. 단일 데이터베이스의 S3 퍼포먼스 수준내의 각각의 데이터베이스를 배치하기위해 

이 예는 다음 이유로 이상적입니다.

* 최고사용률과 데이터베이스당 평균 사용률간에 큰차이가 있습니다.  
* 각 데이터베이스의 최고 사용률이 시간 내에 여러 지점에서 발생합니다.
* eDTU는 많은 데이터베이스 간에 공유됩니다.

풀의 가격은 풀 eDTU의 함수입니다. 풀의 eDTU 단가는 단일 데이터베이스의 DTU 단가보다 1.5배 높지만, **풀 eDTU는 많은 데이터베이스에서 공유할 수 있으며 필요한 전체 eDTU가 더 적습니다**. 가격 책정 및 eDTU 공유에서의 이러한 차이가 풀이 제공할 수 있는 가격 절감 가능성의 기초가 됩니다.  

데이터베이스 수 및 데이터베이스 사용에 관련된 다음 규칙은 풀이 단일 데이터베이스에 대한 성능 수준을 사용하는 데 비해 비용 절감을 제공하는 데 도움이 됩니다.


### <a name="minimum-number-of-databases"></a>데이터베이스의 최소 수
단일 데이터베이스에 대한 성능 수준의 DTU의 합계가 풀에 필요한 eDTU의 1.5배 이상인 경우 탄력적 풀은 더욱 비용 효율적입니다. 사용 가능한 크기에 대해서는 [탄력적 풀 및 탄력적 데이터베이스에 대한 eDTU 및 저장소 제한](sql-database-elastic-pool.md#edtu-and-storage-limits-for-elastic-pools)을 참조하세요.

***예제***<br>
단일 데이터베이스에 성능 수준을 사용하는 것보다 비용 효율을 증가시키려면 100 DTU 풀에 최소 두 개의 S3 데이터베이스 혹은 15개의 S0 데이터베이스가 필요합니다.

### <a name="maximum-number-of-concurrently-peaking-databases"></a>최대 동시에 최고 데이터베이스
eDTU를 공유하면 풀의 일부 데이터베이스가 단일 데이터베이스에 대한 성능 수준을 사용할 때 사용 가능한 제한까지 eDTU를 동시에 사용할 수 없습니다. 동시에 최대 사용량에 도달하는 데이터베이스 수가 작을수록 더 낮은 풀 eDTU를 설정할 수 있으며 풀도 더 비용 효율적입니다. 일반적으로 eDTU 제한까지 동시에 최대 사용량에 도달하는 풀의 데이터베이스 수가 2/3(또는 67%)를 초과하면 안 됩니다.

***예제***<br>
200 eDTU 풀에서 S3 데이터베이스 3개의 비용을 줄이려면 최대 2개의 데이터베이스가 동시에 최대 사용률에 도달할 수 있습니다. 이러한 S3 데이터베이스 4개 중 2개 이상이 동시에 최대 사용률에 도달하는 경우 200 eDTU 이상으로 풀 크기를 조정해야 합니다. 풀 크기가 201eDTU 이상일 때 단일 데이터베이스의 성능 수준보다 낮은 비용을 유지하려면 S3 데이터베이스를 풀에 더 많이 추가해야 합니다.

예에서는 풀에 있는 다른 데이터베이스의 사용률을 고려 하지 않습니다. 모든 데이터베이스 시간에 특정된 시점에서 일부 사용률의 경우, 데이터베이스의 2/3(또는 67%) 보다 작은 사용률이 동시에 정점에 달할 수 있습니다.

### <a name="dtu-utilization-per-database"></a>데이터베이스당 DTU 사용률
데이터 베이스 사용률의 최고와 평균 사이의 큰 차이는 장시간 동안 낮은 사용률 그리고 짧은 시간 동안 큰사용률을 나타냅니다. 이 사용률 패턴은 데이터베이스에서 리소스를 공유 하기에 이상적입니다. 최고 사용률이 평균사용률의 1.5배 이상이 될 때 풀에 대한 데이터베이스를 고려해야 합니다.

***예제***<br>
S3 데이터베이스의 최고 사용률이 100 DTU이며 평균 사용률이 67 DTU 이하인 경우 풀의 eDTU를 공유하기 적합합니다. 또는 S1 데이터베이스의 최고 사용률이 20 DTU이고 평균 사용률이 13 DTU 이하인 경우 풀에 적합합니다.

## <a name="sizing-an-elastic-pool"></a>탄력적 풀 크기 조정
풀의 적절한 크기는 eDTU 집계 및 풀의 모든 데이터베이스에 필요한 저장소 원본의 값에 따라 달라집니다. 이것은 다음 중 큰 값 결정을 포함합니다.

* 풀에 있는 모든 데이터베이스의 최대 DTU 수입니다.
* 풀에 있는 모든 데이터베이스의 최대 저장된 바이트 수입니다.

사용 가능한 크기에 대해서는 [탄력적 풀 및 탄력적 데이터베이스에 대한 eDTU 및 저장소 제한](sql-database-elastic-pool.md#edtu-and-storage-limits-for-elastic-pools)을 참조하세요.

SQL 데이터베이스는 기존 SQL 데이터베이스 서버에서 데이터베이스의 기록 리소스 사용률을 자동으로 평가하고 Azure 포털의 적절한 풀 구성을 권장합니다. 권장 사항 외에도 기본 제공 환경은 서버에서 데이터베이스의 사용자 지정 그룹에 대한 eDTU 사용률을 예측합니다. 그러면 풀에 데이터베이스를 대화형으로 추가하고 제거하여 변경 사항을 커밋하기 전에 리소스 사용률 분석 및 크기 조정 조언을 가져오기 위해 "가정" 분석을 수행할 수 있습니다. 방법은 [탄력적 풀 모니터링, 관리 및 크기 조정](sql-database-elastic-pool-manage-portal.md)을 참조하세요.

도구를 사용할 수 없는 경우 다음 단계는 풀이 단일 데이터베이스보다 비용 효율적인지를 예측하는 데 도움이 될 수 있습니다.

1. 다음 수식에 따라 풀에 필요한 eDTU를 예측합니다.

   MAX(<*총 DB 수* X *DB당 평균 DTU 사용률*>,<br>
   <*동시 최고 DB의 수* X *DB당 최고 DTU 사용률*)
2. 풀에서 모든 데이터베이스에 필요한 바이트 수를 추가하여 풀에 필요한 저장소 공간을 예측합니다. 그런 다음 이 저장소의 양을 제공하는 eDTU 풀 크기를 결정합니다. eDTU 풀 크기에 기반한 풀 저장소 한도는 [탄력적 풀 및 탄력적 데이터베이스에 대한 eDTU 및 저장소 제한](sql-database-elastic-pool.md#edtu-and-storage-limits-for-elastic-pools)을 참조하세요.
3. 1단계 및 2단계에서 eDTU 예측 중 큰 수를 사용합니다.
4. [SQL 데이터베이스 가격 책정 페이지](https://azure.microsoft.com/pricing/details/sql-database/) 를 확인하고 3단계의 예상보다 큰 경우 가장 작은 eDTU 풀 크기를 찾습니다.
5. 단일 데이터베이스에 대한 적절한 성능 수준을 사용하는 가격에 5단계의 풀 가격을 비교합니다.

## <a name="edtu-and-storage-limits-for-elastic-pools"></a>탄력적 풀에 대한 eDTU 및 저장소 제한

다음 표에서는 탄력적 풀의 리소스 제한에 대해 설명합니다.  탄력적 풀의 개별 데이터베이스에 대한 리소스 제한은 일반적으로 DTU 및 서비스 계층을 기반으로 하는 풀 외부의 단일 데이터베이스의 경우와 동일합니다.  예를 들어 S2 데이터베이스의 최대 동시 작업자 수는 120명입니다.  따라서 풀의 데이터베이스당 최대 DTU가 50DTU(S2와 동일)인 경우 표준 풀의 데이터베이스에 대한 최대 동시 작업자 수도 120명입니다.

[!INCLUDE [SQL DB service tiers table for elastic pools](../../includes/sql-database-service-tiers-table-elastic-pools.md)]

탄력적 풀의 모든 DTU가 사용되었다면 풀에 있는 각 데이터베이스는 쿼리를 처리할 같은 크기의 리소스를 받습니다.  SQL 데이터베이스 서비스는 같은 분량의 계산 시간을 보장하여 데이터베이스 간의 공정성을 공유할 리소스를 제공합니다. 탄력적 풀 리소스 공유 공정성은 데이터베이스당 DTU 최소값이 0이 아닌 값으로 설정될 때 각 데이터베이스에 보장된 리소스에 적용됩니다.

## <a name="database-properties-for-pooled-databases"></a>풀링된 데이터베이스에 대한 데이터베이스 속성

다음 표에서 풀링된 데이터베이스에 대한 속성을 설명합니다.

| 속성 | 설명 |
|:--- |:--- |
| 데이터베이스당 최대 eDTU |풀에 있는 다른 데이터베이스의 사용률에 따라 사용 가능한 경우 풀에 있는 임의 데이터베이스에서 사용할 수 있는 최대 eDTU 수입니다.  데이터베이스당 최대 eDTU는 데이터베이스에 대해 리소스를 보장하지 않습니다.  풀에 있는 모든 데이터베이스에 적용되는 전역 설정입니다. 데이터베이스 사용률의 최대치를 처리할 만큼 데이터베이스당 최대 eDTU를 충분히 높게 설정합니다. 풀은 일반적으로 모든 데이터베이스가 동시에 최대로 사용되지 않을 경우 데이터베이스에 대해 핫 및 콜드 사용률 패턴을 가정하므로 일정 수준의 오버커밋이 예상됩니다. 예를 들어, 데이터베이스당 최고 사용률이 20 eDTU이며 풀에 있는 100개의 데이터베이스 중 20%만 동시에 최대로 사용되는 것으로 가정합니다.  데이터베이스당 eDTU 최대값이 20 eDTU로 설정된 경우 풀을 5배만큼 오버커밋하고 풀당 eDTU를 400으로 설정하는 것이 적합합니다. |
| 데이터베이스당 최소 eDTU |풀에 있는 임의 데이터베이스에 보장되는 최소 eDTU 수입니다.  풀에 있는 모든 데이터베이스에 적용되는 전역 설정입니다. 데이터베이스당 최소 eDTU를 0으로 설정할 수 있으며 기본값이기도 합니다. 이 속성은 0과 데이터베이스당 평균 기록 eDTU 사용률 사이의 값으로 설정됩니다. 풀에 있는 데이터베이스 수와 데이터베이스당 최소 eDTU를 곱한 값은 풀당 eDTU를 초과할 수 없습니다.  예를 들어, 풀에 20개의 데이터베이스가 있고 데이터베이스당 eDTU 최소값이 10 eDTU로 설정되면 풀당 eDTU는 200 eDTU 이상이어야 합니다. |
| 데이터베이스당 최대 데이터 저장소 |풀에 있는 데이터베이스에 대한 최대 저장소입니다. 풀링된 데이터베이스는 풀 저장소를 공유하므로 데이터베이스 저장소는 남은 풀 저장소와 데이터베이스당 최대 저장소 중 작은 값으로 제한됩니다. 데이터 파일의 최대 저장소를 의미하는 데이터베이스당 최대 저장소는 로그 파일에서 사용하는 공간을 포함하지 않습니다. |
|||

## <a name="elastic-jobs"></a>탄력적 작업
풀을 통해 **[탄력적 작업](sql-database-elastic-jobs-overview.md)**의 스크립트를 실행하여 관리 작업이 간소화됩니다. 탄력적 작업은 많은 수의 데이터베이스와 관련된 번거로움을 대부분 없애 줍니다. 시작하려면 [탄력적 작업 시작](sql-database-elastic-jobs-getting-started.md)을 참조하세요.

여러 데이터베이스를 사용하기 위한 다른 데이터베이스 도구에 대한 자세한 내용은 [Azure SQL Database를 사용하여 확장](sql-database-elastic-scale-introduction.md)을 참조합니다.

## <a name="business-continuity-features-for-databases-in-a-pool"></a>풀의 데이터베이스를 위한 비즈니스 연속성 기능
풀링된 데이터베이스는 일반적으로 단일 데이터베이스에서 사용할 수 있는 동일한 [비즈니스 연속성 기능](sql-database-business-continuity.md)을 지원합니다.

### <a name="point-in-time-restore"></a>특정 시점 복원
특정 시점 복원은 자동 데이터베이스 백업을 사용하여 풀에 있는 데이터베이스를 특정 시점으로 복원합니다. [지정 시간 복원](sql-database-recovery-using-backups.md#point-in-time-restore)

### <a name="geo-restore"></a>지역 복원
지역 복원은 데이터베이스가 호스팅되는 지역에 사고가 발생하여 데이터베이스를 사용할 수 없게 되었을 때를 위한 기본 복구 옵션을 제공합니다. [Azure SQL 데이터베이스 복원 또는 보조 데이터베이스에 대한 장애 조치](sql-database-disaster-recovery.md)

### <a name="active-geo-replication"></a>활성 지역 복제
지역 복원에서 제공하는 것보다 더 까다로운 복구 요구 사항이 있는 응용 프로그램의 경우 [활성 지역 복제](sql-database-geo-replication-overview.md)를 구성합니다.

## <a name="next-steps"></a>다음 단계

* [Azure Portal](sql-database-elastic-pool-manage-portal.md), [PowerShell](sql-database-elastic-pool-manage-powershell.md), [Transact-SQL](sql-database-elastic-pool-manage-tsql.md), [C#](sql-database-elastic-pool-manage-csharp.md) 및 REST API를 사용하여 탄력적 풀을 만들고 관리할 수도 있습니다.
* 비디오는 [Azure SQL Database 탄력적 기능에 대한 Microsoft Virtual Academy 비디오 과정](https://mva.microsoft.com/training-courses/elastic-database-capabilities-with-azure-sql-db-16554)을 참조하세요.
* 탄력적 풀을 사용한 SaaS 응용 프로그램의 디자인 패턴에 대해 자세히 알아보려면 [Azure SQL 데이터베이스를 사용한 다중 테넌트 SaaS 응용 프로그램 디자인 패턴](sql-database-design-patterns-multi-tenancy-saas-applications.md)을 참조하세요.

