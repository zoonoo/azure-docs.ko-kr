---
title: Unity용 Remote Rendering 설정
description: Unity 프로젝트에서 Azure 원격 렌더링을 초기화하는 방법
author: jakrams
ms.author: jakras
ms.date: 02/27/2020
ms.topic: how-to
ms.custom: devx-track-csharp
ms.openlocfilehash: 48f01058d8e879a9610e76638215214c059982fa
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/30/2021
ms.locfileid: "99594217"
---
# <a name="set-up-remote-rendering-for-unity"></a>Unity용 Remote Rendering 설정

Unity에서 ARR(Azure Remote Rendering)을 사용하도록 설정하기 위해 몇 가지 Unity 관련 측면을 처리하는 전용 메서드를 제공합니다.

## <a name="startup-and-shutdown"></a>시작 및 종료

Remote Rendering을 초기화하려면 `RemoteManagerUnity`를 사용합니다. 이 클래스는 제네릭 `RenderingConnection`을 호출하지만 이미 Unity 관련 세부 정보를 구현합니다. 예를 들어 Unity는 특정 좌표계를 사용합니다. `RemoteManagerUnity.Initialize`를 호출할 때 적절한 규칙이 설정됩니다. 또한 이 호출은 원격으로 렌더링된 콘텐츠를 표시하는 데 사용해야 하는 Unity 카메라를 제공해야 합니다.

```cs
// initialize Azure Remote Rendering for use in Unity:
// it needs to know which camera is used for rendering the scene
RemoteUnityClientInit clientInit = new RemoteUnityClientInit(Camera.main);
RemoteManagerUnity.InitializeManager(clientInit);
```

Remote Rendering을 종료하려면 `RemoteManagerStatic.ShutdownRemoteRendering()`을 호출합니다.

`RenderingSession`을 만들고 기본 렌더링 세션으로 선택한 후에는 `RemoteManagerUnity`에 등록해야 합니다.

```cs
RemoteManagerUnity.CurrentSession = ...
```

### <a name="full-example-code"></a>전체 예제 코드

아래 코드에서는 Unity에서 Azure Remote Rendering을 초기화하는 데 필요한 모든 단계를 보여줍니다.

```cs
// initialize Remote Rendering
RemoteUnityClientInit clientInit = new RemoteUnityClientInit(Camera.main);
RemoteManagerUnity.InitializeManager(clientInit);

// create a frontend
SessionConfiguration sessionConfig = new SessionConfiguration();
// ... fill out sessionConfig ...
RemoteRenderingClient client = new RemoteRenderingClient(sessionConfig);

// start a session
CreateRenderingSessionResult result = await client.CreateNewRenderingSessionAsync(new RenderingSessionCreationOptions(RenderingSessionVmSize.Standard, 0, 30));
RenderingSession session = result.Session;

// let RemoteManagerUnity know about the session we want to use
RemoteManagerUnity.CurrentSession = session;

await session.ConnectAsync(new RendererInitOptions());

/// When connected, load and modify content

RemoteManagerStatic.ShutdownRemoteRendering();
```

## <a name="convenience-functions"></a>편리한 함수

### <a name="session-state-events"></a>세션 상태 이벤트

`RemoteManagerUnity.OnSessionUpdate`는 세션 상태가 변경될 때 이벤트를 내보냅니다. 자세한 내용은 코드 설명서를 참조하세요.

### <a name="arrserviceunity"></a>ARRServiceUnity

`ARRServiceUnity`는 설정 및 세션 관리를 간소화하기 위한 선택적 구성 요소입니다. 애플리케이션을 종료하거나 편집기에서 재생 모드를 종료할 때 세션을 자동으로 중지하는 옵션과 필요한 경우 세션 임대를 자동으로 갱신하는 옵션이 포함되어 있습니다. 세션 속성(`LastProperties` 변수 참조)과 같은 데이터를 캐시하고 세션 상태 변경 및 세션 오류에 대한 이벤트를 노출합니다.

한 번에 둘 이상의 `ARRServiceUnity` 인스턴스가 있을 수 없습니다. 몇 가지 일반적인 기능을 구현하여 빠르게 시작할 수 있습니다. 더 큰 애플리케이션의 경우 이러한 작업을 직접 수행하는 것이 더 적합할 수 있습니다.

`ARRServiceUnity`를 설정하고 사용하는 방법에 대한 예제는 [자습서: 원격으로 렌더링된 모델 보기](../../tutorials/unity/view-remote-models/view-remote-models.md)를 참조하세요.

## <a name="next-steps"></a>다음 단계

* [Unity용 Remote Rendering 패키지 설치](install-remote-rendering-unity-package.md)
* [자습서: 원격으로 렌더링된 모델 보기](../../tutorials/unity/view-remote-models/view-remote-models.md)
