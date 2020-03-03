---
title: LUIS(Language Understanding)란?
description: LUIS(Language Understanding)는 사용자 지정 기계 학습 인텔리전스를 사용자의 자연스러운 기존 언어 텍스트에 적용하여 전체적인 의미를 예측하고 관련된 자세한 정보를 추출하는 클라우드 기반 API 서비스입니다.
ms.topic: overview
ms.date: 02/23/2020
ms.openlocfilehash: c74b842d27146683ef3168a534b9a012ae48fcad
ms.sourcegitcommit: 7f929a025ba0b26bf64a367eb6b1ada4042e72ed
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/25/2020
ms.locfileid: "77587042"
---
# <a name="what-is-language-understanding-luis"></a>LUIS(Language Understanding)란?

LUIS(Language Understanding)는 사용자 지정 기계 학습 인텔리전스를 사용자의 자연스러운 기존 언어 텍스트에 적용하여 전체적인 의미를 예측하고 관련된 자세한 정보를 추출하는 클라우드 기반 API 서비스입니다.

LUIS용 클라이언트 애플리케이션은 자연어로 사용자와 통신하여 작업을 완료하는 대화형 애플리케이션입니다. 클라이언트 애플리케이션의 예로는 소셜 미디어 앱, 챗봇 및 음성 지원 데스크톱 애플리케이션을 들 수 있습니다.

![Cognitive Services LUIS(Language Understanding)를 사용하는 3개의 클라이언트 애플리케이션의 개념 이미지](./media/luis-overview/luis-entry-point.png "Cognitive Services LUIS(Language Understanding)를 사용하는 3개의 클라이언트 애플리케이션의 개념 이미지")

## <a name="use-luis-in-a-chat-bot"></a>채팅 봇에서 LUIS 사용

<a name="Accessing-LUIS"></a>

LUIS 앱이 게시되면 클라이언트 애플리케이션은 LUIS 자연어 처리 엔드포인트 [API][endpoint-apis]에 발언을 전송하고, 결과를 JSON 응답으로 수신합니다. LUIS에 사용되는 일반 클라이언트 애플리케이션은 챗봇입니다.


![채팅 봇을 사용하여 NLP(Natural Language Understanding)로 사용자 텍스트를 예측하는 LUIS의 개념 이미지](./media/luis-overview/LUIS-chat-bot-request-response.svg "채팅 봇을 사용하여 NLP(Natural Language Understanding)로 사용자 텍스트를 예측하는 LUIS의 개념 이미지")

|단계|작업|
|:--|:--|
|1|클라이언트 애플리케이션이 사용자 _발언_(사용자가 한 말의 텍스트) "I want to call my HR rep."를 LUIS 엔드포인트에 HTTP 요청으로 보냅니다.|
|2|LUIS를 사용하면 사용자 지정 언어 모델을 만들어서 애플리케이션에 인텔리전스를 추가합니다. 기계 학습 언어 모델은 사용자의 비정형 입력 텍스트를 가져와서 최상위 의도인 `HRContact`와 함께 JSON 형식의 응답을 반환합니다. 최소 JSON 엔드포인트 응답에는 최소한 쿼리 발언 및 최상위 채점 의도가 포함됩니다. _연락처 유형_ 엔터티와 같은 데이터를 추출할 수도 있습니다.|
|3|클라이언트 애플리케이션은 JSON 응답을 사용하여 사용자의 요청을 처리하는 방법에 대한 결정을 내립니다. 이러한 의사 결정에는 봇 프레임워크 코드의 의사 결정 트리 및 기타 서비스에 대한 호출이 포함될 수 있습니다. |

LUIS 앱은 클라이언트 애플리케이션이 현명한 선택을 내릴 수 있도록 인텔리전스를 제공합니다. LUIS는 이러한 선택 항목을 제공하지 않습니다.

<a name="Key-LUIS-concepts"></a>
<a name="what-is-a-luis-model"></a>

## <a name="natural-language-processing"></a>자연어 처리

LUIS 앱은 도메인별 자연어 모델을 포함합니다. 미리 작성된 모델로 LUIS 앱을 시작할 수도 있고, 직접 작성할 수도 있고, 미리 작성된 도메인 조각을 사용자 고유의 정보와 혼합할 수도 있습니다.

* **미리 작성된 모델** LUIS에는 의도, 발언 및 미리 작성된 엔터티를 비롯하여 미리 작성된 여러 도메인 모델이 포함되어 있습니다. 미리 작성된 모델의 의도 및 발언을 사용하지 않고도 미리 작성된 엔터티를 사용할 수 있습니다. [미리 작성된 도메인 모델](luis-how-to-use-prebuilt-domains.md)에는 모든 디자인이 포함되며 LUIS를 빠르게 시작하는 유용한 방법입니다.

* **사용자 지정 모델** LUIS는 의도와 엔터티를 포함한 사용자 지정 모델을 식별하는 여러 가지 방법을 제공합니다. 엔터티에는 기계 학습 엔터티, 특정 또는 리터럴 엔터티 및 기계 학습과 리터럴의 조합이 포함됩니다.

## <a name="build-the-luis-model"></a>LUIS 모델 작성
[작성](https://go.microsoft.com/fwlink/?linkid=2092087) API 또는 [LUIS 포털](https://www.luis.ai)을 사용하여 모델을 작성합니다.

LUIS 모델은 **[의도](luis-concept-intent.md)** 라고 하는 사용자 의도 범주로 시작합니다. 각 의도에는 사용자 **[발언](luis-concept-utterance.md)** 예제가 필요합니다. 각 발화는 추출해야 하는 다양한 데이터를 제공할 수 있습니다.

|예제 사용자 발언|Intent|추출된 데이터|
|-----------|-----------|-----------|
|`Book a flight to __Seattle__?`|BookFlight|Seattle|
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

## <a name="development-lifecycle"></a>개발 수명 주기
LUIS는 전체 [개발 수명 주기](luis-concept-app-iteration.md)에 통합될 수 있는 도구, 버전 관리 및 다른 LUIS 작성자와의 협업을 제공합니다.

## <a name="implementing-luis"></a>LUIS 구현
LUIS(Language Understanding)는 REST API로서 HTTP 요청과 함께 모든 제품, 서비스 또는 프레임워크에 사용할 수 있습니다. 다음 목록은 LUIS에 가장 많이 사용되는 Microsoft 제품 및 서비스입니다.

LUIS에 대한 상위 클라이언트 애플리케이션은 다음과 같습니다.
* [웹앱 봇](https://docs.microsoft.com/azure/bot-service/?view=azure-bot-service-4.0)은 신속하게 LUIS 지원 챗봇을 만들어서 텍스트 입력을 통해 사용자와 대화합니다. 완전한 봇 환경을 구축하기 위해 [Bot Framework][bot-framework] 버전 [4.x](https://github.com/Microsoft/botbuilder-dotnet)를 사용합니다.

봇에서 LUIS를 빠르고 쉽게 사용할 수 있는 도구는 다음과 같습니다.
* [LUIS CLI](https://github.com/Microsoft/botbuilder-tools/tree/master/packages/LUIS) - NPM 패키지에서 독립 실행형 명령줄 도구 또는 가져오기를 사용하여 작성 및 자동 완성을 제공합니다.
* [LUISGen](https://github.com/Microsoft/botbuilder-tools/tree/master/packages/LUISGen) - LUISGen은 내보낸 LUIS 모델에서 강력한 형식의 C# 및 TypeScript 소스 코드를 생성하는 도구입니다.
* [디스패치](https://aka.ms/dispatch-tool)는 디스패처 모델을 사용하는 부모 앱에서 여러 LUIS 및 QnA Maker 앱을 사용할 수 있도록 허용합니다.
* [LUDown](https://github.com/Microsoft/botbuilder-tools/tree/master/packages/Ludown) - 봇용 언어 모델을 관리하는 데 도움이 되는 명령줄 도구입니다.
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