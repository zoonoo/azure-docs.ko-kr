---
title: Bing Entity Search API를 사용하여 엔터티 검색
titlesuffix: Azure Cognitive Services
description: Bing Entity Search API를 사용하여 검색 쿼리에서 엔터티 및 위치를 추출하고 검색합니다.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-entity-search
ms.topic: overview
ms.date: 02/01/2019
ms.author: aahi
ms.openlocfilehash: c205f792138ebd7e8094b3bd7cd303d54dfa8d8f
ms.sourcegitcommit: 3d4121badd265e99d1177a7c78edfa55ed7a9626
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/30/2019
ms.locfileid: "66389853"
---
# <a name="searching-for-entities-with-the-bing-entity-api"></a>Bing Entity API를 사용하여 엔터티 검색

## <a name="suggest-search-terms-with-the-bing-autosuggest-api"></a>Bing Autosuggest API를 사용하여 검색 용어 제안

사용자가 자신의 검색 용어를 입력할 수 있는 검색 상자를 제공하는 경우 [Bing Autosuggest API](../../bing-autosuggest/get-suggested-search-terms.md)를 사용하여 환경을 개선합니다. API는 부분 검색 용어 기반의 제안된 쿼리 문자열을 사용자 형식으로 반환합니다.

사용자가 검색어를 입력하면 URL에서 [q](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-entities-api-v7-reference#query) 쿼리 매개 변수를 설정하기 전에 해당 용어를 인코드합니다. 예를 들어 사용자가 *Marcus Appel*을 입력하는 경우 `q`를 *Marcus+Appel* 또는 *Marcus%20Appel*로 설정합니다.

검색에 오타가 있는 경우 검색 응답에 [QueryContext](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-entities-api-v7-reference#querycontext) 개체가 포함됩니다. 이 개체는 원래 철자 및 Bing이 검색에 사용한 수정된 철자를 보여 줍니다.

```json
"queryContext": {
    "originalQuery": "hollo wrld",
    "alteredQuery": "hello world",
    "alterationOverrideQuery": "+hollo wrld",
    "adultIntent": false
}
```

## <a name="the-bing-entity-search-api-response"></a>Bing Entity Search API 응답

API 응답에는 [SearchResponse](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-entities-api-v7-reference#searchresponse) 개체가 포함됩니다. Bing에서 관련된 엔터티 또는 장소를 찾은 경우 개체에 `entities` 필드, `places` 필드 또는 둘 다가 포함됩니다. 찾지 못한 경우에는 응답 개체에 필드가 포함되지 않습니다.
> [!NOTE]
> 엔터티 응답은 여러 지역/국가를 지원하지만 위치 응답은 미국 비즈니스 위치만 지원합니다. 

`entities` 필드는 [Entity](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-entities-api-v7-reference#entity) 개체 목록을 포함하는 [EntityAnswer](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-entities-api-v7-reference) 개체입니다(`value` 필드 참조). 목록에는 단일 기준 엔터티, 여러 명확성 엔터티 또는 둘 다가 포함될 수 있습니다. 

요청을 충족하는 엔터티가 기준 엔터티밖에 없다고 Bing에서 판단하는 경우(요청을 충족하는 엔터티에 대한 모호성 없음) 기준 엔터티가 반환됩니다. 여러 엔터티가 요청을 충족하는 경우 목록에는 둘 이상의 명확성 엔터티가 포함됩니다. 예를 들어 요청이 프랜차이즈 영화의 일반 제목을 사용하는 경우 목록에 모호성 엔터티가 포함될 가능성이 큽니다. 그러나 요청이 프랜차이즈의 특정 제목을 지정하는 경우 목록에 단일 기준 엔터티가 포함될 가능성이 큽니다.

엔터티에는 가수, 배우, 운동 선수, 모델 등 잘 알려진 개인, 레이니어 산, 링컨 기념관 등의 장소 및 랜드마크, 바나나, 골든두들, 책, 영화 제목 등의 사물이 포함됩니다. [entityPresentationInfo](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-entities-api-v7-reference#entitypresentationinfo) 필드에는 엔터티 형식을 식별하는 힌트가 포함됩니다. 예를 들어 사람, 영화, 동물, 명소 등입니다. 가능한 형식 목록은 [엔터티 형식](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-entities-api-v7-reference#entity-types)을 참조하세요.

```json
"entityPresentationInfo": {
    "entityScenario": "DominantEntity",
    "entityTypeHints": ["Attraction"],
    "entityTypeDisplayHint": "Mountain"
}, ...
```

다음은 기준 및 모호성 엔터티를 포함하는 응답을 보여 줍니다.

```json
{
    "_type": "SearchResponse",
    "queryContext": {
        "originalQuery": "Mount Rainier"
    },
    "entities": {
        "value": [{
            "contractualRules": [{
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
                "text": "contoso.com",
                "url": "http://contoso.com/mount_rainier"
            },
            {
                "_type": "ContractualRules/MediaAttribution",
                "targetPropertyName": "image",
                "mustBeCloseToContent": true,
                "url": "http://contoso.com/mount-rainier"
            }],
            "webSearchUrl": "https://www.bing.com/search?q=Mount%20Rainier...",
            "name": "Mount Rainier",
            "url": "http://www.northwindtraders.com/",
            "image": {
                "name": "Mount Rainier",
                "thumbnailUrl": "https://www.bing.com/th?id=A4ae343983daa4...",
                "provider": [{
                    "_type": "Organization",
                    "url": "http://contoso.com/mount_rainier"
                }],
                "hostPageUrl": "http://contoso.com/commons/7/72/mount_rain...",
                "width": 110,
                "height": 110
            },
            "description": "Mount Rainier is 14,411 ft tall and the highest mountain...",
            "entityPresentationInfo": {
                "entityScenario": "DominantEntity",
                "entityTypeHints": ["Attraction"]
            },
            "bingId": "38b9431e-cf91-93be-0584-c42a3ecbfdc7"
        },
        {
            "contractualRules": [{
                "_type": "ContractualRules/MediaAttribution",
                "targetPropertyName": "image",
                "mustBeCloseToContent": true,
                "url": "http://contoso.com/mount_rainier_national_park"
            }],
            "webSearchUrl": "https://www.bing.com/search?q=Mount%20Rainier%20National...",
            "name": "Mount Rainier National Park",
            "url": "http://worldwideimporters.com/",
            "image": {
                "name": "Mount Rainier National Park",
                "thumbnailUrl": "https://www.bing.com/th?id=A91bdc5a1b648a695a39...",
                "provider": [{
                    "_type": "Organization",
                    "url": "http://contoso.com/mount_rainier_national_park"
                }],
                "hostPageUrl": "http://contoso.com/en/7/7a...",
                "width": 50,
                "height": 50
            },
            "description": "Mount Rainier National Park is a United States National Park...",
            "entityPresentationInfo": {
                "entityScenario": "DisambiguationItem",
                "entityTypeHints": ["Organization"]
            },
            "bingId": "29d4b681-227a-3924-7bb1-8a54e8666b8c"
        }]
    }
}
```

엔터티에는 `name`, `description` 및 `image` 필드가 포함됩니다. 이러한 필드를 사용자 환경에 표시하는 경우 필드를 귀속해야 합니다. `contractualRules` 필드에는 적용해야 하는 특성 목록이 포함됩니다. 계약 규칙은 특성이 적용되는 필드를 식별합니다. 특성 적용에 대한 자세한 내용은 [특성](#data-attribution)을 참조하세요.

```json
"contractualRules": [{
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
    "text": "contoso.com",
    "url": "http://contoso.com/wiki/Mount_Rainier"
},
{
    "_type": "ContractualRules/MediaAttribution",
    "targetPropertyName": "image",
    "mustBeCloseToContent": true,
    "url": "http://contoso.com/wiki/Mount_Rainier"
}], ...
```

엔터티 정보(이름, 설명 및 이미지)를 표시하는 경우 `webSearchUrl` 필드의 URL을 사용하여 엔터티가 포함된 Bing 검색 결과 페이지에도 연결해야 합니다.

## <a name="find-places"></a>장소 찾기

`places` 필드는 [Place](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-entities-api-v7-reference#place) 개체 목록을 포함하는 [LocalEntityAnswer](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-entities-api-v7-reference) 개체입니다(자세한 내용은 [엔터티 형식](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-entities-api-v7-reference#entity-types) 참조). 목록에는 요청을 충족하는 로컬 엔터티가 하나 이상 포함됩니다.

장소에는 식당, 호텔 또는 현지 기업이 포함됩니다. [entityPresentationInfo](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-entities-api-v7-reference#entitypresentationinfo) 필드에는 로컬 엔터티 형식을 식별하는 힌트가 포함됩니다. 목록에는 장소, LocalBusiness, 식당과 같은 힌트 목록이 포함됩니다. 배열의 각 연속 힌트는 엔터티 형식의 범위를 좁힙니다. 가능한 형식 목록은 [엔터티 형식](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-entities-api-v7-reference#entity-types)을 참조하세요.

```json
"entityPresentationInfo": {
    "entityScenario": "ListItem",
    "entityTypeHints": ["Place",
    "LocalBusiness",
    "Restaurant"]
}, ...
```
> [!NOTE]
> 엔터티 응답은 여러 지역/국가를 지원하지만 위치 응답은 미국 비즈니스 위치만 지원합니다. 

*주변 식당* 등의 로컬 인식 엔터티 쿼리에서 정확한 결과를 제공하려면 사용자 위치가 필요합니다. 요청은 항상 X-Search-Location 및 X-MSEdge-ClientIP 헤더를 사용하여 사용자 위치를 지정해야 합니다. Bing은 사용자 위치가 쿼리에 도움이 된다고 판단할 경우 [QueryContext](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-entities-api-v7-reference#querycontext)의 `askUserForLocation` 필드를 **true**로 설정합니다. 

```json
{
    "_type": "SearchResponse",
    "queryContext": {
        "originalQuery": "Sinful Bakery and Cafe",
        "askUserForLocation": true
    },
    ...
}
```

장소 결과에는 장소 이름, 주소, 전화 번호 및 엔터티 웹 사이트의 URL이 포함됩니다. 엔터티 정보를 표시하는 경우 `webSearchUrl` 필드의 URL을 사용하여 엔터티가 포함된 Bing 검색 결과 페이지에도 연결해야 합니다.

```json
"places": {
    "value": [{
        "_type": "Restaurant",
        "webSearchUrl": "https://www.bing.com/search?q=Sinful%20Bakery...",
        "name": "Liberty's Delightful Sinful Bakery & Cafe",
        "url": "http://libertysdelightfulsinfulbakeryandcafe.com/",
        "entityPresentationInfo": {
            "entityScenario": "ListItem",
            "entityTypeHints": ["Place",
            "LocalBusiness",
            "Restaurant"]
        },
        "address": {
            "addressLocality": "Seattle",
            "addressRegion": "WA",
            "postalCode": "98112",
            "addressCountry": "US",
            "neighborhood": "Madison Park"
        },
        "telephone": "(800) 555-1212"
    }]
}
```

> [!NOTE]
> 사용자 또는 사용자를 대신한 제3자는 모든 타사 서비스나 기능을 테스트, 개발, 학습, 배포 또는 제공하기 위해 Entities API의 모든 데이터를 사용, 유지, 저장, 캐시, 공유 또는 배포할 수 없습니다.  

## <a name="data-attribution"></a>데이터 특성

Bing Entity API 응답에는 제3자가 소유한 정보가 포함됩니다. 예를 들어 사용자 환경에서 사용할 수 있는 모든 크리에이티브 커먼즈 라이선스를 준수하여 사용이 적절하도록 할 책임이 있습니다.

답변 또는 결과에 `contractualRules`, `attributions` 또는 `provider` 필드가 포함된 경우 데이터를 귀속해야 합니다. 답변에 이러한 필드가 포함되지 않은 경우에는 특성이 필요 없습니다. 답변에 `contractualRules` 필드와 `attributions` 및/또는 `provider` 필드가 포함된 경우 계약 규칙을 사용하여 데이터를 귀속해야 합니다.

다음 예제는 MediaAttribution 계약 규칙을 포함하는 엔터티와 `provider` 필드를 포함하는 이미지를 보여 줍니다. MediaAttribution 규칙은 이미지를 규칙의 대상으로 식별하므로 이미지의 `provider` 필드를 무시하고, 대신 MediaAttribution 규칙을 사용하여 특성을 제공합니다.  

```json
"value": [{
    "contractualRules": [
        ...
        {
            "_type": "ContractualRules/MediaAttribution",
            "targetPropertyName": "image",
            "mustBeCloseToContent": true,
            "url": "http://contoso.com/mount_rainier"
        }
    ],
    ...
    "image": {
        "name": "Mount Rainier",
        "thumbnailUrl": "https://www.bing.com/th?id=A46378861201...",
        "provider": [{
            "_type": "Organization",
            "url": "http://contoso.com/mount_rainier"
        }],
        "hostPageUrl": "http://www.graphicdesigninstitute.com/Uploaded...",
        "width": 110,
        "height": 110
    },
    ...
}]
```

계약 규칙에 `targetPropertyName` 필드가 포함된 경우 규칙은 대상 필드에만 적용됩니다. 포함되지 않은 경우에는 `contractualRules` 필드를 포함하는 부모 개체에 규칙이 적용됩니다.

다음 예제에서 `LinkAttribution` 규칙은 `targetPropertyName` 필드를 포함하므로 `description` 필드에 규칙이 적용됩니다. 특정 필드에 적용되는 규칙의 경우 공급자 웹 사이트에 대한 하이퍼링크를 포함하는 줄을 대상 데이터 바로 다음에 포함해야 합니다. 예를 들어 설명을 귀속하려면 공급자 웹 사이트 데이터에 대한 하이퍼링크를 포함하는 줄을 설명 텍스트 바로 다음에 포함합니다. 이 경우에는 contoso.com에 대한 링크를 만듭니다.

```json
"entities": {
    "value": [{
            ...
            "description": "Marcus Appel is a former American....",
            ...
            "contractualRules": [{
                    "_type": "ContractualRules/LinkAttribution",
                    "targetPropertyName": "description",
                    "mustBeCloseToContent": true,
                    "text": "contoso.com",
                    "url": "http://contoso.com/cr?IG=B8AD73..."
                 },
            ...
  
```

### <a name="license-attribution"></a>라이선스 특성

계약 규칙 목록에 [LicenseAttribution](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-entities-api-v7-reference#licenseattribution) 규칙이 포함된 경우, 라이선스가 적용되는 콘텐츠 바로 다음 줄에 고지 사항을 표시해야 합니다. `LicenseAttribution` 규칙은 `targetPropertyName` 필드를 사용하여 라이선스가 적용되는 자산을 식별합니다.

다음은 `LicenseAttribution` 규칙을 포함하는 예제를 보여 줍니다.

![라이선스 특성](../media/cognitive-services-bing-entities-api/licenseattribution.png)

표시하는 라이선스 고지 사항에는 라이선스 정보를 포함하는 웹 사이트에 대한 하이퍼링크가 포함되어야 합니다. 일반적으로 라이선스 이름을 하이퍼링크로 만듭니다. 예를 들어 고지 사항이 **CC-BY-SA 라이선스가 적용되는 텍스트**이고 CC-BY-SA가 라이선스 이름인 경우 CC-BY-SA를 하이퍼링크로 만듭니다.

### <a name="link-and-text-attribution"></a>링크 및 텍스트 특성

일반적으로 [LinkAttribution](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-entities-api-v7-reference#linkattribution) 및 [TextAttribution](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-entities-api-v7-reference#textattribution) 규칙은 데이터 공급자를 식별하는 데 사용됩니다. `targetPropertyName` 필드는 규칙이 적용되는 필드를 식별합니다.

공급자를 귀속하려면 특성이 적용되는 콘텐츠(예: 대상 필드) 바로 다음에 줄을 포함합니다. 공급자가 데이터 출처임을 표시하도록 줄에 명확한 레이블을 지정해야 합니다. 예를 들어 “Data from: contoso.com”입니다. `LinkAttribution` 규칙의 경우 공급자 웹 사이트에 대한 하이퍼링크를 만들어야 합니다.

다음은 `LinkAttribution` 및 `TextAttribution` 규칙을 포함하는 예제를 보여 줍니다.

![링크 텍스트 특성](../media/cognitive-services-bing-entities-api/linktextattribution.png)

### <a name="media-attribution"></a>미디어 특성

엔터티에 이미지가 포함되어 있고 이미지를 표시하는 경우 공급자 웹 사이트에 대한 클릭 광고 링크를 제공해야 합니다. 엔터티에 [MediaAttribution](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-entities-api-v7-reference#mediaattribution) 규칙이 포함된 경우 규칙의 URL을 사용하여 클릭 광고 링크를 만듭니다. 포함되지 않은 경우에는 이미지의 `provider` 필드에 포함된 URL을 사용하여 클릭 광고 링크를 만듭니다.

다음은 이미지의 `provider` 필드 및 계약 규칙을 포함하는 예제를 보여 줍니다. 예제에는 계약 규칙이 포함되어 있으므로 이미지의 `provider` 필드를 무시하고 `MediaAttribution` 규칙을 적용합니다.

![미디어 특성](../media/cognitive-services-bing-entities-api/mediaattribution.png)

### <a name="search-or-search-like-experience"></a>검색 또는 검색과 유사한 환경

Bing Web Search API와 마찬가지로, Bing Entity Search API는 직접 사용자 쿼리 또는 검색의 결과로 또는 논리적으로 사용자의 검색 요청으로 해석될 수 있는, 앱 또는 환경 내 작업의 결과로만 사용할 수 있습니다. 자세히 설명한다면, 다음은 허용되는 검색 또는 검색과 유사한 환경의 몇 가지 예입니다.

- 사용자가 앱의 검색 상자에 직접 쿼리를 입력합니다.
- 사용자가 특정 텍스트 또는 이미지를 선택하고 “자세한 정보” 또는 “추가 정보”를 요청합니다.
- 사용자가 특정 토픽에 대해 검색 봇에 질문을 합니다.
- 사용자가 시각적 검색 유형 시나리오에서 특정 개체 또는 엔터티에 집중합니다.

사용자 환경을 검색과 유사한 환경으로 간주할 수 있는지 잘 모르겠으면 Microsoft에 확인하는 것이 좋습니다.

## <a name="throttling-requests"></a>제한 요청

[!INCLUDE [cognitive-services-bing-throttling-requests](../../../../includes/cognitive-services-bing-throttling-requests.md)]

## <a name="next-steps"></a>다음 단계

* [빠른 시작](../quickstarts/csharp.md)에 따라 Bing Entity Search API를 사용하여 엔터티 검색을 시작해 보세요.
