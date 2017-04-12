---
title: "Azure Functions Storage Blob 바인딩 | Microsoft Docs"
description: "Azure Functions에서 Azure Storage 트리거 및 바인딩을 사용하는 방법을 파악합니다."
services: functions
documentationcenter: na
author: christopheranderson
manager: erikre
editor: 
tags: 
keywords: "Azure Functions, 함수, 이벤트 처리, 동적 계산, 서버를 사용하지 않는 아키텍처"
ms.assetid: aba8976c-6568-4ec7-86f5-410efd6b0fb9
ms.service: functions
ms.devlang: multiple
ms.topic: reference
ms.tgt_pltfrm: multiple
ms.workload: na
ms.date: 03/06/2017
ms.author: chrande, glenga
translationtype: Human Translation
ms.sourcegitcommit: eeb56316b337c90cc83455be11917674eba898a3
ms.openlocfilehash: 7b4ae9281bca20949c37b2c797e4a1a677665929
ms.lasthandoff: 04/03/2017


---
# <a name="azure-functions-storage-blob-bindings"></a>Azure Functions Storage Blob 바인딩
[!INCLUDE [functions-selector-bindings](../../includes/functions-selector-bindings.md)]

이 문서에서는 Azure Functions에서 Azure Storage Blob 바인딩을 구성하고 코딩하는 방법을 설명합니다. Azure Functions는 Azure Storage Blob에 대한 트리거, 입력 및 출력 바인딩을 지원합니다.

[!INCLUDE [intro](../../includes/functions-bindings-intro.md)]

> [!NOTE]
> [Blob 전용 저장소 계정](../storage/storage-create-storage-account.md#blob-storage-accounts)은 지원되지 않습니다. Azure Functions에는 Blob과 함께 사용하는 범용 저장소 계정이 필요 합니다. 
> 
> 

<a name="trigger"></a>

## <a name="storage-blob-trigger"></a>Storage Blob 트리거
Azure Storage Blob 트리거를 통해 저장소 컨테이너에서 새롭고 업데이트된 Blob을 모니터링하고 변경 사항이 감지되면 함수 코드를 실행할 수 있습니다. 

함수에 대한 Storage Blob 트리거는 function.json의 `bindings` 배열에서 다음과 같은 JSON 개체를 사용합니다.

```json
{
    "name": "<Name of input parameter in function signature>",
    "type": "blobTrigger",
    "direction": "in",
    "path": "<container to monitor, and optionally a blob name pattern - see below>",
    "connection":"<Name of app setting - see below>"
}
```

다음 사항에 유의하세요.

* `path`에 대해서는 [Name patterns](#pattern)를 참조하여 Blob 이름 패턴을 형성하는 방법에 대해 알아보세요.
* `connection`은 저장소 연결 문자열을 포함하는 앱 설정의 이름을 포함해야 합니다. Azure Portal에서 **통합** 탭에 있는 표준 편집기는 저장소 계정을 만들거나 기존 계정을 선택하는 경우 사용하는 이 앱 설정을 구성합니다. 이 앱 설정을 수동으로 만들려면 [이 앱 설정을 수동으로 구성](functions-how-to-use-azure-function-app-settings.md)을 참조하세요. 

소비 계획을 실행할 때 함수 앱이 유휴 상태가 되는 경우 새 Blob 처리에 하루 최대 10분이 걸릴 수 있습니다. 함수 앱이 실행되면 Blob이 더 신속하게 처리됩니다. 이 초기 지연을 방지하려면 Always On을 활성화하여 App Service 계획을 사용하거나 다른 메커니즘을 사용하여 Blob 이름을 포함하는 큐 메시지처럼 Blob 처리를 트리거합니다. 

또한 자세한 내용은 다음 부제목 중 하나를 참조하세요.

* [Name patterns](#pattern)
* [Blob receipts](#receipts)
* [Handling poison blobs](#poison)

<a name="pattern"></a>

### <a name="name-patterns"></a>이름 패턴
`path` 속성에 Blob 이름 패턴을 지정할 수 있습니다. 예:

```json
"path": "input/original-{name}",
```

이 경로에는 *input* 컨테이너에 *original-Blob1.txt*라는 Blob이 있으며 함수 코드에 있는 `name` 변수의 값은 `Blob1`입니다.

다른 예제:

```json
"path": "input/{blobname}.{blobextension}",
```

또한 이 경로에는 *original-Blob1.txt*라는 Blob이 있으며 함수 코드에 있는 `blobname` 및 `blobextension` 변수의 값은 *original-Blob1* 및 *txt*입니다.

파일 확장명에 고정된 값을 사용하여 Blob의 파일 형식을 제한할 수 있습니다. 예:

```json
"path": "samples/{name}.png",
```

이 경우 *samples* 컨테이너의 *.png* Blob만 함수를 트리거합니다.

중괄호는 이름 패턴에서 특수 문자입니다. 이름에 중괄호가 있는 Blob 이름을 지정하려면 이중 중괄호를 사용합니다. 예:

```json
"path": "images/{{20140101}}-{name}",
```

이 경로에는 *images* 컨테이너에 *{20140101}-soundfile.mp3*라는 Blob이 있으며 함수 코드에 있는 `name` 변수 값은 *soundfile.mp3*입니다. 

<a name="receipts"></a>

### <a name="blob-receipts"></a>Blob 수신 확인
Azure Functions 런타임은 동일한 새 Blob 또는 업데이트된 Blob에 대해 Blob 트리거 함수가 두 번 이상 호출되지 않도록 합니다. 이를 위해 *Blob 수신 확인*을 유지 관리하여 지정된 Blob 버전이 처리되었는지 확인합니다.

Blob 수신 확인은 사용자 함수 앱에서 사용하는(`AzureWebJobsStorage` 앱 설정에서 지정됨) Azure Storage 계정의 *azure-webjobs-hosts*라는 컨테이너에 저장됩니다. Blob 수신 확인에는 다음 정보가 포함됩니다.

* 트리거된 함수("*&lt;함수 앱 이름>*.Functions.*&lt;함수 이름>*", 예: "functionsf74b96f7.Functions.CopyBlob")
* 컨테이너 이름
* Blob 유형("BlockBlob" 또는 "PageBlob")
* Blob 이름
* ETag(Blob 버전 식별자, 예: "0x8D1DC6E70A277EF")

Blob을 강제로 처리하려면 *azure-webjobs-hosts* 컨테이너에서 해당 Blob에 대한 Blob 수신 확인을 수동으로 삭제하면 됩니다.

<a name="poison"></a>

### <a name="handling-poison-blobs"></a>포이즌 Blob 처리
Blob 트리거 함수가 실패하는 경우 Azure Functions는 해당 함수를 지정된 Blob에 대해 기본적으로 최대 5번(첫 번째 시도 포함) 다시 시도합니다. 5번 모두 실패할 경우 Functions는 메시지를 *webjobs-blobtrigger-poison*이라는 Storage 큐에 추가합니다. 포이즌 Blob에 대한 큐 메시지는 다음 속성을 포함하는 JSON 개체입니다.

* FunctionId(형식에서 *&lt;함수 앱 이름>*.Functions.*&lt;함수 이름>*)
* BlobType("BlockBlob" 또는 "PageBlob")
* ContainerName
* BlobName
* ETag(Blob 버전 식별자, 예: "0x8D1DC6E70A277EF")

### <a name="blob-polling-for-large-containers"></a>큰 컨테이너에 대한 Blob 폴링
바인딩으로 감시하는 Blob 컨테이너가 10,000개 이상의 Blob을 포함하는 경우 Functions 런타임은 로그 파일을 스캔하여 새롭거나 변경된 Blob을 확인합니다. 이 프로세스는 실시간으로 하지 않습니다. Blob을 만든 후 몇 분이 경과할 때까지 함수가 트리거되지 않을 수도 있습니다. 또한 [저장소 로그는 "최선을 다해" 생성됩니다](https://msdn.microsoft.com/library/azure/hh343262.aspx). 하지만 모든 이벤트가 캡처되는 것은 아닙니다. 경우에 따라 로그가 누락될 수 있습니다. 응용 프로그램에서 큰 컨테이너에 대한 Blob 트리거의 속도 및 안정성 제한 사항이 적합하지 않을 경우, Blob을 만들 때 [큐 메시지](../storage/storage-dotnet-how-to-use-queues.md)를 만들고 Blob을 처리하는 Blob 트리거 대신 [큐 트리거](functions-bindings-storage-queue.md)를 사용하는 것이 좋습니다.

<a name="triggerusage"></a>

## <a name="trigger-usage"></a>트리거 사용
C# 함수에서 `<T> <name>`같은 함수 시그니처의 명명된 매개 변수를 사용하여 입력 Blob 데이터를 바인딩합니다.
여기서 `T`는 데이터를 deserialize할 데이터 형식이며, `paramName`은 [JSON 트리거](#trigger)에서 사용자가 지정한 이름입니다. Node.js 함수에서 `context.bindings.<name>`을 사용하여 입력 Blob 데이터에 액세스합니다.

Blob을 다음 중 원하는 형식으로 deserialize할 수 있습니다.

* 모든 [개체](https://msdn.microsoft.com/library/system.object.aspx)는 JSON 직렬화된 Blob 데이터에 유용 합니다.
  사용자 지정 입력 형식을 선언하는 경우(예: `FooType`), Azure Functions에서 지정된 형식에 JSON 데이터를 deserialize하려고 시도합니다.
* 문자열은 텍스트 Blob 데이터에 유용합니다.

C# 함수에서 다음 형식 중 하나에 바인딩할 수도 있으며, Functions 런타임이 해당 형식을 사용하여 Blob 데이터를 deserialize하려고 시도하게 됩니다.

* `TextReader`
* `Stream`
* `ICloudBlob`
* `CloudBlockBlob`
* `CloudPageBlob`
* `CloudBlobContainer`
* `CloudBlobDirectory`
* `IEnumerable<CloudBlockBlob>`
* `IEnumerable<CloudPageBlob>`
* [ICloudBlobStreamBinder](../app-service-web/websites-dotnet-webjobs-sdk-storage-blobs-how-to.md#icbsb) 

## <a name="trigger-sample"></a>트리거 샘플
Storage Blob 트리거를 정의하는 다음과 같은 function.json이 있다고 가정합니다.

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

모니터링되는 컨테이너에 추가된 각 Blob의 콘텐츠를 기록하는 언어별 샘플을 참조하세요.

* [C#](#triggercsharp)
* [Node.JS](#triggernodejs)

<a name="triggercsharp"></a>

### <a name="trigger-usage-in-c"></a>C에서 트리거 사용# #

```cs
public static void Run(string myBlob, TraceWriter log)
{
    log.Info($"C# Blob trigger function processed: {myBlob}");
}
```

<!--
<a name="triggerfsharp"></a>
### Trigger usage in F# ##
```fsharp

``` 
-->

<a name="triggernodejs"></a>

### <a name="trigger-usage-in-nodejs"></a>Node.js에서 트리거 사용

```javascript
module.exports = function(context) {
    context.log('Node.js Blob trigger function processed', context.bindings.myBlob);
    context.done();
};
```

<a name="input"></a>

## <a name="storage-blob-input-binding"></a>Storage Blob 입력 바인딩
Azure Storage Blob 입력 바인딩을 사용하면 함수의 저장소 컨테이너에서 Blob을 사용할 수 있습니다. 

함수에 대한 Storage Blob 입력은 function.json의 `bindings` 배열에서 다음과 같은 JSON 개체를 사용합니다.

```json
{
  "name": "<Name of input parameter in function signature>",
  "type": "blob",
  "direction": "in"
  "path": "<Path of input blob - see below>",
  "connection":"<Name of app setting - see below>"
},
```

다음 사항에 유의하세요.

* `path`는 컨테이너 이름과 Blob 이름을 포함해야 합니다. 예를 들어 함수에 하나의 [큐 트리거](functions-bindings-storage-queue.md)가 있는 경우 `"path": "samples-workitems/{queueTrigger}"`를 사용하여 트리거 메시지에서 지정된 Blob 이름과 일치하는 이름을 가진 Blob을 `samples-workitems` 컨테이너에서 가리키도록 할 수 있습니다.   
* `connection`은 저장소 연결 문자열을 포함하는 앱 설정의 이름을 포함해야 합니다. Azure Portal에서 **통합** 탭에 있는 표준 편집기는 저장소 계정을 만들거나 기존 계정을 선택하는 경우 사용하는 이 앱 설정을 구성합니다. 이 앱 설정을 수동으로 만들려면 [이 앱 설정을 수동으로 구성](functions-how-to-use-azure-function-app-settings.md)을 참조하세요. 

<a name="inputusage"></a>

## <a name="input-usage"></a>입력 사용
C# 함수에서 `<T> <name>`같은 함수 시그니처의 명명된 매개 변수를 사용하여 입력 Blob 데이터를 바인딩합니다.
여기서 `T`는 데이터를 deserialize할 데이터 형식이며, `paramName`은 [입력 바인딩](#input)에서 사용자가 지정한 이름입니다. Node.js 함수에서 `context.bindings.<name>`을 사용하여 입력 Blob 데이터에 액세스합니다.

Blob을 다음 중 원하는 형식으로 deserialize할 수 있습니다.

* 모든 [개체](https://msdn.microsoft.com/library/system.object.aspx)는 JSON 직렬화된 Blob 데이터에 유용 합니다.
  사용자 지정 입력 형식을 선언하는 경우(예: `InputType`), Azure Functions에서 지정된 형식에 JSON 데이터를 deserialize하려고 시도합니다.
* 문자열은 텍스트 Blob 데이터에 유용합니다.

C# 함수에서 다음 형식 중 하나에 바인딩할 수도 있으며, Functions 런타임이 해당 형식을 사용하여 Blob 데이터를 deserialize하려고 시도하게 됩니다.

* `TextReader`
* `Stream`
* `ICloudBlob`
* `CloudBlockBlob` 
* `CloudPageBlob` 

<a name="inputsample"></a>

## <a name="input-sample"></a>입력 샘플
[Storage 큐 트리거](functions-bindings-storage-queue.md), Storage Blob 입력 및 Storage Blob 출력을 정의하는 다음과 같은 function.json이 있다고 가정합니다.

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

### <a name="input-usage-in-c"></a>C에서 입력 사용# #

```cs
public static void Run(string myQueueItem, string myInputBlob, out string myOutputBlob, TraceWriter log)
{
    log.Info($"C# Queue trigger function processed: {myQueueItem}");
    myOutputBlob = myInputBlob;
}
```

<!--
<a name="infsharp"></a>
### Input usage in F# ##
```fsharp

``` 
-->

<a name="innodejs"></a>

### <a name="input-usage-in-nodejs"></a>Node.js에서 입력 사용

```javascript
module.exports = function(context) {
    context.log('Node.js Queue trigger function processed', context.bindings.myQueueItem);
    context.bindings.myOutputBlob = context.bindings.myInputBlob;
    context.done();
};
```

<a name="output"></a>

## <a name="storage-blob-output-binding"></a>Storage Blob 출력 바인딩
Azure Storage Blob 출력 바인딩을 사용하면 함수의 저장소 컨테이너에 Blob을 작성할 수 있습니다. 

함수에 대한 Storage Blob 출력은 function.json의 `bindings` 배열에서 다음과 같은 JSON 개체를 사용합니다.

```json
{
  "name": "<Name of output parameter in function signature>",
  "type": "blob",
  "direction": "out",
  "path": "<Path of input blob - see below>",
  "connection": "<Name of app setting - see below>"
}
```

다음 사항에 유의하세요.

* `path`는 작성할 컨테이너 이름과 Blob 이름을 포함해야 합니다. 예를 들어 함수에 하나의 [큐 트리거](functions-bindings-storage-queue.md)가 있는 경우 `"path": "samples-workitems/{queueTrigger}"`를 사용하여 트리거 메시지에서 지정된 Blob 이름과 일치하는 이름을 가진 Blob을 `samples-workitems` 컨테이너에서 가리키도록 할 수 있습니다.   
* `connection`은 저장소 연결 문자열을 포함하는 앱 설정의 이름을 포함해야 합니다. Azure Portal에서 **통합** 탭에 있는 표준 편집기는 저장소 계정을 만들거나 기존 계정을 선택하는 경우 사용하는 이 앱 설정을 구성합니다. 이 앱 설정을 수동으로 만들려면 [이 앱 설정을 수동으로 구성](functions-how-to-use-azure-function-app-settings.md)을 참조하세요. 

<a name="outputusage"></a>

## <a name="output-usage"></a>출력 사용
C# 함수에서 `out <T> <name>`같은 함수 시그니처의 명명된 `out` 매개 변수를 사용하여 출력 Blob을 바인딩합니다. 여기서 `T`는 데이터를 직렬화하려는 데이터 형식이며, `paramName`은 [출력 바인딩](#output)에서 사용자가 지정한 이름입니다. Node.js 함수에서 `context.bindings.<name>`을 사용하여 출력 Blob 데이터에 액세스합니다.

다음 형식 중 하나를 사용하여 출력 Blob을 작성할 수 있습니다.

* 모든 [개체](https://msdn.microsoft.com/library/system.object.aspx)는 JSON 직렬화에 유용합니다.
  사용자 지정 출력 형식을 선언하는 경우(예: `out OutputType paramName`), Azure Functions에서 개체를 JSON으로 직렬화하려고 시도합니다. 함수가 종료될 때 출력 매개 변수가 null이면 Functions 런타임은 Blob을 null 개체로 만듭니다.
* 문자열(`out string paramName`)은 텍스트 Blob 데이터에 유용합니다. Functions 런타임은 함수가 종료될 때 문자열 매개 변수가 null이 아닌 경우에만 Blob을 생성합니다.

C# 함수에서 다음 중 원하는 형식으로 출력할 수 있습니다.

* `TextWriter`
* `Stream`
* `CloudBlobStream`
* `ICloudBlob`
* `CloudBlockBlob` 
* `CloudPageBlob` 

<a name="outputsample"></a>

## <a name="output-sample"></a>출력 샘플
[입력 샘플](#inputsample)을 참조하세요.

## <a name="next-steps"></a>다음 단계
[!INCLUDE [next steps](../../includes/functions-bindings-next-steps.md)]


