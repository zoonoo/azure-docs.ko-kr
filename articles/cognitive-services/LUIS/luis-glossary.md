---
title: 용어
titleSuffix: Language Understanding - Azure Cognitive Services
description: 용어집에서는 LUIS API 서비스를 사용할 때 나타날 수 있는 용어에 대해 설명합니다.
services: cognitive-services
author: diberry
manager: cgronlun
ms.service: cognitive-services
ms.component: language-understanding
ms.topic: article
ms.date: 01/23/2019
ms.author: diberry
ms.openlocfilehash: 8ab5000f1f50e10195c02b6198fa0bcffd0408b5
ms.sourcegitcommit: 8115c7fa126ce9bf3e16415f275680f4486192c1
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/24/2019
ms.locfileid: "54844767"
---
# <a name="language-understanding-glossary-of-common-vocabulary-and-concepts"></a>일반적인 용어 및 개념의 언어 이해 용어집
Language Understanding(LUIS) 용어집에서는 LUIS API 서비스를 사용할 때 나타날 수 있는 용어에 대해 설명합니다.

## <a name="active-version"></a>활성 버전

활성 LUIS 버전은 모델의 변경 내용을 수신하는 버전입니다. [LUIS](luis-reference-regions.md) 웹 사이트에서 활성 버전이 아닌 버전을 변경하려면 먼저 해당 버전을 활성으로 설정해야 합니다.

## <a name="authoring"></a>작성

작성은 [LUIS](luis-reference-regions.md) 웹 사이트 또는 [작성 API](https://aka.ms/luis-authoring-api)를 사용하여 [LUIS 앱](#luis-app)을 생성, 관리 및 배포하는 기능입니다.

## <a name="authoring-key"></a>작성 키

이전에는 “프로그래밍 방식” 키라고 했습니다. 앱을 작성하는 데 사용됩니다. 프로덕션 수준 엔드포인트 쿼리에는 사용되지 않습니다. 자세한 내용은 [키 제한](luis-boundaries.md#key-limits)을 참조하세요.   

## <a name="batch-test-json-file"></a>텍스트 JSON 파일 일괄 처리

일괄 처리 파일은 JSON 배열입니다. 배열의 각 요소에는 `text`, `intent` 및 `entities`의 세 가지 속성이 있습니다. `entities` 속성은 배열입니다. 배열은 비어 있을 수 있습니다. 비어 있지 않은 `entities` 배열은 엔터티를 정확히 식별해야 합니다.

```JSON
[
    {
        "text": "drive me home",
        "intent": "None",
        "entities": []
    },
    {
        "text": "book a flight to orlando on the 25th",
        "intent": "BookFlight",
        "entities": [
            {
                "entity": "orlando",
                "type": "Location",
                "startIndex": 18,
                "endIndex": 25
            }
        ]
    }
]

```


## <a name="collaborator"></a>협력자

협력자는 앱의 [소유자](#owner)가 아니지만, 의도, 엔터티, 발화를 추가, 편집 및 삭제할 동일한 권한을 가집니다.

## <a name="currently-editing"></a>현재 편집 중

[활성 버전](#active-version)과 동일함

## <a name="domain"></a>도메인

LUIS 컨텍스트에서 **도메인**은 정보 영역입니다. 도메인은 정보의 앱 영역에 관련됩니다. 이것은 여행사 앱과 같은 일반적인 영역일 수 있습니다. 여행사 앱은 특정 지리적 위치, 언어 및 서비스와 같은 회사의 정보 영역에만 관련될 수 있습니다.

## <a name="endpoint"></a>엔드포인트

[LUIS 엔드포인트](https://aka.ms/luis-endpoint-apis) URL은 [LUIS 앱](#luis-app)이 작성되고 게시된 후 LUIS 쿼리를 제출하는 위치입니다. 엔드포인트 URL에는 앱 ID와 게시된 앱의 지역이 포함됩니다. 앱의 **[키 및 엔드포인트](luis-how-to-azure-subscription.md)** 페이지에 있는 엔드포인트를 찾거나 [앱 정보 가져오기](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5890b47c39e2bb052c5b9c37) API에서 엔드포인트 URL을 가져올 수 있습니다.

예제 엔드포인트가 다음과 같이 표시됩니다.

`https://<region>.api.cognitive.microsoft.com/luis/v2.0/apps/<appID>?subscription-key=<subscriptionID>&verbose=true&timezoneOffset=0&q=<utterance>`

|쿼리 문자열 매개 변수|description|
|--|--|
|region| [게시된 지역](luis-reference-regions.md#publishing-regions) |
|appID | LUIS 앱 ID |
|subscriptionID | Azure Portal에서 생성된 LUIS 끝점(구독) 키 |
|q | 발화 |
|timezoneOffset| minutes|

## <a name="entity"></a>엔터티

[엔터티](luis-concept-entity-types.md)는 [의도](luis-concept-intent.md)에 관련된 정보를 설명하는 [발화](luis-concept-utterance.md)의 중요한 단어이고 때때로 의도에 필수적입니다. 엔터티는 기본적으로 LUIS의 데이터 형식입니다.

## <a name="f-measure"></a>F 측정값

[일괄 테스트](luis-interactive-test.md#batch-testing)에서 테스트 정확도의 측정값입니다.

## <a name="false-negative"></a>가음성(TN)

[일괄 테스트](luis-interactive-test.md#batch-testing)에서 데이터 요소가 앱이 대상 의도/엔터티가 없음을 잘못 예측한 발언을 나타냅니다.

## <a name="false-positive"></a>가양성(TP)

[일괄 테스트](luis-interactive-test.md#batch-testing)에서 데이터 요소가 앱이 대상 의도/엔터티가 있음을 잘못 예측한 발언을 나타냅니다.

## <a name="features"></a>기능

기계 학습에서 [기능](luis-concept-feature.md)은 시스템이 관찰하는 데이터의 구별되는 특성입니다.

## <a name="intent"></a>의도

[의도](luis-concept-intent.md)는 사용자가 수행하려는 작업을 나타냅니다. 항공편 예약, 결제 또는 뉴스 기사 찾기와 같이 사용자 입력으로 표현된 목적이나 목표입니다. LUIS에서 의도 예측은 전체 발화를 기반으로 합니다. 비교해 보면 엔터티는 발화의 조각입니다.

## <a name="labeling"></a>레이블 지정

레이블 지정은 의도의 [발화](#utterance)에 있는 단어 또는 구문을 [엔터티](#entity)(데이터 형식)와 연결하는 프로세스입니다.

## <a name="luis-app"></a>LUIS 앱

LUIS 앱은 [의도](#intent), [엔터티](#entity) 및 레이블이 지정된 [발화](#utterance)를 포함하여 자연어 처리를 위한 학습된 데이터 모델입니다.

## <a name="owner"></a>소유자

각 앱에는 앱을 만든 사용자인 하나의 소유자가 있습니다. 소유자는 [협력자](#collaborator)를 추가할 수 있습니다.

## <a name="pattern"></a>패턴
이전 패턴 기능은 [패턴](luis-concept-patterns.md)으로 바뀌었습니다. 패턴을 사용하여 더 적은 학습 예제를 제공하는 방식으로 예측 정확도를 개선합니다.

## <a name="phrase-list"></a>구문 목록

[구문 목록](luis-concept-feature.md#what-is-a-phrase-list-feature)에는 동일한 클래스에 속하고 비슷하게 처리되어야 하는 값(단어 또는 구문) 그룹이 포함됩니다(예: 도시 또는 제품의 이름). 서로 교환 가능한 목록은 동의어로 처리됩니다.

## <a name="prebuilt-domains"></a>미리 빌드된 도메인

[미리 빌드된 도메인](luis-how-to-use-prebuilt-domains.md)은 홈 자동화(HomeAutomation) 또는 음식점 예약(RestaurantReservation)과 같이 특정 도메인에 구성된 LUIS 앱입니다. 의도, 발화 및 엔터티가 이 도메인에 구성됩니다.

## <a name="prebuilt-entity"></a>미리 빌드된 엔터티

[미리 빌드된 엔터티](luis-prebuilt-entities.md)는 LUIS가 번호, URL 및 메일과 같은 일반적인 정보 유형에 제공하는 엔터티입니다. 미리 빌드된 엔터티를 애플리케이션에 추가하도록 선택합니다.

## <a name="precision"></a>정밀도
[일괄 테스트](luis-interactive-test.md#batch-testing)에서 정밀도(긍정적 예측 값이라고도 함)는 검색된 발언 중에 관련된 발언의 일부입니다.

## <a name="programmatic-key"></a>프로그래밍 방식 키

이름이 [작성 키](#authoring-key)로 바뀌었습니다.

## <a name="publish"></a>게시

게시는 스테이징 또는 프로덕션 [엔드포인트](#endpoint)에서 LUIS [활성 버전](#active-version)을 사용 가능하게 만드는 것입니다.  

## <a name="quota"></a>할당량

LUIS 할당량은 [Azure 구독 계층](https://aka.ms/luis-price-tier)의 제한 사항입니다. LUIS 할당량은 초당 요청 수(HTTP 상태 429) 및 월별 총 요청 수(HTTP 상태 403)를 통해 제한할 수 있습니다.

## <a name="recall"></a>재현율
[일괄 테스트](luis-interactive-test.md#batch-testing)에서 재현율(민감도라고도 함)은 LUIS가 일반화하는 기능입니다.

## <a name="semantic-dictionary"></a>의미 체계 사전
의미 체계 사전은 구문 목록 페이지와 목록 엔터티 페이지에서 제공됩니다. 의미 체계 사전은 현재 범위를 기반으로 단어 제안을 제공합니다.

## <a name="sentiment-analysis"></a>감정 분석
감정 분석은 [Text Analytics](https://azure.microsoft.com/services/cognitive-services/text-analytics/)에서 제공되는 발화의 긍정적 또는 부정적 값을 제공합니다.

## <a name="speech-priming"></a>음성 초기화

음성 초기화를 사용하면 LUIS 모델을 통해 Speech Service를 초기화할 수 있습니다.

## <a name="spelling-correction"></a>맞춤법 교정

Bing Spell Checker를 사용하여 예측 전에 발화에서 맞춤법이 잘못된 단어를 교정합니다.

## <a name="starter-key"></a>시작 키

[프로그래밍 방식 키](#programmatic-key)와 동일하고 작성 키로 이름이 바뀌었습니다.

## <a name="subscription-key"></a>구독 키

구독 키는 [Azure에서 만든](luis-how-to-azure-subscription.md) LUIS 서비스와 연결된 **끝점** 키입니다. 이 키는 [작성 키](#programmatic-key)가 아닙니다. 끝점 키가 있는 경우 작성 키 대신 모든 끝점 요청에 사용해야 합니다. [LUIS](luis-reference-regions.md) 웹 사이트의 [**키 및 엔드포인트** 페이지](luis-how-to-azure-subscription.md) 아래쪽에 있는 엔드포인트 URL 내부에서 현재 엔드포인트 키를 확인할 수 있습니다. 이는 **subscription-key** 이름/값 쌍의 값입니다.

## <a name="test"></a>테스트

LUIS 앱 [테스트](luis-interactive-test.md#test-your-app)는 LUIS에 발화를 전달하고 JSON 결과를 확인하는 것을 의미합니다.

## <a name="timezoneoffset"></a>표준 시간대 오프셋

엔드포인트에는 timezoneOffset이 포함됩니다. 이는 datetimeV2 미리 빌드된 엔터티에서 추가하거나 제거하려는 시간(분)입니다. 예를 들어, 발화가 “몇 시인가요?”인 경우 반환된 datetimeV2는 클라이언트 요청의 현재 시간입니다. 클라이언트 요청을 봇에서 가져오거나 봇의 사용자와 동일하지 않은 다른 애플리케이션에서 가져오는 경우에는 봇과 사용자 간의 오프셋을 전달해야 합니다.

[미리 빌드된 datetimeV2 엔터티의 표준 시간대 변경](luis-concept-data-alteration.md?#change-time-zone-of-prebuilt-datetimev2-entity)을 참조하세요.

## <a name="token"></a>토큰
토큰은 엔터티에서 레이블을 지정할 수 있는 가장 작은 단위입니다. 토큰화는 애플리케이션의 [문화권](luis-language-support.md#tokenization)을 기반으로 합니다.

## <a name="train"></a>학습

학습은 마지막 학습 이후 [활성 버전](#active-version)의 변경 내용을 LUIS에 학습시키는 프로세스입니다.

## <a name="true-negative"></a>참 부정(TN)

[일괄 테스트](luis-interactive-test.md#batch-testing)에서 데이터 요소가 앱이 대상 의도/엔터티가 없음을 올바르게 예측한 발언을 나타냅니다.

## <a name="true-positive"></a>참 긍정(TP)

[일괄 테스트](luis-interactive-test.md#batch-testing)에서 데이터 요소가 앱이 대상 의도/엔터티가 있음을 올바르게 예측한 발언을 나타냅니다.

## <a name="utterance"></a>발화

발화는 “다음 주 화요일 시애틀행 티켓 2매 예약”과 같은 자연어 구문입니다. 예제 발화는 의도에 추가됩니다.

## <a name="version"></a>버전

LUIS [버전](luis-how-to-manage-versions.md)은 LUIS 앱 ID 및 게시된 엔드포인트와 연결된 특정 데이터 모델입니다. 모든 LUIS 앱에는 하나 이상의 버전이 있습니다.
