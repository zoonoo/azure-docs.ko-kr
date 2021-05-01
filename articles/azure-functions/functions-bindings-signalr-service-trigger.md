---
title: Azure Functions SignalR Service 트리거 바인딩
description: Azure Functions에서 SignalR Service 메시지를 보내는 방법에 대해 알아봅니다.
author: chenyl
ms.topic: reference
ms.custom: devx-track-csharp
ms.date: 05/11/2020
ms.author: chenyl
ms.openlocfilehash: 2482a26987ec142880acc51bf470d844655b6e3f
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/29/2021
ms.locfileid: "97763519"
---
# <a name="signalr-service-trigger-binding-for-azure-functions"></a>Azure Functions의 SignalR Service 바인딩

*SignalR* 트리거 바인딩을 사용하여 Azure SignalR Service에서 보낸 메시지에 응답합니다. 함수가 트리거되면 함수에 전달된 메시지는 json 개체로 구문 분석됩니다.

SignalR Service 서버리스 모드에서 SignalR Service는 [업스트림](../azure-signalr/concept-upstream.md) 기능을 사용하여 클라이언트에서 함수 앱으로 메시지를 보냅니다. 함수 앱은 SignalR Service 트리거 바인딩을 사용하여 클라이언트에서 받은 메시지를 처리합니다. 일반적인 아키텍처는 아래와 같습니다. :::image type="content" source="media/functions-bindings-signalr-service/signalr-trigger.png" alt-text="SignalR 트리거 아키텍처":::

설정 및 구성 세부 정보에 관한 내용은 [개요](functions-bindings-signalr-service.md)를 참조하세요.

## <a name="example"></a>예제

다음 예제에서는 트리거 바인딩을 사용하여 메시지를 받고 메시지를 로깅하는 함수를 보여 줍니다.

# <a name="c"></a>[C#](#tab/csharp)

C#에 대한 SignalR Service 트리거 바인딩에는 두 개의 프로그래밍 모델이 있습니다. 클래스 기반 모델과 기존 모델입니다. 클래스 기반 모델은 일관된 SignalR 서버 측 프로그래밍 환경을 제공할 수 있습니다. 기존 모델은 향상된 유연성 및 다른 함수 바인딩과 비슷한 환경을 제공합니다.

### <a name="with-class-based-model"></a>클래스 기반 모델 사용

자세한 내용은 [클래스 기반 모델](../azure-signalr/signalr-concept-serverless-development-config.md#class-based-model)을 참조하세요.

```cs
public class SignalRTestHub : ServerlessHub
{
    [FunctionName("SignalRTest")]
    public async Task SendMessage([SignalRTrigger]InvocationContext invocationContext, string message, ILogger logger)
    {
        logger.LogInformation($"Receive {message} from {invocationContext.ConnectionId}.");
    }
}
```

### <a name="with-traditional-model"></a>기존 모델 사용

기존 모델은 C#으로 개발된 Azure 함수의 규칙을 따릅니다. 규칙에 대해 잘 모르는 경우 [문서](./functions-dotnet-class-library.md)에서 알아볼 수 있습니다.

```cs
[FunctionName("SignalRTest")]
public static async Task Run([SignalRTrigger("SignalRTest", "messages", "SendMessage", parameterNames: new string[] {"message"})]InvocationContext invocationContext, string message, ILogger logger)
{
    logger.LogInformation($"Receive {message} from {invocationContext.ConnectionId}.");
}
```

#### <a name="use-attribute-signalrparameter-to-simplify-parameternames"></a>`[SignalRParameter]` 특성을 사용하여 `ParameterNames` 단순화

`ParameterNames` 매개변수를 사용하는 것이 다소 복잡하지만 `SignalRParameter` 제공을 통해 동일한 결과를 얻을 수 있습니다.

```cs
[FunctionName("SignalRTest")]
public static async Task Run([SignalRTrigger("SignalRTest", "messages", "SendMessage")]InvocationContext invocationContext, [SignalRParameter]string message, ILogger logger)
{
    logger.LogInformation($"Receive {message} from {invocationContext.ConnectionId}.");
}
```

# <a name="c-script"></a>[C# Script](#tab/csharp-script)

*function.json* 파일의 바인딩 데이터는 다음과 같습니다.

예제 function.json:

```json
{
    "type": "signalRTrigger",
    "name": "invocation",
    "hubName": "SignalRTest",
    "category": "messages",
    "event": "SendMessage",
    "parameterNames": [
        "message"
    ],
    "direction": "in"
}
```

C# 스크립트 코드는 다음과 같습니다.

```cs
#r "Microsoft.Azure.WebJobs.Extensions.SignalRService"
using System;
using Microsoft.Azure.WebJobs.Extensions.SignalRService;
using Microsoft.Extensions.Logging;

public static void Run(InvocationContext invocation, string message, ILogger logger)
{
    logger.LogInformation($"Receive {message} from {invocationContext.ConnectionId}.");
}
```

# <a name="javascript"></a>[JavaScript](#tab/javascript)

*function.json* 파일의 바인딩 데이터는 다음과 같습니다.

예제 function.json:

```json
{
    "type": "signalRTrigger",
    "name": "invocation",
    "hubName": "SignalRTest",
    "category": "messages",
    "event": "SendMessage",
    "parameterNames": [
        "message"
    ],
    "direction": "in"
}
```

JavaScript 코드는 다음과 같습니다.

```javascript
module.exports = function (context, invocation) {
    context.log(`Receive ${context.bindingData.message} from ${invocation.ConnectionId}.`)
    context.done();
};
```

# <a name="python"></a>[Python](#tab/python)

*function.json* 파일의 바인딩 데이터는 다음과 같습니다.

예제 function.json:

```json
{
    "type": "signalRTrigger",
    "name": "invocation",
    "hubName": "SignalRTest",
    "category": "messages",
    "event": "SendMessage",
    "parameterNames": [
        "message"
    ],
    "direction": "in"
}
```

다음은 Python 코드입니다.

```python
import logging
import json
import azure.functions as func

def main(invocation) -> None:
    invocation_json = json.loads(invocation)
    logging.info("Receive {0} from {1}".format(invocation_json['Arguments'][0], invocation_json['ConnectionId']))
```

---

## <a name="configuration"></a>구성

### <a name="signalrtrigger"></a>SignalRTrigger

다음 표에서는 *function.json* 파일 및 `SignalRTrigger` 특성에 설정된 바인딩 구성 속성을 설명합니다.

|function.json 속성 | 특성 속성 |Description|
|---------|---------|----------------------|
|**type**| 해당 없음 | `SignalRTrigger`로 설정해야 합니다.|
|**direction**| 해당 없음 | `in`로 설정해야 합니다.|
|**name**| 해당 없음 | 트리거 호출 컨텍스트 개체의 함수 코드에 사용되는 변수 이름입니다. |
|**hubName**|**HubName**| 이 값은 트리거할 함수를 위해 반드시 SignalR 허브 이름으로 설정해야 합니다.|
|**category**|**범주**| 이 값은 트리거할 함수를 위해 반드시 메시지의 범주로 설정해야 합니다. 범주는 다음 값 중 하나가 될 수 있습니다. <ul><li>**연결**: *연결됨* 및 *연결 끊김* 이벤트 포함</li><li>**메시지**: *연결* 범주에 있는 이벤트를 제외한 모든 이벤트 포함</li></ul> |
|**event**|**이벤트**| 이 값은 트리거할 함수를 위해 반드시 메시지의 이벤트로 설정해야 합니다. *메시지* 범주의 경우 이벤트는 클라이언트에서 보내는 [호출 메시지](https://github.com/dotnet/aspnetcore/blob/master/src/SignalR/docs/specs/HubProtocol.md#invocation-message-encoding)의 *대상* 입니다. *연결* 범주에는 *연결됨* 및 *연결 끊김* 만 사용됩니다. |
|**parameterNames**|**ParameterNames**| (선택 사항)매개 변수에 바인딩되는 이름 목록입니다. |
|**connectionStringSetting**|**ConnectionStringSetting**| SignalR Service 연결 문자열("AzureSignalRConnectionString"에 대한 기본값)을 포함하는 앱 설정의 이름 |

## <a name="payload"></a>페이로드

트리거 입력 형식은 `InvocationContext` 또는 사용자 지정 형식으로 선언됩니다. `InvocationContext` 선택 시, 요청 콘텐츠에 대한 모든 액세스 권한을 갖게 됩니다. 사용자 지정 형식의 경우 런타임은 JSON 요청 본문의 구문을 분석하여 개체 속성을 설정하려고 합니다.

### <a name="invocationcontext"></a>InvocationContext

InvocationContext는 SignalR Service에서 보내는 메시지의 모든 콘텐츠를 포함합니다.

|InvocationContext의 속성 | Description|
|------------------------------|------------|
|인수| *메시지* 범주에 사용할 수 있습니다. [호출 메시지](https://github.com/dotnet/aspnetcore/blob/master/src/SignalR/docs/specs/HubProtocol.md#invocation-message-encoding)의 *인수* 를 포함합니다.|
|Error| *연결 끊김* 이벤트에 사용할 수 있습니다. 오류 없이 연결이 닫혔거나 오류 메시지가 포함된 경우 비어 있을 수 있습니다.|
|허브| 메시지가 속한 허브 이름입니다.|
|범주| 메시지의 범주입니다.|
|이벤트| 메시지의 이벤트입니다.|
|ConnectionId| 메시지를 보내는 클라이언트의 연결 ID입니다.|
|UserId| 메시지를 보내는 클라이언트의 사용자 ID입니다.|
|헤더| 요청의 헤더입니다.|
|쿼리| 클라이언트가 서비스에 연결하는 경우 요청에 대한 쿼리입니다.|
|클레임| 클라이언트의 클레임입니다.|

## <a name="using-parameternames"></a>`ParameterNames` 사용

`SignalRTrigger`의 `ParameterNames` 속성을 사용하면 호출 메시지의 인수를 함수의 매개 변수에 바인딩할 수 있습니다. 정의한 이름은 다른 바인딩에서 [바인딩 식](../azure-functions/functions-bindings-expressions-patterns.md)의 일부로 사용하거나 코드에서 매개 변수로 사용할 수 있습니다. 이렇게 하면 `InvocationContext`의 인수에 더욱 편리하게 액세스할 수 있습니다.

두 개의 `message1`, `message2` 인수를 사용하여 Azure Function에서 `broadcast` 메서드를 호출하려는 JavaScript SignalR 클라이언트가 있다고 가정해 보겠습니다.

```javascript
await connection.invoke("broadcast", message1, message2);
```

`parameterNames` 설정 후에는 정의한 이름이 클라이언트 측에서 보낸 인수와 일치하게 됩니다. 

```cs
[SignalRTrigger(parameterNames: new string[] {"arg1, arg2"})]
```

그런 다음에는 `arg1`에는 `message1`의 콘텐츠가 포함되고 `arg2`에는 `message2`의 콘텐츠가 포함됩니다.


### <a name="remarks"></a>설명

매개 변수 바인딩의 경우 순서가 중요합니다. `ParameterNames` 사용 시, `ParameterNames`의 순서는 클라이언트에서 호출하는 인수 순서와 일치합니다. C#에서 `[SignalRParameter]` 특성을 사용하는 경우 Azure Function 메서드의 인수 순서는 클라이언트의 인수 순서와 일치합니다.

`ParameterNames` 및 `[SignalRParameter]` 특성은 동시에 사용할 수 **없습니다**. 동시에 사용할 경우 예외가 발생합니다.

## <a name="signalr-service-integration"></a>SignalR Service 통합

SignalR Service 트리거 바인딩을 사용하는 경우 SignalR Service에는 함수 앱에 액세스하기 위한 URL이 필요합니다. URL은 SignalR Service 측의 **업스트림 설정** 에서 구성되어야 합니다. 

:::image type="content" source="../azure-signalr/media/concept-upstream/upstream-portal.png" alt-text="업스트림 포털":::

SignalR Service 트리거를 사용하는 경우 URL은 단순하며 다음과 같은 형식으로 지정될 수 있습니다.

```http
<Function_App_URL>/runtime/webhooks/signalr?code=<API_KEY>
```

`Function_App_URL`의 경우 함수 앱의 개요 페이지에서 찾을 수 있고 `API_KEY`의 경우 Azure Function에서 생성합니다. `API_KEY`의 경우 함수 앱의 **앱 키** 블레이드에 있는 `signalr_extension`에서 가져올 수 있습니다.
:::image type="content" source="media/functions-bindings-signalr-service/signalr-keys.png" alt-text="API 키":::

둘 이상의 함수 앱을 하나의 SignalR Service와 함께 사용하려면 업스트림에서 복잡한 회람 규칙도 지원받을 수 있습니다. [업스트림 설정](../azure-signalr/concept-upstream.md)에서 자세한 내용을 확인하세요.

## <a name="step-by-step-sample"></a>단계별 샘플

GitHub의 [양방향 채팅방 샘플](https://github.com/aspnet/AzureSignalR-samples/tree/master/samples/BidirectionChat)을 따라 SignalR Service 트리거 바인딩 및 업스트림 기능을 사용하여 함수 앱에 채팅방을 배포할 수 있습니다.

## <a name="next-steps"></a>다음 단계

* [Azure SignalR Service를 사용하여 Azure Functions 개발 및 구성](../azure-signalr/signalr-concept-serverless-development-config.md)
* [SignalR Service 트리거 바인딩 샘플](https://github.com/aspnet/AzureSignalR-samples/tree/master/samples/BidirectionChat)
