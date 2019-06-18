---
title: Azure Database for MariaDB에서에서 query Performance Insight
description: 이 문서에서는 MariaDB에 대 한 Azure Database에서 Query Performance Insight 기능 설명
author: ajlam
ms.author: andrela
ms.service: mariadb
ms.topic: conceptual
ms.date: 06/12/2019
ms.openlocfilehash: be9d5c4745cb03d9d3eaa324b7191d82b9d4a14e
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/13/2019
ms.locfileid: "67079419"
---
# <a name="query-performance-insight-in-azure-database-for-mariadb"></a>Azure Database for MariaDB에서에서 query Performance Insight

**적용 대상:**  Azure Database for MariaDB 10.2

> [!NOTE]
> Query Performance Insight는 미리 보기 상태입니다. Azure portal에서 Query Performance Insight에 대 한 지원을 롤아웃하는 아직 못할 지역의 하며

Query Performance Insight를 사용하면 가장 오랫동안 실행되는 쿼리, 쿼리가 시간의 경과에 따라 변경되는 방식 및 쿼리에 영향을 주는 대기 등을 빠르게 파악할 수 있습니다.

## <a name="permissions"></a>권한

**소유자** 하거나 **참가자** Query Performance Insight에서 쿼리 텍스트를 보려면 필요한 사용 권한. ** 판독기** 차트 및 테이블을 볼 수는 있지만 텍스트를 쿼리할 수 있습니다.

## <a name="prerequisites"></a>필수 조건

함수에 대 한 Query Performance Insight를 데이터에 존재 해야 합니다는 [쿼리 저장소](concepts-query-store.md)합니다.

## <a name="viewing-performance-insights"></a>Performance Insight 보기

Azure Portal의 [Query Performance Insight](concepts-query-performance-insight.md) 보기에는 쿼리 저장소의 핵심 정보가 시각화되어 표시됩니다.

MariaDB 서버용 Azure Database의 포털 페이지에서 선택 **Query Performance Insight** 아래를 **지능형 성능** 메뉴의 섹션입니다.

![Query Performance Insight 장기 실행 쿼리](./media/concepts-query-performance-insight/query-performance-insight-landing-page.png)

합니다 **장기 실행 쿼리의** 15 분 간격으로 집계 탭 실행 당 평균 기간별 상위 5 개 쿼리를 보여 줍니다. 선택 하 여 더 많은 쿼리를 볼 수 있습니다 합니다 **수의 쿼리** 드롭다운 합니다. 이 작업을 수행하면 특정 쿼리 ID에 대해 차트 색이 변경될 수 있습니다.

차트를 클릭하고 끌어 특정 기간으로 범위를 좁힐 수 있습니다. 또는 각각 늘리거나 기간을 보려면 아이콘 입출력 확대/축소를 사용 합니다.

선택 된 **Wait Statistics** 대기 서버에서에서 해당 시각화를 보려면 탭 합니다.

쿼리 대기 통계 보기에 표시 되는 최대 대기 지정 된 시간 간격 동안 발생 하는 쿼리 그룹화 됩니다.

![Query Performance Insight 대기 통계](./media/concepts-query-performance-insight/query-performance-insight-wait-statistics.png)

## <a name="next-steps"></a>다음 단계

- 에 대해 자세히 알아보세요 [모니터링 및 튜닝](concepts-monitoring.md) Azure Database for MariaDB에서에서.