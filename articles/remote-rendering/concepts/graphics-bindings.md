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
ms.sourcegitcommit: 642a297b1c279454df792ca21fdaa9513b5c2f8b
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/06/2020
ms.locfileid: "80681884"
---
# <a name="graphics-binding"></a>그래픽 바인딩

사용자 지정 응용 프로그램에서 Azure 원격 렌더링을 사용하려면 응용 프로그램의 렌더링 파이프라인에 통합해야 합니다. 이러한 통합은 그래픽 바인딩의 책임입니다.

설정이 완료되면 그래픽 바인딩을 통해 렌더링된 이미지에 영향을 주는 다양한 기능에 액세스할 수 있습니다. 이러한 함수는 항상 사용할 수 있는 일반 함수와 선택한 `Microsoft.Azure.RemoteRendering.GraphicsApiType`함수에만 관련된 특정 함수의 두 가지 범주로 구분할 수 있습니다.

## <a name="graphics-binding-in-unity"></a>유니티에 바인딩된 그래픽

Unity에서 전체 바인딩은 에 `RemoteUnityClientInit` 전달된 구조체에 `RemoteManagerUnity.InitializeManager`의해 처리됩니다. 그래픽 모드를 설정하려면 `GraphicsApiType` 필드를 선택한 바인딩으로 설정해야 합니다. 필드는 XRDevice가 있는지 여부에 따라 자동으로 채워집니다. 다음 동작으로 수동으로 비헤이비어를 재정의할 수 있습니다.

* **HoloLens 2**: [윈도우 혼합 현실](#windows-mixed-reality) 그래픽 바인딩은 항상 사용된다.
* **평면 UWP 데스크톱 응용 프로그램**: [시뮬레이션은](#simulation) 항상 사용됩니다. 이 모드를 사용하려면 [자습서: Unity 프로젝트를 처음부터 설정하는](../tutorials/unity/project-setup.md)단계를 따르십시오.
* **유니티 에디터**: WMR VR 헤드셋이 연결되어 있는 경우가 아니면 [시뮬레이션이](#simulation) 항상 사용되며, 이 경우 ARR이 비활성화되어 응용 프로그램의 비 ARR 관련 부분을 디버깅할 수 있습니다. 또한 [홀로그램 리모트](../how-tos/unity/holographic-remoting.md).

Unity의 다른 관련 부분은 기본 [바인딩에](#access)액세스하는 것이며 아래의 다른 모든 섹션은 건너뛸 수 있습니다.

## <a name="graphics-binding-setup-in-custom-applications"></a>사용자 지정 응용 프로그램에서 그래픽 바인딩 설정

그래픽 바인딩을 선택하려면 다음 두 단계를 수행합니다: 첫째, 그래픽 바인딩은 프로그램이 초기화될 때 정적으로 초기화되어야 합니다.

``` cs
RemoteRenderingInitialization managerInit = new RemoteRenderingInitialization;
managerInit.graphicsApi = GraphicsApiType.WmrD3D11;
managerInit.connectionType = ConnectionType.General;
managerInit.right = ///...
RemoteManagerStatic.StartupRemoteRendering(managerInit);
```

Azure 원격 렌더링을 홀로그램 API로 초기화하려면 위의 호출이 필요합니다. 홀로그램 API가 호출되기 전에 다른 원격 렌더링 API에 액세스하기 전에 이 함수를 호출해야 합니다. 마찬가지로 홀로그램 API가 더 `RemoteManagerStatic.ShutdownRemoteRendering();` 이상 호출되지 않은 후에 해당 in-init 함수를 호출해야 합니다.

## <a name="span-idaccessaccessing-graphics-binding"></a><span id="access">그래픽 바인딩 에 액세스

클라이언트가 설정되면 getter를 통해 기본 그래픽 바인딩에 `AzureSession.GraphicsBinding` 액세스할 수 있습니다. 예를 들어 마지막 프레임 통계는 다음과 같이 검색할 수 있습니다.

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

## <a name="graphic-apis"></a>그래픽 API

현재 선택할 수 있는 두 개의 `WmrD3D11` 그래픽 `SimD3D11`API와 . 세 번째 `Headless` 는 존재하지만 클라이언트 측에서는 아직 지원되지 않습니다.

### <a name="windows-mixed-reality"></a>Windows Mixed Reality

`GraphicsApiType.WmrD3D11`는 HoloLens 2에서 실행되는 기본 바인딩입니다. 바인딩을 `GraphicsBindingWmrD3d11` 만듭니다. 이 모드에서 Azure 원격 렌더링은 홀로그램 API에 직접 연결됩니다.

파생 된 그래픽 바인딩에 액세스하려면 `GraphicsBinding` 베이스를 캐스팅해야합니다.
WMR 바인딩을 사용 하려면 두 가지 작업을 수행 해야 합니다.

#### <a name="inform-remote-rendering-of-the-used-coordinate-system"></a>사용된 좌표계의 원격 렌더링 정보

``` cs
AzureSession currentSesson = ...;
IntPtr ptr = ...; // native pointer to ISpatialCoordinateSystem
GraphicsBindingWmrD3d11 wmrBinding = (currentSession.GraphicsBinding as GraphicsBindingWmrD3d11);
if (binding.UpdateUserCoordinateSystem(ptr) == Result.Success)
{
    ...
}
```

위의 `ptr` 경우 API의 좌표가 `ABI::Windows::Perception::Spatial::ISpatialCoordinateSystem` 표현되는 기본 공간 좌표계를 정의하는 네이티브 개체에 대한 포인터여야 합니다.

#### <a name="render-remote-image"></a>원격 이미지 렌더링

각 프레임이 시작될 때 원격 프레임을 백 버퍼로 렌더링해야 합니다. 이 작업은 현재 `BlitRemoteFrame`바인딩된 렌더 대상에 색상 및 깊이 정보를 모두 채우는 호출을 수행합니다. 따라서 백 버퍼를 렌더 대상으로 바인딩한 후 이 작업을 수행하는 것이 중요합니다.

``` cs
AzureSession currentSesson = ...;
GraphicsBindingWmrD3d11 wmrBinding = (currentSession.GraphicsBinding as GraphicsBindingWmrD3d11);
binding.BlitRemoteFrame();
```

### <a name="simulation"></a>시뮬레이션

`GraphicsApiType.SimD3D11`는 시뮬레이션 바인딩을 선택하고 선택한 `GraphicsBindingSimD3d11` 경우 그래픽 바인딩을 만듭니다. 이 인터페이스는 예를 들어 데스크톱 응용 프로그램에서 머리 움직임을 시뮬레이션하는 데 사용되며 단조로운 이미지를 렌더링합니다.
설정은 좀 더 관련이 있으며 다음과 같이 작동합니다.

#### <a name="create-proxy-render-target"></a>프록시 렌더 대상 만들기

리모트 및 로컬 콘텐츠는 `GraphicsBindingSimD3d11.Update` 함수에서 제공하는 프록시 카메라 데이터를 사용하여 '프록시'라는 오프스크린 색상/깊이 렌더링 대상으로 렌더링되어야 합니다. 프록시는 백 버퍼의 해상도와 일치해야 합니다. 세션이 준비되면 `GraphicsBindingSimD3d11.InitSimulation` 세션에 연결하기 전에 호출해야 합니다.

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

init 함수는 프록시 렌더링 대상의 색상 및 깊이 텍스처뿐만 아니라 기본 d3d 장치에 대한 포인터와 함께 제공되어야 합니다. 일단 `AzureSession.ConnectToRuntime` 초기화되고 `DisconnectFromRuntime` 여러 번 호출할 수 있지만 다른 `GraphicsBindingSimD3d11.DeinitSimulation` 세션으로 전환할 때 이전 `GraphicsBindingSimD3d11.InitSimulation` 세션에서 먼저 호출해야 다른 세션에서 호출할 수 있습니다.

#### <a name="render-loop-update"></a>렌더 루프 업데이트

렌더 루프 업데이트는 여러 단계로 구성됩니다.

1. 렌더링이 수행되기 전에 각 `GraphicsBindingSimD3d11.Update` 프레임은 렌더링할 서버로 전송되는 현재 카메라 변환으로 호출됩니다. 동시에 반환된 프록시 변환을 프록시 카메라에 적용하여 프록시 렌더링 대상으로 렌더링해야 합니다.
반환된 프록시 `SimulationUpdate.frameId` 업데이트가 null이면 원격 데이터가 아직 없습니다. 이 경우 프록시 렌더링 대상으로 렌더링하는 대신 현재 카메라 데이터를 사용하여 로컬 콘텐츠를 백 버퍼로 직접 렌더링해야 하며 다음 두 단계를 건너뜁니다.
1. 이제 응용 프로그램이 프록시 렌더링 대상을 바인딩하고 호출합니다. `GraphicsBindingSimD3d11.BlitRemoteFrameToProxy` 이렇게 하면 원격 색상 및 깊이 정보가 프록시 렌더링 대상에 채워집니다. 이제 프록시 카메라 변환을 사용하여 모든 로컬 콘텐츠를 프록시에 렌더링할 수 있습니다.
1. 다음으로 백 버퍼는 렌더링 대상으로 바인딩되고 `GraphicsBindingSimD3d11.ReprojectProxy` 백 버퍼를 표시할 수 있는 지점을 호출해야 합니다.

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

* [자습서: 처음부터 Unity 프로젝트 설정](../tutorials/unity/project-setup.md)
