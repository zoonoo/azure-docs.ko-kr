---
title: .NET Azure Functions에서 종속성 주입 사용
description: .NET 함수에서 서비스를 등록하고 사용하는 데 종속성 주입을 사용하는 방법에 대해 알아봅니다.
author: craigshoemaker
ms.topic: reference
ms.date: 09/05/2019
ms.author: cshoe
ms.reviewer: jehollan
ms.openlocfilehash: a1ff8e0aedce5d3a6acc9a39084cf0839efdd88e
ms.sourcegitcommit: acb82fc770128234f2e9222939826e3ade3a2a28
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/21/2020
ms.locfileid: "81678449"
---
# <a name="use-dependency-injection-in-net-azure-functions"></a>.NET Azure Functions에서 종속성 주입 사용

Azure Functions는 클래스와 해당 종속성 간에 [제어(IoC)의 반전을](https://docs.microsoft.com/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#dependency-inversion) 달성하는 기술인 DI(종속성 주입) 소프트웨어 디자인 패턴을 지원합니다.

- Azure 함수의 종속성 주입은 .NET 코어 종속성 주입 기능을 기반으로 합니다. [.NET Core 종속성 주입에](https://docs.microsoft.com/aspnet/core/fundamentals/dependency-injection) 익숙해지는 것이 좋습니다. 종속성을 재정의하는 방법과 사용량 계획에서 Azure Functions를 통해 구성 값을 읽는 방법에는 차이가 있습니다.

- 종속성 주입에 대한 지원은 Azure Functions 2.x에서 시작합니다.

## <a name="prerequisites"></a>사전 요구 사항

종속성 주입을 사용하려면 먼저 다음 NuGet 패키지를 설치해야 합니다.

- [마이크로소프트.Azure.Functions.확장](https://www.nuget.org/packages/Microsoft.Azure.Functions.Extensions/)

- [Microsoft.NET.Sdk.Functions 패키지](https://www.nuget.org/packages/Microsoft.NET.Sdk.Functions/) 버전 1.0.28 이상

## <a name="register-services"></a>서비스 등록

서비스를 등록하려면 인스턴스에 구성 요소를 구성하고 `IFunctionsHostBuilder` 추가하는 메서드를 만듭니다.  Azure Functions 호스트는 `IFunctionsHostBuilder` 인스턴스를 만들고 메서드에 직접 전달합니다.

메서드를 등록하려면 시작 `FunctionsStartup` 중에 사용되는 형식 이름을 지정하는 어셈블리 특성을 추가합니다.

```csharp
using System;
using Microsoft.Azure.Functions.Extensions.DependencyInjection;
using Microsoft.Extensions.DependencyInjection;
using Microsoft.Extensions.Http;
using Microsoft.Extensions.Logging;

[assembly: FunctionsStartup(typeof(MyNamespace.Startup))]

namespace MyNamespace
{
    public class Startup : FunctionsStartup
    {
        public override void Configure(IFunctionsHostBuilder builder)
        {
            builder.Services.AddHttpClient();

            builder.Services.AddSingleton((s) => {
                return new MyService();
            });

            builder.Services.AddSingleton<ILoggerProvider, MyLoggerProvider>();
        }
    }
}
```

### <a name="caveats"></a>제한 사항

런타임이 시작 클래스를 처리하기 전과 후에 일련의 등록 단계가 실행됩니다. 따라서 다음 항목을 염두에 두십시오.

- *시작 클래스는 설치 및 등록전용입니다.* 시작 프로세스 중에 시작 시 등록된 서비스를 사용하지 마십시오. 예를 들어 시작 중에 등록된 로거에 메시지를 기록하지 마십시오. 등록 프로세스의 이 시점은 서비스를 사용할 수 없을 정도로 이르다. 메서드를 `Configure` 실행한 후 Functions 런타임은 추가 종속성을 계속 등록하므로 서비스 작동 방식에 영향을 줄 수 있습니다.

- *종속성 주입 컨테이너는 명시적으로 등록된 형식만 보유합니다.* 주사용 유형으로 사용할 수 있는 유일한 서비스는 `Configure` 메서드에서 설정되는 서비스입니다. 결과적으로 설치 중 또는 주사용 `BindingContext` `ExecutionContext` 유형으로 사용할 수 있는 함수 별 형식과 같은 형식이 없습니다.

## <a name="use-injected-dependencies"></a>주입된 종속성 사용

생성자 삽입은 함수에서 종속성을 사용할 수 있도록 하는 데 사용됩니다. 생성자 주입을 사용하려면 정적 클래스를 사용하지 않아도 됩니다.

다음 샘플에서는 HTTP `IMyService` 트리거 `HttpClient` 함수에 종속성이 주입되는 방법을 보여 줍니다. 이 예제에서는 시작 `HttpClient` 시 등록하는 데 필요한 [Microsoft.Extensions.Http](https://www.nuget.org/packages/Microsoft.Extensions.Http/) 패키지를 사용합니다.

```csharp
using System;
using System.IO;
using System.Threading.Tasks;
using Microsoft.AspNetCore.Mvc;
using Microsoft.Azure.WebJobs;
using Microsoft.Azure.WebJobs.Extensions.Http;
using Microsoft.AspNetCore.Http;
using Microsoft.Extensions.Logging;
using System.Net.Http;

namespace MyNamespace
{
    public class HttpTrigger
    {
        private readonly IMyService _service;
        private readonly HttpClient _client;

        public HttpTrigger(IMyService service, HttpClient httpClient)
        {
            _service = service;
            _client = httpClient;
        }

        [FunctionName("GetPosts")]
        public async Task<IActionResult> Get(
            [HttpTrigger(AuthorizationLevel.Function, "get", Route = "posts")] HttpRequest req,
            ILogger log)
        {
            log.LogInformation("C# HTTP trigger function processed a request.");
            var res = await _client.GetAsync("https://microsoft.com");
            await _service.AddResponse(res);

            return new OkResult();
        }
    }
}
```

## <a name="service-lifetimes"></a>서비스 수명

Azure Functions 앱은 [ASP.NET 종속성 주입과](https://docs.microsoft.com/aspnet/core/fundamentals/dependency-injection#service-lifetimes)동일한 서비스 수명을 제공합니다. Functions 앱의 경우 다른 서비스 수명은 다음과 같이 작동합니다.

- **일시적**: 일시적인 서비스는 서비스의 각 요청에 따라 생성됩니다.
- **범위 :** 범위가 조정된 서비스 수명은 함수 실행 수명과 일치합니다. 범위 별 서비스는 실행당 한 번 만들어집니다. 나중에 실행 중에 해당 서비스에 대한 요청은 기존 서비스 인스턴스를 다시 사용합니다.
- **Singleton**: 싱글톤 서비스 수명은 호스트 수명과 일치하며 해당 인스턴스의 함수 실행 간에 다시 사용됩니다. 싱글톤 수명 서비스는 연결 및 클라이언트(예: `SqlConnection` 인스턴스)에 `HttpClient` 권장됩니다.

GitHub에서 다양한 서비스 수명 샘플을 보거나 [다운로드합니다.](https://aka.ms/functions/di-sample)

## <a name="logging-services"></a>로깅 서비스

고유한 로깅 공급자가 필요한 경우 사용자 `ILoggerProvider` 지정 형식을 인스턴스로 등록합니다. 응용 프로그램 인사이트는 Azure 함수에 의해 자동으로 추가됩니다.

> [!WARNING]
> - 환경에서 제공하는 `AddApplicationInsightsTelemetry()` 서비스와 충돌하는 서비스를 등록할 때 서비스 컬렉션에 추가하지 마십시오.
> - 직접 `TelemetryConfiguration` 등록하지 않거나 `TelemetryClient` 기본 제공 Application Insights 기능을 사용하는 경우 등록하지 마십시오. 사용자 고유의 `TelemetryClient` 인스턴스를 구성해야 하는 경우 `TelemetryConfiguration` [Azure Functions 모니터에](./functions-monitoring.md#version-2x-and-later-2)표시된 대로 주입을 통해 인스턴스를 만듭니다.

### <a name="iloggert-and-iloggerfactory"></a>아이로거와<T> 아이로거팩토리

호스트는 생성자에 삽입하고 `ILogger<T>` `ILoggerFactory` 서비스를 제공합니다.  그러나 기본적으로 이러한 새 로깅 필터는 함수 로그에서 필터링됩니다.  추가 필터 및 `host.json` 범주를 선택하려면 파일을 수정해야 합니다.  다음 샘플에서는 호스트가 `ILogger<HttpTrigger>` 노출할 로그를 추가하는 것을 보여 줍니다.

```csharp
namespace MyNamespace
{
    public class HttpTrigger
    {
        private readonly ILogger<HttpTrigger> _log;

        public HttpTrigger(ILogger<HttpTrigger> log)
        {
            _log = log;
        }

        [FunctionName("HttpTrigger")]
        public async Task<IActionResult> Run(
            [HttpTrigger(AuthorizationLevel.Anonymous, "get", "post", Route = null)] HttpRequest req)
        {
            _log.LogInformation("C# HTTP trigger function processed a request.");

            // ...
    }
}
```

로그 `host.json` 필터를 추가하는 파일입니다.

```json
{
    "version": "2.0",
    "logging": {
        "applicationInsights": {
            "samplingExcludedTypes": "Request",
            "samplingSettings": {
                "isEnabled": true
            }
        },
        "logLevel": {
            "MyNamespace.HttpTrigger": "Information"
        }
    }
}
```

## <a name="function-app-provided-services"></a>기능 앱 제공 서비스

함수 호스트는 많은 서비스를 등록합니다. 다음 서비스는 응용 프로그램에서 종속성으로 안전하게 수행할 수 있습니다.

|서비스 유형|수명|설명|
|--|--|--|
|`Microsoft.Extensions.Configuration.IConfiguration`|Singleton|런타임 구성|
|`Microsoft.Azure.WebJobs.Host.Executors.IHostIdProvider`|Singleton|호스트 인스턴스의 ID 제공담당|

종속성을 수행하려는 다른 서비스가 있는 경우 [문제를 만들고 GitHub 에 제안하십시오.](https://github.com/azure/azure-functions-host)

### <a name="overriding-host-services"></a>호스트 서비스 재정의

호스트에서 제공하는 재정의 서비스는 현재 지원되지 않습니다.  재정의하려는 서비스가 있는 경우 [문제를 만들고 GitHub 에 제안하십시오.](https://github.com/azure/azure-functions-host)

## <a name="working-with-options-and-settings"></a>옵션 및 설정 작업

[앱 설정에](./functions-how-to-use-azure-function-app-settings.md#settings) 정의된 값은 `IConfiguration` 인스턴스에서 사용할 수 있으며, 이를 통해 시작 클래스에서 앱 설정 값을 읽을 수 있습니다.

`IConfiguration` 인스턴스에서 사용자 지정 유형으로 값을 추출할 수 있습니다. 앱 설정 값을 사용자 지정 형식에 복사하면 이러한 값을 주용으로 만들어 서비스를 쉽게 테스트할 수 있습니다. 구성 인스턴스로 읽는 설정은 간단한 키/값 쌍이어야 합니다.

앱 설정과 일치하는 이름의 속성이 포함된 다음 클래스를 고려하십시오.

```csharp
public class MyOptions
{
    public string MyCustomSetting { get; set; }
}
```

사용자 `local.settings.json` 지정 설정을 다음과 같이 구성할 수 있는 파일은 다음과 같습니다.
```json
{
  "IsEncrypted": false,
  "Values": {
    "MyOptions:MyCustomSetting": "Foobar"
  }
}
```

`Startup.Configure` 메서드 내부에서 다음 코드를 사용하여 `IConfiguration` 인스턴스에서 사용자 지정 유형으로 값을 추출할 수 있습니다.

```csharp
builder.Services.AddOptions<MyOptions>()
                .Configure<IConfiguration>((settings, configuration) =>
                                           {
                                                configuration.GetSection("MyOptions").Bind(settings);
                                           });
```

구성에서 사용자 지정 인스턴스로 속성 이름이 일치하는 복사 값을 호출합니다. `Bind` 이제 IoC 컨테이너에서 함수에 삽입할 수 있는 옵션 인스턴스를 사용할 수 있습니다.

옵션 개체는 제네릭 `IOptions` 인터페이스의 인스턴스로 함수에 삽입됩니다. 속성을 `Value` 사용하여 구성에 있는 값에 액세스합니다.

```csharp
using System;
using Microsoft.Extensions.Options;

public class HttpTrigger
{
    private readonly MyOptions _settings;

    public HttpTrigger(IOptions<MyOptions> options)
    {
        _settings = options.Value;
    }
}
```

옵션 작업에 대한 자세한 내용은 [ASP.NET 코어의 옵션 패턴을](https://docs.microsoft.com/aspnet/core/fundamentals/configuration/options) 참조하십시오.

> [!WARNING]
> *local.settings.json* 또는 appsettings.{ 같은 파일에서 값을 읽으려고 시도하지 *마십시오. 환경}.json* 소비 계획에. 호스팅 인프라가 구성 정보에 액세스할 수 없기 때문에 트리거 연결과 관련된 이러한 파일에서 읽은 값은 앱 확장으로 사용할 수 없습니다.

## <a name="next-steps"></a>다음 단계

자세한 내용은 다음 자료를 참조하세요.

- [함수 앱을 모니터링하는 방법](functions-monitoring.md)
- [함수에 대한 모범 사례](functions-best-practices.md)
