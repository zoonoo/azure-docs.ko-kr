---
title: 사용자 지정 명령 (미리 보기)-음성 서비스
titleSuffix: Azure Cognitive Services
description: 음성 응용 프로그램을 만들기 위한 솔루션인 사용자 지정 명령 (미리 보기)의 기능, 기능 및 제한 사항에 대 한 개요입니다.
services: cognitive-services
author: trrwilson
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 03/11/2020
ms.author: travisw
ms.openlocfilehash: 3c02e823202aa848a4de94885276835899562e4b
ms.sourcegitcommit: 309cf6876d906425a0d6f72deceb9ecd231d387c
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/01/2020
ms.locfileid: "84266018"
---
# <a name="what-is-custom-commands-preview"></a>사용자 지정 명령 (미리 보기) 이란?

[음성 도우미](voice-assistants.md) 와 같은 음성 응용 프로그램은 사용자를 수신 대기 하 고 응답으로 작업을 수행 하는 경우가 많습니다. [음성 텍스트](speech-to-text.md) 를 사용 하 여 사용자의 음성을 높여줄 다음 텍스트에 대 한 자연어 이해에 대 한 작업을 수행 합니다. 이 작업에는 [텍스트를 음성](text-to-speech.md)으로 생성 하 여 생성 된 비서의 음성 출력이 포함 되는 경우가 많습니다. 장치는 음성 SDK 개체를 사용 하 여 도우미에 연결 `DialogServiceConnector` 합니다.

**사용자 지정 명령 (미리 보기)** 은 음성 응용 프로그램을 만들기 위한 간소화 된 솔루션입니다. 통합 된 작성 환경을 제공 하 고, 자동 호스팅 모델을 제공 하며, 비교적 낮은 복잡성과 [직접 줄 음성](direct-line-speech.md)같은 기타 옵션을 제공 합니다. 그러나이 단순화는 유연성이 줄어듭니다. 따라서 사용자 지정 명령 (미리 보기)은 작업 완료 또는 명령 및 제어 시나리오에 가장 적합 합니다. 사물 인터넷 (IoT) 및 헤드리스 장치에 대해 특히 잘 일치 합니다.

[가상 길잡이 솔루션 및 엔터프라이즈 템플릿과](https://docs.microsoft.com/azure/bot-service/bot-builder-enterprise-template-overview) 같은 다른 솔루션과의 상호 작용 및 복잡 한 대화형 상호 작용을 위해 직접 줄 음성을 사용 하는 것이 좋습니다.

사용자 지정 명령에 적합 한 후보 (미리 보기)에는 잘 정의 된 변수 집합이 있는 고정 된 어휘가 있습니다. 예를 들어 자동 온도 조절기 제어와 같은 홈 자동화 작업은 이상적입니다.

   ![작업 완료 시나리오의 예](media/voice-assistants/task-completion-examples.png "작업 완료 예")

## <a name="getting-started-with-custom-commands-preview"></a>사용자 지정 명령 시작 (미리 보기)

사용자 지정 명령 (미리 보기)을 사용 하 여 음성 응용 프로그램을 만드는 첫 번째 단계는 음성 [구독 키를 가져오고](get-started.md) [Speech Studio](https://speech.microsoft.com)에서 사용자 지정 명령 (미리 보기) 작성기에 액세스 하는 것입니다. 여기에서 새 사용자 지정 명령 (미리 보기) 응용 프로그램을 작성 하 고 게시할 수 있습니다. 그 후에는 장치 응용 프로그램이 음성 SDK를 사용 하 여 통신할 수 있습니다.

   ![사용자 지정 명령의 제작 흐름 (미리 보기)](media/voice-assistants/custom-commands-flow.png "사용자 지정 명령 (미리 보기) 제작 흐름")

10 분 이내에 코드를 실행 하도록 설계 된 빠른 시작을 제공 합니다.

* [사용자 지정 명령 (미리 보기) 응용 프로그램 만들기](quickstart-custom-speech-commands-create-new.md)
* [매개 변수를 사용 하 여 사용자 지정 명령 (미리 보기) 응용 프로그램 만들기](quickstart-custom-speech-commands-create-parameters.md)
* [음성 SDK, C를 사용 하 여 사용자 지정 명령 (미리 보기) 응용 프로그램에 연결 #](quickstart-custom-speech-commands-speech-sdk.md)

빠른 시작을 완료 한 후에는 방법을 살펴보세요.

- [사용자 지정 명령 매개 변수에 유효성 검사 추가](./how-to-custom-speech-commands-validations.md)
- [음성 SDK를 사용 하 여 클라이언트에서 명령 수행](./how-to-custom-speech-commands-fulfill-sdk.md)
- [사용자 지정 명령에 확인 추가](./how-to-custom-speech-commands-confirmations.md)
- [사용자 지정 명령에 1단계 수정 추가](./how-to-custom-speech-commands-one-step-correction.md)

## <a name="next-steps"></a>다음 단계

* [평가판 Speech Service 구독 키 받기](get-started.md)
* [Speech Studio로 이동 하 여 사용자 지정 명령 사용해 보기](https://speech.microsoft.com)
* [Speech SDK 가져오기](speech-sdk.md)
