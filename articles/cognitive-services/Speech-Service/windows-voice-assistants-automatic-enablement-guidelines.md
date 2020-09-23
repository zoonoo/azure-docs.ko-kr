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
ms.openlocfilehash: a9fe083818c5850ad05556b18911c19fe19e0e72
ms.sourcegitcommit: bdd5c76457b0f0504f4f679a316b959dcfabf1ef
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/22/2020
ms.locfileid: "90987398"
---
# <a name="privacy-guidelines-for-voice-assistants-on-windows"></a>Windows의 음성 도우미에 대 한 개인 정보 취급 방침

사용자에 게 음성 데이터를 수집 하 고 사용 하는 방법에 대 한 명확한 정보를 제공 하는 것이 중요 합니다 개인 정보 보호의 핵심 패싯 ( *공개* 및 *동의* )은 항상 수신 대기 특성을 감안 하 여 Windows에 통합 된 음성 도우미에 특히 중요 합니다.

Windows에서 음성 도우미를 만드는 개발자는 해당 응용 프로그램 내에서 길잡이의 수신 기능을 반영 하는 명확한 사용자 인터페이스 요소를 포함 해야 합니다.

> [!NOTE]
> 응용 프로그램 업데이트 후를 포함 하 여 길잡이 응용 프로그램에 대해 적절 한 공개 및 동의를 제공 하지 못하면 개인 정보 문제를 해결할 때까지 길잡이를 음성 활성화에 사용할 수 없게 될 수 있습니다.

## <a name="minimum-requirements-for-feature-inclusion"></a>기능 포함을 위한 최소 요구 사항

Windows 사용자는에서 해당 길잡이 응용 프로그램의 가용성을 확인 하 고 제어할 수 있습니다 **`Settings > Privacy > Voice activation`** .

 > [!div class="mx-imgBorder"]
 > [![가용성 Cortana를 제어 하는 옵션을 보여 줍니다. ](media/voice-assistants/windows_voice_assistant/privacy-app-listing.png "길잡이 응용 프로그램에 대 한 Windows 음성 정품 인증 개인 정보 설정 항목")](media/voice-assistants/windows_voice_assistant/privacy-app-listing.png#lightbox)

이 목록에 포함 될 수 있도록 하려면 Microsoft에 문의 winvoiceassistants@microsoft.com 하 여 시작 하세요. 기본적으로 사용자는에서 새 길잡이에 대 한 음성 활성화를 명시적으로 사용 하도록 설정 해야 **`Settings > Privacy > Voice Activation`** 합니다. 그러면 응용 프로그램에서에 대 한 프로토콜 링크를 사용할 수 있습니다 `ms-settings:privacy-voiceactivation` . 허용 된 응용 프로그램이 실행 되 고 api를 사용 하면 목록에 표시 됩니다 `Windows.ApplicationModel.ConversationalAgent` . 응용 프로그램이 사용자 로부터 마이크 동의를 받은 후에는 음성 활성화 설정을 수정할 수 있습니다.

Windows 개인 정보 설정에는 음성 정품 인증의 작동 방식에 대 한 정보와 사용 권한 제어에 대 한 표준 UI가 포함 되어 있기 때문에, 공개 및 동의가 모두 충족 됩니다. 길잡이는 다음과 같은 경우에만 허용 되는 목록에 그대로 남아 있습니다.

* 사용자에 게 사용자에 게 음성 활성화 또는 길잡이의 음성 데이터 처리에 대 한 잘못 된 정보를 알립니다.
* 다른 비서와 간섭 지나치게
* 기타 관련 Microsoft 정책을 중단 합니다.

위의 내용이 검색 되 면 문제가 해결 될 때까지 Microsoft에서 허용 된 목록에 있는 길잡이를 제거할 수 있습니다.

> [!NOTE]
> 모든 경우에 음성 활성화 권한에는 마이크 권한이 필요 합니다. 길잡이 응용 프로그램에 마이크 액세스 권한이 없는 경우 음성 정품 인증을 수행할 수 없으며 음성 정품 인증 개인 정보 설정에 사용 안 함 상태로 표시 됩니다.

## <a name="additional-requirements-for-inclusion-in-microphone-consent"></a>마이크 동의에 포함 하기 위한 추가 요구 사항

사용자가 음성 활성화에 참여 하는 것이 더 쉽고 원활 하 게 하려는 비서 작성자는 설정 페이지를 추가로 이동 하지 않고도 공개 및 동의를 적절 하 게 수행 하기 위한 추가 요구 사항을 충족 하 여 이러한 작업을 수행할 수 있습니다. 승인 되 면 사용자가 길잡이 응용 프로그램에 마이크 사용 권한을 부여 하 고 나면 즉시 음성 활성화를 사용할 수 있게 됩니다. 이를 위해 길잡이 응용 프로그램은 마이크 동의를 요청 **하기 전에** 다음을 수행 해야 합니다 (예 `AppCapability.RequestAccessAsync` : API 사용).

1. 응용 프로그램이 *실행 되 고 있지 않고*사용자의 동의를 원하는 경우에도 응용 프로그램에서 사용자의 음성으로 키워드를 수신 대기 하도록 사용자에 게 명확 하 고 두드러진 표시를 제공 합니다.
1. 공식 개인 정보 취급 방침에 대 한 링크와 같은 데이터 사용량 및 개인 정보 취급 방침에 대 한 관련 정보를 포함 합니다.
1. 공개 오디오 캡처 동작을 수행 하는 환경 흐름에서 지시문 또는 선행 단어를 사용 하지 않습니다 (예: "다음 프롬프트에서 예를 클릭 합니다").

응용 프로그램에서 위의 모든 기능을 수행 하는 경우 마이크 동의와 함께 음성 활성화 기능을 사용 하도록 설정할 수 있습니다. winvoiceassistants@microsoft.com자세한 내용을 확인 하 고 첫 번째 사용 환경을 검토 하세요.

> [!NOTE]
> 위의 음성 활성화는 마이크 액세스를 사용 하 여 자동으로 사용 하기에 적합 하지 않으며, 길잡이에 대해 위의 잠금 액세스를 사용 하도록 설정 하려면 음성 정품 인증 개인 정보 페이지를 방문 하는 사용자도 필요 합니다.

## <a name="next-steps"></a>다음 단계

> [!div class="nextstepaction"]
> [Windows의 음성 도우미 모범 사례에 대 한 자세한 정보](windows-voice-assistants-best-practices.md)