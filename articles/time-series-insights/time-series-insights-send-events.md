---
title: "Azure Time Series Insights 환경으로 이벤트를 보내는 방법 | Microsoft Docs"
description: "이 자습서에서는 Azure Time Series Insights에 표시하기 위해 이벤트 허브를 생성 및 구성하고 이벤트를 푸시하는 샘플 응용 프로그램을 실행하는 방법을 설명합니다."
services: time-series-insights
ms.service: time-series-insights
author: venkatgct
ms.author: venkatja
manager: jhubbard
editor: MarkMcGeeAtAquent
ms.reviewer: v-mamcge, jasonh, kfile, anshan
ms.devlang: csharp
ms.workload: big-data
ms.topic: article
ms.date: 11/15/2017
ms.openlocfilehash: 543fafac63423ab874c6c8e40d91a1ce0f161987
ms.sourcegitcommit: 9a61faf3463003375a53279e3adce241b5700879
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/15/2017
---
# <a name="send-events-to-a-time-series-insights-environment-using-event-hub"></a>이벤트 허브를 사용하여 Time Series Insights 환경으로 이벤트 보내기
이 문서에서는 이벤트 허브를 생성 및 구성하고 이벤트를 푸시하는 샘플 응용 프로그램을 실행하는 방법을 설명합니다. JSON 형식의 이벤트가 있는 기존 이벤트 허브가 있는 경우 이 자습서를 건너뛰고 [시계열 정보](https://insights.timeseries.azure.com)에서 환경을 봅니다.

## <a name="configure-an-event-hub"></a>이벤트 허브 구성
1. 이벤트 허브를 만들려면 이벤트 허브 [설명서](../event-hubs/event-hubs-create.md)의 지침을 따릅니다.

2. 검색 창에서 **이벤트 허브**를 검색합니다. 반환된 목록에서 **이벤트 허브**를 클릭합니다.

3. 해당 이름을 클릭하여 이벤트 허브를 선택합니다.

4. 중간 구성 창의 엔터티 아래에서 **이벤트 허브**를 다시 클릭합니다.

5. 구성할 이벤트 허브의 이름을 선택합니다.

  ![이벤트 허브 소비자 그룹 선택](media/send-events/consumer-group.png)

6. **엔터티** 아래에서 **소비자 그룹**을 선택합니다.
 
7. Time Series Insights 이벤트 원본에서 단독으로 사용하는 소비자 그룹을 만들어야 합니다.

   > [!IMPORTANT]
   > 이 소비자 그룹을 다른 서비스(예: Stream Analytics 작업 또는 다른 Time Series Insights 환경)에서 사용하지 못하게 합니다. 소비자 그룹을 다른 서비스에서 사용하는 경우 읽기 작업이 이 환경 및 다른 서비스에 부정적인 영향을 미칩니다. 소비자 그룹으로 “$Default”를 사용할 경우 다른 읽기 권한자가 재사용할 수도 있습니다.

8. **설정** 제목 아래에서 **액세스 정책 공유**를 선택합니다.

9. 이벤트 허브에서 csharp 샘플에서 이벤트를 보내는 데 사용되는 **MySendPolicy**를 만듭니다.

  ![공유 액세스 정책을 선택하고 추가 단추 클릭](media/send-events/shared-access-policy.png)  

  ![새 공유 액세스 정책 추가](media/send-events/shared-access-policy-2.png)  

## <a name="create-time-series-insights-event-source"></a>Time Series Insights 이벤트 원본 만들기
1. 아직 이벤트 원본을 만들지 않은 경우 [다음된 지침](time-series-insights-how-to-add-an-event-source-eventhub.md)에 따라 이벤트 원본을 만듭니다.

2. 타임스탬프 속성 이름으로 **deviceTimestamp**를 지정합니다. 이 속성은 C# 샘플에서 실제 타임스탬프로 사용됩니다. 타임스탬프 속성 이름은 대소문자를 구분하며 이벤트 허브에 JSON으로 보낼 때 값이 __yyyy-MM-ddTHH:mm:ss.FFFFFFFK__ 형식을 따라야 합니다. 해당 속성이 이벤트에 없는 경우 시간이 큐에 추가된 이벤트 허브가 사용됩니다.

  ![이벤트 원본 만들기](media/send-events/event-source-1.png)

## <a name="sample-code-to-push-events"></a>이벤트를 푸시하는 샘플 코드
1. **MySendPolicy**라고 명명된 이벤트 허브 정책으로 이동합니다. **연결 문자열**을 정책 키와 함께 복사합니다.

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
    "id":"device1",
    "timestamp":"2016-01-08T01:08:00Z"
}
```
#### <a name="output---1-event"></a>출력 - 1개 이벤트

|id|timestamp|
|--------|---------------|
|device1|2016-01-08T01:08:00Z|

### <a name="sample-2"></a>샘플 2

#### <a name="input"></a>입력
두 JSON 개체가 포함된 JSON 배열입니다. 각 JSON 개체는 이벤트로 변환됩니다.
```json
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
#### <a name="output---2-events"></a>출력 - 2개 이벤트

|id|timestamp|
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
#### <a name="output---2-events"></a>출력 - 2개 이벤트
"위치" 속성은 각 이벤트로 복사됩니다.

|위치|events.id|events.timestamp|
|--------|---------------|----------------------|
|WestUs|device1|2016-01-08T01:08:00Z|
|WestUs|device2|2016-01-08T01:17:00Z|

### <a name="sample-4"></a>샘플 4

#### <a name="input"></a>입력

두 JSON 개체가 들어 있는 중첩된 JSON 배열이 포함된 JSON 개체입니다. 이 입력은 복합 JSON 개체로 전역 속성을 표시할 수 있음을 보여줍니다.

```json
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
#### <a name="output---2-events"></a>출력 - 2개 이벤트

|위치|manufacturer.name|manufacturer.location|events.id|events.timestamp|events.data.type|events.data.type|events.data.type|
|---|---|---|---|---|---|---|---|
|WestUs|manufacturer1|EastUs|device1|2016-01-08T01:08:00Z|pressure|psi|108.09|
|WestUs|manufacturer1|EastUs|device2|2016-01-08T01:17:00Z|vibration|abs G|217.09|

## <a name="next-steps"></a>다음 단계
[Time Series Insights 탐색기](https://insights.timeseries.azure.com)에서 환경 보기
