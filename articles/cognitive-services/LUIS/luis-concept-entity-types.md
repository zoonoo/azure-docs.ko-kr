---
title: 엔터티 형식-LUIS
titleSuffix: Azure Cognitive Services
description: 엔터티는 utterance에서 데이터를 추출 합니다. 엔터티 형식은 데이터의 예측 가능한 추출을 제공 합니다. 두 가지 유형의 엔터티가 있습니다. 컴퓨터를 학습 하 고 컴퓨터를 인식 하지 못합니다. 길이 발언에서 작업 하는 엔터티의 유형을 파악 하는 것이 중요 합니다.
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
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/13/2020
ms.locfileid: "79221518"
---
# <a name="entities-and-their-purpose-in-luis"></a>LUIS에서 엔터티 및 해당 용도

엔터티의 주요 목적은 클라이언트 응용 프로그램에서 데이터를 예측 가능한 방식으로 추출할 수 있도록 하는 것입니다. _선택적인_보조 목적은 설명자를 사용 하 여 의도 또는 다른 엔터티의 예측을 높이는 것입니다.

엔터티는 다음과 같은 두 가지 유형이 있습니다.

* 컴퓨터에서 학습-컨텍스트
* 비 기계어 학습-정확히 일치 하는 텍스트 일치, 패턴 일치 또는 미리 작성 된 엔터티에서 검색

컴퓨터에서 배운 엔터티는 가장 넓은 범위의 데이터 추출 선택 항목을 제공 합니다. 컴퓨터에서 학습 하지 않은 엔터티는 텍스트 일치를 통해 작동 하며 독립적으로 또는 컴퓨터에서 학습 한 엔터티에 대 한 [제약 조건](#design-entities-for-decomposition) 으로 사용 될 수 있습니다.

## <a name="entities-represent-data"></a>데이터를 나타내는 엔터티

엔터티는 utterance에서 가져올 데이터 (예: 이름, 날짜, 제품 이름 또는 단어의 중요 한 그룹)입니다. 발화에는 여러 엔터티가 포함되거나 전혀 포함되지 않을 수 있습니다. 클라이언트 응용 프로그램에서 작업을 수행 하는 데 필요한 데이터가 필요할 _수 있습니다_ .

모델의 각 의도에 대해 모든 학습 길이 발언 엔터티에 일관 되 게 레이블을 지정 해야 합니다.

 사용자 고유의 엔터티를 정의 하거나 미리 작성 된 엔터티를 사용 하 여 [datetimeV2](luis-reference-prebuilt-datetimev2.md), [서 수](luis-reference-prebuilt-ordinal.md), [전자 메일](luis-reference-prebuilt-email.md)및 [전화 번호](luis-reference-prebuilt-phonenumber.md)와 같은 일반적인 개념의 시간을 절약할 수 있습니다.

|발화|엔터티|data|
|--|--|--|
|Buy 3 tickets to New York|미리 빌드된 숫자<br>Location.Destination|3<br>뉴욕|
|Buy a ticket from New York to London on March 5|Location.Origin<br>Location.Destination<br>미리 빌드된 datetimeV2|뉴욕<br>London<br>March 5, 2018|

### <a name="entities-are-optional"></a>엔터티는 선택 사항입니다.

의도는 필수인 반면 엔터티는 선택 사항입니다. 앱의 모든 개념에 대해 엔터티를 만들 필요는 없지만 클라이언트 애플리케이션이 작업을 수행하는 데 필요한 개념에 대해서는 만들어야 합니다.

클라이언트 응용 프로그램에 필요한 데이터가 길이 발언에 없으면 엔터티를 추가할 필요가 없습니다. 응용 프로그램이 개발 되 고 데이터에 대 한 새로운 요구 사항이 확인 되 면 나중에 LUIS 모델에 적절 한 엔터티를 추가할 수 있습니다.

## <a name="entity-compared-to-intent"></a>엔터티 및 의도 비교

엔터티는 추출 하려는 utterance 내부의 데이터 개념을 나타냅니다.

Utterance는 엔터티를 선택적으로 포함할 수 있습니다. 비교 하 여 utterance에 대 한 의도의 예측은 _필수_ 이며 전체 utterance를 나타냅니다. LUIS에는 예제 길이 발언가 의도에 포함 되어 있어야 합니다.

다음 4 길이 발언을 고려 합니다.

|발화|예측된 의도|추출 된 엔터티|설명|
|--|--|--|--|
|도움말|help|-|추출할 항목이 없습니다.|
|항목 보내기|sendSomething|-|추출할 항목이 없습니다. 모델은이 컨텍스트에서 `something`를 추출 하도록 학습 되지 않았으므로 받는 사람이 없습니다.|
|Bob이 있는 메일 보내기|sendSomething|`Bob`, `present`|[PersonName](luis-reference-prebuilt-person.md) 미리 작성 된 엔터티를 사용 하 여 모델을 학습 한 후 `Bob`이름을 추출 했습니다. 컴퓨터에서 학습 한 엔터티를 사용 하 여 `present`을 추출 했습니다.|
|Bob에 게 빠졌습니다의 상자를 보냅니다.|sendSomething|`Bob`, `box of chocolates`|엔터티는 두 가지 중요 한 데이터, `Bob` 및 `box of chocolates`를 추출 했습니다.|

## <a name="design-entities-for-decomposition"></a>분해를 위한 엔터티 디자인

최상위 엔터티를 컴퓨터에서 학습 한 엔터티로 만들기 위한 좋은 엔터티 디자인입니다. 이를 통해 시간에 따라 엔터티 디자인을 변경 하 고, 필요 **subcomponents** 에 따라 **제약 조건** 및 **설명자**를 사용 하 여 최상위 엔터티를 클라이언트 응용 프로그램에 필요한 부분으로 분해할 수 있습니다.

분해를 위한 디자인을 통해 LUIS은 클라이언트 응용 프로그램에 깊이 있는 엔터티 해상도를 반환할 수 있습니다. 이렇게 하면 클라이언트 응용 프로그램이 비즈니스 규칙에 집중 하 고 데이터 확인을 LUIS로 유지할 수 있습니다.

### <a name="machine-learned-entities-are-primary-data-collections"></a>컴퓨터에서 학습 한 엔터티는 주 데이터 컬렉션입니다.

[**컴퓨터에서 배운 엔터티**](tutorial-machine-learned-entity.md) 는 최상위 데이터 단위입니다. 하위 구성 요소는 컴퓨터에서 학습 한 엔터티의 자식 엔터티입니다.

학습 길이 발언을 통해 학습 된 컨텍스트를 기반으로 하는 컴퓨터에서 배운 엔터티 트리거입니다. **제약 조건은** [목록](reference-entity-list.md) 또는 [Regex](reference-entity-regular-expression.md)와 같이 기계어에서 학습 하지 않은 엔터티의 정확한 텍스트 일치 정의에 따라 트리거를 추가로 제한 하는 컴퓨터에서 학습 한 엔터티에 적용 되는 선택적 규칙입니다. 예를 들어 `size` 컴퓨터에서 배운 엔터티는 `sizeList` 엔터티 내에 포함 된 값이 발견 되는 경우에만 트리거되도록 `size` 엔터티를 제한 하는 `sizeList` 목록 엔터티의 제약 조건을 가질 수 있습니다.

[**설명자**](luis-concept-feature.md) 는 예측이 적용 되는 단어 또는 구의 관련성을 높이기 위해 적용 되는 기능입니다. 용도 또는 엔터티를 *설명* 하는 데 사용 되므로 *설명자를 설명자* 라고 합니다. 설명자는 LUIS에서 관찰 하 고 학습 하는 중요 한 단어 또는 구와 같은 데이터의 특징이 나 특성을 설명 합니다.

LUIS 앱에서 구 목록 기능을 만들 때 기본적으로 전역으로 설정 되 고 모든 의도 및 엔터티에 균등 하 게 적용 됩니다. 그러나 컴퓨터에서 학습 한 엔터티 (또는 *모델*)의 설명자 (기능)로 문구 목록을 적용 하는 경우 해당 범위는 해당 모델에만 적용 되 고 다른 모든 모델에는 더 이상 사용 되지 않습니다. 모델에 대 한 설명자로 문구 목록을 사용 하면 적용 되는 모델에 대 한 정확도를 지 원하는 방식으로 분해 됩니다.

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
|[**컴퓨터-학습**](tutorial-machine-learned-entity.md)|컴퓨터에서 배운 엔터티는 utterance의 컨텍스트에서 학습 합니다. 엔터티 형식에 관계 없이 엔터티의 부모 그룹입니다. 이렇게 하면 예 길이 발언에서 배치의 변형이 중요 합니다. |
|[**목록**](reference-entity-list.md)|**정확히 일치**하는 텍스트를 사용 하 여 추출 된 항목 및 해당 동의어의 목록입니다.|
|[**Pattern.any**](reference-entity-pattern-any.md)|엔터티의 끝을 확인하기 어려운 엔터티 |
|[**미리 빌드됨**](luis-reference-prebuilt-entities.md)|URL, 전자 메일 등의 특정 종류의 데이터를 추출 하도록 이미 학습 되었습니다. 이러한 미리 빌드된 엔터티 중 일부는 오픈 소스 [Recognizers-Text](https://github.com/Microsoft/Recognizers-Text) 프로젝트에 정의되어 있습니다. 특정 문화권이나 엔터티가 현재 지원되지 않은 경우 프로젝트에 적용됩니다.|
|[**정규식**](reference-entity-regular-expression.md)|**정확히 일치**하는 텍스트를 위해 정규식을 사용 합니다.|

## <a name="extracting-contextually-related-data"></a>컨텍스트 관련 데이터 추출

Utterance에는 데이터의 의미가 utterance 내의 컨텍스트를 기반으로 하는 두 개 이상의 엔터티가 포함 될 수 있습니다. 예는 원본 및 대상의 두 위치를 포함 하는 비행을 예약 하기 위한 utterance입니다.

`Book a flight from Seattle to Cairo`

`location` 엔터티의 두 예제를 추출 해야 합니다. 클라이언트 응용 프로그램은 티켓 구매를 완료 하기 위해 각각의 위치 유형을 알고 있어야 합니다.

컨텍스트 관련 데이터를 추출 하는 방법에는 두 가지가 있습니다.

 * `location` 엔터티는 컴퓨터에서 학습 한 엔터티 이며 두 개의 구성 요소 엔터티를 사용 하 여 `origin` 및 `destination` (기본 설정)를 캡처합니다.
 * `location` 엔터티는 `origin` 및의 두 **역할** 을 사용 `destination`

여러 엔터티가 utterance에 존재할 수 있으며, 사용 되는 컨텍스트에 의미가 없는 경우 분해 또는 역할을 사용 하지 않고 추출할 수 있습니다. 예를 들어 utterance에 `I want to travel to Seattle, Cairo, and London.`위치 목록이 포함 된 경우이 목록은 각 항목에 추가 의미가 없는 목록입니다.

### <a name="using-subcomponent-entities-of-a-machine-learned-entity-to-define-context"></a>컴퓨터에서 학습 한 엔터티의 하위 구성 요소 엔터티를 사용 하 여 컨텍스트 정의

컴퓨터에서 학습 한 [**엔터티**](tutorial-machine-learned-entity.md) 를 사용 하 여 비행 예약 작업을 설명 하는 데이터를 추출한 다음 클라이언트 응용 프로그램에 필요한 개별 부분으로 최상위 엔터티를 분해할 수 있습니다.

이 예제에서 `Book a flight from Seattle to Cairo`최상위 엔터티를 `travelAction` 하 고 `flight from Seattle to Cairo`를 추출 하도록 레이블을 지정할 수 있습니다. 그런 다음 `origin` 및 `destination`라는 두 개의 하위 구성 요소가 만들어지고 미리 작성 된 `geographyV2` 엔터티에 적용 된 제약 조건이 적용 됩니다. 학습 길이 발언 `origin` 및 `destination`에 적절 하 게 레이블이 지정 됩니다.

### <a name="using-entity-role-to-define-context"></a>엔터티 역할을 사용 하 여 컨텍스트 정의

역할은 utterance 내의 컨텍스트를 기반으로 하는 엔터티의 명명 된 별칭입니다. 역할은 미리 작성된 엔터티 유형 또는 사용자 지정 엔터티 유형에 사용할 수 있으며, 두 예제 발화 및 패턴 모두에서 사용할 수 있습니다. 이 예제에서 `location` 엔터티에는 `origin` 및 `destination`의 두 역할이 필요 하며,이 둘은 예제 길이 발언에 표시 되어야 합니다.

LUIS가 `location`를 찾았지만 역할을 확인할 수 없는 경우에도 위치 엔터티가 반환 됩니다. 클라이언트 응용 프로그램은 사용자가 지정 하는 위치 유형을 확인 하기 위해 질문을 해야 합니다.


## <a name="if-you-need-more-than-the-maximum-number-of-entities"></a>최대 엔터티 수보다 더 많이 필요한 경우

제한 보다 더 많이 필요한 경우 지원 담당자에 게 문의 하세요. 이렇게 하려면 시스템에 대한 자세한 정보를 수집하고 [LUIS](luis-reference-regions.md#luis-website) 웹 사이트로 이동한 다음, **지원**을 선택하세요. Azure 구독에 지원 서비스가 포함된 경우, [Azure 기술 지원](https://azure.microsoft.com/support/options/)에 문의하세요.

## <a name="entity-prediction-status"></a>엔터티 예측 상태

LUIS 포털에서는 utterance 예의 엔터티에 선택한 엔터티와 다른 엔터티 예측이 있는 경우를 보여 줍니다. 이 다른 점수는 현재 학습 된 모델을 기반으로 합니다.

## <a name="next-steps"></a>다음 단계

좋은 [발화](luis-concept-utterance.md)에 대한 개념을 알아보세요.

LUIS 앱에 엔터티를 추가하는 방법에 대한 자세한 내용은 [엔터티 추가](luis-how-to-add-entities.md)를 참조하세요.

컴퓨터에서 학습 한 엔터티를 사용 하 여 utterance에서 구조화 된 데이터를 추출 하는 방법에 대 한 자세한 내용은 [자습서: utterance에서 컴퓨터에서 배운 Language Understanding 엔터티를 사용 하 여 사용자에서 구조화 된 데이터 추출](tutorial-machine-learned-entity.md) 을 참조 하세요.
 
