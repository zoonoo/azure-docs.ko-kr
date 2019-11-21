---
title: Azure Functions의 트리거 및 바인딩
description: Learn to use triggers and bindings to connect your Azure Function to online events and cloud-based services.
author: craigshoemaker
ms.topic: reference
ms.date: 02/18/2019
ms.author: cshoe
ms.openlocfilehash: d41fd7f66ecef3a563345424d7dc4366e47d3f0e
ms.sourcegitcommit: d6b68b907e5158b451239e4c09bb55eccb5fef89
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/20/2019
ms.locfileid: "74226555"
---
# <a name="azure-functions-triggers-and-bindings-concepts"></a>Azure Functions 트리거 및 바인딩 개념

In this article you learn the high-level concepts surrounding functions triggers and bindings.

Triggers are what cause a function to run. A trigger defines how a function is invoked and a function must have exactly one trigger. Triggers have associated data, which is often provided as the payload of the function. 

Binding to a function is a way of declaratively connecting another resource to the function; bindings may be connected as *input bindings*, *output bindings*, or both. 바인딩의 데이터는 함수에 매개 변수로 제공됩니다.

You can mix and match different bindings to suit your needs. Bindings are optional and a function might have one or multiple input and/or output bindings.

Triggers and bindings let you avoid hardcoding access to other services. 함수는 함수 매개 변수에서 데이터를 수신합니다(예: 큐 메시지의 콘텐츠). 함수의 반환 값을 사용하여 데이터를 보냅니다(예를 들어 큐 메시지를 만들기 위해). 

Consider the following examples of how you could implement different functions.

| 예제 시나리오 | 트리거 | Input binding | Output binding |
|-------------|---------|---------------|----------------|
| A new queue message arrives which runs a function to write to another queue. | Queue<sup>*</sup> | *없음* | Queue<sup>*</sup> |
|A scheduled job reads Blob Storage contents and creates a new Cosmos DB document. | 타이머 | Blob Storage | Cosmos DB |
|The Event Grid is used to read an image from Blob Storage and a document from Cosmos DB to send an email. | Event Grid | Blob Storage and  Cosmos DB | SendGrid |
| A webhook that uses Microsoft Graph to update an Excel sheet. | http | *없음* | Microsoft Graph |

<sup>\*</sup> Represents different queues

These examples are not meant to be exhaustive, but are provided to illustrate how you can use triggers and bindings together.

###  <a name="trigger-and-binding-definitions"></a>Trigger and binding definitions

Triggers and bindings are defined differently depending on the development approach.

| 플랫폼 | Triggers and bindings are configured by... |
|-------------|--------------------------------------------|
| C# class library | &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;decorating methods and parameters with C# attributes |
| All others (including Azure portal) | &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;updating [function.json](./functions-reference.md) ([schema](http://json.schemastore.org/function)) |

The portal provides a UI for this configuration, but you can edit the file directly by opening the **Advanced editor** available via the **Integrate** tab of your function.

In .NET, the parameter type defines the data type for input data. For instance, use `string` to bind to the text of a queue trigger, a byte array to read as binary and a custom type to de-serialize to an object.

JavaScript와 같은 동적으로 형식화되는 언어의 경우 *function.json* 파일의 `dataType` 속성을 사용합니다. 예를 들어 이진 형식의 HTTP 요청 내용을 읽으려면 `dataType`을 `binary`로 설정합니다.

```json
{
    "dataType": "binary",
    "type": "httpTrigger",
    "name": "req",
    "direction": "in"
}
```

`dataType`에 대한 다른 옵션은 `stream` 및 `string`입니다.

## <a name="binding-direction"></a>바인딩 방향

모든 트리거와 바인딩은 [function.json](./functions-reference.md) 파일에 `direction` 속성이 있습니다.

- 트리거의 경우 방향은 언제나 `in`입니다
- 입력 및 출력 바인딩은 `in`과 `out`을 사용합니다
- 일부 바인딩은 특수 방향인 `inout`을 사용합니다. If you use `inout`, only the **Advanced editor** is available via the **Integrate** tab in the portal.

[클래스 라이브러리의 특성](functions-dotnet-class-library.md)을 사용하여 트리거 및 바인딩을 구성하는 경우 방향은 특성 생성자에서 제공되거나 매개 변수 형식에서 유추됩니다.

## <a name="supported-bindings"></a>지원되는 바인딩

[!INCLUDE [Full bindings table](../../includes/functions-bindings.md)]

미리 보기 상태 바인딩 또는 프로덕션 용도로 승인된 바인딩에 대한 자세한 내용은 [지원되는 언어](supported-languages.md)를 참조하세요.

## <a name="resources"></a>리소스
- [Binding expressions and patterns](./functions-bindings-expressions-patterns.md)
- [Using the Azure Function return value](./functions-bindings-return-value.md)
- [How to register a binding expression](./functions-bindings-register.md)
- 테스트:
  - [Azure Functions에서 코드를 테스트하기 위한 전략](functions-test-a-function.md)
  - [HTTP 이외 트리거 함수를 수동으로 실행](functions-manually-run-non-http.md)
- [Handling binding errors](./functions-bindings-errors.md)

## <a name="next-steps"></a>다음 단계
> [!div class="nextstepaction"]
> [Register Azure Functions binding extensions](./functions-bindings-register.md)
