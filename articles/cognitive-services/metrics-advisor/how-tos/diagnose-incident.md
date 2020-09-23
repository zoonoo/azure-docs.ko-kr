---
title: 메트릭 관리자를 사용 하 여 인시던트 진단
titleSuffix: Azure Cognitive Services
description: 메트릭 Advisor를 사용 하 여 인시던트를 진단 하 고 데이터의 비정상 상황에 대 한 자세한 보기를 확인 하는 방법을 알아봅니다.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: metrics-advisor
ms.topic: conceptual
ms.date: 08/19/2020
ms.author: aahi
ms.openlocfilehash: 7acd895832307d68c259139704565962fe534d22
ms.sourcegitcommit: 53acd9895a4a395efa6d7cd41d7f78e392b9cfbe
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/22/2020
ms.locfileid: "90938364"
---
# <a name="how-to-diagnose-an-incident-using-metrics-advisor"></a>방법: 메트릭 관리자를 사용 하 여 인시던트 진단

메트릭 관리자는 진단을 위한 몇 가지 기능을 제공 하 고 검색 된 인시던트를 자세히 보여 주며 근본 원인 분석을 제공 합니다. 메트릭에 대 한 변칙 그룹이 검색 되 면 메트릭 관리자는 변칙을 계층으로 그룹화 하 고 그 위에 분석 합니다.

> [!NOTE]
> 현재 메트릭 관리자는 하나 이상의 차원이 있는 메트릭에 대 한 인시던트 진단을 지원 하 고  *숫자* 유형을 사용 하는 측정값을 지원 합니다. 메트릭에는 진단 계층 구조를 작성 하는 데 사용 되는 각 차원에 대 한 합계와 같은 집계 차원 값이 있어야 합니다. 메트릭 관리자는 집계 된 값을 생성 하는 데 도움이 되는 [**자동 롤업 설정을**](onboard-your-data.md#automatic-roll-up-settings) 제공 합니다. 

왼쪽 탐색 창에서 **인시던트 허브** 를 클릭 하 여 지정 된 메트릭의 모든 인시던트를 확인 합니다. 페이지 맨 위에서 다른 메트릭을 선택 하 여 검색 구성 및 검색 결과를 확인 하 고 시간 범위를 변경할 수 있습니다.

> [!TIP]
> 다음과 같은 방법으로 **인시던트 허브** 에 액세스할 수도 있습니다.
> * 메트릭에 대 한 시각화에서 데이터 요소를 클릭 하 고 표시 되는 **피드백 추가** 창의 맨 아래에 있는 링크를 사용 합니다.
> * 메트릭에 대 한 **인시던트** 탭의 비정상 중 하나를 클릭 합니다. 

**개요** 섹션에는 선택한 시간 범위 내의 비정상 및 경고 수를 포함 하 여 검색 결과가 포함 됩니다.

:::image type="content" source="../media/diagnostics/incident-hub-overview.png" alt-text="인시던트 허브" lightbox="../media/diagnostics/incident-hub-overview.png":::

선택한 메트릭 및 시간 범위 내에서 발견 된 인시던트는 **인시던트 목록**에 나열 됩니다. 인시던트를 필터링 하 고 순서를 정렬 하는 옵션이 있습니다. 예를 들어 심각도로 합니다. 인시던트 중 하나를 클릭 하 여 추가 진단을 위한 **인시던트** 페이지로 이동 합니다.

:::image type="content" source="../media/diagnostics/incident-list.png" alt-text="인시던트 목록" lightbox="../media/diagnostics/incident-list.png":::

**진단** 섹션에서 인시던트에 대 한 심층 분석을 수행 하 고 근본 원인을 식별 하는 도구를 사용할 수 있습니다.

:::image type="content" source="../media/diagnostics/diagnose-incident.png" alt-text="인시던트 진단" lightbox="../media/diagnostics/diagnose-incident.png" :::

## <a name="root-cause-advice"></a>근본 원인 조언

메트릭에 오류가 발생 하 여 문제가 발생 하는 경우 메트릭 관리자는 인시던트의 근본 원인을 분석 하려고 시도 합니다. **근본 원인 충고** 는 사고의 원인이 될 가능성이 있는 자동 제안을 제공 합니다. 차원 내에서 집계 된 값이 있는 경우에만이 기능을 사용할 수 있습니다. 메트릭에 차원이 없으면 근본적인 원인이 됩니다. 근본 원인은 오른쪽 패널에 나열 되며 몇 가지 이유가 나열 될 수 있습니다. 테이블에 데이터가 없으면 차원이 분석을 수행 하기 위한 요구 사항을 충족 하지 않습니다.

:::image type="content" source="../media/diagnostics/root-cause-advice.png" lightbox="../media/diagnostics/root-cause-advice.png" alt-text="근본 원인 조언":::


근본 원인 메트릭이 특정 차원과 함께 제공 되는 경우 메트릭 **으로 이동** 을 클릭 하 여 메트릭에 대 한 자세한 정보를 볼 수 있습니다.

## <a name="incident-tree"></a>인시던트 트리

메트릭 관리자는 잠재적 근본 원인에 대 한 자동화 된 분석과 함께 **인시던트 트리**를 사용 하 여 수동 근본 원인 분석을 지원 합니다. 인시던트 페이지에는 **빠른 진단** 트리와 **대화형 트리와**같은 두 가지 종류의 인시던트 트리가 있습니다.

빠른 진단 트리는 현재 인시던트를 진단 하는 데 사용할 수 있으며 루트 노드는 현재 인시던트 루트 노드로 제한 됩니다. 트리 노드를 클릭 하 여 확장 하 고 축소할 수 있으며 해당 계열은 트리 위의 차트에 현재 인시던트 계열과 함께 표시 됩니다.

대화형 트리를 사용 하 여 현재 인시던트 뿐만 아니라 이전 인시던트 및 관련 된 인시던트를 진단할 수 있습니다. 대화형 트리를 사용 하는 경우 노드를 마우스 오른쪽 단추로 클릭 하 여 작업 메뉴를 엽니다. 여기서 루트 노드를 통해 드릴업 할 차원을 선택 하 고 각 노드에 대해 드릴 다운할 차원을 선택할 수 있습니다. 위쪽의 차원 목록에서 취소 단추를 클릭 하면이 차원에서 드릴 다운 또는 축소를 제거할 수 있습니다. 노드를 마우스 왼쪽 단추를 클릭 하 여 선택 하 고 차트에 현재 인시던트 계열과 함께 해당 계열을 표시 합니다.

:::image type="content" source="../media/diagnostics/incident-tree.png" alt-text="인시던트 트리" lightbox="../media/diagnostics/incident-tree.png" :::

## <a name="anomaly-drill-down"></a>변칙 드릴 다운

인시던트 정보를 볼 때 다양 한 차원과 타임 스탬프 등에 대 한 자세한 정보를 확인 해야 할 수 있습니다. 데이터에 차원이 하나 이상 있는 경우 드릴 다운 함수를 사용 하 여 보다 자세한 뷰를 얻을 수 있습니다. 

드릴 다운 함수를 사용 하려면 **인시던트 허브**에서 **메트릭 드릴링** 탭을 클릭 합니다. 

:::image type="content" source="../media/diagnostics/metric-drilling.png" lightbox="../media/diagnostics/metric-drilling.png" alt-text="메트릭 드릴링 ":::

**차원** 설정은 인시던트의 차원 목록으로, 각각에 대해 사용 가능한 다른 차원 값을 선택할 수 있습니다. 차원 값이 변경 된 후 **타임 스탬프** 설정을 사용 하면 다른 시간에 현재 인시던트를 볼 수 있습니다.

### <a name="select-drilling-options-and-choose-a-dimension"></a>드릴 옵션을 선택 하 고 차원 선택

**드릴 다운 및** **수평 비교**라는 두 가지 유형의 드릴 다운 옵션이 있습니다.

> [!Note]
> 1. 드릴 다운의 경우 선택한 currenly 차원을 제외 하 고 다른 차원 값의 데이터를 탐색할 수 있습니다. 
> 2. 가로 비교의 경우 모든 차원을 제외 하 고 다른 차원 값의 데이터를 탐색할 수 있습니다.

:::image type="content" source="../media/diagnostics/drill-down-dimension.png"  lightbox="../media/diagnostics/drill-down-dimension.png" alt-text="드릴 다운 차원":::

### <a name="value-comparison-for-different-dimension-values"></a>다른 차원 값에 대 한 값 비교

드릴 다운 탭의 두 번째 섹션은 다른 차원 값에 대 한 비교를 포함 하는 테이블입니다. 값, 기준 값, 차이 값, 델타 값 및 비정상 인지 여부를 포함 합니다.
 
:::image type="content" source="../media/diagnostics/drill-down-comparison.png" alt-text="드릴 다운 비교" lightbox="../media/diagnostics/drill-down-comparison.png":::


### <a name="value-and-expected-value-comparisons-for-different-dimension-value"></a>다른 차원 값에 대 한 값 및 예상 값 비교

드릴 다운 탭의 세 번째 섹션은 다양 한 차원 값에 대 한 값과 예상 값을 포함 하는 히스토그램입니다. 히스토그램은 값과 예상 값의 차이를 기준으로 정렬 됩니다. 가장 큰 영향을 주는 예기치 않은 값을 쉽게 찾을 수 있습니다. 예를 들어 위 그림에서 모든 값을 제외 하 고 **US7** 는 변칙에 가장 적합 한 것을 찾을 수 있습니다.

:::image type="content" source="../media/diagnostics/drill-down-table.png" alt-text="드릴 다운 테이블" lightbox="../media/diagnostics/drill-down-table.png":::

### <a name="raw-value-visualization"></a>원시 값 시각화
드릴 다운 탭의 마지막 부분은 원시 값의 꺾은선형 차트입니다. 이 차트가 제공 되 면 메트릭 페이지로 이동 하 여 세부 정보를 볼 필요가 없습니다.

:::image type="content" source="../media/diagnostics/drill-down-line-chart.png" alt-text="드릴 다운 꺾은선형 차트" lightbox="../media/diagnostics/drill-down-line-chart.png":::

## <a name="view-similar-anomalies-using-time-series-clustering"></a>시계열 클러스터링을 사용 하 여 유사한 변칙 보기

인시던트를 볼 때 **비슷한 시계열-클러스터링** 탭을 사용 하 여 관련 된 다양 한 계열을 볼 수 있습니다. 한 그룹의 계열이 함께 요약 됩니다. 위의 그림에서 두 개 이상의 계열 그룹이 있음을 알 수 있습니다. 다음 요구 사항을 충족 하는 경우에만이 기능을 사용할 수 있습니다.

1. 메트릭에는 차원이 나 차원 값이 하나 이상 있어야 합니다.
2. 한 메트릭의 계열의 추세는 동일 해야 합니다.

사용 가능한 차원은 탭 맨 위에 나열 되며 계열을 지정 하도록 선택할 수 있습니다.

:::image type="content" source="../media/diagnostics/series-group.png" lightbox="../media/diagnostics/series-group.png"alt-text="계열 그룹":::

## <a name="compare-time-series"></a>시계열 비교

경우에 따라 특정 시계열에서 변칙이 검색 되 면 단일 시각화에서 여러 다른 계열과 비교 하는 것이 좋습니다. **비교 도구** 탭을 클릭 한 다음 파란색 **+ 추가** 단추를 클릭 합니다. 

:::image type="content" source="../media/diagnostics/add-series.png" alt-text="비교할 계열 추가" lightbox="../media/diagnostics/add-series.png":::

데이터 피드에서 계열을 선택 합니다. 동일한 세분성 또는 다른 세분성을 선택할 수 있습니다. 대상 차원을 선택 하 고 계열 추세를 로드 한 다음 **확인** 을 클릭 하 여 이전 계열과 비교 합니다. 계열은 하나의 시각화로 함께 배치 됩니다. 비교를 위해 계속 해 서 더 많은 계열을 추가 하 고 자세한 정보를 얻을 수 있습니다. **비교 도구** 탭의 맨 위에 있는 드롭다운 메뉴를 클릭 하 여 일정 시간 동안 시계열 데이터를 비교 하 고 이동 합니다.  

> [!Warning]
> 이동 된 비교를 사용 하도록 설정 하려면 데이터의 세분성이이를 지원 해야 합니다. 예를 들어 데이터를 매주로 사용 하 **고 하루 종일 비교를** 사용 하는 경우 결과를 얻을 수 없습니다. 이 예에서는 month **over month** 비교를 대신 사용 합니다.

이동 된 비교를 선택한 후에는 데이터 값, 델타 값 또는 백분율 델타를 비교할지 여부를 선택할 수 있습니다.

> [!Note]
> * **데이터 값** 은 원시 데이터 값입니다.
> * **델타 값** 은 원시 값과 비교 된 값의 차이입니다.
> * **백분율 델타 값** 은 원시 값과 비교 된 값의 차이를 비교 값으로 나눈 값입니다.

## <a name="related-incidents-across-metrics"></a>메트릭에 걸친 관련 인시던트

경우에 따라 다른 메트릭의 인시던트 또는 다른 메트릭의 관련 인시던트를 확인 해야 할 수도 있습니다. **교차 메트릭 분석** 섹션에서 관련 인시던트 목록을 찾을 수 있습니다. 

:::image type="content" source="../media/graph/metrics-graph-cross-metrics-analysis.png" alt-text="메트릭에 걸친 관련 인시던트":::

현재 메트릭에 대 한 관련 인시던트를 보려면 메트릭 간의 관계를 추가 해야 합니다. **메트릭 그래프 설정** 을 클릭 하 여 관계를 추가 합니다. 차원 이름이 동일한 메트릭만 관련 될 수 있습니다. 다음 매개 변수를 사용합니다.

- 현재 데이터 피드 & 메트릭: 현재 인시던트의 데이터 피드 및 메트릭입니다.
- 방향: 두 메트릭 간의 관계 방향입니다. (관련 인시던트 목록에는 영향을 주지 않음)
- 다른 데이터 피드 & 메트릭: 현재 메트릭과 연결할 데이터 피드 및 메트릭입니다.


## <a name="next-steps"></a>다음 단계 

- [피드백을 사용하여 변칙 검색 조정](anomaly-feedback.md)
- [메트릭을 구성하고 구성 감지 미세 조정](configure-metrics.md)