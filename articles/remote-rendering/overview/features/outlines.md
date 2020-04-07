---
title: 개요 렌더링
description: 선택 개요 렌더링을 수행하는 방법에 대해 설명합니다.
author: florianborn71
ms.author: flborn
ms.date: 02/11/2020
ms.topic: article
ms.openlocfilehash: 8b52dbe8cd12e51c42677ce37acbd57ad551ec50
ms.sourcegitcommit: 642a297b1c279454df792ca21fdaa9513b5c2f8b
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/06/2020
ms.locfileid: "80680831"
---
# <a name="outline-rendering"></a>개요 렌더링

선택된 오브젝트는 [계층 상태 재정의 구성 요소를](../../overview/features/override-hierarchical-state.md)통해 윤곽선 렌더링을 추가하여 시각적으로 강조 표시할 수 있습니다. 이 장에서는 클라이언트 API를 통해 개요 렌더링에 대한 전역 매개 변수가 어떻게 변경되는지 설명합니다.

윤곽선 속성은 전역 설정입니다. 윤곽선 렌더링을 사용하는 모든 오브젝트는 동일한 설정을 사용합니다.

## <a name="parameters-for-outlinesettings"></a>에 대한 매개 변수`OutlineSettings`

클래스는 `OutlineSettings` 전역 윤곽선 속성과 관련된 설정을 보유합니다. 다음 멤버를 노출합니다.

| 매개 변수      | Type    | Description                                             |
|----------------|---------|---------------------------------------------------------|
| `Color`          | 컬러4Ub | 윤곽선을 그리는 데 사용되는 색상입니다. 알파 부분은 무시됩니다.         |
| `PulseRateHz`    | float   | 윤곽선이 초당 진동하는 속도|
| `PulseIntensity` | float   | 윤곽선 펄스 효과의 강도입니다. 맥동이 없는 경우 0.0, 전체 펄스의 경우 1.0 사이여야 합니다. 강도는 암시적으로 윤곽선의 최소 불투명도를 로 `MinOpacity = 1.0 - PulseIntensity`설정합니다. |

![](./media/outlines.png) 매개변수를 `color` 노란색(왼쪽)에서 자홍색(가운데)으로 `pulseIntensity` 변경하고 0에서 0.8(오른쪽)으로 변경하는 효과의 윤곽선입니다.

## <a name="example"></a>예제

다음 코드는 API를 통해 개요 매개 변수를 설정하는 예제를 보여 주며 있습니다.

``` cs
void SetOutlineParameters(AzureSession session)
{
    OutlineSettings outlineSettings = session.Actions.OutlineSettings;
    outlineSettings.Color = new Color4Ub(255, 255, 0, 255);
    outlineSettings.PulseRateHz = 2.0f;
    outlineSettings.PulseIntensity = 0.5f;
}
```

## <a name="performance"></a>성능

개요 렌더링은 렌더링 성능에 큰 영향을 미칠 수 있습니다. 이 영향은 지정된 프레임에 대해 선택된 객체와 선택되지 않은 객체 간의 화면 공간 공간 관계에 따라 달라집니다.

## <a name="next-steps"></a>다음 단계

* [계층 적 상태 재정의 구성 요소](../../overview/features/override-hierarchical-state.md)
