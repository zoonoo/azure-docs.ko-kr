---
title: Metrics Advisor로 인시던트 진단
titleSuffix: Azure Cognitive Services
description: Metrics Advisor를 사용하여 인시던트를 진단하고 데이터의 변칙에 대해 상세히 보는 방법을 알아봅니다.
services: cognitive-services
author: mrbullwinkle
manager: nitinme
ms.service: applied-ai-services
ms.subservice: metrics-advisor
ms.topic: conceptual
ms.date: 08/19/2020
ms.author: mbullwin
ms.openlocfilehash: dd84293ab627c1e379af1e76d259a566998cb0ea
ms.sourcegitcommit: 6a3096e92c5ae2540f2b3fe040bd18b70aa257ae
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/20/2021
ms.locfileid: "112410525"
---
# <a name="how-to-diagnose-an-incident-using-metrics-advisor"></a>방법: Metrics Advisor를 사용한 인시던트 진단

Metrics Advisor는 몇 가지 진단 기능, 검색된 인시던트에 대한 상세 보기 및 근본 원인 분석을 제공합니다. 메트릭에서 변칙 그룹이 검색되면 Metrics Advisor가 변칙을 계층 구조로 그룹화하고 그에 대해 분석합니다.

> [!NOTE]
> 현재 Metrics Advisor는 하나 이상의 차원이 있는 메트릭에 대한 인시던트 진단과, 유형이 *숫자* 인 측정값을 지원합니다. 메트릭에는 각 차원에 대한 SUM 등 집계된 차원 값이 있어야 합니다. 이것으로 진단 계층 구조를 빌드하게 됩니다. Metrics Advisor는 집계된 값의 생성을 지원하는 [**자동 롤업 설정**](onboard-your-data.md#automatic-roll-up-settings)을 제공합니다. 

왼쪽 탐색 창에서 **인시던트 허브** 를 클릭하여 주어진 메트릭의 모든 인시던트를 확인합니다. 페이지 맨 위에서 다른 메트릭을 선택하면 해당 검색 구성과 검색 결과를 확인하고 시간 범위를 변경할 수 있습니다.

> [!TIP]
> 다음과 같은 방법으로도 **인시던트 허브** 로 이동할 수 있습니다.
> * 메트릭에 대한 시각화에서 데이터 포인트를 클릭하고, 표시되는 **피드백 추가** 창의 맨 아래에 있는 링크를 사용합니다.
> * 메트릭에 대해 **인시던트** 탭의 변칙 중 하나를 클릭합니다. 

**개요** 섹션에는 선택한 시간 범위 내의 변칙과 경고 수를 비롯한 검색 결과가 표시됩니다.

:::image type="content" source="../media/diagnostics/incident-hub-overview.png" alt-text="인시던트 허브" lightbox="../media/diagnostics/incident-hub-overview.png":::

선택한 메트릭 및 시간 범위 내에서 검색된 인시던트는 **인시던트 목록** 에 나열됩니다. 인시던트를 필터링하고 정렬하는 옵션이 있습니다. 심각도를 예로 들 수 있습니다. 인시던트 중 하나를 클릭하면 추가 진단을 위한 **인시던트** 페이지로 이동합니다.

:::image type="content" source="../media/diagnostics/incident-list.png" alt-text="인시던트 목록" lightbox="../media/diagnostics/incident-list.png":::

**진단** 섹션에서는 인시던트에 대한 심층 분석을 수행하고 도구를 통해 근본 원인을 식별할 수 있습니다.

:::image type="content" source="../media/diagnostics/diagnose-incident.png" alt-text="인시던트 진단" lightbox="../media/diagnostics/diagnose-incident.png" :::

## <a name="root-cause-advice"></a>근본 원인 조언

변칙 그룹이 메트릭에서 검색되고 인시던트를 유발하면 Metrics Advisor가 인시던트의 근본 원인 분석을 시도합니다. **근본 원인 조언** 은 인시던트의 가능한 원인에 대한 자동 제안을 제공합니다. 차원 내에서 집계된 값이 있는 경우에만 이 기능을 사용할 수 있습니다. 메트릭에 차원이 없으면 근본 원인은 메트릭 자체입니다. 근본 원인은 오른쪽 패널에 나열되며 몇 가지 이유가 나열될 수 있습니다. 테이블에 데이터가 없으면 차원이 분석을 수행하기 위한 요구 사항을 충족하지 않습니다.

:::image type="content" source="../media/diagnostics/root-cause-advice.png" lightbox="../media/diagnostics/root-cause-advice.png" alt-text="근본 원인 조언":::


근본 원인 메트릭이 특정 차원과 함께 제공되는 경우 **메트릭으로 이동** 을 클릭하여 메트릭에 대한 자세한 정보를 볼 수 있습니다.

## <a name="incident-tree"></a>인시던트 트리

Metrics Advisor는 가능한 근본 원인에 대한 자동 분석과 함께 **인시던트 트리** 를 통한 수동 근본 원인 분석을 지원합니다. 인시던트 페이지에는 **빠른 진단** 트리와 **대화형 트리** 등 두 종류의 인시던트 트리가 있습니다.

빠른 진단 트리는 현재 인시던트 진단용이며 루트 노드가 현재 인시던트 루트 노드로 한정됩니다. 트리 노드를 클릭하여 확장 및 축소할 수 있고, 트리 위의 차트에 현재 인시던트 시리즈와 함께 해당 시리즈가 표시됩니다.

대화형 트리를 통해 현재 인시던트와 이전 인시던트는 물론 관련이 있는 인시던트를 진단할 수 있습니다. 대화형 트리를 사용할 때 노드를 클릭하면 작업 메뉴가 열립니다. 여기서 루트 노드로 드릴업할 차원과, 각 노드에 대해 드릴다운할 차원을 선택할 수 있습니다. 맨 위의 차원 목록에서 취소 단추를 클릭하면 이 차원에 대한 드릴업 또는 드릴다운을 제거할 수 있습니다. 노드를 마우스 왼쪽 단추를 클릭하고 선택하면 차트에 현재 인시던트 계열과 함께 해당 계열이 표시됩니다.

:::image type="content" source="../media/diagnostics/incident-tree.png" alt-text="인시던트 트리" lightbox="../media/diagnostics/incident-tree.png" :::

## <a name="anomaly-drill-down"></a>변칙 드릴다운

인시던트 정보를 볼 때 다양한 차원과 타임 스탬프 등 더 상세한 정보가 필요할 수 있습니다. 데이터에 하나 이상의 차원이 있다면 드릴다운 기능을 통해 더 상세한 보기를 가져올 수 있습니다. 

드릴다운 기능을 사용하려면 **인시던트 허브** 에서 **메트릭 드릴다운** 탭을 클릭합니다. 

:::image type="content" source="../media/diagnostics/metric-drilling.png" lightbox="../media/diagnostics/metric-drilling.png" alt-text="메트릭 드릴다운":::

**차원** 설정은 인시던트의 차원 목록으로, 각각에 대해 사용 가능한 다른 차원 값을 선택할 수 있습니다. 차원 값 변경 후 **타임스탬프** 설정을 통해 다른 시점에서의 현재 인시던트를 볼 수 있습니다.

### <a name="select-drilling-options-and-choose-a-dimension"></a>드릴다운 옵션 선택 및 차원 선택

**드릴다운** 및 **수평 비교** 라는 두 가지 유형의 드릴다운 옵션이 있습니다.

> [!Note]
> 1. 드릴다운의 경우 현재 선택한 차원을 제외한 다른 차원 값의 데이터를 살펴볼 수 있습니다. 
> 2. 수평 비교의 경우 종료된 차원을 제외한 다른 차원 값의 데이터를 살펴볼 수 있습니다.

:::image type="content" source="../media/diagnostics/drill-down-dimension.png" lightbox="../media/diagnostics/drill-down-dimension.png" alt-text="차원 드릴다운":::

### <a name="value-comparison-for-different-dimension-values"></a>다른 차원 값에 대한 값 비교

드릴다운 탭의 두 번째 섹션은 다양한 차원 값에 대한 비교가 있는 테이블입니다. 값, 기준 값, 차이 값, 델타 값 및 변칙 여부를 포함합니다.
 
:::image type="content" source="../media/diagnostics/drill-down-comparison.png" alt-text="비교 드릴다운" lightbox="../media/diagnostics/drill-down-comparison.png":::


### <a name="value-and-expected-value-comparisons-for-different-dimension-value"></a>다른 차원 값에 대한 값 및 예상 값 비교

드릴다운 탭의 세 번째 섹션은 다양한 차원 값에 대한 값과 예상 값을 포함하는 히스토그램입니다. 이 히스토그램은 값과 예상 값의 차이를 기준으로 정렬됩니다. 가장 큰 영향을 주는 예외 값을 쉽게 찾을 수 있습니다. 예를 들어, 위 그림에서 종료 값을 제외하고 **US7** 이 변칙에 가장 기여하는 것을 확인할 수 있습니다.

:::image type="content" source="../media/diagnostics/drill-down-table.png" alt-text="테이블 드릴다운" lightbox="../media/diagnostics/drill-down-table.png":::

### <a name="raw-value-visualization"></a>원시 값 시각화
드릴다운 탭의 마지막 부분은 원시 값의 꺾은선형 차트입니다. 이 차트가 제공되면 세부 정보를 보기 위해 메트릭 페이지로 이동하지 않아도 됩니다.

:::image type="content" source="../media/diagnostics/drill-down-line-chart.png" alt-text="꺾은선형 차트 드릴다운" lightbox="../media/diagnostics/drill-down-line-chart.png":::

## <a name="view-similar-anomalies-using-time-series-clustering"></a>시계열 클러스터링을 사용하여 유사 변칙 보기

인시던트를 볼 때 **유사 시계열 클러스터링** 탭을 사용하여 연관 있는 다양한 계열을 볼 수 있습니다. 한 그룹의 계열은 함께 요약됩니다. 위의 그림에서는 둘 이상의 계열 그룹이 있음을 알 수 있습니다. 이 기능은 다음 요구 사항에 부합하는 경우에만 사용 가능합니다.

1. 메트릭에 하나 이상의 차원 또는 차원 값이 있어야 합니다.
2. 한 메트릭의 계열은 추세가 동일해야 합니다.

사용 가능한 차원은 탭 맨 위에 나열되며 선택하여 계열을 지정할 수 있습니다.

:::image type="content" source="../media/diagnostics/series-group.png" lightbox="../media/diagnostics/series-group.png" alt-text="계열 그룹":::

## <a name="compare-time-series"></a>시계열 비교

경우에 따라 특정 시계열에서 변칙이 검색되면 단일 시각화 안에서 여러 다른 계열과 비교 하는 것이 유용합니다. **비교 도구** 탭을 클릭한 다음, 파란색 **+ 추가** 단추를 클릭합니다. 

:::image type="content" source="../media/diagnostics/add-series.png" alt-text="비교할 계열 추가" lightbox="../media/diagnostics/add-series.png":::

데이터 피드에서 계열을 선택합니다. 동일한 세분성 또는 다른 세분성을 선택할 수 있습니다. 대상 차원을 선택하고 계열 추세를 로드한 다음, **확인** 을 클릭하여 이전 계열과 비교 합니다. 계열이 하나의 시각화 안에 함께 배치됩니다. 계속해서 계열을 더 추가하여 비교하고 추가적인 인사이트를 얻을 수 있습니다. **비교 도구** 탭의 맨 위에 있는 드롭다운 메뉴를 클릭하여 시간 이동 기간의 시계열 데이터를 비교합니다.  

> [!Warning]
> 비교를 위해 시계열 데이터 분석을 데이터 포인트로 이동해야 할 수 있으므로 데이터의 세분성에서 이것이 지원되어야 합니다. 예를 들어 데이터가 주간인데 **일일** 비교를 사용한다면 결과를 얻지 못합니다. 이 예제에서는 대신 **월별** 비교를 사용합니다.

시간 이동 비교를 선택한 후에는 데이터 값, 델타 값 또는 백분율 델타를 비교할지 여부를 선택할 수 있습니다.

> [!Note]
> * **데이터 값** 은 원시 데이터 값입니다.
> * **델타 값** 은 원시 값과 비교된 값의 차이입니다.
> * **백분율 델타 값** 은 원시 값과 비교된 값의 차이를 비교된 값으로 나눈 값입니다.

## <a name="related-incidents-across-metrics"></a>메트릭 간 교차 관련 인시던트

경우에 따라 다른 메트릭의 인시던트 또는 다른 메트릭의 관련 인시던트를 확인해야 할 수도 있습니다. **교차 메트릭 분석** 섹션에서 관련 인시던트 목록을 확인할 수 있습니다. 

:::image type="content" source="../media/graph/metrics-graph-cross-metrics-analysis.png" alt-text="메트릭 간 교차 관련 인시던트":::

현재 메트릭에 대한 관련 인시던트를 보려면 메트릭 간의 관계를 추가해야 합니다. **메트릭 그래프 설정** 을 클릭하여 관계를 추가합니다. 차원 이름이 동일한 메트릭만 관련될 수 있습니다. 다음 매개 변수를 사용합니다.

- 현재 데이터 피드 및 메트릭: 현재 인시던트의 데이터 피드와 메트릭
- 방향: 두 메트릭 간의 관계 방향 (현재의 관련 인시던트 목록에는 영향을 주지 않음)
- 다른 데이터 피드 및 메트릭: 현재 메트릭과 연결할 데이터 피드와 메트릭


## <a name="next-steps"></a>다음 단계 

- [피드백을 사용하여 변칙 검색 조정](anomaly-feedback.md)
- [메트릭을 구성하고 구성 감지 미세 조정](configure-metrics.md)
