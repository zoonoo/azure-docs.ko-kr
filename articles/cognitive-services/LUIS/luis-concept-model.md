---
title: 모델을 사용 하 여 디자인-LUIS
description: 언어 이해는 여러 유형의 모델을 제공 합니다. 일부 모델은 여러 가지 방법으로 사용할 수 있습니다.
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 04/30/2020
ms.openlocfilehash: 576ba945018d13db9cd24888f3c41a2215857694
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/25/2020
ms.locfileid: "91316514"
---
# <a name="design-with-intent-and-entity-models"></a>의도 및 엔터티 모델을 사용하여 설계

언어 이해에서는 앱 스키마를 정의 하는 데 사용할 수 있는 두 가지 유형의 모델을 제공 합니다. 앱 스키마는 새 사용자 utterance의 예측에서 수신 하는 정보를 결정 합니다.

앱 스키마는 [기계 학습](#authoring-uses-machine-teaching)을 사용 하 여 만든 모델을 기반으로 합니다.
* [의도](#intents-classify-utterances) 분류 사용자 길이 발언
* [엔터티](#entities-extract-data) 는 utterance에서 데이터를 추출 합니다.

## <a name="authoring-uses-machine-teaching"></a>제작에서 기계 학습 사용

LUIS의 기계 학습 방법론을 사용 하면 컴퓨터에 대 한 개념을 쉽게 학습할 수 있습니다. _기계 학습_ 을 이해 하는 것은 LUIS를 사용 하는 데 필요 하지 않습니다. 대신 교사는 개념의 예제를 제공 하 고 다른 관련 개념을 사용 하 여 개념을 모델링 하는 방법을 설명 하 여 LUIS 개념을 전달 합니다. 또한 교사는 예측 실수를 식별 하 고 수정 하 여 LUIS의 모델을 대화형으로 향상 시킬 수 있습니다.

<a name="v3-authoring-model-decomposition"></a>

## <a name="intents-classify-utterances"></a>의도가 발화 분류

의도 분류 예 길이 발언는 의도에 대해 LUIS를 학습 합니다. 의도 내에서 예제 길이 발언는 utterance의 긍정적인 예제로 사용 됩니다. 이러한 동일한 길이 발언는 다른 모든 의도의 부정 예제로 사용 됩니다.

사용자가 책을 주문 하 고 고객에 대 한 배송 주소가 필요한 앱을 확인 해야 하는 앱을 생각해 보세요. 이 앱에는 및 라는 두 가지 의도가 있습니다. `OrderBook` `ShippingLocation`

다음 utterance는 의도에 대 한 **긍정 예제** 이며 `OrderBook` 및 의도에 대 한 **부정적 예** 입니다 `ShippingLocation` `None` .

`Buy the top-rated book on bot architecture.`

## <a name="entities-extract-data"></a>엔터티가 데이터 추출

엔터티는 utterance에서 추출 하려는 데이터의 단위를 나타냅니다. Machine learning 엔터티는 기계 학습 엔터티인 하위 엔터티를 포함 하는 최상위 엔터티입니다.

기계 학습 엔터티의 예는 평면 티켓의 순서입니다. 개념적으로이는 날짜, 시간, 좌석 수량, 첫 번째 클래스 또는 coach, 원본 위치, 대상 위치, 식사 선택 등의 다양 한 데이터 단위가 포함 된 단일 트랜잭션입니다.

## <a name="intents-versus-entities"></a>의도 및 엔터티

의도는 _전체_ utterance의 원하는 결과이 고, 엔터티는 utterance에서 추출 된 데이터의 일부입니다. 일반적으로 의도는 클라이언트 응용 프로그램에서 수행 해야 하는 작업에 연결 됩니다. 엔터티는이 작업을 수행 하는 데 필요한 정보입니다. 프로그래밍 관점에서 보면 의도는 메서드 호출을 트리거하고 엔터티는 해당 메서드 호출에 대 한 매개 변수로 사용 됩니다.

이 utterance은 의도 된 것 _이어야_ 하며, 엔터티가 있을 _수 있습니다_ .

`Buy an airline ticket from Seattle to Cairo`

이 utterance에는 단일 의도가 있습니다.

* 평면 티켓 구입

이 utterance에는 다음과 같은 여러 엔터티가 있을 _수 있습니다_ .

* 시애틀 (원점) 및 카이로 (대상)의 위치
* 단일 티켓 수량

## <a name="entity-model-decomposition"></a>엔터티 모델 분해

LUIS는 작성 Api를 사용 하 여 _모델 분해_ 를 지원 하 고 개념을 더 작은 부분으로 나눕니다. 이를 통해 다양 한 부분을 생성 하 고 예측 하는 방법에 대 한 확신을가지고 모델을 빌드할 수 있습니다.

모델 분해는 다음과 같은 부분으로 구성 됩니다.

* [의도](#intents-classify-utterances)
    * [요소](#features)
* [기계 학습 엔터티](reference-entity-machine-learned-entity.md)
    * 하위 엔터티 (기계 학습 엔터티 이기도)
        * [요소](#features)
            * [구 목록](luis-concept-feature.md)
            * [정규식](reference-entity-regular-expression.md), [목록](reference-entity-list.md)및 [미리](luis-reference-prebuilt-entities.md) 작성 된 엔터티와 같은 [비 기계어 학습 엔터티](luis-concept-feature.md)

<a name="entities-extract-data"></a>
<a name="machine-learned-entities"></a>

## <a name="features"></a>기능

[기능은](luis-concept-feature.md) 시스템이 관찰 하는 데이터의 특징 또는 특성입니다. 기계 학습 기능을 통해 개념을 구분 하는 항목을 찾을 수 있는 위치에 대 한 중요 한 LUIS 수 있습니다. LUIS는 사용할 수 있지만 하드 규칙은 사용할 수 없다는 힌트입니다. 이러한 힌트는 데이터를 찾기 위해 레이블과 함께 사용 됩니다.

## <a name="patterns"></a>패턴

[패턴](luis-concept-patterns.md) 은 여러 길이 발언 매우 유사한 경우 정확성을 향상 시 키도 록 설계 되었습니다. 패턴를 통해 더 많은 발화를 제공하지 않고도 의도에 대한 더 높은 정확성을 얻을 수 있습니다.

## <a name="extending-the-app-at-runtime"></a>런타임에 앱 확장

앱의 스키마 (모델 및 기능)를 학습 하 고 예측 끝점에 게시 합니다. 예측을 보강 하기 위해 사용자의 utterance 함께 [새 정보](schema-change-prediction-runtime.md)를 예측 끝점에 전달할 수 있습니다.

## <a name="next-steps"></a>다음 단계

* [의도](luis-concept-intent.md) 및 [엔터티](luis-concept-entity-types.md)를 이해 합니다.
* [기능](luis-concept-feature.md) 에 대 한 자세한 정보