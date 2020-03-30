---
title: Azure 함수에 대한 Azure 이벤트 그리드 출력 바인딩
description: Azure 함수에서 이벤트 그리드 이벤트를 보내는 방법을 알아봅니다.
author: craigshoemaker
ms.topic: reference
ms.date: 02/14/2020
ms.author: cshoe
ms.custom: fasttrack-edit
ms.openlocfilehash: e7a2611312ffc33703dd5cc9d0a2d7142ddb0532
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/27/2020
ms.locfileid: "77368949"
---
# <a name="azure-event-grid-output-binding-for-azure-functions"></a>Azure 함수에 대한 Azure 이벤트 그리드 출력 바인딩

이벤트 그리드 출력 바인딩을 사용하여 사용자 지정 토픽에 이벤트를 작성합니다. [사용자 지정 항목에 대한](../event-grid/security-authentication.md#custom-topic-publishing)유효한 액세스 키가 있어야 합니다.

설정 및 구성 세부 정보에 대한 자세한 내용은 [개요를](./functions-bindings-event-grid.md)참조하십시오.

> [!NOTE]
> Event Grid 출력 바인딩은 공유 액세스 서명(SAS 토큰)을 지원하지 않습니다. 토픽의 액세스 키를 사용해야 합니다.

> [!IMPORTANT]
> 이벤트 그리드 출력 바인딩은 함수 2.x 이상에서만 사용할 수 있습니다.

## <a name="example"></a>예제

# <a name="c"></a>[C #](#tab/csharp)

다음 예제에서는 메서드 반환 값을 출력으로 사용 하 여 Event Grid 사용자 지정 토픽에 메시지를 작성 하는 [C# 함수를](functions-dotnet-class-library.md) 보여 주었습니다.

```csharp
[FunctionName("EventGridOutput")]
[return: EventGrid(TopicEndpointUri = "MyEventGridTopicUriSetting", TopicKeySetting = "MyEventGridTopicKeySetting")]
public static EventGridEvent Run([TimerTrigger("0 */5 * * * *")] TimerInfo myTimer, ILogger log)
{
    return new EventGridEvent("message-id", "subject-name", "event-data", "event-type", DateTime.UtcNow, "1.0");
}
```

다음 예제에서는 인터페이스를 `IAsyncCollector` 사용하여 메시지 일괄 처리를 보내는 방법을 보여 줍니다.

```csharp
[FunctionName("EventGridAsyncOutput")]
public static async Task Run(
    [TimerTrigger("0 */5 * * * *")] TimerInfo myTimer,
    [EventGrid(TopicEndpointUri = "MyEventGridTopicUriSetting", TopicKeySetting = "MyEventGridTopicKeySetting")]IAsyncCollector<EventGridEvent> outputEvents,
    ILogger log)
{
    for (var i = 0; i < 3; i++)
    {
        var myEvent = new EventGridEvent("message-id-" + i, "subject-name", "event-data", "event-type", DateTime.UtcNow, "1.0");
        await outputEvents.AddAsync(myEvent);
    }
}
```

# <a name="c-script"></a>[C# 스크립트](#tab/csharp-script)

다음 예제에서는 *function.json* 파일에서 이벤트 그리드 출력 바인딩 데이터를 보여 주다.

```json
{
    "type": "eventGrid",
    "name": "outputEvent",
    "topicEndpointUri": "MyEventGridTopicUriSetting",
    "topicKeySetting": "MyEventGridTopicKeySetting",
    "direction": "out"
}
```

다음은 하나의 이벤트를 만드는 C# 스크립트 코드입니다.

```cs
#r "Microsoft.Azure.EventGrid"
using System;
using Microsoft.Azure.EventGrid.Models;
using Microsoft.Extensions.Logging;

public static void Run(TimerInfo myTimer, out EventGridEvent outputEvent, ILogger log)
{
    outputEvent = new EventGridEvent("message-id", "subject-name", "event-data", "event-type", DateTime.UtcNow, "1.0");
}
```

다음은 여러 이벤트를 만드는 C# 스크립트 코드입니다.

```cs
#r "Microsoft.Azure.EventGrid"
using System;
using Microsoft.Azure.EventGrid.Models;
using Microsoft.Extensions.Logging;

public static void Run(TimerInfo myTimer, ICollector<EventGridEvent> outputEvent, ILogger log)
{
    outputEvent.Add(new EventGridEvent("message-id-1", "subject-name", "event-data", "event-type", DateTime.UtcNow, "1.0"));
    outputEvent.Add(new EventGridEvent("message-id-2", "subject-name", "event-data", "event-type", DateTime.UtcNow, "1.0"));
}
```

# <a name="javascript"></a>[자바 스크립트](#tab/javascript)

다음 예제에서는 *function.json* 파일에서 이벤트 그리드 출력 바인딩 데이터를 보여 주다.

```json
{
    "type": "eventGrid",
    "name": "outputEvent",
    "topicEndpointUri": "MyEventGridTopicUriSetting",
    "topicKeySetting": "MyEventGridTopicKeySetting",
    "direction": "out"
}
```

단일 이벤트를 만드는 JavaScript 코드는 다음과 같습니다.

```javascript
module.exports = async function (context, myTimer) {
    var timeStamp = new Date().toISOString();

    context.bindings.outputEvent = {
        id: 'message-id',
        subject: 'subject-name',
        dataVersion: '1.0',
        eventType: 'event-type',
        data: "event-data",
        eventTime: timeStamp
    };
    context.done();
};
```

여러 이벤트를 만드는 JavaScript 코드는 다음과 같습니다.

```javascript
module.exports = function(context) {
    var timeStamp = new Date().toISOString();

    context.bindings.outputEvent = [];

    context.bindings.outputEvent.push({
        id: 'message-id-1',
        subject: 'subject-name',
        dataVersion: '1.0',
        eventType: 'event-type',
        data: "event-data",
        eventTime: timeStamp
    });
    context.bindings.outputEvent.push({
        id: 'message-id-2',
        subject: 'subject-name',
        dataVersion: '1.0',
        eventType: 'event-type',
        data: "event-data",
        eventTime: timeStamp
    });
    context.done();
};
```

# <a name="python"></a>[Python](#tab/python)

파이썬에는 이벤트 그리드 출력 바인딩을 사용할 수 없습니다.

# <a name="java"></a>[Java](#tab/java)

Java에서 이벤트 그리드 출력 바인딩을 사용할 수 없습니다.

---

## <a name="attributes-and-annotations"></a>특성 및 주석

# <a name="c"></a>[C #](#tab/csharp)

[C# 클래스 라이브러리의](functions-dotnet-class-library.md)경우 [EventGridAttribute](https://github.com/Azure/azure-functions-eventgrid-extension/blob/dev/src/EventGridExtension/OutputBinding/EventGridAttribute.cs) 특성을 사용합니다.

특성의 생성자는 사용자 지정 항목의 이름과 토픽 키가 포함된 앱 설정의 이름을 포함하는 앱 설정의 이름을 지정합니다. 이러한 설정에 대한 자세한 내용은 [출력 - 구성](#configuration)을 참조하세요. `EventGrid` 특성 예제는 다음과 같습니다.

```csharp
[FunctionName("EventGridOutput")]
[return: EventGrid(TopicEndpointUri = "MyEventGridTopicUriSetting", TopicKeySetting = "MyEventGridTopicKeySetting")]
public static string Run([TimerTrigger("0 */5 * * * *")] TimerInfo myTimer, ILogger log)
{
    ...
}
```

전체 예제를 보려면 [예제를](#example)참조하십시오.

# <a name="c-script"></a>[C# 스크립트](#tab/csharp-script)

특성은 C# 스크립트에서 지원되지 않습니다.

# <a name="javascript"></a>[자바 스크립트](#tab/javascript)

속성은 자바 스크립트에서 지원되지 않습니다.

# <a name="python"></a>[Python](#tab/python)

파이썬에는 이벤트 그리드 출력 바인딩을 사용할 수 없습니다.

# <a name="java"></a>[Java](#tab/java)

Java에서 이벤트 그리드 출력 바인딩을 사용할 수 없습니다.

---

## <a name="configuration"></a>Configuration

다음 표에서는 *function.json* 파일및 특성에서 설정한 바인딩 `EventGrid` 구성 속성에 대해 설명합니다.

|function.json 속성 | 특성 속성 |설명|
|---------|---------|----------------------|
|**종류** | 해당 없음 | "eventGrid"로 설정해야 합니다. |
|**direction** | 해당 없음 | "out"으로 설정해야 합니다. 이 매개 변수는 사용자가 Azure Portal에서 바인딩을 만들 때 자동으로 설정됩니다. |
|**(이름)** | 해당 없음 | 이벤트를 나타내는 함수 코드에서 사용되는 변수 이름입니다. |
|**토픽엔드포구리** |**토픽엔드포구리** | 와 같은 `MyTopicEndpointUri`사용자 지정 항목에 대한 URI를 포함하는 앱 설정의 이름입니다. |
|**토픽키세팅** |**토픽키 세팅** | 사용자 지정 항목에 대한 액세스 키가 포함된 앱 설정의 이름입니다. |

[!INCLUDE [app settings to local.settings.json](../../includes/functions-app-settings-local.md)]

> [!IMPORTANT]
> `TopicEndpointUri` 구성 속성의 값을 사용자 지정 항목의 URI가 포함된 앱 설정의 이름으로 설정해야 합니다. 이 속성에서 사용자 지정 항목의 URI를 직접 지정하지 마십시오.

## <a name="usage"></a>사용

# <a name="c"></a>[C #](#tab/csharp)

`out EventGridEvent paramName`와 같은 메서드 매개 변수를 사용하여 메시지를 보냅니다. 여러 메시지를 쓰려면 `out EventGridEvent` 대신 `ICollector<EventGridEvent>` 또는 `IAsyncCollector<EventGridEvent>`를 사용할 수 있습니다.

# <a name="c-script"></a>[C# 스크립트](#tab/csharp-script)

`out EventGridEvent paramName`와 같은 메서드 매개 변수를 사용하여 메시지를 보냅니다. C# 스크립트에서 `paramName`은 *function.json*의 `name` 속성에 지정된 값입니다. 여러 메시지를 쓰려면 `out EventGridEvent` 대신 `ICollector<EventGridEvent>` 또는 `IAsyncCollector<EventGridEvent>`를 사용할 수 있습니다.

# <a name="javascript"></a>[자바 스크립트](#tab/javascript)

*function.json*의 `context.bindings.<name>` `<name>` `name` 속성에 지정된 값을 사용하여 출력 이벤트에 액세스합니다.

# <a name="python"></a>[Python](#tab/python)

파이썬에는 이벤트 그리드 출력 바인딩을 사용할 수 없습니다.

# <a name="java"></a>[Java](#tab/java)

Java에서 이벤트 그리드 출력 바인딩을 사용할 수 없습니다.

---

## <a name="next-steps"></a>다음 단계

* [이벤트 그리드 이벤트 디스패치](./functions-bindings-event-grid-trigger.md)
