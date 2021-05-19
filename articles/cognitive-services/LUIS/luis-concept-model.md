---
title: 모델을 사용하여 설계 - LUIS
description: 언어 이해는 다양한 유형의 모델을 제공합니다. 일부 모델은 두 가지 이상의 방법으로 사용할 수 있습니다.
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 04/30/2020
ms.openlocfilehash: 576ba945018d13db9cd24888f3c41a2215857694
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/30/2021
ms.locfileid: "91316514"
---
# <a name="design-with-intent-and-entity-models"></a>의도 및 엔터티 모델을 사용하여 설계

언어 이해에서는 앱 스키마를 정의하는 데 사용할 수 있는 두 가지 유형의 모델을 제공합니다. 앱 스키마는 새 사용자 발화의 예측에서 수신하는 정보를 결정합니다.

앱 스키마는 [머신 티칭](#authoring-uses-machine-teaching)을 사용하여 만든 모델을 기반으로 합니다.
* [의도](#intents-classify-utterances)가 사용자 발화를 분류합니다.
* [엔터티](#entities-extract-data)가 발화에서 데이터를 추출합니다.

## <a name="authoring-uses-machine-teaching"></a>작성이 머신 티칭을 사용합니다.

LUIS의 머신 티칭 방법론을 사용하면 컴퓨터에 대한 개념을 쉽게 학습할 수 있습니다. LUIS를 사용하기 위해 _기계 학습_ 을 이해할 필요가 없습니다. 대신 교사는 개념의 예를 제공하고 다른 관련 개념을 사용하여 개념을 모델링하는 방법을 설명하여 LUIS 개념을 전달합니다. 또한 교사는 예측 실수를 식별하고 수정하여 LUIS의 모델을 대화형으로 향상시킬 수 있습니다.

<a name="v3-authoring-model-decomposition"></a>

## <a name="intents-classify-utterances"></a>의도가 발화 분류

의도는 예제 발화를 분류하여 의도에 대해 LUIS를 학습시킵니다. 의도 내에서 예제 발화는 발화의 긍정 예로 사용됩니다. 이러한 동일한 발화는 다른 모든 의도의 부정 예로 사용됩니다.

책을 주문하는 사용자의 의도를 결정해야 하는 앱과 고객의 배송 주소가 필요한 앱이 있다고 가정합니다. 이 앱에는 두 개의 의도, `OrderBook`과 `ShippingLocation`이 있습니다.

다음 발화는 `OrderBook` 의도에 대한 **긍정 예** 와 `ShippingLocation` 및 `None` 의도에 대한 **부정 예** 입니다.

`Buy the top-rated book on bot architecture.`

## <a name="entities-extract-data"></a>엔터티가 데이터 추출

엔터티는 발화에서 추출하려는 데이터의 단위를 나타냅니다. 기계 학습 엔터티는 기계 학습 엔터티인 하위 엔터티를 포함하는 최상위 엔터티입니다.

기계 학습 엔터티의 예는 비행기 티켓 주문입니다. 개념적으로 이 주문은 날짜, 시간, 좌석 수, 좌석 유형(예: 일등석 또는 보통석), 출발지 위치, 도착지 위치 및 식사 선택 등의 다양한 작은 데이터 단위가 포함된 단일 트랜잭션입니다.

## <a name="intents-versus-entities"></a>의도 및 엔터티

의도는 _전체_ 발화의 원하는 결과이고, 엔터티는 발화에서 추출된 데이터의 일부입니다. 일반적으로 의도는 클라이언트 애플리케이션에서 수행해야 하는 작업에 연결됩니다. 엔터티는 이 작업을 수행하는 데 필요한 정보입니다. 프로그래밍 관점에서 보면 의도는 메서드 호출을 트리거하고 엔터티는 해당 메서드 호출에 대한 매개 변수로 사용됩니다.

이 발화는 의도를 _포함해야_ 하며, 엔터티를 _포함할 수도_ 있습니다.

`Buy an airline ticket from Seattle to Cairo`

이 발화에는 다음과 같은 단일 의도가 있습니다.

* 비행기 티켓 구입

이 발화에는 다음과 같은 여러 엔터티가 _있을 수 있습니다_.

* 위치 - 시애틀(출발지) 및 카이로(도착지)
* 단일 티켓 수량

## <a name="entity-model-decomposition"></a>엔터티 모델 분해

LUIS는 작성 API를 사용하여 _모델 분해_ 를 지원하고 개념을 더 작은 부분으로 나눕니다. 이를 통해 다양한 부분을 생성하고 예측하는 방법에 대한 확신을 가지고 모델을 빌드할 수 있습니다.

모델 분해는 다음과 같은 부분으로 구성됩니다.

* [의도](#intents-classify-utterances)
    * [features](#features)
* [기계 학습 엔터티](reference-entity-machine-learned-entity.md)
    * 하위 엔터티(기계 학습 엔터티)
        * [features](#features)
            * [구문 목록](luis-concept-feature.md)
            * [정규식](reference-entity-regular-expression.md), [목록](reference-entity-list.md) 및 [미리 빌드된 엔터티](luis-reference-prebuilt-entities.md)와 같은 [비 기계 학습 엔터티](luis-concept-feature.md)

<a name="entities-extract-data"></a>
<a name="machine-learned-entities"></a>

## <a name="features"></a>기능

[기능](luis-concept-feature.md)은 시스템이 관찰하는 데이터의 구별되는 특성입니다. 기계 학습 기능은 개념을 구분하는 항목을 찾을 수 있는 위치에 대한 중요한 실마리를 LUIS에 제공합니다. 이는 LUIS에서 사용할 수 있는 힌트이지만 어려운 규칙이 아닙니다. 이러한 힌트는 레이블과 함께 데이터를 찾는 데 사용됩니다.

## <a name="patterns"></a>패턴

[패턴](luis-concept-patterns.md)은 여러 발화가 매우 유사할 경우 정확도를 향상시키도록 디자인됩니다. 패턴를 통해 더 많은 발화를 제공하지 않고도 의도에 대한 더 높은 정확성을 얻을 수 있습니다.

## <a name="extending-the-app-at-runtime"></a>런타임에 앱 확장

앱의 스키마(모델 및 기능)가 학습되고 예측 엔드포인트에 게시됩니다. 예측 엔드포인트에 사용자의 발화와 함께 [새 정보를 전달](schema-change-prediction-runtime.md)하여 예측을 강화할 수 있습니다.

## <a name="next-steps"></a>다음 단계

* [의도](luis-concept-intent.md) 및 [엔터티](luis-concept-entity-types.md)를 이해합니다.
* [기능](luis-concept-feature.md)에 대해 알아봅니다.