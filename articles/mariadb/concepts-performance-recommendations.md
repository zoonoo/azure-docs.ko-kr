---
title: Azure Database for MariaDB의 성능 권장 사항
description: 이 문서에서는의 성능 권장 사항 기능에 대해 설명 Azure Database for MariaDB
author: ajlam
ms.author: andrela
ms.service: mariadb
ms.topic: conceptual
ms.date: 11/04/2019
ms.openlocfilehash: 1c903dcd9015de47c5364c5abd65569d17e7e21f
ms.sourcegitcommit: c62a68ed80289d0daada860b837c31625b0fa0f0
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/05/2019
ms.locfileid: "73604131"
---
# <a name="performance-recommendations-in-azure-database-for-mariadb"></a>Azure Database for MariaDB의 성능 권장 사항

**적용 대상:** Azure Database for MariaDB 10.2

성능 권장 사항 기능은 데이터베이스를 분석 하 여 향상 된 성능을 위한 사용자 지정 제안을 만듭니다. 권장 사항을 생성 하기 위해 분석은 스키마를 비롯 한 다양 한 데이터베이스 특성을 살펴봅니다. 서버에서 [쿼리 저장소](concepts-query-store.md) 를 사용 하도록 설정 하 여 성능 권장 사항 기능을 완벽 하 게 활용 합니다. 성능 스키마가 OFF 인 경우 쿼리 저장소를 켜면 기능에 필요한 performance_schema 및 performance schema 악기의 하위 집합이 활성화 됩니다. 성능 권장 사항을 구현한 후 성능을 테스트 하 여 이러한 변경의 영향을 평가 해야 합니다.

## <a name="permissions"></a>권한

성능 권장 사항 기능을 사용하여 분석을 실행하는 데 필요한**소유자** 또는 **참가자** 권한입니다.

## <a name="performance-recommendations"></a>성능 권장 사항

[성능 권장 사항](concepts-performance-recommendations.md) 기능은 서버 전체의 워크로드를 분석하여 성능 향상 가능성이 있는 인덱스를 식별합니다.

MariaDB 서버에 대 한 Azure Portal 페이지에 있는 메뉴 모음의 **지능형 성능** 섹션에서 **성능 권장 사항을** 엽니다.

![성능 권장 사항 방문 페이지](./media/concepts-performance-recommendations/performance-recommendations-page.png)

분석 **을 선택 하 고 분석** 을 시작 하는 데이터베이스를 선택 합니다. 워크 로드에 따라 분석을 완료 하는 데 몇 분 정도 걸릴 수 있습니다. 분석이 완료되면 포털에 알림이 표시됩니다. 분석은 데이터베이스에 대 한 심층 검사를 수행 합니다. 사용량이 적은 기간에 분석을 수행 하는 것이 좋습니다.

권장 사항을 찾은 경우 권장 사항 목록과이 권장 구성을 생성 한 관련 쿼리 ID **가 권장 사항 창에** 표시 됩니다. 쿼리 ID를 사용 하 여 [query_store](concepts-query-store.md#mysqlquery_store) 뷰를 사용 하 여 쿼리에 대 한 자세한 정보를 확인할 수 있습니다.

![성능 권장 사항 새 페이지](./media/concepts-performance-recommendations/performance-recommendations-result.png)

권장 사항은 자동으로 적용 되지 않습니다. 권장 사항을 적용 하려면 쿼리 텍스트를 복사 하 고 원하는 클라이언트에서 실행 합니다. 테스트 및 모니터링을 통해 권장 사항을 평가 해야 합니다.

## <a name="recommendation-types"></a>권장 사항 유형

현재는 *인덱스 만들기* 권장 사항만 지원 됩니다.

### <a name="create-index-recommendations"></a>인덱스 만들기 권장 사항

*인덱스 만들기* 권장 구성 작업에서 가장 자주 실행 되거나 시간이 많이 걸리는 쿼리를 가속화 하기 위해 새 인덱스를 제안 합니다. 이 권장 사항 유형을 사용 하려면 [쿼리 저장소](concepts-query-store.md) 해야 합니다. 쿼리 저장소 쿼리 정보를 수집 하 고 분석에서 권장 사항을 적용 하는 데 사용 하는 자세한 쿼리 런타임 및 빈도 통계를 제공 합니다.

## <a name="next-steps"></a>다음 단계

- Azure Database for MariaDB [모니터링 및 튜닝](concepts-monitoring.md) 에 대해 자세히 알아보세요.