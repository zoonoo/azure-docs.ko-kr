---
title: 렌더링 모드
description: 다양한 서버 측 렌더링 모드에 대해 설명합니다.
author: florianborn71
ms.author: flborn
ms.date: 02/03/2020
ms.topic: conceptual
ms.openlocfilehash: 7f2b1031659864ae338bb0aa320c048ea23c21f3
ms.sourcegitcommit: 642a297b1c279454df792ca21fdaa9513b5c2f8b
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/06/2020
ms.locfileid: "80681702"
---
# <a name="rendering-modes"></a>렌더링 모드

원격 렌더링은 **타일 기반 컴포지션** 모드와 **깊이 기반 컴포지션** 모드의 두 가지 주요 작업 모드를 제공합니다. 이러한 모드는 워크로드가 서버의 여러 GPU에 분산되는 방식을 결정합니다. 모드는 연결 시 지정해야 하며 런타임 중에 변경할 수 없습니다.

두 모드 모두 장점이 있지만 고유한 기능 제한도 있으므로 가장 적합한 모드를 선택하는 것은 사용 사례에 따라 다릅니다.

## <a name="modes"></a>모드

이제 두 가지 모드에 대해 더 자세히 설명합니다.

### <a name="tilebasedcomposition-mode"></a>'타일 기반 구성' 모드

**타일 기반 컴포지션** 모드에서는 관련된 모든 GPU가 화면에 특정 하위 사각형(타일)을 렌더링합니다. 주 GPU는 클라이언트에 비디오 프레임으로 전송되기 전에 타일의 최종 이미지를 구성합니다. 따라서 모든 GPU는 렌더링을 위해 동일한 리소스 집합을 가져야 하므로 로드된 자산은 단일 GPU의 메모리에 맞아야 합니다.

MSAA는 모든 GPU에 대한 전체 형상 집합에서 작업할 수 있으므로 이 모드의 렌더링 품질은 **DepthBasedComposition** 모드보다 약간 더 좋습니다. 다음 스크린 샷은 antialiasing 마찬가지로 양쪽 가장자리에 대해 제대로 작동한다는 것을 보여줍니다.

![타일 기반의 MSAA구성](./media/service-render-mode-quality.png)

또한 이 모드에서는 각 부품을 투명 재질로 전환하거나 [계층적StateOverride Component을](../overview/features/override-hierarchical-state.md) 통해 **투명** 모드로 전환할 수 있습니다.

### <a name="depthbasedcomposition-mode"></a>'깊이 기반 구성' 모드

**DepthBased컴컴포지션** 모드에서는 관련된 모든 GPU가 전체 화면 해상도에서 렌더링되지만 메시의 하위 집합만 렌더링합니다. 주 GPU의 최종 이미지 컴포지션은 깊이 정보에 따라 부품이 적절하게 병합되는 것을 관리합니다. 당연히 메모리 페이로드는 GPU에 분산되어 단일 GPU 메모리에 맞지 않는 모델을 렌더링할 수 있습니다.

모든 단일 GPU는 MSAA를 사용하여 로컬 콘텐츠를 antialias합니다. 그러나 고유한 GPU의 가장자리 사이에 는 고유별 별칭이 있을 수 있습니다. 이 효과는 최종 이미지를 후처리하여 완화되지만 MSAA 품질은 **타일 기반 구성** 모드보다 여전히 더 나쁩습니다.

MSAA 아티팩트는 다음 이미지에 ![설명되어 있습니다: 깊이 기반의 MSAA](./media/service-render-mode-balanced.png)

두 부품이 동일한 GPU로 렌더링되기 때문에 안티앨리어싱은 조각과 커튼 사이에 제대로 작동합니다. 반면에 커튼과 벽 사이의 가장자리는 이 두 부분이 고유한 GPU로 구성되기 때문에 일부 별칭을 보여줍니다.

이 모드의 가장 큰 제한사항은 지오메트리 부품을 동적으로 투명 재질로 전환할 수 없으며 [계층적StateOverride Component에](../overview/features/override-hierarchical-state.md)대해 **투명** 모드가 작동하지 않는다는 것입니다. 다른 상태 재정의 피처 (윤곽선, 색상 색조, ...) 하지만 작동 합니다. 또한 변환 시 투명으로 표시된 재질이 이 모드에서 제대로 작동합니다.

### <a name="performance"></a>성능

두 모드의 성능 특성은 사용 사례에 따라 다르며 일반적인 권장 사항을 추론하거나 제공하기가 어렵습니다. 위에서 언급한 제한 사항(메모리 또는 투명도/시스루)에 제약을 받지 않는 경우 두 모드를 모두 시도하고 다양한 카메라 위치를 사용하여 성능을 모니터링하는 것이 좋습니다.

## <a name="setting-the-render-mode"></a>렌더 모드 설정

원격 렌더링 VM에 사용되는 렌더링 모드는 `AzureSession.ConnectToRuntime` `ConnectToRuntimeParams`을 통해 지정됩니다.

```cs
async void ExampleConnect(AzureSession session)
{
    ConnectToRuntimeParams parameters = new ConnectToRuntimeParams();

    // Connect with one rendering mode
    parameters.mode = ServiceRenderMode.TileBasedComposition;
    await session.ConnectToRuntime(parameters).AsTask();

    session.DisconnectFromRuntime();

    // Wait until session.IsConnected == false

    // Reconnect with a different rendering mode
    parameters.mode = ServiceRenderMode.DepthBasedComposition;
    await session.ConnectToRuntime(parameters).AsTask();
}
```

## <a name="next-steps"></a>다음 단계

* [세션](../concepts/sessions.md)
* [계층 적 상태 재정의 구성 요소](../overview/features/override-hierarchical-state.md)
