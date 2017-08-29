---
title: "Azure Functions의 트리거 및 바인딩 작업 | Microsoft Docs"
description: "Azure Functions에서 트리거 및 바인딩을 사용하여 코드 실행을 온라인 이벤트 및 클라우드 기반 서비스에 연결하는 방법을 알아봅니다."
services: functions
documentationcenter: na
author: lindydonna
manager: cfowler
editor: 
tags: 
keywords: "Azure Functions, 함수, 이벤트 처리, webhook, 동적 계산, 서버가 없는 아키텍처"
ms.assetid: cbc7460a-4d8a-423f-a63e-1cd33fef7252
ms.service: functions
ms.devlang: multiple
ms.topic: reference
ms.tgt_pltfrm: multiple
ms.workload: na
ms.date: 05/30/2017
ms.author: donnam
ms.translationtype: Human Translation
ms.sourcegitcommit: 31ecec607c78da2253fcf16b3638cc716ba3ab89
ms.openlocfilehash: cc41debb2523df77be4db05817a4c7ac55604439
ms.contentlocale: ko-kr
ms.lasthandoff: 06/23/2017

---

# <a name="azure-functions-triggers-and-bindings-concepts"></a>Azure Functions 트리거 및 바인딩 개념
Azure Functions에서는 *트리거* 및 *바인딩*을 통해 Azure 및 기타 서비스의 이벤트에 대응하는 코드를 쓸 수 있습니다. 이 문서는 지원되는 모든 프로그래밍 언어의 트리거 및 바인딩에 대한 개념적 개요를 제공합니다. 여기에서는 모든 바인딩에 공통되는 기능을 설명합니다.

## <a name="overview"></a>개요

트리거와 바인딩은 함수가 호출되는 방식과 사용하는 데이터를 정의하는 선언적 방식입니다. *트리거*는 함수가 호출되는 방식을 정의합니다. 함수에는 정확히 하나의 트리거만 있어야 합니다. 트리거는 관련 데이터가 있으며, 이 데이터는 일반적으로 함수를 트리거한 페이로드입니다. 

입력 및 출력 *바인딩*은 코드에서 데이터에 연결하기 위한 선언적 방식을 제공합니다. 트리거와 마찬가지로, 함수 구성에 연결 문자열과 기타 속성을 지정합니다. 바인딩은 선택 사항이며 함수는 여러 개의 입력 및 출력 바인딩을 가질 수 있습니다. 

트리거와 바인딩을 사용하면 더욱 일반적이면서 코드가 상호 작용하는 서비스의 상세 정보를 하드코딩하지 않는 코드를 작성할 수 있습니다. 서비스의 데이터가 함수 코드의 입력 값이 됩니다. 데이터를 다른 서비스(예: Azure Table Storage에서 새 행 만들기)로 출력하려면 메서드의 리턴값을 사용합니다. 복수 값을 출력해야 하는 경우에는 도우미 개체를 사용합니다. 트리거와 바인딩에는 **name** 속성이 있습니다. 이 속성은 바인딩에 액세스하기 위해 코드에서 사용하는 식별자입니다.

Azure Functions Portal의 **통합** 탭에서 트리거와 바인딩을 구성할 수 있습니다. 이때 UI는 function 디렉터리에 있는 *function.json* 파일을 수정합니다. 이 파일은 **고급 편집기**로 변경하여 편집할 수 있습니다.

다음 표에 Azure Functions에 지원되는 트리거와 바인딩이 나와 있습니다. 

[!INCLUDE [Full bindings table](../../includes/functions-bindings.md)]

### <a name="example-queue-trigger-and-table-output-binding"></a>예: 큐 트리거 및 테이블 출력 바인딩

Azure Queue Storage에 새 메시지가 나타날 때마다 Azure Table Storage에 새 행을 쓰려는 경우를 가정하겠습니다. 이 시나리오는 Azure Queue 트리거 및 Table 출력 바인딩을 사용하여 구현할 수 있습니다. 

큐 트리거는 **통합** 탭에 다음 정보가 필요합니다.

* 큐에 대한 저장소 계정 연결 문자열이 포함된 앱 설정의 이름
* 큐 이름
* `order`와 같이 큐 메시지의 내용을 읽을 수 있는 코드 내 식별자

Azure Table Storage를 작성하려면 다음 정보로 출력 바인딩을 사용합니다.

* 테이블의 저장소 계정 연결 문자열이 포함된 앱 설정의 이름
* 테이블 이름
* 코드에서 출력 항목을 만들기 위한 식별자 또는 함수에서 반환된 값

바인딩은 *function.json*에 서비스 비밀이 포함되지 않은 모범 사례를 실행하기 위해 연결 문자열에 앱 설정을 사용합니다.

그런 다음 코드에서 제공한 식별자를 사용하여 Azure Storage와 통합합니다.

```cs
#r "Newtonsoft.Json"

using Newtonsoft.Json.Linq;

// From an incoming queue message that is a JSON object, add fields and write to Table Storage
// The method return value creates a new row in Table Storage
public static Person Run(JObject order, TraceWriter log)
{
    return new Person() { 
            PartitionKey = "Orders", 
            RowKey = Guid.NewGuid().ToString(),  
            Name = order["Name"].ToString(),
            MobileNumber = order["MobileNumber"].ToString() };  
}
 
public class Person
{
    public string PartitionKey { get; set; }
    public string RowKey { get; set; }
    public string Name { get; set; }
    public string MobileNumber { get; set; }
}
```

```javascript
// From an incoming queue message that is a JSON object, add fields and write to Table Storage
// The second parameter to context.done is used as the value for the new row
module.exports = function (context, order) {
    order.PartitionKey = "Orders";
    order.RowKey = generateRandomId(); 

    context.done(null, order);
};

function generateRandomId() {
    return Math.random().toString(36).substring(2, 15) +
        Math.random().toString(36).substring(2, 15);
}
```

다음은 이전 코드에 해당하는 *function.json*입니다. 함수 구현 언어와 상관없이 동일한 구성을 사용할 수 있습니다.

```json
{
  "bindings": [
    {
      "name": "order",
      "type": "queueTrigger",
      "direction": "in",
      "queueName": "myqueue-items",
      "connection": "MY_STORAGE_ACCT_APP_SETTING"
    },
    {
      "name": "$return",
      "type": "table",
      "direction": "out",
      "tableName": "outTable",
      "connection": "MY_TABLE_STORAGE_ACCT_APP_SETTING"
    }
  ]
}
```
Azure Portal에서 *function.json*의 내용을 보고 편집하려면 함수의 **통합** 탭에서 **고급 편집기**를 클릭합니다.

Azure Storage 통합에 대한 추가 코드 예제와 상세 정보를 보려면 [Azure 저장소에 대한 Azure Functions 트리거 및 바인딩](functions-bindings-storage.md)을 참조하세요.

### <a name="binding-direction"></a>바인딩 방향

모든 트리거와 바인딩에는 `direction` 속성이 있습니다.

- 트리거의 경우 방향은 언제나 `in`입니다
- 입력 및 출력 바인딩은 `in`과 `out`을 사용합니다
- 일부 바인딩은 특수 방향인 `inout`을 사용합니다. `inout`을 사용할 경우 **통합** 탭에서 **고급 편집기**만 사용할 수 있습니다.

## <a name="using-the-function-return-type-to-return-a-single-output"></a>함수 반환 유형을 사용하여 단일 출력 반환

위의 예제는 함수의 반환값을 사용하여 바인딩에 출력을 제공하는 방법을 보여줍니다. 이 경우 특수 이름 매개 변수인 `$return`을 사용합니다. (C#, JavaScript, F#과 같이 반환값이 있는 언어에서만 지원됩니다.) 함수에 복수의 출력 바인딩이 있는 경우 출력 바인딩 중 하나에 대해서만 `$return`을 사용합니다. 

```json
// excerpt of function.json
{
    "name": "$return",
    "type": "blob",
    "direction": "out",
    "path": "output-container/{id}"
}
```

아래 예제는 C#, JavaScript, F#에서 반환 형식이 출력 바인딩과 함께 사용되는 방식을 보여줍니다.

```cs
// C# example: use method return value for output binding
public static string Run(WorkItem input, TraceWriter log)
{
    string json = string.Format("{{ \"id\": \"{0}\" }}", input.Id);
    log.Info($"C# script processed queue message. Item={json}");
    return json;
}
```

```cs
// C# example: async method, using return value for output binding
public static Task<string> Run(WorkItem input, TraceWriter log)
{
    string json = string.Format("{{ \"id\": \"{0}\" }}", input.Id);
    log.Info($"C# script processed queue message. Item={json}");
    return json;
}
```

```javascript
// JavaScript: return a value in the second parameter to context.done
module.exports = function (context, input) {
    var json = JSON.stringify(input);
    context.log('Node.js script processed queue message', json);
    context.done(null, json);
}
```

```fsharp
// F# example: use return value for output binding
let Run(input: WorkItem, log: TraceWriter) =
    let json = String.Format("{{ \"id\": \"{0}\" }}", input.Id)   
    log.Info(sprintf "F# script processed queue message '%s'" json)
    json
```

## <a name="binding-datatype-property"></a>dataType 속성 바인딩

.NET에서는 형식을 사용하여 입력 데이터에 대한 데이터 형식을 정의합니다. 예를 들어 `string`을 사용하여 이진으로 읽을 바이트 배열 및 큐 트리거의 텍스트에 바인딩합니다.

JavaScript와 같은 동적으로 형식화되는 언어의 경우 바인딩 정의에 `dataType` 속성을 사용합니다. 예를 들어 이진 형식의 HTTP 요청 내용을 읽으려면 `binary` 형식을 사용합니다.

```json
{
    "type": "httpTrigger",
    "name": "req",
    "direction": "in",
    "dataType": "binary"
}
```

`dataType`에 대한 다른 옵션은 `stream` 및 `string`입니다.

## <a name="resolving-app-settings"></a>앱 설정 해결
비밀과 연결 문자열은 구성 파일이 아닌 앱 설정을 사용하여 관리하는 것이 가장 좋습니다. 그럴 경우 이러한 비밀에 대한 액세스가 제한되고 *function.json*을 공용 원본 제어 리포지토리에 안전하게 저장할 수 있습니다.

환경을 기준으로 구성을 변경하려는 경우에도 앱 설정이 유용합니다. 예를 들어 테스트 환경에서 다른 큐 또는 Blob Storage 컨테이너를 모니터링할 수 있습니다.

앱 설정은 `%MyAppSetting%`과 같이 값이 퍼센트 기호로 둘러싸인 경우에만 확인됩니다. 트리거 및 바인딩의 `connection` 속성은 특수한 경우이며 앱 설정으로 값을 자동 확인합니다. 

다음 예제는 `%input-queue-name%` 앱 설정을 사용하여 트리거할 큐를 정의하는 큐 트리거입니다.

```json
{
  "bindings": [
    {
      "name": "order",
      "type": "queueTrigger",
      "direction": "in",
      "queueName": "%input-queue-name%",
      "connection": "MY_STORAGE_ACCT_APP_SETTING"
    }
  ]
}
```

## <a name="trigger-metadata-properties"></a>트리거 메타데이터 속성

트리거가 제공한 데이터 페이로드(예: 함수를 트리거한 큐 메시지) 이외에 많은 트리거가 추가 메타데이터 값을 제공합니다. 이러한 값은 C# 및 F#에서 입력 매개 변수로 사용하거나 JavaScript에서 `context.bindings` 개체의 속성으로 사용할 수 있습니다. 

예를 들어 큐 트리거는 다음 속성을 지원합니다.

* QueueTrigger - 유효한 문자열인 경우 트리거 메시지 내용
* DequeueCount
* ExpirationTime
* Id
* InsertionTime
* NextVisibleTime
* PopReceipt

각 트리거의 메타데이터 속성은 해당 참조 항목에서 자세히 설명되어 있습니다. 설명서는 Portal에서 **통합** 탭의 바인딩 구성 영역 아래 **설명서** 섹션에서도 참조할 수 있습니다.  

예를 들어 Blob 트리거는 약간의 지연이 있으므로 큐 트리거를 사용하여 함수를 실행합니다([Blob Storage 트리거](functions-bindings-storage-blob.md#storage-blob-trigger) 참조). 큐 메시지에는 트리거할 Blob 파일 이름이 있는 경우가 일반적입니다. `queueTrigger` 메타데이터 속성을 사용하면 코드가 아닌 구성에서 이 동작을 모두 지정할 수 있습니다.

```json
  "bindings": [
    {
      "name": "myQueueItem",
      "type": "queueTrigger",
      "queueName": "myqueue-items",
      "connection": "MyStorageConnection",
    },
    {
      "name": "myInputBlob",
      "type": "blob",
      "path": "samples-workitems/{queueTrigger}",
      "direction": "in",
      "connection": "MyStorageConnection"
    }
  ]
```

트리거의 메타데이터 속성도 다음 섹션에서 설명하는 바와 같이 다른 바인딩에 대한 *바인딩 식*에 사용할 수 있습니다.

## <a name="binding-expressions-and-patterns"></a>바인딩 식 및 패턴

트리거와 바인딩의 가장 강력한 기능 중 하나는 *바인딩 식*입니다. 바인딩 안에서 패턴 식을 정의한 다음 다른 바인딩 또는 코드에서 이 패턴 식을 사용할 수 있습니다. 위 섹션의 샘플과 같이, 트리거 메타데이터도 바인딩 식에 사용할 수 있습니다.

예를 들어 **새 함수** 페이지의 **이미지 크기 조정** 템플릿과 같이 특정 Blob Storage 컨테이너에서 이미지 크기를 조정하려는 경우를 가정하겠습니다. **새 함수** -> 언어 **C#** -> 시나리오 **샘플** -> **ImageResizer-CSharp**로 이동합니다. 

*function.json* 정의는 다음과 같습니다.

```json
{
  "bindings": [
    {
      "name": "image",
      "type": "blobTrigger",
      "path": "sample-images/{filename}",
      "direction": "in",
      "connection": "MyStorageConnection"
    },
    {
      "name": "imageSmall",
      "type": "blob",
      "path": "sample-images-sm/{filename}",
      "direction": "out",
      "connection": "MyStorageConnection"
    }
  ],
}
```

Blob 트리거 정의와 Blob 출력 바인딩에 `filename` 매개 변수가 사용되었습니다. 이 매개 변수는 함수 코드에서도 사용할 수 있습니다.

```csharp
// C# example of binding to {filename}
public static void Run(Stream image, string filename, Stream imageSmall, TraceWriter log)  
{
    log.Info($"Blob trigger processing: {filename}");
    // ...
} 
```

<!--TODO: add JavaScript example -->
<!-- Blocked by bug https://github.com/Azure/Azure-Functions/issues/248 -->


### <a name="random-guids"></a>임의 GUID
Azure Functions는 `{rand-guid}` 바인딩 식을 통해 바인딩에서 GUID를 편리하게 생성할 수 있는 구문을 제공합니다. 다음 예제는 이 식을 사용하여 고유한 Blob 이름을 생성합니다. 

```json
{
  "type": "blob",
  "name": "blobOutput",
  "direction": "out",
  "path": "my-output-container/{rand-guid}"
}
```

### <a name="current-time"></a>현재 시간

`DateTime.UtcNow`로 확인되는 바인딩 식 `DateTime`을 사용할 수 있습니다.

```json
{
  "type": "blob",
  "name": "blobOutput",
  "direction": "out",
  "path": "my-output-container/{DateTime}"
}
```

## <a name="bind-to-custom-input-properties-in-a-binding-expression"></a>바인딩 식에서 사용자 지정 입력 속성에 바인딩

바인딩 식은 트리거 페이로드 자체에 정의된 속성도 참조할 수 있습니다. 예를 들어 webhook에 제공된 파일 이름에서 Blob Storage 파일에 동적으로 바인딩하는 경우가 있습니다.

예를 들어 다음 *function.json*은 트리거 페이로드에서 `BlobName`이라는 속성을 사용합니다.

```json
{
  "bindings": [
    {
      "name": "info",
      "type": "httpTrigger",
      "direction": "in",
      "webHookType": "genericJson",
    },
    {
      "name": "blobContents",
      "type": "blob",
      "direction": "in",
      "path": "strings/{BlobName}",
      "connection": "AzureWebJobsStorage"
    },
    {
      "name": "res",
      "type": "http",
      "direction": "out"
    }
  ]
}
```

C# 및 F#에서 이를 달성하려면 트리거 페이로드에서 deserialize되는 필드를 정의하는 POCO를 정의해야 합니다.

```csharp
using System.Net;

public class BlobInfo
{
    public string BlobName { get; set; }
}
  
public static HttpResponseMessage Run(HttpRequestMessage req, BlobInfo info, string blobContents)
{
    if (blobContents == null) {
        return req.CreateResponse(HttpStatusCode.NotFound);
    } 

    return req.CreateResponse(HttpStatusCode.OK, new {
        data = $"{blobContents}"
    });
}
```

JavaScript에서 JSON deserialization은 자동으로 실행되며 속성을 직접 사용할 수 있습니다.

```javascript
module.exports = function (context, info) {
    if ('BlobName' in info) {
        context.res = {
            body: { 'data': context.bindings.blobContents }
        }
    }
    else {
        context.res = {
            status: 404
        };
    }
    context.done();
}
```

## <a name="configuring-binding-data-at-runtime"></a>런타임에 바인딩 데이터 구성

C# 및 기타 .NET 언어에서는 *function.json*의 선언적 바인딩과 달리 명령적 바인딩 패턴을 사용할 수 있습니다. 명령적 바인딩은 바인딩 매개 변수를 디자인 타임이 아닌 런타임에 계산해야 할 경우 유용합니다. 자세한 내용은 C# 개발자 참조에서 [명령적 바인딩을 통해 런타임 시 바인딩](functions-reference-csharp.md#imperative-bindings)을 참조하세요.

## <a name="next-steps"></a>다음 단계
특성 바인딩에 대한 자세한 내용은 다음 문서를 참조하십시오.

- [HTTP 및 webhook](functions-bindings-http-webhook.md)
- [타이머](functions-bindings-timer.md)
- [큐 저장소](functions-bindings-storage-queue.md)
- [Blob 저장소](functions-bindings-storage-blob.md)
- [Table Storage](functions-bindings-storage-table.md)
- [이벤트 허브](functions-bindings-event-hubs.md)
- [서비스 버스](functions-bindings-service-bus.md)
- [Cosmos DB](functions-bindings-documentdb.md)
- [SendGrid](functions-bindings-sendgrid.md)
- [Twilio](functions-bindings-twilio.md)
- [알림 허브](functions-bindings-notification-hubs.md)
- [모바일 앱](functions-bindings-mobile-apps.md)
- [외부 파일](functions-bindings-external-file.md)

