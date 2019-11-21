---
title: JavaScript developer reference for Azure Functions
description: JavaScript를 사용하여 함수를 개발하는 방법을 알아봅니다.
ms.assetid: 45dedd78-3ff9-411f-bb4b-16d29a11384c
ms.topic: reference
ms.date: 02/24/2019
ms.openlocfilehash: b6b7db4c5f13a264b76dcab02dba51c464297307
ms.sourcegitcommit: d6b68b907e5158b451239e4c09bb55eccb5fef89
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/20/2019
ms.locfileid: "74226717"
---
# <a name="azure-functions-javascript-developer-guide"></a>Azure Functions JavaScript 개발자 가이드

이 가이드에는 JavaScript로 Azure Functions를 작성하는 복잡성에 대한 정보가 포함되어 있습니다.

JavaScript 함수는 트리거될 때 실행되는 내보낸 `function`입니다([트리거는 function.json에서 구성됨](functions-triggers-bindings.md)). The first argument passed to every function is a `context` object, which is used for receiving and sending binding data, logging, and communicating with the runtime.

이 문서에서는 [Azure Functions 개발자 참조](functions-reference.md)를 이미 읽었다고 가정합니다. Complete the Functions quickstart to create your first function, using [Visual Studio Code](functions-create-first-function-vs-code.md) or [in the portal](functions-create-first-azure-function.md).

This article also supports [TypeScript app development](#typescript).

## <a name="folder-structure"></a>폴더 구조

JavaScript 프로젝트에 필요한 폴더 구조는 다음과 같습니다. 이 기본값은 변경 가능합니다. 자세한 내용은 아래의 [scriptFile](#using-scriptfile) 섹션을 참조하세요.

```
FunctionsProject
 | - MyFirstFunction
 | | - index.js
 | | - function.json
 | - MySecondFunction
 | | - index.js
 | | - function.json
 | - SharedCode
 | | - myFirstHelperFunction.js
 | | - mySecondHelperFunction.js
 | - node_modules
 | - host.json
 | - package.json
 | - extensions.csproj
```

프로젝트 루트에는 함수 앱을 구성하는 데 사용할 수 있는 공유 [host.json](functions-host-json.md) 파일이 있습니다. 각 함수에는 자체 코드 파일(.js)과 바인딩 구성 파일(function.json)이 있는 폴더가 있습니다. `function.json`의 부모 디렉터리 이름은 항상 함수의 이름입니다.

Functions 런타임의 [버전 2.x](functions-versions.md)에 필요한 바인딩 확장은 `extensions.csproj` 파일에 정의되어 있고 실제 라이브러리 파일은 `bin` 폴더에 있습니다. 로컬에서 개발할 때는 [바인딩 확장을 등록](./functions-bindings-register.md#extension-bundles)해야 합니다. Azure Portal에서 함수를 개발할 때 등록이 알아서 수행됩니다.

## <a name="exporting-a-function"></a>함수 내보내기

JavaScript 함수는 [`module.exports`](https://nodejs.org/api/modules.html#modules_module_exports)(또는 [`exports`](https://nodejs.org/api/modules.html#modules_exports))를 통해 내보내야 합니다. 내보낸 함수는 트리거될 때 실행되는 JavaScript 함수여야 합니다.

기본적으로 Functions 런타임은 `index.js`에서 함수를 찾습니다. 여기서 `index.js`는 해당하는 `function.json`과 동일한 부모 디렉터리를 공유합니다. 기본적인 경우 내보낸 함수는 `run` 또는 `index`라는 해당 파일 또는 내보내기의 유일한 내보내기여야 합니다. 함수의 파일 위치 및 내보내기 이름을 구성하려면 아래에서 [함수의 진입점 구성](functions-reference-node.md#configure-function-entry-point)에 대한 내용을 읽어보세요.

내보낸 함수는 실행에서 인수의 수로 전달됩니다. 사용하는 첫 번째 인수는 항상 `context` 개체입니다. If your function is synchronous (doesn't return a Promise), you must pass the `context` object, as calling `context.done` is required for correct use.

```javascript
// You should include context, other arguments are optional
module.exports = function(context, myTrigger, myInput, myOtherInput) {
    // function logic goes here :)
    context.done();
};
```

### <a name="exporting-an-async-function"></a>비동기 함수 내보내기
Functions 런타임 2.x 버전에서 [`async function`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Statements/async_function) 선언 또는 일반 JavaScript [Promises](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Promise)를 사용할 때 함수가 완료되었음을 나타내는 [`context.done`](#contextdone-method) 콜백을 명시적으로 호출할 필요가 없습니다. 내보낸 비동기 함수/Promise가 완료되면 함수가 완료됩니다. 버전 1.x 런타임을 대상으로 하는 함수의 경우 코드 실행이 완료되면 [`context.done`](#contextdone-method)을 호출해야 합니다.

다음 예제는 트리거되었으며 즉시 실행을 완료한다고 기록하는 간단한 함수입니다.

```javascript
module.exports = async function (context) {
    context.log('JavaScript trigger function processed a request.');
};
```

비동기 함수를 내보낼 때는 `return` 값을 사용하도록 출력 바인딩을 구성할 수도 있습니다. 하나의 출력 바인딩이 있는 경우에 권장됩니다.

`return`을 사용하여 출력을 할당하려면 `function.json`에서 `name` 속성을 `$return`으로 변경합니다.

```json
{
  "type": "http",
  "direction": "out",
  "name": "$return"
}
```

이 경우에 함수는 다음 예제와 유사합니다.

```javascript
module.exports = async function (context, req) {
    context.log('JavaScript HTTP trigger function processed a request.');
    // You can call and await an async method here
    return {
        body: "Hello, world!"
    };
}
```

## <a name="bindings"></a>바인딩 
JavaScript에서 [바인딩](functions-triggers-bindings.md)은 함수의 function.json에서 구성되고 정의됩니다. Functions는 다양한 방법으로 바인딩과 상호 작용합니다.

### <a name="inputs"></a>입력
입력은 Azure Functions에서 두 가지 범주로 나뉩니다. 즉 하나는 트리거 입력이고, 다른 하나는 추가 입력입니다. 트리거 및 기타 입력 바인딩(`direction === "in"`의 바인딩)은 다음과 같은 세 가지 방법으로 함수에서 읽을 수 있습니다.
 - **_[권장]_  함수에 전달된 매개 변수입니다.** 이러한 항목은 *function.json*에 정의된 순서대로 함수에 전달됩니다. The `name` property defined in *function.json* does not need to match the name of your parameter, although it should.
 
   ```javascript
   module.exports = async function(context, myTrigger, myInput, myOtherInput) { ... };
   ```
   
 - **[`context.bindings`](#contextbindings-property) 개체의 모든 멤버입니다.** 각 멤버는 *function.json*에서 정의된 `name` 속성으로 이름이 지정됩니다.
 
   ```javascript
   module.exports = async function(context) { 
       context.log("This is myTrigger: " + context.bindings.myTrigger);
       context.log("This is myInput: " + context.bindings.myInput);
       context.log("This is myOtherInput: " + context.bindings.myOtherInput);
   };
   ```
   
 - **입력으로 JavaScript[`arguments`](https://msdn.microsoft.com/library/87dw3w1k.aspx) 개체를 사용합니다.** 그러면 기본적으로 입력 매개 변수로 전달하는 것과 동일하지만 동적으로 입력을 처리할 수 있습니다.
 
   ```javascript
   module.exports = async function(context) { 
       context.log("This is myTrigger: " + arguments[1]);
       context.log("This is myInput: " + arguments[2]);
       context.log("This is myOtherInput: " + arguments[3]);
   };
   ```

### <a name="outputs"></a>outputs
출력(`direction === "out"`의 바인딩)은 다양한 방법으로 함수에서 작성될 수 있습니다. 모든 경우에 *function.json*에 정의된 대로 바인딩의 `name` 속성은 함수에서 작성된 개체 멤버의 이름에 해당합니다. 

You can assign data to output bindings in one of the following ways (don't combine these methods):

- **_[여러 출력에 대한 권장]_ 개체를 반환합니다.** If you are using an async/Promise returning function, you can return an object with assigned output data. 아래 예제에서 출력 바인딩의 이름은 *function.json*에서 "httpResponse" 및 "queueOutput"으로 지정됩니다.

  ```javascript
  module.exports = async function(context) {
      let retMsg = 'Hello, world!';
      return {
          httpResponse: {
              body: retMsg
          },
          queueOutput: retMsg
      };
  };
  ```

  동기 함수를 사용하는 경우 [`context.done`](#contextdone-method)을 사용하여 이 개체를 반환할 수 있습니다(예제 참조).
- **_[단일 출력에 대한 권장]_ 직접 값을 반환하고 $return 바인딩 이름을 사용합니다.** 함수를 반환하는 비동기/Promise에서 작동합니다. [비동기 함수 내보내기](#exporting-an-async-function)에서 예제를 참조하세요. 
- **`context.bindings`에 대한 값을 할당합니다.** context.bindings에 직접 값을 할당할 수 있습니다.

  ```javascript
  module.exports = async function(context) {
      let retMsg = 'Hello, world!';
      context.bindings.httpResponse = {
          body: retMsg
      };
      context.bindings.queueOutput = retMsg;
      return;
  };
  ```

### <a name="bindings-data-type"></a>바인딩 데이터 형식

입력 바인딩에 대한 데이터 형식을 정의하려면 바인딩 정의에서 `dataType` 속성을 사용합니다. 예를 들어 이진 형식의 HTTP 요청 내용을 읽으려면 `binary` 형식을 사용합니다.

```json
{
    "type": "httpTrigger",
    "name": "req",
    "direction": "in",
    "dataType": "binary"
}
```

`dataType`에 대한 옵션은 `binary`, `stream` 및 `string`입니다.

## <a name="context-object"></a>context 개체
런타임은 함수로 데이터를 전달하거나 전달받으며 사용자가 런타임과 통신할 수 있도록 하는 `context` 개체를 사용합니다. 내보낸 함수가 동기화된 경우 컨텍스트 개체는 바인딩에서 데이터를 읽고 설정하고, 로그를 작성하고, `context.done` 콜백을 사용하는 데 사용될 수 있습니다.

`context` 개체는 항상 함수의 첫 번째 매개 변수입니다. `context.done` 및 `context.log`와 같은 중요한 메서드가 있으므로 포함되어야 합니다. 원하는 개체 이름(예: `ctx` 또는 `c`)을 지정할 수 있습니다.

```javascript
// You must include a context, but other arguments are optional
module.exports = function(ctx) {
    // function logic goes here :)
    ctx.done();
};
```

### <a name="contextbindings-property"></a>context.bindings 속성

```js
context.bindings
```

Returns a named object that is used to read or assign binding data. Input and trigger binding data can be accessed by reading properties on `context.bindings`. Output binding data can be assigned by adding data to `context.bindings`

예를 들어 function.json의 다음 바인딩 정의를 통해 `context.bindings.myInput`에서 큐의 콘텐츠에 액세스하고 `context.bindings.myOutput`을 사용하여 출력을 큐에 할당할 수 있습니다.

```json
{
    "type":"queue",
    "direction":"in",
    "name":"myInput"
    ...
},
{
    "type":"queue",
    "direction":"out",
    "name":"myOutput"
    ...
}
```

```javascript
// myInput contains the input data, which may have properties such as "name"
var author = context.bindings.myInput.name;
// Similarly, you can set your output data
context.bindings.myOutput = { 
        some_text: 'hello world', 
        a_number: 1 };
```

`context.binding` 개체 대신 `context.done` 메서드를 사용하여 출력 바인딩 데이터를 정의하도록 선택할 수 있습니다(아래 참조).

### <a name="contextbindingdata-property"></a>context.bindingData property

```js
context.bindingData
```

트리거 메타데이터 및 함수 호출 데이터(`invocationId`, `sys.methodName`, `sys.utcNow`, `sys.randGuid`)를 포함하는 명명된 개체를 반환합니다. 트리거 메타데이터의 예제는 [event hubs example](functions-bindings-event-hubs.md#trigger---javascript-example)을 참조하세요.

### <a name="contextdone-method"></a>context.done 메서드

```js
context.done([err],[propertyBag])
```

런타임에서는 코드가 완료되었음을 알 수 있습니다. 함수가 [`async function`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Statements/async_function) 선언을 사용하는 경우 `context.done()`을 사용할 필요가 없습니다. `context.done` 콜백을 암시적으로 호출합니다. 비동기 함수는 Node 8 이상 버전에서 지원되며 Functions 런타임의 2.x 버전이 필요합니다.

함수가 비동기 함수가 아닌 경우 함수가 완료됐음을 런타임에 알리려면 `context.done`을 **반드시 호출해야** 합니다. 실행이 누락된 경우 시간 초과가 발생합니다.

`context.done` 메서드를 사용하면 출력 바인딩 데이터가 포함된 JSON 개체와 런타임에 사용자 정의 오류를 다시 전달할 수 있습니다. `context.done`에 전달된 속성은 `context.bindings` 개체에 설정된 내용을 덮어씁니다.

```javascript
// Even though we set myOutput to have:
//  -> text: 'hello world', number: 123
context.bindings.myOutput = { text: 'hello world', number: 123 };
// If we pass an object to the done function...
context.done(null, { myOutput: { text: 'hello there, world', noNumber: true }});
// the done method overwrites the myOutput binding to be: 
//  -> text: 'hello there, world', noNumber: true
```

### <a name="contextlog-method"></a>context.log 메서드  

```js
context.log(message)
```

기본 추적 수준에서 스트리밍 함수 로그에 기록할 수 있습니다. `context.log`에 다른 추적 수준에서 함수 로그를 작성할 수 있는 추가 로깅 메서드가 제공됩니다.


| 방법                 | 설명                                |
| ---------------------- | ------------------------------------------ |
| **error(_message_)**   | 오류 수준 로깅 또는 더 낮은 수준의 로깅에 씁니다.   |
| **warn(_message_)**    | 경고 수준 로깅 또는 더 낮은 수준의 로깅에 씁니다. |
| **info(_message_)**    | 정보 수준 로깅 또는 더 낮은 수준의 로깅에 씁니다.    |
| **verbose(_message_)** | 자세한 정보 표시 수준 로깅에 씁니다.           |

다음 예제는 경고 추적 수준에서 로그를 기록합니다.

```javascript
context.log.warn("Something has happened."); 
```

host.json 파일에 [로깅에 대한 추적 수준 임계값을 구성](#configure-the-trace-level-for-console-logging)할 수 있습니다. 로그 작성에 대한 자세한 내용은 아래에서 [추적 출력 작성](#writing-trace-output-to-the-console)을 참조하세요.

함수 로그 보기 및 쿼리에 대해 자세히 알아보려면 [Azure Functions 모니터링](functions-monitoring.md)을 읽어보세요.

## <a name="writing-trace-output-to-the-console"></a>콘솔에 추적 출력 작성 

Functions에서 `context.log` 메서드를 사용하여 추적 출력을 콘솔에 씁니다. Functions v2.x에서 `console.log`를 사용하는 추적 출력은 함수 앱 수준에서 캡처됩니다. This means that outputs from `console.log` are not tied to a specific function invocation and aren't displayed in a specific function's logs. 하지만 Application Insights로 전파됩니다. Functions v1.x에서는 `console.log`를 사용하여 콘솔에 쓸 수 없습니다.

`context.log()`를 호출하면 메시지를 _정보_ 추적 수준인 기본 추적 수준에서 콘솔에 씁니다. 다음 코드는 정보 추적 수준에서 콘솔에 씁니다.

```javascript
context.log({hello: 'world'});  
```

이 코드는 위의 코드와 같습니다.

```javascript
context.log.info({hello: 'world'});  
```

이 코드는 오류 수준에서 콘솔에 기록합니다.

```javascript
context.log.error("An error has occurred.");  
```

_error_(오류)가 가장 높은 추적 수준이므로 로깅이 활성화되어 있는 한 이 추적은 모든 추적 수준에서 출력에 씁니다.

모든 `context.log` 메서드는 Node.js [util.format 메서드](https://nodejs.org/api/util.html#util_util_format_format)에서 지원하는 것과 동일한 매개 변수 형식을 지원합니다. 기본 추적 수준을 사용하여 함수 로그를 작성하는 다음 코드를 살펴보세요.

```javascript
context.log('Node.js HTTP trigger function processed a request. RequestUri=' + req.originalUrl);
context.log('Request Headers = ' + JSON.stringify(req.headers));
```

또한 동일한 코드를 다음과 같은 형식으로 작성할 수도 있습니다.

```javascript
context.log('Node.js HTTP trigger function processed a request. RequestUri=%s', req.originalUrl);
context.log('Request Headers = ', JSON.stringify(req.headers));
```

### <a name="configure-the-trace-level-for-console-logging"></a>콘솔 로깅에 대한 추적 수준 구성

Functions 1.x에서는 콘솔에 작성할 임계값 추적 수준을 정의할 수 있으므로 추적이 함수에서 콘솔에 작성되는 방식을 쉽게 제어할 수 있습니다. 콘솔에 기록되는 모든 추적에 대한 임계값을 설정하려면 host.json 파일의 `tracing.consoleLevel` 속성을 사용합니다. 이 설정은 함수 앱의 모든 함수에 적용됩니다. 다음 예제에서는 추적 임계값을 설정하여 자세한 정보 표시 로깅을 사용하도록 설정합니다.

```json
{
    "tracing": {
        "consoleLevel": "verbose"
    }
}  
```

**consoleLevel**의 값은 `context.log` 메서드의 이름에 해당합니다. 콘솔에 대한 모든 추적 로깅을 사용하지 않으려면 **consoleLevel**을 _off_로 설정합니다. 자세한 내용은 [host.json 참조](functions-host-json-v1.md)를 참조하세요.

## <a name="http-triggers-and-bindings"></a>HTTP 트리거 및 바인딩

HTTP, 웹후크 트리거 및 HTTP 출력 바인딩은 요청 및 응답 개체를 사용하여 HTTP 메시지를 나타냅니다.  

### <a name="request-object"></a>요청 개체

`context.req`(요청) 개체의 속성은 다음과 같습니다.

| 자산      | 설명                                                    |
| ------------- | -------------------------------------------------------------- |
| _body_        | 요청의 본문을 포함하는 개체입니다.               |
| _headers_     | 요청 헤더를 포함하는 개체입니다.                   |
| _method_      | 요청의 HTTP 메서드입니다.                                |
| _originalUrl_ | 요청의 URL입니다.                                        |
| _params_      | 요청의 라우팅 매개 변수를 포함하는 개체입니다. |
| _query_       | 쿼리 매개 변수를 포함하는 개체입니다.                  |
| _rawBody_     | 문자열 형식의 메시지 본문입니다.                           |


### <a name="response-object"></a>응답 개체

`context.res`(응답) 개체의 속성은 다음과 같습니다.

| 자산  | 설명                                               |
| --------- | --------------------------------------------------------- |
| _body_    | 응답의 본문을 포함하는 개체입니다.         |
| _headers_ | 응답 헤더를 포함하는 개체입니다.             |
| _isRaw_   | 응답에 대한 서식 지정을 건너뜀을 나타냅니다.    |
| _status_  | 응답의 HTTP 상태 코드입니다.                     |

### <a name="accessing-the-request-and-response"></a>요청 및 응답 액세스 

HTTP 트리거로 작업할 때 여러 가지 방법으로 HTTP 요청 및 응답 개체에 액세스할 수 있습니다.

+ **`context` 개체의 `req` 및 `res` 속성에서.** 이러한 방식으로 전체 `context.bindings.name` 패턴을 사용하지 않고 대신 기존 패턴을 사용하여 context 개체에서 HTTP 데이터에 액세스할 수 있습니다. 다음 예제에서는 `context`의 `req` 및 `res` 개체에 액세스하는 방법을 보여 줍니다.

    ```javascript
    // You can access your http request off the context ...
    if(context.req.body.emoji === ':pizza:') context.log('Yay!');
    // and also set your http response
    context.res = { status: 202, body: 'You successfully ordered more coffee!' }; 
    ```

+ **명명된 입출력 바인딩에서.** 이러한 방식으로 HTTP 트리거와 바인딩은 다른 바인딩과 동일하게 작동합니다. 다음 예제에서는 명명된 `response` 바인딩을 사용하여 응답 개체를 설정합니다. 

    ```json
    {
        "type": "http",
        "direction": "out",
        "name": "response"
    }
    ```
    ```javascript
    context.bindings.response = { status: 201, body: "Insert succeeded." };
    ```
+ **_[응답 전용]_ `context.res.send(body?: any)`를 호출합니다.** HTTP 응답은 입력 `body`를 응답 본문으로 작성합니다. `context.done()`은 암시적으로 호출됩니다.

+ **_[응답 전용]_ `context.done()`를 호출합니다.** A special type of HTTP binding returns the response that is passed to the `context.done()` method. 다음 HTTP 출력 바인딩은 `$return` 출력 매개 변수를 정의합니다.

    ```json
    {
      "type": "http",
      "direction": "out",
      "name": "$return"
    }
    ``` 
    ```javascript
     // Define a valid response object.
    res = { status: 201, body: "Insert succeeded." };
    context.done(null, res);   
    ```  

## <a name="node-version"></a>노드 버전

다음 표에서는 주 버전의 Functions 런타임 각각에서 사용되는 Node.js 버전을 보여 줍니다.

| Functions 버전 | Node.js 버전 | 
|---|---|
| 1.x | 6.11.2(런타임에 의해 잠김) |
| 2.x  | _Active LTS_ and _Maintenance LTS_ Node.js versions (~10 recommended). Target the version in Azure by setting the WEBSITE_NODE_DEFAULT_VERSION [app setting](functions-how-to-use-azure-function-app-settings.md#settings) to `~10`.|

위의 앱 설정을 확인하거나 함수에서 `process.version`을 인쇄하여 런타임에 사용 중인 현재 버전을 확인할 수 있습니다.

## <a name="dependency-management"></a>종속성 관리
아래 예제와 같이 JavaScript 코드에서 커뮤니티 라이브러리를 사용하려면, Azure의 함수 앱에 모든 종속성이 설치되어 있는지 확인해야 합니다.

```javascript
// Import the underscore.js library
var _ = require('underscore');
var version = process.version; // version === 'v6.5.0'

module.exports = function(context) {
    // Using our imported underscore.js library
    var matched_names = _
        .where(context.bindings.myInput.names, {first: 'Carla'});
```

> [!NOTE]
> 함수 앱의 루트에 `package.json` 파일을 정의해야 합니다. 파일을 정의하면 앱의 모든 함수에서 동일한 캐시된 패키지를 공유할 수 있으므로 최상의 성능을 제공합니다. 버전 충돌이 발생하는 경우 특정 함수의 폴더에 `package.json` 파일을 추가하여 이 충돌을 해결할 수 있습니다.  

원본 제어에서 함수 앱을 배포할 때 리포지토리에 있는 모든 `package.json` 파일이 배포 중에 폴더의 `npm install`을 트리거합니다. 그러나 포털 또는 CLI를 통해 배포할 때는 수동으로 패키지를 설치해야 합니다.

함수 앱에 패키지를 설치하는 방법에는 두 가지가 있습니다. 

### <a name="deploying-with-dependencies"></a>종속성을 사용하여 배포
1. `npm install`을 실행하여 모든 필수 패키지를 로컬에 설치합니다.

2. 코드를 배포하고 `node_modules` 폴더가 배포에 포함되어 있는지 확인합니다. 


### <a name="using-kudu"></a>Kudu 사용
1. `https://<function_app_name>.scm.azurewebsites.net`로 이동합니다.

2. **디버그 콘솔** > **CMD**를 클릭합니다.

3. `D:\home\site\wwwroot`로 이동한 다음 package.json 파일을 페이지 위쪽의 **wwwroot** 폴더로 끌어갑니다.  
    다른 방법으로 함수 앱에 파일을 업로드할 수도 있습니다. 자세한 내용은 [함수 앱 파일을 업데이트하는 방법](functions-reference.md#fileupdate)을 참조하세요. 

4. package.json 파일을 업로드한 후 **Kudu 원격 실행 콘솔**에서 `npm install` 명령을 실행합니다.  
    이 작업은 package.json 파일에 표시된 패키지를 다운로드하고 함수 앱을 다시 시작합니다.

## <a name="environment-variables"></a>환경 변수

Functions에서 [앱 설정](functions-app-settings.md)(예: 서비스 연결 문자열)은 실행 중에 환경 변수로 노출됩니다. You can access these settings using `process.env`, as shown here in the second and third calls to `context.log()` where we log the `AzureWebJobsStorage` and `WEBSITE_SITE_NAME` environment variables:

```javascript
module.exports = async function (context, myTimer) {
    var timeStamp = new Date().toISOString();

    context.log('Node.js timer trigger function ran!', timeStamp);
    context.log("AzureWebJobsStorage: " + process.env["AzureWebJobsStorage"]);
    context.log("WEBSITE_SITE_NAME: " + process.env["WEBSITE_SITE_NAME"]);
};
```

[!INCLUDE [Function app settings](../../includes/functions-app-settings.md)]

로컬로 실행하는 경우 앱 설정은 [local.settings.json](functions-run-local.md#local-settings-file) 프로젝트 파일에서 읽습니다.

## <a name="configure-function-entry-point"></a>함수 진입점 구성

`function.json` 속성과 `scriptFile` 및 `entryPoint`는 내보낸 함수의 위치와 이름을 구성하는 데 사용할 수 있습니다. 이러한 속성은 JavaScript가 트랜스파일된 경우에 중요할 수 있습니다.

### <a name="using-scriptfile"></a>`scriptFile` 사용

기본적으로 JavaScript 함수는 해당하는 `function.json`과 동일한 부모 디렉터리를 공유하는 `index.js` 파일에서 실행됩니다.

`scriptFile`은 다음 예제와 같은 폴더 구조를 가져오는 데 사용할 수 있습니다.

```
FunctionApp
 | - host.json
 | - myNodeFunction
 | | - function.json
 | - lib
 | | - sayHello.js
 | - node_modules
 | | - ... packages ...
 | - package.json
```

`myNodeFunction`의 `function.json`에는 실행할 내보낸 함수가 있는 파일을 가리키는 `scriptFile` 속성이 포함되어야 합니다.

```json
{
  "scriptFile": "../lib/sayHello.js",
  "bindings": [
    ...
  ]
}
```

### <a name="using-entrypoint"></a>`entryPoint` 사용

`scriptFile`(또는 `index.js`)에서 함수를 찾아서 실행하려면 `module.exports`를 사용하여 함수를 내보내야 합니다. 기본적으로 트리거되면 실행되는 함수는 해당 파일의 내보내기, `run`이라는 이름의 내보내기 또는 `index`라고 명명된 내보내기입니다.

다음 예제와 같이 `function.json`에서 `entryPoint`를 사용하여 구성할 수 있습니다.

```json
{
  "entryPoint": "logFoo",
  "bindings": [
    ...
  ]
}
```

사용자 함수에서 `this` 매개 변수를 지원하는 Functions v2.x에서 함수 코드는 다음 예제와 같을 수 있습니다.

```javascript
class MyObj {
    constructor() {
        this.foo = 1;
    };

    logFoo(context) { 
        context.log("Foo is " + this.foo); 
        context.done(); 
    }
}

const myObj = new MyObj();
module.exports = myObj;
```

In this example, it is important to note that although an object is being exported, there are no guarantees for preserving state between executions.

## <a name="local-debugging"></a>Local Debugging

When started with the `--inspect` parameter, a Node.js process listens for a debugging client on the specified port. In Azure Functions 2.x, you can specify arguments to pass into the Node.js process that runs your code by adding the environment variable or App Setting `languageWorkers:node:arguments = <args>`. 

To debug locally, add `"languageWorkers:node:arguments": "--inspect=5858"` under `Values` in your [local.settings.json](https://docs.microsoft.com/azure/azure-functions/functions-run-local#local-settings-file) file and attach a debugger to port 5858.

When debugging using VS Code, the `--inspect` parameter is automatically added using the `port` value in the project's launch.json file.

In version 1.x, setting `languageWorkers:node:arguments` will not work. The debug port can be selected with the [`--nodeDebugPort`](https://docs.microsoft.com/azure/azure-functions/functions-run-local#start) parameter on Azure Functions Core Tools.

## <a name="typescript"></a>TypeScript

When you target version 2.x of the Functions runtime, both [Azure Functions for Visual Studio Code](functions-create-first-function-vs-code.md) and the [Azure Functions Core Tools](functions-run-local.md) let you create function apps using a template that support TypeScript function app projects. The template generates `package.json` and `tsconfig.json` project files that make it easier to transpile, run, and publish JavaScript functions from TypeScript code with these tools.

A generated `.funcignore` file is used to indicate which files are excluded when a project is published to Azure.  

TypeScript files (.ts) are transpiled into JavaScript files (.js) in the `dist` output directory. TypeScript templates use the [`scriptFile` parameter](#using-scriptfile) in `function.json` to indicate the location of the corresponding .js file in the `dist` folder. The output location is set by the template by using `outDir` parameter in the `tsconfig.json` file. If you change this setting or the name of the folder, the runtime is not able to find the code to run.

> [!NOTE]
> Experimental support for TypeScript exists version 1.x of the Functions runtime. The experimental version transpiles TypeScript files into JavaScript files when the function is invoked. In version 2.x, this experimental support has been superseded by the tool-driven method that does transpilation before the host is initialized and during the deployment process.

The way that you locally develop and deploy from a TypeScript project depends on your development tool.

### <a name="visual-studio-code"></a>Visual Studio Code

The [Azure Functions for Visual Studio Code](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-azurefunctions) extension lets you develop your functions using TypeScript. The Core Tools is a requirement of the Azure Functions extension.

To create a TypeScript function app in Visual Studio Code, choose `TypeScript` as your language when you create a function app.

When you press **F5** to run the app locally, transpilation is done before the host (func.exe) is initialized. 

When you deploy your function app to Azure using the **Deploy to function app...** button, the Azure Functions extension first generates a production-ready build of JavaScript files from the TypeScript source files.

### <a name="azure-functions-core-tools"></a>Azure Functions Core Tools

There are several ways in which a TypeScript project differs from a JavaScript project when using the Core Tools.

#### <a name="create-project"></a>프로젝트 만들기

To create a TypeScript function app project using Core Tools, you must specify the TypeScript language option when you create your function app. You can do this in one of the following ways:

- Run the `func init` command, select `node` as your language stack, and then select `typescript`.

- `func init --worker-runtime typescript` 명령을 실행합니다.

#### <a name="run-local"></a>Run local

To run your function app code locally using Core Tools, use the following commands instead of `func host start`: 

```command
npm install
npm start
```

The `npm start` command is equivalent to the following commands:

- `npm run build`
- `func extensions install`
- `tsc`
- `func start`

#### <a name="publish-to-azure"></a>Azure에 게시

Before you use the [`func azure functionapp publish`] command to deploy to Azure, you create a production-ready build of JavaScript files from the TypeScript source files. 

The following commands prepare and publish your TypeScript project using Core Tools: 

```command
npm run build:production 
func azure functionapp publish <APP_NAME>
```

In this command, replace `<APP_NAME>` with the name of your function app.

## <a name="considerations-for-javascript-functions"></a>JavaScript 함수에 대한 고려 사항

JavaScript 함수로 작업하는 경우 다음 섹션의 고려 사항에 유의해야 합니다.

### <a name="choose-single-vcpu-app-service-plans"></a>단일 vCPU App Service 계획 선택

App Service 계획을 사용하는 함수 앱을 만들 때 여러 vCPU가 있는 계획보다는 단일 vCPU 계획을 선택하는 것이 좋습니다. 현재 Functions는 단일 vCPU VM에서 JavaScript 함수를 더 효율적으로 실행합니다. 더 큰 VM을 사용해도 예상된 성능 향상을 보여 주지 않습니다. When necessary, you can manually scale out by adding more single-vCPU VM instances, or you can enable autoscale. 자세한 내용은 [수동 또는 자동으로 인스턴스 개수 조정](../monitoring-and-diagnostics/insights-how-to-scale.md?toc=%2fazure%2fapp-service%2ftoc.json)을 참조하세요.

### <a name="cold-start"></a>콜드 부팅

서버리스 호스팅 모델에서 Azure Functions를 개발하는 경우 콜드 부팅이 현실입니다. *콜드 부팅*이란 일정 기간 동안 비활성이었다가 처음으로 함수 앱을 시작하면 시작하는 데 더 오래 걸린다는 사실을 의미합니다. 특히 종속성 트리가 큰 JavaScript 함수의 경우 콜드 부팅은 중요할 수 있습니다. 콜드 부팅 프로세스의 속도를 높이려면 가능한 경우 [함수를 패키지 파일로 실행](run-functions-from-deployment-package.md)합니다. 여러 배포 방법은 기본적으로 패키지 모델에서 실행을 사용하지만 대규모 콜드 부팅이 발생하고 이러한 방식으로 실행하지 않는 경우 이 변경 내용으로 인해 크게 개선될 수 있습니다.

### <a name="connection-limits"></a>Connection Limits

When you use a service-specific client in an Azure Functions application, don't create a new client with every function invocation. Instead, create a single, static client in the global scope. For more information, see [managing connections in Azure Functions](manage-connections.md).

### <a name="use-async-and-await"></a>Use `async` and `await`

When writing Azure Functions in JavaScript, you should write code using the `async` and `await` keywords. Writing code using `async` and `await` instead of callbacks or `.then` and `.catch` with Promises helps avoid two common problems:
 - Throwing uncaught exceptions that [crash the Node.js process](https://nodejs.org/api/process.html#process_warning_using_uncaughtexception_correctly), potentially affecting the execution of other functions.
 - Unexpected behavior, such as missing logs from context.log, caused by asynchronous calls that are not properly awaited.

In the example below, the asynchronous method `fs.readFile` is invoked with an error-first callback function as its second parameter. This code causes both of the issues mentioned above. An exception that is not explicitly caught in the correct scope crashed the entire process (issue #1). Calling `context.done()` outside of the scope of the callback function means that the function invocation may end before the file is read (issue #2). In this example, calling `context.done()` too early results in missing log entries starting with `Data from file:`.

```javascript
// NOT RECOMMENDED PATTERN
const fs = require('fs');

module.exports = function (context) {
    fs.readFile('./hello.txt', (err, data) => {
        if (err) {
            context.log.error('ERROR', err);
            // BUG #1: This will result in an uncaught exception that crashes the entire process
            throw err;
        }
        context.log(`Data from file: ${data}`);
        // context.done() should be called here
    });
    // BUG #2: Data is not guaranteed to be read before the Azure Function's invocation ends
    context.done();
}
```

Using the `async` and `await` keywords helps avoid both of these errors. You should use the Node.js utility function [`util.promisify`](https://nodejs.org/api/util.html#util_util_promisify_original) to turn error-first callback-style functions into awaitable functions.

In the example below, any unhandled exceptions thrown during the function execution only fail the individual invocation that raised an exception. The `await` keyword means that steps following `readFileAsync` only execute after `readFile` is complete. With `async` and `await`, you also don't need to call the `context.done()` callback.

```javascript
// Recommended pattern
const fs = require('fs');
const util = require('util');
const readFileAsync = util.promisify(fs.readFile);

module.exports = async function (context) {
    let data;
    try {
        data = await readFileAsync('./hello.txt');
    } catch (err) {
        context.log.error('ERROR', err);
        // This rethrown exception will be handled by the Functions Runtime and will only fail the individual invocation
        throw err;
    }
    context.log(`Data from file: ${data}`);
}
```

## <a name="next-steps"></a>다음 단계

자세한 내용은 다음 리소스를 참조하세요.

+ [Azure Functions에 대한 모범 사례](functions-best-practices.md)
+ [Azure Functions 개발자 참조](functions-reference.md)
+ [Azure Functions 트리거 및 바인딩](functions-triggers-bindings.md)

[`func azure functionapp publish`]: functions-run-local.md#project-file-deployment
