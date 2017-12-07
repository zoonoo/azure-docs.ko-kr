---
title: "Azure Functions의 타이머 트리거"
description: "Azure Functions에서 타이머 트리거를 사용하는 방법을 파악합니다."
services: functions
documentationcenter: na
author: tdykstra
manager: cfowler
editor: 
tags: 
keywords: "Azure Functions, 함수, 이벤트 처리, 동적 계산, 서버를 사용하지 않는 아키텍처"
ms.assetid: d2f013d1-f458-42ae-baf8-1810138118ac
ms.service: functions
ms.devlang: multiple
ms.topic: reference
ms.tgt_pltfrm: multiple
ms.workload: na
ms.date: 02/27/2017
ms.author: tdykstra
ms.custom: 
ms.openlocfilehash: fd9c1d40ba1398c7ca3f48f0423457482da9a483
ms.sourcegitcommit: 29bac59f1d62f38740b60274cb4912816ee775ea
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/29/2017
---
# <a name="timer-trigger-for-azure-functions"></a>Azure Functions의 타이머 트리거 

이 문서에서는 Azure Functions에서 타이머 트리거를 사용하는 방법을 설명합니다. 타이머 트리거를 사용하면 일정에 따라 함수를 실행할 수 있습니다. 

[!INCLUDE [intro](../../includes/functions-bindings-intro.md)]

## <a name="example"></a>예제

언어 관련 예제를 참조하세요.

* [미리 컴파일된 C#](#trigger---c-example)
* [C# 스크립트](#trigger---c-script-example)
* [F#](#trigger---f-example)
* [JavaScript](#trigger---javascript-example)

### <a name="c-example"></a>C# 예제

다음 예제에서는 5분마다 실행되는 [미리 컴파일된 C# 함수](functions-dotnet-class-library.md)를 보여줍니다.

```cs
[FunctionName("TimerTriggerCSharp")]
public static void Run([TimerTrigger("0 */5 * * * *")]TimerInfo myTimer, TraceWriter log)
{
    log.Info($"C# Timer trigger function executed at: {DateTime.Now}");
}
```

### <a name="c-script-example"></a>C# 스크립트 예제

다음 예에서는 *function.json* 파일의 타이머 트리거 바인딩 및 바인딩을 사용하는 [C# 스크립트 함수](functions-reference-csharp.md)를 보여줍니다. 함수는 누락된 일정으로 인해 이 함수 호출이 발생했는지를 나타내는 로그를 씁니다.

*function.json* 파일의 바인딩 데이터는 다음과 같습니다.

```json
{
    "schedule": "0 */5 * * * *",
    "name": "myTimer",
    "type": "timerTrigger",
    "direction": "in"
}
```

C# 스크립트 코드는 다음과 같습니다.

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

### <a name="f-example"></a>F# 예제

다음 예에서는 *function.json* 파일의 타이머 트리거 바인딩 및 바인딩을 사용하는 [F# 스크립트 함수](functions-reference-fsharp.md)를 보여줍니다. 함수는 누락된 일정으로 인해 이 함수 호출이 발생했는지를 나타내는 로그를 씁니다.

*function.json* 파일의 바인딩 데이터는 다음과 같습니다.

```json
{
    "schedule": "0 */5 * * * *",
    "name": "myTimer",
    "type": "timerTrigger",
    "direction": "in"
}
```

F# 스크립트 코드는 다음과 같습니다.

```fsharp
let Run(myTimer: TimerInfo, log: TraceWriter ) =
    if (myTimer.IsPastDue) then
        log.Info("F# function is running late.")
    let now = DateTime.Now.ToLongTimeString()
    log.Info(sprintf "F# function executed at %s!" now)
```

### <a name="javascript-example"></a>JavaScript 예제

다음 예에서는 *function.json* 파일의 타이머 트리거 바인딩 및 바인딩을 사용하는 [JavaScript 함수](functions-reference-node.md)를 보여줍니다. 함수는 누락된 일정으로 인해 이 함수 호출이 발생했는지를 나타내는 로그를 씁니다.

*function.json* 파일의 바인딩 데이터는 다음과 같습니다.

```json
{
    "schedule": "0 */5 * * * *",
    "name": "myTimer",
    "type": "timerTrigger",
    "direction": "in"
}
```

JavaScript 스크립트 코드는 다음과 같습니다.

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

## <a name="attributes"></a>특성

[미리 컴파일된 C#](functions-dotnet-class-library.md) 함수의 경우 [TimerTriggerAttribute](https://github.com/Azure/azure-webjobs-sdk-extensions/blob/master/src/WebJobs.Extensions/Extensions/Timers/TimerTriggerAttribute.cs)를 사용하며 [Microsoft.Azure.WebJobs.Extensions](http://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions) NuGet 패키지에 정의됩니다.

특성의 생성자는 다음 예제와 같이 CRON 식을 사용합니다.

```csharp
[FunctionName("TimerTriggerCSharp")]
public static void Run([TimerTrigger("0 */5 * * * *")]TimerInfo myTimer, TraceWriter log)
{
   ...
}
 ```

함수 앱이 App Service 계획(소비 계획 아님)에서 실행되는 경우 CRON 식 대신 `TimeSpan`을 지정할 수 있습니다.

전체 예제는 [미리 컴파일된 C# 예제](#c-example)를 참조하세요.

## <a name="configuration"></a>구성

다음 표에서는 *function.json* 파일 및 `TimerTrigger` 특성에 설정된 바인딩 구성 속성을 설명합니다.

|function.json 속성 | 특성 속성 |설명|
|---------|---------|----------------------|
|**type** | 해당 없음 | "timerTrigger"로 설정해야 합니다. 이 속성은 사용자가 Azure Portal에서 트리거를 만들 때 자동으로 설정됩니다.|
|**direction** | 해당 없음 | "in"으로 설정해야 합니다. 이 속성은 사용자가 Azure Portal에서 트리거를 만들 때 자동으로 설정됩니다. |
|**name** | 해당 없음 | 함수 코드에서 타이머 개체를 나타내는 변수의 이름입니다. | 
|**schedule**|**ScheduleExpression**|소비 계획에서 CRON 식을 사용하여 일정을 정의할 수 있습니다. App Service 계획을 사용하는 경우 `TimeSpan` 문자열을 사용할 수도 있습니다. 다음 섹션에서는 CRON 식을 설명합니다. "%NameOfAppSettingWithCRONExpression%" 예제와 같이 앱 설정에서 일정 식을 설정하고 이 속성을 **%** 기호에서 래핑된 값으로 설정할 수 있습니다. |

[!INCLUDE [app settings to local.settings.json](../../includes/functions-app-settings-local.md)]

### <a name="cron-format"></a>CRON 형식 

Azure Functions 타이머 트리거의 [CRON 식](http://en.wikipedia.org/wiki/Cron#CRON_expression)에는 다음과 같은 6개의 필드가 포함됩니다. 

```
{second} {minute} {hour} {day} {month} {day-of-week}
```

>[!NOTE]   
>온라인에서 볼 수 있는 많은 CRON 식은 `{second}` 필드를 생략합니다. 이들 중 하나를 복사하는 경우 누락된 `{second}` 필드를 추가합니다.

### <a name="cron-time-zones"></a>CRON 표준 시간대

CRON 식과 함께 사용하는 기본 표준 시간대는 UTC(협정 세계시)입니다. 다른 표준 시간대를 기반으로 하는 CRON 식을 사용하려면 `WEBSITE_TIME_ZONE`이라는 함수 앱에 대한 새 앱 설정을 만듭니다. [Microsoft 표준 시간대 색인](https://technet.microsoft.com/library/cc749073(v=ws.10).aspx)에 나온 대로 값을 원하는 표준 시간대의 이름으로 설정합니다. 

예를 들어 *동부 표준시*는 UTC-05:00입니다. 타이머 트리거를 매일 오전 10시 EST에 발생하도록 하려면 UTC 표준 시간대를 반영하는 다음과 같은 CRON 식을 사용합니다.

```json
"schedule": "0 0 15 * * *",
``` 

또는 `WEBSITE_TIME_ZONE`이라는 함수 앱에 대한 새 앱 설정을 추가하고 값을 **동부 표준시**로 설정할 수도 있습니다.  그런 다음 오전 10시 EST에 대해 다음과 같은 CRON 식을 사용할 수 있습니다. 

```json
"schedule": "0 0 10 * * *",
``` 
### <a name="cron-examples"></a>CRON 예제

Azure Functions에서 타이머 트리거를 사용할 수 있는 CRON 식의 몇 가지 예는 다음과 같습니다. 

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

## <a name="usage"></a>사용

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

## <a name="scale-out"></a>스케일 아웃

타이머 트리거는 다중 인스턴스 확장을 지원합니다. 특정 타이머 함수의 단일 인스턴스는 모든 인스턴스에 대해 실행됩니다.

## <a name="next-steps"></a>다음 단계

> [!div class="nextstepaction"]
> [타이머 트리거를 사용하는 빠른 시작으로 이동](functions-create-scheduled-function.md)

> [!div class="nextstepaction"]
> [Azure Functions 트리거 및 바인딩에 대한 자세한 정보](functions-triggers-bindings.md)
