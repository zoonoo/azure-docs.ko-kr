---
title: 윤곽 렌더링
description: 선택 윤곽 렌더링을 수행 하는 방법을 설명 합니다.
author: florianborn71
ms.author: flborn
ms.date: 02/11/2020
ms.topic: article
ms.openlocfilehash: 8b52dbe8cd12e51c42677ce37acbd57ad551ec50
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/28/2020
ms.locfileid: "80680831"
---
# <a name="outline-rendering"></a>윤곽 렌더링

[계층 상태 재정의 구성 요소](../../overview/features/override-hierarchical-state.md)를 통해 윤곽 렌더링을 추가 하 여 선택한 개체를 시각적으로 강조 표시할 수 있습니다. 이 장에서는 클라이언트 API를 통해 개요 렌더링의 글로벌 매개 변수를 변경 하는 방법을 설명 합니다.

윤곽선 속성은 전역 설정입니다. 개요 렌더링을 사용 하는 모든 개체는 동일한 설정을 사용 합니다. 즉, 개체당 개요 색을 사용할 수 없습니다.

## <a name="parameters-for-outlinesettings"></a>매개 변수`OutlineSettings`

클래스 `OutlineSettings` 에는 전역 윤곽선 속성과 관련 된 설정이 포함 됩니다. 다음 멤버를 노출 합니다.

| 매개 변수      | Type    | Description                                             |
|----------------|---------|---------------------------------------------------------|
| `Color`          | Color4Ub | 윤곽선을 그리는 데 사용 되는 색입니다. 알파 부분은 무시 됩니다.         |
| `PulseRateHz`    | float   | 개요가 oscillates 된 초당 비율입니다.|
| `PulseIntensity` | float   | 윤곽선 펄스 효과의 농도입니다. 펄스 없이 0.0 사이 여야 하 고, 완전히 펄스 인 경우 1.0 여야 합니다. 강도는 윤곽선의 최소 불투명도를로 `MinOpacity = 1.0 - PulseIntensity`암시적으로 설정 합니다. |

![매개 변수를 노랑 (왼쪽)에서 마젠타 (가운데)로,](./media/outlines.png) `pulseIntensity` 0에서 0.8 (오른쪽)으로 변경 하는 효과를 간략하게 설명 합니다. `color`

## <a name="example"></a>예제

다음 코드에서는 API를 통해 윤곽선 매개 변수를 설정 하는 예제를 보여 줍니다.

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

개요 렌더링은 렌더링 성능에 상당한 영향을 줄 수 있습니다. 이러한 영향은 지정 된 프레임에서 선택 된 개체와 선택 되지 않은 개체 사이의 화면 공간 공간 관계에 따라 달라 집니다.

## <a name="next-steps"></a>다음 단계

* [계층 구조 상태 재정의 구성 요소](../../overview/features/override-hierarchical-state.md)
