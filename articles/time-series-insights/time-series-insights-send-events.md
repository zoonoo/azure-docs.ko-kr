---
title: 환경에 이벤트 보내기 - Azure 타임시리즈 인사이트 | 마이크로 소프트 문서
description: 이벤트 허브를 구성하고, 샘플 응용 프로그램을 실행하고, Azure Time Series Insights 환경으로 이벤트를 보내는 방법을 알아봅니다.
ms.service: time-series-insights
services: time-series-insights
author: deepakpalled
ms.author: dpalled
manager: cshankar
ms.devlang: csharp
ms.workload: big-data
ms.topic: conceptual
ms.date: 02/11/2020
ms.custom: seodec18
ms.openlocfilehash: c3c7f59ecb3a06d80012917e2da4425a899859d7
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79254249"
---
# <a name="send-events-to-a-time-series-insights-environment-by-using-an-event-hub"></a>이벤트 허브를 사용하여 Time Series Insights 환경으로 이벤트 보내기

이 문서에서는 Azure 이벤트 허브에서 이벤트 허브를 만들고 구성하는 방법을 설명합니다. 또한 이벤트 허브에서 Azure Time Series Insights로 이벤트를 푸시하기 위해 샘플 응용 프로그램을 실행하는 방법에 대해서도 설명합니다. JSON 형식의 이벤트가 있는 기존 이벤트 허브가 있는 경우 이 자습서를 건너뛰고 [Azure Time Series Insights에서](./time-series-insights-update-create-environment.md)환경을 봅니다.

## <a name="configure-an-event-hub"></a>이벤트 허브 구성

1. 이벤트 허브를 만드는 방법에 대해 알아보려면 [이벤트 허브 설명서를](https://docs.microsoft.com/azure/event-hubs/)참조하십시오.
1. 검색 상자에서 **Event Hubs**를 검색합니다. 반환된 목록에서 **Event Hubs**를 선택합니다.
1. 이벤트 허브를 선택합니다.
1. 이벤트 허브를 만들 때 이벤트 허브 네임스페이스를 만듭니다. 네임스페이스 내에서 이벤트 허브를 아직 만들지 않은 경우 **메뉴의 엔터티**에서 이벤트 허브를 만듭니다.  

    [![이벤트 허브의 목록](media/send-events/tsi-connect-event-hub-namespace.png)](media/send-events/tsi-connect-event-hub-namespace.png#lightbox)

1. 이벤트 허브를 만든 후 이벤트 허브의 목록에서 선택합니다.
1. 메뉴에서 **엔터티**아래에서 **이벤트 허브를 선택합니다.**
1. 구성할 이벤트 허브의 이름을 선택합니다.
1. **개요에서**소비자 **그룹을**선택한 다음 **소비자 그룹을**선택합니다.

    [![소비자 그룹 만들기](media/send-events/add-event-hub-consumer-group.png)](media/send-events/add-event-hub-consumer-group.png#lightbox)

1. 타임시리즈 인사이트 이벤트 소스에서만 사용하는 소비자 그룹을 만들어야 합니다.

    > [!IMPORTANT]
    > Azure Stream Analytics 작업 또는 다른 열렬 인사이트 환경과 같은 다른 서비스에서 이 소비자 그룹을 사용하지 않는지 확인합니다. 소비자 그룹을 다른 서비스에서 사용하는 경우 읽기 작업이 이 환경 및 다른 서비스 모두에 부정적인 영향을 미칩니다. 소비자 그룹으로 **$Default**를 사용하는 경우 다른 읽기 권한자는 소비자 그룹을 잠재적으로 다시 사용할 수 있습니다.

1. 메뉴에서 **설정**에서 공유 **액세스 정책을**선택한 다음 에 **추가를**선택합니다.

    [![공유 액세스 정책을 선택한 다음, 추가 단추를 선택합니다.](media/send-events/add-shared-access-policy.png)](media/send-events/add-shared-access-policy.png#lightbox)

1. **새 공유 액세스 정책 추가** 창에서 **MySendPolicy**라는 공유 액세스를 만듭니다. 이 공유 액세스 정책을 사용하여 이 문서의 후반부C# 예제에서 이벤트를 보냅니다.

    [![정책 이름 상자에 MySendPolicy 입력](media/send-events/configure-shared-access-policy-confirm.png)](media/send-events/configure-shared-access-policy-confirm.png#lightbox)

1. **클레임에서** **보내기** 확인란을 선택합니다.

## <a name="add-a-time-series-insights-instance"></a>Time Series Insights 인스턴스 추가

Time Series Insights 업데이트는 인스턴스를 사용하여 들어오는 원격 분석 데이터에 상황별 데이터를 추가합니다. 데이터는 **시계열 ID**를 사용하여 쿼리 시에 조인됩니다. 이 문서의 나중에 사용하는 샘플 풍차 프로젝트의 **타임시리즈 ID는** `id`. 타임시리즈 인사이트 인스턴스 및 **타임시리즈 ID에**대해 자세히 알아보려면 [타임시리즈 모델을](./time-series-insights-update-tsm.md)참조하십시오.

### <a name="create-a-time-series-insights-event-source"></a>Time Series Insights 이벤트 원본 만들기

1. 아직 이벤트 원본을 만들지 않은 경우 [이벤트 원본 만들기](https://docs.microsoft.com/azure/time-series-insights/time-series-insights-how-to-add-an-event-source-eventhub)에 대한 단계를 완료합니다.

1. `timeSeriesId`에 대한 값을 설정합니다. 타임 시리즈 **ID에**대한 자세한 내용은 [타임 시리즈 모델을](./time-series-insights-update-tsm.md)참조하십시오.

### <a name="push-events-to-windmills-sample"></a>이벤트를 풍차 샘플로 푸시

1. 검색 창에서 **Event Hubs**를 검색합니다. 반환된 목록에서 **Event Hubs**를 선택합니다.

1. 이벤트 허브 인스턴스를 선택합니다.

1. 공유 **액세스 정책** > **MySendPolicy로**이동합니다. 연결 문자열 **기본 키에**대한 값을 복사합니다.

    [![기본 키 연결 문자열의 값 복사](media/send-events/configure-sample-code-connection-string.png)](media/send-events/configure-sample-code-connection-string.png#lightbox)

1. [https://editor.swagger.io](https://tsiclientsample.azurewebsites.net/windFarmGen.html) 로 이동합니다. URL은 시뮬레이션된 풍차 장치를 만들고 실행합니다.
1. 웹 페이지의 **이벤트 허브 연결 문자열** 상자에서 [풍차 입력 필드에](#push-events-to-windmills-sample)복사한 연결 문자열을 붙여넣습니다.
  
    [![이벤트 허브 연결 문자열 상자에 기본 키 연결 문자열 붙여넣기](media/send-events/configure-wind-mill-sim.png)](media/send-events/configure-wind-mill-sim.png#lightbox)

1. **시작하려면 클릭**을 선택합니다. 

    > [!TIP]
    > 풍차 시뮬레이터는 또한 [타임 시리즈 인사이트 GA 쿼리 API를](https://docs.microsoft.com/rest/api/time-series-insights/ga-query)사용하여 페이로드로 사용할 수 있는 JSON을 만듭니다.

    > [!NOTE]
    > 시뮬레이터는 브라우저 탭이 닫혀질 때까지 데이터를 계속 전송합니다.

1. Azure Portal에서 이벤트 허브로 돌아갑니다. **개요** 페이지에 이벤트 허브에서 받은 새 이벤트가 표시됩니다.

    [![이벤트 허브에 대한 메트릭을 보여주는 이벤트 허브 개요 페이지](media/send-events/review-windmill-telemetry.png)](media/send-events/review-windmill-telemetry.png#lightbox)

## <a name="supported-json-shapes"></a>지원되는 JSON 셰이프

### <a name="example-one"></a>예 1

* **입력**: 간단한 JSON 개체입니다.

    ```JSON
    {
        "id":"device1",
        "timestamp":"2016-01-08T01:08:00Z"
    }
    ```

* **출력**: 하나의 이벤트.

    |id|timestamp|
    |--------|---------------|
    |device1|2016-01-08T01:08:00Z|

### <a name="example-two"></a>예제 2

* **입력**: 두 개의 JSON 개체가 있는 JSON 배열입니다. 각 JSON 개체는 이벤트로 변환됩니다.

    ```JSON
    [
        {
            "id":"device1",
            "timestamp":"2016-01-08T01:08:00Z"
        },
        {
            "id":"device2",
            "timestamp":"2016-01-17T01:17:00Z"
        }
    ]
    ```

* **출력**: 두 개의 이벤트.

    |id|timestamp|
    |--------|---------------|
    |device1|2016-01-08T01:08:00Z|
    |device2|2016-01-08T01:17:00Z|

### <a name="example-three"></a>실시예 3

* **입력**: 두 개의 JSON 개체를 포함하는 중첩 된 JSON 배열이있는 JSON 개체입니다.

    ```JSON
    {
        "location":"WestUs",
        "events":[
            {
                "id":"device1",
                "timestamp":"2016-01-08T01:08:00Z"
            },
            {
                "id":"device2",
                "timestamp":"2016-01-17T01:17:00Z"
            }
        ]
    }
    ```

* **출력**: 두 개의 이벤트. **location** 속성은 각 이벤트로 복사됩니다.

    |위치|events.id|events.timestamp|
    |--------|---------------|----------------------|
    |WestUs|device1|2016-01-08T01:08:00Z|
    |WestUs|device2|2016-01-08T01:17:00Z|

### <a name="example-four"></a>실시예 4

* **입력**: 두 개의 JSON 개체를 포함하는 중첩 된 JSON 배열이있는 JSON 개체입니다. 이 입력은 복합 JSON 개체로 글로벌 속성을 표시할 수 있음을 보여줍니다.

    ```JSON
    {
        "location":"WestUs",
        "manufacturer":{
            "name":"manufacturer1",
            "location":"EastUs"
        },
        "events":[
            {
                "id":"device1",
                "timestamp":"2016-01-08T01:08:00Z",
                "data":{
                    "type":"pressure",
                    "units":"psi",
                    "value":108.09
                }
            },
            {
                "id":"device2",
                "timestamp":"2016-01-17T01:17:00Z",
                "data":{
                    "type":"vibration",
                    "units":"abs G",
                    "value":217.09
                }
            }
        ]
    }
    ```

* **출력**: 두 개의 이벤트.

    |위치|manufacturer.name|manufacturer.location|events.id|events.timestamp|events.data.type|events.data.type|events.data.type|
    |---|---|---|---|---|---|---|---|
    |WestUs|manufacturer1|EastUs|device1|2016-01-08T01:08:00Z|pressure|psi|108.09|
    |WestUs|manufacturer1|EastUs|device2|2016-01-08T01:17:00Z|vibration|abs G|217.09|

## <a name="next-steps"></a>다음 단계

- 타임시리즈 인사이트 탐색기에서 [환경을 봅니다.](https://insights.timeseries.azure.com)

- [IoT 허브 장치 메시지에](https://docs.microsoft.com/azure/iot-hub/iot-hub-devguide-messages-construct) 대해 자세히 알아보기
