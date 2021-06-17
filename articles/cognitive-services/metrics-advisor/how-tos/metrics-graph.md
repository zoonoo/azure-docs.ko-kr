---
title: Metrics Advisor 메트릭 그래프
titleSuffix: Azure Cognitive Services
description: 메트릭 그래프를 구성하고 데이터에서 관련 변칙을 시각화하는 방법을 알아봅니다.
services: cognitive-services
author: mrbullwinkle
manager: nitinme
ms.service: applied-ai-services
ms.subservice: metrics-advisor
ms.topic: conceptual
ms.date: 09/08/2020
ms.author: mbullwin
ms.openlocfilehash: d26d7ab61486617c13ec18fc65ba29f5771cf54e
ms.sourcegitcommit: 34feb2a5bdba1351d9fc375c46e62aa40bbd5a1f
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/10/2021
ms.locfileid: "111891341"
---
# <a name="how-to-build-a-metrics-graph-to-analyze-related-metrics"></a>방법: 메트릭 그래프를 작성하여 관련 메트릭 분석

Metrics Advisor의 각 메트릭은 과거 데이터에서 학습하여 미래의 추세를 예측하는 모델에 의해 개별적으로 모니터링됩니다. 각 메트릭에는 별도의 모델이 적용됩니다. 그러나 경우에 따라서는 여러 메트릭이 서로 관련될 수 있으며 여러 메트릭에서 변칙을 분석해야 합니다. **메트릭 그래프** 는 이러한 기능을 지원합니다. 

예를 들어 각 메트릭에 서로 다른 원격 분석 스트림이 있는 경우 Metrics Advisor는 이러한 메트릭을 개별적으로 모니터링합니다. 한 메트릭의 변칙으로 인해 다른 메트릭에 이상이 발생하는 경우 데이터에서 이러한 관계와 근본 원인을 찾는 것이 인시던트를 해결하는 데 도움이 될 수 있습니다. 메트릭 그래프를 사용하면 발견된 변칙의 시각적 토폴로지 그래프를 만들 수 있습니다. 

## <a name="select-a-metric-to-put-the-first-node-to-the-graph"></a>첫 번째 노드를 그래프에 표시할 메트릭 선택

탐색 모음에서 **메트릭 그래프** 탭을 클릭합니다. 메트릭 그래프를 작성하는 첫 번째 단계는 그래프에 노드를 추가하는 것입니다. 페이지 맨 위에서 데이터 피드 및 메트릭을 선택합니다. 아래쪽 패널에 노드가 나타납니다. 

:::image type="content" source="../media/graph/metrics-graph-select.png" alt-text="메트릭 선택":::

## <a name="add-a-noderelation-on-existing-node"></a>기존 노드에 노드/관계 추가

다음엔 다른 노드를 추가하고 기존 노드와의 관계를 지정해야 합니다. 기존 노드를 선택하고 마우스 오른쪽 버튼으로 클릭합니다. 상황에 맞는 메뉴가 여러 가지 옵션과 함께 나타납니다. 

**관계 추가** 를 클릭하면 다른 메트릭을 선택하고 두 노드 간에 관계 유형을 지정할 수 있습니다. 특정 차원 필터를 적용할 수도 있습니다. 

:::image type="content" source="../media/graph/metrics-graph-node-action.png" alt-text="노드 및 관계 추가":::

위의 단계를 반복하면 모든 관련 메트릭 간의 관계를 설명하는 메트릭 그래프가 표시됩니다.
**노드 색에 대한 힌트**
> [!TIP]
> - 메트릭 및 차원 필터를 선택하면 그래프에서 메트릭 및 차원 필터가 동일한 모든 노드가 **<font color=blue>파란색</font>** 으로 표시됩니다.
> - 그래프의 메트릭을 나타내는 선택되지 않은 노드는 **<font color=green>녹색</font>** 으로 표시됩니다.
> - 현재 메트릭에서 변칙이 관찰되면 노드가 **<font color=red>빨간색</font>** 으로 표시됩니다.

## <a name="view-related-metrics-anomaly-status-in-incident-hub"></a>인시던트 허브에서 관련 메트릭 변칙 상태 보기

메트릭 그래프가 작성되면 그래프 내의 메트릭에서 변칙이 검색될 때마다 관련 변칙 상태를 보고 인시던트에 대한 높은 수준의 보기를 얻을 수 있습니다. 

그래프에서 인시던트를 클릭하고 진단 정보 아래에 있는 **교차 메트릭 분석** 까지 아래로 스크롤합니다.

:::image type="content" source="../media/graph/metrics-graph-cross-metrics-analysis.png" alt-text="관련 메트릭 및 변칙 보기":::

## <a name="next-steps"></a>다음 단계

- [피드백을 사용하여 변칙 검색 조정](anomaly-feedback.md)
- [인시던트 진단](diagnose-incident.md)
- [메트릭을 구성하고 구성 감지 미세 조정](configure-metrics.md)
