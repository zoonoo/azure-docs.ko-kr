---
title: "Azure Functions Blob Storage 바인딩 | Microsoft Docs"
description: "Azure Functions에서 Azure Storage 트리거 및 바인딩을 사용하는 방법을 파악합니다."
services: functions
documentationcenter: na
author: lindydonna
manager: cfowler
editor: 
tags: 
keywords: "Azure Functions, 함수, 이벤트 처리, 동적 계산, 서버를 사용하지 않는 아키텍처"
ms.assetid: aba8976c-6568-4ec7-86f5-410efd6b0fb9
ms.service: functions
ms.devlang: multiple
ms.topic: reference
ms.tgt_pltfrm: multiple
ms.workload: na
ms.date: 05/25/2017
ms.author: glenga
ms.openlocfilehash: b123578dbac48018f674f85ec923e4c6e65fb9f8
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/11/2017
---
# <a name="azure-functions-blob-storage-bindings"></a>Azure Functions Blob Storage 바인딩
[!INCLUDE [functions-selector-bindings](../../includes/functions-selector-bindings.md)]

이 문서에서는 Azure Functions에서 Azure Blob Storage 바인딩을 구성하고 사용하는 방법을 설명합니다. Azure Functions는 Azure Blob Storage에 대한 트리거, 입력 및 출력 바인딩을 지원합니다. 모든 바인딩에서 사용할 수 있는 기능은 [Azure Functions 트리거 및 바인딩 개념](functions-triggers-bindings.md)을 참조하세요.

[!INCLUDE [intro](../../includes/functions-bindings-intro.md)]

> [!NOTE]
> [Blob 전용 저장소 계정](../storage/common/storage-create-storage-account.md#blob-storage-accounts)은 지원되지 않습니다. Blob Storage 트리거 및 바인딩에는 범용 저장소 계정이 필요합니다. 
> 

<a name="trigger"></a>
<a name="storage-blob-trigger"></a>
## <a name="blob-storage-triggers-and-bindings"></a>Blob Storage 트리거 및 바인딩

Azure Blob Storage 트리거를 사용하면 새 Blob 또는 업데이트된 Blob이 검색되었을 때 함수 코드가 호출됩니다. Blob 내용은 함수의 입력으로 제공됩니다.

Functions 포털에서 **통합** 탭을 사용하여 Blob Storage 트리거를 정의합니다. 이 포털에서는 *function.json*의 **bindings** 섹션에 다음 정의를 만듭니다.

```json
{
    "name": "<The name used to identify the trigger data in your code>",
    "type": "blobTrigger",
    "direction": "in",
    "path": "<container to monitor, and optionally a blob name pattern - see below>",
    "connection": "<Name of app setting - see below>"
}
```

Blob 입력 및 출력 바인딩은 `blob`을 바인딩 형식으로 사용하여 정의됩니다.

```json
{
  "name": "<The name used to identify the blob input in your code>",
  "type": "blob",
  "direction": "in", // other supported directions are "inout" and "out"
  "path": "<Path of input blob - see below>",
  "connection":"<Name of app setting - see below>"
},
```

* `path` 속성은 바인딩 식 및 필터 매개 변수를 지원합니다. [이름 패턴](#pattern)을 참조하세요.
* `connection` 속성은 저장소 연결 문자열을 포함하는 앱 설정의 이름을 포함해야 합니다. Azure Portal에서 **통합** 탭에 있는 표준 편집기는 저장소 계정을 선택하는 경우 이 앱 설정을 구성합니다.

> [!NOTE]
> 소비 계획에서 Blob 트리거를 사용하는 경우 함수 앱이 유휴 상태가 된 후 새 Blob을 처리하는 데 최대 10분이 지연될 수 있습니다. 함수 앱이 실행된 후에는 Blob이 즉시 처리됩니다. 이 초기 지연을 방지하려면 다음 옵션 중 하나를 고려합니다.
> - 무중단이 사용되는 App Service 계획을 사용합니다.
> - Blob 이름을 포함하는 큐 메시지와 같은 다른 메커니즘을 사용하여 Blob 처리를 트리거합니다. 예를 들어 [Blob 입력 바인딩을 사용하여 큐 트리거](#input-sample)를 참조하세요.

<a name="pattern"></a>

### <a name="name-patterns"></a>이름 패턴
`path` 속성에서 Blob 이름 패턴을 지정할 수 있으며, 이는 필터 또는 바인딩 식일 수 있습니다. [바인딩 식 및 패턴](functions-triggers-bindings.md#binding-expressions-and-patterns)을 참조하세요.

예를 들어 문자열 "original"로 시작하는 Blob을 필터링하려면 다음 정의를 사용합니다. 이 경로에는 *input* 컨테이너에 *original-Blob1.txt*라는 Blob이 있으며 함수 코드에 있는 `name` 변수의 값은 `Blob1`입니다.

```json
"path": "input/original-{name}",
```

Blob 파일 이름 및 확장명에 별도로 바인딩하려면 두 가지 패턴을 사용합니다. 또한 이 경로에는 *original-Blob1.txt*라는 Blob이 있으며 함수 코드에 있는 `blobname` 및 `blobextension` 변수의 값은 *original-Blob1* 및 *txt*입니다.

```json
"path": "input/{blobname}.{blobextension}",
```

파일 확장명에 고정된 값을 사용하여 Blob의 파일 형식을 제한할 수 있습니다. 예를 들어 .png 파일에 대해서만 트리거하려면 다음 패턴을 사용합니다.

```json
"path": "samples/{name}.png",
```

중괄호는 이름 패턴에서 특수 문자입니다. 이름에 중괄호가 있는 Blob 이름을 지정하려면 중괄호 두 개를 사용하여 중괄호를 이스케이프합니다. 다음 예제에는 *images* 컨테이너에 *{20140101}-soundfile.mp3*라는 Blob이 있으며 함수 코드에 있는 `name` 변수 값은 *soundfile.mp3*입니다. 

```json
"path": "images/{{20140101}}-{name}",
```

### <a name="trigger-metadata"></a>트리거 메타데이터

Blob 트리거는 몇 가지 메타데이터 속성을 제공합니다. 이러한 속성을 다른 바인딩에서 바인딩 식의 일부로 사용하거나 코드에서 매개 변수로 사용할 수 있습니다. 이러한 값은 [CloudBlob](https://docs.microsoft.com/en-us/dotnet/api/microsoft.windowsazure.storage.blob.cloudblob?view=azure-dotnet)과 동일한 의미 체계를 가집니다.

- **BlobTrigger**. `string`을 입력합니다. Blob 트리거 경로
- **Uri**. `System.Uri`을 입력합니다. 기본 위치에 대한 Blob의 URI입니다.
- **Properties**. `Microsoft.WindowsAzure.Storage.Blob.BlobProperties`을 입력합니다. Blob의 시스템 속성입니다.
- **Metadata**. `IDictionary<string,string>`을 입력합니다. Blob에 대한 사용자 정의 메타데이터입니다.

<a name="receipts"></a>

### <a name="blob-receipts"></a>Blob 수신 확인
Azure Functions 런타임은 동일한 새 Blob 또는 업데이트된 Blob에 대해 Blob 트리거 함수가 두 번 이상 호출되지 않도록 합니다. 지정된 Blob 버전이 처리되었는지 확인하려면 *Blob 수신 확인*을 유지 관리합니다.

Azure Functions는 사용자 함수 앱에서 사용하는(`AzureWebJobsStorage` 앱 설정에서 지정됨) Azure Storage 계정의 *azure-webjobs-hosts*라는 컨테이너에 Blob 수신 확인을 저장합니다. Blob 수신 확인에는 다음 정보가 포함됩니다.

* 트리거된 함수("*&lt;함수 앱 이름>*.Functions.*&lt;함수 이름>*", 예: "MyFunctionApp.Functions.CopyBlob")
* 컨테이너 이름
* Blob 유형("BlockBlob" 또는 "PageBlob")
* Blob 이름
* ETag(Blob 버전 식별자, 예: "0x8D1DC6E70A277EF")

Blob을 강제로 처리하려면 *azure-webjobs-hosts* 컨테이너에서 해당 Blob에 대한 Blob 수신 확인을 수동으로 삭제하면 됩니다.

<a name="poison"></a>

### <a name="handling-poison-blobs"></a>포이즌 Blob 처리
지정된 Blob에 대한 Blob 트리거 함수가 실패한 경우 Azure Functions는 기본적으로 총 5번 해당 함수를 다시 시도합니다. 

5번 모두 실패한 경우 Azure Functions는 *webjobs-blobtrigger-poison*이라는 저장소 큐에 메시지를 추가합니다. 포이즌 Blob에 대한 큐 메시지는 다음 속성을 포함하는 JSON 개체입니다.

* FunctionId(형식에서 *&lt;함수 앱 이름>*.Functions.*&lt;함수 이름>*)
* BlobType("BlockBlob" 또는 "PageBlob")
* ContainerName
* BlobName
* ETag(Blob 버전 식별자, 예: "0x8D1DC6E70A277EF")

### <a name="blob-polling-for-large-containers"></a>큰 컨테이너에 대한 Blob 폴링
모니터링 중인 Blob 컨테이너에 10,000개 이상의 Blob이 포함된 경우 Functions 런타임은 로그 파일을 스캔하여 새롭거나 변경된 Blob을 확인합니다. 이 프로세스는 실시간으로 하지 않습니다. Blob을 만든 후 몇 분이 경과할 때까지 함수가 트리거되지 않을 수도 있습니다. 또한 [저장소 로그는 "최선을 다해" 생성됩니다](/rest/api/storageservices/About-Storage-Analytics-Logging). 하지만 모든 이벤트가 캡처되는 것은 아닙니다. 경우에 따라 로그가 누락될 수 있습니다. 더 빠르거나 안정적인 Blob 처리가 필요한 경우 Blob을 만들 때 [큐 메시지](../storage/queues/storage-dotnet-how-to-use-queues.md)를 만드는 것이 좋습니다. 그런 다음 Blob 트리거 대신 [큐 트리거](functions-bindings-storage-queue.md)를 사용하여 Blob을 처리합니다.

<a name="triggerusage"></a>

## <a name="using-a-blob-trigger-and-input-binding"></a>Blob 트리거 및 입력 바인딩 사용
.NET 함수에서는 `Stream paramName`과 같은 메서드 매개 변수를 사용하여 Blob 데이터에 액세스합니다. 여기에서 `paramName`은 [트리거 구성](#trigger)에서 지정한 값입니다. Node.js 함수에서는 `context.bindings.<name>`을 사용하여 입력 Blob 데이터에 액세스합니다.

.NET에서는 아래 목록의 형식 중 하나에 바인딩할 수 있습니다. 입력 바인딩으로 사용되는 경우 이러한 형식 중 일부에는 *function.json*에서 `inout` 바인딩 방향이 필요합니다. 이 방향은 표준 편집기에서 지원되지 않으므로 고급 편집기를 사용해야 합니다.

* `TextReader`
* `Stream`
* `ICloudBlob`("inout" 바인딩 방향 필요)
* `CloudBlockBlob`("inout" 바인딩 방향 필요)
* `CloudPageBlob`("inout" 바인딩 방향 필요)
* `CloudAppendBlob`("inout" 바인딩 방향 필요)

텍스트 Blob이 필요한 경우 .NET `string` 형식에 바인딩할 수도 있습니다. 전체 Blob 내용이 메모리에 로드되므로 이는 Blob 크기가 작은 경우에만 권장됩니다. 일반적으로 `Stream` 또는 `CloudBlockBlob` 형식을 사용하는 것이 좋습니다.

## <a name="trigger-sample"></a>트리거 샘플
Blob Storage 트리거를 정의하는 다음과 같은 function.json이 있다고 가정합니다.

```json
{
    "disabled": false,
    "bindings": [
        {
            "name": "myBlob",
            "type": "blobTrigger",
            "direction": "in",
            "path": "samples-workitems",
            "connection":"MyStorageAccount"
        }
    ]
}
```

모니터링되는 컨테이너에 추가된 각 Blob의 콘텐츠를 기록하는 언어별 샘플을 참조하세요.

* [C#](#triggercsharp)
* [Node.JS](#triggernodejs)

<a name="triggercsharp"></a>

### <a name="blob-trigger-examples-in-c"></a>C#의 Blob 트리거 예 #

```cs
// Blob trigger sample using a Stream binding
public static void Run(Stream myBlob, TraceWriter log)
{
   log.Info($"C# Blob trigger function Processed blob\n Name:{name} \n Size: {myBlob.Length} Bytes");
}
```

```cs
// Blob trigger binding to a CloudBlockBlob
#r "Microsoft.WindowsAzure.Storage"

using Microsoft.WindowsAzure.Storage.Blob;

public static void Run(CloudBlockBlob myBlob, string name, TraceWriter log)
{
    log.Info($"C# Blob trigger function Processed blob\n Name:{name}\nURI:{myBlob.StorageUri}");
}
```

<a name="triggernodejs"></a>

### <a name="trigger-example-in-nodejs"></a>Node.js의 트리거 예

```javascript
module.exports = function(context) {
    context.log('Node.js Blob trigger function processed', context.bindings.myBlob);
    context.done();
};
```
<a name="outputusage"></a>
<a name="storage-blob-output-binding"></a>

## <a name="using-a-blob-output-binding"></a>Blob 출력 바인딩 사용

.NET 함수에서는 함수 서명에 `out string` 매개 변수를 사용하거나, 다음 목록의 형식 중 하나를 사용해야 합니다. Node.js 함수에서 `context.bindings.<name>`을 사용하여 출력 Blob 데이터에 액세스합니다.

.NET 함수에서는 다음 형식으로 출력할 수 있습니다.

* `out string`
* `TextWriter`
* `Stream`
* `CloudBlobStream`
* `ICloudBlob`
* `CloudBlockBlob` 
* `CloudPageBlob` 

<a name="input-sample"></a>

## <a name="queue-trigger-with-blob-input-and-output-sample"></a>Blob 입력 및 출력이 있는 큐 트리거 샘플
[Queue Storage 트리거](functions-bindings-storage-queue.md), Blob Storage 입력 및 Blob Storage 출력을 정의하는 다음과 같은 function.json이 있다고 가정합니다. `queueTrigger` 메타데이터 속성이 Blob 입력 및 출력 `path` 속성에 사용된 것을 볼 수 있습니다.

```json
{
  "bindings": [
    {
      "queueName": "myqueue-items",
      "connection": "MyStorageConnection",
      "name": "myQueueItem",
      "type": "queueTrigger",
      "direction": "in"
    },
    {
      "name": "myInputBlob",
      "type": "blob",
      "path": "samples-workitems/{queueTrigger}",
      "connection": "MyStorageConnection",
      "direction": "in"
    },
    {
      "name": "myOutputBlob",
      "type": "blob",
      "path": "samples-workitems/{queueTrigger}-Copy",
      "connection": "MyStorageConnection",
      "direction": "out"
    }
  ],
  "disabled": false
}
``` 

출력 Blob에 입력 Blob을 복사하는 언어별 샘플을 참조하세요.

* [C#](#incsharp)
* [Node.JS](#innodejs)

<a name="incsharp"></a>

### <a name="blob-binding-example-in-c"></a>C#의 Blob 바인딩 예 #

```cs
// Copy blob from input to output, based on a queue trigger
public static void Run(string myQueueItem, Stream myInputBlob, out string myOutputBlob, TraceWriter log)
{
    log.Info($"C# Queue trigger function processed: {myQueueItem}");
    myOutputBlob = myInputBlob;
}
```

<a name="innodejs"></a>

### <a name="blob-binding-example-in-nodejs"></a>Node.js의 Blob 바인딩 예

```javascript
// Copy blob from input to output, based on a queue trigger
module.exports = function(context) {
    context.log('Node.js Queue trigger function processed', context.bindings.myQueueItem);
    context.bindings.myOutputBlob = context.bindings.myInputBlob;
    context.done();
};
```

## <a name="next-steps"></a>다음 단계
[!INCLUDE [next steps](../../includes/functions-bindings-next-steps.md)]

