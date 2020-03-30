---
title: 대중교통 데이터 요청 | 마이크로소프트 Azure 지도
description: 이 문서에서는 Microsoft Azure 지도 이동성 서비스를 사용하여 대중 교통 데이터를 요청하는 방법을 알아봅니다.
author: philmea
ms.author: philmea
ms.date: 09/06/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: philmea
ms.custom: mvc
ms.openlocfilehash: f60b66790342874620971c8f15a1e8ace9a3c7cc
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "80335465"
---
# <a name="request-public-transit-data-using-the-azure-maps-mobility-service"></a>Azure 지도 모빌리티 서비스를 사용하여 대중 교통 데이터 요청 

이 문서에서는 Azure Maps [모빌리티 서비스를](https://aka.ms/AzureMapsMobilityService) 사용하여 대중 교통 데이터를 요청하는 방법을 보여 주며 있습니다. 대중 교통 데이터에는 대중 교통 정류장, 경로 정보 및 이동 시간 예측이 포함됩니다.

이 문서에서는 방법을 알아봅니다.

* 메트로 지역 [API를](https://aka.ms/AzureMapsMobilityMetro) 사용하여 메트로 지역 ID 받기
* 인근 대중교통 서비스 이용을 통해 인근 대중교통 정류장을 [요청하십시오.](https://aka.ms/AzureMapsMobilityNearbyTransit)
* 대중 교통을 사용하여 경로를 계획하는 [대중 교통 경로 API 를 가져옵니다.](https://aka.ms/AzureMapsMobilityTransitRoute)
* 대중 교통 여정 [API를](https://aka.ms/https://azure.microsoft.com/services/azure-maps/)사용하여 경로에 대한 대중 교통 경로 지오메트리 및 자세한 일정을 요청하십시오.


## <a name="prerequisites"></a>사전 요구 사항

먼저 Azure Maps 공용 이동 API를 호출하려면 Azure Maps 계정과 구독 키가 있어야 합니다. 자세한 내용은 계정 [만들기의](quick-demo-map-app.md#create-an-account-with-azure-maps) 지침에 따라 Azure Maps 계정을 만듭니다. 기본 [키를 가져오는](quick-demo-map-app.md#get-the-primary-key-for-your-account) 단계를 수행하여 계정의 기본 키를 가져옵니다. Azure Maps의 인증에 대한 자세한 내용은 [Azure Maps의 인증 관리](./how-to-manage-authentication.md)를 참조하세요.


이 문서에서는 [우체부 앱](https://www.getpostman.com/apps)을 사용하여 REST 호출을 빌드합니다. 선호하는 모든 API 개발 환경을 사용할 수 있습니다.


## <a name="get-a-metro-area-id"></a>메트로 지역 ID 받기

특정 대도시 지역에 대한 대중 교통 정보를 요청하려면 `metroId` 해당 지역의 교통 정보가 필요합니다. [메트로 영역 GET API를](https://aka.ms/AzureMapsMobilityMetro) 사용하면 Azure 지도 모빌리티 서비스를 사용할 수 있는 메트로 지역을 요청할 수 있습니다. 응답에는 GeoJSON `metroId`형식의 메트로 영역 지오메트리의 표현 및 에 `metroName`대한 세부 정보가 포함됩니다.

시애틀-타코마 메트로 지역 ID의 메트로 지역을 요청해 보겠습니다. 지하철 지역에 대한 신분증을 요청하려면 다음 단계를 완료하십시오.

1. Postman 앱을 열고 요청을 저장할 컬렉션을 만들어 보겠습니다. 우체부 앱 의 상단 근처에서 **새**를 선택합니다. 새 **만들기** 창에서 **컬렉션**을 선택합니다.  컬렉션의 이름을 지정하고 **만들기** 단추를 선택합니다.

2. 요청을 만들려면 **새로 만들기를** 다시 선택합니다. 새 **만들기** 창에서 **요청을**선택합니다. 요청에 대한 **요청 이름을** 입력합니다. 이전 단계에서 만든 컬렉션을 요청을 저장할 위치로 선택합니다. 그런 다음 **저장을**선택합니다.
    
    ![우체부에서 요청 만들기](./media/how-to-request-transit-data/postman-new.png)

3. 빌더 탭에서 **GET** HTTP 메서드를 선택하고 다음 URL을 입력하여 GET 요청을 만듭니다. 을 `{subscription-key}`Azure Maps 기본 키로 바꿉습니다.

    ```HTTP
    https://atlas.microsoft.com/mobility/metroArea/id/json?subscription-key={subscription-key}&api-version=1.0&query=47.63096,-122.126
    ```

4. 요청이 성공하면 다음과 같은 응답을 받게 됩니다.

    ```JSON
    {
        "results": [
            {
                "metroId": 522,
                "metroName": "Seattle–Tacoma–Bellevue, WA",
                "viewport": {
                    "topLeftPoint": {
                        "latitude": 48.5853,
                        "longitude": -124.80934
                    },
                    "btmRightPoint": {
                        "latitude": 46.90534,
                        "longitude": -121.55032
                    }
                },
                "geometry": {
                    "type": "Polygon",
                    "coordinates": [
                        [
                            [
                                -121.99604,
                                47.16147
                            ],
                            [
                                -121.97051,
                                47.17222
                            ],
                            [
                                -121.96308,
                                47.17671
                            ],
                            ...,
                            ...,
                            ...,
                            [
                                -122.01525,
                                47.16008
                            ],
                            [
                                -122.00553,
                                47.15919
                            ],
                            [
                                -121.99604,
                                47.16147
                            ]
                        ]
                    ]
                }
            }
        ]
    }
    ```

5. `metroId`을 복사하면 나중에 사용해야 합니다.

## <a name="request-nearby-transit-stops"></a>인근 환승 정류장 요청

Azure 지도 [근처 대중 교통](https://aka.ms/AzureMapsMobilityNearbyTransit) 서비스를 통해 전송 개체를 검색할 수 있습니다.  API는 지정된 위치 주변의 대중 교통 정류장 및 공유 자전거와 같은 대중 교통 개체 세부 정보를 반환합니다. 다음으로 지정된 위치 주변 의 반경 300미터 이내에 있는 인근 대중 교통 정류장을 검색하도록 서비스에 요청합니다. 요청에서 이전에 검색된 검색된 검색어를 `metroId` 포함해야 합니다.

[인근 대중교통](https://aka.ms/AzureMapsMobilityNearbyTransit)이용을 요청하려면 아래 단계를 따르세요.

1. 우체부에서 **새 요청** | **GET 요청을** 클릭하고 이름 근처의 **중지를 클릭합니다.**

2. 빌더 탭에서 **GET** HTTP 메서드를 선택하고 API 끝점에 대한 다음 요청 URL을 입력하고 **보내기를**클릭합니다.

    ```HTTP
    https://atlas.microsoft.com/mobility/transit/nearby/json?subscription-key={subscription-key}&api-version=1.0&metroId=522&query=47.63096,-122.126&radius=300&objectType=stop
    ```

3. 요청이 성공하면 응답 구조는 아래와 같아야 합니다.

    ```JSON
    {
        "results": [
            {
                "id": "522---2060603",
                "type": "stop",
                "objectDetails": {
                    "stopKey": "71300",
                    "stopName": "NE 24th St & 162nd Ave NE",
                    "stopCode": "71300",
                    "mainTransitType": "Bus",
                    "mainAgencyId": "522---5872",
                    "mainAgencyName": "Metro Transit"
                },
                "position": {
                    "latitude": 47.631504,
                    "longitude": -122.125275
                },
                "viewport": {
                    "topLeftPoint": {
                        "latitude": 47.632413,
                        "longitude": -122.12659
                    },
                    "btmRightPoint": {
                        "latitude": 47.630594,
                        "longitude": -122.123959
                    }
                }
            },
            {
                "id": "522---2061020",
                "type": "stop",
                "objectDetails": {
                    "stopKey": "68372",
                    "stopName": "NE 24th St & 160th Ave NE",
                    "stopCode": "68372",
                    "mainTransitType": "Bus",
                    "mainAgencyId": "522---5872",
                    "mainAgencyName": "Metro Transit"
                },
                "position": {
                    "latitude": 47.631409,
                    "longitude": -122.127136
                },
                "viewport": {
                    "topLeftPoint": {
                        "latitude": 47.632318,
                        "longitude": -122.128451
                    },
                    "btmRightPoint": {
                        "latitude": 47.630499,
                        "longitude": -122.12582
                    }
                }
            },
            {
                "id": "522---2060604",
                "type": "stop",
                "objectDetails": {
                    "stopKey": "71310",
                    "stopName": "NE 24th St & 160th Ave NE",
                    "stopCode": "71310",
                    "mainTransitType": "Bus",
                    "mainAgencyId": "522---5872",
                    "mainAgencyName": "Metro Transit"
                },
                "position": {
                    "latitude": 47.631565,
                    "longitude": -122.127808
                },
                "viewport": {
                    "topLeftPoint": {
                        "latitude": 47.632474,
                        "longitude": -122.129124
                    },
                    "btmRightPoint": {
                        "latitude": 47.630655,
                        "longitude": -122.126492
                    }
                }
            }
        ]
    }   
    ```

응답 구조를 주의 깊게 관찰하면 각 transit 개체에 대한 매개 변수가 포함되어 있음을 알 수 있습니다. 각 transit `id`개체에는 개체의 `type` `stopName`좌표에서 의 . . `mainTransitType` `mainAgencyName`

학습을 위해 다음 `id` 섹션의 노선에 버스 정류장을 원산지로 사용합니다.  


## <a name="request-a-transit-route"></a>환승 경로 요청

Azure 지도 [전송 경로 API를 가져옵니다](https://aka.ms/AzureMapsMobilityTransitRoute) 여행 계획을 허용 합니다. 출발지에서 목적지까지 최상의 경로 옵션을 반환합니다. 이 서비스는 도보, 자전거 타기 및 대중 교통을 포함한 다양한 종류의 여행 모드를 제공합니다. 다음으로, 가장 가까운 버스 정류장에서 시애틀의 스페이스 니들 타워까지 가는 경로를 검색합니다.

### <a name="get-location-coordinates-for-destination"></a>목적지에 대한 위치 좌표 받기

스페이스 니들 타워의 위치 좌표를 얻으려면 Azure Maps [퍼지 검색 서비스를](https://docs.microsoft.com/rest/api/maps/search/getsearchfuzzy)사용할 수 있습니다.

퍼지 검색 서비스에 요청하려면 다음 단계를 따르십시오.

1. 우체부에서 **새 요청** | **GET 요청을** 클릭하고 위치 **좌표를 가져옵니다.**

2.  빌더 탭에서 **GET** HTTP 메서드를 선택하고 다음 요청 URL을 입력하고 **보내기를**클릭합니다.
 
    ```HTTP
    https://atlas.microsoft.com/search/fuzzy/json?subscription-key={subscription-key}&api-version=1.0&query=space needle
    ```
    
3. 응답을 주의 깊게 살펴보면 스페이스 니들 검색 결과에 여러 위치가 포함됩니다. 각 결과에는 **위치**아래의 위치 좌표가 포함됩니다. 첫 번째 결과의 위치에 있는 복사합니다. **position** `lat` `lon`
    
   ```JSON
   {
        "summary": {
            "query": "space needle",
            "queryType": "NON_NEAR",
            "queryTime": 35,
            "numResults": 8,
            "offset": 0,
            "totalResults": 11,
            "fuzzyLevel": 1
        },
        "results": [
            {
                "type": "POI",
                "id": "US/POI/p0/6993440",
                "score": 4.67369,
                "info": "search:ta:840539001406144-US",
                "poi": {
                    "name": "Space Needle",
                    "phone": "+(1)-(206)-9052100",
                    "categorySet": [
                        {
                            "id": 7376009
                        }
                    ],
                    "url": "www.spaceneedle.com",
                    "categories": [
                        "important tourist attraction",
                        "tower"
                    ],
                    "classifications": [
                        {
                            "code": "IMPORTANT_TOURIST_ATTRACTION",
                            "names": [
                                {
                                    "nameLocale": "en-US",
                                    "name": "important tourist attraction"
                                },
                                {
                                    "nameLocale": "en-US",
                                    "name": "tower"
                                }
                            ]
                        }
                    ]
                },
                "address": {
                    "streetNumber": "400",
                    "streetName": "Broad St",
                    "municipalitySubdivision": "South Lake Union, Seattle, Lower Queen Anne",
                    "municipality": "Seattle",
                    "countrySecondarySubdivision": "King",
                    "countryTertiarySubdivision": "Seattle",
                    "countrySubdivision": "WA",
                    "postalCode": "98109",
                    "countryCode": "US",
                    "country": "United States",
                    "countryCodeISO3": "USA",
                    "freeformAddress": "400 Broad St, Seattle, WA 98109",
                    "localName": "Seattle",
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
            ...,
            ...,
            ...
        ]
    }
    ``` 
    

### <a name="request-route"></a>경로 요청

경로 요청을 하려면 아래 단계를 완료하십시오.

1. 우체부에서 **새 요청** | **GET 요청을** 클릭하고 경로 정보 **받기의**이름을 지정합니다.

2. 빌더 탭에서 **GET** HTTP 메서드를 선택하고 API 끝점에 대한 다음 요청 URL을 입력하고 **보내기를**클릭합니다.

    우리는 `modeType` 버스에 `transitType` 대한 대중 교통 노선을 요청하고 매개 변수를 지정합니다. 요청 URL에는 이전 섹션에서 검색된 위치가 포함됩니다. `originType`에 대한, 우리는 지금 **stopId**. `destionationType`그리고, 우리는 **위치가**.

    [전송 경로 API에](https://aka.ms/AzureMapsMobilityTransitRoute)대한 요청에서 사용할 수 있는 [URI 매개 변수 목록을](https://aka.ms/AzureMapsMobilityTransitRoute#uri-parameters) 확인합니다. 
  
    ```HTTP
    https://atlas.microsoft.com/mobility/transit/route/json?subscription-key={subscription-key}&api-version=1.0&metroId=522&originType=stopId&origin=522---2060603&destionationType=position&destination=47.62039,-122.34928&modeType=publicTransit&transitType=bus
    ```

3. 요청이 성공하면 응답 구조는 아래와 같아야 합니다.

    ```JSON
    {
        "results": [
            {
                "itineraryId": "cb6b6b6f-5cda-451e-b68d-2e97971dd60c---20190906BBBEC4D2219A436A9D794224978C9BBF:0---522",
                "departureTime": "2019-09-07T01:01:50Z",
                "arrivalTime": "2019-09-07T02:16:33Z",
                "travelTimeInSeconds": 4483,
                "numberOfLegs": 8,
                "legs": [
                    {
                        "legType": "Wait",
                        "legStartTime": "2019-09-07T01:01:50Z",
                        "legEndTime": "2019-09-07T01:01:50Z",
                        "caption": "249"
                    },
                    {
                        "legType": "Bus",
                        "legStartTime": "2019-09-07T01:01:50Z",
                        "legEndTime": "2019-09-07T01:26:00Z",
                        "caption": "249",
                        "lengthInMeters": 9139
                    },
                    {
                        "legType": "Wait",
                        "legStartTime": "2019-09-07T01:26:00Z",
                        "legEndTime": "2019-09-07T01:28:00Z",
                        "caption": "255"
                    },
                    {
                        "legType": "Bus",
                        "legStartTime": "2019-09-07T01:28:00Z",
                        "legEndTime": "2019-09-07T01:57:21Z",
                        "caption": "255",
                        "lengthInMeters": 13136
                    },
                    {
                        "legType": "Walk",
                        "legStartTime": "2019-09-07T01:57:22Z",
                        "legEndTime": "2019-09-07T02:01:27Z",
                        "caption": "Denny Way",
                        "lengthInMeters": 308
                    },
                    {
                        "legType": "Wait",
                        "legStartTime": "2019-09-07T02:01:27Z",
                        "legEndTime": "2019-09-07T02:06:33Z",
                        "caption": "8"
                    },
                    {
                        "legType": "Bus",
                        "legStartTime": "2019-09-07T02:06:33Z",
                        "legEndTime": "2019-09-07T02:12:41Z",
                        "caption": "8",
                        "lengthInMeters": 1060
                    },
                    {
                        "legType": "Walk",
                        "legStartTime": "2019-09-07T02:12:42Z",
                        "legEndTime": "2019-09-07T02:16:33Z",
                        "lengthInMeters": 251
                    }
                ]
            },
            ...,
            {
                "itineraryId": "cb6b6b6f-5cda-451e-b68d-2e97971dd60c---20190906BBBEC4D2219A436A9D794224978C9BBF:2---522",
                "departureTime": "2019-09-07T00:49:32Z",
                "arrivalTime": "2019-09-07T02:20:06Z",
                "travelTimeInSeconds": 5434,
                "numberOfLegs": 10,
                "legs": [
                    {
                        "legType": "Wait",
                        "legStartTime": "2019-09-07T00:49:32Z",
                        "legEndTime": "2019-09-07T00:49:32Z",
                        "caption": "226"
                    },
                    {
                        "legType": "Bus",
                        "legStartTime": "2019-09-07T00:49:32Z",
                        "legEndTime": "2019-09-07T01:15:00Z",
                        "caption": "226",
                        "lengthInMeters": 6792
                    },
                    {
                        "legType": "Wait",
                        "legStartTime": "2019-09-07T01:15:00Z",
                        "legEndTime": "2019-09-07T01:20:00Z",
                        "caption": "241"
                    },
                    {
                        "legType": "Bus",
                        "legStartTime": "2019-09-07T01:20:00Z",
                        "legEndTime": "2019-09-07T01:28:00Z",
                        "caption": "241",
                        "lengthInMeters": 3397
                    },
                    {
                        "legType": "Wait",
                        "legStartTime": "2019-09-07T01:28:00Z",
                        "legEndTime": "2019-09-07T01:33:00Z",
                        "caption": "550"
                    },
                    {
                        "legType": "Bus",
                        "legStartTime": "2019-09-07T01:33:00Z",
                        "legEndTime": "2019-09-07T01:58:00Z",
                        "caption": "550",
                        "lengthInMeters": 12899
                    },
                    {
                        "legType": "Walk",
                        "legStartTime": "2019-09-07T01:58:01Z",
                        "legEndTime": "2019-09-07T01:59:21Z",
                        "caption": "4th Avenue South",
                        "lengthInMeters": 99
                    },
                    {
                        "legType": "Wait",
                        "legStartTime": "2019-09-07T01:59:21Z",
                        "legEndTime": "2019-09-07T02:01:00Z",
                        "caption": "33"
                    },
                    {
                        "legType": "Bus",
                        "legStartTime": "2019-09-07T02:01:00Z",
                        "legEndTime": "2019-09-07T02:13:29Z",
                        "caption": "33,24",
                        "lengthInMeters": 2447
                    },
                    {
                        "legType": "Walk",
                        "legStartTime": "2019-09-07T02:13:30Z",
                        "legEndTime": "2019-09-07T02:20:06Z",
                        "lengthInMeters": 457
                    }
                ]
            }
        ]
    }
    ```

4. 주의 깊게 관찰하는 경우 응답에 여러 **버스** 노선이 있습니다. 각 경로에는 고유한 **여정 ID와** 경로의 각 구간을 설명하는 요약이 있습니다. 루트 다리는 두 스톱 웨이포인트 사이의 루트의 일부입니다. 다음으로 응답에서 를 사용하여 가장 빠른 `itineraryId` 경로에 대한 세부 정보를 요청합니다.

## <a name="request-fastest-route-itinerary"></a>가장 빠른 경로 여정 요청

Azure Maps [Get Transit 여정](https://aka.ms/AzureMapsMobilityTransitItinerary) 서비스를 사용하면 전송 경로 API 서비스에서 반환되는 경로의 여정 **ID를** 사용하여 특정 경로에 대한 데이터를 요청할 [수](https://aka.ms/AzureMapsMobilityTransitRoute) 있습니다. 요청을 하려면 아래 단계를 완료하십시오.

1. 우체부에서 **새 요청** | **GET 요청을** 클릭하고 대중 교통 정보 받기 **의**이름을 지정합니다.

2. 빌더 탭에서 **GET** HTTP 메서드를 선택합니다. API 끝점에 대해 다음 요청 URL을 입력하고 **보내기를**클릭합니다.

    응답에 `detailType` 대중 교통에 대한 정지 정보및 경로의 걷기 및 자전거 다리에 대한 턴 바이 턴 내비게이션이 포함되도록 매개 변수를 **지오메트리로** 설정합니다.

    ```HTTP
    https://atlas.microsoft.com/mobility/transit/itinerary/json?api-version=1.0&subscription-key={subscription-key}&query={itineraryId}&detailType=geometry
    ```
    
3. 요청이 성공하면 응답 구조는 아래와 같아야 합니다.

    ```JSON
    {
        "departureTime": "2019-09-07T01:01:50Z",
        "arrivalTime": "2019-09-07T02:16:33Z",
        "legs": [
            {
                "legType": "Wait",
                "legStartTime": "2019-09-07T01:01:50Z",
                "legEndTime": "2019-09-07T01:01:50Z",
                "lineGroup": {
                    "lineGroupId": "522---666077",
                    "agencyId": "522---5872",
                    "agencyName": "Metro Transit",
                    "lineNumber": "249",
                    "caption1": "Overlake TC - South Bellevue P&R",
                    "caption2": "249 Overlake TC - South Bellevue P&R",
                    "color": "347E5D",
                    "transitType": "Bus"
                },
                "line": {
                    "lineId": "522---3760143",
                    "lineGroupId": "522---666077",
                    "direction": "backward",
                    "agencyId": "522---5872",
                    "lineNumber": "249",
                    "lineDestination": "South Bellevue S Kirkland P&R"
                },
                "stops": [
                    {
                        "stopId": "522---2060603",
                        "stopKey": "71300",
                        "stopName": "NE 24th St & 162nd Ave NE",
                        "stopCode": "71300",
                        "position": {
                            "latitude": 47.631504,
                            "longitude": -122.125275
                        },
                        "mainTransitType": "Bus",
                        "mainAgencyId": "522---5872"
                    },
                    {
                        "stopId": "522---2061703",
                        "stopKey": "74450",
                        "stopName": "South Kirkland P&R & 108th Ave NE",
                        "stopCode": "74450",
                        "position": {
                            "latitude": 47.643852,
                            "longitude": -122.196693
                        },
                        "mainTransitType": "Bus",
                        "mainAgencyId": "522---5872"
                    }
                ],
                "waitOnVehicle": false
            },
            {
                "legType": "Bus",
                "legStartTime": "2019-09-07T01:01:50Z",
                "legEndTime": "2019-09-07T01:26:00Z",
                "lineGroup": {
                    "lineGroupId": "522---666077",
                    "agencyId": "522---5872",
                    "agencyName": "Metro Transit",
                    "lineNumber": "249",
                    "caption1": "Overlake TC - South Bellevue P&R",
                    "caption2": "249 Overlake TC - South Bellevue P&R",
                    "color": "347E5D",
                    "transitType": "Bus"
                },
                "line": {
                    "lineId": "522---3760143",
                    "lineGroupId": "522---666077",
                    "direction": "backward",
                    "agencyId": "522---5872",
                    "lineNumber": "249",
                    "lineDestination": "South Bellevue S Kirkland P&R"
                },
                "stops": [
                    {
                        "stopId": "522---2060603",
                        "stopKey": "71300",
                        "stopName": "NE 24th St & 162nd Ave NE",
                        "stopCode": "71300",
                        "position": {
                            "latitude": 47.631504,
                            "longitude": -122.125275
                        },
                        "mainTransitType": "Bus",
                        "mainAgencyId": "522---5872"
                    },
                    {
                        "stopId": "522---2060604",
                        "stopKey": "71310",
                        "stopName": "NE 24th St & 160th Ave NE",
                        "stopCode": "71310",
                        "position": {
                            "latitude": 47.631565,
                            "longitude": -122.127808
                        },
                        "mainTransitType": "Bus",
                        "mainAgencyId": "522---5872"
                    },
                    ...,
                    ...,
                    {
                        "stopId": "522---2061704",
                        "stopKey": "74451",
                        "stopName": "Northup Way & NE 33rd Pl",
                        "stopCode": "74451",
                        "position": {
                            "latitude": 47.640911,
                            "longitude": -122.194443
                        },
                        "mainTransitType": "Bus",
                        "mainAgencyId": "522---5872"
                    },
                    {
                        "stopId": "522---2061703",
                        "stopKey": "74450",
                        "stopName": "South Kirkland P&R & 108th Ave NE",
                        "stopCode": "74450",
                        "position": {
                            "latitude": 47.643852,
                            "longitude": -122.196693
                        },
                        "mainTransitType": "Bus",
                        "mainAgencyId": "522---5872"
                    }
                ],
                "geometry": {
                    "type": "LineString",
                    "coordinates": [
                        [
                            -122.12527,
                            47.63143
                        ],
                        [
                            -122.12529,
                            47.63143
                        ],
                        [
                            -122.12561,
                            47.63144
                        ],
                        [
                            -122.12701,
                            47.63148
                        ],
                        ...,
                        ...,
                        ...,
                        [
                            -122.19601,
                            47.64304
                        ],
                        [
                            -122.19584,
                            47.64315
                        ],
                        [
                            -122.19677,
                            47.6438
                        ]
                    ]
                }
            },
            ...,
            ...,
            ...,
            {
                "legType": "Walk",
                "legStartTime": "2019-09-07T02:12:42Z",
                "legEndTime": "2019-09-07T02:16:33Z",
                "steps": [
                    {
                        "direction": {
                            "relativeDirection": "depart"
                        },
                        "streetName": "Denny Way"
                    },
                    {
                        "direction": {
                            "relativeDirection": "right"
                        },
                        "streetName": "4th Avenue North"
                    },
                    {
                        "direction": {
                            "relativeDirection": "right"
                        },
                        "streetName": "Broad Street"
                    }
                ],
                "origin": {
                    "position": {
                        "latitude": 47.618578,
                        "longitude": -122.348058
                    }
                },
                "destination": {
                    "position": {
                        "latitude": 47.62039,
                        "longitude": -122.34928
                    }
                },
                "geometry": {
                    "type": "LineString",
                    "coordinates": [
                        [
                            -122.34806,
                            47.61857
                        ],
                        [
                            -122.3481,
                            47.61857
                        ],
                        [
                            -122.34894,
                            47.61858
                        ],
                        [
                            -122.34892,
                            47.61964
                        ],
                        [
                            -122.34877,
                            47.61975
                        ],
                        [
                            -122.3492,
                            47.62001
                        ],
                        [
                            -122.34918,
                            47.62003
                        ],
                        [
                            -122.34917,
                            47.62006
                        ],
                        [
                            -122.34916,
                            47.62008
                        ],
                        [
                            -122.34916,
                            47.62008
                        ],
                        [
                            -122.34916,
                            47.62008
                        ],
                        [
                            -122.34916,
                            47.62008
                        ],
                        [
                            -122.34928,
                            47.62039
                        ]
                    ]
                }
            }
        ]
    }
    ```

## <a name="next-steps"></a>다음 단계

모빌리티 서비스를 사용하여 실시간 데이터를 요청하는 방법에 대해 알아봅니다.

> [!div class="nextstepaction"]
> [실시간 데이터를 요청하는 방법](how-to-request-real-time-data.md)

Azure 지도 모빌리티 서비스 API 설명서 살펴보기

> [!div class="nextstepaction"]
> [모빌리티 서비스 API 문서](https://aka.ms/AzureMapsMobilityService)

