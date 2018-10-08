---
title: 모니터링 및 성능 튜닝 - Azure SQL Database | Microsoft Docs
description: 평가 및 개선을 통한 Azure SQL Database의 성능 튜닝 관련 팁.
services: sql-database
ms.service: sql-database
ms.subservice: performance
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: danimir
ms.author: v-daljep
ms.reviewer: carlrab
manager: craigg
ms.date: 07/16/2018
ms.openlocfilehash: c79285247950510791ede915fcf0e5373792044f
ms.sourcegitcommit: 51a1476c85ca518a6d8b4cc35aed7a76b33e130f
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/25/2018
ms.locfileid: "47165044"
---
# <a name="monitoring-and-performance-tuning"></a>모니터링 및 성능 튜닝

Azure SQL Database는 사용량을 쉽게 모니터링하고, 리소스(CPU, 메모리, IO)를 추가 또는 제거하며, 데이터베이스 성능을 향상시킬 수 있는 권장 사항을 찾거나, 데이터베이스가 사용자 워크로드에 맞게 조정되고 성능을 자동으로 최적화할 수 있도록 해주는 자동으로 관리되는 유연한 데이터 서비스입니다.

## <a name="overview-of-monitoring-database-performance-in-azure-sql-database"></a>Azure SQL Database에서 데이터베이스 성능 모니터링 개요
Azure에서 SQL 데이터베이스의 성능 모니터링은 데이터베이스에 대해 선택한 데이터베이스 성능 수준을 기준으로 리소스 사용률을 모니터링하는 것으로 시작합니다. 모니터링을 통해 데이터베이스에 과도한 용량이 있는지 또는 리소스가 최댓값을 초과하여 문제가 있는지 여부를 확인한 다음, 데이터베이스의 계산 크기 및 [DTU 기반 구매 모델](sql-database-service-tiers-dtu.md) 또는 [vCore 기반 구매 모델](sql-database-service-tiers-vcore.md)에서 데이터베이스의 계산 크기와 서비스 계층을 조정할 때인지 여부를 결정할 수 있습니다. 다음 그래픽 도구를 사용하거나 SQL [DMV(동적 관리 뷰)](sql-database-monitoring-with-dmvs.md)를 사용하여 [Azure Portal](https://portal.azure.com)에서 데이터베이스를 모니터링할 수 있습니다.

Azure SQL Database를 사용하면 [성능 튜닝 권장 사항](sql-database-advisor.md)을 검토하여 리소스를 변경하지 않고도 쿼리 성능을 개선 및 최적화하는 기회를 찾을 수 있습니다. 인덱스 누락 및 최적화되지 않은 쿼리는 데이터베이스 성능을 저하시키는 일반적인 원인입니다. 이러한 튜닝 권장 사항을 적용하여 워크로드의 성능을 개선할 수 있습니다.
또한 Azure SQL Database를 통해 식별된 모든 권장 사항을 적용하고 이를 통해 데이터베이스 성능이 개선되는지 확인하여 [쿼리의 성능을 자동으로 최적화](sql-database-automatic-tuning.md)할 수 있습니다. 데이터베이스 성능 문제 해결 및 모니터링에 대한 다음과 같은 옵션이 있습니다.

- [Azure Portal](https://portal.azure.com)에서 **SQL Database**를 클릭하고 데이터베이스를 선택한 후 모니터링 차트를 사용하여 최대값에 근접한 리소스를 찾습니다. 기본적으로 DTU 사용량이 표시됩니다. **편집** 을 클릭하여 표시된 시간 범위 및 값을 변경합니다.
- [Query Performance Insight](sql-database-query-performance.md)를 사용하여 가장 많은 리소스를 소비하는 쿼리를 파악합니다.
- [SQL Database Advisor](sql-database-advisor-portal.md)를 사용하여 인덱스 만들기 및 삭제, 쿼리 매개 변수화, 스키마 문제 해결에 대한 권장 사항을 확인합니다.
- 데이터베이스 성능 자동 모니터링에 [Azure SQL Intelligent Insights](sql-database-intelligent-insights.md)를 사용합니다. 성능 문제가 검색되면 문제의 세부 정보와 RCA(근본 원인 분석)가 포함된 진단 로그가 생성됩니다. 성능 개선 권장 향상이 제공됩니다(가능한 경우).
- [자동 튜닝을 사용](sql-database-automatic-tuning-enable.md)하고 Azure SQL Database에서 식별된 성능 문제를 자동으로 수정하도록 합니다.
- 또한 [DMV(동적 관리 뷰)](sql-database-monitoring-with-dmvs.md), 확장 이벤트 (`XEvents`)(sql-database/sql-database-xevent-db-diff-from-svr.md) 및 [쿼리 저장소](https://docs.microsoft.com/sql/relational-databases/performance/monitoring-performance-by-using-the-query-store)를 사용하여 성능 매개 변수를 실시간으로 가져올 수 있습니다. 이러한 보고서 또는 뷰를 사용하여 일부 문제를 식별하는 경우 Azure SQL Database의 성능을 향상시키는 데 사용할 수 있는 기술을 찾으려면 [성능 지침](sql-database-performance-guidance.md)을 참조하세요.

## <a name="monitor-databases-using-the-azure-portal"></a>Azure 포털을 사용하여 데이터베이스 모니터링
[Azure Portal](https://portal.azure.com/)에서 데이터베이스를 선택하고 **모니터링** 차트를 클릭하여 단일 데이터베이스의 사용률을 모니터링할 수 있습니다. 그러면 **메트릭** 창이 나타나며 **차트 편집** 단추를 클릭하면 이 창을 변경할 수 있습니다. 다음 메트릭을 추가합니다.

* CPU 비율
* DTU 비율
* 데이터 IO 비율
* 데이터베이스 크기 비율

이러한 메트릭을 추가한 후 **메트릭** 창의 세부 정보가 포함된 **모니터링** 차트에서 메트릭을 계속 볼 수 있습니다. 네 가지 메트릭 모두 데이터베이스의 **DTU** 를 기준으로 평균 사용률 비율을 표시합니다. 서비스 계층에 대한 자세한 내용은 [DTU 기반 구매 모델](sql-database-service-tiers-dtu.md) 및 [vCore 기반 구매 모델](sql-database-service-tiers-vcore.md) 문서를 참조하세요.  

![데이터베이스 성능의 서비스 계층 모니터링.](./media/sql-database-single-database-monitoring/sqldb_service_tier_monitoring.png)

성능 메트릭에 대한 경고를 구성할 수도 있습니다. **메트릭** 창에서 **경고 추가** 단추를 클릭합니다. 마법사를 따라 경고를 구성합니다. 메트릭이 특정 임계값을 초과하거나 메트릭이 특정 임계값보다 낮은 경우 경고하는 옵션이 있습니다.

예를 들어 데이터베이스에 대한 워크로드가 확장될 것으로 예상되면 데이터베이스가 성능 메트릭의 80%에 도달할 때마다 이메일 경고를 보내도록 구성할 수 있습니다. 이 경고를 조기 경고로 사용하여 더 큰 다음 계산 크기로 전환해야 하는 시기를 파악할 수 있습니다.

또한 성능 메트릭을 사용하여 작은 계산 크기로 다운그레이드할 수 있는지 여부를 판단할 수도 있습니다. 표준 S2 데이터베이스를 사용하고 있는데 모든 성능 메트릭에서 지정한 시기에 데이터베이스가 평균적으로 10% 이하를 사용하는 것으로 나타난다고 가정합니다. 데이터베이스가 표준 S1에서 잘 작동할 가능성이 있습니다. 그러나 더 작은 계산 크기로 이동하도록 결정하기 전에 급증하거나 변동하는 워크로드에 주의해야 합니다.

## <a name="improving-database-performance-with-more-resources"></a>보다 많은 리소스와 함께 데이터베이스 성능 개선

마지막으로, 데이터베이스의 성능을 향상시킬 수 있는 실행 가능한 항목이 더 없으면 Azure SQL Database에서 사용할 수 있는 리소스의 양을 변경할 수 있습니다. 단일 데이터베이스의 [DTU 서비스 계층](sql-database-service-tiers-dtu.md)을 변경하여 더 많은 리소스를 할당하거나 탄력적 풀의 eDTU를 언제든지 늘릴 수 있습니다. 또는 [vCore 기반 구매 모델](sql-database-service-tiers-vcore.md)을 사용하는 경우, 서비스 계층을 변경하거나 데이터베이스에 할당된 리소스를 늘릴 수 있습니다. 
1. 단일 데이터베이스의 경우 필요에 따라 [서비스 계층을 변경](sql-database-service-tiers-dtu.md)하거나 [리소스를 계산](sql-database-service-tiers-vcore.md)하여 데이터베이스 성능을 향상시킬 수 있습니다.
2. 여러 데이터베이스의 경우 [탄력적 풀](sql-database-elastic-pool-guidance.md)을 사용하여 자동으로 리소스 규모를 조정할 수 있습니다.

## <a name="tune-and-refactor-application-or-database-code"></a>응용 프로그램 또는 데이터베이스 코드 조정 및 리팩터링

데이터베이스를 보다 최적으로 사용하도록 응용 프로그램 코드를 변경하거나 인덱스를 변경하며 계획을 강제 적용하거나 힌트를 사용하여 사용자 워크로드에 맞게 데이터베이스를 수동으로 조정할 수 있습니다. 수동 튜닝 및 코드 다시 작성에 대한 몇 가지 지침 및 팁은 [성능 지침 항목](sql-database-performance-guidance.md) 문서에서 확인합니다.


## <a name="next-steps"></a>다음 단계

- Azure SQL Database에서 자동 조정을 사용하도록 설정하고 자동 조정 기능으로 작업을 완벽하게 관리하려면 [자동 조정 사용](sql-database-automatic-tuning-enable.md)을 참조하세요.
- 수동 튜닝을 사용하려면 [Azure Portal에서 튜닝 권장 사항](sql-database-advisor-portal.md)을 검토하고 쿼리의 성능을 개선하는 권장 사항을 직접 적용합니다.
- [Azure SQL Database 서비스 계층](sql-database-performance-guidance.md)을 변경하여 데이터베이스에 제공되는 리소스를 변경합니다.
