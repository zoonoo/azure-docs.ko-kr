---
title: Azure Database for MariaDB에서에서 query Performance Insight
description: 이 문서에서는 MariaDB에 대 한 Azure Database에서 Query Performance Insight 기능 설명
author: ajlam
ms.author: andrela
ms.service: mariadb
ms.topic: conceptual
ms.date: 06/27/2019
ms.openlocfilehash: 48ff1fdc08e0df463ec48fd1415c7b67d5beb744
ms.sourcegitcommit: aa66898338a8f8c2eb7c952a8629e6d5c99d1468
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/28/2019
ms.locfileid: "67462114"
---
# <a name="query-performance-insight-in-azure-database-for-mariadb"></a>Azure Database for MariaDB에서에서 query Performance Insight

**적용 대상:**  Azure Database for MariaDB 10.2

> [!NOTE]
> Query Performance Insight는 미리 보기 상태입니다.

Query Performance Insight를 사용하면 가장 오랫동안 실행되는 쿼리, 쿼리가 시간의 경과에 따라 변경되는 방식 및 쿼리에 영향을 주는 대기 등을 빠르게 파악할 수 있습니다.

## <a name="common-scenarios"></a>일반적인 시나리오

### <a name="long-running-queries"></a>장기 실행 쿼리

- 지난 X시간 동안 가장 오래 실행되는 쿼리를 식별하는 경우
- 리소스에서 대기 중인 최상위 N개 쿼리를 식별하는 경우
 
### <a name="wait-statistics"></a>대기 작업 통계

- 쿼리에 대 한 대기 특성 이해
- 리소스 대기 및 리소스 경합 존재 하는 위치에 대 한 추세 이해

## <a name="permissions"></a>권한

**소유자** 하거나 **참가자** Query Performance Insight에서 쿼리 텍스트를 보려면 필요한 사용 권한. ** 판독기** 차트 및 테이블을 볼 수는 있지만 텍스트를 쿼리할 수 있습니다.

## <a name="prerequisites"></a>필수 조건

함수에 대 한 Query Performance Insight를 데이터에 존재 해야 합니다는 [쿼리 저장소](concepts-query-store.md)합니다.

## <a name="viewing-performance-insights"></a>Performance Insight 보기

Azure Portal의 [Query Performance Insight](concepts-query-performance-insight.md) 보기에는 쿼리 저장소의 핵심 정보가 시각화되어 표시됩니다.

MariaDB 서버용 Azure Database의 포털 페이지에서 선택 **Query Performance Insight** 아래를 **지능형 성능** 메뉴의 섹션입니다.

### <a name="long-running-queries"></a>장기 실행 쿼리

합니다 **장기 실행 쿼리의** 15 분 간격으로 집계 탭 실행 당 평균 기간별 상위 5 개 쿼리를 보여 줍니다. 선택 하 여 더 많은 쿼리를 볼 수 있습니다 합니다 **수의 쿼리** 드롭다운 합니다. 이 작업을 수행하면 특정 쿼리 ID에 대해 차트 색이 변경될 수 있습니다.

차트를 클릭하고 끌어 특정 기간으로 범위를 좁힐 수 있습니다. 또는 각각 늘리거나 기간을 보려면 아이콘 입출력 확대/축소를 사용 합니다.

![Query Performance Insight 장기 실행 쿼리](./media/concepts-query-performance-insight/query-performance-insight-landing-page.png)

### <a name="wait-statistics"></a>대기 작업 통계 

> [!NOTE]
> 대기 통계는 쿼리 성능 문제 해결을 위한 것입니다. 문제 해결을 위해 동안만 켜져 있어야 하는 것이 좋습니다.

대기 통계에는 특정 쿼리를 실행 하는 동안 발생 하는 대기 이벤트의 보기를 제공 합니다. 대기 이벤트 형식에 대 한 자세한 정보는 [MySQL 엔진 설명서](https://go.microsoft.com/fwlink/?linkid=2098206)합니다.

선택 된 **Wait Statistics** 대기 서버에서에서 해당 시각화를 보려면 탭 합니다.

쿼리 대기 통계 보기에 표시 되는 최대 대기 지정 된 시간 간격 동안 발생 하는 쿼리 그룹화 됩니다.

![Query Performance Insight 대기 통계](./media/concepts-query-performance-insight/query-performance-insight-wait-statistics.png)

## <a name="next-steps"></a>다음 단계

- 에 대해 자세히 알아보세요 [모니터링 및 튜닝](concepts-monitoring.md) Azure Database for MariaDB에서에서.