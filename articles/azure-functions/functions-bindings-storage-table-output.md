---
title: Azure Functions에 대 한 Azure Table storage 출력 바인딩
description: Azure Functions에서 Azure Table storage 출력 바인딩을 사용 하는 방법을 이해 합니다.
author: craigshoemaker
ms.topic: reference
ms.date: 09/03/2018
ms.author: cshoe
ms.custom: devx-track-csharp, devx-track-python
ms.openlocfilehash: ec857db64529a27db7412c61f8f09c66f8a76363
ms.sourcegitcommit: 93329b2fcdb9b4091dbd632ee031801f74beb05b
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/15/2020
ms.locfileid: "92098226"
---
# <a name="azure-table-storage-output-bindings-for-azure-functions"></a>Azure Functions에 대 한 Azure Table storage 출력 바인딩

Azure Table Storage 출력 바인딩을 사용하여 Azure Storage 계정에서 테이블에 엔터티를 쓸 수 있습니다.

> [!NOTE]
> 이 출력 바인딩은 기존 엔터티 업데이트를 지원하지 않습니다. [Azure Storage SDK](../cosmos-db/tutorial-develop-table-dotnet.md#delete-an-entity)에서 `TableOperation.Replace` 작업을 사용하여 기존 엔터티를 업데이트합니다.

## <a name="example"></a>예제

# <a name="c"></a>[C#](#tab/csharp)

다음 예제에서는 단일 테이블 행을 쓰기 위해 HTTP 트리거를 사용하는 [C# 함수](functions-dotnet-class-library.md)를 보여줍니다.

```csharp
public class TableStorage
{
    public class MyPoco
    {
        public string PartitionKey { get; set; }
        public string RowKey { get; set; }
        public string Text { get; set; }
    }

    [FunctionName("TableOutput")]
    [return: Table("MyTable")]
    public static MyPoco TableOutput([HttpTrigger] dynamic input, ILogger log)
    {
        log.LogInformation($"C# http trigger function processed: {input.Text}");
        return new MyPoco { PartitionKey = "Http", RowKey = Guid.NewGuid().ToString(), Text = input.Text };
    }
}
```

# <a name="c-script"></a>[C# Script](#tab/csharp-script)

다음 예에서는 바인딩을 사용하는 *function.json* 파일 및 [C# 스크립트](functions-reference-csharp.md) 코드에서 테이블 출력 바인딩을 보여줍니다. 함수는 여러 테이블 엔터티를 씁니다.

*function.json* 파일은 다음과 같습니다.

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
      "connection": "MyStorageConnectionAppSetting",
      "name": "tableBinding",
      "type": "table",
      "direction": "out"
    }
  ],
  "disabled": false
}
```

[구성](#configuration) 섹션에서는 이러한 속성을 설명합니다.

C# 스크립트 코드는 다음과 같습니다.

```csharp
public static void Run(string input, ICollector<Person> tableBinding, ILogger log)
{
    for (int i = 1; i < 10; i++)
        {
            log.LogInformation($"Adding Person entity {i}");
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

# <a name="javascript"></a>[JavaScript](#tab/javascript)

다음 예에서는 바인딩을 사용하는 *function.json* 파일 및 [JavaScript 함수](functions-reference-node.md)에서 테이블 출력 바인딩을 보여줍니다. 함수는 여러 테이블 엔터티를 씁니다.

*function.json* 파일은 다음과 같습니다.

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
      "connection": "MyStorageConnectionAppSetting",
      "name": "tableBinding",
      "type": "table",
      "direction": "out"
    }
  ],
  "disabled": false
}
```

[구성](#configuration) 섹션에서는 이러한 속성을 설명합니다.

JavaScript 코드는 다음과 같습니다.

```javascript
module.exports = function (context) {

    context.bindings.tableBinding = [];

    for (var i = 1; i < 10; i++) {
        context.bindings.tableBinding.push({
            PartitionKey: "Test",
            RowKey: i.toString(),
            Name: "Name " + i
        });
    }

    context.done();
};
```

# <a name="python"></a>[Python](#tab/python)

다음 예에서는 Table storage 출력 바인딩을 사용 하는 방법을 보여 줍니다. `table`바인딩은,, 및에 값을 할당 하 여 *function.js* 에서 구성 됩니다 `name` `tableName` `partitionKey` `connection` .

```json
{
  "scriptFile": "__init__.py",
  "bindings": [
    {
      "name": "message",
      "type": "table",
      "tableName": "messages",
      "partitionKey": "message",
      "connection": "AzureWebJobsStorage",
      "direction": "out"
    },
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
      "type": "http",
      "direction": "out",
      "name": "$return"
    }
  ]
}
```

다음 함수는 값에 대 한 고유 UUI를 생성 `rowKey` 하 고 테이블 저장소에 메시지를 유지 합니다.

```python
import logging
import uuid
import json

import azure.functions as func

def main(req: func.HttpRequest, message: func.Out[str]) -> func.HttpResponse:

    rowKey = str(uuid.uuid4())

    data = {
        "Name": "Output binding message",
        "PartitionKey": "message",
        "RowKey": rowKey
    }

    message.set(json.dumps(data))

    return func.HttpResponse(f"Message created with the rowKey: {rowKey}")
```

# <a name="java"></a>[Java](#tab/java)

다음 예제에서는 단일 테이블 행을 쓰기 위해 HTTP 트리거를 사용 하는 Java 함수를 보여 줍니다.

```java
public class Person {
    private String PartitionKey;
    private String RowKey;
    private String Name;

    public String getPartitionKey() {return this.PartitionKey;}
    public void setPartitionKey(String key) {this.PartitionKey = key; }
    public String getRowKey() {return this.RowKey;}
    public void setRowKey(String key) {this.RowKey = key; }
    public String getName() {return this.Name;}
    public void setName(String name) {this.Name = name; }
}

public class AddPerson {

    @FunctionName("addPerson")
    public HttpResponseMessage get(
            @HttpTrigger(name = "postPerson", methods = {HttpMethod.POST}, authLevel = AuthorizationLevel.FUNCTION, route="persons/{partitionKey}/{rowKey}") HttpRequestMessage<Optional<Person>> request,
            @BindingName("partitionKey") String partitionKey,
            @BindingName("rowKey") String rowKey,
            @TableOutput(name="person", partitionKey="{partitionKey}", rowKey = "{rowKey}", tableName="%MyTableName%", connection="MyConnectionString") OutputBinding<Person> person,
            final ExecutionContext context) {

        Person outPerson = new Person();
        outPerson.setPartitionKey(partitionKey);
        outPerson.setRowKey(rowKey);
        outPerson.setName(request.getBody().get().getName());

        person.setValue(outPerson);

        return request.createResponseBuilder(HttpStatus.OK)
                        .header("Content-Type", "application/json")
                        .body(outPerson)
                        .build();
    }
}
```

다음 예제에서는 여러 테이블 행을 쓰기 위해 HTTP 트리거를 사용 하는 Java 함수를 보여 줍니다.

```java
public class Person {
    private String PartitionKey;
    private String RowKey;
    private String Name;

    public String getPartitionKey() {return this.PartitionKey;}
    public void setPartitionKey(String key) {this.PartitionKey = key; }
    public String getRowKey() {return this.RowKey;}
    public void setRowKey(String key) {this.RowKey = key; }
    public String getName() {return this.Name;}
    public void setName(String name) {this.Name = name; }
}

public class AddPersons {

    @FunctionName("addPersons")
    public HttpResponseMessage get(
            @HttpTrigger(name = "postPersons", methods = {HttpMethod.POST}, authLevel = AuthorizationLevel.FUNCTION, route="persons/") HttpRequestMessage<Optional<Person[]>> request,
            @TableOutput(name="person", tableName="%MyTableName%", connection="MyConnectionString") OutputBinding<Person[]> persons,
            final ExecutionContext context) {

        persons.setValue(request.getBody().get());

        return request.createResponseBuilder(HttpStatus.OK)
                        .header("Content-Type", "application/json")
                        .body(request.getBody().get())
                        .build();
    }
}
```

---

## <a name="attributes-and-annotations"></a>특성 및 주석

# <a name="c"></a>[C#](#tab/csharp)

[C# 클래스 라이브러리](functions-dotnet-class-library.md)에서 [TableAttribute](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs.Extensions.Storage/Tables/TableAttribute.cs)를 사용합니다.

특성의 생성자는 테이블 이름을 사용합니다. 특성은 `out` 다음 예제에 표시 된 것 처럼 매개 변수 또는 함수의 반환 값에 사용할 수 있습니다.

```csharp
[FunctionName("TableOutput")]
[return: Table("MyTable")]
public static MyPoco TableOutput(
    [HttpTrigger] dynamic input, 
    ILogger log)
{
    ...
}
```

다음 예와 같이 사용할 스토리지 계정을 지정하도록 `Connection` 속성을 설정할 수 있습니다.

```csharp
[FunctionName("TableOutput")]
[return: Table("MyTable", Connection = "StorageConnectionAppSetting")]
public static MyPoco TableOutput(
    [HttpTrigger] dynamic input, 
    ILogger log)
{
    ...
}
```

전체 예제는 [c # 예제](#example)를 참조 하세요.

`StorageAccount` 특성을 사용하여 클래스, 메서드 또는 매개 변수 수준에서 스토리지 계정을 지정합니다. 자세한 내용은 [입력 - 특성](./functions-bindings-storage-table-input.md#attributes-and-annotations)을 참조하세요.

# <a name="c-script"></a>[C# Script](#tab/csharp-script)

C# 스크립트에서는 특성을 지원하지 않습니다.

# <a name="javascript"></a>[JavaScript](#tab/javascript)

JavaScript에서는 특성을 지원하지 않습니다.

# <a name="python"></a>[Python](#tab/python)

Python에서는 특성을 지원하지 않습니다.

# <a name="java"></a>[Java](#tab/java)

[Java 함수 런타임 라이브러리](/java/api/overview/azure/functions/runtime)에서 매개 변수에 대 한 [tableoutput](https://github.com/Azure/azure-functions-java-library/blob/master/src/main/java/com/microsoft/azure/functions/annotation/TableOutput.java/) 주석을 사용 하 여 테이블 저장소에 값을 씁니다.

자세한 내용은 [예제](#example)를 참조 하세요.

---

## <a name="configuration"></a>구성

다음 표에서는 *function.json* 파일 및 `Table` 특성에 설정된 바인딩 구성 속성을 설명합니다.

|function.json 속성 | 특성 속성 |Description|
|---------|---------|----------------------|
|**type** | 해당 없음 | `table`로 설정해야 합니다. 이 속성은 사용자가 Azure Portal에서 바인딩을 만들 때 자동으로 설정됩니다.|
|**direction** | 해당 없음 | `out`로 설정해야 합니다. 이 속성은 사용자가 Azure Portal에서 바인딩을 만들 때 자동으로 설정됩니다. |
|**name** | 해당 없음 | 테이블 또는 엔터티를 나타내는 함수 코드에서 사용되는 변수 이름입니다. `$return`으로 설정하여 함수 반환 값을 참조합니다.| 
|**tableName** |**TableName** | 테이블의 이름입니다.| 
|**partitionKey** |**PartitionKey** | 쓸 테이블 엔터티의 파티션 키입니다. 이 속성을 사용 하는 방법에 대 한 지침은 [사용 섹션](#usage) 을 참조 하세요.| 
|**rowKey** |**RowKey** | 쓸 테이블 엔터티의 행 키입니다. 이 속성을 사용 하는 방법에 대 한 지침은 [사용 섹션](#usage) 을 참조 하세요.| 
|**connection** |**연결** | 이 바인딩에 사용할 스토리지 연결 문자열을 포함하는 앱 설정의 이름입니다. 앱 설정 이름이 "AzureWebJobs"로 시작하는 경우 여기에서 이름의 나머지만을 지정할 수 있습니다. 예를 들어를 `connection` "MyStorage"로 설정 하는 경우 함수 런타임은 "MyStorage" 라는 앱 설정을 찾습니다. `connection`을 비워 두면 함수 런타임 기능은 `AzureWebJobsStorage`라는 앱 설정에서 기본 스토리지 연결 문자열을 사용합니다.|

[!INCLUDE [app settings to local.settings.json](../../includes/functions-app-settings-local.md)]

## <a name="usage"></a>사용

# <a name="c"></a>[C#](#tab/csharp)

메서드 매개 변수를 사용 하거나에 및 속성을 포함 하 여 출력 테이블 엔터티에 액세스 `ICollector<T> paramName` `IAsyncCollector<T> paramName` `T` `PartitionKey` `RowKey` 합니다. 이러한 속성에는를 구현 하거나 상속 하는 경우가 많습니다 `ITableEntity` `TableEntity` .

또는 `CloudTable` AZURE STORAGE SDK를 사용 하 여 메서드 매개 변수를 사용 하 여 테이블에 쓸 수 있습니다. `CloudTable`에 바인딩하려고 하면 오류 메시지가 표시되는 경우 [올바른 Storage SDK 버전](./functions-bindings-storage-table.md#azure-storage-sdk-version-in-functions-1x)에 대한 참조가 있는지 확인합니다.

# <a name="c-script"></a>[C# Script](#tab/csharp-script)

메서드 매개 변수를 사용 하거나에 및 속성을 포함 하 여 출력 테이블 엔터티에 액세스 `ICollector<T> paramName` `IAsyncCollector<T> paramName` `T` `PartitionKey` `RowKey` 합니다. 이러한 속성에는를 구현 하거나 상속 하는 경우가 많습니다 `ITableEntity` `TableEntity` . `paramName`값은 `name` *function.js*의 속성에 지정 됩니다.

또는 `CloudTable` AZURE STORAGE SDK를 사용 하 여 메서드 매개 변수를 사용 하 여 테이블에 쓸 수 있습니다. `CloudTable`에 바인딩하려고 하면 오류 메시지가 표시되는 경우 [올바른 Storage SDK 버전](./functions-bindings-storage-table.md#azure-storage-sdk-version-in-functions-1x)에 대한 참조가 있는지 확인합니다.

# <a name="javascript"></a>[JavaScript](#tab/javascript)

`context.bindings.<name>`을 사용하여 출력 이벤트에 액세스합니다. 여기서 `<name>`은 *function.json*의 `name` 속성에 지정된 값입니다.

# <a name="python"></a>[Python](#tab/python)

함수에서 테이블 저장소 행 메시지를 출력 하는 두 가지 옵션은 다음과 같습니다.

- **반환 값**: *function.json*의 `name` 속성을 `$return`으로 설정합니다. 이 구성을 사용 하면 함수의 반환 값이 테이블 저장소 행으로 유지 됩니다.

- **명령형**: [출력](/python/api/azure-functions/azure.functions.out?view=azure-python&preserve-view=true) 형식으로 선언된 매개 변수의 [set](/python/api/azure-functions/azure.functions.out?view=azure-python&preserve-view=true#set-val--t-----none) 메서드에 값을 전달합니다. `set`에 전달되는 값은 Event Hub 메시지로 유지됩니다.

# <a name="java"></a>[Java](#tab/java)

[Tablestorageoutput](/java/api/com.microsoft.azure.functions.annotation.tableoutput?view=azure-java-stablet&preserve-view=true) 주석을 사용 하 여 함수에서 테이블 저장소 행을 출력 하는 두 가지 옵션이 있습니다.

- **반환 값**: 함수 자체에 주석을 적용 하면 함수의 반환 값이 테이블 저장소 행으로 유지 됩니다.

- **명령적**: 메시지 값을 명시적으로 설정 하려면 [`OutputBinding<T>`](/java/api/com.microsoft.azure.functions.outputbinding) 및 속성을 포함 하는 형식의 특정 매개 변수에 주석을 적용 합니다 `T` `PartitionKey` `RowKey` . 이러한 속성에는를 구현 하거나 상속 하는 경우가 많습니다 `ITableEntity` `TableEntity` .

---

## <a name="exceptions-and-return-codes"></a>예외 및 반환 코드

| 바인딩 | 참고 |
|---|---|
| 테이블 | [테이블 오류 코드](/rest/api/storageservices/fileservices/table-service-error-codes) |
| Blob, 테이블, 큐 | [스토리지 오류 코드](/rest/api/storageservices/fileservices/common-rest-api-error-codes) |
| Blob, 테이블, 큐 | [문제 해결](/rest/api/storageservices/fileservices/troubleshooting-api-operations) |

## <a name="next-steps"></a>다음 단계

> [!div class="nextstepaction"]
> [Azure Functions 트리거 및 바인딩에 대한 자세한 정보](functions-triggers-bindings.md)
