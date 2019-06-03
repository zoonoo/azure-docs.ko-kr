---
title: Project Answer Search란?
titlesuffix: Azure Cognitive Services
description: Project Answer Search 소개입니다.
services: cognitive-services
author: mikedodaro
manager: nitinme
ms.service: cognitive-services
ms.subservice: answer-search
ms.topic: overview
ms.date: 04/13/2018
ms.author: rosh
ms.openlocfilehash: ac1717a8e8a08fcfedc3bc21bb0f03b3e3ca2511
ms.sourcegitcommit: 509e1583c3a3dde34c8090d2149d255cb92fe991
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/27/2019
ms.locfileid: "60721854"
---
# <a name="what-is-project-answer-search"></a>Project Answer Search란?
Project Answer Search API는 Bing v7 엔드포인트를 사용하여 질문 형태의 쿼리에 대한 답변을 가져옵니다. “What is the circumference of the earth?”와 같은 질문은 팩트 정보가 포함된 답변을 반환합니다.  사람, 장소 또는 사물에 대한 쿼리는 쿼리에 의해 식별된 엔터티에 대한 정보를 반환합니다. 대화 봇, 메시징 앱, 리더 등과 같은 애플리케이션에서는 이러한 시나리오가 유용할 수 있습니다.  

쿼리는 쿼리 시나리오에 종속된 응답을 반환합니다. 웹 페이지는 항상 반환되는 반면, [팩트](fact-queries.md) 및/또는 [엔터티](entity-queries.md)는 관련된 경우에 반환됩니다.

## <a name="endpoint"></a>엔드포인트
사람, 장소 또는 사물에 대한 정보나 질문에 대한 답변을 가져오려면 Answer Search API 엔드포인트에 요청을 보냅니다. 다양한 사양을 위해 헤더 및 URL 매개 변수를 사용합니다.  유효한 토큰이 포함된 *Ocp-Apim-Subscription-Key* 헤더를 포함합니다.  지역/국가 매개 변수가 필요합니다. 현재, `en-us` 지역/국가만 지원됩니다.

다음 쿼리는 “지구의 둘레는 얼마인가요?”라는 질문에 대한 답을 가져옵니다.

GET:
```
https://api.labs.cognitive.microsoft.com/answerSearch/v7.0/search?q=what+is+circumference+of+the=earth?&mkt=en-us

```

검색의 개체를 지정하려면 URL 매개 변수 `q=`가 필요합니다.

## <a name="response-object"></a>응답 개체

응답에는 HTTP 헤더, 웹 페이지, 팩트 및/또는 엔터티가 포함됩니다.

```
BingAPIs-TraceId: AB2E75C998614ADB8EBF5110DF648298
X-MSEdge-ClientID: 1E48FC4F7B8768C80B14F7997A106906
BingAPIs-SessionId: 0504DDD6DAE84861A4842306F8DA7A58
BingAPIs-Market: en-US
X-MSEdge-Ref: Ref A: AB2E75C998614ADB8EBF5110DF648298 Ref B: CO1EDGE0322 Ref C: 2018-04-19T19:57:13Z

JSON Response:

{
  "_type": "SearchResponse",
  "queryContext": {
    "originalQuery": "what is the circumference of earth"
  },
  "webPages": {
    "webSearchUrl": "https://www.bing.com/search?q\u003dwhat+is+the+circumference+of+earth",
    "totalEstimatedMatches": 217000,
    "value": [
      {
        "id": "https://www.bingapis.com/api/v7/#WebPages.0",
        "name": "Circumference of the Earth - Universe Today",
        "url": "https://www.universetoday.com/26461/circumference-of-the-earth/",
        "isFamilyFriendly": true,
        "displayUrl": "https://www.universetoday.com/26461/circumference-of-the-earth",
        "snippet": "The circumference of the Earth in kilometers is 40,075 km, and the circumference of the Earth in miles is 24,901. In other words, if you could drive your car around the equator of the Earth (yes, even over the oceans), youâ€™d put on an extra 40,075 km on the odometer.",
        "deepLinks": [
          {
            "name": "About Earth",
            "url": "https://www.universetoday.com/14382/10-interesting-facts-about-planet-earth/"
          }
        ],
        "dateLastCrawled": "2018-04-12T14:13:00.0000000Z",
        "language": "en"
      },
      {
        "id": "https://www.bingapis.com/api/v7/#WebPages.1",
        "name": "Earth - Wikipedia",
        "url": "https://en.wikipedia.org/wiki/Earth",
        "about": [
          {
            "name": "Earth"
          },
          {
            "name": "Earth"
          }
        ],
        "isFamilyFriendly": true,
        "displayUrl": "https://en.wikipedia.org/wiki/Earth",
        "snippet": "Circumference: 40 075.017 km equatorial (24 901.461 mi) ... Earth is the third planet from the Sun and the only object in the Universe known to harbor life.",
        "deepLinks": [
          {
            "name": "Moon",
            "url": "https://en.wikipedia.org/wiki/Moon"
          },
          {
            "name": "Planet",
            "url": "https://en.wikipedia.org/wiki/Planet"
          },
          {
            "name": "Quasi-Satellites",
            "url": "https://en.wikipedia.org/wiki/Quasi-satellite"
          },
          {
            "name": "World Population",
            "url": "https://en.wikipedia.org/wiki/World_population"
          },
   . . .

    ]
  },
  "entities": {
    "value": [
      {
        "id": "https://www.bingapis.com/api/v7/#Entities.0",
        "contractualRules": [
          {
            "_type": "ContractualRules/LicenseAttribution",
            "targetPropertyName": "description",
            "mustBeCloseToContent": true,
            "license": {
              "name": "CC-BY-SA",
              "url": "https://creativecommons.org/licenses/by-sa/3.0/"
            },
            "licenseNotice": "Text under CC-BY-SA license"
          },
          {
            "_type": "ContractualRules/LinkAttribution",
            "targetPropertyName": "description",
            "mustBeCloseToContent": true,
            "text": "Wikipedia",
            "url": "https://en.wikipedia.org/wiki/Earth"
          },
          {
            "_type": "ContractualRules/MediaAttribution",
            "targetPropertyName": "image",
            "mustBeCloseToContent": true,
            "url": "https://en.wikipedia.org/wiki/Earth"
          }
        ],
        "webSearchUrl": "https://www.bing.com/entityexplore?q\u003dEarth\u0026filters\u003dsid:%226ddb3372-4801-5567-321e-e8a53bd774a4%22\u0026elv\u003dAXXfrEiqqD9r3GuelwApulpmymQx!ODfuQu*veOQHkvP0!Zbvi5F5tVcMSDJvDEWiQWwrdueYTtIszgj03oFQHykYYLYgq3q5!Sf00QxXGIS",
        "name": "Earth",
        "image": {
          "name": "Earth",
          "thumbnailUrl": "https://www.bing.com/th?id\u003dA3ab623665ab412f386c162bd29f0683a\u0026w\u003d110\u0026h\u003d110\u0026c\u003d7\u0026rs\u003d1\u0026qlt\u003d80\u0026cdv\u003d1\u0026pid\u003d16.1",
          "provider": [
            {
              "_type": "Organization",
              "url": "https://en.wikipedia.org/wiki/Earth"
            }
          ],
          "hostPageUrl": "https://upload.wikimedia.org/wikipedia/commons/9/97/The_Earth_seen_from_Apollo_17.jpg",
          "width": 110,
          "height": 110,
          "sourceWidth": 799,
          "sourceHeight": 800
        },
        "description": "Earth is the third planet from the Sun and the only object in the Universe known to harbor life. According to radiometric dating and other sources of evidence, Earth formed over 4.5 billion years ago. Earth\u0027s gravity interacts with other objects in space, especially the Sun and the Moon, Earth\u0027s only natural satellite. Earth revolves around the Sun in 365.26 days, a period known as an Earth year. During this time, Earth rotates about its axis about 366.26 times.",
        "entityPresentationInfo": {
          "entityScenario": "DominantEntity",
          "entityTypeHints": [
            "Generic"
          ]
        },
        "bingId": "6ddb3372-4801-5567-321e-e8a53bd774a4"
      }
    ]
  },
  "facts": {
    "id": "https://www.bingapis.com/api/v7/#Facts",
    "contractualRules": [
      {
        "_type": "ContractualRules/LinkAttribution",
        "text": "www.universetoday.com/26461/circumference-of-the-earth/",
        "url": "http://www.universetoday.com/26461/circumference-of-the-earth/"
      }
    ],
    "attributions": [
      {
        "providerDisplayName": "www.universetoday.com/26461/circumference-of-the-earth/",
        "seeMoreUrl": "http://www.universetoday.com/26461/circumference-of-the-earth/"
      }
    ],
    "value": [
      {
        "description": "The circumference of the Earth in kilometers is 40,075 km, and the circumference of the Earth in miles is 24,901. In other words, if you could drive your car around the equator of the Earth (yes, even over the oceans), youâ€™d put on an extra 40,075 km on the odometer.",
        "subjectName": ""
      }
    ]
  },
  "rankingResponse": {
    "mainline": {
      "items": [
        {
          "answerType": "Facts",
          "value": {
            "id": "https://www.bingapis.com/api/v7/#Facts"
          }
        },
        {
          "answerType": "WebPages",
          "resultIndex": 0,
          "value": {
            "id": "https://www.bingapis.com/api/v7/#WebPages.0"
          }
        },
        {
          "answerType": "WebPages",
          "resultIndex": 1,
          "value": {
            "id": "https://www.bingapis.com/api/v7/#WebPages.1"
          }
        },
        {
          "answerType": "WebPages",
          "resultIndex": 2,
          "value": {
            "id": "https://www.bingapis.com/api/v7/#WebPages.2"
          }
        },
        
        . . . 
      ]
    },
    "sidebar": {
      "items": [
        {
          "answerType": "Entities",
          "resultIndex": 0,
          "value": {
            "id": "https://www.bingapis.com/api/v7/#Entities.0"
          }
        }
      ]
    }
  }
}

```

## <a name="terms-of-use"></a>사용 약관
Project Answer Search 및 Project Video Trends에는 [Bing Search 사용 및 표시 요구 사항](use-display-requirements.md)이 적용됩니다.

사용자 또는 사용자를 대신한 제3자는 모든 타사 서비스나 기능을 테스트, 개발, 학습, 배포 또는 제공하기 위해 URL Preview API의 모든 데이터를 사용, 유지, 저장, 캐시, 공유 또는 배포할 수 없습니다. 

## <a name="throttling-requests"></a>제한 요청

[!INCLUDE [cognitive-services-bing-throttling-requests](../../../../includes/cognitive-services-bing-throttling-requests.md)]


## <a name="data-attribution"></a>데이터 특성  

Project Answer Search 응답에는 제3자가 소유한 정보가 포함됩니다. 예를 들어 사용자 환경에서 사용할 수 있는 모든 크리에이티브 커먼즈 라이선스를 준수하여 사용이 적절하도록 할 책임이 있습니다.  
  
답변 또는 결과에 `contractualRules`, `attributions` 또는 `provider` 필드가 포함된 경우 데이터를 귀속해야 합니다. 답변에 이러한 필드가 포함되지 않은 경우에는 특성이 필요 없습니다. 답변에 `contractualRules` 필드와 `attributions` 및/또는 `provider` 필드가 포함된 경우 계약 규칙을 사용하여 데이터를 귀속해야 합니다.  
  
다음 예제는 MediaAttribution 계약 규칙을 포함하는 엔터티와 `provider` 필드를 포함하는 이미지를 보여 줍니다. MediaAttribution 규칙은 이미지를 규칙의 대상으로 식별하므로 이미지의 `provider` 필드를 무시하고, 대신 MediaAttribution 규칙을 사용하여 특성을 제공합니다.  
  
```  
        "value" : [{
            "contractualRules" : [
                . . .
                {
                    "_type" : "ContractualRules\/MediaAttribution",
                    "targetPropertyName" : "image",
                    "mustBeCloseToContent" : true,
                    "url" : "http:\/\/en.wikipedia.org\/wiki\/Space_Needle"
                }
            ],
            . . .
            "image" : {
                "name" : "Space Needle",
                "thumbnailUrl" : "https:\/\/www.bing.com\/th?id=A46378861201...",
                "provider" : [{
                    "_type" : "Organization",
                    "url" : "http:\/\/en.wikipedia.org\/wiki\/Space_Needle"
                }],
                "hostPageUrl" : "http:\/\/www.citydictionary.com\/Uploaded...",
                "width" : 110,
                "height" : 110
            },
            . . .
        }]
```  
  
계약 규칙에 `targetPropertyName` 필드가 포함된 경우 규칙은 대상 필드에만 적용됩니다. 포함되지 않은 경우에는 `contractualRules` 필드를 포함하는 부모 개체에 규칙이 적용됩니다.  
  
  
다음 예제에서 `LinkAttribution` 규칙은 `targetPropertyName` 필드를 포함하므로 `description` 필드에 규칙이 적용됩니다. 특정 필드에 적용되는 규칙의 경우 공급자 웹 사이트에 대한 하이퍼링크를 포함하는 줄을 대상 데이터 바로 다음에 포함해야 합니다. 예를 들어 설명을 귀속하려면 공급자 웹 사이트 데이터에 대한 하이퍼링크를 포함하는 줄을 설명 텍스트 바로 다음에 포함합니다. 이 경우에는 en.wikipedia.org에 대한 링크를 만듭니다.  
  
```  
"entities" : {  
    "value" : [{  
            . . .  
            "description" : "Peyton Williams Manning is a former American....",  
            . . .  
            "contractualRules" : [{  
                    "_type" : "ContractualRules\/LinkAttribution",  
                    "targetPropertyName" : "description",  
                    "mustBeCloseToContent" : true,  
                    "text" : "en.wikipedia.org",  
                    "url" : "http:\/\/www.bing.com\/cr?IG=B8AD73..."  
                 },  
            . . .  
  
```  

### <a name="license-attribution"></a>라이선스 특성  

계약 규칙 목록에 [LicenseAttribution](https://docs.microsoft.com/rest/api/cognitiveservices/bing-entities-api-v7-reference#licenseattribution) 규칙이 포함된 경우, 라이선스가 적용되는 콘텐츠 바로 다음 줄에 고지 사항을 표시해야 합니다. `LicenseAttribution` 규칙은 `targetPropertyName` 필드를 사용하여 라이선스가 적용되는 자산을 식별합니다.  
  
다음은 `LicenseAttribution` 규칙을 포함하는 예제를 보여 줍니다.  
  
![라이선스 특성](./media/licenseattribution.png)  
  
표시하는 라이선스 고지 사항에는 라이선스 정보를 포함하는 웹 사이트에 대한 하이퍼링크가 포함되어야 합니다. 일반적으로 라이선스 이름을 하이퍼링크로 만듭니다. 예를 들어 고지 사항이 **CC-BY-SA 라이선스가 적용되는 텍스트**이고 CC-BY-SA가 라이선스 이름인 경우 CC-BY-SA를 하이퍼링크로 만듭니다.  
  
### <a name="link-and-text-attribution"></a>링크 및 텍스트 특성  

일반적으로 [LinkAttribution](reference.md#linkattribution) 및 [TextAttribution](reference.md#textattribution) 규칙은 데이터 공급자를 식별하는 데 사용됩니다. `targetPropertyName` 필드는 규칙이 적용되는 필드를 식별합니다.  
  
공급자를 귀속하려면 특성이 적용되는 콘텐츠(예: 대상 필드) 바로 다음에 줄을 포함합니다. 공급자가 데이터 출처임을 표시하도록 줄에 명확한 레이블을 지정해야 합니다. 예를 들어 “Data from: en.wikipedia.org”입니다. `LinkAttribution` 규칙의 경우 공급자 웹 사이트에 대한 하이퍼링크를 만들어야 합니다.  
  
다음은 `LinkAttribution` 및 `TextAttribution` 규칙을 포함하는 예제를 보여 줍니다.  
  
![링크 텍스트 특성](./media/linktextattribution.png)  

### <a name="media-attribution"></a>미디어 특성  

엔터티에 이미지가 포함되어 있고 이미지를 표시하는 경우 공급자 웹 사이트에 대한 클릭 광고 링크를 제공해야 합니다. 엔터티에 [MediaAttribution](reference.md#mediaattribution) 규칙이 포함된 경우 규칙의 URL을 사용하여 클릭 광고 링크를 만듭니다. 포함되지 않은 경우에는 이미지의 `provider` 필드에 포함된 URL을 사용하여 클릭 광고 링크를 만듭니다.  
  
다음은 이미지의 `provider` 필드 및 계약 규칙을 포함하는 예제를 보여 줍니다. 예제에는 계약 규칙이 포함되어 있으므로 이미지의 `provider` 필드를 무시하고 `MediaAttribution` 규칙을 적용합니다.  
  
![미디어 특성](./media/mediaattribution.png)  

## <a name="next-steps"></a>다음 단계
- [C# 빠른 시작](c-sharp-quickstart.md)
- [Java 빠른 시작](java-quickstart.md)
- [Node 빠른 시작](node-quickstart.md)
- [Python 빠른 시작](python-quickstart.md)
