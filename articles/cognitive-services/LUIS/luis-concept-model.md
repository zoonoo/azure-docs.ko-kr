---
title: 모델 설계 - LUIS
titleSuffix: Azure Cognitive Services
description: 언어 이해는 여러 유형의 모델을 제공합니다. 일부 모델은 두 가지 이상의 방법으로 사용할 수 있습니다.
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79221330"
---
# <a name="design-with-intent-and-entity-models"></a>의도 및 엔터티 모델로 설계 

언어 이해는 여러 유형의 모델을 제공합니다. 일부 모델은 두 가지 이상의 방법으로 사용할 수 있습니다. 

## <a name="v3-authoring-uses-machine-teaching"></a>V3 작성은 기계 교육을 사용합니다.

LUIS를 사용하면 사람들이 기계에 개념을 쉽게 가르칠 수 있습니다. 그런 다음 기계는 지능형 응용 프로그램에 전력을 공급하는 데 사용할 수 있는 모델(분류기 및 추출기와 같은 개념의 기능 근사치)을 빌드할 수 있습니다. LUIS는 기계 학습에 의해 구동되지만 기계 학습에 대한 이해는 이를 사용할 필요가 없습니다. 대신, 기계 교사는 개념의 긍정적이고 부정적인 예를 보여주고 다른 관련 개념을 사용하여 개념을 모델링하는 방법을 설명하여 LUIS에 개념을 전달합니다. 교사는 예측 실수를 식별하고 수정하여 LUIS 모델을 대화형으로 개선할 수도 있습니다. 

## <a name="v3-authoring-model-decomposition"></a>V3 작성 모델 분해

LUIS는 V3 저작 API를 사용하여 _모델 분해를_ 지원하여 모델을 더 작은 부분으로 세분화합니다. 이를 통해 다양한 부품이 구성되고 예측되는 방식에 확신을 가지고 모델을 작성할 수 있습니다.

모델 분해에는 다음과 같은 부분이 있습니다.

* [의도](#intents-classify-utterances)
    * 기능에서 제공하는 [설명자](#descriptors-are-features)
* [기계 학습 엔터티](#machine-learned-entities)
    * [하위 구성 요소(또한](#entity-subcomponents-help-extract-data) 기계 학습된 엔터티)
        * 기능에서 제공하는 [설명자](#descriptors-are-features) 
        * 정규식 및 목록과 같이 기계학습되지 않은 엔터티에서 제공하는 [제약 조건](#constraints-are-text-rules)

## <a name="v2-authoring-models"></a>V2 작성 모델

LUIS는 V2 작성 API를 사용하여 복합 엔터티를 지원합니다. 이는 유사한 모델 분해를 제공하지만 V3 모델 분해와 동일하지 않습니다. 권장되는 모델 아키텍처는 V3 작성 API에서 분해 모델로 이동하는 것입니다. 

## <a name="intents-classify-utterances"></a>의도를 발언으로 분류

의도는 LUIS가 의도에 대해 가르치기 위해 예제 발언을 분류합니다. 의도 내의 예제 발언은 발언의 긍정적인 예로 사용됩니다. 이러한 동일한 발언은 다른 모든 의도에서 부정적인 예제로 사용됩니다.

책을 주문하려는 사용자의 의도를 결정해야 하는 앱과 고객의 배송 주소가 필요한 앱을 고려해 보입니다. 이 앱에는 두 `OrderBook` 가지 `ShippingLocation`의도가 있습니다.

다음 발언은 `OrderBook` 의도에 대한 **긍정적인 예이며** `ShippingLocation` 및 `None` 의도에 대한 부정적인 **예입니다.** 

`Buy the top-rated book on bot architecture.`

예제 발언과 함께 잘 설계된 의도의 결과는 높은 의도 예측입니다. 

## <a name="entities-extract-data"></a>엔터티 추출 데이터

엔터티는 발언에서 추출하려는 데이터 단위를 나타냅니다. 

### <a name="machine-learned-entities"></a>기계 학습 엔터티

기계 학습 엔터티는 하위 구성 요소를 포함하는 최상위 엔터티이며, 이 엔터티는 기계 학습 엔터티이기도 합니다. 

**기계 학습 엔터티 사용:**

* 클라이언트 응용 프로그램에서 하위 구성 요소가 필요한 경우
* 기계 학습 알고리즘이 엔터티를 분해할 수 있도록 지원

각 하위 구성 요소는 다음을 가질 수 있습니다.

* 서브컴포넌트
* 제약 조건(정규식 엔터티 또는 목록 엔터티)
* 설명자(구 목록과 같은 기능) 

기계 학습 엔터티의 예로는 비행기 티켓주문이 있습니다. 개념적으로 이것은 날짜, 시간, 좌석 수량, 퍼스트 클래스 또는 코치, 출발지 위치, 목적지 위치 및 식사 선택과 같은 좌석 유형과 같은 많은 작은 데이터 단위의 단일 거래입니다.


### <a name="entity-subcomponents-help-extract-data"></a>엔터티 하위 구성 요소는 데이터를 추출하는 데 도움이 됩니다.

하위 구성 요소는 기계학습 상위 엔터티 내에서 학습된 하위 엔터티입니다. 

**하위 구성 요소를 다음으로 사용합니다.**

* 기계 학습 된 엔터티 (상위 엔터티)의 일부를 분해합니다.

다음은 이러한 모든 개별 데이터 조각을 가진 기계 학습 엔터티를 나타냅니다.

* 트래블오더(기계학습법인)
    * 날짜 시간(미리 빌드된 날짜시간V2)
    * 위치(기계 학습 엔터티)
        * 원본(컨텍스트를 `from`통해 발견되는 역할)
        * 대상(컨텍스트를 `to`통해 검색되는 역할)
    * 좌석(기계 학습 실)
        * 수량(미리 빌드된 번호)
        * 품질(구 목록 설명자가 있는 기계 학습 엔터티)
    * 식사(목록 엔터티를 음식 선택으로 제약을 가하는 기계 학습 엔터티)

원본 위치 및 대상 위치와 같은 이 데이터 중 일부는 발언의 컨텍스트에서 학습해야 합니다. `from` `to` 데이터의 다른 부분은 정확한 문자열 일치 ()`Vegan`또는 미리 빌드된 엔터티(geographyV2 `Seattle` 및)로 `Cairo`추출할 수 있습니다. 

선택한 모델과 데이터를 구성하는 방법에 따라 데이터가 일치하고 추출되는 방법을 설계합니다.

### <a name="constraints-are-text-rules"></a>제약 조건은 텍스트 규칙입니다.

제약 조건은 정규식 엔터티 또는 목록 엔터티와 같이 기계학습되지 않은 엔터티에서 제공하는 텍스트 일치 규칙입니다. 제약 조건은 예측을 제한하고 클라이언트 응용 프로그램에 필요한 엔터티 해결을 제공하기 위해 예측 시간에 적용됩니다. 하위 구성 요소를 작성하는 동안 이러한 규칙을 정의합니다. 

**제약 조건 사용:**
* 추출할 정확한 텍스트를 알 수 있습니다.

제약 조건은 다음과 같습니다.

* [정규식](reference-entity-regular-expression.md) 엔터티
* [목록](reference-entity-list.md) 엔터티 
* [미리 빌드된](luis-reference-prebuilt-entities.md) 엔터티

비행기 티켓의 예를 계속, 공항 코드는 정확한 텍스트 일치에 대 한 목록 엔터티에 있을 수 있습니다. 

공항 목록의 경우 시애틀의 목록 항목은 도시 `Seattle` 이름이며 시애틀의 동의어에는 주변 도시 및 도시와 함께 시애틀공항 코드가 포함됩니다.

|`Seattle`엔터티 동의어 목록|
|--|
|`Sea`|
|`seatac`|
|`Bellevue`|

공항 코드에 대한 3개의 문자 코드만 인식하려면 정규식을 제약 조건으로 사용합니다. 

`/^[A-Z]{3}$/`

## <a name="intents-versus-entities"></a>의도 대 엔터티

의도는 _전체_ 발언의 원하는 결과이며 엔터티는 발화에서 추출된 데이터의 일부입니다. 일반적으로 의도는 클라이언트 응용 프로그램에서 수행해야 하는 작업에 연결되며 엔터티는 이 작업을 수행하는 데 필요한 정보입니다. 프로그래밍 관점에서 의도메서드 호출을 트리거하고 엔터티가 해당 메서드 호출의 매개 변수로 사용됩니다.

이 발언에는 의도가 _있어야 하며_ 엔터티가 있을 _수 있습니다._

`Buy an airline ticket from Seattle to Cairo`

이 발언에는 단일 의도가 있습니다.

* 비행기 티켓 구매

이 발언에는 다음과 같은 여러 엔터티가 _있을 수 있습니다._

* 시애틀(출발지) 및 카이로(목적지) 위치
* 단일 티켓의 수량

## <a name="descriptors-are-features"></a>설명자는 기능입니다.

설명자는 구 목록 및 엔터티를 포함하여 교육 시간에 모델에 적용되는 기능입니다. 

**다음을 수행할 때 설명자 사용**

* 설명자가 식별한 단어와 구의 중요성을 높입니다.
* LUIS가 설명자에게 추천할 새 텍스트 나 구를 추천합니다.
* 학습 데이터에 대한 오류 수정

## <a name="next-steps"></a>다음 단계

* [의도와](luis-concept-intent.md) [엔터티이해](luis-concept-entity-types.md). 