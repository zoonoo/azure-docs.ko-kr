---
title: Azure Maps Search 서비스를 사용 하 여 효율적으로 검색 하는 방법 | Microsoft Docs
description: Azure Maps Search 서비스를 사용 하 여 검색에 대 한 모범 사례를 사용 하는 방법을 알아봅니다.
author: walsehgal
ms.author: v-musehg
ms.date: 04/08/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: philmea
ms.openlocfilehash: de9e484e43c87375c2fdf9b34dd2efce3bb8aa8c
ms.sourcegitcommit: 77bfc067c8cdc856f0ee4bfde9f84437c73a6141
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/16/2019
ms.locfileid: "72429171"
---
# <a name="best-practices-to-use-azure-maps-search-service"></a>Azure Maps Search Service를 사용 하는 모범 사례

Azure Maps [Search Service](https://docs.microsoft.com/rest/api/maps/search) 에는 다양 한 기능을 제공 하는 api가 포함 되어 있습니다. 예를 들어 주소 검색에서 특정 위치에 대 한 Poi (Point Point) 데이터를 검색 합니다. 이 문서에서는 Azure Maps Search 서비스를 통해 데이터를 호출 하는 모범 사례를 공유 합니다. 다음 방법을 알게 됩니다.

* 관련 일치 항목을 반환 하는 쿼리 작성
* 검색 결과 제한
* 다양 한 결과 형식 간의 차이점 알아보기
* 주소 검색 응답 구조 읽기


## <a name="prerequisites"></a>전제 조건

맵 서비스 API에 대한 호출을 생성하려면 맵 계정 및 키가 필요합니다. 계정을 만드는 방법에 대 한 자세한 내용은 [계정 관리](https://docs.microsoft.com/azure/azure-maps/how-to-manage-account-keys#create-a-new-account) 의 지침에 따르고 [기본 키 가져오기](./tutorial-search-location.md#getkey) 의 단계에 따라 계정에 대 한 기본 구독 키를 검색 합니다.

> [!Tip]
> 검색 서비스를 쿼리하려면 [Postman 앱](https://www.getpostman.com/apps) 을 사용 하 여 REST 호출을 빌드하거나 선호 하는 모든 API 개발 환경을 사용할 수 있습니다.


## <a name="best-practices-for-geocoding"></a>지 오 코딩에 대 한 모범 사례

Azure Maps Search Service를 사용 하 여 전체 또는 부분 주소를 검색 하는 경우 검색 용어를 사용 하 고 주소의 경도 및 위도 좌표를 반환 합니다. 이 프로세스를 지 오 코딩 라고 합니다. 국가의 지오코딩하는 기능은 도로 데이터 검사 및 지오코딩 서비스의 지오코딩 정밀도에 따라 달라집니다.

국가/지역별 Azure Maps 지 오 코딩 기능에 대해 자세히 알아보려면 [지 오 코딩 범위](https://docs.microsoft.com/azure/azure-maps/geocoding-coverage) 를 참조 하세요.

### <a name="limit-search-results"></a>검색 결과 제한

   이 섹션에서는 Azure Maps 검색 Api를 사용 하 여 검색 결과를 제한 하는 방법을 알아봅니다. 

   > [!Note]
   > 모든 검색 Api에서 아래 나열 된 매개 변수를 완전히 지원 하지는 않습니다.

   **지리적 편차 검색 결과**

   사용자의 관련 영역에 대 한 결과를 지리적으로 구분 하기 위해 항상 가능한 최대 자세한 위치 입력을 추가 해야 합니다. 검색 결과를 제한 하려면 다음 입력 형식을 추가 하는 것이 좋습니다.

   1. @No__t-0 매개 변수를 설정 합니다 (예: "US, FR"). 기본 검색 동작은 전체 세계를 검색 하 여 불필요 한 결과를 반환 하는 것입니다. 쿼리에서 @no__t 매개 변수를 포함 하지 않으면 검색에서 부정확 한 결과가 반환 될 수 있습니다. 예를 들어 **Bellevue** 이라는 도시를 검색 하면 프랑스와 Usa에 **Bellevue** 라는 도시가 있으므로 usa 및 프랑스에서 결과를 반환 합니다.

   2. @No__t-0 및 `topleft` 매개 변수를 사용 하 여 경계 상자를 설정 하 고 검색을 지도의 특정 영역으로 제한할 수 있습니다.

   3. 결과의 관련성 영역에 영향을 주기 위해 `lat` 및 `lon` 좌표 매개 변수를 정의 하 고 `radius` 매개 변수를 사용 하 여 검색 영역의 반경을 설정할 수 있습니다.


   **유사 항목 검색 매개 변수**

   1. @No__t-0 및 `maxFuzzyLevel`은 쿼리 매개 변수가 원하는 정보와 정확히 일치 하지 않는 경우에도 관련 일치 항목을 반환 합니다. 대부분의 검색 쿼리는 기본값 `minFuzzyLevel=1` 및 `maxFuzzyLevel=2`을 통해 성능을 얻고 비정상적인 결과를 줄입니다. 검색 단어 "restrant"에 대 한 예를 들어 `maxFuzzyLevel`이 2로 설정 된 경우 "식당"과 일치 합니다. 기본 유사 수준 수준은 요청 요구에 따라 재정의할 수 있습니다. 

   2. @No__t-0 매개 변수를 사용 하 여 반환 되는 정확한 결과 형식 집합을 지정할 수도 있습니다. 이 목적을 위해 쉼표로 구분 된 인덱스 목록을 제출할 수 있습니다. 항목 순서는 중요 하지 않습니다. 다음은 지원 되는 인덱스입니다.

       * `Addr` @ no__t**주소 범위**: 일부 거리의 경우 거리의 시작과 끝에서 보간된 주소 점이 있습니다. 이러한 점은 주소 범위로 표시 됩니다.
       * `Geo` @ no__t-1**지리: 지역의**관리 구분 (country, state, city)을 나타내는 지도에 있는 영역입니다.
       * `PAD` @ no__t-1**점 주소**: 주소와 숫자가 있는 특정 주소를 인덱스에서 찾을 수 있는 지도를 가리킵니다 (예: Soquel Dr 2501). 주소에 사용할 수 있는 가장 높은 수준의 정확도입니다.  
       * `POI` @ no__t-1 개**관심 사항**: 지도에서 주목 해야 하는 점이 매우 중요 합니다.  [검색 주소 가져오기](https://docs.microsoft.com/rest/api/maps/search/getsearchaddress) 는 poi을 반환 하지 않습니다.  
       * `Str` @ no__t-1**거리**: 지도의 거리를 나타냅니다.
       * `XStr` @ no__t-1**교차 거리/교차로**: 연결점 표현 두 거리를 교차 하는 위치입니다.


       **사용 예**:

       * idxSet = POI (관심 있는 검색 지점만) 

       * idxSet = PAD, Addr (검색 주소만, 채움 = Point Address, Addr = 주소 범위)

### <a name="reverse-geocode-and-geography-entity-type-filter"></a>역방향 geocode 및 geography 엔터티 형식 필터

[검색 주소 역방향 API](https://docs.microsoft.com/rest/api/maps/search/getsearchaddressreverse)를 사용 하 여 역방향 geocode 검색을 수행 하는 경우 서비스는 관리 영역에 대 한 다각형을 반환 하는 기능을 가집니다. 요청에서-0 @no__t 매개 변수를 제공 하 여 지정 된 지리 엔터티 형식에 대 한 검색 범위를 좁힐 수 있습니다. 결과 응답에는 지리 ID와 일치 하는 엔터티 형식이 포함 됩니다. 둘 이상의 엔터티를 제공 하는 경우 끝점은 **사용 가능한 가장 작은 엔터티**를 반환 합니다. 반환 된 Geometry ID를 사용 하 여 [Polygon 가져오기 서비스](https://docs.microsoft.com/rest/api/maps/search/getsearchpolygon)를 통해 지리 기 하 도형을 가져올 수 있습니다.

**샘플 요청:**

```HTTP
https://atlas.microsoft.com/search/address/reverse/json?api-version=1.0&subscription-key={subscription-key}&query=47.6394532,-122.1304551&language=en-US&entityType=Municipality
```

**응답:**

```JSON
{
    "summary": {
        "queryTime": 8,
        "numResults": 1
    },
    "addresses": [
        {
            "address": {
                "routeNumbers": [],
                "countryCode": "US",
                "countrySubdivision": "WA",
                "countrySecondarySubdivision": "King",
                "countryTertiarySubdivision": "Seattle East",
                "municipality": "Redmond",
                "country": "United States",
                "countryCodeISO3": "USA",
                "freeformAddress": "Redmond, WA",
                "boundingBox": {
                    "northEast": "47.717105,-122.034537",
                    "southWest": "47.627016,-122.164998",
                    "entity": "position"
                },
                "countrySubdivisionName": "Washington"
            },
            "position": "47.639454,-122.130455",
            "dataSources": {
                "geometry": {
                    "id": "00005557-4100-3c00-0000-0000596ae571"
                }
            },
            "entityType": "Municipality"
        }
    ]
}
```

### <a name="search-results-language"></a>검색 결과 언어

@No__t-0 매개 변수를 사용 하 여 검색 결과를 반환할 언어를 설정할 수 있습니다. 이 언어가 요청에 설정 되어 있지 않으면 검색 서비스는 자동으로 국가/지역의 가장 일반적인 언어로 설정 됩니다. 또한 지정 된 언어의 데이터를 사용할 수 없는 경우 기본 언어가 사용 됩니다. 국가/지역별 Azure Maps 서비스와 관련 하 여 지원 되는 언어 목록은 [지원 되는 언어](https://docs.microsoft.com/azure/azure-maps/supported-languages) 를 참조 하세요.


### <a name="predictive-mode-auto-suggest"></a>예측 모드 (자동 제안)

부분 쿼리에 대해 더 많은 일치 항목을 찾으려면 `typeahead` 매개 변수를 ' t r u e '로 설정 해야 합니다. 쿼리가 부분 입력으로 해석 되 고 검색이 예측 모드로 전환 됩니다. 그렇지 않으면 서비스에서 모든 관련 정보를 전달 하는 것으로 가정 합니다.

아래 예제 쿼리에서는 `typeahead` 매개 변수를 **true**로 설정 하 여 검색 주소 서비스가 "마이크로"에 대해 쿼리 됨을 확인할 수 있습니다. 응답을 관찰 하는 경우 검색 서비스는 쿼리를 부분 쿼리로 해석 하 고 응답에 자동 제안 된 쿼리를 포함 하는 것을 볼 수 있습니다.

**샘플 쿼리:**

```HTTP
https://atlas.microsoft.com/search/address/json?subscription-key={subscription-key}&api-version=1.0&typeahead=true&countrySet=US&lat=47.6370891183&lon=-122.123736172&query=Microsoft
```

**응답:**

```JSON
{
    "summary": {
        "query": "microsoft",
        "queryType": "NON_NEAR",
        "queryTime": 25,
        "numResults": 6,
        "offset": 0,
        "totalResults": 6,
        "fuzzyLevel": 1,
        "geoBias": {
            "lat": 47.6370891183,
            "lon": -122.123736172
        }
    },
    "results": [
        {
            "type": "Street",
            "id": "US/STR/p0/10294417",
            "score": 2.594,
            "dist": 327.546040632591,
            "address": {
                "streetName": "Microsoft Way",
                "municipalitySubdivision": "Redmond",
                "municipality": "Redmond",
                "countrySecondarySubdivision": "King",
                "countryTertiarySubdivision": "Seattle East",
                "countrySubdivision": "WA",
                "postalCode": "98052",
                "extendedPostalCode": "980526399,980528300",
                "countryCode": "US",
                "country": "United States Of America",
                "countryCodeISO3": "USA",
                "freeformAddress": "Microsoft Way, Redmond, WA 98052",
                "countrySubdivisionName": "Washington"
            },
            "position": {
                "lat": 47.63989,
                "lon": -122.12509
            },
            "viewport": {
                "topLeftPoint": {
                    "lat": 47.63748,
                    "lon": -122.12309
                },
                "btmRightPoint": {
                    "lat": 47.64223,
                    "lon": -122.13061
                }
            }
        },
        ...,
        ...,
        ...,
        ...,
        {
            "type": "Street",
            "id": "US/STR/p0/9063400",
            "score": 2.075,
            "dist": 3655467.6406921702,
            "address": {
                "streetName": "Microsoft Way",
                "municipalitySubdivision": "Yorkmount, Charlotte",
                "municipality": "Charlotte",
                "countrySecondarySubdivision": "Mecklenburg",
                "countryTertiarySubdivision": "Township 1 Charlotte",
                "countrySubdivision": "NC",
                "postalCode": "28217",
                "countryCode": "US",
                "country": "United States Of America",
                "countryCodeISO3": "USA",
                "freeformAddress": "Microsoft Way, Charlotte, NC 28217",
                "countrySubdivisionName": "North Carolina"
            },
            "position": {
                "lat": 35.14279,
                "lon": -80.91814
            },
            "viewport": {
                "topLeftPoint": {
                    "lat": 35.14267,
                    "lon": -80.91814
                },
                "btmRightPoint": {
                    "lat": 35.14279,
                    "lon": -80.91824
                }
            }
        }
    ]
}
```


### <a name="uri-encoding-to-handle-special-characters"></a>특수 문자를 처리 하는 URI 인코딩입니다. 

주소 간 주소를 찾으려면 즉, "1 통로 & 통합 주소, 시애틀", 특수 문자 ' & '를 인코딩해야 요청을 전송 해야 합니다. 모든 문자가 '% ' 문자를 사용 하 여 인코딩되고 UTF-8 문자에 해당 하는 두 문자로 된 16 진수 값을 사용 하 여 URI에 문자 데이터를 인코딩하는 것이 좋습니다.

**사용 예**:

검색 주소 가져오기:

```
query=1st Avenue & E 111th St, New York
```

 다음과 같이 인코딩해야 합니다.

```
query=1st%20Avenue%20%26%20E%20111th%20St%2C%20New%20York
```


다음은 다양 한 언어에 대해 사용 하는 다양 한 방법입니다. 

JavaScript/TypeScript:
```Javascript
encodeURIComponent(query)
```

C#.VB
```csharp
Uri.EscapeDataString(query)
```

Java:
```Java
URLEncoder.encode(query, "UTF-8") 
```

Python:
```Python
import urllib.parse 
urllib.parse.quote(query)
```

C++:
```C++
#include <curl/curl.h>
curl_easy_escape(query)
```

PHP:
```PHP
urlencode(query)
```

채워쓰기
```Ruby
CGI::escape(query) 
```

Swift:
```Swift
query.stringByAddingPercentEncodingWithAllowedCharacters(.URLHostAllowedCharacterSet()) 
```

으로
```Go
import ("net/url") 
url.QueryEscape(query)
```


## <a name="best-practices-for-poi-search"></a>POI 검색에 대 한 모범 사례

POI (관심 지점) 검색을 사용 하면 이름별로 비즈니스 검색과 같은 이름으로 POI 결과를 요청할 수 있습니다. @No__t-0 매개 변수를 사용 하 여 전체 세계를 검색 하 고, 잠재적으로 불필요 한 결과를 반환 하 고, 검색 시간이 길어질 수 있으므로 응용 프로그램에 적용 해야 하는 국가를 지정 하는 것이 좋습니다.

### <a name="brand-search"></a>브랜드 검색

응답의 결과와 정보의 관련성을 향상 시키기 위해 POI (관심 지점) 검색 응답에는 응답을 구문 분석 하는 데 추가로 사용할 수 있는 브랜드 정보가 포함 되어 있습니다.

Microsoft 캠퍼스 (Redmond, WA) 근처 가스 스테이션에 대 한 [Poi 범주 검색](https://docs.microsoft.com/rest/api/maps/search/getsearchpoicategory) 요청을 만들어 보겠습니다. 응답을 관찰 하는 경우 반환 된 각 POI에 대 한 브랜드 정보를 볼 수 있습니다.

**샘플 쿼리:**

```HTTP
https://atlas.microsoft.com/search/poi/json?subscription-key={subscription-key}&api-version=1.0&query=gas%20station&limit=3&lat=47.6413362&lon=-122.1327968
```

**응답:**

```JSON
{
    "summary": {
        "query": "gas station",
        "queryType": "NON_NEAR",
        "queryTime": 206,
        "numResults": 3,
        "offset": 0,
        "totalResults": 742169,
        "fuzzyLevel": 1,
        "geoBias": {
            "lat": 47.6413362,
            "lon": -122.1327968
        }
    },
    "results": [
        {
            "type": "POI",
            "id": "US/POI/p0/245813",
            "score": 5.663,
            "dist": 1037.0280221303253,
            "info": "search:ta:840531000004190-US",
            "poi": {
                "name": "Chevron",
                "phone": "+(1)-(425)-6532200",
                "brands": [
                    {
                        "name": "Chevron"
                    }
                ],
                "url": "www.chevron.com",
                "classifications": [
                    {
                        "code": "PETROL_STATION",
                        "names": [
                            {
                                "nameLocale": "en-US",
                                "name": "petrol station"
                            }
                        ]
                    }
                ]
            },
            "address": {
                "streetNumber": "2444",
                "streetName": "Bel Red Rd",
                "municipalitySubdivision": "Northeast Bellevue, Bellevue",
                "municipality": "Bellevue",
                "countrySecondarySubdivision": "King",
                "countryTertiarySubdivision": "Seattle East",
                "countrySubdivision": "WA",
                "postalCode": "98007",
                "countryCode": "US",
                "country": "United States Of America",
                "countryCodeISO3": "USA",
                "freeformAddress": "2444 Bel Red Rd, Bellevue, WA 98007",
                "countrySubdivisionName": "Washington"
            },
            "position": {
                "lat": 47.63201,
                "lon": -122.13281
            },
            "viewport": {
                "topLeftPoint": {
                    "lat": 47.63291,
                    "lon": -122.13414
                },
                "btmRightPoint": {
                    "lat": 47.63111,
                    "lon": -122.13148
                }
            },
            "entryPoints": [
                {
                    "type": "main",
                    "position": {
                        "lat": 47.63223,
                        "lon": -122.13311
                    }
                }
            ]
        },
        ...,
        {
            "type": "POI",
            "id": "US/POI/p0/7727106",
            "score": 5.662,
            "dist": 1458.645407416307,
            "info": "search:ta:840539000488527-US",
            "poi": {
                "name": "BROWN BEAR CAR WASH",
                "phone": "+(1)-(425)-6442868",
                "brands": [
                    {
                        "name": "Texaco"
                    }
                ],
                "url": "www.texaco.com/",
                "classifications": [
                    {
                        "code": "PETROL_STATION",
                        "names": [
                            {
                                "nameLocale": "en-US",
                                "name": "petrol station"
                            }
                        ]
                    }
                ]
            },
            "address": {
                "streetNumber": "15248",
                "streetName": "Bel Red Rd",
                "municipalitySubdivision": "Redmond",
                "municipality": "Redmond",
                "countrySecondarySubdivision": "King",
                "countryTertiarySubdivision": "Seattle East",
                "countrySubdivision": "WA",
                "postalCode": "98052",
                "extendedPostalCode": "980525511",
                "countryCode": "US",
                "country": "United States Of America",
                "countryCodeISO3": "USA",
                "freeformAddress": "15248 Bel Red Rd, Redmond, WA 98052",
                "countrySubdivisionName": "Washington"
            },
            "position": {
                "lat": 47.62843,
                "lon": -122.13628
            },
            "viewport": {
                "topLeftPoint": {
                    "lat": 47.62933,
                    "lon": -122.13761
                },
                "btmRightPoint": {
                    "lat": 47.62753,
                    "lon": -122.13495
                }
            },
            "entryPoints": [
                {
                    "type": "main",
                    "position": {
                        "lat": 47.62826,
                        "lon": -122.13626
                    }
                }
            ]
        }
    ]
}
```


### <a name="airport-search"></a>공항 검색

POI 검색은 공식 공항 코드를 사용 하 여 공항 검색을 지원 합니다. 예: **해상** (시애틀-Tacoma 국제 공항). 

```HTTP
https://atlas.microsoft.com/search/poi/json?subscription-key={subscription-key}&api-version=1.0&query=SEA 
```

### <a name="nearby-search"></a>근접 검색

특정 위치 주변의 POI 결과만 검색 하려면 [근접 한 검색 API](https://docs.microsoft.com/rest/api/maps/search/getsearchnearby) 를 선택 하는 것이 적합 합니다. 이 끝점은 POI 결과만 반환 하며 검색 쿼리 매개 변수를 사용 하지 않습니다. 결과를 제한 하려면 radius를 설정 하는 것이 좋습니다.

## <a name="understanding-the-responses"></a>응답 이해

시애틀의 주소에 대해 Azure Maps [search 서비스](https://docs.microsoft.com/rest/api/maps/search) 에 대 한 주소 검색 요청을 만들어 보겠습니다. 아래의 요청 URL을 자세히 살펴보면, 미국의 주소를 검색 하도록 `countrySet` 매개 변수를 **US** 로 설정 했습니다.

**샘플 쿼리:**

```HTTP
https://atlas.microsoft.com/search/address/json?subscription-key={subscription-key}&api-version=1&query=400%20Broad%20Street%2C%20Seattle%2C%20WA&countrySet=US
```

아래의 응답 구조를 살펴보겠습니다. 응답에서 결과 개체의 결과 형식이 서로 다릅니다. 신중 하 게 확인 하는 경우 "지점 주소", "주소" 및 "교차 위치"와 같은 세 가지 유형의 결과 개체가 있음을 볼 수 있습니다. 주소 검색은 Poi를 반환 하지 않습니다. 각 응답 개체에 대 한 `Score` 매개 변수는 동일한 응답에서 다른 개체의 점수에 대 한 상대 일치 점수를 나타냅니다. 응답 개체 매개 변수에 대 한 자세한 내용은 [검색 주소 가져오기](https://docs.microsoft.com/rest/api/maps/search/getsearchaddress) 를 참조 하세요.

**지원 되는 결과 형식:**

* **지점 주소:** 주소와 숫자가 있는 특정 주소를 사용 하 여 지도를 가리킵니다. 주소에 사용할 수 있는 가장 높은 정확도 수준입니다. 

* **주소 범위:**  거리의 시작과 끝에서 보간된 주소 점이 몇 개 있습니다. 이러한 점은 주소 범위로 표시 됩니다. 

* **지리:** 육지의 관리 구분 (country, state, city)을 나타내는 지도의 영역입니다. 

* **Poi-(관심 지점):** 지도에서 주목 해야 하는 것은 중요 합니다.

* **번 지:** 지도의 거리를 나타냅니다. 주소는 주소를 포함 하는 번 지의 위도/경도 좌표로 확인 됩니다. 번지는 처리되지 않을 수 있습니다. 

* **교차 주소:** 교차점. 연결점의 표현 두 거리를 교차 하는 위치입니다.

**응답:**

```JSON
{
    "summary": {
        "query": "400 broad street seattle wa",
        "queryType": "NON_NEAR",
        "queryTime": 129,
        "numResults": 6,
        "offset": 0,
        "totalResults": 6,
        "fuzzyLevel": 1
    },
    "results": [
        {
            "type": "Point Address",
            "id": "US/PAD/p0/43076024",
            "score": 9.894,
            "address": {
                "streetNumber": "400",
                "streetName": "Broad Street",
                "municipalitySubdivision": "Seattle, South Lake Union, Lower Queen Anne",
                "municipality": "Seattle",
                "countrySecondarySubdivision": "King",
                "countryTertiarySubdivision": "Seattle",
                "countrySubdivision": "WA",
                "postalCode": "98109",
                "countryCode": "US",
                "country": "United States Of America",
                "countryCodeISO3": "USA",
                "freeformAddress": "400 Broad Street, Seattle, WA 98109",
                "countrySubdivisionName": "Washington"
            },
            "position": {
                "lat": 47.62039,
                "lon": -122.34928
            },
            "viewport": {
                "topLeftPoint": {
                    "lat": 47.62129,
                    "lon": -122.35061
                },
                "btmRightPoint": {
                    "lat": 47.61949,
                    "lon": -122.34795
                }
            },
            "entryPoints": [
                {
                    "type": "main",
                    "position": {
                        "lat": 47.61982,
                        "lon": -122.34886
                    }
                }
            ]
        },
        {
            "type": "Street",
            "id": "US/STR/p0/2440854",
            "score": 8.129,
            "address": {
                "streetName": "Broad Street",
                "municipalitySubdivision": "Seattle, Westlake, South Lake Union",
                "municipality": "Seattle",
                "countrySecondarySubdivision": "King",
                "countryTertiarySubdivision": "Seattle",
                "countrySubdivision": "WA",
                "postalCode": "98109",
                "extendedPostalCode": "981094347,981094700,981094701,981094702",
                "countryCode": "US",
                "country": "United States Of America",
                "countryCodeISO3": "USA",
                "freeformAddress": "Broad Street, Seattle, WA 98109",
                "countrySubdivisionName": "Washington"
            },
            "position": {
                "lat": 47.62553,
                "lon": -122.33936
            },
            "viewport": {
                "topLeftPoint": {
                    "lat": 47.62545,
                    "lon": -122.33861
                },
                "btmRightPoint": {
                    "lat": 47.62574,
                    "lon": -122.33974
                }
            }
        },
        {
            "type": "Street",
            "id": "US/STR/p0/8450985",
            "score": 8.129,
            "address": {
                "streetName": "Broad Street",
                "municipalitySubdivision": "Seattle, Belltown",
                "municipality": "Seattle",
                "countrySecondarySubdivision": "King",
                "countryTertiarySubdivision": "Seattle",
                "countrySubdivision": "WA",
                "postalCode": "98109,98121",
                "extendedPostalCode": "981094991,981211117,981211237,981213206",
                "countryCode": "US",
                "country": "United States Of America",
                "countryCodeISO3": "USA",
                "freeformAddress": "Broad Street, Seattle, WA",
                "countrySubdivisionName": "Washington"
            },
            "position": {
                "lat": 47.61691,
                "lon": -122.35251
            },
            "viewport": {
                "topLeftPoint": {
                    "lat": 47.61502,
                    "lon": -122.35041
                },
                "btmRightPoint": {
                    "lat": 47.61857,
                    "lon": -122.35484
                }
            }
        },
        ...,
        ...,
        {
            "type": "Cross Street",
            "id": "US/XSTR/p1/3816818",
            "score": 6.759,
            "address": {
                "streetName": "Broad Street & Valley Street",
                "municipalitySubdivision": "South Lake Union, Seattle",
                "municipality": "Seattle",
                "countrySecondarySubdivision": "King",
                "countryTertiarySubdivision": "Seattle",
                "countrySubdivision": "WA",
                "postalCode": "98109",
                "countryCode": "US",
                "country": "United States Of America",
                "countryCodeISO3": "USA",
                "freeformAddress": "Broad Street & Valley Street, Seattle, WA 98109",
                "countrySubdivisionName": "Washington"
            },
            "position": {
                "lat": 47.62574,
                "lon": -122.33861
            },
            "viewport": {
                "topLeftPoint": {
                    "lat": 47.62664,
                    "lon": -122.33994
                },
                "btmRightPoint": {
                    "lat": 47.62484,
                    "lon": -122.33728
                }
            }
        }
    ]
}
```

### <a name="geometry"></a>기하학

응답 유형이 **geometry**인 경우 "geometry" 및 "ID"의 **데이터 원본** 개체에서 반환 되는 기 하 도형 ID를 포함할 수 있습니다. 예를 들어, [Get Polygon service](https://docs.microsoft.com/rest/api/maps/search/getsearchpolygon) 를 사용 하면 엔터티 집합에 대 한 도시 또는 공항 개요와 같은 GeoJSON 형식으로 geometry 데이터를 요청할 수 있습니다. [지 오 펜싱](https://docs.microsoft.com/azure/azure-maps/tutorial-geofence) 에이 경계 데이터를 사용 하거나 [기 하 도형 내에서 poi를 검색할](https://docs.microsoft.com/rest/api/maps/search/postsearchinsidegeometry)수 있습니다.


[주소 검색](https://docs.microsoft.com/rest/api/maps/search/getsearchaddress) 또는 [유사 항목 API 검색](https://docs.microsoft.com/rest/api/maps/search/getsearchfuzzy) 은 데이터 원본 개체에서 반환 되는 **geometry ID** 를 "geometry" 및 "ID"로 포함할 수 있습니다.


```JSON 
"dataSources": { 
        "geometry": { 
            "id": "00005557-4100-3c00-0000-000059690938" // The geometry ID is returned in the dataSources object under "geometry" and "id".
        }
} 
```

## <a name="next-steps"></a>다음 단계

* [Azure Maps Search 서비스 요청을 빌드하는 방법](https://docs.microsoft.com/azure/azure-maps/how-to-search-for-address)에 대해 알아봅니다.
* Azure Maps [search 서비스 API 설명서](https://docs.microsoft.com/rest/api/maps/search)를 탐색 합니다. 
