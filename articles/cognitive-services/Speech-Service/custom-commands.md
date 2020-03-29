---
title: 사용자 지정 명령(미리 보기) - 음성 서비스
titleSuffix: Azure Cognitive Services
description: 음성 응용 프로그램을 만들기 위한 솔루션인 사용자 지정 명령(미리 보기)의 기능, 기능 및 제한 사항에 대한 개요입니다.
services: cognitive-services
author: trrwilson
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 03/11/2020
ms.author: travisw
ms.openlocfilehash: 2e1b6ee0bd6c392804915fac6ff23278a00b6d33
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79367842"
---
# <a name="what-are-custom-commands-preview"></a>사용자 지정 명령(미리 보기)이란 무엇입니까?

[음성 도우미와](voice-assistants.md) 같은 음성 응용 프로그램은 사용자의 말을 듣고 응답하여 조치를 취하며 종종 다시 말하기를 합니다. [음성-텍스트를](speech-to-text.md) 사용하여 사용자의 음성을 전사한 다음 텍스트의 자연어 이해에 대한 조치를 취합니다. 이 작업에는 [텍스트 음성 변환으로](text-to-speech.md)생성된 도우미의 음성 출력이 자주 포함됩니다. 장치는 음성 SDK의 `DialogServiceConnector` 개체와 도우미에 연결합니다.

**사용자 지정 명령(미리 보기)은** 음성 응용 프로그램을 만들기 위한 간소화된 솔루션입니다. 통합 된 작성 환경, 자동 호스팅 모델 및 [직접 회선 음성](direct-line-speech.md)과 같은 다른 옵션에 비해 상대적으로 낮은 복잡성을 제공합니다. 그러나 이러한 단순화는 유연성의 감소와 함께 제공됩니다. 따라서 사용자 지정 명령(미리 보기)은 작업 완료 또는 명령 및 제어 시나리오에 가장 적합합니다. 특히 사물 인터넷(IoT) 및 헤드리스 디바이스와 잘 어울린다.

[가상 도우미 솔루션 및 엔터프라이즈 템플릿과](https://docs.microsoft.com/azure/bot-service/bot-builder-enterprise-template-overview) 같은 다른 솔루션과의 복잡한 대화 상호 작용 및 통합을 위해 직접 회선 음성을 사용하는 것이 좋습니다.

사용자 지정 명령(미리 보기)에 적합한 후보에는 잘 정의된 변수 집합이 있는 고정 된 어휘가 있습니다. 예를 들어, 온도 조절 장치 제어와 같은 홈 오토메이션 작업이 이상적입니다.

   ![작업 완료 시나리오의 예](media/voice-assistants/task-completion-examples.png "작업 완료 예")

## <a name="getting-started-with-custom-commands-preview"></a>사용자 지정 명령 시작(미리 보기)

음성 응용 프로그램을 만들기 위해 사용자 지정 명령(미리 보기)을 사용하는 첫 번째 단계는 [음성 구독 키를 얻고 음성](get-started.md) [스튜디오의](https://speech.microsoft.com)사용자 지정 명령(미리 보기) 빌더에 액세스하는 것입니다. 거기에서 새 사용자 지정 명령(미리 보기) 응용 프로그램을 작성하고 게시한 다음 장치 내 응용 프로그램이 음성 SDK를 사용하여 통신할 수 있습니다.

   ![사용자 지정 명령에 대한 흐름 작성(미리 보기)](media/voice-assistants/custom-commands-flow.png "사용자 지정 명령(미리 보기) 작성 흐름")

10분 이내에 코드를 실행할 수 있도록 설계된 퀵스타트를 제공합니다.

* [사용자 지정 명령 만들기(미리 보기) 응용 프로그램 만들기](quickstart-custom-speech-commands-create-new.md)
* [매개 변수를 사용하여 사용자 지정 명령(미리 보기) 응용 프로그램 만들기](quickstart-custom-speech-commands-create-parameters.md)
* [음성 SDK, C를 사용하여 사용자 지정 명령(미리 보기) 응용 프로그램에 연결 #](quickstart-custom-speech-commands-speech-sdk.md)

빠른 시작이 완료되면, 우리의 방법을 탐구한다.

- [사용자 지정 명령 매개 변수에 유효성 검사 추가](./how-to-custom-speech-commands-validations.md)
- [음성 SDK로 클라이언트에 대한 명령 이행](./how-to-custom-speech-commands-fulfill-sdk.md)
- [사용자 지정 명령에 확인 추가](./how-to-custom-speech-commands-confirmations.md)
- [사용자 지정 명령에 1단계 수정 추가](./how-to-custom-speech-commands-one-step-correction.md)

## <a name="next-steps"></a>다음 단계

* [평가판 Speech Service 구독 키 받기](get-started.md)
* [사용자 지정 명령을 사용해 보시려면 음성 스튜디오로 이동](https://speech.microsoft.com)
* [Speech SDK 가져오기](speech-sdk.md)
