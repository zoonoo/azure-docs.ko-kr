---
title: "Azure Functions Storage 테이블 바인딩 | Microsoft Docs"
description: "Azure Functions에서 Azure Storage 바인딩을 사용하는 방법을 이해합니다."
services: functions
documentationcenter: na
author: christopheranderson
manager: erikre
editor: 
tags: 
keywords: "Azure Functions, 함수, 이벤트 처리, 동적 계산, 서버를 사용하지 않는 아키텍처"
ms.assetid: 65b3437e-2571-4d3f-a996-61a74b50a1c2
ms.service: functions
ms.devlang: multiple
ms.topic: reference
ms.tgt_pltfrm: multiple
ms.workload: na
ms.date: 10/28/2016
ms.author: chrande
translationtype: Human Translation
ms.sourcegitcommit: 503f5151047870aaf87e9bb7ebf2c7e4afa27b83
ms.openlocfilehash: 67c308f8216ebd291ae483c9b18d43528687e9a9
ms.lasthandoff: 03/29/2017


---
# <a name="azure-functions-storage-table-bindings"></a>Azure Functions Storage 테이블 바인딩
[!INCLUDE [functions-selector-bindings](../../includes/functions-selector-bindings.md)]

이 문서에서는 Azure Functions에서 Azure Storage 테이블 바인딩을 구성하고 코딩하는 방법을 설명합니다. Azure Functions는 Azure Storage 테이블에 대한 입력 및 출력 바인딩을 지원합니다.

Storage 테이블 바인딩은 다음과 같은 시나리오를 지원합니다.

* **C# 또는 Node.js 함수에서 단일 행을 읽습니다.** - `partitionKey` 및 `rowKey`를 설정합니다. `filter` 및 `take` 속성은 이 시나리오에서 사용되지 않습니다.
* **C# 함수에서 여러 행을 읽습니다.** - 함수 런타임은 테이블에 바인딩된 `IQueryable<T>` 개체를 제공합니다. `T` 형식은 `TableEntity`에서 파생되거나 `ITableEntity`를 구현해야 합니다. `partitionKey`, `rowKey`, `filter` 및 `take` 속성은 이 시나리오에서 사용되지 않습니다. `IQueryable` 개체를 사용하여 필요한 모든 필터링 작업을 수행할 수 있습니다. 
* **Node 함수에서 여러 행을 읽습니다.** - `filter` 및 `take` 속성을 설정합니다. `partitionKey` 또는 `rowKey`를 설정하지 않습니다.
* **C# 함수에서 하나 이상의 행을 작성합니다.** - 함수 런타임에서는 테이블에 바인딩된 `ICollector<T>` 또는 `IAsyncCollector<T>`를 제공하며 여기서 `T`는 추가하려는 엔터티의 스키마를 지정합니다. 일반적으로 `T` 형식은 `TableEntity`에서 파생되거나 `ITableEntity`을 구현하지만 반드시 그런 것은 아닙니다. `partitionKey`, `rowKey`, `filter` 및 `take` 속성은 이 시나리오에서 사용되지 않습니다.

[!INCLUDE [intro](../../includes/functions-bindings-intro.md)]

<a name="input"></a>

## <a name="storage-table-input-binding"></a>Storage 테이블 입력 바인딩
Azure Storage 테이블 입력 바인딩을 사용하면 함수의 저장소 테이블을 사용할 수 있습니다. 

함수에 대한 Storage 테이블 입력은 function.json의 `bindings` 배열에서 다음과 같은 JSON 개체를 사용합니다.

```json
{
    "name": "<Name of input parameter in function signature>",
    "type": "table",
    "direction": "in",
    "tableName": "<Name of Storage table>",
    "partitionKey": "<PartitionKey of table entity to read - see below>",
    "rowKey": "<RowKey of table entity to read - see below>",
    "take": "<Maximum number of entities to read in Node.js - optional>",
    "filter": "<OData filter expression for table input in Node.js - optional>",
    "connection": "<Name of app setting - see below>",
}
```

다음 사항에 유의하세요. 

* `partitionKey` 및 `rowKey`를 함께 사용하여 단일 엔터티를 읽습니다. 이러한 속성은 선택적입니다. 
* `connection`은 저장소 연결 문자열을 포함하는 앱 설정의 이름을 포함해야 합니다. Azure Portal에서 **통합** 탭에 있는 표준 편집기는 저장소 계정을 만들거나 기존 계정을 선택하는 경우 사용하는 이 앱 설정을 구성합니다. 또한 [이 앱 설정을 수동으로 구성](functions-how-to-use-azure-function-app-settings.md#application-settings)할 수도 있습니다.  

<a name="inputusage"></a>

## <a name="input-usage"></a>입력 사용
C# 함수에서 `<T> <name>`같은 함수 시그니처의 명명된 매개 변수를 사용하여 입력 테이블 엔터티(또는 여러 엔터티)를 바인딩합니다.
여기서 `T`는 데이터를 deserialize할 데이터 형식이며, `paramName`은 [입력 바인딩](#input)에서 사용자가 지정한 이름입니다. Node.js 함수에서 `context.bindings.<name>`을 사용하여 입력 테이블 엔터티(또는 여러 엔터티)에 액세스합니다.

입력 데이터는 Node.js 또는 C# 함수에서 deserialize될 수 있습니다. Deserialize된 개체는 `RowKey` 및 `PartitionKey` 속성을 가집니다.

C# 함수에서 다음 형식 중 하나에 바인딩할 수도 있으며, Functions 런타임이 해당 형식을 사용하여 테이블 데이터를 deserialize하려고 시도하게 됩니다.

* 구현하는 모든 형식 `ITableEntity`
* `IQueryable<T>`

<a name="inputsample"></a>

## <a name="input-sample"></a>입력 샘플
큐 트리거를 사용하여 단일 테이블 행을 읽는 다음과 같은 function.json이 있다고 가정합니다. JSON은 `PartitionKey` 
`RowKey`를 지정합니다. `"rowKey": "{queueTrigger}"`는 큐 메시지 문자열에서 나온 행 키를 의미합니다.

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
      "name": "personEntity",
      "type": "table",
      "tableName": "Person",
      "partitionKey": "Test",
      "rowKey": "{queueTrigger}",
      "connection": "MyStorageConnection",
      "direction": "in"
    }
  ],
  "disabled": false
}
```

단일 테이블 엔터티를 읽는 언어별 샘플을 참조하세요.

* [C#](#inputcsharp)
* [F#](#inputfsharp)
* [Node.JS](#inputnodejs)

<a name="inputcsharp"></a>

### <a name="input-sample-in-c"></a>C의 입력 샘플# #
```csharp
public static void Run(string myQueueItem, Person personEntity, TraceWriter log)
{
    log.Info($"C# Queue trigger function processed: {myQueueItem}");
    log.Info($"Name in Person entity: {personEntity.Name}");
}

public class Person
{
    public string PartitionKey { get; set; }
    public string RowKey { get; set; }
    public string Name { get; set; }
}
```

<a name="inputfsharp"></a>

### <a name="input-sample-in-f"></a>F의 입력 샘플# #
```fsharp
[<CLIMutable>]
type Person = {
  PartitionKey: string
  RowKey: string
  Name: string
}

let Run(myQueueItem: string, personEntity: Person) =
    log.Info(sprintf "F# Queue trigger function processed: %s" myQueueItem)
    log.Info(sprintf "Name in Person entity: %s" personEntity.Name)
```

<a name="inputnodejs"></a>

### <a name="input-sample-in-nodejs"></a>Node.js에서 입력 샘플
```javascript
module.exports = function (context, myQueueItem) {
    context.log('Node.js queue trigger function processed work item', myQueueItem);
    context.log('Person entity name: ' + context.bindings.personEntity.Name);
    context.done();
};
```

<a name="output"></a>

## <a name="storage-table-output-binding"></a>Storage 테이블 출력 바인딩
Azure Storage 테이블 출력 바인딩을 사용하면 엔터티를 함수의 저장소 테이블에 쓸 수 있습니다. 

함수에 대한 Storage 테이블 출력은 function.json의 `bindings` 배열에서 다음과 같은 JSON 개체를 사용합니다.

```json
{
    "name": "<Name of input parameter in function signature>",
    "type": "table",
    "direction": "out",
    "tableName": "<Name of Storage table>",
    "partitionKey": "<PartitionKey of table entity to write - see below>",
    "rowKey": "<RowKey of table entity to write - see below>",
    "connection": "<Name of app setting - see below>",
}
```

다음 사항에 유의하세요. 

* `partitionKey` 및 `rowKey`를 함께 사용하여 단일 엔터티를 씁니다. 이러한 속성은 선택적입니다. 또한 함수 코드에 엔터티 개체를 만들 때 `PartitionKey` 및 `RowKey`를 지정할 수 있습니다.
* `connection`은 저장소 연결 문자열을 포함하는 앱 설정의 이름을 포함해야 합니다. Azure Portal에서 **통합** 탭에 있는 표준 편집기는 저장소 계정을 만들거나 기존 계정을 선택하는 경우 사용하는 이 앱 설정을 구성합니다. 또한 [이 앱 설정을 수동으로 구성](functions-how-to-use-azure-function-app-settings.md#application-settings)할 수도 있습니다. 

<a name="outputusage"></a>

## <a name="output-usage"></a>출력 사용
C# 함수에서 `out <T> <name>`같은 함수 시그니처의 명명된 `out` 매개 변수를 사용하여 테이블 출력을 바인딩합니다. 여기서 `T`는 데이터를 직렬화하려는 데이터 형식이며, `paramName`은 [출력 바인딩](#output)에서 사용자가 지정한 이름입니다. Node.js 함수에서 `context.bindings.<name>`을 사용하여 테이블 출력에 액세스합니다.

Node.js 또는 C# 함수에서 개체를 직렬화할 수 있습니다. C# 함수에서 다음 형식으로 바인딩할 수 있습니다.

* 구현하는 모든 형식 `ITableEntity`
* `ICollector<T>`(여러 엔터티를 출력. [샘플](#outcsharp)을 참조하세요.)
* `IAsyncCollector<T>`(비동기 버전의 `ICollector<T>`)
* `CloudTable` (Azure Storage SDK 사용. [샘플](#readmulti)을 참조하세요.)

<a name="outputsample"></a>

## <a name="output-sample"></a>출력 샘플
다음 *function.json* 및 *run.csx* 예제에서는 여러 테이블 엔터티를 작성하는 방법을 보여 줍니다.

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
      "connection": "MyStorageConnection",
      "name": "tableBinding",
      "type": "table",
      "direction": "out"
    }
  ],
  "disabled": false
}
```

여러 테이블 엔터티를 만드는 언어별 샘플을 참조하세요.

* [C#](#outcsharp)
* [F#](#outfsharp)
* [Node.JS](#outnodejs)

<a name="outcsharp"></a>

### <a name="output-sample-in-c"></a>C에서 출력 샘플# #
```csharp
public static void Run(string input, ICollector<Person> tableBinding, TraceWriter log)
{
    for (int i = 1; i < 10; i++)
        {
            log.Info($"Adding Person entity {i}");
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
<a name="outfsharp"></a>

### <a name="output-sample-in-f"></a>F에서 출력 샘플# #
```fsharp
[<CLIMutable>]
type Person = {
  PartitionKey: string
  RowKey: string
  Name: string
}

let Run(input: string, tableBinding: ICollector<Person>, log: TraceWriter) =
    for i = 1 to 10 do
        log.Info(sprintf "Adding Person entity %d" i)
        tableBinding.Add(
            { PartitionKey = "Test"
              RowKey = i.ToString()
              Name = "Name" + i.ToString() })
```

<a name="outnodejs"></a>

### <a name="output-sample-in-nodejs"></a>Node.js에서 출력 샘플
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

<a name="readmulti"></a>

## <a name="sample-read-multiple-table-entities-in-c"></a>샘플: C에서 여러 테이블 엔터티 읽기#  #
다음 *function.json* 및 C# 코드 예제에서는 큐 메시지에 지정된 파티션 키에 대한 엔터티를 읽습니다.

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
      "name": "tableBinding",
      "type": "table",
      "connection": "MyStorageConnection",
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
    log.Info($"C# Queue trigger function processed: {myQueueItem}");
    foreach (Person person in tableBinding.Where(p => p.PartitionKey == myQueueItem).ToList())
    {
        log.Info($"Name: {person.Name}");
    }
}

public class Person : TableEntity
{
    public string Name { get; set; }
}
```

## <a name="next-steps"></a>다음 단계
[!INCLUDE [next steps](../../includes/functions-bindings-next-steps.md)]


