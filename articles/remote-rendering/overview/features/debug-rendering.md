---
title: 디버그 렌더링
description: 서버 쪽 디버깅 렌더링 효과 개요
author: jumeder
ms.author: jumeder
ms.date: 04/09/2020
ms.topic: article
ms.openlocfilehash: f10c736cad9322752d5d552d29ef0c63635628a5
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/28/2020
ms.locfileid: "81868153"
---
# <a name="debug-rendering"></a>디버그 렌더링

디버그 렌더링 API는 다양 한 디버깅 효과를 사용 하 여 서버 쪽 렌더링을 변경 하는 다양 한 전역 옵션을 제공 합니다.

## <a name="available-debug-rendering-effects"></a>사용 가능한 디버그 렌더링 효과

|설정                          | 영향                               |
|---------------------------------|:-------------------------------------|
|Frame 카운터                    | 텍스트 오버레이를 프레임의 왼쪽 위 모퉁이에 렌더링 합니다. 텍스트는 렌더링을 진행할 때 지속적으로 증가 하는 현재 서버 쪽 프레임 ID를 표시 합니다. |
|다각형 수                    | 텍스트 오버레이를 프레임의 왼쪽 위 모퉁이에 렌더링 합니다. 이 텍스트는 [서버 쪽 성능 쿼리에서](performance-queries.md) 쿼리 하는 것과 동일한 값인 현재 렌더링 된 다각형의 양을 보여 줍니다.| 
|와이어 프레임                        | 사용 하도록 설정 하면 서버에 로드 된 모든 개체 형상이 와이어 프레임 모드로 렌더링 됩니다. 다각형의 가장자리만이 모드에서 래스터화됩니다. |

다음 코드에서는 이러한 디버깅 효과를 사용 합니다.

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

디버그 렌더링 API는 서비스 연결이 실제로 작동 하 고 올바르게 실행 되는지 확인 하는 것과 같은 간단한 디버깅 작업을 위한 것입니다. 텍스트 렌더링 옵션은 하향 스트리밍 비디오 프레임에 직접 영향을 줍니다. 사용 하도록 설정 하면 새 프레임이 수신 되 고 비디오가 올바르게 디코딩 되는지 확인 합니다.

그러나 제공 된 효과는 서비스 상태에 대 한 자세한 검사을 제공 하지 않습니다. 이 사용 사례에 대 한 [서버 쪽 성능 쿼리](performance-queries.md) 를 사용 하는 것이 좋습니다.

## <a name="performance-considerations"></a>성능 고려 사항

* 텍스트 오버레이를 사용 하면 성능 오버 헤드가 거의 발생 하지 않습니다.
* 와이어 프레임 모드를 사용 하도록 설정 하면 사소한 성능 오버 헤드가 발생 하지만 장면에 따라 달라질 수 있습니다. 복잡 한 장면을 위해이 모드는 프레임 비율이 60-Hz 대상 아래로 떨어질 수 있습니다.

## <a name="next-steps"></a>다음 단계

* [렌더링 모드](../../concepts/rendering-modes.md)
* [서버 쪽 성능 쿼리](performance-queries.md)
