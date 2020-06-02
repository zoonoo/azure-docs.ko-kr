---
title: 성능 권장 사항 - Azure Database for MySQL
description: 이 문서에서는 Azure Database for MySQL의 성능 권장 사항을 설명합니다.
author: ajlam
ms.author: andrela
ms.service: mysql
ms.topic: conceptual
ms.date: 5/12/2020
ms.openlocfilehash: cba07b84a1ddc2b9362c818ae2d3747b98379f2e
ms.sourcegitcommit: 90d2d95f2ae972046b1cb13d9956d6668756a02e
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/14/2020
ms.locfileid: "83402639"
---
# <a name="performance-recommendations-in-azure-database-for-mysql"></a>Azure Database for MySQL의 성능 권장 사항

**적용 대상:** Azure Database for MySQL 5.7, 8.0

성능 권장 사항 기능은 데이터베이스를 분석하여 향상된 성능을 위한 사용자 지정 제안을 만듭니다. 권장 사항을 생성하기 위해 분석은 스키마를 비롯한 다양한 데이터베이스 특성을 살펴봅니다. 서버에서 [쿼리 저장소](concepts-query-store.md)를 사용 설정하여 성능 권장 구성 기능을 완벽하게 활용합니다. 성능 스키마가 꺼진 경우 쿼리 저장소를 켜면 performance_schema와 이 기능에 필요한 성능 스키마 계측의 하위 집합이 켜집니다. 성능 권장 사항이 구현되면 성능을 테스트하여 변경에 따른 영향을 평가해야 합니다.

## <a name="permissions"></a>사용 권한

성능 권장 사항 기능을 사용하여 분석을 실행하는 데 필요한**소유자** 또는 **참가자** 권한입니다.

## <a name="performance-recommendations"></a>성능 권장 사항

[성능 권장 사항](concepts-performance-recommendations.md) 기능은 서버 전체의 워크로드를 분석하여 성능 향상 가능성이 있는 인덱스를 식별합니다.

MySQL 서버에 대한 Azure Portal 페이지의 메뉴 표시줄에 있는 **지능형 성능** 섹션에서 **성능 권장 사항**을 엽니다.

![성능 권장 사항 방문 페이지](./media/concepts-performance-recommendations/performance-recommendations-page.png)

**분석**을 선택하고 분석을 시작할 데이터베이스를 선택합니다. 워크로드에 따라 분석을 완료하는 데 몇 분이 걸릴 수 있습니다. 분석이 완료되면 포털에 알림이 표시됩니다. 분석은 데이터베이스에 대한 심층 검사를 수행합니다. 사용량이 적은 기간에 분석을 수행하는 것이 좋습니다.

**권장 사항** 창에는 권장 사항이 있는 경우 그 목록과 해당 권장 사항을 생성한 관련 쿼리 ID가 표시됩니다. 쿼리 ID를 사용하여 [mysql.query_store](concepts-query-store.md#mysqlquery_store) 보기를 사용하여 쿼리에 대한 자세한 내용을 확인할 수 있습니다.

![성능 권장 사항 새 페이지](./media/concepts-performance-recommendations/performance-recommendations-result.png)

이러한 권장 사항은 자동으로 적용되지 않습니다. 권장 사항을 적용하려면 쿼리 텍스트를 복사하고 선택한 클라이언트에서 실행합니다. 테스트 및 모니터링을 통해 권장 사항을 평가해야 합니다.

## <a name="recommendation-types"></a>권장 사항 유형

현재 ‘인덱스 만들기’ 권장 사항만 지원됩니다.

### <a name="create-index-recommendations"></a>인덱스 만들기 권장 사항

‘인덱스 만들기’ 권장 사항에서는 워크로드에서 가장 빈번하게 실행되거나 시간이 많이 걸리는 쿼리를 가속화하기 위한 새 인덱스를 제안합니다. 이 권장 사항 유형을 사용하려면 [쿼리 저장소](concepts-query-store.md)를 사용하도록 설정해야 합니다. 쿼리 저장소는 쿼리 정보를 수집하고 분석에서 권장 사항을 적용하는 데 사용하는 자세한 쿼리 런타임 및 빈도 통계를 제공합니다.

## <a name="next-steps"></a>다음 단계
- Azure Database for MySQL의 [모니터링 및 튜닝](concepts-monitoring.md)에 대해 자세히 알아봅니다.