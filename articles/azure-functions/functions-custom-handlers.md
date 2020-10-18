---
title: 사용자 지정 처리기 Azure Functions (미리 보기)
description: 모든 언어 또는 런타임 버전과 Azure Functions를 사용 하는 방법에 대해 알아봅니다.
author: anthonychu
ms.author: antchu
ms.date: 8/18/2020
ms.topic: article
ms.openlocfilehash: 402ce1e9e92ab87689abe9c18a503a479d7421f9
ms.sourcegitcommit: 419c8c8061c0ff6dc12c66ad6eda1b266d2f40bd
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/18/2020
ms.locfileid: "92164553"
---
# <a name="azure-functions-custom-handlers-preview"></a>사용자 지정 처리기 Azure Functions (미리 보기)

모든 함수 앱은 언어별 처리기를 통해 실행 됩니다. Azure Functions는 기본적으로 다양 한 [언어 처리기](./supported-languages.md) 를 지원 하지만 다른 언어나 런타임을 사용 하려는 경우가 있습니다.

사용자 지정 처리기는 함수 호스트에서 이벤트를 수신 하는 간단한 웹 서버입니다. HTTP 기본 형식을 지 원하는 모든 언어는 사용자 지정 처리기를 구현할 수 있습니다.

사용자 지정 처리기는 다음을 수행 하려는 경우에 가장 적합 합니다.

- Go 및 Rust와 같이 현재 지원 되지 않는 언어로 함수 앱을 구현 합니다.
- Deno와 같이 현재 지원 되지 않는 런타임에 함수 앱을 구현 합니다.

사용자 지정 처리기를 사용 하 여 [확장 번들](./functions-bindings-register.md)을 통해 [트리거와 입력 및 출력 바인딩을](./functions-triggers-bindings.md) 사용할 수 있습니다.

## <a name="overview"></a>개요

다음 다이어그램에서는 함수 호스트와 사용자 지정 처리기로 구현 된 웹 서버 간의 관계를 보여 줍니다.

![Azure Functions 사용자 지정 처리기 개요](./media/functions-custom-handlers/azure-functions-custom-handlers-overview.png)

1. 각 이벤트는 함수 호스트로 전송 된 요청을 트리거합니다. 이벤트는 Azure Functions에서 지 원하는 모든 트리거입니다.
1. 그러면 함수 호스트가 웹 서버에 대 한 [요청 페이로드](#request-payload) 를 발급 합니다. 페이로드는 함수에 대 한 트리거 및 입력 바인딩 데이터 및 기타 메타 데이터를 보유 합니다.
1. 웹 서버는 개별 함수를 실행 하 고 함수 호스트에 [응답 페이로드](#response-payload) 를 반환 합니다.
1. 함수 호스트는 응답의 데이터를 처리를 위해 함수의 출력 바인딩에 전달 합니다.

사용자 지정 처리기로 구현 된 Azure Functions 앱은 몇 가지 규칙에 따라 파일에 대 한 *host.js*on, *local.settings.js*및 *function.js* 를 구성 해야 합니다.

## <a name="application-structure"></a>애플리케이션 구조

사용자 지정 처리기를 구현 하려면 응용 프로그램에 다음 요소가 필요 합니다.

- 응용 프로그램의 루트에 있는 파일 *에 대 한host.js*
- 응용 프로그램의 루트에 있는 파일 *에 대 한local.settings.js*
- 각 함수에 대 한 파일 * 에function.js* (함수 이름과 일치 하는 폴더 내)
- 웹 서버를 실행 하는 명령, 스크립트 또는 실행 파일

다음 다이어그램에서는 이러한 파일이 "MyQueueFunction" 함수 및 *handler.exe*이라는 사용자 지정 처리기 실행 파일의 파일 시스템에서 어떻게 표시 되는지 보여 줍니다.

```bash
| /MyQueueFunction
|   function.json
|
| host.json
| local.settings.json
| handler.exe
```

### <a name="configuration"></a>구성

응용 프로그램은 *host.js* 를 통해 구성 되 고 파일 * 에local.settings.js* 됩니다.

#### <a name="hostjson"></a>host.json

*host.js* 은 HTTP 이벤트를 처리할 수 있는 웹 서버를 가리켜 요청을 보낼 위치를 함수 호스트에 알려 줍니다.

사용자 지정 처리기는 섹션을 통해 웹 서버를 실행 하는 방법에 대 한 세부 정보를 사용 하 여 파일 * 에host.js* 를 구성 하 여 정의 합니다 `customHandler` .

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

`customHandler`섹션은에 정의 된 대상을 가리킵니다 `defaultExecutablePath` . 실행 대상은 웹 서버가 구현 되는 명령, 실행 파일 또는 파일 일 수 있습니다.

배열을 사용 하 여 `arguments` 실행 파일에 인수를 전달 합니다. 인수는 표기법을 사용 하 여 환경 변수 (응용 프로그램 설정)의 확장을 지원 `%%` 합니다.

또한에서 실행 파일에 사용 되는 작업 디렉터리를로 변경할 수 있습니다 `workingDirectory` .

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

입력 및 출력 바인딩과 함께 표준 트리거는 파일 *의host.js* 에서 [확장 번들](./functions-bindings-register.md) 을 참조 하 여 사용할 수 있습니다.

#### <a name="localsettingsjson"></a>local.settings.json

*local.settings.js* 는 함수 앱을 로컬로 실행할 때 사용 되는 응용 프로그램 설정을 정의 합니다. 비밀을 포함할 수 있으므로 *local.settings.js* 는 소스 제어에서 제외 해야 합니다. Azure에서 응용 프로그램 설정을 대신 사용 합니다.

사용자 지정 처리기의 경우 `FUNCTIONS_WORKER_RUNTIME` `Custom` *local.settings.json*에서을로 설정 합니다.

```json
{
  "IsEncrypted": false,
  "Values": {
    "FUNCTIONS_WORKER_RUNTIME": "Custom"
  }
}
```

> [!NOTE]
> `Custom` Linux Premium 또는 App Service 계획에서 유효한 런타임으로 인식 되지 않을 수 있습니다. 배포 대상인 경우를 `FUNCTIONS_WORKER_RUNTIME` 빈 문자열로 설정 합니다.

### <a name="function-metadata"></a>함수 메타 데이터

사용자 지정 처리기와 함께 사용 하는 경우 내용 * 에 대 한function.js* 다른 모든 컨텍스트에서 함수를 정의 하는 방법과 다르지 않습니다. 유일한 요구 사항은 파일 * 의function.js* 가 함수 이름과 일치 하도록 이름이 지정 된 폴더에 있어야 한다는 것입니다.

*에 대* 한 다음function.js는 큐 트리거 및 큐 출력 바인딩이 있는 함수를 구성 합니다. *Myqueuefunction*이라는 폴더에 있기 때문에 *myqueuefunction*이라는 함수를 정의 합니다.

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

큐 메시지를 받으면 함수 호스트가 본문의 페이로드를 사용 하 여 사용자 지정 처리기에 HTTP post 요청을 보냅니다.

다음 코드는 샘플 요청 페이로드를 나타냅니다. 페이로드에는와 라는 두 개의 멤버가 있는 JSON 구조가 포함 되어 있습니다. `Data` `Metadata`

멤버에는 `Data` *function.js* 파일의 바인딩 배열에 정의 된 입력 및 트리거 이름과 일치 하는 키가 포함 되어 있습니다.

`Metadata`멤버는 [이벤트 소스에서 생성 된 메타 데이터](./functions-bindings-expressions-patterns.md#trigger-metadata)를 포함 합니다.

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

규칙에 따라 함수 응답은 키/값 쌍으로 형식이 지정 됩니다. 지원 되는 키는 다음과 같습니다.

| <nobr>페이로드 키</nobr>   | 데이터 형식 | 설명                                                      |
| ------------- | --------- | ------------------------------------------------------------ |
| `Outputs`     | 개체    | function.js에서 배열에 정의 된 응답 값을 포함 `bindings` 합니다. *function.json*<br /><br />예를 들어 함수가 "myQueueOutput" 이라는 큐 출력 바인딩으로 구성 된 경우에는 `Outputs` `myQueueOutput` 사용자 지정 처리기에 의해 큐에 전송 되는 메시지에 설정 되는 라는 키가 포함 됩니다. |
| `Logs`        | array     | 메시지는 함수 호출 로그에 표시 됩니다.<br /><br />Azure에서 실행 하는 경우 메시지가 Application Insights 표시 됩니다. |
| `ReturnValue` | 문자열    | function.js파일에서로 출력을 구성할 때 응답을 제공 하는 데 사용 됩니다 `$return` . *function.json* |

이는 응답 페이로드의 예입니다.

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

사용자 지정 처리기는 HTTP 이벤트 수신을 지 원하는 모든 언어로 구현할 수 있습니다. 다음 예제에서는 Go 프로그래밍 언어를 사용 하 여 사용자 지정 처리기를 구현 하는 방법을 보여 줍니다.

### <a name="function-with-bindings"></a>바인딩을 사용 하는 함수

이 예제에서 구현 된 시나리오에는 `order` `POST` 제품 주문을 나타내는 페이로드가 포함 된을 허용 하는 이라는 함수가 있습니다. 함수가 함수에 게시 될 때 Queue Storage 메시지가 만들어지고 HTTP 응답이 반환 됩니다.

<a id="bindings-implementation" name="bindings-implementation"></a>

#### <a name="implementation"></a>구현

이름이 *order*인 폴더에서 파일 * 의function.js* 는 HTTP 트리거 함수를 구성 합니다.

**주문/function.js**

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

이 함수는 [http 응답](./functions-bindings-http-webhook-output.md) 을 반환 하 고 [큐 저장소](./functions-bindings-storage-queue-output.md) 메시지를 출력 하는 [http 트리거 함수로](./functions-bindings-http-webhook-trigger.md) 정의 됩니다.

앱의 루트에서 파일 * 의host.js* 는 `handler.exe` ( `handler` Linux 또는 macos에서) 라는 실행 파일을 실행 하도록 구성 됩니다.

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

이는 함수 런타임으로 전송 되는 HTTP 요청입니다.

```http
POST http://127.0.0.1:7071/api/order HTTP/1.1
Content-Type: application/json

{
  "id": 1005,
  "quantity": 2,
  "color": "black"
}
```

그러면 함수 런타임에서 사용자 지정 처리기에 다음 HTTP 요청을 보냅니다.

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
> 페이로드에 대 한 일부 부분이 제거 되었습니다.

*handler.exe* 은 웹 서버를 실행 하 고 함수 호스트의 함수 호출 요청에 응답 하는 컴파일된 Go 사용자 지정 처리기 프로그램입니다.

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

이 예제에서 사용자 지정 처리기는 HTTP 이벤트를 처리 하기 위해 웹 서버를 실행 하 고을 통해 요청을 수신 하도록 설정 됩니다 `FUNCTIONS_CUSTOMHANDLER_PORT` .

함수 호스트는에서 원래 HTTP 요청을 수신 하더라도 `/api/order` 함수 이름 (해당 폴더 이름)을 사용 하 여 사용자 지정 처리기를 호출 합니다. 이 예제에서 함수는의 경로에 정의 되어 `/order` 있습니다. 호스트는의 경로에서 HTTP 요청을 사용자 지정 처리기에 보냅니다 `/order` .

`POST`요청이이 함수로 전송 되 면 HTTP 요청 본문을 통해 트리거 데이터 및 함수 메타 데이터를 사용할 수 있습니다. 원본 HTTP 요청 본문은 페이로드의에서 액세스할 수 있습니다 `Data.req.Body` .

함수의 응답 형식은 키/값 쌍으로 지정 됩니다. 여기서 멤버는 키가 `Outputs` 파일의 *function.js* 에 정의 된 출력과 일치 하는 JSON 값을 포함 합니다.

이 처리기가 함수 호스트로 반환 하는 예제 페이로드입니다.

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

`message`이 함수는 요청에서 제공 된 주문 데이터와 같은 출력을 설정 하 여 해당 주문 데이터를 구성 된 큐에 출력 합니다. 또한 함수 호스트는 호출자에 구성 된 HTTP 응답을 반환 합니다 `res` .

### <a name="http-only-function"></a>HTTP 전용 함수

추가 바인딩이나 출력이 없는 HTTP 트리거 함수의 경우 처리기가 사용자 지정 처리기 [요청](#request-payload) 및 [응답](#response-payload) 페이로드 대신 http 요청 및 응답과 함께 직접 작동 하도록 할 수 있습니다. 이 동작은 설정을 사용 하 여 *host.js* 에서 구성할 수 있습니다 `enableForwardingHttpRequest` .

> [!IMPORTANT]
> 사용자 지정 처리기 기능의 주요 목적은 현재 Azure Functions에 대 한 최고 수준의 지원을 제공 하지 않는 언어와 런타임을 사용 하도록 설정 하는 것입니다. 사용자 지정 처리기를 사용 하 여 웹 응용 프로그램을 실행할 수는 있지만 Azure Functions 표준 역방향 프록시가 아닙니다. 응답 스트리밍, HTTP/2 및 Websocket과 같은 일부 기능은 사용할 수 없습니다. 특정 헤더 및 경로와 같은 HTTP 요청의 일부 구성 요소가 제한 될 수 있습니다. 응용 프로그램에 과도 한 [콜드 시작](functions-scale.md#cold-start)이 발생할 수도 있습니다.
>
> 이러한 상황을 해결 하려면 [Azure App Service](../app-service/overview.md)에서 웹 앱을 실행 하는 것이 좋습니다.

다음 예제에서는 추가 바인딩이나 출력 없이 HTTP로 트리거되는 함수를 구성 하는 방법을 보여 줍니다. 이 예제에서 구현 된 시나리오에서는 `hello` 또는를 허용 하는 라는 함수를 제공 합니다 `GET` `POST` .

<a id="hello-implementation" name="hello-implementation"></a>

#### <a name="implementation"></a>구현

*Hello*라는 폴더에서 파일 *의function.js* 는 HTTP 트리거 함수를 구성 합니다.

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

함수는 및 요청을 모두 허용 하도록 구성 되 `GET` `POST` 고, 결과 값은 라는 인수를 통해 제공 됩니다 `res` .

앱의 루트에서 파일 * 의host.js* 은 실행 되도록 구성 되 `handler.exe` 고 `enableForwardingHttpRequest` 는로 설정 됩니다 `true` .

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

`enableForwardingHttpRequest`가 이면 `true` HTTP 전용 함수의 동작은 다음과 같은 방식으로 기본 사용자 지정 처리기 동작과 다릅니다.

* HTTP 요청에 사용자 지정 처리기 [요청](#request-payload) 페이로드가 포함 되어 있지 않습니다. 대신 함수 호스트는 원래 HTTP 요청의 복사본으로 처리기를 호출 합니다.
* 함수 호스트는 쿼리 문자열 매개 변수를 포함 하 여 원래 요청과 동일한 경로를 사용 하 여 처리기를 호출 합니다.
* 함수 호스트는 처리기의 HTTP 응답에 대 한 복사본을 원래 요청에 대 한 응답으로 반환 합니다.

다음은 함수 호스트에 대 한 POST 요청입니다. 그러면 함수 호스트는 동일한 경로에 있는 사용자 지정 처리기에 요청 복사본을 보냅니다.

```http
POST http://127.0.0.1:7071/api/hello HTTP/1.1
Content-Type: application/json

{
  "message": "Hello World!"
}
```

파일 *처리기. go* 파일은 웹 서버 및 HTTP 함수를 구현 합니다.

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

이 예제에서 사용자 지정 처리기는 HTTP 이벤트를 처리 하는 웹 서버를 만들고를 통해 요청을 수신 하도록 설정 됩니다 `FUNCTIONS_CUSTOMHANDLER_PORT` .

`GET` 요청은 문자열을 반환 하 여 처리 되며 요청은 요청 `POST` 본문에 액세스할 수 있습니다.

여기서 주문 함수의 경로는 `/api/hello` 원래 요청과 동일 합니다.

>[!NOTE]
>는 `FUNCTIONS_CUSTOMHANDLER_PORT` 함수를 호출 하는 데 사용 되는 공용 포트가 아닙니다. 이 포트는 함수 호스트에서 사용자 지정 처리기를 호출 하는 데 사용 됩니다.

## <a name="deploying"></a>배포 중

모든 Azure Functions 호스팅 옵션에 사용자 지정 처리기를 배포할 수 있습니다. 처리기에 운영 체제 또는 플랫폼 종속성이 필요한 경우 (예: 언어 런타임) [사용자 지정 컨테이너](./functions-create-function-linux-custom-image.md)를 사용 해야 할 수 있습니다.

Azure에서 사용자 지정 처리기에 대 한 함수 앱을 만들 때 .NET Core를 스택으로 선택 하는 것이 좋습니다. 사용자 지정 처리기에 대 한 "사용자 지정" 스택은 나중에 추가 될 예정입니다.

Azure Functions Core Tools를 사용 하 여 사용자 지정 처리기 앱을 배포 하려면 다음 명령을 실행 합니다.

```bash
func azure functionapp publish $functionAppName
```

> [!NOTE]
> 사용자 지정 처리기를 실행 하는 데 필요한 모든 파일이 폴더에 있고 배포에 포함 되었는지 확인 합니다. 사용자 지정 처리기가 이진 실행 파일 이거나 플랫폼별 종속성이 있는 경우 이러한 파일이 대상 배포 플랫폼과 일치 하는지 확인 합니다.

## <a name="restrictions"></a>제한

- 사용자 지정 처리기 웹 서버는 60 초 이내에 시작 해야 합니다.

## <a name="samples"></a>샘플

다양 한 언어로 함수를 구현 하는 방법에 대 한 예제는 [사용자 지정 처리기 샘플 GitHub 리포지토리](https://github.com/Azure-Samples/functions-custom-handlers) 를 참조 하세요.

## <a name="troubleshooting-and-support"></a>문제 해결 및 지원

### <a name="trace-logging"></a>추적 로그

사용자 지정 처리기 프로세스가 시작 되지 않거나 함수 호스트와 통신 하는 데 문제가 있는 경우 함수 앱의 로그 수준을로 늘려 `Trace` 호스트에서 더 많은 진단 메시지를 볼 수 있습니다.

함수 앱의 기본 로그 수준을 변경 하려면 `logLevel` `logging` * onhost.js*의 섹션에서 설정을 구성 합니다.

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

함수 호스트는 사용자 지정 처리기 프로세스와 관련 된 정보를 포함 하 여 추가 로그 메시지를 출력 합니다. 로그를 사용 하 여 사용자 지정 처리기 프로세스를 시작 하거나 사용자 지정 처리기에서 함수를 호출 하는 문제를 조사 합니다.

로컬로 로그가 콘솔에 출력 됩니다.

Azure에서 [Application Insights 추적을 쿼리하여](analyze-telemetry-data.md#query-telemetry-data) 로그 메시지를 확인 합니다. 앱이 많은 양의 로그를 생성 하는 경우 로그 메시지의 하위 집합만 Application Insights 전송 됩니다. 모든 메시지가 기록 되도록 [샘플링을 사용 하지 않도록 설정](configure-monitoring.md#configure-sampling) 합니다.

### <a name="test-custom-handler-in-isolation"></a>격리에서 사용자 지정 처리기 테스트

사용자 지정 처리기 앱은 웹 서버 프로세스 이므로,이를 자체적으로 시작 하 고 함수 호출을 [테스트 하는](#request-payload) 것이 도움이 될 수 [있습니다.](https://www.postman.com/) [cURL](https://curl.haxx.se/)

CI/CD 파이프라인에서이 전략을 사용 하 여 사용자 지정 처리기에서 자동화 된 테스트를 실행할 수도 있습니다.

### <a name="execution-environment"></a>실행 환경

사용자 지정 처리기는 일반적인 Azure Functions 앱과 동일한 환경에서 실행 됩니다. 처리기를 테스트 하 여 실행 하는 데 필요한 모든 종속성이 환경에 포함 되도록 합니다. 추가 종속성이 필요한 앱의 경우 Azure Functions [프리미엄 계획](functions-premium-plan.md)에 호스트 된 [사용자 지정 컨테이너 이미지](functions-create-function-linux-custom-image.md) 를 사용 하 여 실행 해야 할 수 있습니다.

### <a name="get-support"></a>지원 받기

사용자 지정 처리기를 사용 하는 함수 앱에 대 한 도움이 필요한 경우 정기 지원 채널을 통해 요청을 제출할 수 있습니다. 그러나 사용자 지정 처리기 앱을 빌드하는 데 사용 되는 다양 한 언어 때문에 지원이 제한 되지 않습니다.

함수 호스트에서 사용자 지정 처리기 프로세스를 시작 하거나이 프로세스와 통신 하는 데 문제가 있는 경우에는 지원을 사용할 수 있습니다. 선택한 언어나 프레임 워크와 관련 된 문제 등 사용자 지정 처리기 프로세스의 내부 작업에 관련 된 문제의 경우 지원 팀은이 컨텍스트에서 지원을 제공할 수 없습니다.
