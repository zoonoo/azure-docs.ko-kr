---
title: .NET Azure Functions에서 사용 하 여 종속성 주입
description: 등록 하 고 서비스를 사용 하 여.NET 함수에서는 대 한 종속성 주입을 사용 하는 방법 알아보기
services: functions
documentationcenter: na
author: ggailey777
manager: jeconnoc
keywords: azure functions, 함수, 서버리스 아키텍처
ms.service: azure-functions
ms.devlang: dotnet
ms.topic: reference
ms.date: 03/22/2019
ms.author: jehollan
ms.openlocfilehash: 2044718d2ec7a7acc58e1e7ba9ba04ec5caf16b3
ms.sourcegitcommit: 6f043a4da4454d5cb673377bb6c4ddd0ed30672d
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/08/2019
ms.locfileid: "65408458"
---
# <a name="use-dependency-injection-in-net-azure-functions"></a>.NET Azure Functions에서 사용 하 여 종속성 주입

Azure Functions는 종속성 주입 (DI) 소프트웨어 디자인 패턴이 며은 달성 하기 위한 기술 지원 [제어 반전 (IoC)](https://docs.microsoft.com/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#dependency-inversion) 클래스와 해당 종속성입니다.

Azure Functions는 ASP.NET Core 종속성 주입 기능을 기반으로 작성 합니다.  서비스, 수명 및 디자인 패턴을 이해 해야 [ASP.NET Core 종속성 주입](https://docs.microsoft.com/aspnet/core/fundamentals/dependency-injection) 함수에서 사용 하기 전에 합니다.

## <a name="installing-dependency-injection-packages"></a>종속성 주입 패키지 설치

종속성 주입 기능을 사용 하려면 해당 Api를 노출 하는 NuGet 패키지를 포함 하도록 해야 합니다.

```powershell
Install-Package Microsoft.Azure.Functions.Extensions
```

## <a name="registering-services"></a>서비스 등록

서비스를 등록 하려면 구성 메서드를 만들고 추가 하는 구성 요소는 `IFunctionsHostBuilder` 인스턴스.  Azure Functions 호스트를 만듭니다는 `IFunctionsHostBuilder` 구성 된 메서드로 직접 전달 합니다.

등록에 메서드를 구성에 대 한 형식을 지정 하는 어셈블리 특성을 추가 해야 합니다에 메서드를 사용 하 여 구성의 `FunctionsStartup` 특성입니다.

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

## <a name="service-lifetimes"></a>서비스 수명

Azure 함수 앱 제공으로 동일한 서비스 수명 [ASP.NET 종속성 주입](https://docs.microsoft.com/aspnet/core/fundamentals/dependency-injection#service-lifetimes)일시적인, 범위, 및 단일 항목입니다.

함수 앱으로 범위가 지정 된 서비스 수명 함수 실행 수명을 일치합니다. 범위가 지정 된 서비스는 실행 당 한 번 생성 됩니다.  해당 인스턴스를 다시 사용 하는 실행 하는 동안 해당 서비스에 대 한 이후 요청 합니다.  Singleton 서비스 수명 호스트 수명 일치 하 고 해당 인스턴스에서 함수 실행 간에 다시 사용 됩니다.

Singleton 수명 서비스 연결 및 클라이언트에 대 한 예를 들어 권장 되는 `SqlConnection`, `CloudBlobClient`, 또는 `HttpClient`합니다.

보기 또는 다운로드 한 [다양 한 서비스 수명이 샘플](https://aka.ms/functions/di-sample)합니다.

## <a name="logging-services"></a>로깅 서비스

권장 되는 방식은 등록 하려면 사용자 고유의 로깅 공급자에 필요한 경우는 `ILoggerProvider`합니다.  Application Insights에 대 한 함수에서 자동으로 추가 Application Insights 자동으로 합니다.  

> [!WARNING]
> 추가 하지 않으면 `AddApplicationInsightsTelemetry()` 서비스 컬렉션 충돌 하는 서비스에 등록 됩니다 사용 하 여 제공 됩니다 환경에서. 
 
## <a name="function-app-provided-services"></a>제공 하는 앱 서비스 함수

함수 호스트가 자체 다양 한 서비스 등록 됩니다.  다음은 안전에 의존 하 게 하는 서비스입니다.  다른 호스트 서비스를 등록 하거나 종속 지원 되지 않습니다.  하세요에 종속성을 유지 하려는 경우 다른 서비스 [GitHub에서 문제 및 토론 만들기](https://github.com/azure/azure-functions-host)합니다.

|서비스 유형|수명|설명|
|--|--|--|
|`Microsoft.Extensions.Configuration.IConfiguration`|단일 항목|런타임 구성|
|`Microsoft.Azure.WebJobs.Host.Executors.IHostIdProvider`|단일 항목|호스트 인스턴스의 ID를 제공 합니다.|

### <a name="overriding-host-services"></a>호스트 서비스를 재정의합니다.

호스트에서 제공 하는 서비스를 재정의 합니다. 현재 지원 되지 않습니다.  재정의 하려는 서비스의 경우 하세요 [GitHub에서 문제 및 토론 만들기](https://github.com/azure/azure-functions-host)합니다.

## <a name="next-steps"></a>다음 단계

자세한 내용은 다음 리소스를 참조하세요.

* [함수 앱을 모니터링 하는 방법](functions-monitoring.md)
* [함수에 대 한 모범 사례](functions-best-practices.md)