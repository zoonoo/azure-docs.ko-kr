---
title: 모델을 사용 하 여 디자인-LUIS
titleSuffix: Azure Cognitive Services
description: 언어 이해는 여러 유형의 모델을 제공 합니다. 일부 모델은 여러 가지 방법으로 사용할 수 있습니다.
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 10/25/2019
ms.author: diberry
ms.openlocfilehash: d721ceb25b3ce2408563a0bed16457d05affe7b4
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/13/2020
ms.locfileid: "79221330"
---
# <a name="design-with-intent-and-entity-models"></a>의도 및 엔터티 모델로 디자인 

언어 이해는 여러 유형의 모델을 제공 합니다. 일부 모델은 여러 가지 방법으로 사용할 수 있습니다. 

## <a name="v3-authoring-uses-machine-teaching"></a>V3 제작에서 기계 학습 사용

LUIS를 통해 사용자는 컴퓨터에 대 한 개념을 쉽게 학습할 수 있습니다. 그러면 컴퓨터에서 지능형 응용 프로그램을 지 원하는 데 사용할 수 있는 모델 (분류자 및 추출기 같은 개념의 기능 근사치)을 빌드할 수 있습니다. LUIS는 machine learning에서 제공 되지만 machine learning을 이해 하는 데에는이를 사용할 필요가 없습니다. 대신, machine 교사는 개념의 긍정적이 고 부정적 예를 표시 하 고 다른 관련 개념을 사용 하 여 개념을 모델링 하는 방법을 설명 하 여 LUIS에 대 한 개념을 전달 합니다. 또한 교사는 예측 실수를 식별 하 고 수정 하 여 LUIS의 모델을 대화형으로 향상 시킬 수 있습니다. 

## <a name="v3-authoring-model-decomposition"></a>V3 제작 모델 분해

LUIS은 V3 제작 Api를 사용한 _모델 분해_ 를 지원 하 여 모델을 더 작은 부분으로 분할 합니다. 이를 통해 다양 한 부분을 생성 하 고 예측 하는 방법에 대 한 확신을가지고 모델을 빌드할 수 있습니다.

모델 분해는 다음과 같은 부분으로 구성 됩니다.

* [의도](#intents-classify-utterances)
    * 기능에서 제공 하는 [설명자](#descriptors-are-features)
* [컴퓨터에서 배운 엔터티](#machine-learned-entities)
    * [하위 구성 요소](#entity-subcomponents-help-extract-data) (컴퓨터에서 학습 한 엔터티)
        * 기능에서 제공 하는 [설명자](#descriptors-are-features) 
        * 정규식 및 목록과 같이 컴퓨터에서 학습 하지 않은 엔터티에서 제공 하는 [제약 조건](#constraints-are-text-rules)

## <a name="v2-authoring-models"></a>V2 제작 모델

LUIS는 V2 authoring Api를 사용 하 여 복합 엔터티를 지원 합니다. 이와 유사한 모델 분해를 제공 하지만 V3 모델 분해와 동일 하지는 않습니다. 권장 되는 모델 아키텍처는 V3 제작 Api에서 모델 분해로 이동 하는 것입니다. 

## <a name="intents-classify-utterances"></a>의도 분류 길이 발언

의도 분류 예 길이 발언는 의도에 대해 LUIS를 학습 합니다. 의도 내에서 예제 길이 발언는 utterance의 긍정적인 예제로 사용 됩니다. 이러한 동일한 길이 발언는 다른 모든 의도의 부정 예제로 사용 됩니다.

사용자가 책을 주문 하 고 고객에 대 한 배송 주소가 필요한 앱을 확인 해야 하는 앱을 생각해 보세요. 이 앱에는 `OrderBook` 및 `ShippingLocation`의 두 가지 의도가 있습니다.

다음 utterance `OrderBook` 의도에 대 한 **긍정 예제** 이며 `ShippingLocation` 및 `None` 의도에 대 한 **부정적** 예입니다. 

`Buy the top-rated book on bot architecture.`

길이 발언의 예제를 사용 하 여 잘 설계 된 의도의 결과는 높은 의도 예측입니다. 

## <a name="entities-extract-data"></a>엔터티 데이터 추출

엔터티는 utterance에서 추출 하려는 데이터의 단위를 나타냅니다. 

### <a name="machine-learned-entities"></a>컴퓨터에서 배운 엔터티

컴퓨터에서 학습 한 엔터티는 하위 구성 요소를 포함 하는 최상위 엔터티가 며,이 엔터티는 컴퓨터에서 학습 한 엔터티 이기도 합니다. 

**컴퓨터에서 학습 한 엔터티 사용**:

* 클라이언트 응용 프로그램에 하위 구성 요소가 필요한 경우
* 기계 학습 알고리즘에서 엔터티를 분해 하는 데 도움이 되도록

각 하위 구성 요소는 다음을 가질 수 있습니다.

* 요소가
* 제약 조건 (정규식 엔터티 또는 목록 엔터티)
* 설명자 (구 목록과 같은 기능) 

기계 학습 엔터티의 예는 평면 티켓의 순서입니다. 개념적으로이는 날짜, 시간, 좌석 수량, 첫 번째 클래스 또는 coach, 원본 위치, 대상 위치, 식사 선택 등의 다양 한 데이터 단위가 포함 된 단일 트랜잭션입니다.


### <a name="entity-subcomponents-help-extract-data"></a>엔터티 하위 구성 요소를 통해 데이터 추출

하위 구성 요소는 컴퓨터에서 학습 한 부모 엔터티 내에서 컴퓨터에서 배운 자식 엔터티입니다. 

하위 **구성 요소를 사용 하**여 다음을 수행 합니다.

* 컴퓨터에서 학습 한 엔터티 (부모 엔터티)의 파트를 분해 합니다.

다음은 이러한 모든 개별 데이터를 포함 하는 컴퓨터에서 학습 한 엔터티를 나타냅니다.

* TravelOrder (컴퓨터에서 학습 한 엔터티)
    * DateTime (미리 작성 한 datetimeV2)
    * 위치 (컴퓨터에서 학습 한 엔터티)
        * 원본 (`from`와 같은 컨텍스트를 통해 찾은 역할)
        * Destination (`to`와 같은 컨텍스트를 통해 찾은 역할)
    * 좌석 배치 (컴퓨터에서 학습 한 엔터티)
        * 수량 (미리 작성 한 숫자)
        * 품질 (구문 목록 설명자를 사용 하 여 컴퓨터에서 학습 한 엔터티)
    * 식사 (음식 선택 항목으로 목록 엔터티의 제약 조건을 포함 하는 시스템 학습 엔터티)

원본 위치 및 대상 위치와 같은 이러한 데이터 중 일부는 `from` 및 `to`와 같은 utterance 컨텍스트에서 파악 해야 합니다. 데이터의 다른 부분은 정확한 문자열 일치 (`Vegan`) 또는 미리 작성 된 엔터티 (`Seattle`의 geographyV2 및 `Cairo`)로 추출할 수 있습니다. 

선택한 모델과 구성 방법에 따라 데이터를 일치 시키고 추출 하는 방법을 디자인 합니다.

### <a name="constraints-are-text-rules"></a>제약 조건은 텍스트 규칙입니다.

제약 조건은 정규식 엔터티 또는 목록 엔터티와 같이 컴퓨터에서 학습 하지 않은 엔터티에서 제공 하는 텍스트 일치 규칙입니다. 예측을 제한 하 고 클라이언트 응용 프로그램에 필요한 엔터티 확인을 제공 하기 위해 예측 시간에 제약 조건이 적용 됩니다. 이러한 규칙은 하위 구성 요소를 작성 하는 동안 정의 합니다. 

**제약 조건 사용**:
* 추출할 정확한 텍스트를 알고 있는 경우

제약 조건은 다음과 같습니다.

* [정규식](reference-entity-regular-expression.md) 엔터티
* 엔터티 [나열](reference-entity-list.md) 
* [미리](luis-reference-prebuilt-entities.md) 작성 한 엔터티

평면 티켓의 예제를 계속 진행 하면 정확한 텍스트 일치를 위해 목록 엔터티에 공항 코드가 있을 수 있습니다. 

공항 목록의 경우 시애틀에 대 한 목록 항목은 도시 이름 `Seattle`이 고 시애틀의 동의어는 시애틀 및 도시와 함께 시애틀에 대 한 공항 코드를 포함 합니다.

|`Seattle` 목록 엔터티 동의어|
|--|
|`Sea`|
|`seatac`|
|`Bellevue`|

공항 코드에 대해 3 문자 코드만 인식 하려면 정규식을 제약 조건으로 사용 합니다. 

`/^[A-Z]{3}$/`

## <a name="intents-versus-entities"></a>의도 및 엔터티

의도는 _전체_ utterance의 원하는 결과이 고, 엔터티는 utterance에서 추출 된 데이터의 일부입니다. 일반적으로는 클라이언트 응용 프로그램에서 수행 해야 하는 작업에 연결 되며 엔터티는이 작업을 수행 하는 데 필요한 정보입니다. 프로그래밍 관점에서 보면 의도는 메서드 호출을 트리거하고 엔터티는 해당 메서드 호출에 대 한 매개 변수로 사용 됩니다.

이 utterance은 의도 된 것 _이어야_ 하며, 엔터티가 있을 _수 있습니다_ .

`Buy an airline ticket from Seattle to Cairo`

이 utterance에는 단일 의도가 있습니다.

* 평면 티켓 구입

이 utterance에는 다음과 같은 여러 엔터티가 있을 _수 있습니다_ .

* 시애틀 (원점) 및 카이로 (대상)의 위치
* 단일 티켓 수량

## <a name="descriptors-are-features"></a>설명자가 기능입니다.

설명자는 문구 목록 및 엔터티를 포함 하 여 학습 시간에 모델에 적용 되는 기능입니다. 

다음을 수행 하려는 **경우 설명자를 사용**합니다.

* 설명자에 의해 식별 되는 단어 및 구의 중요도 높이기
* 설명자에 대해 권장 되는 새로운 텍스트 또는 구를 LUIS 권장 합니다.
* 학습 데이터의 오류 수정

## <a name="next-steps"></a>다음 단계

* [의도](luis-concept-intent.md) 및 [엔터티](luis-concept-entity-types.md)를 이해 합니다. 