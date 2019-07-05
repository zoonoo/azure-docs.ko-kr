---
title: MySQL 용 Azure Database에서 성능 권장 사항
description: 이 문서에서는 MySQL 용 Azure Database에서 성능 권장 사항 기능 설명
author: ajlam
ms.author: andrela
ms.service: mysql
ms.topic: conceptual
ms.date: 06/27/2019
ms.openlocfilehash: af3c4482b1ce9e521d14a0e0c63de40625c25c73
ms.sourcegitcommit: aa66898338a8f8c2eb7c952a8629e6d5c99d1468
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/28/2019
ms.locfileid: "67461791"
---
# <a name="performance-recommendations-in-azure-database-for-mysql"></a>MySQL 용 Azure Database에서 성능 권장 사항

**적용 대상:** Azure Database for MySQL 5.7

> [!NOTE]
> 미리 보기 성능 권장 사항입니다.

성능 권장 사항 기능에는 데이터베이스 성능 향상된을 위해 사용자 지정 된 제안 만들기를 분석 합니다. 권장 사항을 생성하기 위해, 해당 분석은 스키마를 포함하여 다양한 데이터베이스 특성을 살펴봅니다. 성능 권장 사항 기능을 완벽하게 활용하려면 서버의 [쿼리 저장소](concepts-query-store.md)를 사용 설정합니다. 성능 스키마가 OFF 이면 performance_schema 및 기능에 필요한 성능 스키마 계측의 하위 집합을 사용 하면 쿼리 저장소 설정. 성능 권장 사항을 구현한 후, 이러한 변경의 영향을 평가하는 성능을 테스트 해야 합니다.

## <a name="permissions"></a>권한

성능 권장 사항 기능을 사용하여 분석을 실행하는 데는 **소유자** 또는 **참가자** 권한이 필요합니다.

## <a name="performance-recommendations"></a>성능 권장 사항

[성능 권장 사항](concepts-performance-recommendations.md) 기능은 서버 전체의 워크로드를 분석하여 성능 향상 가능성이 있는 인덱스를 식별합니다.

오픈 **성능 권장 사항** 에서 합니다 **지능형 성능** MySQL 서버용 Azure 포털 페이지의 메뉴 표시줄의 섹션입니다.

![성능 권장 사항 방문 페이지](./media/concepts-performance-recommendations/performance-recommendations-page.png)

분석을 시작할 데이터베이스를 선택하고 **분석**을 선택합니다. 워크 로드에 따라 분석을 완료 하려면 몇 분 정도 걸릴 수 있습니다. 분석이 완료되면 포털에 알림이 표시됩니다. 분석은 데이터베이스의 전체 검사를 수행합니다. 사용량이 적은 기간 동안 분석을 수행하는 것이 좋습니다.

합니다 **권장 사항** 창에 발견 된 경우 권장 사항 및이 권장 사항을 생성 하는 관련된 쿼리 ID의 목록이 표시 됩니다. 쿼리 ID를 사용 하 여 사용할 수 있습니다 합니다 [mysql.query_store](concepts-query-store.md#mysqlquery_store) 쿼리에 대 한 자세한 보기입니다.

![성능 권장 사항 새 페이지](./media/concepts-performance-recommendations/performance-recommendations-result.png)

권장 사항은 자동으로 적용되지 않습니다. 권장 사항을 적용할 경우, 쿼리 텍스트를 복사하고 원하는 클라이언트에서 실행합니다. 권장 구성 평가를 모니터링 및 테스트해야 합니다.

## <a name="recommendation-types"></a>권장 사항 유형

현재에 *Create Index* 권장 사항을 지원 됩니다.

### <a name="create-index-recommendations"></a>인덱스 만들기 권장 사항

*인덱스 만들기* 권장 사항은 워크로드에서 가장 자주 실행하거나 시간이 많이 걸리는 쿼리의 속도 향상을 위해 새 인덱스를 제안합니다. 이 권장 사항 유형은 [쿼리 저장소](concepts-query-store.md) 사용 설정을 필요로 합니다. 쿼리 저장소는 쿼리 정보를 수집하고 그 분석이 권장 사항을 작성하는 데 사용하는 자세한 쿼리 빈도 및 런타임 통계를 제공합니다.

## <a name="next-steps"></a>다음 단계
- 에 대해 자세히 알아보세요 [모니터링 및 튜닝](concepts-monitoring.md) Azure Database for MySQL에서에서.