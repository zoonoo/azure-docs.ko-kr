---
title: 추가 하 고 Azure Logic Apps에서 Azure functions 호출
description: 추가 하 고 논리 앱에서 Azure functions 실행
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: ecfan
ms.author: estfan
ms.topic: article
ms.date: 06/04/2019
ms.reviewer: klam, LADocs
ms.openlocfilehash: 524b927ec0966199c51cdee93e920d7b847139ae
ms.sourcegitcommit: 600d5b140dae979f029c43c033757652cddc2029
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/04/2019
ms.locfileid: "66495082"
---
# <a name="call-azure-functions-from-azure-logic-apps"></a>Azure Logic Apps에서 Azure functions 호출

논리 앱에서 특정 작업을 수행 하는 코드를 실행 하려는 경우 사용 하 여 사용자 고유의 함수를 만들 수 있습니다 [Azure Functions](../azure-functions/functions-overview.md)합니다. 이 서비스를 사용 하면 Node.js를 만들어 C#, 및 F# 는 완전 한 앱 또는 코드를 실행 하는 인프라를 작성 하지 않아도 되므로 함수입니다. [Azure Functions에서 논리 앱을 호출](#call-logic-app)할 수도 있습니다. Azure Functions는 클라우드에서 서버리스 컴퓨팅을 제공하며, 다음 예제와 같은 작업을 수행하는 데 유용합니다.

* Node.js 또는 C#의 함수를 사용하여 논리 앱의 동작을 확장합니다.
* 논리 앱 워크플로에서 계산을 수행합니다.
* 논리 앱에서 고급 서식 지정 또는 계산 필드를 적용합니다.

Azure functions를 만들지 않고 코드 조각을 실행 하려면 하는 방법 [추가 하 고 인라인 코드를 실행할](../logic-apps/logic-apps-add-run-inline-code.md)합니다.

> [!NOTE]
> 현재 Logic Apps 및 Azure Functions 간의 통합은 사용 하도록 설정 하는 슬롯을 사용 하 여 작동 하지 않습니다.

## <a name="prerequisites"></a>필수 조건

* Azure 구독. Azure 구독이 없는 경우 [체험 Azure 계정에 등록](https://azure.microsoft.com/free/)합니다.

* Azure function과 함께 Azure functions에 대 한 컨테이너인 Azure 함수 앱입니다. 함수 앱이 없는 경우 [함수 앱을 먼저 만듭니다](../azure-functions/functions-create-first-azure-function.md). 만들 수 있습니다 다음 함수 논리 앱 외부 Azure portal에서 또는 [에서 논리 앱 내에서](#create-function-designer) Logic App Designer에서 합니다.

* 논리 앱에서 작업할 때 기존 또는 새 되었든 관계 없이 동일한 요구 사항을 함수 앱 및 함수에 적용 됩니다.

  * 함수 앱 및 논리 앱과 동일한 Azure 구독을 사용 해야 합니다.

  * 새 함수 앱을 런타임 스택으로.NET 또는 JavaScript를 사용 해야 합니다. 기존 함수 앱에 새 함수를 추가 하면 선택할 수 있습니다 C# 또는 JavaScript입니다.

  * 함수를 사용 하 여 **HTTP 트리거** 템플릿.

    HTTP 트리거 템플릿은 논리 앱의 `application/json` 형식을 갖는 콘텐츠를 수락할 수 있습니다. 논리 앱에 Azure 함수를 추가 하면 논리 앱 디자이너 Azure 구독 내에서이 템플릿에서 만든 사용자 지정 함수를 보여 줍니다.

  * 함수는 정의한 하지 않는 한 사용자 지정 경로 사용 하지는 [OpenAPI 정의](../azure-functions/functions-openapi-definition.md) (이전의 [Swagger 파일](https://swagger.io/)).

  * 함수에 대 한 OpenAPI 정의 있는 경우 Logic Apps 디자이너를 사용 하면 다양 한 경우 환경을 함수 매개 변수를 사용 하 여 작업 합니다. 논리 앱이 OpenAPI 정의가 포함된 함수를 찾아 액세스할 수 있도록 [먼저 다음 단계를 수행하여 함수 앱을 설정](#function-swagger)합니다.

* 논리 앱의 첫 번째 단계인 [트리거](../logic-apps/logic-apps-overview.md#logic-app-concepts)를 포함하여 함수를 추가하려는 논리 앱

  함수를 실행 하는 작업에 추가 하기 전에 논리 앱은 트리거로 시작 해야 합니다. 논리 앱을 처음 접하는 경우 [Azure Logic Apps란?](../logic-apps/logic-apps-overview.md) 및 [빠른 시작: 첫 번째 논리 앱 만들기](../logic-apps/quickstart-create-first-logic-app-workflow.md)를 검토하세요.

<a name="function-swagger"></a>

## <a name="find-functions-that-have-openapi-descriptions"></a>OpenAPI 설명이 포함 된 함수 찾기

논리 앱 디자이너에서 함수 매개 변수를 사용 하 여 작업할 때 다양 한 환경을 [OpenAPI 정의 생성](../azure-functions/functions-openapi-definition.md)이전의는 [Swagger 파일](https://swagger.io/), 함수에 대 한 합니다. 논리 앱이 Swagger 설명이 포함된 함수를 찾아 사용할 수 있도록 함수 앱을 설정하려면 다음 단계를 수행합니다.

1. 함수 앱 적극적으로 실행 되 고 있는지 확인 하십시오.

1. 함수 앱 설정 [크로스-원본 자원 공유 (CORS)](https://en.wikipedia.org/wiki/Cross-origin_resource_sharing) 는 다음이 단계를 수행 하 여 모든 원본을 허용 됩니다.

   1. **함수 앱** 목록에서 함수 앱을 선택 합니다. 오른쪽 창에서 선택 **플랫폼 기능** > **CORS**합니다.

      ![함수 앱 > "플랫폼 기능" > "CORS" 선택](./media/logic-apps-azure-functions/function-platform-features-cors.png)

   1. 아래 **CORS**, 별표를 추가 ( **`*`** ) 와일드 카드 문자를 있지만 목록에서 다른 원본을 모두 제거 하 고 선택 **저장**합니다.

      !["CORS*를 와일드카드 문자 "*"로 설정](./media/logic-apps-azure-functions/function-platform-features-cors-origins.png)

## <a name="access-property-values-inside-http-requests"></a>HTTP 요청 내에서 속성 값에 액세스

웹후크 함수는 HTTP 요청을 입력으로 수락하고 해당 요청을 다른 함수에 전달할 수 있습니다. 예를 들어, Logic Apps에는 [DateTime 값을 변환하는 함수](../logic-apps/workflow-definition-language-functions-reference.md)가 있지만 이 기본 샘플 JavaScript 함수는 함수에 전달되는 요청 개체 내의 속성에 액세스하고 해당 속성 값에 대해 작업을 수행하는 방법을 보여 줍니다. 개체 내의 속성에 액세스하기 위해 이 예제에서는 [점(.) 연산자](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Operators/Property_accessors)를 사용합니다.

```javascript
function convertToDateString(request, response){
   var data = request.body;
   response = {
      body: data.date.ToDateString();
   }
}
```

이 함수 내에서 수행되는 작업은 다음과 같습니다.

1. 이 함수는 `data` 변수를 만들고 `request` 개체 내의 `body` 개체를 해당 변수에 할당합니다. 이 함수는 점(.) 연산자를 사용하여 `request` 개체 내의 `body` 개체를 참조합니다.

   ```javascript
   var data = request.body;
   ```

1. 이 함수는 이제 `data` 변수를 통해 `date` 속성에 액세스하고, `ToDateString()` 함수를 호출하여 해당 속성 값을 DateTime 형식에서 DateString 형식으로 변환할 수 있습니다. 또한 이 함수는 다음과 같이 함수의 응답에서 `body` 속성을 통해 결과를 반환합니다.

   ```javascript
   body: data.date.ToDateString();
   ```

지금까지 Azure 함수를 만들었으므로 [논리 앱에 함수를 추가](#add-function-logic-app)하는 방법에 대한 단계를 따르세요.

<a name="create-function-designer"></a>

## <a name="create-functions-inside-logic-apps"></a>논리 앱 내에서 함수 만들기

논리 앱 디자이너를 사용 하 여 논리 앱 내에서 시작 하는 Azure 함수를 만들 수 있습니다, 전에 함수에 대 한 컨테이너인 Azure 함수 앱을 먼저 있어야 합니다. 함수 앱이 없는 경우 함수 앱을 먼저 만듭니다. [Azure Portal에서 첫 번째 함수 만들기](../azure-functions/functions-create-first-azure-function.md)를 참조하세요.

1. [Azure Portal](https://portal.azure.com)의 Logic Apps 디자이너에서 논리 앱을 엽니다.

1. 함수를 만들고 추가하려면 시나리오에 적용되는 단계를 따릅니다.

   * 논리 앱 워크플로의 마지막 단계에서 **새 단계**를 선택합니다.

   * 논리 앱 워크플로의 기존 단계 사이에서 화살표 위로 마우스를 이동한 후 더하기(+) 기호를 선택하고 **작업 추가**를 선택합니다.

1. 검색 상자에서 "azure functions"를 필터로 입력합니다. 작업 목록에서 다음 작업을 선택합니다. **Azure 함수 선택 - Azure Functions** 작업을 선택합니다.

   !["Azure 함수" 찾기](./media/logic-apps-azure-functions/find-azure-functions-action.png)

1. 함수 앱 목록에서 함수 앱을 선택합니다. 작업 목록을 연 후이 작업을 선택 합니다. **Azure Functions-새 함수 만들기**

   ![함수 앱 선택](./media/logic-apps-azure-functions/select-function-app-create-function.png)

1. 함수 정의 편집기에서 함수를 정의합니다.

   1. **함수 이름** 상자에 함수에 대한 이름을 입력합니다.

   1. 에 **코드** 상자에서 함수 실행이 완료 되 면 논리 앱에 반환 되 고 응답 페이로드를 포함 하 여 함수 템플릿 코드를 추가 합니다.

      ![함수 정의](./media/logic-apps-azure-functions/function-definition.png)

      템플릿의 코드에서 *`context` 개체*는 논리 앱이 이후 단계에서 **요청 본문** 필드를 통해 전송하는 메시지를 참조합니다. 함수 내에서 `context` 개체의 속성에 액세스하려면 다음 구문을 사용합니다.

      `context.body.<property-name>`

      예를 들어, `context` 개체 내의 `content` 속성을 참조하려면 다음 구문을 사용합니다. 

      `context.body.content`

      또한 템플릿 코드에는 함수가 해당 값에 대해 작업을 수행할 수 있도록 `data` 매개 변수의 값을 저장하는 `input` 변수도 포함되어 있습니다. JavaScript 함수 내에서 `data` 변수는 `context.body`를 빠르게 수행하는 방법이기도 합니다.

      > [!NOTE]
      > 여기서 `body` 속성은 `context` 개체에 적용되며, 함수에도 전달될 수 있는 작업의 출력에서 가져온 **Body** 토큰과 다릅니다.

   1. 작업을 완료하면 **만들기**를 선택합니다.

1. **요청 본문** 상자에 JSON (JavaScript Notation) 개체로 형식이 지정된 함수 입력을 제공합니다.

   이 입력은 *컨텍스트 개체*이거나, 논리 앱이 함수에 보내는 메시지입니다. **요청 본문** 필드를 클릭하면 이전 단계의 출력에 대한 토큰을 선택할 수 있도록 동적 콘텐츠 목록이 나타납니다. 이 예제에서는 컨텍스트 페이로드에 이메일 트리거의 **From** 토큰 값이 있는 `content`라는 속성이 포함되어 있다고 지정합니다.

   !["요청 본문" 예제 - 컨텍스트 개체 페이로드](./media/logic-apps-azure-functions/function-request-body-example.png)

   여기서 컨텍스트 개체는 문자열로 캐스팅되지 않으므로 개체의 콘텐츠는 JSON 페이로드에 직접 추가됩니다. 그러나 컨텍스트 개체가 문자열, JSON 개체 또는 JSON 배열을 전달하는 JSON 토큰이 아닌 경우 오류가 발생합니다. 따라서 이 예제에서 **Received Time** 토큰을 대신 사용하면 큰따옴표를 추가하여 컨텍스트 개체를 문자열로 캐스팅할 수 있습니다.  

   ![개체를 문자열로 캐스팅](./media/logic-apps-azure-functions/function-request-body-string-cast-example.png)

1. 메서드를 사용 하 여, 요청 헤더 또는 쿼리 매개 변수 같은 기타 세부 정보를 지정 하려면 열을 **새 매개 변수 추가** 를 나열 하 고 원하는 옵션을 선택 합니다.

<a name="add-function-logic-app"></a>

## <a name="add-existing-functions-to-logic-apps"></a>논리 앱에 기존 함수 추가

논리 앱에서 기존 Azure 함수를 호출하려면 Logic Apps 디자이너에서 다른 작업처럼 Azure 함수를 추가할 수 있습니다.

1. [Azure Portal](https://portal.azure.com)의 Logic Apps 디자이너에서 논리 앱을 엽니다.

1. 함수를 추가 하려는 단계를 선택 **새 단계**, 선택한 **액션 추가**합니다.

1. 검색 상자에서 "azure functions"를 필터로 입력합니다. 작업 목록에서 다음 작업을 선택합니다. **Azure 함수 선택 - Azure Functions** 작업을 선택합니다.

   !["Azure 함수" 찾기](./media/logic-apps-azure-functions/find-azure-functions-action.png)

1. 함수 앱 목록에서 함수 앱을 선택합니다. 함수 목록이 표시되면 함수를 선택합니다.

   ![함수 앱 및 Azure 함수 선택](./media/logic-apps-azure-functions/select-function-app-existing-function.png)

   API 정의(Swagger 설명)가 있고 [논리 앱이 해당 함수를 찾아 액세스할 수 있도록 설정된](#function-swagger) 함수에 대해 **Swagger 작업**을 선택할 수 있습니다.

   ![함수 앱, "Swagger 작업" 및 Azure 함수 선택](./media/logic-apps-azure-functions/select-function-app-existing-function-swagger.png)

1. **요청 본문** 상자에 JSON (JavaScript Notation) 개체로 형식이 지정된 함수 입력을 제공합니다.

   이 입력은 *컨텍스트 개체*이거나, 논리 앱이 함수에 보내는 메시지입니다. 클릭 합니다 **요청 본문** 이전 단계의 출력에 대 한 토큰을 선택할 수 있도록 필드에 동적 콘텐츠 목록이 나타납니다. 이 예제에서는 컨텍스트 페이로드에 이메일 트리거의 **From** 토큰 값이 있는 `content`라는 속성이 포함되어 있다고 지정합니다.

   !["요청 본문" 예제 - 컨텍스트 개체 페이로드](./media/logic-apps-azure-functions/function-request-body-example.png)

   여기서 컨텍스트 개체는 문자열로 캐스팅되지 않으므로 개체의 콘텐츠는 JSON 페이로드에 직접 추가됩니다. 그러나 컨텍스트 개체가 문자열, JSON 개체 또는 JSON 배열을 전달하는 JSON 토큰이 아닌 경우 오류가 발생합니다. 따라서 이 예제에서 **Received Time** 토큰을 대신 사용하면 큰따옴표를 추가하여 컨텍스트 개체를 문자열로 캐스팅할 수 있습니다.

   ![개체를 문자열로 캐스팅](./media/logic-apps-azure-functions/function-request-body-string-cast-example.png)

1. 메서드를 사용 하 여, 요청 헤더 또는 쿼리 매개 변수 같은 기타 세부 정보를 지정 하려면 열을 **새 매개 변수 추가** 를 나열 하 고 원하는 옵션을 선택 합니다.

<a name="call-logic-app"></a>

## <a name="call-logic-apps-from-azure-functions"></a>Azure functions에서 논리 앱 호출

Azure 함수 내에서 논리 앱을 트리거하려면 논리 앱이 호출 가능한 엔드포인트를 제공하는 트리거로 시작해야 합니다. 예를 들어, **HTTP**, **Request**, **Azure Queues** 또는 **Event Grid** 트리거로 논리 앱을 시작할 수 있습니다. 함수 내에서 트리거의 URL에 HTTP POST 요청을 전송하고 해당 논리 앱에서 처리할 페이로드를 포함합니다. 자세한 내용은 [논리 앱 호출, 트리거 또는 중첩](../logic-apps/logic-apps-http-endpoint.md)을 참조하세요.

## <a name="next-steps"></a>다음 단계

* [Logic Apps 커넥터](../connectors/apis-list.md)에 대해 알아봅니다.
