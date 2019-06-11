---
title: .NET Azure Functions에서 사용 하 여 종속성 주입
description: 등록 하 고 서비스를 사용 하 여.NET 함수에서는 대 한 종속성 주입을 사용 하는 방법 알아보기
services: functions
documentationcenter: na
author: craigshoemaker
manager: jeconnoc
keywords: azure functions, 함수, 서버리스 아키텍처
ms.service: azure-functions
ms.devlang: dotnet
ms.topic: reference
ms.date: 05/28/2019
ms.author: jehollan, glenga, cshoe
ms.openlocfilehash: b1a6751f0d788c26af60b28eee994dc9b3877f00
ms.sourcegitcommit: 18a0d58358ec860c87961a45d10403079113164d
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/05/2019
ms.locfileid: "66693259"
---
# <a name="use-dependency-injection-in-net-azure-functions"></a>.NET Azure Functions에서 사용 하 여 종속성 주입

Azure Functions는 종속성 주입 (DI) 소프트웨어 디자인 패턴을 달성 하기 위해 기술 지원 [제어 반전 (IoC)](https://docs.microsoft.com/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#dependency-inversion) 클래스와 해당 종속성입니다.

Azure Functions는 ASP.NET Core 종속성 주입 기능을 기반으로 작성 합니다. 서비스, 수명 및 디자인 패턴을 인식 [ASP.NET Core 종속성 주입](https://docs.microsoft.com/aspnet/core/fundamentals/dependency-injection) Azure Functions에서 DI 기능을 사용 하기 전에 앱 것이 좋습니다.

## <a name="prerequisites"></a>필수 조건

종속성 주입을 사용 하려면 다음 NuGet 패키지를 설치 해야 합니다.

- [Microsoft.Azure.Functions.Extensions](https://www.nuget.org/packages/Microsoft.Azure.Functions.Extensions/)

- [Microsoft.NET.Sdk.Functions 패키지](https://www.nuget.org/packages/Microsoft.NET.Sdk.Functions/) 1.0.28 버전 이상

## <a name="register-services"></a>서비스 등록

서비스를 등록 하려면 구성 하 고 구성 요소를 추가 하는 메서드를 만들 수 있습니다는 `IFunctionsHostBuilder` 인스턴스.  Azure Functions 호스트의 인스턴스를 만들고 `IFunctionsHostBuilder` 메서드로 직접 전달 합니다.

메서드를 등록 하려면 추가 `FunctionsStartup` 시작 중에 형식 이름을 지정 하는 어셈블리 특성 사용.

```csharp
[assembly: FunctionsStartup(typeof(MyNamespace.Startup))]

namespace MyNamespace
{
    public class Startup : FunctionsStartup
    {
        public override void Configure(IFunctionsHostBuilder builder)
        {
            builder.Services.AddHttpClient();
            builder.Services.AddSingleton((s) => {
                return new CosmosClient(Environment.GetEnvironmentVariable("COSMOSDB_CONNECTIONSTRING"));
            });
            builder.Services.AddSingleton<ILoggerProvider, MyLoggerProvider>();
        }
    }
}
```

## <a name="use-injected-dependencies"></a>종속성 주입 사용

ASP.NET Core 종속성을 함수를 사용할 수 있도록 생성자 주입을 사용 합니다. 다음 샘플을 참조 하십시오. 방법을 `IMyService` 고 `HttpClient` 종속성 HTTP에서 트리거한 함수를 주입 됩니다.

```csharp
namespace MyNamespace
{
    public class HttpTrigger
    {
        private readonly IMyService _service;
        private readonly HttpClient _client;

        public HttpTrigger(IMyService service, IHttpClientFactory httpClientFactory)
        {
            _service = service;
            _client = httpClientFactory.CreateClient();;
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

생성자 주입 사용을 의미 종속성 주입을 활용 하려는 경우 정적 함수를 사용 하지 않아야 합니다.

## <a name="service-lifetimes"></a>서비스 수명

Azure 함수 앱 제공으로 동일한 서비스 수명 [ASP.NET 종속성 주입](https://docs.microsoft.com/aspnet/core/fundamentals/dependency-injection#service-lifetimes): 일시적인, 범위, 및 단일 항목입니다.

함수 앱에서 범위가 지정 된 서비스 수명 함수 실행 수명을 일치합니다. 범위가 지정 된 서비스는 실행 당 한 번 생성 됩니다. 실행 하는 동안 해당 서비스에 대 한 이후 요청에는 기존 서비스 인스턴스가 다시 사용합니다. Singleton 서비스 수명 호스트 수명 일치 하 고 해당 인스턴스에서 함수 실행 간에 다시 사용 됩니다.

Singleton 수명 서비스 연결 및 클라이언트에 대 한 예를 들어 권장 됩니다 `SqlConnection`하십시오 `CloudBlobClient`, 또는 `HttpClient` 인스턴스.

보기 또는 다운로드 한 [다양 한 서비스 수명이 샘플](https://aka.ms/functions/di-sample) GitHub에서.

## <a name="logging-services"></a>로깅 서비스

권장 되는 방식은 등록 하려면 사용자 고유의 로깅 공급자에 필요한 경우는 `ILoggerProvider` 인스턴스. Application Insights는 Azure Functions에서 자동으로 추가 됩니다.

> [!WARNING]
> 추가 하지 않으면 `AddApplicationInsightsTelemetry()` 충돌 하는 환경에서 제공 하는 서비스를 사용 하 여 등록 하므로 서비스 컬렉션을 서비스 합니다.

## <a name="function-app-provided-services"></a>제공 하는 앱 서비스 함수

함수 호스트는 여러 서비스를 등록합니다. 다음 서비스는 응용 프로그램의 종속성을 안전 하 게 합니다.

|서비스 유형|수명|설명|
|--|--|--|
|`Microsoft.Extensions.Configuration.IConfiguration`|단일 항목|런타임 구성|
|`Microsoft.Azure.WebJobs.Host.Executors.IHostIdProvider`|단일 항목|호스트 인스턴스의 ID를 제공 합니다.|

다른 서비스에 대 한 종속성을 가져오려는 경우 [문제 만들기 및 GitHub의 이러한 제안](https://github.com/azure/azure-functions-host)합니다.

### <a name="overriding-host-services"></a>호스트 서비스를 재정의합니다.

호스트에서 제공 하는 서비스를 재정의 합니다. 현재 지원 되지 않습니다.  서비스를 재정의 하려는 경우 [문제 만들기 및 GitHub의 이러한 제안](https://github.com/azure/azure-functions-host)합니다.

## <a name="next-steps"></a>다음 단계

자세한 내용은 다음 리소스를 참조하세요.

- [함수 앱을 모니터링 하는 방법](functions-monitoring.md)
- [함수에 대 한 모범 사례](functions-best-practices.md)
