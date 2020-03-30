---
title: Azure 코스모스 DB 함수에 대한 출력 바인딩 2.x
description: Azure 함수에서 Azure Cosmos DB 출력 바인딩을 사용하는 방법을 알아봅니다.
author: craigshoemaker
ms.topic: reference
ms.date: 02/24/2020
ms.author: cshoe
ms.openlocfilehash: 636903c20e07f11a2fd919654cfaa62037171f20
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79277766"
---
# <a name="azure-cosmos-db-output-binding-for-azure-functions-2x"></a>Azure 코스모스 DB 출력 바인딩용 Azure 함수 2.x

Azure Cosmos DB 출력 바인딩을 사용하면 Azure Cosmos DB 데이터베이스에 SQL API를 사용하여 새 문서를 작성할 수 있습니다.

설정 및 구성 세부 정보에 대한 자세한 내용은 [개요를](./functions-bindings-cosmosdb-v2.md)참조하십시오.

<a id="example" name="example"></a>

# <a name="c"></a>[C #](#tab/csharp)

이 섹션에는 다음 예제가 포함되어 있습니다.

* [큐 트리거, 하나의 문서 쓰기](#queue-trigger-write-one-doc-c)
* [큐 트리거, IAsyncCollector를 사용하여 문서 쓰기](#queue-trigger-write-docs-using-iasynccollector-c)

예제에서는 간단한 `ToDoItem` 형식을 참조하세요.

```cs
namespace CosmosDBSamplesV2
{
    public class ToDoItem
    {
        public string Id { get; set; }
        public string Description { get; set; }
    }
}
```

<a id="queue-trigger-write-one-doc-c"></a>

### <a name="queue-trigger-write-one-doc"></a>큐 트리거, 하나의 문서 쓰기

다음 예제에서는 Queue storage의 메시지에 제공된 데이터를 사용하여 문서를 데이터베이스에 추가하는 [C# 함수](functions-dotnet-class-library.md)를 보여 줍니다.

```cs
using Microsoft.Azure.WebJobs;
using Microsoft.Azure.WebJobs.Host;
using Microsoft.Extensions.Logging;
using System;

namespace CosmosDBSamplesV2
{
    public static class WriteOneDoc
    {
        [FunctionName("WriteOneDoc")]
        public static void Run(
            [QueueTrigger("todoqueueforwrite")] string queueMessage,
            [CosmosDB(
                databaseName: "ToDoItems",
                collectionName: "Items",
                ConnectionStringSetting = "CosmosDBConnection")]out dynamic document,
            ILogger log)
        {
            document = new { Description = queueMessage, id = Guid.NewGuid() };

            log.LogInformation($"C# Queue trigger function inserted one row");
            log.LogInformation($"Description={queueMessage}");
        }
    }
}
```

<a id="queue-trigger-write-docs-using-iasynccollector-c"></a>

### <a name="queue-trigger-write-docs-using-iasynccollector"></a>큐 트리거, IAsyncCollector를 사용하여 문서 쓰기

다음 예제에서는 큐 메시지 JSON에서 제공된 데이터를 사용하여 문서의 컬렉션을 데이터베이스에 추가하는 [C# 함수](functions-dotnet-class-library.md)를 보여줍니다.

```cs
using Microsoft.Azure.WebJobs;
using Microsoft.Azure.WebJobs.Host;
using System.Threading.Tasks;
using Microsoft.Extensions.Logging;

namespace CosmosDBSamplesV2
{
    public static class WriteDocsIAsyncCollector
    {
        [FunctionName("WriteDocsIAsyncCollector")]
        public static async Task Run(
            [QueueTrigger("todoqueueforwritemulti")] ToDoItem[] toDoItemsIn,
            [CosmosDB(
                databaseName: "ToDoItems",
                collectionName: "Items",
                ConnectionStringSetting = "CosmosDBConnection")]
                IAsyncCollector<ToDoItem> toDoItemsOut,
            ILogger log)
        {
            log.LogInformation($"C# Queue trigger function processed {toDoItemsIn?.Length} items");

            foreach (ToDoItem toDoItem in toDoItemsIn)
            {
                log.LogInformation($"Description={toDoItem.Description}");
                await toDoItemsOut.AddAsync(toDoItem);
            }
        }
    }
}
```

# <a name="c-script"></a>[C# 스크립트](#tab/csharp-script)

이 섹션에는 다음 예제가 포함되어 있습니다.

* [큐 트리거, 하나의 문서 쓰기](#queue-trigger-write-one-doc-c-script)
* [큐 트리거, IAsyncCollector를 사용하여 문서 쓰기](#queue-trigger-write-docs-using-iasynccollector-c-script)


<a id="queue-trigger-write-one-doc-c-script"></a>

### <a name="queue-trigger-write-one-doc"></a>큐 트리거, 하나의 문서 쓰기

다음 예제에서는 *function.json* 파일의 Azure Cosmos DB 출력 바인딩 및 해당 바인딩을 사용하는 [C# 스크립트 함수](functions-reference-csharp.md)를 보여 줍니다. 이 함수는 다음 형식으로 JSON을 수신하는 큐에 대한 큐 입력 바인딩을 사용합니다.

```json
{
    "name": "John Henry",
    "employeeId": "123456",
    "address": "A town nearby"
}
```

이 함수는 각 레코드에 대해 다음과 같은 형식의 Azure Cosmos DB 문서를 만듭니다.

```json
{
    "id": "John Henry-123456",
    "name": "John Henry",
    "employeeId": "123456",
    "address": "A town nearby"
}
```

*function.json* 파일의 바인딩 데이터는 다음과 같습니다.

```json
{
    "name": "employeeDocument",
    "type": "cosmosDB",
    "databaseName": "MyDatabase",
    "collectionName": "MyCollection",
    "createIfNotExists": true,
    "connectionStringSetting": "MyAccount_COSMOSDB",
    "direction": "out"
}
```

[구성](#configuration) 섹션에서는 이러한 속성을 설명합니다.

C# 스크립트 코드는 다음과 같습니다.

```cs
    #r "Newtonsoft.Json"

    using Microsoft.Azure.WebJobs.Host;
    using Newtonsoft.Json.Linq;
    using Microsoft.Extensions.Logging;

    public static void Run(string myQueueItem, out object employeeDocument, ILogger log)
    {
      log.LogInformation($"C# Queue trigger function processed: {myQueueItem}");

      dynamic employee = JObject.Parse(myQueueItem);

      employeeDocument = new {
        id = employee.name + "-" + employee.employeeId,
        name = employee.name,
        employeeId = employee.employeeId,
        address = employee.address
      };
    }
```

<a id="queue-trigger-write-docs-using-iasynccollector-c-script"></a>

### <a name="queue-trigger-write-docs-using-iasynccollector"></a>큐 트리거, IAsyncCollector를 사용하여 문서 쓰기

여러 문서를 만들려면 `ICollector<T>` 또는 `IAsyncCollector<T>`에 바인딩할 수 있으며, 여기서 `T`는 지원되는 형식 중 하나입니다.

이 예제에서는 간단한 `ToDoItem` 형식을 참조합니다.

```cs
namespace CosmosDBSamplesV2
{
    public class ToDoItem
    {
        public string Id { get; set; }
        public string Description { get; set; }
    }
}
```

function.json 파일은 다음과 같습니다.

```json
{
  "bindings": [
    {
      "name": "toDoItemsIn",
      "type": "queueTrigger",
      "direction": "in",
      "queueName": "todoqueueforwritemulti",
      "connectionStringSetting": "AzureWebJobsStorage"
    },
    {
      "type": "cosmosDB",
      "name": "toDoItemsOut",
      "databaseName": "ToDoItems",
      "collectionName": "Items",
      "connectionStringSetting": "CosmosDBConnection",
      "direction": "out"
    }
  ],
  "disabled": false
}
```

C# 스크립트 코드는 다음과 같습니다.

```cs
using System;
using Microsoft.Extensions.Logging;

public static async Task Run(ToDoItem[] toDoItemsIn, IAsyncCollector<ToDoItem> toDoItemsOut, ILogger log)
{
    log.LogInformation($"C# Queue trigger function processed {toDoItemsIn?.Length} items");

    foreach (ToDoItem toDoItem in toDoItemsIn)
    {
        log.LogInformation($"Description={toDoItem.Description}");
        await toDoItemsOut.AddAsync(toDoItem);
    }
}
```

# <a name="javascript"></a>[자바 스크립트](#tab/javascript)

다음 예제에서는 *function.json* 파일의 Azure Cosmos DB 출력 바인딩 및 해당 바인딩을 사용하는 [JavaScript 함수](functions-reference-node.md)를 보여 줍니다. 이 함수는 다음 형식으로 JSON을 수신하는 큐에 대한 큐 입력 바인딩을 사용합니다.

```json
{
    "name": "John Henry",
    "employeeId": "123456",
    "address": "A town nearby"
}
```

이 함수는 각 레코드에 대해 다음과 같은 형식의 Azure Cosmos DB 문서를 만듭니다.

```json
{
    "id": "John Henry-123456",
    "name": "John Henry",
    "employeeId": "123456",
    "address": "A town nearby"
}
```

*function.json* 파일의 바인딩 데이터는 다음과 같습니다.

```json
{
    "name": "employeeDocument",
    "type": "cosmosDB",
    "databaseName": "MyDatabase",
    "collectionName": "MyCollection",
    "createIfNotExists": true,
    "connectionStringSetting": "MyAccount_COSMOSDB",
    "direction": "out"
}
```

[구성](#configuration) 섹션에서는 이러한 속성을 설명합니다.

JavaScript 코드는 다음과 같습니다.

```javascript
    module.exports = function (context) {

      context.bindings.employeeDocument = JSON.stringify({
        id: context.bindings.myQueueItem.name + "-" + context.bindings.myQueueItem.employeeId,
        name: context.bindings.myQueueItem.name,
        employeeId: context.bindings.myQueueItem.employeeId,
        address: context.bindings.myQueueItem.address
      });

      context.done();
    };
```

# <a name="python"></a>[Python](#tab/python)

다음 예제에서는 Azure CosmosDB 데이터베이스에 함수의 출력으로 문서를 작성하는 방법을 보여 줍니다.

바인딩 정의는 *type이* 로 설정된 `cosmosDB` *function.json에서* 정의됩니다.

```json
{
  "scriptFile": "__init__.py",
  "bindings": [
    {
      "authLevel": "function",
      "type": "httpTrigger",
      "direction": "in",
      "name": "req",
      "methods": [
        "get",
        "post"
      ]
    },
    {
      "type": "cosmosDB",
      "direction": "out",
      "name": "doc",
      "databaseName": "demodb",
      "collectionName": "data",
      "createIfNotExists": "true",
      "connectionStringSetting": "AzureCosmosDBConnectionString"
    },
    {
      "type": "http",
      "direction": "out",
      "name": "$return"
    }
  ]
}
```

데이터베이스에 쓰려면 문서 개체를 데이터베이스 `set` 매개 변수의 메서드에 전달합니다.

```python
import azure.functions as func

def main(req: func.HttpRequest, doc: func.Out[func.Document]) -> func.HttpResponse:

    request_body = req.get_body()

    doc.set(func.Document.from_json(request_body))

    return 'OK'
```

# <a name="java"></a>[Java](#tab/java)

* [큐 트리거, 반환 값을 통해 데이터베이스에 메시지 저장](#queue-trigger-save-message-to-database-via-return-value-java)
* [HTTP 트리거, 반환 값을 통해 데이터베이스에 단일 문서 저장](#http-trigger-save-one-document-to-database-via-return-value-java)
* [HTTP 트리거, OutputBinding을 통해 데이터베이스에 단일 문서 저장](#http-trigger-save-one-document-to-database-via-outputbinding-java)
* [HTTP 트리거, OutputBinding을 통해 데이터베이스에 여러 문서 저장](#http-trigger-save-multiple-documents-to-database-via-outputbinding-java)


<a id="queue-trigger-save-message-to-database-via-return-value-java"></a>

### <a name="queue-trigger-save-message-to-database-via-return-value"></a>큐 트리거, 반환 값을 통해 데이터베이스에 메시지 저장

다음 예제에서는 Queue Storage의 메시지에서 데이터를 사용하여 문서를 데이터베이스에 추가하는 Java 함수를 보여줍니다.

```java
@FunctionName("getItem")
@CosmosDBOutput(name = "database",
  databaseName = "ToDoList",
  collectionName = "Items",
  connectionStringSetting = "AzureCosmosDBConnection")
public String cosmosDbQueryById(
    @QueueTrigger(name = "msg",
      queueName = "myqueue-items",
      connection = "AzureWebJobsStorage")
    String message,
    final ExecutionContext context)  {
     return "{ id: \"" + System.currentTimeMillis() + "\", Description: " + message + " }";
   }
```
<a id="http-trigger-save-one-document-to-database-via-return-value-java"></a>

#### <a name="http-trigger-save-one-document-to-database-via-return-value"></a>HTTP 트리거, 반환 값을 통해 데이터베이스에 단일 문서 저장

다음 예제에서는 시그니처에 ```@CosmosDBOutput```이 주석으로 지정되고 ```String``` 형식의 반환 값을 갖는 Java 함수를 보여줍니다. 함수에서 반환된 JSON 문서는 해당 CosmosDB 컬렉션에 자동으로 기록됩니다.

```java
    @FunctionName("WriteOneDoc")
    @CosmosDBOutput(name = "database",
      databaseName = "ToDoList",
      collectionName = "Items",
      connectionStringSetting = "Cosmos_DB_Connection_String")
    public String run(
            @HttpTrigger(name = "req",
              methods = {HttpMethod.GET, HttpMethod.POST},
              authLevel = AuthorizationLevel.ANONYMOUS)
            HttpRequestMessage<Optional<String>> request,
            final ExecutionContext context) {

        // Item list
        context.getLogger().info("Parameters are: " + request.getQueryParameters());

        // Parse query parameter
        String query = request.getQueryParameters().get("desc");
        String name = request.getBody().orElse(query);

        // Generate random ID
        final int id = Math.abs(new Random().nextInt());

        // Generate document
        final String jsonDocument = "{\"id\":\"" + id + "\", " +
                                    "\"description\": \"" + name + "\"}";

        context.getLogger().info("Document to be saved: " + jsonDocument);

        return jsonDocument;
    }
```

<a id="http-trigger-save-one-document-to-database-via-outputbinding-java"></a>

### <a name="http-trigger-save-one-document-to-database-via-outputbinding"></a>HTTP 트리거, OutputBinding을 통해 데이터베이스에 단일 문서 저장

다음 예제에서는 ```OutputBinding<T>``` 출력 매개 변수를 통해 CosmosDB에 문서를 쓰는 Java 함수를 보여줍니다. 이 예제에서는 ```outputItem``` 매개 변수에 함수 시그니처가 ```@CosmosDBOutput```아닌 에 추가되어야 합니다. ```OutputBinding<T>```을 사용하면 JSON 또는 XML 문서와 같은 함수 호출자에 다른 값을 반환하도록 허용하면서 함수가 바인딩을 활용하여 CosmosDB에 문서를 쓸 수 있습니다.

```java
    @FunctionName("WriteOneDocOutputBinding")
    public HttpResponseMessage run(
            @HttpTrigger(name = "req",
              methods = {HttpMethod.GET, HttpMethod.POST},
              authLevel = AuthorizationLevel.ANONYMOUS)
            HttpRequestMessage<Optional<String>> request,
            @CosmosDBOutput(name = "database",
              databaseName = "ToDoList",
              collectionName = "Items",
              connectionStringSetting = "Cosmos_DB_Connection_String")
            OutputBinding<String> outputItem,
            final ExecutionContext context) {

        // Parse query parameter
        String query = request.getQueryParameters().get("desc");
        String name = request.getBody().orElse(query);

        // Item list
        context.getLogger().info("Parameters are: " + request.getQueryParameters());

        // Generate random ID
        final int id = Math.abs(new Random().nextInt());

        // Generate document
        final String jsonDocument = "{\"id\":\"" + id + "\", " +
                                    "\"description\": \"" + name + "\"}";

        context.getLogger().info("Document to be saved: " + jsonDocument);

        // Set outputItem's value to the JSON document to be saved
        outputItem.setValue(jsonDocument);

        // return a different document to the browser or calling client.
        return request.createResponseBuilder(HttpStatus.OK)
                      .body("Document created successfully.")
                      .build();
    }
```

<a id="http-trigger-save-multiple-documents-to-database-via-outputbinding-java"></a>

### <a name="http-trigger-save-multiple-documents-to-database-via-outputbinding"></a>HTTP 트리거, OutputBinding을 통해 데이터베이스에 여러 문서 저장

다음 예제에서는 ```OutputBinding<T>``` 출력 매개 변수를 통해 CosmosDB에 여러 문서를 쓰는 Java 함수를 보여줍니다. 이 예제에서는 ```outputItem``` 매개 변수에 함수 ```@CosmosDBOutput```서명이 아닌 "로 추가됩니다. 출력 매개 변수 ```outputItem```에는 ```ToDoItem``` 개체 목록이 템플릿 매개 변수 형식으로 포함되어 있습니다. ```OutputBinding<T>```을 사용하면 JSON 또는 XML 문서와 같은 함수 호출자에 다른 값을 반환하도록 허용하면서 함수가 바인딩을 활용하여 CosmosDB에 문서를 쓸 수 있습니다.

```java
    @FunctionName("WriteMultipleDocsOutputBinding")
    public HttpResponseMessage run(
            @HttpTrigger(name = "req",
              methods = {HttpMethod.GET, HttpMethod.POST},
              authLevel = AuthorizationLevel.ANONYMOUS)
            HttpRequestMessage<Optional<String>> request,
            @CosmosDBOutput(name = "database",
              databaseName = "ToDoList",
              collectionName = "Items",
              connectionStringSetting = "Cosmos_DB_Connection_String")
            OutputBinding<List<ToDoItem>> outputItem,
            final ExecutionContext context) {

        // Parse query parameter
        String query = request.getQueryParameters().get("desc");
        String name = request.getBody().orElse(query);

        // Item list
        context.getLogger().info("Parameters are: " + request.getQueryParameters());

        // Generate documents
        List<ToDoItem> items = new ArrayList<>();

        for (int i = 0; i < 5; i ++) {
          // Generate random ID
          final int id = Math.abs(new Random().nextInt());

          // Create ToDoItem
          ToDoItem item = new ToDoItem(String.valueOf(id), name);

          items.add(item);
        }

        // Set outputItem's value to the list of POJOs to be saved
        outputItem.setValue(items);
        context.getLogger().info("Document to be saved: " + items);

        // return a different document to the browser or calling client.
        return request.createResponseBuilder(HttpStatus.OK)
                      .body("Documents created successfully.")
                      .build();
    }
```

[Java 함수 런타임 라이브러리](/java/api/overview/azure/functions/runtime)에서 Cosmos DB에 작성될 매개 변수에 대한 `@CosmosDBOutput` 주석을 사용합니다.  주석 매개 변수 형식은 ```OutputBinding<T>```이어야 합니다. 여기서 T는 원시 Java 형식 또는 POJO입니다.

---

## <a name="attributes-and-annotations"></a>특성 및 주석

# <a name="c"></a>[C #](#tab/csharp)

[C# 클래스 라이브러리](functions-dotnet-class-library.md)에서 [CosmosDB](https://github.com/Azure/azure-webjobs-sdk-extensions/blob/v2.x/master/WebJobs.Extensions.CosmosDB/CosmosDBAttribute.cs) 특성을 사용합니다.

특성의 생성자는 데이터베이스 이름과 컬렉션 이름을 사용합니다. 이러한 설정 및 구성할 수 있는 다른 속성에 대한 자세한 내용은 [출력 - 구성](#configuration)을 참조하세요. 다음은 메서드 서명의 `CosmosDB` 특성 예제입니다.

```csharp
    [FunctionName("QueueToDocDB")]
    public static void Run(
        [QueueTrigger("myqueue-items", Connection = "AzureWebJobsStorage")] string myQueueItem,
        [CosmosDB("ToDoList", "Items", Id = "id", ConnectionStringSetting = "myCosmosDB")] out dynamic document)
    {
        ...
    }
```

# <a name="c-script"></a>[C# 스크립트](#tab/csharp-script)

특성은 C# 스크립트에서 지원되지 않습니다.

# <a name="javascript"></a>[자바 스크립트](#tab/javascript)

속성은 자바 스크립트에서 지원되지 않습니다.

# <a name="python"></a>[Python](#tab/python)

특성은 파이썬에서 지원되지 않습니다.

# <a name="java"></a>[Java](#tab/java)

`CosmosDBOutput` 이 비추가 Cosmos DB에 데이터를 쓸 수 있습니다. 함수 또는 개별 함수 매개 변수에 추가를 적용할 수 있습니다. 함수 메서드에서 사용할 경우 함수의 반환 값은 Cosmos DB에 기록된 값입니다. 매개 변수와 함께 추가를 사용하는 경우 매개 변수의 형식은 `OutputBinding<T>` `T` 네이티브 Java 형식 또는 POJO로 선언되어야 합니다.

---

## <a name="configuration"></a>Configuration

다음 표에서는 *function.json* 파일및 특성에서 설정한 바인딩 `CosmosDB` 구성 속성에 대해 설명합니다.

|function.json 속성 | 특성 속성 |설명|
|---------|---------|----------------------|
|**종류**     | 해당 없음 | `cosmosDB`로 설정해야 합니다.        |
|**direction**     | 해당 없음 | `out`로 설정해야 합니다.         |
|**(이름)**     | 해당 없음 | 함수에서 문서를 나타내는 바인딩 매개 변수의 이름입니다.  |
|**databaseName** | **Databasename**|문서가 만들어진 컬렉션을 포함하는 데이터베이스입니다.     |
|**컬렉션이름** |**CollectionName**  | 문서가 만들어진 컬렉션의 이름입니다. |
|**createIfNotExists**  |**CreateIfNotExists**    | 컬렉션이 존재하지 않는 경우 만들 수 있는지 여부를 나타내는 부울 값입니다. 새 컬렉션이 예약된 처리량으로 만들어져 비용이 부과되기 기본값은 *false*입니다. 자세한 내용은 가격 [책정 페이지를](https://azure.microsoft.com/pricing/details/cosmos-db/)참조하십시오.  |
|**파티션키**|**PartitionKey** |true이면 `CreateIfNotExists` 생성된 컬렉션의 파티션 키 경로를 정의합니다.|
|**컬렉션관통**|**CollectionThroughput**| true이면 `CreateIfNotExists` 생성된 컬렉션의 [처리량을](../cosmos-db/set-throughput.md) 정의합니다.|
|**연결문자열 설정**    |**ConnectionStringSetting** |Azure Cosmos DB 연결 문자열을 포함하는 앱 설정의 이름입니다.        |
|**기본 설정위치**| **기본 설정 위치**| (선택 사항) Azure Cosmos DB 서비스에서 지리적으로 복제된 데이터베이스 계정에 대한 기본 위치(영역)를 정의합니다. 값은 쉼표로 구분되어야 합니다. 예를 들어 "미국 동부, 미국 중남부, 북유럽". |
|**사용다중쓰기위치**| **사용다중쓰기위치**| (선택 사항) 와 `PreferredLocations`함께 `true` 설정하면 Azure Cosmos DB 서비스에서 [다중 지역 쓰기를](../cosmos-db/how-to-manage-database-account.md#configure-multiple-write-regions) 활용할 수 있습니다. |

[!INCLUDE [app settings to local.settings.json](../../includes/functions-app-settings-local.md)]

## <a name="usage"></a>사용

기본적으로 함수에서 출력 매개 변수에 쓸 경우 데이터베이스에서 문서가 생성됩니다. 이 문서에는 자동으로 생성된 GUID가 문서 ID로 지정되어 있습니다. 출력 매개 변수에 전달되는 JSON 개체에 `id` 속성을 지정하여 출력 문서의 문서 ID를 지정할 수 있습니다.

> [!Note]
> 기존 문서의 ID를 지정하면 새 출력 문서에 의해 덮어쓰여집니다.

## <a name="exceptions-and-return-codes"></a>예외 및 반환 코드

| 바인딩 | 참고 |
|---|---|
| CosmosDB | [CosmosDB 오류 코드](https://docs.microsoft.com/rest/api/cosmos-db/http-status-codes-for-cosmosdb) |

<a name="host-json"></a>

## <a name="hostjson-settings"></a>host.json 설정

이 섹션에서는 버전 2.x에서 이 바인딩에 사용할 수 있는 글로벌 구성 설정을 설명합니다. 버전 2.x의 글로벌 구성 설정에 대한 자세한 내용은 [Azure Functions 버전 2.x에 대한 host.json 참조](functions-host-json.md)를 참조하세요.

```json
{
    "version": "2.0",
    "extensions": {
        "cosmosDB": {
            "connectionMode": "Gateway",
            "protocol": "Https",
            "leaseOptions": {
                "leasePrefix": "prefix1"
            }
        }
    }
}
```

|속성  |기본값 | 설명 |
|---------|---------|---------|
|GatewayMode|게이트웨이|Azure Cosmos DB 서비스에 연결할 때 해당 함수에 의해 사용되는 연결 모드입니다. 옵션은 `Direct` 및 `Gateway`입니다.|
|프로토콜|Https|Azure Cosmos DB 서비스에 연결할 때 해당 함수에 의해 사용되는 연결 프로토콜입니다.  [두 모드에 대한 설명은 여기](../cosmos-db/performance-tips.md#networking)를 참조하세요.|
|leasePrefix|해당 없음|앱의 모든 함수에서 사용할 접두사를 임대합니다.|

## <a name="next-steps"></a>다음 단계

- [Azure Cosmos DB 문서를 만들거나 수정할 때 함수실행(트리거)](./functions-bindings-cosmosdb-v2-trigger.md)
- [Azure 코스모스 DB 문서 읽기(입력 바인딩)](./functions-bindings-cosmosdb-v2-input.md)