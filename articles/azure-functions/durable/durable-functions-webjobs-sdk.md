---
title: Durable Functions를 WebJobs로 실행하는 방법 - Azure
description: WebJobs SDK를 사용하여 WebJobs에서 실행하도록 지속성 함수를 코딩하고 구성하는 방법을 알아봅니다.
ms.topic: conceptual
ms.date: 05/12/2021
ms.author: azfuncdf
ms.openlocfilehash: 1bfc285d1afba0c83d07dcd7da3851231553e2f1
ms.sourcegitcommit: 58e5d3f4a6cb44607e946f6b931345b6fe237e0e
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/25/2021
ms.locfileid: "110375727"
---
# <a name="how-to-run-durable-functions-as-webjobs"></a>Durable Functions를 WebJobs로 실행하는 방법

기본적으로 Durable Functions는 Azure Functions 런타임을 사용하여 오케스트레이션을 호스트합니다. 그러나 이벤트를 수신하는 코드를 보다 세밀하게 제어해야 하는 경우도 있습니다. 이 문서에서는 WebJobs SDK를 사용하여 오케스트레이션을 구현하는 방법을 보여 줍니다. Functions와 WebJobs 간 비교에 대한 자세한 내용은 [Functions 및 WebJobs 비교](../functions-compare-logic-apps-ms-flow-webjobs.md#compare-functions-and-webjobs)를 참조하세요.

[Azure Functions](../functions-overview.md) 및 [지속성 함수](durable-functions-overview.md) 확장은 [WebJobs SDK](../../app-service/webjobs-sdk-how-to.md)에 기본 제공됩니다. WebJobs SDK의 작업 호스트는 Azure Functions에서 런타임입니다. Azure Functions에서 지원하지 않는 방법으로 동작을 제어하는 경우 사용자가 직접 WebJobs SDK를 사용하여 Durable Functions를 개발하고 실행할 수 있습니다.

WebJobs SDK 버전 3.x에서 호스트는 `IHost`의 구현이고, 버전 2.x에서는 `JobHost` 개체를 사용합니다.

체이닝 Durable Functions 샘플은 WebJobs SDK 2.x 버전에서 사용할 수 있습니다. [Durable Functions 리포지토리](https://github.com/azure/azure-functions-durable-extension/)를 다운로드하거나 복제하고 *v1* 분기를 확인한 후 *samples\\webjobssdk\\chaining* 폴더로 이동합니다.

## <a name="prerequisites"></a>필수 구성 요소

이 문서에서는 사용자가 WebJobs SDK의 기본 사항, Azure Functions에 대한 C# 클래스 라이브러리 개발 및 지속성 함수에 대해 익숙한 것으로 가정합니다. 이러한 항목에 대한 소개가 필요한 경우 다음 리소스를 참조하세요.

* [WebJobs SDK 시작](../../app-service/webjobs-sdk-get-started.md)
* [Visual Studio를 사용하여 첫 번째 함수 만들기](../functions-create-your-first-function-visual-studio.md)
* [지속성 함수](durable-functions-sequence.md)

이 문서의 단계를 완료하려면 다음을 수행합니다.

* **Azure 개발** 워크로드를 사용하여 [Visual Studio 2019](/visualstudio/install/)를 설치합니다.

  Visual Studio는 있지만 해당 워크로드가 없는 경우 **도구** > **도구 및 기능 가져오기** 를 선택하여 워크로드를 추가합니다.

  (대신 [Visual Studio Code](https://code.visualstudio.com/)를 사용할 수 있지만 지침 중 일부는 Visual Studio에 국한됩니다.)

* [Azure Storage 에뮬레이터](../../storage/common/storage-use-emulator.md)를 설치하고 실행합니다. 다른 방법은 실제 Azure Storage 연결 문자열을 사용하여 *App.config* 파일을 업데이트하는 것입니다.

## <a name="webjobs-sdk-versions"></a>WebJobs SDK 버전

이 문서에서는 WebJobs SDK 2.x 프로젝트를 개발하는 방법에 대해 설명합니다(Azure Functions 버전 1.x와 동일). 버전 3.x에 대한 내용은 이 문서의 뒷부분에 나오는 [WebJobs SDK 3.x](#webjobs-sdk-3x)를 참조하세요.

## <a name="create-a-console-app"></a>콘솔 앱 만들기

Durable Functions를 WebJobs로 실행하려면 먼저 콘솔 앱을 만들어야 합니다. WebJobs SDK 프로젝트는 설치된 적절한 NuGet 패키지를 사용하는 콘솔 앱 프로젝트입니다.

Visual Studio **새 프로젝트** 대화 상자에서 **Windows 클래식 데스크톱** > **콘솔 앱(.NET Framework)** 을 선택합니다. 프로젝트 파일에서 `TargetFrameworkVersion`은 `v4.6.1`이어야 합니다.

또한 Visual Studio에는 **클라우드** > **Azure WebJob(.NET Framework)** 을 선택하여 사용할 수 있는 WebJob 프로젝트 템플릿이 있어야 합니다. 이 템플릿은 일부 필요하지 않은 많은 패키지를 설치합니다.

## <a name="install-nuget-packages"></a>NuGet 패키지 설치

WebJobs SDK에 대한 NuGet 패키지, 코어 바인딩, 로깅 프레임워크 및 지속성 작업 확장이 필요합니다. 다음은 이 문서가 작성된 날짜를 기준으로 안정적인 최신 버전 번호가 포함된 해당 패키지에 대한 **패키지 관리자 콘솔** 명령입니다.

```powershell
Install-Package Microsoft.Azure.WebJobs.Extensions -version 2.2.0
Install-Package Microsoft.Extensions.Logging -version 2.0.1
Install-Package Microsoft.Azure.WebJobs.Extensions.DurableTask -version 1.8.7
```

또한 로깅 공급자가 필요합니다. 다음 명령은 Azure Application Insights 공급자 및 `ConfigurationManager`를 설치합니다. `ConfigurationManager`를 사용하면 앱 설정에서 Application Insights 계측 키를 가져올 수 있습니다.

```powershell
Install-Package Microsoft.Azure.WebJobs.Logging.ApplicationInsights -version 2.2.0
Install-Package System.Configuration.ConfigurationManager -version 4.4.1
```

다음 명령은 콘솔 공급자를 설치합니다.

```powershell
Install-Package Microsoft.Extensions.Logging.Console -version 2.0.1
```

## <a name="jobhost-code"></a>JobHost 코드

콘솔 앱을 만들고 필요한 NuGet 패키지를 설치했으므로 지속적 함수를 사용할 준비가 되었습니다. JobHost 코드를 사용하여 이 작업을 수행합니다.

지속성 함수 확장을 사용하려면 `Main` 메서드의 `JobHostConfiguration` 개체에서 `UseDurableTask`를 호출합니다.

```cs
var config = new JobHostConfiguration();
config.UseDurableTask(new DurableTaskExtension
{
    HubName = "MyTaskHub",
};
```

`DurableTaskExtension` 개체에서 설정할 수 있는 속성의 목록은 [host.json](../functions-host-json.md#durabletask)을 참조하세요.

또한 `Main` 메서드는 로깅 공급자를 설정하기에 적합합니다. 다음 예제에서는 콘솔 및 Application Insights 공급자를 구성합니다.

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

WebJobs 컨텍스트의 Durable Functions는 Azure Functions 컨텍스트의 Durable Functions와는 약간 다릅니다. 코드를 작성할 때 이러한 차이를 파악하는 것이 중요합니다.

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

WebJobs SDK 프로젝트에서 HTTP 요청을 전송하는 대신 오케스트레이션 클라이언트 개체에서 메서드를 호출할 수 있습니다. 다음 메서드는 HTTP 관리 API로 수행할 수 있는 세 가지 작업에 해당합니다.

* `GetStatusAsync`
* `RaiseEventAsync`
* `TerminateAsync`

샘플 프로젝트의 오케스트레이션 클라이언트 함수는 오케스트레이터 함수를 시작한 다음, 2초마다 `GetStatusAsync`를 호출하는 루프로 이동합니다.

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

Durable Functions를 WebJob으로 실행되도록 설정했으며 이제 이러한 방식이 Durable Functions를 독립 실행형 Azure Functions로 실행하는 것과 어떻게 다른지 이해할 수 있습니다. 이제 샘플에서 이 기능을 살펴보는 것이 유용할 수 있습니다.

이 섹션에서는 [샘플 프로젝트](https://github.com/Azure/azure-functions-durable-extension/tree/v1/samples/webjobssdk/chaining)를 실행하는 방법에 대한 개요를 제공합니다. WebJobs SDK 프로젝트를 로컬로 실행하여 Azure WebJob에 배포하는 방법을 설명하는 자세한 지침은 [WebJobs SDK 시작](../../app-service/webjobs-sdk-get-started.md#deploy-as-a-webjob)을 참조하세요.

### <a name="run-locally"></a>로컬 실행

1. 스토리지 에뮬레이터가 실행되고 있는지 확인합니다([필수 구성 요소](#prerequisites) 참조).

1. 프로젝트를 로컬로 실행하는 경우 Application Insights에서 로그를 확인하려면 다음을 수행합니다.

    a. Application Insights 리소스를 만들고 해당 리소스에 대한 **일반** 앱 유형을 사용합니다.

    b. *App.config* 파일에 계측 키를 저장합니다.

1. 프로젝트를 실행합니다.

### <a name="run-in-azure"></a>Azure에서 실행

1. 웹앱 및 스토리지 계정을 만듭니다.

1. 웹앱에서 `AzureWebJobsStorage`라는 앱 설정에 스토리지 연결 문자열을 저장합니다.

1. Application Insights 리소스를 만들고 해당 리소스에 대한 **일반** 앱 유형을 사용합니다.

1. `APPINSIGHTS_INSTRUMENTATIONKEY`라는 앱 설정에 계측 키를 저장합니다.

1. WebJob으로 배포합니다.

## <a name="webjobs-sdk-3x"></a>WebJobs SDK 3.x

이 문서에서는 WebJobs SDK 2.x 프로젝트의 개발 방법을 설명합니다. [WebJobs SDK 3.x](../../app-service/webjobs-sdk-get-started.md) 프로젝트를 개발하는 경우 이 섹션에서 차이점을 이해할 수 있습니다.

도입된 주요 변경 내용은 .NET Framework 대신 .NET Core를 사용하는 것입니다. WebJobs SDK 3.x 프로젝트를 만들려면 지침은 동일하지만, 다음과 같은 제외 사항이 있습니다.

1. .NET Core 콘솔 앱을 만듭니다. Visual Studio **새 프로젝트** 대화 상자에서 **.NET Core** > **콘솔 앱(.NET Core)** 을 선택합니다. 프로젝트 파일은 해당 `TargetFramework`가 `netcoreapp2.x`이 되도록 지정합니다.

1. 다음 패키지 중 릴리스 버전 WebJobs SDK 3.x를 선택합니다.

    * `Microsoft.Azure.WebJobs.Extensions`
    * `Microsoft.Azure.WebJobs.Extensions.Storage`
    * `Microsoft.Azure.WebJobs.Logging.ApplicationInsights`

1. .NET Core 구성 프레임워크를 사용하여 *appsettings.json* 파일에서 스토리지 연결 문자열 및 Application Insights 계측 키를 설정합니다. 예를 들면 다음과 같습니다.

    ```json
        {
            "AzureWebJobsStorage": "<replace with storage connection string>",
            "APPINSIGHTS_INSTRUMENTATIONKEY": "<replace with Application Insights instrumentation key>"
        }
    ```

1. 이렇게 하려면 `Main` 메서드 코드를 변경합니다. 예를 들면 다음과 같습니다.

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
