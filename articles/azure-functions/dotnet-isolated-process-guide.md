---
title: Azure Functions의 .NET 5.0에 대 한 .NET isolated 프로세스 가이드
description: '.NET 격리 된 프로세스를 사용 하 여 Azure에서 .NET 5.0에 대 한 c # 함수를 실행 하는 방법에 대해 알아봅니다.'
ms.service: azure-functions
ms.topic: conceptual
ms.date: 03/01/2021
ms.custom: template-concept
ms.openlocfilehash: b4cf3699243e990b5e7b7478ba643067ac456020
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/19/2021
ms.locfileid: "104584706"
---
# <a name="guide-for-running-functions-on-net-50-in-azure"></a>Azure의 .NET 5.0에서 함수를 실행 하는 방법에 대 한 가이드

이 문서에서는 c #을 사용 하 여 Azure Functions에서 out-of-process로 실행 되는 .NET 격리 된 프로세스 함수를 개발 하는 방법을 소개 합니다. Out-of-process를 실행 하면 Azure Functions 런타임에서 함수 코드를 분리할 수 있습니다. 또한 현재 .NET 5.0 릴리스를 대상으로 하는 함수를 만들고 실행 하는 방법을 제공 합니다. 

| 시작 | 개념| 샘플 |
|--|--|--| 
| <ul><li>[Visual Studio Code 사용](dotnet-isolated-process-developer-howtos.md?pivots=development-environment-vscode)</li><li>[명령줄 도구 사용](dotnet-isolated-process-developer-howtos.md?pivots=development-environment-cli)</li><li>[Visual Studio 사용](dotnet-isolated-process-developer-howtos.md?pivots=development-environment-vs)</li></ul> | <ul><li>[호스팅 옵션](functions-scale.md)</li><li>[Monitoring](functions-monitoring.md)</li> | <ul><li>[참조 샘플](https://github.com/Azure/azure-functions-dotnet-worker/tree/main/samples)</li></ul> |

.NET 5.0을 지원 하지 않거나 함수를 out-of-process로 실행 하지 않아도 되는 경우 [c # 클래스 라이브러리 함수를 개발](functions-dotnet-class-library.md)하는 것이 좋습니다.

## <a name="why-net-isolated-process"></a>.NET 격리 된 프로세스는 무엇 인가요?

이전에 Azure Functions는 호스트와 동일한 프로세스에서 [클래스 라이브러리로](functions-dotnet-class-library.md) 실행 되는 .net 함수에 대해 긴밀 하 게 통합 된 모드만 지원 했습니다. 이 모드는 호스트 프로세스와 함수 간의 긴밀 한 통합을 제공 합니다. 예를 들어 .NET 클래스 라이브러리 함수는 바인딩 Api 및 형식을 공유할 수 있습니다. 그러나이 통합에는 호스트 프로세스와 .NET 함수 간의 긴밀 한 결합도 필요 합니다. 예를 들어 in-process를 실행 하는 .NET 함수는 함수 런타임과 동일한 버전의 .NET에서 실행 해야 합니다. 이러한 제약 조건 외부에서 실행할 수 있도록 이제 격리 된 프로세스에서 실행 하도록 선택할 수 있습니다. 이 프로세스 격리를 통해 함수 런타임에 의해 기본적으로 지원 되지 않는 현재 .NET 릴리스 (예: .NET 5.0)를 사용 하는 함수를 개발할 수도 있습니다.

이러한 함수는 별도의 프로세스에서 실행 되므로 .NET isolated 함수 앱과 .NET 클래스 라이브러리 함수 앱 간에는 몇 가지 [기능과 기능이 다릅니다](#differences-with-net-class-library-functions) .

### <a name="benefits-of-running-out-of-process"></a>Out-of-process 실행의 이점

Out-of-process를 실행 하는 경우 .NET 함수는 다음과 같은 이점을 활용할 수 있습니다. 

+ 충돌 감소: 함수는 별도의 프로세스에서 실행 되므로 앱에서 사용 되는 어셈블리는 호스트 프로세스에서 사용 하는 동일한 어셈블리의 다른 버전과 충돌 하지 않습니다.  
+ 프로세스에 대 한 모든 권한: 앱의 시작을 제어 하 고 사용 된 구성과 미들웨어를 시작 하는 방법을 제어할 수 있습니다.
+ 종속성 주입: 프로세스에 대 한 모든 권한이 있으므로 종속성 주입에 대 한 현재 .NET 동작을 사용 하 고 함수 앱에 미들웨어를 통합할 수 있습니다. 

## <a name="supported-versions"></a>지원되는 버전

현재 out-of-process를 실행 하는 데 지원 되는 .NET의 유일한 버전은 .NET 5.0입니다.

## <a name="net-isolated-project"></a>.NET 격리 프로젝트

.NET isolated 함수 프로젝트는 기본적으로 .NET 5.0를 대상으로 하는 .NET 콘솔 응용 프로그램 프로젝트입니다. 다음은 .NET 격리 프로젝트에 필요한 기본 파일입니다.

+ 파일 [에host.js](functions-host-json.md) 합니다.
+ 파일 [에local.settings.js](functions-run-local.md#local-settings-file) 합니다.
+ 프로젝트 및 종속성을 정의 하는 c # 프로젝트 파일 (.csproj)입니다.
+ 응용 프로그램의 진입점인 Program .cs 파일입니다.

## <a name="package-references"></a>패키지 참조

Out-of-process를 실행 하는 경우 .NET 프로젝트는 핵심 기능과 바인딩 확장을 모두 구현 하는 고유한 패키지 집합을 사용 합니다. 

### <a name="core-packages"></a>핵심 패키지 

격리 된 프로세스에서 .NET 함수를 실행 하려면 다음 패키지가 필요 합니다.

+ [Microsoft.Azure.Functions.Worker](https://www.nuget.org/packages/Microsoft.Azure.Functions.Worker/)
+ [Microsoft.Azure.Functions.Worker.Sdk](https://www.nuget.org/packages/Microsoft.Azure.Functions.Worker.Sdk/)

### <a name="extension-packages"></a>확장 패키지

.NET 격리 된 프로세스에서 실행 되는 함수는 서로 다른 바인딩 형식을 사용 하므로 고유한 바인딩 확장 패키지 집합이 필요 합니다. 

이러한 확장 패키지는 [Microsoft](https://www.nuget.org/packages?q=Microsoft.Azure.Functions.Worker.Extensions)에서 찾을 수 있습니다.

## <a name="start-up-and-configuration"></a>시작 및 구성 

.NET isolated 함수를 사용 하는 경우 함수 앱의 시작에 액세스할 수 있습니다 .이는 일반적으로 Program .cs에 있습니다. 사용자는 자신의 호스트 인스턴스를 만들고 시작 해야 합니다. 따라서 앱에 대 한 구성 파이프라인에 직접 액세스할 수도 있습니다. Out-of-process를 실행할 때 종속성을 훨씬 더 쉽게 주입 하 고 미들웨어를 실행할 수 있습니다. 

다음 코드에서는 파이프라인의 예를 보여 줍니다 `HostBuilder` .

:::code language="csharp" source="~/azure-functions-dotnet-worker/samples/FunctionApp/Program.cs" id="docsnippet_startup":::

는 `HostBuilder` `IHost` 함수 앱을 시작 하기 위해 비동기적으로 실행 하는 완전히 초기화 된 인스턴스를 빌드하고 반환 하는 데 사용 됩니다. 

:::code language="csharp" source="~/azure-functions-dotnet-worker/samples/FunctionApp/Program.cs" id="docsnippet_host_run":::

### <a name="configuration"></a>구성

호스트 빌더 파이프라인에 대 한 액세스 권한이 있는 경우 초기화 하는 동안 앱 별 구성을 설정할 수 있습니다. 이러한 구성은 별도의 프로세스로 실행 되는 함수 앱에 적용 됩니다. 함수 호스트나 트리거 및 바인딩 구성을 변경 하려면 [ 파일에host.js](functions-host-json.md)를 사용 해야 합니다.      

<!--The following example shows how to add configuration `args`, which are read as command-line arguments: 
 
:::code language="csharp" 
                .ConfigureAppConfiguration(c =>
                {
                    c.AddCommandLine(args);
                })
                :::

The `ConfigureAppConfiguration` method is used to configure the rest of the build process and application. This example also uses an [IConfigurationBuilder](/dotnet/api/microsoft.extensions.configuration.iconfigurationbuilder?view=dotnet-plat-ext-5.0&preserve-view=true), which makes it easier to add multiple configuration items. Because `ConfigureAppConfiguration` returns the same instance of [`IConfiguration`](/dotnet/api/microsoft.extensions.configuration.iconfiguration?view=dotnet-plat-ext-5.0&preserve-view=true), you can also just call it multiple times to add multiple configuration items.-->  
및 둘 다에서 구성의 전체 집합에 액세스할 수 있습니다 [`HostBuilderContext.Configuration`](/dotnet/api/microsoft.extensions.hosting.hostbuildercontext.configuration?view=dotnet-plat-ext-5.0&preserve-view=true) [`IHost.Services`](/dotnet/api/microsoft.extensions.hosting.ihost.services?view=dotnet-plat-ext-5.0&preserve-view=true) .

구성에 대 한 자세한 내용은 [ASP.NET Core 구성](/aspnet/core/fundamentals/configuration/?view=aspnetcore-5.0&preserve-view=true)을 참조 하세요. 

### <a name="dependency-injection"></a>종속성 주입

종속성 주입은 .NET 클래스 라이브러리와 비교 하 여 간소화 됩니다. 서비스를 등록 하기 위해 시작 클래스를 만들어야 하는 대신 `ConfigureServices` 호스트 작성기에서를 호출 하 고의 확장 메서드를 사용 하 여 [`IServiceCollection`](/dotnet/api/microsoft.extensions.dependencyinjection.iservicecollection?view=dotnet-plat-ext-5.0&preserve-view=true) 특정 서비스를 삽입 하기만 하면 됩니다. 

다음 예에서는 singleton 서비스 종속성을 삽입 합니다.  
 
:::code language="csharp" source="~/azure-functions-dotnet-worker/samples/FunctionApp/Program.cs" id="docsnippet_dependency_injection" :::

자세히 알아보려면 [ASP.NET Core의 종속성 주입](/aspnet/core/fundamentals/dependency-injection?view=aspnetcore-5.0&preserve-view=true)을 참조 하세요.

<!--### Middleware

.NET isolated also supports middleware registration, again by using a model similar to what exists in ASP.NET. This model gives you the ability to inject logic into the invocation pipeline, and before and after functions execute.

While the full middleware registration set of APIs is not yet exposed, we do support middleware registration and have added an example to the sample application under the Middleware folder.

:::code language="csharp" source="~/azure-functions-dotnet-worker/samples/FunctionApp/Program.cs" id="docsnippet_middleware" :::-->

## <a name="execution-context"></a>실행 컨텍스트

.NET 격리는 `FunctionContext` 함수 메서드에 개체를 전달 합니다. 이 개체를 사용 하면 [`ILogger`](/dotnet/api/microsoft.extensions.logging.ilogger?view=dotnet-plat-ext-5.0&preserve-view=true) 메서드를 호출 `GetLogger` 하 고 문자열을 제공 하 여 로그에 쓸 인스턴스를 가져올 수 있습니다 `categoryName` . 자세히 알아보려면 [로깅](#logging)을 참조 하세요. 

## <a name="bindings"></a>바인딩 

바인딩은 메서드, 매개 변수 및 반환 형식에 대해 특성을 사용 하 여 정의 됩니다. 함수 메서드는 `Function` 다음 예제와 같이 입력 매개 변수에 적용 되는 및 트리거 특성이 있는 메서드입니다.

:::code language="csharp" source="~/azure-functions-dotnet-worker/samples/Extensions/Queue/QueueFunction.cs" id="docsnippet_queue_trigger" :::

트리거 특성은 트리거 유형을 지정하고, 입력 데이터를 메서드 매개 변수에 바인딩합니다. 이전 예제 함수는 큐 메시지에 의해 트리거되고 큐 메시지는 매개 변수의 메서드에 전달 됩니다 `myQueueItem` .

`Function` 특성은 메서드를 함수 진입점으로 표시합니다. 이름은 프로젝트 내에서 고유 해야 하 고, 문자로 시작 하 고, 문자, 숫자, 및 문자 ( `_` `-` 최대 127 자)를 포함 해야 합니다. 프로젝트 템플릿에서 `Run` 메서드를 자주 만들지만, 유효한 C# 이름은 모두 메서드 이름이 될 수 있습니다.

.NET 격리 프로젝트는 별도의 작업자 프로세스에서 실행 되기 때문에 바인딩은,, 등의 다양 한 바인딩 클래스를 활용할 수 없습니다 `ICollector<T>` `IAsyncCollector<T>` `CloudBlockBlob` . 또한 [Documentclient](/dotnet/api/microsoft.azure.documents.client.documentclient) 및 [BrokeredMessage](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage)와 같은 기본 서비스 sdk에서 상속 된 형식을 직접 지원 하지 않습니다. 대신, 바인딩은 일반 POCOs (일반 클래스 개체)와 같이 문자열, 배열 및 serialize 가능한 형식에 의존 합니다. 

HTTP 트리거의 경우 및를 사용 `HttpRequestData` 하 여 `HttpResponseData` 요청 및 응답 데이터에 액세스 해야 합니다. 이는 out-of-process를 실행할 때 원래 HTTP 요청 및 응답 개체에 액세스할 수 없기 때문입니다. 

### <a name="input-bindings"></a>입력 바인딩

함수에는 데이터를 함수에 전달할 수 있는 0 개 이상의 입력 바인딩이 있을 수 있습니다. 트리거와 마찬가지로 입력 바인딩은 입력 매개 변수에 바인딩 특성을 적용 하 여 정의 합니다. 함수가 실행 되 면 런타임에서는 바인딩에 지정 된 데이터를 가져오려고 시도 합니다. 요청 되는 데이터는 일반적으로 바인딩 매개 변수를 사용 하 여 트리거에서 제공 하는 정보에 따라 달라 집니다.  

### <a name="output-bindings"></a>출력 바인딩

출력 바인딩에 쓰려면 바인딩된 서비스에 쓰는 방법을 정의 하는 함수 메서드에 출력 바인딩 특성을 적용 해야 합니다. 메서드에서 반환 되는 값은 출력 바인딩에 기록 됩니다. 예를 들어 다음 예제에서는 `functiontesting2` 출력 바인딩을 사용 하 여 라는 메시지 큐에 문자열 값을 씁니다.

:::code language="csharp" source="~/azure-functions-dotnet-worker/samples/Extensions/Queue/QueueFunction.cs" id="docsnippet_queue_output_binding" :::

### <a name="multiple-output-bindings"></a>여러 출력 바인딩

출력 바인딩에 기록 된 데이터는 항상 함수의 반환 값입니다. 둘 이상의 출력 바인딩에 써야 하는 경우 사용자 지정 반환 형식을 만들어야 합니다. 이 반환 형식에는 클래스의 하나 이상의 속성에 적용 되는 출력 바인딩 특성이 있어야 합니다. 다음 예제에서는 HTTP 응답과 큐 출력 바인딩에 모두 씁니다.

:::code language="csharp" source="~/azure-functions-dotnet-worker/samples/FunctionApp/Function1/Function1.cs" id="docsnippet_multiple_outputs":::

### <a name="http-trigger"></a>HTTP 트리거

HTTP 트리거는 들어오는 HTTP 요청 메시지를 `HttpRequestData` 함수에 전달 된 개체로 변환 합니다. 이 개체는 `Headers` ,, `Cookies` `Identities` , `URL` 및 선택적 메시지를 포함 하 여 요청의 데이터를 제공 `Body` 합니다. 이 개체는 요청 자체가 아닌 HTTP 요청 개체의 표현입니다. 

마찬가지로 함수는 메시지, 메시지 등의 메시지를 포함 하 여 `HttpReponseData` HTTP 응답을 만드는 데 사용 되는 데이터를 제공 하는 개체를 반환 합니다 `StatusCode` `Headers` `Body` .  

다음 코드는 HTTP 트리거입니다. 

:::code language="csharp" source="~/azure-functions-dotnet-worker/samples/Extensions/Http/HttpFunction.cs" id="docsnippet_http_trigger" :::

## <a name="logging"></a>로깅

.NET 격리에서 [`ILogger`](/dotnet/api/microsoft.extensions.logging.ilogger?view=dotnet-plat-ext-5.0&preserve-view=true) 함수에 전달 된 개체에서 가져온 인스턴스를 사용 하 여 로그에 쓸 수 있습니다 `FunctionContext` . `GetLogger`로그를 쓸 범주의 이름인 문자열 값을 전달 하 여 메서드를 호출 합니다. 범주는 일반적으로 로그가 기록 되는 특정 함수의 이름입니다. 범주에 대해 자세히 알아보려면 [모니터링 문서](functions-monitoring.md#log-levels-and-categories)를 참조 하세요. 

다음 예제에서는를 가져오고 함수 내에서 로그를 작성 하는 방법을 보여 줍니다 `ILogger` .

:::code language="csharp" source="~/azure-functions-dotnet-worker/samples/Extensions/Http/HttpFunction.cs" id="docsnippet_logging" ::: 

의 다양 한 방법을 사용 `ILogger` 하 여 또는와 같은 다양 한 로그 수준을 작성 `LogWarning` `LogError` 합니다. 로그 수준에 대 한 자세한 내용은 [모니터링 문서](functions-monitoring.md#log-levels-and-categories)를 참조 하세요.

는 [`ILogger`](/dotnet/api/microsoft.extensions.logging.ilogger?view=dotnet-plat-ext-5.0&preserve-view=true) [종속성 주입](#dependency-injection)을 사용 하는 경우에도 제공 됩니다.

## <a name="differences-with-net-class-library-functions"></a>.NET 클래스 라이브러리 함수와의 차이점

이 섹션에서는 in-process로 실행 되는 .NET 클래스 라이브러리 함수에 비해 .NET 5.0에서 실행 되는 함수 및 동작 차이점의 현재 상태에 대해 설명 합니다.

| 기능/동작 |  In-process (.NET Core 3.1) | Out-of-process (.NET 5.0) |
| ---- | ---- | ---- |
| .NET 버전 | LTS (.NET Core 3.1) | 현재 (.NET 5.0) |
| 핵심 패키지 | [Microsoft.NET.Sdk.Functions](https://www.nuget.org/packages/Microsoft.NET.Sdk.Functions/) | [Microsoft.Azure.Functions.Worker](https://www.nuget.org/packages/Microsoft.Azure.Functions.Worker/)<br/>[Microsoft.Azure.Functions.Worker.Sdk](https://www.nuget.org/packages/Microsoft.Azure.Functions.Worker.Sdk) | 
| 바인딩 확장 패키지 | [`Microsoft.Azure.WebJobs.Extensions.*`](https://www.nuget.org/packages?q=Microsoft.Azure.WebJobs.Extensions)  | 에서 [`Microsoft.Azure.Functions.Worker.Extensions.*`](https://www.nuget.org/packages?q=Microsoft.Azure.Functions.Worker.Extensions) | 
| 로깅 | [`ILogger`](/dotnet/api/microsoft.extensions.logging.ilogger?view=dotnet-plat-ext-5.0&preserve-view=true) 함수에 전달 됩니다. | [`ILogger`](/dotnet/api/microsoft.extensions.logging.ilogger?view=dotnet-plat-ext-5.0&preserve-view=true) 다음에서 가져옴 `FunctionContext` |
| 취소 토큰 | [지원됨](functions-dotnet-class-library.md#cancellation-tokens) | 지원되지 않음 |
| 출력 바인딩 | Out 매개 변수 | 반환 값 |
| 출력 바인딩 형식 |  `IAsyncCollector`, [Documentclient](/dotnet/api/microsoft.azure.documents.client.documentclient?view=azure-dotnet&preserve-view=true), [BrokeredMessage](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage?view=azure-dotnet&preserve-view=true)및 기타 클라이언트 관련 형식 | 단순 형식, JSON 직렬화 가능 형식 및 배열입니다. |
| 여러 출력 바인딩 | 지원됨 | [지원됨](#multiple-output-bindings) |
| HTTP 트리거 | [`HttpRequest`](/dotnet/api/microsoft.aspnetcore.http.httprequest?view=aspnetcore-5.0&preserve-view=true)/[`ObjectResult`](/dotnet/api/microsoft.aspnetcore.mvc.objectresult?view=aspnetcore-5.0&preserve-view=true) | `HttpRequestData`/`HttpResponseData` |
| 지속성 함수 | [지원됨](durable/durable-functions-overview.md) | 지원되지 않음 | 
| 명령적 바인딩 | [지원됨](functions-dotnet-class-library.md#binding-at-runtime) | 지원되지 않음 |
| 아티팩트의 function.js | 생성된 계획 | 생성 되지 않음 |
| 구성 | [host.json](functions-host-json.md) | [host.js설정](functions-host-json.md) 및 사용자 지정 초기화 |
| 종속성 주입 | [지원됨](functions-dotnet-dependency-injection.md)  | [지원됨](#dependency-injection) |
| 미들웨어 | 지원되지 않음 | 지원됨 |
| 콜드 시작 시간 | 일반 | 적시에 시작 되기 때문에 더 깁니다. 잠재적 지연을 줄이기 위해 Windows 대신 Linux에서를 실행 합니다. |
| ReadyToRun | [지원됨](functions-dotnet-class-library.md#readytorun) | _TBD_ |

## <a name="known-issues"></a>알려진 문제

.NET isolated 프로세스 함수를 실행 하는 문제를 파악 하기 위한 해결 방법에 대 한 자세한 내용은 [이 알려진 문제 페이지](https://aka.ms/AAbh18e)를 참조 하세요. 문제를 보고 하려면 [이 GitHub 리포지토리에서 문제를 만듭니다](https://github.com/Azure/azure-functions-dotnet-worker/issues/new/choose).  

## <a name="next-steps"></a>다음 단계

+ [트리거 및 바인딩에 대해 자세히 알아보기](functions-triggers-bindings.md)
+ [Azure Functions에 대한 모범 사례에 대해 자세히 알아보기](functions-best-practices.md)