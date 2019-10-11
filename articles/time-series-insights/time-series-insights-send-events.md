---
title: Azure Time Series Insights 환경으로 이벤트 보내기 | Microsoft Docs
description: Azure Time Series Insights에서 볼 수 있는 이벤트를 푸시하기 위해 이벤트 허브를 구성하고 샘플 애플리케이션을 실행하는 방법을 알아봅니다.
ms.service: time-series-insights
services: time-series-insights
author: ashannon7
ms.author: dpalled
manager: cshankar
ms.reviewer: v-mamcge, jasonh, kfile
ms.devlang: csharp
ms.workload: big-data
ms.topic: conceptual
ms.date: 10/10/2019
ms.custom: seodec18
ms.openlocfilehash: accf3adea08e713a7a2f06bb175c759ae66a72c0
ms.sourcegitcommit: f272ba8ecdbc126d22a596863d49e55bc7b22d37
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/11/2019
ms.locfileid: "72274597"
---
# <a name="send-events-to-a-time-series-insights-environment-by-using-an-event-hub"></a>이벤트 허브를 사용하여 Time Series Insights 환경으로 이벤트 보내기

이 문서에서는 Azure Event Hubs에서 이벤트 허브를 만들고 구성 하는 방법을 설명 합니다. 또한 Event Hubs에서 Azure Time Series Insights 하는 이벤트를 푸시하는 샘플 응용 프로그램을 실행 하는 방법을 설명 합니다. JSON 형식의 이벤트가 있는 기존 이벤트 허브가 있는 경우이 자습서를 건너뛰고 [Azure Time Series Insights](./time-series-insights-update-create-environment.md)에서 환경을 확인 합니다.

## <a name="configure-an-event-hub"></a>이벤트 허브 구성

1. 이벤트 허브를 만드는 방법을 알아보려면 [Event Hubs 설명서](https://docs.microsoft.com/azure/event-hubs/)를 참조하세요.
1. 검색 상자에서 **Event Hubs**를 검색합니다. 반환된 목록에서 **Event Hubs**를 선택합니다.
1. 이벤트 허브를 선택합니다.
1. 이벤트 허브를 만들 때 event hubs 네임 스페이스를 만듭니다. 네임 스페이스 내에서 이벤트 허브를 아직 만들지 않은 경우 메뉴의 **엔터티**아래에서 이벤트 허브를 만듭니다.  

    [![List hubs 목록](media/send-events/1-event-hub-namespace.png)](media/send-events/1-event-hub-namespace.png#lightbox)

1. 이벤트 허브를 만든 후 이벤트 허브의 목록에서 선택합니다.
1. 메뉴의 **엔터티**아래에서 **Event Hubs**를 선택 합니다.
1. 구성할 이벤트 허브의 이름을 선택합니다.
1. **개요**아래에서 **소비자 그룹**을 선택 하 고 **소비자 그룹**을 선택 합니다.

    [![ 소비자 그룹 만들기](media/send-events/2-consumer-group.png)](media/send-events/2-consumer-group.png#lightbox)

1. Time Series Insights 이벤트 원본에 의해 독점적으로 사용 되는 소비자 그룹을 만들어야 합니다.

    > [!IMPORTANT]
    > 이 소비자 그룹이 Azure Stream Analytics 작업 또는 다른 Time Series Insights 환경과 같은 다른 서비스에서 사용 되지 않는지 확인 합니다. 소비자 그룹을 다른 서비스에서 사용하는 경우 읽기 작업이 이 환경 및 다른 서비스 모두에 부정적인 영향을 미칩니다. 소비자 그룹으로 **$Default**를 사용하는 경우 다른 읽기 권한자는 소비자 그룹을 잠재적으로 다시 사용할 수 있습니다.

1. 메뉴의 **설정**에서 **공유 액세스 정책**을 선택 하 고 **추가**를 선택 합니다.

    [@no__t 공유 액세스 정책을 선택 하 고 추가 단추를 선택 합니다.](media/send-events/3-shared-access-policy.png)](media/send-events/3-shared-access-policy.png#lightbox)

1. **새 공유 액세스 정책 추가** 창에서 **MySendPolicy**라는 공유 액세스를 만듭니다. 이 공유 액세스 정책을 사용 하 여이 문서의 뒷부분에 C# 나오는 예제에서 이벤트를 전송 합니다.

    [정책 이름 상자를 @no__t 1In MySendPolicy을 입력 합니다.](media/send-events/4-shared-access-policy-confirm.png)](media/send-events/4-shared-access-policy-confirm.png#lightbox)

1. **클레임**에서 **송신** 확인란을 선택 합니다.

## <a name="add-a-time-series-insights-instance"></a>Time Series Insights 인스턴스 추가

Time Series Insights 업데이트는 인스턴스를 사용하여 들어오는 원격 분석 데이터에 상황별 데이터를 추가합니다. 데이터는 **시계열 ID**를 사용하여 쿼리 시에 조인됩니다. 이 문서의 뒷부분에서 사용 하는 샘플 windmills 프로젝트의 **시계열 ID** @no__t는-1입니다. Time Series Insight 인스턴스 및 **시계열 ID**에 대해 자세히 알아보려면 [시계열 모델](./time-series-insights-update-tsm.md)을 참조하세요.

### <a name="create-a-time-series-insights-event-source"></a>Time Series Insights 이벤트 원본 만들기

1. 아직 이벤트 원본을 만들지 않은 경우 [이벤트 원본 만들기](https://docs.microsoft.com/azure/time-series-insights/time-series-insights-how-to-add-an-event-source-eventhub)에 대한 단계를 완료합니다.

1. `timeSeriesId`에 대한 값을 설정합니다. **시계열 ID**에 대해 자세히 알아보려면 [시계열 모델](./time-series-insights-update-tsm.md)을 참조하세요.

### <a name="push-events-to-windmills-sample"></a>Windmills 샘플로 이벤트 푸시

1. 검색 창에서 **Event Hubs**를 검색합니다. 반환된 목록에서 **Event Hubs**를 선택합니다.

1. 이벤트 허브 인스턴스를 선택 합니다.

1. **공유 액세스 정책** > **MySendPolicy**로 이동 합니다. **연결 문자열-기본 키**의 값을 복사 합니다.

    [![ 기본 키 연결 문자열의 값을 복사 합니다.](media/send-events/5-sample-code-connection-string.png)](media/send-events/5-sample-code-connection-string.png#lightbox)

1. 로 이동하세요. https://tsiclientsample.azurewebsites.net/windFarmGen.html URL은 시뮬레이션된 가상 디바이스를 실행합니다.
1. 웹 페이지의 **이벤트 허브 연결 문자열** 상자에, [풍차 입력 필드](#push-events-to-windmills-sample)에 복사한 연결 문자열을 붙여넣습니다.
  
    [![ 기본 키 연결 문자열을 이벤트 허브 연결 문자열 상자에 붙여 넣습니다.](media/send-events/6-wind-mill-sim.png)](media/send-events/6-wind-mill-sim.png#lightbox)

1. **시작하려면 클릭**을 선택합니다. 시뮬레이터는 직접 사용할 수 있는 인스턴스 JSON을 생성합니다.

1. Azure Portal에서 이벤트 허브로 돌아갑니다. **개요** 페이지에 이벤트 허브에서 받은 새 이벤트가 표시 됩니다.

    [![ 이벤트 허브에 대 한 메트릭을 표시 하는 이벤트 허브 개요 페이지](media/send-events/7-telemetry.png)](media/send-events/7-telemetry.png#lightbox)

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

### <a name="example-two"></a>예 2

* **입력**: 두 JSON 개체가 포함된 JSON 배열입니다. 각 JSON 개체는 이벤트로 변환됩니다.

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

* **출력**: 두 이벤트.

    |id|timestamp|
    |--------|---------------|
    |device1|2016-01-08T01:08:00Z|
    |device2|2016-01-08T01:17:00Z|

### <a name="example-three"></a>예 3

* **입력**: 두 JSON 개체가 들어 있는 중첩된 JSON 배열이 포함된 JSON 개체입니다.

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

* **출력**: 두 이벤트. **location** 속성은 각 이벤트로 복사됩니다.

    |location|events.id|events.timestamp|
    |--------|---------------|----------------------|
    |WestUs|device1|2016-01-08T01:08:00Z|
    |WestUs|device2|2016-01-08T01:17:00Z|

### <a name="example-four"></a>예 4

* **입력**: 두 JSON 개체가 들어 있는 중첩된 JSON 배열이 포함된 JSON 개체입니다. 이 입력은 복합 JSON 개체로 글로벌 속성을 표시할 수 있음을 보여줍니다.

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

* **출력**: 두 이벤트.

    |location|manufacturer.name|manufacturer.location|events.id|events.timestamp|events.data.type|events.data.type|events.data.type|
    |---|---|---|---|---|---|---|---|
    |WestUs|manufacturer1|EastUs|device1|2016-01-08T01:08:00Z|pressure|psi|108.09|
    |WestUs|manufacturer1|EastUs|device2|2016-01-08T01:17:00Z|vibration|abs G|217.09|

## <a name="next-steps"></a>다음 단계

- Time Series Insights 탐색기에서 [환경을 확인](https://insights.timeseries.azure.com) 합니다.

- [IoT Hub 장치 메시지](https://docs.microsoft.com/azure/iot-hub/iot-hub-devguide-messages-construct) 에 대 한 자세한 정보
