---
title: "Azure Functions Blob Storage 바인딩"
description: "Azure Functions에서 Azure Blob Storage 트리거 및 바인딩을 사용하는 방법을 파악합니다."
services: functions
documentationcenter: na
author: ggailey777
manager: cfowler
editor: 
tags: 
keywords: "Azure Functions, 함수, 이벤트 처리, 동적 계산, 서버를 사용하지 않는 아키텍처"
ms.service: functions
ms.devlang: multiple
ms.topic: reference
ms.tgt_pltfrm: multiple
ms.workload: na
ms.date: 10/27/2017
ms.author: glenga
ms.openlocfilehash: 31a2fa3d3c87c16109514b130c95e731f401f8bd
ms.sourcegitcommit: c7215d71e1cdeab731dd923a9b6b6643cee6eb04
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/17/2017
---
# <a name="azure-functions-blob-storage-bindings"></a>Azure Functions Blob Storage 바인딩

이 문서에서는 Azure Functions에서 Azure Blob Storage 바인딩을 사용하는 방법을 설명합니다. Azure Functions는 Blob에 대한 트리거, 입력 및 출력 바인딩을 지원합니다.

[!INCLUDE [intro](../../includes/functions-bindings-intro.md)]

> [!NOTE]
> [Blob 전용 저장소 계정](../storage/common/storage-create-storage-account.md#blob-storage-accounts)은 지원되지 않습니다. Blob Storage 트리거 및 바인딩에는 범용 저장소 계정이 필요합니다. 

## <a name="blob-storage-trigger"></a>Blob Storage 트리거

Blob Storage 트리거를 사용하여 신규 또는 업데이트된 Blob를 검색할 때 함수를 사용합니다. Blob 내용은 함수의 입력으로 제공됩니다.

> [!NOTE]
> 소비 계획에서 Blob 트리거를 사용하는 경우 함수 앱이 유휴 상태가 된 후 새 Blob을 처리하는 데 최대 10분이 지연될 수 있습니다. 함수 앱이 실행된 후에는 Blob이 즉시 처리됩니다. 이 초기 지연을 방지하려면 다음 옵션 중 하나를 고려합니다.
> - 무중단이 사용되는 App Service 계획을 사용합니다.
> - Blob 이름을 포함하는 큐 메시지와 같은 다른 메커니즘을 사용하여 Blob 처리를 트리거합니다. 예를 들어 [이 문서의 뒷부분에 나오는 Blob 입/출력 바인딩 예제](#input--output---example)를 참조하세요.

## <a name="trigger---example"></a>트리거 - 예제

언어 관련 예제를 참조하세요.

* [미리 컴파일된 C#](#trigger---c-example)
* [C# 스크립트](#trigger---c-script-example)
* [JavaScript](#trigger---javascript-example)

### <a name="trigger---c-example"></a>트리거 - C# 예제

다음 예제에서는 `samples-workitems` 컨테이너에서 Blob을 추가하거나 업데이트할 때 로그를 기록하는 [미리 컴파일된 C#](functions-dotnet-class-library.md) 코드를 보여줍니다.

```csharp
[FunctionName("BlobTriggerCSharp")]        
public static void Run([BlobTrigger("samples-workitems/{name}")] Stream myBlob, string name, TraceWriter log)
{
    log.Info($"C# Blob trigger function Processed blob\n Name:{name} \n Size: {myBlob.Length} Bytes");
}
```

`BlobTrigger` 특성에 대한 자세한 내용은 [트리거 - 미리 컴파일된 C#의 특성](#trigger---attributes-for-precompiled-c)을 참조하세요.

### <a name="trigger---c-script-example"></a>트리거 - C# 스크립트 예제

다음 예에서는 바인딩을 사용하는 *function.json* 파일 및 [C# 스크립트](functions-reference-csharp.md) 코드에서 Blob 트리거 바인딩을 보여줍니다. 함수는 `samples-workitems` 컨테이너에서 Blob을 추가하거나 업데이트할 때 로그를 씁니다.

*function.json* 파일의 바인딩 데이터는 다음과 같습니다.

```json
{
    "disabled": false,
    "bindings": [
        {
            "name": "myBlob",
            "type": "blobTrigger",
            "direction": "in",
            "path": "samples-workitems",
            "connection":"MyStorageAccountAppSetting"
        }
    ]
}
```

[구성](#trigger---configuration) 섹션에서는 이러한 속성을 설명합니다.

`Stream`에 바인딩되는 C# 스크립트 코드는 다음과 같습니다.

```cs
public static void Run(Stream myBlob, TraceWriter log)
{
   log.Info($"C# Blob trigger function Processed blob\n Name:{name} \n Size: {myBlob.Length} Bytes");
}
```

`CloudBlockBlob`에 바인딩되는 C# 스크립트 코드는 다음과 같습니다.

```cs
#r "Microsoft.WindowsAzure.Storage"

using Microsoft.WindowsAzure.Storage.Blob;

public static void Run(CloudBlockBlob myBlob, string name, TraceWriter log)
{
    log.Info($"C# Blob trigger function Processed blob\n Name:{name}\nURI:{myBlob.StorageUri}");
}
```

### <a name="trigger---javascript-example"></a>트리거 - JavaScript 예제

다음 예에서는 바인딩을 사용하는 *function.json* 파일 및 [JavaScript 코드](functions-reference-node.md)에서 Blob 트리거 바인딩을 보여줍니다. 함수는 `samples-workitems` 컨테이너에서 Blob을 추가하거나 업데이트할 때 로그를 씁니다.

*function.json* 파일은 다음과 같습니다.

```json
{
    "disabled": false,
    "bindings": [
        {
            "name": "myBlob",
            "type": "blobTrigger",
            "direction": "in",
            "path": "samples-workitems",
            "connection":"MyStorageAccountAppSetting"
        }
    ]
}
```

[구성](#trigger---configuration) 섹션에서는 이러한 속성을 설명합니다.

JavaScript 코드는 다음과 같습니다.

```javascript
module.exports = function(context) {
    context.log('Node.js Blob trigger function processed', context.bindings.myBlob);
    context.done();
};
```

## <a name="trigger---attributes-for-precompiled-c"></a>트리거 - 미리 컴파일된 C#의 특성

[미리 컴파일된 C#](functions-dotnet-class-library.md) 함수의 경우 다음 특성을 사용하여 Blob 트리거를 구성합니다.

* [BlobTriggerAttribute](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs/BlobTriggerAttribute.cs)는 [Microsoft.Azure.WebJobs](http://www.nuget.org/packages/Microsoft.Azure.WebJobs) NuGet 패키지에 정의되어 있습니다.

  특성의 생성자는 조사할 컨테이너 및 선택적으로 [Blob 이름 패턴](#trigger---blob-name-patterns)을 나타내는 경로 문자열을 사용합니다. 예를 들면 다음과 같습니다.

  ```csharp
  [FunctionName("ResizeImage")]
  public static void Run(
      [BlobTrigger("sample-images/{name}")] Stream image, 
      [Blob("sample-images-md/{name}", FileAccess.Write)] Stream imageSmall)
  ```

  다음 예와 같이 사용할 저장소 계정을 지정하도록 `Connection` 속성을 설정할 수 있습니다.

   ```csharp
  [FunctionName("ResizeImage")]
  public static void Run(
      [BlobTrigger("sample-images/{name}", Connection = "StorageConnectionAppSetting")] Stream image, 
      [Blob("sample-images-md/{name}", FileAccess.Write)] Stream imageSmall)
  ```

* [StorageAccountAttribute](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs/StorageAccountAttribute.cs)는 [Microsoft.Azure.WebJobs](http://www.nuget.org/packages/Microsoft.Azure.WebJobs) NuGet 패키지에 정의되어 있습니다.

  사용할 저장소 계정을 지정하는 다른 방법을 제공합니다. 생성자는 저장소 연결 문자열을 포함하는 앱 설정의 이름을 사용합니다. 매개 변수, 메서드 또는 클래스 수준에서 특성을 적용할 수 있습니다. 다음 예제에서는 클래스 수준 및 메서드 수준을 보여줍니다.

  ```csharp
  [StorageAccount("ClassLevelStorageAppSetting")]
  public static class AzureFunctions
  {
      [FunctionName("BlobTrigger")]
      [StorageAccount("FunctionLevelStorageAppSetting")]
      public static void Run( //...
  ```

사용할 저장소 계정은 다음과 같은 순서로 결정됩니다.

* `BlobTrigger` 특성의 `Connection` 속성
* `BlobTrigger` 특성과 동일한 매개 변수에 적용된 `StorageAccount` 특성
* 함수에 적용된 `StorageAccount` 특성
* 클래스에 적용된 `StorageAccount` 특성
* 함수 앱의 기본 저장소 계정("AzureWebJobsStorage" 앱 설정)

## <a name="trigger---configuration"></a>트리거 - 구성

다음 표에서는 *function.json* 파일 및 `BlobTrigger` 특성에 설정된 바인딩 구성 속성을 설명합니다.

|function.json 속성 | 특성 속성 |설명|
|---------|---------|----------------------|
|**type** | 해당 없음 | `blobTrigger`로 설정해야 합니다. 이 속성은 사용자가 Azure Portal에서 트리거를 만들 때 자동으로 설정됩니다.|
|**direction** | 해당 없음 | `in`로 설정해야 합니다. 이 속성은 사용자가 Azure Portal에서 트리거를 만들 때 자동으로 설정됩니다. 예외는 [사용](#trigger---usage) 섹션에서 표시됩니다. |
|**name** | 해당 없음 | 함수 코드에서 Blob을 나타내는 변수의 이름입니다. | 
|**path** | **BlobPath** |모니터링할 컨테이너입니다.  [Blob 이름 패턴](#trigger-blob-name-patterns)일 수 있습니다. | 
|**연결** | **연결** | 이 바인딩에 사용할 저장소 연결 문자열을 포함하는 앱 설정의 이름입니다. 앱 설정 이름이 "AzureWebJobs"로 시작하는 경우 여기에서 이름의 나머지만을 지정할 수 있습니다. 예를 들어 `connection`을 "MyStorage"로 설정한 경우 함수 런타임 기능은 "AzureWebJobsMyStorage"라는 앱 설정을 찾습니다. `connection`을 비워 두면 함수 런타임 기능은 `AzureWebJobsStorage`라는 앱 설정에서 기본 저장소 연결 문자열을 사용합니다.<br><br>연결 문자열은 [Blob 전용 저장소 계정](../storage/common/storage-create-storage-account.md#blob-storage-accounts)이 아닌 범용 저장소 계정의 문자열이어야 합니다.<br>로컬로 개발하는 경우 앱 설정은 [local.settings.json 파일](functions-run-local.md#local-settings-file) 값으로 이동합니다.|

## <a name="trigger---usage"></a>트리거 - 사용

C# 및 C# 스크립트에서는 `Stream paramName`과 같은 메서드 매개 변수를 사용하여 Blob 데이터에 액세스합니다. C# 스크립트에서 `paramName`은 *function.json*의 `name` 속성에 지정된 값입니다. 다음 중 원하는 형식으로 바인딩할 수 있습니다.

* `TextReader`
* `Stream`
* `ICloudBlob`(*function.json*에서 "inout" 바인딩 방향 필요)
* `CloudBlockBlob`(*function.json*에서 "inout" 바인딩 방향 필요)
* `CloudPageBlob`(*function.json*에서 "inout" 바인딩 방향 필요)
* `CloudAppendBlob`(*function.json*에서 "inout" 바인딩 방향 필요)

언급했 듯이 이러한 형식 중 일부에는 *function.json*에서 `inout` 바인딩 방향이 필요합니다. 이 방향은 Azure Portal의 표준 편집기에서 지원되지 않으므로 고급 편집기를 사용해야 합니다.

텍스트 Blob이 필요한 경우 `string` 형식에 바인딩할 수 있습니다. 전체 Blob 내용이 메모리에 로드되므로 이는 Blob 크기가 작은 경우에만 권장됩니다. 일반적으로 `Stream` 또는 `CloudBlockBlob` 형식을 사용하는 것이 좋습니다.

JavaScript에서는 `context.bindings.<name>`을 사용하여 입력 Blob 데이터에 액세스합니다.

## <a name="trigger---blob-name-patterns"></a>트리거 - Blob 이름 패턴

*function.json*의 `path` 속성 또는 `BlobTrigger` 특성 생성자에서 Blob 이름 패턴을 지정할 수 있습니다. 이름 패턴은 [필터 또는 바인딩 식](functions-triggers-bindings.md#binding-expressions-and-patterns)일 수 있습니다.

### <a name="filter-on-blob-name"></a>Blob 이름에 대한 필터링

다음 예제는 "original-" 문자열로 시작하는 `input` 컨테이너에 있는 Blob에서만 트리거됩니다.

```json
"path": "input/original-{name}",
```
 
Blob 이름이 *original-Blob1.txt*인 경우 함수 코드에 있는 `name` 변수의 값은 `Blob1`입니다.

### <a name="filter-on-file-type"></a>파일 형식에 대한 필터링

다음 예제는 *.png* 파일에서만 트리거됩니다.

```json
"path": "samples/{name}.png",
```

### <a name="filter-on-curly-braces-in-file-names"></a>파일 이름에서 중괄호에 대한 필터링

파일 이름에서 중괄호를 찾으려면 2개의 중괄호를 사용하여 중괄호를 이스케이프합니다. 다음 예제는 중괄호가 이름에 포함된 Blob에 대해서만 필터링됩니다.

```json
"path": "images/{{20140101}}-{name}",
```

Blob의 이름이 *{20140101}-soundfile.mp3*인 경우 함수 코드에서 `name` 변수 값은 *soundfile.mp3*입니다. 

### <a name="get-file-name-and-extension"></a>파일 이름 및 확장명 가져오기

다음 예제에서는 Blob 파일 이름 및 확장명에 별도로 바인딩하는 방법을 보여줍니다.

```json
"path": "input/{blobname}.{blobextension}",
```
Blob이 *original-Blob1.txt*인 경우 함수 코드에 있는 `blobname` 및 `blobextension` 변수의 값은 *original-Blob1* 및 *txt*입니다.

## <a name="trigger---metadata"></a>트리거 - 메타데이터

Blob 트리거는 몇 가지 메타데이터 속성을 제공합니다. 이러한 속성을 다른 바인딩에서 바인딩 식의 일부로 사용하거나 코드에서 매개 변수로 사용할 수 있습니다. 이러한 값은 [CloudBlob](https://docs.microsoft.com/dotnet/api/microsoft.windowsazure.storage.blob.cloudblob?view=azure-dotnet) 형식과 동일한 의미 체계를 가집니다.


|속성  |형식  |설명  |
|---------|---------|---------|
|`BlobTrigger`|`string`|Blob을 트리거하는 경로입니다.|
|`Uri`|`System.Uri`|기본 위치에 대한 Blob의 URI입니다.|
|`Properties` |[BlobProperties](https://docs.microsoft.com/dotnet/api/microsoft.windowsazure.storage.blob.blobproperties)|Blob의 시스템 속성입니다. |
|`Metadata` |`IDictionary<string,string>`|Blob에 대한 사용자 정의 메타데이터입니다.|

## <a name="trigger---blob-receipts"></a>트리거 - Blob 수신 확인

Azure Functions 런타임은 동일한 새 Blob 또는 업데이트된 Blob에 대해 Blob 트리거 함수가 두 번 이상 호출되지 않도록 합니다. 지정된 Blob 버전이 처리되었는지 확인하려면 *Blob 수신 확인*을 유지 관리합니다.

Azure Functions는 사용자 함수 앱에서 사용하는(`AzureWebJobsStorage` 앱 설정에서 지정됨) Azure Storage 계정의 *azure-webjobs-hosts*라는 컨테이너에 Blob 수신 확인을 저장합니다. Blob 수신 확인에는 다음 정보가 포함됩니다.

* 트리거된 함수("*&lt;함수 앱 이름>*.Functions.*&lt;함수 이름>*", 예: "MyFunctionApp.Functions.CopyBlob")
* 컨테이너 이름
* Blob 유형("BlockBlob" 또는 "PageBlob")
* Blob 이름
* ETag(Blob 버전 식별자, 예: "0x8D1DC6E70A277EF")

Blob을 강제로 처리하려면 *azure-webjobs-hosts* 컨테이너에서 해당 Blob에 대한 Blob 수신 확인을 수동으로 삭제하면 됩니다.

## <a name="trigger---poison-blobs"></a>트리거 - 포이즌 Blob

지정된 Blob에 대한 Blob 트리거 함수가 실패한 경우 Azure Functions는 기본적으로 총 5번 해당 함수를 다시 시도합니다. 

5번 모두 실패한 경우 Azure Functions는 *webjobs-blobtrigger-poison*이라는 저장소 큐에 메시지를 추가합니다. 포이즌 Blob에 대한 큐 메시지는 다음 속성을 포함하는 JSON 개체입니다.

* FunctionId(형식에서 *&lt;함수 앱 이름>*.Functions.*&lt;함수 이름>*)
* BlobType("BlockBlob" 또는 "PageBlob")
* ContainerName
* BlobName
* ETag(Blob 버전 식별자, 예: "0x8D1DC6E70A277EF")

## <a name="trigger---polling-for-large-containers"></a>트리거 - 큰 컨테이너에 대한 폴링

모니터링 중인 Blob 컨테이너에 10,000개 이상의 Blob이 포함된 경우 Functions 런타임은 로그 파일을 스캔하여 새롭거나 변경된 Blob을 확인합니다. 이 프로세스는 지연이 발생할 수 있습니다. Blob을 만든 후 몇 분이 경과할 때까지 함수가 트리거되지 않을 수도 있습니다. 또한 [저장소 로그는 "최선을 다해" 생성됩니다](/rest/api/storageservices/About-Storage-Analytics-Logging). 하지만 모든 이벤트가 캡처되는 것은 아닙니다. 경우에 따라 로그가 누락될 수 있습니다. 더 빠르거나 안정적인 Blob 처리가 필요한 경우 Blob을 만들 때 [큐 메시지](../storage/queues/storage-dotnet-how-to-use-queues.md)를 만드는 것이 좋습니다. 그런 다음 Blob 트리거 대신 [큐 트리거](functions-bindings-storage-queue.md)를 사용하여 Blob을 처리합니다. 다른 옵션은 Event Grid를 사용하는 겻입니다. [Event Grid를 사용하여 업로드된 이미지 크기 자동 조정](../event-grid/resize-images-on-storage-blob-upload-event.md) 자습서를 참조하세요.

## <a name="blob-storage-input--output-bindings"></a>Blob Storage 입력 및 출력 바인딩

Blob Storage 입력 및 출력 바인딩을 사용하여 Blob 읽기 및 쓰기

## <a name="input--output---example"></a>입력 및 출력 - 예제

언어 관련 예제를 참조하세요.

* [미리 컴파일된 C#](#input--output---c-example)
* [C# 스크립트](#input--output---c-script-example)
* [JavaScript](#input--output---javascript-example)

### <a name="input--output---c-example"></a>입력 및 출력 - C# 예제

다음 예제는 하나의 Blob 트리거와 두 개의 출력 Blob 바인딩을 사용하는 [미리 컴파일된 C#](functions-dotnet-class-library.md) 함수입니다. *샘플 이미지* 컨테이너에서 이미지 Blob을 만들어서 함수를 트리거합니다. 그러면 이미지 Blob의 소량 및 중간 크기 복사본을 만듭니다. 

```csharp
[FunctionName("ResizeImage")]
public static void Run(
    [BlobTrigger("sample-images/{name}")] Stream image, 
    [Blob("sample-images-sm/{name}", FileAccess.Write)] Stream imageSmall, 
    [Blob("sample-images-md/{name}", FileAccess.Write)] Stream imageMedium)
{
    var imageBuilder = ImageResizer.ImageBuilder.Current;
    var size = imageDimensionsTable[ImageSize.Small];

    imageBuilder.Build(image, imageSmall,
        new ResizeSettings(size.Item1, size.Item2, FitMode.Max, null), false);

    image.Position = 0;
    size = imageDimensionsTable[ImageSize.Medium];

    imageBuilder.Build(image, imageMedium,
        new ResizeSettings(size.Item1, size.Item2, FitMode.Max, null), false);
}

public enum ImageSize { ExtraSmall, Small, Medium }

private static Dictionary<ImageSize, (int, int)> imageDimensionsTable = new Dictionary<ImageSize, (int, int)>() {
    { ImageSize.ExtraSmall, (320, 200) },
    { ImageSize.Small,      (640, 400) },
    { ImageSize.Medium,     (800, 600) }
};
```        

### <a name="input--output---c-script-example"></a>입력 및 출력 - C# 스크립트 예제

다음 예에서는 바인딩을 사용하는 *function.json* 파일 및 [C# 스크립트](functions-reference-csharp.md) 코드에서 Blob 입력 및 출력 바인딩을 보여줍니다. 함수는 텍스트 Blob의 복사본을 만듭니다. 함수는 복사할 Blob의 이름을 포함하는 큐 메시지에 의해 트리거됩니다. 새 Blob의 이름은 *{originalblobname}-Copy*입니다.

*function.json* 파일에서 `queueTrigger` 메타데이터 속성은 `path` 속성에서 Blob 이름을 지정하는 데 사용됩니다.

```json
{
  "bindings": [
    {
      "queueName": "myqueue-items",
      "connection": "MyStorageConnectionAppSetting",
      "name": "myQueueItem",
      "type": "queueTrigger",
      "direction": "in"
    },
    {
      "name": "myInputBlob",
      "type": "blob",
      "path": "samples-workitems/{queueTrigger}",
      "connection": "MyStorageConnectionAppSetting",
      "direction": "in"
    },
    {
      "name": "myOutputBlob",
      "type": "blob",
      "path": "samples-workitems/{queueTrigger}-Copy",
      "connection": "MyStorageConnectionAppSetting",
      "direction": "out"
    }
  ],
  "disabled": false
}
``` 

[구성](#input--output---configuration) 섹션에서는 이러한 속성을 설명합니다.

C# 스크립트 코드는 다음과 같습니다.

```cs
public static void Run(string myQueueItem, string myInputBlob, out string myOutputBlob, TraceWriter log)
{
    log.Info($"C# Queue trigger function processed: {myQueueItem}");
    myOutputBlob = myInputBlob;
}
```

### <a name="input--output---javascript-example"></a>입력 및 출력 - JavaScript 예제

다음 예에서는 바인딩을 사용하는 *function.json* 파일 및 [JavaScript 코드](functions-reference-node.md)에서 Blob 입력 및 출력 바인딩을 보여줍니다. 함수는 Blob의 복사본을 만듭니다. 함수는 복사할 Blob의 이름을 포함하는 큐 메시지에 의해 트리거됩니다. 새 Blob의 이름은 *{originalblobname}-Copy*입니다.

*function.json* 파일에서 `queueTrigger` 메타데이터 속성은 `path` 속성에서 Blob 이름을 지정하는 데 사용됩니다.

```json
{
  "bindings": [
    {
      "queueName": "myqueue-items",
      "connection": "MyStorageConnectionAppSetting",
      "name": "myQueueItem",
      "type": "queueTrigger",
      "direction": "in"
    },
    {
      "name": "myInputBlob",
      "type": "blob",
      "path": "samples-workitems/{queueTrigger}",
      "connection": "MyStorageConnectionAppSetting",
      "direction": "in"
    },
    {
      "name": "myOutputBlob",
      "type": "blob",
      "path": "samples-workitems/{queueTrigger}-Copy",
      "connection": "MyStorageConnectionAppSetting",
      "direction": "out"
    }
  ],
  "disabled": false
}
``` 

[구성](#input--output---configuration) 섹션에서는 이러한 속성을 설명합니다.

JavaScript 코드는 다음과 같습니다.

```javascript
module.exports = function(context) {
    context.log('Node.js Queue trigger function processed', context.bindings.myQueueItem);
    context.bindings.myOutputBlob = context.bindings.myInputBlob;
    context.done();
};
```

## <a name="input--output---attributes-for-precompiled-c"></a>입력 및 출력 - 미리 컴파일된 C#의 특성

[미리 컴파일된 C#](functions-dotnet-class-library.md) 함수의 경우 [Microsoft.Azure.WebJobs](http://www.nuget.org/packages/Microsoft.Azure.WebJobs) NuGet 패키지에 정의되어 있는 [BlobAttribute](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs/BlobAttribute.cs)를 사용합니다.

특성의 생성자는 다음 예제에 표시된 대로 Blob에 대한 경로 및 읽기 또는 쓰기를 나타내는 `FileAccess` 매개 변수를 사용합니다.

```csharp
[FunctionName("ResizeImage")]
public static void Run(
    [BlobTrigger("sample-images/{name}")] Stream image, 
    [Blob("sample-images-md/{name}", FileAccess.Write)] Stream imageSmall)
```

다음 예와 같이 사용할 저장소 계정을 지정하도록 `Connection` 속성을 설정할 수 있습니다.

```csharp
[FunctionName("ResizeImage")]
public static void Run(
    [BlobTrigger("sample-images/{name}")] Stream image, 
    [Blob("sample-images-md/{name}", FileAccess.Write, Connection = "StorageConnectionAppSetting")] Stream imageSmall)
```

`StorageAccount` 특성을 사용하여 클래스, 메서드 또는 매개 변수 수준에서 저장소 계정을 지정합니다. 자세한 내용은 [트리거 - 미리 컴파일된 C#의 특성](#trigger---attributes-for-precompiled-c)을 참조하세요.

## <a name="input--output---configuration"></a>입력 및 출력 - 구성

다음 표에서는 *function.json* 파일 및 `Blob` 특성에 설정된 바인딩 구성 속성을 설명합니다.

|function.json 속성 | 특성 속성 |설명|
|---------|---------|----------------------|
|**type** | 해당 없음 | `blob`로 설정해야 합니다. |
|**direction** | 해당 없음 | 입력 바인딩의 경우 `in`으로 설정하거나 출력 바인딩의 경우 out으로 설정해야 합니다. 예외는 [사용](#input--output---usage) 섹션에서 표시됩니다. |
|**name** | 해당 없음 | 함수 코드에서 Blob을 나타내는 변수의 이름입니다.  `$return`으로 설정하여 함수 반환 값을 참조합니다.|
|**path** |**BlobPath** | Blob에 대한 경로입니다. | 
|**연결** |**연결**| 이 바인딩에 사용할 저장소 연결 문자열을 포함하는 앱 설정의 이름입니다. 앱 설정 이름이 "AzureWebJobs"로 시작하는 경우 여기에서 이름의 나머지만을 지정할 수 있습니다. 예를 들어 `connection`을 "MyStorage"로 설정한 경우 함수 런타임 기능은 "AzureWebJobsMyStorage"라는 앱 설정을 찾습니다. `connection`을 비워 두면 함수 런타임 기능은 `AzureWebJobsStorage`라는 앱 설정에서 기본 저장소 연결 문자열을 사용합니다.<br><br>연결 문자열은 [Blob 전용 저장소 계정](../storage/common/storage-create-storage-account.md#blob-storage-accounts)이 아닌 범용 저장소 계정의 문자열이어야 합니다.<br>로컬로 개발하는 경우 앱 설정은 [local.settings.json 파일](functions-run-local.md#local-settings-file) 값으로 이동합니다.|
|해당 없음 | **Access** | 읽기 또는 쓰기를 나타냅니다. |

## <a name="input--output---usage"></a>입력 및 출력 - 사용

미리 컴파일된 C# 및 C# 스크립트에서는 `Stream paramName`과 같은 메서드 매개 변수를 사용하여 Blob에 액세스합니다. C# 스크립트에서 `paramName`은 *function.json*의 `name` 속성에 지정된 값입니다. 다음 중 원하는 형식으로 바인딩할 수 있습니다.

* `out string`
* `TextWriter` 
* `TextReader`
* `Stream`
* `ICloudBlob`(*function.json*에서 "inout" 바인딩 방향 필요)
* `CloudBlockBlob`(*function.json*에서 "inout" 바인딩 방향 필요)
* `CloudPageBlob`(*function.json*에서 "inout" 바인딩 방향 필요)
* `CloudAppendBlob`(*function.json*에서 "inout" 바인딩 방향 필요)

언급했 듯이 이러한 형식 중 일부에는 *function.json*에서 `inout` 바인딩 방향이 필요합니다. 이 방향은 Azure Portal의 표준 편집기에서 지원되지 않으므로 고급 편집기를 사용해야 합니다.

텍스트 Blob을 읽는 경우 `string` 형식에 바인딩할 수 있습니다. 전체 Blob 내용이 메모리에 로드되므로 이 형식은 Blob 크기가 작은 경우에만 권장됩니다. 일반적으로 `Stream` 또는 `CloudBlockBlob` 형식을 사용하는 것이 좋습니다.

JavaScript에서는 `context.bindings.<name>`을 사용하여 Blob 데이터에 액세스합니다.

## <a name="next-steps"></a>다음 단계

> [!div class="nextstepaction"]
> [Blob Storage 트리거를 사용하는 빠른 시작으로 이동](functions-create-storage-blob-triggered-function.md)

> [!div class="nextstepaction"]
> [Azure Functions 트리거 및 바인딩에 대한 자세한 정보](functions-triggers-bindings.md)
