---
title: 엔터티 형식
titleSuffix: Language Understanding - Azure Cognitive Services
description: 엔터티는 utterance에서 데이터를 추출 합니다. 엔터티 형식은 데이터의 예측 가능한 추출을 제공 합니다. 두 가지 유형의 엔터티가 있습니다. 컴퓨터를 학습 하 고 컴퓨터를 인식 하지 못합니다. 길이 발언에서 작업 하는 엔터티의 유형을 파악 하는 것이 중요 합니다.
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 07/24/2019
ms.author: diberry
ms.openlocfilehash: a5a3ba8c25107317e7c47ee358f9a6ebe7d4556f
ms.sourcegitcommit: 75a56915dce1c538dc7a921beb4a5305e79d3c7a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/24/2019
ms.locfileid: "68479109"
---
# <a name="entity-types-and-their-purposes-in-luis"></a>엔터티 형식 및 LUIS에서의 용도

엔터티는 utterance에서 데이터를 추출 합니다. 엔터티 형식은 데이터의 예측 가능한 추출을 제공 합니다. 두 가지 유형의 엔터티가 있습니다. 컴퓨터를 학습 하 고 컴퓨터를 인식 하지 못합니다. 길이 발언에서 작업 하는 엔터티의 유형을 파악 하는 것이 중요 합니다. 

## <a name="entity-compared-to-intent"></a>엔터티 및 의도 비교

엔터티는 추출하려는 발화 내의 단어 또는 구를 나타냅니다. 발화에는 여러 엔터티가 포함되거나 전혀 포함되지 않을 수 있습니다. 클라이언트 응용 프로그램은 작업을 수행 하기 위해 엔터티가 필요 하거나 사용자에 게 제공 되는 여러 선택 항목의 지침으로 사용할 수 있습니다. 

엔터티:

* 유사한 개체 (위치, 사물, 사람, 이벤트 또는 개념)의 컬렉션을 포함 하는 클래스를 나타냅니다. 
* 의도와 관련 된 정보를 설명 합니다.


예를 들어, News Search 앱에는 뉴스 검색의 키 데이터인 “주제”, “원본”, “키워드” 및 “게시 날짜”와 같은 엔터티가 포함될 수 있습니다. 여행 예약 앱에서는 “위치”, “날짜”, “항공사”, “여행 클래스” 및 “티켓”이 항공편 예약의 주요 정보입니다(“항공편 예약” 의도와 관련됨).

반면, 의도는 전체 발화의 예측을 나타냅니다. 

## <a name="entities-help-with-data-extraction-only"></a>엔터티는 데이터 추출에만 유용함

엔터티를 추출하기 위한 목적으로만 엔터티의 레이블을 지정하거나 표시하면 의도를 예측하는 데 도움이 되지 않습니다.

## <a name="entities-represent-data"></a>데이터를 나타내는 엔터티

엔터티는 발화에서 가져오려는 데이터입니다. 이름, 날짜, 제품 이름 또는 모든 단어 그룹이 될 수 있습니다. 

|발화|엔터티|보기|
|--|--|--|
|Buy 3 tickets to New York|미리 빌드된 숫자<br>Location.Destination|3<br>New York|
|Buy a ticket from New York to London on March 5|Location.Origin<br>Location.Destination<br>미리 빌드된 datetimeV2|New York<br>런던<br>March 5, 2018|

## <a name="entities-are-optional-but-highly-recommended"></a>선택 사항이지만 많이 권장되는 엔터티

의도는 필수인 반면 엔터티는 선택 사항입니다. 앱의 모든 개념에 대해 엔터티를 만들 필요는 없지만 클라이언트 애플리케이션이 작업을 수행하는 데 필요한 개념에 대해서는 만들어야 합니다. 

발화에 봇이 계속 수행해야 하는 세부 정보가 없는 경우, 추가할 필요가 없습니다. 앱이 완성되고 나중에 추가할 수 있습니다. 

정보를 사용하는 방법을 잘 모르는 경우, [datetimeV2](luis-reference-prebuilt-datetimev2.md), [서수](luis-reference-prebuilt-ordinal.md), [이메일](luis-reference-prebuilt-email.md) 및 [전화 번호](luis-reference-prebuilt-phonenumber.md)와 같은 몇 가지 일반적인 미리 빌드된 엔터티를 추가합니다.

## <a name="label-for-word-meaning"></a>단어 의미에 대한 레이블

단어 선택이나 단어 배열은 동일하지만 같은 의미를 나타내지 않는 경우 엔터티로 레이블을 지정하지 마세요. 

다음 발화에서 단어 `fair`는 동형이의어입니다. 즉, 철자는 동일하지만 의미가 다릅니다.

|발화|
|--|
|What kind of county fairs are happening in the Seattle area this summer?|
|Is the current rating for the Seattle review fair?|

이벤트 엔터티에서 모든 이벤트 데이터를 찾도록 하려면 두 번째 발화가 아닌 첫 번째 발화에서 `fair` 단어에 레이블을 지정하세요.

## <a name="entities-are-shared-across-intents"></a>의도 간에 공유되는 엔터티

엔터티는 의도 간에 공유되며, 단일 의도에 속하지 않습니다. 의도와 엔터티는 의미상 서로 연관될 수 있지만 배타적 관계가 아닙니다.

"파리행 티켓을 예약해주세요." 발화에서 "파리"는 위치 형식의 엔터티입니다. LUIS는 사용자의 발화에서 언급된 엔터티를 인식하여 클라이언트 애플리케이션이 사용자의 요청을 이행하기 위해 수행할 특정 조치를 선택하는 데 도움을 줍니다.

## <a name="mark-entities-in-none-intent"></a>None 의도로 엔터티 표시

**None** 의도를 포함한 모든 의도는 가능하면 엔터티를 표시해야 합니다. 그러면 LUIS는 발화에서 엔터티의 위치와 엔터티 주위에 있는 단어에 대해 보다 자세히 알 수 있습니다. 

## <a name="entity-status-for-predictions"></a>예측할 엔터티 상태

LUIS 포털은 예제 발화의 엔터티가 표시된 엔터티와 다르거나 다른 엔터티와 너무 비슷해서 명확하지 않은 경우 사용자에게 알립니다. 이를 예제 발화에서 빨간색 밑줄로 표시합니다. 

자세한 내용은 [엔터티 상태 예측](luis-how-to-add-example-utterances.md#entity-status-predictions)을 참조하세요. 

## <a name="types-of-entities"></a>엔터티의 형식

LUIS는 다양한 형식의 엔터티를 제공합니다. 데이터를 추출해야 하는 방법 및 추출된 후에 데이터를 표시하는 방법에 따라 엔터티를 선택합니다.

기계 학습을 사용하여 엔터티를 추출할 수 있습니다. 그러면 LUIS에서 엔터티를 발화에 표시하는 방법에 대해 계속 자세히 알아볼 수 있습니다. 기계 학습을 사용하지 않고 엔터티를 추출할 수 있으며, 그러면 정확한 텍스트 또는 정규식을 일치시킵니다. 혼합 구현을 사용하여 패턴의 엔터티를 추출할 수 있습니다. 

엔터티를 추출하면 엔터티 데이터는 정보의 단일 단위로 표시되거나 클라이언트 애플리케이션에서 사용할 수 있는 정보의 단위를 형성하는 다른 엔터티와 결합될 수 있습니다.

|기계 학습|표시할 수 있음|자습서|예제<br>응답|엔터티 유형|용도|
|--|--|--|--|--|--|
|✔|✔|[✔](luis-tutorial-composite-entity.md)|[✔](luis-concept-data-extraction.md#composite-entity-data)|[**복합**](#composite-entity)|엔터티 형식에 관계없이 엔터티 그룹화|
|||[✔](luis-quickstart-intent-and-list-entity.md)|[✔](luis-concept-data-extraction.md#list-entity-data)|[**목록**](#list-entity)|정확한 텍스트 일치 항목을 사용하여 추출된 항목 및 동의어 목록|
|혼합||[✔](luis-tutorial-pattern.md)|[✔](luis-concept-data-extraction.md#patternany-entity-data)|[**Pattern.any**](#patternany-entity)|엔터티의 끝을 확인하기 어려운 엔터티|
|||[✔](luis-tutorial-prebuilt-intents-entities.md)|[✔](luis-concept-data-extraction.md#prebuilt-entity-data)|[**미리 빌드됨**](#prebuilt-entity)|다양한 종류의 데이터를 추출하도록 이미 학습됨|
|||[✔](luis-quickstart-intents-regex-entity.md)|[✔](luis-concept-data-extraction.md#regular-expression-entity-data)|[**정규식**](#regular-expression-entity)|정규식을 사용하여 텍스트 일치|
|✔|✔|[✔](luis-quickstart-primary-and-secondary-data.md)|[✔](luis-concept-data-extraction.md#simple-entity-data)|[**단순**](#simple-entity)|단어 또는 구에서 단일 개념 포함|

컴퓨터에서 학습 한 엔터티만 예 길이 발언로 표시 해야 합니다. 기계 학습 엔터티는 [엔드포인트 쿼리](luis-concept-test.md#endpoint-testing)를 통해 테스트되고 [엔드포인트 발화를 검토](luis-how-to-review-endoint-utt.md)하는 경우에 가장 적합합니다. 

Pattern.any 엔터티는 의도 사용자 예제가 아닌 [패턴](luis-how-to-model-intent-pattern.md) 템플릿 예제에서 표시되어야 합니다. 

혼합 엔터티는 엔터티 검색 방법의 조합을 사용합니다.

## <a name="machine-learned-entities-use-context"></a>컴퓨터에서 학습 한 엔터티 사용 컨텍스트

컴퓨터에서 배운 엔터티는 utterance의 컨텍스트에서 학습 합니다. 이렇게 하면 예 길이 발언에서 배치의 변형이 중요 합니다. 

## <a name="non-machine-learned-entities-dont-use-context"></a>컴퓨터에서 학습 하지 않은 엔터티는 컨텍스트를 사용 하지 않습니다.

다음과 같은 컴퓨터에서 알 수 없는 엔터티는 엔터티를 일치 시킬 때 utterance 컨텍스트를 고려 하지 않습니다. 

* [미리 빌드된 엔터티](#prebuilt-entity)
* [Regex 엔터티](#regular-expression-entity)
* [목록 엔터티](#list-entity) 

이러한 엔터티에는 모델에 대 한 레이블 지정 이나 교육이 필요 하지 않습니다. 엔터티를 추가 하거나 구성 하면 엔터티가 추출 됩니다. 이러한 엔터티는 컨텍스트를 고려 하 여 컨텍스트를 고려 하는 경우 이러한 엔터티를 과도 하 게 일치 시킬 수 있다는 단점이 있습니다. 

이는 새 모델에서 엔터티를 자주 나열 하는 경우에 발생 합니다. 목록 엔터티를 사용 하 여 모델을 빌드 및 테스트 하지만 모델을 게시 하 고 끝점에서 쿼리를 수신 하는 경우 컨텍스트가 부족 하 여 모델이 과도 하 게 일치 하는 것을 알 수 있습니다. 

단어나 구를 일치 시키고 컨텍스트를 고려 하려는 경우 두 가지 옵션을 사용할 수 있습니다. 첫 번째는 구 목록과 쌍을 이루는 단순 엔터티를 사용 하는 것입니다. 구 목록은 일치에 사용 되지 않고, 대신 상대적으로 유사한 단어 (교환 가능한 목록) 신호를 표시 하는 데 도움이 됩니다. 구 목록의 변형 대신 정확 하 게 일치 해야 하는 경우 아래에 설명 된 역할과 함께 목록 엔터티를 사용 합니다.

### <a name="context-with-non-machine-learned-entities"></a>컴퓨터에서 학습 하지 않은 엔터티가 포함 된 컨텍스트

컴퓨터에서 학습 하지 않은 엔터티에 대 한 utterance의 컨텍스트가 필요한 경우 [역할](luis-concept-roles.md)을 사용 해야 합니다.

미리 작성 된 [엔터티](#prebuilt-entity), [regex](#regular-expression-entity) 엔터티 또는 원하는 인스턴스와 일치 하는 [목록](#list-entity) 엔터티와 같이 컴퓨터에서 학습 하지 않은 엔터티가 있는 경우 두 개의 역할로 하나의 엔터티를 만드는 것이 좋습니다. 한 역할은 원하는 항목을 캡처하고, 한 역할은 찾고 있는 항목을 캡처합니다. 두 버전 모두 예 길이 발언에 레이블을 지정 해야 합니다.  

## <a name="composite-entity"></a>복합 엔터티

[복합 엔터티](reference-entity-composite.md) 는 미리 작성 된 엔터티, 단순, 정규식, 목록 엔터티 등의 다른 엔터티로 구성 됩니다. 개별 엔터티가 전체 엔터티를 형성합니다. 

## <a name="list-entity"></a>목록 엔터티

[목록 엔터티](reference-entity-list.md) 는 동의어와 함께 고정 된 닫힌 관련 단어 집합을 나타냅니다. LUIS는 목록 엔터티에 대한 추가 값을 검색하지 않습니다. **권장** 기능을 사용하여 현재 목록을 기준으로 권장되는 새 단어를 확인합니다. 동일한 값을 갖는 목록 엔터티가 둘 이상 있는 경우, 각 엔터티가 엔드포인트 쿼리에서 반환됩니다. 

## <a name="patternany-entity"></a>Pattern.any 엔터티

[패턴. any](reference-entity-pattern-any.md) 는 패턴이 시작 되 고 끝나는 위치를 표시 하는 패턴의 템플릿 utterance에만 사용 되는 가변 길이 자리 표시자입니다.  

## <a name="prebuilt-entity"></a>미리 빌드된 엔터티

미리 작성된 엔터티는 이메일, URL 및 전화 번호와 같은 일반적인 개념을 나타태는 기본 제공 형식입니다. 미리 빌드된 엔터티 이름은 예약되어 있습니다. 애플리케이션에 추가된 [미리 빌드된 모든 엔터티](luis-prebuilt-entities.md)가 발화에 위치한 경우 엔드포인트 예측 쿼리에서 반환됩니다. 

다음과 같은 경우 이 엔터티가 적합합니다.

* 데이터는 언어 문화권을 위해 미리 빌드된 엔터티에서 지원되는 일반적인 사용 사례와 일치합니다. 

미리 빌드된 엔터티를 언제든지 추가하고 제거할 수 있습니다.

![Number 미리 빌드된 엔터티](./media/luis-concept-entities/number-entity.png)

[자습서](luis-tutorial-prebuilt-intents-entities.md)<br>
[엔터티에 대한 JSON 응답 예제](luis-concept-data-extraction.md#prebuilt-entity-data)

이러한 미리 빌드된 엔터티 중 일부는 오픈 소스 [Recognizers-Text](https://github.com/Microsoft/Recognizers-Text) 프로젝트에 정의되어 있습니다. 특정 문화권이나 엔터티가 현재 지원되지 않은 경우 프로젝트에 적용됩니다. 

### <a name="troubleshooting-prebuilt-entities"></a>미리 작성 한 엔터티 문제 해결

LUIS 포털에서 미리 빌드된 엔터티에 사용자 지정 엔터티 대신 태그를 지정 하는 경우이를 해결 하는 방법에 대 한 몇 가지 옵션을 선택할 수 있습니다.

Utterance가 동일한 텍스트에 대 한 사용자 지정 엔터티를 추출 해야 하는 경우에도 앱에 추가 된 미리 작성 된 엔터티는 _항상_ 반환 됩니다. 

#### <a name="change-tagged-entity-in-example-utterance"></a>예 utterance에서 태그가 지정 된 엔터티 변경

미리 작성 된 엔터티가 사용자 지정 엔터티와 동일한 텍스트 또는 토큰이 면 utterance 예제에서 텍스트를 선택 하 고 태그가 지정 된 utterance를 변경 합니다. 

미리 빌드된 엔터티에 사용자 지정 엔터티 보다 더 많은 텍스트 또는 토큰으로 태그가 지정 된 경우이를 해결 하는 방법에 대 한 몇 가지 선택 사항이 있습니다.

* [Remove example utterance](#remove-example-utterance-to-fix-tagging) 메서드
* [미리](#remove-prebuilt-entity-to-fix-tagging) 작성 한 엔터티 메서드 제거

#### <a name="remove-example-utterance-to-fix-tagging"></a>태그를 수정 하는 예제 utterance 제거 

Utterance 예제를 제거 하는 것이 가장 좋습니다. 

1. 예제 utterance를 삭제 합니다.
1. 앱을 다시 학습 합니다. 
1. 엔터티에 해당 하는 단어 또는 구 (미리 작성 된 엔터티로 표시 됨)만 다시 추가 합니다. 전체 예제 utterance. 단어나 구에는 미리 작성 된 엔터티가 표시 됩니다. 
1. **의도** 페이지의 utterance 예제에서 엔터티를 선택 하 고 사용자 지정 엔터티로 변경한 후 다시 학습 합니다. 이로 인해 LUIS는 해당 텍스트를 사용 하는 예제 길이 발언에서이 정확한 텍스트를 미리 작성 된 엔터티로 표시 하지 않습니다. 
1. 원래 utterance 전체 예제를 다시 추가 합니다. 사용자 지정 엔터티는 미리 작성 된 엔터티 대신 계속 표시 되어야 합니다. 사용자 지정 엔터티가 표시 되지 않은 경우에는 길이 발언에 해당 텍스트의 예를 더 추가 해야 합니다.

#### <a name="remove-prebuilt-entity-to-fix-tagging"></a>태그를 수정할 미리 작성 한 엔터티 제거

1. 앱에서 미리 빌드된 엔터티를 제거 합니다. 
1. **의도** 페이지에서 utterance 예제에서 사용자 지정 엔터티를 표시 합니다.
1. 앱을 학습합니다.
1. 미리 작성 된 엔터티를 앱에 다시 추가 하 고 앱을 학습 합니다. 이 픽스는 미리 작성 된 엔터티가 복합 엔터티의 일부가 아닌 것으로 가정 합니다.

## <a name="regular-expression-entity"></a>정규식 엔터티 

[정규식 엔터티](reference-entity-regular-expression.md) 는 사용자가 제공 하는 정규식 패턴을 기반으로 엔터티를 추출 합니다.

## <a name="simple-entity"></a>단순 엔터티

[단순 엔터티](reference-entity-simple.md)는 기계 학습 값입니다. 이 값은 단어 또는 구문일 수 있습니다.
## <a name="entity-limits"></a>엔터티 제한

모델에 추가할 수 있는 각 엔터티 형식의 수를 파악하려면 [제한](luis-boundaries.md#model-boundaries)을 검토하세요.

## <a name="if-you-need-more-than-the-maximum-number-of-entities"></a>최대 엔터티 수보다 더 많이 필요한 경우 

엔터티 역할과 함께 복합 엔터티를 사용 해야 할 수도 있습니다.

복합 엔터티는 전체의 일부를 나타냅니다. 예를 들어, PlaneTicketOrder라는 복합 엔터티에는 Airline, Destination, DepartureCity, DepartureDate 및 PlaneTicketClass라는 자식 엔터티가 있을 수 있습니다.

LUIS는 기계 학습화되지 않지만 LUIS 앱에서 고정된 값 목록을 지정할 수 있도록 하는 목록 엔터티 형식도 제공합니다. 목록 엔터티 형식의 제한을 검토하려면 [LUIS 경계](luis-boundaries.md) 참조를 참조하세요. 

이러한 엔터티를 고려 하 여 제한 보다 더 많이 필요한 경우 지원 담당자에 게 문의 하세요. 이렇게 하려면 시스템에 대한 자세한 정보를 수집하고 [LUIS](luis-reference-regions.md#luis-website) 웹 사이트로 이동한 다음, **지원**을 선택하세요. Azure 구독에 지원 서비스가 포함된 경우, [Azure 기술 지원](https://azure.microsoft.com/support/options/)에 문의하세요. 

## <a name="next-steps"></a>다음 단계

좋은 [발화](luis-concept-utterance.md)에 대한 개념을 알아보세요. 

LUIS 앱에 엔터티를 추가하는 방법에 대한 자세한 내용은 [엔터티 추가](luis-how-to-add-entities.md)를 참조하세요.
