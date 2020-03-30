---
title: Azure Functions의 트리거 및 바인딩
description: 트리거 및 바인딩을 사용하여 Azure Function을 온라인 이벤트 및 클라우드 기반 서비스에 연결하는 방법을 알아봅니다.
author: craigshoemaker
ms.topic: reference
ms.date: 02/18/2019
ms.author: cshoe
ms.openlocfilehash: d41fd7f66ecef3a563345424d7dc4366e47d3f0e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79276505"
---
# <a name="azure-functions-triggers-and-bindings-concepts"></a>Azure Functions 트리거 및 바인딩 개념

이 문서에서는 함수 트리거 및 바인딩을 둘러싼 상위 수준 개념을 배웁니다.

트리거는 함수가 실행되는 원인입니다. 트리거는 함수가 호출되는 방법을 정의하고 함수에 정확히 하나의 트리거가 있어야 합니다. 트리거에는 관련 데이터가 있으며, 일반적으로 함수의 페이로드로 제공됩니다. 

함수에 바인딩하는 것은 다른 리소스를 함수에 선언적으로 연결하는 방법입니다. 바인딩은 *입력 바인딩,* 출력 바인딩 또는 둘 다로 연결될 수 *있습니다.* 바인딩의 데이터는 함수에 매개 변수로 제공됩니다.

필요에 맞게 다른 바인딩과 혼합하고 일치시킬 수 있습니다. 바인딩은 선택 사항이며 함수는 여러 개의 입력 및 출력 바인딩을 가질 수 있습니다.

트리거 및 바인딩을 사용하면 다른 서비스에 대한 하드코딩 액세스를 방지할 수 있습니다. 함수는 함수 매개 변수에서 데이터를 수신합니다(예: 큐 메시지의 콘텐츠). 함수의 반환 값을 사용하여 데이터를 보냅니다(예를 들어 큐 메시지를 만들기 위해). 

다른 함수를 구현하는 방법에 대한 다음 예제를 고려하십시오.

| 예제 시나리오 | 트리거 | 입력 바인딩 | 출력 바인딩 |
|-------------|---------|---------------|----------------|
| 다른 큐에 쓸 함수를 실행하는 새 큐 메시지가 도착합니다. | 큐<sup>*</sup> | *없음* | 큐<sup>*</sup> |
|예약된 작업은 Blob 저장소 내용을 읽고 새 Cosmos DB 문서를 만듭니다. | Timer | Blob Storage | Cosmos DB |
|이벤트 그리드는 Blob 스토리지의 이미지와 Cosmos DB의 문서를 읽고 이메일을 보내는 데 사용됩니다. | Event Grid | Blob 스토리지 및 코스모스 DB | SendGrid |
| Excel 시트를 업데이트하기 위해 Microsoft 그래프를 사용하는 웹후크입니다. | HTTP | *없음* | Microsoft Graph |

<sup>\*</sup>다른 큐를 나타냅니다.

이러한 예제는 전체적인 것이 아니라 트리거와 바인딩을 함께 사용하는 방법을 설명하기 위해 제공됩니다.

###  <a name="trigger-and-binding-definitions"></a>트리거 및 바인딩 정의

트리거 및 바인딩은 개발 접근 방식에 따라 다르게 정의됩니다.

| 플랫폼 | 트리거 및 바인딩은... |
|-------------|--------------------------------------------|
| C# 클래스 라이브러리 | &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;C# 속성으로 메서드 및 매개 변수 장식 |
| 기타 모든(Azure 포털 포함) | &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;업데이트 [function.json](./functions-reference.md) [(스키마)](http://json.schemastore.org/function) |

포털은 이 구성에 대한 UI를 제공하지만 함수의 **통합** 탭을 통해 사용할 수 있는 **고급 편집기열기를** 통해 파일을 직접 편집할 수 있습니다.

.NET에서 매개 변수 형식은 입력 데이터에 대한 데이터 형식을 정의합니다. 예를 들어 `string` 큐 트리거의 텍스트, 바이너리로 읽을 바이트 배열 및 개체에 대한 직렬화를 해제하는 사용자 지정 형식에 바인딩하는 데 사용합니다.

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
- 일부 바인딩은 특수 방향인 `inout`을 사용합니다. 을 사용하는 `inout`경우 포털의 **통합** 탭을 통해 **고급 편집기만** 사용할 수 있습니다.

[클래스 라이브러리의 특성](functions-dotnet-class-library.md)을 사용하여 트리거 및 바인딩을 구성하는 경우 방향은 특성 생성자에서 제공되거나 매개 변수 형식에서 유추됩니다.

## <a name="supported-bindings"></a>지원되는 바인딩

[!INCLUDE [Full bindings table](../../includes/functions-bindings.md)]

미리 보기 상태 바인딩 또는 프로덕션 용도로 승인된 바인딩에 대한 자세한 내용은 [지원되는 언어](supported-languages.md)를 참조하세요.

## <a name="resources"></a>리소스
- [바인딩 식 및 패턴](./functions-bindings-expressions-patterns.md)
- [Azure 함수 반환 값 사용](./functions-bindings-return-value.md)
- [바인딩 식을 등록하는 방법](./functions-bindings-register.md)
- 테스트:
  - [Azure Functions에서 코드를 테스트하기 위한 전략](functions-test-a-function.md)
  - [HTTP 이외 트리거 함수를 수동으로 실행](functions-manually-run-non-http.md)
- [바인딩 오류 처리](./functions-bindings-errors.md)

## <a name="next-steps"></a>다음 단계
> [!div class="nextstepaction"]
> [Azure 함수 바인딩 확장 등록](./functions-bindings-register.md)
