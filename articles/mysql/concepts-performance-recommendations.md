---
title: Azure Database for MySQL의 성능 권장 사항
description: 이 문서에서는의 성능 권장 사항 기능에 대해 설명 Azure Database for MySQL
author: ajlam
ms.author: andrela
ms.service: mysql
ms.topic: conceptual
ms.date: 06/27/2019
ms.openlocfilehash: 87942ae8132c89c502bd6e0f4c8d5b5c81a0a14c
ms.sourcegitcommit: 78ebf29ee6be84b415c558f43d34cbe1bcc0b38a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/12/2019
ms.locfileid: "68950379"
---
# <a name="performance-recommendations-in-azure-database-for-mysql"></a>Azure Database for MySQL의 성능 권장 사항

**적용 대상:** Azure Database for MySQL 5.7

> [!IMPORTANT]
> 성능 권장 사항은 미리 보기 상태입니다.

성능 권장 사항 기능은 데이터베이스를 분석 하 여 향상 된 성능을 위한 사용자 지정 제안을 만듭니다. 권장 사항을 생성하기 위해, 해당 분석은 스키마를 포함하여 다양한 데이터베이스 특성을 살펴봅니다. 성능 권장 사항 기능을 완벽하게 활용하려면 서버의 [쿼리 저장소](concepts-query-store.md)를 사용 설정합니다. 성능 스키마가 OFF 인 경우 쿼리 저장소를 켜면 기능에 필요한 performance_schema 및 performance schema 악기의 하위 집합이 활성화 됩니다. 성능 권장 사항을 구현한 후, 이러한 변경의 영향을 평가하는 성능을 테스트 해야 합니다.

## <a name="permissions"></a>사용 권한

성능 권장 사항 기능을 사용하여 분석을 실행하는 데는 **소유자** 또는 **참가자** 권한이 필요합니다.

## <a name="performance-recommendations"></a>성능 권장 사항

[성능 권장 사항](concepts-performance-recommendations.md) 기능은 서버 전체의 워크로드를 분석하여 성능 향상 가능성이 있는 인덱스를 식별합니다.

MySQL server에 대 한 Azure Portal 페이지의 메뉴 모음에 있는 **지능형 성능** 섹션에서 **성능 권장 사항을** 엽니다.

![성능 권장 사항 방문 페이지](./media/concepts-performance-recommendations/performance-recommendations-page.png)

분석을 시작할 데이터베이스를 선택하고 **분석**을 선택합니다. 워크 로드에 따라 분석을 완료 하는 데 몇 분 정도 걸릴 수 있습니다. 분석이 완료되면 포털에 알림이 표시됩니다. 분석은 데이터베이스에 대 한 심층 검사를 수행 합니다. 사용량이 적은 기간 동안 분석을 수행하는 것이 좋습니다.

권장 사항을 찾은 경우 권장 사항 목록과이 권장 구성을 생성 한 관련 쿼리 ID가 권장 사항 창에 표시 됩니다. 쿼리 ID를 사용 하 여 [query_store](concepts-query-store.md#mysqlquery_store) 뷰를 사용 하 여 쿼리에 대 한 자세한 정보를 확인할 수 있습니다.

![성능 권장 사항 새 페이지](./media/concepts-performance-recommendations/performance-recommendations-result.png)

권장 사항은 자동으로 적용되지 않습니다. 권장 사항을 적용할 경우, 쿼리 텍스트를 복사하고 원하는 클라이언트에서 실행합니다. 권장 구성 평가를 모니터링 및 테스트해야 합니다.

## <a name="recommendation-types"></a>권장 사항 유형

현재는 *인덱스 만들기* 권장 사항만 지원 됩니다.

### <a name="create-index-recommendations"></a>인덱스 만들기 권장 사항

*인덱스 만들기* 권장 사항은 워크로드에서 가장 자주 실행하거나 시간이 많이 걸리는 쿼리의 속도 향상을 위해 새 인덱스를 제안합니다. 이 권장 사항 유형은 [쿼리 저장소](concepts-query-store.md) 사용 설정을 필요로 합니다. 쿼리 저장소는 쿼리 정보를 수집하고 그 분석이 권장 사항을 작성하는 데 사용하는 자세한 쿼리 빈도 및 런타임 통계를 제공합니다.

## <a name="next-steps"></a>다음 단계
- Azure Database for MySQL [모니터링 및 튜닝](concepts-monitoring.md) 에 대해 자세히 알아보세요.