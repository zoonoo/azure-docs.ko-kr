---
title: Azure Functions 사용자 지정 처리기
description: 모든 언어 또는 런타임 버전에서 Azure Functions를 사용하는 방법을 알아봅니다.
author: anthonychu
ms.author: antchu
ms.date: 12/1/2020
ms.topic: article
ms.openlocfilehash: 82166ca69db6dc2dc1b7562e23078609b01cb2ed
ms.sourcegitcommit: 8bca2d622fdce67b07746a2fb5a40c0c644100c6
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/09/2021
ms.locfileid: "111756026"
---
# <a name="azure-functions-custom-handlers"></a>Azure Functions 사용자 지정 처리기

모든 Functions 앱은 언어별 처리기를 통해 실행됩니다. Azure Functions에는 기본적으로 다양한 [언어 처리기](./supported-languages.md)가 있지만 다른 언어 또는 런타임을 사용하려는 경우가 있습니다.

사용자 지정 처리기는 Functions 호스트에서 이벤트를 받는 간단한 웹 서버입니다. HTTP 기본 형식을 지원하는 모든 언어는 사용자 지정 처리기를 구현할 수 있습니다.

사용자 지정 처리기는 다음과 같은 상황에 가장 적합합니다.

- 함수 앱을 현재 즉시 제공하지 않는 언어(예: Go 또는 Rust)로 구현합니다.
- 함수 앱을 현재 제공하지 않는 런타임(예: Deno)에 구현합니다.

사용자 지정 처리기를 사용하면 [확장 번들](./functions-bindings-register.md)을 통해 [트리거와 입력 및 출력 바인딩](./functions-triggers-bindings.md)을 사용할 수 있습니다.

[Go 및 Rust에서 빠른 시작](create-first-function-vs-code-other.md)을 사용하여 Azure Functions 사용자 지정 처리기를 시작합니다.

## <a name="overview"></a>개요

다음 다이어그램에서는 Functions 호스트와 사용자 지정 처리기로 구현된 웹 서버 간의 관계를 보여 줍니다.

![Azure Functions 사용자 지정 처리기 개요](./media/functions-custom-handlers/azure-functions-custom-handlers-overview.png)

1. 각 이벤트는 Functions 호스트에 보낸 요청을 트리거합니다. 이벤트는 Azure Functions에서 지원되는 모든 트리거입니다.
1. 그런 다음, Functions 호스트에서 [요청 페이로드](#request-payload)를 웹 서버에 보냅니다. 페이로드에는 함수에 대한 트리거, 입력 바인딩 데이터 및 기타 메타데이터가 포함됩니다.
1. 웹 서버는 개별 함수를 실행하고 [응답 페이로드](#response-payload)를 Functions 호스트에 반환합니다.
1. Functions 호스트는 처리를 위해 데이터를 응답에서 함수의 출력 바인딩으로 전달합니다.

사용자 지정 처리기로 구현된 Azure Functions 앱은 몇 가지 규칙에 따라 *host.json*, *local.settings.json* 및 *function.json* 파일을 구성해야 합니다.

## <a name="application-structure"></a>애플리케이션 구조

사용자 지정 처리기를 구현하려면 애플리케이션에 다음과 같은 측면이 필요합니다.

- 앱의 루트에 있는 *host.json* 파일
- 앱의 루트에 있는 *local.settings.json* 파일
- 각 함수에 대한 *function.json* 파일(함수 이름과 일치하는 폴더 내)
- 웹 서버를 실행하는 명령, 스크립트 또는 실행 파일

다음 다이어그램에서는 파일 시스템에서 "MyQueueFunction"이라는 함수 및 사용자 지정 처리기 실행 파일인 *handler.exe* 에 대한 파일이 표시되는 방식을 보여 줍니다.

```bash
| /MyQueueFunction
|   function.json
|
| host.json
| local.settings.json
| handler.exe
```

### <a name="configuration"></a>구성

애플리케이션은 *host.json* 및 *local.settings.json* 파일을 통해 구성됩니다.

#### <a name="hostjson"></a>host.json

*host.json* 은 HTTP 이벤트를 처리할 수 있는 웹 서버를 가리켜서 요청을 보낼 위치를 Functions 호스트에 알려줍니다.

사용자 지정 처리기는 `customHandler` 섹션을 통해 웹 서버를 실행하는 방법에 대한 세부 정보로 *host.json* 파일을 구성하여 정의합니다.

```json
{
  "version": "2.0",
  "customHandler": {
    "description": {
      "defaultExecutablePath": "handler.exe"
    }
  }
}
```

`customHandler` 섹션은 `defaultExecutablePath`에 정의된 대상을 가리킵니다. 실행 대상은 웹 서버가 구현되는 명령, 실행 파일 또는 파일일 수 있습니다.

`arguments` 배열을 사용하여 모든 인수를 실행 파일에 전달합니다. 인수는 `%%` 표기법을 사용하여 환경 변수(애플리케이션 설정)의 확장을 지원합니다.

또한 `workingDirectory`를 사용하여 실행 파일에서 사용하는 작업 디렉터리를 변경할 수 있습니다.

```json
{
  "version": "2.0",
  "customHandler": {
    "description": {
      "defaultExecutablePath": "app/handler.exe",
      "arguments": [
        "--database-connection-string",
        "%DATABASE_CONNECTION_STRING%"
      ],
      "workingDirectory": "app"
    }
  }
}
```

##### <a name="bindings-support"></a>바인딩 지원

입력 및 출력 바인딩과 함께 표준 트리거는 *host.json* 파일의 [확장 번들](./functions-bindings-register.md)을 참조하여 사용할 수 있습니다.

#### <a name="localsettingsjson"></a>local.settings.json

*local.settings.json* 은 함수 앱을 로컬로 실행할 때 사용되는 애플리케이션 설정을 정의합니다. 비밀이 포함될 수 있으므로 *local.settings.json* 은 원본 제어에서 제외해야 합니다. Azure에서는 애플리케이션 설정을 대신 사용합니다.

사용자 지정 처리기의 경우 *local.settings.json* 에서 `FUNCTIONS_WORKER_RUNTIME`을 `Custom`으로 설정합니다.

```json
{
  "IsEncrypted": false,
  "Values": {
    "FUNCTIONS_WORKER_RUNTIME": "Custom"
  }
}
```

### <a name="function-metadata"></a>함수 메타데이터

사용자 지정 처리기와 함께 사용하는 경우 *function.json* 의 내용은 다른 컨텍스트에서 함수를 정의하는 방법과 다르지 않습니다. 유일한 요구 사항은 *function.json* 파일이 함수 이름과 일치하도록 명명된 폴더에 있어야 한다는 것입니다.

다음 *function.json* 은 큐 트리거와 큐 출력 바인딩이 포함된 함수를 구성합니다. *MyQueueFunction* 이라는 폴더에 있으므로 *MyQueueFunction* 이라는 함수를 정의합니다.

**MyQueueFunction/function.json**

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

### <a name="request-payload"></a>요청 페이로드

큐 메시지를 받으면 Functions 호스트에서 HTTP post 요청을 페이로드가 본문에 있는 사용자 지정 처리기에 보냅니다.

다음 코드는 요청 페이로드 샘플을 나타냅니다. 페이로드에는 두 멤버(`Data` 및 `Metadata`)가 있는 JSON 구조체가 포함됩니다.

`Data` 멤버에는 *function.json* 파일의 바인딩 배열에 정의된 입력 및 트리거 이름과 일치하는 키가 포함됩니다.

`Metadata` 멤버에는 [이벤트 원본에서 생성된 메타데이터](./functions-bindings-expressions-patterns.md#trigger-metadata)가 포함됩니다.

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

규칙에 따라 함수 응답은 키/값 쌍 형식으로 지정됩니다. 지원되는 키는 다음과 같습니다.

| <nobr>페이로드 키</nobr>   | 데이터 형식 | 설명                                                      |
| ------------- | --------- | ------------------------------------------------------------ |
| `Outputs`     | object    | *function.json* 의 `bindings` 배열에 정의된 응답 값을 포함합니다.<br /><br />예를 들어 함수가 "myQueueOutput"이라는 큐 출력 바인딩으로 구성된 경우 `Outputs`에 `myQueueOutput`이라는 키가 포함됩니다. 이 키는 사용자 지정 처리기에서 큐에 보내는 메시지에 설정합니다. |
| `Logs`        | array     | 메시지가 Functions 호출 로그에 표시됩니다.<br /><br />Azure에서 실행하는 경우 메시지가 Application Insights에 표시됩니다. |
| `ReturnValue` | 문자열    | *function.json* 파일에서 출력을 `$return`으로 구성할 때 응답을 제공하는 데 사용됩니다. |

응답 페이로드의 예제는 다음과 같습니다.

```json
{
  "Outputs": {
    "res": {
      "body": "Message enqueued"
    },
    "myQueueOutput": [
      "queue message 1",
      "queue message 2"
    ]
  },
  "Logs": [
    "Log message 1",
    "Log message 2"
  ],
  "ReturnValue": "{\"hello\":\"world\"}"
}
```

## <a name="examples"></a>예

사용자 지정 처리기는 HTTP 이벤트 수신을 지원하는 모든 언어로 구현할 수 있습니다. 다음 예제에서는 Go 프로그래밍 언어를 사용하여 사용자 지정 처리기를 구현하는 방법을 보여 줍니다.

### <a name="function-with-bindings"></a>바인딩을 사용하는 함수

이 예제에서 구현된 시나리오에는 제품 주문을 나타내는 페이로드가 포함된 `POST`를 수락하는 `order`라는 함수가 있습니다. 주문이 함수에 게시되면 Queue Storage 메시지가 만들어지고 HTTP 응답이 반환됩니다.

<a id="bindings-implementation" name="bindings-implementation"></a>

#### <a name="implementation"></a>구현

*order* 라고 폴더에서 *function.json* 파일은 HTTP 트리거 함수를 구성합니다.

**order/function.json**

```json
{
  "bindings": [
    {
      "type": "httpTrigger",
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

이 함수는 [HTTP 응답](./functions-bindings-http-webhook-output.md)을 반환하고 [Queue Storage](./functions-bindings-storage-queue-output.md) 메시지를 출력하는 [HTTP 트리거 함수](./functions-bindings-http-webhook-trigger.md)로 정의됩니다.

앱의 루트에서 *host.json* 파일은 `handler.exe`(Linux 또는 macOS의 `handler`)라는 실행 파일을 실행하도록 구성됩니다.

```json
{
  "version": "2.0",
  "customHandler": {
    "description": {
      "defaultExecutablePath": "handler.exe"
    }
  },
  "extensionBundle": {
    "id": "Microsoft.Azure.Functions.ExtensionBundle",
    "version": "[1.*, 2.0.0)"
  }
}
```

이는 Functions 런타임에 보낸 HTTP 요청입니다.

```http
POST http://127.0.0.1:7071/api/order HTTP/1.1
Content-Type: application/json

{
  "id": 1005,
  "quantity": 2,
  "color": "black"
}
```

그런 다음, Functions 런타임에서 다음 HTTP 요청을 사용자 지정 처리기에 보냅니다.

```http
POST http://127.0.0.1:<FUNCTIONS_CUSTOMHANDLER_PORT>/order HTTP/1.1
Content-Type: application/json

{
  "Data": {
    "req": {
      "Url": "http://localhost:7071/api/order",
      "Method": "POST",
      "Query": "{}",
      "Headers": {
        "Content-Type": [
          "application/json"
        ]
      },
      "Params": {},
      "Body": "{\"id\":1005,\"quantity\":2,\"color\":\"black\"}"
    }
  },
  "Metadata": {
  }
}
```

> [!NOTE]
> 간단히 하기 위해 페이로드의 일부가 제거되었습니다.

*handler.exe* 는 웹 서버를 실행하고 Functions 호스트의 함수 호출 요청에 응답하는 컴파일된 Go 사용자 지정 처리기 프로그램입니다.

```go
package main

import (
    "encoding/json"
    "fmt"
    "log"
    "net/http"
    "os"
)

type InvokeRequest struct {
    Data     map[string]json.RawMessage
    Metadata map[string]interface{}
}

type InvokeResponse struct {
    Outputs     map[string]interface{}
    Logs        []string
    ReturnValue interface{}
}

func orderHandler(w http.ResponseWriter, r *http.Request) {
    var invokeRequest InvokeRequest

    d := json.NewDecoder(r.Body)
    d.Decode(&invokeRequest)

    var reqData map[string]interface{}
    json.Unmarshal(invokeRequest.Data["req"], &reqData)

    outputs := make(map[string]interface{})
    outputs["message"] = reqData["Body"]

    resData := make(map[string]interface{})
    resData["body"] = "Order enqueued"
    outputs["res"] = resData
    invokeResponse := InvokeResponse{outputs, nil, nil}

    responseJson, _ := json.Marshal(invokeResponse)

    w.Header().Set("Content-Type", "application/json")
    w.Write(responseJson)
}

func main() {
    customHandlerPort, exists := os.LookupEnv("FUNCTIONS_CUSTOMHANDLER_PORT")
    if !exists {
        customHandlerPort = "8080"
    }
    mux := http.NewServeMux()
    mux.HandleFunc("/order", orderHandler)
    fmt.Println("Go server Listening on: ", customHandlerPort)
    log.Fatal(http.ListenAndServe(":"+customHandlerPort, mux))
}
```

이 예제에서 사용자 지정 처리기는 웹 서버를 실행하여 HTTP 이벤트를 처리하고 `FUNCTIONS_CUSTOMHANDLER_PORT`를 통해 요청을 받도록 설정됩니다.

Functions 호스트는 `/api/order`에서 원래 HTTP 요청을 받았지만 함수 이름(폴더 이름)을 사용하여 사용자 지정 처리기를 호출합니다. 이 예제에서 함수는 `/order` 경로에 정의됩니다. 호스트는 `/order` 경로에 있는 HTTP 요청을 사용자 지정 처리기에 보냅니다.

`POST` 요청이 이 함수에 보내지면 HTTP 요청 본문을 통해 트리거 데이터와 함수 메타데이터를 사용할 수 있습니다. 원래 HTTP 요청 본문은 페이로드의 `Data.req.Body`에서 액세스할 수 있습니다.

함수의 응답은 키/값 쌍 형식으로 지정되며, 여기서 `Outputs` 멤버는 키가 *function.json* 파일에 정의된 출력과 일치하는 JSON 값을 포함합니다.

이 처리기에서 Functions 호스트에 반환하는 페이로드 예제는 다음과 같습니다.

```json
{
  "Outputs": {
    "message": "{\"id\":1005,\"quantity\":2,\"color\":\"black\"}",
    "res": {
      "body": "Order enqueued"
    }
  },
  "Logs": null,
  "ReturnValue": null
}
```

`message` 출력을 요청에서 제공된 주문 데이터와 동일하게 설정하여 함수에서 해당 주문 데이터를 구성된 큐에 출력합니다. Functions 호스트는 `res`로 구성된 HTTP 응답도 호출자에 반환합니다.

### <a name="http-only-function"></a>HTTP 전용 함수

추가 바인딩 또는 출력이 없는 HTTP 트리거 함수의 경우 사용자 지정 처리기 [요청](#request-payload) 및 [응답](#response-payload) 페이로드 대신 처리기가 HTTP 요청 및 응답으로 직접 작동하도록 할 수 있습니다. 이 동작은 *host.json* 에서 `enableForwardingHttpRequest` 설정을 사용하여 구성할 수 있습니다.

> [!IMPORTANT]
> 사용자 지정 처리기 기능의 기본 목적은 현재 Azure Functions에서 최고 수준의 지원이 없는 언어와 런타임을 사용하도록 설정하는 것입니다. 사용자 지정 처리기를 사용하여 웹 애플리케이션을 실행할 수 있지만 Azure Functions는 표준 역방향 프록시가 아닙니다. 응답 스트리밍, HTTP/2 및 WebSocket과 같은 일부 기능은 사용할 수 없습니다. 특정 헤더 및 경로와 같은 HTTP 요청의 일부 구성 요소가 제한될 수 있습니다. 또한 애플리케이션에서 과도한 [콜드 부팅](event-driven-scaling.md#cold-start)이 발생할 수 있습니다.
>
> 이러한 상황을 해결하려면 [Azure App Service](../app-service/overview.md)에서 웹앱을 실행하는 것이 좋습니다.

다음 예제에서는 추가 바인딩 또는 출력 없이 HTTP 트리거 함수를 구성하는 방법을 보여 줍니다. 이 예제에서 구현된 시나리오에는 `GET` 또는 `POST`를 수락하는 `hello`라는 함수가 있습니다.

<a id="hello-implementation" name="hello-implementation"></a>

#### <a name="implementation"></a>구현

*hello* 라는 폴더에서 *function.json* 파일은 HTTP 트리거 함수를 구성합니다.

**hello/function.json**

```json
{
  "bindings": [
    {
      "type": "httpTrigger",
      "authLevel": "anonymous",
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

이 함수는 `GET` 및 `POST` 요청을 모두 수락하도록 구성되며, 결과 값은 `res`라는 인수를 통해 제공됩니다.

앱의 루트에서 *host.json* 파일은 `handler.exe`를 실행하도록 구성되고 `enableForwardingHttpRequest`가 `true`로 설정됩니다.

```json
{
  "version": "2.0",
  "customHandler": {
    "description": {
      "defaultExecutablePath": "handler.exe"
    },
    "enableForwardingHttpRequest": true
  }
}
```

`enableForwardingHttpRequest`가 `true`이면 HTTP 전용 함수의 동작은 다음과 같은 방식으로 기본 사용자 지정 처리기의 동작과 다릅니다.

* HTTP 요청에는 사용자 지정 처리기 [요청](#request-payload) 페이로드가 포함되지 않습니다. 대신 Functions 호스트에서 원래 HTTP 요청의 복사본을 사용하여 처리기를 호출합니다.
* Functions 호스트는 쿼리 문자열 매개 변수를 포함하여 원래 요청과 동일한 경로를 사용하여 처리기를 호출합니다.
* Functions 호스트는 처리기의 HTTP 응답 복사본을 원래 요청에 대한 응답으로 반환합니다.

Functions 호스트에 대한 POST 요청은 다음과 같습니다. 그러면 Functions 호스트에서 요청의 복사본을 동일한 경로에 있는 사용자 지정 처리기에 보냅니다.

```http
POST http://127.0.0.1:7071/api/hello HTTP/1.1
Content-Type: application/json

{
  "message": "Hello World!"
}
```

*handler.go* 파일은 웹 서버와 HTTP 함수를 구현합니다.

```go
package main

import (
    "fmt"
    "io/ioutil"
    "log"
    "net/http"
    "os"
)

func helloHandler(w http.ResponseWriter, r *http.Request) {
    w.Header().Set("Content-Type", "application/json")
    if r.Method == "GET" {
        w.Write([]byte("hello world"))
    } else {
        body, _ := ioutil.ReadAll(r.Body)
        w.Write(body)
    }
}

func main() {
    customHandlerPort, exists := os.LookupEnv("FUNCTIONS_CUSTOMHANDLER_PORT")
    if !exists {
        customHandlerPort = "8080"
    }
    mux := http.NewServeMux()
    mux.HandleFunc("/api/hello", helloHandler)
    fmt.Println("Go server Listening on: ", customHandlerPort)
    log.Fatal(http.ListenAndServe(":"+customHandlerPort, mux))
}
```

이 예제에서 사용자 지정 처리기는 HTTP 이벤트를 처리할 웹 서버를 만들고, `FUNCTIONS_CUSTOMHANDLER_PORT`를 통해 요청을 받도록 설정됩니다.

`GET` 요청은 문자열을 반환하여 처리되고, `POST` 요청은 요청 본문에 액세스할 수 있습니다.

여기서 주문 함수의 경로는 원래 요청과 동일한 `/api/hello`입니다.

>[!NOTE]
>`FUNCTIONS_CUSTOMHANDLER_PORT`는 함수를 호출하는 데 사용되는 공용 포트가 아닙니다. 이 포트는 Functions 호스트에서 사용자 지정 처리기를 호출하는 데 사용됩니다.

## <a name="deploying"></a>배포 중

사용자 지정 처리기는 모든 Azure Functions 호스팅 옵션에 배포할 수 있습니다. 처리기에 운영 체제 또는 플랫폼 종속성(예: 언어 런타임)이 필요한 경우 [사용자 지정 컨테이너](./functions-create-function-linux-custom-image.md)를 사용해야 할 수 있습니다.

Azure에서 사용자 지정 처리기에 대한 함수 앱을 만드는 경우 .NET Core를 스택으로 선택하는 것이 좋습니다. 사용자 지정 처리기에 대한 "Custom" 스택은 나중에 추가될 예정입니다.

Azure Functions Core Tools를 사용하여 사용자 지정 처리기 앱을 배포하려면 다음 명령을 실행합니다.

```bash
func azure functionapp publish $functionAppName
```

> [!NOTE]
> 사용자 지정 처리기를 실행하는 데 필요한 모든 파일이 폴더에 있고 배포에 포함되었는지 확인합니다. 사용자 지정 처리기가 이진 실행 파일이거나 플랫폼별 종속성이 있는 경우 이러한 파일이 대상 배포 플랫폼과 일치하는지 확인합니다.

## <a name="restrictions"></a>제한

- 사용자 지정 처리기 웹 서버는 60초 이내에 시작해야 합니다.

## <a name="samples"></a>샘플

함수를 다양한 언어로 구현하는 방법에 대한 예제는 [사용자 지정 처리기 샘플 GitHub 리포지토리](https://github.com/Azure-Samples/functions-custom-handlers)를 참조하세요.

## <a name="troubleshooting-and-support"></a>문제 해결 및 지원

### <a name="trace-logging"></a>추적 로그

사용자 지정 처리기 프로세스가 시작되지 않거나 Functions 호스트와 통신하는 데 문제가 있는 경우 호스트에서 더 많은 진단 메시지를 표시하도록 함수 앱의 로그 수준을 `Trace`로 높일 수 있습니다.

함수 앱의 기본 로그 수준을 변경하려면 *host.json* 의 `logging` 섹션에서 `logLevel` 설정을 구성합니다.

```json
{
  "version": "2.0",
  "customHandler": {
    "description": {
      "defaultExecutablePath": "handler.exe"
    }
  },
  "logging": {
    "logLevel": {
      "default": "Trace"
    }
  }
}
```

Functions 호스트는 사용자 지정 처리기 프로세스와 관련된 정보가 포함된 추가 로그 메시지를 출력합니다. 로그를 사용하여 사용자 지정 처리기 프로세스를 시작하거나 사용자 지정 처리기에서 함수를 호출하는 문제를 조사합니다.

로그는 로컬로 콘솔에 출력됩니다.

Azure에서 [Application Insights 추적을 쿼리](analyze-telemetry-data.md#query-telemetry-data)하여 로그 메시지를 확인합니다. 앱에서 많은 양의 로그를 생성하는 경우 로그 메시지의 하위 세트만 Application Insights에 보내집니다. 모든 메시지가 기록되도록 [샘플링을 사용하지 않도록 설정](configure-monitoring.md#configure-sampling)합니다.

### <a name="test-custom-handler-in-isolation"></a>사용자 지정 처리기를 격리하여 테스트

사용자 지정 처리기 앱은 웹 서버 프로세스이므로 자체적으로 시작하고 [cURL](https://curl.haxx.se/) 또는 [Postman](https://www.postman.com/)과 같은 도구를 통해 모의 [HTTP 요청](#request-payload)을 보내 함수 호출을 테스트하는 것이 도움이 될 수 있습니다.

또한 CI/CD 파이프라인에서 이 전략을 사용하여 사용자 지정 처리기에서 자동화된 테스트를 실행할 수 있습니다.

### <a name="execution-environment"></a>실행 환경

사용자 지정 처리기는 일반적인 Azure Functions 앱과 동일한 환경에서 실행됩니다. 처리기를 테스트하여 환경이 실행하는 데 필요한 모든 종속성이 포함되도록 합니다. 추가 종속성이 필요한 앱의 경우 Azure Functions [프리미엄 플랜](functions-premium-plan.md)에 호스팅된 [사용자 지정 컨테이너 이미지](functions-create-function-linux-custom-image.md)를 사용하여 해당 앱을 실행해야 할 수 있습니다.

### <a name="get-support"></a>지원 받기

사용자 지정 처리기가 있는 함수 앱에 대한 도움이 필요한 경우 일반 지원 채널을 통해 요청을 제출할 수 있습니다. 그러나 사용자 지정 처리기 앱을 빌드하는 데 사용되는 다양한 언어로 인해 지원이 제한적입니다.

Functions 호스트에서 사용자 지정 처리기 프로세스를 시작하거나 통신하는 데 문제가 있는 경우 지원을 사용할 수 있습니다. 선택한 언어 또는 프레임워크의 문제와 같이 사용자 지정 처리기 프로세스의 내부 작업과 관련된 문제의 경우 지원 팀은 이 컨텍스트에서 지원을 제공할 수 없습니다.

## <a name="next-steps"></a>다음 단계

[사용자 지정 처리기 빠른 시작](create-first-function-vs-code-other.md)을 사용하여 Go 또는 Rust에서 Azure Functions 앱 빌드를 시작합니다.
