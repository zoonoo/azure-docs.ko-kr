---
title: '빠른 시작: Project Answer Search Entity 쿼리'
titlesuffix: Azure Cognitive Services
description: Project Answer Search를 사용한 엔터티 쿼리
services: cognitive-services
author: mikedodaro
manager: cgronlun
ms.service: cognitive-services
ms.subservice: answer-search
ms.topic: quickstart
ms.date: 04/16/2018
ms.author: rosh
ms.openlocfilehash: 2ec11412b5b0e713742029f05c91a6ecbe78c344
ms.sourcegitcommit: 95822822bfe8da01ffb061fe229fbcc3ef7c2c19
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/29/2019
ms.locfileid: "55210695"
---
# <a name="quickstart-query-for-entities"></a>빠른 시작: 엔터티 쿼리

쿼리에서 사람, 장소 또는 사물에 대한 정보를 요청하는 경우 응답에 `entities` 답변이 포함될 수 있습니다.  쿼리는 항상 웹 페이지를 반환하며, [팩트](fact-queries.md) 및/또는 [엔터티](entity-queries.md)는 쿼리에 종속됩니다.

엔터티는 다음 세 개의 쿼리 시나리오를 지원합니다. 
-   DominantEntity - 사용자 쿼리 및 의도와 일치하는 엔터티가 하나뿐입니다. 예를 들어 Space Needle 쿼리는 DominantEntity 시나리오입니다. 
-   Disambiguation - 사용자의 쿼리 및 의도와 일치하는 엔터티가 두 개 이상 있으며 올바른 엔터티를 선택하는 것은 사용자 책임입니다. 예를 들어 Game of Thrones 쿼리는 TV 프로그램과 책 시리즈를 반환하는 Disambiguation 시나리오입니다. 
-   List - 사용자의 쿼리 및 의도와 일치하는 엔터티가 여러 개 있습니다. 예를 들어 “List of endangered species” 쿼리는 행과 셀에 표시되도록 형식이 지정된 표 형식 값을 반환하는 List 시나리오입니다. 
 
쿼리 시나리오를 확인하려면 `entities` 개체의 `queryScenario` 필드를 사용합니다. 엔터티에 포함된 데이터는 엔터티 형식에 따라 다릅니다. 엔터티가 동일한 기본 정보를 포함하더라도 관광 명소나 책 등의 일부 엔터티에는 추가 속성이 포함됩니다. 추가 속성을 포함하는 엔터티에는 직렬 변환기에서 사용되는 힌트를 포함하는 `_type` 필드가 포함됩니다. 다음 엔터티에는 추가 속성이 포함됩니다. 
-   Book 
-   MusicRecording 
-   사람 
-   Attraction 
 
응답에 포함된 엔터티 형식을 확인하려면 Bill Gates에 대한 쿼리에 표시된 대로 `entityTypeHints` 필드를 사용합니다.
```
        },
        "description": "Bill Gates is an American business man and philanthropist, co-founder of Microsoft",
        "entityPresentationInfo": {
          "entityScenario": "DominantEntity",
          "entityTypeHints": [
            "Person"
          ]
        },
        "bingId": "6d7d66a7-2cb8-0ae9-637c-f81fd749dc9a"
      }
```
다음은 스페이스 니들에 대한 쿼리입니다.
```
https://api.labs.cognitive.microsoft.com/answerSearch/v7.0/search?q=space+needle&mkt=en-us
```
응답에는 `entities` 답변이 포함됩니다. `entityScenario` 및 `entityTypeHints` 필드를 확인합니다. 
```
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
              "url": "http://creativecommons.org/licenses/by-sa/3.0/"
            },
            "licenseNotice": "Text under CC-BY-SA license"
          },
          {
            "_type": "ContractualRules/LinkAttribution",
            "targetPropertyName": "description",
            "mustBeCloseToContent": true,
            "text": "Wikipedia",
            "url": "http://en.wikipedia.org/wiki/Space_Needle"
          },
          {
            "_type": "ContractualRules/MediaAttribution",
            "targetPropertyName": "image",
            "mustBeCloseToContent": true,
            "url": "http://en.wikipedia.org/wiki/Space_Needle"
          }
        ],
        "webSearchUrl": "https://www.bing.com/entityexplore?q\u003dSpace+Needle\u0026filters\u003dsid:%22f8dd5b08-206d-2554-6e4a-893f51f4de7e%22\u0026elv\u003dAXXfrEiqqD9r3GuelwApulpmymQx!ODfuQu*veOQHkvP0!Zbvi5F5tVcMSDJvDEWiQWwrdueYTtIszgj03oFQHykYYLYgq3q5!Sf00QxXGIS",
        "name": "Space Needle",
        "image": {
          "name": "Space Needle",
          "thumbnailUrl": "https://www.bing.com/th?id\u003dA15d336cf119b9b5c7e0ab37e271421d3\u0026w\u003d110\u0026h\u003d110\u0026c\u003d7\u0026rs\u003d1\u0026qlt\u003d80\u0026cdv\u003d1\u0026pid\u003d16.1",
          "provider": [
            {
              "_type": "Organization",
              "url": "http://en.wikipedia.org/wiki/Space_Needle"
            }
          ],
          "hostPageUrl": "http://upload.wikimedia.org/wikipedia/commons/2/23/Space_Needle_2011-07-04.jpg",
          "width": 110,
          "height": 110,
          "sourceWidth": 152,
          "sourceHeight": 300
        },
        "description": "The Space Needle is an observation tower in Seattle, Washington, a landmark of the Pacific Northwest, and an icon of Seattle. It was built in the Seattle Center for the 1962 World\u0027s Fair, which drew over 2.3 million visitors, when nearly 20,000 people a day used its elevators.",
        "entityPresentationInfo": {
          "entityScenario": "DominantEntity",
          "entityTypeHints": [
            "Attraction"
          ]
        },
        "bingId": "f8dd5b08-206d-2554-6e4a-893f51f4de7e"
      }
    ]
  },
```

해당하는 경우 쿼리에서 목록을 반환할 수 있습니다.

**쿼리:** 다음 쿼리는 멸종 위기종 목록을 찾습니다.

```
https://api.labs.cognitive.microsoft.com/answerSearch/v7.0/search?q=list+of+endangered+species

```

**응답:** 응답에는 표 형식 값으로 표시되도록 형식이 지정된 목록이 포함됩니다.
```
  "facts": {
    "id": "https://www.bingapis.com/api/v7/#Facts",
    "contractualRules": [
      {
        "_type": "ContractualRules/LinkAttribution",
        "text": "www.worldwildlife.org/species/directory?direction=desc&sort=e…",
        "url": "https://www.worldwildlife.org/species/directory?direction=desc&sort=extinction_status"
      }
    ],
    "attributions": [
      {
        "providerDisplayName": "www.worldwildlife.org/species/directory?direction=desc&sort=e…",
        "seeMoreUrl": "https://www.worldwildlife.org/species/directory?direction=desc&sort=extinction_status"
      }
    ],
    "value": [
      {
        "subjectName": "Species Directory",
        "richCaption": {
          "_type": "StructuredValue/TabularData",
          "header": [
            "Common name",
            "Scientific name",
            "Conservation status ↓"
          ],
          "rows": [
            {
              "cells": [
                {
                  "_type": "Properties/Link",
                  "text": "Amur Leopard",
                  "url": "https://www.bing.com/https//www.worldwildlife.org/species/amur-leopard"
                },
                {
                  "text": "Panthera pardus orientalis"
                },
                {
                  "text": "Critically Endangered"
                }
              ]
            },
            {
              "cells": [
                {
                  "_type": "Properties/Link",
                  "text": "Black Rhino",
                  "url": "https://www.bing.com/https//www.worldwildlife.org/species/black-rhino"
                },
                {
                  "text": "Diceros bicornis"
                },
                {
                  "text": "Critically Endangered"
                }
              ]
            },
            {
              "cells": [
                {
                  "_type": "Properties/Link",
                  "text": "Bornean Orangutan",
                  "url": "https://www.bing.com/https//www.worldwildlife.org/species/bornean-orangutan"
                },
                {
                  "text": "Pongo pygmaeus"
                },
                {
                  "text": "Critically Endangered"
                }
              ]
            },
            {
              "cells": [
                {
                  "_type": "Properties/Link",
                  "text": "Cross River Gorilla",
                  "url": "https://www.bing.com/https//www.worldwildlife.org/species/cross-river-gorilla"
                },
                {
                  "text": "Gorilla gorilla diehli"
                },
                {
                  "text": "Critically Endangered"
                }
              ]
            }
          ],
          "seeMoreUrl": {
            "text": "46 more rows",
            "url": "https://www.worldwildlife.org/species/directory?direction=desc&sort=extinction_status"
          }
        }
      }
    ]
  },

```


## <a name="next-steps"></a>다음 단계
- [C# 빠른 시작](c-sharp-quickstart.md)
- [Java 빠른 시작](java-quickstart.md)
- [Node 빠른 시작](node-quickstart.md)
- [Python 빠른 시작](python-quickstart.md)
