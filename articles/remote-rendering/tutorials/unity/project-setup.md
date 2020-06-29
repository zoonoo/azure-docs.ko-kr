---
title: Unity 프로젝트를 처음부터 설정
description: Azure Remote Rendering에서 사용할 빈 Unity 프로젝트를 구성하는 방법을 설명합니다.
author: florianborn71
ms.author: flborn
ms.date: 01/30/2020
ms.topic: tutorial
ms.openlocfilehash: c05daa998829c4ac0687f75ae5678695127a50b0
ms.sourcegitcommit: 5a8c8ac84c36859611158892422fc66395f808dc
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/10/2020
ms.locfileid: "84659906"
---
# <a name="tutorial-setting-up-a-unity-project-from-scratch"></a>자습서: Unity 프로젝트를 처음부터 설정

이 자습서에서는 다음에 대해 알아봅니다.

> [!div class="checklist"]
>
> * ARR용 Unity 스크래치 프로젝트 구성
> * 렌더링 세션 만들기 및 중지
> * 기존 세션 다시 사용
> * 세션에서 연결 및 연결 끊기
> * 렌더링 세션에 모델 로드
> * 연결 통계 표시

## <a name="prerequisites"></a>필수 구성 요소

이 자습서에는 다음이 필요합니다.

* 계정 정보(계정 ID, 계정 키, 구독 ID). Azure 계정이 없는 경우 [계정을 만듭니다](../../how-tos/create-an-account.md).
* Windows SDK 10.0.18362.0[(다운로드)](https://developer.microsoft.com/windows/downloads/windows-10-sdk)
* 최신 버전의 Visual Studio 2019[(다운로드)](https://visualstudio.microsoft.com/vs/older-downloads/) 
* [혼합 현실용 Visual Studio 도구](https://docs.microsoft.com/windows/mixed-reality/install-the-tools). 특히, 다음 *워크로드* 설치는 필수입니다.
  * **C++를 사용한 데스크톱 개발**
  * **UWP(유니버설 Windows 플랫폼) 개발**
* GIT([다운로드](https://git-scm.com/downloads))
* Unity 2019.3.1[(다운로드)](https://unity3d.com/get-unity/download)
  * 다음 모듈을 Unity에 설치합니다.
    * **UWP** - 유니버설 Windows 플랫폼 빌드 지원
    * **IL2CPP** - Windows 빌드 지원(IL2CPP)

> [!TIP]
> [ARR 샘플 리포지토리](https://github.com/Azure/azure-remote-rendering)에는 모든 자습서에서 사용할 수 있도록 준비된 Unity 프로젝트가 포함되어 있습니다. 이러한 프로젝트는 참조로 사용할 수 있습니다.

## <a name="create-a-new-unity-project"></a>새 Unity 프로젝트 만들기

Unity Hub에서 새 프로젝트를 만듭니다.
다음 예에서는 프로젝트가 `RemoteRendering`이라는 폴더에 만들어진다고 가정합니다.

![새 프로젝트 창](media/new-project.png)

## <a name="configure-the-projects-manifest"></a>프로젝트의 매니페스트 구성

Unity 프로젝트 폴더에 있는 `Packages/manifest.json` 파일을 수정해야 합니다. 텍스트 편집기에서 파일을 열고 아래에 나열된 줄을 추가합니다.

```json
{
  "scopedRegistries": [
    {
      "name": "Azure Mixed Reality Services",
      "url": "https://api.bintray.com/npm/microsoft/AzureMixedReality-NPM/",
      "scopes": ["com.microsoft.azure"]
    }
   ],
  "dependencies": {
    "com.microsoft.azure.remote-rendering": "0.1.11",
    "com.unity.render-pipelines.universal": "7.2.1",
    ...existing dependencies...
  }
}
```

유니버설 렌더링 파이프라인 패키지는 선택 사항이지만 성능상의 이유로 추천됩니다.
매니페스트가 수정되어 저장되면 Unity에서 자동으로 새로 고칩니다. *Project(프로젝트)* 창에서 패키지가 로드되었는지 확인합니다.

![패키지 가져오기 확인](media/confirm-packages.png)

## <a name="ensure-you-have-the-latest-version-of-the-package"></a>최신 버전의 패키지가 있는지 확인

다음 단계에서는 프로젝트에서 최신 버전의 원격 렌더링 패키지를 사용하는지 확인합니다.
1. 프로젝트 창에서 패키지를 선택하고 :::no-loc text="package"::: 아이콘을 클릭합니다. ![패키지 아이콘 선택](media/package-icons.png)
1. 검사기에서 "View in Package Manager(패키지 관리자에서 보기)"를 클릭합니다. ![패키지 검사기](media/package-properties.png)
1. 원격 렌더링 패키지의 패키지 관리자 페이지에서 업데이트 단추를 사용할 수 있는지 여부를 확인합니다. 해당하는 경우 이 단추를 클릭하면 패키지가 사용 가능한 최신 버전으로 업데이트됩니다. ![패키지 관리자의 ARR 패키지](media/package-manager.png)
1. 경우에 따라 패키지를 업데이트하면 콘솔에서 오류가 발생할 수 있습니다. 이 경우 프로젝트를 닫고 다시 열어 보세요.

## <a name="configure-the-camera"></a>카메라 구성

**Main Camera(기본 카메라)** 노드를 선택합니다.

1. 해당 *Transform(변환)* 을 다시 설정합니다.

    ![카메라 변환 다시 설정](media/camera-reset-transform.png)

1. **:::no-loc text="Clear flags":::** 를 *:::no-loc text="Solid Color":::* 로 설정

1. **:::no-loc text="Background":::** 를 *:::no-loc text="Black":::* 로 설정

1. **:::no-loc text="Clipping Planes":::** 를 *Near = 0.3* 및 *Far = 20*으로 설정합니다. 즉, 렌더링에서 30cm보다 더 가깝거나 20m보다 더 먼 기하 도형을 클리핑합니다.

    ![Unity 카메라 속성](media/camera-properties.png)

## <a name="adjust-the-project-settings"></a>프로젝트 설정 조정

1. *Edit(편집) >Project Settings...(프로젝트 설정...)* 를 차례로 엽니다.
1. 왼쪽의 목록에서 [Quality(품질)]를 선택합니다.
1. **활성 품질 수준**을 선택하여 *낮음*으로 변경합니다.
1. **Default Quality Level(기본 품질 수준)** 을 *Low(낮음)* 로 변경합니다.

    ![프로젝트 품질 설정 변경](media/settings-quality.png)

1. 왼쪽에서 **Graphics(그래픽)** 를 선택합니다.
1. **Scriptable Rendering Pipeline(스크립트 가능한 렌더링 파이프라인)** 설정을 *HybridRenderingPipeline*으로 변경합니다. 유니버설 렌더링 파이프라인이 사용되지 않는 경우 이 단계를 건너뛰세요.

    ![프로젝트 그래픽 설정 변경](media/settings-graphics-lwrp.png) UI가 패키지에서 사용 가능한 파이프라인 유형 목록을 채우지 않는 경우가 있습니다. 이 경우 *HybridRenderingPipeline* 자산을 필드에 수동으로 끌어다 놓아야 합니다. ![프로젝트 그래픽 설정 변경](media/hybrid-rendering-pipeline.png)
1. 왼쪽에서 **Player(플레이어)** 를 선택합니다.
1. **Universal Windows Platform settings(유니버설 Windows 플랫폼 설정)** 탭을 선택합니다.
1. Windows Mixed Reality를 지원하도록 **XR Settings(XR 설정)** 를 변경합니다. ![플레이어 설정](media/xr-player-settings.png)
1. 위의 스크린샷과 같이 설정을 선택합니다.
    1. **Virtual Reality Supported(가상 현실 지원됨)** 를 사용하도록 설정합니다.
    1. **Depth Format(깊이 형식)** 을 *16-Bit Depth(16비트 깊이)* 로 설정합니다.
    1. **Depth Buffer Sharing(깊이 버퍼 공유)** 을 사용하도록 설정합니다.
    1. **Stereo Rendering Mode(스테레오 렌더링 모드)** 를 *Single Pass Instanced(단일 패스 인스턴스화됨)* 로 설정합니다.

1. 동일한 창에서 *XR Settings* 위에 있는 **Publishing Settings(게시 설정)** 를 펼칩니다.
1. **Capabilities(기능)** 까지 아래로 스크롤하여 다음을 선택합니다.
    * **InternetClient**
    * **InternetClientServer**
    * **SpatialPerception**
    * 선택적 개발 항목: **PrivateNetworkClientServer**

      이 선택적 항목은 Unity 원격 디버거를 디바이스에 연결하는 데 필요합니다.

1. **Supported Device Families(지원되는 디바이스 제품군)** 에서 **Holographic(홀로그램)** 및 **Desktop(데스크톱)** 을 사용하도록 설정합니다.

1. Mixed Reality Toolkit를 사용하려는 경우 추천 설정 및 기능에 대한 자세한 내용은 [MRTK 설명서](https://docs.microsoft.com/windows/mixed-reality/unity-development-overview)를 참조하세요.

## <a name="validate-project-setup"></a>프로젝트 설정 유효성 검사

프로젝트 설정이 올바른지 확인하려면 다음 단계를 수행합니다.

1. Unity 편집기 도구 모음의 RemoteRendering 메뉴에서 ValidateProject 항목을 선택합니다.
1. ValidateProject 창을 사용하여 프로젝트 설정을 확인하고 필요한 경우 수정합니다.

    ![Unity 편집기 프로젝트 유효성 검사](media/arr-unity-validation.png)

## <a name="create-a-script-to-initialize-azure-remote-rendering"></a>Azure Remote Rendering을 초기화하는 스크립트 만들기

[새 스크립트](https://docs.unity3d.com/Manual/CreatingAndUsingScripts.html)를 만들고, **RemoteRendering**이라는 이름을 지정합니다. 스크립트 파일을 열고 전체 내용을 아래 코드로 바꿉니다.

```csharp
using System.Collections;
using System.Collections.Generic;
using UnityEngine;
using Microsoft.Azure.RemoteRendering;
using Microsoft.Azure.RemoteRendering.Unity;

#if UNITY_WSA
    using UnityEngine.XR.WSA;
#endif

// ask Unity to automatically append an ARRServiceUnity component when a RemoteRendering script is attached
[RequireComponent(typeof(ARRServiceUnity))]
public class RemoteRendering : MonoBehaviour
{
    // fill out the variables below with your account details

    // AccountDomain must be '<region>.mixedreality.azure.com' - if no '<region>' is specified, connections will fail
    // See the documentation for the list of available regions.
    public string AccountDomain = "westus2.mixedreality.azure.com";
    public string AccountId = "<enter your account id here>";
    public string AccountKey = "<enter your account key here>";

    public uint MaxLeaseTimeHours = 0;
    public uint MaxLeaseTimeMinutes = 10;
    public RenderingSessionVmSize VMSize = RenderingSessionVmSize.Standard;

    private ARRServiceUnity arrService = null;

    private void Awake()
    {
        // initialize Azure Remote Rendering for use in Unity:
        // it needs to know which camera is used for rendering the scene
        RemoteUnityClientInit clientInit = new RemoteUnityClientInit(Camera.main);
        RemoteManagerUnity.InitializeManager(clientInit);

        // lookup the ARRServiceUnity component and subscribe to session events
        arrService = GetComponent<ARRServiceUnity>();
        arrService.OnSessionErrorEncountered += ARRService_OnSessionErrorEncountered;
        arrService.OnSessionStatusChanged += ARRService_OnSessionStatusChanged;
    }

#if !UNITY_EDITOR
    private void Start()
    {
        StartCoroutine(AutoStartSessionAsync());
    }
#endif

    private void OnDestroy()
    {
        arrService.OnSessionErrorEncountered -= ARRService_OnSessionErrorEncountered;
        arrService.OnSessionStatusChanged -= ARRService_OnSessionStatusChanged;

        RemoteManagerStatic.ShutdownRemoteRendering();
    }

    private void CreateFrontend()
    {
        if (arrService.Frontend != null)
        {
            // early out if the front-end has been created before
            return;
        }

        // initialize the ARR service with our account details
        AzureFrontendAccountInfo accountInfo = new AzureFrontendAccountInfo();
        accountInfo.AccountKey = AccountKey;
        accountInfo.AccountId = AccountId;
        accountInfo.AccountDomain = AccountDomain;

        arrService.Initialize(accountInfo);
    }

    public void CreateSession()
    {
        CreateFrontend();

        // StartSession will call ARRService_OnSessionStarted once the session becomes available
        arrService.StartSession(new RenderingSessionCreationParams(VMSize, MaxLeaseTimeHours, MaxLeaseTimeMinutes));
    }

    public void StopSession()
    {
        arrService.StopSession();
    }

    private void ARRService_OnSessionStatusChanged(ARRServiceUnity caller, AzureSession session)
    {
        LogSessionStatus(session);
    }

    private void ARRService_OnSessionErrorEncountered(ARRServiceUnity caller, SessionGeneralContext context)
    {
        Debug.LogError($"Session error encountered. Context: {context.ErrorMessage}. Request Cv: {context.RequestCorrelationVector}. Response Cv: {context.ResponseCorrelationVector}");
    }

    private async void LogSessionStatus(AzureSession session)
    {
        if (session != null)
        {
            var sessionProperties = await session.GetPropertiesAsync().AsTask();
            LogSessionStatus(sessionProperties);
        }
        else
        {
            var sessionProperties = arrService.LastProperties;
            Debug.Log($"Session ended: Id={sessionProperties.Id}");
        }
    }

    private void LogSessionStatus(RenderingSessionProperties sessionProperties)
    {
        Debug.Log($"Session '{sessionProperties.Id}' is {sessionProperties.Status}. Size={sessionProperties.Size}" +
            (!string.IsNullOrEmpty(sessionProperties.Hostname) ? $", Hostname='{sessionProperties.Hostname}'" : "") +
            (!string.IsNullOrEmpty(sessionProperties.Message) ? $", Message='{sessionProperties.Message}'" : ""));
    }

#if UNITY_EDITOR
    private void OnGUI()
    {
        if (arrService.CurrentActiveSession == null)
        {
            if (GUI.Button(new Rect(10, 10, 175, 30), "Create Session"))
            {
                CreateSession();
            }
        }
        else
        {
            if (GUI.Button(new Rect(10, 10, 175, 30), "Stop Session"))
            {
                StopSession();
            }
        }
    }
#else
    public IEnumerator AutoStartSessionAsync()
    {
        CreateSession();
    }
#endif
}
```

이 스크립트는 Azure Remote Rendering을 초기화하고, 렌더링하는 데 사용할 카메라 개체를 알려주고, *Play Mode(재생 모드)* 가 활성화되면 **Create Session(세션 만들기)** 단추를 뷰포트에 배치합니다.

> [!CAUTION]
> Unity에서 재생 모드가 활성화된 상태에서 스크립트를 수정하고 저장하면 Unity가 중단될 수 있으며 작업 관리자를 통해 강제로 종료됩니다. 따라서 *RemoteRendering* 스크립트를 편집하기 전에 항상 재생 모드를 중지하세요.

## <a name="test-azure-remote-rendering-session-creation"></a>Azure Remote Rendering 세션 만들기 테스트

장면에서 새 GameObject를 만들고, *RemoteRendering* 구성 요소를 추가합니다. Remote Rendering 계정에 적합한 *Account Domain(계정 도메인)* , *Account Id(계정 ID)* 및 *Account Key(계정 키)* 를 입력합니다.

![원격 렌더링 구성 요소 속성](media/remote-rendering-component.png)

편집기에서 애플리케이션을 시작합니다(**Play(재생)** 또는 CTRL+P 누름). 뷰포트에 **Create Session(세션 만들기)** 단추가 표시됩니다. 이 단추를 클릭하여 첫 번째 ARR 세션을 시작합니다.

![첫 번째 세션 만들기](media/test-create.png)

이 작업이 실패하면 계정 세부 정보를 RemoteRendering 구성 요소 속성에 올바르게 입력했는지 확인합니다. 그렇지 않으면 할당된 세션 ID를 보여 주고 세션이 현재 *시작 중* 상태임을 나타내는 메시지가 콘솔 창에 표시됩니다.

![세션 시작 출력](media/create-session-output.png)

이 시점에서 Azure는 서버를 프로비저닝하고 원격 렌더링 가상 머신을 시작합니다. 이 작업에는 일반적으로 **3-5분 정도** 걸립니다. VM이 준비되면 Unity 스크립트의 `OnSessionStatusChanged` 콜백이 실행되고 새 세션 상태가 출력됩니다.

![세션 준비 출력](media/create-session-output-2.png)

지금 시작이에요! 당분간 아무 상황도 발생하지 않습니다. 요금 청구를 방지하려면 더 이상 필요하지 않은 세션을 항상 중지해야 합니다. 이 샘플에서는 **Stop Session(세션 중지)** 단추를 클릭하거나 Unity 시뮬레이션을 중지하여 이 작업을 수행할 수 있습니다. 기본적으로 설정되어 있는 *ARRServiceUnity* 구성 요소의 **Auto-Stop Session(자동 세션 중지)** 속성으로 인해 세션이 자동으로 중지됩니다. 크래시 또는 연결 문제로 인해 모든 작업이 실패하면 서버에서 *MaxLeaseTime* 동안 세션을 실행한 후에 해당 세션을 종료할 수 있습니다.

> [!NOTE]
> 세션 중지는 즉시 적용되며, 실행 취소할 수 없습니다. 중지되면 동일한 시작 오버헤드를 사용하여 새 세션을 만들어야 합니다.

## <a name="reusing-sessions"></a>세션 다시 사용

아쉽게도 새 세션을 만드는 경우 시간이 많이 걸립니다. 따라서 세션을 가끔 만들고 가능한 경우 다시 사용하는 것이 좋습니다.

다음 코드를 *RemoteRendering* 스크립트에 삽입하고, 이전 버전의 중복 함수를 제거합니다.

```csharp
    public string SessionId = null;

    private void ARRService_OnSessionStatusChanged(ARRServiceUnity caller, AzureSession session)
    {
        LogSessionStatus(session);

        var status = caller.LastProperties.Status;

        // Capture the session id for the starting/ready session so it can be reused
        if (status == RenderingSessionStatus.Ready || status == RenderingSessionStatus.Starting)
        {
            SessionId = session.SessionUUID;
        }
        // Our session is stopped, expired, or in an error state.
        else
        {
            SessionId = null;
        }
    }

    public async void QueryActiveSessions()
    {
        CreateFrontend();

        var allSessionsProperties = await arrService.Frontend.GetCurrentRenderingSessionsAsync().AsTask();

        Debug.Log($"Number of active sessions: {allSessionsProperties.Length}");

        foreach (var sessionProperties in allSessionsProperties)
        {
            if (string.IsNullOrEmpty(SessionId))
            {
                Debug.Log($"Number of active sessions: {allSessionsProperties.Length}");
                SessionId = sessionProperties.Id;
            }

            LogSessionStatus(sessionProperties);
        }
    }

    public void UseExistingSession()
    {
        CreateFrontend();

        // OpenSession will call ARRService_OnSessionStarted once the session becomes available
        arrService.OpenSession(SessionId);
    }

#if UNITY_EDITOR
    private void OnGUI()
    {
        if (arrService.CurrentActiveSession == null)
        {
            if (GUI.Button(new Rect(10, 10, 175, 30), "Create Session"))
            {
                CreateSession();
            }

            if (GUI.Button(new Rect(10, 50, 175, 30), "Query Active Sessions"))
            {
                QueryActiveSessions();
            }

            if (!string.IsNullOrEmpty(SessionId))
            {
                if (GUI.Button(new Rect(10, 90, 175, 30), "Use Existing Session"))
                {
                    UseExistingSession();
                }
            }
        }
        else
        {
            if (GUI.Button(new Rect(10, 10, 175, 30), "Stop Session"))
            {
                StopSession();
            }
        }
    }
#else
    public IEnumerator AutoStartSessionAsync()
    {
        CreateSession();
    }
#endif
```

> [!CAUTION]
> 이 코드를 실행하려면 먼저 RemoteRendering 구성 요소에서 **Auto-Stop Session(자동 세션 중지)** 옵션을 비활성화해야 합니다. 그렇지 않으면 시뮬레이션을 중지할 때 만드는 모든 세션이 자동으로 중지되고, 해당 세션을 다시 사용하려고 시도하면 실패합니다.

*Play(재생)* 를 누르면 이제 **Create Session(세션 만들기)** , **Query Active Sessions(활성 세션 쿼리)** 및 **Use Existing Session(기존 세션 사용)** 이라는 세 개의 단추가 뷰포트에 표시됩니다. 첫 번째 단추는 항상 새 세션을 만듭니다. 두 번째 단추는 *활성* 상태의 세션을 쿼리합니다. 사용하려는 세션 ID를 수동으로 지정하지 않은 경우 이 작업은 나중에 사용할 해당 세션 ID를 자동으로 선택합니다. 세 번째 단추는 기존 세션에 대한 연결을 시도합니다. 이는 *Session Id(세션 ID)* 구성 요소 속성을 통해 수동으로 지정했거나 *Query Active Sessions(활성 세션 쿼리)* 에서 찾은 속성 중 하나입니다.

**AutoStartSessionAsync** 함수는 편집기 외부에서 단추 누름을 시뮬레이션하는 데 사용됩니다.

> [!TIP]
> 중지되었거나 만료되었거나 오류 상태에 있는 세션을 열 수 있습니다. 더 이상 렌더링하는 데 사용할 수 없지만, 비활성 세션을 열면 해당 세부 정보를 쿼리할 수 있습니다. 위의 코드는 `ARRService_OnSessionStarted`에서 세션 상태를 확인하여 세션을 사용할 수 없게 되면 자동으로 중지합니다.

이 기능을 사용하면 이제 세션을 만들고 다시 사용할 수 있으므로 개발 워크플로를 크게 향상시킬 수 있습니다.

일반적으로 서버를 가동하는 데 필요한 시간으로 인해 세션을 만드는 것은 클라이언트 애플리케이션 외부에서 트리거됩니다.

## <a name="connect-to-an-active-session"></a>활성 세션에 연결

지금까지 세션을 만들거나 열었습니다. 다음 단계는 세션에 *연결*하는 것입니다. 세션에 연결되면 렌더링 서버에서 이미지를 생성하고 비디오 스트림을 애플리케이션에 보냅니다.

다음 코드를 *RemoteRendering* 스크립트에 삽입하고, 이전 버전의 중복 함수를 제거합니다.

```csharp
    private bool isConnected = false;

    private void ARRService_OnSessionStatusChanged(ARRServiceUnity caller, AzureSession session)
    {
        LogSessionStatus(session);

        var status = caller.LastProperties.Status;

        // Capture the session id for the starting/ready session so it can be reused
        if (status == RenderingSessionStatus.Ready || status == RenderingSessionStatus.Starting)
        {
            SessionId = session.SessionUUID;

            // If our session properties are 'Ready' we are ready to start connecting to the runtime of the service.
            if (status == RenderingSessionStatus.Ready)
            {
                session.ConnectionStatusChanged += AzureSession_OnConnectionStatusChanged;
            }
        }
        // Our session is stopped, expired, or in an error state.
        else
        {
            SessionId = null;
            session.ConnectionStatusChanged -= AzureSession_OnConnectionStatusChanged;
        }
    }

    private void AzureSession_OnConnectionStatusChanged(ConnectionStatus status, Result result)
    {
        Debug.Log($"Connection status: '{status}', result: '{result}'");
        isConnected = (status == ConnectionStatus.Connected);
    }

    public void ConnectSession()
    {
        arrService.CurrentActiveSession?.ConnectToRuntime(new ConnectToRuntimeParams());
    }

    public void DisconnectSession()
    {
        if (isConnected)
        {
            arrService.CurrentActiveSession?.DisconnectFromRuntime();
        }
    }

    private void LateUpdate()
    {
        // The session must have its runtime pump updated.
        // The update will push messages to the server, receive messages, and update the frame-buffer with the remotely rendered content.
        arrService.CurrentActiveSession?.Actions.Update();
    }

    private void OnDisable()
    {
        DisconnectSession();
    }

#if UNITY_EDITOR
    private void OnGUI()
    {
        if (arrService.CurrentActiveSession == null)
        {
            if (GUI.Button(new Rect(10, 10, 175, 30), "Create Session"))
            {
                CreateSession();
            }

            if (GUI.Button(new Rect(10, 50, 175, 30), "Query Active Sessions"))
            {
                QueryActiveSessions();
            }

            if (!string.IsNullOrEmpty(SessionId))
            {
                if (GUI.Button(new Rect(10, 90, 175, 30), "Use Existing Session"))
                {
                    UseExistingSession();
                }
            }
        }
        else
        {
            if (GUI.Button(new Rect(10, 10, 175, 30), "Stop Session"))
            {
                StopSession();
            }

            if (arrService.LastProperties.Status == RenderingSessionStatus.Ready)
            {
                if (!isConnected)
                {
                    if (GUI.Button(new Rect(10, 50, 175, 30), "Connect"))
                    {
                        ConnectSession();
                    }
                }
                else
                {
                    if (GUI.Button(new Rect(10, 50, 175, 30), "Disconnect"))
                    {
                        DisconnectSession();
                    }
                }
            }
        }
    }
#else
    public IEnumerator AutoStartSessionAsync()
    {
        CreateSession();
        while (arrService.CurrentActiveSession == null)
        {
            yield return null;
        }
        ConnectSession();
    }
#endif
```

이 기능을 테스트하려면

1. Unity에서 **Play(재생)** 를 누릅니다.
1. 세션을 엽니다.
    1. 세션이 이미 있는 경우 **Query Active Sessions(활성 세션 쿼리)** , **Use Existing session(기존 세션 사용)** 을 차례로 누릅니다.
    1. 그렇지 않으면 **Create Session(세션 만들기)** 을 누릅니다.
1. **Connect(연결)** 를 누릅니다.
1. 몇 초 후에 콘솔 출력에서 연결되었다는 메시지가 출력됩니다.
1. 지금은 다른 작업이 수행되지 않습니다.
1. **Disconnect(연결 끊기)** 를 누르거나 Unity의 재생 모드를 중지합니다.

>[!NOTE]
> 여러 사용자가 한 세션을 *열어* 해당 정보를 쿼리할 수 있지만, 한 번에 한 사용자만 세션에 *연결할* 수 있습니다. 다른 사용자가 이미 연결되어 있으면 **핸드셰이크 오류**로 인해 연결이 실패합니다.

## <a name="load-a-model"></a>모델 로드

다음 코드를 *RemoteRendering* 스크립트에 삽입하고, 이전 버전의 중복 함수를 제거합니다.

```csharp

    public string ModelName = "builtin://Engine";

    private Entity modelEntity = null;
    private GameObject modelEntityGO = null;

#if UNITY_WSA
    private WorldAnchor modelWorldAnchor = null;
#endif

    public async void LoadModel()
    {
        // create a root object to parent a loaded model to
        modelEntity = arrService.CurrentActiveSession.Actions.CreateEntity();

        // get the game object representation of this entity
        modelEntityGO = modelEntity.GetOrCreateGameObject(UnityCreationMode.DoNotCreateUnityComponents);

        // ensure the entity will sync translations with the server
        var sync = modelEntityGO.GetComponent<RemoteEntitySyncObject>();
        sync.SyncEveryFrame = true;

        // set position to an arbitrary distance from the parent
        modelEntityGO.transform.position = Camera.main.transform.position + Camera.main.transform.forward * 2;
        modelEntityGO.transform.localScale = Vector3.one;

#if UNITY_WSA
        // anchor the model in the world
        modelWorldAnchor = modelEntityGO.AddComponent<WorldAnchor>();
#endif

        // load a model that will be parented to the entity
        // We are using the 'from SAS' flavor because that variant can load the built-in model
        var loadModelParams = new LoadModelFromSASParams(ModelName, modelEntity);
        var async = arrService.CurrentActiveSession.Actions.LoadModelFromSASAsync(loadModelParams);
        async.ProgressUpdated += (float progress) =>
        {
            Debug.Log($"Loading: {progress * 100.0f}%");
        };

        await async.AsTask();
    }

    public void DestroyModel()
    {
        if (modelEntity == null)
        {
            return;
        }

#if UNITY_WSA
        DestroyImmediate(modelWorldAnchor);
#endif

        modelEntity.Destroy();
        modelEntity = null;

        DestroyImmediate(modelEntityGO);
    }

    public void DisconnectSession()
    {
        if (isConnected)
        {
            arrService.CurrentActiveSession?.DisconnectFromRuntime();
        }

        DestroyModel();
    }

#if UNITY_EDITOR
    private void OnGUI()
    {
        if (arrService.CurrentActiveSession == null)
        {
            if (GUI.Button(new Rect(10, 10, 175, 30), "Create Session"))
            {
                CreateSession();
            }

            if (GUI.Button(new Rect(10, 50, 175, 30), "Query Active Sessions"))
            {
                QueryActiveSessions();
            }

            if (!string.IsNullOrEmpty(SessionId))
            {
                if (GUI.Button(new Rect(10, 90, 175, 30), "Use Existing Session"))
                {
                    UseExistingSession();
                }
            }
        }
        else
        {
            if (GUI.Button(new Rect(10, 10, 175, 30), "Stop Session"))
            {
                StopSession();
            }

            if (arrService.LastProperties.Status == RenderingSessionStatus.Ready)
            {
                if (!isConnected)
                {
                    if (GUI.Button(new Rect(10, 50, 175, 30), "Connect"))
                    {
                        ConnectSession();
                    }
                }
                else
                {
                    if (GUI.Button(new Rect(10, 50, 175, 30), "Disconnect"))
                    {
                        DisconnectSession();
                    }

                    if (modelEntity == null)
                    {
                        if (GUI.Button(new Rect(10, 90, 175, 30), "Load Model"))
                        {
                            LoadModel();
                        }
                    }
                    else
                    {
                        if (GUI.Button(new Rect(10, 90, 175, 30), "Destroy Model"))
                        {
                            DestroyModel();
                        }
                    }
                }
            }
        }
    }
#else
    public IEnumerator AutoStartSessionAsync()
    {
        CreateSession();
        while (arrService.CurrentActiveSession == null)
        {
            yield return null;
        }
        ConnectSession();
        while (!isConnected)
        {
            yield return null;
        }
        LoadModel();
    }
#endif
```

이제 재생을 누르고 세션을 열고 연결하면 **Load Model(모델 로드)** 단추가 표시됩니다. 이 단추를 클릭하면 콘솔 출력에서 로드 진행 상황이 표시되고 100%에 도달하면 엔진 모델이 표시됩니다.

![편집기에서 로드된 모델](media/model-loaded-replace-me.png)

[WorldAnchor](https://docs.unity3d.com/ScriptReference/XR.WSA.WorldAnchor.html)는 [홀로그램 안정성](https://docs.microsoft.com/windows/mixed-reality/hologram-stability)에 사용되는 중요한 구성 요소입니다. 그러나 Mixed Reality 디바이스에 배포되는 경우에만 효과가 있습니다.

> [!TIP]
> [빠른 시작: 렌더링을 위해 모델 변환](../../quickstarts/convert-model.md)을 수행한 경우 사용자 고유의 모델을 변환하는 방법을 이미 알고 있습니다. 이제 렌더링을 위해 수행해야 하는 작업으로, 변환된 모델에 대한 URI를 *Model Name(모델 이름)* 속성에 배치하기만 하면 됩니다.

## <a name="display-frame-statistics"></a>프레임 통계 표시

Azure Remote Rendering은 연결 품질에 대한 다양한 정보를 추적합니다. 이 정보를 빠르게 표시하려면 다음을 수행합니다.

[새 스크립트](https://docs.unity3d.com/Manual/CreatingAndUsingScripts.html)를 만들고, **RemoteFrameStats**라는 이름을 지정합니다. 스크립트 파일을 열고 전체 내용을 아래 코드로 바꿉니다.

```csharp
using Microsoft.Azure.RemoteRendering;
using Microsoft.Azure.RemoteRendering.Unity;
using System;
using System.Collections.Generic;
using UnityEngine;
using UnityEngine.UI;

public class RemoteFrameStats : MonoBehaviour
{
    public Text FrameStats = null;

    ARRServiceStats arrServiceStats = null;

#if UNITY_EDITOR
    private void OnEnable()
    {
        arrServiceStats = new ARRServiceStats();
    }

    void Update()
    {
        if (!RemoteManagerUnity.IsConnected)
        {
            FrameStats.text = string.Empty;
            return;
        }

        arrServiceStats.Update(RemoteManagerUnity.CurrentSession);

        if (FrameStats != null)
        {
            FrameStats.text = arrServiceStats.GetStatsString();
        }
    }
#endif
}
```

GameObject를 만들고, *FrameStats*라는 이름을 지정합니다. 이를 자식 노드로 *Main Camera(기본 카메라)* 개체에 연결하고, 위치를 **x = 0, y = 0, z = 0.325**로 설정합니다. **RemoteFrameStats** 구성 요소를 개체에 추가합니다.

**UI > Canvas(캔버스)** 자식 개체를 *FrameStats* 개체에 추가하고, 해당 속성을 다음과 같이 설정합니다.

![캔버스 속성](media/framestats-canvas.png)

**UI > Text(텍스트)** 개체를 캔버스의 자식 개체로 추가하고, 해당 속성을 다음과 같이 설정합니다.

![텍스트 속성](media/framestats-text.png)

*FrameStats* 개체를 선택하고, 원 아이콘을 클릭하고 **Text** 개체를 선택하여 **FrameStats 필드**를 채웁니다.

![텍스트 속성 설정](media/framestats-set-text.png)

이제 원격 세션에 연결되면 텍스트에 스트리밍 통계가 표시됩니다.

![프레임 통계 출력](media/framestats-output.png)

헤드 잠금 텍스트 상자에서 방해할 수 있으므로 이 코드는 편집기 외부에서 통계 업데이트를 사용하지 않도록 설정합니다. 더 정교한 구현은 [빠른 시작](../../quickstarts/render-model.md) 프로젝트에 있습니다.

## <a name="next-steps"></a>다음 단계

이 자습서에서는 빈 Unity 프로젝트를 가져오고 Azure Remote Rendering을 사용하는 데 필요한 모든 단계를 알아보았습니다. 다음 자습서에서는 원격 엔터티를 사용하는 방법을 자세히 살펴봅니다.

> [!div class="nextstepaction"]
> [자습서: Unity에서 원격 엔터티로 작업](working-with-remote-entities.md)
