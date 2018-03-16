---
title: "탄력적 풀을 사용하여 여러 SQL Database 관리 - Azure | Microsoft Docs"
description: "탄력적 풀을 사용하여 수백 및 수천 개의 SQL Database를 관리하고 크기를 조정합니다. 필요한 경우 배포할 수는 리소스에 대한 단일 가격"
keywords: "여러 데이터베이스, 데이터베이스 리소스, 데이터베이스 성능"
services: sql-database
documentationcenter: 
author: CarlRabeler
manager: jhubbard
editor: 
ms.assetid: b46e7fdc-2238-4b3b-a944-8ab36c5bdb8e
ms.service: sql-database
ms.custom: DBs & servers
ms.devlang: NA
ms.date: 03/02/2018
ms.author: carlrab
ms.workload: Active
ms.topic: article
ms.openlocfilehash: e89d348c4b21a249401254c64e3887ec36283022
ms.sourcegitcommit: 8c3267c34fc46c681ea476fee87f5fb0bf858f9e
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/09/2018
---
# <a name="elastic-pools-help-you-manage-and-scale-multiple-azure-sql-databases"></a>탄력적 풀이 여러 Azure SQL Database를 관리하고 크기를 조정하는 데 도움을 주는 방식

SQL Database 탄력적 풀은 사용 요구가 다양하고 예측하기 어려운 여러 데이터베이스를 관리하고 크기를 조정하기 위한 간단하고 비용 효과적인 솔루션입니다. 탄력적 풀에 있는 데이터베이스는 단일 Azure SQL Database 서버에 있으며 설정된 가격으로 설정된 수의 리소스(eDTU([Elastic Database 트랜잭션 단위](sql-database-what-is-a-dtu.md)))를 공유합니다. Azure SQL Database의 탄력적 풀을 사용하면 SaaS 개발자가 각각의 데이터베이스에 성능 탄력성을 제공하는 동시에 규정된 예산 내의 데이터베이스 그룹에 가격 성능을 최적화할 수 있습니다. 

## <a name="what-are-sql-elastic-pools"></a>SQL 탄력적 풀이란? 

SaaS 개발자는 여러 데이터베이스로 구성된 대규모 데이터 계층에 응용 프로그램을 작성합니다. 일반적인 응용 프로그램 패턴은 각 고객에 대해 단일 데이터베이스를 프로비전합니다. 하지만 다양한 고객은 종종 다양하고 예측할 수 없는 사용 패턴이 있으며 각 데이터베이스 사용자의 리소스 요구 사항을 예측하기 어렵습니다. 일반적으로 두 가지 옵션이 있습니다. 

- 최대 사용량에 따른 리소스 오버프로비저닝과 과다 지불 또는
- 최대 사용 기간 동안 성능 및 고객 만족은 저하되지만 비용을 절감하는 언더프로비저닝 

탄력적 풀은 데이터베이스가 필요한 성능 리소스를 필요할 때 얻도록 하여 이 문제를 해결합니다. 예측 가능한 예산 내에서 간단한 리소스 할당 메커니즘을 제공합니다. 탄력적 풀을 사용한 SaaS 응용 프로그램의 디자인 패턴에 대해 자세히 알아보려면 [Azure SQL Database를 사용한 다중 테넌트 SaaS 응용 프로그램 디자인 패턴](sql-database-design-patterns-multi-tenancy-saas-applications.md)을 참조하세요.

> [!VIDEO https://channel9.msdn.com/Blogs/Azure/Elastic-databases-helps-SaaS-developers-tame-explosive-growth/player]
>

탄력적 풀을 사용하면 개발자가 예측할 수 없는 개별 데이터베이스의 사용 기간을 수용하기 위해 여러 데이터베이스에서 공유되는 풀에 대한 eDTU([Elastic Database 트랜잭션 단위](sql-database-what-is-a-dtu.md))를 구매할 수 있습니다. 풀에 대한 eDTU 요구 사항은 해당 데이터베이스의 집계 사용률에 의해 결정됩니다. 풀에 사용 가능한 eDTU 수는 개발자 예산에 따라 제어됩니다. 개발자는 풀에 데이터베이스를 추가하고 데이터베이스에 대한 최소 및 최대 eDTU를 설정하며 예산에 따라 풀의 eDTU를 설정합니다. 개발자는 풀을 사용하여 처음 시작부터 계속 성장하는 성숙한 비즈니스까지 해당 서비스를 원활하게 증가시킬 수 있습니다.

풀 내에서 개별 데이터베이스는 집합 매개 변수 내에서 자동 크기 조정에 유연성이 제공됩니다. 부하가 높은 상태에서 데이터베이스는 더 많은 eDTU를 사용하여 수요를 충족할 수 있습니다. 낮은 부하량에서 데이터베이스는 적게 사용하고 부하가 없는 데이터베이스는 eDTU를 사용하지 않습니다. 단일 데이터베이스가 아닌 전체 풀에 대한 리소스 프로비저닝은 관리 작업을 간소화합니다. 또한 풀에 대한 예측 가능한 예산이 있습니다. 새 eDTU 예약에 대한 추가 계산 리소스를 제공하기 위해 데이터베이스를 이동해야 한다는 점을 제외하면 데이터베이스 가동 중지 시간을 발생시키지 않고 추가 eDTU를 기존 풀에 추가할 수 있습니다. 마찬가지로, 추가 eDTU가 더 이상 필요하지 않은 경우에는 언제든지 기존 풀에서 제거할 수 있습니다. 풀에 데이터베이스를 추가하거나 뺄 수 있습니다. 데이터베이스가 예측 가능한 방식으로 리소스를 사용하는 경우 리소스를 이동합니다.

## <a name="when-should-you-consider-a-sql-database-elastic-pool"></a>SQL Database 탄력적 풀은 언제 고려해야 하나요?

풀은 특정 사용 패턴을 사용하여 많은 수의 데이터베이스에 적합합니다. 주어진 데이터 베이스에 대해, 이 패턴은 상대적으로 사용률 급증이 드물고 평균 사용률이 낮음으로 규정됩니다.

풀에 더 많은 데이터베이스를 추가할수록 절감이 커집니다. 응용 프로그램 사용 패턴에 따라 S3 데이터베이스 두 개만큼 절감이 가능합니다. 

다음 섹션에서는 데이터베이스의 특정 컬렉션이 풀에 있는 것이 이득이 될 수 있는지 평가하는 방법을 이해할 수 있습니다. 예제에서는 표준 풀을 사용하지만 기본 및 프리미엄 풀에도 동일한 원칙이 적용됩니다.

### <a name="assessing-database-utilization-patterns"></a>데이터베이스 사용량 패턴 평가

다음 그림에는 대부분의 시간을 유휴 상태로 있지만 정기적으로 갑작스러운 활동이 증가하는 데이터베이스의 예가 나와있습니다. 다음은 풀에 적합한 사용률 패턴입니다.

   ![풀에 적합한 단일 데이터베이스](./media/sql-database-elastic-pool/one-database.png)

위에서 보여 주는 5분 주기에서 DB1은 최대 90DTU까지 도달하지만 전체 평균 사용량은 5DTU 미만입니다. 단일 데이터베이스에서 이 워크로드를 실행하려면 S3 성능 수준이 필요하지만 활동이 적은 기간 동안 리소스의 대부분을 사용하지 않게 됩니다.

풀을 사용하면 이러한 사용하지 않는 DTU를 여러 데이터베이스에서 공유할 수 있으므로 필요한 DTU 및 전체 비용을 줄일 수 있습니다.

앞의 예제에 기초하여, DB1같은 유사한 사용률 패턴의 추가 데이터베이스가 있다고 가정합니다. 아래 두 그림에서 4개의 데이터베이스 사용률 및 20개의 데이터베이스는 동일한 그래프에 계층화되어 시간에 따라 해당 사용률의 특성이 겹치지 않는다는 것을 설명합니다.

   ![풀에 적합한 사용 패턴을 가진 네 개의 데이터베이스](./media/sql-database-elastic-pool/four-databases.png)

   ![풀에 적합한 사용 패턴을 가진 20개의 데이터베이스](./media/sql-database-elastic-pool/twenty-databases.png)

전체 20개 데이터베이스의 총 DTU 사용률은 위 그림에서 검은색 선으로 표시되었습니다. 이는 DTU 사용률 집계가 100 DTU를 초과하지 않음을 보여 주며, 이 기간 동안 20개 데이터베이스가 100 eDTU를 공유할 수 있음을 나타냅니다. 이러한 결과 20X DTUs의 감소와 13x 가격 감소를 비교합니다. 단일 데이터베이스의 S3 퍼포먼스 수준내의 각각의 데이터베이스를 배치하기 위해

이 예는 다음 이유로 이상적입니다.

* 최고사용률과 데이터베이스당 평균 사용률간에 큰 차이가 있습니다. 
* 각 데이터베이스의 최고 사용률이 시간 내에 여러 지점에서 발생합니다.
* eDTU는 많은 데이터베이스 간에 공유됩니다.

풀의 가격은 풀 eDTU의 함수입니다. 풀의 eDTU 단가는 단일 데이터베이스의 DTU 단가보다 1.5배 높지만, **풀 eDTU는 많은 데이터베이스에서 공유할 수 있으며 필요한 전체 eDTU가 더 적습니다**. 가격 책정 및 eDTU 공유에서의 이러한 차이가 풀이 제공할 수 있는 가격 절감 가능성의 기초가 됩니다. 

데이터베이스 수 및 데이터베이스 사용에 관련된 다음 규칙은 풀이 단일 데이터베이스에 대한 성능 수준을 사용하는 데 비해 비용 절감을 제공하는 데 도움이 됩니다.

### <a name="minimum-number-of-databases"></a>데이터베이스의 최소 수

단일 데이터베이스에 대한 성능 수준의 DTU의 합계가 풀에 필요한 eDTU의 1.5배 이상인 경우 탄력적 풀은 더욱 비용 효율적입니다. 사용 가능한 크기에 대해서는 [탄력적 풀 및 탄력적 데이터베이스에 대한 eDTU 및 저장소 제한](sql-database-resource-limits.md#elastic-pool-storage-sizes-and-performance-levels)을 참조하세요.

***예제***<br>
단일 데이터베이스에 성능 수준을 사용하는 것보다 비용 효율을 증가시키려면 100 DTU 풀에 최소 두 개의 S3 데이터베이스 혹은 15개의 S0 데이터베이스가 필요합니다.

### <a name="maximum-number-of-concurrently-peaking-databases"></a>최대 동시에 최고 데이터베이스

eDTU를 공유하면 풀의 일부 데이터베이스가 단일 데이터베이스에 대한 성능 수준을 사용할 때 사용 가능한 제한까지 eDTU를 동시에 사용할 수 없습니다. 동시에 최대 사용량에 도달하는 데이터베이스 수가 작을수록 더 낮은 풀 eDTU를 설정할 수 있으며 풀도 더 비용 효율적입니다. 일반적으로 eDTU 제한까지 동시에 최대 사용량에 도달하는 풀의 데이터베이스 수가 2/3(또는 67%)를 초과하면 안 됩니다.

***예제***<br>
200 eDTU 풀에서 S3 데이터베이스 3개의 비용을 줄이려면 최대 2개의 데이터베이스가 동시에 최대 사용률에 도달할 수 있습니다. 이러한 S3 데이터베이스 4개 중 2개 이상이 동시에 최대 사용률에 도달하는 경우 200 eDTU 이상으로 풀 크기를 조정해야 합니다. 풀 크기가 201eDTU 이상일 때 단일 데이터베이스의 성능 수준보다 낮은 비용을 유지하려면 S3 데이터베이스를 풀에 더 많이 추가해야 합니다.

예에서는 풀에 있는 다른 데이터베이스의 사용률을 고려 하지 않습니다. 모든 데이터베이스 시간에 특정된 시점에서 일부 사용률의 경우, 데이터베이스의 2/3(또는 67%) 보다 작은 사용률이 동시에 정점에 달할 수 있습니다.

### <a name="dtu-utilization-per-database"></a>데이터베이스당 DTU 사용률
데이터 베이스 사용률의 최고와 평균 사이의 큰 차이는 장시간 동안 낮은 사용률 그리고 짧은 시간 동안 큰 사용률을 나타냅니다. 이 사용률 패턴은 데이터베이스에서 리소스를 공유 하기에 이상적입니다. 최고 사용률이 평균사용률의 1.5배 이상이 될 때 풀에 대한 데이터베이스를 고려해야 합니다.

***예제***<br>
S3 데이터베이스의 최고 사용률이 100 DTU이며 평균 사용률이 67 DTU 이하인 경우 풀의 eDTU를 공유하기 적합합니다. 또는 S1 데이터베이스의 최고 사용률이 20 DTU이고 평균 사용률이 13 DTU 이하인 경우 풀에 적합합니다.

## <a name="how-do-i-choose-the-correct-pool-size"></a>올바른 풀 크기를 선택하려면 어떻게 할까요?

풀의 적절한 크기는 eDTU 집계 및 풀의 모든 데이터베이스에 필요한 저장소 원본의 값에 따라 달라집니다. 이것은 다음 중 큰 값 결정을 포함합니다.

* 풀에 있는 모든 데이터베이스의 최대 DTU 수입니다.
* 풀에 있는 모든 데이터베이스의 최대 저장된 바이트 수입니다.

사용 가능한 크기에 대해서는 [탄력적 풀 및 탄력적 데이터베이스에 대한 eDTU 및 저장소 제한](sql-database-resource-limits.md#elastic-pool-storage-sizes-and-performance-levels)을 참조하세요.

SQL Database는 기존 SQL Database 서버에서 데이터베이스의 기록 리소스 사용률을 자동으로 평가하고 Azure Portal의 적절한 풀 구성을 권장합니다. 권장 사항 외에도 기본 제공 환경은 서버에서 데이터베이스의 사용자 지정 그룹에 대한 eDTU 사용률을 예측합니다. 그러면 풀에 데이터베이스를 대화형으로 추가하고 제거하여 변경 사항을 커밋하기 전에 리소스 사용률 분석 및 크기 조정 조언을 가져오기 위해 "가정" 분석을 수행할 수 있습니다. 방법은 [탄력적 풀 모니터링, 관리 및 크기 조정](sql-database-elastic-pool-manage-portal.md)을 참조하세요.

도구를 사용할 수 없는 경우 다음 단계는 풀이 단일 데이터베이스보다 비용 효율적인지를 예측하는 데 도움이 될 수 있습니다.

1. 다음 수식에 따라 풀에 필요한 eDTU를 예측합니다.

   MAX(<*총 DB 수* X *DB당 평균 DTU 사용률*>,<br>
   <*동시 최고 DB의 수* X *DB당 최고 DTU 사용률*)
2. 풀에서 모든 데이터베이스에 필요한 바이트 수를 추가하여 풀에 필요한 저장소 공간을 예측합니다. 그런 다음 이 저장소의 양을 제공하는 eDTU 풀 크기를 결정합니다. eDTU 풀 크기에 기반한 풀 저장소 한도는 [탄력적 풀 및 탄력적 데이터베이스에 대한 eDTU 및 저장소 제한](sql-database-resource-limits.md#elastic-pool-storage-sizes-and-performance-levels)을 참조하세요.
3. 1단계 및 2단계에서 eDTU 예측 중 큰 수를 사용합니다.
4. [SQL Database 가격 책정 페이지](https://azure.microsoft.com/pricing/details/sql-database/)를 확인하고 3단계의 예상보다 큰 경우 가장 작은 eDTU 풀 크기를 찾습니다.
5. 단일 데이터베이스에 대한 적절한 성능 수준을 사용하는 가격에 5단계의 풀 가격을 비교합니다.

## <a name="using-other-sql-database-features-with-elastic-pools"></a>탄력적 풀과 기타 SQL Database 기능 사용

### <a name="elastic-jobs-and-elastic-pools"></a>탄력적 작업 및 탄력적 풀

풀을 통해 **[탄력적 작업](sql-database-elastic-jobs-overview.md)**의 스크립트를 실행하여 관리 작업이 간소화됩니다. 탄력적 작업은 많은 수의 데이터베이스와 관련된 번거로움을 대부분 없애 줍니다. 시작하려면 [탄력적 작업 시작](sql-database-elastic-jobs-getting-started.md)을 참조하세요.

여러 데이터베이스를 사용하기 위한 다른 데이터베이스 도구에 대한 자세한 내용은 [Azure SQL Database를 사용하여 확장](sql-database-elastic-scale-introduction.md)을 참조합니다.

### <a name="business-continuity-options-for-databases-in-an-elastic-pool"></a>탄력적 풀의 데이터베이스에 대한 비즈니스 연속성 옵션
풀링된 데이터베이스는 일반적으로 단일 데이터베이스에서 사용할 수 있는 동일한 [비즈니스 연속성 기능](sql-database-business-continuity.md)을 지원합니다.

- **지정 시간 복원**: 지정 시간 복원은 자동 데이터베이스 백업을 사용하여 풀에 있는 데이터베이스를 특정 시점으로 복원합니다. [지정 시간 복원](sql-database-recovery-using-backups.md#point-in-time-restore)

- **지역 복원**: 지역 복원은 데이터베이스가 호스팅되는 지역에 사고가 발생하여 데이터베이스를 사용할 수 없게 되었을 때를 위한 기본 복구 옵션을 제공합니다. [Azure SQL Database 복원 또는 보조 데이터베이스에 대한 장애 조치](sql-database-disaster-recovery.md)

- **활성 지역 복제**: 지역 복원에서 제공하는 것보다 더 까다로운 복구 요구 사항이 있는 응용 프로그램의 경우 [활성 지역 복제](sql-database-geo-replication-overview.md)를 구성합니다.

## <a name="manage-elastic-pools-and-databases-using-the-azure-portal"></a>Azure Portal을 사용하여 탄력적 풀 및 데이터베이스 관리

### <a name="creating-a-new-sql-database-elastic-pool-using-the-azure-portal"></a>Azure Portal을 사용하여 새 SQL Database 탄력적 풀 만들기

Azure Portal에서 두 가지 방법으로 탄력적 풀을 만들 수 있습니다. 원하는 풀 설정을 알고 있는 경우 처음부터 수행하거나 서비스에서 권장 내용으로 시작할 수 있습니다. SQL Database에는 데이터베이스에 대한 과거 사용량 원격 분석을 기반으로 보다 비용 효율적인 경우 탄력적 풀 설정을 권장하는 기본 제공 인텔리전스가 있습니다. 

포털의 기존 서버 페이지에서 탄력적 풀을 만드는 것은 기존 데이터베이스를 탄력적 풀로 이동하는 가장 쉬운 방법입니다. **Marketplace**에서 **SQL 탄력적 풀**을 검색하거나 SQL 탄력적 풀 페이지에서 **+추가**를 클릭하여 탄력적 풀을 만들 수도 있습니다. 이 풀 프로비저닝 워크플로를 통해 새 서버 또는 기존 서버를 지정할 수 있습니다.

> [!NOTE]
> 서버에 풀을 여러 개 만들 수 있지만 다른 서버에 속하는 데이터베이스를 동일한 풀에 추가할 수 없습니다.
> 

풀의 가격 책정 계층에 따라 풀에 있는 탄력적 데이터베이스에서 사용 가능한 기능과 각 데이터베이스에서 사용 가능한 최대 eDTU 수(eDTU MAX) 및 저장소(GB)가 결정됩니다. 자세한 내용은 [탄력적 풀에 대한 리소스 한도](sql-database-resource-limits.md#elastic-pool-storage-sizes-and-performance-levels)를 참조하세요.

풀에 대한 가격 책정 계층을 변경하려면 **가격 책정 계층**, 원하는 가격 책정 계층, **선택**을 차례로 클릭합니다.

> [!IMPORTANT]
> 가격 책정 계층을 선택하고 마지막 단계에서 **확인**을 클릭하여 변경 내용을 적용한 후에는 풀의 가격 책정 계층을 변경할 수 없습니다. 기존 탄력적 풀의 가격 책정 계층을 변경하려면 원하는 가격 책정 계층에서 탄력적 풀을 만들고 데이터베이스를 이 새로운 풀로 마이그레이션합니다.
>

사용하고 있는 데이터베이스에 충분한 기록 사용량 원격 분석이 있는 경우 **예상되는 eDTU 및 GB 사용량** 그래프 및 **실제 eDTU 사용량** 막대형 차트는 구성을 결정할 수 있도록 업데이트됩니다. 또한 서비스가 적정 크기의 풀을 만들도록 권장 사항 메시지를 제공할 수 있습니다.

SQL Database 서비스는 사용 기록을 평가하고 단일 데이터베이스를 사용하는 경우보다 비용 효율적인 경우 하나 이상의 풀을 권장합니다. 각 권장 사항은 풀에 가장 적합한 서버 데이터베이스의 고유한 하위 집합으로 구성됩니다.

![권장되는 풀](./media/sql-database-elastic-pool-create-portal/recommended-pool.png) 

풀 권장 사항은 다음으로 구성됩니다.

- 풀에 대한 가격 책정 계층(Basic, Standard 또는 Premium)
- 적절한 **풀 eDTU** (풀당 최대 eDTU)
- 데이터베이스당 **eDTU 최대** 및 **eDTU 최소**
- 풀에 대한 권장 데이터베이스 목록

> [!IMPORTANT]
> 서비스는 풀을 권장할 때 최근 30일간의 원격 분석을 고려합니다. 데이터베이스가 탄력적 풀의 후보로 간주되려면 7일 이상 존재해야 합니다. 이미 탄력적 풀에 있는 데이터베이스는 탄력적 풀 권장 사항에 대한 후보로 간주되지 않습니다.
>

서비스는 리소스 요구와 각 서비스 계층에 있는 단일 데이터베이스를 동일한 계층의 풀로 이동하는 경우 비용 효율성을 평가합니다. 예를 들어 서버의 모든 Standard 데이터베이스는 표준 탄력적 풀에 적합한지 평가됩니다. 즉, 서비스는 Standard 데이터베이스를 Premium 풀로 이동하는 경우와 같은 계층 간 권장 사항을 제공하지 않습니다.

풀에 데이터베이스를 추가한 후에는, 사용자가 선택한 데이터베이스의 기존 사용 정보를 기반으로 권장 사항이 동적으로 생성됩니다. 권장 사항은 eDTU 및 GB 사용 현황 차트는 물론 **풀 구성** 페이지 상단의 권장 사항 배너에도 표시됩니다. 권장 사항은 특정 데이터베이스에 최적화된 탄력적 풀을 만드는 데 도움을 주기 위해 제공됩니다.

![동적 권장 사항](./media/sql-database-elastic-pool-create-portal/dynamic-recommendation.png)

### <a name="manage-and-monitor-an-elastic-pool"></a>탄력적 풀 관리 및 모니터링

Azure Portal에서 탄력적 풀 및 해당 풀 내의 데이터베이스의 사용률을 모니터링할 수 있습니다. 탄력적 풀에 일련의 내용을 변경하는 동시에 모든 변경 내용을 전송할 수도 있습니다. 이러한 변경 내용에는 데이터베이스 추가 또는 제거, 탄력적 풀 설정 변경, 데이터베이스 설정 변경이 포함됩니다.

다음 그림에서는 탄력적 풀 예제를 보여 줍니다. 보기에는 다음이 포함됩니다.

* 탄력적 풀과 풀에 포함된 데이터베이스 모두의 리소스 사용을 모니터링하는 차트
* 탄력적 풀을 변경하는 **구성** 풀 단추
* 데이터베이스를 만들고 현재 탄력적 풀에 추가하는 **데이터베이스 만들기** 단추
* 목록의 모든 데이터베이스에 대해 Transact SQL 스크립트를 실행하여 많은 데이터베이스를 관리하는 데 도움이 되는 탄력적 작업

![풀 보기](./media/sql-database-elastic-pool-manage-portal/basic.png)

특정 풀로 이동하여 해당 리소스 사용률을 확인할 수 있습니다. 기본적으로 풀은 지난 1시간 동안 저장소 및 eDTU 사용량을 표시하도록 구성됩니다. 다양한 시간 창에 다양한 메트릭이 표시되도록 차트를 구성할 수 있습니다. **탄력적 풀 모니터링**에 있는 **리소스 사용률** 차트를 클릭하여 지정한 시간 창에서 지정된 메트릭에 대한 자세히 보기를 표시합니다.

![탄력적 풀 모니터링](./media/sql-database-elastic-pool-manage-portal/basic-2.png)

![메트릭 페이지](./media/sql-database-elastic-pool-manage-portal/metric.png)

### <a name="to-customize-the-chart-display"></a>차트 표시를 사용자 지정하려면

차트 및 메트릭 페이지를 편집하여 CPU 비율, 데이터 IO 비율, 사용되는 로그 IO 백분율 등 다른 메트릭을 표시할 수 있습니다.

![편집 클릭](./media/sql-database-elastic-pool-manage-portal/edit-metric.png)

**차트 편집** 양식에서 시간 범위(지난 시간, 오늘 또는 지난 주)를 선택하거나 **사용자 지정**을 클릭하여 지난 두 주 동안 날짜 범위를 선택합니다. 막대형 또는 꺾은선형 차트 중에서 선택하고 모니터링할 리소스를 선택할 수 있습니다.

> [!Note]
> 측정 단위가 동일한 메트릭만 차트에 동시에 표시될 수 있습니다. 예를 들어, “eDTU 백분율”을 선택하면 측정 단위로 다른 백분율 메트릭만 선택할 수 있습니다.
>

![편집 클릭](./media/sql-database-elastic-pool-manage-portal/edit-chart.png)

### <a name="manage-and-monitor-databases-in-an-elastic-pool"></a>탄력적 풀의 데이터베이스 관리 및 모니터링

문제 발생 가능성에 대한 개별 데이터베이스를 모니터링할 수도 있습니다. ph x="1" /> 모니터링**에는 다섯 개의 데이터베이스에 대한 메트릭을 표시하는 차트가 있습니다. 기본적으로 차트는 지난 한 시간 동안 평균 eDTU별로 풀의 상위 5개 데이터베이스를 표시합니다. 

![탄력적 풀 모니터링](./media/sql-database-elastic-pool-manage-portal/basic-3.png)

**Elastic Database 모니터링**에서 **지난 시간 동안 데이터베이스에 대한 eDTU 사용량**을 클릭합니다. 그러면 **데이터베이스 리소스 사용률**이 열리고 풀의 데이터베이스 사용에 관한 자세한 보기가 표시됩니다. 페이지의 아래쪽에 있는 그리드를 사용하여 차트(최대 5개의 데이터베이스)에서 사용량을 표시하도록 풀에서 모든 데이터베이스를 선택할 수 있습니다. **차트 편집**을 클릭하여 차트에 표시되는 메트릭 및 시간 창을 사용자 지정할 수도 있습니다.

![데이터베이스 리소스 사용률 페이지](./media/sql-database-elastic-pool-manage-portal/db-utilization.png)

### <a name="to-customize-the-view"></a>보기를 사용자 지정하려면

차트를 편집하여 시간 범위(지난 시간 또는 지난 24시간)를 선택하거나 **사용자 지정**을 클릭하여 지난 2주 동안에 표시할 다른 날을 선택합니다.

![차트 편집 클릭](./media/sql-database-elastic-pool-manage-portal/db-utilization-blade.png)

![사용자 지정 클릭](./media/sql-database-elastic-pool-manage-portal/editchart-date-time.png)

**데이터베이스 비교 기준** 드롭다운을 클릭하여 데이터베이스를 비교할 경우 사용할 다른 메트릭을 선택할 수도 있습니다.

![차트 편집](./media/sql-database-elastic-pool-manage-portal/edit-comparison-metric.png)

### <a name="to-select-databases-to-monitor"></a>모니터링할 데이터베이스를 선택하려면

데이터베이스 목록의 **데이터베이스 리소스 사용률** 페이지에서 목록에 있는 페이지를 탐색하거나 데이터베이스의 이름을 입력하여 특정 데이터베이스를 찾을 수 있습니다. 확인란을 사용하여 데이터베이스를 선택합니다.

![모니터링할 데이터베이스 검색](./media/sql-database-elastic-pool-manage-portal/select-dbs.png)


### <a name="add-an-alert-to-an-elastic-pool-resource"></a>탄력적 풀 리소스에 경고 추가

탄력적 풀에 규칙을 추가하여 탄력적 풀이 설정한 사용률 임계값에 도달할 경우 사용자에게 전자 메일을 보내거나 URL 끝점에 경고 문자열을 보낼 수 있습니다.

**리소스에 경고 추가:**

1. **리소스 사용률** 차트를 클릭하여 **메트릭** 페이지를 열고 **경고 추가**를 클릭한 다음 **경고 규칙 추가** 페이지에 정보를 입력합니다(**리소스**는 자동으로 작업 중인 풀로 설정됨).
2. 사용자 및 받는 사람에 대한 경고를 식별하는 **이름** 및 **설명**을 입력합니다.
3. 목록에서 경고하려는 **메트릭**을 선택합니다.

   차트는 동적으로 임계값을 선택할 수 있도록 해당 메트릭에 대한 리소스 사용률을 보여줍니다.

4. **조건**(보다 큼, 보다 작음 등) 및 **임계값**을 선택합니다.
5. 경고를 트리거하기 전에 메트릭 규칙을 만족해야 하는 **기간**을 선택합니다.
6. **확인**을 클릭합니다.

자세한 내용은 [Azure Portal에서 SQL Database 경고 만들기](sql-database-insights-alerts-portal.md)를 참조하세요.

### <a name="move-a-database-into-an-elastic-pool"></a>탄력적 풀로 데이터베이스 이동

기존 풀에서 데이터베이스를 제거하거나 추가할 수 있습니다. 데이터베이스가 다른 풀에 있을 수 있습니다. 그러나 동일한 논리 서버에 있는 데이터베이스만 추가할 수 있습니다.

 ![풀 구성 클릭](./media/sql-database-elastic-pool-manage-portal/configure-pool.png)

![풀에 추가 클릭](./media/sql-database-elastic-pool-manage-portal/add-to-pool.png)

![추가할 데이터베이스 선택](./media/sql-database-elastic-pool-manage-portal/add-databases-pool.png)

![보류 중인 풀 추가](./media/sql-database-elastic-pool-manage-portal/pending-additions.png)

![저장을 클릭합니다.](./media/sql-database-elastic-pool-manage-portal/click-save.png)

### <a name="move-a-database-out-of-an-elastic-pool"></a>탄력적 풀 외부로 데이터베이스 이동

![데이터베이스 나열](./media/sql-database-elastic-pool-manage-portal/select-pools-removal.png)

### <a name="change-performance-settings-of-an-elastic-pool"></a>탄력적 풀의 성능 설정 변경

탄력적 풀의 리소스 사용률을 모니터링하면서 일부 조정이 필요함을 알 수 있습니다. 어쩌면 풀에서 성능이나 저장소 제한을 변경해야 합니다. 풀에서 데이터베이스 설정을 변경하려고 할 수도 있습니다. 언제든지 풀의 설치 프로그램을 변경하여 적절한 성능 및 비용을 얻을 수 있습니다. 자세한 내용은 [탄력적 풀을 사용해야 하는 경우](sql-database-elastic-pool.md)를 참조하세요.

풀당 eDTU 또는 저장소 제한 및 데이터베이스당 eDTU를 변경하려면:

![탄력적 풀 리소스 사용률](./media/sql-database-elastic-pool-manage-portal/resize-pool.png)

## <a name="manage-elastic-pools-and-databases-using-powershell"></a>PowerShell을 사용하여 탄력적 풀 및 데이터베이스 관리

Azure PowerShell을 사용하여 SQL Database 탄력적 풀을 만들고 관리하려면 다음 PowerShell cmdlet을 사용합니다. PowerShell을 설치하거나 업그레이드해야 하는 경우 [Azure PowerShell 모듈 설치](/powershell/azure/install-azurerm-ps)를 참조하세요. 데이터베이스, 서버 및 방화벽 규칙을 만들고 관리하려면 [PowerShell을 사용하여 Azure SQL Database 서버 및 데이터베이스 만들기 및 관리](sql-database-servers-databases.md#manage-azure-sql-servers-databases-and-firewalls-using-powershell)를 참조하세요. 

> [!TIP]
> PowerShell 예제 스크립트의 경우 [PowerShell을 사용하여 탄력적 풀 만들기 및 풀 간에 데이터베이스 이동](scripts/sql-database-move-database-between-pools-powershell.md) 및 [PowerShell을 사용하여 Azure SQL Database에서 SQL 탄력적 풀 모니터링 및 크기 조정](scripts/sql-database-monitor-and-scale-pool-powershell.md)을 참조하세요.
>

| Cmdlet | 설명 |
| --- | --- |
|[New-AzureRmSqlElasticPool](/powershell/module/azurerm.sql/new-azurermsqlelasticpool)|논리 SQL Server에서 Elastic Database 풀을 만듭니다.|
|[Get-AzureRmSqlElasticPool](/powershell/module/azurerm.sql/get-azurermsqlelasticpool)|논리 SQL Server에서 탄력적 풀과 해당 속성 값을 가져옵니다.|
|[Set-AzureRmSqlElasticPool](/powershell/module/azurerm.sql/set-azurermsqlelasticpool)|논리 SQL Server에서 Elastic Database 풀의 속성을 수정합니다. 예를 들어 **StorageMB** 속성을 사용하여 탄력적 풀의 최대 저장소를 수정합니다.|
|[Remove-AzureRmSqlElasticPool](/powershell/module/azurerm.sql/remove-azurermsqlelasticpool)|논리 SQL Server에서 Elastic Database 풀을 삭제합니다.|
|[Get-AzureRmSqlElasticPoolActivity](/powershell/module/azurerm.sql/get-azurermsqlelasticpoolactivity)|논리 SQL Server에서 탄력적 풀에 대한 작업 상태를 가져옵니다.|
|[New-AzureRmSqlDatabase](/powershell/module/azurerm.sql/new-azurermsqldatabase)|기존 풀 또는 단일 데이터베이스에서 새 데이터베이스를 만듭니다. |
|[Get-AzureRmSqlDatabase](/powershell/module/azurerm.sql/get-azurermsqldatabase)|하나 이상의 데이터베이스를 가져옵니다.|
|[Set-AzureRmSqlDatabase](/powershell/module/azurerm.sql/set-azurermsqldatabase)|데이터베이스의 속성을 설정하거나 기존 데이터베이스와 탄력적 풀 사이를 이동합니다.|
|[Remove-AzureRmSqlDatabase](/powershell/module/azurerm.sql/remove-azurermsqldatabase)|데이터베이스를 제거합니다.|


> [!TIP]
> 탄력적 풀에 많은 수의 데이터베이스를 만드는 작업은 한 번에 단일 데이터베이스만을 만들 수 있는 포털 또는 PowerShell cmdlet을 사용하는 경우와 같은 시간이 걸릴 수 있습니다. 탄력적 풀로 만들기를 자동화하려면 [CreateOrUpdateElasticPoolAndPopulate](https://gist.github.com/billgib/d80c7687b17355d3c2ec8042323819ae)를 참조하세요.
>

## <a name="manage-elastic-pools-and-databases-using-the-azure-cli"></a>Azure CLI를 사용하여 탄력적 풀 및 데이터베이스 관리

[Azure CLI](/cli/azure)를 사용하여 SQL Database 탄력적 풀을 만들고 관리하려면 다음 [Azure CLI SQL Database](/cli/azure/sql/db) 명령을 사용합니다. [Cloud Shell](/azure/cloud-shell/overview)을 사용하여 CLI 브라우저에서 실행하거나 macOS, Linux 또는 Windows에서 [설치](/cli/azure/install-azure-cli)합니다. 

> [!TIP]
> Azure CLI 예제 스크립트의 경우 [CLI를 사용하여 SQL 탄력적 풀에서 Azure SQL Database 이동](scripts/sql-database-move-database-between-pools-cli.md) 및 [Azure CLI를 사용하여 Azure SQL Database에서 SQL 탄력적 풀 크기 조정](scripts/sql-database-scale-pool-cli.md)을 참조하세요.
>

| Cmdlet | 설명 |
| --- | --- |
|[az sql elastic-pool create](/cli/azure/sql/elastic-pool#az_sql_elastic_pool_create)|탄력적 풀을 만듭니다.|
|[az sql elastic-pool list](/cli/azure/sql/elastic-pool#az_sql_elastic_pool_list)|서버에서 탄력적 풀의 목록을 반환합니다.|
|[az sql elastic-pool list-dbs](/cli/azure/sql/elastic-pool#az_sql_elastic_pool_list_dbs)|탄력적 풀에서 데이터베이스의 목록을 반환합니다.|
|[az sql elastic-pool list-editions](/cli/azure/sql/elastic-pool#az_sql_elastic_pool_list_editions)|사용 가능한 풀 DTU 설정, 저장소 용량 한도 및 데이터베이스별 설정이 포함됩니다. 세부 정보 표시를 줄이기 위해 추가 저장소 용량 한도 및 데이터베이스별 설정은 기본적으로 숨겨져 있습니다.|
|[az sql elastic-pool update](/cli/azure/sql/elastic-pool#az_sql_elastic_pool_update)|탄력적 풀을 업데이트합니다.|
|[az sql elastic-pool delete](/cli/azure/sql/elastic-pool#az_sql_elastic_pool_delete)|탄력적 풀을 삭제합니다.|

## <a name="manage-databases-within-elastic-pools-using-transact-sql"></a>Transact-SQL을 사용하여 탄력적 풀 내에서 데이터베이스 관리

기존 탄력적 풀 내에서 데이터베이스를 만들고 이동하거나, Transact-SQL을 사용하여 SQL Database 탄력적 풀에 대한 정보를 반환하려면 다음 T-SQL 명령을 사용합니다. Azure Portal, [SQL Server Management Studio](/sql/ssms/use-sql-server-management-studio), [Visual Studio Code](https://code.visualstudio.com/docs) 또는 Azure SQL Database 서버에 연결하여 Transact-SQL 명령을 전달할 수 있는 다른 프로그램을 사용하여 이러한 명령을 실행할 수 있습니다 . 데이터베이스, 서버 및 방화벽 규칙을 만들고 관리하려면 [Transact-SQL을 사용하여 Azure SQL Database 서버 및 데이터베이스 만들기 및 관리](sql-database-servers-databases.md#manage-azure-sql-servers-databases-and-firewalls-using-transact-sql)를 참조하세요.

> [!IMPORTANT]
> Transact-SQL을 사용하여 Azure SQL Database 탄력적 풀을 만들거나, 업데이트하거나 삭제할 수는 없습니다. 탄력적 풀에서 데이터베이스를 추가하거나 제거할 수는 있으며, DMV를 사용하여 기존 탄력적 풀에 대한 정보를 반환할 수 있습니다.
>

| 명령 | 설명 |
| --- | --- |
|[CREATE DATABASE (Azure SQL Database)](/sql/t-sql/statements/create-database-azure-sql-database)|기존 풀 또는 단일 데이터베이스에서 새 데이터베이스를 만듭니다. 새 데이터베이스를 만들려면 master 데이터베이스에 연결되어 있어야 합니다.|
| [ALTER DATABASE (Azure SQL Database)](/sql/t-sql/statements/alter-database-azure-sql-database) |탄력적 풀 간에 데이터베이스를 이동합니다.|
|[DROP DATABASE(Transact-SQL)](/sql/t-sql/statements/drop-database-transact-sql)|데이터베이스를 삭제합니다.|
|[sys.elastic_pool_resource_stats (Azure SQL Database)](/sql/relational-databases/system-catalog-views/sys-elastic-pool-resource-stats-azure-sql-database)|논리 서버에서 모든 Elastic Database 풀에 대한 리소스 사용 통계를 반환합니다. 각 Elastic Database 풀의 경우 매 15초 보고 창에 대해 한 행이 있습니다(분당 4개 행). 여기에는 풀의 모든 데이터베이스에 의한 CPU, IO, 로그, 저장소 계산 및 동시 요청/세션 사용률이 포함됩니다.|
|[sys.database_service_objectives(Azure SQL Database)](/sql/relational-databases/system-catalog-views/sys-database-service-objectives-azure-sql-database)|Azure SQL Database 또는 Azure SQL Data Warehouse가 있는 경우 버전(서비스 계층), 서비스 목표(가격 책정 계층) 및 탄력적 풀 이름을 반환합니다. Azure SQL Database 서버의 마스터 데이터베이스에 로그인하면 모든 데이터베이스에 대한 정보를 반환합니다. Azure SQL Data Warehouse의 경우 마스터 데이터베이스에 연결되어 있어야 합니다.|

## <a name="manage-elastic-pools-and-databases-using-the-rest-api"></a>REST API를 사용하여 탄력적 풀 및 데이터베이스 관리

SQL Database 탄력적 풀을 만들고 관리하려면 다음 REST API 요청을 사용합니다.

| 명령 | 설명 |
| --- | --- |
|[탄력적 풀 - Create 또는 Update](/rest/api/sql/elasticpools/createorupdate)|새 탄력적 풀을 만들거나 기존 탄력적 풀을 업데이트합니다.|
|[탄력적 풀 - Delete](/rest/api/sql/elasticpools/delete)|탄력적 풀을 삭제합니다.|
|[탄력적 풀 - Get](/rest/api/sql/elasticpools/get)|탄력적 풀을 가져옵니다.|
|[탄력적 풀 - List By Server](/rest/api/sql/elasticpools/listbyserver)|서버에서 탄력적 풀의 목록을 반환합니다.|
|[탄력적 풀 - Update](/rest/api/sql/elasticpools/update)|기준 탄력적 풀을 업데이트합니다.|
|[권장되는 탄력적 풀 - Get](/rest/api/sql/recommendedelasticpools/get)|권장되는 탄력적 풀을 가져옵니다.|
|[권장되는 탄력적 풀 - List By Server](/rest/api/sql/recommendedelasticpools/listbyserver)|권장되는 탄력적 풀을 반환합니다.|
|[권장되는 탄력적 풀 - List Metrics](/rest/api/sql/recommendedelasticpools/listmetrics)|권장되는 탄력적 풀 메트릭을 반환합니다.|
|[탄력적 풀 활동](/rest/api/sql/elasticpoolactivities)|탄력적 풀 활동을 반환합니다.|
|[탄력적 풀 데이터베이스 활동](/rest/api/sql/elasticpooldatabaseactivities)|탄력적 풀 내에서 데이터베이스에 대한 활동을 반환합니다.|
|[데이터베이스 - Create 또는 Update](/rest/api/sql/databases/createorupdate)|새 데이터베이스를 만들거나 기존 데이터베이스를 업데이트합니다.|
|[데이터베이스 - Get](/rest/api/sql/databases/get)|데이터베이스를 가져옵니다.|
|[데이터베이스 - Get By Elastic Pool](/rest/api/sql/databases/getbyelasticpool)|탄력적 풀 내부의 데이터베이스를 가져옵니다.|
|[데이터베이스 - Get By Recommended Elastic Pool](/rest/api/sql/databases/getbyrecommendedelasticpool)|권장되는 탄력적 풀 내부의 데이터베이스를 가져옵니다.|
|[데이터베이스 - List By Elastic Pool](/rest/api/sql/databases/listbyelasticpool)|탄력적 풀에서 데이터베이스의 목록을 반환합니다.|
|[데이터베이스 - List By Recommended Elastic Pool](/rest/api/sql/databases/listbyrecommendedelasticpool)|권장되는 탄력적 풀 내부의 데이터베이스 목록을 반환합니다.|
|[데이터베이스 - List By Server](/rest/api/sql/databases/listbyserver)|서버의 데이터베이스의 목록을 반환합니다.|
|[데이터베이스 - Update](/rest/api/sql/databases/update)|기존 데이터베이스를 업데이트합니다.|

## <a name="next-steps"></a>다음 단계

* 비디오는 [Azure SQL Database 탄력적 기능에 대한 Microsoft Virtual Academy 비디오 과정](https://mva.microsoft.com/training-courses/elastic-database-capabilities-with-azure-sql-db-16554)을 참조하세요.
* 탄력적 풀을 사용한 SaaS 응용 프로그램의 디자인 패턴에 대해 자세히 알아보려면 [Azure SQL Database를 사용한 다중 테넌트 SaaS 응용 프로그램 디자인 패턴](sql-database-design-patterns-multi-tenancy-saas-applications.md)을 참조하세요.
* 탄력적 풀을 사용하는 SaaS 자습서의 경우 [Wingtip SaaS 응용 프로그램 소개](sql-database-wtp-overview.md)를 참조하세요.
