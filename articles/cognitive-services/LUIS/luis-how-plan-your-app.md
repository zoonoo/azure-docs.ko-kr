---
title: 앱 계획
titleSuffix: Language Understanding - Azure Cognitive Services
description: 관련 앱 의도 및 엔터티를 간략하게 설명한 다음, LUIS(Language Understanding Intelligent Services)에서 애플리케이션 계획을 만듭니다.
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 01/02/2019
ms.author: diberry
ms.openlocfilehash: 9d54cff81f39f41b60800e9b33f3b4da1a735d85
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/18/2019
ms.locfileid: "59794995"
---
# <a name="plan-your-luis-app-with-subject-domain-intents-and-entities"></a>주체 도메인, 의도 및 엔터티를 사용하여 LUIS 앱 계획

앱을 계획하는 것이 중요합니다. 애플리케이션에 관련된 가능한 의도 및 엔터티를 포함한 도메인을 식별합니다.  

## <a name="identify-your-domain"></a>도메인 식별

LUIS 앱은 도메인 특정 항목을 중심으로 합니다.  예를 들어, 티켓, 항공편, 호텔 및 렌터카를 예약하는 여행 앱이 있을 수 있습니다. 또 다른 앱에서는 운동, 피트니스 노력 추적 및 목표 설정과 관련된 콘텐츠를 제공할 수 있습니다. 도메인 식별은 도메인에 중요한 단어 또는 구를 찾는 데 도움이 됩니다.

> [!TIP]
> LUIS는 많은 일반적인 시나리오에 대해 [미리 빌드된 도메인](luis-how-to-use-prebuilt-domains.md)을 제공합니다.
> 미리 빌드된 도메인을 앱의 시작점으로 사용할 수 있는지 확인하세요.

## <a name="identify-your-intents"></a>의도 식별

애플리케이션의 작업에 중요한 [의도](luis-concept-intent.md)에 대해 생각해 보세요. 항공편을 예약하고 사용자 목적지의 날씨를 확인하는 기능이 있는 여행 앱을 예로 들어보겠습니다. 이러한 동작에 대해 “BookFlight” 및 “GetWeather” 의도를 정의할 수 있습니다. 기능이 더 많은 보다 복잡한 앱에서는 의도가 더 많으므로 너무 구체적이지 않도록 주의해서 정의해야 합니다. 예를 들어, “BookFlight” 및 “BookHotel”은 별도의 의도여야 하지만 “BookInternationalFlight” 및 “BookDomesticFlight”는 너무 유사할 수 있습니다.

> [!NOTE]
> 앱의 기능을 수행하는 데 필요한 만큼의 의도만 사용하는 것이 좋습니다. 너무 많은 의도를 정의하면 LUIS에서 발화를 올바르게 분류하기가 더욱 어려워집니다. 너무 적게 정의하면 너무 일반적이어서 겹칠 수 있습니다.

## <a name="create-example-utterances-for-each-intent"></a>각 의도의 예제 발화 만들기

의도를 확인한 후 각 의도에 대한 예제 발화를 10개 또는 15개를 만듭니다. 시작하려면 이 숫자보다 적으면 안 됩니다. 각 의도에 대해 많은 발화를 만듭니다. 각 발화는 이전 발화와 달라야 합니다. 발화는 전체 단어 개수, 단어 선택, 동사 시제 및 문장 부호가 다양한 것이 좋습니다. 

## <a name="identify-your-entities"></a>엔터티 식별

예제 발화에서 추출하려는 엔터티를 식별합니다. 항공편을 예약하려면 목적지, 날짜, 항공사, 티켓 범주 및 여행 클래스와 같은 몇 가지 정보가 필요합니다. 이러한 데이터 형식에 대한 엔터티를 만든 다음, 의도를 수행하는 데 중요하므로 예제 발화에서 [엔터티](luis-concept-entity-types.md)를 표시합니다. 

앱에서 사용할 엔터티를 결정할 때 개체 형식 간의 관계를 캡처하기 위한 다양한 형식의 엔터티가 있다는 점에 주의하세요. [LUIS의 엔터티](luis-concept-entity-types.md)에서는 다양한 형식에 대해 자세히 설명합니다.

## <a name="next-steps"></a>다음 단계

앱에서 엔드포인트 발화를 학습, 게시 및 가져온 후에는 [능동적 학습](luis-how-to-review-endpoint-utterances.md), [구 목록](luis-concept-feature.md) 및 [패턴](luis-concept-patterns.md)을 사용하여 예측 기능 향상을 구현하도록 계획합니다. 


* LUIS 앱을 만드는 방법에 대한 간단한 연습은 [Create your first Language Understanding Intelligent Services (LUIS) app](luis-get-started-create-app.md)(첫 번째 LUIS(Language Understanding Intelligent Services) 앱 만들기)을 참조하세요.
