---
title: LUIS 앱의 엔터티 형식 - Language Understanding
titleSuffix: Azure Cognitive Services
description: LUIS(Language Understanding Intelligent Service) 앱에서 엔터티(응용 프로그램 도메인의 키 데이터)를 추가합니다.
services: cognitive-services
author: diberry
manager: cgronlun
ms.service: cognitive-services
ms.component: language-understanding
ms.topic: conceptual
ms.date: 10/19/2018
ms.author: diberry
ms.openlocfilehash: fdf81943a7bdbae80f4474915a72bb61f1123a30
ms.sourcegitcommit: 5de9de61a6ba33236caabb7d61bee69d57799142
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/25/2018
ms.locfileid: "50085867"
---
# <a name="entities-in-luis"></a>LUIS의 엔터티

엔터티는 응용 프로그램 도메인의 키 데이터인 발화의 단어 또는 구입니다.

## <a name="entity-compared-to-intent"></a>엔터티 및 의도 비교
엔터티는 추출하려는 발화 내의 단어 또는 구를 나타냅니다. 발화에는 여러 엔터티가 포함되거나 전혀 포함되지 않을 수 있습니다. 엔터티는 유사한 개체 모음(장소, 사물, 사람, 이벤트 또는 개념)을 포함한 클래스를 나타냅니다. 엔터티는 의도와 관련된 정보를 설명하며 앱이 작업을 수행하는 데 꼭 필요한 경우도 있습니다. 예를 들어, News Search 앱에는 뉴스 검색의 키 데이터인 “주제”, “원본”, “키워드” 및 “게시 날짜”와 같은 엔터티가 포함될 수 있습니다. 여행 예약 앱에서는 “위치”, “날짜”, “항공사”, “여행 클래스” 및 “티켓”이 항공편 예약의 키 정보입니다(“Bookflight” 의도와 관련됨).

반면, 의도는 전체 발화의 예측을 나타냅니다. 

## <a name="entities-represent-data"></a>데이터를 나타내는 엔터티
엔터티는 발화에서 가져오려는 데이터입니다. 이름, 날짜, 제품 이름 또는 모든 단어 그룹이 될 수 있습니다. 

|발화|엔터티|Data|
|--|--|--|
|Buy 3 tickets to New York|미리 빌드된 숫자<br>Location.Destination|3<br>뉴욕|
|Buy a ticket from New York to London on March 5|Location.Origin<br>Location.Destination<br>미리 빌드된 datetimeV2|뉴욕<br>런던<br>March 5, 2018|

## <a name="entities-are-optional-but-highly-recommended"></a>선택 사항이지만 많이 권장되는 엔터티
의도는 필수인 반면 엔터티는 선택 사항입니다. 앱의 모든 개념에 대해 엔터티를 만들 필요는 없지만 앱이 작업을 수행하는 데 필요한 개념에 대해서만 만들면 됩니다. 

발화에 봇이 계속 수행해야 하는 세부 정보가 없는 경우, 추가할 필요가 없습니다. 앱이 완성되고 나중에 추가할 수 있습니다. 

정보를 사용하는 방법을 잘 모르는 경우, datetimeV2, 서수, 메일 및 전화 번호와 같은 몇 가지 일반적인 미리 빌드된 엔터티를 추가합니다.

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

“Book me a ticket to Paris” 발화에서 “Paris”는 위치 형식의 엔터티입니다. LUIS는 사용자의 입력에서 언급된 엔터티를 인식하여 의도를 이행하기 위해 수행할 특정 조치를 선택하는 데 도움을 줍니다.

## <a name="assign-entities-in-none-intent"></a>None 의도에서 엔터티 할당
**None** 의도를 포함한 모든 의도에서 엔터티에 레이블을 지정해야 합니다. 그러면 LUIS는 발화에서 엔터티의 위치와 엔터티 주위에 있는 단어에 대해 보다 자세히 알 수 있습니다. 

## <a name="entity-status-for-predictions"></a>예측할 엔터티 상태

자세한 내용은 [엔터티 상태 예측](luis-how-to-add-example-utterances.md#entity-status-predictions)을 참조하세요. 

## <a name="types-of-entities"></a>엔터티의 형식
LUIS는 미리 빌드된 엔터티, 사용자 지정 기계 학습 엔터티, 목록 엔터티 등 많은 형식의 엔터티를 제공합니다.

| 이름 | 레이블 지정 가능 | 설명 |
| -- |--|--|
| **미리 빌드된** <br/>[사용자 지정](#prebuilt)| |  **정의**<br>일반 개념을 나타내는 기본 제공 유형입니다. <br><br>**목록**<br/>핵심 구 번호, 서수, 온도, 차원, 비용, 연령, 백분율, 메일, URL, 전화 번호 및 핵심 구. <br><br>미리 빌드된 엔터티 이름은 예약되어 있습니다. <br><br>응용 프로그램에 추가되는 모든 미리 빌드된 엔터티는 [엔드포인트](luis-glossary.md#endpoint) 쿼리에서 반환됩니다. 자세한 내용은 [미리 빌드된 엔터티](./luis-prebuilt-entities.md)를 참조하세요. <br/><br/>[엔터티에 대한 예제 응답](luis-concept-data-extraction.md#prebuilt-entity-data)|
|<!-- added week of 3/21/08 --> **정규식**<br/>[RegEx](#regex)||**정의**<br>서식이 지정된 원시 발화 텍스트에 대한 사용자 지정 정규식입니다. 대/소문자를 무시하고 문화적 변형을 무시합니다.  <br><br>이 엔터티는 항상 일관된 변형으로 일관되게 서식을 지정하는 단어나 구에 적합합니다.<br><br>정규식 일치는 맞춤법 검사 변경 후에 적용됩니다. <br><br>많은 대괄호를 사용하는 경우처럼 정규식이 너무 복잡한 경우, 모델에 식을 추가할 수 없습니다. <br><br>**예제**<br>`kb[0-9]{6,}`은 kb123456과 일치합니다.<br/><br/>[빠른 시작](luis-quickstart-intents-regex-entity.md)<br>[엔터티에 대한 예제 응답](luis-concept-data-extraction.md)|
| **간단** <br/>[기계 학습](#machine-learned) | ✔ | **정의**<br>단순 엔터티는 단일 개념을 설명하고 기계 학습 컨텍스트에서 학습되는 일반 엔터티입니다. 컨텍스트에는 단어 선택, 단어 배치 및 발화 길이가 포함됩니다.<br/><br/>일관되게 서식을 지정하지는 않지만 동일한 항목을 나타내는 단어나 구에 적합한 엔터티입니다. <br/><br/>[빠른 시작](luis-quickstart-primary-and-secondary-data.md)<br/>[엔터티에 대한 예제 응답](luis-concept-data-extraction.md#simple-entity-data)|  
| **목록** <br/>[정확한 일치](#exact-match)|| **정의**<br>목록 엔터티는 시스템의 동의어와 함께 관련 단어의 고정된 폐쇄형 집합을 나타냅니다. <br><br>각 목록 엔터티에는 하나 이상의 형식이 있을 수 있습니다. 동일한 개념을 나타내는 방법의 알려진 변형 집합에 가장 적합합니다.<br/><br/>LUIS는 목록 엔터티에 대한 추가 값을 검색하지 않습니다. **권장** 기능을 사용하여 현재 목록을 기준으로 권장되는 새 단어를 확인합니다.<br/><br>동일한 값을 갖는 목록 엔터티가 둘 이상 있는 경우, 각 엔터티가 엔드포인트 쿼리에서 반환됩니다. <br/><br/>[빠른 시작](luis-quickstart-intent-and-list-entity.md)<br>[엔터티에 대한 예제 응답](luis-concept-data-extraction.md#list-entity-data)| 
| **Pattern.any** <br/>[혼합](#mixed) | ✔|**정의**<br>Patterns.any는 엔터티가 시작되고 끝나는 위치를 표시하기 위해 패턴의 템플릿 발화에서만 사용되는 가변 길이 자리 표시자입니다.  <br><br>**예제**<br>발화에서 제목을 기준으로 도서를 검색하는 경우, pattern.any는 전체 제목을 추출합니다. pattern.any를 사용하는 템플릿 발화는 `Who wrote {BookTitle}[?]`입니다.<br/><br/>[자습서](luis-tutorial-pattern.md)<br>[엔터티에 대한 예제 응답](luis-concept-data-extraction.md#composite-entity-data)|  
| **복합** <br/>[기계 학습](#machine-learned) | ✔|**정의**<br>복합 엔터티는 미리 빌드된 엔터티, 단순, 정규식, 목록, 계층 구조 등의 다른 엔터티로 구성됩니다. 개별 엔터티가 전체 엔터티를 형성합니다. <br><br>**예제**<br>PlaneTicketOrder라는 복합 엔터티에는 미리 빌드된 `number` 및 `ToLocation` 자식 엔터티가 있을 수 있습니다. <br/><br/>[자습서](luis-tutorial-composite-entity.md)<br>[엔터티에 대한 예제 응답](luis-concept-data-extraction.md#composite-entity-data)|  
| **계층적** <br/>[기계 학습](#machine-learned) |✔ | **정의**<br>계층 구조 엔터티는 컨텍스트에 따라 학습된 단순 엔터티의 범주입니다.<br><br>**예제**<br>`Location`이라는 계층적 엔터티에 `ToLocation` 및 `FromLocation` 자식이 있을 경우 각 자식은 발화 내의 **컨텍스트**에 따라 확인할 수 있습니다. `Book 2 tickets from Seattle to New York` 발화에서 `ToLocation` 및 `FromLocation`은 주위의 단어를 기반으로 컨텍스트에 따라 달라집니다. <br/><br/>**사용하지 않는 경우**<br>컨텍스트에 관계없이 자식에 대해 정확하게 일치하는 텍스트가 있는 엔터티를 찾는 경우, 목록 엔터티를 사용해야 합니다. 다른 엔터티 형식과의 부모-자식 관계를 찾는 경우 복합 엔터티를 사용해야 합니다.<br/><br/>[빠른 시작](luis-quickstart-intent-and-hier-entity.md)<br>[엔터티에 대한 예제 응답](luis-concept-data-extraction.md#hierarchical-entity-data)|

<a name="prebuilt"></a>
**미리 빌드된** 엔터티는 LUIS에서 제공하는 사용자 지정 엔터티입니다. 이러한 엔터티 중 일부는 오픈 소스 [Recognizers-Text](https://github.com/Microsoft/Recognizers-Text) 프로젝트에 정의되어 있습니다. 지원되는 문화권에 대한 /Specs 디렉터리에 많은 [예제](https://github.com/Microsoft/Recognizers-Text/tree/master/Specs)가 있습니다. 특정 문화권이나 엔터티가 현재 지원되지 않은 경우 프로젝트에 적용됩니다. 

<a name="machine-learned"></a>
**기계 학습** 엔터티는 [엔드포인트 쿼리](luis-concept-test.md#endpoint-testing)를 통해 테스트하고 [엔드포인트 발화를 검토](luis-how-to-review-endoint-utt.md)할 대 가장 적합합니다. 

<a name="regex"></a>
**정규식 엔터티**는 사용자가 엔터티 정의의 일부로 제공하는 정규식으로 정의됩니다. 

<a name="exact-match"></a>
**정확히 일치** 엔터티는 엔터티에 제공된 텍스트를 사용하여 정확한 텍스트 일치를 만듭니다.

<a name="mixed"></a>
**혼합** 엔터티는 엔터티 검색 방법의 조합을 사용합니다.

## <a name="entity-limits"></a>엔터티 제한
모델에 추가할 수 있는 각 엔터티 형식의 수를 파악하려면 [제한](luis-boundaries.md#model-boundaries)을 검토하세요.

## <a name="roles-versus-hierarchical-entities"></a>역할 및 계층 구조 엔터티

자세한 내용은 [역할 및 계층 구조 엔터티](luis-concept-roles.md#roles-versus-hierarchical-entities)를 참조하세요.

## <a name="composite-vs-hierarchical-entities"></a>복합 및 계층적 엔터티
복합 엔터티 및 계층적 엔터티에는 둘 다 부모-자식 관계가 있으며 기계 학습됩니다. 기계 학습을 사용하면 LUIS는 다양한 컨텍스트(단어의 배열)를 기반으로 하여 엔터티를 이해할 수 있습니다. 복합 엔터티는 다양한 엔터티 형식을 자식으로 허용하므로 보다 유연합니다. 계층적 엔터티의 자식은 단순 엔터티뿐입니다. 

|type|목적|예|
|--|--|--|
|계층적|단순 엔터티의 부모-자식|Location.Origin=New York<br>Location.Destination=London|
|복합|부모-자식 엔터티: 미리 작성, 목록, 단순, 계층적| number=3<br>list=first class<br>prebuilt.datetimeV2=March 5|

## <a name="data-matching-multiple-entities"></a>여러 엔터티와 일치하는 데이터
단어나 구가 둘 이상의 엔터티와 일치하는 경우, 엔드포인트 쿼리는 각 엔터티를 반환합니다. 미리 빌드된 number 엔터티와 미리 빌드된 datetimeV2를 둘 다 추가하고 `create meeting on 2018/03/12 for lunch with wayne` 발화가 있는 경우 LUIS는 모든 엔터티를 인식하고 엔터티의 배열을 JSON 엔드포인트 응답의 일부로 반환합니다. 

```JSON
{
  "query": "create meeting on 2018/03/12 for lunch with wayne",
  "topScoringIntent": {
    "intent": "Calendar.Add",
    "score": 0.9333419
  },
  "entities": [
    {
      "entity": "2018/03/12",
      "type": "builtin.datetimeV2.date",
      "startIndex": 18,
      "endIndex": 27,
      "resolution": {
        "values": [
          {
            "timex": "2018-03-12",
            "type": "date",
            "value": "2018-03-12"
          }
        ]
      }
    },
    {
      "entity": "2018",
      "type": "builtin.number",
      "startIndex": 18,
      "endIndex": 21,
      "resolution": {
        "value": "2018"
      }
    },
    {
      "entity": "03/12",
      "type": "builtin.number",
      "startIndex": 23,
      "endIndex": 27,
      "resolution": {
        "value": "0.25"
      }
    }
  ]
}
```

## <a name="data-matching-multiple-list-entities"></a>여러 목록 엔터티와 일치하는 데이터
단어나 구가 둘 이상의 목록 엔터티와 일치하는 경우, 엔드포인트 쿼리는 각 목록 엔터티를 반환합니다.

`when is the best time to go to red rock?` 쿼리에서 앱이 둘 이상의 목록에 `red`라는 단어를 갖는 경우 LUIS는 모든 엔터티를 인식하고 엔터티의 배열을 JSON 엔드포인트 응답의 일부로 반환합니다. 

```JSON
{
  "query": "when is the best time to go to red rock?",
  "topScoringIntent": {
    "intent": "Calendar.Find",
    "score": 0.06701678
  },
  "entities": [
    {
      "entity": "red",
      "type": "Colors",
      "startIndex": 31,
      "endIndex": 33,
      "resolution": {
        "values": [
          "Red"
        ]
      }
    },
    {
      "entity": "red rock",
      "type": "Cities",
      "startIndex": 31,
      "endIndex": 38,
      "resolution": {
        "values": [
          "Destinations"
        ]
      }
    }
  ]
}
```

## <a name="if-you-need-more-than-the-maximum-number-of-entities"></a>최대 엔터티 수보다 더 많이 필요한 경우 

계층적 및 복합 엔터티를 사용해야 할 수 있습니다. 계층적 엔터티는 특성을 공유하거나 범주의 멤버인 엔터티 간 관계를 반영합니다. 자식 엔터티는 모두 부모 범주의 멤버입니다. 예를 들어, PlaneTicketClass라는 계층적 엔터티에는 EconomyClass 및 FirstClass라는 자식 엔터티가 있을 수 있습니다. 계층 구조의 범위는 한 수준의 깊이로만 구성됩니다.  

복합 엔터티는 전체의 일부를 나타냅니다. 예를 들어, PlaneTicketOrder라는 복합 엔터티에는 Airline, Destination, DepartureCity, DepartureDate 및 PlaneTicketClass라는 자식 엔터티가 있을 수 있습니다. 복합 엔터티는 기존 단순 엔터티, 계층적 엔터티의 자식 또는 미리 빌드된 엔터티로 작성합니다.  

LUIS는 기계 학습되지 않고 LUIS 앱에서 고정된 값 목록을 지정할 수 있도록 하는 목록 엔터티 형식도 제공합니다. 목록 엔터티 형식의 제한을 검토하려면 [LUIS 경계](luis-boundaries.md) 참조를 참조하세요. 

계층적, 복합 및 목록 엔터티를 고려하고 있는데 제한보다 더 많이 필요한 경우, 지원 담당자에게 문의하세요. 이렇게 하려면 시스템에 대한 자세한 정보를 수집하고 [LUIS](luis-reference-regions.md#luis-website) 웹 사이트로 이동한 다음, **지원**을 선택하세요. Azure 구독에 지원 서비스가 포함된 경우, [Azure 기술 지원](https://azure.microsoft.com/support/options/)에 문의하세요. 

## <a name="best-practices"></a>모범 사례

호출하는 응용 프로그램이나 봇에서 작업을 수행하는 데 필요한 발화의 일부 매개 변수나 데이터가 필요한 경우에 [엔터티](luis-concept-entity-types.md)를 만듭니다. 엔터티는 함수의 매개 변수처럼 발화에서 추출해야 하는 단어 또는 구입니다. 

응용 프로그램에 추가할 올바른 엔터티 형식을 선택하려면 사용자가 해당 데이터를 입력하는 방법을 알고 있어야 합니다. 각 엔터티 형식은 기계 학습, 폐쇄형 목록 또는 정규식과 같은 다양한 메커니즘을 사용하여 찾을 수 있습니다. 확실하지 않은 경우, 단순 엔터티로 시작하고 None 의도를 포함한 모든 의도의 모든 발화에서 해당 데이터를 나타내는 단어나 구에 레이블을 지정합니다.  

정기적으로 엔드포인트 발화를 검토하여 엔터티를 정규식으로 식별하거나 정확한 텍스트 일치를 찾을 수 있는 일반적인 사용 패턴을 찾습니다.  

검토의 일부로 구 목록을 추가하여 도메인에는 중요하지만 정확히 일치하지는 않는 단어나 구 및 신뢰도가 높지 않은 LUIS에 대한 신호를 LUIS에 추가하는 것이 좋습니다.  

자세한 내용은 [모범 사례](luis-concept-best-practices.md)를 참조하세요.

## <a name="next-steps"></a>다음 단계

좋은 [발화](luis-concept-utterance.md)에 대한 개념을 알아보세요. 

LUIS 앱에 엔터티를 추가하는 방법에 대한 자세한 내용은 [엔터티 추가](luis-how-to-add-entities.md)를 참조하세요.