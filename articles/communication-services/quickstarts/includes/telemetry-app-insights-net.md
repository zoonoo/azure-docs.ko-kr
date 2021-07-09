---
title: 포함 파일
description: 포함 파일
services: azure-communication-services
author: peiliu
manager: vravikumar
ms.service: azure-communication-services
ms.subservice: azure-communication-services
ms.date: 06/01/2021
ms.topic: include
ms.custom: include file
ms.author: peiliu
ms.openlocfilehash: fc76538023af79d84becc1662bf2402a3daadff4
ms.sourcegitcommit: b11257b15f7f16ed01b9a78c471debb81c30f20c
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/08/2021
ms.locfileid: "111593138"
---
## <a name="prerequisites"></a>사전 요구 사항

- 활성 구독이 있는 Azure 계정. [체험 계정을 만듭니다](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
- 운영 체제에 대한 최신 버전의 [.NET Core SDK](https://dotnet.microsoft.com/download/dotnet-core)
- 활성 Communication Services 리소스 및 연결 문자열입니다. [Communication Services 리소스를 만듭니다](../create-communication-resource.md).
- Azure Portal에서 [Application Insights 리소스](https://docs.microsoft.com/azure/azure-monitor/app/create-new-resource)를 만듭니다.

## <a name="setting-up"></a>설치

### <a name="create-a-new-c-application"></a>새 C# 애플리케이션 만들기

콘솔 창(예: cmd, PowerShell 또는 Bash)에서 `dotnet new` 명령을 사용하여 `TelemetryAppInsightsQuickstart`라는 새 콘솔 앱을 만듭니다. 이 명령은 **Program.cs** 라는 원본 파일 하나만 들어 있는 간단한 "Hello World" C# 프로젝트를 만듭니다.

```console
dotnet new console -o TelemetryAppInsightsQuickstart
```

디렉터리를 새로 만든 앱 폴더로 변경하고 `dotnet build` 명령을 사용하여 애플리케이션을 컴파일합니다.

```console
cd TelemetryAppInsightsQuickstart
dotnet build
```

### <a name="install-the-package"></a>패키지 설치

애플리케이션 디렉터리에 있는 동안 `dotnet add package` 명령을 사용하여 .NET 패키지용 Azure Communication Services ID 라이브러리를 설치합니다.

```console
dotnet add package Azure.Communication.Identity --version 1.0.0
```

OpenTelemetry 라이브러리에 대한 Azure Monitor 내보내기도 설치해야 합니다.

```console
dotnet add package Azure.Monitor.OpenTelemetry.Exporter
```

### <a name="set-up-the-app-framework"></a>앱 프레임워크 설정

프로젝트 디렉터리에서 다음을 수행합니다.

1. 텍스트 편집기에서 **Program.cs** 파일 열기
2. `Azure.Communication.Identity` 네임스페이스를 포함하는 `using` 지시문 추가
3. `Azure.Monitor.OpenTelemetry.Exporter` 네임스페이스를 포함하는 `using` 지시문 추가
4. `OpenTelemetry.Trace` 네임스페이스를 포함하는 `using` 지시문 추가
5. 비동기 코드를 지원하도록 `Main` 메서드 선언 업데이트

시작하려면 다음 코드를 사용합니다.

```csharp
using System;
using Azure.Communication;
using Azure.Communication.Identity;
using Azure.Monitor.OpenTelemetry.Exporter;
using OpenTelemetry.Trace;

namespace TelemetryAppInsightsQuickstart
{
    class Program
    {
        static async Task Main(string[] args)
        {
            Console.WriteLine("Azure Communication Services - Export Telemetry to Application Insights");

            // Quickstart code goes here
        }
    }
}
```
## <a name="setting-up-the-telemetry-tracer-with-communication-identity-sdk-calls"></a>통신 ID SDK 호출을 사용하여 원격 분석 추적 프로그램 설정

연결 문자열로 `CommunicationIdentityClient`를 초기화합니다. [리소스의 연결 문자열을 관리](../create-communication-resource.md#store-your-connection-string)하는 방법을 알아봅니다.

클라이언트를 만든 후에는 모든 활동을 추적하는 `Activity Source`를 정의해야 합니다. 그런 다음, `Activity Source`를 사용하여 `Activity` SDK 호출을 추적하는 데 사용되는 `CreateUserAsync`를 시작할 수 있습니다. `SetTag` 메서드를 사용하여 각 `Activity` 내에서 추적할 사용자 지정 속성을 정의할 수도 있습니다.

`GetTokenAsync` 함수에 대해 비슷한 추적 패턴이 수행됩니다.

`TracedSample`이라는 새 함수를 만들고 다음 코드를 추가합니다.

```csharp
public static async Task TracedSample()
{
    // This code demonstrates how to fetch your connection string
    // from an environment variable.
    string connectionString = Environment.GetEnvironmentVariable("COMMUNICATION_SERVICES_CONNECTION_STRING");
    var client = new CommunicationIdentityClient(connectionString);

    using var source = new ActivitySource("Quickstart.IdentityTelemetry");
    CommunicationUserIdentifier identity = null;

    using (var activity = source.StartActivity("Create User Activity"))
    {
        var identityResponse = await client.CreateUserAsync();

        identity = identityResponse.Value;
        Console.WriteLine($"\nCreated an identity with ID: {identity.Id}");
        activity?.SetTag("Identity id", identity.Id);
    }

    using (var activity = source.StartActivity("Get Token Activity"))
    {
        var tokenResponse = await client.GetTokenAsync(identity, scopes: new[] { CommunicationTokenScope.Chat });

        activity?.SetTag("Token value", tokenResponse.Value.Token);
        activity?.SetTag("Expires on", tokenResponse.Value.ExpiresOn);

        Console.WriteLine($"\nIssued an access token with 'chat' scope that expires at {expiresOn}:");
    }
}
```

## <a name="funneling-telemetry-data-to-application-insights"></a>Application Insights로 원격 분석 데이터 이동

SDK 호출이 활동으로 래핑된 후에는 OpenTelemetry 추적 내보내기를 추가하고 데이터를 Application Insights 리소스로 이동할 수 있습니다.

Application Insights에 표시되는 일부 리소스 특성을 사용하여 사전을 정의하는 옵션이 있습니다.
그런 다음, `AddSource`를 호출하고 `TracedSample`에 정의된 것과 동일한 작업 원본 이름을 사용합니다.
또한 Application Insights 리소스에서 연결 문자열을 가져와 `AddAzureMonitorTraceExporter()`에 전달해야 합니다. 그러면 원격 분석 데이터가 Application Insights 리소스로 이동됩니다.

마지막으로 SDK 호출이 수행된 위치에서 `TracedSample()` 함수를 호출하고 기다립니다.

`Main` 메서드에 다음 코드를 추가합니다.

```csharp
var resourceAttributes = new Dictionary<string, object> { { "service.name", "<service-name>" }, { "service.instance.id", "<service-instance-id>" } };
var resourceBuilder = ResourceBuilder.CreateDefault().AddAttributes(resourceAttributes);

using var tracerProvider = Sdk.CreateTracerProviderBuilder()
    .SetResourceBuilder(resourceBuilder)
    .AddSource("Quickstart.IdentityTelemetry")
    .AddAzureMonitorTraceExporter(o =>
    {
        o.ConnectionString = Environment.GetEnvironmentVariable("APPLICATION_INSIGHTS_CONNECTION_STRING");
    })
    .Build();

await TracedSample();
```

## <a name="run-the-code"></a>코드 실행

`dotnet run` 명령을 사용하여 애플리케이션 디렉터리에서 애플리케이션을 실행합니다.

```console
dotnet run
```
