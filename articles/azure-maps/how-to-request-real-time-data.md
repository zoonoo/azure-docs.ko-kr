---
title: 실시간 대중 교통 데이터 요청 | 마이크로소프트 Azure 지도
description: Microsoft Azure 지도 이동성 서비스를 사용하여 실시간 대중 교통 데이터를 요청합니다.
author: anastasia-ms
ms.author: v-stharr
ms.date: 09/06/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: philmea
ms.custom: mvc
ms.openlocfilehash: 4743fbe84f5d41b4659e13d96868d2f64a473e4b
ms.sourcegitcommit: 09a124d851fbbab7bc0b14efd6ef4e0275c7ee88
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2020
ms.locfileid: "82086080"
---
# <a name="request-real-time-public-transit-data-using-the-azure-maps-mobility-service"></a>Azure Maps 모빌리티 서비스를 사용하여 실시간 대중 교통 데이터 요청

이 문서에서는 Azure Maps [모빌리티 서비스를](https://aka.ms/AzureMapsMobilityService) 사용하여 실시간 대중 교통 데이터를 요청하는 방법을 보여 주며 있습니다.

이 기사에서는 지정된 정류장에 도착하는 모든 노선에 대해 다음 실시간 도착을 요청하는 방법을 배웁니다.

## <a name="prerequisites"></a>사전 요구 사항

먼저 Azure Maps 공용 이동 API를 호출하려면 Azure Maps 계정과 구독 키가 있어야 합니다. 자세한 내용은 계정 [만들기의](quick-demo-map-app.md#create-an-account-with-azure-maps) 지침에 따라 Azure Maps 계정을 만듭니다. 기본 [키를 가져오는](quick-demo-map-app.md#get-the-primary-key-for-your-account) 단계를 수행하여 계정의 기본 키를 가져옵니다. Azure Maps의 인증에 대한 자세한 내용은 [Azure Maps의 인증 관리](./how-to-manage-authentication.md)를 참조하세요.

이 문서에서는 [우체부 앱](https://www.getpostman.com/apps)을 사용하여 REST 호출을 빌드합니다. 선호하는 모든 API 개발 환경을 사용할 수 있습니다.

## <a name="request-real-time-arrivals-for-a-stop"></a>실시간 도착 요청

특정 대중 교통 정류장의 실시간 도착 데이터를 요청하려면 Azure Maps [모빌리티 서비스의](https://aka.ms/AzureMapsMobilityService) [실시간 도착 API에](https://aka.ms/AzureMapsMobilityRealTimeArrivals) 요청해야 합니다. 요청을 완료하려면 **metroID** 및 **stopID가** 필요합니다. 이러한 매개 변수를 요청하는 방법에 대한 자세한 내용은 [대중 교통 경로를 요청하는](https://aka.ms/AMapsHowToGuidePublicTransitRouting)방법에 대한 가이드를 참조하십시오.

"시애틀-타코마-벨뷰, 워싱턴 주" 지역의 메트로 ID인 메트로 ID로 "522"를 사용해 보겠습니다. "522---2060603"을 정류장 ID로 사용하여이 버스 정류장은 "Ne 24th St & 162 th Ave Ne, Bellevue WA"에 있습니다. 다음 5개의 실시간 도착 데이터를 요청하려면 이 정류장의 모든 다음 실시간 도착에 대해 다음 단계를 완료하십시오.

1. Postman 앱을 열고 요청을 저장할 컬렉션을 만들어 보겠습니다. 우체부 앱 의 상단 근처에서 **새**를 선택합니다. 새 **만들기** 창에서 **컬렉션**을 선택합니다.  컬렉션의 이름을 지정하고 **만들기** 단추를 선택합니다.

2. 요청을 만들려면 **새로 만들기를** 다시 선택합니다. 새 **만들기** 창에서 **요청을**선택합니다. 요청에 대한 **요청 이름을** 입력합니다. 요청을 저장할 위치로 이전 단계에서 만든 컬렉션을 선택합니다. 그런 다음 **저장을**선택합니다.

    ![우체부에서 요청 만들기](./media/how-to-request-transit-data/postman-new.png)

3. 빌더 탭에서 **GET** HTTP 메서드를 선택하고 다음 URL을 입력하여 GET 요청을 만듭니다. 을 `{subscription-key}`Azure Maps 기본 키로 바꿉습니다.

    ```HTTP
    https://atlas.microsoft.com/mobility/realtime/arrivals/json?subscription-key={subscription-key}&api-version=1.0&metroId=522&query=522---2060603&transitType=bus
    ```

4. 요청이 성공하면 다음 응답을 받게 됩니다.  매개 변수 'scheduleType'은 예상 도착 시간이 실시간 또는 정적 데이터를 기반으로 하는지 여부를 정의합니다.

    ```JSON
    {
        "results": [
            {
                "arrivalMinutes": 8,
                "scheduleType": "realTime",
                "patternId": "522---4143196",
                "line": {
                    "lineId": "522---3760143",
                    "lineGroupId": "522---666077",
                    "direction": "backward",
                    "agencyId": "522---5872",
                    "agencyName": "Metro Transit",
                    "lineNumber": "249",
                    "lineDestination": "South Bellevue S Kirkland P&R",
                    "transitType": "Bus"
                },
                "stop": {
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
                }
            },
            {
                "arrivalMinutes": 25,
                "scheduleType": "realTime",
                "patternId": "522---3510227",
                "line": {
                    "lineId": "522---2756599",
                    "lineGroupId": "522---666063",
                    "direction": "forward",
                    "agencyId": "522---5872",
                    "agencyName": "Metro Transit",
                    "lineNumber": "226",
                    "lineDestination": "Bellevue Transit Center Crossroads",
                    "transitType": "Bus"
                },
                "stop": {
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
                }
            }
        ]
    }
    ```

## <a name="next-steps"></a>다음 단계

모빌리티 서비스를 사용하여 대중교통 데이터를 요청하는 방법에 대해 알아보십시오.

> [!div class="nextstepaction"]
> [대중 교통 데이터를 요청하는 방법](how-to-request-transit-data.md)

Azure Maps 모빌리티 서비스 API 설명서를 살펴보십시오.

> [!div class="nextstepaction"]
> [모빌리티 서비스 API 문서](https://aka.ms/AzureMapsMobilityService)
