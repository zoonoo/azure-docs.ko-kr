---
title: 렌더링 모드
description: 다양한 서버 쪽 렌더링 모드에 대해 설명합니다.
author: florianborn71
ms.author: flborn
ms.date: 02/03/2020
ms.topic: conceptual
ms.custom: devx-track-csharp
ms.openlocfilehash: 2cf1872bcdd7b1bda74046198f5fc32be1069913
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/30/2021
ms.locfileid: "99594504"
---
# <a name="rendering-modes"></a>렌더링 모드

Remote Rendering은 **TileBasedComposition** 모드 및 **DepthBasedComposition** 모드라는 두 가지 주요 작업 모드를 제공합니다. 이러한 모드는 워크로드가 서버의 여러 GPU에 분산되는 방법을 결정합니다. 모드는 연결 시 지정해야 하며 런타임 중에는 변경할 수 없습니다.

두 모드는 모두 장점이 있지만 고유한 기능 제한 사항이 있으므로 가장 적합한 모드를 선택하는 것은 사용 사례에 따라 달라집니다.

## <a name="modes"></a>모드

이제 두 가지 모드에 대해 자세히 설명합니다.

### <a name="tilebasedcomposition-mode"></a>'TileBasedComposition' 모드

**TileBasedComposition** 모드에서 관련된 모든 GPU는 특정 하위 사각형(타일)을 화면에 렌더링합니다. 주 GPU는 비디오 프레임으로 클라이언트에 보내기 전에 타일에서 최종 이미지를 작성합니다. 따라서 모든 GPU는 렌더링을 위해 동일한 리소스 집합을 사용해야 하므로 로드된 자산은 단일 GPU의 메모리에 적합해야 합니다.

MSAA가 모든 GPU의 전체 기하 도형 집합에서 작동할 수 있으므로 이 모드의 렌더링 품질은 **DepthBasedComposition** 모드보다 약간 더 높습니다. 다음 스크린샷에서는 앤티앨리어싱이 양쪽 가장자리에서 마찬가지로 제대로 작동함을 보여 줍니다.

![TileBasedComposition의 MSAA](./media/service-render-mode-quality.png)

또한 이 모드에서는 각 부분을 투명한 재질로 전환하거나 [HierarchicalStateOverrideComponent](../overview/features/override-hierarchical-state.md)를 통해 **투명하게 표시** 모드로 전환할 수 있습니다.

### <a name="depthbasedcomposition-mode"></a>'DepthBasedComposition' 모드

**DepthBasedComposition** 모드에서 관련된 모든 GPU는 전체 화면 해상도로 렌더링되지만 메시의 하위 집합만 렌더링됩니다. 주 GPU의 최종 이미지 작성은 깊이 정보에 따라 부분이 적절히 병합되도록 고려합니다. 기본적으로 메모리 페이로드는 GPU 전체에 분산되므로 단일 GPU의 메모리에 적합하지 않는 모델을 렌더링할 수 있습니다.

모든 단일 GPU는 MSAA를 사용하여 로컬 콘텐츠를 앤티앨리어싱합니다. 그러나 고유한 GPU의 가장자리 간에 고유한 앨리어싱이 있을 수 있습니다. 이 효과는 최종 이미지를 후 처리하여 완화되지만 MSAA 품질은 여전히 **TileBasedComposition** 모드보다 낮습니다.

MSAA 아티팩트는 다음 이미지에 설명되어 있습니다. ![DepthBasedComposition의 MSAA](./media/service-render-mode-balanced.png)

조각과 커튼이 모두 동일한 GPU에 렌더링되므로 앤티앨리어싱이 두 부분 사이에서 제대로 작동합니다. 반면에 커튼과 벽이 별개의 GPU로 구성되어 있으므로 이러한 두 부분 사이의 가장자리에서 약간의 앨리어싱을 보여 줍니다.

이 모드의 가장 큰 제한 사항은 기하 도형 부분을 투명한 재질로 동적으로 전환할 수 없고 [HierarchicalStateOverrideComponent](../overview/features/override-hierarchical-state.md)에 대해 **투명하게 표시** 모드가 작동하지 않는다는 것입니다. 그러나 다른 상태 재정의 기능(윤곽선, 색조 등)은 작동합니다. 또한 변환 시 투명으로 표시된 재질도 이 모드에서 제대로 작동합니다.

### <a name="performance"></a>성능

두 모드의 성능 특성은 사용 사례에 따라 달라지며, 일반적인 권장 사항을 추론하거나 제공하기가 어렵습니다. 위에서 언급한 제한 사항(메모리 또는 투명도/투명하게 표시)의 제약을 받지 않는 경우 두 모드를 모두 사용해 보고 다양한 카메라 위치를 사용하여 성능을 모니터링하는 것이 좋습니다.

## <a name="setting-the-render-mode"></a>렌더링 모드 설정

Remote Rendering 서버에서 사용되는 렌더링 모드는 `RendererInitOptions`를 통해 `RenderingSession.ConnectAsync` 중에 지정됩니다.

```cs
async void ExampleConnect(RenderingSession session)
{
    RendererInitOptions parameters = new RendererInitOptions();

    // Connect with one rendering mode
    parameters.RenderMode = ServiceRenderMode.TileBasedComposition;
    await session.ConnectAsync(parameters);

    session.Disconnect();

    // Wait until session.IsConnected == false

    // Reconnect with a different rendering mode
    parameters.RenderMode = ServiceRenderMode.DepthBasedComposition;
    await session.ConnectAsync(parameters);
}
```

## <a name="api-documentation"></a>API 설명서

* [C# RenderingSession.ConnectAsync()](/dotnet/api/microsoft.azure.remoterendering.renderingsession.connectasync)
* [C# RendererInitOptions 구조체](/dotnet/api/microsoft.azure.remoterendering.rendererinitoptions)
* [C++ RenderingSession::ConnectToConnectAsyncRuntime()](/cpp/api/remote-rendering/renderingsession#connectasync)
* [C++ RendererInitOptions 구조체](/cpp/api/remote-rendering/rendererinitoptions)

## <a name="next-steps"></a>다음 단계

* [세션](../concepts/sessions.md)
* [계층 상태 재정의 구성 요소](../overview/features/override-hierarchical-state.md)