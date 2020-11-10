---
title: 셸 렌더링
description: 셸 렌더링 효과를 사용 하는 방법을 설명 합니다.
author: jumeder
ms.author: jumeder
ms.date: 10/23/2020
ms.topic: article
ms.custom: devx-track-csharp
ms.openlocfilehash: f59c4f8225d31b61df08f30863c8b9300e20e820
ms.sourcegitcommit: 6109f1d9f0acd8e5d1c1775bc9aa7c61ca076c45
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/10/2020
ms.locfileid: "94447871"
---
# <a name="shell-rendering"></a>셸 렌더링

[계층 구조 상태 재정의 구성 요소의](../../overview/features/override-hierarchical-state.md) 셸 상태는 투명도 효과입니다. [참조](../../overview/features/override-hierarchical-state.md) 렌더링과 달리 불투명 렌더링과 유사 하 게 개체의 맨 앞에 있는 계층만 표시 됩니다. 또한 셸에 렌더링 될 때 개체의 법선 모양을 변경할 수 있습니다. 이 효과는 전체 장면에 대해 공간 인식을 계속 유지 하면서 사용자가 중요 하지 않은 부분을 시각적으로 파악 해야 하는 사용 사례를 위한 것입니다.

전역 상태를 통해 셸 렌더링 개체의 모양을 구성할 수 있습니다 `ShellRenderingSettings` . 셸 렌더링을 사용 하는 모든 개체는 동일한 설정을 사용 합니다. 개체별 매개 변수는 없습니다.

## <a name="shellrenderingsettings-parameters"></a>ShellRenderingSettings 매개 변수

클래스 `ShellRenderingSettings` 에는 전역 셸 렌더링 속성과 관련 된 설정이 포함 됩니다.

| 매개 변수      | Type    | Description                                             |
|----------------|---------|---------------------------------------------------------|
| `Desaturation` | float   | 일반적인 최종 개체 색에 적용할 desaturation의 크기 (0 (desaturation 없음)-1 (전체 desaturation))입니다. |
| `Opacity`      | float   | 0 (보이지 않음)에서 1 (완전히 불투명) 사이의 셸 렌더링 된 개체의 불투명도입니다. |

또한 전체 장면에 적용 될 때의 매개 변수 효과에 대 한 예제는 다음 표를 참조 하세요.

|                | 0 | 0.25 | 0.5 | 0.75 | 1.0 | 
|----------------|:-:|:----:|:---:|:----:|:---:|
| **Desaturation** | ![Desaturation-0.0](./media/shell-desaturation-00.png) | ![Desaturation-0.25](./media/shell-desaturation-025.png) | ![Desaturation-0.5](./media/shell-desaturation-05.png) | ![Desaturation-0.75](./media/shell-desaturation-075.png) | ![Desaturation-1.0](./media/shell-desaturation-10.png) |
| **불투명도**      | ![불투명도-0.0](./media/shell-opacity-00.png) | ![불투명도-0.25](./media/shell-opacity-025.png) | ![불투명도-0.5](./media/shell-opacity-05.png) | ![불투명도-0.75](./media/shell-opacity-075.png) | ![불투명도-1.0](./media/shell-opacity-10.png) |

셸 효과는 최종 불투명 색에 적용 됩니다. 그렇지 않은 경우에는 장면이 렌더링 됩니다. 이는 [색조 계층적 상태 재정의](../../overview/features/override-hierarchical-state.md)를 포함 합니다.

## <a name="example"></a>예제

다음 코드에서는 `ShellRenderingSettings` API를 통해 상태를 사용 하는 예제를 보여 줍니다.

```cs
void SetShellSettings(AzureSession session)
{
    ShellRenderingSettings shellRenderingSettings = session.Actions.ShellRenderingSettings;
    shellRenderingSettings.Desaturation = 0.5f;
    shellRenderingSettings.Opacity = 0.1f;
}
```

```cpp
void SetShellSettings(ApiHandle<AzureSession> session)
{
    ApiHandle<ShellRenderingSettings> shellRenderingSettings = session->Actions()->GetShellRenderingSettings();
    shellRenderingSettings->SetDesaturation(0.5f);
    shellRenderingSettings->SetOpacity(0.1f);
}
```

## <a name="performance"></a>성능

셸 렌더링 기능은 표준 불투명 렌더링과 비교할 때 작은 상수 오버 헤드를 포함 합니다. 개체 또는 [참조](../../overview/features/override-hierarchical-state.md) 렌더링에 투명 자료를 사용 하는 것 보다 훨씬 빠릅니다. 장면의 일부만 셸 렌더링으로 전환 되 면 성능이 더 강력 하 게 저하 될 수 있습니다. 이러한 저하는 렌더링을 필요로 하는 추가로 표시 되는 개체 때문에 발생할 수 있습니다. 이와 관련 하 여 성능은 [잘라내기 평면](../../overview/features/cut-planes.md) 기능과 비슷하게 동작 합니다.

## <a name="next-steps"></a>다음 단계

* [계층 상태 재정의 구성 요소](../../overview/features/override-hierarchical-state.md)