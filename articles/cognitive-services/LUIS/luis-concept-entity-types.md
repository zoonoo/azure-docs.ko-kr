---
title: 엔터티 형식-LUIS
description: 엔터티는 예측 런타임에 사용자 utterance에서 데이터를 추출 합니다. _선택적인_보조 목적은 엔터티를 기능으로 사용 하 여 의도 또는 다른 엔터티의 예측을 높이는 것입니다.
ms.topic: conceptual
ms.date: 04/30/2020
ms.openlocfilehash: 9d8afd5a660b3af5556256835486e984d7d657bc
ms.sourcegitcommit: bb0afd0df5563cc53f76a642fd8fc709e366568b
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/19/2020
ms.locfileid: "83585643"
---
# <a name="extract-data-with-entities"></a>엔터티를 사용 하 여 데이터 추출

엔터티는 예측 런타임에 사용자 utterance에서 데이터를 추출 합니다. _선택적인_보조 목적은 엔터티를 기능으로 사용 하 여 의도 또는 다른 엔터티의 예측을 높이는 것입니다.

엔터티는 다음과 같은 여러 가지 유형이 있습니다.

* [기계 학습된 엔터티](reference-entity-machine-learned-entity.md)
* 비 기계어 학습 된 텍스트 일치, 패턴 일치 또는 미리 작성 된 엔터티에서의 검색에 필요한 [기능](luis-concept-feature.md) 으로 사용 됩니다.
* [패턴.](#patternany-entity) [패턴](reference-entity-pattern-any.md) 에서 책 제목과 같은 자유 형식 텍스트를 추출 하는 데 사용 됩니다.

컴퓨터에서 배운 엔터티는 가장 넓은 범위의 데이터 추출 선택 항목을 제공 합니다. 컴퓨터에서 학습 하지 않은 엔터티는 텍스트 일치를 통해 작동 하며 컴퓨터에서 학습 한 엔터티 또는 의도에 대 한 [필수 기능](#design-entities-for-decomposition) 으로 사용 됩니다.

## <a name="entities-represent-data"></a>데이터를 나타내는 엔터티

엔터티는 utterance에서 가져올 데이터 (예: 이름, 날짜, 제품 이름 또는 단어의 중요 한 그룹)입니다. 발화에는 여러 엔터티가 포함되거나 전혀 포함되지 않을 수 있습니다. 클라이언트 응용 프로그램에서 작업을 수행 하는 데 필요한 데이터가 필요할 _수 있습니다_ .

모델의 각 의도에 대해 모든 학습 길이 발언 엔터티에 일관 되 게 레이블을 지정 해야 합니다.

 사용자 고유의 엔터티를 정의 하거나 미리 작성 된 엔터티를 사용 하 여 [datetimeV2](luis-reference-prebuilt-datetimev2.md), [서 수](luis-reference-prebuilt-ordinal.md), [전자 메일](luis-reference-prebuilt-email.md)및 [전화 번호](luis-reference-prebuilt-phonenumber.md)와 같은 일반적인 개념의 시간을 절약할 수 있습니다.

|발화|엔터티|데이터|
|--|--|--|
|Buy 3 tickets to New York|미리 빌드된 숫자<br>대상|3<br>뉴욕|


### <a name="entities-are-optional-but-recommended"></a>엔터티는 선택 사항 이지만 권장 됩니다.

[의도](luis-concept-intent.md) 는 필수 이지만 엔터티는 선택 사항입니다. 앱의 모든 개념에 대 한 엔터티를 만들 필요는 없지만, 클라이언트 응용 프로그램에 데이터가 필요 하거나 엔터티가 다른 엔터티 또는 의도에 대 한 힌트 또는 신호 역할을 수행 하는 경우에만 해당 됩니다.

응용 프로그램이 개발 되 고 데이터에 대 한 새로운 요구 사항이 확인 되 면 나중에 LUIS 모델에 적절 한 엔터티를 추가할 수 있습니다.

## <a name="entity-compared-to-intent"></a>엔터티 및 의도 비교

엔터티는 _utterance 내의_데이터 개념을 나타냅니다. 의도는 _전체 utterance_을 분류 합니다.

다음 네 가지 길이 발언을 고려 합니다.

|발화|예측된 의도|추출 된 엔터티|설명|
|--|--|--|--|
|도움말|도움말|-|추출할 항목이 없습니다.|
|항목 보내기|sendSomething|-|추출할 항목이 없습니다. 모델에는이 컨텍스트에서 추출 하는 데 필요한 기능이 없으며 `something` 받는 사람이 언급 되지 않습니다.|
|Bob이 있는 메일 보내기|sendSomething|`Bob`, `present`|모델은 미리 작성 된 `Bob` 엔터티의 필수 기능을 추가 하 여 추출 `personName` 합니다. 컴퓨터에서 학습 한 엔터티를 추출 하는 데 사용 되었습니다 `present` .|
|Bob에 게 빠졌습니다의 상자를 보냅니다.|sendSomething|`Bob`, `box of chocolates`|두 가지 중요 한 데이터, `Bob` 및는 `box of chocolates` 컴퓨터에서 배운 엔터티에 의해 추출 되었습니다.|

## <a name="design-entities-for-decomposition"></a>분해를 위한 엔터티 디자인

컴퓨터에서 배운 엔터티를 사용 하면 분해를 위해 앱 스키마를 디자인 하 여 큰 개념을 하위 엔터티로 나눌 수 있습니다.

분해를 위한 디자인을 통해 LUIS은 클라이언트 응용 프로그램에 깊이 있는 엔터티 해상도를 반환할 수 있습니다. 이렇게 하면 클라이언트 응용 프로그램이 비즈니스 규칙에 집중 하 고 데이터 확인을 LUIS로 유지할 수 있습니다.

길이 발언 예제를 통해 학습 된 컨텍스트를 기반으로 하는 컴퓨터의 학습 된 엔터티 트리거입니다.

[**컴퓨터에서 학습 한 엔터티**](tutorial-machine-learned-entity.md) 는 최상위 수준 추출기. 하위 엔터티는 컴퓨터에서 학습 한 엔터티의 자식 엔터티입니다.

<a name="composite-entity"></a>
<a name="list-entity"></a>
<a name="patternany-entity"></a>
<a name="prebuilt-entity"></a>
<a name="regular-expression-entity"></a>
<a name="simple-entity"></a>

## <a name="types-of-entities"></a>엔터티의 형식

부모에 대 한 하위 엔터티는 컴퓨터에서 학습 된 엔터티입니다. 하위 엔터티는 컴퓨터에서 학습 하지 않은 엔터티를 [기능](luis-concept-feature.md)으로 사용할 수 있습니다.

데이터를 추출해야 하는 방법 및 추출된 후에 데이터를 표시하는 방법에 따라 엔터티를 선택합니다.

|엔터티 유형|용도|
|--|--|
|[**컴퓨터-학습**](tutorial-machine-learned-entity.md)|레이블이 지정 된 예제에서 배운 중첩 된 복잡 한 데이터를 추출 합니다. |
|[**은**](reference-entity-list.md)|**정확히 일치**하는 텍스트를 사용 하 여 추출 된 항목 및 해당 동의어의 목록입니다.|
|[**패턴. 모든**](#patternany-entity)|엔터티 끝을 찾는 엔터티는 자유 형식 이므로 확인 하기 어렵습니다. [패턴](luis-concept-patterns.md)에서만 사용할 수 있습니다.|
|[**미리 빌드됨**](luis-reference-prebuilt-entities.md)|URL, 전자 메일 등의 특정 종류의 데이터를 추출 하도록 이미 학습 되었습니다. 이러한 미리 빌드된 엔터티 중 일부는 오픈 소스 [Recognizers-Text](https://github.com/Microsoft/Recognizers-Text) 프로젝트에 정의되어 있습니다. 특정 문화권이나 엔터티가 현재 지원되지 않은 경우 프로젝트에 적용됩니다.|
|[**정규식**](reference-entity-regular-expression.md)|**정확히 일치**하는 텍스트를 위해 정규식을 사용 합니다.|

## <a name="extracting-contextually-related-data"></a>컨텍스트 관련 데이터 추출

Utterance에는 데이터의 의미가 utterance 내의 컨텍스트를 기반으로 하는 두 개 이상의 엔터티가 포함 될 수 있습니다. 예를 들어 원본 및 대상 이라는 두 개의 지리적 위치를 포함 하는 비행을 예약 하는 utterance 있습니다.

`Book a flight from Seattle to Cairo`

두 위치는 티켓 구매를 완료 하기 위해 클라이언트 응용 프로그램에서 각 위치의 유형을 알고 있는 방식으로 추출 해야 합니다.

원본 및 대상을 추출 하려면 티켓 주문 기계 학습 엔터티의 일부로 두 개의 하위 엔터티를 만듭니다. 각 하위 엔터티에 대해 geographyV2를 사용 하는 필수 기능을 만듭니다.

<a name="using-component-constraints-to-help-define-entity"></a>
<a name="using-subentity-constraints-to-help-define-entity"></a>

### <a name="using-required-features-to-constrain-entities"></a>필요한 기능을 사용 하 여 엔터티 제한

[필요한 기능](luis-concept-feature.md) 에 대 한 자세한 정보

## <a name="patternany-entity"></a>Pattern.any 엔터티

패턴입니다. any는 [패턴](luis-concept-patterns.md)에서만 사용할 수 있습니다.

<a name="if-you-need-more-than-the-maximum-number-of-entities"></a>
## <a name="exceeding-app-limits-for-entities"></a>엔터티에 대 한 앱 제한 초과

[제한](luis-limits.md#model-limits)보다 더 많이 필요한 경우 지원 담당자에 게 문의 하세요. 이렇게 하려면 시스템에 대한 자세한 정보를 수집하고 [LUIS](luis-reference-regions.md#luis-website) 웹 사이트로 이동한 다음, **지원**을 선택하세요. Azure 구독에 지원 서비스가 포함된 경우, [Azure 기술 지원](https://azure.microsoft.com/support/options/)에 문의하세요.

## <a name="entity-prediction-status"></a>엔터티 예측 상태

LUIS 포털은 엔터티에 utterance 예제에 대해 선택한 엔터티와 다른 엔터티 예측이 있는 경우를 표시 합니다. 이 다른 점수는 현재 학습 된 모델을 기반으로 합니다. 이 정보를 사용 하 여 다음 중 하나 이상을 사용 하 여 학습 오류를 해결할 수 있습니다.
* 엔터티의 개념을 식별 하는 데 도움이 되는 엔터티의 [기능](luis-concept-feature.md) 을 만듭니다.
* 엔터티를 사용 하 여 [예제 길이 발언](luis-concept-utterance.md) 및 레이블 추가
* 엔터티 개념을 식별 하는 데 도움이 될 수 있는 예측 끝점에서 수신 된 길이 발언에 대 한 [활성 학습 제안을 검토](luis-concept-review-endpoint-utterances.md) 합니다.

## <a name="next-steps"></a>다음 단계

좋은 [발화](luis-concept-utterance.md)에 대한 개념을 알아보세요.

LUIS 앱에 엔터티를 추가하는 방법에 대한 자세한 내용은 [엔터티 추가](luis-how-to-add-entities.md)를 참조하세요.

컴퓨터에서 학습 한 엔터티를 사용 하 여 utterance에서 구조화 된 데이터를 추출 하는 방법에 대 한 자세한 내용은 [자습서: utterance에서 컴퓨터에서 배운 Language Understanding 엔터티를 사용 하 여 사용자에서 구조화 된 데이터 추출](tutorial-machine-learned-entity.md) 을 참조 하세요.

