---
title: 디버그 렌더링
description: 서버 측 디버깅 렌더링 효과 개요
author: jumeder
ms.author: jumeder
ms.date: 04/09/2020
ms.topic: article
ms.openlocfilehash: 675f8d988e64ed7b556f154f681ccb53ed1000c6
ms.sourcegitcommit: d6e4eebf663df8adf8efe07deabdc3586616d1e4
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/15/2020
ms.locfileid: "81394294"
---
# <a name="debug-rendering"></a>디버그 렌더링

디버그 렌더링 API는 다양한 디버깅 효과로 서버 측 렌더링을 변경하는 다양한 전역 옵션을 제공합니다.

## <a name="available-debug-rendering-effects"></a>사용 가능한 디버그 렌더링 효과

|설정                          | 효과                               |
|---------------------------------|:-------------------------------------|
|프레임 카운터                    | 텍스트 오버레이를 프레임의 왼쪽 위 모서리에 렌더링합니다. 텍스트는 렌더링이 진행됨에 따라 지속적으로 증가하는 현재 서버 측 프레임 ID를 보여 줍니다. |
|다각형 개수                    | 텍스트 오버레이를 프레임의 왼쪽 위 모서리에 렌더링합니다. 텍스트는 현재 렌더링된 다각형 의 양을 보여 [주며, 서버 측 성능 쿼리에서](performance-queries.md) 쿼리한 값과 동일한 값입니다.| 
|와이어 프레임                        | 이 옵션을 사용하면 서버에 로드된 모든 오브젝트 형상이 와이어프레임 모드로 렌더링됩니다. 다각형의 가장자리만 이 모드를 래스터화합니다. |

다음 코드를 사용하면 이러한 디버깅 효과를 사용할 수 있습니다.

``` cs
void EnableDebugRenderingEffects(AzureSession session, bool highlight)
{
    DebugRenderingSettings settings = session.Actions.DebugRenderingSettings;

    // Enable frame counter text overlay on the server side rendering
    settings.RenderFrameCount = true;

    // Enable polygon count text overlay on the server side rendering
    settings.RenderPolygonCount = true;

    // Enable wireframe rendering of object geometry on the server
    settings.RenderWireframe = true;
}
```

![디버그 렌더링](./media/debug-rendering.png)

> [!NOTE]
> 모든 디버그 렌더링 효과는 전체 프레임에 영향을 주는 전역 설정입니다.

## <a name="use-cases"></a>사용 사례

디버그 렌더링 API는 서비스 연결이 실제로 올바르게 실행되고 있는지 확인하는 것과 같은 간단한 디버깅 작업을 위한 것입니다. 텍스트 렌더링 옵션은 다운 스트림 비디오 프레임에 직접적인 영향을 미칩니다. 새 프레임이 수신되고 비디오 디코딩이 올바르게 수행되었는지 확인합니다.

그러나 제공된 효과는 서비스 상태와 관련된 자세한 성찰을 제공하지 않습니다. 이 사용 사례에는 [서버 측 성능 쿼리를](performance-queries.md) 사용하는 것이 좋습니다.

## <a name="performance-considerations"></a>성능 고려 사항

* 텍스트 오버레이를 사용하도록 설정하면 성능 오버레이가 거의 또는 전혀 발생하지 않습니다.
* 또한 오버레이를 사용하도록 설정하면 장면에 따라 다를 수 있지만 사소한 성능 오버헤드가 발생합니다. 복잡한 장면의 경우 이 모드로 인해 프레임 속도가 60Hz 목표 아래로 떨어질 수 있습니다.

## <a name="next-steps"></a>다음 단계

* [렌더링 모드](../../concepts/rendering-modes.md)
* [서버 쪽 성능 쿼리](performance-queries.md)
