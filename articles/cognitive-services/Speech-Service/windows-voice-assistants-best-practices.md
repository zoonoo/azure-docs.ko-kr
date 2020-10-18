---
title: Windows의 음성 도우미-디자인 지침
titleSuffix: Azure Cognitive Services
description: 음성 에이전트 환경을 디자인 하는 경우 모범 사례에 대 한 지침입니다.
services: cognitive-services
author: cfogg6
manager: spencer.king
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 05/1/2020
ms.author: adamwa
ms.openlocfilehash: 76a29af80891142fcf7f56f93f5c7acc5c58ab04
ms.sourcegitcommit: 419c8c8061c0ff6dc12c66ad6eda1b266d2f40bd
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/18/2020
ms.locfileid: "92165148"
---
# <a name="design-assistant-experiences-for-windows-10"></a>Windows 10 용 디자인 도우미 환경

Windows 10에서 개발한 음성 도우미는 Windows 10에서 음성 정품 인증을 수행할 수 있는 최상의 환경을 제공 하기 위해 아래 사용자 환경 지침을 구현 해야 합니다. 이 문서에서는 개발자가 음성 길잡이가 Windows 10 Shell과 통합 하는 데 필요한 주요 작업을 이해 하는 과정을 안내 합니다.

## <a name="contents"></a>콘텐츠

- [Windows 10에서 지원 되는 음성 활성화 보기 요약](#summary-of-voice-activation-views-supported-in-windows-10)
- [요구 사항 요약](#requirements-summary)
- [바람직한 수신 환경에 대 한 모범 사례](#best-practices-for-good-listening-experiences)
- [앱 내 음성 활성화를 위한 디자인 지침](#design-guidance-for-in-app-voice-activation)
- [잠금 위의 음성 활성화를 위한 디자인 지침](#design-guidance-for-voice-activation-above-lock)
- [음성 활성화 미리 보기에 대 한 디자인 지침](#design-guidance-for-voice-activation-preview)

## <a name="summary-of-voice-activation-views-supported-in-windows-10"></a>Windows 10에서 지원 되는 음성 활성화 보기 요약

Windows 10은 장치 컨텍스트를 기준으로 고객 컨텍스트에 대 한 정품 인증 환경을 유추 합니다. 다음 요약 표는 화면이 켜져 있을 때 사용할 수 있는 여러 보기에 대 한 개략적인 개요입니다.

| 보기 (가용성) | 장치 컨텍스트 | 고객 목표 | 표시 되는 경우 | 디자인 요구 사항 |
| --- | --- | --- | --- | --- |
| **앱 내 (19H1)** | 잠금 아래에는 도우미가 집중 되어 있습니다. | 길잡이 앱과 상호 작용 | 도우미가 앱 내에서 요청을 처리 합니다. | 기본 앱 내 보기 수신 환경 |
| **잠금 초과 (19H2)** | 잠금 초과, 인증 되지 않음 | 한 거리에서 도우미와 상호 작용 | 시스템이 잠겨 있고 도우미 요청 활성화 | Far UI에 대 한 전체 화면 시각적 개체입니다. 잠금 해제를 차단 하지 않도록 해제 정책을 구현 합니다. |
| **음성 활성화 미리 보기 (20H1)** | 잠금 아래에는 길잡이에 포커스가 없습니다. | 도우미와 상호 작용 하지만, 더 저렴 한 방법으로 | 시스템이 잠금 및 도우미 요청 배경 활성화 아래에 있습니다. | 최소 캔버스. 필요에 따라 주 앱 보기로 크기를 조정 하거나 직접 해제 합니다. |

## <a name="requirements-summary"></a>요구 사항 요약

다양 한 환경에 액세스 하려면 최소한의 노력이 필요 합니다. 그러나 도우미는 각 보기에 적합 한 디자인 지침을 구현 해야 합니다. 다음 표에서는 따라야 하는 요구 사항에 대 한 검사 목록을 제공 합니다.

| **음성 활성화 보기** | **길잡이 요구 사항 요약** |
| --- | --- |
| **앱 내** | <ul><li>앱 내 요청 처리</li><li>수신 대기 상태에 대 한 UI 표시기를 제공 합니다.</li><li>UI는 창 크기 변경으로 조정 됩니다.</li></ul> |
| **잠금 초과** | <ul><li>잠금 상태 검색 및 활성화 요청</li><li>Windows 잠금 화면에 대 한 액세스를 차단 하는 항상 영구적인 UX를 제공 하지 마십시오.</li><li>전체 화면 시각적 개체 및 음성 우선 환경 제공</li><li>아래 해제 지침 준수</li><li>아래의 개인 정보 및 보안 고려 사항 준수</li></ul> |
| **음성 활성화 미리 보기** | <ul><li>잠금 해제 상태 검색 및 백그라운드 활성화 요청</li><li>미리 보기 창에서 최소 수신 UX 그리기</li><li>오른쪽 위에 있는 X를 그리면 닫을 때 오디오 스트리밍을 자동으로 해제 하 고 중지 합니다.</li><li>답변을 제공 하기 위해 필요에 따라 주 길잡이 앱 보기에 대 한 크기 조정 또는 핸드 오프</li></ul> |

## <a name="best-practices-for-good-listening-experiences"></a>바람직한 수신 환경에 대 한 모범 사례

도우미는 고객이 길잡이 상태를 이해할 수 있도록 중요 한 피드백을 제공 하는 수신 환경을 구축 해야 합니다. 다음은 길잡이 환경을 구축할 때 고려할 수 있는 몇 가지 상태입니다. 이러한 항목은 필수 지침이 아닌 제안 사항에 불과합니다.

- 길잡이를 음성 입력에 사용할 수 있습니다.
- 길잡이를 활성화 하는 중입니다 (키워드 또는 mic 단추 누르기).
- Assistant가 현재 길잡이 클라우드에 오디오를 스트리밍하는 경우
- 도우미가 고객에 게 말하기를 시작할 준비가 되었습니다.
- 도우미가 단어를 말합니다.
- 도우미가 고객이 말하는 것을 이해 합니다.
- 도우미가 응답을 처리 하 고 준비 중입니다.
- 도우미가 응답 중입니다.

상태가 빠르게 변경 되는 경우에도 기간은 Windows 에코 시스템에서 가변적인 상태 이므로 상태에 대 한 UX를 제공 하는 것을 고려할 가치가 있습니다. 시각적 피드백 뿐만 아니라 brief 단점 이라고 하는 brief 오디오 chimes 또는 chimes는 &quot; &quot; 솔루션에 포함 될 수 있습니다. 마찬가지로, 오디오 설명과 결합 된 시각적 카드는 좋은 응답 옵션을 위해 작성 되었습니다.

## <a name="design-guidance-for-in-app-voice-activation"></a>앱 내 음성 활성화를 위한 디자인 지침

길잡이 앱에 포커스가 있는 경우 고객 의도가 명확 하 게 앱과 상호 작용 하 여 모든 음성 활성화 환경을 기본 앱 보기에서 처리 해야 합니다. 이 보기는 고객의 크기를 조정할 수 있습니다. 이 문서의 나머지 부분에서는 도우미 셸 상호 작용을 설명 하기 위해 Contoso 라는 금융 서비스 길잡이의 구체적인 예를 사용 합니다. 이 다이어그램과 이후 다이어그램에서 고객에 게 표시 되는 것은 오른쪽의 카툰 거품형에서 보조자 응답을 사용 하 여 왼쪽에 있는 만화 음성 거품에 표시 됩니다.

**앱 내 보기입니다. 음성 활성화가 시작 되는 초기 상태:** 
 ![ Contoso 재무 길잡이 앱이 기본 캔버스로 열려 있음을 보여 주는 스크린샷 오른쪽에 있는 만화 음성 거품형에는 "Contoso"가 표시 됩니다.](media/voice-assistants/windows_voice_assistant/initial_state.png)

**앱 내 보기입니다. 음성 활성화가 완료 되 면 수신 환경이 시작 됩니다.** ![ 음성 길잡이가 수신 대기 하는 동안 Windows에서 음성 도우미 스크린샷](media/voice-assistants/windows_voice_assistant/listening.png)

**앱 내 보기입니다. 모든 응답은 앱 환경에서 유지 됩니다.** ![ 길잡이 회신으로 Windows의 음성 도우미 스크린샷](media/voice-assistants/windows_voice_assistant/response.png)

## <a name="design-guidance-for-voice-activation-above-lock"></a>잠금 위의 음성 활성화를 위한 디자인 지침

19H2에서 사용할 수 있습니다. Windows 음성 정품 인증 플랫폼을 기반으로 하는 길잡이는 위의 잠금에 대답할 수 있습니다.

### <a name="customer-opt-in"></a>고객 옵트인

잠금 위의 음성 활성화는 기본적으로 항상 사용 하지 않도록 설정 됩니다. Windows 설정>개인 정보 보호>음성 활성화를 통해 옵트인 합니다. 이 설정에 대 한 모니터링 및 요청에 대 한 자세한 내용은 [위의 잠금 구현 가이드](windows-voice-assistants-implementation-guide.md#detecting-above-lock-activation-user-preference)를 참조 하세요.

### <a name="not-a-lock-screen-replacement"></a>잠금 화면을 대체 하지 않음

알림 또는 다른 표준 앱 잠금 화면 통합 지점은 길잡이에 계속 사용할 수 있지만 Windows 잠금 화면에서는 음성 활성화가 발생할 때까지 항상 초기 사용자 환경을 정의 합니다. 음성 활성화가 검색 되 면 도우미 앱은 일시적으로 잠금 화면 위에 표시 됩니다. 사용자의 혼동을 방지 하기 위해, 위의 잠금 활성 시 길잡이 앱은 모든 종류의 자격 증명을 요청 하거나 로그인 할 수 있는 UI를 제공 해서는 안 됩니다.

![Windows 잠금 화면 스크린샷](media/voice-assistants/windows_voice_assistant/lock_screen1.png)

### <a name="above-lock-experience-following-voice-activation"></a>음성 활성화 후의 잠금 환경

화면이 켜져 있으면 도우미 앱은 잠금 화면 위의 제목 표시줄 없이 전체 화면에 표시 됩니다. 강력한 음성 기본 인터페이스를 사용 하는 더 큰 시각적 개체 및 강력한 음성 설명은 고객이 UI를 읽거나 다른 (비 PC) 작업을 사용 하는 데 너무 멀리 떨어져 있는 경우에 허용 됩니다.

화면이 꺼진 상태로 유지 되 면 길잡이 앱은 도우미가 활성화 되 고 음성 전용 환경을 제공 한다는 것을 나타내는 데 사용할 수 있습니다.

![잠금 위의 음성 길잡이 스크린샷](media/voice-assistants/windows_voice_assistant/above_lock_listening.png)

### <a name="dismissal-policies"></a>해제 정책

이 길잡이는 고객이 다음에 Windows PC를 사용할 때 쉽게 로그인 할 수 있도록 하기 위해이 섹션의 해제 지침을 구현 해야 합니다. 다음은 도우미가 구현 해야 하는 특정 요구 사항입니다.

- **위의 잠금을 보여 주는 모든 보조자 캔버스** 는 도우미를 해제 하는 오른쪽 위에 X를 포함 해야 합니다.
- **키를 누르면 길잡이 앱도 해제 해야**합니다. 키보드 입력은 고객이 로그인 하려는 기존의 잠금 앱 신호입니다. 따라서 키보드/텍스트 입력을 앱으로 전달 하면 안 됩니다. 대신, 사용자가 장치에 쉽게 로그인 할 수 있도록 키보드 입력이 검색 되 면 앱을 자동으로 해제 해야 합니다.
- **화면이 꺼진 경우 앱을 자동으로 해제 해야 합니다.** 이렇게 하면 다음에 고객이 PC를 사용할 때 로그인 화면이 준비 되 고 대기 하 게 됩니다.
- 앱을 사용 중인 경우 &quot; &quot; 잠금을 계속 진행할 수 있습니다. &quot;in 사용에서 &quot; 입력 또는 출력을 구성 합니다. 예를 들어 음악 또는 동영상을 스트리밍할 때 앱이 잠금을 계속 진행할 수 있습니다. &quot;후속 &quot; 및 기타 multiturn 대화 상자 단계를 통해 앱을 잠금 상태로 유지할 수 있습니다.
- **응용 프로그램 해제에 대 한 구현 세부 정보** 는 [위의 잠금 구현 가이드에서](windows-voice-assistants-implementation-guide.md#closing-the-application)찾을 수 있습니다.

![Contoso 재무 길잡이 앱의 위의 잠금 보기를 보여 주는 스크린샷](media/voice-assistants/windows_voice_assistant/above_lock_response.png)

![Windows 잠금 화면을 표시 하는 바탕 화면의 스크린샷.](media/voice-assistants/windows_voice_assistant/lock_screen2.png)

### <a name="privacy-amp-security-considerations-above-lock"></a>잠금 위에 있는 개인 정보 보호 &amp; 보안 고려 사항

많은 Pc는 이식 가능 하지만 항상 고객에 게 도달 하는 것은 아닙니다. 다른 사람이 물리적으로 액세스 하는 호텔 대화방, 비행기 좌석 또는 작업 영역에 잠시 남아 있을 수 있습니다. 잠금을 통해 사용 하도록 설정 된 도우미가 준비 되지 않은 경우에는 &quot; [악의적인 maid](https://en.wikipedia.org/wiki/Evil_maid_attack) 공격 클래스가 될 수 있습니다 &quot; .

따라서 도우미는이 섹션의 지침에 따라 환경을 안전 하 게 유지 해야 합니다. 위의 상호 작용 잠금은 Windows 사용자가 인증 되지 않은 경우에 발생 합니다. 즉, 일반적으로 **도우미에 대 한 입력도 인증 되지 않은 것으로 처리**되어야 합니다.

- 도우미는 보안을 확인 하 고 안전 하 게 안전 하 게 액세스할 수 **있는 기술을 식별 하는 데 사용할 수 있는 기술 허용 목록을 구현** 해야 합니다.
- 스피커 ID 기술은 일부 위험을 완화 하는 역할을 수행할 수 있지만, 스피커 ID는 Windows 인증에 적합 한 대체가 아닙니다.
- 사용할 수 있는 기술 목록은 다음과 같은 세 가지 종류의 동작 또는 기술을 고려해 야 합니다.

| **Action 클래스** | **설명** | **예 (전체 목록 아님)** |
| --- | --- | --- |
| 인증 없이 안전 | 범용 정보 또는 기본 앱 명령 및 제어 | &quot;시간 이란 무엇 인가요? &quot; &quot; 다음 트랙을 재생 합니다.&quot; |
| 스피커 ID로 안전 | 가장 위험은 개인 정보를 노출 합니다. | &quot;다음 약속을&#39;하 &quot; 고, &quot; 쇼핑 목록을 검토 하 고 &quot; , &quot; 통화에 답변 하세요.&quot; |
| Windows 인증 후에만 안전 | 공격자가 고객을 손상 시키는 데 사용할 수 있는 높은 위험 수준 작업 | &quot;더 많은 잡화 &quot; 를 구매 하 고 ( &quot; 중요) 약속을 삭제 하 고 &quot; &quot; (평균) 문자 메시지 &quot; 를 보내고 &quot; (부정한) 웹 페이지를 시작 합니다.&quot; |

Contoso의 경우 공개 주식 정보에 대 한 일반 정보는 인증 없이 안전 합니다. 소유 하 고 있는 공유 수와 같은 고객 관련 정보는 발표자 ID로 안전 합니다. 그러나 Windows 인증 없이는 주식 구매 또는 판매를 허용 해서는 안 됩니다.

환경을 더욱 안전 하 게 보호 하기 위해 **고객이 로그인 할 때까지 weblinks 또는 기타 앱 간 시작은 항상 Windows에서 차단 됩니다.** 최후의 수단으로, Microsoft는 심각한 보안 문제가 적시에 해결 되지 않은 경우 허용 되는 길잡이 목록에서 응용 프로그램을 제거할 권리를 보유 합니다.

## <a name="design-guidance-for-voice-activation-preview"></a>음성 활성화 미리 보기에 대 한 디자인 지침

잠금 아래에는 길잡이 앱에 포커스가 _없는 경우_ 고객에 게 흐름을 유지 하는 데 도움이 되는 낮은 개입 음성 활성화 UI가 제공 됩니다. 이는 전체 앱을 시작한 경우 매우 방해가 되는 거짓 활성화의 경우 특히 그렇습니다. 핵심 개념은 각 길잡이가 보조자 작업 표시줄 아이콘 셸에서 또 다른 홈을 포함 한다는 것입니다. 백그라운드 활성화 요청이 발생 하면 길잡이 작업 표시줄 아이콘 위에 작은 보기가 나타납니다. 도우미는이 캔버스에서 소규모 수신 대기 환경을 제공 해야 합니다. 요청을 처리 한 후 길잡이는이 보기의 크기를 조정 하 여 상황에 맞는 답변을 표시 하거나 주 앱 보기를 직접 표시 하 여 더 크고 더 자세한 시각적 개체를 표시 하도록 선택할 수 있습니다.

- 최소 상태를 유지 하기 위해 미리 보기에는 제목 표시줄이 없으므로 **길잡이는 고객이 뷰를 해제할 수 있도록 오른쪽 위에 X를 그려야 합니다.** 해제 단추를 누를 때 호출할 특정 Api에 대 한 [응용 프로그램 닫기](windows-voice-assistants-implementation-guide.md#closing-the-application) 를 참조 하세요.
- 음성 활성화 미리 보기를 지원 하기 위해 도우미가 처음 실행 중에 길잡이를 작업 표시줄에 고정 하도록 초대할 수 있습니다.

**음성 활성화 미리 보기: 초기 상태**

Contoso assistant에는 작업 표시줄에 대 한 홈이 있습니다 (swirling, 원형 아이콘).

![작업 표시줄 아이콘 사전 활성화로 Windows의 음성 길잡이 스크린샷](media/voice-assistants/windows_voice_assistant/pre_compact_view.png)

**활성화가 진행 되**면 길잡이가 백그라운드 활성화를 요청 합니다. 길잡이에는 작은 미리 보기 창이 제공 됩니다 (기본 너비 408 및 높이: 248). 서버 쪽 음성 활성화에서 신호를 거짓 긍정으로 판단 하는 경우 최소한의 중단으로이 보기를 해제할 수 있습니다.

![활성화를 확인 하는 동안 압축 보기의 Windows 음성 길잡이 스크린샷](media/voice-assistants/windows_voice_assistant/compact_view_activating.png)

**최종 활성화가 확인 되 면이**길잡이는 수신 UX를 표시 합니다. 길잡이는 항상 음성 활성화 미리 보기의 오른쪽 위에 있는 해제 X를 그려야 합니다.

![Compact 보기에서 수신 대기 중인 Windows의 음성 도우미 스크린샷](media/voice-assistants/windows_voice_assistant/compact_view_listening.png)

**빠른 답** 은 음성 활성화 미리 보기에 표시 될 수 있습니다. TryResizeView는 도우미가 다른 크기를 요청할 수 있도록 합니다.

![Compact 보기의 Windows 회신 음성 도우미 스크린샷](media/voice-assistants/windows_voice_assistant/compact_view_response.png)

**백오프**. 언제 든 지 길잡이는 추가 정보, 대화 또는 더 많은 화면 부동산을 필요로 하는 답변을 제공 하기 위해 주 앱 보기로 이동할 수 있습니다. 구현에 대 한 자세한 내용은 [compact 뷰에서 전체 뷰로 전환](windows-voice-assistants-implementation-guide.md#transition-from-compact-view-to-full-view) 섹션을 참조 하세요.

![컴팩트 뷰를 확장 하기 전 및 후의 Windows에서 음성 길잡이 스크린샷](media/voice-assistants/windows_voice_assistant/compact_transition.png)

## <a name="next-steps"></a>다음 단계

> [!div class="nextstepaction"]
> [음성 도우미 개발 시작 하기](how-to-windows-voice-assistants-get-started.md)