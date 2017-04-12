---
title: "Azure Functions HTTP 및 webhook 바인딩 | Microsoft 문서"
description: "Azure Functions에서 HTTP와 WebHook 트리거 및 바인딩을 사용하는 방법을 파악합니다."
services: functions
documentationcenter: na
author: mattchenderson
manager: erikre
editor: 
tags: 
keywords: "Azure Functions, 함수, 이벤트 처리, webhook, 동적 계산, 서버가 없는 아키텍처, HTTP, API, REST"
ms.assetid: 2b12200d-63d8-4ec1-9da8-39831d5a51b1
ms.service: functions
ms.devlang: multiple
ms.topic: reference
ms.tgt_pltfrm: multiple
ms.workload: na
ms.date: 11/18/2016
ms.author: mahender
translationtype: Human Translation
ms.sourcegitcommit: 503f5151047870aaf87e9bb7ebf2c7e4afa27b83
ms.openlocfilehash: 06958522139d621f86afd8bf25128ee64cf822b3
ms.lasthandoff: 03/29/2017


---
# <a name="azure-functions-http-and-webhook-bindings"></a>Azure Functions HTTP 및 WebHook 바인딩
[!INCLUDE [functions-selector-bindings](../../includes/functions-selector-bindings.md)]

이 문서에서는 Azure Functions에서 HTTP 트리거 및 바인딩을 구성하고 사용하는 방법에 대해 설명합니다.
이러한 트리거와 바인딩을 사용하면 Azure Functions에서 서버가 없는 API를 만들고 webhook에 응답할 수 있습니다.

Azure Functions에서 제공하는 바인딩은 다음과 같습니다.
- [HTTP 트리거](#httptrigger)를 사용하면 HTTP 요청으로 함수를 호출할 수 있습니다. 이 트리거는 [webhook](#hooktrigger)에 응답하도록 사용자 지정할 수 있습니다.
- [HTTP 출력 바인딩](#output)을 사용하면 요청에 응답할 수 있습니다.

[!INCLUDE [intro](../../includes/functions-bindings-intro.md)]

> [!TIP]
>
> [HTTPClient](https://github.com/mspnp/performance-optimization/blob/master/ImproperInstantiation/docs/ImproperInstantiation.md)에서 이 모범 사례 문서를 읽는 것이 좋습니다.
>

<a name="httptrigger"></a>

## <a name="http-trigger"></a>HTTP 트리거
HTTP 트리거는 HTTP 요청에 대한 응답으로 함수를 실행합니다. 특정 URL 또는 HTTP 메서드 집합에 응답하도록 사용자 지정할 수 있습니다. 또한 HTTP 트리거는 webhook에도 응답하도록 구성할 수 있습니다. 

Functions 포털을 사용하는 경우 미리 만든 템플릿을 사용하여 바로 시작할 수도 있습니다. **새 함수**를 선택하고 **시나리오** 드롭다운에서 [API & Webhooks]를 선택합니다. 템플릿 중 하나를 선택하고 **만들기**를 클릭합니다.

기본적으로 HTTP 트리거는 HTTP 200 OK 상태 코드와 비어 있는 본문을 포함한 요청에 응답합니다. 응답을 수정하려면 [HTTP 바인딩 출력](#output)을 구성합니다.

### <a name="configuring-an-http-trigger"></a>HTTP 트리거 구성
HTTP 트리거는 function.json의 `bindings` 배열에 다음과 유사한 JSON 개체를 포함하여 정의됩니다.

```json
{
    "name": "req",
    "type": "httpTrigger",
    "direction": "in",
    "authLevel": "function",
    "methods": [ "GET" ],
    "route": "values/{id}"
},
```
바인딩에서 지원하는 속성은 다음과 같습니다.

* **name**: 필수 - 요청 또는 요청 본문의 함수 코드에 사용되는 변수 이름입니다. [코드에서 HTTP 트리거 사용](#httptriggerusage)을 참조하세요.
* **type**: 필수 - "httpTrigger"로 설정해야 합니다.
* **direction**: 필수 - "in"으로 설정해야 합니다.
* _authLevel_: 키가 있는 경우 함수를 호출하기 위해 요청에 포함되어야 하는 키를 결정합니다. 아래의 [키 사용](#keys)을 참조하세요. 값은 다음 중 하나일 수 있습니다.
    * _anonymous_: API 키가 필요하지 않습니다.
    * _function_: 함수 전용 API 키가 필요합니다. authLevel 속성 값을 제공하지 않을 경우 기본값입니다.
    * _admin_: 마스터 키가 필요합니다.
* **methods**: 함수에서 응답할 HTTP 메서드의 배열입니다. 이 속성을 지정하지 않으면 함수에서 모든 HTTP 메서드에 응답합니다. [HTTP 끝점 사용자 지정](#url)을 참조하세요.
* **route**: 경로 템플릿을 정의하여 함수에서 응답할 요청 URL을 제어합니다. 값을 제공하지 않을 경우 기본값은 `<functionname>`입니다. [HTTP 끝점 사용자 지정](#url)을 참조하세요.
* **webHookType**: HTTP 트리거가 지정된 공급자의 webhook 수신기(receiver)로 작동하도록 구성합니다. 이 속성을 선택하는 경우 _methods_ 속성을 설정하면 안됩니다. [webhook에 응답](#hooktrigger)을 참조하세요. 값은 다음 중 하나일 수 있습니다.
    * _genericJson_: 특정 공급자를 위한 논리가 없는 범용 webhook 끝점입니다.
    * _github_: GitHub webhook에 응답하는 함수입니다. 이 값을 선택하는 경우 _authLevel_ 속성을 설정하면 안됩니다.
    * _slack_: Slack webhook에 응답하는 함수입니다. 이 값을 선택하는 경우 _authLevel_ 속성을 설정하면 안됩니다.

<a name="httptriggerusage"></a>
### <a name="working-with-an-http-trigger-from-code"></a>코드에서 HTTP 트리거 사용
C# 및 F# 함수의 경우 트리거 입력 형식을 `HttpRequestMessage` 또는 사용자 지정 형식으로 선언할 수 있습니다. `HttpRequestMessage`를 선택하면 요청 개체에 대한 모든 권한을 갖게 됩니다. 사용자 지정 형식(예: POCO)의 경우 함수는 요청 본문을 JSON으로 구문 분석하여 개체 속성을 채웁니다.

Node.js 함수의 경우 함수 런타임은 요청 개체 대신 요청 본문을 제공합니다.

사용 예제는 [HTTP 트리거 샘플](#httptriggersample)을 참조하세요.


<a name="output"></a>
## <a name="http-response-output-binding"></a>HTTP 응답 출력 바인딩
HTTP 요청 발신기(sender)에 응답하려면 HTTP 출력 바인딩을 사용합니다. 이 바인딩에는 HTTP 트리거가 필요하며 트리거 요청과 관련된 응답을 사용자 지정할 수 있습니다. HTTP 출력 바인딩이 제공되지 않으면 HTTP 트리거는 빈 본문과 함께 HTTP 200 OK를 반환합니다. 

### <a name="configuring-an-http-output-binding"></a>HTTP 출력 바인딩 구성
HTTP 출력 바인딩은 function.json의 `bindings` 배열에 다음과 유사한 JSON 개체를 포함하여 정의됩니다.

```json
{
    "name": "res",
    "type": "http",
    "direction": "out"
}
```
바인딩에서 포함하는 속성은 다음과 같습니다.

* **name**: 필수 - 응답의 함수 코드에 사용되는 변수 이름입니다. [코드에서 HTTP 출력 바인딩 사용](#outputusage)을 참조하세요.
* **type**: 필수 - "http"로 설정해야 합니다.
* **direction**: 필수 - "out"으로 설정해야 합니다.

<a name="outputusage"></a>
### <a name="working-with-an-http-output-binding-from-code"></a>코드에서 HTTP 출력 바인딩 사용
출력 매개 변수(예: "res")를 사용하여 http 또는 webhook 호출기(caller)에 응답할 수 있습니다. 또는 표준 `Request.CreateResponse()`(C#) 또는 `context.res`(Node.JS) 패턴을 사용하여 응답을 반환할 수 있습니다. 후자의 메서드를 사용하는 방법에 대한 예제는 [HTTP 트리거 샘플](#httptriggersample) 및 [Webhook 트리거 샘플](#hooktriggersample)을 참조하세요.


<a name="hooktrigger"></a>
## <a name="responding-to-webhooks"></a>webhook에 응답
_webHookType_ 속성이 있는 HTTP 트리거에서 [webhook](https://en.wikipedia.org/wiki/Webhook)에 응답하도록 구성됩니다. 기본 구성에서는 "genericJson" 설정을 사용합니다. 이렇게 하면 HTTP POST 및 `application/json` 콘텐츠 형식을 사용하는 요청으로만 제한됩니다.

또한 트리거는 특정 webhook 공급자(예: [GitHub](https://developer.github.com/webhooks/) 및 [Slack](https://api.slack.com/outgoing-webhooks))에 맞춰 지정할 수 있습니다. 공급자를 지정하면 Functions 런타임에서 공급자의 유효성 검사 논리를 처리할 수 있습니다.  

### <a name="configuring-github-as-a-webhook-provider"></a>GitHub를 웹후크 공급자로 구성
GitHub webhook에 응답하려면 먼저 HTTP 트리거를 사용하여 함수를 만들고 _webHookType_ 속성을 "github"로 설정합니다. 그런 다음 [URL](#url) 및 [API 키](#keys)를 GitHub 리포지토리의 **webhook 추가** 페이지에 복사합니다. 자세한 내용은 GitHub의 [Webhook 만들기](http://go.microsoft.com/fwlink/?LinkID=761099&clcid=0x409) 설명서를 참조하세요.

![](./media/functions-bindings-http-webhook/github-add-webhook.png)

### <a name="configuring-slack-as-a-webhook-provider"></a>Slack을 webhook 공급자로 구성
Slack webhook은 함수 전용 키를 지정하는 대신 사용자를 위한 토큰을 생성하므로 Slack의 토큰을 사용하여 함수 전용 키를 구성해야 합니다. [키 사용](#keys)을 참조하세요.

<a name="url"></a>
## <a name="customizing-the-http-endpoint"></a>HTTP 끝점 사용자 지정
기본적으로 HTTP 트리거 또는 WebHook용 함수를 만드는 경우 폼의 경로를 사용하여 이 함수의 주소를 지정할 수 있습니다.

    http://<yourapp>.azurewebsites.net/api/<funcname> 

HTTP 트리거의 입력 바인딩에서 선택적 `route` 속성을 사용하여 이 경로를 사용자 지정할 수 있습니다. 예를 들어 다음 *function.json* 파일은 HTTP 트리거에 대한 `route` 속성을 정의합니다.

```json
    {
      "bindings": [
        {
          "type": "httpTrigger",
          "name": "req",
          "direction": "in",
          "methods": [ "get" ],
          "route": "products/{category:alpha}/{id:int?}"
        },
        {
          "type": "http",
          "name": "res",
          "direction": "out"
        }
      ]
    }
```

이 구성을 사용하면 원래 경로 대신 다음 경로를 사용하여 함수의 주소를 지정할 수 있습니다.

    http://<yourapp>.azurewebsites.net/api/products/electronics/357

이렇게 하면 함수 코드에서 주소의 두 매개 변수, "category" 및 "id"를 지원할 수 있습니다. 매개 변수에서 [웹 API 경로 제약 조건](https://www.asp.net/web-api/overview/web-api-routing-and-actions/attribute-routing-in-web-api-2#constraints)을 사용할 수 있습니다. 다음 C# 함수 코드는 두 매개 변수를 모두 사용합니다.

```csharp
    public static Task<HttpResponseMessage> Run(HttpRequestMessage request, string category, int? id, 
                                                    TraceWriter log)
    {
        if (id == null)
           return  req.CreateResponse(HttpStatusCode.OK, $"All {category} items were requested.");
        else
           return  req.CreateResponse(HttpStatusCode.OK, $"{category} item with id = {id} has been requested.");
    }
```

다음은 동일한 경로 매개 변수를 사용하는 Node.js 함수 코드입니다.

```javascript
    module.exports = function (context, req) {

        var category = context.bindingData.category;
        var id = context.bindingData.id;

        if (!id) {
            context.res = {
                // status: 200, /* Defaults to 200 */
                body: "All " + category + " items were requested."
            };
        }
        else {
            context.res = {
                // status: 200, /* Defaults to 200 */
                body: category + " item with id = " + id + " was requested."
            };
        }

        context.done();
    } 
```

기본적으로 모든 함수 경로에는 *api* 접두사가 붙습니다. *host.json* 파일에서 `http.routePrefix` 속성을 사용하여 접두사를 사용자 지정하거나 제거할 수도 있습니다. 다음 예제에서는 *host.json* 파일에서 빈 문자열을 접두사로 사용하여 *api* 경로 접두사를 제거합니다.

```json
    {
      "http": {
        "routePrefix": ""
      }
    }
```

함수의 *host.json* 파일을 업데이트하는 방법에 대한 자세한 내용은 [함수 앱 파일을 업데이트하는 방법](functions-reference.md#fileupdate)을 참조하세요. 

*host.json* 파일에서 구성할 수 있는 다른 속성에 대한 자세한 내용은 [host.json 참조](https://github.com/Azure/azure-webjobs-sdk-script/wiki/host.json)를 참조하세요.


<a name="keys"></a>
## <a name="working-with-keys"></a>키 사용
HttpTriggers는 보안 강화를 위해 키를 활용할 수 있습니다. 표준 HttpTrigger에서 이러한 키는 API 키로 사용될 수 있으므로 요청 시에 필요합니다. Webhooks에서는 공급자가 지원하는 항목에 따라 다양한 방식으로 요청을 인증하는 데 키를 사용할 수 있습니다.

키는 Azure에 함수 앱의 일부로 저장되며 나머지는 암호화되어 있습니다. 키를 보거나, 새 키를 만들거나, 키를 새 값으로 전환하려면 포털에 있는 함수 중 하나를 탐색하여 [관리]를 선택합니다. 

다음과 같이 두 가지 유형의 키가 있습니다.
- **호스트 키**: 함수 앱 내의 모든 함수에서 공유합니다. API 키로 사용되면 이 키를 통해 함수 앱 내의 모든 함수에 액세스할 수 있습니다.
- **function 키**: 정의된 특정 함수에만 적용됩니다. API 키로 사용되면 이 키를 통해 해당 함수에만 액세스할 수 있습니다.

각 키의 이름은 참조될 수 있도록 지정되며 함수 및 호스트 수준에서는 "default"라는 기본 키가 있습니다. **master 키**는 각 함수 앱에 대해 정의하고 취소할 수 없는 "_master"라는 기본 호스트 키입니다. 런타임 API에 대한 관리 액세스를 제공합니다. 바인딩 JSON에서 `"authLevel": "admin"`을 사용하면 요청 시 이 키가 필요합니다. 다른 키는 권한 부여 오류가 발생합니다.

> [!NOTE]
> master 키에서 부여하는 승격된 권한으로 인해 이 키를 타사와 공유하거나 네이티브 클라이언트 응용 프로그램에 배포해서는 안됩니다. 따라서 관리자 권한 부여 수준을 선택할 때는 주의해야 합니다.
> 
> 

### <a name="api-key-authorization"></a>API 키 권한 부여
기본적으로 HttpTrigger는 HTTP 요청에서 API 키가 필요합니다. 따라서 HTTP 요청은 일반적으로 다음과 같습니다.

    https://<yourapp>.azurewebsites.net/api/<function>?code=<ApiKey>

키는 위와 같이 `code`라는 쿼리 문자열 변수에 포함되거나 `x-functions-key` HTTP 헤더에 포함될 수 있습니다. 키 값은 함수에 대해 정의된 모든 function 키 또는 모든 호스트 키일 수 있습니다.

키가 없는 요청을 허용하도록 선택하거나 바인딩 JSON([HTTP 트리거](#httptrigger) 참조)에서 `authLevel` 속성을 변경하여 master 키를 사용해야 한다고 지정할 수 있습니다.

### <a name="keys-and-webhooks"></a>키 및 webhook
Webhook 인증은 HttpTrigger의 일부로 Webhook 수신기 구성 요소에서 처리하며 메커니즘은 Webhook 유형에 따라 다릅니다. 그러나 각 메커니즘마다 키를 사용합니다. 기본적으로 "default"라는 function 키가 사용됩니다. 다른 키를 사용하려면 다음 중 한 가지 방법으로 요청과 함께 키 이름을 보내도록 webhook 공급자를 구성해야 합니다.

- **쿼리 문자열**: 공급자에서 `clientid` 쿼리 문자열 매개 변수에 키 이름을 전달합니다(예제: `https://<yourapp>.azurewebsites.net/api/<funcname>?clientid=<keyname>`).
- **요청 헤더**: 공급자에서 `x-functions-clientid` 헤더에 키 이름을 전달합니다.

> [!NOTE]
> function 키는 호스트 키보다 우선합니다. 두 키가 동일한 이름으로 정의되면 function 키가 사용됩니다.
> 
> 


<a name="httptriggersample"></a>
## <a name="http-trigger-samples"></a>HTTP 트리거 샘플
function.json의 `bindings` 배열에 다음과 같은 HTTP 트리거가 있다고 가정합니다.

```json
{
    "name": "req",
    "type": "httpTrigger",
    "direction": "in",
    "authLevel": "function"
},
```

쿼리 문자열 또는 HTTP 요청 본문에서 `name` 매개 변수를 찾는 언어 특정 샘플을 참조하세요.

* [C#](#httptriggercsharp)
* [F#](#httptriggerfsharp)
* [Node.JS](#httptriggernodejs)


<a name="httptriggercsharp"></a>
### <a name="http-trigger-sample-in-c"></a>C의 HTTP 트리거 샘플# #
```csharp
using System.Net;
using System.Threading.Tasks;

public static async Task<HttpResponseMessage> Run(HttpRequestMessage req, TraceWriter log)
{
    log.Info($"C# HTTP trigger function processed a request. RequestUri={req.RequestUri}");

    // parse query parameter
    string name = req.GetQueryNameValuePairs()
        .FirstOrDefault(q => string.Compare(q.Key, "name", true) == 0)
        .Value;

    // Get request body
    dynamic data = await req.Content.ReadAsAsync<object>();

    // Set name to query string or body data
    name = name ?? data?.name;

    return name == null
        ? req.CreateResponse(HttpStatusCode.BadRequest, "Please pass a name on the query string or in the request body")
        : req.CreateResponse(HttpStatusCode.OK, "Hello " + name);
}
```

<a name="httptriggerfsharp"></a>
### <a name="http-trigger-sample-in-f"></a>F의 HTTP 트리거 샘플# #
```fsharp
open System.Net
open System.Net.Http
open FSharp.Interop.Dynamic

let Run(req: HttpRequestMessage) =
    async {
        let q =
            req.GetQueryNameValuePairs()
                |> Seq.tryFind (fun kv -> kv.Key = "name")
        match q with
        | Some kv ->
            return req.CreateResponse(HttpStatusCode.OK, "Hello " + kv.Value)
        | None ->
            let! data = Async.AwaitTask(req.Content.ReadAsAsync<obj>())
            try
                return req.CreateResponse(HttpStatusCode.OK, "Hello " + data?name)
            with e ->
                return req.CreateErrorResponse(HttpStatusCode.BadRequest, "Please pass a name on the query string or in the request body")
    } |> Async.StartAsTask
```

다음과 같이 NuGet을 사용하여 `FSharp.Interop.Dynamic` 및 `Dynamitey` 어셈블리를 참조하는 `project.json` 파일이 필요합니다.

```json
{
  "frameworks": {
    "net46": {
      "dependencies": {
        "Dynamitey": "1.0.2",
        "FSharp.Interop.Dynamic": "3.0.0"
      }
    }
  }
}
```

그러면 NuGet을 사용하여 종속성을 가져오고 스크립트에서 해당 항목을 참조하게 됩니다.

<a name="httptriggernodejs"></a>
### <a name="http-trigger-sample-in-nodejs"></a>Node.js의 트리거 샘플
```javascript
module.exports = function(context, req) {
    context.log('Node.js HTTP trigger function processed a request. RequestUri=%s', req.originalUrl);

    if (req.query.name || (req.body && req.body.name)) {
        context.res = {
            // status: 200, /* Defaults to 200 */
            body: "Hello " + (req.query.name || req.body.name)
        };
    }
    else {
        context.res = {
            status: 400,
            body: "Please pass a name on the query string or in the request body"
        };
    }
    context.done();
};
```



<a name="hooktriggersample"></a>
## <a name="webhook-samples"></a>Webhook 샘플
function.json의 `bindings` 배열에 다음과 같은 webhook 트리거가 있다고 가정합니다.

```json
{
    "webHookType": "github",
    "name": "req",
    "type": "httpTrigger",
    "direction": "in",
},
```

GitHub 문제 설명을 기록하는 언어 특정 샘플을 참조하세요.

* [C#](#hooktriggercsharp)
* [F#](#hooktriggerfsharp)
* [Node.JS](#hooktriggernodejs)

<a name="hooktriggercsharp"></a>

### <a name="webhook-sample-in-c"></a>C의 Webhook 샘플# #
```csharp
#r "Newtonsoft.Json"

using System;
using System.Net;
using System.Threading.Tasks;
using Newtonsoft.Json;

public static async Task<object> Run(HttpRequestMessage req, TraceWriter log)
{
    string jsonContent = await req.Content.ReadAsStringAsync();
    dynamic data = JsonConvert.DeserializeObject(jsonContent);

    log.Info($"WebHook was triggered! Comment: {data.comment.body}");

    return req.CreateResponse(HttpStatusCode.OK, new {
        body = $"New GitHub comment: {data.comment.body}"
    });
}
```

<a name="hooktriggerfsharp"></a>

### <a name="webhook-sample-in-f"></a>F의 Webhook 샘플# #
```fsharp
open System.Net
open System.Net.Http
open FSharp.Interop.Dynamic
open Newtonsoft.Json

type Response = {
    body: string
}

let Run(req: HttpRequestMessage, log: TraceWriter) =
    async {
        let! content = req.Content.ReadAsStringAsync() |> Async.AwaitTask
        let data = content |> JsonConvert.DeserializeObject
        log.Info(sprintf "GitHub WebHook triggered! %s" data?comment?body)
        return req.CreateResponse(
            HttpStatusCode.OK,
            { body = sprintf "New GitHub comment: %s" data?comment?body })
    } |> Async.StartAsTask
```

<a name="hooktriggernodejs"></a>

### <a name="webhook-sample-in-nodejs"></a>Node.JS의 Webhook 샘플
```javascript
module.exports = function (context, data) {
    context.log('GitHub WebHook triggered!', data.comment.body);
    context.res = { body: 'New GitHub comment: ' + data.comment.body };
    context.done();
};
```


## <a name="next-steps"></a>다음 단계
[!INCLUDE [next steps](../../includes/functions-bindings-next-steps.md)]


