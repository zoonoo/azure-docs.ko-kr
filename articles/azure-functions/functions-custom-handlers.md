---
title: Azure Functions 사용자 지정 처리기(미리 보기)
description: 모든 언어 또는 런타임 버전에서 Azure 함수를 사용하는 방법을 알아봅니다.
author: craigshoemaker
ms.author: cshoe
ms.date: 3/18/2020
ms.topic: article
ms.openlocfilehash: 5abc216e182d7becd9d6f42e0f566ee96d09c2a5
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79479255"
---
# <a name="azure-functions-custom-handlers-preview"></a>Azure Functions 사용자 지정 처리기(미리 보기)

모든 함수 앱은 언어별 처리기에 의해 실행됩니다. Azure Functions는 기본적으로 많은 [언어 처리기를](./supported-languages.md) 지원하지만 앱 실행 환경에 대한 추가 제어를 원하는 경우가 있습니다. 사용자 지정 처리기는 이 추가 컨트롤을 제공합니다.

사용자 지정 처리기는 Functions 호스트에서 이벤트를 수신하는 경량 웹 서버입니다. HTTP 프리미티브를 지원하는 모든 언어는 사용자 지정 처리기를 구현할 수 있습니다.

사용자 지정 처리기는 다음을 수행하려는 상황에 가장 적합합니다.

- 공식적으로 지원되는 언어를 넘어 언어로 함수 앱 구현
- 기본적으로 지원되지 않는 언어 버전 또는 런타임에서 Functions 앱 구현
- 앱 실행 환경을 세부적으로 제어

사용자 지정 처리기를 사용하면 확장 번들을 통해 모든 [트리거 및 입력 및 출력 바인딩이](./functions-triggers-bindings.md) [지원됩니다.](./functions-bindings-register.md)

## <a name="overview"></a>개요

다음 다이어그램은 사용자 지정 처리기로 구현된 Functions 호스트와 웹 서버 간의 관계를 보여 주며 있습니다.

![Azure Functions 사용자 지정 처리기 개요](./media/functions-custom-handlers/azure-functions-custom-handlers-overview.png)

- 이벤트는 Functions 호스트로 전송된 요청을 트리거합니다. 이 이벤트는 원시 HTTP 페이로드(바인딩이 없는 HTTP 트리거 함수의 경우) 또는 함수에 대한 입력 바인딩 데이터를 보유하는 페이로드를 전달합니다.
- 그런 다음 함수 호스트는 [요청 페이로드를](#request-payload)실행하여 웹 서버에 요청을 프록시합니다.
- 웹 서버는 개별 함수를 실행하고 [응답 페이로드를](#response-payload) Functions 호스트에 반환합니다.
- Functions 호스트는 응답을 대상에 대한 출력 바인딩 페이로드로 프록시합니다.

사용자 지정 처리기로 구현된 Azure Functions 앱은 몇 가지 규칙에 따라 *host.json* 및 *function.json* 파일을 구성해야 합니다.

## <a name="application-structure"></a>애플리케이션 구조

사용자 지정 처리기를 구현하려면 응용 프로그램에 다음과 같은 측면이 필요합니다.

- 앱의 루트에 있는 *host.json* 파일
- 각 함수에 대한 *function.json* 파일(함수 이름과 일치하는 폴더 내부)
- 웹 서버를 실행하는 명령, 스크립트 또는 실행

다음 다이어그램에서는 이러한 파일이 "order"라는 함수에 대해 파일 시스템에서 어떻게 보이는지 보여 주며 있습니다.

```bash
| /order
|   function.json
|
| host.json
```

### <a name="configuration"></a>Configuration

응용 프로그램은 *host.json* 파일을 통해 구성됩니다. 이 파일은 HTTP 이벤트를 처리할 수 있는 웹 서버를 가리켜 요청을 보낼 위치를 함수 호스트에 알려줍니다.

사용자 지정 처리기는 `httpWorker` 섹션을 통해 웹 서버를 실행하는 방법에 대한 세부 정보가 있는 *host.json* 파일을 구성하여 정의됩니다.

```json
{
    "version": "2.0",
    "httpWorker": {
        "description": {
            "defaultExecutablePath": "server.exe"
        }
    }
}
```

단면은 `httpWorker` `defaultExecutablePath`에 정의된 대상을 가리킵니다. 실행 대상은 명령, 실행 파일 또는 웹 서버가 구현되는 파일일 수 있습니다.

스크립팅된 `defaultExecutablePath` 앱의 경우 스크립트 언어의 `defaultWorkerPath` 런타임을 가리키고 스크립트 파일 위치를 가리킵니다. 다음 예제에서는 Node.js의 JavaScript 앱이 사용자 지정 처리기로 구성되는 방법을 보여 주며 있습니다.

```json
{
    "version": "2.0",
    "httpWorker": {
        "description": {
            "defaultExecutablePath": "node",
            "defaultWorkerPath": "server.js"
        }
    }
}
```

`arguments` 배열을 사용하여 인수를 전달할 수도 있습니다.

```json
{
    "version": "2.0",
    "httpWorker": {
        "description": {
            "defaultExecutablePath": "node",
            "defaultWorkerPath": "server.js",
            "arguments": [ "--argument1", "--argument2" ]
        }
    }
}
```

인수는 많은 디버깅 설정에 필요합니다. 자세한 내용은 [디버깅](#debugging) 섹션을 참조하십시오.

> [!NOTE]
> *host.json* 파일은 실행 중인 웹 서버와 디렉터리 구조의 수준이 같아야 합니다. 일부 언어 및 도구 체인은 기본적으로 이 파일을 응용 프로그램 루트에 배치하지 않을 수 있습니다.

#### <a name="bindings-support"></a>바인딩 지원

*host.json* 파일에서 [확장 번들을](./functions-bindings-register.md) 참조하여 입력 및 출력 바인딩과 함께 표준 트리거를 사용할 수 있습니다.

### <a name="function-metadata"></a>함수 메타데이터

사용자 지정 처리기와 함께 사용할 경우 *function.json* 내용은 다른 컨텍스트에서 함수를 정의하는 방법과 다르지 않습니다. 유일한 요구 사항은 *function.json* 파일이 함수 이름과 일치하도록 명명된 폴더에 있어야 한다는 것입니다.

### <a name="request-payload"></a>페이로드 요청

순수 HTTP 함수에 대한 요청 페이로드는 원시 HTTP 요청 페이로드입니다. 순수 HTTP 함수는 HTTP 응답을 반환하는 입력 또는 출력 바인딩이 없는 함수로 정의됩니다.

입력, 출력 바인딩을 포함하거나 HTTP 이외의 이벤트 소스를 통해 트리거되는 다른 유형의 함수에는 사용자 지정 요청 페이로드가 있습니다.

다음 코드는 샘플 요청 페이로드를 나타냅니다. 페이로드에는 두 멤버가 있는 JSON 구조가 `Data` 포함됩니다. `Metadata`

멤버에는 `Data` *function.json* 파일의 바인딩 배열에 정의된 입력 및 트리거 이름을 일치시키는 키가 포함됩니다.

멤버에는 `Metadata` [이벤트 원본에서 생성된 메타데이터가](./functions-bindings-expressions-patterns.md#trigger-metadata)포함됩니다.

다음 *function.json* 파일에 정의된 바인딩이 지정되어 있습니다.

```json
{
  "bindings": [
    {
      "name": "myQueueItem",
      "type": "queueTrigger",
      "direction": "in",
      "queueName": "messages-incoming",
      "connection": "AzureWebJobsStorage"
    },
    {
      "name": "$return",
      "type": "queue",
      "direction": "out",
      "queueName": "messages-outgoing",
      "connection": "AzureWebJobsStorage"
    }
  ]
}
```

이 예제와 유사한 요청 페이로드가 반환됩니다.

```json
{
    "Data": {
        "myQueueItem": "{ message: \"Message sent\" }"
    },
    "Metadata": {
        "DequeueCount": 1,
        "ExpirationTime": "2019-10-16T17:58:31+00:00",
        "Id": "800ae4b3-bdd2-4c08-badd-f08e5a34b865",
        "InsertionTime": "2019-10-09T17:58:31+00:00",
        "NextVisibleTime": "2019-10-09T18:08:32+00:00",
        "PopReceipt": "AgAAAAMAAAAAAAAAAgtnj8x+1QE=",
        "sys": {
            "MethodName": "QueueTrigger",
            "UtcNow": "2019-10-09T17:58:32.2205399Z",
            "RandGuid": "24ad4c06-24ad-4e5b-8294-3da9714877e9"
        }
    }
}
```

### <a name="response-payload"></a>응답 페이로드

규칙에 따라 함수 응답은 키/값 쌍으로 서식이 지정됩니다. 지원되는 키는 다음과 같습니다.

| <nobr>페이로드 키</nobr>   | 데이터 형식 | 설명                                                      |
| ------------- | --------- | ------------------------------------------------------------ |
| `Outputs`     | JSON      | *함수.json* 파일 `bindings` 배열에 정의된 응답 값을 보유합니다.<br /><br />예를 들어 함수가 "Blob"이라는 이름의 Blob 저장소 출력 바인딩으로 구성된 `Outputs` `blob`경우 Blob 값으로 설정된 키라는 키가 포함됩니다. |
| `Logs`        | array     | 메시지는 함수 호출 로그에 나타납니다.<br /><br />Azure에서 실행될 때 메시지는 응용 프로그램 인사이트에 나타납니다. |
| `ReturnValue` | 문자열    | 출력이 *function.json* 파일에서와 `$return` 같이 구성될 때 응답을 제공하는 데 사용됩니다. |

샘플 [페이로드에 대한 예제를](#bindings-implementation)참조하십시오.

## <a name="examples"></a>예

사용자 지정 처리기는 HTTP 이벤트를 지원하는 모든 언어로 구현할 수 있습니다. Azure Functions는 [JavaScript 및 Node.js를 완전히 지원하지만](./functions-reference-node.md)다음 예제에서는 명령을 위해 Node.js에서 JavaScript를 사용하여 사용자 지정 처리기를 구현하는 방법을 보여 주며 있습니다.

> [!TIP]
> 다른 언어로 사용자 지정 처리기를 구현하는 방법을 학습하는 방법에 대한 가이드이지만 여기에 표시된 Node.js 기반 예제는 지원되지 않는 Node.js 버전에서 Functions 앱을 실행하려는 경우에도 유용할 수 있습니다.

## <a name="http-only-function"></a>HTTP 전용 기능

다음 예제에서는 추가 바인딩 또는 출력 없이 HTTP 트리거된 함수를 구성 하는 방법을 보여 줍니다. 이 예제에서 구현된 시나리오에는 `http` `GET` 또는 `POST` 를 허용하는 함수가 있습니다.

다음 코드 조각은 함수에 대한 요청이 구성되는 방법을 나타냅니다.

```http
POST http://127.0.0.1:7071/api/hello HTTP/1.1
content-type: application/json

{
  "message": "Hello World!"
}
```

<a id="hello-implementation" name="hello-implementation"></a>

### <a name="implementation"></a>구현

*http라는*폴더에서 *function.json* 파일은 HTTP 트리거 기능을 구성합니다.

```json
{
  "bindings": [
    {
      "type": "httpTrigger",
      "direction": "in",
      "name": "req",
      "methods": ["get", "post"]
    },
    {
      "type": "http",
      "direction": "out",
      "name": "res"
    }
  ]
}
```

함수는 두 요청을 모두 `GET` `POST` 수락하도록 구성되며 결과 값은 `res`명명된 인수를 통해 제공됩니다.

앱의 루트에서 *host.json* 파일은 Node.js를 실행하고 `server.js` 파일을 가리키도록 구성됩니다.

```json
{
    "version": "2.0",
    "httpWorker": {
        "description": {
            "defaultExecutablePath": "node",
            "defaultWorkerPath": "server.js"
        }
    }
}
```

파일 *server.js* 파일은 웹 서버 및 HTTP 기능을 구현합니다.

```javascript
const express = require("express");
const app = express();

app.use(express.json());

const PORT = process.env.FUNCTIONS_HTTPWORKER_PORT;

const server = app.listen(PORT, "localhost", () => {
  console.log(`Your port is ${PORT}`);
  const { address: host, port } = server.address();
  console.log(`Example app listening at http://${host}:${port}`);
});

app.get("/hello", (req, res) => {
  res.json("Hello World!");
});

app.post("/hello", (req, res) => {
  res.json({ value: req.body });
});
```

이 예제에서 Express는 HTTP 이벤트를 처리하는 웹 서버를 만드는 데 사용되며 `FUNCTIONS_HTTPWORKER_PORT`을 통해 요청을 수신하도록 설정됩니다.

함수는 의 `/hello`경로에 정의됩니다. `GET`요청은 간단한 JSON 개체를 반환하여 처리되며 `POST` 요청은 `req.body`을 통해 요청 본문에 액세스할 수 있습니다.

여기서 주문 함수에 대한 `/hello` 경로는 Functions 호스트가 사용자 지정 처리기에 요청을 프록시하기 때문이 아닙니다. `/api/hello`

>[!NOTE]
>는 `FUNCTIONS_HTTPWORKER_PORT` 함수를 호출하는 데 사용되는 공용 마주보고 포트가 아닙니다. 이 포트는 Functions 호스트에서 사용자 지정 처리기를 호출하는 데 사용됩니다.

## <a name="function-with-bindings"></a>바인딩이 있는 기능

이 예제에서 구현된 시나리오에는 `order` 제품 주문을 `POST` 나타내는 페이로드를 포함하는 함수가 있습니다. 주문이 함수에 게시되면 큐 저장소 메시지가 만들어지고 HTTP 응답이 반환됩니다.

```http
POST http://127.0.0.1:7071/api/order HTTP/1.1
content-type: application/json

{
  "id": 1005,
  "quantity": 2,
  "color": "black"
}
```

<a id="bindings-implementation" name="bindings-implementation"></a>

### <a name="implementation"></a>구현

*순서라는*폴더에서 *function.json* 파일은 HTTP 트리거 함수를 구성합니다.

```json
{
  "bindings": [
    {
      "type": "httpTrigger",
      "authLevel": "function",
      "direction": "in",
      "name": "req",
      "methods": ["post"]
    },
    {
      "type": "http",
      "direction": "out",
      "name": "res"
    },
    {
      "type": "queue",
      "name": "message",
      "direction": "out",
      "queueName": "orders",
      "connection": "AzureWebJobsStorage"
    }
  ]
}

```

이 함수는 [HTTP 응답을](./functions-bindings-http-webhook-output.md) 반환하고 Queue [저장소](./functions-bindings-storage-queue-output.md) 메시지를 출력하는 HTTP [트리거 함수로](./functions-bindings-http-webhook-trigger.md) 정의됩니다.

앱의 루트에서 *host.json* 파일은 Node.js를 실행하고 `server.js` 파일을 가리키도록 구성됩니다.

```json
{
    "version": "2.0",
    "httpWorker": {
        "description": {
            "defaultExecutablePath": "node",
            "defaultWorkerPath": "server.js"
        }
    }
}
```

파일 *server.js* 파일은 웹 서버 및 HTTP 기능을 구현합니다.

```javascript
const express = require("express");
const app = express();

app.use(express.json());

const PORT = process.env.FUNCTIONS_HTTPWORKER_PORT;

const server = app.listen(PORT, "localhost", () => {
  console.log(`Your port is ${PORT}`);
  const { address: host, port } = server.address();
  console.log(`Example app listening at http://${host}:${port}`);
});

app.post("/order", (req, res) => {
  const message = req.body.Data.req.Body;
  const response = {
    Outputs: {
      message: message,
      res: {
        statusCode: 200,
        body: "Order complete"
      }
    },
    Logs: ["order processed"]
  };
  res.json(response);
});
```

이 예제에서 Express는 HTTP 이벤트를 처리하는 웹 서버를 만드는 데 사용되며 `FUNCTIONS_HTTPWORKER_PORT`을 통해 요청을 수신하도록 설정됩니다.

함수는 의 `/order` 경로에 정의됩니다.  여기서 주문 함수에 대한 `/order` 경로는 Functions 호스트가 사용자 지정 처리기에 요청을 프록시하기 때문이 아닙니다. `/api/order`

`POST` 요청이 이 함수로 전송되면 데이터는 다음과 같은 몇 가지 지점을 통해 노출됩니다.

- 요청 본문을 통해 사용할 수 있습니다.`req.body`
- 함수에 게시된 데이터는 다음을 통해 사용할 수 있습니다.`req.body.Data.req.Body`

함수의 응답은 `Outputs` 멤버가 *function.json* 파일에 정의된 출력과 일치하는 JSON 값을 보유하는 키/값 쌍으로 서식이 지정됩니다.

이 `message` 함수는 요청에서 들어온 메시지와 예상되는 `res` HTTP 응답과 동일하게 설정하여 메시지를 큐 저장소에 출력하고 HTTP 응답을 반환합니다.

## <a name="debugging"></a>디버깅

Functions 사용자 지정 처리기 앱을 디버깅하려면 디버깅을 사용하려면 언어 및 런타임에 적합한 인수를 추가해야 합니다.

예를 들어 Node.js 응용 프로그램을 디버깅하려면 `--inspect` 플래그가 *host.json* 파일에서 인수로 전달됩니다.

```json
{
    "version": "2.0",
    "httpWorker": {
        "description": {
            "defaultExecutablePath": "node",
            "defaultWorkerPath": "server.js",
            "arguments": [ "--inspect" ]
        }
    }
}
```

> [!NOTE]
> 디버깅 구성은 *host.json* 파일의 일부이며 프로덕션에 배포하기 전에 일부 인수를 제거해야 할 수 있습니다.

이 구성을 사용하면 다음 명령을 사용하여 Function의 호스트 프로세스를 시작할 수 있습니다.

```bash
func host start
```

프로세스가 시작되면 디버거를 연결하고 중단점을 적중할 수 있습니다.

### <a name="visual-studio-code"></a>Visual Studio Code

다음 예제는 앱을 Visual Studio 코드 디버거에 연결하도록 *launch.json* 파일을 설정하는 방법을 보여 주는 샘플 구성입니다.

이 예제는 Node.js에 대한 것이므로 다른 언어 또는 런타임에 대해 이 예제를 변경해야 할 수 있습니다.

```json
{
  "version": "0.2.0",
  "configurations": [
    {
      "name": "Attach to Node Functions",
      "type": "node",
      "request": "attach",
      "port": 9229,
      "preLaunchTask": "func: host start"
    }
  ]
}
```

## <a name="deploying"></a>배포 중

사용자 지정 처리기를 거의 모든 Azure Functions 호스팅 옵션에 배포할 수 [있습니다(제한](#restrictions)참조). 처리기에 사용자 지정 종속성(예: 언어 런타임)이 필요한 경우 [사용자 지정 컨테이너를](./functions-create-function-linux-custom-image.md)사용해야 할 수 있습니다.

## <a name="restrictions"></a>제한

- 사용자 지정 처리기는 Linux 소비 계획에서 지원되지 않습니다.
- 웹 서버는 60초 이내에 시작해야 합니다.

## <a name="samples"></a>샘플

[사용자 지정 처리기 샘플 GitHub 리포지토리는](https://github.com/Azure-Samples/functions-custom-handlers) 다양한 언어로 함수를 구현하는 방법에 대한 예제를 참조하십시오.
