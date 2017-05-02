---
title: "Azure Time Series Insights 환경으로 이벤트 보내기 | Microsoft Docs"
description: "이 자습서에서는 Azure Time Series Insights 환경으로 이벤트를 푸시하는 방법을 다룹니다."
keywords: 
services: time-series-insights
documentationcenter: 
author: venkatgct
manager: almineev
editor: cgronlun
ms.assetid: 
ms.service: time-series-insights
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 04/21/2017
ms.author: venkatja
translationtype: Human Translation
ms.sourcegitcommit: 1cc1ee946d8eb2214fd05701b495bbce6d471a49
ms.openlocfilehash: 92e3e64f235e165a6a1772b6e1724789f3ec3049
ms.lasthandoff: 04/26/2017

---
# <a name="send-events-to-a-time-series-insights-environment-via-event-hub"></a>이벤트 허브를 통해 Azure Time Series Insights 환경으로 이벤트 보내기

이 자습서에서는 이벤트 허브를 생성 및 구성하고 이벤트를 푸시하는 샘플 응용 프로그램을 실행하는 방법을 설명합니다. 이미 JSON 형식의 이벤트가 들어 있는 기존 이벤트 허브가 있는 경우 이 자습서를 건너뛰고 [time series 탐색기](https://insights.timeseries.azure.com)에서 환경을 볼 수 있습니다.

## <a name="configure-an-event-hub"></a>이벤트 허브 구성
1. 이벤트 허브를 만들려면 이벤트 허브 [설명서](https://docs.microsoft.com/azure/event-hubs/event-hubs-create)의 지침을 따릅니다.

2. Time Series Insights 이벤트 원본에서 단독으로 사용하는 소비자 그룹을 만들어야 합니다.

  > [!IMPORTANT]
  > 이 소비자 그룹을 다른 서비스(예: Stream Analytics 작업 또는 다른 Time Series Insights 환경)에서 사용하지 못하게 합니다. 소비자 그룹을 다른 서비스에서 사용하는 경우 읽기 작업이 이 환경 및 다른 서비스에 부정적인 영향을 미칩니다. 소비자 그룹으로 “$Default”를 사용할 경우 다른 읽기 권한자가 재사용할 수도 있습니다.

  ![이벤트 허브 소비자 그룹 선택](media/send-events/consumer-group.png)

3. 이벤트 허브에서 아래 샘플의 이벤트를 보내는 데 사용되는 “MySendPolicy”를 만듭니다.

  ![공유 액세스 정책을 선택하고 추가 단추 클릭](media/send-events/shared-access-policy.png)  

  ![새 공유 액세스 정책 추가](media/send-events/shared-access-policy-2.png)  

## <a name="create-time-series-insights-event-source"></a>Time Series Insights 이벤트 원본 만들기
1. 아직 이벤트 원본을 만들지 않은 경우 [여기](time-series-insights-add-event-source.md)에 지정된 지침에 따라 이벤트 원본을 만듭니다.

2. 타임스탬프 속성 이름으로 “deviceTimestamp”를 지정합니다. 이 속성은 아래 샘플에서 실제 타임스탬프로 사용됩니다. 타임스탬프 속성 이름은 대소문자를 구분하며 이벤트 허브에 JSON으로 보낼 때 값이 __yyyy-MM-ddTHH:mm:ss.FFFFFFFK__ 형식이어야 합니다. 해당 속성이 이벤트에 없으면 이벤트가 이벤트 허브 큐에 추가된 시간이 사용됩니다.

  ![이벤트 원본 만들기](media/send-events/event-source-1.png)

## <a name="run-sample-code-to-push-events"></a>이벤트를 푸시하는 샘플 코드 실행
1. 이벤트 허브 정책 “MySendPolicy”로 이동하여 정책 키와 함께 연결 문자열을 복사합니다.

  ![MySendPolicy 연결 문자열 복사](media/send-events/sample-code-connection-string.png)

2. 세 개의 각 장치마다 600개의 이벤트를 보내는 다음 코드를 실행합니다. 연결 문자열로 `eventHubConnectionString`을 업데이트합니다.

```csharp
using System;
using System.Collections.Generic;
using System.Globalization;
using System.IO;
using Microsoft.ServiceBus.Messaging;

namespace Microsoft.Rdx.DataGenerator
{
    internal class Program
    {
        private static void Main(string[] args)
        {
            var from = new DateTime(2017, 4, 20, 15, 0, 0, DateTimeKind.Utc);
            Random r = new Random();
            const int numberOfEvents = 600;

            var deviceIds = new[] { "device1", "device2", "device3" };

            var events = new List<string>();
            for (int i = 0; i < numberOfEvents; ++i)
            {
                for (int device = 0; device < deviceIds.Length; ++device)
                {
                    // Generate event and serialize as JSON object:
                    // { "deviceTimestamp": "utc timestamp", "deviceId": "guid", "value": 123.456 }
                    events.Add(
                        String.Format(
                            CultureInfo.InvariantCulture,
                            @"{{ ""deviceTimestamp"": ""{0}"", ""deviceId"": ""{1}"", ""value"": {2} }}",
                            (from + TimeSpan.FromSeconds(i * 30)).ToString("o"),
                            deviceIds[device],
                            r.NextDouble()));
                }
            }

            // Create event hub connection.
            var eventHubConnectionString = @"Endpoint=sb://...";
            var eventHubClient = EventHubClient.CreateFromConnectionString(eventHubConnectionString);

            using (var ms = new MemoryStream())
            using (var sw = new StreamWriter(ms))
            {
                // Wrap events into JSON array:
                sw.Write("[");
                for (int i = 0; i < events.Count; ++i)
                {
                    if (i > 0)
                    {
                        sw.Write(',');
                    }
                    sw.Write(events[i]);
                }
                sw.Write("]");

                sw.Flush();
                ms.Position = 0;

                // Send JSON to event hub.
                EventData eventData = new EventData(ms);
                eventHubClient.Send(eventData);
            }
        }
    }
}

```
## <a name="supported-json-shapes"></a>지원되는 JSON 셰이프
### <a name="sample-1"></a>샘플 1

#### <a name="input"></a>입력

간단한 JSON 개체입니다.

```json
{
    "deviceId":"device1",
    "deviceTimestamp":"2016-01-08T01:08:00Z"
}
```
#### <a name="output---1-event"></a>출력 - 1개 이벤트

|deviceId|deviceTimestamp|
|--------|---------------|
|device1|2016-01-08T01:08:00Z|

### <a name="sample-2"></a>샘플 2

#### <a name="input"></a>입력
두 JSON 개체가 포함된 JSON 배열입니다. 각 JSON 개체는 이벤트로 변환됩니다.
```json
[
    {
        "deviceId":"device1",
        "deviceTimestamp":"2016-01-08T01:08:00Z"
    },
    {
        "deviceId":"device2",
        "deviceTimestamp":"2016-01-17T01:17:00Z"
    }
]
```
#### <a name="output---2-events"></a>출력 - 2개 이벤트

|deviceId|deviceTimestamp|
|--------|---------------|
|device1|2016-01-08T01:08:00Z|
|device2|2016-01-08T01:17:00Z|

### <a name="sample-3"></a>샘플 3

#### <a name="input"></a>입력

두 JSON 개체가 들어 있는 중첩된 JSON 배열이 포함된 JSON 개체입니다.
```json
{
    "location":"WestUs",
    "events":[
        {
            "deviceId":"device1",
            "deviceTimestamp":"2016-01-08T01:08:00Z"
        },
        {
            "deviceId":"device2",
            "deviceTimestamp":"2016-01-17T01:17:00Z"
        }
    ]
}

```
#### <a name="output---2-events"></a>출력 - 2개 이벤트
"위치" 속성은 각 이벤트로 복사됩니다.

|location|events.deviceId|events.deviceTimestamp|
|--------|---------------|----------------------|
|WestUs|device1|2016-01-08T01:08:00Z|
|WestUs|device2|2016-01-08T01:17:00Z|

### <a name="sample-4"></a>샘플 4

#### <a name="input"></a>입력

```json
{
    "location":"WestUs",
    "manufacturerInfo":{
        "name":"manufacturer1",
        "location":"EastUs"
    },
    "events":[
        {
            "deviceId":"device1",
            "deviceTimestamp":"2016-01-08T01:08:00Z",
            "deviceData":{
                "type":"pressure",
                "units":"psi",
                "value":108.09
            }
        },
        {
            "deviceId":"device2",
            "deviceTimestamp":"2016-01-17T01:17:00Z",
            "deviceData":{
                "type":"vibration",
                "units":"abs G",
                "value":217.09
            }
        }
    ]
}
```
#### <a name="output---2-events"></a>출력 - 2개 이벤트

|location|manufacturerInfo.name|manufacturerInfo.location|events.deviceId|events.deviceTimestamp|events.deviceData.type|events.deviceData.units|events.deviceData.value|
|---|---|---|---|---|---|---|---|
|WestUs|manufacturer1|EastUs|device1|2016-01-08T01:08:00Z|pressure|psi|108.09|
|WestUs|manufacturer1|EastUs|device1|2016-01-08T01:17:00Z|vibration|abs G|217.09|

## <a name="next-steps"></a>다음 단계

* [Time Series Insights 포털](https://insights.timeseries.azure.com)에서 환경 보기

