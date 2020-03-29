---
title: 엔터티 유형 - LUIS
titleSuffix: Azure Cognitive Services
description: 엔터티는 발화에서 데이터를 추출합니다. 엔터티 형식은 예측 가능한 데이터 추출을 제공합니다. 엔터티에는 기계 학습 및 비학습 엔터티의 두 가지 유형이 있습니다. 발언에서 작업하는 엔터티 유형을 아는 것이 중요합니다.
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 11/12/2019
ms.author: diberry
ms.openlocfilehash: 6ee156efb5512c92d86ba05513b6a2b91df4eae8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79221518"
---
# <a name="entities-and-their-purpose-in-luis"></a>LUIS의 엔터티 및 목적

엔터티의 주요 목적은 클라이언트 응용 프로그램에 예측 가능한 데이터 추출을 제공하는 것입니다. _선택적_보조 목적은 설명자가 있는 의도 또는 다른 엔터티의 예측을 향상시키는 것입니다.

엔터티에는 두 가지 유형이 있습니다.

* 기계 학습 - 컨텍스트에서
* 기계 학습되지 않은 - 미리 빌드된 엔터티에 의한 정확한 텍스트 일치, 패턴 일치 또는 감지용

기계 학습 엔터티는 가장 광범위한 데이터 추출 선택을 제공합니다. 기계 학습되지 않은 엔터티는 텍스트 일치에 의해 작동하며 독립적으로 또는 기계 학습 엔터티의 [제약 조건으로](#design-entities-for-decomposition) 사용될 수 있습니다.

## <a name="entities-represent-data"></a>데이터를 나타내는 엔터티

엔터티는 이름, 날짜, 제품 이름 또는 중요한 단어 그룹과 같은 발언에서 가져오려는 데이터입니다. 발화에는 여러 엔터티가 포함되거나 전혀 포함되지 않을 수 있습니다. 클라이언트 응용 프로그램은 해당 작업을 수행하기 위해 데이터가 필요할 _수 있습니다._

엔터티는 모델의 각 의도에 대한 모든 학습 발언에 대해 일관되게 레이블을 지정해야 합니다.

 사용자 고유의 엔터티를 정의하거나 미리 빌드된 엔터티를 사용하여 [datetimeV2,](luis-reference-prebuilt-datetimev2.md) [서수,](luis-reference-prebuilt-ordinal.md) [전자 메일](luis-reference-prebuilt-email.md)및 전화 [번호와](luis-reference-prebuilt-phonenumber.md)같은 일반적인 개념에 대한 시간을 절약할 수 있습니다.

|발화|엔터티|데이터|
|--|--|--|
|Buy 3 tickets to New York|미리 빌드된 숫자<br>Location.Destination|3<br>뉴욕|
|Buy a ticket from New York to London on March 5|Location.Origin<br>Location.Destination<br>미리 빌드된 datetimeV2|뉴욕<br>London<br>March 5, 2018|

### <a name="entities-are-optional"></a>엔터티는 선택 사항입니다.

의도는 필수인 반면 엔터티는 선택 사항입니다. 앱의 모든 개념에 대해 엔터티를 만들 필요는 없지만 클라이언트 애플리케이션이 작업을 수행하는 데 필요한 개념에 대해서는 만들어야 합니다.

발언에 클라이언트 응용 프로그램에 필요한 데이터가 없는 경우 엔터티를 추가할 필요가 없습니다. 응용 프로그램이 개발되고 데이터에 대한 새로운 필요성이 확인되면 나중에 LUIS 모델에 적절한 엔터티를 추가할 수 있습니다.

## <a name="entity-compared-to-intent"></a>엔터티 및 의도 비교

엔터티는 추출하려는 발언 내부의 데이터 개념을 나타냅니다.

발언에는 선택적으로 엔터티가 포함될 수 있습니다. 이에 비해 발언 의도를 예측하는 것은 _필요하며_ 전체 발언을 나타냅니다. LUIS는 의도에 포함된 예제 발언이 필요합니다.

다음 4개의 발언을 고려하십시오.

|발화|예측된 의도|추출된 엔터티|설명|
|--|--|--|--|
|도움말|help|-|추출할 것이 없습니다.|
|뭔가 보내기|보내기뭔가|-|추출할 것이 없습니다. 이 컨텍스트에서 추출하도록 `something` 모델이 학습되지 않았으며 받는 사람도 없습니다.|
|밥에게 선물 보내기|보내기뭔가|`Bob`, `present`|모델은 이름을 추출한 미리 빌드된 엔터티를 [사용자이름으로](luis-reference-prebuilt-person.md) `Bob`학습했습니다. 에서 학습한 엔터티를 사용하여 `present`을 추출했습니다.|
|밥에게 초콜릿 상자 보내기|보내기뭔가|`Bob`, `box of chocolates`|의 두 가지 중요한 `Bob` 데이터 `box of chocolates`조각과 는 엔터티에 의해 추출되었습니다.|

## <a name="design-entities-for-decomposition"></a>분해를 위한 설계 엔티티

최상위 엔터티를 기계 학습 엔터티로 만드는 것이 좋습니다. 이렇게 하면 시간이 지남에 따라 엔터티 디자인을 변경하고 **선택적으로 제약 조건** 및 **설명자가**있는 **하위 구성 요소(하위** 엔터티)를 사용하여 최상위 엔터티를 클라이언트 응용 프로그램에 필요한 부분으로 분해할 수 있습니다.

분해를 위해 설계하면 LUIS가 클라이언트 응용 프로그램에 대한 심층적인 엔터티 확인을 반환할 수 있습니다. 이렇게 하면 클라이언트 응용 프로그램이 비즈니스 규칙에 집중하고 데이터 확인을 LUIS에 맡길 수 있습니다.

### <a name="machine-learned-entities-are-primary-data-collections"></a>기계 학습 엔터티는 기본 데이터 컬렉션입니다.

[**기계 학습 엔터티는**](tutorial-machine-learned-entity.md) 최상위 데이터 단위입니다. 하위 구성 요소는 기계 학습 엔터티의 하위 엔터티입니다.

컴퓨터 학습 엔터티는 학습 발언을 통해 학습된 컨텍스트에 따라 트리거됩니다. **제약 조건은** [목록](reference-entity-list.md) 또는 [정규고와](reference-entity-regular-expression.md)같이 기계에서 학습하지 않은 엔터티의 정확한 텍스트 일치 정의에 따라 트리거링을 추가로 제한하는 기계 학습 엔터티에 적용되는 선택적 규칙입니다. 예를 `size` 들어, 기계 학습 된 엔터티는 `sizeList` `size` 엔터티 내에 포함 된 값이 발생 하는 경우에 `sizeList` 트리거 하도록 엔터티를 제한 하는 목록 엔터티의 제약 조건이 있을 수 있습니다.

[**설명자는**](luis-concept-feature.md) 예측에 대한 단어 나 구의 관련성을 높이기 위해 적용 된 기능입니다. 의도 또는 엔터티를 *설명하는* 데 사용되기 때문에 *설명자라고* 합니다. 설명자는 LUIS가 관찰하고 학습하는 중요한 단어나 구와 같이 데이터의 특성이나 특성을 구별하는 방법을 설명합니다.

LUIS 앱에서 구 목록 기능을 만들면 기본적으로 전역적으로 활성화되며 모든 의도 및 엔터티에 균등하게 적용됩니다. 그러나 구 목록을 기계 학습 엔터티(또는 *모델)의*설명자(기능)로 적용하면 해당 범위가 해당 모델에만 적용되도록 줄어들고 다른 모든 모델과 함께 더 이상 사용되지 않습니다. 구 목록을 모델에 대한 설명자로 사용하면 적용된 모델의 정확도를 지원하여 분해하는 데 도움이 됩니다.

<a name="composite-entity"></a>
<a name="list-entity"></a>
<a name="patternany-entity"></a>
<a name="prebuilt-entity"></a>
<a name="regular-expression-entity"></a>
<a name="simple-entity"></a>

## <a name="types-of-entities"></a>엔터티의 형식

데이터를 추출해야 하는 방법 및 추출된 후에 데이터를 표시하는 방법에 따라 엔터티를 선택합니다.

|엔터티 유형|목적|
|--|--|
|[**기계 학습**](tutorial-machine-learned-entity.md)|기계 학습 엔터티는 발언의 컨텍스트에서 학습합니다. 엔터티 유형에 관계없이 엔터티의 상위 그룹화입니다. 이렇게 하면 예제 발언에서 배치 변형이 크게 됩니다. |
|[**목록**](reference-entity-list.md)|항목 및 **정확한 텍스트 일치로**추출된 동의어 목록입니다.|
|[**패턴.any**](reference-entity-pattern-any.md)|엔터티의 끝을 확인하기 어려운 엔터티 |
|[**미리 빌드됨**](luis-reference-prebuilt-entities.md)|URL 또는 전자 메일과 같은 특정 종류의 데이터를 추출하도록 이미 교육을 받은 경우 이러한 미리 빌드된 엔터티 중 일부는 오픈 소스 [Recognizers-Text](https://github.com/Microsoft/Recognizers-Text) 프로젝트에 정의되어 있습니다. 특정 문화권이나 엔터티가 현재 지원되지 않은 경우 프로젝트에 적용됩니다.|
|[**정규식**](reference-entity-regular-expression.md)|정확한 텍스트 **일치에**정규식을 사용합니다.|

## <a name="extracting-contextually-related-data"></a>컨텍스트 관련 데이터 추출

발언에는 데이터의 의미가 발언 내의 컨텍스트를 기반으로 하는 엔터티의 두 개 이상의 발생이 포함될 수 있습니다. 예를 들어 출발지와 도착지의 두 위치가 있는 항공편을 예약하기 위한 발언입니다.

`Book a flight from Seattle to Cairo`

`location` 엔터티의 두 예제를 추출해야 합니다. 티켓 구매를 완료하려면 클라이언트 응용 프로그램이 각 사용자의 위치 유형을 알아야 합니다.

컨텍스트 관련 데이터를 추출하는 두 가지 기술이 있습니다.

 * 엔터티는 `location` 기계 학습 엔터티이며 두 개의 하위 구성 `origin` `destination` 요소 엔터티를 사용하여 엔터티및(기본 설정)을 캡처합니다.
 * 엔터티는 `location` 두 가지 `origin` **역할을** 사용합니다.`destination`

여러 엔터티가 발언에 존재할 수 있으며 사용되는 컨텍스트에 아무런 의미가 없는 경우 분해 또는 역할을 사용하지 않고 추출할 수 있습니다. 예를 들어 발언에 위치 목록이 포함된 경우 `I want to travel to Seattle, Cairo, and London.`이 목록은 각 항목에 추가 의미가 없는 목록입니다.

### <a name="using-subcomponent-entities-of-a-machine-learned-entity-to-define-context"></a>기계 학습 된 엔터티의 하위 구성 요소 엔터티를 사용하여 컨텍스트 정의

기계 학습 [**엔터티를**](tutorial-machine-learned-entity.md) 사용하여 항공편 예약 작업을 설명하는 데이터를 추출한 다음 최상위 엔터티를 클라이언트 응용 프로그램에 필요한 별도의 부분으로 분해할 수 있습니다.

이 예제에서는 `Book a flight from Seattle to Cairo`최상위 엔터티를 추출하기 `travelAction` `flight from Seattle to Cairo`위해 레이블을 지정할 수 있습니다. 그런 다음 미리 빌드된 `origin` `destination` `geographyV2` 엔터티에 적용된 제약 조건이 적용된 두 하위 구성 요소 엔터티가 만들어집니다. 트레이닝 발언에서, `origin` 적절하게 `destination` 레이블이 지정된다.

### <a name="using-entity-role-to-define-context"></a>엔터티 역할을 사용하여 컨텍스트 정의

역할은 발언 내의 컨텍스트를 기반으로 하는 엔터티에 대한 명명된 별칭입니다. 역할은 미리 작성된 엔터티 유형 또는 사용자 지정 엔터티 유형에 사용할 수 있으며, 두 예제 발화 및 패턴 모두에서 사용할 수 있습니다. 이 예제에서 `location` 엔터티에는 두 `origin` 가지 `destination` 역할이 필요하며 둘 다 예제 발언에 표시되어야 합니다.

LUIS가 `location` 역할을 찾지만 역할을 확인할 수 없는 경우 위치 엔터티는 계속 반환됩니다. 클라이언트 응용 프로그램은 사용자가 의미하는 위치 유형을 결정하기 위해 질문에 대한 후속 조치를 취해야 합니다.


## <a name="if-you-need-more-than-the-maximum-number-of-entities"></a>최대 엔터티 수보다 더 많이 필요한 경우

한도 이상이 필요한 경우 지원팀에 문의하십시오. 이렇게 하려면 시스템에 대한 자세한 정보를 수집하고 [LUIS](luis-reference-regions.md#luis-website) 웹 사이트로 이동한 다음, **지원**을 선택하세요. Azure 구독에 지원 서비스가 포함된 경우, [Azure 기술 지원](https://azure.microsoft.com/support/options/)에 문의하세요.

## <a name="entity-prediction-status"></a>엔터티 예측 상태

LUIS 포털은 예제 발언에서 엔터티에 선택한 엔터티와 다른 엔터티 예측이 있는 경우를 표시합니다. 이 다른 점수는 현재 학습된 모델을 기반으로 합니다.

## <a name="next-steps"></a>다음 단계

좋은 [발화](luis-concept-utterance.md)에 대한 개념을 알아보세요.

LUIS 앱에 엔터티를 추가하는 방법에 대한 자세한 내용은 [엔터티 추가](luis-how-to-add-entities.md)를 참조하세요.

[자습서 참조: 언어 이해(LUIS)에서 기계 학습 엔터티를 사용하여 사용자 발언에서 구조화된 데이터를 추출하여](tutorial-machine-learned-entity.md) 기계 학습 엔터티를 사용하여 발언에서 구조화된 데이터를 추출하는 방법을 알아봅니다.
 
