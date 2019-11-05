---
title: 사용자 지정 명령 (미리 보기)-음성 서비스
titleSuffix: Azure Cognitive Services
description: 음성 도우미를 만들기 위한 솔루션인 사용자 지정 명령 (미리 보기)의 기능, 기능 및 제한 사항에 대 한 개요입니다.
services: cognitive-services
author: trrwilson
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 11/05/2019
ms.author: travisw
ms.openlocfilehash: e41a037e657a8d2b484a3fcb62eb3bc03f53682d
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/04/2019
ms.locfileid: "73500899"
---
# <a name="custom-commands-preview"></a>사용자 지정 명령 (미리 보기)

[음성 도우미](voice-assistants.md) 는 사용자를 수신 하 고 응답으로 작업을 수행 하 여 자주 말하는 경우를 말합니다. [음성 텍스트](speech-to-text.md) 를 사용 하 여 사용자의 음성을 높여줄 다음 텍스트에 대 한 자연어 이해에 대 한 작업을 수행 합니다. 이 작업에는 [텍스트를 음성](text-to-speech.md)으로 생성 하 여 생성 된 비서의 음성 출력이 포함 되는 경우가 많습니다. 장치는 Speech SDK의 `DialogServiceConnector` 개체를 사용 하 여 도우미에 연결 합니다.

**사용자 지정 명령 (미리 보기)** 은 음성 도우미를 만드는 간소화 된 솔루션입니다. 통합 된 제작 환경, 자동 호스팅 모델 및 [직접 줄 음성](direct-line-speech.md)같은 다른 도우미 만들기 옵션과는 상대적으로 낮은 복잡성을 제공 합니다. 그러나이 단순화는 유연성이 줄어듭니다. 따라서 사용자 지정 명령 (미리 보기)은 작업 완료 또는 명령 및 제어 시나리오에 가장 적합 합니다.

[가상 길잡이 솔루션 및 엔터프라이즈 템플릿과](https://docs.microsoft.com/azure/bot-service/bot-builder-enterprise-template-overview) 같은 다른 솔루션과의 상호 작용 및 복잡 한 대화형 상호 작용을 위해 직접 줄 음성을 사용 하는 것이 좋습니다.

사용자 지정 명령에 적합 한 후보 (미리 보기)에는 잘 정의 된 변수 집합이 있는 고정 된 어휘가 있습니다. 예를 들어 자동 온도 조절기 제어와 같은 홈 자동화 작업은 이상적입니다.

   ![작업 완료 시나리오의 예](media/voice-assistants/task-completion-examples.png "작업 완료 예")

## <a name="getting-started-with-custom-commands-preview"></a>사용자 지정 명령 시작 (미리 보기)

사용자 지정 명령 (미리 보기)을 사용 하 여 음성 도우미를 만드는 첫 번째 단계는 음성 [구독 키를 가져오고](get-started.md) [Speech Studio](https://speech.microsoft.com)에서 사용자 지정 명령 (미리 보기) 작성기에 액세스 하는 것입니다. 여기에서 새 사용자 지정 명령 (미리 보기) 응용 프로그램을 작성 하 고 게시할 수 있습니다. 그 후에는 장치 응용 프로그램이 음성 SDK를 사용 하 여 통신할 수 있습니다.

   ![사용자 지정 명령의 제작 흐름 (미리 보기)](media/voice-assistants/custom-commands-flow.png "사용자 지정 명령 (미리 보기) 제작 흐름")

10 분 이내에 코드를 실행 하도록 설계 된 빠른 시작을 제공 합니다.

* [사용자 지정 명령 (미리 보기) 응용 프로그램 만들기](quickstart-custom-speech-commands-create-new.md)
* [매개 변수를 사용 하 여 사용자 지정 명령 (미리 보기) 응용 프로그램 만들기](quickstart-custom-speech-commands-create-parameters.md)
* [음성 SDK를 사용 하 여 사용자 지정 명령 (미리 보기) 응용 프로그램에 연결C#](quickstart-custom-speech-commands-speech-sdk.md)

## <a name="sample-code"></a>샘플 코드

사용자 지정 명령 (미리 보기)을 사용 하 여 음성 도우미를 만드는 샘플 코드는 GitHub에서 사용할 수 있습니다.

* [음성 도우미 샘플 (SDK)](https://aka.ms/csspeech/samples)

## <a name="customization"></a>사용자 지정

Azure Speech Services를 사용 하 여 빌드된 음성 도우미는 [음성 텍스트](speech-to-text.md), [텍스트 음성 변환](text-to-speech.md)및 [사용자 지정 키워드 선택](speech-devices-sdk-create-kws.md)에 사용할 수 있는 사용자 지정 옵션의 전체 범위를 사용할 수 있습니다.

> [!NOTE]
> 사용자 지정 옵션은 언어/로캘에 따라 다릅니다 ( [지원 되는 언어](supported-languages.md)참조).

## <a name="reference-docs"></a>참조 문서

* [Speech SDK](speech-sdk-reference.md)

## <a name="next-steps"></a>다음 단계

* [무료로 Speech Service 구독 키 받기](get-started.md)
* [음성 SDK 가져오기](speech-sdk.md)
