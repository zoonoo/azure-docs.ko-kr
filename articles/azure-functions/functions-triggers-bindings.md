<properties
	pageTitle="Azure Functions 트리거 및 바인딩 | Microsoft Azure"
	description="Azure Functions에서 트리거 및 바인딩을 사용하는 방법을 이해합니다."
	services="functions"
	documentationCenter="na"
	authors="christopheranderson"
	manager="erikre"
	editor=""
	tags=""
	keywords="Azure 함수, 함수, 이벤트 처리, webhook, 동적 계산, 서버가 없는 아키텍처"/>

<tags
	ms.service="functions"
	ms.devlang="multiple"
	ms.topic="reference"
	ms.tgt_pltfrm="multiple"
	ms.workload="na"
	ms.date="04/07/2016"
	ms.author="chrande"/>

# Azure Functions 트리거 및 바인딩 개발자 참조

이 문서에서는 Azure Functions에서 트리거 및 바인딩을 구성하고 코딩하는 방법을 설명합니다. 대부분의 이러한 바인딩은 Azure 포털의 **통합** UI를 통해 쉽게 관리될 수 있지만 포털은 각 바인딩에 대한 모든 기능 및 옵션을 설명하지 않습니다.

이 문서에서는 [Azure Functions 개발자 참조](functions-reference.md) 및 [C#](functions-reference-csharp.md) 또는 [노드](functions-reference-node.md) 개발자 참조 문서를 이미 읽었다고 가정합니다.

## HTTP 및 WebHook 트리거 및 바인딩

HTTP 또는 WebHook 트리거를 사용하여 HTTP 요청에 대한 응답으로 함수를 호출할 수 있습니다. 요청은 API 키를 포함해야 하며 이는 현재 Azure 포털 UI에서 제공합니다.

함수 URL은 다음과 같이 함수 앱 URL 및 함수 이름을 결합합니다.

```
 https://{function app name}.azurewebsites.net/api/{function name} 
```

*function.json* 파일은 HTTP 요청 및 응답에 속하는 속성을 제공합니다.

HTTP 요청에 대한 속성:

- `name` : 요청 개체(또는 Node.js 함수의 경우 요청 본문)에 대한 함수 코드에 사용되는 변수 이름입니다.
- `type` : *httpTrigger*로 설정되어 있어야 합니다.
- `direction`: *in*으로 설정되어야 합니다. 
- `webHookType` : WebHook 트리거의 경우 유효한 값은 *github*, *slack* 및 *genericJson*입니다. WebHook이 아닌 HTTP 트리거의 경우 이 속성을 빈 문자열로 설정합니다. WebHook에 대한 자세한 내용은 [WebHook 트리거](#webhook-triggers) 섹션을 참조하세요.
- `authLevel` : WebHook 트리거에 적용되지 않습니다. "함수"가 API 키를 요구하도록 설정하거나 "익명"이 API 키 요구 사항을 삭제하도록 설정하거나 "관리자"가 마스터 API 키를 요구하도록 설정합니다. 자세한 내용은 아래 [API 키](#apikeys)를 참조하세요.

HTTP 응답에 대한 속성:

- `name` : 응답 개체에 대한 함수 코드에 사용되는 변수 이름입니다.
- `type`: *http*로 설정되어야 합니다.
- `direction`: *out*으로 설정되어야 합니다. 
 
예제 *function.json*:

```json
{
  "bindings": [
    {
      "webHookType": "",
      "name": "req",
      "type": "httpTrigger",
      "direction": "in"
      "authLevel": "function"
    },
    {
      "name": "res",
      "type": "http",
      "direction": "out"
    }
  ],
  "disabled": false
}
```

### WebHook 트리거

WebHook 트리거는 WebHook을 위해 디자인된 다음과 같은 기능을 포함하는 HTTP 트리거입니다.

* 특정 WebHook 공급자(현재 GitHub와 Slack 지원)의 경우 함수 런타임은 공급자 서명의 유효성을 검사합니다.
* Node.js 함수의 경우 함수 런타임은 요청 개체 대신 요청 본문을 제공합니다. 매개 변수 형식을 지정하여 제공되는 항목을 제어하기 때문에 C# 함수에 대한 특별한 처리가 없습니다. `HttpRequestMessage`을 지정하는 경우 요청 개체를 가져옵니다. POCO 형식을 지정하는 경우 함수 런타임은 요청 본문에서 JSON 개체를 구문 분석하려고 시도하여 개체 속성을 채웁니다.
* WebHook 함수를 트리거하려면 HTTP 요청이 API 키를 포함해야 합니다. WebHook HTTP가 아닌 트리거의 경우 이 요구 사항은 선택 사항입니다.

GitHub WebHook을 설정하는 방법에 대한 내용은 [GitHub 개발자 - WebHook 만들기](http://go.microsoft.com/fwlink/?LinkID=761099&clcid=0x409)를 참조하세요.

### API 키

기본적으로 API 키는 HTTP 또는 WebHook 함수를 트리거하는 HTTP 요청에 포함되어야 합니다. 키는 `code`이라는 쿼리 문자열 변수에 포함되거나 `x-functions-key` HTTP 헤더에 포함될 수 있습니다. WebHook이 아닌 함수의 경우 `authLevel` 속성을 *function.json* 파일의 "익명"으로 설정하여 API 키가 필요하지 않음을 알릴 수 있습니다.

API 키 값을 함수 앱에 있는 파일 시스템의 *D:\\home\\data\\Functions\\secrets* 폴더에서 찾을 수 있습니다. 마스터 키 및 함 수 키는 이 예제에 표시된 대로 *host.json* 파일에서 설정됩니다.

```json
{
  "masterKey": "K6P2VxK6P2VxK6P2VxmuefWzd4ljqeOOZWpgDdHW269P2hb7OSJbDg==",
  "functionKey": "OBmXvc2K6P2VxK6P2VxK6P2VxVvCdB89gChyHbzwTS/YYGWWndAbmA=="
}
```

*host.json*에서 기능 키는 모든 함수를 트리거하는 데 사용할 수 있지만 사용하지 않는 함수를 트리거하지 않습니다. 마스터 키는 모든 함수를 트리거하는 데 사용할 수 있고 사용하지 않는 경우에도 함수를 트리거합니다. `authLevel` 속성을 "관리자"로 설정하여 마스터 키를 요청하는 함수를 구성할 수 있습니다.

*비밀* 폴더가 함수와 동일한 이름을 가진 JSON 파일을 포함하는 경우 해당 파일의 `key` 속성은 함수를 트리거하는 데에도 사용할 수 있고 이 키는 참조하는 함수에서만 작동합니다. 예를 들어 `HttpTrigger`이라는 함수에 대한 API 키는 *비밀* 폴더의 *HttpTrigger.json*에 지정됩니다. 다음은 예제입니다.

```json
{
  "key":"0t04nmo37hmoir2rwk16skyb9xsug32pdo75oce9r4kg9zfrn93wn4cx0sxo4af0kdcz69a4i"
}
```

> [AZURE.NOTE] WebHook 트리거를 설정할 경우 마스터 키를 WebHook 공급자와 공유하지 마세요. WebHook을 처리하는 함수로만 작동하는 키를 사용합니다. 마스터 키는 사용하지 않는 함수를 포함한 모든 함수를 트리거하는 데 사용할 수 있습니다.

### HTTP 트리거 함수에 대한 예제 C# 코드 

예제 코드는 쿼리 문자열 또는 HTTP 요청의 본문에서 `name` 매개 변수를 찾습니다.

```csharp
using System.Net;
using System.Threading.Tasks;

public static async Task<HttpResponseMessage> Run(HttpRequestMessage req, TraceWriter log)
{
    log.Verbose($"C# HTTP trigger function processed a request. RequestUri={req.RequestUri}");

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

### HTTP 트리거 함수에 대한 예제 Node.js 코드 

이 예제 코드는 쿼리 문자열 또는 HTTP 요청의 본문에서 `name` 매개 변수를 찾습니다.

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

### GitHub WebHook 함수에 대한 예제 C# 코드 

이 예제 코드는 GitHub 문제 설명을 기록합니다.

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

    log.Verbose($"WebHook was triggered! Comment: {data.comment.body}");

    return req.CreateResponse(HttpStatusCode.OK, new {
        body = $"New GitHub comment: {data.comment.body}"
    });
}
```

### GitHub WebHook 함수에 대한 예제 Node.js 코드 

이 예제 코드는 GitHub 문제 설명을 기록합니다.

```javascript
module.exports = function (context, data) {
    context.log('GitHub WebHook triggered!', data.comment.body);
    context.res = { body: 'New GitHub comment: ' + data.comment.body };
    context.done();
};
```

## 타이머 트리거

*function.json* 파일은 함수를 즉시 트리거해야 하는지 여부를 나타내는 일정 식 및 스위치를 제공합니다.

```json
{
  "bindings": [
    {
      "schedule": "0 * * * * *",
      "runOnStartup": true,
      "name": "myTimer",
      "type": "timerTrigger",
      "direction": "in"
    }
  ],
  "disabled": false
}
```

타이머 트리거는 다중 인스턴스 확장을 자동으로 처리합니다. 특정 타이머 함수의 단일 인스턴스만이 모든 인스턴스에 실행됩니다.

### 일정 식의 서식

일정 식은 다음과 같은 6개의 필드를 포함하는 [CRON 식](http://en.wikipedia.org/wiki/Cron#CRON_expression)일 수 있습니다. {초} {분} {시간} {일} {월} {요일}. 온라인에서 찾은 cron 식 문서는 대부분 {두 번째} 필드를 생략하므로 해당 필드 중 하나를 복사하면 추가 필드에 대해 조정해야 합니다.

일정 식은 함수가 트리거되는 간격의 지연 시간을 지정하는 *hh:mm:ss* 서식일 수도 있습니다.

다음은 몇 가지 일정 식의 예입니다.

5분마다 한 번씩 트리거하려면:

```json
"schedule": "0 */5 * * * *",
"runOnStartup": false,
```

즉시 트리거한 다음 이후 두 시간마다 트리거하려면:

```json
"schedule": "0 0 */2 * * *",
"runOnStartup": true,
```

15초마다 트리거하려면:

```json
"schedule": "00:00:15",
"runOnStartup": false,
```

### 타이머 트리거 C# 코드 예제

이 C# 코드 예제에서는 함수가 트리거될 때마다 단일 로그를 작성합니다.

```csharp
public static void Run(TimerInfo myTimer, TraceWriter log)
{
    log.Verbose($"C# Timer trigger function executed at: {DateTime.Now}");    
}
```

## Azure 저장소(큐, Blob, 테이블) 트리거 및 바인딩

이 섹션은 다음 하위 섹션을 포함합니다.

* [function.json의 Azure 저장소 연결 속성](#storageconnection)
* [Azure 저장소 큐 트리거](#storagequeuetrigger)
* [Azure 저장소 큐 출력 바인딩](#storagequeueoutput)
* [Azure 저장소 Blob 트리거](#storageblobtrigger)
* [Azure 저장소 Blob 입력 및 출력 바인딩](#storageblobbindings)
* [Azure 저장소 테이블 입력 및 출력 바인딩](#storagetablesbindings)

### <a id="storageconnection"></a> function.json의 Azure 저장소 연결 속성

모든 Azure 저장소 트리거 및 바인딩의 경우 *function.json* 파일은 `connection` 속성을 포함합니다. 예:

```json
{
    "disabled": false,
    "bindings": [
        {
            "name": "myQueueItem",
            "type": "queueTrigger",
            "direction": "in",
            "queueName": "myqueue-items",
            "connection":""
        }
    ]
}
```

`connection`을 비워 두면 트리거 또는 바인딩은 함수 앱에 대한 기본 저장소 계정으로 작동합니다. 트리거 또는 바인딩을 다른 저장소 계정으로 작동하도록 하려는 경우 사용하려는 저장소 계정을 가리키는 함수 앱에 앱 설정을 만들고 `connection`를 앱 설정 이름으로 설정합니다. 앱 설정을 추가하려면 다음 단계를 수행합니다.

1. Azure 포털의 **함수 앱** 블레이드에서 **함수 앱 설정 > 앱 서비스 설정으로 이동**을 클릭합니다.

2. **설정** 블레이드에서 **응용 프로그램 설정**을 클릭합니다.

3. **앱 설정** 섹션 아래로 스크롤하여 **키** = *{선택한 일부 고유 값}* 및 **값** = 저장소 계정에 대한 연결 문자열로 항목을 추가합니다.

### <a id="storagequeuetrigger"></a> Azure 저장소 큐 트리거

*function.json* 파일은 폴링할 큐의 이름 및 큐 메시지에 대한 변수 이름을 제공합니다. 예:

```json
{
    "disabled": false,
    "bindings": [
        {
            "name": "myQueueItem",
            "type": "queueTrigger",
            "direction": "in",
            "queueName": "myqueue-items",
            "connection":""
        }
    ]
}
```

#### 큐 트리거를 지원하는 형식

이러한 형식 중 하나에 큐 메시지를 역직렬화할 수 있습니다.

* `string`
* `byte[]`
* JSON 개체   
* `CloudQueueMessage`

#### 큐 트리거 메타데이터

이러한 변수 이름을 사용하여 함수에서 큐 메타데이터를 얻을 수 있습니다.

* expirationTime
* insertionTime
* nextVisibleTime
* id
* popReceipt
* dequeueCount
* queueTrigger(큐 메시지 텍스트를 문자열로 검색하는 다른 방법)

이 C# 코드 예제에서는 큐 메타데이터를 검색하고 기록합니다.

```csharp
public static void Run(string myQueueItem, 
    DateTimeOffset expirationTime, 
    DateTimeOffset insertionTime, 
    DateTimeOffset nextVisibleTime,
    string queueTrigger,
    string id,
    string popReceipt,
    int dequeueCount,
    TraceWriter log)
{
    log.Verbose($"C# Queue trigger function processed: {myQueueItem}\n" +
        $"queueTrigger={queueTrigger}\n" +
        $"expirationTime={expirationTime}\n" +
        $"insertionTime={insertionTime}\n" +
        $"nextVisibleTime={nextVisibleTime}\n" +
        $"id={id}\n" +
        $"popReceipt={popReceipt}\n" + 
        $"dequeueCount={dequeueCount}");
}
```

#### 포이즌 큐 메시지 처리

함수가 실패하게 만드는 내용이 포함된 메시지를 *포이즌 메시지*라고 합니다. 함수가 실패할 경우 큐 메시지가 삭제되지 않고 결국 다시 선택되어 주기가 반복됩니다. SDK에서 제한된 반복 횟수 후에 자동으로 주기를 중단하거나 수동으로 중단할 수 있습니다.

SDK는 최대 5회까지 함수를 호출하여 큐 메시지를 처리합니다. 다섯 번째 시도가 실패하면 메시지가 포이즌 큐로 이동됩니다.

포이즌 큐의 이름은 *{originalqueuename}*-poison으로 지정됩니다. 메시지를 기록하거나 수동 작업이 필요하다는 알림을 보내 포이즌 큐의 메시지를 처리하는 함수를 작성할 수 있습니다.

포이즌 메시지를 수동으로 처리하려는 경우 `dequeueCount`을 확인하여 처리를 위해 메시지를 선택한 횟수를 가져올 수 있습니다.

### <a id="storagequeueoutput"></a> Azure 저장소 큐 출력 바인딩

*function.json* 파일은 출력 큐의 이름 및 메시지의 콘텐츠에 대한 변수 이름을 제공합니다. 이 예제에서는 큐 트리거를 사용하고 큐 메시지를 작성합니다.

```json
{
  "bindings": [
    {
      "queueName": "myqueue-items",
      "connection": "",
      "name": "myQueueItem",
      "type": "queueTrigger",
      "direction": "in"
    },
    {
      "name": "myQueue",
      "type": "queue",
      "queueName": "samples-workitems-out",
      "connection": "",
      "direction": "out"
    }
  ],
  "disabled": false
}
``` 

#### 큐 출력 바인딩을 지원하는 형식

`queue` 바인딩은 큐 메시지에 다음 형식을 직렬화할 수 있습니다.

* `string`(함수가 종료될 때 매개 변수 값이 null이 아닌 경우 큐 메시지 생성)
* `byte[]`(문자열처럼 작동) 
* `CloudQueueMessage`(문자열처럼 작동) 
* JSON 개체(함수가 종료될 때 매개 변수가 null인 경우 null 개체가 포함된 메시지 생성)

#### 큐 출력 바인딩 코드 예제

이 C# 코드 예제에서는 각 입력 큐 메시지에 단일 출력 큐 메시지를 작성합니다.

```csharp
public static void Run(string myQueueItem, out string myOutputQueueItem, TraceWriter log)
{
    myOutputQueueItem = myQueueItem + "(next step)";
}
```

이 C# 코드 예제는 `ICollector<T>`을 사용하여 여러 메시지를 작성합니다(비동기 함수에 `IAsyncCollector<T>`을 사용).

```csharp
public static void Run(string myQueueItem, ICollector<string> myQueue, TraceWriter log)
{
    myQueue.Add(myQueueItem + "(step 1)");
    myQueue.Add(myQueueItem + "(step 2)");
}
```

### <a id="storageblobtrigger"></a> Azure 저장소 Blob 트리거

*function.json*은 모니터링할 컨테이너를 지정하는 경로 및 Blob 이름 패턴을 선택적으로 제공합니다. 이 예제에서는 샘플 작업 항목 컨테이너에 추가되는 Blob를 트리거합니다.

```json
{
    "disabled": false,
    "bindings": [
        {
            "name": "myBlob",
            "type": "blobTrigger",
            "direction": "in",
            "path": "samples-workitems",
            "connection":""
        }
    ]
}
```

> [AZURE.NOTE] 트리거하는 Blob 컨테이너가 10,000개 이상의 Blob를 포함하는 모니터링인 경우 함수 런타임은 로그 파일을 스캔하여 새롭거나 변경된 Blob를 확인합니다. 이 프로세스는 실시간으로 처리되지 않으므로 Blob을 만든 후 몇 분이 경과할 때까지 함수가 트리거되지 않을 수도 있습니다. 또한 [저장소 로그를 만들기 위해 "최선"](https://msdn.microsoft.com/library/azure/hh343262.aspx)을 다하고 있지만 모든 이벤트를 캡처하는 것을 보장하지는 않습니다. 경우에 따라 로그가 누락될 수 있습니다. 응용 프로그램에서 큰 컨테이너에 대한 Blob 트리거의 빠르고 안정성 있는 제한 사항을 사용할 수 없는 경우 Blob를 만들 때 큐 메시지를 만들고 Blob를 처리하는 Blob 트리거 대신 큐 트리거를 사용하는 것이 좋습니다.

#### Blob 트리거를 지원하는 형식

Blob는 이러한 형식에 역직렬화할 수 있습니다.

* string
* `TextReader`
* `Stream`
* `ICloudBlob`
* `CloudBlockBlob`
* `CloudPageBlob`
* `CloudBlobContainer`
* `CloudBlobDirectory`
* `IEnumerable<CloudBlockBlob>`
* `IEnumerable<CloudPageBlob>`
* [ICloudBlobStreamBinder](../app-service-web/websites-dotnet-webjobs-sdk-storage-blobs-how-to.md#icbsb)에 의해 deserialize된 다른 유형 

#### Blob 트리거 C# 코드 예제

이 C# 코드 예제에서는 컨테이너에 추가된 각 Blob의 콘텐츠를 기록합니다.

```csharp
public static void Run(string myBlob, TraceWriter log)
{
    log.Verbose($"C# Blob trigger function processed: {myBlob}");
}
```

#### Blob 트리거 이름 패턴

`path`에 Blob 이름 패턴을 지정할 수 있습니다. 예:

```json
"path": "input/original-{name}",
```

이 경로는 *입력* 컨테이너에서 *original-Blob1.txt*라는 Blob에 있으며 함수 코드에 있는 `name` 변수의 값은 `Blob1`입니다.

다른 예제:

```json
"path": "input/{blobname}.{blobextension}",
```

또한 이 경로는 *original-Blob1.txt*라는 Blob에 있으며 함수 코드에 있는 `blobname` 및 `blobextension` 변수의 값은 *original-Blob1* 및 *txt*입니다.

파일 확장명에 대한 고정된 값으로 패턴을 지정하여 함수를 트리거하는 Blob의 종류를 제한할 수 있습니다. `path`을 *samples/{name}.png*로 설정하는 경우 *샘플* 컨테이너 *.png* Blob는 함수를 트리거합니다.

이름에 중괄호가 있는 Blob 이름에 대한 이름 패턴을 지정해야 하는 경우 이중 중괄호를 사용합니다. 예를 들어 *images* 컨테이너에서 이름이 다음과 같은 Blob를 찾은 경우

		{20140101}-soundfile.mp3

`path` 속성에 사용합니다.

		images/{{20140101}}-{name}

예제에서는 `name` 변수 값은 *soundfile.mp3*입니다.

#### Blob 수신 확인

Azure Functions 런타임은 동일한 새 Blob 또는 업데이트된 Blob에 대해 Blob 트리거 함수가 두 번 이상 호출되지 않도록 합니다. 이를 위해 *blob 수신 확인*을 유지 관리하여 지정된 Blob 버전이 처리되었는지 확인합니다.

Blob 수신 확인은 AzureWebJobsStorage 연결 문자열에 지정된 Azure 저장소 계정의 *azure-webjobs-hosts*라는 컨테이너에 저장됩니다. Blob 수신 확인에는 다음 정보가 포함됩니다.

* Blob에 대해 호출된 함수("**{함수 앱 이름}*.Functions.*{함수 이름}*", 예: "functionsf74b96f7.Functions.CopyBlob")
* 컨테이너 이름
* Blob 유형("BlockBlob" 또는 "PageBlob")
* Blob 이름
* ETag(Blob 버전 식별자, 예: "0x8D1DC6E70A277EF")

Blob를 강제로 처리하려면 *azure-webjobs-hosts* 컨테이너에서 해당 Blob에 대한 Blob 수신 확인을 수동으로 삭제하면 됩니다.

#### 포이즌 Blob 처리

Blob 트리거 함수가 일시적인 오류로 인해 실패할 경우 SDK에서 이 함수를 다시 호출합니다. 그러나 Blob의 콘텐츠로 인해 실패한 경우에는 Blob을 처리하려고 할 때마다 함수가 실패합니다. 기본적으로 SDK는 지정된 Blob에 대해 함수를 최대 5번 호출합니다. 5번의 시도에 실패하면 *webjobs-blobtrigger-poison*이라는 큐에 메시지를 추가합니다.

포이즌 Blob에 대한 큐 메시지는 다음 속성을 포함하는 JSON 개체입니다.

* FunctionId(*{함수 앱 이름}*.함수.*{함수 이름}* 형식)
* BlobType("BlockBlob" 또는 "PageBlob")
* ContainerName
* BlobName
* ETag(Blob 버전 식별자, 예: "0x8D1DC6E70A277EF")

### <a id="storageblobbindings"></a> Azure 저장소 Blob 입력 및 출력 바인딩

*function.json*은 Blob 이름 및 콘텐츠에 컨테이너의 이름 및 변수 이름을 제공합니다. 이 예제에서는 큐 트리거를 사용하여 Blob를 복사합니다.

```json
{
  "bindings": [
    {
      "queueName": "myqueue-items",
      "connection": "",
      "name": "myQueueItem",
      "type": "queueTrigger",
      "direction": "in"
    },
    {
      "name": "myInputBlob",
      "type": "blob",
      "path": "samples-workitems/{queueTrigger}",
      "connection": "",
      "direction": "in"
    },
    {
      "name": "myOutputBlob",
      "type": "blob",
      "path": "samples-workitems/{queueTrigger}-Copy",
      "connection": "",
      "direction": "out"
    }
  ],
  "disabled": false
}
``` 

#### Blob 입력 및 출력을 지원하는 형식

`blob` 바인딩은 다음 형식을 직렬화 또는 역직렬화할 수 있습니다.

* `Stream`
* `TextReader`
* `TextWriter`
* `string`(출력 Blob의 경우, 함수가 반환될 때 문자열 매개 변수가 null이 아닌 경우에만 Blob 생성)
* JSON 개체(출력 Blob의 경우, 함수가 종료될 때 매개 변수 값이 null이면 Blob를 null 개체로 생성)
* `CloudBlobStream`(출력에만 해당)
* `ICloudBlob`
* `CloudBlockBlob` 
* `CloudPageBlob` 

#### Blob 출력 C# 코드 예제

이 C# 코드 예제에서는 이름이 큐 메시지에 수신되는 Blob를 복사합니다.

```CSHARP
public static void Run(string myQueueItem, string myInputBlob, out string myOutputBlob, TraceWriter log)
{
    log.Verbose($"C# Queue trigger function processed: {myQueueItem}");
    myOutputBlob = myInputBlob;
}
```

### <a id="storagetablesbindings"></a> Azure 저장소 테이블 입력 및 출력 바인딩

저장소 테이블에 대한 *function.json*은 여러 속성을 제공합니다.

* `name` - 테이블 바인딩에 대한 코드에서 사용하는 변수의 이름입니다.
* `tableName`
* `partitionKey` 및 `rowKey` - 함께 사용되어 C# 또는 노드 함수에서 단일 엔터티를 읽거나 노드 함수에서 단일 엔터티를 작성합니다.
* `take` - 노드 함수에서 테이블 입력에 대해 읽는 행의 최대 수입니다.
* `filter` - 노드 함수에서 테이블 입력에 대한 OData 필터 식입니다.

이러한 속성은 다음과 같은 시나리오를 지원합니다.

* C# 또는 노드 함수에서 단일 행을 읽습니다.

	`partitionKey` 및 `rowKey`를 설정합니다. `filter` 및 `take` 속성은 이 시나리오에서 사용되지 않습니다.

* C# 함수에서 여러 행을 읽습니다.

	함수 런타임은 테이블에 바인딩된 `IQueryable<T>` 개체를 제공합니다. `T` 형식은 `TableEntity`에서 파생되거나 `ITableEntity`를 구현해야 합니다. `partitionKey`, `rowKey`, `filter` 및 `take` 속성은 이 시나리오에서 사용되지 않습니다. `IQueryable` 개체를 사용하여 필요한 모든 필터링 작업을 수행할 수 있습니다.

* 노드 함수에서 여러 행을 읽습니다.

	`filter` 및 `take` 속성을 설정합니다. `partitionKey` 또는 `rowKey`를 설정하지 않습니다.

* C# 함수에서 하나 이상의 행을 작성합니다.

	함수 런타임에서는 테이블에 바인딩된 `ICollector<T>` 또는 `IAsyncCollector<T>`를 제공하며 여기서 `T`은 추가하려는 엔터티의 스키마를 지정합니다. 일반적으로 `T` 형식은 `TableEntity`에서 파생되거나 `ITableEntity`을 구현하지만 반드시 그런 것은 아닙니다. `partitionKey`, `rowKey`, `filter` 및 `take` 속성은 이 시나리오에서 사용되지 않습니다.

#### C# 또는 노드에서 단일 테이블 엔터티 읽기

이 *function.json* 예제에서는 큐 트리거를 사용하여 하드 코딩된 파티션 키 값 및 큐 메시지에서 제공하는 행 키를 포함하는 단일 테이블 행을 읽습니다.

```json
{
  "bindings": [
    {
      "queueName": "myqueue-items",
      "connection": "",
      "name": "myQueueItem",
      "type": "queueTrigger",
      "direction": "in"
    },
    {
      "name": "personEntity",
      "type": "table",
      "tableName": "Person",
      "partitionKey": "Test",
      "rowKey": "{queueTrigger}",
      "connection": "",
      "direction": "in"
    }
  ],
  "disabled": false
}
```
다음 C# 코드 예제에서는 앞의 *function.json* 파일로 작업하여 단일 테이블 엔터티를 읽습니다. 큐 메시지에는 행 키 값이 있고 *run.csx* 파일에 정의된 형식으로 테이블 엔터티를 읽습니다. 형식은 `PartitionKey` 및 `RowKey` 속성을 포함하며 `TableEntity`에서 파생되지 않습니다.

```csharp
public static void Run(string myQueueItem, Person personEntity, TraceWriter log)
{
    log.Verbose($"C# Queue trigger function processed: {myQueueItem}");
    log.Verbose($"Name in Person entity: {personEntity.Name}");
}

public class Person
{
    public string PartitionKey { get; set; }
    public string RowKey { get; set; }
    public string Name { get; set; }
}
```

또한 다음 노드 코드 예제에서는 앞의 *function.json* 파일로 작업하여 단일 테이블 엔터티를 읽습니다.

```javascript
module.exports = function (context, myQueueItem) {
    context.log('Node.js queue trigger function processed work item', myQueueItem);
    context.log('Person entity name: ' + context.bindings.personEntity.Name);
    context.done();
};
```

#### C에서 여러 테이블 엔터티 읽기# 

다음 *function.json* 및 C# 코드 예제에서는 큐 메시지에 지정된 파티션 키에 대한 엔터티를 읽습니다.

```json
{
  "bindings": [
    {
      "queueName": "myqueue-items",
      "connection": "",
      "name": "myQueueItem",
      "type": "queueTrigger",
      "direction": "in"
    },
    {
      "name": "tableBinding",
      "type": "table",
      "tableName": "Person",
      "direction": "in"
    }
  ],
  "disabled": false
}
```

C# 코드는 엔터티 형식이 `TableEntity`에서 파생할 수 있도록 Azure 저장소 SDK에 대한 참조를 추가합니다.

```csharp
#r "Microsoft.WindowsAzure.Storage"
using Microsoft.WindowsAzure.Storage.Table;

public static void Run(string myQueueItem, IQueryable<Person> tableBinding, TraceWriter log)
{
    log.Verbose($"C# Queue trigger function processed: {myQueueItem}");
    foreach (Person person in tableBinding.Where(p => p.PartitionKey == myQueueItem).ToList())
    {
        log.Verbose($"Name: {person.Name}");
    }
}

public class Person : TableEntity
{
    public string Name { get; set; }
}
``` 

#### C에서 테이블 엔터티 만들기# 

다음 *function.json* 및 *run.csx* 예제에서는 C#에서 테이블 엔터티를 작성하는 방법을 보여줍니다.

```json
{
  "bindings": [
    {
      "name": "input",
      "type": "manualTrigger",
      "direction": "in"
    },
    {
      "tableName": "Person",
      "connection": "",
      "name": "tableBinding",
      "type": "table",
      "direction": "out"
    }
  ],
  "disabled": false
}
```

```csharp
public static void Run(string input, ICollector<Person> tableBinding, TraceWriter log)
{
    for (int i = 1; i < 10; i++)
        {
            log.Verbose($"Adding Person entity {i}");
            tableBinding.Add(
                new Person() { 
                    PartitionKey = "Test", 
                    RowKey = i.ToString(), 
                    Name = "Name" + i.ToString() }
                );
        }

}

public class Person
{
    public string PartitionKey { get; set; }
    public string RowKey { get; set; }
    public string Name { get; set; }
}

```

#### 노드에서 테이블 엔터티 만들기

다음 *function.json* 및 *run.csx* 예제에서는 노드에서 테이블 엔터티를 작성하는 방법을 보여줍니다.

```json
{
  "bindings": [
    {
      "queueName": "myqueue-items",
      "connection": "",
      "name": "myQueueItem",
      "type": "queueTrigger",
      "direction": "in"
    },
    {
      "tableName": "Person",
      "partitionKey": "Test",
      "rowKey": "{queueTrigger}",
      "connection": "",
      "name": "personEntity",
      "type": "table",
      "direction": "out"
    }
  ],
  "disabled": true
}
```

```javascript
module.exports = function (context, myQueueItem) {
    context.log('Node.js queue trigger function processed work item', myQueueItem);
    context.bindings.personEntity = {"Name": "Name" + myQueueItem }
    context.done();
};
```

## Azure DocumentDB 바인딩

이 섹션은 다음 하위 섹션을 포함합니다.

* [Azure DocumentDB 입력 바인딩](#docdbinput)
* [Azure DocumentDB 출력 바인딩](#docdboutput)

### <a id="docdbinput"></a> Azure DocumentDB 입력 바인딩

입력 바인딩은 DocumentDB 컬렉션에서 문서를 로드하고 바인딩에 직접 전달할 수 있습니다. 함수를 호출한 트리거에 따라 문서 ID를 결정할 수 있습니다. C# 함수에서 함수가 성공적으로 종료될 경우 레코드에 변경한 내용을 자동으로 다시 컬렉션에 전송합니다.

function.json 파일은 DocumentDB 입력 바인딩과 함께 사용하도록 다음 속성을 제공합니다.

- `name` : 문서에 대한 함수 코드에 사용되는 변수 이름입니다.
- `type` : "documentdb"로 설정해야 합니다.
- `databaseName` : 문서를 포함하는 데이터베이스입니다.
- `collectionName` : 문서를 포함하는 컬렉션입니다.
- `id` : 검색할 문서의 ID입니다. 이 속성은 "{queueTrigger}"와 유사한 바인딩을 지원하며 이는 큐 메시지의 문자열 값을 문서 ID로 사용합니다
- `connection` : 이 문자열은 DocumentDB 계정에 대한 끝점에 설정된 응용 프로그램 설정이어야 합니다. 통합 탭에서 계정을 선택하는 경우 yourAccount\_DOCUMENTDB 형식을 사용하는 이름으로 새 앱 설정이 만들어집니다. 앱 설정을 수동으로 만들어야 하는 경우 실제 연결 문자열은 AccountEndpoint=<Endpoint for your account>;AccountKey=<Your primary access key>; 형식을 사용해야 합니다.
- `direction: *"in"*으로 설정되어야 합니다.

예제 function.json:
 
	{
	  "bindings": [
	    {
	      "name": "document",
	      "type": "documentdb",
	      "databaseName": "MyDatabase",
	      "collectionName": "MyCollection",
	      "id" : "{queueTrigger}",
	      "connection": "MyAccount_DOCUMENTDB",     
	      "direction": "in"
	    }
	  ],
	  "disabled": false
	}

#### C# 큐 트리거에 대한 Azure DocumentDB 입력 코드 예제
 
위의 function.json 예제를 사용하여 DocumentDB 입력 바인딩이 큐 메시지 문자열과 일치하는 ID로 문서를 검색하고 '문서' 매개 변수에 전달합니다. 해당 문서가 없는 경우 '문서' 매개 변수가 null이 됩니다. 문서는 함수가 종료되는 경우 새 텍스트 값으로 업데이트됩니다.
 
	public static void Run(string myQueueItem, dynamic document)
	{   
	    document.text = "This has changed.";
	}

#### Node.js 큐 트리거에 대한 Azure DocumentDB 입력 코드 예제
 
위의 function.json 예제를 사용하여 DocumentDB 입력 바인딩이 큐 메시지 문자열과 일치하는 ID로 문서를 검색하고 `documentIn` 바인딩 속성에 전달합니다. Node.js 함수에서 업데이트된 문서를 컬렉션에 다시 전송하지 않습니다. 그러나 입력 바인딩을 직접 `documentOut`라는 DocumentDB 출력 바인딩에 전달하여 업데이트를 지원할 수 있습니다. 이 코드 예제에서는 입력 문서의 text 속성을 업데이트하고 출력 문서로 설정합니다.
 
	module.exports = function (context, input) {   
	    context.bindings.documentOut = context.bindings.documentIn;
	    context.bindings.documentOut.text = "This was updated!";
	    context.done();
	};

### <a id="docdboutput"></a> Azure DocumentDB 출력 바인딩

함수는 **Azure DocumentDB 문서** 출력 바인딩을 사용하여 Azure DocumentDB 데이터베이스에 JSON 문서를 작성할 수 있습니다. Azure DocumentDB에 대한 자세한 내용은 [DocumentDB 소개](../documentdb/documentdb-introduction.md) 및 [시작 자습서](../documentdb/documentdb-get-started.md)를 검토합니다.

function.json 파일은 DocumentDB 출력 바인딩과 함께 사용하도록 다음 속성을 제공합니다.

- `name` : 새 문서에 대한 함수 코드에 사용되는 변수 이름입니다.
- `type` : *"documentdb"*로 설정해야 합니다.
- `databaseName` : 새 문서를 만들 경우 컬렉션을 포함하는 데이터베이스입니다.
- `collectionName` : 새 문서를 만들 경우의 컬렉션입니다.
- `createIfNotExists` : 컬렉션이 존재하지 않는 경우 만들 수 있는지 여부를 나타내는 부울 값입니다. 기본값은 *false*입니다. 새 컬렉션에 처리량이 예약된 상태로 만들어지면 가격 책정 면에서 의미하는 바가 있기 때문입니다. 자세한 내용은 [가격 책정 페이지](https://azure.microsoft.com/pricing/details/documentdb/)를 참조하세요.
- `connection` : 이 문자열은 DocumentDB 계정에 대한 끝점에 설정된 **응용 프로그램 설정**이어야 합니다. **통합** 탭에서 계정을 선택하는 경우 `yourAccount_DOCUMENTDB` 형식을 사용하는 이름으로 새 앱 설정이 만들어집니다. 앱 설정을 수동으로 만들어야 하는 경우 실제 연결 문자열은 `AccountEndpoint=<Endpoint for your account>;AccountKey=<Your primary access key>;` 형식을 사용해야 합니다. 
- `direction`: *"out"*으로 설정되어야 합니다. 
 
예제 function.json:

	{
	  "bindings": [
	    {
	      "name": "document",
	      "type": "documentdb",
	      "databaseName": "MyDatabase",
	      "collectionName": "MyCollection",
	      "createIfNotExists": false,
	      "connection": "MyAccount_DOCUMENTDB",
	      "direction": "out"
	    }
	  ],
	  "disabled": false
	}


#### Node.js 큐 트리거에 대한 Azure DocumentDB 출력 코드 예제

	module.exports = function (context, input) {
	   
	    context.bindings.document = {
	        text : "I'm running in a Node function! Data: '" + input + "'"
	    }   
	 
	    context.done();
	};

출력 문서:

	{
	  "text": "I'm running in a Node function! Data: 'example queue data'",
	  "id": "01a817fe-f582-4839-b30c-fb32574ff13f"
	}
 

#### C# 큐 트리거에 대한 Azure DocumentDB 출력 코드 예제


	using System;

	public static void Run(string myQueueItem, out object document, TraceWriter log)
	{
	    log.Verbose($"C# Queue trigger function processed: {myQueueItem}");
	   
	    document = new {
	        text = $"I'm running in a C# function! {myQueueItem}"
	    };
	}


#### Azure DocumentDB 출력 코드 예제 설정 파일 이름

함수에 문서의 이름을 설정하려는 경우 `id` 값을 설정합니다. 예를 들어 직원에 대한 JSON 콘텐츠가 다음과 유사하게 큐에서 삭제된 경우:

	{
	  "name" : "John Henry",
      "employeeId" : "123456",
	  "address" : "A town nearby"
	}

큐 트리거 함수에서 다음 C# 코드를 사용할 수 있습니다.
	
	#r "Newtonsoft.Json"
	
	using System;
	using Newtonsoft.Json;
	using Newtonsoft.Json.Linq;
	
	public static void Run(string myQueueItem, out object employeeDocument, TraceWriter log)
	{
	    log.Verbose($"C# Queue trigger function processed: {myQueueItem}");
	    
	    dynamic employee = JObject.Parse(myQueueItem);
	    
	    employeeDocument = new {
	        id = employee.name + "-" + employee.employeeId,
	        name = employee.name,
	        employeeId = employee.employeeId,
	        address = employee.address
	    };
	}

예제 출력:

	{
	  "id": "John Henry-123456",
	  "name": "John Henry",
	  "employeeId": "123456",
	  "address": "A town nearby"
	}

## Azure 모바일 앱 쉬운 테이블 바인딩

Azure 앱 서비스 모바일 앱을 사용하면 테이블 끝점 데이터를 모바일 클라이언트에 노출할 수 있습니다. 이 동일한 표 형식 데이터는 Azure Functions로 입력 및 출력 바인딩에 사용될 수 있습니다. Node.js 백 엔드 모바일 앱이 있는 경우 *쉬운 테이블*을 사용하여 Azure 포털에서 이 표 형식 데이터로 작업할 수 있습니다. 쉬운 테이블은 열이 삽입되는 데이터의 모양에 맞게 자동으로 추가되도록 동적 스키마를 지원하여 스키마 개발을 간소화합니다. 동적 스키마를 기본적으로 사용하며 프로덕션 모바일 앱에서 사용하지 않아야 합니다. 모바일 앱에서 쉬운 테이블에 대한 자세한 내용은 [방법: Azure 포털에서 쉬운 테이블로 작업](../app-service-mobile/app-service-mobile-node-backend-how-to-use-server-sdk.md#in-portal-editing)을 참조하세요. 포털에 있는 쉬운 테이블은 .NET 백 엔드 모바일 앱에 대해 현재 지원되지 않습니다. 여전히 .NET 백 엔드 모바일 앱 테이블 끝점 함수 바인딩을 사용할 수 있지만 동적 스키마는 지원되지 않는 .NET 백 엔드 모바일 앱입니다.

이 섹션은 다음 하위 섹션을 포함합니다.

* [Azure 모바일 앱 쉬운 테이블 API 키](#easytablesapikey)
* [Azure 모바일 앱 쉬운 테이블 입력 바인딩](#easytablesinput)
* [Azure 모바일 앱 쉬운 테이블 출력 바인딩](#easytablesoutput)

### <a id="easytablesapikey"></a> API 키를 사용하여 모바일 앱 쉬운 테이블 끝점에 대한 액세스 보호

현재 Azure Functions는 앱 서비스 인증에서 보호하는 끝점에 액세스할 수 없습니다. 즉, 쉬운 테이블 바인딩이 포함된 함수에 사용되는 모바일 앱 끝점은 기본값인 익명 액세스를 허용해야 합니다. 쉬운 테이블 바인딩을 사용하면 API 키를 지정할 수 있으며 이는 함수 이외의 앱에서 원치 않는 액세스를 방지하는 데 사용될 수 있는 공유 암호입니다. 모바일 앱은 API 키 인증에 기본으로 제공되는 지원이 없습니다. 그러나 [API 키를 구현하는 Azure 앱 서비스 모바일 앱 백 엔드](https://github.com/Azure/azure-mobile-apps-node/tree/master/samples/api-key)의 예제를 수행하여 Node.js 백 엔드 모바일 앱에 API 키를 구현할 수 있습니다.

>[AZURE.IMPORTANT] 이 API 키는 모바일 앱 클라이언트와 함께 배포해서는 안되며 안전하게 Azure Functions과 같은 서비스 측 클라이언트에만 배포되어야 합니다.

### <a id="easytablesinput"></a> Azure 모바일 앱 쉬운 테이블 입력 바인딩

입력 바인딩은 모바일 앱 테이블 끝점에서 레코드를 로드하고 바인딩에 직접 전달할 수 있습니다. 함수를 호출한 트리거에 따라 레코드 ID가 결정됩니다. C# 함수에서 함수가 성공적으로 종료될 경우 레코드에 변경한 내용을 자동으로 다시 테이블에 전송합니다.

function.json 파일은 모바일 앱 쉬운 테이블 출력 바인딩과 함께 사용하도록 다음 속성을 지원합니다.

- `name` : 새 레코드에 대한 함수 코드에 사용되는 변수 이름입니다.
- `type` : 바인딩 유형은 *easyTable*로 설정해야 합니다.
- `tableName` : 새 레코드를 만드는 테이블입니다.
- `id` : 검색할 레코드의 ID입니다. 이 속성은 `{queueTrigger}`와 유사한 바인딩을 지원하며 이는 큐 메시지의 문자열 값을 레코드 ID로 사용합니다
- `apiKey` : 모바일 앱에 대한 선택적 API 키를 지정하는 응용 프로그램 설정인 문자열입니다. 모바일 앱이 API 키를 사용하여 클라이언트 액세스를 제한하는 경우 필요합니다.
- `connection` : 모바일 앱의 URI를 지정하는 응용 프로그램 설정인 문자열입니다.
- `direction` : 바인딩 방향이며 *in*으로 설정되어야 합니다.

예제 function.json:

	{
	  "bindings": [
	    {
	      "name": "record",
	      "type": "easyTable",
	      "tableName": "MyTable",
	      "id" : "{queueTrigger}",
	      "connection": "My_MobileApp_Uri",
	      "apiKey": "My_MobileApp_Key",
	      "direction": "in"
	    }
	  ],
	  "disabled": false
	}

#### C# 큐 트리거에 대한 Azure 모바일 앱 쉬운 테이블 코드 예제

위의 function.json 예제에 따라 입력 바인딩은 큐 메시지를 문자열과 일치하고 *레코드* 매개 변수에 전달하는 ID로 레코드를 검색합니다. 레코드가 없는 경우 매개 변수는 null입니다. 레코드는 함수가 종료되는 경우 새 *텍스트* 값으로 업데이트됩니다.

	#r "Newtonsoft.Json"	
	using Newtonsoft.Json.Linq;
	
	public static void Run(string myQueueItem, JObject record)
	{
	    if (record != null)
	    {
	        record["Text"] = "This has changed.";
	    }    
	}

#### Node.js 큐 트리거에 대한 Azure 모바일 앱 쉬운 테이블 코드 예제

위의 function.json 예제에 따라 입력 바인딩은 큐 메시지를 문자열과 일치하고 *레코드* 매개 변수에 전달하는 ID로 레코드를 검색합니다. Node.js 함수에서 업데이트된 레코드를 테이블에 다시 전송하지 않습니다. 이 코드 예제는 검색된 레코드를 로그에 작성합니다.

	module.exports = function (context, input) {    
	    context.log(context.bindings.record);
	    context.done();
	};


### <a id="easytablesoutput"></a> Azure 모바일 앱 쉬운 테이블 출력 바인딩

함수는 쉬운 테이블 출력 바인딩을 사용하여 모바일 앱 테이블 끝점에 레코드를 작성할 수 있습니다.

function.json 파일은 쉬운 테이블 출력 바인딩과 함께 사용하도록 다음 속성을 지원합니다.

- `name` : 새 레코드에 대한 함수 코드에 사용되는 변수 이름입니다.
- `type` : *easyTable*로 설정해야 하는 바인딩 유형입니다.
- `tableName` : 새 레코드를 만드는 테이블입니다.
- `apiKey` : 모바일 앱에 대한 선택적 API 키를 지정하는 응용 프로그램 설정인 문자열입니다. 모바일 앱이 API 키를 사용하여 클라이언트 액세스를 제한하는 경우 필요합니다.
- `connection` : 모바일 앱의 URI를 지정하는 응용 프로그램 설정인 문자열입니다.
- `direction` : 바인딩 방향이며 *out*으로 설정되어야 합니다.

예제 function.json:

	{
	  "bindings": [
	    {
	      "name": "record",
	      "type": "easyTable",
	      "tableName": "MyTable",
	      "connection": "My_MobileApp_Uri",
	      "apiKey": "My_MobileApp_Key",
	      "direction": "out"
	    }
	  ],
	  "disabled": false
	}

#### C# 큐 트리거에 대한 Azure 모바일 앱 쉬운 테이블 코드 예제

이 C# 코드 예제는 *텍스트* 속성을 가진 새 레코드를 위의 바인딩에 지정된 테이블에 삽입합니다.

	public static void Run(string myQueueItem, out object record)
	{
	    record = new {
	        Text = $"I'm running in a C# function! {myQueueItem}"
	    };
	}

#### Node.js 큐 트리거에 대한 Azure 모바일 앱 쉬운 테이블 코드 예제

이 Node.js 코드 예제는 *텍스트* 속성을 가진 새 레코드를 위의 바인딩에 지정된 테이블에 삽입합니다.

	module.exports = function (context, input) {
	
	    context.bindings.record = {
	        text : "I'm running in a Node function! Data: '" + input + "'"
	    }   
	
	    context.done();
	};

## Azure 알림 허브 출력 바인딩

함수는 몇 줄의 코드로 구성된 Azure 알림 허브를 사용하여 푸시 알림을 보낼 수 있습니다. 그러나 알림 허브는 사용하려는 PNS(플랫폼 알림 서비스)에 대해 구성되어야 합니다. Azure 알림 허브를 구성하고 알림을 등록하는 클라이언트 응용 프로그램을 개발하는 데 대한 자세한 내용은 [알림 허브 시작](../notification-hubs/notification-hubs-windows-store-dotnet-get-started.md)을 참조하고 맨 위에 있는 대상 클라이언트 플랫폼을 클릭합니다.

function.json 파일은 알림 허브 출력 바인딩과 함께 사용하도록 다음 속성을 제공합니다.

- `name` : 알림 허브 메시지에 대한 함수 코드에 사용되는 변수 이름입니다.
- `type` : *"notificationHub"*로 설정해야 합니다.
- `tagExpression` : 태그 식을 사용하면 알림을 태그 식과 일치하는 알림을 수신하도록 등록된 일련의 장치에 배달하도록 지정할 수 있습니다. 자세한 내용은 [라우팅 및 태그 식](../notification-hubs/notification-hubs-routing-tag-expressions.md)을 참조하세요.
- `hubName` : Azure 포털에서 알림 허브 리소스의 이름입니다.
- `connection` : 이 연결 문자열은 알림 허브에 대한 *DefaultFullSharedAccessSignature*값으로 설정된 **응용 프로그램 설정** 연결 문자열이어야 합니다.
- `direction`: *"out"*으로 설정되어야 합니다. 
 
예제 function.json:

	{
	  "bindings": [
	    {
	      "name": "notification",
	      "type": "notificationHub",
	      "tagExpression": "",
	      "hubName": "my-notification-hub",
	      "connection": "MyHubConnectionString",
	      "direction": "out"
	    }
	  ],
	  "disabled": false
	}

### Azure 알림 허브 연결 문자열 설정

알림 허브 출력 바인딩을 사용하려면 허브에 대한 연결 문자열을 구성해야 합니다. *통합* 탭에서 알림 허브를 선택하거나 새로 만들어서 이를 수행할 수 있습니다.

알림 허브에 *DefaultFullSharedAccessSignature*에 대한 연결 문자열을 추가하여 기존 허브에 대한 연결 문자열을 수동으로 추가할 수 있습니다. 이 연결 문자열에서는 알림 메시지를 보내는 함수 액세스 권한을 제공합니다. *DefaultFullSharedAccessSignature* 연결 문자열 값은 Azure 포털의 알림 허브 리소스의 주 블레이드에 있는 **키** 단추에서 액세스할 수 있습니다. 허브에 대한 연결 문자열을 수동으로 추가하려면 다음 단계를 사용합니다.

1. Azure 포털의 **함수 앱** 블레이드에서 **함수 앱 설정 > 앱 서비스 설정으로 이동**을 클릭합니다.

2. **설정** 블레이드에서 **응용 프로그램 설정**을 클릭합니다.

3. **연결 문자열** 섹션을 아래로 스크롤하여 알림 허브에 *DefaultFullSharedAccessSignature* 값에 대한 명명된 항목을 추가합니다. 형식을 **사용자 지정**으로 변경합니다.
4. 출력 바인딩에서 연결 문자열 이름을 참조합니다. 위의 예제에서 사용된 **MyHubConnectionString**과 유사합니다.

### Node.js 타이머 트리거에 대한 Azure 알림 허브 코드 예제 

이 예제는 `location` 및 `message`을 포함하는 [템플릿 등록](../notification-hubs/notification-hubs-templates.md)에 대한 알림을 보냅니다.

	module.exports = function (context, myTimer) {
	    var timeStamp = new Date().toISOString();
	   
	    if(myTimer.isPastDue)
	    {
	        context.log('Node.js is running late!');
	    }
	    context.log('Node.js timer trigger function ran!', timeStamp);  
	    context.bindings.notification = {
	        location: "Redmond",
	        message: "Hello from Node!"
	    };
	    context.done();
	};

### C# 큐 트리거에 대한 Azure 알림 허브 코드 예제

이 예제는 `message`를 포함하는 [템플릿 등록](../notification-hubs/notification-hubs-templates.md)에 대한 알림을 보냅니다.


	using System;
	using System.Threading.Tasks;
	using System.Collections.Generic;
	 
	public static void Run(string myQueueItem,  out IDictionary<string, string> notification, TraceWriter log)
	{
	    log.Verbose($"C# Queue trigger function processed: {myQueueItem}");
        notification = GetTemplateProperties(myQueueItem);
	}
	 
	private static IDictionary<string, string> GetTemplateProperties(string message)
	{
	    Dictionary<string, string> templateProperties = new Dictionary<string, string>();
	    templateProperties["message"] = message;
	    return templateProperties;
	}

이 예제는 유효한 JSON 문자열을 사용하여 `message`를 포함하는 [템플릿 등록](../notification-hubs/notification-hubs-templates.md)에 대한 알림을 보냅니다.

	using System;
	 
	public static void Run(string myQueueItem,  out string notification, TraceWriter log)
	{
		log.Verbose($"C# Queue trigger function processed: {myQueueItem}");
		notification = "{"message":"Hello from C#. Processed a queue item!"}";
	}

### 알림 유형을 사용하는 Azure 알림 허브 큐 트리거 C# 코드 예제

이 예제는 [Microsoft Azure 알림 허브 라이브러리](https://www.nuget.org/packages/Microsoft.Azure.NotificationHubs/)에 정의된 `Notification` 형식을 사용하는 방법을 보여줍니다. 이 형식 및 라이브러리를 사용하기 위해 함수 앱에 *project.json* 파일을 업로드해야 합니다. project.json 파일은 다음과 유사한 JSON 텍스트 파일입니다.

	{
	  "frameworks": {
	    ".NETFramework,Version=v4.6": {
	      "dependencies": {
	        "Microsoft.Azure.NotificationHubs": "1.0.4"
	      }
	    }
	  }
	}

project.json 파일을 업로드하는 데 대한 자세한 내용은 [project.json 파일 업로드](http://stackoverflow.com/questions/36411536/how-can-i-use-nuget-packages-in-my-azure-functions)를 참조하세요.

예제 코드:

	using System;
	using System.Threading.Tasks;
	using Microsoft.Azure.NotificationHubs;
	 
	public static void Run(string myQueueItem,  out Notification notification, TraceWriter log)
	{
	   log.Verbose($"C# Queue trigger function processed: {myQueueItem}");
	   notification = GetTemplateNotification(myQueueItem);
	}
	private static TemplateNotification GetTemplateNotification(string message)
	{
	    Dictionary<string, string> templateProperties = new Dictionary<string, string>();
	    templateProperties["message"] = message;
	    return new TemplateNotification(templateProperties);
	}

## 다음 단계

자세한 내용은 다음 리소스를 참조하세요.

* [Azure Functions 개발자 참조](functions-reference.md)
* [Azure Functions C# 개발자 참조](functions-reference-csharp.md)
* [Azure Functions NodeJS 개발자 참조](functions-reference-node.md)

<!---HONumber=AcomDC_0420_2016-->