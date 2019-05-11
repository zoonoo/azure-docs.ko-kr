---
title: '기능: 작업 및 컨텍스트 - Personalizer'
titleSuffix: Azure Cognitive Services
description: Personalizer는 작업 및 컨텍스트에 관한 정보인 기능을 사용하여 더 나은 순위를 추천합니다. 기능은 매우 일반적일 수도 있고 항목에 대해 구체적일 수도 있습니다.
services: cognitive-services
author: edjez
manager: nitinme
ms.service: cognitive-services
ms.subservice: personalizer
ms.topic: overview
ms.date: 05/07/2019
ms.author: edjez
ms.openlocfilehash: ebe7f9307fcfa39d6cb133203a4c17243ad390c5
ms.sourcegitcommit: 4b9c06dad94dfb3a103feb2ee0da5a6202c910cc
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/02/2019
ms.locfileid: "65026663"
---
# <a name="features-are-information-about-actions-and-context"></a>기능은 작업 및 컨텍스트에 관한 정보입니다.

Personalizer 서비스는 애플리케이션이 지정된 컨텍스트에서 사용자에게 표시해야 하는 내용을 학습하는 방법으로 작동합니다.

Personalizer는 **현재 컨텍스트**에 관한 정보인 **기능**을 사용하여 최선의 **작업**을 선택합니다. 기능은 더 높은 보상을 달성하도록 맞춤화하는 데 도움이 될 수 있다고 생각하는 모든 정보를 나타냅니다. 기능은 매우 일반적일 수도 있고 항목에 대해 구체적일 수도 있습니다. 

예를 들어 다음 사항에 관한 **기능**이 있을 수 있습니다.

* _사용자_(예: `UserID`) 
* _콘텐츠_(예: 동영상이 `Documentary`, `Movie` 또는 `TV Series` 중 어느 유형인지, 또는 소매 항목이 점포에서 구입할 수 있는지 여부)
* _현재_ 기간(예: 요일)

Personalizer는 작업 및 컨텍스트에 대해 보낼 수 있는 기능이 무엇인지 규정하거나 제한하거나 고정하지 않습니다.

* 일부 작업을 일부 작업에 대해 보내고 다른 작업에 대해서는 보내지 않을 수 있습니다(없는 경우). 예를 들어 TV 시리즈에는 동영상에 없는 특성이 있을 수 있습니다.
* 일부 기능은 일부 시간에만 사용 가능할 수 있습니다. 예를 들어 모바일 애플리케이션은 웹 페이지보다 더 많은 기능을 제공할 수 있습니다. 
* 시간이 경과함에 따라 컨텍스트 및 작업에 관한 기능을 추가 및 제거할 수 있습니다. Personalizer는 사용 가능한 정보에서 학습을 계속합니다.
* 컨텍스트에 대해 적어도 한 개의 기능이 있어야 합니다. Personalizer는 비어 있는 컨텍스트를 지원하지 않습니다. 언제나 고정된 컨텍스트만 보내는 경우 Personalizer는 작업의 기능에 관한 순위에 대해서만 작업을 선택합니다. 
* Personalizer는 언제든지 모든 사람에게 가장 적합한 작업을 선택하려고 합니다.

## <a name="supported-feature-types"></a>지원되는 기능 형식

Personalizer는 문자열, 숫자 및 부울 형식의 기능을 지원합니다.

존재하지 않는 기능은 요청에서 생략해야 합니다. Null 값은 모델을 학습할 때 기능이 존재하고 값이 "null"인 것으로 처리되므로 Null 값을 포함하는 기능은 보내지 않아야 합니다.

## <a name="categorize-features-with-namespaces"></a>네임스페이스로 기능 분류

Personalizer는 네임스페이스로 구성된 기능을 인식합니다. 사용자는 자신의 애플리케이션에서 네임스페이스를 사용하는지 여부 및 사용하는 네임스페이스를 결정합니다. 네임스페이스는 유사한 주제에 관한 기능 또는 특정 소스에서 오는 기능을 그룹화하기 위해 사용됩니다.

다음은 애플리케이션에 사용하는 기능 네임스페이스의 예입니다.

* User_Profile_from_CRM
* Time
* Mobile_Device_Info
* http_user_agent
* VideoResolution
* UserDeviceInfo
* Weather
* Product_Recommendation_Ratings
* current_time
* NewsArticle_TextAnalytics

규칙이 유효한 JSON 키인 경우 자기만의 규칙에 따라 기능 네임스페이스의 이름을 지정할 수 있습니다.

다음 JSON에서 `user`, `state` 및 `device`는 기능 네임스페이스입니다.

```JSON
{
    "contextFeatures": [
        { 
            "user": {
                "name":"Doug"
            }
        },
        {
            "state": {
                "timeOfDay": "noon",
                "weather": "sunny"
            }
        },
        {
            "device": {
                "mobile":true,
                "Windows":true
            }
        }
    ]
}
```

## <a name="how-to-make-feature-sets-more-effective-for-personalizer"></a>기능 세트를 Personalizer에 더 효과적으로 만드는 방법

좋은 기능 세트는 Personalizer에서 가장 높은 보상을 달성할 작업을 예측하는 방법을 학습하는 데 도움이 됩니다. 

다음 권장 사항을 따르는 Personalizer 순위 API에 기능을 보내는 것을 검토합니다.

* 맞춤화를 달성하기에 충분한 기능이 있습니다. 콘텐츠가 달성해야 하는 목표가 더 정밀할수록 더 많은 기능이 필요합니다.

* 다양한 *정교성*의 충분한 기능이 있습니다. 기능은 많은 항목이 소수의 버킷에 그룹화된 경우 *정교*합니다. 예를 들어 동영상 수천 개를 "긴"(5분이 넘는 길이) 동영상과 "짧은"(5분 미만 길이) 동영상으로 분류할 수 있습니다. 이는 *매우 정교한* 기능입니다. 반면에 같은 항목 수천 개가 항목 간에 동일한 값을 거의 갖지 않을 "제목"이라는 특성을 포함할 수 있습니다. 이는 그다지 정교하지 않거나 *대략적인* 기능입니다.  

정교성이 높은 기능을 포함하면 Personalizer가 항목 간에 학습을 추정하는 데 도움이 됩니다. 그러나 기능이 몇 개만 있고 해당 기능이 너무 정교한 경우 Personalizer는 몇몇 버킷만 포함하는 콘텐츠를 선택하도록 대상을 정밀하게 설정하려고 합니다.

### <a name="improve-feature-sets"></a>기능 세트 개선 

오프라인 평가를 수행하여 사용자 행동을 분석합니다. 이렇게 하면 과거 데이터를 조사하여 긍정적 보상에 크게 기여하는 기능과 기여도가 작은 기능을 확인할 수 있습니다. 유용한 기능을 확인할 수 있으면 사용자 및 애플리케이션에서 결과를 훨씬 더 개선하기 위해 Personalizer에 보내기에 더 유리한 기능을 선택할 수 있습니다.

다음 기능 섹션은 Personalizer에 보내는 기능을 개선하기 위한 일반적인 방법입니다.

#### <a name="make-features-more-dense"></a>기능을 더 정교하게 만들기

기능 세트를 더 크고 더 정교하게 또는 덜 정교하게 만들도록 편집하여 개선할 수 있습니다.

예를 들어 초 단위까지의 타임스탬프는 매우 대략적인 기능입니다. 시간을 "아침", "한낮", "오후" 등으로 분류하여 더 정교하게(효과적으로) 만들 수 있습니다.


#### <a name="expand-feature-sets-with-extrapolated-information"></a>추정한 정보를 사용하여 기능 세트 확장

이미 가지고 있는 정보에서 추정할 수 있는 암묵적 특성을 생각하여 더 많은 기능을 얻을 수도 있습니다. 예를 들어 가상의 동영상 목록을 맞춤화하는 경우 주말과 평일에 사용자의 행동이 서로 다르다고 추정할 수 있습니까? 시간은 "주말" 또는 "평일" 특성을 갖도록 확장할 수 있습니다. 국경일에는 특정 동영상 유형에 주의를 기울이게 될까요? 예를 들어 "핼러윈" 특성은 핼러윈 행사와 관련된 장소에서 유용합니다. 비가 오는 날씨는 많은 사람의 동영상 선택에 크게 영향을 미칠 수 있습니까? 기상 서비스에서 시간 및 장소와 함께 해당 정보를 제공하면 사용자는 해당 서비스를 특별한 기능으로 추가할 수 있습니다. 

#### <a name="expand-feature-sets-with-artificial-intelligence-and-cognitive-services"></a>AI 및 Cognitive Services를 사용하여 기능 세트 확장

AI 및 즉시 실행할 수 있는 Cognitive Services는 Personalizer에 매우 강력한 추가 기능이 될 수 있습니다. 

AI 서비스를 사용하여 항목을 미리 처리하면 맞춤화에 유용할 가능성이 있는 정보를 자동으로 추출할 수 있습니다.

예: 

* [Video Indexer](https://azure.microsoft.com/services/media-services/video-indexer/)를 통해 동영상 파일을 실행하여 장면 요소, 텍스트, 감정 및 기타 많은 특성을 추출할 수 있습니다. 그런 다음, 원본 항목 메타데이터에 없었던 특성을 반영하도록 해당 특성을 더 정교하게 만들 수 있습니다. 
* 개체 감지를 통해 이미지를 반영하고 감정을 통해 얼굴을 반영할 수 있는 식입니다.
* 감정 엔터티를 추출하고 Bing 지식 그래프 등을 사용하여 엔터티를 확장하면 텍스트의 정보를 늘릴 수 있습니다.

다음과 같은 여러 가지 다른 [Azure Cognitive Services](https://www.microsoft.com/cognitive-services)를 사용할 수 있습니다.

* [엔터티 연결](../entitylinking/home.md)
* [텍스트 분석](../text-analytics/overview.md)
* [Emotion](../emotion/home.md)
* [Computer Vision](../computer-vision/home.md)

## <a name="actions-represent-a-list-of-options"></a>작업은 옵션 목록을 나타냅니다.

각 작업:

* ID를 갖습니다.
* 기능 목록을 갖습니다.
* 기능 목록은 클(수백 개) 수도 있지만 보상 얻기에 기여하지 않는 기능을 제거하기 위해 기능 효과를 평가하는 것이 좋습니다. 
* **작업**의 기능은 Personalizer에서 사용하는 **컨텍스트**의 기능과 상관성이 있을 수도 있고 없을 수도 있습니다.
* 작업에 대한 기능은 일부 작업에는 존재하고 다른 작업에는 존재하지 않을 수 있습니다. 
* 특정 작업 ID에 대한 기능은 하루 동안 사용할 수 있지만 그 후에는 사용 불가능하게 될 수 있습니다. 

Personalizer의 기계 학습 알고리즘은 안정된 기능 세트가 있을 때 더 잘 수행되지만, 기능 세트가 시간에 따라 변화하더라도 순위 호출은 실패하지 않습니다.

작업 순위를 지정할 때 50개보다 많은 작업 단위로 보내지 않습니다. 이는 매번 50개의 작업과 같지만 달라질 수도 있습니다. 예를 들어 항목 10,000개로 이루어진 전자 상거래 애플리케이션용 제품 카탈로그가 있다면 추천 또는 필터링 엔진을 사용하여 상위 40대 고객과 같은 것을 결정하고 Personalizer를 사용하여 현재 컨텍스트에서 가장 많은 보상을 창출하는(예를 들어 사용자가 장바구니에 추가하는) 작업을 찾을 수 있습니다.


### <a name="examples-of-actions"></a>작업 예제

순위 API에 보내는 작업은 맞춤화하려는 목적에 따라 달라집니다.

예를 들어 다음과 같은 노래를 선택할 수 있다.

|목적|조치|
|--|--|
|새 웹 사이트에서 강조 표시되는 문서를 맞춤화합니다.|각 작업은 잠재적인 뉴스 기사입니다.|
|웹 사이트에 대한 광고 위치를 최적화합니다.|각 작업은 광고용 레이아웃을 만들기 위한 레이아웃 또는 규칙입니다(예: 맨 위, 오른쪽, 작은 이미지, 큰 이미지).|
|추천 항목의 맞춤화한 순위를 쇼핑 웹 사이트에 표시합니다.|각 작업은 특정 제품입니다.|
|특정 사진에 적용할 필터와 같은 사용자 인터페이스 요소를 추천합니다.|각 작업은 서로 다른 필터일 수 있습니다.|
|사용자 의도를 구체화하거나 작업을 추천하기 위한 채팅 봇의 응답을 선택합니다.|각 작업은 응답을 해석하는 방법에 대한 옵션입니다.|
|검색 결과 목록의 맨 위에 표시할 내용 선택|각 작업은 맨 위에 있는 몇몇 검색 결과 중 하나입니다.|


### <a name="examples-of-features-for-actions"></a>작업에 대한 기능의 예

다음은 작업에 대한 기능의 좋은 예입니다. 이는 각 애플리케이션에 따라 크게 달라집니다.

* 작업의 특성을 포함하는 기능 예를 들어 동영상입니까 아니면 TV 시리즈입니까?
* 과거에 이 작업과 상호 작용했을 수 있는 사용자 수에 관한 기능. 예를 들어 이 동영상은 주로 인구통계학상 A 또는 B에 속하는 사람들이 보며, 일반적으로 한 번만 재생합니다.
* 작업을 *보는* 사용자 수의 특성에 관한 기능. 예를 들어 썸네일에 표시된 동영상의 포스터는 얼굴, 자동차 또는 경치를 포함합니까?

### <a name="load-actions-from-the-client-application"></a>클라이언트 애플리케이션의 작업 로드

작업의 기능은 일반적으로 콘텐츠 관리 시스템, 카탈로그 및 추천 시스템에서 나올 수 있습니다. 관련 데이터베이스 및 사용자가 보유하고 있는 시스템에서 작업에 관한 정보를 로드하는 것은 애플리케이션에서 처리해야 합니다. 작업이 변하지 않거나 작업을 매번 로드하면 성능에 불필요한 영향을 미치는 경우 이 정보를 캐시에 기억하는 논리를 애플리케이션에 추가할 수 있습니다.

### <a name="prevent-actions-from-being-ranked"></a>작업의 순위 지정 금지

경우에 따라 사용자에게 표시하지 않으려는 작업이 있습니다. 작업이 맨 위의 항목으로 순위 지정되는 것을 금지하는 최선의 방법은 순위 API에 대한 작업 목록의 첫 번째 자리에 포함하지 않는 것입니다.

경우에 따라 순위 API 호출의 결과로 얻는 _작업_이 사용자에게 표시되어야 하는지 여부는 나중에 비즈니스 논리에서만 결정할 수 있습니다. 이러한 경우 _대화형 이벤트_를 사용하는 것이 좋습니다.

## <a name="json-format-for-actions"></a>작업에 대한 JSON 형식

순위를 호출할 때에는 여러 작업을 보내고 해당 작업 중에서 선택하게 합니다.

```json
{
    "actions": [
    {
      "id": "pasta",
      "features": [
        {
          "taste": "salty",
          "spiceLevel": "medium"
        },
        {
          "nutritionLevel": 5,
          "cuisine": "italian"
        }
      ]
    },
    {
      "id": "ice cream",
      "features": [
        {
          "taste": "sweet",
          "spiceLevel": "none"
        },
        {
          "nutritionalLevel": 2
        }
      ]
    },
    {
      "id": "juice",
      "features": [
        {
          "taste": "sweet",
          "spiceLevel": "none"
        },
        {
          "nutritionLevel": 5
        },
        {
          "drink": true
        }
      ]
    },
    {
      "id": "salad",
      "features": [
        {
          "taste": "salty",
          "spiceLevel": "low"
        },
        {
          "nutritionLevel": 8
        }
      ]
    }
  ]
}
```

## <a name="examples-of-context-information"></a>컨텍스트 정보의 예

_컨텍스트_에 대한 정보는 각 애플리케이션 및 사용 사례에 따라 달라지지만, 일반적으로 다음과 같은 정보를 포함할 수 있습니다.

* 사용자에 관한 인구통계학상 및 프로필 정보
* 사용자 에이전트와 같은 HTTP 헤더에서 추출한 정보, 또는 IP 주소에 기초한 역방향 지리적 조회와 같은 HTTP 정보에서 유도한 정보
* 현재 시간에 관한 정보(예: 주, 주말 여부, 아침 또는 오후, 휴가철 여부 등)
* 모바일 애플리케이션에서 추출한 정보(예: 위치, 이동 또는 배터리 레벨)
* 사용자 행동의 기록 집계(예: 이 사용자가 가장 많이 본 동영상 장르)

관련 데이터베이스, 센서 및 사용자가 보유하고 있는 시스템에서 컨텍스트에 관한 정보를 로드하는 것은 애플리케이션에서 처리해야 합니다. 컨텍스트 정보가 변하지 않는 경우 순위 API에 보내기 전에 애플리케이션에서 이 정보를 캐시에 기억하는 논리를 추가할 수 있습니다.

## <a name="json-format-for-context"></a>컨텍스트에 대한 JSON 형식 

컨텍스트는 순위 API에 보내는 JSON 개체로 표시됩니다.

```JSON
{
    "contextFeatures": [
        { 
            "user": {
                "name":"Doug"
            }
        },
        {
            "state": {
                "timeOfDay": "noon",
                "weather": "sunny"
            }
        },
        {
            "device": {
                "mobile":true,
                "Windows":true
            }
        }
    ]
}
```

## <a name="next-steps"></a>다음 단계

[보충 학습](concepts-reinforcement-learning.md) 