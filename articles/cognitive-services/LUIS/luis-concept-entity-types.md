---
title: 엔터티 형식
titleSuffix: Language Understanding - Azure Cognitive Services
description: '엔터티를 utterance에서 데이터를 추출합니다. 엔터티 형식 제공 예측 가능한 데이터를 추출 합니다. 엔터티는 방법은 두 가지가: 기계 학습 및 비 기계 학습. 표현에서 사용 하는 엔터티의 유형을 알고 있어야 하는 것이 반드시 합니다.'
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 06/12/2019
ms.author: diberry
ms.openlocfilehash: 628a96c4e912341226d67a7ed8f241194e7b7825
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/13/2019
ms.locfileid: "67080046"
---
# <a name="entity-types-and-their-purposes-in-luis"></a>엔터티 형식 및 LUIS에서의 용도

엔터티를 utterance에서 데이터를 추출합니다. 엔터티 형식 제공 예측 가능한 데이터를 추출 합니다. 엔터티는 방법은 두 가지가: 기계 학습 및 비 기계 학습. 표현에서 사용 하는 엔터티의 유형을 알고 있어야 하는 것이 반드시 합니다. 

## <a name="entity-compared-to-intent"></a>엔터티 및 의도 비교

엔터티는 추출하려는 발화 내의 단어 또는 구를 나타냅니다. 발화에는 여러 엔터티가 포함되거나 전혀 포함되지 않을 수 있습니다. 클라이언트 응용 프로그램 엔터티를 해당 작업을 수행 하거나 사용자에 게 제공 하려면 여러 선택 항목의 지침으로 사용 해야 합니다. 

엔터티:

* 비슷한 개체 (장소, 사물, 사용자, 이벤트 또는 개념)의 컬렉션을 포함 하 여 클래스를 나타냅니다. 
* 의도에 관련 된 정보를 설명합니다.


예를 들어, News Search 앱에는 뉴스 검색의 키 데이터인 “주제”, “원본”, “키워드” 및 “게시 날짜”와 같은 엔터티가 포함될 수 있습니다. 여행 예약 앱에서는 “위치”, “날짜”, “항공사”, “여행 클래스” 및 “티켓”이 항공편 예약의 주요 정보입니다(“항공편 예약” 의도와 관련됨).

반면, 의도는 전체 발화의 예측을 나타냅니다. 

## <a name="entities-help-with-data-extraction-only"></a>엔터티는 데이터 추출에만 유용함

엔터티를 추출하기 위한 목적으로만 엔터티의 레이블을 지정하거나 표시하면 의도를 예측하는 데 도움이 되지 않습니다.

## <a name="entities-represent-data"></a>데이터를 나타내는 엔터티

엔터티는 발화에서 가져오려는 데이터입니다. 이름, 날짜, 제품 이름 또는 모든 단어 그룹이 될 수 있습니다. 

|발화|엔터티|Data|
|--|--|--|
|Buy 3 tickets to New York|미리 빌드된 숫자<br>Location.Destination|3<br>뉴욕|
|Buy a ticket from New York to London on March 5|Location.Origin<br>Location.Destination<br>미리 빌드된 datetimeV2|뉴욕<br>런던<br>March 5, 2018|

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

|기계 학습|표시할 수 있음|자습서|예<br>response|엔터티 형식|목적|
|--|--|--|--|--|--|
|✔|✔|[✔](luis-tutorial-composite-entity.md)|[✔](luis-concept-data-extraction.md#composite-entity-data)|[**복합**](#composite-entity)|엔터티 형식에 관계없이 엔터티 그룹화|
|||[✔](luis-quickstart-intent-and-list-entity.md)|[✔](luis-concept-data-extraction.md#list-entity-data)|[**목록**](#list-entity)|정확한 텍스트 일치 항목을 사용하여 추출된 항목 및 동의어 목록|
|혼합||[✔](luis-tutorial-pattern.md)|[✔](luis-concept-data-extraction.md#patternany-entity-data)|[**Pattern.any**](#patternany-entity)|엔터티의 끝을 확인하기 어려운 엔터티|
|||[✔](luis-tutorial-prebuilt-intents-entities.md)|[✔](luis-concept-data-extraction.md#prebuilt-entity-data)|[**미리 빌드됨**](#prebuilt-entity)|다양한 종류의 데이터를 추출하도록 이미 학습됨|
|||[✔](luis-quickstart-intents-regex-entity.md)|[✔](luis-concept-data-extraction.md#regular-expression-entity-data)|[**정규식**](#regular-expression-entity)|정규식을 사용하여 텍스트 일치|
|✔|✔|[✔](luis-quickstart-primary-and-secondary-data.md)|[✔](luis-concept-data-extraction.md#simple-entity-data)|[**단순**](#simple-entity)|단어 또는 구에서 단일 개념 포함|

기계 학습 엔터티만 예제 표현에 표시 해야 합니다. 기계 학습 엔터티는 [엔드포인트 쿼리](luis-concept-test.md#endpoint-testing)를 통해 테스트되고 [엔드포인트 발화를 검토](luis-how-to-review-endoint-utt.md)하는 경우에 가장 적합합니다. 

Pattern.any 엔터티는 의도 사용자 예제가 아닌 [패턴](luis-how-to-model-intent-pattern.md) 템플릿 예제에서 표시되어야 합니다. 

혼합 엔터티는 엔터티 검색 방법의 조합을 사용합니다.

## <a name="machine-learned-entities-use-context"></a>기계 학습 엔터티 컨텍스트를 사용합니다.

기계 학습 엔터티를 utterance 컨텍스트에서에서 알아봅니다. 이렇게 하면 배치의 변형 예제 표현에서 중요 합니다. 

## <a name="non-machine-learned-entities-dont-use-context"></a>기계 학습 엔터티 컨텍스트를 사용 하지 마세요

비-컴퓨터 엔터티 고려 하지 않은 utterance 상황에 맞는 계정 엔터티를 일치 시킬 때 학습 합니다. 

* [미리 빌드된 엔터티](#prebuilt-entity)
* [Regex 엔터티](#regular-expression-entity)
* [목록 엔터티](#list-entity) 

이러한 엔터티는 레이블 지정 또는 모델 학습에 필요 하지 않습니다. 를 추가 하거나 엔터티 구성 되 면 엔터티 추출 됩니다. 단점은 이러한 엔터티 수 수 overmatched, 여기서 컨텍스트를 고려 하는 경우 일치 하는 것 없습니다 내용이 있습니다. 

이 목록 엔터티를 사용 하 여 새 모델에서 자주 발생 합니다. 작성 하 고 목록 엔터티를 사용 하 여 모델을 테스트 하지만 모델을 게시 하 고 끝점에서 쿼리를 받을 때 있습니다 모델 컨텍스트 부족으로 인해 overmatching 됩니다. 

단어 또는 구를 일치 및 상황에 맞는 고려 하려는 경우 두 가지 옵션이 있습니다. 첫 번째 구 목록을 사용 하 여 쌍을 이루는 간단한 엔터티를 사용 하는 것입니다. 구 목록 일치 하는 사용 되지 않습니다 하지만 대신에 신호 비교적 유사한 단어 (교환할 목록) 데 도움이 됩니다. 구 목록의 변형 하는 대신 정확히 일치 해야 하는 경우 아래에 설명 된 역할을 가진 목록 엔터티를 사용 합니다.

### <a name="context-with-non-machine-learned-entities"></a>컴퓨터 학습 되지 않은 엔터티를 사용 하 여 상황에 맞는

머신이 아닌 컴퓨터 학습된 엔터티에 대 한 중요 utterance의 컨텍스트를 하려는 경우 사용 해야 [역할](luis-concept-roles.md)입니다.

컴퓨터 학습 되지 않은 엔터티를 같은 있는지 [미리 빌드된 엔터티로](#prebuilt-entity)를 [regex](#regular-expression-entity) 엔터티 또는 [목록](#list-entity) 엔터티 인스턴스를 넘어 일치 하는 것이 좋습니다. 두 가지 역할을 사용 하 여 엔터티를 만드는 중입니다. 하나의 역할에 대 한 원하는 내용을 캡처합니다 및 없습니다 원하는 내용을 대 한 하나의 역할 캡처됩니다. 두 버전 모두에 예제 표현에 레이블을 표시 해야 합니다.  

## <a name="composite-entity"></a>복합 엔터티

복합 엔터티는 이루어져 미리 작성 된 엔터티를 같은 다른 엔터티를 단순 목록 엔터티와 정규식 있습니다. 개별 엔터티가 전체 엔터티를 형성합니다. 

데이터의 상태가 다음과 같은 경우 이 엔터티가 적합합니다.

* 서로 관련이 있습니다. 
* 발언의 컨텍스트에서 서로 관련되어 있습니다.
* 다양한 엔터티 형식을 사용합니다.
* 클라이언트 애플리케이션에서 정보 단위로 그룹화되고 처리되어야 합니다.
* 기계 학습을 요구하는 다양한 사용자 발언이 포함됩니다.

![복합 엔터티](./media/luis-concept-entities/composite-entity.png)

[자습서](luis-tutorial-composite-entity.md)<br>
[엔터티에 대한 JSON 응답 예제](luis-concept-data-extraction.md#composite-entity-data)<br>

## <a name="list-entity"></a>목록 엔터티

목록 엔터티는 동의어와 함께 일련의 고정된 폐쇄형 관련 단어를 나타냅니다. LUIS는 목록 엔터티에 대한 추가 값을 검색하지 않습니다. **권장** 기능을 사용하여 현재 목록을 기준으로 권장되는 새 단어를 확인합니다. 동일한 값을 갖는 목록 엔터티가 둘 이상 있는 경우, 각 엔터티가 엔드포인트 쿼리에서 반환됩니다. 

텍스트 데이터의 상태가 다음과 같은 경우 이 엔터티가 적합합니다.

* 알려진 세트입니다.
* 자주 변경 되지 않습니다. 자체 확장 목록 또는 목록에 자주 변경 해야 할 경우 구 목록을 사용 하 여 승격 된 간단한 엔터티를 보다 적합 합니다. 
* 집합이 이 엔터티 형식의 최대 LUIS [경계](luis-boundaries.md)를 초과하지 않습니다.
* 발언의 텍스트가 동의어 또는 정식 이름과 정확히 일치합니다. LUIS는 정확한 텍스트 일치를 벗어나는 목록을 사용하지 않습니다. 유사 항목 일치, case-insensitivity, 형태소 분석, 복수, 및 다른 변형 목록 엔터티와 확인 되지 않습니다. 변형을 관리하려면 선택적인 텍스트 구문이 포함된 [패턴](luis-concept-patterns.md#syntax-to-mark-optional-text-in-a-template-utterance)을 사용하는 것이 좋습니다.

![목록 엔터티](./media/luis-concept-entities/list-entity.png)

[자습서](luis-quickstart-intent-and-list-entity.md)<br>
[엔터티에 대한 JSON 응답 예제](luis-concept-data-extraction.md#list-entity-data)

## <a name="patternany-entity"></a>Pattern.any 엔터티

Pattern.any는 엔터티가 시작되고 끝나는 위치를 표시하기 위해 패턴의 템플릿 발화에서만 사용되는 가변 길이 자리 표시자입니다.  

다음과 같은 경우 이 엔터티가 적합합니다.

* 엔터티의 끝이 발화의 나머지 텍스트와 혼동될 수 있는 경우 
[자습서](luis-tutorial-pattern.md)<br>
[엔터티에 대한 JSON 응답 예제](luis-concept-data-extraction.md#patternany-entity-data)

**예제**  
클라이언트 애플리케이션이 제목을 기준으로 도서를 검색하는 경우 pattern.any는 전체 제목을 추출합니다. 이 도서 검색에서 pattern.any를 사용하는 템플릿 발화는 `Was {BookTitle} written by an American this year[?]`입니다. 

다음 표에서 각 행에는 두 가지 버전의 발화가 있습니다. 위쪽 발화는 도서 제목 시작 및 종료가 명확하지 않은 경우 LUIS에서 발화를 처음 표시하는 방법입니다. 아래쪽 발화는 추출에 대한 패턴이 있는 경우 LUIS에서 도서 제목을 알아내는 방법입니다. 

|발화|
|--|
|The Man Who Mistook His Wife for a Hat and Other Clinical Tales는 올해 미국에서 저술되었나요?<br><br>**The Man Who Mistook His Wife for a Hat and Other Clinical Tales**는 올해 미국에서 저술되었나요?|
|Half Asleep in Frog Pajamas는 올해 미국에서 저술되었나요?<br><br>**Half Asleep in Frog Pajamas**는 올해 미국에서 저술되었나요?|
|The Particular Sadness of Lemon Cake: A Novel은 올해 미국에서 저술되었나요?<br><br>**The Particular Sadness of Lemon Cake: A Novel**은 올해 미국에서 저술되었나요?|
|There's A Wocket In My Pocket! 은 올해 미국에서 저술되었나요?<br><br>**There's A Wocket In My Pocket!** 은 올해 미국에서 저술되었나요?|
||

## <a name="prebuilt-entity"></a>미리 빌드된 엔터티

미리 작성된 엔터티는 이메일, URL 및 전화 번호와 같은 일반적인 개념을 나타태는 기본 제공 형식입니다. 미리 빌드된 엔터티 이름은 예약되어 있습니다. 애플리케이션에 추가된 [미리 빌드된 모든 엔터티](luis-prebuilt-entities.md)가 발화에 위치한 경우 엔드포인트 예측 쿼리에서 반환됩니다. 

다음과 같은 경우 이 엔터티가 적합합니다.

* 데이터는 언어 문화권을 위해 미리 빌드된 엔터티에서 지원되는 일반적인 사용 사례와 일치합니다. 

미리 빌드된 엔터티를 언제든지 추가하고 제거할 수 있습니다.

![Number 미리 빌드된 엔터티](./media/luis-concept-entities/number-entity.png)

[자습서](luis-tutorial-prebuilt-intents-entities.md)<br>
[엔터티에 대한 JSON 응답 예제](luis-concept-data-extraction.md#prebuilt-entity-data)

이러한 미리 빌드된 엔터티 중 일부는 오픈 소스 [Recognizers-Text](https://github.com/Microsoft/Recognizers-Text) 프로젝트에 정의되어 있습니다. 특정 문화권이나 엔터티가 현재 지원되지 않은 경우 프로젝트에 적용됩니다. 

### <a name="troubleshooting-prebuilt-entities"></a>미리 빌드된 엔터티로 문제 해결

LUIS 포털에서 미리 작성 된 엔터티를 사용자 지정 엔터티를 대신 태그가 지정 되어 있으면이 문제를 해결 하는 방법의 몇 가지 옵션입니다.

앱에 추가 된 미리 작성 된 엔터티는 _항상_ 를 utterance 동일한 텍스트에 대 한 사용자 지정 엔터티 추출 해야 하는 경우에 반환 합니다. 

#### <a name="change-tagged-entity-in-example-utterance"></a>예제 utterance에서 태그가 지정 된 엔터티를 변경 합니다.

미리 작성 된 엔터티를 동일한 텍스트 또는 토큰에 사용자 지정 엔터티로 예제에서는 utterance 텍스트를 선택 하 고 태그가 지정 된 utterance를 바꿉니다. 

자세한 텍스트 또는 사용자 지정 엔터티 보다는 토큰을 사용 하 여 미리 작성 된 엔터티 태그가 지정 되는 몇 가지 방법이 문제를 해결 하는 방법의 사용 해야 합니다.

* [예제 utterance 제거](#remove-example-utterance-to-fix-tagging) 메서드
* [미리 작성 된 엔터티 제거](#remove-prebuilt-entity-to-fix-tagging) 메서드

#### <a name="remove-example-utterance-to-fix-tagging"></a>태그 지정을 해결 하려면 예제 utterance 제거 

첫 번째 선택 예제 utterance를 제거 하는 것입니다. 

1. 예제 utterance를 삭제 합니다.
1. 앱을 다시 학습 합니다. 
1. 뿐만 아니라 단어를 다시 추가 하거나 전체 예제 utterance로 미리 빌드된 엔터티로 표시 된 엔터티, 즉 구를 합니다. 단어 또는 구를 표시 된 미리 작성 된 엔터티도 해야 합니다. 
1. 예제 utterance에서 엔터티를 선택 합니다 **의도** 페이지 사용자 지정 엔터티를 변경 하 고 다시 학습 합니다. 이 텍스트는 텍스트를 사용 하는 모든 예제 표현에서 미리 빌드된 엔터티로 표시에서 LUIS 이렇게 해야 합니다. 
1. 전체 원래 예제 utterance 의도를 다시 추가 합니다. 사용자 지정 엔터티는 미리 작성 된 엔터티 대신 표시할 계속 해야 합니다. 사용자 지정 엔터티가 표시 되지 않으면 표현에서 텍스트의 더 많은 예제를 추가 해야 합니다.

#### <a name="remove-prebuilt-entity-to-fix-tagging"></a>태그 지정을 해결 하려면 미리 작성 된 엔터티를 제거 합니다.

1. 앱에서 미리 작성 된 엔터티를 제거 합니다. 
1. 에 **의도** 페이지에서 예제 utterance에서 사용자 지정 엔터티를 표시 합니다.
1. 앱을 학습합니다.
1. 앱으로 다시 미리 작성 된 엔터티를 추가 하 고 앱을 학습 합니다. 이 문제가 해결 미리 작성 된 엔터티 복합 엔터티의 일부가 아닌 것을 가정 합니다.

## <a name="regular-expression-entity"></a>정규식 엔터티 

정규식은 원시 발화 텍스트에 적합합니다. 대/소문자를 무시하고 문화적 변형을 무시합니다.  정규식 일치는 토큰 수준이 아니라 문자 수준에서 맞춤법 검사 변경 후에 적용됩니다. 많은 대괄호를 사용하는 것처럼 정규식이 너무 복잡한 경우 모델에 식을 추가할 수 없습니다. 전체가 아니라 일부를 사용 하 여 [.NET Regex](https://docs.microsoft.com/dotnet/standard/base-types/regular-expressions) 라이브러리입니다. 

다음과 같은 경우 이 엔터티가 적합합니다.

* 데이터는 일관된 변형으로 일관되게 서식이 지정됩니다.
* 정규식에는 2개 이상의 중첩 수준이 필요하지 않습니다. 

![정규식 엔터티](./media/luis-concept-entities/regex-entity.png)

[자습서](luis-quickstart-intents-regex-entity.md)<br>
[엔터티에 대한 JSON 응답 예제](luis-concept-data-extraction.md#regular-expression-entity-data)<br>

정규식 일치에 예상 보다 더 일치할 수 있습니다. 이 예로 숫자 word와 같은 일치 `one` 고 `two`입니다. 예로 수와 일치 하는 다음 정규식 `one` 다른 숫자와 함께 합니다.

```javascript
(plus )?(zero|one|two|three|four|five|six|seven|eight|nine)(\s+(zero|one|two|three|four|five|six|seven|eight|nine))*
``` 

정규식이 이러한 수치를 사용 하 여 같은 종료 하는 모든 단어 일치 `phone`합니다. 다음과 같은 문제 해결, regex와 일치 하는지 확인 하기 위해 계정 단어 경계를 고려 합니다. 이 예제에 대 한 단어 경계를 사용 하는 regex는 다음 정규식에 사용 됩니다.

```javascript
\b(plus )?(zero|one|two|three|four|five|six|seven|eight|nine)(\s+(zero|one|two|three|four|five|six|seven|eight|nine))*\b
```

## <a name="simple-entity"></a>단순 엔터티 

단순 엔터티는 단일 개념을 설명하고 기계 학습 컨텍스트에서 학습되는 일반 엔터티입니다. 단준 엔터티 이름이 일반적으로 회사 이름, 제품 이름 또는 이름의 기타 범주와 같은 이름이기 때문에 사용된 이름의 신호를 향상시키기 위해 간단한 엔터티를 사용하는 경우 [구 목록](luis-concept-feature.md)을 추가합니다. 

다음과 같은 경우 이 엔터티가 적합합니다.

* 데이터는 일관되게 형식이 지정되지 않고 동일한 작업을 나타냅니다. 

![단순 엔터티](./media/luis-concept-entities/simple-entity.png)

[자습서](luis-quickstart-primary-and-secondary-data.md)<br/>
[엔터티에 대한 예제 응답](luis-concept-data-extraction.md#simple-entity-data)<br/>

## <a name="entity-limits"></a>엔터티 제한

모델에 추가할 수 있는 각 엔터티 형식의 수를 파악하려면 [제한](luis-boundaries.md#model-boundaries)을 검토하세요.

## <a name="if-you-need-more-than-the-maximum-number-of-entities"></a>최대 엔터티 수보다 더 많이 필요한 경우 

엔터티 역할과 함께에서 복합 엔터티를 사용 해야 합니다.

복합 엔터티는 전체의 일부를 나타냅니다. 예를 들어, PlaneTicketOrder라는 복합 엔터티에는 Airline, Destination, DepartureCity, DepartureDate 및 PlaneTicketClass라는 자식 엔터티가 있을 수 있습니다.

LUIS는 기계 학습화되지 않지만 LUIS 앱에서 고정된 값 목록을 지정할 수 있도록 하는 목록 엔터티 형식도 제공합니다. 목록 엔터티 형식의 제한을 검토하려면 [LUIS 경계](luis-boundaries.md) 참조를 참조하세요. 

이러한 엔터티를 한 것으로 간주 하 고 여전히 제한 보다 많이 필요한 경우 지원에 문의 합니다. 이렇게 하려면 시스템에 대한 자세한 정보를 수집하고 [LUIS](luis-reference-regions.md#luis-website) 웹 사이트로 이동한 다음, **지원**을 선택하세요. Azure 구독에 지원 서비스가 포함된 경우, [Azure 기술 지원](https://azure.microsoft.com/support/options/)에 문의하세요. 

## <a name="next-steps"></a>다음 단계

좋은 [발화](luis-concept-utterance.md)에 대한 개념을 알아보세요. 

LUIS 앱에 엔터티를 추가하는 방법에 대한 자세한 내용은 [엔터티 추가](luis-how-to-add-entities.md)를 참조하세요.
