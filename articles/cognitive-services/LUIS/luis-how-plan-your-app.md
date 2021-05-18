---
title: 앱 계획 - LUIS
description: 관련 앱 의도 및 엔터티를 간략하게 설명한 다음, LUIS(Language Understanding Intelligent Services)에서 애플리케이션 계획을 만듭니다.
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: how-to
ms.date: 05/14/2020
ms.openlocfilehash: 66df23466694fe8b9caea4e56565cde6d8fd7416
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/30/2021
ms.locfileid: "95018959"
---
# <a name="plan-your-luis-app-schema-with-subject-domain-and-data-extraction"></a>주체 도메인 및 데이터 추출을 사용하여 LUIS 앱 스키마 계획

LUIS 앱 스키마는 주체 [도메인](luis-glossary.md#domain)과 관련된 [의도](luis-glossary.md#intent) 및 [엔터티](luis-glossary.md#entity)를 포함합니다. 의도는 사용자 [발화](luis-glossary.md#utterance)를 분류하고 엔터티는 사용자 발화에서 데이터를 추출합니다.

## <a name="identify-your-domain"></a>도메인 식별

LUIS 앱은 주체 도메인을 중심으로 합니다. 예를 들어, 티켓, 항공편, 호텔 및 렌터카 예약을 처리하는 여행 앱이 있을 수 있습니다. 또 다른 앱에서는 운동, 피트니스 노력 추적 및 목표 설정과 관련된 콘텐츠를 제공할 수 있습니다. 도메인 식별은 도메인과 관련된 단어 또는 구를 찾는 데 도움이 됩니다.

> [!TIP]
> LUIS는 많은 일반적인 시나리오에 대해 [미리 빌드된 도메인](./howto-add-prebuilt-models.md)을 제공합니다. 미리 빌드된 도메인을 앱의 시작점으로 사용할 수 있는지 확인하세요.

## <a name="identify-your-intents"></a>의도 식별

애플리케이션의 작업에 중요한 [의도](luis-concept-intent.md)에 대해 생각해 보세요.

항공편을 예약하고 사용자 목적지의 날씨를 확인하는 기능이 있는 여행 앱을 예로 들어보겠습니다. 이러한 작업에 대해 `BookFlight` 및 `GetWeather` 의도를 정의할 수 있습니다.

더 많은 기능이 있는 복잡한 애플리케이션에서는 의도가 너무 구체적이지 않도록 신중하게 정의해야 합니다. 예를 들어 `BookFlight`와 `BookHotel`은 별도의 의도여야 할 수 있지만 `BookInternationalFlight`와 `BookDomesticFlight`는 너무 유사할 수 있습니다.

> [!NOTE]
> 앱의 기능을 수행하는 데 필요한 만큼의 의도만 사용하는 것이 좋습니다. 너무 많은 의도를 정의하면 LUIS에서 발화를 올바르게 분류하기가 더욱 어려워집니다. 너무 적게 정의하면 너무 일반적이어서 겹칠 수 있습니다.

전반적인 사용자 의도를 식별할 필요가 없는 경우 모든 사용자 예제 발화를 `None` 의도에 추가합니다. 앱에 더 많은 의도가 필요하게 되면 나중에 의도를 만들 수 있습니다.

## <a name="create-example-utterances-for-each-intent"></a>각 의도의 예제 발화 만들기

우선 각 의도에 대해 너무 많은 발화를 만들지 않습니다. 의도를 확인한 후 각 의도에 대한 예제 발화를 15~30개를 만듭니다. 각 발화는 이전에 제공된 발화와 달라야 합니다. 발화는 전체 단어 개수, 단어 선택, 동사 시제 및 [문장 부호](luis-reference-application-settings.md#punctuation-normalization)가 다양한 것이 좋습니다.

자세한 내용은 [LUIS 앱에 대한 좋은 발화 이해](luis-concept-utterance.md)를 참조하세요.

## <a name="identify-your-entities"></a>엔터티 식별

예제 발화에서 추출하려는 엔터티를 식별합니다. 항공편을 예약하려면 목적지, 날짜, 항공사, 티켓 범주 및 여행 클래스와 같은 정보가 필요합니다. 이러한 데이터 형식에 대한 엔터티를 만든 다음 예제 발화에서 [엔터티](luis-concept-entity-types.md)를 표시합니다. 엔터티는 의도를 수행하는 데 중요합니다.

앱에서 사용할 엔터티를 결정할 때는 개체 형식 간의 관계를 캡처하기 위한 엔터티 형식이 서로 다르다는 점에 유의합니다. [LUIS의 엔터티](luis-concept-entity-types.md)에서는 다양한 형식에 대해 자세히 설명합니다.

> [!TIP]
> LUIS는 일반적인 대화형 사용자 시나리오에 대해 [미리 빌드된 엔터티](./howto-add-prebuilt-models.md)를 제공합니다. 미리 빌드된 엔터티를 애플리케이션 개발의 시작점으로 사용하는 것이 좋습니다.

## <a name="resolution-with-intent-or-entity"></a>의도 또는 엔터티에 대한 해결 방법

대부분의 경우, 특히 자연스런 대화를 사용하는 경우 사용자는 둘 이상의 기능이나 의도를 포함할 수 있는 발화를 제공합니다. 이를 해결하기 위해 일반적으로 출력의 표현이 의도 및 엔터티 모두에서 수행될 수 있음을 이해해야 합니다. 이 표현은 클라이언트 애플리케이션 작업에 매핑할 수 있어야 하며, 의도로 제한될 필요가 없습니다.

**Int-ent-ties** 는 작업(일반적으로 의도로 이해됨)을 엔터티로 캡처할 수도 있고 특정 작업에 매핑 가능한 이 형식의 출력 JSON에서 신뢰할 수 있는 개념입니다. _부정_ 은 전체 추출에 대한 의도와 엔터티 모두에 대해 이러한 의존도를 활용하기 위해 일반적으로 사용됩니다.

다음과 같이 단어 선택이 매우 유사하지만 결과가 다른 두 개의 발화를 참조하세요.

|발화|
|--|
|`Please schedule my flight from Cairo to Seattle`|
|`Cancel my flight from Cairo to Seattle`|

별도의 의도 두 개를 사용하지 않고 `FlightAction` 기계 학습 엔터티를 사용하여 단일 의도를 만듭니다. 기계 학습 엔터티는 일정 예약 및 취소 요청뿐만 아니라 출발지 또는 목적지 위치에 대한 작업 세부 정보를 추출해야 합니다.

`FlightAction` 엔터티는 다음과 같이 기계 학습 엔터티 및 하위 엔터티로 구성된 의사 스키마로 구조화됩니다.

* FlightAction
    * 작업
    * 원본
    * 대상

추출하려면 하위 엔터티에 기능을 추가하면 됩니다. 사용자 발화에 표시될 어휘와 예측 응답에 반환될 값을 기준으로 기능을 선택합니다.

## <a name="next-steps"></a>다음 단계

> [!div class="nextstepaction"]
> [LUIS 개발 수명 주기 알아보기](luis-concept-app-iteration.md)