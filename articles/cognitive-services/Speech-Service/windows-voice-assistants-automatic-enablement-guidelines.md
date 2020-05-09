---
title: Windows의 음성 도우미에 대 한 개인 정보 취급 방침
titleSuffix: Azure Cognitive Services
description: 음성에 대 한 음성 활성화를 사용 하도록 설정 하는 지침입니다.
services: cognitive-services
author: cfogg6
manager: trrwilson
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 04/15/2020
ms.author: travisw
ms.openlocfilehash: 436367ede4f4be323b5334a201b1c9fb8f7f28e8
ms.sourcegitcommit: 309a9d26f94ab775673fd4c9a0ffc6caa571f598
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/09/2020
ms.locfileid: "82997777"
---
# <a name="privacy-guidelines-for-voice-assistants-on-windows"></a>Windows의 음성 도우미에 대 한 개인 정보 취급 방침

사용자에 게 음성 데이터를 수집 하 고 사용 하는 방법에 대 한 명확한 정보를 제공 하는 것이 중요 합니다 개인 정보 보호의 핵심 패싯 ( *공개* 및 *동의* )은 항상 수신 대기 특성을 감안 하 여 Windows에 통합 된 음성 도우미에 특히 중요 합니다.

Windows에서 음성 도우미를 만드는 개발자는 해당 응용 프로그램 내에서 길잡이의 수신 기능을 반영 하는 명확한 사용자 인터페이스 요소를 포함 해야 합니다.

> [!NOTE]
> 응용 프로그램 업데이트 후를 포함 하 여 길잡이 응용 프로그램에 대해 적절 한 공개 및 동의를 제공 하지 못하면 개인 정보 문제를 해결할 때까지 길잡이를 음성 활성화에 사용할 수 없게 될 수 있습니다. 

## <a name="minimum-requirements-for-feature-inclusion"></a>기능 포함을 위한 최소 요구 사항

Windows 사용자는에서 **`Settings > Privacy > Voice activation`** 해당 길잡이 응용 프로그램의 가용성을 확인 하 고 제어할 수 있습니다.

 > [!div class="mx-imgBorder"]
 > [![개인 정보-앱 목록](media/voice-assistants/windows_voice_assistant/privacy-app-listing.png "길잡이 응용 프로그램에 대 한 Windows 음성 정품 인증 개인 정보 설정 항목")](media/voice-assistants/windows_voice_assistant/privacy-app-listing.png#lightbox)

이 목록에 포함할 수 있게 하려면 응용 프로그램에서 다음을 수행 해야 합니다.

1. 응용 프로그램이 실행 되 고 있지 않은 경우에도 키워드를 수신 대기 한다는 사실을 사용자에 게 명확 하 게 알려 줍니다.
1. 관련 개인 정보 취급 방침에 대 한 링크 또는 참조를 포함 하 여 사용자의 음성 데이터를 사용 하는 방법에 대 한 설명을 포함 합니다.
1. 사용자에 게 앱 내 설정 외에도, 사용자가 직접 액세스를 **`Settings > Privacy > Voice activation`** `ms-settings:privacy-voiceactivation` 위해에 대 한 프로토콜 링크를 포함 하 여에서 개인 정보 선택을 보고 수정할 수 있음을 알립니다.

이러한 요구 사항을 충족 하 고 Microsoft 로부터 승인을 받은 후에는 길잡이 응용 프로그램이 `Windows.ApplicationModel.ConversationalAgent` api에 등록 되 면 음성 활성화 앱 목록에 표시 되 고 사용자는 키워드 활성화를 위해 응용 프로그램에 동의를 부여할 수 있습니다. 기본적으로 이러한 설정은 모두 `Off` 이며 사용자가 설정 페이지를 수동으로 방문 하 여를 사용 하도록 설정 해야 합니다.

> [!NOTE]
> 모든 경우에 음성 활성화 권한에는 마이크 권한이 필요 합니다. 길잡이 응용 프로그램에 마이크 액세스 권한이 없는 경우 음성 정품 인증을 수행할 수 없으며 음성 정품 인증 개인 정보 설정에 사용 안 함 상태로 표시 됩니다.

## <a name="additional-requirements-for-inclusion-in-microphone-consent"></a>마이크 동의에 포함 하기 위한 추가 요구 사항

사용자가 음성 활성화에 참여 하는 것이 더 쉽고 원활 하 게 하려는 보조자 작성자는 위의 요구 사항에 몇 가지 추가 요구 사항을 충족 하 여이 작업을 수행할 수 있습니다. 이러한 기능을 충족 한 후에는 길잡이 응용 프로그램의 표준 장치 잠금 해제 음성 정품 인증 `On` 설정이 기본적으로 한 번 (한 번만) 마이크 액세스가 응용 프로그램에 부여 됩니다. 이렇게 하면 길잡이를 음성으로 활성화 하기 전에 설정에 대 한 추가 여행 필요성이 제거 됩니다.

추가 요구 사항은 길잡이 응용 프로그램에서 다음을 수행 해야 한다는 것입니다.

1. 사용자에 게 응용 프로그램이 실행 되 고 있지 않은 경우 `AppCapability.RequestAccessAsync` 에도 사용자의 동의를 요청 하는 경우에도, 마이크 동의 확인 (예: API 사용) **하기 전에** 사용자에 게 키워드에 대 한 사용자 의견을 수신 하는 것을 사용자에 게 시각적으로 제공 합니다.
2. 마이크 액세스를 요청 하거나 `Windows.ApplicationModel.ConversationalAgent` api를 사용 하기 **전에** 데이터 사용량 및 개인 정보 취급 방침에 대 한 모든 관련 정보를 포함 합니다.
3. 환경 흐름에서 오디오 캡처 동작을 공개 하 고 권한을 요청 하는 것과 같은 지시문 또는 선행 단어를 사용 하지 않습니다.

이러한 요구 사항이 충족 되 면 마이크 액세스가 부여 된 후 `enabled` 상태에서 음성 정품 인증에 적합 한 응용 프로그램 목록에 적격 지원자 응용 프로그램이 표시 됩니다.

> [!NOTE]
> 위의 음성 활성화는 마이크 액세스를 사용 하 여 자동으로 사용 하기에 적합 하지 않으며, 길잡이에 대해 위의 잠금 액세스를 사용 하도록 설정 하려면 음성 정품 인증 개인 정보 페이지를 방문 하는 사용자도 필요 합니다.

## <a name="next-steps"></a>다음 단계

> [!div class="nextstepaction"]
> [Windows의 음성 도우미 모범 사례에 대 한 자세한 정보](windows-voice-assistants-best-practices.md)