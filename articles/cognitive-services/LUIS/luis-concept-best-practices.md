---
title: 모범 사례
titleSuffix: Language Understanding - Azure Cognitive Services
description: LUIS 앱의 모델에서 최상의 결과를 얻기 위해 LUIS 모범 사례를 알아봅니다.
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 02/26/2019
ms.author: diberry
ms.openlocfilehash: 9a6f9d54c52f36b8f709eacaf25d3fea31dbe516
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/13/2019
ms.locfileid: "60812921"
---
# <a name="best-practices-for-building-a-language-understanding-app-with-cognitive-services"></a>Cognitive Services로 Language Understanding 앱을 빌드하는 경우의 모범 사례
앱 작성 프로세스를 사용하여 LUIS 앱을 빌드합니다. 

* 언어 모델 빌드
* 몇 가지 학습 예제 발화를 추가합니다(의도당 10~15개).
* 게시 
* 엔드포인트에서 테스트 
* 기능 추가

앱이 [게시](luis-how-to-publish-app.md)된 후 기능 추가, 게시 및 엔드포인트에서 테스트의 작성 주기를 사용합니다. 다른 예제 발화를 추가하는 방식으로 다음 작성 주기를 시작하지 마세요. 이렇게 하면 LUIS는 실제 사용자 발화를 사용하여 모델을 학습할 수 없습니다. 

LUIS가 학습을 효율적으로 진행하려면 예제 및 엔드포인트 발화의 현재 집합이 신뢰할 수 있는 높은 예측 점수를 반환할 때까지 발화를 확장하지 마세요. [활성 학습](luis-concept-review-endpoint-utterances.md), [패턴](luis-concept-patterns.md) 및 [문구 목록](luis-concept-feature.md)을 사용하여 점수를 개선합니다. 

## <a name="do-and-dont"></a>허용 및 금지
다음 목록에는 LUIS 앱의 모범 사례가 포함되어 있습니다.

|실행 사항|금지 사항|
|--|--|
|[고유한 의도 정의](#do-define-distinct-intents) |[의도에 많은 예제 발화 추가](#dont-add-many-example-utterances-to-intents) |
|[각 의도에 너무 일반적 및 너무 구체적 사이에 안정적인 지점 찾기](#do-find-sweet-spot-for-intents)|[LUIS를 학습 플랫폼으로 사용](#dont-use-luis-as-a-training-platform)|
|[반복적으로 앱 빌드](#do-build-the-app-iteratively)|[동일한 형식의 많은 예제 발화를 추가하여 다른 형식 무시](#dont-add-many-example-utterances-of-the-same-format-ignoring-other-formats)|
|[이후 반복에서 구문 목록 및 패턴 추가](#do-add-phrase-lists-and-patterns-in-later-iterations)|[의도 및 엔터티 정의 혼합](#dont-mix-the-definition-of-intents-and-entities)|
|None 의도를 제외한 [모든 의도에서 발언의 균형 맞추기](#balance-your-utterances-across-all-intents)<br>[None 의도에 예제 발화 추가](#do-add-example-utterances-to-none-intent)|[모든 가능한 값을 사용하여 구문 목록 만들기](#dont-create-phrase-lists-with-all-the-possible-values)|
|[활성 학습의 제안 기능 활용](#do-leverage-the-suggest-feature-for-active-learning)|[너무 많은 패턴 추가](#dont-add-many-patterns)|
|[앱 성능 모니터링](#do-monitor-the-performance-of-your-app)|[추가된 모든 단일 예제를 사용하여 학습 및 게시](#dont-train-and-publish-with-every-single-example-utterance)|
|[앱 반복마다 버전 사용](#do-use-versions-for-each-app-iteration)||

## <a name="do-define-distinct-intents"></a>고유한 의도 정의
각 의도의 어휘가 해당 의도에만 사용되고 다른 의도와 겹치지 않는지 확인합니다. 예를 들어, 항공사 항공편 및 호텔과 같은 여행 계획을 처리하는 앱을 사용하려는 경우, 이러한 주제 영역을 발언 내부에 별도의 의도로 포함하거나 특정 데이터의 엔터티가 있는 동일한 의도로 포함하도록 선택할 수 있습니다.

두 의도의 어휘가 동일한 경우, 의도를 결합하고 엔터티를 사용합니다. 

다음 예제 발화를 고려해 보세요.

|예제 발화|
|--|
|항공권 예약|
|호텔 예약|

“항공편 예약” 및 “호텔 예약”에는 “예약”이라는 동일한 어휘가 사용됩니다. 이 형식은 동일하므로 항공편 및 호텔에서 추출된 엔터티의 다른 단어와 동일한 의도여야 합니다. 

추가 정보
* 개념: [LUIS 앱에서 의도에 대한 개념](luis-concept-intent.md)
* 자습서: [사용자 의도를 확인하는 LUIS 앱 빌드](luis-quickstart-intents-only.md)
* 방법: [의도를 추가하여 발화에 대한 사용자 의도 결정](luis-how-to-add-intents.md)


## <a name="do-find-sweet-spot-for-intents"></a>의도의 안정적인 지점 찾기
LUIS의 예측 데이터를 사용하여 의도가 겹치는지 확인합니다. 겹치는 의도는 LUIS에 혼동을 유발합니다. 결과는 상위 점수 의도가 다른 의도에 너무 가깝다는 것입니다. LUIS는 매번 학습할 데이터를 통해 똑같은 경로를 사용하지 않으므로 겹치는 의도는 학습에서 첫 번째 또는 두 번째가 될 수 있습니다. 이러한 플립플롭이 발생하지 않도록 각 의도의 발언 점수가 멀리 떨어지는 좋습니다. 의도를 분명히 구별하면 매번 상위 의도가 예측됩니다. 
 
## <a name="do-build-the-app-iteratively"></a>반복적으로 앱 빌드
[예제 발언](luis-concept-utterance.md) 또는 엔드포인트 발언으로 사용되지 않는 별도의 발언 세트를 유지합니다. 테스트 집합의 앱을 계속 개선합니다. 실제 사용자 발화를 반영하도록 테스트 집합을 조정합니다. 이 테스트 세트를 사용하여 앱의 반복 또는 버전을 평가합니다. 

개발자에게는 세 개의 데이터 집합이 있어야 합니다. 첫 번째는 모델을 빌드하기 위한 예제 발화입니다. 두 번째는 엔드포인트에서 모델을 테스트하는 데 사용됩니다. 세 번째는 [일괄 테스트](luis-how-to-batch-test.md)에서 사용되는 블라인드 테스트 데이터입니다. 이 마지막 세트는 애플리케이션 학습에 사용되지 않고 엔드포인트에서 전송되지 않습니다.  

추가 정보
* 개념: [LUIS 앱에 대한 주기 작성](luis-concept-app-iteration.md)

## <a name="do-add-phrase-lists-and-patterns-in-later-iterations"></a>이후 반복에서 구문 목록 및 패턴 추가

앱이 테스트되기 전에는 이러한 방법을 적용하지 않는 것이 좋습니다. 이러한 기능 예제 표현 보다 더 많은 가중치가 적용 및 신뢰도 왜곡 시킵니다 있으므로 구 목록 및 패턴을 추가 하기 전에 앱의 동작 방식 이해 해야 합니다. 

사례를 적용하지 않은 상태에서 앱이 어떻게 작동하는지 이해했으면 앱에 적용되는 각 기능을 추가합니다. 각 [반복](luis-concept-app-iteration.md)에서 이러한 기능을 추가하거나, 각 버전에서 기능을 변경할 필요가 없습니다. 

모델 디자인을 시작할 때 기능을 추가해도 아무 문제 없지만, 모델이 발화로 테스트된 후에 각 기능이 결과를 어떻게 바꾸는지 확인하는 것이 더 편리합니다. 

[활성 학습](luis-concept-review-endpoint-utterances.md)의 추가 혜택을 얻을 수 있도록 [엔드포인트](luis-get-started-create-app.md#query-the-endpoint-with-a-different-utterance)를 통해 테스트하는 것이 좋습니다. [대화형 테스트 창](luis-interactive-test.md)도 유효한 테스트 방법입니다. 
 

### <a name="phrase-lists"></a>문구 목록

[구문 목록](luis-concept-feature.md)을 사용하면 앱 도메인에 관련된 단어의 사전을 정의할 수 있습니다. 몇 가지 단어를 사용하여 구문 목록을 시드한 다음, 제안 기능을 사용하면 LUIS가 앱과 관련된 어휘에서 더 많은 단어를 인식합니다. 구 목록은 앱에 중요한 단어 또는 구와 연관된 신호를 강화하여 의도 감지 및 엔터티 분류를 향상합니다. 

구문 목록이 정확하게 일치하지 않으므로 모든 단어를 어휘에 추가하지는 마세요. 

추가 정보
* 개념: [LUIS 앱의 구문 목록 기능](luis-concept-feature.md)
* 방법: [단어 목록의 신호를 강화하는 구문 사용](luis-how-to-add-features.md)

### <a name="patterns"></a>패턴

서로 매우 비슷한 엔드포인트의 실제 사용자 발화는 단어 선택 및 배치 패턴을 표시할 수 있습니다. [패턴](luis-concept-patterns.md) 기능은 이 단어 선택 및 배치를 정규식과 함께 사용하여 예측 정확도를 개선합니다. 패턴의 정규식은 패턴을 일치시키는 동안 무시하려는 단어 및 문장 부호를 고려합니다. 

문장 부호를 무시할 수 있도록 문장 부호에 패턴의 [선택적 구문](luis-concept-patterns.md) 을 사용합니다. [명시적 목록](luis-concept-patterns.md#explicit-lists)을 사용하여 구문 문제에 대해 패턴을 보완합니다. 

추가 정보
* 개념: [패턴을 통해 예측 정확도 개선](luis-concept-patterns.md)
* 방법: [패턴을 추가하여 예측 정확도를 개선하는 방법](luis-how-to-model-intent-pattern.md)

## <a name="balance-your-utterances-across-all-intents"></a>모든 의도에서 발언의 균형 맞추기

LUIS 예측의 정확도를 높이려면 각 의도(None 의도 제외)의 예제 발화 양이 상대적으로 동등해야 합니다. 

예제 발언이 100개인 의도와 예제 발언이 20개인 의도가 있으면 발언이 100개인 의도의 예측 비율이 더 높아집니다.  

## <a name="do-add-example-utterances-to-none-intent"></a>None 의도에 예제 발화 추가

이 의도는 애플리케이션 외부의 모든 것을 나타내는 대체 의도입니다. LUIS 앱의 나머지 부분에 있는 예제 발화 10개마다 하나의 예제 발화를 None 의도에 추가합니다.

추가 정보
* 개념: [LUIS 앱에 적합한 발화가 무엇인지 이해](luis-concept-utterance.md)

## <a name="do-leverage-the-suggest-feature-for-active-learning"></a>활성 학습의 제안 기능 활용

의도에 더 많은 예제 의도를 추가하는 대신 정기적으로 [활성 학습](luis-how-to-review-endpoint-utterances.md)의 **엔드포인트 발화 검토**를 사용합니다. 앱이 지속적으로 엔드포인트 발화를 수신하기 때문에 이 목록은 계속 증가하고 변경됩니다.

추가 정보
* 개념: [엔드포인트 발화를 검토하여 활성 학습을 사용하도록 설정하는 것과 관련된 개념입니다.](luis-concept-review-endpoint-utterances.md)
* 자습서: [자습서: 엔드포인트 발화를 검토하여 알 수 없는 예측 수정](luis-tutorial-review-endpoint-utterances.md)
* 방법: [LUIS 포털에서 엔드포인트 발화 검토 방법](luis-how-to-review-endpoint-utterances.md)

## <a name="do-monitor-the-performance-of-your-app"></a>앱 성능 모니터링

[일괄 테스트](luis-concept-batch-test.md) 세트를 사용하여 예측 정확도를 모니터링합니다. 

## <a name="dont-add-many-example-utterances-to-intents"></a>의도에 많은 예제 발화 추가 안 함

앱이 게시된 후 반복 프로세스에서 활성 학습의 발화만 추가합니다. 발화가 너무 비슷한 경우, 패턴을 추가합니다. 

## <a name="dont-use-luis-as-a-training-platform"></a>LUIS를 학습 플랫폼으로 사용 안 함

LUIS는 언어 모델의 도메인에 관련됩니다. 일반 자연어 학습 플랫폼으로 작동하지 않습니다. 

## <a name="dont-add-many-example-utterances-of-the-same-format-ignoring-other-formats"></a>동일한 형식의 많은 예제 발화를 추가하여 다른 형식 무시 안 함

LUIS는 의도의 발화에서 변형을 예측합니다. 전체 의미는 동일하지만 발화가 달라질 수 있습니다. 변형에는 발화 길이, 단어 선택 및 단어 배치가 포함될 수 있습니다. 

|같은 형식 사용 안 함|다양한 형식 사용|
|--|--|
|Buy a ticket to Seattle<br>파리행 항공권 구입<br>올랜도행 항공권 구입|시애틀행 항공권 1매 구입<br>다음 월요일에 파리행 야간 항공편에서 2석 예약<br>봄 방학을 위해 올란도행 항공권 3매를 예약|

두 번째 열에는 여러 가지 동사(buy, reserve, book), 여러 가지 수량(1, two, 3), 여러 가지 단어 배열이 사용되지만 모두 여행을 위해 항공사 티켓을 구매하려는 동의할 의도를 포함합니다. 

## <a name="dont-mix-the-definition-of-intents-and-entities"></a>의도 및 엔터티 정의 혼합 안 함

봇이 수행할 작업의 의도를 만듭니다. 작업을 가능하게 하는 매개 변수로 엔터티를 사용합니다. 

항공사 항공편을 예약할 챗봇의 경우 **BookFlight** 의도를 만듭니다. 모든 항공사 또는 모든 목적지의 의도는 만들지 마세요. 이러한 데이터 조각을 [엔터티](luis-concept-entity-types.md)로 사용하고 예제 발화에서 이러한 데이터를 표시합니다. 

## <a name="dont-create-phrase-lists-with-all-the-possible-values"></a>모든 가능한 값을 사용하여 구문 목록 만들지 않음

[구문 목록](luis-concept-feature.md)에 몇 가지 예제를 제공하지만 모든 단어를 제공하지는 않습니다. LUIS는 컨텍스트를 일반화하고 고려합니다. 

## <a name="dont-add-many-patterns"></a>너무 많은 패턴 추가 안 함

[패턴](luis-concept-patterns.md)을 너무 많이 추가하지 마세요. LUIS는 더 적은 예제를 사용하여 빠르게 학습합니다. 시스템을 불필요하게 오버로드하지 마세요.

## <a name="dont-train-and-publish-with-every-single-example-utterance"></a>모든 단일 예제를 사용하여 학습 및 게시 안 함

학습 및 게시 전에 10개 또는 15개의 발화를 추가합니다. 이렇게 하면 예측 정확도에 미치는 영향을 확인할 수 있습니다. 단일 발화를 추가하면 점수에 분명한 영향을 주지 않습니다. 

## <a name="do-use-versions-for-each-app-iteration"></a>앱 반복마다 버전 사용

각각의 작성 주기는 기존 버전에서 복제된 새 [버전](luis-concept-version.md) 내에 있어야 합니다. LUIS는 버전에 대한 제한이 없습니다. 버전 이름은 API 경로의 일부로 사용되기 때문에 URL에 허용되는 문자를 사용하고 버전의 문자를 10자 이내로 유지하는 것이 중요합니다. 버전이 잘 정리될 수 있도록 버전 이름 전략을 개발하십시오. 

추가 정보
* 개념: [LUIS 버전을 사용하는 방법 및 시기 이해](luis-concept-version.md)
* 방법: [스테이징 또는 프로덕션 앱에 영향을 주지 않고 버전을 사용하여 편집 및 테스트](luis-how-to-manage-versions.md)


## <a name="next-steps"></a>다음 단계

* LUIS 앱에서 [앱을 계획](luis-how-plan-your-app.md)하는 방법을 알아봅니다.
