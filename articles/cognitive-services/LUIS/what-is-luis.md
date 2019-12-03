---
title: LUIS(Language Understanding)란?
titleSuffix: Azure Cognitive Services
description: LUIS(Language Understanding)는 사용자 지정 기계 학습 인텔리전스를 사용자의 자연스러운 기존 언어 텍스트에 적용하여 전체적인 의미를 예측하고 관련된 자세한 정보를 추출하는 클라우드 기반 API 서비스입니다.
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: overview
ms.date: 11/22/2019
ms.author: diberry
ms.openlocfilehash: 99f312521727658788e96a57b619a7c0e3d4751b
ms.sourcegitcommit: 12d902e78d6617f7e78c062bd9d47564b5ff2208
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/24/2019
ms.locfileid: "74456561"
---
# <a name="what-is-language-understanding-luis"></a>LUIS(Language Understanding)란?

LUIS(Language Understanding)는 사용자 지정 기계 학습 인텔리전스를 자연어 텍스트에 적용하여 전체적인 의미를 예측하고 관련된 자세한 정보를 추출하는 클라우드 기반 API 서비스입니다. 

예를 들어 클라이언트 애플리케이션에서 `find me a wireless keyboard for $30` 텍스트를 보내면 LUIS는 다음 JSON 개체를 사용하여 응답합니다. 

```JSON
{
    "query": "find me a wireless keyboard for $30",
    "prediction": {
        "topIntent": "Finditem",
        "intents": {
            "Finditem": {
                "score": 0.934672
            }
        },
        "entities": {
            "item": [
                "wireless keyboard"
            ],
            "money": [
        {
            "number": 30,
            "units": "Dollar"
        }
           ]
        }
        
    }
}
```
위의 예제에서 _**의도**_ 또는 구의 전체적인 의미는 사용자가 항목을 찾으려고 시도한다는 것입니다. LUIS가 추출하는 세부 정보를 _**엔터티**_ 라고 합니다. 이 예제의 엔터티는 사용자가 찾고 있는 항목의 이름과 사용자가 지출하려는 금액입니다.

클라이언트 애플리케이션은 LUIS에서 반환한 JSON, _의도_(범주) 및 _엔터티_(추출된 세부 정보)를 사용하여 클라이언트 애플리케이션에서 작업을 구동합니다. LUIS용 클라이언트 애플리케이션은 자연어로 사용자와 통신하여 작업을 완료하는 대화형 애플리케이션인 경우가 많습니다. 클라이언트 애플리케이션의 예로는 소셜 미디어 앱, 챗봇 및 음성 지원 데스크톱 애플리케이션을 들 수 있습니다. 

![Cognitive Services LUIS(Language Understanding)를 사용하는 3개의 클라이언트 애플리케이션의 개념 이미지](./media/luis-overview/luis-entry-point.png "Cognitive Services LUIS(Language Understanding)를 사용하는 3개의 클라이언트 애플리케이션의 개념 이미지")

## <a name="example-use-luis-in-a-chat-bot"></a>채팅 봇에서 LUIS 사용 예제

<a name="Accessing-LUIS"></a>

클라이언트 애플리케이션은 게시된 LUIS 자연어 처리 엔드포인트 [API][endpoint-apis]에 발화(텍스트)를 전송하고, 결과를 JSON 응답으로 수신합니다. LUIS에 사용되는 일반 클라이언트 애플리케이션은 챗봇입니다.


![채팅 봇을 사용하여 NLP(Natural Language Understanding)로 사용자 텍스트를 예측하는 LUIS의 개념 이미지](./media/luis-overview/LUIS-chat-bot-request-response.svg "채팅 봇을 사용하여 NLP(Natural Language Understanding)로 사용자 텍스트를 예측하는 LUIS의 개념 이미지")

|단계|조치|
|:--|:--|
|1|클라이언트 애플리케이션이 사용자 _발언_(사용자가 한 말의 텍스트) "I want to call my HR rep."를 LUIS 엔드포인트에 HTTP 요청으로 보냅니다.|
|2|LUIS는 기계 학습된 언어 모델을 사용자의 비정형 입력 텍스트에 적용하고, 최상위 의도인 `HRContact`와 함께 JSON 형식의 응답을 반환합니다. 최소 JSON 엔드포인트 응답에는 최소한 쿼리 발언 및 최상위 채점 의도가 포함됩니다. _연락처 유형_ 엔터티와 같은 데이터를 추출할 수도 있습니다.|
|3|클라이언트 애플리케이션은 JSON 응답을 사용하여 사용자의 요청을 처리하는 방법에 대한 결정을 내립니다. 이러한 의사 결정에는 봇의 의사 결정 트리와 기타 서비스 호출이 포함될 수 있습니다. |

LUIS 앱은 클라이언트 애플리케이션이 현명한 선택을 내릴 수 있도록 인텔리전스를 제공합니다. LUIS는 이러한 선택 항목을 제공하지 않습니다. 

<a name="Key-LUIS-concepts"></a>
<a name="what-is-a-luis-model"></a>

## <a name="natural-language-processing"></a>자연어 처리

LUIS 앱은 도메인별 자연어 모델을 포함하고 있으며, 이러한 모델은 함께 작동합니다. 미리 작성된 모델을 하나 이상 사용하여 LUIS 앱을 시작할 수도 있고, 모델을 직접 빌드할 수도 있고, 미리 작성된 모델과 사용자 지정 정보를 혼합해서 사용할 수도 있습니다.

* **미리 빌드된 모델** LUIS는 여러 도메인이 미리 빌드되어 있으며, 이러한 도메인에는 함께 작동하여 일반적인 사용 시나리오를 완성하는 의도와 엔터티 모델이 들어 있습니다. 이러한 도메인에는 레이블이 지정된 발화가 포함되어 있는데, 이 발화를 검사하고 편집하고 사용자 지정할 수 있습니다. [미리 작성된 도메인 모델](luis-how-to-use-prebuilt-domains.md)에는 모든 디자인이 포함되며 LUIS를 빠르게 시작하는 유용한 방법입니다. 또한 통화나 숫자처럼 미리 빌드된 도메인과 독립적으로 사용할 수 있는 엔터티가 미리 빌드되어 있습니다.

* **사용자 지정 모델** LUIS는 의도와 엔터티를 포함한 사용자 지정 모델을 빌드하는 여러 가지 방법을 제공합니다. 엔터티에는 기계 학습 엔터티, 패턴 매칭 엔터티, 기계 학습과 패턴 매칭의 조합이 포함됩니다.

## <a name="build-the-luis-app"></a>LUIS 앱 빌드
[작성](https://go.microsoft.com/fwlink/?linkid=2092087) API 또는 [LUIS 포털](https://www.luis.ai)을 사용하여 앱을 빌드합니다.

LUIS 앱은 **[의도](luis-concept-intent.md)** 라는 입력 텍스트 범주로 시작합니다. 각 의도에는 사용자 **[발언](luis-concept-utterance.md)** 예제가 필요합니다. 각 발화는 추출해야 하는 다양한 데이터를 제공할 수 있습니다. 

|예제 사용자 발언|Intent|추출된 데이터|
|-----------|-----------|-----------|
|`Book a flight to __Seattle__?`|BookFlight|시애틀|
|`When does your store __open__?`|StoreHoursAndLocation|open|
|`Schedule a meeting at __1pm__ with __Bob__ in Distribution`|ScheduleMeeting|1pm, Bob|

## <a name="query-prediction-endpoint"></a>예측 엔드포인트 쿼리

앱을 학습하여 엔드포인트에 게시하면 클라이언트 애플리케이션은 예측 [엔드포인트](https://go.microsoft.com/fwlink/?linkid=2092356) API에 발화를 보냅니다. API는 분석을 위해 발화에 앱을 적용하고 JSON 형식의 예측 결과로 응답합니다.  

최소 JSON 엔드포인트 응답에는 최소한 쿼리 발언 및 최상위 채점 의도가 포함됩니다. 다음 **연락처 유형** 엔터티와 전반적인 감정 등의 데이터를 추출할 수도 있습니다. 

```JSON
{
    "query": "I want to call my HR rep",
    "prediction": {
        "normalizedQuery": "i want to call my hr rep",
        "topIntent": "HRContact",
        "intents": {
            "HRContact": {
                "score": 0.8582669
            }
        },
        "entities": {
            "Contact Type": [
                "call"
            ]
        },
        "sentiment": {
            "label": "negative",
            "score": 0.103343368
        }
    }
}
```

## <a name="improve-model-prediction"></a>모델 예측 개선

LUIS 앱이 게시되고 실제 사용자 발화를 받은 후 LUIS가 엔드포인트 발화의 [활성 학습](luis-concept-review-endpoint-utterances.md)을 제공하여 예측 정확도를 높입니다. 

<a name="using-luis"></a>

## <a name="iterative-development-lifecycle"></a>반복 개발 수명 주기
LUIS는 완전한 반복 [개발 수명 주기](luis-concept-app-iteration.md)에 통합할 수 있는 도구, 버전 관리 및 다른 LUIS 작성자와의 협업을 제공합니다. 

## <a name="implementing-luis"></a>LUIS 구현
LUIS(Language Understanding)는 REST API로서 HTTP 요청과 함께 모든 제품, 서비스 또는 프레임워크에 사용할 수 있습니다. 다음 목록은 LUIS에 가장 많이 사용되는 Microsoft 제품 및 서비스입니다.

LUIS에 대한 상위 클라이언트 애플리케이션은 다음과 같습니다.
* [웹앱 봇](https://docs.microsoft.com/azure/bot-service/?view=azure-bot-service-4.0)은 신속하게 LUIS 지원 챗봇을 만들어서 텍스트 입력을 통해 사용자와 대화합니다. 완전한 봇 환경을 구축하기 위해 [Bot Framework][bot-framework] 버전 [4.x](https://github.com/Microsoft/botbuilder-dotnet)를 사용합니다.

봇에서 LUIS를 빠르고 쉽게 사용할 수 있는 도구는 다음과 같습니다.
* [LUIS CLI](https://github.com/Microsoft/botbuilder-tools/tree/master/packages/LUIS) - NPM 패키지에서 독립 실행형 명령줄 도구 또는 가져오기를 사용하여 작성 및 자동 완성을 제공합니다. 
* [LUISGen](https://github.com/Microsoft/botbuilder-tools/tree/master/packages/LUISGen) - LUISGen은 내보낸 LUIS 모델에서 강력한 형식의 C# 및 TypeScript 소스 코드를 생성하는 도구입니다.
* [디스패치](https://aka.ms/dispatch-tool)는 디스패처 모델을 사용하는 부모 앱에서 여러 LUIS 및 QnA Maker 앱을 사용할 수 있도록 허용합니다.
* [LUDown](https://github.com/Microsoft/botbuilder-tools/tree/master/packages/Ludown) - LUDown은 봇용 언어 모델을 관리하는 데 유용한 명령줄 도구입니다.
* [봇 프레임워크 - 작성기](https://github.com/microsoft/BotFramework-Composer) - 개발자 및 멀티 징계 팀이 Microsoft Bot Framework를 사용하여 봇 및 대화형 환경을 빌드하는 통합 개발 도구입니다.

LUIS에 사용되는 다른 Cognitive Services:
* [QnA Maker][qnamaker]에서는 몇 가지 유형의 텍스트를 질문 및 답변 기술 자료로 결합할 수 있습니다.
* [음성 서비스](../Speech-Service/overview.md)는 음성 언어 요청을 텍스트로 변환합니다. 
* [대화 학습자](https://docs.microsoft.com/azure/cognitive-services/labs/conversation-learner/overview)를 사용하면 LUIS를 사용하여 보다 빠르게 봇 대화를 빌드할 수 있습니다.

LUIS를 사용하는 샘플:
* [대화형 AI](https://github.com/Microsoft/AI) GitHub 리포지토리.
* [Bot Framework - 봇 예시](https://github.com/microsoft/BotBuilder-Samples)

## <a name="next-steps"></a>다음 단계

* [새로운 기능](whats-new.md)
* [미리 작성된](luis-get-started-create-app.md) 또는 [사용자 지정](luis-quickstart-intents-only.md) 도메인으로 새 LUIS 앱을 작성합니다.
* 공용 IoT 앱의 [예측 엔드포인트를 쿼리합니다](luis-get-started-get-intent-from-browser.md). 
* LUIS의 [개발자 리소스](developer-reference-resource.md). 

[bot-framework]: https://docs.microsoft.com/bot-framework/
[flow]: https://docs.microsoft.com/connectors/luis/
[authoring-apis]: https://go.microsoft.com/fwlink/?linkid=2092087
[endpoint-apis]: https://go.microsoft.com/fwlink/?linkid=2092356
[qnamaker]: https://qnamaker.ai/
