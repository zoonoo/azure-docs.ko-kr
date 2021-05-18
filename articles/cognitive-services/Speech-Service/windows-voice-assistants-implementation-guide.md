---
title: Windows의 음성 도우미 - 잠금 화면 위 구현 지침
titleSuffix: Azure Cognitive Services
description: 음성 에이전트 애플리케이션에 대한 음성 활성화 및 잠금 화면 위 기능을 구현하는 지침입니다.
services: cognitive-services
author: cfogg6
manager: trrwilson
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 04/15/2020
ms.author: travisw
ms.custom: devx-track-csharp
ms.openlocfilehash: 92ab043d4fccbe0764e361eac6f71ef69a5963cb
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/30/2021
ms.locfileid: "98939858"
---
# <a name="implementing-voice-assistants-on-windows"></a>Windows에서 음성 도우미 구현

이 가이드에서는 Windows에서 음성 도우미를 만들기 위한 중요한 구현 세부 정보를 안내합니다.

## <a name="implementing-voice-activation"></a>음성 활성화 구현

[환경을 설정](how-to-windows-voice-assistants-get-started.md)하고 [음성 활성화 작동 방식](windows-voice-assistants-overview.md#how-does-voice-activation-work)을 학습한 후 자체 음성 도우미 애플리케이션에 대한 음성 활성화 구현을 시작할 수 있습니다.

### <a name="registration"></a>등록

#### <a name="ensure-that-the-microphone-is-available-and-accessible-then-monitor-its-state"></a>마이크를 사용할 수 있고 액세스할 수 있는지 확인한 다음 상태를 모니터링합니다.

MVA는 음성 활성화를 감지할 수 있도록 마이크가 있고 액세스할 수 있어야 합니다. [AppCapability](/uwp/api/windows.security.authorization.appcapabilityaccess.appcapability), [DeviceWatcher](/uwp/api/windows.devices.enumeration.devicewatcher) 및 [MediaCapture](/uwp/api/windows.media.capture.mediacapture) 클래스를 사용하여 각각 마이크 개인 정보 액세스, 디바이스 존재 및 디바이스 상태(예: 볼륨 및 음소거)를 확인합니다.

### <a name="register-the-application-with-the-background-service"></a>백그라운드 서비스에 애플리케이션 등록

MVA가 백그라운드에서 애플리케이션을 시작하려면 애플리케이션을 백그라운드 서비스에 등록해야 합니다. [여기](/windows/uwp/launch-resume/register-a-background-task)에서 백그라운드 서비스 등록에 대한 전체 지침을 참조하세요.

### <a name="unlock-the-limited-access-feature"></a>제한된 액세스 기능 잠금 해제

Microsoft에서 제공하는 제한된 액세스 기능 키를 사용하여 음성 도우미 기능을 잠금 해제합니다. 이 작업을 수행하려면 Windows SDK의 [LimitedAccessFeature](/uwp/api/windows.applicationmodel.limitedaccessfeatures) 클래스를 사용합니다.

### <a name="register-the-keyword-for-the-application"></a>애플리케이션에 대한 키워드 등록

애플리케이션은 자체, 키워드 모델 및 언어를 Windows에 등록해야 합니다.

먼저 키워드 감지기를 검색합니다. 이 샘플 코드에서는 첫 번째 감지기를 검색하지만 `configurableDetectors`에서 선택하여 특정 감지기를 선택할 수 있습니다.

```csharp
private static async Task<ActivationSignalDetector> GetFirstEligibleDetectorAsync()
{
    var detectorManager = ConversationalAgentDetectorManager.Default;
    var allDetectors = await detectorManager.GetAllActivationSignalDetectorsAsync();
    var configurableDetectors = allDetectors.Where(candidate => candidate.CanCreateConfigurations
        && candidate.Kind == ActivationSignalDetectorKind.AudioPattern
        && (candidate.SupportedModelDataTypes.Contains("MICROSOFT_KWSGRAPH_V1")));

    if (configurableDetectors.Count() != 1)
    {
        throw new NotSupportedException($"System expects one eligible configurable keyword spotter; actual is {configurableDetectors.Count()}.");
    }

    var detector = configurableDetectors.First();

    return detector;
}
```

ActivationSignalDetector 개체를 검색한 후 신호 ID, 모델 ID 및 표시 이름을 통해 `ActivationSignalDetector.CreateConfigurationAsync` 메서드를 호출하여 키워드를 등록하고 애플리케이션의 `ActivationSignalDetectionConfiguration`을 검색합니다. 신호 및 모델 ID는 개발자가 결정한 GUID여야 하며 동일한 키워드에 대해 일관성을 유지해야 합니다.

### <a name="verify-that-the-voice-activation-setting-is-enabled"></a>음성 활성화가 설정되었는지 확인

음성 활성화를 사용하려면 사용자가 시스템에 대해 음성 활성화를 사용하도록 설정하고 애플리케이션에 대해 음성 활성화를 사용하도록 설정해야 합니다. Windows 설정의 "음성 활성화 개인 정보 보호 설정"에서 설정을 찾을 수 있습니다. 애플리케이션에서 음성 활성화 설정의 상태를 확인하려면 키워드 등록에서 `ActivationSignalDetectionConfiguration` 인스턴스를 사용합니다. `ActivationSignalDetectionConfiguration`의 [AvailabilityInfo](https://github.com/Azure-Samples/Cognitive-Services-Voice-Assistant/blob/master/clients/csharp-uwp/UWPVoiceAssistantSample/UIAudioStatus.cs#L128) 필드에는 음성 활성화 설정의 상태를 설명하는 열거형 값이 포함되어 있습니다.

### <a name="retrieve-a-conversationalagentsession-to-register-the-app-with-the-mva-system"></a>ConversationalAgentSession을 검색하여 MVA 시스템에 앱 등록

`ConversationalAgentSession`은 앱이 앱 상태(유휴, 감지, 수신, 작업, 말하기)로 Windows를 업데이트하고 활성화 감지 및 화면 잠금과 같은 시스템 상태 변경과 같은 이벤트를 수신할 수 있도록 하는 Windows SDK의 클래스입니다. AgentSession의 인스턴스를 검색하면 음성으로 활성화할 수 있도록 Windows에 애플리케이션을 등록하는 역할도 합니다. `ConversationalAgentSession`에 대해 하나의 참조를 유지하는 것이 가장 좋습니다. 세션을 검색하려면 `ConversationalAgentSession.GetCurrentSessionAsync` API를 사용합니다.

### <a name="listen-to-the-two-activation-signals-the-onbackgroundactivated-and-onsignaldetected"></a>OnBackgroundActivated 및 OnSignalDetected의 두 가지 활성화 신호를 수신합니다.

Windows에서 두 가지 방법 중 하나로 키워드를 감지하면 앱에 신호를 보냅니다. 앱이 활성 상태가 아니면(즉, 삭제되지 않은 `ConversationalAgentSession` 인스턴스에 대한 참조가 없는 경우) 앱을 시작하고 애플리케이션의 App.xaml.cs 파일에서 OnBackgroundActivated 메서드를 호출합니다. 이벤트 인수의 `BackgroundActivatedEventArgs.TaskInstance.Task.Name` 필드가 "AgentBackgroundTrigger" 문자열과 일치하면 애플리케이션 시작이 음성 활성화에 의해 트리거된 것입니다. 애플리케이션은 이 메서드를 재정의하고 ConversationalAgentSession의 인스턴스를 검색하여 현재 활성화된 Windows에 신호를 보내야 합니다. 애플리케이션이 활성화되면 Windows에서 `ConversationalAgentSession.OnSignalDetected` 이벤트를 사용하여 음성 활성화 발생을 알립니다. `ConversationalAgentSession`이 감지되는 즉시 이 이벤트에 이벤트 처리기를 추가합니다.

## <a name="keyword-verification"></a>키워드 확인

음성 에이전트 애플리케이션이 음성으로 활성화되면 다음 단계는 키워드 감지가 유효한지 확인하는 것입니다. Windows는 키워드 확인을 위한 솔루션을 제공하지 않지만 음성 도우미가 가정된 활성화에서 오디오에 액세스하고 자체적으로 확인을 완료할 수 있도록 합니다.

### <a name="retrieve-activation-audio"></a>활성화 오디오 검색

[AudioGraph](/uwp/api/windows.media.audio.audiograph)를 만들고 `ConversationalAgentSession`의 `CreateAudioDeviceInputNodeAsync`에 전달합니다. 그러면 *키워드가 감지되기 약 3초 전에 시작되는* 오디오와 함께 그래프의 오디오 버퍼가 로드됩니다. 이 추가 선행 오디오는 다양한 키워드 길이 및 화자의 속도를 수용하기 위해 포함되었습니다. 그런 다음 오디오 그래프에서 [QuantumStarted](/uwp/api/windows.media.audio.audiograph.quantumstarted) 이벤트를 처리하여 오디오 데이터를 검색합니다.

```csharp
var inputNode = await agentSession.CreateAudioDeviceInputNodeAsync(audioGraph);
var outputNode = inputGraph.CreateFrameOutputNode();
inputNode.AddOutgoingConnection(outputNode);
audioGraph.QuantumStarted += OnQuantumStarted;
```

참고: 오디오 버퍼에 포함된 선행 오디오로 인해 키워드 확인이 실패할 수 있습니다. 이 문제를 해결하려면 키워드 확인을 위해 오디오를 보내기 전에 오디오 버퍼의 시작 부분을 잘라냅니다. 이 초기 트리밍은 각 도우미에 맞게 조정해야 합니다.

### <a name="launch-in-the-foreground"></a>전경에서 시작

키워드 확인이 성공하면 애플리케이션이 UI를 표시하기 위해 전경으로 이동해야 합니다. `ConversationalAgentSession.RequestForegroundActivationAsync` API를 호출하여 애플리케이션을 전경으로 이동합니다.

### <a name="transition-from-compact-view-to-full-view"></a>간단히 보기에서 전체 보기로 전환

음성으로 애플리케이션을 처음 활성화하면 간단히 보기에서 시작됩니다. Windows의 음성 도우미에 대한 다양한 보기 및 전환에 대한 지침을 보려면 [음성 활성화 미리 보기를 위한 디자인 지침](windows-voice-assistants-best-practices.md#design-guidance-for-voice-activation-preview)을 읽어보세요.

간단히 보기에서 전체 앱 보기로 전환하려면 ApplicationView API `TryEnterViewModeAsync`를 사용합니다.

```csharp
var appView = ApplicationView.GetForCurrentView();
await appView.TryEnterViewModeAsync(ApplicationViewMode.Default);
```

## <a name="implementing-above-lock-activation"></a>잠금 화면 위 활성화 구현

다음 단계에서는 애플리케이션 수명 주기를 관리하기 위한 코드 예 및 지침에 대한 참조를 포함하여 Windows의 음성 도우미가 잠금 화면 위에서 실행될 수 있도록 하기 위한 요구 사항을 다룹니다.

잠금 환경 위 환경 설계에 대한 지침은 [모범 사례 가이드](windows-voice-assistants-best-practices.md)를 참조하세요.

앱에 잠금 화면 위 보기가 표시되면 "키오스크 모드"로 간주됩니다. 키오스크 모드를 사용하는 앱 구현에 대한 자세한 내용은 [키오스크 모드 설명서](/windows-hardware/drivers/partnerapps/create-a-kiosk-app-for-assigned-access)를 참조하세요.

### <a name="transitioning-above-lock"></a>잠금 화면 위 전환

잠금 화면 위 활성화는 잠금 화면 아래 활성화와 유사합니다. 애플리케이션의 활성 인스턴스가 없는 경우 새 인스턴스가 백그라운드에서 시작되고 App.xaml.cs의 `OnBackgroundActivated`가 호출됩니다. 애플리케이션의 인스턴스가 있는 경우 해당 인스턴스는 `ConversationalAgentSession.SignalDetected` 이벤트를 통해 알림을 받습니다.

애플리케이션이 아직 잠금 화면 위에 표시되지 않는 경우 `ConversationalAgentSession.RequestForegroundActivationAsync`를 호출해야 합니다. 그러면 잠금 화면 위에 표시될 보기로 이동해야 하는 App.xaml.cs의 `OnLaunched` 메서드가 트리거됩니다.

### <a name="detecting-lock-screen-transitions"></a>잠금 화면 전환 감지

Windows SDK의 ConversationalAgent 라이브러리는 잠금 화면 상태 및 잠금 화면 상태 변경에 쉽게 액세스할 수 있는 API를 제공합니다. 현재 잠금 화면 상태를 감지하려면 `ConversationalAgentSession.IsUserAuthenticated` 필드를 확인합니다. 잠금 상태의 변경을 감지하려면 `ConversationalAgentSession` 개체의 `SystemStateChanged` 이벤트에 이벤트 처리기를 추가합니다. 화면이 잠금 해제에서 잠금으로 또는 그 반대로 변경될 때마다 실행됩니다. 이벤트 인수의 값이 `ConversationalAgentSystemStateChangeType.UserAuthentication`이면 잠금 화면 상태가 변경된 것입니다.

```csharp
conversationalAgentSession.SystemStateChanged += (s, e) =>
{
    if (e.SystemStateChangeType == ConversationalAgentSystemStateChangeType.UserAuthentication)
    {
        // Handle lock state change
    }
};
```

### <a name="detecting-above-lock-activation-user-preference"></a>잠금 화면 위 활성화 사용자 기본 설정 감지

음성 활성화 개인 정보 보호 설정 페이지의 애플리케이션 항목에는 잠금 화면 위 기능에 대한 토글이 있습니다. 앱이 잠금 화면 위에서 실행될 수 있으려면 사용자가 이 설정을 켜야 합니다. 잠금 화면 위 권한 상태는 ActivationSignalDetectionConfiguration 개체에도 저장됩니다. AvailabilityInfo.HasLockScreenPermission 상태는 사용자가 잠금 화면 위 권한을 부여했는지 여부를 반영합니다. 이 설정을 사용하지 않도록 설정하면 음성 애플리케이션에서 "ms-settings:privacy-voiceactivation" 링크의 적절한 설정 페이지로 이동하라는 메시지를 사용자에게 표시합니다. 이 페이지에는 애플리케이션에 대해 잠금 화면 위 활성화를 설정하는 방법에 대한 지침이 있습니다.

## <a name="closing-the-application"></a>애플리케이션 닫기

잠금 화면 위 또는 아래에서 프로그래밍 방식으로 애플리케이션을 올바르게 닫으려면 `WindowService.CloseWindow()` API를 사용합니다. 이렇게 하면 OnSuspend를 포함한 모든 UWP 수명 주기 메서드가 트리거되어 애플리케이션이 닫기 전에 `ConversationalAgentSession` 인스턴스를 삭제할 수 있습니다.

> [!NOTE]
> [잠금 화면 아래 인스턴스](/windows-hardware/drivers/partnerapps/create-a-kiosk-app-for-assigned-access#add-a-way-out-of-assigned-access-)를 닫지 않고도 애플리케이션을 닫을 수 있습니다. 이 경우 화면이 잠금 해제될 때 잠금 화면 아래 보기를 조작하려는 이벤트 처리기나 작업이 없도록 잠금 화면 위 보기를 "정리"해야 합니다.

## <a name="next-steps"></a>다음 단계

> [!div class="nextstepaction"]
> [UWP 음성 도우미 샘플 앱을 방문하여 예 및 코드 연습 확인](windows-voice-assistants-faq.md#the-uwp-voice-assistant-sample)
