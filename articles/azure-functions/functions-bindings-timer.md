---
title: "Azure Functions 타이머 트리거 | Microsoft Docs"
description: "Azure Functions에서 타이머 트리거를 사용하는 방법을 파악합니다."
services: functions
documentationcenter: na
author: christopheranderson
manager: cfowler
editor: 
tags: 
keywords: "Azure 함수, 함수, 이벤트 처리, 동적 계산, 서버를 사용하지 않는 아키텍처"
ms.assetid: d2f013d1-f458-42ae-baf8-1810138118ac
ms.service: functions
ms.devlang: multiple
ms.topic: reference
ms.tgt_pltfrm: multiple
ms.workload: na
ms.date: 02/27/2017
ms.author: glenga
ms.custom: 
ms.openlocfilehash: ed4afa2b946fe11d5802a95f8a490f3419dc0f39
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/11/2017
---
# <a name="azure-functions-timer-trigger"></a>Azure Functions 타이머 트리거

[!INCLUDE [functions-selector-bindings](../../includes/functions-selector-bindings.md)]

이 문서에서는 Azure Functions에서 타이머 트리거를 구성하고 코딩하는 방법을 설명합니다. Azure Functions에는 정의된 일정에 따라 함수 코드를 실행할 수 있는 타이머 트리거 바인딩이 있습니다. 

타이머 트리거는 다중 인스턴스 확장을 지원합니다. 특정 타이머 함수의 단일 인스턴스는 모든 인스턴스에 대해 실행됩니다.

[!INCLUDE [intro](../../includes/functions-bindings-intro.md)]

<a id="trigger"></a>

## <a name="timer-trigger"></a>타이머 트리거
함수에 대한 타이머 트리거는 function.json의 `bindings` 배열에서 다음과 같은 JSON 개체를 사용합니다.

```json
{
    "schedule": "<CRON expression - see below>",
    "name": "<Name of trigger parameter in function signature>",
    "type": "timerTrigger",
    "direction": "in"
}
```

`schedule`의 값은 이러한 6개 필드를 포함하는 [CRON 식](http://en.wikipedia.org/wiki/Cron#CRON_expression)입니다. 

    {second} {minute} {hour} {day} {month} {day-of-week}
&nbsp;
>[!NOTE]   
>온라인에서 볼 수 있는 많은 cron 식은 `{second}` 필드를 생략합니다. 그 중 하나를 복사하는 경우 추가 `{second}` 필드에 대해 조정해야 합니다. 특정 예제를 보려면 아래에 있는 [일정 예제](#examples)를 참조하세요.

CRON 식과 함께 사용하는 기본 표준 시간대는 UTC(협정 세계시)입니다. 다른 표준 시간대를 기반으로 하는 CRON 식을 사용하려면 `WEBSITE_TIME_ZONE`이라는 함수 앱에 대한 새 앱 설정을 만듭니다. [Microsoft 표준 시간대 색인](https://msdn.microsoft.com/library/ms912391.aspx)에 나온 대로 값을 원하는 표준 시간대의 이름으로 설정합니다. 

예를 들어 *동부 표준시*는 UTC-05:00입니다. 타이머 트리거를 매일 오전 10시 EST에 발생하도록 하려면 UTC 표준 시간대를 반영하는 다음과 같은 CRON 식을 사용합니다.

```json
"schedule": "0 0 15 * * *",
``` 

또는 `WEBSITE_TIME_ZONE`이라는 함수 앱에 대한 새 앱 설정을 추가하고 값을 **동부 표준시**로 설정할 수도 있습니다.  그런 다음 오전 10시 EST에 대해 다음과 같은 CRON 식을 사용할 수 있습니다. 

```json
"schedule": "0 0 10 * * *",
``` 


<a name="examples"></a>

## <a name="schedule-examples"></a>일정 예제
다음은 `schedule` 속성에 사용할 수 있는 몇 가지 샘플 CRON 식입니다. 

5분마다 한 번씩 트리거하려면:

```json
"schedule": "0 */5 * * * *"
```

1시간마다 맨 위에 한 번씩 트리거하려면:

```json
"schedule": "0 0 * * * *",
```

2시간마다 한 번씩 트리거하려면:

```json
"schedule": "0 0 */2 * * *",
```

오전 9시에서 오후 5시까지 1시간마다 한 번씩 트리거하려면:

```json
"schedule": "0 0 9-17 * * *",
```

매일 오전 9시 30분에 트리거하려면:

```json
"schedule": "0 30 9 * * *",
```

월요일부터 금요일까지 오전 9시 30분에 트리거하려면:

```json
"schedule": "0 30 9 * * 1-5",
```

<a name="usage"></a>

## <a name="trigger-usage"></a>트리거 사용
타이머 트리거 함수를 호출하면 [타이머 개체](https://github.com/Azure/azure-webjobs-sdk-extensions/blob/master/src/WebJobs.Extensions/Extensions/Timers/TimerInfo.cs)가 함수에 전달됩니다. 다음 JSON은 타이머 개체의 예제 표현입니다. 

```json
{
    "Schedule":{
    },
    "ScheduleStatus": {
        "Last":"2016-10-04T10:15:00.012699+00:00",
        "Next":"2016-10-04T10:20:00+00:00"
    },
    "IsPastDue":false
}
```

<a name="sample"></a>

## <a name="trigger-sample"></a>트리거 샘플
function.json의 `bindings` 배열에 다음과 같은 타이머 트리거가 있다고 가정합니다.

```json
{
    "schedule": "0 */5 * * * *",
    "name": "myTimer",
    "type": "timerTrigger",
    "direction": "in"
}
```

타이머 개체를 읽어 실행이 지연되는지 여부를 확인하는 언어별 샘플을 참조하세요.

* [C#](#triggercsharp)
* [F#](#triggerfsharp)
* [Node.JS](#triggernodejs)

<a name="triggercsharp"></a>

### <a name="trigger-sample-in-c"></a>C#에서 트리거 샘플 #
```csharp
public static void Run(TimerInfo myTimer, TraceWriter log)
{
    if(myTimer.IsPastDue)
    {
        log.Info("Timer is running late!");
    }
    log.Info($"C# Timer trigger function executed at: {DateTime.Now}" );  
}
```

<a name="triggerfsharp"></a>

### <a name="trigger-sample-in-f"></a>F#에서 트리거 샘플 #
```fsharp
let Run(myTimer: TimerInfo, log: TraceWriter ) =
    if (myTimer.IsPastDue) then
        log.Info("F# function is running late.")
    let now = DateTime.Now.ToLongTimeString()
    log.Info(sprintf "F# function executed at %s!" now)
```

<a name="triggernodejs"></a>

### <a name="trigger-sample-in-nodejs"></a>Node.js에서 트리거 샘플
```JavaScript
module.exports = function (context, myTimer) {
    var timeStamp = new Date().toISOString();

    if(myTimer.isPastDue)
    {
        context.log('Node.js is running late!');
    }
    context.log('Node.js timer trigger function ran!', timeStamp);   

    context.done();
};
```

## <a name="next-steps"></a>다음 단계
[!INCLUDE [next steps](../../includes/functions-bindings-next-steps.md)]

