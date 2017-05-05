---
title: "Azure Functions DocumentDB 바인딩 | Microsoft Docs"
description: "Azure Functions에서 Azure DocumentDB 바인딩을 사용하는 방법을 파악합니다."
services: functions
documentationcenter: na
author: christopheranderson
manager: erikre
editor: 
tags: 
keywords: "Azure Functions, 함수, 이벤트 처리, 동적 계산, 서버를 사용하지 않는 아키텍처"
ms.assetid: 3d8497f0-21f3-437d-ba24-5ece8c90ac85
ms.service: functions
ms.devlang: multiple
ms.topic: reference
ms.tgt_pltfrm: multiple
ms.workload: na
ms.date: 04/18/2016
ms.author: chrande; glenga
translationtype: Human Translation
ms.sourcegitcommit: abdbb9a43f6f01303844677d900d11d984150df0
ms.openlocfilehash: e38c9187be42946df1e8059ba44f10f76d32d984
ms.lasthandoff: 04/21/2017


---
# <a name="azure-functions-documentdb-bindings"></a>Azure Functions DocumentDB 바인딩
[!INCLUDE [functions-selector-bindings](../../includes/functions-selector-bindings.md)]

이 문서에서는 Azure Functions에서 Azure DocumentDB 바인딩을 구성하고 코딩하는 방법을 설명합니다. Azure Functions는 DocumentDB에 대한 입력 및 출력 바인딩을 지원합니다.

[!INCLUDE [intro](../../includes/functions-bindings-intro.md)]

DocumentDB에 대한 자세한 내용은 [DocumentDB 소개](../documentdb/documentdb-introduction.md) 및 [DocumentDB 콘솔 응용 프로그램 빌드](../documentdb/documentdb-get-started.md)를 참조하세요.

<a id="docdbinput"></a>

## <a name="documentdb-input-binding"></a>DocumentDB 입력 바인딩
DocumentDB 입력 바인딩은 DocumentDB 문서를 검색하고 함수의 명명된 입력 매개 변수를 전달합니다. 함수를 호출한 트리거에 따라 문서 ID를 결정할 수 있습니다. 

DocumentDB 입력 바인딩은 *function.json*에 다음 속성이 있습니다.

- `name` : 문서에 대한 함수 코드에 사용되는 식별자 이름입니다.
- `type` : “documentdb”로 설정해야 합니다.
- `databaseName` : 문서를 포함하는 데이터베이스입니다.
- `collectionName` : 문서를 포함하는 컬렉션입니다.
- `id` : 검색할 문서의 ID입니다. 이 속성은 바인딩 매개 변수를 지원합니다. [Azure Functions 트리거 및 바인딩 개념](functions-triggers-bindings.md) 문서에서 [바인딩 식에서 사용자 지정 입력 속성에 바인딩](functions-triggers-bindings.md#bind-to-custom-input-properties-in-a-binding-expression)을 참조하세요.
- `sqlQuery`: 여러 문서를 검색하는 데 사용되는 DocumentDB SQL 쿼리입니다. 쿼리는 런타임 바인딩을 지원합니다. 예: `SELECT * FROM c where c.departmentId = {departmentId}`
- `connection`: DocumentDB 연결 문자열을 포함하는 앱 설정의 이름입니다.
- `direction` : `"in"`으로 설정해야 합니다.

속성 `id` 및 `sqlQuery`를 둘 다 지정할 수는 없습니다. `id` 및 `sqlQuery`를 둘 다 설정하지 않으면 전체 컬렉션이 검색됩니다.

## <a name="using-a-documentdb-input-binding"></a>DocumentDB 입력 바인딩 사용

* C# 및 F# 함수에서 함수가 성공적으로 종료되면 명명된 입력 매개 변수를 통해 입력 문서가 자동으로 변경됩니다. 
* JavaScript 함수에서는 함수 종료 시 자동으로 업데이트되지 않습니다. 대신 `context.bindings.<documentName>In` 및 `context.bindings.<documentName>Out`을 사용하여 업데이트합니다. [JavaScript 샘플](#injavascript)을 참조하세요.

<a name="inputsample"></a>

## <a name="input-sample-for-single-document"></a>단일 문서에 대한 입력 샘플
function.json의 `bindings` 배열에 다음과 같은 DocumentDB 입력 바인딩이 있다고 가정합니다.

```json
{
  "name": "inputDocument",
  "type": "documentDB",
  "databaseName": "MyDatabase",
  "collectionName": "MyCollection",
  "id" : "{queueTrigger}",
  "connection": "MyAccount_DOCUMENTDB",     
  "direction": "in"
}
```

이 입력 바인딩을 사용하여 문서의 텍스트 값을 업데이트하는 언어별 샘플을 참조하세요.

* [C#](#incsharp)
* [F#](#infsharp)
* [JavaScript](#injavascript)

<a name="incsharp"></a>
### <a name="input-sample-in-c"></a>C의 입력 샘플# #

```cs
// Change input document contents using DocumentDB input binding 
public static void Run(string myQueueItem, dynamic inputDocument)
{   
  inputDocument.text = "This has changed.";
}
```
<a name="infsharp"></a>

### <a name="input-sample-in-f"></a>F의 입력 샘플# #

```fsharp
(* Change input document contents using DocumentDB input binding *)
open FSharp.Interop.Dynamic
let Run(myQueueItem: string, inputDocument: obj) =
  inputDocument?text <- "This has changed."
```

이 샘플에는 `FSharp.Interop.Dynamic` 및 `Dynamitey` NuGet 종속성을 지정하는 `project.json` 파일이 필요합니다.

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

`project.json` 파일을 추가하려면 [F# 패키지 관리](functions-reference-fsharp.md#package)를 참조하세요.

<a name="injavascript"></a>

### <a name="input-sample-in-javascript"></a>JavaScript의 입력 샘플

```javascript
// Change input document contents using DocumentDB input binding, using context.bindings.inputDocumentOut
module.exports = function (context) {   
  context.bindings.inputDocumentOut = context.bindings.inputDocumentIn;
  context.bindings.inputDocumentOut.text = "This was updated!";
  context.done();
};
```

## <a name="input-sample-with-multiple-documents"></a>여러 문서가 있는 입력 샘플

SQL 쿼리로 지정된 여러 문서를 검색하려고 하며 큐 트리거를 사용하여 쿼리 매개 변수를 사용자 지정한다고 가정합니다. 

이 예제에서 큐 트리거는 매개 변수 `departmentId`를 제공합니다. 큐 메시지 `{ "departmentId" : "Finance" }`는 재무 부서에 대한 모든 레코드를 반환합니다. *function.json*에서 다음을 사용합니다.

```
{
    "name": "documents",
    "type": "documentdb",
    "direction": "in",
    "databaseName": "MyDb",
    "collectionName": "MyCollection",
    "sqlQuery": "SELECT * from c where c.departmentId = {departmentId}"
    "connection": "DocumentDBConnection"
}
```

### <a name="input-sample-with-multiple-documents-in-c"></a>C로 작성된 여러 문서가 있는 입력 샘플#

```csharp
public static void Run(QueuePayload myQueueItem, IEnumerable<dynamic> documents)
{   
    foreach (var doc in documents)
    {
        // operate on each document
    }    
}

public class QueuePayload
{
    public string departmentId { get; set; }
}
```

### <a name="input-sample-with-multiple-documents-in-javascript"></a>JavaScript로 작성된 여러 문서가 있는 입력 샘플

```javascript
module.exports = function (context, input) {    
    var documents = context.bindings.documents;
    for (var i = 0; i < documents.length; i++) {
        var document = documents[i];
        // operate on each document
    }        
    context.done();
};
```

## <a id="docdboutput"></a>DocumentDB 출력 바인딩
DocumentDB 출력 바인딩을 사용하면 Azure DocumentDB 데이터베이스에 새 문서를 작성할 수 있습니다. *function.json*에 다음 속성이 있습니다.

- `name` : 새 문서에 대한 함수 코드에 사용되는 식별자입니다.
- `type` : `"documentdb"`로 설정해야 합니다.
- `databaseName` : 새 문서를 만들 컬렉션을 포함하는 데이터베이스입니다.
- `collectionName` : 새 문서를 만들 컬렉션입니다.
- `createIfNotExists` : 컬렉션이 존재하지 않는 경우 만들 수 있는지 여부를 나타내는 부울 값입니다. 기본값은 *false*입니다. 새 컬렉션이 예약된 처리량을 사용하여 만들어지면 가격 책정 면에서 의미하는 바가 있기 때문입니다. 자세한 내용은 [가격 책정 페이지](https://azure.microsoft.com/pricing/details/documentdb/)를 참조하세요.
- `connection`: DocumentDB 연결 문자열을 포함하는 앱 설정의 이름입니다.
- `direction` : `"out"`으로 설정해야 합니다.

## <a name="using-a-documentdb-output-binding"></a>DocumentDB 출력 바인딩 사용
이 섹션에서는 함수 코드에서 DocumentDB 출력 바인딩을 사용하는 방법을 보여 줍니다.

함수에서 출력 매개 변수를 작성하는 경우 기본적으로 새 문서는 사용자의 데이터베이스에 생성되며, 자동으로 생성된 GUID를 문서 ID로 사용합니다. 출력 매개 변수의 `id` JSON 속성을 지정하여 출력 문서의 문서 ID를 지정할 수 있습니다. 

>[!Note]  
>기존 문서의 ID를 지정하면 새 출력 문서에 의해 덮어쓰여집니다. 

여러 문서를 출력하기 위해 `ICollector<T>` 또는 `IAsyncCollector<T>`에 바인딩할 수 있으며, 여기서 `T`는 지원되는 형식 중 하나입니다.

<a name="outputsample"></a>

## <a name="documentdb-output-binding-sample"></a>DocumentDB 출력 바인딩 샘플
function.json의 `bindings` 배열에 다음과 같은 DocumentDB 출력 바인딩이 있다고 가정합니다.

```json
{
  "name": "employeeDocument",
  "type": "documentDB",
  "databaseName": "MyDatabase",
  "collectionName": "MyCollection",
  "createIfNotExists": true,
  "connection": "MyAccount_DOCUMENTDB",     
  "direction": "out"
}
```

또한 다음과 같은 형식의 JSON을 수신하는 큐에 대한 큐 입력 바인딩이 있습니다.

```json
{
  "name": "John Henry",
  "employeeId": "123456",
  "address": "A town nearby"
}
```

그리고 각 레코드에 대해 다음과 같은 형식의 DocumentDB 문서를 만들려고 합니다.

```json
{
  "id": "John Henry-123456",
  "name": "John Henry",
  "employeeId": "123456",
  "address": "A town nearby"
}
```

이 출력 바인딩을 사용하여 문서를 데이터베이스에 추가하는 언어별 샘플을 참조하세요.

* [C#](#outcsharp)
* [F#](#outfsharp)
* [JavaScript](#outjavascript)

<a name="outcsharp"></a>

### <a name="output-sample-in-c"></a>C에서 출력 샘플# #

```cs
#r "Newtonsoft.Json"

using System;
using Newtonsoft.Json;
using Newtonsoft.Json.Linq;

public static void Run(string myQueueItem, out object employeeDocument, TraceWriter log)
{
  log.Info($"C# Queue trigger function processed: {myQueueItem}");

  dynamic employee = JObject.Parse(myQueueItem);

  employeeDocument = new {
    id = employee.name + "-" + employee.employeeId,
    name = employee.name,
    employeeId = employee.employeeId,
    address = employee.address
  };
}
```

<a name="outfsharp"></a>

### <a name="output-sample-in-f"></a>F에서 출력 샘플# #

```fsharp
open FSharp.Interop.Dynamic
open Newtonsoft.Json

type Employee = {
  id: string
  name: string
  employeeId: string
  address: string
}

let Run(myQueueItem: string, employeeDocument: byref<obj>, log: TraceWriter) =
  log.Info(sprintf "F# Queue trigger function processed: %s" myQueueItem)
  let employee = JObject.Parse(myQueueItem)
  employeeDocument <-
    { id = sprintf "%s-%s" employee?name employee?employeeId
      name = employee?name
      employeeId = employee?employeeId
      address = employee?address }
```

이 샘플에는 `FSharp.Interop.Dynamic` 및 `Dynamitey` NuGet 종속성을 지정하는 `project.json` 파일이 필요합니다.

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

`project.json` 파일을 추가하려면 [F# 패키지 관리](functions-reference-fsharp.md#package)를 참조하세요.

<a name="outjavascript"></a>

### <a name="output-sample-in-javascript"></a>JavaScript의 출력 샘플

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

