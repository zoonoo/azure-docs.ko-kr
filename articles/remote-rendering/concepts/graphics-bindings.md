---
title: 그래픽 바인딩
description: 그래픽 바인딩 및 사용 사례 설정
author: florianborn71
manager: jlyons
services: azure-remote-rendering
titleSuffix: Azure Remote Rendering
ms.author: flborn
ms.date: 12/11/2019
ms.topic: conceptual
ms.service: azure-remote-rendering
ms.openlocfilehash: 8b5db0532f3dcc8b6dfb024238d0cacff2e6d2a1
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/28/2020
ms.locfileid: "80681884"
---
# <a name="graphics-binding"></a>그래픽 바인딩

사용자 지정 응용 프로그램에서 Azure 원격 렌더링을 사용할 수 있으려면 응용 프로그램의 렌더링 파이프라인에 통합 되어 있어야 합니다. 이 통합은 그래픽 바인딩의 책임입니다.

설정 되 면 그래픽 바인딩은 렌더링 된 이미지에 영향을 주는 다양 한 함수에 대 한 액세스를 제공 합니다. 이러한 함수는 항상 사용할 수 있는 일반 함수와 선택한 `Microsoft.Azure.RemoteRendering.GraphicsApiType`와만 관련 된 특정 함수의 두 범주로 구분할 수 있습니다.

## <a name="graphics-binding-in-unity"></a>Unity의 그래픽 바인딩

Unity에서 전체 바인딩은로 `RemoteUnityClientInit` `RemoteManagerUnity.InitializeManager`전달 된 구조체에 의해 처리 됩니다. 그래픽 모드를 설정 하려면 `GraphicsApiType` 필드를 선택 된 바인딩으로 설정 해야 합니다. 필드는 XRDevice가 있는지 여부에 따라 자동으로 채워집니다. 다음 동작을 사용 하 여 동작을 수동으로 재정의할 수 있습니다.

* **HoloLens 2**: [Windows Mixed Reality](#windows-mixed-reality) 그래픽 바인딩이 항상 사용 됩니다.
* **플랫 UWP 데스크톱 앱**: [시뮬레이션이](#simulation) 항상 사용 됩니다. 이 모드를 사용 하려면 [자습서: Unity 프로젝트를 처음부터 설정](../tutorials/unity/project-setup.md)하는 단계를 수행 해야 합니다.
* **Unity 편집기**: WMR VR 헤드셋이 연결 되어 있지 않으면 항상 [시뮬레이션이](#simulation) 사용 됩니다 .이 경우에는 arr이 응용 프로그램의 비 arr 관련 부분을 디버그할 수 있도록 사용 하지 않도록 설정 됩니다. [Holographic remoting](../how-tos/unity/holographic-remoting.md)도 참조 하세요.

Unity에서 유일 하 게 관련 된 다른 부분은 [기본 바인딩에](#access)액세스 하는 것입니다. 아래의 다른 모든 섹션을 건너뛸 수 있습니다.

## <a name="graphics-binding-setup-in-custom-applications"></a>사용자 지정 응용 프로그램의 그래픽 바인딩 설정

그래픽 바인딩을 선택 하려면 다음 두 단계를 수행 합니다. 먼저 프로그램이 초기화 될 때 그래픽 바인딩이 정적으로 초기화 되어야 합니다.

``` cs
RemoteRenderingInitialization managerInit = new RemoteRenderingInitialization;
managerInit.graphicsApi = GraphicsApiType.WmrD3D11;
managerInit.connectionType = ConnectionType.General;
managerInit.right = ///...
RemoteManagerStatic.StartupRemoteRendering(managerInit);
```

위의 호출은 holographic Api로 Azure 원격 렌더링을 초기화 하는 데 필요 합니다. 이 함수는 holographic API를 호출 하 고 다른 원격 렌더링 Api에 액세스 하기 전에 호출 해야 합니다. 마찬가지로, holographic Api가 더 이상 호출 `RemoteManagerStatic.ShutdownRemoteRendering();` 되지 않은 후에는 해당 하는 init 함수를 호출 해야 합니다.

## <a name="span-idaccessaccessing-graphics-binding"></a><span id="access">그래픽 바인딩 액세스

클라이언트를 설정한 후에는 `AzureSession.GraphicsBinding` getter를 사용 하 여 기본 그래픽 바인딩에 액세스할 수 있습니다. 예를 들어 마지막 프레임 통계는 다음과 같이 검색할 수 있습니다.

``` cs
AzureSession currentSesson = ...;
if (currentSesson.GraphicsBinding)
{
    FrameStatistics frameStatistics;
    if (session.GraphicsBinding.GetLastFrameStatistics(out frameStatistics) == Result.Success)
    {
        ...
    }
}
```

## <a name="graphic-apis"></a>그래픽 Api

현재는 두 개의 그래픽 Api를 `WmrD3D11` 선택할 수 있습니다. `SimD3D11` 세 번째는 `Headless` 존재 하지만 클라이언트 쪽에서는 아직 지원 되지 않습니다.

### <a name="windows-mixed-reality"></a>Windows Mixed Reality

`GraphicsApiType.WmrD3D11`는 HoloLens 2에서 실행 되는 기본 바인딩입니다. 그러면 `GraphicsBindingWmrD3d11` 바인딩이 생성 됩니다. 이 모드에서 Azure 원격 렌더링 후크는 holographic Api에 직접 후크 됩니다.

파생 된 그래픽 바인딩에 액세스 하려면 base `GraphicsBinding` 를 캐스팅 해야 합니다.
WMR 바인딩을 사용 하려면 다음 두 가지 작업을 수행 해야 합니다.

#### <a name="inform-remote-rendering-of-the-used-coordinate-system"></a>사용 된 좌표계의 원격 렌더링을 알립니다.

``` cs
AzureSession currentSesson = ...;
IntPtr ptr = ...; // native pointer to ISpatialCoordinateSystem
GraphicsBindingWmrD3d11 wmrBinding = (currentSession.GraphicsBinding as GraphicsBindingWmrD3d11);
if (binding.UpdateUserCoordinateSystem(ptr) == Result.Success)
{
    ...
}
```

위의 `ptr` 은 API의 좌표가 표시 되는 세계 좌표 `ABI::Windows::Perception::Spatial::ISpatialCoordinateSystem` 좌표 시스템을 정의 하는 네이티브 개체에 대 한 포인터 여야 합니다.

#### <a name="render-remote-image"></a>렌더링 원격 이미지

각 프레임의 시작 부분에서 원격 프레임을 백 버퍼로 렌더링 해야 합니다. 이 작업은 색 및 `BlitRemoteFrame`깊이 정보를 모두 현재 바인딩된 렌더링 대상으로 채우는를 호출 하 여 수행 됩니다. 따라서 백 버퍼를 렌더링 대상으로 바인딩한 후이 작업을 수행 하는 것이 중요 합니다.

``` cs
AzureSession currentSesson = ...;
GraphicsBindingWmrD3d11 wmrBinding = (currentSession.GraphicsBinding as GraphicsBindingWmrD3d11);
binding.BlitRemoteFrame();
```

### <a name="simulation"></a>시뮬레이션

`GraphicsApiType.SimD3D11`는 시뮬레이션 바인딩이 고, 선택 하는 경우 `GraphicsBindingSimD3d11` 그래픽 바인딩을 만듭니다. 이 인터페이스는 데스크톱 응용 프로그램에서와 같이 헤드 이동을 시뮬레이션 하 고 monoscopic 이미지를 렌더링 하는 데 사용 됩니다.
설치는 약간 더 복잡 하 고 다음과 같이 작동 합니다.

#### <a name="create-proxy-render-target"></a>프록시 렌더링 대상 만들기

`GraphicsBindingSimD3d11.Update` 함수에서 제공 하는 프록시 카메라 데이터를 사용 하 여 원격 및 로컬 콘텐츠를 ' 프록시 ' 라는 오프 스크린 색/깊이 렌더링 대상으로 렌더링 해야 합니다. 프록시가 백 버퍼의 확인과 일치 해야 합니다. 세션이 준비 되 면를 연결 `GraphicsBindingSimD3d11.InitSimulation` 하기 전에를 호출 해야 합니다.

``` cs
AzureSession currentSesson = ...;
IntPtr d3dDevice = ...; // native pointer to ID3D11Device
IntPtr color = ...; // native pointer to ID3D11Texture2D
IntPtr depth = ...; // native pointer to ID3D11Texture2D
float refreshRate = 60.0f; // Monitor refresh rate up to 60hz.
bool flipBlitRemoteFrameTextureVertically = false;
bool flipReprojectTextureVertically = false;
GraphicsBindingSimD3d11 simBinding = (currentSession.GraphicsBinding as GraphicsBindingSimD3d11);
simBinding.InitSimulation(d3dDevice, depth, color, refreshRate, flipBlitRemoteFrameTextureVertically, flipReprojectTextureVertically);
```

Init 함수는 네이티브 d3d 장치 및 프록시 렌더링 대상의 색 및 깊이 질감에 대 한 포인터와 함께 제공 해야 합니다. 초기화 `AzureSession.ConnectToRuntime` 된 후에 `DisconnectFromRuntime` 는 여러 번 호출할 수 있지만 다른 세션으로 전환할 때는 먼저 이전 `GraphicsBindingSimD3d11.DeinitSimulation` 세션에서를 호출 해야 다른 세션에서를 호출할 `GraphicsBindingSimD3d11.InitSimulation` 수 있습니다.

#### <a name="render-loop-update"></a>렌더링 루프 업데이트

렌더링 루프 업데이트는 여러 단계로 구성 됩니다.

1. 렌더링을 수행 하기 전에 각 프레임 `GraphicsBindingSimD3d11.Update` 은 렌더링 될 서버에 전송 되는 현재 카메라 변환으로 호출 됩니다. 반환 된 프록시 변환이 프록시 카메라에 적용 되어 프록시 렌더링 대상으로 렌더링 됩니다.
반환 된 프록시 업데이트가 `SimulationUpdate.frameId` null 이면 아직 원격 데이터가 없는 것입니다. 이 경우 프록시 렌더링 대상으로 렌더링 하는 대신, 현재 카메라 데이터를 사용 하 여 로컬 콘텐츠를 백 버퍼에 직접 렌더링 하 고 다음 두 단계를 건너뜁니다.
1. 이제 응용 프로그램은 프록시 렌더링 대상을 바인딩하고를 호출 `GraphicsBindingSimD3d11.BlitRemoteFrameToProxy`해야 합니다. 이렇게 하면 원격 색 및 깊이 정보가 프록시 렌더링 대상에 채워집니다. 이제 프록시 카메라 변환을 사용 하 여 모든 로컬 콘텐츠를 프록시로 렌더링할 수 있습니다.
1. 그런 다음 백 버퍼를 렌더링 대상으로 바인딩되어야 하 고 `GraphicsBindingSimD3d11.ReprojectProxy` 백 버퍼를 표시할 수 있는 지점에서를 호출 해야 합니다.

``` cs
AzureSession currentSesson = ...;
GraphicsBindingSimD3d11 simBinding = (currentSession.GraphicsBinding as GraphicsBindingSimD3d11);
SimulationUpdate update = new SimulationUpdate();
// Fill out camera data with current camera data
...
SimulationUpdate proxyUpdate = new SimulationUpdate();
simBinding.Update(update, out proxyUpdate);
// Is the frame data valid?
if (proxyUpdate.frameId != 0)
{
    // Bind proxy render target
    simBinding.BlitRemoteFrameToProxy();
    // Use proxy camera data to render local content
    ...
    // Bind back buffer
    simBinding.ReprojectProxy();
}
else
{
    // Bind back buffer
    // Use current camera data to render local content
    ...
}
```

## <a name="next-steps"></a>다음 단계

* [자습서: Unity 프로젝트를 처음부터 설정](../tutorials/unity/project-setup.md)
