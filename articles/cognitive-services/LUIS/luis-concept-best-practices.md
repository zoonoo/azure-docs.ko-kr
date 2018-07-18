---
title: LUIS 모범 사례 이해 - Azure | Microsoft Docs
description: 최상의 결과를 얻기 위해 LUIS 모범 사례를 알아봅니다.
services: cognitive-services
author: v-geberr
manager: kaiqb
ms.service: cognitive-services
ms.component: language-understanding
ms.topic: article
ms.date: 06/08/2018
ms.author: v-geberr;
ms.openlocfilehash: 6c4e4c7acb5083e758aa7c6b94e2464a85ae8e9e
ms.sourcegitcommit: a1e1b5c15cfd7a38192d63ab8ee3c2c55a42f59c
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/10/2018
ms.locfileid: "37951116"
---
# <a name="best-practices"></a>모범 사례
앱 작성 프로세스를 사용하여 LUIS 앱을 빌드합니다. 

* 언어 모델 빌드
* 몇 가지 학습 예제 발화를 추가합니다(의도당 10~15개).
* 게시 
* 끝점에서 테스트 
* 기능 추가

앱이 [게시](luis-how-to-publish-app.md)된 후 기능 추가, 게시 및 끝점에서 테스트의 작성 주기를 사용합니다. 다른 예제 발화를 추가하는 방식으로 다음 작성 주기를 시작하지 마세요. 이렇게 하면 LUIS는 실제 사용자 발화를 사용하여 모델을 학습할 수 없습니다. 

LUIS가 학습을 효율적으로 진행하려면 예제 및 끝점 발화의 현재 집합이 신뢰할 수 있는 높은 예측 점수를 반환할 때까지 발화를 확장하지 마세요. 활성 학습, [패턴](luis-concept-patterns.md) 및 [구문 목록](luis-concept-feature.md)을 사용하여 점수를 개선합니다. 

## <a name="do-and-dont"></a>허용 및 금지
다음 목록에는 LUIS 앱의 모범 사례가 포함되어 있습니다.

|실행 사항|금지 사항|
|--|--|
|[고유한 의도 정의](#do-define-distinct-intents) |[의도에 많은 예제 발화 추가](#dont-add-many-example-utterances-to-intents) |
|[각 의도에 너무 일반적 및 너무 구체적 사이에 안정적인 지점 찾기](#do-find-sweet-spot-for-intents)|[LUIS를 학습 플랫폼으로 사용](#dont-use-luis-as-a-training-platform)|
|[반복적으로 앱 빌드](#do-build-the-app-iteratively)|[동일한 형식의 많은 예제 발화를 추가하여 다른 형식 무시](#dont-add-many-example-utterances-of-the-same-format-ignoring-other-formats)|
|[이후 반복에서 구문 목록 및 패턴 추가](#do-add-phrase-lists-and-patterns-in-later-iterations)|[의도 및 엔터티 정의 혼합](#dont-mix-the-definition-of-intents-and-entities)|
|[None 의도에 예제 발화 추가](#do-add-example-utterances-to-none-intent)|[모든 가능한 값을 사용하여 구문 목록 만들기](#dont-create-phrase-lists-with-all-the-possible-values)|
|[활성 학습의 제안 기능 활용](#do-leverage-the-suggest-feature-for-active-learning)|[너무 많은 패턴 추가](#dont-add-many-patterns)|
|[앱 성능 모니터링](#do-monitor-the-performance-of-your-app)|[추가된 모든 단일 예제를 사용하여 학습 및 게시](#dont-train-and-publish-with-every-single-example-utterance)|

## <a name="do-define-distinct-intents"></a>고유한 의도 정의
각 의도의 어휘가 해당 의도에만 사용되고 다른 의도와 겹치지 않는지 확인합니다. 예를 들어, 항공사 항공편 및 호텔과 같은 여행 계획을 처리하는 앱을 사용하려는 경우, 이러한 항목을 발화 내부에 개별 의도로 포함하거나 특정 데이터의 엔터티가 있는 동일한 의도로 포함하도록 선택할 수 있습니다.

두 의도의 어휘가 동일한 경우, 의도를 결합하고 엔터티를 사용합니다. 

다음 예제 발화를 고려해 보세요.

```
Book a flight
Book a hotel
```

“항공편 예약” 및 “호텔 예약”에는 “예약”이라는 동일한 어휘가 사용됩니다. 이는 겹치므로 항공편 및 호텔에서 추출된 엔터티의 서로 다른 단어가 포함된 동일한 의도여야 합니다. 

## <a name="do-find-sweet-spot-for-intents"></a>의도의 안정적인 지점 찾기
LUIS의 예측 데이터를 사용하여 의도가 겹치는지 확인합니다. 겹치는 의도가 있으면 LUIS가 제대로 구별하지 못할 수 있습니다. 결과는 상위 점수 의도가 다른 의도에 너무 가깝다는 것입니다. LUIS는 매번 학습할 데이터를 통해 똑같은 경로를 사용하지 않으므로 겹치는 의도는 학습에서 첫 번째 또는 두 번째가 될 수 있습니다. 이 문제가 발생하지 않도록 각 의도의 발화 점수를 멀리 떨어지게 합니다. 의도를 분명히 구별하면 매번 상위 의도가 예측됩니다. 
 
## <a name="do-build-the-app-iteratively"></a>반복적으로 앱 빌드
[예제 발화](luis-concept-utterance.md) 또는 끝점 발화로 사용되지 않는 별도의 ‘블라인드’ 테스트 집합을 유지합니다. 테스트 집합의 앱을 계속 개선합니다. 실제 사용자 발화를 반영하도록 테스트 집합을 조정합니다. 이 블라인드 테스트 집합을 사용하여 각 반복을 평가합니다. 

개발자에게는 세 개의 데이터 집합이 있어야 합니다. 첫 번째는 모델을 빌드하기 위한 예제 발화입니다. 두 번째는 끝점에서 모델을 테스트하는 데 사용됩니다. 세 번째는 [일괄 테스트](luis-how-to-batch-test.md)에서 사용되는 블라인드 테스트 데이터입니다. 이 마지막 집합은 응용 프로그램 학습에 사용되지 않고 끝점에서 전송되지 않습니다.  

## <a name="do-add-phrase-lists-and-patterns-in-later-iterations"></a>이후 반복에서 구문 목록 및 패턴 추가
[구문 목록](luis-concept-feature.md)을 사용하면 앱 도메인에 관련된 단어의 사전을 정의할 수 있습니다. 몇 가지 단어를 사용하여 구문 목록을 시드한 다음, 제안 기능을 사용면 LUIS가 어휘에서 더 많은 단어를 인식합니다. 구문 목록이 정확하게 일치하지 않으므로 모든 단어를 어휘에 추가하지는 마세요. 

서로 매우 비슷한 끝점의 실제 사용자 발화는 단어 선택 및 배치 패턴을 표시할 수 있습니다. [패턴](luis-concept-patterns.md) 기능은 이 단어 선택 및 배치를 정규식과 함께 사용하여 예측 정확도를 개선합니다. 패턴의 정규식은 패턴을 일치시키는 동안 무시하려는 단어 및 문장 부호를 고려합니다. 

문장 부호를 무시할 수 있도록 문장 부호에 패턴의 선택적 구문을 사용합니다.

신뢰도가 왜곡되므로 앱이 끝점 요청을 수신하기 전에 이러한 사례를 적용하지 마세요.  

## <a name="do-add-example-utterances-to-none-intent"></a>None 의도에 예제 발화 추가
이는 응용 프로그램 외부의 모든 것을 나타내는 대체 의도입니다. LUIS 앱의 나머지 부분에 있는 예제 발화 10개마다 하나의 예제 발화를 None 의도에 추가합니다.

## <a name="do-leverage-the-suggest-feature-for-active-learning"></a>활성 학습의 제안 기능 활용
의도에 더 많은 예제 의도를 추가하는 대신 정기적으로 [활성 학습](luis-how-to-review-endoint-utt.md)의 **끝점 발화 검토**를 사용합니다. 앱이 지속적으로 끝점 발화를 수신하기 때문에 이 목록은 계속 증가하고 변경됩니다.

## <a name="do-monitor-the-performance-of-your-app"></a>앱 성능 모니터링
테스트 집합을 사용하여 예측 정확도를 모니터링합니다. 

## <a name="dont-add-many-example-utterances-to-intents"></a>의도에 많은 예제 발화 추가 안 함
앱이 게시된 후 반복 프로세스에서 활성 학습의 발화만 추가합니다. 발화가 너무 비슷한 경우, 패턴을 추가합니다. 

## <a name="dont-use-luis-as-a-training-platform"></a>LUIS를 학습 플랫폼으로 사용 안 함
LUIS는 언어 모델의 도메인에 관련됩니다. 이는 일반적인 학습 플랫폼으로 작동한다는 의미가 아닙니다. 

## <a name="dont-add-many-example-utterances-of-the-same-format-ignoring-other-formats"></a>동일한 형식의 많은 예제 발화를 추가하여 다른 형식 무시 안 함
LUIS는 의도의 발화에서 변형을 예측합니다. 전체 의미는 동일하지만 발화가 달라질 수 있습니다. 변형에는 발화 길이, 단어 선택 및 단어 배치가 포함될 수 있습니다. 

|같은 형식 사용 안 함|다양한 형식 사용|
|--|--|
|Buy a ticket to Seattle<br>Buy a ticket to Paris<br>Buy a ticket to Orlando|Buy 1 ticket to Seattle<br>Reserve two seats on the red eye to Paris next Monday<br>I would like to book 3 tickets to Orlando for spring break|

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

## <a name="next-steps"></a>다음 단계

* LUIS 앱에서 [앱을 계획](luis-how-plan-your-app.md)하는 방법을 알아봅니다.