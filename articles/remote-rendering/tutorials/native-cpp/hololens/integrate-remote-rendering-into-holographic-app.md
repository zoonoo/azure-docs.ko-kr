---
title: C++/DirectX11 Holographic 앱에 Remote Rendering 통합
description: Visual Studio 프로젝트 마법사를 사용하여 만든 일반 C++/DirectX11 Holographic 앱에 Remote Rendering을 통합하는 방법을 설명합니다.
author: florianborn71
ms.author: flborn
ms.date: 05/04/2020
ms.topic: tutorial
ms.openlocfilehash: b6dceff8b777d09a6f791437eb4aaca70365b518
ms.sourcegitcommit: d118ad4fb2b66c759b70d4d8a18e6368760da3ad
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/02/2020
ms.locfileid: "84300720"
---
# <a name="tutorial-integrate-remote-rendering-into-a-hololens-holographic-app"></a>자습서: Hololens Holographic 앱에 Remote Rendering 통합

이 자습서에서는 다음에 대해 알아봅니다.

> [!div class="checklist"]
>
> * Visual Studio를 사용하여 HoloLens에 배포할 수 있는 Holographic 앱 만들기
> * 원격으로 렌더링된 콘텐츠와 로컬 렌더링을 결합하는 데 필요한 코드 조각 및 프로젝트 설정 추가

이 자습서에서는 로컬 렌더링과 Azure Remote Rendering을 결합하는 데 필요한 비트를 네이티브 `Holographic App` 샘플에 추가하는 방법을 집중적으로 설명합니다. 이 앱의 상태 피드백에 대한 유일한 유형은 Visual Studio 내에서 디버그 출력 패널을 통하는 것이므로 Visual Studio 내에서 샘플을 시작하는 것이 좋습니다. 동적 텍스트 패널을 처음부터 빌드하려면 많은 코딩이 필요하므로 적절한 앱 내 피드백을 추가하는 것은 이 샘플의 범위를 벗어납니다. 올바른 시작 지점은 `StatusDisplay` 클래스이며, 이는 [GitHub의 Remoting Player 샘플 프로젝트](https://github.com/microsoft/MixedReality-HolographicRemoting-Samples/tree/master/player/common/Content)의 일부입니다. 실제로 이 자습서의 미리 준비된 버전은 해당 클래스의 로컬 복사본을 사용합니다.

> [!TIP]
> [ARR 샘플 리포지토리](https://github.com/Azure/azure-remote-rendering)에는 이 자습서의 결과가 사용할 준비가 된 Visual Studio 프로젝트로 포함되어 있습니다. 또한 이는 `StatusDisplay` UI 클래스를 통해 적절한 오류 및 상태 보고 기능으로 보강되었습니다. 자습서 내에서 모든 ARR 특정 추가 기능의 범위는 `#ifdef USE_REMOTE_RENDERING` / `#endif`로 지정되에 Remote Rendering 추가 기능을 쉽게 식별할 수 있습니다.

## <a name="prerequisites"></a>필수 구성 요소

이 자습서에는 다음이 필요합니다.

* 계정 정보(계정 ID, 계정 키, 구독 ID). Azure 계정이 없는 경우 [계정을 만듭니다](../../../how-tos/create-an-account.md).
* Windows SDK 10.0.18362.0[(다운로드)](https://developer.microsoft.com/windows/downloads/windows-10-sdk)
* 최신 버전의 Visual Studio 2019[(다운로드)](https://visualstudio.microsoft.com/vs/older-downloads/)
* [혼합 현실용 Visual Studio 도구](https://docs.microsoft.com/windows/mixed-reality/install-the-tools). 특히, 다음 *워크로드* 설치는 필수입니다.
  * **C++를 사용한 데스크톱 개발**
  * **UWP(유니버설 Windows 플랫폼) 개발**
* Visual Studio용 Windows Mixed Reality 앱 템플릿[(다운로드)](https://marketplace.visualstudio.com/items?itemName=WindowsMixedRealityteam.WindowsMixedRealityAppTemplatesVSIX)

## <a name="create-a-new-holographic-app-sample"></a>새 Holographic 앱 샘플 만들기

첫 번째 단계로, Remote Rendering 통합의 기반이 되는 주식 샘플을 만듭니다. Visual Studio를 열고, "새 프로젝트 만들기"를 선택하고, "Holographic DirectX 11 앱(유니버설 Windows)(C++/WinRT)"을 검색합니다.

![새 프로젝트 만들기](media/new-project-wizard.png)

선택한 프로젝트 이름을 입력하고, 경로를 선택하고, "만들기" 단추를 선택합니다.
새 프로젝트에서 구성을 **"디버그/ARM64"** 로 전환합니다. 이제 이를 컴파일하여 연결된 HoloLens 2 디바이스에 배포할 수 있습니다. HoloLens에서 실행하는 경우 사용자 앞에 회전하는 큐브가 표시됩니다.

## <a name="add-remote-rendering-dependencies-through-nuget"></a>NuGet을 통해 Remote Rendering 종속성 추가

Remote Rendering 기능을 추가하기 위한 첫 번째 단계는 클라이언트 쪽 종속성을 추가하는 것입니다. 관련 종속성은 NuGet 패키지로 사용할 수 있습니다.
솔루션 탐색기에서 마우스 오른쪽 단추로 프로젝트 이름을 클릭하고, 상황에 맞는 메뉴에서 **"NuGet 패키지 관리..."** 를 선택합니다.

메시지가 표시된 대화 상자에서 **"Microsoft.Azure.RemoteRendering.Cpp"** 라는 NuGet 패키지를 찾습니다.

![NuGet 패키지 찾아보기](media/add-nuget.png)

패키지를 선택한 다음, "설치" 단추를 눌러 프로젝트에 추가합니다.

NuGet 패키지는 Remote Rendering 종속성을 프로젝트에 추가합니다. 특히 다음에 대한 내용을 설명합니다.
* 클라이언트 라이브러리(RemoteRenderingClient.lib)에 연결합니다.
* .dll 종속성을 설치합니다.
* 포함 디렉터리에 올바른 경로를 설정합니다.

## <a name="project-preparation"></a>프로젝트 준비

기존 프로젝트를 약간 변경해야 합니다. 이러한 변경은 매우 사소하지만, 변경하지 않으면 Remote Rendering이 작동하지 않습니다.

### <a name="enable-multithread-protection-on-directx-device"></a>DirectX 디바이스에서 다중 스레드 보호 사용
`DirectX11` 디바이스에는 다중 스레드 보호를 사용하도록 설정되어 있어야 합니다. 이를 변경하려면 "Common" 폴더에서 DeviceResources.cpp 파일을 열고, `DeviceResources::CreateDeviceResources()` 함수의 끝에 다음 코드를 삽입합니다.

```cpp
// Enable multi thread protection as now multiple threads use the immediate context.
Microsoft::WRL::ComPtr<ID3D11Multithread> contextMultithread;
if (context.As(&contextMultithread) == S_OK)
{
    contextMultithread->SetMultithreadProtected(true);
}
```

### <a name="enable-network-capabilities-in-the-app-manifest"></a>애플리케이션 매니페스트의 네트워크 기능 사용
배포된 앱에 대해 네트워크 기능을 명시적으로 사용하도록 설정해야 합니다. 이렇게 구성되지 않으면 결국에는 연결 쿼리에서 시간 제한이 발생합니다. 사용하도록 설정하려면 솔루션 탐색기에서 `package.appxmanifest` 항목을 두 번 클릭합니다. 다음 UI에서 **기능** 탭으로 이동하여 다음을 선택합니다.
* 인터넷(클라이언트 및 서버)
* 인터넷(클라이언트)

![네트워크 기능](media/appx-manifest-caps.png)


## <a name="integrate-remote-rendering"></a>Remote Rendering 통합

이제 프로젝트가 준비되었으므로 코드부터 시작할 수 있습니다. 초기화, 초기화 취소 및 렌더링에 필요한 모든 후크가 있으므로 애플리케이션에 대한 적절한 진입점은 `HolographicAppMain` 클래스(HolographicAppMain.h/cpp 파일)입니다.

### <a name="includes"></a>Includes

먼저 필요한 include를 추가합니다. 다음 include를 HolographicAppMain.h 파일에 추가합니다.

```cpp
#include <AzureRemoteRendering.h>
```

그리고 HolographicAppMain.cpp 파일에 대한 추가 `include` 지시문은 다음과 같습니다.

```cpp
#include <AzureRemoteRendering.inl>
#include <RemoteRenderingExtensions.h>
```

코드를 간단히 하기 위해 HolographicAppMain.h 파일 맨 위쪽의 `include` 지시문 뒤에 다음 네임스페이스 바로 가기를 정의합니다.

```cpp
namespace RR = Microsoft::Azure::RemoteRendering;
```

이 바로 가기는 유용하므로 전체 네임스페이스를 작성할 필요는 없지만 ARR 특정 데이터 구조를 여전히 인식할 수 있습니다. 물론 `using namespace...` 지시문도 사용할 수 있습니다.

### <a name="remote-rendering-initialization"></a>Remote Rendering 초기화
 
애플리케이션 수명 동안 세션 등에 대한 몇 가지 개체를 보유해야 합니다. 수명은 애플리케이션의 `HolographicAppMain` 개체에 대한 수명과 일치하므로 개체를 멤버로 `HolographicAppMain` 클래스에 추가합니다. 다음 단계는 다음 클래스 멤버를 HolographicAppMain.h 파일에 추가하는 것입니다.

```cpp
class HolographicAppMain
{
    ...
    // members:
    std::string m_sessionOverride;                // if we have a valid session ID, we specify it here. Otherwise a new one is created
    RR::ApiHandle<RR::AzureFrontend> m_frontEnd;  // the front end instance
    RR::ApiHandle<RR::AzureSession> m_session;    // the current remote rendering session
    RR::ApiHandle<RR::RemoteManager> m_api;       // the API instance, that is used to perform all the actions. This is just a shortcut to m_session->Actions()
    RR::ApiHandle<RR::GraphicsBindingWmrD3d11> m_graphicsBinding; // the graphics binding instance
}
```

실제 구현을 수행하는 데 적절한 장소는 `HolographicAppMain` 클래스의 생성자입니다. 여기서는 다음 세 가지 유형의 초기화를 수행해야 합니다.
1. Remote Rendering 시스템의 일회성 초기화
1. 프런트 엔드 만들기
1. 세션 만들기

생성자에서 모든 작업을 순차적으로 수행합니다. 그러나 실제 사용 사례에서는 이러한 단계를 별도로 수행하는 것이 적절할 수 있습니다.

다음 코드를 HolographicAppMain.cpp 파일에서 생성자 본문의 시작 부분에 추가합니다.

```cpp
HolographicAppMain::HolographicAppMain(std::shared_ptr<DX::DeviceResources> const& deviceResources) :
    m_deviceResources(deviceResources)
{
    // 1. One time initialization
    {
        RR::RemoteRenderingInitialization clientInit;
        memset(&clientInit, 0, sizeof(RR::RemoteRenderingInitialization));
        clientInit.connectionType = RR::ConnectionType::General;
        clientInit.graphicsApi = RR::GraphicsApiType::WmrD3D11;
        clientInit.toolId = "<sample name goes here>"; // <put your sample name here>
        clientInit.unitsPerMeter = 1.0f;
        clientInit.forward = RR::Axis::Z_Neg;
        clientInit.right = RR::Axis::X;
        clientInit.up = RR::Axis::Y;
        RR::StartupRemoteRendering(clientInit);
    }


    // 2. Create front end
    {
        // Users need to fill out the following with their account data and model
        RR::AzureFrontendAccountInfo init;
        memset(&init, 0, sizeof(RR::AzureFrontendAccountInfo));
        init.AccountId = "00000000-0000-0000-0000-000000000000";
        init.AccountKey = "<account key>";
        init.AccountDomain = "westus2.mixedreality.azure.com"; // <change to your region>
        m_modelURI = "builtin://Engine";
        m_sessionOverride = ""; // if there is a valid session ID to re-use, put it here. Otherwise a new one is created

        m_frontEnd = RR::ApiHandle(RR::AzureFrontend(init));
    }

    // 3. Open/create rendering session
    {
        bool createNewSession = true;

        auto SetNewSession = [this](RR::ApiHandle<RR::AzureSession> newSession)
        {
            SetNewState(AppConnectionStatus::Connecting, RR::Result::Success);
            m_session = newSession;
            m_api = m_session->Actions();
            m_graphicsBinding = m_session->GetGraphicsBinding().as<RR::GraphicsBindingWmrD3d11>();
            m_session->ConnectionStatusChanged([this](auto status, auto error)
                {
                    OnConnectionStatusChanged(status, error);
                });

            // The following is async, but we'll get notifications via OnConnectionStatusChanged
            RR::ConnectToRuntimeParams init;
            init.mode = RR::ServiceRenderMode::Default;
            m_session->ConnectToRuntime(init);
        };

        // If we had an old (valid) session that we can recycle, we call synchronous function m_frontEnd->OpenRenderingSession
        if (!m_sessionOverride.empty())
        {
            auto openSessionRes = m_frontEnd->OpenRenderingSession(m_sessionOverride);
            if (openSessionRes->valid())
            {
                SetNewSession(*openSessionRes);
                createNewSession = false;
            }
        }

        if (createNewSession)
        {
            // Create a new session
            RR::RenderingSessionCreationParams init;
            memset(&init, 0, sizeof(RR::RenderingSessionCreationParams));
            init.MaxLease.minute = 10; // session is leased for 10 minutes
            init.Size = RR::RenderingSessionVmSize::Standard;
            auto createSessionAsync = *m_frontEnd->CreateNewRenderingSessionAsync(init);
            createSessionAsync->Completed([&](auto handler)
                {
                    if (handler->Result())
                    {
                        SetNewSession(*handler->Result());
                    }
                    else
                    {
                        SetNewState(AppConnectionStatus::ConnectionFailed, RR::Result::Fail);
                    }
                });
            SetNewState(AppConnectionStatus::CreatingSession, RR::Result::Success);
        }
    }

    // Rest of constructor code:
    ...
}
```
`SetNewSession` 로컬 함수 내에서 지정된 세션의 연결 상태가 변경될 때마다 트리거되는 콜백에 등록합니다. 자체의 `OnConnectionStatusChanged` 함수에 대한 해당 호출을 리디렉션합니다. 다음 단락에서 이(및 상태 머신 코드의 나머지 부분)를 선언하고 구현합니다. 또한 자격 증명은 샘플에서 하드 코딩되며 [계정 ID, 계정 키](../../../how-tos/create-an-account.md#retrieve-the-account-information) 및 [도메인](../../../reference/regions.md)을 제 위치에 입력해야 합니다.

소멸자 본문의 끝에서 초기화 취소를 대칭적이고 역순으로 수행합니다.

```cpp
HolographicAppMain::~HolographicAppMain()
{
    // Existing destructor code:
    ...
    
    // Destroy session:
    if (m_session != nullptr)
    {
        m_session->DisconnectFromRuntime();
        m_session = nullptr;
    }

    // Destroy front end:
    m_frontEnd = nullptr;

    // One-time de-initialization:
    RR::ShutdownRemoteRendering();
}
```

## <a name="state-machine"></a>상태 머신

Remote Rendering에서 세션을 만들고 모델을 로드하는 핵심 함수는 비동기 함수입니다. 이를 고려하기 위해 기본적으로 다음 상태를 자동으로 전환하는 간단한 상태 머신이 필요합니다.

*초기화-> 세션 만들기-> 모델 로드 중(진행률 포함)*

따라서 다음 단계로 약간의 상태 머신 처리를 클래스에 추가합니다. 애플리케이션이 속할 수 있는 다양한 상태에 대해 자체의 `AppConnectionStatus` 열거형을 선언합니다. `RR::ConnectionStatus`와 비슷하지만 실패한 연결에 대한 추가 상태가 있습니다.

다음 멤버와 함수를 클래스 선언에 추가합니다.

```cpp
namespace HolographicApp
{
    // Our application's possible states:
    enum class AppConnectionStatus
    {
        Disconnected,

        CreatingSession,
        Connecting,
        Connected,

        // error state:
        ConnectionFailed,
    };

    class HolographicAppMain
    {
        ...
        // Member functions for state transition handling
        void OnConnectionStatusChanged(RR::ConnectionStatus status, RR::Result error);
        void SetNewState(AppConnectionStatus state, RR::Result error);
        void StartModelLoading();

        // Members for state handling:

        // Model loading:
        std::string m_modelURI;
        RR::ApiHandle<RR::LoadModelAsync> m_loadModelAsync;

        // Connection state machine:
        AppConnectionStatus m_currentStatus = AppConnectionStatus::Disconnected;
        RR::Result m_connectionResult = RR::Result::Success;
        RR::Result m_modelLoadResult = RR::Result::Success;
        bool m_isConnected = false;
        bool m_modelLoadTriggered = false;
        float m_modelLoadingProgress = 0.f;
        bool m_modelLoadFinished = false;

    }
```

.cpp 파일의 구현 쪽에서 다음 함수 본문을 추가합니다.

```cpp
void HolographicAppMain::StartModelLoading()
{
    m_modelLoadingProgress = 0.f;

    RR::LoadModelFromSASParams params;
    params.ModelUrl = m_modelURI.c_str();
    params.Parent = nullptr;

    // Start the async model loading
    if (auto loadModel = m_api->LoadModelFromSASAsync(params))
    {
        m_loadModelAsync = *loadModel;
        m_loadModelAsync->Completed([this](const RR::ApiHandle<RR::LoadModelAsync>& async)
        {
            m_modelLoadResult = *async->Status();
            m_modelLoadFinished = true; // successful if m_modelLoadResult==RR::Result::Success
            m_loadModelAsync = nullptr;
            char buffer[1024];
            sprintf_s(buffer, "Remote Rendering: Model loading completed. Result: %s\n", RR::ResultToString(m_modelLoadResult));
            OutputDebugStringA(buffer);
            });
        m_loadModelAsync->ProgressUpdated([this](float progress)
        {
            // Progress callback
            m_modelLoadingProgress = progress;

            // Output progress percentage to VS output
            char buffer[1024];
            sprintf_s(buffer, "Remote Rendering: Model loading progress: %.1f\n", m_modelLoadingProgress * 100.f);
            OutputDebugStringA(buffer);
        });
    }
}



void HolographicAppMain::SetNewState(AppConnectionStatus state, RR::Result error)
{
    m_currentStatus = state;
    m_connectionResult = error;

    // Some log for the VS output panel:
    const char* appStatus = nullptr;

    switch (state)
    {
        case AppConnectionStatus::Disconnected: appStatus = "Disconnected"; break;
        case AppConnectionStatus::CreatingSession: appStatus = "CreatingSession"; break;
        case AppConnectionStatus::Connecting: appStatus = "Connecting"; break;
        case AppConnectionStatus::Connected: appStatus = "Connected"; break;
        case AppConnectionStatus::ConnectionFailed: appStatus = "ConnectionFailed"; break;
    }

    char buffer[1024];
    sprintf_s(buffer, "Remote Rendering: New status: %s, result: %s\n", appStatus, RR::ResultToString(error));
    OutputDebugStringA(buffer);
}


void HolographicAppMain::OnConnectionStatusChanged(RR::ConnectionStatus status, RR::Result error)
{
    switch (status)
    {
    case RR::ConnectionStatus::Connecting:
        SetNewState(AppConnectionStatus::Connecting, error);
        break;
    case RR::ConnectionStatus::Connected:
        if (error == RR::Result::Success)
        {
            SetNewState(AppConnectionStatus::Connected, error);
        }
        else
        {
            SetNewState(AppConnectionStatus::ConnectionFailed, error);
        }
        m_modelLoadTriggered = m_modelLoadFinished = false;
        m_isConnected = error == RR::Result::Success;

        // start model loading as soon as we have a valid connection
        if (m_isConnected && !m_modelLoadTriggered)
        {
            m_modelLoadTriggered = true;
            StartModelLoading();
        }
        break;
    case RR::ConnectionStatus::Disconnected:
        if (error == RR::Result::Success)
        {
            SetNewState(AppConnectionStatus::Disconnected, error);
        }
        else
        {
            SetNewState(AppConnectionStatus::ConnectionFailed, error);
        }
        m_modelLoadTriggered = m_modelLoadFinished = false;
        m_isConnected = false;
        break;
    default:
        break;
    }
}
```

### <a name="per-frame-update"></a>프레임당 업데이트

시뮬레이션 틱당 한 번씩 클라이언트를 틱해야 합니다. `HolographicApp1Main` 클래스는 프레임당 업데이트에 적절한 후크를 제공하므로 다음 코드를 `HolographicApp1Main::Update` 함수의 본문에 추가합니다.

```cpp
// Updates the application state once per frame.
HolographicFrame HolographicAppMain::Update()
{
    // Tick Remote rendering:
    if (m_session != nullptr)
    {
        // Tick the client to receive messages
        m_api->Update();
    }

    // Rest of the body:
    ...
}
```

### <a name="rendering"></a>렌더링

마지막으로, 원격 콘텐츠 렌더링을 호출합니다. 렌더링 대상을 정리한 후에 렌더링 파이프라인 내의 정확한 위치에서 이 호출을 수행해야 합니다. 다음 코드 조각을 `UseHolographicCameraResources` 잠금 내부 함수인 `HolographicAppMain::Render`에 삽입합니다.

```cpp
        ...
        // Existing clear function:
        context->ClearDepthStencilView(depthStencilView, D3D11_CLEAR_DEPTH | D3D11_CLEAR_STENCIL, 1.0f, 0);

        // Inject remote rendering: as soon as we are connected, start blitting the remote frame.
        // We do the blitting after the Clear, and before cube rendering.
        if (m_isConnected)
        {
            m_graphicsBinding->BlitRemoteFrame();
        }

        ...
```

## <a name="run-the-sample"></a>샘플 실행

이제 샘플이 컴파일되어 실행되는 상태에 있어야 합니다.

샘플이 제대로 실행되면 바로 앞에 회전하는 큐브가 표시되고, 세션이 만들어지고 모델이 로드되면 현재 헤드 위치에 있는 엔진 모델을 렌더링합니다. 세션을 만들고 모델을 로드하는 데 최대 몇 분 정도 걸릴 수 있습니다. 현재 상태는 Visual Studio의 출력 패널에만 기록됩니다. 따라서 Visual Studio 내에서 샘플을 시작하는 것이 좋습니다.

> [!CAUTION]
> 틱 함수가 몇 초 동안 호출되지 않으면 클라이언트와 서버의 연결이 끊어집니다. 따라서 중단점을 트리거하면 애플리케이션의 연결이 매우 쉽게 끊어질 수 있습니다.

텍스트 패널에서 적절한 상태를 표시하려면 GitHub에서 이 자습서의 미리 준비된 버전을 참조하세요.

## <a name="next-steps"></a>다음 단계

이 자습서에서는 Remote Rendering을 **Holographic 앱** C++/DirectX11 샘플에 추가하는 데 필요한 모든 단계를 알아보았습니다.
사용자 고유의 모델을 변환하려면 다음 빠른 시작을 참조하세요.

> [!div class="nextstepaction"]
> [빠른 시작: 렌더링을 위해 모델 변환](../../../quickstarts/convert-model.md)
