---
title: 지속 형 기능의 HTTP 기능 - Azure 함수
description: Azure 함수에 대한 지속형 함수 확장에서 통합된 HTTP 기능에 대해 알아봅니다.
author: cgillum
ms.topic: conceptual
ms.date: 09/04/2019
ms.author: azfuncdf
ms.openlocfilehash: 29d837446960b7535b26284efdfab7a1c59ea968
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "80132508"
---
# <a name="http-features"></a>HTTP 기능

내구성있는 함수에는 지속성 오케스트레이션 및 엔터티를 HTTP 워크플로에 쉽게 통합할 수 있는 몇 가지 기능이 있습니다. 이 문서에서는 이러한 기능 중 일부에 대해 자세히 설명합니다.

## <a name="exposing-http-apis"></a>HTTP API 노출

HTTP 요청을 사용하여 오케스트레이션 및 엔터티를 호출하고 관리할 수 있습니다. 지속형 함수 확장은 기본 제공 HTTP API를 노출합니다. 또한 HTTP 트리거 함수 내에서 오케스트레이션 및 엔터티와 상호 작용하기 위한 API를 제공합니다.

### <a name="built-in-http-apis"></a>내장 된 HTTP API

지속형 함수 확장은 Azure Functions 호스트에 HTTP API 집합을 자동으로 추가합니다. 이러한 API를 사용하면 코드를 작성하지 않고도 오케스트레이션 및 엔터티와 상호 작용하고 관리할 수 있습니다.

다음과 같은 기본 제공 HTTP API가 지원됩니다.

* [새 오케스트레이션 시작](durable-functions-http-api.md#start-orchestration)
* [쿼리 오케스트레이션 인스턴스](durable-functions-http-api.md#get-instance-status)
* [오케스트레이션 인스턴스 종료](durable-functions-http-api.md#terminate-instance)
* [외부 이벤트를 오케스트레이션으로 보내기](durable-functions-http-api.md#raise-event)
* [제거 오케스트레이션 기록](durable-functions-http-api.md#purge-single-instance-history)
* [엔터티에 작업 이벤트 보내기](durable-functions-http-api.md#signal-entity)
* [엔터티의 상태 가져옵니다.](durable-functions-http-api.md#get-entity)
* [엔터티 목록 쿼리](durable-functions-http-api.md#list-entities)

내구성 함수 확장에서 노출된 모든 기본 제공 HTTP API에 대한 자세한 설명은 [HTTP API 문서를](durable-functions-http-api.md) 참조하십시오.

### <a name="http-api-url-discovery"></a>HTTP API URL 검색

[오케스트레이션 클라이언트 바인딩은](durable-functions-bindings.md#orchestration-client) 편리한 HTTP 응답 페이로드를 생성할 수 있는 API를 노출합니다. 예를 들어 특정 오케스트레이션 인스턴스에 대한 관리 API에 대한 링크가 포함된 응답을 만들 수 있습니다. 다음 예제에서는 새 오케스트레이션 인스턴스에 이 API를 사용하는 방법을 보여 주는 HTTP 트리거 함수를 보여 줍니다.

# <a name="c"></a>[C #](#tab/csharp)

[!code-csharp[Main](~/samples-durable-functions/samples/precompiled/HttpStart.cs)]

# <a name="javascript"></a>[자바 스크립트](#tab/javascript)

**index.js**

[!code-javascript[Main](~/samples-durable-functions/samples/javascript/HttpStart/index.js)]

**함수.json**

[!code-json[Main](~/samples-durable-functions/samples/javascript/HttpStart/function.json)]

---

이전에 표시된 HTTP 트리거 함수를 사용하여 오케스트레이터 함수를 시작하면 HTTP 클라이언트를 사용하여 수행할 수 있습니다. 다음 cURL 명령은 다음 오케스트레이터 함수를 `DoWork`시작합니다.

```bash
curl -X POST https://localhost:7071/orchestrators/DoWork -H "Content-Length: 0" -i
```

다음은 ID로 된 `abc123` 오케스트레이션에 대한 예제 응답입니다. 명확성을 위해 일부 세부 정보가 제거되었습니다.

```http
HTTP/1.1 202 Accepted
Content-Type: application/json; charset=utf-8
Location: http://localhost:7071/runtime/webhooks/durabletask/instances/abc123?code=XXX
Retry-After: 10

{
    "id": "abc123",
    "purgeHistoryDeleteUri": "http://localhost:7071/runtime/webhooks/durabletask/instances/abc123?code=XXX",
    "sendEventPostUri": "http://localhost:7071/runtime/webhooks/durabletask/instances/abc123/raiseEvent/{eventName}?code=XXX",
    "statusQueryGetUri": "http://localhost:7071/runtime/webhooks/durabletask/instances/abc123?code=XXX",
    "terminatePostUri": "http://localhost:7071/runtime/webhooks/durabletask/instances/abc123/terminate?reason={text}&code=XXX"
}
```

이전 예제에서 끝나는 각 필드는 `Uri` 기본 제공 HTTP API에 해당 합니다. 이러한 API를 사용하여 대상 오케스트레이션 인스턴스를 관리할 수 있습니다.

> [!NOTE]
> 웹후크 URL의 형식은 실행 중인 Azure Functions 호스트버전에 따라 다릅니다. 이전 예제는 Azure Functions 2.0 호스트에 대 한 것입니다.

모든 기본 제공 HTTP API에 대한 설명은 [HTTP API 참조를](durable-functions-http-api.md)참조하십시오.

### <a name="async-operation-tracking"></a>비동기 작업 추적

앞에서 언급한 HTTP 응답은 Durable Functions를 사용하여 장기 실행 HTTP 비동기 API를 구현하는 데 도움이 되도록 설계되었습니다. 이 패턴을 *폴링 소비자 패턴이라고도*합니다. 클라이언트/서버 흐름은 다음과 같이 작동합니다.

1. 클라이언트는 오케스트레이터 함수와 같은 장기 실행 프로세스를 시작하도록 HTTP 요청을 실행합니다.
1. 대상 HTTP 트리거는 "statusQueryGetUri" 값이 있는 위치 헤더를 통해 HTTP 202 응답을 반환합니다.
1. 클라이언트는 위치 헤더의 URL을 폴링을 합니다. 클라이언트는 위치 헤더를 사용하여 HTTP 202 응답을 계속 볼 수 있습니다.
1. 인스턴스가 완료되거나 실패하면 위치 헤더의 끝점이 HTTP 200을 반환합니다.

이 프로토콜을 사용하면 HTTP 끝점을 폴링하고 위치 헤더를 따를 수 있는 외부 클라이언트 또는 서비스와 장기 실행 프로세스를 조정할 수 있습니다. 이 패턴의 클라이언트 구현과 서버 구현은 모두 지속형 함수 HTTP API에 내장되어 있습니다.

> [!NOTE]
> 기본적으로 [Azure Logic Apps](https://azure.microsoft.com/services/logic-apps/)에서 제공하는 모든 HTTP 기반 작업은 표준 비동기 작업 패턴을 지원합니다. 이 기능을 사용하면 Logic Apps 워크플로의 일부로 장기 실행 지속성 함수를 포함할 수 있습니다. Azure Logic Apps [워크플로 작업 및 트리거 설명서에서](../../logic-apps/logic-apps-workflow-actions-triggers.md)비동기 HTTP 패턴에 대한 Logic Apps 지원에 대한 자세한 내용을 찾을 수 있습니다.

> [!NOTE]
> 오케스트레이션과의 상호 작용은 HTTP 트리거 함수뿐만 아니라 모든 함수 유형에서 수행할 수 있습니다.

클라이언트 API를 사용하여 오케스트레이션 및 엔터티를 관리하는 방법에 대한 자세한 내용은 [인스턴스 관리 문서를](durable-functions-instance-management.md)참조하십시오.

## <a name="consuming-http-apis"></a>HTTP API 사용

[오케스트레이터 함수 코드 제약 조건에](durable-functions-code-constraints.md)설명된 대로 오케스트레이터 함수는 I/O를 직접 수행할 수 없습니다. 대신 일반적으로 I/O 작업을 수행하는 [활동 함수를 호출합니다.](durable-functions-types-features-overview.md#activity-functions)

지속 함수 2.0부터 오케스트레이션은 [오케스트레이션 트리거 바인딩을](durable-functions-bindings.md#orchestration-trigger)사용하여 기본적으로 HTTP API를 사용할 수 있습니다.

다음 예제 코드는 아웃바운드 HTTP 요청을 하는 오케스트레이터 함수를 보여 주며, 다음과 같은 경우를 보여 주며,

# <a name="c"></a>[C #](#tab/csharp)

```csharp
[FunctionName("CheckSiteAvailable")]
public static async Task CheckSiteAvailable(
    [OrchestrationTrigger] IDurableOrchestrationContext context)
{
    Uri url = context.GetInput<Uri>();

    // Makes an HTTP GET request to the specified endpoint
    DurableHttpResponse response = 
        await context.CallHttpAsync(HttpMethod.Get, url);

    if (response.StatusCode >= 400)
    {
        // handling of error codes goes here
    }
}
```

# <a name="javascript"></a>[자바 스크립트](#tab/javascript)

```javascript
const df = require("durable-functions");

module.exports = df.orchestrator(function*(context){
    const url = context.df.getInput();
    const response = context.df.callHttp("GET", url)

    if (response.statusCode >= 400) {
        // handling of error codes goes here
    }
});
```

---

"HTTP 호출" 작업을 사용 하 여 오케스트레이터 함수에서 다음 작업을 수행할 수 있습니다.

* 나중에 언급된 몇 가지 제한 사항이 있는 오케스트레이션 함수에서 직접 HTTP API를 호출합니다.
* 클라이언트 측 HTTP 202 상태 폴링 패턴을 자동으로 지원합니다.
* [Azure 관리 ID를](../../active-directory/managed-identities-azure-resources/overview.md) 사용하여 다른 Azure 끝점에 대한 권한 있는 HTTP 호출을 합니다.

오케스트레이터 함수에서 직접 HTTP API를 사용하는 기능은 특정 공통 시나리오 집합에 대한 편의를 위해 사용됩니다. 활동 함수를 사용하여 이러한 모든 기능을 직접 구현할 수 있습니다. 대부분의 경우 활동 함수는 더 많은 유연성을 제공할 수 있습니다.

### <a name="http-202-handling"></a>HTTP 202 취급

"HTTP 호출" API는 폴링 소비자 패턴의 클라이언트 측을 자동으로 구현할 수 있습니다. 호출된 API가 위치 헤더를 사용 하 여 HTTP 202 응답을 반환 하는 경우 오케스트레이터 함수는 자동으로 202 이외의 응답을 받을 때까지 위치 리소스를 폴링을 폴링을 합니다. 이 응답은 오케스트레이터 함수 코드에 반환되는 응답입니다.

> [!NOTE]
> 또한 오케스트레이터 함수는 [비동기 작업 추적에](#async-operation-tracking)설명된 대로 서버 측 폴링 소비자 패턴을 기본적으로 지원합니다. 이 지원은 한 함수 앱의 오케스트레이션을 통해 다른 함수 앱의 오케스트레이터 함수를 쉽게 조정할 수 있습니다. 이는 [하위 오케스트레이션](durable-functions-sub-orchestrations.md) 개념과 유사하지만 앱 간 통신을 지원합니다. 이 지원은 마이크로 서비스 스타일 앱 개발에 특히 유용합니다.

### <a name="managed-identities"></a>관리 ID

내구성 함수는 권한 부여를 위해 Azure Active Directory(Azure AD) 토큰을 허용하는 API 호출을 기본적으로 지원합니다. 이 지원은 [Azure 관리 ID를](../../active-directory/managed-identities-azure-resources/overview.md) 사용하여 이러한 토큰을 획득합니다.

다음 코드는 .NET 오케스트레이터 함수의 예입니다. 이 함수는 Azure Resource Manager 가상 시스템 [REST API를](https://docs.microsoft.com/rest/api/compute/virtualmachines)사용하여 가상 컴퓨터를 다시 시작하기 위해 인증된 호출을 만듭니다.

# <a name="c"></a>[C #](#tab/csharp)

```csharp
[FunctionName("RestartVm")]
public static async Task RunOrchestrator(
    [OrchestrationTrigger] IDurableOrchestrationContext context)
{
    string subscriptionId = "mySubId";
    string resourceGroup = "myRG";
    string vmName = "myVM";
    string apiVersion = "2019-03-01";
    
    // Automatically fetches an Azure AD token for resource = https://management.core.windows.net
    // and attaches it to the outgoing Azure Resource Manager API call.
    var restartRequest = new DurableHttpRequest(
        HttpMethod.Post, 
        new Uri($"https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroup}/providers/Microsoft.Compute/virtualMachines/{vmName}/restart?api-version={apiVersion}"),
        tokenSource: new ManagedIdentityTokenSource("https://management.core.windows.net"));
    DurableHttpResponse restartResponse = await context.CallHttpAsync(restartRequest);
    if (restartResponse.StatusCode != HttpStatusCode.OK)
    {
        throw new ArgumentException($"Failed to restart VM: {restartResponse.StatusCode}: {restartResponse.Content}");
    }
}
```

# <a name="javascript"></a>[자바 스크립트](#tab/javascript)

```javascript
const df = require("durable-functions");

module.exports = df.orchestrator(function*(context) {
    const subscriptionId = "mySubId";
    const resourceGroup = "myRG";
    const vmName = "myVM";
    const apiVersion = "2019-03-01";
    const tokenSource = new df.ManagedIdentityTokenSource("https://management.core.windows.net");

    // get a list of the Azure subscriptions that I have access to
    const restartResponse = yield context.df.callHttp(
        "POST",
        `https://management.azure.com/subscriptions/${subscriptionId}/resourceGroups/${resourceGroup}/providers/Microsoft.Compute/virtualMachines/${vmName}/restart?api-version=${apiVersion}`,
        undefined, // no request content
        undefined, // no request headers (besides auth which is handled by the token source)
        tokenSource);

    return restartResponse;
});
```

---

이전 예제에서 `tokenSource` 매개 변수는 Azure 리소스 [관리자에](../../azure-resource-manager/management/overview.md)대 한 Azure AD 토큰을 획득 하도록 구성 됩니다. 토큰은 리소스 URI로 `https://management.core.windows.net`식별됩니다. 이 예제에서는 현재 함수 앱이 로컬로 실행중이거나 관리되는 ID가 있는 함수 앱으로 배포되었다고 가정합니다. 로컬 ID 또는 관리되는 ID는 지정된 리소스 그룹에서 `myRG`VM을 관리할 수 있는 권한이 있는 것으로 가정합니다.

런타임시 구성된 토큰 소스는 OAuth 2.0 액세스 토큰을 자동으로 반환합니다. 그런 다음 소스는 나가는 요청의 권한 부여 헤더에 토큰을 보유자 토큰으로 추가합니다. 이 모델은 다음과 같은 이유로 HTTP 요청에 권한 부여 헤더를 수동으로 추가하는 것에 대한 개선 사항입니다.

* 토큰 새로 고침이 자동으로 처리됩니다. 만료된 토큰에 대해 걱정할 필요가 없습니다.
* 토큰은 지속가능한 오케스트레이션 상태로 저장되지 않습니다.
* 토큰 수집을 관리하기 위해 코드를 작성할 필요가 없습니다.

[미리 컴파일된 C# RestartVM 샘플에서](https://github.com/Azure/azure-functions-durable-extension/blob/v2/samples/v2/precompiled/RestartVMs.cs)보다 완전한 예제를 찾을 수 있습니다.

관리되는 ID는 Azure 리소스 관리로 제한되지 않습니다. 관리되는 ID를 사용하여 Microsoft의 Azure 서비스 및 파트너의 웹 앱을 포함하여 Azure AD 베어러 토큰을 허용하는 모든 API에 액세스할 수 있습니다. 파트너의 웹 앱은 다른 기능 앱이 될 수도 있습니다. Azure AD로 인증을 지원하는 Microsoft의 Azure 서비스 목록은 [Azure AD 인증을 지원하는 Azure 서비스를](../../active-directory/managed-identities-azure-resources/services-support-managed-identities.md#azure-services-that-support-azure-ad-authentication)참조하십시오.

### <a name="limitations"></a>제한 사항

HTTP API 호출에 대한 기본 제공 지원은 편리한 기능입니다. 모든 시나리오에 적합하지 는 않습니다.

오케스트레이터 함수에서 보낸 HTTP 요청과 해당 응답은 직렬화되고 큐 메시지로 지속됩니다. 이 큐잉 동작은 HTTP 호출이 [오케스트레이션 재생에 안정적이고 안전하도록](durable-functions-orchestrations.md#reliability)합니다. 그러나 큐 동작에는 다음과 같은 제한 사항이 있습니다.

* 각 HTTP 요청에는 네이티브 HTTP 클라이언트와 비교할 때 추가 대기 시간이 포함됩니다.
* 큐 메시지에 맞지 않는 큰 요청 또는 응답 메시지는 오케스트레이션 성능을 크게 저하시킬 수 있습니다. 메시지 페이로드를 Blob 저장소로 오프로드할 경우 성능이 저하될 수 있습니다.
* 스트리밍, 청크 및 이진 페이로드는 지원되지 않습니다.
* HTTP 클라이언트의 동작을 사용자 지정하는 기능은 제한되어 있습니다.

이러한 제한 사항이 사용 사례에 영향을 줄 수 있는 경우 대신 활동 함수와 언어별 HTTP 클라이언트 라이브러리를 사용하여 아웃바운드 HTTP 호출을 수행하는 것이 좋습니다.

> [!NOTE]
> .NET 개발자인 경우 이 기능이 기본 제공 .NET **HttpRequestMessage** 및 **HttpResponseMessage** 유형 대신 **DurableHttpRequest** 및 **DurableHttpResponse** 형식을 사용하는 이유가 궁금할 수 있습니다.
>
> 이 디자인 선택은 의도적입니다. 주된 이유는 사용자 지정 형식을 사용하면 사용자가 내부 HTTP 클라이언트의 지원되는 동작에 대해 잘못된 가정을 하지 않도록 하기 때문입니다. 또한 지속형 함수와 관련된 형식을 사용하면 API 디자인을 단순화할 수 있습니다. 또한 [관리되는 ID 통합](#managed-identities) 및 [폴링 소비자 패턴과](#http-202-handling)같은 특수 기능을 보다 쉽게 사용할 수 있습니다. 

### <a name="extensibility-net-only"></a>확장성(.NET만)

[Azure Function .NET 종속성 주입을](https://docs.microsoft.com/azure/azure-functions/functions-dotnet-dependency-injection)사용하여 오케스트레이션의 내부 HTTP 클라이언트의 동작을 사용자 지정할 수 있습니다. 이 기능은 작은 행동 을 변경하는 데 유용 할 수 있습니다. 모의 개체를 삽입하여 HTTP 클라이언트를 단위 테스트하는 데도 유용할 수 있습니다.

다음 예제에서는 종속성 주입을 사용하여 외부 HTTP 끝점을 호출하는 오케스트레이터 함수에 대한 TLS/SSL 인증서 유효성 검사를 사용하지 않도록 설정하는 방법을 보여 줍니다.

```csharp
public class Startup : FunctionsStartup
{
    public override void Configure(IFunctionsHostBuilder builder)
    {
        // Register own factory
        builder.Services.AddSingleton<
            IDurableHttpMessageHandlerFactory,
            MyDurableHttpMessageHandlerFactory>();
    }
}

public class MyDurableHttpMessageHandlerFactory : IDurableHttpMessageHandlerFactory
{
    public HttpMessageHandler CreateHttpMessageHandler()
    {
        // Disable TLS/SSL certificate validation (not recommended in production!)
        return new HttpClientHandler
        {
            ServerCertificateCustomValidationCallback =
                HttpClientHandler.DangerousAcceptAnyServerCertificateValidator,
        };
    }
}
```

## <a name="next-steps"></a>다음 단계

> [!div class="nextstepaction"]
> [지속적 엔터티에 대해 알아보기](durable-functions-entities.md)
