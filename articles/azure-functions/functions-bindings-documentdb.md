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
ms.date: 11/10/2016
ms.author: chrande; glenga
translationtype: Human Translation
ms.sourcegitcommit: eeb56316b337c90cc83455be11917674eba898a3
ms.openlocfilehash: 2ac78606f851068fa0fb7dcab3bac1c629b9cdb3
ms.lasthandoff: 04/03/2017


---
# <a name="azure-functions-documentdb-bindings"></a>Azure Functions DocumentDB 바인딩
[!INCLUDE [functions-selector-bindings](../../includes/functions-selector-bindings.md)]

이 문서에서는 Azure Functions에서 Azure DocumentDB 바인딩을 구성하고 코딩하는 방법을 설명합니다. Azure Functions는 DocumentDB에 대한 입력 및 출력 바인딩을 지원합니다.

[!INCLUDE [intro](../../includes/functions-bindings-intro.md)]

DocumentDB에 대한 자세한 내용은 [DocumentDB 소개](../documentdb/documentdb-introduction.md) 및 [DocumentDB 콘솔 응용 프로그램 빌드](../documentdb/documentdb-get-started.md)를 참조하세요.

<a id="docdbinput"></a>

## <a name="documentdb-input-binding"></a>DocumentDB 입력 바인딩
DocumentDB 입력 바인딩은 DocumentDB 문서를 검색하고 함수의 명명된 입력 매개 변수를 전달합니다. 함수를 호출한 트리거에 따라 문서 ID를 결정할 수 있습니다. 

함수에 대한 DocumentDB 입력은 function.json의 `bindings` 배열에서 다음과 같은 JSON 개체를 사용합니다.

```json
{
  "name": "<Name of input parameter in function signature>",
  "type": "documentDB",
  "databaseName": "<Name of the DocumentDB database>",
  "collectionName": "<Name of the DocumentDB collection>",
  "id": "<Id of the DocumentDB document - see below>",
  "connection": "<Name of app setting with connection string - see below>",
  "direction": "in"
},
```

다음 사항에 유의하세요.

* `id`는 `{queueTrigger}`와 유사한 바인딩을 지원하며 이는 큐 메시지의 문자열 값을 문서 ID로 사용합니다.
* `connection`은 반드시 DocumentDB 계정에 대한 끝점을 가리키는(값 `AccountEndpoint=<Endpoint for your account>;AccountKey=<Your primary access key>`를 갖는) 앱 설정의 이름이어야 합니다. 함수 포털 UI를 통해 DocumentDB 계정을 만들 경우 계정 만들기 프로세스에서 사용자를 위한 앱 설정이 만들어집니다. 기존 DocumentDB 계정을 사용하려면 [이 앱 설정을 수동으로 구성](functions-how-to-use-azure-function-app-settings.md)해야 합니다. 
* 지정된 문서가 없는 경우 함수에 대해 명명된 입력 매개 변수가 `null`로 설정됩니다. 

## <a name="input-usage"></a>입력 사용
이 섹션에서는 함수 코드에서 DocumentDB 입력 바인딩을 사용하는 방법을 보여 줍니다.

C# 및 F# 함수에서 함수가 성공적으로 종료되면 입력 문서에 변경한 내용(명명된 입력 매개 변수)을 자동으로 다시 컬렉션에 전송합니다. Node.js 함수에서는 입력 바인딩에 업데이트된 문서를 컬렉션에 다시 전송하지 않습니다. 하지만 `context.bindings.<documentName>In` 및 `context.bindings.<documentName>Out`을 사용하여 입력 문서를 업데이트할 수 있습니다. [Node.js 샘플](#innodejs)에서 어떻게 수행되는지 확인하세요.

<a name="inputsample"></a>

## <a name="input-sample"></a>입력 샘플
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
* [Node.JS](#innodejs)

<a name="incsharp"></a>
### <a name="input-sample-in-c"></a>C의 입력 샘플# #

```cs
public static void Run(string myQueueItem, dynamic inputDocument)
{   
  inputDocument.text = "This has changed.";
}
```
<a name="infsharp"></a>

### <a name="input-sample-in-f"></a>F의 입력 샘플# #

```fsharp
open FSharp.Interop.Dynamic
let Run(myQueueItem: string, inputDocument: obj) =
  inputDocument?text <- "This has changed."
```

`FSharp.Interop.Dynamic` 및 `Dynamitey` NuGet 종속성을 지정하는 `project.json` 파일을 추가해야 합니다.

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

<a name="innodejs"></a>

### <a name="input-sample-in-nodejs"></a>Node.js에서 입력 샘플

```javascript
module.exports = function (context) {   
  context.bindings.inputDocumentOut = context.bindings.inputDocumentIn;
  context.bindings.inputDocumentOut.text = "This was updated!";
  context.done();
};
```

## <a id="docdboutput"></a>DocumentDB 출력 바인딩
DocumentDB 출력 바인딩을 사용하면 Azure DocumentDB 데이터베이스에 새 문서를 작성할 수 있습니다. 

출력 바인딩은 function.json의 `bindings` 배열에서 다음과 같은 JSON 개체를 사용합니다. 

```json
{
  "name": "<Name of output parameter in function signature>",
  "type": "documentDB",
  "databaseName": "<Name of the DocumentDB database>",
  "collectionName": "<Name of the DocumentDB collection>",
  "createIfNotExists": <true or false - see below>,
  "connection": "<Value of AccountEndpoint in Application Setting - see below>",
  "direction": "out"
}
```

다음 사항에 유의하세요.

* 데이터베이스 및 컬렉션이 없는 경우 `createIfNotExists`를 `true`로 설정하여 만듭니다. 기본값은 `false`입니다. 새 컬렉션이 예약된 처리량을 사용하여 만들어지면 가격 책정 면에서 의미하는 바가 있습니다. 자세한 내용은 [DocumentDB 가격](https://azure.microsoft.com/pricing/details/documentdb/)을 참조하세요.
* `connection`은 반드시 DocumentDB 계정에 대한 끝점을 가리키는(값 `AccountEndpoint=<Endpoint for your account>;AccountKey=<Your primary access key>`를 갖는) 앱 설정의 이름이어야 합니다. 함수 포털 UI를 통해 DocumentDB 계정을 만들 경우 계정 만들기 프로세스에서 사용자를 위한 새로운 앱 설정이 만들어집니다. 기존 DocumentDB 계정을 사용하려면 [이 앱 설정을 수동으로 구성](functions-how-to-use-azure-function-app-settings.md)해야 합니다. 

## <a name="output-usage"></a>출력 사용
이 섹션에서는 함수 코드에서 DocumentDB 출력 바인딩을 사용하는 방법을 보여 줍니다.

함수에서 출력 매개 변수를 작성하는 경우 기본적으로 새 문서는 사용자의 데이터베이스에 생성되며, 자동으로 생성된 GUID를 문서 ID로 사용합니다. 출력 매개 변수의 `id` JSON 속성을 지정하여 출력 문서의 문서 ID를 지정할 수 있습니다. 

>[!Note]  
>기존 문서의 ID를 지정하면 새 출력 문서에 의해 덮어쓰여집니다. 

다음 형식 중 하나를 사용하여 출력에 작성할 수 있습니다.

* 모든 [개체](https://msdn.microsoft.com/library/system.object.aspx)는 JSON 직렬화에 유용합니다.
  사용자 지정 출력 형식을 선언하는 경우(예: `out FooType paramName`), Azure Functions에서 개체를 JSON으로 직렬화하려고 시도합니다. 함수가 종료될 때 출력 매개 변수가 null이면 Functions 런타임은 Blob을 null 개체로 만듭니다.
* 문자열(`out string paramName`)은 텍스트 Blob 데이터에 유용합니다. Functions 런타임은 함수가 종료될 때 문자열 매개 변수가 null이 아닌 경우에만 Blob을 생성합니다.

C# 함수에서 다음 중 원하는 형식으로 출력할 수 있습니다.

* `TextWriter`
* `Stream`
* `CloudBlobStream`
* `ICloudBlob`
* `CloudBlockBlob` 
* `CloudPageBlob` 

여러 문서를 출력하기 위해 `ICollector<T>` 또는 `IAsyncCollector<T>`에 바인딩할 수 있으며, 여기서 `T`는 지원되는 형식 중 하나입니다.


<a name="outputsample"></a>

## <a name="output-sample"></a>출력 샘플
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
* [Node.JS](#outnodejs)

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

`FSharp.Interop.Dynamic` 및 `Dynamitey` NuGet 종속성을 지정하는 `project.json` 파일을 추가해야 합니다.

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

<a name="outnodejs"></a>

### <a name="output-sample-in-nodejs"></a>Node.js에서 출력 샘플

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

