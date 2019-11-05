---
title: 앱 계획-LUIS
titleSuffix: Azure Cognitive Services
description: 관련 앱 의도 및 엔터티를 간략하게 설명한 다음, LUIS(Language Understanding Intelligent Services)에서 애플리케이션 계획을 만듭니다.
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 10/25/2019
ms.author: diberry
ms.openlocfilehash: b5e5df111b81cb60b6d194be190421bdb5ce2683
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/04/2019
ms.locfileid: "73467705"
---
# <a name="plan-your-luis-app-schema-with-subject-domain-and-data-extraction"></a>주제 도메인 및 데이터 추출을 사용 하 여 LUIS 앱 스키마 계획

LUIS 앱 스키마는 주체 도메인과 관련 된 의도 및 엔터티를 포함 합니다. 의도는 사용자 길이 발언를 분류 하 고 엔터티는 사용자 길이 발언에서 데이터를 추출 합니다. 

## <a name="identify-your-domain"></a>도메인 식별

LUIS 앱은 도메인 특정 항목을 중심으로 합니다.  예를 들어, 티켓, 항공편, 호텔 및 렌터카를 예약하는 여행 앱이 있을 수 있습니다. 또 다른 앱에서는 운동, 피트니스 노력 추적 및 목표 설정과 관련된 콘텐츠를 제공할 수 있습니다. 도메인 식별은 도메인에 중요한 단어 또는 구를 찾는 데 도움이 됩니다.

> [!TIP]
> LUIS는 많은 일반적인 시나리오에 대해 [미리 빌드된 도메인](luis-how-to-use-prebuilt-domains.md)을 제공합니다.
> 미리 빌드된 도메인을 앱의 시작점으로 사용할 수 있는지 확인하세요.

## <a name="identify-your-intents"></a>의도 식별

애플리케이션의 작업에 중요한 [의도](luis-concept-intent.md)에 대해 생각해 보세요. 

항공편을 예약하고 사용자 목적지의 날씨를 확인하는 기능이 있는 여행 앱을 예로 들어보겠습니다. 이러한 작업에 대해 `BookFlight` 및 `GetWeather` 의도를 정의할 수 있습니다. 

더 복잡 한 응용 프로그램에서는 더 많은 기능을 사용할 수 있으며, 의도 한 것이 너무 구체적이 지 않도록 신중 하 게 정의 해야 합니다. 예를 들어 `BookFlight` 및 `BookHotel`은 별도의 의도 여야 할 수 있지만 `BookInternationalFlight`와 `BookDomesticFlight`는 너무 유사할 수 있습니다.

> [!NOTE]
> 앱의 기능을 수행하는 데 필요한 만큼의 의도만 사용하는 것이 좋습니다. 너무 많은 의도를 정의하면 LUIS에서 발화를 올바르게 분류하기가 더욱 어려워집니다. 너무 적은 수를 정의 하는 경우 중복 될 수 있습니다.

전반적인 사용자 의도를 식별할 필요가 없는 경우 모든 예제 사용자 길이 발언을 없음 의도에 추가 합니다. 앱이 더 많은 의도를 필요로 하는 경우 나중에 만들 수 있습니다. 

## <a name="create-example-utterances-for-each-intent"></a>각 의도의 예제 발화 만들기

의도를 결정 한 후에는 각 의도에 대해 15 ~ 30 개의 예제 길이 발언를 만듭니다. 시작 하려면이 숫자 보다 적거나 각 의도에 대해 너무 많은 길이 발언를 만들어야 합니다. 각 발화는 이전 발화와 달라야 합니다. 발화는 전체 단어 개수, 단어 선택, 동사 시제 및 문장 부호가 다양한 것이 좋습니다. 

자세한 내용은 [길이 발언](luis-concept-utterance.md) 를 검토 하십시오.

## <a name="identify-your-entities"></a>엔터티 식별

예제 발화에서 추출하려는 엔터티를 식별합니다. 비행을 예약 하려면 대상, 날짜, 항공, 티켓 범주 및 여행 클래스와 같은 정보가 필요 합니다. 이러한 데이터 형식에 대 한 엔터티를 만든 다음 의도를 수행 하는 데 중요 하기 때문에 길이 발언 예제에서 [엔터티](luis-concept-entity-types.md) 를 표시 합니다. 

앱에서 사용할 엔터티를 결정할 때 개체 형식 간의 관계를 캡처하기 위한 다양한 형식의 엔터티가 있다는 점에 주의하세요. [LUIS의 엔터티](luis-concept-entity-types.md)에서는 다양한 형식에 대해 자세히 설명합니다.

## <a name="next-steps"></a>다음 단계

일반적인 [개발 주기에](luis-concept-app-iteration.md)대해 알아보세요.  