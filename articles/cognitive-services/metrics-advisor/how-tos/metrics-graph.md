---
title: 메트릭 관리자 메트릭 그래프
titleSuffix: Azure Cognitive Services
description: 메트릭 그래프를 구성 하 고 데이터에서 관련 된 변칙을 시각화 하는 방법을 설명 합니다.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: metrics-advisor
ms.topic: conceptual
ms.date: 09/08/2020
ms.author: aahi
ms.openlocfilehash: 09ea16f07973757b169f21c7c3f909a24651daa4
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/09/2020
ms.locfileid: "90940648"
---
# <a name="how-to-build-a-metrics-graph-to-analyze-related-metrics"></a>방법: 메트릭 그래프를 작성 하 여 관련 메트릭 분석

메트릭 관리자의 각 메트릭은 기록 데이터를 학습 하 여 미래 추세를 예측 하는 모델에 의해 별도로 모니터링 됩니다. 각 메트릭에는 적용 되는 별도의 모델이 있습니다. 그러나 일부 경우에는 여러 메트릭이 서로 관련 될 수 있으며, 여러 메트릭에 대해 변칙을 분석 해야 합니다. **메트릭 그래프** 는이를 지원 합니다. 

예를 들어 별도의 메트릭에 여러 원격 분석 스트림이 있는 경우 메트릭 관리자는 별도로 모니터링 합니다. 한 메트릭의 비정상 상황에서 다른 오류가 발생 하는 경우 문제를 해결 하는 경우 이러한 관계와 데이터의 근본 원인을 찾는 것이 유용할 수 있습니다. 메트릭 그래프를 사용 하면 발견 된 변칙의 시각적 토폴로지 그래프를 만들 수 있습니다. 

## <a name="select-a-metric-to-put-the-first-node-to-the-graph"></a>그래프에 첫 번째 노드를 넣을 메트릭을 선택 합니다.

탐색 모음에서 **메트릭 그래프** 탭을 클릭 합니다. 메트릭 그래프를 작성 하는 첫 번째 단계는 노드를 그래프에 배치 하는 것입니다. 페이지 위쪽에서 데이터 피드 및 메트릭을 선택 합니다. 노드가 아래쪽 패널에 표시 됩니다. 

:::image type="content" source="../media/graph/metrics-graph-select.png" alt-text="메트릭 선택":::

## <a name="add-a-noderelation-on-existing-node"></a>기존 노드에 노드/관계 추가

다음으로, 다른 노드를 추가 하 고 기존 노드에 대 한 관계를 지정 해야 합니다. 기존 노드를 선택 하 고 마우스 오른쪽 단추를 클릭 합니다. 상황에 맞는 메뉴에 몇 가지 옵션이 표시 됩니다. 

**관계 추가**를 클릭 하면 다른 메트릭을 선택 하 고 두 노드 간의 관계 유형을 지정할 수 있습니다. 특정 차원 필터를 적용할 수도 있습니다. 

:::image type="content" source="../media/graph/metrics-graph-node-action.png" alt-text="메트릭 선택":::

위의 단계를 반복 하면 관련 된 모든 메트릭 간의 관계를 설명 하는 메트릭 그래프가 만들어집니다.
**노드 색에 대 한 힌트**
> [!TIP]
> - 메트릭 및 차원 필터를 선택 하면 그래프에서 동일한 메트릭과 차원 필터를 사용 하는 모든 노드가 **<font color=blue>파란색</font>** 으로 표시 됩니다.
> - 그래프의 메트릭을 나타내는 선택 하지 않은 노드는 **<font color=green>녹색</font>** 으로 표시 됩니다.
> - 현재 메트릭에 비정상이 관찰 된 경우 노드는 **<font color=red>빨간색</font>** 으로 나타납니다.

## <a name="view-related-metrics-anomaly-status-in-incident-hub"></a>인시던트 허브에서 관련 메트릭 변칙 상태 보기

메트릭 그래프가 작성 될 때 그래프 내의 메트릭에 변칙이 검색 될 때마다 관련 비정상 상태를 확인 하 고 문제에 대 한 상위 수준 보기를 가져올 수 있습니다. 

그래프 내에서 인시던트를 클릭 하 고 진단 정보 아래의 **교차 메트릭 분석**까지 아래로 스크롤합니다.

:::image type="content" source="../media/graph/metrics-graph-cross-metrics-analysis.png" alt-text="메트릭 선택":::

## <a name="next-steps"></a>다음 단계

- [피드백을 사용하여 변칙 검색 조정](anomaly-feedback.md)
- [인시던트를 진단](diagnose-incident.md)합니다.
- [메트릭을 구성하고 구성 감지 미세 조정](configure-metrics.md)
