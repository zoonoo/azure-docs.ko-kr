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
ms.date: 05/06/2019
ms.custom: seodec18
ms.openlocfilehash: ae59e8115ca2d1ba69c8a3a099216eb3d98e2658
ms.sourcegitcommit: 509e1583c3a3dde34c8090d2149d255cb92fe991
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/27/2019
ms.locfileid: "66237703"
---
# <a name="send-events-to-a-time-series-insights-environment-by-using-an-event-hub"></a>이벤트 허브를 사용하여 Time Series Insights 환경으로 이벤트 보내기

이 문서를 만들고 Azure Event Hubs에서 이벤트 허브를 구성 하는 방법을 설명 합니다. Azure Time Series Insights에 Event Hubs에서 이벤트를 푸시하는 샘플 응용 프로그램을 실행 하는 방법을 설명 합니다. JSON 형식으로 이벤트를 사용 하 여 기존 이벤트 허브가 있는 경우이 자습서를 건너뛰고에서 환경을 봅니다 [Azure Time Series Insights](./time-series-insights-update-create-environment.md)합니다.

## <a name="configure-an-event-hub"></a>이벤트 허브 구성

1. 이벤트 허브를 만드는 방법을 알아보려면 [Event Hubs 설명서](https://docs.microsoft.com/azure/event-hubs/)를 참조하세요.
1. 검색 상자에서 **Event Hubs**를 검색합니다. 반환된 목록에서 **Event Hubs**를 선택합니다.
1. 이벤트 허브를 선택합니다.
1. 이벤트 허브를 만들 때 만들 이벤트 허브 네임 스페이스입니다. 아직 메뉴의 네임 스페이스 내에서 이벤트 허브를 아래에서 만들지 않은 경우 **엔터티**, 이벤트 허브를 만듭니다.  

    [![Event hubs의 목록](media/send-events/updated.png)](media/send-events/updated.png#lightbox)

1. 이벤트 허브를 만든 후 이벤트 허브의 목록에서 선택합니다.
1. 메뉴에서 아래 **엔터티**를 선택 **Event Hubs**합니다.
1. 구성할 이벤트 허브의 이름을 선택합니다.
1. **엔터티**에서 **소비자 그룹**을 선택한 다음, **소비자 그룹**을 선택합니다.

    [![소비자 그룹 만들기](media/send-events/consumer-group.png)](media/send-events/consumer-group.png#lightbox)

1. Time Series Insights 이벤트 원본에서 단독으로 사용 되는 소비자 그룹을 만들면 있는지 확인 합니다.

    > [!IMPORTANT]
    > 이 소비자 그룹 Azure Stream Analytics 작업 또는 다른 Time Series Insights 환경 등의 다른 서비스에서 사용 되지 않도록 확인 하십시오. 소비자 그룹을 다른 서비스에서 사용하는 경우 읽기 작업이 이 환경 및 다른 서비스 모두에 부정적인 영향을 미칩니다. 소비자 그룹으로 **$Default**를 사용하는 경우 다른 읽기 권한자는 소비자 그룹을 잠재적으로 다시 사용할 수 있습니다.

1. 메뉴에서 아래 **설정을**를 선택 **공유 액세스 정책**를 선택한 후 **추가**합니다.

    [![공유 액세스 정책을 선택를 선택한 다음 추가 단추](media/send-events/shared-access-policy.png)](media/send-events/shared-access-policy.png#lightbox)

1. **새 공유 액세스 정책 추가** 창에서 **MySendPolicy**라는 공유 액세스를 만듭니다. 이 공유 액세스 정책을 사용 하 여 이벤트를 전송 하는 C# 이 문서 뒷부분의 예제입니다.

    [![정책 이름 상자에 입력 MySendPolicy](media/send-events/shared-access-policy-2.png)](media/send-events/shared-access-policy-2.png#lightbox)

1. 아래 **클레임**를 선택 합니다 **보내기** 확인란 합니다.

## <a name="add-a-time-series-insights-instance"></a>Time Series Insights 인스턴스 추가

Time Series Insights 업데이트는 인스턴스를 사용하여 들어오는 원격 분석 데이터에 상황별 데이터를 추가합니다. 데이터는 **시계열 ID**를 사용하여 쿼리 시에 조인됩니다. 합니다 **시간 시리즈 ID** 샘플 무모가이 문서의 뒷부분에서 사용 하는 프로젝트는 `id`합니다. Time Series Insight 인스턴스 및 **시계열 ID**에 대해 자세히 알아보려면 [시계열 모델](./time-series-insights-update-tsm.md)을 참조하세요.

### <a name="create-a-time-series-insights-event-source"></a>Time Series Insights 이벤트 원본 만들기

1. 아직 이벤트 원본을 만들지 않은 경우 [이벤트 원본 만들기](https://docs.microsoft.com/azure/time-series-insights/time-series-insights-how-to-add-an-event-source-eventhub)에 대한 단계를 완료합니다.

1. `timeSeriesId`에 대한 값을 설정합니다. **시계열 ID**에 대해 자세히 알아보려면 [시계열 모델](./time-series-insights-update-tsm.md)을 참조하세요.

### <a name="push-events"></a>이벤트 푸시(가상 샘플)

1. 검색 창에서 **Event Hubs**를 검색합니다. 반환된 목록에서 **Event Hubs**를 선택합니다.

1. 이벤트 허브를 선택합니다.

1. **공유 액세스 정책** > **RootManageSharedAccessKey**로 이동합니다. 값을 복사 **연결 문자열-기본 키**합니다.

    [![기본 키 연결 문자열 값을 복사](media/send-events/sample-code-connection-string.png)](media/send-events/sample-code-connection-string.png#lightbox)

1. https://tsiclientsample.azurewebsites.net/windFarmGen.html로 이동합니다. URL은 시뮬레이션된 가상 디바이스를 실행합니다.
1. 웹 페이지의 **이벤트 허브 연결 문자열** 상자에 [이벤트 푸시](#push-events)에서 복사한 연결 문자열을 붙여넣습니다.
  
    [![이벤트 허브 연결 문자열 상자에서 기본 키 연결 문자열을 붙여 넣습니다.](media/send-events/updated_two.png)](media/send-events/updated_two.png#lightbox)

1. **시작하려면 클릭**을 선택합니다. 시뮬레이터는 직접 사용할 수 있는 인스턴스 JSON을 생성합니다.

1. Azure Portal에서 이벤트 허브로 돌아갑니다. 에 **개요** 페이지에 이벤트 허브에서 수신 되는 새 이벤트를 표시 합니다.

    [![이벤트 허브에 대 한 메트릭을 보여 주는 이벤트 허브 개요 페이지](media/send-events/telemetry.png)](media/send-events/telemetry.png#lightbox)

## <a name="json"></a>지원 되는 JSON 셰이프

### <a name="example-one"></a>예제 1

* **입력**: 간단한 JSON 개체입니다.

    ```JSON
    {
        "id":"device1",
        "timestamp":"2016-01-08T01:08:00Z"
    }
    ```

* **출력**: 하나의 이벤트입니다.

    |id|timestamp|
    |--------|---------------|
    |device1|2016-01-08T01:08:00Z|

### <a name="example-two"></a>예제 2

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

* **출력**: 두 이벤트입니다.

    |id|timestamp|
    |--------|---------------|
    |device1|2016-01-08T01:08:00Z|
    |device2|2016-01-08T01:17:00Z|

### <a name="example-three"></a>예제 3

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

* **출력**: 두 이벤트입니다. **location** 속성은 각 이벤트로 복사됩니다.

    |location|events.id|events.timestamp|
    |--------|---------------|----------------------|
    |WestUs|device1|2016-01-08T01:08:00Z|
    |WestUs|device2|2016-01-08T01:17:00Z|

### <a name="example-four"></a>예제 4

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

* **출력**: 두 이벤트입니다.

    |location|manufacturer.name|manufacturer.location|events.id|events.timestamp|events.data.type|events.data.type|events.data.type|
    |---|---|---|---|---|---|---|---|
    |WestUs|manufacturer1|EastUs|device1|2016-01-08T01:08:00Z|pressure|psi|108.09|
    |WestUs|manufacturer1|EastUs|device2|2016-01-08T01:17:00Z|vibration|abs G|217.09|

## <a name="next-steps"></a>다음 단계

- [환경 보기](https://insights.timeseries.azure.com) Time Series Insights 탐색기에서.
