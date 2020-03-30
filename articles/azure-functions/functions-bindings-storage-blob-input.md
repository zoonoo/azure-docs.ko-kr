---
title: Azure 함수에 대한 Azure Blob 저장소 입력 바인딩
description: Azure Blob 저장소 데이터를 Azure 함수에 제공하는 방법에 대해 알아봅니다.
author: craigshoemaker
ms.topic: reference
ms.date: 02/13/2020
ms.author: cshoe
ms.openlocfilehash: e074d7d74c0c5f020cb8086124634b25012927db
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/27/2020
ms.locfileid: "77202152"
---
# <a name="azure-blob-storage-input-binding-for-azure-functions"></a>Azure 함수에 대한 Azure Blob 저장소 입력 바인딩

입력 바인딩을 사용하면 Blob 저장소 데이터를 Azure Function에 대한 입력으로 읽을 수 있습니다.

설정 및 구성 세부 정보에 대한 자세한 내용은 [개요를](./functions-bindings-storage-blob.md)참조하십시오.

## <a name="example"></a>예제

# <a name="c"></a>[C #](#tab/csharp)

다음 예제는 하나의 큐 트리거와 입력 Blob 바인딩을 사용하는 [C# 함수](functions-dotnet-class-library.md)입니다. 큐 메시지에는 Blob의 이름이 포함되고 함수는 Blob의 크기를 기록합니다.

```csharp
[FunctionName("BlobInput")]
public static void Run(
    [QueueTrigger("myqueue-items")] string myQueueItem,
    [Blob("samples-workitems/{queueTrigger}", FileAccess.Read)] Stream myBlob,
    ILogger log)
{
    log.LogInformation($"BlobInput processed blob\n Name:{myQueueItem} \n Size: {myBlob.Length} bytes");
}
```

# <a name="c-script"></a>[C# 스크립트](#tab/csharp-script)

<!--Same example for input and output. -->

다음 예제에서는 바인딩을 사용하는 *function.json* 파일 및 [C# 스크립트(.csx)](functions-reference-csharp.md) 코드에서 Blob 입력 및 출력 바인딩을 보여줍니다. 함수는 텍스트 Blob의 복사본을 만듭니다. 함수는 복사할 Blob의 이름을 포함하는 큐 메시지에 의해 트리거됩니다. 새 Blob의 이름은 *{originalblobname}-복사입니다.*

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

[구성](#configuration) 섹션에서는 이러한 속성을 설명합니다.

C# 스크립트 코드는 다음과 같습니다.

```cs
public static void Run(string myQueueItem, string myInputBlob, out string myOutputBlob, ILogger log)
{
    log.LogInformation($"C# Queue trigger function processed: {myQueueItem}");
    myOutputBlob = myInputBlob;
}
```

# <a name="javascript"></a>[자바 스크립트](#tab/javascript)

<!--Same example for input and output. -->

다음 예제에서는 바인딩을 사용하는 *function.json* 파일 및 [JavaScript 스크립트](functions-reference-node.md) 코드에서 Blob 입력 및 출력 바인딩을 보여 줍니다. 함수는 Blob의 복사본을 만듭니다. 함수는 복사할 Blob의 이름을 포함하는 큐 메시지에 의해 트리거됩니다. 새 Blob의 이름은 *{originalblobname}-복사입니다.*

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

[구성](#configuration) 섹션에서는 이러한 속성을 설명합니다.

JavaScript 코드는 다음과 같습니다.

```javascript
module.exports = function(context) {
    context.log('Node.js Queue trigger function processed', context.bindings.myQueueItem);
    context.bindings.myOutputBlob = context.bindings.myInputBlob;
    context.done();
};
```

# <a name="python"></a>[Python](#tab/python)

<!--Same example for input and output. -->

다음 예제에서는 바인딩을 사용하는 *function.json* 파일 및 [Python 코드](functions-reference-python.md)에서 Blob 입력 및 출력 바인딩을 보여 줍니다. 함수는 Blob의 복사본을 만듭니다. 함수는 복사할 Blob의 이름을 포함하는 큐 메시지에 의해 트리거됩니다. 새 Blob의 이름은 *{originalblobname}-복사입니다.*

*function.json* 파일에서 `queueTrigger` 메타데이터 속성은 `path` 속성에서 Blob 이름을 지정하는 데 사용됩니다.

```json
{
  "bindings": [
    {
      "queueName": "myqueue-items",
      "connection": "MyStorageConnectionAppSetting",
      "name": "queuemsg",
      "type": "queueTrigger",
      "direction": "in"
    },
    {
      "name": "inputblob",
      "type": "blob",
      "path": "samples-workitems/{queueTrigger}",
      "connection": "MyStorageConnectionAppSetting",
      "direction": "in"
    },
    {
      "name": "$return",
      "type": "blob",
      "path": "samples-workitems/{queueTrigger}-Copy",
      "connection": "MyStorageConnectionAppSetting",
      "direction": "out"
    }
  ],
  "disabled": false,
  "scriptFile": "__init__.py"
}
```

[구성](#configuration) 섹션에서는 이러한 속성을 설명합니다.

다음은 Python 코드입니다.

```python
import logging
import azure.functions as func


def main(queuemsg: func.QueueMessage, inputblob: func.InputStream) -> func.InputStream:
    logging.info('Python Queue trigger function processed %s', inputblob.name)
    return inputblob
```

# <a name="java"></a>[Java](#tab/java)

이 섹션에는 다음 예제가 포함되어 있습니다.

* [HTTP 트리거, 쿼리 문자열에서 Blob 이름 조회](#http-trigger-look-up-blob-name-from-query-string)
* [큐 트리거, 큐 메시지에서 Blob 이름 수신](#queue-trigger-receive-blob-name-from-queue-message)

#### <a name="http-trigger-look-up-blob-name-from-query-string"></a>HTTP 트리거, 쿼리 문자열에서 Blob 이름 조회

 다음 예제는 `HttpTrigger` 주석을 사용하여 Blob Storage 컨테이너에 있는 파일 이름을 포함하는 매개 변수를 수신하는 Java 함수를 보여 줍니다. 그런 다음, `BlobInput` 주석이 파일을 읽고 파일 내용을 함수에 `byte[]`로 전달합니다.

```java
  @FunctionName("getBlobSizeHttp")
  @StorageAccount("Storage_Account_Connection_String")
  public HttpResponseMessage blobSize(
    @HttpTrigger(name = "req", 
      methods = {HttpMethod.GET}, 
      authLevel = AuthorizationLevel.ANONYMOUS) 
    HttpRequestMessage<Optional<String>> request,
    @BlobInput(
      name = "file", 
      dataType = "binary", 
      path = "samples-workitems/{Query.file}") 
    byte[] content,
    final ExecutionContext context) {
      // build HTTP response with size of requested blob
      return request.createResponseBuilder(HttpStatus.OK)
        .body("The size of \"" + request.getQueryParameters().get("file") + "\" is: " + content.length + " bytes")
        .build();
  }
```

#### <a name="queue-trigger-receive-blob-name-from-queue-message"></a>큐 트리거, 큐 메시지에서 Blob 이름 수신

 다음 예제는 `QueueTrigger` 주석을 사용하여 Blob Storage 컨테이너에 있는 파일 이름을 포함하는 메시지를 수신하는 Java 함수를 보여 줍니다. 그런 다음, `BlobInput` 주석이 파일을 읽고 파일 내용을 함수에 `byte[]`로 전달합니다.

```java
  @FunctionName("getBlobSize")
  @StorageAccount("Storage_Account_Connection_String")
  public void blobSize(
    @QueueTrigger(
      name = "filename", 
      queueName = "myqueue-items-sample") 
    String filename,
    @BlobInput(
      name = "file", 
      dataType = "binary", 
      path = "samples-workitems/{queueTrigger}") 
    byte[] content,
    final ExecutionContext context) {
      context.getLogger().info("The size of \"" + filename + "\" is: " + content.length + " bytes");
  }
```

[Java 함수 런타임 라이브러리](/java/api/overview/azure/functions/runtime)에서 값이 Blob에서 제공되는 매개 변수에 대한 `@BlobInput` 주석을 사용합니다.  `Optional<T>`을 사용하여 원시 Java 형식, POJO 또는 null 허용 값으로 이 주석을 사용할 수 있습니다.

---

## <a name="attributes-and-annotations"></a>특성 및 주석

# <a name="c"></a>[C #](#tab/csharp)

[C# 클래스 라이브러리](functions-dotnet-class-library.md)에서 [BlobAttribute](https://github.com/Azure/azure-webjobs-sdk/blob/dev/src/Microsoft.Azure.WebJobs.Extensions.Storage/Blobs/BlobAttribute.cs)를 사용합니다.

특성의 생성자는 다음 예제에 표시된 대로 Blob에 대한 경로 및 읽기 또는 쓰기를 나타내는 `FileAccess` 매개 변수를 사용합니다.

```csharp
[FunctionName("BlobInput")]
public static void Run(
    [QueueTrigger("myqueue-items")] string myQueueItem,
    [Blob("samples-workitems/{queueTrigger}", FileAccess.Read)] Stream myBlob,
    ILogger log)
{
    log.LogInformation($"BlobInput processed blob\n Name:{myQueueItem} \n Size: {myBlob.Length} bytes");
}

```

다음 예와 같이 사용할 스토리지 계정을 지정하도록 `Connection` 속성을 설정할 수 있습니다.

```csharp
[FunctionName("BlobInput")]
public static void Run(
    [QueueTrigger("myqueue-items")] string myQueueItem,
    [Blob("samples-workitems/{queueTrigger}", FileAccess.Read, Connection = "StorageConnectionAppSetting")] Stream myBlob,
    ILogger log)
{
    log.LogInformation($"BlobInput processed blob\n Name:{myQueueItem} \n Size: {myBlob.Length} bytes");
}
```

`StorageAccount` 특성을 사용하여 클래스, 메서드 또는 매개 변수 수준에서 스토리지 계정을 지정합니다. 자세한 내용은 [트리거 - 특성 및 주석을](./functions-bindings-storage-blob-trigger.md#attributes-and-annotations)참조하십시오.

# <a name="c-script"></a>[C# 스크립트](#tab/csharp-script)

특성은 C# 스크립트에서 지원되지 않습니다.

# <a name="javascript"></a>[자바 스크립트](#tab/javascript)

속성은 자바 스크립트에서 지원되지 않습니다.

# <a name="python"></a>[Python](#tab/python)

특성은 파이썬에서 지원되지 않습니다.

# <a name="java"></a>[Java](#tab/java)

특성을 `@BlobInput` 사용하면 함수를 트리거한 Blob에 액세스할 수 있습니다. 특성이 있는 바이트 배열을 사용하는 `dataType` 경우 `binary`을 로 설정합니다. 자세한 내용은 [입력 예제를](#example) 참조하십시오.

---

## <a name="configuration"></a>Configuration

다음 표에서는 *function.json* 파일및 특성에서 설정한 바인딩 `Blob` 구성 속성에 대해 설명합니다.

|function.json 속성 | 특성 속성 |설명|
|---------|---------|----------------------|
|**종류** | 해당 없음 | `blob`로 설정해야 합니다. |
|**direction** | 해당 없음 | `in`로 설정해야 합니다. 예외는 [사용](#usage) 섹션에서 표시됩니다. |
|**(이름)** | 해당 없음 | 함수 코드에서 Blob을 나타내는 변수의 이름입니다.|
|**경로** |**BlobPath** | Blob에 대한 경로입니다. |
|**연결** |**연결**| 이 바인딩에 사용할 저장소 연결 문자열이 포함된 앱 설정의 [이름입니다.](../storage/common/storage-configure-connection-string.md) 앱 설정 이름이 "AzureWebJobs"로 시작하는 경우 여기에서 이름의 나머지만을 지정할 수 있습니다. 예를 들어 "MyStorage"로 설정하면 `connection` 함수 런타임에서 "AzureWebJobsMyStorage"라는 앱 설정을 찾습니다. `connection`을 비워 두면 함수 런타임 기능은 `AzureWebJobsStorage`라는 앱 설정에서 기본 스토리지 연결 문자열을 사용합니다.<br><br>연결 문자열은 [Blob 전용 스토리지 계정](../storage/common/storage-account-overview.md#types-of-storage-accounts)이 아닌 범용 스토리지 계정의 문자열이어야 합니다.|
|해당 없음 | **액세스** | 읽기 또는 쓰기를 나타냅니다. |

[!INCLUDE [app settings to local.settings.json](../../includes/functions-app-settings-local.md)]

## <a name="usage"></a>사용

# <a name="c"></a>[C #](#tab/csharp)

[!INCLUDE [functions-bindings-blob-storage-input-usage.md](../../includes/functions-bindings-blob-storage-input-usage.md)]

# <a name="c-script"></a>[C# 스크립트](#tab/csharp-script)

[!INCLUDE [functions-bindings-blob-storage-input-usage.md](../../includes/functions-bindings-blob-storage-input-usage.md)]

# <a name="javascript"></a>[자바 스크립트](#tab/javascript)

*function.json* `context.bindings.<NAME>` 에 `<NAME>` 정의된 값과 일치하는 위치를 사용하여 Blob 데이터에 액세스합니다.

# <a name="python"></a>[Python](#tab/python)

InputStream으로 입력된 매개 변수를 통해 Blob 데이터에 [액세스합니다.](https://docs.microsoft.com/python/api/azure-functions/azure.functions.inputstream?view=azure-python) 자세한 내용은 [입력 예제를](#example) 참조하십시오.

# <a name="java"></a>[Java](#tab/java)

특성을 `@BlobInput` 사용하면 함수를 트리거한 Blob에 액세스할 수 있습니다. 특성이 있는 바이트 배열을 사용하는 `dataType` 경우 `binary`을 로 설정합니다. 자세한 내용은 [입력 예제를](#example) 참조하십시오.

---

## <a name="next-steps"></a>다음 단계

- [Blob 저장소 데이터가 변경될 때 함수 실행](./functions-bindings-storage-blob-trigger.md)
- [함수에서 Blob 저장소 데이터 쓰기](./functions-bindings-storage-blob-output.md)
