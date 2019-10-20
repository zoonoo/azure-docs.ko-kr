---
title: Azure Database for MySQL Query Performance Insight
description: 이 문서에서는의 Query Performance Insight 기능에 대해 설명 Azure Database for MySQL
author: ajlam
ms.author: andrela
ms.service: mysql
ms.topic: conceptual
ms.date: 10/17/2019
ms.openlocfilehash: b7395a8ab71e860c2e584339dcd581077a4f4020
ms.sourcegitcommit: b4f201a633775fee96c7e13e176946f6e0e5dd85
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/18/2019
ms.locfileid: "72595480"
---
# <a name="query-performance-insight-in-azure-database-for-mysql"></a>Azure Database for MySQL Query Performance Insight

**적용 대상:** Azure Database for MySQL 5.7

> [!IMPORTANT]
> Query Performance Insight 미리 보기 상태입니다.

Query Performance Insight를 사용하면 가장 오랫동안 실행되는 쿼리, 쿼리가 시간의 경과에 따라 변경되는 방식 및 쿼리에 영향을 주는 대기 등을 빠르게 파악할 수 있습니다.

## <a name="common-scenarios"></a>일반적인 시나리오

### <a name="long-running-queries"></a>장기 실행 쿼리

- 지난 X시간 동안 가장 오래 실행되는 쿼리를 식별하는 경우
- 리소스에서 대기 중인 최상위 N개 쿼리를 식별하는 경우
 
### <a name="wait-statistics"></a>대기 통계

- 쿼리의 대기 특성 이해
- 리소스 대기 및 리소스 경합이 있는 위치에 대 한 추세 이해

## <a name="permissions"></a>권한

Query Performance Insight에서 쿼리 텍스트를 보는 데 필요한 **소유자** 또는 **참가자** 권한입니다. **읽기 권한자**는 차트 및 표를 볼 수 있지만 쿼리 텍스트는 볼 수 없습니다.

## <a name="prerequisites"></a>전제 조건

Query Performance Insight가 작동하려면 [쿼리 저장소](concepts-query-store.md)에 데이터가 있어야 합니다.

## <a name="viewing-performance-insights"></a>Performance Insight 보기

Azure Portal의 [Query Performance Insight](concepts-query-performance-insight.md) 보기에는 쿼리 저장소의 핵심 정보가 시각화되어 표시됩니다.

Azure Database for MySQL 서버의 포털 페이지에서 메뉴 모음의 **지능형 성능** 섹션 아래에 있는 **Query Performance Insight** 를 선택 합니다.

### <a name="long-running-queries"></a>장기 실행 쿼리

**장기 실행 쿼리** 탭에는 15 분 간격으로 집계 된 실행 당 평균 기간별 상위 5 개 쿼리가 표시 됩니다. **쿼리 수 드롭다운 목록을** 선택 하 여 더 많은 쿼리를 볼 수 있습니다. 이 작업을 수행하면 특정 쿼리 ID에 대해 차트 색이 변경될 수 있습니다.

차트를 클릭하고 끌어 특정 기간으로 범위를 좁힐 수 있습니다. 또는 확대 및 축소 아이콘을 사용 하 여 각각 더 작거나 더 큰 기간을 볼 수 있습니다.

![Query Performance Insight 장기 실행 쿼리](./media/concepts-query-performance-insight/query-performance-insight-landing-page.png) 

### <a name="wait-statistics"></a>대기 통계

> [!NOTE]
> 대기 통계는 쿼리 성능 문제를 해결 하기 위한 것입니다. 문제 해결을 위해서만 설정 하는 것이 좋습니다. <br>Azure Portal "에 오류 메시지가 표시 되는 경우 *' Microsoft DBforMySQL '에 문제가 발생 했습니다. 에서 요청을 수행할 수 없습니다. 이 문제가 계속 되거나 예기치 않은 경우 지원 서비스에 문의 하세요.* " 대기 통계를 보는 동안 더 작은 기간을 사용 합니다.

대기 통계는 특정 쿼리를 실행 하는 동안 발생 하는 대기 이벤트에 대 한 뷰를 제공 합니다. [MySQL 엔진 설명서](https://go.microsoft.com/fwlink/?linkid=2098206)의 대기 이벤트 형식에 대해 자세히 알아보세요.

서버의 대기 쿼리를 시각화하려면 **대기 통계** 탭을 선택합니다.

대기 통계 보기에 표시 되는 쿼리는 지정 된 시간 간격 동안 가장 큰 대기를 나타내는 쿼리로 그룹화 됩니다.

![Query Performance Insight 대기 통계](./media/concepts-query-performance-insight/query-performance-insight-wait-statistics.png)

## <a name="next-steps"></a>다음 단계

- Azure Database for MySQL [모니터링 및 튜닝](concepts-monitoring.md) 에 대해 자세히 알아보세요.