---
title: 계층 적 상태 재정의
description: 계층 적 상태 재정의 구성 요소의 개념을 설명 합니다.
author: florianborn71
ms.author: flborn
ms.date: 02/10/2020
ms.topic: article
ms.openlocfilehash: f3be073857cc8583669ab26f306760478479e2ae
ms.sourcegitcommit: 642a297b1c279454df792ca21fdaa9513b5c2f8b
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/06/2020
ms.locfileid: "80680792"
---
# <a name="hierarchical-state-override"></a>계층 적 상태 재정의

대부분의 경우 하위 그래프 숨기기 또는 투명 렌더링으로 부품 전환과 같은 [모델](../../concepts/models.md)부품의 모양을 동적으로 변경해야 합니다. 관련된 각 부품의 재질을 변경하는 것은 전체 장면 그래프를 반복하고 각 노드에서 재질 복제 및 할당을 관리해야 하므로 실용적이지 않습니다.

최소한의 오버헤드로 이 사용 사례를 수행하려면 `HierarchicalStateOverrideComponent`을 사용합니다. 이 구성 요소는 장면 그래프의 임의의 분기에 계층 적 상태 업데이트를 구현합니다. 즉, 장면 그래프의 모든 수준에서 상태를 정의할 수 있으며 새 상태로 재정의되거나 리프 오브젝트에 적용될 때까지 계층 구조아래로 흘러갑니다.

예를 들어, 자동차의 모델을 고려하고 내부 엔진 부분을 제외하고 전체 자동차를 투명하게 전환하려고 합니다. 이 사용 사례에는 구성 요소의 인스턴스가 두 개만 포함됩니다.

* 첫 번째 구성요소는 모델의 루트 노드에 할당되고 전체 차량에 대해 투명 렌더링을 켭니다.
* 두 번째 구성 요소는 엔진의 루트 노드에 할당되고 시스루 모드를 명시적으로 해제하여 상태를 다시 재정의합니다.

## <a name="features"></a>기능

재정의할 수 있는 고정된 상태 집합은 다음과 같습니다.

* **숨김**: 장면 그래프의 각 메싱이 숨김 또는 표시됩니다.
* **색조**: 렌더링된 오브젝트는 개별 색조 및 색조 가중치로 색상을 칠할 수 있습니다. 아래 이미지는 휠의 테두리에 색조를 입힐 수 있는 색상을 보여줍니다.
  
  ![컬러 틴트](./media/color-tint.png)

* **시스루**: 형상이 반투명하게 렌더링됩니다(예: 오브젝트의 내부 부분 표시). 다음 이미지는 빨간색 브레이크 캘리퍼를 제외한 전체 차량이 시스루 모드로 렌더링되는 것을 보여줍니다.

  ![시스루](./media/see-through.png)

  > [!IMPORTANT]
  > 시스루 효과는 *타일기반컴포지션* [렌더링 모드를](../../concepts/rendering-modes.md) 사용할 때만 작동합니다.

* **선택**: 형상이 [선택 윤곽선으로](outlines.md)렌더링됩니다.

  ![선택 윤곽선](./media/selection-outline.png)

* **충돌 해제**: 형상이 [공간 쿼리에서 제외됩니다.](spatial-queries.md) **숨겨진** 플래그는 충돌을 해제하지 않으므로 이 두 플래그는 종종 함께 설정됩니다.

## <a name="hierarchical-overrides"></a>계층적 재정의

개체 `HierarchicalStateOverrideComponent` 계층 구조의 여러 수준에 연결할 수 있습니다. 엔터티에는 각 형식의 구성 요소가 하나만 `HierarchicalStateOverrideComponent` 있을 수 있기 때문에 각 구성 요소는 숨겨진, 시스루, 선택됨, 색상 색조 및 충돌에 대한 상태를 관리합니다.

따라서 각 상태는 다음 중 하나로 설정할 수 있습니다.

* `ForceOn`- 상태는이 노드의 모든 메쉬에 대해 활성화됩니다
* `ForceOff`- 이 노드 안팎의 모든 메시에 대해 상태가 비활성화됩니다.
* `InheritFromParent`- 상태는 이 재정의 구성 요소의 영향을 받지 않습니다.

직접 또는 함수를 `SetState` 통해 상태를 변경할 수 있습니다.

```cs
HierarchicalStateOverrideComponent component = ...;

// set one state directly
component.HiddenState = HierarchicalEnableState.ForceOn;

// set a state with the SetState function
component.SetState(HierarchicalStates.SeeThrough, HierarchicalEnableState.InheritFromParent);

// set multiple states at once with the SetState function
component.SetState(HierarchicalStates.Hidden | HierarchicalStates.DisableCollision, HierarchicalEnableState.ForceOff);
```

### <a name="tint-color"></a>색조 색상

색조 색상 재정의는 온/오프/상속 상태와 색조 색상 속성이 모두 있다는 점에서 약간 특별합니다. 색조 색상의 알파 부분은 색조 효과의 가중치를 정의합니다: 0.0으로 설정하면 색조 색상이 표시되지 않으며 1.0으로 설정하면 오브젝트가 순수한 색조로 렌더링됩니다. 중간 값의 경우 최종 색상이 색조 색상과 혼합됩니다. 색조 색상은 색상 애니메이션을 달성하기 위해 프레임단위로 변경할 수 있습니다.

## <a name="performance-considerations"></a>성능 고려 사항

자체 인스턴스는 `HierarchicalStateOverrideComponent` 런타임 오버헤드를 많이 추가하지 않습니다. 그러나 활성 구성 요소의 수를 낮게 유지하는 것이 항상 좋은 방법입니다. 예를 들어 선택한 오브젝트를 강조 표시하는 선택 시스템을 구현할 때 강조 표시가 제거될 때 구성 요소를 삭제하는 것이 좋습니다. 중립 피쳐로 구성 요소를 유지하면 빠르게 추가할 수 있습니다.

투명 렌더링은 표준 렌더링보다 서버의 GPU에 더 많은 워크로드를 가합니다. 장면 그래프의 큰 부분이 *시스루로*전환되고 많은 지오메트리 레이어가 표시되면 성능 병목 현상이 발생할 수 있습니다. 선택 윤곽선이 있는 객체에 대해도 [마찬가지입니다.](../../overview/features/outlines.md#performance)

## <a name="next-steps"></a>다음 단계

* [윤곽선](../../overview/features/outlines.md)
* [렌더링 모드](../../concepts/rendering-modes.md)
* [공간 쿼리](../../overview/features/spatial-queries.md)
