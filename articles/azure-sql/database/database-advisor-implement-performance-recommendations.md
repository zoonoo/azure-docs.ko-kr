---
title: Azure SQL Database에 대 한 데이터베이스 관리자 성능 권장 사항
description: Azure SQL Database Azure SQL Database에서 쿼리 성능을 향상 시킬 수 있는 데이터베이스에 대 한 권장 사항을 제공 합니다.
services: sql-database
ms.service: sql-database
ms.subservice: performance
ms.custom: fasttrack-edit, sqldbrb=1
ms.devlang: ''
ms.topic: conceptual
author: danimir
ms.author: danil
ms.reviewer: jrasnik, carlrab
ms.date: 03/10/2020
ms.openlocfilehash: 5a81ceea151b937b63544cbe51cc22de11d25230
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/02/2020
ms.locfileid: "85254942"
---
# <a name="database-advisor-performance-recommendations-for-azure-sql-database"></a>Azure SQL Database에 대 한 Database Advisor 성능 권장 사항
[!INCLUDE[appliesto-sqldb](../includes/appliesto-sqldb.md)]

Azure SQL Database는 애플리케이션과 함께 학습하고 조정됩니다. Azure SQL Database에는 성능을 최대화할 수 있는 사용자 지정 권장 사항을 제공 하는 많은 데이터베이스 관리자가 있습니다. 이러한 데이터베이스 관리자는 사용 기록을 지속적으로 평가 및 분석 하 고 성능을 향상 시키는 데 도움이 되는 워크 로드 패턴에 따라 권장 사항을 제공 합니다.

## <a name="performance-overview"></a>성능 개요

성능 개요는 데이터베이스 성능에 대 한 요약을 제공 하 고 성능 튜닝 및 문제 해결에 도움이 됩니다.

![Azure SQL Database에 대 한 성능 개요](./media/database-advisor-implement-performance-recommendations/performance-overview-annotated.png)

- **권장 사항** 타일은 데이터베이스에 대한 튜닝 권장 사항을 분석하여 제공합니다(표시되는 내용이 많은 경우 상위 3가지 권장 사항이 표시됨). 이 타일을 클릭 하면 **[성능 권장 사항 옵션](database-advisor-find-recommendations-portal.md#viewing-recommendations)** 으로 이동 합니다.
- **튜닝 활동** 타일은 데이터베이스에 대해 진행 중이고 완료된 튜닝 작업을 제공하여 튜닝 활동의 기록을 간략히 볼 수 있습니다. 이 타일을 클릭하면 데이터베이스에 대한 전체 튜닝 기록 보기로 이동합니다.
- **자동 조정** 타일은 데이터베이스에 대 한 자동 **[조정 구성을](automatic-tuning-enable.md)** 보여 줍니다 (데이터베이스에 자동으로 적용 되는 튜닝 옵션). 이 타일을 클릭하면 자동화 구성 대화 상자가 열립니다.
- **데이터베이스 쿼리** 타일은 데이터베이스에 대한 쿼리 성능 요약을 보여 줍니다(전체 DTU 사용량 및 최상위 리소스 사용 쿼리). 이 타일을 클릭하면 **[Query Performance Insight](query-performance-insight-use.md)** 로 이동합니다.

## <a name="performance-recommendation-options"></a>성능 권장 옵션

Azure SQL Database에서 사용할 수 있는 성능 권장 옵션은 다음과 같습니다.

| 성능 권장 사항 | 단일 데이터베이스 및 풀링된 데이터베이스 지원 | 인스턴스 데이터베이스 지원 |
| :----------------------------- | ----- | ----- |
| **인덱스 만들기 권장 사항** -작업의 성능을 향상 시킬 수 있는 인덱스를 만드는 것이 좋습니다. | 예 | 아니요 |
| **Drop index 권장 사항** -긴 시간 (>90 일) 동안 사용 되지 않은 인덱스 및 고유 인덱스를 제외 하 고 매일 중복 인덱스 및 중복 인덱스를 제거 하는 것이 좋습니다. 이 옵션은 파티션 전환 및 인덱스 힌트를 사용하는 애플리케이션과 호환되지 않습니다. Premium 및 중요 비즈니스용 서비스 계층에서는 사용 하지 않는 인덱스를 삭제할 수 없습니다. | 예 | 아니요 |
| **쿼리 매개 변수화 권장 구성 (미리 보기)** -지속적으로 다시 컴파일되는 하나 이상의 쿼리가 있고 동일한 쿼리 실행 계획으로 끝나는 경우 강제 매개 변수화를 권장 합니다. | 예 | 아니요 |
| **스키마 문제 해결 권장 사항 (미리 보기)** -데이터베이스에서 발생 하는 스키마 관련 SQL 오류 수가 비정상으로 Azure SQL Database 경우 스키마 수정에 대 한 권장 사항이 표시 됩니다. Microsoft는 현재 “스키마 문제 해결” 권장 사항을 사용하지 않습니다. | 예 | 아니요 |

![Azure SQL Database에 대 한 성능 권장 사항](./media/database-advisor-implement-performance-recommendations/performance-recommendations-annotated.png)

성능 권장 사항을 적용 하려면 [권장 구성 적용](database-advisor-find-recommendations-portal.md#applying-recommendations)을 참조 하세요. 권장 구성의 상태를 보려면 [모니터링 작업](database-advisor-find-recommendations-portal.md#monitoring-operations)을 참조 하세요.

또한 이전에 적용되었던 튜닝 작업의 전체 기록을 찾을 수도 있습니다.

## <a name="create-index-recommendations"></a>인덱스 만들기 권장 사항

Azure SQL Database는 실행 중인 쿼리를 지속적으로 모니터링 하 고 성능을 향상 시킬 수 있는 인덱스를 식별 합니다. 특정 인덱스가 없다는 충분한 신뢰도가 빌드되면 새 **인덱스 만들기** 권장 사항이 생성됩니다.

Azure SQL Database는 시간이 지나면서 인덱스로 인한 성능 향상을 예상하여 신뢰도를 빌드합니다. 예상된 성능 향상에 따라 권장 사항은 높음, 보통, 낮음으로 분류됩니다.

권장 사항을 사용하여 만든 인덱스에는 항상 자동 생성된 인덱스로 플래그가 지정됩니다. [Sys. 인덱스 뷰](https://docs.microsoft.com/sql/relational-databases/system-catalog-views/sys-indexes-transact-sql)를 살펴보면 자동으로 생성 되는 인덱스를 확인할 수 있습니다. 자동 생성 인덱스는 ALTER/RENAME 명령을 차단 하지 않습니다.

자동 생성된 인덱스가 있는 열을 삭제하려고 하면 명령이 성공합니다. 자동 생성된 인덱스도 이 명령으로 삭제됩니다. 일반 인덱스는 인덱싱된 열에 대한 ALTER/RENAME 명령을 차단합니다.

인덱스 만들기 권장 사항이 적용된 후 Azure SQL Database는 기준 성능과 쿼리 성능을 비교합니다. 새 인덱스가 성능을 향상하면 권장 사항에 성공으로 플래그가 지정되고 영향 보고서를 사용할 수 있습니다. 인덱스가 성능을 향상 시 키 지 못하면 자동으로 되돌려집니다. Azure SQL Database는이 프로세스를 사용 하 여 권장 사항이 데이터베이스 성능을 향상 시킬 수 있도록 합니다.

모든 **인덱스 만들기** 권장 사항에는 데이터베이스 또는 풀의 리소스 사용량이 높을 경우 권장 사항을 적용하지 않는 백오프 정책이 있습니다. 백오프 정책은 CPU, 데이터 IO, 로그 IO 및 사용 가능한 스토리지를 고려합니다.

CPU, 데이터 IO 또는 로그 IO가 이전 30 분 동안 80% 보다 높은 경우 인덱스 만들기 권장 사항이 연기 됩니다. 인덱스를 만든 후에 사용 가능한 스토리지가 10% 아래로 떨어지면 권장 사항은 오류 상태로 전환됩니다. 며칠 후에도 자동 조정 기능이 인덱스가 유용할 것이라고 판단할 경우 프로세스가 다시 시작됩니다.

이 프로세스는 인덱스를 만드는 데 사용할 수 있는 스토리지가 충분하거나, 인덱스가 더 이상 유용하지 않을 것으로 확인될 때까지 반복됩니다.

## <a name="drop-index-recommendations"></a>인덱스 삭제 권장 사항

누락 된 인덱스를 검색 하는 것 외에도 Azure SQL Database는 기존 인덱스의 성능을 지속적으로 분석 합니다. 인덱스를 사용하지 않을 경우 Azure SQL Database에서 인덱스를 삭제하도록 권장합니다. 다음과 같은 두 가지 경우에는 인덱스를 삭제하는 것이 좋습니다.

- 인덱스가 다른 인덱스의 복제본인 경우(동일하게 인덱싱되거나 포함된 열, 파티션 스키마 및 필터)
- 인덱스가 오랜 기간(93일) 동안 사용되지 않은 경우

인덱스 삭제 권장 사항은 구현 후에 확인도 수행합니다. 성능이 향상되면 영향 보고서를 사용할 수 있습니다. 성능이 저하되면 권장 사항이 되돌려집니다.

## <a name="parameterize-queries-recommendations-preview"></a>쿼리 매개 변수화 권장 사항 (미리 보기)

*쿼리 매개 변수화* 권장 사항은 지속적으로 다시 컴파일되지만 동일한 쿼리 실행 계획으로 종료되는 하나 이상의 쿼리가 있을 때 나타납니다. 이 조건은 강제 매개 변수화를 적용할 기회를 만듭니다. 또한 강제 매개 변수화를 설정하면 나중에 쿼리 계획을 캐시하고 다시 사용할 수 있으므로 성능이 향상되고 리소스 사용량이 줄어듭니다.

실행 계획을 생성 하려면 먼저 모든 쿼리를 컴파일해야 합니다. 생성된 각 계획은 계획 캐시에 추가됩니다. 이후에 동일한 쿼리를 실행할 때 캐시에서 이 계획을 다시 사용할 수 있으므로 추가 컴파일이 필요하지 않게 됩니다.

매개 변수가 없는 값이 있는 쿼리의 경우 매개 변수가 없는 값이 다를 때마다 실행 계획이 다시 컴파일되므로 성능 오버헤드가 발생할 수 있습니다. 대체로 다른 매개 변수 값을 사용하는 동일한 쿼리는 동일한 실행 계획을 생성합니다. 그러나 이러한 계획은 여전히 계획 캐시에 개별적으로 추가됩니다.

실행 계획을 다시 컴파일하는 프로세스로 인해 데이터베이스 리소스가 사용되고, 쿼리 지속 시간이 늘어나며, 계획 캐시가 오버플로됩니다. 결국 이러한 이벤트로 인해 계획이 캐시에서 제거됩니다. 이 동작은 데이터베이스에 대해 강제 매개 변수화 옵션을 설정 하 여 변경할 수 있습니다.

이러한 권장 사항의 영향을 예측할 수 있도록 하기 위해 실제 CPU 사용량과 예상 CPU 사용량 간의 비교 결과가 제공됩니다(권장 사항을 적용했다고 가정). 이 권장 사항은 CPU를 절약하는 데 도움이 될 수 있습니다. 또한 쿼리 지속 기간 및 계획 캐시에 대한 오버헤드를 줄일 수 있으므로 캐시에 유지되고 다시 사용할 수 있는 계획이 더 많아집니다. **적용** 명령을 선택하여 이 권장 사항을 빠르게 적용할 수 있습니다.

이 권장 사항을 적용하면 수분 내에 데이터베이스에서 강제 매개 변수화를 사용할 수 있습니다. 약 24시간 동안 지속되는 모니터링 프로세스가 시작됩니다. 이 기간 후에 유효성 검사 보고서를 확인할 수 있습니다. 이 보고서에는 권장 사항 적용 전후 24시간 동안의 데이터베이스 CPU 사용량이 표시됩니다. Azure SQL Database Advisor에는 성능 재발이 검색 된 경우 적용 된 권장 사항을 자동으로 되돌리는 안전 메커니즘이 있습니다.

## <a name="fix-schema-issues-recommendations-preview"></a>스키마 문제 해결 권장 사항(미리 보기)

> [!IMPORTANT]
> Microsoft는 현재 “스키마 문제 해결” 권장 사항을 사용하지 않습니다. 이전에 “스키마 문제 해결” 권장 사항이 처리했던 스키마 문제를 포함하여 데이터베이스 성능 문제를 모니터링하려면 [Intelligent Insights](intelligent-insights-overview.md)를 사용하는 것이 좋습니다.

**스키마 문제 해결** 권장 사항은 Azure SQL Database 데이터베이스에서 발생 하는 스키마 관련 SQL 오류 수를 비정상으로 표시 하는 경우에 나타납니다. 이 권장 사항은 일반적으로 데이터베이스에서 1시간 내에 여러 스키마 관련 오류(잘못된 열 이름, 잘못된 개체 이름 등)가 발생할 때 나타납니다.

"스키마 문제"는 구문 오류의 클래스입니다. 이러한 문제는 SQL 쿼리의 정의와 데이터베이스 스키마의 정의가 정렬되지 않은 경우에 발생합니다. 예를 들어 쿼리에서 예상하는 열 중 하나가 대상 테이블에서 누락될 수 있고 그 반대의 경우도 마찬가지입니다.

"스키마 문제 해결" 권장 사항은 Azure SQL Database에서 데이터베이스에 발생 하는 스키마 관련 SQL 오류 수를 비정상으로 표시 하는 경우에 나타납니다. 다음 표에는 스키마 문제와 관련된 오류가 나와 있습니다.

| SQL 오류 코드 | 메시지 |
| --- | --- |
| 201 |프로시저 또는 함수 '*'에서 매개 변수 '*'이(가) 필요하지만 제공되지 않았습니다. |
| 207 |잘못된 열 이름: '*'. |
| 208 |잘못된 개체 이름: '*'. |
| 213 |제공된 값의 개수나 열 이름이 테이블 정의와 일치하지 않습니다. |
| 2812 |저장 프로시저를 찾을 수 없습니다. '*'. |
| 8144 |프로시저 또는 함수 *에 너무 많은 인수가 지정되었습니다. |

## <a name="custom-applications"></a>사용자 지정 애플리케이션

개발자는 Azure SQL Database에 대 한 성능 권장 사항을 사용 하 여 사용자 지정 응용 프로그램을 개발할 수 있습니다. 데이터베이스 포털에 나열 된 모든 권장 사항은 [AzSqlDatabaseRecommendedAction](https://docs.microsoft.com/powershell/module/az.sql/get-azsqldatabaserecommendedaction) API를 통해 액세스할 수 있습니다.

## <a name="next-steps"></a>다음 단계

- 데이터베이스 인덱스 및 쿼리 실행 계획에 대한 자세한 내용은 [Azure SQL Database 자동 조정](automatic-tuning-overview.md)을 참조하세요.
- 자동화된 진단을 사용하여 데이터베이스 성능을 자동으로 모니터링하는 방법 및 성능 문제의 근본 원인 분석에 대한 자세한 내용은 [Azure SQL Intelligent Insights](intelligent-insights-overview.md)를 참조하세요.
- 상위 쿼리의 성능에 미치는 영향을 알아보려면 [Query Performance Insight](query-performance-insight-use.md)를 참조하세요.
