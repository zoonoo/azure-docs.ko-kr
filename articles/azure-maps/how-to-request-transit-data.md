---
title: Microsoft Azure Maps 모바일 서비스를 사용 하 여 전송 데이터 요청
description: Azure Maps 모바일 서비스를 사용 하 여 metro 영역 Id, 전송 중지, 경로 및 경로 여정을 같은 공용 전송 데이터를 요청 하는 방법을 알아봅니다.
author: anastasia-ms
ms.author: v-stharr
ms.date: 07/22/2020
ms.topic: how-to
ms.service: azure-maps
services: azure-maps
manager: philmea
ms.custom: mvc
ms.openlocfilehash: 3ded5f6186d8ccdae0f42ecb9942c70e09f2dd9f
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/25/2020
ms.locfileid: "91310886"
---
# <a name="request-public-transit-data-using-the-azure-maps-mobility-service"></a>Azure Maps 모바일 서비스를 사용 하 여 공용 전송 데이터 요청

이 문서에서는 Azure Maps [모바일 서비스](https://aka.ms/AzureMapsMobilityService) 를 사용 하 여 공용 전송 데이터를 요청 하는 방법을 보여 줍니다. 전송 데이터에는 전송 중지, 경로 정보 및 이동 시간 예측이 포함 됩니다.

이 문서에서는 다음 방법에 대해 알아봅니다.

* [Metro 영역 다운로드 API](https://aka.ms/AzureMapsMobilityMetro) 를 사용 하 여 METRO 영역 ID 가져오기
* 가까운 전송 서비스 [가져오기](https://aka.ms/AzureMapsMobilityNearbyTransit) 를 사용 하 여 근접 한 전송 요청을 중지 합니다.
* 공개 전송을 사용 하 여 경로를 계획 하는 [Get 전송 경로 API](https://aka.ms/AzureMapsMobilityTransitRoute) 를 쿼리 합니다.
* [전송 여행 일정 가져오기 API](https://aka.ms/https://azure.microsoft.com/services/azure-maps/)를 사용 하 여 경로에 대 한 전송 경로 기 하 도형 및 자세한 일정을 요청 합니다.

## <a name="prerequisites"></a>사전 요구 사항

1. [Azure Maps 계정을 만듭니다](quick-demo-map-app.md#create-an-azure-maps-account).
2. 기본 키 또는 구독 키라고도 하는 [기본 구독 키를 가져옵니다](quick-demo-map-app.md#get-the-primary-key-for-your-account). Azure Maps의 인증에 대한 자세한 내용은 [Azure Maps의 인증 관리](./how-to-manage-authentication.md)를 참조하세요.

이 자습서에서는 [Postman](https://www.postman.com/) 애플리케이션을 사용하지만 다른 API 개발 환경을 선택할 수도 있습니다.

## <a name="get-a-metro-area-id"></a>Metro 영역 ID 가져오기

특정 대도시 영역에 대해 전송 기관 및 지원 되는 전송 형식에 대 한 세부 정보를 요청 하려면 `metroId` 해당 영역의가 필요 합니다. [Metro 영역 가져오기 API](https://aka.ms/AzureMapsMobilityMetro) 를 사용 하면 Azure Maps 모바일 서비스를 사용할 수 있는 metro 영역을 요청할 수 있습니다. 응답에는 `metroId` `metroName` GeoJSON 형식의 metro 영역 기 하 도형에 대 한,, 및 표현과 같은 세부 정보가 포함 됩니다.

시애틀-Tacoma metro 영역 ID에 대 한 Metro 영역을 가져오도록 요청 하겠습니다. Metro 영역에 대 한 ID를 요청 하려면 다음 단계를 완료 합니다.

1. Postman 앱을 열고 요청을 저장할 컬렉션을 만들어 보겠습니다. Postman 앱의 위쪽 근처에서 **새로 만들기**를 선택합니다. **새로 만들기** 창에서 **컬렉션**을 선택합니다.  컬렉션 이름을 지정하고, **만들기** 단추를 선택합니다.

2. 요청을 만들려면 **새로 만들기**를 다시 선택합니다. **새로 만들기** 창에서 **요청**을 선택합니다. 요청에 대한 **요청 이름**을 입력합니다. 이전 단계에서 만든 컬렉션을 요청을 저장할 위치로 선택 합니다. 그런 다음 **저장**을 선택합니다.
  
    ![Postman에서 요청 만들기](./media/how-to-request-transit-data/postman-new.png)

3. 작성기 탭에서 **get** HTTP 메서드를 선택 하 고 다음 URL을 입력 하 여 get 요청을 만듭니다. `{subscription-key}`을 Azure Maps 기본 키로 바꿉니다.

    ```HTTP
    https://atlas.microsoft.com/mobility/metroArea/id/json?subscription-key={subscription-key}&api-version=1.0&query=47.63096,-122.126
    ```

4. 요청이 성공적으로 완료 되 면 다음 응답을 받게 됩니다.

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

## <a name="request-nearby-transit-stops"></a>가까운 전송 중지 요청

[주변 전송](https://aka.ms/AzureMapsMobilityNearbyTransit) 서비스를 Azure Maps 하 여 전송 개체를 검색할 수 있습니다. API는 지정 된 위치에 대 한 공개 전송 정지 및 공유 bikes와 같은 전송 개체 세부 정보를 반환 합니다. 다음으로 서비스에 대 한 요청을 수행 하 여 지정 된 위치 주위의 300-미터 반경 내에서 가까운 공용 전송 중지를 검색 합니다.

[주변 전송](https://aka.ms/AzureMapsMobilityNearbyTransit)에 대 한 요청을 수행 하려면 다음 단계를 수행 합니다.

1. Postman에서 **새 요청**  |  **가져오기 요청** 을 클릭 하 고 이름이 **근처에서 중지**되도록 합니다.

2. 작성기 탭에서 **GET** HTTP 메서드를 선택 하 고 API 끝점에 대 한 다음 요청 URL을 입력 하 고 **보내기**를 클릭 합니다.

    ```HTTP
    https://atlas.microsoft.com/mobility/transit/nearby/json?subscription-key={subscription-key}&api-version=1.0&query=47.63096,-122.126&radius=300&objectType=stop
    ```

3. 요청이 성공적으로 완료 되 면 응답 구조는 아래와 같이 표시 됩니다.

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

응답 구조를 신중 하 게 관찰 하면 각 전송 개체에 대 한 매개 변수가 포함 된 것을 알 수 있습니다. 각 전송 개체에는,, `id` `type` ,, `stopName` `mainTransitType` `mainAgencyName` , 개체의 위치 (좌표)와 같은 매개 변수가 있습니다.

학습을 위해 `id` 다음 섹션의 경로에 대 한 버스 중지를 원본으로 사용 합니다.  

## <a name="request-a-transit-route"></a>전송 경로 요청

Azure Maps [전송 경로 가져오기 API](https://aka.ms/AzureMapsMobilityTransitRoute) 는 여행 계획을 허용 합니다. 원본에서 대상으로 가능한 가장 적합 한 경로 옵션을 반환 합니다. 이 서비스는 탐색, biking 및 공개 전송을 비롯 한 다양 한 유형의 여행 모드를 제공 합니다. 다음으로 가장 가까운 버스 정지의 경로를 시애틀의 공간 니 들 타워로 검색 합니다.

### <a name="get-location-coordinates-for-destination"></a>대상에 대 한 위치 좌표 가져오기

우주 니 들 타워의 위치 좌표를 가져오기 위해 Azure Maps [유사 항목 검색 서비스](https://docs.microsoft.com/rest/api/maps/search/getsearchfuzzy)를 사용 합니다.

유사 항목 검색 서비스에 대 한 요청을 만들려면 다음 단계를 수행 합니다.

1. Postman에서 **새 요청**  |  **get 요청** 을 클릭 하 고 이름을 **location 좌표**로 이름을 가져옵니다.

2. 작성기 탭에서 **GET** HTTP 메서드를 선택 하 고 다음 요청 URL을 입력 한 후 **보내기**를 클릭 합니다.

    ```HTTP
    https://atlas.microsoft.com/search/fuzzy/json?subscription-key={subscription-key}&api-version=1.0&query=space needle
    ```

3. 응답을 신중 하 게 살펴보면 공간 니 들 검색 결과에 여러 위치가 포함 되어 있습니다. 각 결과 **는 위치 아래의**위치 좌표를 포함 합니다. `lat` `lon` 첫 번째 결과의 **위치** 에서 및를 복사 합니다.

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

### <a name="request-route"></a>요청 경로

경로 요청을 만들려면 다음 단계를 완료 합니다.

1. Postman에서 **새 요청**  |  **가져오기 요청** 을 클릭 하 고 이름으로 **경로 정보를 가져옵니다**.

2. 작성기 탭에서 **GET** HTTP 메서드를 선택 하 고 API 끝점에 대 한 다음 요청 URL을 입력 하 고 **보내기**를 클릭 합니다.

    및 매개 변수를 지정 하 여 버스에 대 한 공용 전송 경로를 요청 `modeType` `transitType` 합니다. 요청 URL에는 이전 섹션에서 검색 된 위치가 포함 되어 있습니다. 에는 `originType` 이제 **stopid**가 있습니다. 의 경우 `destionationType` **위치가**있습니다.

    [전송 경로 가져오기 API](https://aka.ms/AzureMapsMobilityTransitRoute)에 대 한 요청에서 사용할 수 있는 [URI 매개 변수 목록을](https://aka.ms/AzureMapsMobilityTransitRoute#uri-parameters) 참조 하세요.
  
    ```HTTP
    https://atlas.microsoft.com/mobility/transit/route/json?subscription-key={subscription-key}&api-version=1.0&originType=stopId&origin=522---2060603&destionationType=position&destination=47.62039,-122.34928&modeType=publicTransit&transitType=bus
    ```

3. 요청이 성공적으로 완료 되 면 응답 구조는 아래와 같습니다.

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
                ],
                "itineraryFare": {
                    "price": {
                        "amount": 550,
                        "currencyCode": "USD"
                    },
                    "tickets": [
                        {
                            "amount": 275,
                            "currencyCode": "USD"
                        },
                        {
                            "amount": 275,
                            "currencyCode": "USD"
                        }
                    ]
                }
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
                ],
                "itineraryFare": {
                    "price": {
                        "amount": 550,
                        "currencyCode": "USD"
                    },
                    "tickets": [
                        {
                            "amount": 275,
                            "currencyCode": "USD"
                        },
                        {
                            "amount": 275,
                            "currencyCode": "USD"
                        }
                    ]
                }
            }
        ]
    }
    ```

4. 신중 하 게 관찰 하면 응답에 여러 개의 **버스** 경로가 있습니다. 각 경로에는 고유한 일정 **ID**, 경로의 각 다리를 설명 하는 요약 및 `itineraryFare` 버스 티켓에 대 한 항목별 및 총 가격을 모두 제공 하는가 있습니다. 경로 레그는 두 중지 waypoints 간의 경로 부분입니다. 그런 다음 응답에서를 사용 하 여 가장 빠른 경로에 대 한 세부 정보를 요청 `itineraryId` 합니다.

## <a name="request-fastest-route-itinerary"></a>가장 빠른 경로 요청 일정표

Azure Maps [전송 여행 서비스 가져오기](https://aka.ms/AzureMapsMobilityTransitItinerary) 서비스를 사용 하면 [전송 경로 가져오기 API](https://aka.ms/AzureMapsMobilityTransitRoute) 서비스에서 반환 된 경로의 일정 **ID** 를 사용 하 여 특정 경로에 대 한 데이터를 요청할 수 있습니다. 요청을 수행 하려면 다음 단계를 완료 합니다.

1. Postman에서 **새 요청**  |  **가져오기 요청** 을 클릭 하 고 이름으로 **전송 정보를 가져옵니다**.

2. 작성기 탭에서 **GET** HTTP 메서드를 선택 합니다. API 끝점에 대 한 다음 요청 URL을 입력 하 고 **보내기**를 클릭 합니다.

    `detailType`이 매개 변수를 **geometry** 로 설정 하 여 응답에 공용 전송에 대 한 중지 정보를 포함 하 고 경로를 탐색 하 고 자전거를 탐색할 수 있습니다.

    ```HTTP
    https://atlas.microsoft.com/mobility/transit/itinerary/json?api-version=1.0&subscription-key={subscription-key}&query={itineraryId}&detailType=geometry
    ```

3. 요청이 성공적으로 완료 되 면 응답 구조는 아래와 같이 표시 됩니다.  JSON 응답을 관찰 하는 경우 각 버스 레그에 요소가 포함 되어 있음을 알 수 있습니다 `legfare` . 요소에는 `legfare` 개별적으로 구매한 각 버스 경로에 대 한 센트의 비용이 포함 됩니다. 응답의 끝에 `itineraryFare` 전체 경로의 센트에 대 한 비용을 포함 하는 요소가 표시 됩니다. 이 예제에서는 각각 4 개의 버스 경로가 있습니다 `$2.75` . 그러나 전체 경로에 대 한 단일 티켓을 구매 하는 경우 비용은 `$5.50` 입니다.

    ```JSON
   {
        "departureTime": "2020-07-22T19:54:47Z",
        "arrivalTime": "2020-07-22T21:12:21Z",
        "legs": [
            {
                "legType": "Wait",
                "legStartTime": "2020-07-22T19:54:47Z",
                "legEndTime": "2020-07-22T19:54:47Z",
                "lineGroup": {
                    "lineGroupId": "522---666063",
                    "agencyId": "522---5872",
                    "agencyName": "Metro Transit",
                    "lineNumber": "226",
                    "caption1": "Eastgate P&R-Crossroads-Overlake-Bellevue TC",
                    "caption2": "226 Eastgate P&R-Crossroads-Overlake-Bellevue TC",
                    "color": "347E5D",
                    "transitType": "Bus"
                },
                "line": {
                    "lineId": "522---2756599",
                    "lineGroupId": "522---666063",
                    "direction": "forward",
                    "agencyId": "522---5872",
                    "lineNumber": "226",
                    "lineDestination": "Bellevue Transit Center Crossroads"
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
                        "mainAgencyId": "522---5872",
                        "mainAgencyName": "Metro Transit"
                    },
                    {
                        "stopId": "522---2062263",
                        "stopKey": "85630",
                        "stopName": "Bellevue Tc",
                        "stopCode": "85630",
                        "position": {
                            "latitude": 47.615591,
                            "longitude": -122.196491
                        },
                        "mainTransitType": "Bus",
                        "mainAgencyId": "522---5872",
                        "mainAgencyName": "Metro Transit"
                    }
                ],
                "waitOnVehicle": false
            },
            {
                "legType": "Bus",
                "legStartTime": "2020-07-22T19:54:47Z",
                "legEndTime": "2020-07-22T20:15:00Z",
                "lineGroup": {
                    "lineGroupId": "522---666063",
                    "agencyId": "522---5872",
                    "agencyName": "Metro Transit",
                    "lineNumber": "226",
                    "caption1": "Eastgate P&R-Crossroads-Overlake-Bellevue TC",
                    "caption2": "226 Eastgate P&R-Crossroads-Overlake-Bellevue TC",
                    "color": "347E5D",
                    "transitType": "Bus"
                },
                "line": {
                    "lineId": "522---2756599",
                    "lineGroupId": "522---666063",
                    "direction": "forward",
                    "agencyId": "522---5872",
                    "lineNumber": "226",
                    "lineDestination": "Bellevue Transit Center Crossroads"
                },
                "stops": [
                   ...
                ],
                "geometry": {
                    "type": "LineString",
                    "coordinates": [
                       ...
                    ]
                },
                "legFare": {
                    "fares": [
                        {
                            "price": {
                                "amount": 275,
                                "currencyCode": "USD"
                            },
                            "usage": "pay"
                        }
                    ]
                }
            },
            ...,
            ...,
            {
                "legType": "Bus",
                "legStartTime": "2020-07-22T20:20:00Z",
                "legEndTime": "2020-07-22T20:28:00Z",
                "lineGroup": {
                    "lineGroupId": "522---666071",
                    "agencyId": "522---5872",
                    "agencyName": "Metro Transit",
                    "lineNumber": "241",
                    "caption1": "Eastgate P&R - Bellevue Transit Center",
                    "caption2": "241 Eastgate P&R - Bellevue Transit Center",
                    "color": "347E5D",
                    "transitType": "Bus"
                },
                "line": {
                    "lineId": "522---2756619",
                    "lineGroupId": "522---666071",
                    "direction": "backward",
                    "agencyId": "522---5872",
                    "lineNumber": "241",
                    "lineDestination": "Eastgate P&R Factoria"
                },
                "stops": [
                ...
                ],
                "geometry": {
                    "type": "LineString",
                    "coordinates": [
                   ...
                    ]
                },
                "legFare": {
                    "fares": [
                        {
                            "price": {
                                "amount": 275,
                                "currencyCode": "USD"
                            },
                            "usage": "transfer"
                        }
                    ]
                }
            },
            ...,
            {
                "legType": "Bus",
                "legStartTime": "2020-07-22T20:31:00Z",
                "legEndTime": "2020-07-22T20:54:13Z",
                "lineGroup": {
                    "lineGroupId": "522---312636",
                    "agencyId": "522---854535",
                    "agencyName": "Sound Transit",
                    "lineNumber": "550",
                    "caption1": "Bellevue - Seattle",
                    "caption2": "550 Bellevue - Seattle",
                    "color": "00008B",
                    "transitType": "Bus"
                },
                "line": {
                    "lineId": "522---962201",
                    "lineGroupId": "522---312636",
                    "direction": "backward",
                    "agencyId": "522---854535",
                    "lineNumber": "550",
                    "lineDestination": "Seattle"
                },
                "stops": [
                   ...
                ],
                "geometry": {
                    "type": "LineString",
                    "coordinates": [
                 ...
                    ]
                },
                "legFare": {
                    "fares": [
                        {
                            "price": {
                                "amount": 275,
                                "currencyCode": "USD"
                            },
                            "usage": "pay"
                        }
                    ]
                }
            },
            ...,
            ...,
            {
                "legType": "Bus",
                "legStartTime": "2020-07-22T20:57:00Z",
                "legEndTime": "2020-07-22T21:06:00Z",
                "lineGroup": {
                    "lineGroupId": "522---480518",
                    "agencyId": "522---5872",
                    "agencyName": "Metro Transit",
                    "lineNumber": "13",
                    "caption1": "Seattle Pacific - Downtown Seattle",
                    "caption2": "13 Seattle Pacific - Downtown Seattle",
                    "color": "347E5D",
                    "transitType": "Bus"
                },
                "line": {
                    "lineId": "522---1744932",
                    "lineGroupId": "522---480518",
                    "direction": "forward",
                    "agencyId": "522---5872",
                    "lineNumber": "13",
                    "lineDestination": "Seattle Pacific University Seattle Center W"
                },
                "stops": [
                   ...
                ],
                "geometry": {
                    "type": "LineString",
                    "coordinates": [
                      ...
                    ]
                },
                "legFare": {
                    "fares": [
                        {
                            "price": {
                                "amount": 275,
                                "currencyCode": "USD"
                            },
                            "usage": "transfer"
                        }
                    ]
                }
            },
            ...,
        ],
        "itineraryFare": {
            "price": {
                "amount": 550,
                "currencyCode": "USD"
            },
            "tickets": [
                {
                    "amount": 275,
                    "currencyCode": "USD"
                },
                {
                    "amount": 275,
                    "currencyCode": "USD"
                }
            ]
        }
    }

    ```

## <a name="next-steps"></a>다음 단계

모바일 서비스를 사용 하 여 실시간 데이터를 요청 하는 방법을 알아봅니다.

> [!div class="nextstepaction"]
> [실시간 데이터를 요청 하는 방법](how-to-request-real-time-data.md)

Azure Maps 모바일 서비스 API 설명서 살펴보기

> [!div class="nextstepaction"]
> [모바일 서비스 설명서](https://aka.ms/AzureMapsMobilityService)
