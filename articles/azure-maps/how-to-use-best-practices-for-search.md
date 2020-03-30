---
title: Azure 지도 검색 서비스에 대한 모범 사례 | 마이크로소프트 Azure 지도
description: Microsoft Azure 지도에서 검색 서비스를 사용할 때 모범 사례를 적용하는 방법을 알아봅니다.
author: philmea
ms.author: philmea
ms.date: 01/23/2020
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: philmea
ms.openlocfilehash: 8d62d7d278323baa0ae49b9e12f46468efb067a0
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "80335319"
---
# <a name="best-practices-for-azure-maps-search-service"></a>Azure 지도 검색 서비스에 대한 모범 사례

Azure Maps [검색 서비스에는](https://docs.microsoft.com/rest/api/maps/search) 다양한 기능을 제공하는 API가 포함되어 있습니다. 예를 들어 검색 주소 API는 특정 위치 주변의 POI(관심 지점) 또는 데이터를 찾을 수 있습니다. 

이 문서에서는 Azure Maps 검색 서비스에서 데이터를 호출할 때 사운드 연습을 적용하는 방법을 설명합니다. 이 문서에서 배울 내용은 다음과 같습니다.

* 관련 일치 항목을 반환하는 쿼리를 빌드합니다.
* 검색 결과를 제한합니다.
* 결과 유형 간의 차이점을 알아봅니다.
* 주소 검색 응답 구조를 읽습니다.

## <a name="prerequisites"></a>사전 요구 사항

Azure Maps 서비스 API를 호출하려면 Azure Maps 계정과 키가 필요합니다. 자세한 내용은 [계정 만들기](quick-demo-map-app.md#create-an-account-with-azure-maps) 및 기본 [키 받기를](quick-demo-map-app.md#get-the-primary-key-for-your-account)참조하십시오. 

Azure Maps의 인증에 대한 자세한 내용은 [Azure Maps의 인증 관리를](./how-to-manage-authentication.md)참조하십시오.

> [!TIP]
> 검색 서비스를 쿼리하려면 [Postman 앱을](https://www.getpostman.com/apps) 사용하여 REST 호출을 빌드할 수 있습니다. 또는 원하는 API 개발 환경을 사용할 수 있습니다.

## <a name="best-practices-to-geocode-addresses"></a>주소 지정을 지정하는 모범 사례

Azure Maps 검색 서비스를 사용하여 전체 또는 일부 주소를 검색하면 API가 검색 쿼리에서 키워드를 읽습니다. 그런 다음 주소의 경도 및 위도 좌표를 반환합니다. 이 프로세스를 *지오코딩이라고*합니다. 

한 국가에서 지오코딩하는 기능은 도로 데이터의 가용성과 지오코딩 서비스의 정밀도에 따라 달라집니다. 국가 또는 지역별 Azure Maps 지오코딩 기능에 대한 자세한 내용은 [지오코딩 커버리지를](https://docs.microsoft.com/azure/azure-maps/geocoding-coverage)참조하십시오.

### <a name="limit-search-results"></a>검색 결과 제한

 Azure Maps 검색 API를 사용하면 검색 결과를 적절하게 제한할 수 있습니다. 사용자에게 관련 데이터를 표시할 수 있도록 결과를 제한합니다.

> [!NOTE]
> 검색 API는 이 문서에서 설명하는 매개 변수보다 더 많은 매개 변수를 지원합니다.

#### <a name="geobiased-search-results"></a>지리적 편향검색결과

사용자의 관련 영역에 대한 geobias 결과를 얻으려면 항상 가능한 한 많은 위치 세부 정보를 추가합니다. 다음과 같은 일부 입력 형식을 지정하여 검색 결과를 제한할 수 있습니다.

* 매개 `countrySet` 변수를 설정합니다. 예를 들어 `US,FR`로 설정할 수 있습니다. 기본적으로 API는 전체 를 검색하므로 불필요한 결과를 반환할 수 있습니다. 쿼리에 매개 `countrySet` 변수가 없는 경우 검색에서 부정확한 결과를 반환할 수 있습니다. 예를 들어 *Bellevue라는* 도시를 검색하면 두 국가 모두 *Bellevue라는*도시가 포함되어 있기 때문에 미국과 프랑스의 결과가 반환됩니다.

* `btmRight` 및 `topleft` 매개 변수를 사용하여 경계 상자를 설정할 수 있습니다. 이러한 매개 변수는 검색을 맵의 특정 영역으로 제한합니다.

* 결과에 대한 관련성 영역에 영향을 주려면 `lat` 매개 `lon` 변수 및 좌표를 정의합니다. 매개 `radius` 변수를 사용하여 검색 영역의 반지름을 설정합니다.


#### <a name="fuzzy-search-parameters"></a>퍼지 검색 매개 변수

검색 쿼리에 대한 사용자 입력을 모르는 경우 Azure Maps [검색 퍼지 API를](https://docs.microsoft.com/rest/api/maps/search/getsearchfuzzy) 사용하는 것이 좋습니다. API는 POI 검색 및 지오코딩을 표준 *단일 행 검색으로*결합합니다. 

* 및 `minFuzzyLevel` `maxFuzzyLevel` 매개 변수는 쿼리 매개 변수가 사용자가 원하는 정보와 정확히 일치하지 않는 경우에도 관련 일치 항목을 반환하는 데 도움이 됩니다. 성능을 최대화하고 비정상적인 결과를 줄이려면 검색 쿼리를 기본값 `minFuzzyLevel=1` 및 `maxFuzzyLevel=2`으로 설정합니다. 

    예를 들어 매개 `maxFuzzyLevel` 변수가 2로 설정되면 검색어 *restrant가* *레스토랑과*일치합니다. 필요할 때 기본 퍼지 레벨을 재정의할 수 있습니다. 

* 매개 `idxSet` 변수를 사용하여 정확한 결과 유형 집합의 우선 순위를 지정합니다. 정확한 결과 집합의 우선 순위를 지정하려면 쉼표로 구분된 인덱스 목록을 제출할 수 있습니다. 목록에서 항목 순서는 중요하지 않습니다. Azure Maps는 다음 인덱스를 지원합니다.

* `Addr` - **주소 범위**: 거리의 시작과 끝에서 보간된 주소 지점입니다. 이러한 점은 주소 범위로 표시됩니다.
* `Geo` - **지리**: 토지의 행정분과. 예를 들어 지역은 국가, 주 또는 도시일 수 있습니다.
* `PAD` - **점 주소**: 도로 이름과 번호가 포함된 주소입니다. 포인트 주소는 인덱스에서 찾을 수 있습니다. 예를 들어 *Soquel Dr 2501입니다.* 포인트 주소는 주소에 사용할 수 있는 최고 수준의 정확도를 제공합니다.  
* `POI` - 관심 지점 : 주의가치가 있거나 흥미로워질 수 있는 **맵의**포인트입니다. [검색 주소 API는](https://docs.microsoft.com/rest/api/maps/search/getsearchaddress) POI를 반환하지 않습니다.  
* `Str` - **거리**: 지도에 거리.
* `XStr` - **교차로 또는 교차로**: 교차로 또는 두 거리가 교차하는 장소.


#### <a name="usage-examples"></a>사용 예

* `idxSet=POI`- 검색 POI만. 

* `idxSet=PAD,Addr`- 검색 주소만. `PAD`은 포인트 주소를 `Addr` 나타내고 주소 범위를 나타냅니다.

### <a name="reverse-geocode-and-filter-for-a-geography-entity-type"></a>지리 엔터티 유형에 대한 역방향 지오코드 및 필터

[검색 주소 역방향 API에서](https://docs.microsoft.com/rest/api/maps/search/getsearchaddressreverse)역방향 지오코드 검색을 수행하면 서비스가 관리 영역에 대한 다각형을 반환할 수 있습니다.특정 지역 엔터티 유형으로 검색 범위를 `entityType` 좁히려면 요청에 매개 변수를 포함합니다. 

결과 응답에는 지리 ID와 일치하는 엔터티 유형이 포함됩니다. 두 개 이상의 엔터티를 제공하는 경우 끝점은 *사용 가능한 가장 작은 엔터티를 반환합니다.* 반환된 지오메트리 ID를 사용하여 [검색 다각형 서비스를](https://docs.microsoft.com/rest/api/maps/search/getsearchpolygon)통해 지리지오메트리를 얻을 수 있습니다.

#### <a name="sample-request"></a>샘플 요청

```HTTP
https://atlas.microsoft.com/search/address/reverse/json?api-version=1.0&subscription-key={subscription-key}&query=47.6394532,-122.1304551&language=en-US&entityType=Municipality
```

#### <a name="response"></a>응답

```JSON
{
    "summary": {
        "queryTime": 14,
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

### <a name="set-the-results-language"></a>결과 언어 설정

매개 `language` 변수를 사용하여 반환된 검색 결과의 언어를 설정합니다. 요청이 언어를 설정하지 않으면 기본적으로 검색 서비스는 국가 또는 지역에서 가장 일반적인 언어를 사용합니다. 지정된 언어에서 사용할 수 있는 데이터가 없는 경우 기본 언어가 사용됩니다. 

자세한 내용은 [Azure Maps 지원 언어를](https://docs.microsoft.com/azure/azure-maps/supported-languages)참조하십시오.


### <a name="use-predictive-mode-automatic-suggestions"></a>예측 모드 사용(자동 제안)

부분 쿼리에 대한 더 많은 `typeahead` 일치 `true`항목을 찾으려면 매개 변수를 로 설정합니다. 이 쿼리는 부분 입력으로 해석되며 검색은 예측 모드로 들어갑니다. 매개 변수를 `typeahead` `true`로 설정하지 않으면 서비스는 모든 관련 정보가 전달되었다고 가정합니다.

다음 샘플 쿼리에서 *Microso*에 대 한 검색 주소 서비스가 쿼리 됩니다. 여기서 매개 `typeahead` 변수는 `true`로 설정됩니다. 응답은 검색 서비스가 쿼리를 부분 쿼리로 해석한 것을 보여 주며, 이에 대한 응답은 쿼리를 부분 쿼리로 해석한 것입니다. 응답에는 자동으로 제안된 쿼리에 대한 결과가 포함됩니다.

#### <a name="sample-query"></a>샘플 쿼리

```HTTP
https://atlas.microsoft.com/search/address/json?subscription-key={subscription-key}&api-version=1.0&typeahead=true&countrySet=US&lat=47.6370891183&lon=-122.123736172&query=Microsoft
```

#### <a name="response"></a>응답

```JSON
{
    "summary": {
        "query": "microsoft",
        "queryType": "NON_NEAR",
        "queryTime": 18,
        "numResults": 7,
        "offset": 0,
        "totalResults": 7,
        "fuzzyLevel": 1,
        "geoBias": {
            "lat": 47.6370891183,
            "lon": -122.123736172
        }
    },
    "results": [
        {
            "type": "Street",
            "id": "US/STR/p0/9438784",
            "score": 2.594099998474121,
            "dist": 314.0590106663596,
            "address": {
                "streetName": "Microsoft Way",
                "municipalitySubdivision": "Redmond",
                "municipality": "Redmond",
            },
            "position": {
                "lat": 47.63988,
                "lon": -122.12438
            },
            "viewport": {
                "topLeftPoint": {
                    "lat": 47.64223,
                    "lon": -122.1256,
                    "valid": true
                },
                "btmRightPoint": {
                    "lat": 47.63748,
                    "lon": -122.12309,
                    "valid": true
                }
            }
        },
        {
            "type": "Street",
            "id": "US/STR/p0/1756074",
            "score": 2.592679977416992,
            "dist": 876.0272035824189,
            "address": {
                "streetName": "Microsoft Road",
                "municipalitySubdivision": "Redmond",
                "municipality": "Redmond",
                "countrySecondarySubdivision": "King",
                "countryTertiarySubdivision": "Seattle East",
                "countrySubdivision": "WA",
                "countrySubdivisionName": "Washington",
                "postalCode": "98052",
                "countryCode": "US",
                "country": "United States",
                "countryCodeISO3": "USA",
                "freeformAddress": "Microsoft Road, Redmond, WA 98052"
            },
            "position": {
                "lat": 47.64032,
                "lon": -122.1344
            },
            "viewport": {
                "topLeftPoint": {
                    "lat": 47.64253,
                    "lon": -122.13535,
                    "valid": true
                },
                "btmRightPoint": {
                    "lat": 47.63816,
                    "lon": -122.13305,
                    "valid": true
                }
            }
        },
        {
            "type": "Street",
            "id": "US/STR/p0/1470668",
            "score": 2.5290400981903076,
            "dist": 2735.4883918101486,
            "address": {
                "streetName": "Microsoft West Campus Road",
                "municipalitySubdivision": "Redmond",
                "municipality": "Bellevue",
                "countrySecondarySubdivision": "King",
                "countryTertiarySubdivision": "Seattle East",
                "countrySubdivision": "WA",
                "countrySubdivisionName": "Washington",
                "postalCode": "98007",
                "countryCode": "US",
                "country": "United States",
                "countryCodeISO3": "USA",
                "freeformAddress": "Microsoft West Campus Road, Bellevue, WA 98007"
            },
            "position": {
                "lat": 47.65784,
                "lon": -122.14335
            },
            "viewport": {
                "topLeftPoint": {
                    "lat": 47.65785,
                    "lon": -122.14335,
                    "valid": true
                },
                "btmRightPoint": {
                    "lat": 47.65784,
                    "lon": -122.14325,
                    "valid": true
                }
            }
        },
        {
            "type": "Street",
            "id": "US/STR/p0/12812615",
            "score": 2.527509927749634,
            "dist": 2870.9579016916873,
            "address": {
                "streetName": "Microsoft West Campus Road",
                "municipalitySubdivision": "Redmond",
                "municipality": "Redmond",
                "countrySecondarySubdivision": "King",
                "countryTertiarySubdivision": "Seattle East",
                "countrySubdivision": "WA",
                "countrySubdivisionName": "Washington",
                "postalCode": "98052",
                "countryCode": "US",
                "country": "United States",
                "countryCodeISO3": "USA",
                "freeformAddress": "Microsoft West Campus Road, Redmond, WA 98052"
            },
            "position": {
                "lat": 47.66034,
                "lon": -122.1404
            },
            "viewport": {
                "topLeftPoint": {
                    "lat": 47.66039,
                    "lon": -122.14325,
                    "valid": true
                },
                "btmRightPoint": {
                    "lat": 47.65778,
                    "lon": -122.13749,
                    "valid": true
                }
            }
        },
        {
            "type": "Street",
            "id": "US/STR/p0/197588",
            "score": 2.4630401134490967,
            "dist": 878.1404663812472,
            "address": {
                "streetName": "157th Avenue Northeast",
                "municipalitySubdivision": "Redmond",
                "municipality": "Redmond",
                "countrySecondarySubdivision": "King",
                "countryTertiarySubdivision": "Seattle East",
                "countrySubdivision": "WA",
                "countrySubdivisionName": "Washington",
                "postalCode": "98052",
                "extendedPostalCode": "980525344, 980525398, 980525399",
                "countryCode": "US",
                "country": "United States",
                "countryCodeISO3": "USA",
                "freeformAddress": "157th Avenue Northeast, Redmond, WA 98052"
            },
            "position": {
                "lat": 47.64351,
                "lon": -122.13056
            },
            "viewport": {
                "topLeftPoint": {
                    "lat": 47.64473,
                    "lon": -122.13058,
                    "valid": true
                },
                "btmRightPoint": {
                    "lat": 47.6425,
                    "lon": -122.13016,
                    "valid": true
                }
            }
        },
        {
            "type": "Street",
            "id": "US/STR/p0/3033991",
            "score": 2.0754499435424805,
            "dist": 3655467.8844475765,
            "address": {
                "streetName": "Microsoft Way",
                "municipalitySubdivision": "Yorkmount, Charlotte",
            },
            "position": {
                "lat": 35.14267,
                "lon": -80.91824
            },
            "viewport": {
                "topLeftPoint": {
                    "lat": 35.14287,
                    "lon": -80.91839,
                    "valid": true
                },
                "btmRightPoint": {
                    "lat": 35.14267,
                    "lon": -80.91814,
                    "valid": true
                }
            }
        },
        {
            "type": "Street",
            "id": "US/STR/p0/8395877",
            "score": 2.0754499435424805,
            "dist": 3655437.0037482483,
            "address": {
                "streetName": "Microsoft Way",
                "municipalitySubdivision": "Charlotte",
                "municipality": "Charlotte",
                "countrySecondarySubdivision": "Mecklenburg",
                "countryTertiarySubdivision": "Township 1 Charlotte",
                "countrySubdivision": "NC",
                "countrySubdivisionName": "North Carolina",
                "postalCode": "28273",
                "extendedPostalCode": "282738105, 282738106, 282738108, 2827382, 282738200",
                "countryCode": "US",
                "country": "United States",
                "countryCodeISO3": "USA",
                "freeformAddress": "Microsoft Way, Charlotte, NC 28273"
            },
            "position": {
                "lat": 35.14134,
                "lon": -80.9198
            },
            "viewport": {
                "topLeftPoint": {
                    "lat": 35.14274,
                    "lon": -80.92159,
                    "valid": true
                },
                "btmRightPoint": {
                    "lat": 35.14002,
                    "lon": -80.91824,
                    "valid": true
                }
            }
        }
    ]
}
```


### <a name="encode-a-uri-to-handle-special-characters"></a>특수 문자를 처리하도록 URI 인코딩 

교차 도로 주소를 찾으려면 주소의 특수 문자를 처리하도록 URI를 인코딩해야 합니다. 이 주소 예제를 생각해 보십시오: *1번가 & 유니언 스트리트, 시애틀.* 여기서는 요청을 보내기 전에 앰퍼샌드 문자()를`&`인코딩합니다. 

URI에서 문자 데이터를 인코딩하는 것이 좋습니다. URI에서는 백분율 부호()`%`및 문자의 UTF-8 코드에 해당하는 두 문자 헥사데피만 값을 사용하여 모든 문자를 인코딩합니다.

#### <a name="usage-examples"></a>사용 예

이 주소로 시작합니다.

```
query=1st Avenue & E 111th St, New York
```

주소 인코딩:

```
query=1st%20Avenue%20%26%20E%20111th%20St%2C%20New%20York
```

다음 방법을 사용할 수 있습니다.

자바 스크립트 또는 유형 스크립트 :
```Javascript
encodeURIComponent(query)
```

C# 또는 Visual Basic의 경우: 입니다.
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

루비:
```Ruby
CGI::escape(query) 
```

Swift:
```Swift
query.stringByAddingPercentEncodingWithAllowedCharacters(.URLHostAllowedCharacterSet()) 
```

이동:
```Go
import ("net/url") 
url.QueryEscape(query)
```


## <a name="best-practices-for-poi-searching"></a>POI 검색을 위한 모범 사례

POI 검색에서 이름으로 POI 결과를 요청할 수 있습니다. 예를 들어 이름으로 비즈니스를 검색할 수 있습니다. 

매개 변수를 `countrySet` 사용하여 응용 프로그램에 적용 범위가 필요한 국가를 지정하는 것이 좋습니다. 기본 동작은 전체 세계를 검색하는 것입니다. 이 광범위한 검색은 불필요한 결과를 반환할 수 있으며 검색에 시간이 오래 걸릴 수 있습니다.

### <a name="brand-search"></a>브랜드 검색

응답내의 결과 및 정보의 관련성을 개선하기 위해 POI 검색 응답에는 브랜드 정보가 포함됩니다. 이 정보를 사용하여 응답을 구문 분석할 수 있습니다.

요청에서 쉼표로 구분된 브랜드 이름 목록을 제출할 수 있습니다. 목록을 사용하여 매개 변수를 설정하여 결과를 `brandSet` 특정 브랜드로 제한합니다. 목록에서 항목 순서는 중요하지 않습니다. 여러 브랜드 목록을 제공하는 경우 반환되는 결과는 목록 중 하나 이상에 속해야 합니다.

브랜드 검색을 살펴보려면 [POI 카테고리 검색](https://docs.microsoft.com/rest/api/maps/search/getsearchpoicategory) 요청을 해 보겠습니다. 다음 예제에서는 워싱턴 주 레드먼드에 있는 Microsoft 캠퍼스 근처의 주유소를 찾습니다. 응답에는 반환된 각 POI에 대한 브랜드 정보가 표시됩니다.

#### <a name="sample-query"></a>샘플 쿼리

```HTTP
https://atlas.microsoft.com/search/poi/json?subscription-key={subscription-key}&api-version=1.0&query=gas%20station&limit=3&lat=47.6413362&lon=-122.1327968
```

#### <a name="response"></a>응답

```JSON
{
    "summary": {
        "query": "gas station",
        "queryType": "NON_NEAR",
        "queryTime": 276,
        "numResults": 3,
        "offset": 0,
        "totalResults": 762680,
        "fuzzyLevel": 1,
        "geoBias": {
            "lat": 47.6413362,
            "lon": -122.1327968
        }
    },
    "results": [
        {
            "type": "POI",
            "id": "US/POI/p0/8831765",
            "score": 5.6631999015808105,
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
                "categorySet": [
                    {
                        "id": 7311
                    }
                ],
                "url": "www.chevron.com",
                "categories": [
                    "petrol station"
                ],
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
            },
            "position": {
                "lat": 47.63201,
                "lon": -122.13281
            },
            "viewport": {
                "topLeftPoint": {
                    "lat": 47.63291,
                    "lon": -122.13414,
                    "valid": true
                },
                "btmRightPoint": {
                    "lat": 47.63111,
                    "lon": -122.13148,
                    "valid": true
                }
            },
            "entryPoints": [
                {
                    "type": "main",
                    "position": {
                        "lat": 47.63222,
                        "lon": -122.13312,
                        "valid": true
                    }
                }
            ]
        },
        {
            "type": "POI",
            "id": "US/POI/p0/8831752",
            "score": 5.662710189819336,
            "dist": 1330.1278248163273,
            "info": "search:ta:840539001100326-US",
            "poi": {
                "name": "76",
                "phone": "+(1)-(425)-7472126",
                "brands": [
                    {
                        "name": "76"
                    }
                ],
                "categorySet": [
                    {
                        "id": 7311
                    }
                ],
                "url": "www.76.com",
                "categories": [
                    "petrol station"
                ],
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
                "streetNumber": "2421",
                "streetName": "148Th Ave Ne",
                "municipalitySubdivision": "Redmond, Bridle Trails, Bellevue",
                "municipality": "Redmond, Bellevue",
                "countrySecondarySubdivision": "King",
                "countryTertiarySubdivision": "Seattle East",
                "countrySubdivision": "WA",
                "countrySubdivisionName": "Washington",
                "postalCode": "98007",
                "countryCode": "US",
                "country": "United States",
                "countryCodeISO3": "USA",
                "freeformAddress": "2421 148Th Ave Ne, Bellevue, WA 98007",
                "localName": "Bellevue"
            },
            "position": {
                "lat": 47.63187,
                "lon": -122.14365
            },
            "viewport": {
                "topLeftPoint": {
                    "lat": 47.63277,
                    "lon": -122.14498,
                    "valid": true
                },
                "btmRightPoint": {
                    "lat": 47.63097,
                    "lon": -122.14232,
                    "valid": true
                }
            },
            "entryPoints": [
                {
                    "type": "minor",
                    "position": {
                        "lat": 47.63187,
                        "lon": -122.14374,
                        "valid": true
                    }
                },
                {
                    "type": "main",
                    "position": {
                        "lat": 47.63186,
                        "lon": -122.14313,
                        "valid": true
                    }
                }
            ]
        },
        {
            "type": "POI",
            "id": "US/POI/p0/8831764",
            "score": 5.662449836730957,
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
                "categorySet": [
                    {
                        "id": 7311
                    }
                ],
                "url": "www.texaco.com/",
                "categories": [
                    "petrol station"
                ],
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
            },
            "position": {
                "lat": 47.62843,
                "lon": -122.13628
            },
            "viewport": {
                "topLeftPoint": {
                    "lat": 47.62933,
                    "lon": -122.13761,
                    "valid": true
                },
                "btmRightPoint": {
                    "lat": 47.62753,
                    "lon": -122.13495,
                    "valid": true
                }
            },
            "entryPoints": [
                {
                    "type": "main",
                    "position": {
                        "lat": 47.62827,
                        "lon": -122.13628,
                        "valid": true
                    }
                }
            ]
        }
    ]
}
```


### <a name="airport-search"></a>공항 검색

검색 POI API를 사용하면 공식 코드를 사용하여 공항을 찾을 수 있습니다. 예를 들어 *SEA를* 사용하여 시애틀-타코마 국제공항을 찾을 수 있습니다. 

```HTTP
https://atlas.microsoft.com/search/poi/json?subscription-key={subscription-key}&api-version=1.0&query=SEA 
```

### <a name="nearby-search"></a>주변 검색

특정 위치에서 POI 결과를 검색하려면 [근처 검색 API를](https://docs.microsoft.com/rest/api/maps/search/getsearchnearby)사용하여 시도할 수 있습니다. 끝점은 POI 결과만 반환합니다. 검색 쿼리 매개 변수는 차지하지 않습니다. 

결과를 제한하려면 반경을 설정하는 것이 좋습니다.

## <a name="understanding-the-responses"></a>응답 이해

Azure 지도 검색 서비스에 주소 검색을 요청하여 시애틀의 주소를 찾아보겠습니다. 다음 요청 URL에서 `countrySet` 미국에서 주소를 `US` 검색하는 매개 변수를 설정합니다.

### <a name="sample-query"></a>샘플 쿼리

```HTTP
https://atlas.microsoft.com/search/address/json?subscription-key={subscription-key}&api-version=1&query=400%20Broad%20Street%2C%20Seattle%2C%20WA&countrySet=US
```

### <a name="supported-types-of-results"></a>지원되는 결과 유형

* **점 주소**: 도로 이름과 번호가 있는 특정 주소가 있는 지도의 포인트입니다. 포인트 주소는 주소에 대해 최고 수준의 정확도를 제공합니다. 

* **주소 범위**: 거리의 시작과 끝에서 보간되는 주소 점의 범위입니다.  

* **지역**: 토지의 행정 구역(예: 국가, 주 또는 도시)을 나타내는 지도의 영역입니다. 

* **POI**: 주의할 만한 가치가 있고 흥미로워질 수 있는 지도상의 포인트입니다.

* **거리**: 지도에 거리. 주소는 주소가 포함된 거리의 위도와 경도 좌표로 확인됩니다. 집 번호가 처리되지 않을 수 있습니다. 

* **교차로**: 교차로. 교차로는 두 거리가 교차하는 교차를 나타냅니다.

### <a name="response"></a>응답

응답 구조를 살펴보겠습니다. 다음 응답에서 결과 개체의 형식이 다릅니다. 주의 깊게 살펴보면 세 가지 유형의 결과 개체가 표시됩니다.

* 점 주소
* Street
* 크로스 스트리트

주소 검색이 POI를 반환하지 않습니다.  

각 `Score` 응답 개체에 대한 매개 변수는 일치 점수가 동일한 응답의 다른 개체의 점수와 어떻게 관련되는지를 나타냅니다. 응답 개체 매개 변수에 대한 자세한 내용은 [검색 주소 받기를](https://docs.microsoft.com/rest/api/maps/search/getsearchaddress)참조하십시오.

```JSON
{
    "summary": {
        "query": "400 broad street seattle wa",
        "queryType": "NON_NEAR",
        "queryTime": 146,
        "numResults": 6,
        "offset": 0,
        "totalResults": 7,
        "fuzzyLevel": 1
    },
    "results": [
        {
            "type": "Point Address",
            "id": "US/PAD/p0/28725082",
            "score": 9.893799781799316,
            "address": {
                "streetNumber": "400",
                "streetName": "Broad Street",
            },
            "position": {
                "lat": 47.62039,
                "lon": -122.34928
            },
            "viewport": {
                "topLeftPoint": {
                    "lat": 47.62129,
                    "lon": -122.35061,
                    "valid": true
                },
                "btmRightPoint": {
                    "lat": 47.61949,
                    "lon": -122.34795,
                    "valid": true
                }
            },
            "entryPoints": [
                {
                    "type": "main",
                    "position": {
                        "lat": 47.61982,
                        "lon": -122.34886,
                        "valid": true
                    }
                }
            ]
        },
        {
            "type": "Street",
            "id": "US/STR/p0/6700384",
            "score": 8.129190444946289,
            "address": {
                "streetName": "Broad Street",
            },
            "position": {
                "lat": 47.61724,
                "lon": -122.35207
            },
            "viewport": {
                "topLeftPoint": {
                    "lat": 47.61825,
                    "lon": -122.35336,
                    "valid": true
                },
                "btmRightPoint": {
                    "lat": 47.61626,
                    "lon": -122.35078,
                    "valid": true
                }
            }
        },
        {
            "type": "Street",
            "id": "US/STR/p0/9701953",
            "score": 8.129190444946289,
            "address": {
                "streetName": "Broad Street",
            },
            "position": {
                "lat": 47.61965,
                "lon": -122.349
            },
            "viewport": {
                "topLeftPoint": {
                    "lat": 47.62066,
                    "lon": -122.35041,
                    "valid": true
                },
                "btmRightPoint": {
                    "lat": 47.61857,
                    "lon": -122.34761,
                    "valid": true
                }
            }
        },
        {
            "type": "Street",
            "id": "US/STR/p0/11721297",
            "score": 8.129190444946289,
            "address": {
                "streetName": "Broad Street",
                "municipalitySubdivision": "Seattle, Downtown Seattle, Denny Regrade, Belltown",
                "municipality": "Seattle",
                "countrySecondarySubdivision": "King",
                "countryTertiarySubdivision": "Seattle",
                "countrySubdivision": "WA",
                "countrySubdivisionName": "Washington",
                "postalCode": "98121",
                "extendedPostalCode": "981211237",
                "countryCode": "US",
                "country": "United States",
                "countryCodeISO3": "USA",
                "freeformAddress": "Broad Street, Seattle, WA 98121"
            },
            "position": {
                "lat": 47.61825,
                "lon": -122.35078
            },
            "viewport": {
                "topLeftPoint": {
                    "lat": 47.61857,
                    "lon": -122.35078,
                    "valid": true
                },
                "btmRightPoint": {
                    "lat": 47.61825,
                    "lon": -122.35041,
                    "valid": true
                }
            }
        },
        {
            "type": "Cross Street",
            "id": "US/XSTR/p1/232144",
            "score": 6.754479885101318,
            "address": {
                "streetName": "Broad Street & Valley Street",
                "municipalitySubdivision": "South Lake Union, Seattle",
            },
            "position": {
                "lat": 47.62545,
                "lon": -122.33974
            },
            "viewport": {
                "topLeftPoint": {
                    "lat": 47.62635,
                    "lon": -122.34107,
                    "valid": true
                },
                "btmRightPoint": {
                    "lat": 47.62455,
                    "lon": -122.33841,
                    "valid": true
                }
            }
        }
    ]
}
```

### <a name="geometry"></a>geometry

*지오메트리의* 응답 유형에는 `dataSources` 개체 아래에 `geometry` 반환되는 형상 ID와 `id`. 예를 들어 [다각형 검색 서비스를](https://docs.microsoft.com/rest/api/maps/search/getsearchpolygon) 사용하여 GeoJSON 형식으로 형상 데이터를 요청할 수 있습니다. 이 형식을 사용하면 엔터티 집합에 대한 도시 또는 공항 개요를 얻을 수 있습니다. 그런 다음 이 경계 데이터를 사용하여 [지오메트리 내부의 지리 울타리를 설정하거나](https://docs.microsoft.com/azure/azure-maps/tutorial-geofence) [POI를 검색할](https://docs.microsoft.com/rest/api/maps/search/postsearchinsidegeometry)수 있습니다.


[검색 주소](https://docs.microsoft.com/rest/api/maps/search/getsearchaddress) API 또는 [검색 퍼지](https://docs.microsoft.com/rest/api/maps/search/getsearchfuzzy) API에 대한 응답에는 다음과 같은 `dataSources` `geometry` `id`개체에 반환되는 형상 ID가 포함될 수 있습니다.


```JSON 
"dataSources": { 
        "geometry": { 
            "id": "00005557-4100-3c00-0000-000059690938" // The geometry ID is returned in the dataSources object under "geometry" and "id".
        }
} 
```

## <a name="next-steps"></a>다음 단계

자세한 내용은 다음을 참조하십시오.

> [!div class="nextstepaction"]
> [Azure 지도 검색 서비스 요청을 빌드 하는 방법](https://docs.microsoft.com/azure/azure-maps/how-to-search-for-address)

> [!div class="nextstepaction"]
> [검색 서비스 API 문서](https://docs.microsoft.com/rest/api/maps/search)