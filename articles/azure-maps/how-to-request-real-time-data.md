---
title: 실시간 공용 전송 데이터 요청 | Microsoft Azure 맵
description: Microsoft Azure Maps 모바일 서비스를 사용 하 여 실시간 공용 전송 데이터를 요청 합니다.
author: anastasia-ms
ms.author: v-stharr
ms.date: 09/06/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: philmea
ms.custom: mvc
ms.openlocfilehash: 784d6a94562e8b5a709d65abdadef46ca71de395
ms.sourcegitcommit: 3543d3b4f6c6f496d22ea5f97d8cd2700ac9a481
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/20/2020
ms.locfileid: "86505961"
---
# <a name="request-real-time-public-transit-data-using-the-azure-maps-mobility-service"></a>Azure Maps 모바일 서비스를 사용 하 여 실시간 공용 전송 데이터 요청

이 문서에서는 Azure Maps [모바일 서비스](https://aka.ms/AzureMapsMobilityService) 를 사용 하 여 실시간 공용 전송 데이터를 요청 하는 방법을 보여 줍니다.

이 문서에서는 지정 된 중지 시간에 도착 하는 모든 줄에 대해 다음 실시간 도착 한를 요청 하는 방법을 알아봅니다.

## <a name="prerequisites"></a>필수 구성 요소

Azure Maps 공용 전송 Api에 대 한 호출을 수행 하려면 먼저 Azure Maps 계정 및 구독 키가 있어야 합니다. 자세한 내용은 [계정 만들기](quick-demo-map-app.md#create-an-azure-maps-account) 의 지침에 따라 Azure Maps 계정을 만드세요. [기본 키 가져오기](quick-demo-map-app.md#get-the-primary-key-for-your-account) 의 단계에 따라 계정에 대 한 기본 키를 가져옵니다. Azure Maps의 인증에 대한 자세한 내용은 [Azure Maps의 인증 관리](./how-to-manage-authentication.md)를 참조하세요.

이 문서에서는 [우체부 앱](https://www.getpostman.com/apps)을 사용하여 REST 호출을 빌드합니다. 선호하는 모든 API 개발 환경을 사용할 수 있습니다.

## <a name="request-real-time-arrivals-for-a-stop"></a>중지에 대 한 실시간 도착 한 요청

특정 공용 전송 중지의 실시간 도착 한 데이터를 요청 하려면 Azure Maps [모바일 서비스](https://aka.ms/AzureMapsMobilityService)의 [실시간 도착 한 API](https://aka.ms/AzureMapsMobilityRealTimeArrivals) 를 요청 해야 합니다. 요청을 완료 하려면 **metroID** 및 **stopid** 가 필요 합니다. 이러한 매개 변수를 요청 하는 방법에 대해 자세히 알아보려면 [공용 전송 경로를 요청](https://aka.ms/AMapsHowToGuidePublicTransitRouting)하는 방법에 대 한 가이드를 참조 하세요.

"시애틀 – Tacoma – Bellevue, WA" 영역에 대 한 metro ID 인 metro ID로 "522"을 사용 하겠습니다. "522---2060603"를 중지 ID로 사용 합니다 .이 버스 중지는 "Ne 24 일 St & 162nd 평균 Ne, Bellevue WA"에 있습니다. 다음 5 개의 실시간 도착 한 데이터를 요청 하려면이 중지의 모든 다음 라이브 도착 한에 대해 다음 단계를 완료 합니다.

1. Postman 앱을 열고 요청을 저장할 컬렉션을 만들어 보겠습니다. Postman 앱의 위쪽 근처에서 **새로 만들기**를 선택합니다. **새로 만들기** 창에서 **컬렉션**을 선택합니다.  컬렉션 이름을 지정하고, **만들기** 단추를 선택합니다.

2. 요청을 만들려면 **새로 만들기**를 다시 선택합니다. **새로 만들기** 창에서 **요청**을 선택합니다. 요청에 대한 **요청 이름**을 입력합니다. 이전 단계에서 만든 컬렉션을 요청을 저장할 위치로 선택 합니다. 그런 다음 **저장**을 선택합니다.

    ![Postman에서 요청 만들기](./media/how-to-request-transit-data/postman-new.png)

3. 작성기 탭에서 **get** HTTP 메서드를 선택 하 고 다음 URL을 입력 하 여 get 요청을 만듭니다. `{subscription-key}`을 Azure Maps 기본 키로 바꿉니다.

    ```HTTP
    https://atlas.microsoft.com/mobility/realtime/arrivals/json?subscription-key={subscription-key}&api-version=1.0&metroId=522&query=522---2060603&transitType=bus
    ```

4. 요청이 성공적으로 완료 되 면 다음 응답을 받게 됩니다.  ' ScheduleType ' 매개 변수는 예상 도착 시간이 실시간 또는 정적 데이터를 기반으로 하는지 여부를 정의 합니다.

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

모바일 서비스를 사용 하 여 전송 데이터를 요청 하는 방법을 알아봅니다.

> [!div class="nextstepaction"]
> [전송 데이터를 요청 하는 방법](how-to-request-transit-data.md)

Azure Maps 모바일 서비스 API 설명서를 탐색 합니다.

> [!div class="nextstepaction"]
> [모바일 서비스 API 설명서](https://aka.ms/AzureMapsMobilityService)
