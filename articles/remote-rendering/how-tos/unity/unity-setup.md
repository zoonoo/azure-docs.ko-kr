---
title: 유니티를 위한 원격 렌더링 설정
description: Unity 프로젝트에서 Azure 원격 렌더링초기화 하는 방법
author: jakrams
ms.author: jakras
ms.date: 02/27/2020
ms.topic: how-to
ms.openlocfilehash: 0415c0e7ee1432521c3cc2026feff5fc2a41d77e
ms.sourcegitcommit: 642a297b1c279454df792ca21fdaa9513b5c2f8b
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/06/2020
ms.locfileid: "80681143"
---
# <a name="set-up-remote-rendering-for-unity"></a>유니티를 위한 원격 렌더링 설정

Unity에서 Azure 원격 렌더링(ARR)을 활성화하기 위해 일부 Unity 특정 측면을 처리하는 전용 메서드를 제공합니다.

## <a name="startup-and-shutdown"></a>시작 및 종료

원격 렌더링을 초기화하려면 을 사용합니다. `RemoteManagerUnity` 이 클래스는 일반 `RemoteManager` 을 호출하지만 이미 Unity 관련 세부 정보를 구현합니다. 예를 들어 Unity는 특정 좌표계를 사용합니다. 호출할 `RemoteManagerUnity.Initialize`때 적절한 규칙이 설정됩니다. 또한 이 호출을 통해 원격으로 렌더링된 콘텐츠를 표시하는 데 사용해야 하는 Unity 카메라를 제공해야 합니다.

```cs
// initialize Azure Remote Rendering for use in Unity:
// it needs to know which camera is used for rendering the scene
RemoteUnityClientInit clientInit = new RemoteUnityClientInit(Camera.main);
RemoteManagerUnity.InitializeManager(clientInit);
```

원격 렌더링을 종료하는 `RemoteManagerStatic.ShutdownRemoteRendering()`경우 를 호출합니다.

기본 `AzureSession` 렌더링 세션으로 만들어지고 선택된 후에는 다음에 `RemoteManagerUnity`등록해야 합니다.

```cs
RemoteManagerUnity.CurrentSession = ...
```

### <a name="full-example-code"></a>전체 예제 코드

아래 코드는 Unity에서 Azure 원격 렌더링을 초기화하는 데 필요한 모든 단계를 보여 줍니다.

```cs
// initialize Remote Rendering
RemoteUnityClientInit clientInit = new RemoteUnityClientInit(Camera.main);
RemoteManagerUnity.InitializeManager(clientInit);

// create a frontend
AzureFrontendAccountInfo accountInfo = new AzureFrontendAccountInfo();
// ... fill out accountInfo ...
AzureFrontend frontend = new AzureFrontend(accountInfo);

// start a session
AzureSession session = await frontend.CreateNewRenderingSessionAsync(new RenderingSessionCreationParams(RenderingSessionVmSize.Standard, 0, 30)).AsTask();

// let RemoteManagerUnity know about the session we want to use
RemoteManagerUnity.CurrentSession = session;

session.ConnectToRuntime(new ConnectToRuntimeParams());

/// When connected, load and modify content

RemoteManagerStatic.ShutdownRemoteRendering();
```

## <a name="convenience-functions"></a>편의 기능

### <a name="session-state-events"></a>세션 상태 이벤트

`RemoteManagerUnity.OnSessionUpdate`세션 상태가 변경될 때 이벤트를 방출하는 경우 자세한 내용은 코드 설명서를 참조하십시오.

### <a name="arrserviceunity"></a>ARR서비스유니티

`ARRServiceUnity`는 설정 및 세션 관리를 간소화하는 선택적 구성 요소입니다. 응용 프로그램이 종료되거나 재생 모드가 편집기에서 종료될 때 세션을 자동으로 중지하고 필요할 때 세션 임대를 자동으로 갱신하는 옵션이 포함되어 있습니다. 세션 속성과 같은 데이터를 캐시하고(해당 `LastProperties` 변수 참조) 세션 상태 변경 및 세션 오류에 대한 이벤트를 노출합니다.

한 번에 두 개 이상의 `ARRServiceUnity` 인스턴스가 있을 수 없습니다. 몇 가지 일반적인 기능을 구현하여 더 빨리 시작하기 위한 것입니다. 더 큰 응용 프로그램의 경우 이러한 작업을 직접 수행하는 것이 더 바람직할 수 있습니다.

예를 들어 설정 및 사용 `ARRServiceUnity` 방법은 [자습서: Unity 프로젝트를 처음부터 설정](../../tutorials/unity/project-setup.md)참조합니다.

## <a name="next-steps"></a>다음 단계

* [Unity용 원격 렌더링 패키지 설치](install-remote-rendering-unity-package.md)
* [자습서: 처음부터 Unity 프로젝트 설정](../../tutorials/unity/project-setup.md)
