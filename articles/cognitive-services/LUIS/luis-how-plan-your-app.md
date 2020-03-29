---
title: 앱 계획 - LUIS
titleSuffix: Azure Cognitive Services
description: 관련 앱 의도 및 엔터티를 간략하게 설명한 다음, LUIS(Language Understanding Intelligent Services)에서 애플리케이션 계획을 만듭니다.
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 11/20/2019
ms.author: diberry
ms.openlocfilehash: 6a155f4c43da03ccdc40d289742918973aa6da7b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "74326787"
---
# <a name="plan-your-luis-app-schema-with-subject-domain-and-data-extraction"></a>제목 도메인 및 데이터 추출을 사용하여 LUIS 앱 스키마 계획

LUIS 앱 스키마에는 주체 [도메인과](luis-glossary.md#domain)관련된 [의도](luis-glossary.md#intent) 및 [엔터티가](luis-glossary.md#entity) 포함되어 있습니다. 의도는 사용자 [발언을](luis-glossary.md#utterance)분류하고 엔터티는 사용자 발터에서 데이터를 추출합니다.

## <a name="identify-your-domain"></a>도메인 식별

LUIS 앱은 주제 도메인을 중심으로 합니다. 예를 들어 항공권, 항공편, 호텔 및 렌터카 예약을 처리하는 여행 앱이 있을 수 있습니다. 또 다른 앱에서는 운동, 피트니스 노력 추적 및 목표 설정과 관련된 콘텐츠를 제공할 수 있습니다. 도메인을 식별하면 도메인과 관련된 단어나 구를 찾을 수 있습니다.

> [!TIP]
> LUIS는 많은 일반적인 시나리오에 대해 [미리 빌드된 도메인](luis-how-to-use-prebuilt-domains.md)을 제공합니다. 미리 빌드된 도메인을 앱의 시작점으로 사용할 수 있는지 확인하세요.

## <a name="identify-your-intents"></a>의도 식별

응용 프로그램의 작업에 중요한 [의도를](luis-concept-intent.md) 생각해 보십시오.

항공편을 예약하고 사용자 목적지의 날씨를 확인하는 기능이 있는 여행 앱을 예로 들어보겠습니다. 이러한 작업에 `BookFlight` 대한 `GetWeather` 및 의도를 정의할 수 있습니다.

함수가 많은 더 복잡한 앱에서는 더 많은 의도를 가지며 의도가 너무 구체적이지 않도록 신중하게 정의해야 합니다. 예를 들어 `BookFlight` `BookHotel` 별도의 의도가 필요할 수 있지만 `BookInternationalFlight` `BookDomesticFlight` 너무 유사할 수 있습니다.

> [!NOTE]
> 앱의 기능을 수행하는 데 필요한 만큼의 의도만 사용하는 것이 좋습니다. 너무 많은 의도를 정의하면 LUIS에서 발화를 올바르게 분류하기가 더욱 어려워집니다. 너무 적은 것을 정의하는 경우 너무 일반적이어서 겹칠 수 있습니다.

전체 적인 사용자 의도를 식별할 필요가 없는 경우 의도에 모든 예제 `None` 사용자 발언을 추가합니다. 앱이 더 많은 의도를 필요로 하는 것으로 증가하는 경우 나중에 만들 수 있습니다.

## <a name="create-example-utterances-for-each-intent"></a>각 의도의 예제 발화 만들기

먼저 각 의도에 대해 너무 많은 발언을 만들지 마십시오. 의도를 결정한 후에는 의도당 15~30개의 예제 발언을 만듭니다. 각 발언은 이전에 제공된 발언과 달라야 합니다. 발언의 좋은 다양성은 전체 단어 수, 단어 선택, 동사 시제 및 문장 부호를 포함한다.

자세한 내용은 [LUIS 앱에 대한 양호한 발언 이해를](luis-concept-utterance.md)참조하십시오.

## <a name="identify-your-entities"></a>엔터티 식별

예제 발화에서 추출하려는 엔터티를 식별합니다. 항공편을 예약하려면 목적지, 날짜, 항공사, 항공권 카테고리 및 여행 클래스와 같은 정보가 필요합니다. 이러한 데이터 형식에 대한 엔터티를 만든 다음 예제 발언에 [엔터티를](luis-concept-entity-types.md) 표시합니다. 엔터티는 의도를 달성하는 데 중요합니다.

앱에서 사용할 엔터티를 결정할 때는 개체 형식 간의 관계를 캡처하기 위한 다양한 유형의 엔터티가 있습니다. [LUIS의 엔터티](luis-concept-entity-types.md)에서는 다양한 형식에 대해 자세히 설명합니다.

> [!TIP]
> LUIS는 일반적인 대화형 사용자 시나리오에 대해 [미리 빌드된 엔터티를](luis-prebuilt-entities.md) 제공합니다. 미리 빌드된 엔터티를 응용 프로그램 개발의 시작점으로 사용하는 것이 좋습니다.

## <a name="next-steps"></a>다음 단계

> [!div class="nextstepaction"]
> [루이스 개발 라이프사이클스 배우기](luis-concept-app-iteration.md)

