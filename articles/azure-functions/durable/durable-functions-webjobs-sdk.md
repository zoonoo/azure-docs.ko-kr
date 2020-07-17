---
title: WebJobs로 Durable Functions를 실행 하는 방법-Azure
description: WebJobs SDK를 사용하여 WebJobs에서 실행하도록 지속성 함수를 코딩하고 구성하는 방법을 알아봅니다.
ms.topic: conceptual
ms.date: 04/25/2018
ms.author: azfuncdf
ms.openlocfilehash: ad498bdbc4e6dc9745c6ef45b3dc601ad36c0a62
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/02/2020
ms.locfileid: "82733416"
---
# <a name="how-to-run-durable-functions-as-webjobs"></a>WebJobs로 Durable Functions를 실행 하는 방법

기본적으로 Durable Functions는 Azure Functions 런타임을 사용 하 여 오케스트레이션을 호스팅합니다. 그러나 이벤트를 수신 하는 코드를 보다 세밀 하 게 제어 해야 하는 경우도 있습니다. 이 문서에서는 WebJobs SDK를 사용 하 여 오케스트레이션을 구현 하는 방법을 보여 줍니다. 함수와 WebJobs의 비교에 대 한 자세한 내용은 [Compare 함수 및 WebJobs](../functions-compare-logic-apps-ms-flow-webjobs.md#compare-functions-and-webjobs)를 참조 하세요.

[Azure Functions](../functions-overview.md) 및 [지속성 함수](durable-functions-overview.md) 확장은 [WebJobs SDK](../../app-service/webjobs-sdk-how-to.md)에 기본 제공됩니다. WebJobs SDK의 작업 호스트는 Azure Functions의 런타임입니다. Azure Functions에서 불가능 한 방식으로 동작을 제어 해야 하는 경우 WebJobs SDK를 직접 사용 하 여 Durable Functions을 개발 하 고 실행할 수 있습니다.

WebJobs SDK의 버전 2.x에서 호스트는의 구현 `IHost` 이며, 버전 2.x에서는 개체를 사용 `JobHost` 합니다.

연결 Durable Functions 샘플은 WebJobs SDK 2.x 버전에서 사용할 수 있습니다. [Durable Functions 리포지토리](https://github.com/azure/azure-functions-durable-extension/)를 다운로드 하거나 복제 하 고, *v1* 분기를 체크 아웃 하 고 *samples \\ \\ webjobss00stststststststststststststststststst*

## <a name="prerequisites"></a>필수 구성 요소

이 문서에서는 사용자가 WebJobs SDK의 기본 사항, Azure Functions에 대한 C# 클래스 라이브러리 개발 및 지속성 함수에 대해 익숙한 것으로 가정합니다. 이러한 항목에 대한 소개가 필요한 경우 다음 리소스를 참조하세요.

* [WebJobs SDK 시작](../../app-service/webjobs-sdk-get-started.md)
* [Visual Studio를 사용하여 첫 번째 함수 만들기](../functions-create-your-first-function-visual-studio.md)
* [지속성 함수](durable-functions-sequence.md)

이 문서의 단계를 완료하려면 다음을 수행합니다.

* **Azure 개발** 워크로드를 사용하여 [Visual Studio 2019](https://docs.microsoft.com/visualstudio/install/)를 설치합니다.

  Visual Studio가 이미 있지만 해당 워크 로드가 없는 경우 **도구**  >  **도구 및 기능 가져오기**를 선택 하 여 작업을 추가 합니다.

  (대신 [Visual Studio Code](https://code.visualstudio.com/)를 사용할 수 있지만 지침 중 일부는 Visual Studio에 국한됩니다.)

* [Azure Storage 에뮬레이터](../../storage/common/storage-use-emulator.md) 버전 5.2 이상을 설치 및 실행합니다. 다른 방법은 Azure Storage 연결 문자열을 사용하여 *App.config* 파일을 업데이트하는 것입니다.

## <a name="webjobs-sdk-versions"></a>WebJobs SDK 버전

이 문서에서는 WebJobs SDK 2.x 프로젝트를 개발하는 방법에 대해 설명합니다(Azure Functions 버전 1.x와 동일). 버전 3.x에 대한 내용은 이 문서의 뒷부분에 나오는 [WebJobs SDK 3.x](#webjobs-sdk-3x)를 참조하세요.

## <a name="create-a-console-app"></a>콘솔 앱 만들기

WebJobs로 Durable Functions를 실행 하려면 먼저 콘솔 앱을 만들어야 합니다. WebJobs SDK 프로젝트는 설치된 적절한 NuGet 패키지를 사용하는 콘솔 앱 프로젝트입니다.

Visual Studio **새 프로젝트** 대화 상자에서 **Windows 클래식 데스크톱**  >  **콘솔 앱 (.NET Framework)** 을 선택 합니다. 프로젝트 파일에서 `TargetFrameworkVersion`은 `v4.6.1`이어야 합니다.

또한 Visual Studio에는 **클라우드**  >  **Azure webjob (.NET Framework)** 을 선택 하 여 사용할 수 있는 WebJob 프로젝트 템플릿이 있습니다. 이 템플릿은 일부 필요하지 않은 많은 패키지를 설치합니다.

## <a name="install-nuget-packages"></a>NuGet 패키지 설치

WebJobs SDK에 대한 NuGet 패키지, 코어 바인딩, 로깅 프레임워크 및 지속성 작업 확장이 필요합니다. 다음은 해당 패키지에 대 한 **패키지 관리자 콘솔** 명령입니다 .이 문서는이 문서가 작성 된 날짜의 최신 안정적인 버전 번호를 포함 합니다.

```powershell
Install-Package Microsoft.Azure.WebJobs.Extensions -version 2.2.0
Install-Package Microsoft.Extensions.Logging -version 2.0.1
Install-Package Microsoft.Azure.WebJobs.Extensions.DurableTask -version 1.8.3
```

또한 로깅 공급자가 필요합니다. 다음 명령은 Azure 애플리케이션 Insights 공급자 및를 설치 합니다 `ConfigurationManager` . `ConfigurationManager`를 사용하면 앱 설정에서 Application Insights 계측 키를 가져올 수 있습니다.

```powershell
Install-Package Microsoft.Azure.WebJobs.Logging.ApplicationInsights -version 2.2.0
Install-Package System.Configuration.ConfigurationManager -version 4.4.1
```

다음 명령은 콘솔 공급자를 설치합니다.

```powershell
Install-Package Microsoft.Extensions.Logging.Console -version 2.0.1
```

## <a name="jobhost-code"></a>JobHost 코드

콘솔 앱을 만들고 필요한 NuGet 패키지를 설치 했으므로 Durable Functions 사용할 준비가 되었습니다. JobHost 코드를 사용 하 여이 작업을 수행 합니다.

지속성 함수 확장을 사용하려면 `Main` 메서드의 `JobHostConfiguration` 개체에서 `UseDurableTask`를 호출합니다.

```cs
var config = new JobHostConfiguration();
config.UseDurableTask(new DurableTaskExtension
{
    HubName = "MyTaskHub",
};
```

`DurableTaskExtension` 개체에서 설정할 수 있는 속성의 목록은 [host.json](../functions-host-json.md#durabletask)을 참조하세요.

또한 `Main` 메서드는 로깅 공급자를 설정하기에 적합합니다. 다음 예에서는 콘솔과 Application Insights 공급자를 구성 합니다.

```cs
static void Main(string[] args)
{
    using (var loggerFactory = new LoggerFactory())
    {
        var config = new JobHostConfiguration();

        config.DashboardConnectionString = "";

        var instrumentationKey =
            ConfigurationManager.AppSettings["APPINSIGHTS_INSTRUMENTATIONKEY"];

        config.LoggerFactory = loggerFactory
            .AddApplicationInsights(instrumentationKey, null)
            .AddConsole();

        config.UseTimers();
        config.UseDurableTask(new DurableTaskExtension
        {
            HubName = "MyTaskHub",
        });
        var host = new JobHost(config);
        host.RunAndBlock();
    }
}
```

## <a name="functions"></a>Functions

WebJobs의 컨텍스트에서 Durable Functions는 Azure Functions 컨텍스트에서 Durable Functions는 약간 다릅니다. 코드를 작성할 때의 차이점을 파악 하는 것이 중요 합니다.

WebJobs SDK는 다음과 같은 Azure Functions 기능을 지원하지 않습니다.

* [FunctionName 특성](#functionname-attribute)
* [HTTP 트리거](#http-trigger)
* [지속성 함수 HTTP 관리 API](#http-management-api)

### <a name="functionname-attribute"></a>FunctionName 특성

WebJobs SDK 프로젝트에서 함수의 메서드 이름은 함수 이름입니다. `FunctionName` 특성은 Azure Functions에서만 사용됩니다.

### <a name="http-trigger"></a>HTTP 트리거

WebJobs SDK에는 HTTP 트리거가 없습니다. 샘플 프로젝트의 오케스트레이션 클라이언트는 타이머 트리거를 사용합니다.

```cs
public static async Task CronJob(
    [TimerTrigger("0 */2 * * * *")] TimerInfo timer,
    [OrchestrationClient] DurableOrchestrationClient client,
    ILogger logger)
{
  ...
}
```

### <a name="http-management-api"></a>HTTP 관리 API

HTTP 트리거가 없으므로 WebJobs SDK에는 [HTTP 관리 API](durable-functions-http-api.md)가 없습니다.

WebJobs SDK 프로젝트에서 HTTP 요청을 전송 하는 대신 orchestration 클라이언트 개체에서 메서드를 호출할 수 있습니다. 다음 메서드는 HTTP 관리 API로 수행할 수 있는 세 가지 작업에 해당합니다.

* `GetStatusAsync`
* `RaiseEventAsync`
* `TerminateAsync`

샘플 프로젝트의 오케스트레이션 클라이언트 함수는 오 케 스트레이 터 함수를 시작한 후 2 초 마다를 호출 하는 루프로 이동 합니다 `GetStatusAsync` .

```cs
string instanceId = await client.StartNewAsync(nameof(HelloSequence), input: null);
logger.LogInformation($"Started new instance with ID = {instanceId}.");

DurableOrchestrationStatus status;
while (true)
{
    status = await client.GetStatusAsync(instanceId);
    logger.LogInformation($"Status: {status.RuntimeStatus}, Last update: {status.LastUpdatedTime}.");

    if (status.RuntimeStatus == OrchestrationRuntimeStatus.Completed ||
        status.RuntimeStatus == OrchestrationRuntimeStatus.Failed ||
        status.RuntimeStatus == OrchestrationRuntimeStatus.Terminated)
    {
        break;
    }

    await Task.Delay(TimeSpan.FromSeconds(2));
}
```

## <a name="run-the-sample"></a>샘플 실행

WebJob으로 실행 되도록 설정 Durable Functions 했으며, 이제는이를 독립 실행형 Azure Functions으로 Durable Functions 실행 하는 것과 어떻게 다른 지 이해 하 고 있습니다. 이 시점에서 샘플에서 작업을 보는 것이 도움이 될 수 있습니다.

이 섹션에서는 [샘플 프로젝트](https://github.com/Azure/azure-functions-durable-extension/tree/v1/samples/webjobssdk/chaining)를 실행하는 방법에 대한 개요를 제공합니다. WebJobs SDK 프로젝트를 로컬로 실행하여 Azure WebJob에 배포하는 방법을 설명하는 자세한 지침은 [WebJobs SDK 시작](../../app-service/webjobs-sdk-get-started.md#deploy-as-a-webjob)을 참조하세요.

### <a name="run-locally"></a>로컬 실행

1. 스토리지 에뮬레이터가 실행되고 있는지 확인합니다([필수 구성 요소](#prerequisites) 참조).

1. 프로젝트를 로컬로 실행할 때 Application Insights의 로그를 보려면 다음을 수행 합니다.

    a. Application Insights 리소스를 만들고 해당 리소스에 대 한 **일반** 앱 유형을 사용 합니다.

    b. *App.config* 파일에 계측 키를 저장합니다.

1. 프로젝트를 실행합니다.

### <a name="run-in-azure"></a>Azure에서 실행

1. 웹앱 및 스토리지 계정을 만듭니다.

1. 웹 앱에서 라는 앱 설정에 저장소 연결 문자열을 저장 `AzureWebJobsStorage` 합니다.

1. Application Insights 리소스를 만들고 해당 리소스에 대 한 **일반** 앱 유형을 사용 합니다.

1. 이라는 앱 설정에 계측 키를 저장 `APPINSIGHTS_INSTRUMENTATIONKEY` 합니다.

1. WebJob으로 배포합니다.

## <a name="webjobs-sdk-3x"></a>WebJobs SDK 3.x

이 문서에서는 WebJobs SDK 2.x 프로젝트를 개발 하는 방법을 설명 합니다. [WEBJOBS SDK](../../app-service/webjobs-sdk-get-started.md) 3.x 프로젝트를 개발 하는 경우이 섹션에서 차이점을 이해 하는 데 도움이 됩니다.

도입 된 주요 변경 내용은 .NET Framework 대신 .NET Core를 사용 하는 것입니다. WebJobs SDK 3.x 프로젝트를 만들기 위해 지침은 동일 하지만 다음과 같은 예외가 있습니다.

1. .NET Core 콘솔 앱을 만듭니다. Visual Studio **새 프로젝트** 대화 상자에서 **.net core**  >  **콘솔 앱 (.net core)** 을 선택 합니다. 프로젝트 파일은 해당 `TargetFramework`가 `netcoreapp2.x`이 되도록 지정합니다.

1. 다음 패키지의 릴리스 버전 WebJobs SDK 3.x를 선택 합니다.

    * `Microsoft.Azure.WebJobs.Extensions`
    * `Microsoft.Azure.WebJobs.Extensions.Storage`
    * `Microsoft.Azure.WebJobs.Logging.ApplicationInsights`

1. .NET Core 구성 프레임 워크를 사용 하 여 파일 *의appsettings.js* 에서 저장소 연결 문자열과 Application Insights 계측 키를 설정 합니다. 예를 들면 다음과 같습니다.

    ```json
        {
            "AzureWebJobsStorage": "<replace with storage connection string>",
            "APPINSIGHTS_INSTRUMENTATIONKEY": "<replace with Application Insights instrumentation key>"
        }
    ```

1. `Main`이 작업을 수행 하도록 메서드 코드를 변경 합니다. 예를 들면 다음과 같습니다.

   ```cs
   static void Main(string[] args)
   {
        var hostBuilder = new HostBuilder()
            .ConfigureWebJobs(config =>
            {
                config.AddAzureStorageCoreServices();
                config.AddAzureStorage();
                config.AddTimers();
                config.AddDurableTask(options =>
                {
                    options.HubName = "MyTaskHub";
                    options.AzureStorageConnectionStringName = "AzureWebJobsStorage";
                });
            })
            .ConfigureLogging((context, logging) =>
            {
                logging.AddConsole();
                logging.AddApplicationInsights(config =>
                {
                    config.InstrumentationKey = context.Configuration["APPINSIGHTS_INSTRUMENTATIONKEY"];
                });
            })
            .UseConsoleLifetime();

        var host = hostBuilder.Build();

        using (host)
        {
            host.Run();
        }
   }
   ```

## <a name="next-steps"></a>다음 단계

WebJobs SDK에 대한 자세한 내용은 [WebJobs SDK를 사용하는 방법](../../app-service/webjobs-sdk-how-to.md)을 참조하세요.
