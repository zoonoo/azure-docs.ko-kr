---
title: Speech to Text 정보 | Microsoft Docs
description: Speech to Text API의 기능 개요입니다.
titleSuffix: Microsoft Cognitive Services
services: cognitive-services
author: v-jerkin
manager: noellelacharite
ms.service: cognitive-services
ms.component: speech-service
ms.topic: article
ms.date: 05/07/2018
ms.author: v-jerkin
ms.openlocfilehash: 2eee1c6f9158f128ed5ffe575f8f498f1d3eb5e9
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/23/2018
ms.locfileid: "35382913"
---
# <a name="about-the-speech-to-text-api"></a>Speech to Text API 정보

**Speech to Text**는 오디오 스트림을 응용 프로그램에서 사용자에게 표시하거나 명령 입력으로 작동할 수 있는 텍스트로 *기록*합니다. 이 API는 SDK 클라이언트 라이브러리(지원되는 플랫폼 및 언어용) 또는 REST API와 함께 사용할 수 있습니다.

**Speech to Text** API는 다음과 같은 기능을 제공합니다.

- Cortana, Office 및 기타 Microsoft 제품에서 사용하는 것과 동일한 Microsoft의 고급 음성 인식 기술

- 실시간 연속 인식. **Speech to Text**를 사용하여 오디오를 실시간으로 텍스트로 기록할 수 있습니다. 또한 지금까지 인식된 단어의 중간 결과도 수신할 수 있습니다. 이 서비스는 음성의 끝을 자동으로 인식합니다. 사용자는 대문자 표시 및 문장 부호, 욕설 마스킹 및 텍스트 정규화와 같은 추가 형식 지정 옵션을 선택할 수도 있습니다.

- 대화형, 대화 및 받아쓰기 시나리오에 최적화된 **음성 텍스트 변환** 결과. 

- 여러 언어로 나타낸 다양한 음성 언어 지원. 각 인식 모드에서 지원되는 언어의 전체 목록은 [지원되는 언어](supported-languages.md#speech-to-text)를 참조하세요.

- 사용자 지정된 언어 및 음향 모델: 응용 프로그램을 사용자가 말하는 방식, 말하는 환경 및 전문 어휘로 조정할 수 있습니다.

- 자연어 이해 LUIS([Language Understanding](https://docs.microsoft.com/azure/cognitive-services/luis/))와 통합하여 음성에서 의도 및 엔터티를 파생할 수 있습니다. 사용자는 앱 어휘를 알 필요가 없지만 원하는 내용을 자신만의 단어로 설명할 수 있습니다.

## <a name="api-capabilities"></a>API 기능

**Speech to Text** API의 일부 기능을 REST를 통해 사용할 수 없습니다. 다음 표에서는 API에 액세스하는 각 방법의 기능을 요약해서 설명합니다.

| 사용 사례 | REST (영문) | SDK |
|-----|-----|-----|----|
| 명령과 같은 짧은 발언을 기록합니다(길이 15초 미만). 임시 결과는 없음 | 예 | 예 |
| 좀 더 긴 발언을 기록합니다(15초 초과). | 아니오 | 예 |
| 선택적 중간 결과를 포함하여 스트리밍 오디오 기록 | 아니오 | 예 |
| LUIS를 통해 화자의 의도 이해 | 아니요\* | 예 |

\* *LUIS 의도 및 엔터티는 별도의 LUIS 구독을 사용하여 파생시킬 수 있습니다. 이 구독에서 SDK는 LUIS를 호출하고, 음성 기록 뿐만 아니라 엔터티 및 의도를 제공할 수 있습니다. REST API를 사용하면 LUIS 구독을 통해 LUIS를 직접 호출하고 의도 및 엔터티를 파생할 수 있습니다.*

## <a name="next-steps"></a>다음 단계

* [Speech 평가판 구독 가져오기](https://azure.microsoft.com/try/cognitive-services/)
* [C#에서 음성을 인식하는 방법 참조](quickstart-csharp-windows.md)
