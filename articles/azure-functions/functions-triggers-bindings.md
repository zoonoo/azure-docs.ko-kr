---
title: Azure Functions의 트리거 및 바인딩
description: 트리거 및 바인딩을 사용하여 Azure 함수를 온라인 이벤트 및 클라우드 기반 서비스에 연결하는 방법을 알아봅니다.
author: craigshoemaker
ms.topic: conceptual
ms.date: 02/18/2019
ms.author: cshoe
ms.openlocfilehash: 8648a52c58929983070b9a89c8fe946b89d37385
ms.sourcegitcommit: 425420fe14cf5265d3e7ff31d596be62542837fb
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/20/2021
ms.locfileid: "107739407"
---
# <a name="azure-functions-triggers-and-bindings-concepts"></a>Azure Functions 트리거 및 바인딩 개념

이 문서에서는 함수 트리거 및 바인딩에 관한 개략적인 개념에 대해 알아봅니다.

트리거는 함수가 실행되도록 하는 요인입니다. 트리거는 함수가 호출되는 방식을 정의합니다. 하나의 함수는 하나의 트리거만 가져야 합니다. 트리거에는 관련 데이터가 있으며, 일반적으로 함수의 페이로드로 제공됩니다. 

함수에 바인딩하는 것은 함수에 다른 리소스를 선언적으로 연결하는 한 가지 방법입니다. 바인딩은 ‘입력 바인딩’ 또는 ‘출력 바인딩’으로서 연결될 수도 있고 두 가지로서 연결될 수도 있습니다.  바인딩의 데이터는 함수에 매개 변수로 제공됩니다.

필요에 맞게 다른 바인딩과 혼합하고 일치시킬 수 있습니다. 바인딩은 선택 사항이며 함수는 여러 개의 입력 및 출력 바인딩을 가질 수 있습니다.

트리거와 바인딩을 사용하면 다른 서비스에 대한 액세스를 하드 코딩하는 것을 방지할 수 있습니다. 함수는 함수 매개 변수에서 데이터를 수신합니다(예: 큐 메시지의 콘텐츠). 함수의 반환 값을 사용하여 데이터를 보냅니다(예를 들어 큐 메시지를 만들기 위해). 

여러 함수를 구현하는 다음과 같은 예제를 살펴보세요.

| 예제 시나리오 | 트리거 | 입력 바인딩 | 출력 바인딩 |
|-------------|---------|---------------|----------------|
| 메시지를 다른 큐에 쓰는 함수를 실행하는 새 큐 메시지가 도착합니다. | 큐<sup>*</sup> | *없음* | 큐<sup>*</sup> |
|예약된 작업이 Blob Storage 콘텐츠를 읽고 새 Cosmos DB 문서를 만듭니다. | 타이머 | Blob Storage | Cosmos DB |
|Blob Storage에서 이미지를 읽고 Cosmos DB에서 문서를 읽어서 메일을 보내는 데 Event Grid가 사용됩니다. | Event Grid | Blob Storage와 Cosmos DB | SendGrid |
| Microsoft Graph를 사용하여 Excel 시트를 업데이트하는 웹후크. | HTTP | *없음* | Microsoft Graph |

<sup>\*</sup> 서로 다른 큐임

위에는 몇 가지 예제만 나와 있지만 이를 통해 트리거와 바인딩을 함께 사용하는 방법을 알아볼 수 있습니다.

###  <a name="trigger-and-binding-definitions"></a>트리거 및 바인딩 정의

트리거와 바인딩은 개발 언어에 따라 다르게 정의됩니다.

| 언어 | 트리거와 바인딩이 구성되는 방식 |
|-------------|--------------------------------------------|
| C# 클래스 라이브러리 | &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;메서드 및 매개 변수에 C# 특성 사용 |
| Java | &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;메서드 및 매개 변수에 Java 주석 사용  | 
| JavaScript/PowerShell/Python/TypeScript | &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;[function.json](./functions-reference.md)([스키마](http://json.schemastore.org/function)) 업데이트 |

function.json을 사용하는 언어의 경우, 포털에서 **통합** 탭에 바인딩을 추가하기 위한 UI가 제공됩니다. 함수의 **코드 + 테스트** 탭에서도 포털에서 직접 파일을 편집할 수 있습니다. Visual Studio Code를 사용하면 편리한 프롬프트에 따라 쉽게 [function.json 파일에 바인딩을 추가](functions-develop-vs-code.md?tabs=nodejs#add-a-function-to-your-project)할 수 있습니다. 

.NET 및 Java에서는 매개 변수 유형이 입력 데이터의 데이터 형식을 정의합니다. 예를 들어, 큐 트리거의 텍스트, 이진으로 읽을 바이트 배열, 개체에 대해 역직렬화할 사용자 지정 형식을 바인딩하려면 `string`을 사용합니다. .NET 클래스 라이브러리 함수와 Java 함수는 정의 바인딩을 위해 *function.json* 을 사용하지 않으므로 포털에서 만들고 편집할 수 없습니다. C# 포털 편집은 특성 대신 *function.json* 을 사용하는 C# 스크립트를 기반으로 합니다.

기존 함수에 바인딩을 추가하는 방법을 자세히 알아보려면 [바인딩을 사용하여 Azure 서비스에 함수 연결](add-bindings-existing-function.md)을 참조하세요.

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
- 일부 바인딩은 특수 방향인 `inout`을 사용합니다. `inout`을 사용하는 경우 포털의 **통합** 탭을 통해 **고급 편집기** 만 사용할 수 있습니다.

[클래스 라이브러리의 특성](functions-dotnet-class-library.md)을 사용하여 트리거 및 바인딩을 구성하는 경우 방향은 특성 생성자에서 제공되거나 매개 변수 형식에서 유추됩니다.

## <a name="add-bindings-to-a-function"></a>함수에 바인딩 추가

입력 또는 출력 바인딩을 사용하여 함수를 다른 서비스에 연결할 수 있습니다. 함수에 특정 정의를 추가하여 바인딩을 추가합니다. 방법을 알아보려면 [바인딩을 사용하여 Azure 서비스에 함수 연결](add-bindings-existing-function.md)을 참조하세요.  

## <a name="supported-bindings"></a>지원되는 바인딩

[!INCLUDE [Full bindings table](../../includes/functions-bindings.md)]

미리 보기 상태 바인딩 또는 프로덕션 용도로 승인된 바인딩에 대한 자세한 내용은 [지원되는 언어](supported-languages.md)를 참조하세요.

## <a name="bindings-code-examples"></a>바인딩 코드 예제

다음 표에서 특정 바인딩 형식의 예를 찾아서 함수에서 바인딩을 사용하는 방법을 알아보세요. 먼저 작업 중인 프로젝트에 해당하는 언어 탭을 선택하세요. 

[!INCLUDE [functions-bindings-code-example-chooser](../../includes/functions-bindings-code-example-chooser.md)]

## <a name="custom-bindings"></a>사용자 지정 바인딩

사용자 지정 입력 및 출력 바인딩을 만들 수 있습니다. 바인딩은 .NET으로 작성해야 하지만 모든 지원되는 언어에서 사용할 수 있습니다. 사용자 지정 바인딩을 만드는 방법은 [Creating custom input and output bindings](https://github.com/Azure/azure-webjobs-sdk/wiki/Creating-custom-input-and-output-bindings)(사용자 지정 입력 및 출력 바인딩 만들기)를 참조하세요.

## <a name="resources"></a>리소스
- [바인딩 식 및 패턴](./functions-bindings-expressions-patterns.md)
- [Azure Function 반환 값 사용](./functions-bindings-return-value.md)
- [바인딩 식 등록 방법](./functions-bindings-register.md)
- 테스트:
  - [Azure Functions에서 코드를 테스트하기 위한 전략](functions-test-a-function.md)
  - [HTTP 이외 트리거 함수를 수동으로 실행](functions-manually-run-non-http.md)
- [바인딩 오류 처리](./functions-bindings-errors.md)

## <a name="next-steps"></a>다음 단계
> [!div class="nextstepaction"]
> [Azure Functions 바인딩 확장 등록](./functions-bindings-register.md)
