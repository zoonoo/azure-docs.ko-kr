---
title: 사용자 지정 명령-Speech service
titleSuffix: Azure Cognitive Services
description: 음성 응용 프로그램을 만들기 위한 솔루션인 사용자 지정 명령의 기능, 기능 및 제한 사항에 대 한 개요입니다.
services: cognitive-services
author: trrwilson
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 03/11/2020
ms.author: travisw
ms.openlocfilehash: c9f7168bc0524b483413ade9792af18ff9cfebfa
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/09/2020
ms.locfileid: "91358545"
---
# <a name="what-is-custom-commands"></a>사용자 지정 명령이란?

[음성 도우미](voice-assistants.md) 와 같은 응용 프로그램은 사용자를 수신 대기 하 고 응답으로 작업을 수행 하는 경우가 많습니다. [음성 텍스트](speech-to-text.md) 를 사용 하 여 사용자의 음성을 높여줄 다음 텍스트에 대 한 자연어 이해에 대 한 작업을 수행 합니다. 이 작업에는 [텍스트를 음성](text-to-speech.md)으로 생성 하 여 생성 된 비서의 음성 출력이 포함 되는 경우가 많습니다. 장치는 음성 SDK 개체를 사용 하 여 도우미에 연결 `DialogServiceConnector` 합니다.

**사용자 지정 명령을** 사용 하면 음성 우선 상호 작용 환경에 최적화 된 풍부한 음성 명령 앱을 쉽게 빌드할 수 있습니다. 통합 된 제작 환경, 자동 호스팅 모델 및 비교적 낮은 복잡성을 제공 하므로 음성 명령 시나리오에 가장 적합 한 솔루션을 구축 하는 데 집중할 수 있습니다.

사용자 지정 명령은 작업 완료 또는 명령 및 제어 시나리오에 가장 적합 합니다. 특히 IoT (사물 인터넷) 장치, 주변 및 헤드리스 장치에 대해 잘 일치 합니다. 예를 들면, 의료, 소매 및 자동차 산업을 위한 솔루션을 포함 하 여 게스트에 가장 적합 한 회의실 음성 제어 환경을 구축 하 고, 스토어에서 인벤토리를 관리 하 고, 이동 중에 자동차 기능을 제어할 수 있습니다.

> [!TIP]
> 의 방문 페이지에서 샘플 데모를 봅니다 [https://speech.microsoft.com/customcommands](https://speech.microsoft.com/customcommands) .

복잡 한 대화형 앱을 빌드하는 데 관심이 있는 경우 [가상 길잡이 솔루션](https://docs.microsoft.com/azure/bot-service/bot-builder-enterprise-template-overview)을 사용 하 여 봇 프레임 워크를 사용해 보는 것이 좋습니다. 직접 줄 음성을 사용 하 여 모든 봇 프레임 워크 봇에 음성을 추가할 수 있습니다.

사용자 지정 명령에 적합 한 후보에는 잘 정의 된 변수 집합이 있는 고정 된 어휘가 있습니다. 예를 들어 자동 온도 조절기 제어와 같은 홈 자동화 작업은 이상적입니다.

   ![작업 완료 시나리오의 예](media/voice-assistants/task-completion-examples.png "작업 완료 예")

## <a name="getting-started-with-custom-commands"></a>사용자 지정 명령 시작

사용자 지정 명령의 목표는 다양 한 기술을 배우고 음성 명령 앱을 개발 하는 데 집중 하는 인지 부하를 줄이는 것입니다. 사용자 지정 명령을 사용 하 여 <a href="https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesSpeechServices" target="_blank">Azure Speech 리소스 <span class="docon docon-navigate-external x-hidden-focus"></span> 를 만드는 </a>첫 번째 단계입니다. Speech Studio에서 사용자 지정 명령 앱을 작성 하 여 게시할 수 있습니다. 그 후에는 장치 응용 프로그램이 음성 SDK를 사용 하 여 통신할 수 있습니다.

#### <a name="authoring-flow-for-custom-commands"></a>사용자 지정 명령의 제작 흐름
   ![사용자 지정 명령의 제작 흐름](media/voice-assistants/custom-commands-flow.png "사용자 지정 명령 작성 흐름")

첫 번째 사용자 지정 명령 앱에서 코드를 10 분 이내에 실행 하도록 하려면 빠른 시작을 따르세요.

* [사용자 지정 명령을 사용하여 음성 도우미 만들기](quickstart-custom-commands-application.md)

빠른 시작을 완료 한 후에는 사용자 지정 명령 응용 프로그램의 디자인, 개발, 디버깅, 배포 및 통합에 대 한 자세한 단계에 대 한 방법 가이드를 살펴보세요.

## <a name="building-voice-assistants-with-custom-commands"></a>사용자 지정 명령을 사용 하 여 음성 도우미 빌드
> [!VIDEO https://www.youtube.com/embed/1zr0umHGFyc]

## <a name="next-steps"></a>다음 단계

* [평가판 Speech Service 구독 키 받기](overview.md#try-the-speech-service-for-free)
* [샘플에 대 한 GitHub의 음성 도우미 리포지토리 보기](https://aka.ms/speech/cc-samples)
* [Speech Studio로 이동 하 여 사용자 지정 명령 사용해 보기](https://speech.microsoft.com/customcommands)
* [Speech SDK 가져오기](speech-sdk.md)
