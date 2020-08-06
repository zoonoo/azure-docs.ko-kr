---
title: Express.js에서 Azure Functions로 이동
description: Azure Functions Express.js 끝점을 리팩터링 하는 방법을 알아봅니다.
author: craigshoemaker
ms.topic: conceptual
ms.date: 07/31/2020
ms.author: cshoe
ms.openlocfilehash: 266df5371ff5f47526fa9d6567c62e31d51ebb05
ms.sourcegitcommit: 85eb6e79599a78573db2082fe6f3beee497ad316
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/05/2020
ms.locfileid: "87810227"
---
# <a name="shifting-from-expressjs-to-azure-functions"></a>Express.js에서 Azure Functions로 이동

Express.js는 웹 개발자를 위한 가장 인기 있는 Node.js 프레임 워크 중 하나 이며 API 끝점을 제공 하는 앱을 빌드하는 데 매우 적합 합니다.

서버를 사용 하지 않는 아키텍처로 코드를 마이그레이션하는 경우 리팩터링 Express.js 끝점은 다음 영역에 영향을 줍니다.

- **미들웨어**: Express.js은 강력한 미들웨어 컬렉션을 갖추고 있습니다. 많은 미들웨어 모듈은 Azure Functions 및 [Azure API Management](../api-management/api-management-key-concepts.md) 기능에 더 이상 필요 하지 않습니다. 끝점을 마이그레이션하기 전에 필수 미들웨어에 의해 처리 되는 논리를 복제 하거나 바꿀 수 있는지 확인 합니다.

- **서로 다른 api**: 요청 및 응답을 모두 처리 하는 데 사용 되는 api는 Azure Functions와 Express.js 간에 다릅니다. 다음 예에서는 필요한 변경 내용을 자세히 설명 합니다.

- **기본 경로**: 기본적으로 Azure Functions 끝점은 경로 아래에 노출 됩니다 `api` . 라우팅 규칙은 [ `routePrefix` 파일 _의host.js_ ](./functions-bindings-http-webhook-output.md#hostjson-settings)를 통해 구성할 수 있습니다.

- **구성 및 규칙**: 함수 앱은 파일 _에function.js_ 를 사용 하 여 HTTP 동사를 정의 하 고, 보안 정책을 정의 하 고, 함수의 [입력 및 출력](./functions-triggers-bindings.md)을 구성할 수 있습니다. 기본적으로 함수 파일을 포함 하는 폴더 이름은 끝점 이름을 정의 하지만 `route` 파일 [의function.js](./functions-bindings-http-webhook-trigger.md#customize-the-http-endpoint) 에서 속성을 통해 이름을 변경할 수 있습니다.

> [!TIP]
> 대화형 자습서 [를 통해 Node.js 및 Express api를 사용 하 여 서버를 사용 하지 않는 api Azure Functions에](/learn/modules/shift-nodejs-express-apis-serverless/)대해 자세히 알아보세요.

## <a name="example"></a>예제

### <a name="expressjs"></a>Express.js

다음 예제에서는 일반적인 Express.js 끝점을 보여 줍니다 `GET` .

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

`GET`에 요청을 보내면 `/hello` `HTTP 200` 가 포함 된 응답이 `Success` 반환 됩니다. 끝점에 오류가 발생 하는 경우 응답은 `HTTP 500` 오류 세부 정보를 포함 하는입니다.

### <a name="azure-functions"></a>Azure Functions

Azure Functions는 각 함수에 대 한 단일 폴더에 구성 및 코드 파일을 구성 합니다. 기본적으로 폴더 이름은 함수 이름을 결정 합니다.

예를 들어 라는 함수에는 `hello` 다음과 같은 파일을 포함 하는 폴더가 있습니다.

``` files
| - hello
|  - function.json
|  - index.js
```

다음 예제에서는 위의 Express.js 끝점과 동일한 결과를 구현 하지만 Azure Functions를 사용 합니다.

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

함수로 이동할 때 다음 변경 내용이 적용 됩니다.

- **모듈:** 함수 코드는 JavaScript 모듈로 구현 됩니다.

- **컨텍스트 및 응답 개체**:를 [`context`](./functions-reference-node.md#context-object) 사용 하 여 함수의 런타임과 통신할 수 있습니다. 컨텍스트에서 요청 데이터를 읽고 함수의 응답을 설정할 수 있습니다. 동기 코드에서는를 호출 하 여 `context.done()` 실행을 완료 하 고 `asyc` 함수는 요청을 암시적으로 해결 해야 합니다.

- **명명 규칙**: Azure Functions 파일을 포함 하는 데 사용 되는 폴더 이름은 기본적으로 끝점 이름으로 사용 됩니다 .이는 [function.js](./functions-bindings-http-webhook-trigger.md#customize-the-http-endpoint)에서 재정의할 수 있습니다.

- **구성**: 또는와 같은 파일 [의function.js](./functions-bindings-http-webhook-trigger.md#customize-the-http-endpoint) 에서 HTTP 동사를 정의 합니다 `POST` `PUT` .

다음 파일 _function.js_ 는 함수에 대 한 구성 정보를 포함 합니다.

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

배열에서를 정의 하 여 `get` `methods` HTTP 요청에 함수를 사용할 수 `GET` 있습니다. API에서 지원 요청을 수락 하도록 하려면 `POST` 배열에도를 추가할 수 있습니다 `post` .

## <a name="next-steps"></a>다음 단계

- 대화형 자습서 [를 통해 Node.js 및 Express api를 사용 하 여 서버를 사용 하지 않는 api에](/learn/modules/shift-nodejs-express-apis-serverless/) 대해 자세히 알아보세요 Azure Functions