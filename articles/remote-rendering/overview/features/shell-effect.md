---
title: 셸 렌더링
description: 셸 렌더링 효과를 사용하는 방법을 설명합니다.
author: jumeder
ms.author: jumeder
ms.date: 10/23/2020
ms.topic: article
ms.custom: devx-track-csharp
ms.openlocfilehash: 7af95cba807cea340438a7de30f096758d0369ad
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/30/2021
ms.locfileid: "99594166"
---
# <a name="shell-rendering"></a>셸 렌더링

[계층 구조 상태 재정의 구성 요소](../../overview/features/override-hierarchical-state.md)의 셸 상태는 투명도 효과입니다. [투명](../../overview/features/override-hierarchical-state.md) 렌더링과 달리 불투명 렌더링과 유사하게 개체의 맨 앞에 있는 계층만 표시됩니다. 또한 셸로 렌더링될 때 개체의 일반 모양을 변경할 수 있습니다. 이 효과는 전체 장면에 대해 공간 인식을 계속 유지하면서 사용자가 중요하지 않은 부분을 시각적으로 파악해야 하는 사용 사례를 위한 것입니다.

`ShellRenderingSettings` 전역 상태를 통해 셸 렌더링 개체의 모양을 구성할 수 있습니다. 셸 렌더링을 사용하는 모든 개체는 동일한 설정을 사용합니다. 개체별 매개 변수가 없습니다.

## <a name="shellrenderingsettings-parameters"></a>ShellRenderingSettings 매개 변수

클래스 `ShellRenderingSettings`에는 글로벌 셀 렌더링 속성과 관련된 설정이 있습니다.

| 매개 변수      | Type    | 설명                                             |
|----------------|---------|---------------------------------------------------------|
| `Desaturation` | float   | 0(desaturation채도 감소 없음)에서 1(전체 desaturation) 사이의 일반적인 최종 개체 색에 적용할 desaturation의 크기 |
| `Opacity`      | float   | 0(보이지 않음)에서 1(완전 불투명) 사이의 셸 렌더링 개체의 불투명도 |

또한 전체 장면에 적용될 때 매개 변수 효과에 대한 예제는 다음 표를 참조하세요.

|                | 0 | 0.25 | 0.5 | 0.75 | 1.0 | 
|----------------|:-:|:----:|:---:|:----:|:---:|
| **Desaturation** | ![Desaturation-0.0](./media/shell-desaturation-00.png) | ![Desaturation-0.25](./media/shell-desaturation-025.png) | ![Desaturation-0.5](./media/shell-desaturation-05.png) | ![Desaturation-0.75](./media/shell-desaturation-075.png) | ![Desaturation-1.0](./media/shell-desaturation-10.png) |
| **불투명도**      | ![Opacity-0.0](./media/shell-opacity-00.png) | ![Opacity-0.25](./media/shell-opacity-025.png) | ![Opacity-0.5](./media/shell-opacity-05.png) | ![Opacity-0.75](./media/shell-opacity-075.png) | ![Opacity-1.0](./media/shell-opacity-10.png) |

셸 효과는 장면이 렌더링되는 최종 불투명 색에 적용됩니다. 여기에는 [색조 계층 구조 상태 재정의](../../overview/features/override-hierarchical-state.md)가 포함됩니다.

## <a name="example"></a>예제

다음 코드에서는 API를 통한 `ShellRenderingSettings` 상태의 사용 예제를 보여줍니다.

```cs
void SetShellSettings(RenderingSession session)
{
    ShellRenderingSettings shellRenderingSettings = session.Connection.ShellRenderingSettings;
    shellRenderingSettings.Desaturation = 0.5f;
    shellRenderingSettings.Opacity = 0.1f;
}
```

```cpp
void SetShellSettings(ApiHandle<RenderingSession> session)
{
    ApiHandle<ShellRenderingSettings> shellRenderingSettings = session->Connection()->GetShellRenderingSettings();
    shellRenderingSettings->SetDesaturation(0.5f);
    shellRenderingSettings->SetOpacity(0.1f);
}
```

## <a name="performance"></a>성능

셸 렌더링 기능은 표준 불투명 렌더링에 비해 작은 상수 오버헤드를 전달합니다. 개체 또는 [투명](../../overview/features/override-hierarchical-state.md) 렌더링에 투명 재질을 사용하는 것보다 훨씬 빠릅니다. 장면의 일부만 셸 렌더링으로 전환되면 성능이 더욱 저하될 수 있습니다. 이러한 성능 저하는 렌더링이 필요한 개체가 추가로 표시되었기 때문에 발생할 수 있습니다. 이와 관련하여 성능은 [평면 잘라내기](../../overview/features/cut-planes.md) 기능과 유사하게 작동합니다.

## <a name="next-steps"></a>다음 단계

* [계층 상태 재정의 구성 요소](../../overview/features/override-hierarchical-state.md)