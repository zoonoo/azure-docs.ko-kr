---
title: .NET Azure Functions에서 종속성 주입 사용
description: .NET 함수에서 서비스를 등록 하 고 사용 하는 데 종속성 주입을 사용 하는 방법에 대해 알아봅니다.
services: functions
documentationcenter: na
author: craigshoemaker
manager: gwallace
keywords: azure functions, 함수, 서버리스 아키텍처
ms.service: azure-functions
ms.devlang: dotnet
ms.topic: reference
ms.date: 05/28/2019
ms.author: cshoe
ms.reviewer: jehollan
ms.openlocfilehash: 1dbbb6e7bd88e08520225515c422529dc260e1b2
ms.sourcegitcommit: b49431b29a53efaa5b82f9be0f8a714f668c38ab
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/22/2019
ms.locfileid: "68377380"
---
# <a name="use-dependency-injection-in-net-azure-functions"></a>.NET Azure Functions에서 종속성 주입 사용

Azure Functions는 클래스와 해당 종속성 간의 [IoC (제어 반전)](https://docs.microsoft.com/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#dependency-inversion) 를 구현 하는 기술인 DI (종속성 주입) 소프트웨어 디자인 패턴을 지원 합니다.

Azure Functions는 ASP.NET Core 종속성 주입 기능을 기반으로 작성 됩니다. Azure Functions 앱에서 DI 기능을 사용 하기 전에 [ASP.NET Core 종속성 주입](https://docs.microsoft.com/aspnet/core/fundamentals/dependency-injection) 의 서비스, 수명 및 디자인 패턴을 인식 하는 것이 좋습니다.

종속성 주입에 대 한 지원은 Azure Functions 2.x로 시작 합니다.

## <a name="prerequisites"></a>필수 구성 요소

종속성 주입을 사용 하려면 먼저 다음 NuGet 패키지를 설치 해야 합니다.

- [Microsoft.Azure.Functions.Extensions](https://www.nuget.org/packages/Microsoft.Azure.Functions.Extensions/)

- 1\.0.28 [패키지](https://www.nuget.org/packages/Microsoft.NET.Sdk.Functions/) 버전 이상 (영문)

- 선택 사항: 시작 시 HttpClient를 등록 하는 경우에만 필요 합니다 [.](https://www.nuget.org/packages/Microsoft.Extensions.Http/)

## <a name="register-services"></a>서비스 등록

서비스를 등록 하려면 `IFunctionsHostBuilder` 인스턴스를 구성 하 고 구성 요소를 추가 하는 메서드를 만들 수 있습니다.  Azure Functions 호스트는의 `IFunctionsHostBuilder` 인스턴스를 만들고이를 메서드에 직접 전달 합니다.

메서드를 등록 하려면 시작 하는 `FunctionsStartup` 동안 사용 되는 형식 이름을 지정 하는 assembly 특성을 추가 합니다. 또한 코드는 Nuget에서 [Cosmos](https://www.nuget.org/packages/Microsoft.Azure.Cosmos/) 의 시험판을 참조 합니다.

```csharp
using System;
using Microsoft.Azure.Functions.Extensions.DependencyInjection;
using Microsoft.Extensions.DependencyInjection;
using Microsoft.Extensions.Http;
using Microsoft.Extensions.Logging;
using Microsoft.Azure.Cosmos;

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

## <a name="use-injected-dependencies"></a>삽입 된 종속성 사용

ASP.NET Core는 생성자 주입을 사용 하 여 함수에서 종속성을 사용할 수 있도록 합니다. 다음 샘플에서는 `IMyService` 및 `HttpClient` 종속성이 HTTP로 트리거되는 함수에 삽입 되는 방법을 보여 줍니다.

```csharp
using System;
using System.IO;
using System.Threading.Tasks;
using Microsoft.AspNetCore.Mvc;
using Microsoft.Azure.WebJobs;
using Microsoft.Azure.WebJobs.Extensions.Http;
using Microsoft.AspNetCore.Http;
using Microsoft.Extensions.Logging;
using Newtonsoft.Json;

namespace MyNamespace
{
    public class HttpTrigger
    {
        private readonly IMyService _service;
        private readonly HttpClient _client;

        public HttpTrigger(IMyService service, IHttpClientFactory httpClientFactory)
        {
            _service = service;
            _client = httpClientFactory.CreateClient();
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

생성자 주입을 사용 하는 것은 종속성 주입을 활용 하려는 경우 정적 함수를 사용 하지 않아야 함을 의미 합니다.

## <a name="service-lifetimes"></a>서비스 수명

Azure Functions 앱은 [ASP.NET 종속성 주입](https://docs.microsoft.com/aspnet/core/fundamentals/dependency-injection#service-lifetimes)과 동일한 서비스 수명 (임시, 범위 및 단일 항목)을 제공 합니다.

함수 앱에서 범위가 지정 된 서비스 수명은 함수 실행 수명과 일치 합니다. 범위가 지정 된 서비스는 실행 당 한 번 생성 됩니다. 실행 중에 해당 서비스에 대 한 이후 요청은 기존 서비스 인스턴스를 다시 사용 합니다. 단일 서비스 수명은 호스트 수명과 일치 하며 해당 인스턴스에서 함수 실행을 통해 다시 사용 됩니다.

단일 수명 서비스는 연결 및 클라이언트 (예: `SqlConnection` `CloudBlobClient`또는 `HttpClient` 인스턴스)에 권장 됩니다.

GitHub의 [다른 서비스 수명 샘플](https://aka.ms/functions/di-sample) 을 보거나 다운로드 합니다.

## <a name="logging-services"></a>로깅 서비스

사용자 고유의 로깅 공급자가 필요한 경우 `ILoggerProvider` 인스턴스를 등록 하는 것이 좋습니다. Application Insights는 Azure Functions 자동으로 추가 됩니다.

> [!WARNING]
> 환경에서 제공 `AddApplicationInsightsTelemetry()` 하는 서비스와 충돌 하는 서비스를 등록 하므로 서비스 컬렉션에 추가 하지 마세요.

## <a name="function-app-provided-services"></a>함수 앱 제공 서비스

함수 호스트는 많은 서비스를 등록 합니다. 다음 서비스는 응용 프로그램에서 종속성으로 사용 하기에 안전 합니다.

|서비스 종류|수명|Description|
|--|--|--|
|`Microsoft.Extensions.Configuration.IConfiguration`|단일|런타임 구성|
|`Microsoft.Azure.WebJobs.Host.Executors.IHostIdProvider`|단일|호스트 인스턴스의 ID를 제공 해야 합니다.|

종속성을 적용 하려는 다른 서비스가 있는 경우 [문제를 만들어 GitHub에서 제안](https://github.com/azure/azure-functions-host)합니다.

### <a name="overriding-host-services"></a>호스트 서비스 재정의

호스트에서 제공 하는 서비스 재정의는 현재 지원 되지 않습니다.  재정의 하려는 서비스가 있는 경우 [문제를 만들어 GitHub에서 제안](https://github.com/azure/azure-functions-host)합니다.

## <a name="next-steps"></a>다음 단계

자세한 내용은 다음 리소스를 참조하십시오.

- [함수 앱을 모니터링 하는 방법](functions-monitoring.md)
- [함수에 대 한 모범 사례](functions-best-practices.md)
