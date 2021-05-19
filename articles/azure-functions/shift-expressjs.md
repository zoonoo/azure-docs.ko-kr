---
title: Express.js에서 Azure Functions로 전환
description: Express.js 엔드포인트를 Azure Functions로 리팩터링하는 방법에 대해 알아보세요.
author: craigshoemaker
ms.topic: conceptual
ms.date: 07/31/2020
ms.author: cshoe
ms.openlocfilehash: 266df5371ff5f47526fa9d6567c62e31d51ebb05
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/29/2021
ms.locfileid: "87810227"
---
# <a name="shifting-from-expressjs-to-azure-functions"></a>Express.js서 Azure Functions로 전환

Express.js는 웹 개발자들에게 가장 인기 있는 Node.js 프레임워크 중 하나이며 API 엔드포인트를 제공하는 앱 빌드에 매우 적합합니다.

코드를 서버리스 아키텍처로 마이그레이션할 때 Express.js 엔드포인트를 리팩터링하면 다음 영역에 영향을 줍니다.

- **미들웨어**: Express.js는 강력한 미들웨어 컬렉션을 제공합니다. Azure Functions 및 [Azure API Management](../api-management/api-management-key-concepts.md) 기능을 고려하면 많은 미들웨어 모듈이 더 이상 필요하지 않습니다. 엔드포인트를 마이그레이션하기 전에 필수 미들웨어에서 처리한 모든 논리를 복제하거나 바꿀 수 있는지 확인하세요.

- **상이한 API**: 요청 및 응답을 처리하는 데 사용되는 API는 Azure Functions와 Express.js에 따라 달라집니다. 다음 예에서는 필요한 변경 사항을 자세히 설명합니다.

- **기본 경로**: 기본적으로 Azure Functions 엔드포인트는 `api` 경로 아래에 표시됩니다. 회람 규칙은 [_host.json_ 파일에서 `routePrefix`](./functions-bindings-http-webhook-output.md#hostjson-settings)을 통해 구성할 수 있습니다

- **구성 및 규칙**: 함수 앱은 _function.json_ 파일을 사용하여 HTTP 동사를 정의하고 보안 정책을 정의하며 함수의 [입력 및 출력](./functions-triggers-bindings.md)을 구성할 수 있습니다. 기본적으로 함수 파일이 포함된 폴더 이름은 엔드포이늩 이름을 정의하지만 [function.json](./functions-bindings-http-webhook-trigger.md#customize-the-http-endpoint) 파일의 `route` 속성을 통해 이름을 변경할 수 있습니다.

> [!TIP]
> 대화형 튜토리얼을 통해 [Azure Functions을 사용하여 서버리스 APIs로 Node.js 및 Express APIs의 리팩터링](/learn/modules/shift-nodejs-express-apis-serverless/)에 대해 자세히 알아보세요.

## <a name="example"></a>예제

### <a name="expressjs"></a>Express.js

다음 예에서는 일반적인 Express.js `GET` 엔드포인트를 보여 줍니다.

```javascript
// server.js
app.get('/hello', (req, res) => {
  try {
    res.send("Success!");
  } catch(error) {
    const err = JSON.stringify(error);
    res.status(500).send(`Request error. ${err}`);
  }
});
```

`GET` 요청이 `/hello`에 전송되면 `Success`가 포함된 `HTTP 200` 응답이 반환됩니다. 엔드포인트에 오류가 발생하는 경우 응답은 오류 세부 정보를 포함하는 `HTTP 500`입니다.

### <a name="azure-functions"></a>Azure Functions

Azure Functions는 구성 및 코드 파일을 각 함수에 대한 단일 폴더로 구성합니다. 기본적으로 폴더 이름은 함수 이름을 지정합니다.

예를 들어 `hello` 함수에는 다음과 같은 파일을 포함하는 폴더가 있습니다.

``` files
| - hello
|  - function.json
|  - index.js
```

다음 예에서는 위의 Express.js 엔드포인트와 동일한 결과를 구현하지만 Azure Functions를 사용합니다.

# <a name="javascript"></a>[JavaScript](#tab/javascript)

```javascript
// hello/index.js
module.exports = async function (context, req) {
  try {
    context.res = { body: "Success!" };
  } catch(error) {
    const err = JSON.stringify(error);
    context.res = {
      status: 500,
      body: `Request error. ${err}`
    };
  }
};
```

# <a name="typescript"></a>[TypeScript](#tab/typescript)

```typescript
// hello/index.ts
import { AzureFunction, Context, HttpRequest } from "@azure/functions";

const httpTrigger: AzureFunction = async function (context: Context, req: HttpRequest): Promise<void> {
  try {
    context.res = { body: "Success!" };
  } catch (error) {
    const err = JSON.stringify(error);
    context.res = {
      status: 500,
      body: `Request error. ${err}`,
    };
  }
};

export default httpTrigger;
```

---

함수로 이동할 때 다음 변경 내용이 적용됩니다.

- **모듈:** 함수 코드가 JavaScript 모듈로 구현되었습니다.

- **컨텍스트 및 응답 개체**: [`context`](./functions-reference-node.md#context-object)를 사용하면 함수의 런타임과 통신할 수 있습니다. 컨텍스트에서 요청 데이터를 읽고 함수의 응답을 설정할 수 있습니다. 동기 코드는 `context.done()` 호출하여 실행을 완료할 수 있지만 `asyc` 함수는 요청을 암시적으로 해결합니다.

- **명명 규칙**: Azure Functions 파일을 포함하는 데 사용되는 폴더 이름은 기본적으로 엔드포인트 이름으로 사용됩니다(이는 [function.json](./functions-bindings-http-webhook-trigger.md#customize-the-http-endpoint)에서 재정의할 수 있음).

- **구성**:`POST` 또는 `PUT`과 같은 [ function.json](./functions-bindings-http-webhook-trigger.md#customize-the-http-endpoint) 파일에서 HTTP 동사를 정의합니다.

다음 _function.json_ 파일에는 해당 함수에 대한 구성 정보가 포함되어 있습니다.

```json
{
  "bindings": [
    {
      "authLevel": "function",
      "type": "httpTrigger",
      "direction": "in",
      "name": "req",
      "methods": ["get"]
    },
    {
      "type": "http",
      "direction": "out",
      "name": "res"
    }
  ]
}
```

`methods` 배열에서 `get`을 정의하면 HTTP `GET` 요청에 이 함수를 사용할 수 있습니다. API에서 지원 `POST` 요청을 수락하려면 배열에서 `post`를 추가할 수 있습니다.

## <a name="next-steps"></a>다음 단계

- 대화형 튜토리얼을 통해 [Azure Functions를 사용하여 서버리스 APIs로 Node.js 및 Express APIs의 리팩터링](/learn/modules/shift-nodejs-express-apis-serverless/)에 대해 자세히 알아보세요.