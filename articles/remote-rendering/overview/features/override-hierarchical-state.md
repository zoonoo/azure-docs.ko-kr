---
title: 계층 상태 재정의
description: 계층 구조 상태 재정의 구성 요소의 개념을 설명 합니다.
author: florianborn71
ms.author: flborn
ms.date: 02/10/2020
ms.topic: article
ms.openlocfilehash: f3be073857cc8583669ab26f306760478479e2ae
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/28/2020
ms.locfileid: "80680792"
---
# <a name="hierarchical-state-override"></a>계층 상태 재정의

대부분의 경우에는 하위 그래프를 숨기 거 나 투명 한 렌더링으로 파트를 전환 하는 등의 방법으로 [모델](../../concepts/models.md)의 부분에 대 한 모양을 동적으로 변경 해야 합니다. 관련 된 각 파트의 재질을 변경 하는 것은 전체 장면 그래프를 반복 하 고 각 노드에서 재료 복제 및 할당을 관리 해야 하기 때문에 실용적이 지 않습니다.

최소한의 `HierarchicalStateOverrideComponent`오버 헤드로이 사용 사례를 수행 하려면를 사용 합니다. 이 구성 요소는 장면 그래프의 임의 분기에서 계층 상태 업데이트를 구현 합니다. 즉, 상태는 장면 그래프의 모든 수준에서 정의 될 수 있으며 새 상태로 재정의 되거나 리프 개체에 적용 될 때까지 계층 구조를 trickles 합니다.

예를 들어, 자동차 모델을 고려 하 고 내부 엔진 파트를 제외 하 고 전체 자동차를 투명 하 게 전환 하려고 합니다. 이 사용 사례는 구성 요소의 두 인스턴스만 포함 합니다.

* 첫 번째 구성 요소는 모델의 루트 노드에 할당 되 고 전체 자동차에 대해 투명 렌더링을 설정 합니다.
* 두 번째 구성 요소는 엔진의 루트 노드에 할당 되며, 상태를 다시 재정의 하 여 상태를 재정의 합니다.

## <a name="features"></a>기능

재정의할 수 있는 고정 상태 집합은 다음과 같습니다.

* **Hidden**: 장면 그래프의 각 망상이 숨겨지거나 표시 됩니다.
* **농도 색**: 렌더링 된 개체는 개별 색조 색 및 색조 가중치를 사용 하 여 색 색조가 될 수 있습니다. 아래 이미지는 휠의 테두리 색 농도를 보여줍니다.
  
  ![색 색조](./media/color-tint.png)

* 을 참조 하세요. 예를 들어 개체의 내부 부분을 **표시**하기 위해 기 하 도형을 반투명 하 게 렌더링 합니다. 다음 이미지는 빨간색 브레이크 caliper를 제외 하 고, 표시 모드에서 렌더링 되는 전체 자동차를 보여 줍니다.

  ![참조](./media/see-through.png)

  > [!IMPORTANT]
  > *TileBasedComposition* [렌더링 모드](../../concepts/rendering-modes.md) 를 사용 하는 경우에만 표시 되는 효과를 사용할 수 있습니다.

* **선택 됨**: 기 하 도형은 [선택 윤곽](outlines.md)을 사용 하 여 렌더링 됩니다.

  ![선택 윤곽](./media/selection-outline.png)

* **DisableCollision**: 기 하 도형은 [공간 쿼리에서](spatial-queries.md)제외 됩니다. **숨겨진** 플래그는 충돌을 해제 하지 않으므로 이러한 두 플래그는 자주 함께 설정 됩니다.

## <a name="hierarchical-overrides"></a>계층 재정의

는 `HierarchicalStateOverrideComponent` 여러 수준의 개체 계층 구조에 연결할 수 있습니다. 엔터티에는 각 형식의 구성 요소가 하나만 있을 수 있으므로 각각 `HierarchicalStateOverrideComponent` 숨겨진, 참조, 선택, 색 농도 및 충돌에 대 한 상태를 관리 합니다.

따라서 각 상태를 다음 중 하나로 설정할 수 있습니다.

* `ForceOn`-이 노드 아래의 모든 메시에 대해 상태를 사용할 수 있습니다.
* `ForceOff`-이 노드 아래에 있는 모든 메시의 상태를 사용할 수 없습니다.
* `InheritFromParent`-이 재정의 구성 요소의 상태는 영향을 받지 않습니다.

상태는 직접 또는 함수를 `SetState` 통해 변경할 수 있습니다.

```cs
HierarchicalStateOverrideComponent component = ...;

// set one state directly
component.HiddenState = HierarchicalEnableState.ForceOn;

// set a state with the SetState function
component.SetState(HierarchicalStates.SeeThrough, HierarchicalEnableState.InheritFromParent);

// set multiple states at once with the SetState function
component.SetState(HierarchicalStates.Hidden | HierarchicalStates.DisableCollision, HierarchicalEnableState.ForceOff);
```

### <a name="tint-color"></a>색조 색

색조 색 재정의는 on/off/inherit 상태와 색조 색 속성이 모두 매우 특수 합니다. 농도 색의 알파 부분은 색조 효과의 무게를 정의 합니다. 0.0로 설정 된 경우 색조 색은 표시 되지 않으며 1.0으로 설정 된 경우 개체는 순수 색조 색으로 렌더링 됩니다. 값 사이에서 최종 색은 농도 색과 혼합 됩니다. 색 애니메이션을 얻기 위해 각 프레임 단위로 색조 색을 변경할 수 있습니다.

## <a name="performance-considerations"></a>성능 고려 사항

인스턴스 `HierarchicalStateOverrideComponent` 자체는 런타임 오버 헤드를 더 추가 하지 않습니다. 그러나 활성 구성 요소 수를 낮게 유지 하는 것이 항상 좋은 방법입니다. 예를 들어 선택한 개체를 강조 표시 하는 선택 시스템을 구현 하는 경우 강조 표시가 제거 될 때 구성 요소를 삭제 하는 것이 좋습니다. 중립 기능을 중심으로 구성 요소를 신속 하 게 추가할 수 있습니다.

투명 렌더링은 표준 렌더링 보다 서버 Gpu에 더 많은 작업을 배치 합니다. 많은 수의 기 하 도형 계층이 표시 되는 장면 그래프의 많은 *부분이 표시 된*것으로 전환 되 면 성능 병목 상태가 될 수 있습니다. [선택 윤곽이](../../overview/features/outlines.md#performance)있는 개체에도 동일 하 게 사용할 수 있습니다.

## <a name="next-steps"></a>다음 단계

* [보여](../../overview/features/outlines.md)
* [렌더링 모드](../../concepts/rendering-modes.md)
* [공간 쿼리](../../overview/features/spatial-queries.md)
