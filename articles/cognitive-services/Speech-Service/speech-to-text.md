---
title: Speech to Text 정보
description: Speech to Text API의 기능 개요입니다.
titleSuffix: Microsoft Cognitive Services
services: cognitive-services
author: v-jerkin
ms.service: cognitive-services
ms.component: speech-service
ms.topic: article
ms.date: 05/07/2018
ms.author: v-jerkin
ms.openlocfilehash: ba6710c8b5b8de1c63fa6778ea3853ab52365254
ms.sourcegitcommit: 7ad9db3d5f5fd35cfaa9f0735e8c0187b9c32ab1
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/27/2018
ms.locfileid: "39325339"
---
# <a name="about-the-speech-to-text-api"></a>Speech to Text API 정보

**Speech to Text**는 오디오 스트림을 응용 프로그램에서 사용자에게 표시하거나 명령 입력으로 작동할 수 있는 텍스트로 *기록*합니다. 이 API는 SDK 클라이언트 라이브러리(지원되는 플랫폼 및 언어용) 또는 REST API와 함께 사용할 수 있습니다.

**Speech to Text** API는 다음과 같은 기능을 제공합니다.

- Cortana, Office 및 기타 Microsoft 제품에서 사용하는 것과 동일한 Microsoft의 고급 음성 인식 기술

- 실시간 연속 인식. **Speech to Text**를 사용하여 오디오를 실시간으로 텍스트로 기록할 수 있습니다. 또한 지금까지 인식된 단어의 중간 결과도 수신할 수 있습니다. 이 서비스는 음성의 끝을 자동으로 인식합니다. 사용자는 대문자 표시 및 문장 부호, 욕설 마스킹 및 역 텍스트 정규화와 같은 추가 형식 지정 옵션을 선택할 수도 있습니다.

- 대화형, 대화 및 받아쓰기 시나리오에 최적화된 **음성 텍스트 변환** 결과. 

- 여러 음성 언어 및 언어 지원. 각 인식 모드에서 지원되는 언어의 전체 목록은 [지원되는 언어](supported-languages.md#speech-to-text)를 참조하세요.

- 사용자 지정된 언어 및 음향 모델: 응용 프로그램을 사용자의 전문 영역 어휘, 말하는 환경 및 말하는 방식에 맞게 조정할 수 있습니다.

- 자연어 이해 LUIS([Language Understanding](https://docs.microsoft.com/azure/cognitive-services/luis/))와 통합하여 음성에서 의도 및 엔터티를 파생할 수 있습니다. 사용자는 앱 어휘를 알 필요가 없지만 원하는 내용을 자신만의 단어로 설명할 수 있습니다.

## <a name="api-capabilities"></a>API 기능

**Speech to Text** API의 일부 기능을 REST를 통해 사용할 수 없습니다. 다음 표에서는 API에 액세스하는 각 방법의 기능을 요약해서 설명합니다.

| 사용 사례 | REST (영문) | SDK |
|-----|-----|-----|----|
| 명령과 같은 짧은 발언을 기록합니다(길이 15초 미만). 임시 결과는 없음 | yes | yes |
| 좀 더 긴 발언을 기록합니다(15초 초과). | 아니요 | yes |
| 선택적 중간 결과를 포함하여 스트리밍 오디오 기록 | 아니요 | yes |
| LUIS를 통해 화자의 의도 이해 | 아니요\* | yes |

\* *LUIS 의도 및 엔터티는 별도의 LUIS 구독을 사용하여 파생시킬 수 있습니다. 이 구독에서 SDK는 LUIS를 호출하고, 음성 기록 뿐만 아니라 엔터티 및 의도를 제공할 수 있습니다. REST API를 사용하면 LUIS 구독을 통해 LUIS를 직접 호출하고 의도 및 엔터티를 파생할 수 있습니다.*

## <a name="next-steps"></a>다음 단계

* [음성 평가판 구독 가져오기](https://azure.microsoft.com/try/cognitive-services/)
* [빠른 시작: C#에서 음성 인식](quickstart-csharp-dotnet-windows.md)
* [C#에서 음성 의도를 인식하는 방법 알아보기](how-to-recognize-intents-from-speech-csharp.md)
