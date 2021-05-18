---
title: 엔터티 형식 - LUIS
description: 엔터티는 예측 런타임에 사용자 발화에서 데이터를 추출합니다. 엔터티의 _선택적인_ 보조 목적은 엔터티를 기능으로 사용하여 의도 또는 다른 엔터티의 예측을 높이는 것입니다.
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 08/06/2020
ms.openlocfilehash: 398d18642052726af4d4920443bad515ec0b5bef
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/30/2021
ms.locfileid: "91316565"
---
# <a name="extract-data-with-entities"></a>엔터티를 사용하여 데이터 추출

엔터티는 예측 런타임에 사용자 발화에서 데이터를 추출합니다. 엔터티의 _선택적인_ 보조 목적은 엔터티를 기능으로 사용하여 의도 또는 다른 엔터티의 예측을 높이는 것입니다.

엔터티에는 몇 가지 형식이 있습니다.

* [기계 학습 엔터티](reference-entity-machine-learned-entity.md) - 기본 엔터티입니다. 다른 엔터티를 사용하기 전에 이 엔터티 형식으로 스키마를 디자인해야 합니다.
* 필수 [기능](luis-concept-feature.md)으로 사용되는 비기계 학습 엔터티 - 정확한 텍스트 일치, 패턴 일치 또는 미리 빌드된 엔터티에 의한 검색에 사용됩니다.
* [Pattern.any](#patternany-entity) - [패턴](reference-entity-pattern-any.md)에서 책 제목과 같은 자유 형식 텍스트를 추출하는 데 사용됩니다.

기계 학습 엔터티는 가장 넓은 범위의 데이터 추출 선택 사항을 제공합니다. 비기계 학습 엔터티는 텍스트 일치를 통해 작동하며 기계 학습 엔터티 또는 의도에 대한 [필수 기능](#design-entities-for-decomposition)으로 사용됩니다.

## <a name="entities-represent-data"></a>데이터를 나타내는 엔터티

엔터티는 이름, 날짜, 제품 이름 또는 모든 중요한 단어 그룹 등 발화에서 끌어오려는 데이터입니다. 발화에는 여러 엔터티가 포함되거나 전혀 포함되지 않을 수 있습니다. 클라이언트 애플리케이션에서 작업을 수행하기 위해 데이터를 필요로 _할 수 있습니다_.

엔터티의 레이블은 모델의 각 의도에 대한 모든 학습 발화에 걸쳐 일관되게 지정되어야 합니다.

 사용자 고유의 엔터티를 정의하거나 미리 빌드된 엔터티를 사용하여 [datetimeV2](luis-reference-prebuilt-datetimev2.md), [서수](luis-reference-prebuilt-ordinal.md), [이메일](luis-reference-prebuilt-email.md) 및 [전화 번호](luis-reference-prebuilt-phonenumber.md)와 같은 일반적인 개념에 대한 시간을 절약할 수 있습니다.

|발화|엔터티|데이터|
|--|--|--|
|Buy 3 tickets to New York|미리 빌드된 숫자<br>대상|3<br>뉴욕|


### <a name="entities-are-optional-but-recommended"></a>선택 사항이지만 권장되는 엔터티

[의도](luis-concept-intent.md)는 필수인 반면 엔터티는 선택 사항입니다. 앱의 모든 개념에 대한 엔터티를 만들 필요는 없지만, 클라이언트 애플리케이션에 데이터가 필요하거나 엔터티가 다른 엔터티 또는 의도에 대한 힌트 또는 신호 역할을 하는 개념에 대한 엔터티는 만듭니다.

애플리케이션이 개발되고 데이터에 대한 새로운 요구 사항이 확인되면 나중에 LUIS 모델에 적절한 엔터티를 추가할 수 있습니다.

<a name="entity-compared-to-intent"></a>

## <a name="entity-represents-data-extraction"></a>데이터 추출을 나타내는 엔터티

엔터티는 _발화 내의_ 데이터 개념을 나타냅니다. 의도는 _전체 발화_ 를 분류합니다.

다음 4개의 발화를 고려해 보세요.

|발화|예측된 의도|추출된 엔터티|설명|
|--|--|--|--|
|도움말|help|-|추출할 항목이 없습니다.|
|Send something|sendSomething|-|추출할 항목이 없습니다. 모델에는 이 컨텍스트에서 `something`을 추출하는 데 필요한 기능이 없으며 받는 사람이 언급되지 않습니다.|
|Send Bob a present|sendSomething|`Bob`, `present`|모델은 미리 빌드된 엔터티 `personName`의 필수 기능을 추가하여 `Bob`을 추출합니다. `present`를 추출하는 데 기계 학습 엔터티가 사용되었습니다.|
|Send Bob a box of chocolates|sendSomething|`Bob`, `box of chocolates`|두 개의 중요한 데이터 `Bob`과 `box of chocolates`가 기계 학습 엔터티에 의해 추출됩니다.|

## <a name="label-entities-in-all-intents"></a>모든 의도에서 엔터티 레이블 지정

엔터티는 예측 의도에 관계없이 데이터를 추출합니다. 모든 의도의 _모든_ 예제 발화에 레이블을 지정해야 합니다. 엔터티 레이블 지정이 누락된 `None` 의도는 다른 의도에 대한 훨씬 더 많은 학습 발화가 있는 경우에도 혼동을 일으킵니다.

## <a name="design-entities-for-decomposition"></a>분해를 위한 엔터티 디자인

기계 학습 엔터티를 사용하면 분해를 위해 앱 스키마를 디자인하여 큰 개념을 하위 엔터티로 나눌 수 있습니다.

분해를 위한 디자인을 통해 LUIS는 클라이언트 애플리케이션에 대한 심층적 엔터티 확인을 반환할 수 있습니다. 이렇게 하면 클라이언트 애플리케이션에서 비즈니스 규칙을 중심으로 LUIS에 대한 데이터 확인을 유지할 수 있습니다.

기계 학습 엔터티는 예제 발화를 통해 학습된 컨텍스트를 기반으로 트리거됩니다.

[**기계 학습 엔터티**](tutorial-machine-learned-entity.md)는 최상위 수준의 추출기입니다. 하위 엔터티는 기계 학습 엔터티의 자식 엔터티입니다.

## <a name="effective-machine-learned-entities"></a>효과적인 기계 학습 엔터티

기계 학습 엔터티를 효과적으로 빌드하려면:

* 레이블 지정은 전체 의도에서 일관되어야 합니다. 여기에는 이 엔터티를 포함하는 **None** 의도에서 제공하는 발화도 포함됩니다. 그렇지 않으면 모델에서 시퀀스를 효과적으로 확인할 수 없습니다.
* 하위 엔터티가 포함된 기계 학습 엔터티가 있는 경우 엔터티 및 하위 엔터티의 다른 주문 및 변형이 레이블이 지정된 발화에 표시되는지 확인합니다. 레이블이 지정된 예제 발화는 유효한 모든 형태를 포함하며 발화 내에 표시되는 엔터티와 존재하지 않고 재배열된 엔터티도 포함해야 합니다.
* 엔터티를 매우 고정된 집합에 과잉 맞춤하는 것을 피해야 합니다. **과잉 맞춤** 은 모델을 일반화하지 않은 경우에 발생하며 기계 학습 모델의 일반적인 문제입니다. 이는 앱이 새 데이터에 적절하게 작동하지 않음을 의미합니다. 또한 앱이 사용자가 제공하는 제한된 예를 넘어 일반화할 수 있도록 레이블이 지정된 예제 발화를 변경해야 합니다. 표시된 예뿐만 아니라 더 많은 개념을 고려하기 위해 모델에 대한 변경 내용이 충분한 하위 엔터티가 다양해야 합니다.

## <a name="effective-prebuilt-entities"></a>미리 빌드된 효과적인 엔터티

[미리 빌드된 엔터티](luis-reference-prebuilt-entities.md)에서 제공하는 것과 같이 일반적인 데이터를 추출하는 효과적인 엔터티를 빌드하려면 다음 프로세스를 수행하는 것이 좋습니다.

사용자 고유의 데이터를 기능으로 엔터티에 가져오는 방법으로 데이터 추출을 향상시킵니다. 이렇게 하면 데이터의 모든 추가 레이블이 사용자 이름이 애플리케이션에 존재하는 컨텍스트를 학습합니다.

<a name="composite-entity"></a>
<a name="list-entity"></a>
<a name="patternany-entity"></a>
<a name="prebuilt-entity"></a>
<a name="regular-expression-entity"></a>
<a name="simple-entity"></a>

## <a name="types-of-entities"></a>엔터티의 형식

부모에 대한 하위 엔터티는 기계 학습 엔터티여야 합니다. 하위 엔터티는 비-기계 학습 엔터티를 [기능](luis-concept-feature.md)으로 사용할 수 있습니다.

데이터를 추출해야 하는 방법 및 추출된 후에 데이터를 표시하는 방법에 따라 엔터티를 선택합니다.

|엔터티 유형|목적|
|--|--|
|[**기계 학습**](tutorial-machine-learned-entity.md)|레이블이 지정된 예에서 학습된 복잡한 중첩 데이터를 추출합니다. |
|[**목록**](reference-entity-list.md)|**정확한 텍스트 일치 항목** 을 사용하여 추출된 항목 및 동의어 목록입니다.|
|[**Pattern.any**](#patternany-entity)|엔터티는 자유 형식이므로 엔터티를 확인하기 위해 엔터티의 끝을 찾는 것은 어렵습니다. [패턴](luis-concept-patterns.md)에서만 사용 가능합니다.|
|[**미리 빌드됨**](luis-reference-prebuilt-entities.md)|URL이나 이메일 등 특정 종류의 데이터를 추출하도록 이미 학습되었습니다. 이러한 미리 빌드된 엔터티 중 일부는 오픈 소스 [Recognizers-Text](https://github.com/Microsoft/Recognizers-Text) 프로젝트에 정의되어 있습니다. 특정 문화권이나 엔터티가 현재 지원되지 않은 경우 프로젝트에 적용됩니다.|
|[**정규식**](reference-entity-regular-expression.md)|**정확한 텍스트 일치** 를 위해 정규식을 사용합니다.|


## <a name="extraction-versus-resolution"></a>추출 및 해결

엔터티는 데이터가 발화에 표시될 때 데이터를 추출합니다. 엔터티는 데이터를 변경하거나 해결하지 않습니다. 텍스트가 엔터티에 대한 유효한 값이든 아니든 엔터티는 어떤 해결 방법도 제공하지 않습니다.

추출에 대한 해결을 제공할 수는 있지만 이로 인해 변형 및 실수에 대해 영향을 받지 않도록 앱의 기능이 제한됩니다.

목록 엔터티 및 정규식(텍스트 일치) 엔터티는 추출에 대한 필터 역할을 하는 하위 엔터티에 대한 [필수 기능](luis-concept-feature.md#required-features)으로 사용될 수 있습니다. 앱의 예측 기능을 방해하지 않도록 신중하게 사용해야 합니다.

## <a name="extracting-contextually-related-data"></a>컨텍스트 관련 데이터 추출

발화에는 데이터의 의미가 발화 내의 컨텍스트를 기반으로 하는 두 개 이상의 엔터티가 포함될 수 있습니다. 예를 들어 출발지와 도착지라는 두 개의 지리적 위치가 포함된 항공편을 예약하는 발화가 있습니다.

`Book a flight from Seattle to Cairo`

티켓 구매를 완료하기 위해 클라이언트 애플리케이션에서 각 위치의 유형을 알고 있는 방식으로 두 위치를 추출해야 합니다.

출발지와 도착지를 추출하려면 티켓 주문 기계 학습 엔터티에 포함된 두 개의 하위 엔터티를 만듭니다. 각 하위 엔터티에 대해 geographyV2를 사용하는 필수 기능을 만듭니다.

<a name="using-component-constraints-to-help-define-entity"></a>
<a name="using-subentity-constraints-to-help-define-entity"></a>

### <a name="using-required-features-to-constrain-entities"></a>필수 기능을 사용하여 엔터티 제한

[필수 기능](luis-concept-feature.md)에 대해 자세히 알아보기

## <a name="patternany-entity"></a>Pattern.any 엔터티

Pattern.any는 [패턴](luis-concept-patterns.md)에서만 사용할 수 있습니다.

<a name="if-you-need-more-than-the-maximum-number-of-entities"></a>
## <a name="exceeding-app-limits-for-entities"></a>엔터티에 대한 앱 제한 초과

[제한](luis-limits.md#model-limits)보다 더 많이 필요한 경우 지원에 문의하세요. 이렇게 하려면 시스템에 대한 자세한 정보를 수집하고 [LUIS](luis-reference-regions.md#luis-website) 웹 사이트로 이동한 다음, **지원** 을 선택하세요. Azure 구독에 지원 서비스가 포함된 경우, [Azure 기술 지원](https://azure.microsoft.com/support/options/)에 문의하세요.

## <a name="entity-prediction-status-and-errors"></a>엔터티 예측 상태 및 오류

LUIS 포털은 예제 발화에 대해 선택한 엔터티와 다른 엔터티 예측이 엔터티에 있는 경우를 표시합니다. 이 다른 점수는 현재 학습된 모델을 기반으로 합니다. 

:::image type="content" source="./media/luis-concept-entities/portal-entity-prediction-error.png" alt-text="LUIS 포털은 예제 발화에 대해 선택한 엔터티와 다른 엔터티 예측이 엔터티에 있는 경우를 표시합니다.":::

오류 텍스트가 예제 발화 내에서 강조 표시되고 예제 발화 행의 오른쪽에는 빨간색 삼각형으로 표시된 오류 표시기가 있습니다. 

이 정보를 사용하여 다음 중 하나 이상을 사용해 엔터티 오류를 해결할 수 있습니다.
* 강조 표시된 텍스트의 레이블이 잘못 지정되어 있습니다. 해결하려면 검토하고 수정하고 다시 학습합니다. 
* 엔터티의 개념을 식별할 수 있도록 엔터티에 대한 [기능](luis-concept-feature.md)을 만듭니다.
* [예제 발화](luis-concept-utterance.md)를 추가하고 엔터티에 레이블을 지정합니다.
* 엔터티의 개념을 식별하는 데 도움이 될 수 있는 예측 엔드포인트에서 수신한 모든 발화에 대해 [활성 학습 제안을 검토](luis-concept-review-endpoint-utterances.md)합니다.

## <a name="next-steps"></a>다음 단계

좋은 [발화](luis-concept-utterance.md)에 대한 개념을 알아보세요.

LUIS 앱에 엔터티를 추가하는 방법에 대한 자세한 내용은 [엔터티 추가](luis-how-to-add-entities.md)를 참조하세요.

기계 학습 엔터티를 사용하여 발화에서 구조화된 데이터를 추출하는 방법에 대해 알아보려면 [자습서: LUIS(Language Understanding)에서 기계 학습 엔터티를 사용하여 사용자 발화에서 구조화된 데이터 추출](tutorial-machine-learned-entity.md)을 참조하세요.

