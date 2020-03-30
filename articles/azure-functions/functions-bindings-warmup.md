---
title: Azure 함수 워밍업 트리거
description: Azure Functions에서 워밍업 트리거를 사용하는 방법을 이해합니다.
documentationcenter: na
author: alexkarcher-msft
manager: gwallace
keywords: azure 기능, 기능, 이벤트 처리, 워밍업, 콜드 스타트, 프리미엄, 동적 컴퓨팅, 서버리스 아키텍처
ms.service: azure-functions
ms.topic: reference
ms.date: 11/08/2019
ms.author: alkarche
ms.openlocfilehash: c3ed780bc50b690b2f5c3285024695ec6426b9b3
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/27/2020
ms.locfileid: "77167320"
---
# <a name="azure-functions-warm-up-trigger"></a>Azure 함수 워밍업 트리거

이 문서에서는 Azure Functions에서 워밍업 트리거를 사용하여 작업하는 방법을 설명합니다. 워밍업 트리거는 [프리미엄 요금제에서](functions-premium-plan.md)실행되는 기능 앱에 대해서만 지원됩니다. 실행 중인 함수 앱을 확장하기 위해 인스턴스가 추가되면 워밍업 트리거가 호출됩니다. 워밍업 트리거를 사용하여 [사전 워밍업 프로세스](./functions-premium-plan.md#pre-warmed-instances) 중에 사용자 지정 종속성을 미리 로드하여 함수가 요청을 즉시 처리할 수 있도록 할 수 있습니다. 

[!INCLUDE [intro](../../includes/functions-bindings-intro.md)]

## <a name="packages---functions-2x-and-higher"></a>패키지 - 기능 2.x 이상

[Microsoft.Azure.WebJobs.Extensions](https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions) NuGet 패키지, 버전 **3.0.5 이상이** 필요합니다. 이 패키지에 대한 소스 코드는 [azure-webjobs-sdk-extensions](https://github.com/Azure/azure-webjobs-sdk-extensions/blob/master/src/WebJobs.Extensions.Http/) GitHub 리포지토리에 있습니다. 

[!INCLUDE [functions-package](../../includes/functions-package-auto.md)]

## <a name="trigger"></a>트리거

워밍업 트리거를 사용하면 실행 중인 앱에 추가될 때 새 인스턴스에서 실행되는 함수를 정의할 수 있습니다. 워밍업 함수를 사용하여 연결을 열거나 종속성을 로드하거나 앱에서 트래픽 수신을 시작하기 전에 다른 사용자 지정 논리를 실행할 수 있습니다. 

워밍업 트리거는 앱의 다른 함수에서 사용할 공유 종속성을 만들기 위한 것입니다. [공유 종속성의 예제는 여기를 참조하십시오.](./manage-connections.md#client-code-examples)

워밍업 트리거는 다시 시작하거나 다른 비스케일 시작 하는 동안이 아니라 확장 작업 중에만 호출 됩니다. 워밍업 트리거를 사용하지 않고 논리가 필요한 모든 종속성을 로드할 수 있는지 확인해야 합니다. 지연 로드는 이를 달성하기 위한 좋은 패턴입니다.

## <a name="trigger---example"></a>트리거 - 예제

# <a name="c"></a>[C #](#tab/csharp)

다음 예제에서는 앱에 추가될 때마다 실행되는 [C# 함수를](functions-dotnet-class-library.md) 보여 줍니다. 반환 값 특성은 필요하지 않습니다.


* 함수의 이름을 ```warmup``` 지정해야 하며(대/소문자를 구분하지 않음) 앱당 하나의 워밍업 기능만 있을 수 있습니다.
* 워밍업을 .NET 클래스 라이브러리 함수로 사용하려면 **Microsoft.Azure.WebJobs.Extensions >= 3.0.5에** 대한 패키지 참조가 있는지 확인하십시오.
    * ```<PackageReference Include="Microsoft.Azure.WebJobs.Extensions" Version="3.0.5" />```


자리 표시자 주석은 응용 프로그램에서 공유 종속성을 선언하고 초기화할 위치를 표시합니다. 
[공유 종속성에 대한 자세한 내용은 여기를 참조하세요.](./manage-connections.md#client-code-examples)

```cs
using Microsoft.Azure.WebJobs;
using Microsoft.Extensions.Logging;
 
namespace WarmupSample
{

    //Declare shared dependencies here

    public static class Warmup
    {
        [FunctionName("Warmup")]
        public static void Run([WarmupTrigger()] WarmupContext context,
            ILogger log)
        {
            //Initialize shared dependencies here
            
            log.LogInformation("Function App instance is warm 🌞🌞🌞");
        }
    }
}
```
# <a name="c-script"></a>[C# 스크립트](#tab/csharp-script)


다음 예제에서는 *function.json* 파일에서 워밍업 트리거와 앱에 추가될 때마다 실행되는 [C# 스크립트 함수를](functions-reference-csharp.md) 보여 줍니다.

함수의 이름을 ```warmup``` 지정해야 하며(대/소문자를 구분하지 않음) 앱당 워밍업 함수가 하나만 있을 수 있습니다.

*function.json* 파일은 다음과 같습니다.

```json
{
    "bindings": [
        {
            "type": "warmupTrigger",
            "direction": "in",
            "name": "warmupContext"
        }
    ]
}
```

[구성](#trigger---configuration) 섹션에서는 이러한 속성을 설명합니다.

다음은 `HttpRequest`에 바인딩하는 C# 스크립트 코드입니다.

```cs
public static void Run(ILogger log)
{
    log.LogInformation("Function App instance is warm 🌞🌞🌞");  
}
```

# <a name="javascript"></a>[자바 스크립트](#tab/javascript)

다음 예제에서는 *function.json* 파일에서 워밍업 트리거와 앱에 추가될 때마다 실행되는 [JavaScript 함수를](functions-reference-node.md) 보여 줍니다.

함수의 이름을 ```warmup``` 지정해야 하며(대/소문자를 구분하지 않음) 앱당 하나의 워밍업 기능만 있을 수 있습니다.

*function.json* 파일은 다음과 같습니다.

```json
{
    "bindings": [
        {
            "type": "warmupTrigger",
            "direction": "in",
            "name": "warmupContext"
        }
    ]
}
```

[구성](#trigger---configuration) 섹션에서는 이러한 속성을 설명합니다.

JavaScript 코드는 다음과 같습니다.

```javascript
module.exports = async function (context, warmupContext) {
    context.log('Function App instance is warm 🌞🌞🌞');
    context.done();
};
```

# <a name="python"></a>[Python](#tab/python)

다음 예제에서는 *function.json* 파일에서 워밍업 트리거와 앱에 추가될 때 각 새 인스턴스에서 실행되는 [Python 함수를](functions-reference-python.md) 보여 줍니다.

함수의 이름을 ```warmup``` 지정해야 하며(대/소문자를 구분하지 않음) 앱당 하나의 워밍업 기능만 있을 수 있습니다.

*function.json* 파일은 다음과 같습니다.

```json
{
    "bindings": [
        {
            "type": "warmupTrigger",
            "direction": "in",
            "name": "warmupContext"
        }
    ]
}
```

[구성](#trigger---configuration) 섹션에서는 이러한 속성을 설명합니다.

다음은 Python 코드입니다.

```python
import logging
import azure.functions as func


def main(warmupContext: func.Context) -> None:
    logging.info('Function App instance is warm 🌞🌞🌞')
```

# <a name="java"></a>[Java](#tab/java)

다음 예제에서는 각 새 인스턴스가 앱에 추가될 때 실행되는 워밍업 트리거를 보여 줍니다.

함수의 이름을 `warmup` 지정해야 하며(대/소문자를 구분하지 않음) 앱당 하나의 워밍업 기능만 있을 수 있습니다.

```java
@FunctionName("Warmup")
public void run( ExecutionContext context) {
       context.getLogger().info("Function App instance is warm 🌞🌞🌞");
}
```

---

## <a name="trigger---attributes"></a>트리거 - 특성

[C# 클래스 라이브러리에서](functions-dotnet-class-library.md)함수를 `WarmupTrigger` 구성하는 데 속성을 사용할 수 있습니다.

# <a name="c"></a>[C #](#tab/csharp)

이 예제에서는 [워밍업](https://github.com/Azure/azure-webjobs-sdk-extensions/blob/dev/src/WebJobs.Extensions/Extensions/Warmup/Trigger/WarmupTriggerAttribute.cs) 특성을 사용하는 방법을 보여 줍니다.

함수를 호출해야 ```Warmup``` 하며 앱당 하나의 워밍업 기능만 있을 수 있습니다.

```csharp
 [FunctionName("Warmup")]
        public static void Run(
            [WarmupTrigger()] WarmupContext context, ILogger log)
        {
            ...
        }
```

전체 예제는 트리거 [예제를](#trigger---example)참조하십시오.

# <a name="c-script"></a>[C# 스크립트](#tab/csharp-script)

특성은 C# 스크립트에서 지원되지 않습니다.

# <a name="javascript"></a>[자바 스크립트](#tab/javascript)

속성은 자바 스크립트에서 지원되지 않습니다.

# <a name="python"></a>[Python](#tab/python)

특성은 파이썬에서 지원되지 않습니다.

# <a name="java"></a>[Java](#tab/java)

워밍업 트리거는 Java에서 특성으로 지원되지 않습니다.

---

## <a name="trigger---configuration"></a>트리거 - 구성

다음 표에서는 *function.json* 파일및 특성에서 설정한 바인딩 `WarmupTrigger` 구성 속성에 대해 설명합니다.

|function.json 속성 | 특성 속성 |설명|
|---------|---------|----------------------|
| **종류** | 해당 없음| 필수 - `warmupTrigger`으로 설정해야 합니다. |
| **direction** | 해당 없음| 필수 - `in`으로 설정해야 합니다. |
| **(이름)** | 해당 없음| 필수 - 함수 코드에 사용되는 변수 이름입니다.|

## <a name="trigger---usage"></a>트리거 - 사용

워밍업 트리거 함수는 호출될 때 추가 정보가 제공되지 않습니다.

## <a name="trigger---limits"></a>트리거 - 제한

* 워밍업 트리거는 [프리미엄 요금제에서](./functions-premium-plan.md)실행 중인 앱에서만 사용할 수 있습니다.
* 워밍업 트리거는 다시 시작하거나 다른 비스케일 시작 하는 동안 이 아니라 확장 작업 중에만 호출됩니다. 워밍업 트리거를 사용하지 않고 논리가 필요한 모든 종속성을 로드할 수 있는지 확인해야 합니다. 지연 로드는 이를 달성하기 위한 좋은 패턴입니다.
* 인스턴스가 이미 실행 중이면 워밍업 트리거를 호출할 수 없습니다.
* 함수 앱당 워밍업 트리거 함수는 하나만 있을 수 있습니다.

## <a name="next-steps"></a>다음 단계

[Azure Functions 트리거 및 바인딩에 대한 자세한 정보](functions-triggers-bindings.md)
