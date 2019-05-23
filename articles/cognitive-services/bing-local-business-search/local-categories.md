---
title: Bing Local Business Search API의 검색 범주 | Microsoft Docs
titleSuffix: Azure Cognitive Services
description: 이 문서를 사용하여 Bing Local Business Search API 엔드포인트에 대한 검색 범주를 지정하는 방법을 알아봅니다.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.topic: article
ms.date: 11/01/2018
ms.author: rosh, v-gedod
ms.openlocfilehash: 61bd230ffdb79b31390e96179e5beb3646902142
ms.sourcegitcommit: 36c50860e75d86f0d0e2be9e3213ffa9a06f4150
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/16/2019
ms.locfileid: "65796741"
---
# <a name="search-categories-for-the-bing-local-business-search-api"></a>Bing Local Business Search API의 검색 범주

Bing Local Business Search API를 사용하면 다양한 범주에서 로컬 비즈니스 엔터티를 검색할 수 있으며 사용자 위치에 가까울수록 결과의 우선 순위가 높습니다. 검색에 `localCircularView` 및 `localMapView` [매개 변수](specify-geographic-search.md)와 함께 이러한 검색을 포함할 수 있습니다.


## <a name="toplevel-categories"></a>최상위 범주 

다음 유형은 검색의 주요 범주를 정의합니다.  `localCategories` 매개 변수에 할당된 쉼표로 분리된 목록을 사용하여 둘 이상의 범주를 지정할 수 있습니다.  
- EatDrink 
- SeeDo 
- Shop 
- HotelsAndMotels 
- BanksAndCreditUnions 
- Parking 
- Hospitals 

## <a name="sub-categories"></a>하위 범주
하위 범주는 `localCategories`와 동일한 방식으로 전달됩니다. 하위 범주는 보다 구체적인 범주입니다. 범주 C와 하위 범주 S 중 하나를 동일한 쉼표로 구분된 목록에 지정하면 C만 지정한 경우와 동일한 결과를 얻게 된다는 점에서 이들은 종속적입니다.

### <a name="eat-drink"></a>Eat Drink 
|  |  |  |  |
| - | - | - | - |
| BreweriesAndBrewPubs | CocktailLounges | AfricanRestaurants |
| AmericanRestaurants | Bagels | BarbecueRestaurants |
| Taverns | SportsBars | Bars |
| BarsGrillsAndPubs | BuffetRestaurants| BelgianRestaurants | 
| BritishRestaurants | CafeRestaurants | CaribbeanRestaurants |
| ChineseRestaurants | CoffeeAndTea | Delicatessens | 
| DeliveryService | Diners | DiscountStores | 
| Donuts | FastFood | FrenchRestaurants | 
| FrozenYogurt | GermanRestaurants | Supermarkets | 
| GreekRestaurants | Grocers | HawaiianRestaurants | 
| HungarianRestaurants | IceCreamAndFrozenDesserts | IndianRestaurants | 
| ItalianRestaurants | JapaneseRestaurants | Juices | 
| KoreanRestaurants | LiquorStores | MexicanRestaurants |
| MiddleEasternRestaurants | Pizza | PolishRestaurants | 
| PortugueseRestaurants | Pretzels | Restaurants | 
| RussianAndUkrainianRestaurants | Sandwiches | SeafoodRestaurants | 
| SpanishRestaurants | SteakHouseRestaurants | SushiRestaurants | 
| TakeAway | ThaiRestaurants | TurkishRestaurants | 
| VegetarianAndVeganRestaurants | VietnameseRestaurants|  |
 
### <a name="see-do"></a>See Do 
|  |  |  |
| -- | -- | -- |
| AmusementParks | Attractions | Carnivals |
| Casinos | LandmarksAndHistoricalSites | MiniatureGolfCourses |
| MovieTheaters | Museums | Parks |
| SightseeingTours | TouristInformation | Zoos |
 
### <a name="shop"></a>Shop 
|  |  |  |
| -- | -- | -- |
| AntiqueStores | Bookstores | CDAndRecordStores |
| ChildrensClothingStores | CigarAndTobaccoShops | ComicBookStores |
| DepartmentStores | DiscountStores | FleaMarketsAndBazaars |
| FurnitureStores | HomeImprovementStores | JewelryAndWatchesStores |
| KitchenwareStores | LiquorStores | MallsAndShoppingCenters |
| MensClothingStores | MusicStores | OutletStores |
| PetShops | PetSupplyStores | SchoolAndOfficeSupplyStores |
| ShoeStores | SportingGoodsStores | ToyAndGameStores |
| VitaminAndSupplementStores | WomensClothingStores |  |


## <a name="examples-of-local-categories-search"></a>로컬 범주 검색 예제

다음 예제에서는 `localCategories` 매개 변수에 따라 결과를 가져옵니다.

`https://api.cognitive.microsoft.com/localbusinesses/v7.0/search?&q=&mkt=en-US&localcategories=HotelsAndMotels`

`https://api.cognitive.microsoft.com/localbusinesses/v7.0/search?&q=&mkt=en-US&localcategories=EatDrink`

`https://api.cognitive.microsoft.com/localbusinesses/v7.0/search?&q=&mkt=en-US&localcategories=Shop`

`https://api.cognitive.microsoft.com/localbusinesses/v7.0/search?&q=&mkt=en-US&localcategories=Hospitals`

다음 쿼리는 'hospital' 결과 수를 Bing Local Business Search API에서 반환된 처음 세 개로 제한합니다.

`https://api.cognitive.microsoft.com/localbusinesses/v7.0/search?&q=&mkt=en-US&localCategories=Hospitals&count=3&offset=0`

다음 JSON 응답 예제는 시애틀 근교 지역의 3개 병원을 포함합니다.

```json
BingAPIs-TraceId: 68AFB51807C6485CAB8AAF20E232EFFF
BingAPIs-SessionId: F89E7B8539B34BF58AAF811485E83B20
X-MSEdge-ClientID: 1C44E64DBFAA6BCA1270EADDBE7D6A22
BingAPIs-Market: en-US
X-MSEdge-Ref: Ref A: 68AFB51807C6485CAB8AAF20E232EFFF Ref B: CO1EDGE0108 Ref C: 2018-10-22T18:52:28Z

{
   "_type": "SearchResponse",
   "queryContext": {
      "originalQuery": ""
   },
   "places": {
      "readLink": "https:\/\/www.bingapis.com\/api\/v7\/places\/search?q=",
      "totalEstimatedMatches": 0,
      "value": [
         {
            "_type": "LocalBusiness",
            "id": "https:\/\/www.bingapis.com\/api\/v7\/#Places.0",
            "name": "Overlake Hospital Medical Center",
            "url": "http:\/\/www.overlakehospital.org\/",
            "entityPresentationInfo": {
               "entityScenario": "ListItem",
               "entityTypeHints": [
                  "Place",
                  "LocalBusiness"
               ]
            },
            "geo": {
               "latitude": 47.6204986572266,
               "longitude": -122.185829162598
            },
            "routablePoint": {
               "latitude": 47.6204986572266,
               "longitude": -122.185668945312
            },
            "address": {
               "streetAddress": "1035 116th Ave NE",
               "addressLocality": "Bellevue",
               "addressRegion": "WA",
               "postalCode": "98004",
               "addressCountry": "US",
               "neighborhood": "",
               "text": "1035 116th Ave NE, Bellevue, WA, 98004"
            },
            "telephone": "(425) 688-5000"
         },
         {
            "_type": "LocalBusiness",
            "id": "https:\/\/www.bingapis.com\/api\/v7\/#Places.1",
            "name": "Virginia Mason University Village Medical Center",
            "url": "https:\/\/virginiamason.org\/Seattle",
            "entityPresentationInfo": {
               "entityScenario": "ListItem",
               "entityTypeHints": [
                  "Place",
                  "LocalBusiness"
               ]
            },
            "geo": {
               "latitude": 47.6095390319824,
               "longitude": -122.327941894531
            },
            "routablePoint": {
               "latitude": 47.6093978881836,
               "longitude": -122.328277587891
            },
            "address": {
               "streetAddress": "1100 9th Ave",
               "addressLocality": "Seattle",
               "addressRegion": "WA",
               "postalCode": "98101",
               "addressCountry": "US",
               "neighborhood": "University District",
               "text": "1100 9th Ave, Seattle, WA, 98101"
            },
            "telephone": "(206) 223-6600"
         },
         {
            "_type": "LocalBusiness",
            "id": "https:\/\/www.bingapis.com\/api\/v7\/#Places.2",
            "name": "Seattle Children’s Hospital",
            "url": "http:\/\/www.seattlechildrens.org\/",
            "entityPresentationInfo": {
               "entityScenario": "ListItem",
               "entityTypeHints": [
                  "Place",
                  "LocalBusiness"
               ]
            },
            "geo": {
               "latitude": 47.6625061035156,
               "longitude": -122.283760070801
            },
            "routablePoint": {
               "latitude": 47.6631507873535,
               "longitude": -122.284614562988
            },
            "address": {
               "streetAddress": "4800 Sand Point Way NE",
               "addressLocality": "Seattle",
               "addressRegion": "WA",
               "postalCode": "98105",
               "addressCountry": "US",
               "neighborhood": "Laurelhurst",
               "text": "4800 Sand Point Way NE, Seattle, WA, 98105"
            },
            "telephone": "(206) 987-2000"
         }
      ],
      "searchAction": {

      }
   }
}
```

## <a name="next-steps"></a>다음 단계
- [지리적 검색 경계](specify-geographic-search.md)
- [쿼리 및 응답](local-search-query-response.md)
- [C#으로 빠른 시작](quickstarts/local-quickstart.md)
