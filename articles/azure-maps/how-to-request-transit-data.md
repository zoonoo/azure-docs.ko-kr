---
title: Azure Maps에서 전송 중 데이터를 요청 하는 방법 | Microsoft Docs
description: Azure Maps 모바일 서비스를 사용 하 여 공용 전송 중인 데이터를 요청 합니다.
author: walsehgal
ms.author: v-musehg
ms.date: 06/05/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: philmea
ms.custom: mvc
ms.openlocfilehash: e8250763153f7c5b71f3906a560365dadfd55694
ms.sourcegitcommit: 4cdd4b65ddbd3261967cdcd6bc4adf46b4b49b01
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/06/2019
ms.locfileid: "66735573"
---
# <a name="request-public-transit-data-using-the-azure-maps-mobility-service"></a>Azure Maps Mobility Service를 사용 하 여 공용 전송 중인 데이터를 요청 합니다. 

이 문서에서는 Azure Maps를 사용 하는 방법을 보여 줍니다 [Mobility Service](https://aka.ms/AzureMapsMobilityService) 공용 전송 요청 중지를 비롯 한 데이터를 경로 정보, 및 시간 예상치를 이동 합니다.

이 문서를 배우게 됩니다 하는 방법.

* 사용 하 여 metro 영역 ID는 [Metro 영역 API 가져오기](https://aka.ms/AzureMapsMobilityMetro)
* 사용 하 여 주변 전송 중지 요청 [주변 전송 가져올](https://aka.ms/AzureMapsMobilityNearbyTransit) 서비스입니다.
* 쿼리 [전송 경로 API 가져오기](https://aka.ms/AzureMapsMobilityTransitRoute) 공용 전송을 사용 하 여 경로 계획 합니다.
* 전송 경로 기 하 도형 및 경로 사용에 대 한 상세한 일정을 요청 합니다 [전송 여정 API 가져오기](https://aka.ms/ https://azure.microsoft.com/services/azure-maps/)합니다.


## <a name="prerequisites"></a>필수 조건

Azure Maps 공용 전송 Api에 대 한 모든 호출을 하려면 맵 계정 및 키 필요 합니다. 계정 만들기 및 키 검색에 대한 자세한 내용은 [Azure Maps 계정 및 키를 관리하는 방법](how-to-manage-account-keys.md)을 참조하세요.

이 문서에서는 [우체부 앱](https://www.getpostman.com/apps)을 사용하여 REST 호출을 빌드합니다. 선호하는 모든 API 개발 환경을 사용할 수 있습니다.


## <a name="get-a-metro-area-id"></a>Metro 영역 ID를 가져오려면

요청 해야 하는 특정 대도시 지역에 대 한 정보를 전송 하기 위해는 `metroId` 에 대 한 전송 중 데이터를 요청 하려면 영역에 대 한 합니다. [Metro 영역 api](https://aka.ms/AzureMapsMobilityMetro) Azure Maps Mobility Service를 사용할 수 있는 metro 영역을 요청할 수 있습니다. 응답와 같은 세부 정보를 포함할 `metroId`, `metroName` 및 GeoJSON 형식으로 metro 영역 geometry의 표현입니다.

면적을 얻을 Metro 시애틀 Tacoma metro 영역 ID에 대 한 요청을 만들어 보겠습니다 Metro 영역에 대 한 ID를 요청 하려면 다음 단계를 수행 합니다.

1. 요청을 저장할 컬렉션을 만듭니다. Postman 앱 선택 **새로 만들기**합니다. 에 **새로 만들기** 창에서 **컬렉션**합니다. 컬렉션 이름을 지정 하 고 선택 합니다 **만들기** 단추입니다.

2. 요청을 만들려면 선택 **새로 만들기** 다시 합니다. 에 **새로 만들기** 창에서 **요청**합니다. 입력 한 **요청 이름** 요청에 대 한 요청을 저장 하 고 선택한 위치와 이전 단계에서 만든 컬렉션을 선택 합니다 **저장**합니다.
    
    ![Postman에서 요청을 만듭니다](./media/how-to-request-transit-data/postman-new.png)

3. 작성기 탭에서 GET HTTP 메서드를 선택 하 고 GET 요청을 만들려면 다음 URL을 입력 합니다.

    ```HTTP
    https://atlas.microsoft.com/mobility/metroArea/id/json?subscription-key={subscription-key}&api-version=1.0&query=47.63096,-122.126
    ```

4. 요청이 성공 후 다음과 같은 응답이 표시 됩니다.

    ```JSON
    {
        "results": [
            {
                "metroId": "522",
                "metroName": "Seattle–Tacoma–Bellevue, WA",
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
                            [
                                -121.95725,
                                47.18314
                            ],
                            ...,
                            ...,
                            ...,
                            ...,
                            [
                                -122.18711,
                                47.15571
                            ],
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
                },
                "viewport": {
                    "topLeftPoint": {
                        "latitude": 48.5853,
                        "longitude": -124.80934
                    },
                    "btmRightPoint": {
                        "latitude": 46.90534,
                        "longitude": -121.55032
                    }
                }
            }
        ]
    }
    ```

5. 복사를 `metroId`, 나중에 사용할 수 있습니다.

## <a name="request-nearby-transit-stops"></a>전송 중지 주변 요청

Azure Maps [주변 전송 가져올](https://aka.ms/AzureMapsMobilityNearbyTransit) 공용 전송 중지 하 고 전송 개체 세부 정보를 반환 하는 지정된 된 위치 주변 자전거를 공유 하는 예를 들어, 서비스 전송 개체를 검색할 수 있습니다. 다음 만들겠습니다 요청 300 미터 반경 내 주변 공용 전송 중지에 대 한 검색 서비스에 관련 위치를 지정 합니다. 요청에 포함 해야 합니다 `metroId` 앞에서 검색 합니다.

요청을 수행 하는 [주변 전송 가져올](https://aka.ms/AzureMapsMobilityNearbyTransit), 아래 단계에 따라:

1. Postman에서 클릭 **새 요청** | **GET 요청** 하 고 이름을 **주변 가져오기 중지**합니다.

2. 작성기 탭에서 선택 합니다 **가져올** HTTP 메서드를 API 끝점에 대 한 요청 URL을 입력 하 고 클릭 **보낼**합니다.

    ```HTTP
    https://atlas.microsoft.com/mobility/transit/nearby/json?subscription-key={subscription-key}&api-version=1.0&metroId=522&query=47.63096,-122.126&radius=300&objectType=stop
    ```

3. 요청이 성공 후 응답 구조는 아래 같이 표시 됩니다.

    ```JSON
    {
        "results": [
            {
                "id": "2060603",
                "type": "Stop",
                "objectDetails": {
                    "stopKey": "71300",
                    "stopName": "Ne 24th St & 162nd Ave Ne",
                    "mainTransitType": "BUS",
                    "mainAgencyId": 5872,
                    "mainAgencyName": "Metro Transit"
                },
                "position": {
                    "latitude": 47.631504,
                    "longitude": -122.125275
                },
                "viewport": {
                    "topLeftPoint": {
                        "latitude": 47.63241381296315,
                        "longitude": -122.12659096560266
                    },
                    "btmRightPoint": {
                        "latitude": 47.630594172088166,
                        "longitude": -122.12395908007201
                    }
                }
            },
            {
                "id": "2061020",
                "type": "Stop",
                "objectDetails": {
                    "stopKey": "68372",
                    "stopName": "Ne 24th St & 160th Ave Ne",
                    "mainTransitType": "BUS",
                    "mainAgencyId": 5872,
                    "mainAgencyName": "Metro Transit"
                },
                "position": {
                    "latitude": 47.631409,
                    "longitude": -122.127136
                },
                "viewport": {
                    "topLeftPoint": {
                        "latitude": 47.632318791818726,
                        "longitude": -122.12845199584025
                    },
                    "btmRightPoint": {
                        "latitude": 47.63049919323126,
                        "longitude": -122.12582004983427
                    }
                }
            },
            {
                "id": "2060604",
                "type": "Stop",
                "objectDetails": {
                    "stopKey": "71310",
                    "stopName": "Ne 24th St & 160th Ave Ne",
                    "mainTransitType": "BUS",
                    "mainAgencyId": 5872,
                    "mainAgencyName": "Metro Transit"
                },
                "position": {
                    "latitude": 47.631565,
                    "longitude": -122.127808
                },
                "viewport": {
                    "topLeftPoint": {
                        "latitude": 47.632474784183636,
                        "longitude": -122.12912401149087
                    },
                    "btmRightPoint": {
                        "latitude": 47.630655200865796,
                        "longitude": -122.12649203418405
                    }
                }
            }
        ]
    }    
    ```

응답 구조를 신중 하 게 관찰 하는 경우는 매개 변수를 포함 각 전송 개체와 같은 볼 수 있습니다 `id`, `type`를 `stopName`를 `mainTransitType`, `mainAgencyName` 개체의 위치 (좌표)입니다.

이해 하기 위해 사용 하 여는 `id` 다음 섹션에서이 경로 대 한 원본으로 bus 중지 중 하나입니다.  


## <a name="request-a-transit-route"></a>요청 전송 경로

Azure Maps [전송 경로 API 가져오기](https://aka.ms/AzureMapsMobilityTransitRoute) 여행 계획 원본 및 대상 간의 가능한 경로 가장 옵션을 반환 하는 수 있습니다. 서비스는 다양 한 여행 모드, 도보, 자전거, 및 공용 전송 등을 제공 합니다. 다음 시애틀에서 공간 니 들을 가장 가까운 bus 중지에서 경로 검색 됩니다.

### <a name="get-location-coordinates-for-destination"></a>대상에 대 한 위치 좌표를 가져오기

위치를 가져오기 위해 좌표 공간 니 들에 대 한 Azure Maps를 사용 하도록 [유사 항목 검색 서비스](https://docs.microsoft.com/rest/api/maps/search/getsearchfuzzy)합니다.

유사 항목 검색 서비스를 요청 하려면 아래 단계를 따르세요.

1. Postman에서 클릭 **새 요청** | **GET 요청** 하 고 이름을 **위치 좌표를 가져오려면**합니다.

2.  작성기 탭에서 선택 합니다 **가져올** HTTP 메서드를 다음 요청 URL을 입력 하 고 클릭 **보낼**합니다.
 
    ```HTTP
    https://atlas.microsoft.com/search/fuzzy/json?subscription-key={subscription-key}&api-version=1.0&query=space needle
    ```
    
3. 신중 하 게 응답에 보면 공간 니 들에 대 한 결과에 여러 위치를 포함 하 고 각각에서 위치 좌표 정보도 포함 **위치**합니다. 복사 합니다 `lat` 고 `lon` 첫 번째 결과 대 한 위치에서.
    
   ```JSON
   {
        "summary": {
            "query": "space needle",
            "queryType": "NON_NEAR",
            "queryTime": 61,
            "numResults": 8,
            "offset": 0,
            "totalResults": 24,
            "fuzzyLevel": 1
        },
        "results": [
            {
                "type": "POI",
                "id": "US/POI/p0/8309323",
                "score": 4.674,
                "info": "search:ta:840539000511573-US",
                "poi": {
                    "name": "Space Needle",
                    "phone": "+(1)-(206)-9052100",
                    "url": "www.spaceneedle.com",
                    "categories": [
                        "important tourist attraction",
                        "monument"
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
                                    "name": "monument"
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
                    "country": "United States Of America",
                    "countryCodeISO3": "USA",
                    "freeformAddress": "400 Broad St, Seattle, WA 98109",
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
    

### <a name="request-route"></a>요청 경로

경로 요청을 하려면 아래 단계를 완료 합니다.

1. Postman에서 클릭 **새 요청** | **GET 요청** 하 고 이름을 **경로 가져오기 정보**합니다.

2. 작성기 탭에서 선택 합니다 **가져올** HTTP 메서드를 API 끝점에 대 한 요청 URL을 입력 하 고 클릭 **보낼**합니다.

    버스에 대 한 공용 전송 경로 지정 하 여 요청 됩니다 합니다 `modeType` 고 `transitType` 매개 변수입니다. 요청 URL에는 이전 섹션에서 검색 하는 위치를 포함 합니다. 로 `originType` 이제 **stopId** 와 `destionationType` 것은 **위치**합니다.

    참조를 [URI 매개 변수 목록을](https://aka.ms/AzureMapsMobilityTransitRoute#uri-parameters) 를 요청에 사용할 수는 [전송 경로 API 가져오기](https://aka.ms/AzureMapsMobilityTransitRoute)합니다. 
  
    ```HTTP
    https://atlas.microsoft.com/mobility/transit/route/json?subscription-key={subscription-key}&api-version=1.0&metroId=522&originType=stopId&origin=2060603&destionationType=position&destination=47.62039,-122.34928&modeType=publicTransit&transitType=bus
    ```

3. 요청이 성공 하면 응답 구조는 아래 같이 표시 됩니다.

    ```JSON
    {
        "results": [
            {
                "itineraryId": "302c38dd-6585-4fa1-bf78-44ebbc183e0c---2019040384C30774B4B94F178E7748644A476596:0---522",
                "departureTime": "2019-04-03T14:21:34-07:00",
                "arrivalTime": "2019-04-03T15:15:53-07:00",
                "travelTimeInSeconds": 3259,
                "numberOfLegs": 10,
                "legs": [
                    {
                        "legType": "Walk",
                        "legStartTime": "2019-04-03T14:21:34-07:00",
                        "legEndTime": "2019-04-03T14:28:19-07:00",
                        "caption": "156th Avenue Northeast",
                        "lengthInMeters": 497
                    },
                    {
                        "legType": "Wait",
                        "legStartTime": "2019-04-03T14:28:19-07:00",
                        "legEndTime": "2019-04-03T14:29:20-07:00",
                        "caption": "245"
                    },
                    {
                        "legType": "Bus",
                        "legStartTime": "2019-04-03T14:29:20-07:00",
                        "legEndTime": "2019-04-03T14:32:00-07:00",
                        "caption": "245",
                        "lengthInMeters": 1350
                    },
                    {
                        "legType": "Walk",
                        "legStartTime": "2019-04-03T14:32:01-07:00",
                        "legEndTime": "2019-04-03T14:33:07-07:00",
                        "caption": "156th Avenue Northeast",
                        "lengthInMeters": 63
                    },
                    {
                        "legType": "Wait",
                        "legStartTime": "2019-04-03T14:33:07-07:00",
                        "legEndTime": "2019-04-03T14:38:00-07:00",
                        "caption": "545"
                    },
                    {
                        "legType": "Bus",
                        "legStartTime": "2019-04-03T14:38:00-07:00",
                        "legEndTime": "2019-04-03T14:59:47-07:00",
                        "caption": "545",
                        "lengthInMeters": 16441
                    },
                    {
                        "legType": "Walk",
                        "legStartTime": "2019-04-03T14:59:48-07:00",
                        "legEndTime": "2019-04-03T15:03:53-07:00",
                        "caption": "Denny Way",
                        "lengthInMeters": 308
                    },
                    {
                        "legType": "Wait",
                        "legStartTime": "2019-04-03T15:03:53-07:00",
                        "legEndTime": "2019-04-03T15:07:26-07:00",
                        "caption": "8"
                    },
                    {
                        "legType": "Bus",
                        "legStartTime": "2019-04-03T15:07:26-07:00",
                        "legEndTime": "2019-04-03T15:12:12-07:00",
                        "caption": "8",
                        "lengthInMeters": 1057
                    },
                    {
                        "legType": "Walk",
                        "legStartTime": "2019-04-03T15:12:13-07:00",
                        "legEndTime": "2019-04-03T15:15:53-07:00",
                        "caption": "47.6205,-122.3493",
                        "lengthInMeters": 268
                    }
                ]
            },
            ...,
            {
                "itineraryId": "302c38dd-6585-4fa1-bf78-44ebbc183e0c---2019040384C30774B4B94F178E7748644A476596:2---522",
                "departureTime": "2019-04-03T14:21:34-07:00",
                "arrivalTime": "2019-04-03T15:19:18-07:00",
                "travelTimeInSeconds": 3464,
                "numberOfLegs": 10,
                "legs": [
                    {
                        "legType": "Walk",
                        "legStartTime": "2019-04-03T14:21:34-07:00",
                        "legEndTime": "2019-04-03T14:28:19-07:00",
                        "caption": "156th Avenue Northeast",
                        "lengthInMeters": 497
                    },
                    {
                        "legType": "Wait",
                        "legStartTime": "2019-04-03T14:28:19-07:00",
                        "legEndTime": "2019-04-03T14:29:20-07:00",
                        "caption": "245"
                    },
                    {
                        "legType": "Bus",
                        "legStartTime": "2019-04-03T14:29:20-07:00",
                        "legEndTime": "2019-04-03T14:32:00-07:00",
                        "caption": "245",
                        "lengthInMeters": 1350
                    },
                    {
                        "legType": "Walk",
                        "legStartTime": "2019-04-03T14:32:01-07:00",
                        "legEndTime": "2019-04-03T14:33:07-07:00",
                        "caption": "156th Avenue Northeast",
                        "lengthInMeters": 63
                    },
                    {
                        "legType": "Wait",
                        "legStartTime": "2019-04-03T14:33:07-07:00",
                        "legEndTime": "2019-04-03T14:38:00-07:00",
                        "caption": "545"
                    },
                    {
                        "legType": "Bus",
                        "legStartTime": "2019-04-03T14:38:00-07:00",
                        "legEndTime": "2019-04-03T15:01:00-07:00",
                        "caption": "545",
                        "lengthInMeters": 17400
                    },
                    {
                        "legType": "Walk",
                        "legStartTime": "2019-04-03T15:01:01-07:00",
                        "legEndTime": "2019-04-03T15:04:59-07:00",
                        "caption": "3rd Avenue",
                        "lengthInMeters": 269
                    },
                    {
                        "legType": "Wait",
                        "legStartTime": "2019-04-03T15:04:59-07:00",
                        "legEndTime": "2019-04-03T15:09:14-07:00",
                        "caption": "33"
                    },
                    {
                        "legType": "Bus",
                        "legStartTime": "2019-04-03T15:09:14-07:00",
                        "legEndTime": "2019-04-03T15:12:52-07:00",
                        "caption": "33,24",
                        "lengthInMeters": 947
                    },
                    {
                        "legType": "Walk",
                        "legStartTime": "2019-04-03T15:12:53-07:00",
                        "legEndTime": "2019-04-03T15:19:18-07:00",
                        "caption": "47.6205,-122.3493",
                        "lengthInMeters": 474
                    }
                ]
            }
        ]
    }
    ```

4. 신중 하 게 관찰 되는 경우 여러 개 **bus** 응답의 경로입니다. 각 경로 고유한 **여정 ID** 및 경로의 각 단계를 설명 하는 요약 합니다. 다음을 사용 하 여 가장 빠른 경로 대 한 세부 정보를 요청 합니다 우리는 `itineraryId` 응답에.

## <a name="request-fastest-route-itinerary"></a>가장 빠른 경로 여정 요청

Azure Maps [가져올 전송 여정](https://aka.ms/AzureMapsMobilityTransitItinerary) 서비스를 사용 하면 경로 사용 하 여 특정 경로 대 한 데이터를 요청할 수 있습니다 **여정 ID** 반환한 합니다 [전송 경로 API 가져오기](https://aka.ms/AzureMapsMobilityTransitRoute) 서비스입니다. 요청을 하려면 아래 단계를 완료 합니다.

1. Postman에서 클릭 **새 요청** | **GET 요청** 하 고 이름을 **가져오기 전송 정보**합니다.

2. 작성기 탭에서 선택 합니다 **가져올** HTTP 메서드를 API 끝점에 대 한 요청 URL을 입력 하 고 클릭 **보낼**합니다.

    설정 된 `detailType` 매개 변수를 **기 하 도형** 탐색 경로의 다리 bike 공용 전송 및 탐색을 설정 하 여 설정에 대 한 중지 정보는 응답에 포함 되도록 합니다.

    ```HTTP
    https://atlas.microsoft.com/mobility/transit/itinerary/json?api-version=1.0&subscription-key={subscription-key}&query={itineraryId}&detailType=geometry
    ```
    
3. 요청이 성공 하면 응답 구조는 아래 같이 표시 됩니다.

    ```JSON
    {
    "departureTime": "2019-05-01T11:16:56-07:00",
    "arrivalTime": "2019-05-01T12:23:45-07:00",
    "legs": [
                {
                    "legType": "Walk",
                    "legStartTime": "2019-05-01T11:16:56-07:00",
                    "legEndTime": "2019-05-01T11:24:06-07:00",
                    "walkingSteps": [
                        {
                            "direction": {
                                "relativeDirection": "left"
                            },
                            "streetName": "Northeast 24th Street"
                        },
                        {
                            "direction": {
                                "relativeDirection": "right"
                            },
                            "streetName": "156th Avenue Northeast"
                        }
                    ],
                    "walkingOrigin": {
                        "latitude": 47.63096,
                        "longitude": -122.126
                    },
                    "walkingDestination": {
                        "latitude": 47.631843,
                        "longitude": -122.132294
                    },
                    "geometry": {
                        "type": "LineString",
                        "coordinates": [
                            [
                                -122.126,
                                47.63096
                            ],
                            [
                                -122.12645,
                                47.63099
                            ],
                            ...,
                            ...,
                            [
                                -122.1323,
                                47.63184
                            ]
                        ]
                    }
                },
                {
                    "legType": "Wait",
                    "legStartTime": "2019-05-01T11:24:06-07:00",
                    "legEndTime": "2019-05-01T11:25:07-07:00",
                    "lineGroup": {
                        "lineGroupId": 666074,
                        "agencyId": 5872,
                        "agencyName": "Metro Transit",
                        "lineNumber": "245",
                        "caption1": "Kirkland Transit Center - Crossroads - Factoria",
                        "caption2": "245 Kirkland Transit Center - Crossroads - Factoria",
                        "color": "347E5D",
                        "transitType": "Bus"
                    },
                    "line": {
                        "lineId": 2756624,
                        "lineGroupId": 666074,
                        "direction": "forward",
                        "agencyId": 5872,
                        "lineNumber": "245",
                        "destination": "Kirkland Crossroads"
                    },
                    "stops": [
                        {
                            "stopId": 2061109,
                            "stopKey": "68788",
                            "stopName": "156th Ave NE & NE 24th St",
                            "position": {
                                "latitude": 47.631844,
                                "longitude": -122.132248
                            },
                            "mainTransitType": "Bus",
                            "mainAgencyId": 5872
                        },
                        {
                            "stopId": 2061059,
                            "stopKey": "68498",
                            "stopName": "156th Ave NE & Overlake Transit Center - Bay 8",
                            "position": {
                                "latitude": 47.643986,
                                "longitude": -122.132187
                            },
                            "mainTransitType": "Bus",
                            "mainAgencyId": 5872
                        }
                    ],
                    "waitOnVehicle": "false"
                },
                {
                    "legType": "Bus",
                    "legStartTime": "2019-05-01T11:25:07-07:00",
                    "legEndTime": "2019-05-01T11:30:00-07:00",
                    "lineGroup": {
                        "lineGroupId": 666074,
                        "agencyId": 5872,
                        "agencyName": "Metro Transit",
                        "lineNumber": "245",
                        "caption1": "Kirkland Transit Center - Crossroads - Factoria",
                        "caption2": "245 Kirkland Transit Center - Crossroads - Factoria",
                        "color": "347E5D",
                        "transitType": "Bus"
                    },
                    "line": {
                        "lineId": 2756624,
                        "lineGroupId": 666074,
                        "direction": "forward",
                        "agencyId": 5872,
                        "lineNumber": "245",
                        "destination": "Kirkland Crossroads"
                    },
                    "stops": [
                        {
                            "stopId": 2061109,
                            "stopKey": "68788",
                            "stopName": "156th Ave NE & NE 24th St",
                            "position": {
                                "latitude": 47.631844,
                                "longitude": -122.132248
                            },
                            "mainTransitType": "Bus",
                            "mainAgencyId": 5872
                        },
                        ...,
                        ...,
                        {
                            "stopId": 2061059,
                            "stopKey": "68498",
                            "stopName": "156th Ave NE & Overlake Transit Center - Bay 8",
                            "position": {
                                "latitude": 47.643986,
                                "longitude": -122.132187
                            },
                            "mainTransitType": "Bus",
                            "mainAgencyId": 5872
                        }
                    ],
                    "geometry": {
                        "type": "LineString",
                        "coordinates": [
                            [
                                -122.13235,
                                47.63184
                            ],
                            ...,
                            ...,
                            [
                                -122.1323,
                                47.64398
                            ]
                        ]
                    }
                },
                ...,
                ...,
                ...,
                {
                    "legType": "Tram",
                    "legStartTime": "2019-05-01T12:20:00-07:00",
                    "legEndTime": "2019-05-01T12:22:00-07:00",
                    "lineGroup": {
                        "lineGroupId": 4083239,
                        "agencyId": 1360766,
                        "agencyName": "Seattle Monorail",
                        "lineNumber": "Monorail",
                        "caption1": "Seattle Center - Westlake Center",
                        "caption2": "MONORAIL Seattle Center - Westlake Center",
                        "color": "00AEEF",
                        "transitType": "Tram"
                    },
                    "line": {
                        "lineId": 3769726,
                        "lineGroupId": 4083239,
                        "direction": "backward",
                        "agencyId": 1360766,
                        "lineNumber": "Monorail",
                        "destination": "Seattle Center"
                    },
                    "stops": [
                        {
                            "stopId": 32962125,
                            "stopName": "Westlake Station",
                            "position": {
                                "latitude": 47.611417,
                                "longitude": -122.337089
                            },
                            "mainTransitType": "Tram",
                            "mainAgencyId": 1360766
                        },
                        {
                            "stopId": 32962134,
                            "stopName": "Seattle Center",
                            "position": {
                                "latitude": 47.62123,
                                "longitude": -122.349746
                            },
                            "mainTransitType": "Tram",
                            "mainAgencyId": 1360766
                        }
                    ],
                    "geometry": {
                        "type": "LineString",
                        "coordinates": [
                            [
                                -122.3369,
                                47.61201
                            ],
                            ...,
                            ...,
                            [
                                -122.34973,
                                47.6212
                            ]
                        ]
                    }
                },
                {
                    "legType": "PathWayWalk",
                    "legStartTime": "2019-05-01T12:22:00-07:00",
                    "legEndTime": "2019-05-01T12:23:45-07:00"
                }
          ]
    }
    ```

## <a name="next-steps"></a>다음 단계

모바일 서비스를 사용 하 여 실시간 데이터를 요청 하는 방법에 알아봅니다.

> [!div class="nextstepaction"]
> [실시간 데이터를 요청 하는 방법](how-to-request-real-time-data.md)

Azure Maps 모바일 서비스 API 설명서 살펴보기

> [!div class="nextstepaction"]
> [Mobility Service API 설명서](https://aka.ms/AzureMapsMobilityService)

