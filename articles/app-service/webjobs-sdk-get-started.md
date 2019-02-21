---
title: WebJobs SDK 시작 - Azure
description: 이벤트 구동 백그라운드 처리를 위한 WebJobs SDK에 대해 간략히 소개합니다. Azure 서비스 및 타사 서비스에서 데이터에 액세스하는 방법을 알아봅니다.
services: app-service\web, storage
documentationcenter: .net
author: ggailey777
manager: cfowler
editor: ''
ms.service: app-service-web
ms.workload: web
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: article
ms.date: 01/15/2019
ms.author: glenga
ms.openlocfilehash: b39c8c19b494422d34bae7145d69e8e95cb16846
ms.sourcegitcommit: 79038221c1d2172c0677e25a1e479e04f470c567
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/19/2019
ms.locfileid: "56416823"
---
# <a name="get-started-with-the-azure-webjobs-sdk-for-event-driven-background-processing"></a>이벤트 중심 백그라운드 처리를 위한 Azure WebJobs SDK 시작

이 문서에서는 Azure WebJobs SDK 프로젝트를 만들고, 로컬로 실행하고, Azure App Service에 배포하는 방법을 보여 줍니다.

[Visual Studio 2017](https://www.visualstudio.com/vs/)에 대한 지침이지만, [Visual Studio Code](https://code.visualstudio.com/)와 같은 다른 도구를 사용하여 동일한 작업을 수행할 수 있습니다.

## <a name="what-is-the-azure-webjobs-sdk"></a>Azure WebJobs SDK 정의

Azure WebJobs SDK는 Azure 서비스의 데이터에 액세스하는 백그라운드 처리 코드를 작성하는 작업을 간소화하는 프레임워크입니다. SDK에는 함수를 트리거해야 하는 이벤트(예: 큐에 추가된 새 메시지)를 지정하기 위한 선언적 구문이 있습니다. 함수가 트리거되면 비슷한 선언적 구문에서 데이터 읽기 및 쓰기를 제어합니다. 이 트리거 및 바인딩 시스템은 Azure 및 타사 서비스 액세스와 관련된 대부분의 하위 수준 코딩 작업을 처리합니다.

### <a name="functions-triggers-and-bindings"></a>함수, 트리거 및 바인딩

WebJobs SDK 프로젝트는 하나 이상의 *함수*를 정의합니다. 함수는 해당 메서드 시그니처에 트리거 특성이 있는 메서드입니다. 트리거는 함수를 호출하기 위한 조건을 지정하고, 바인딩은 읽고 쓸 내용을 지정합니다. 예를 들어 다음 함수의 트리거 특성은 런타임에 큐 메시지가 `items` 큐에 나타날 때마다 함수를 호출하도록 지시합니다. `Blob` 특성은 런타임에 큐 메시지를 사용하여 *workitems* 컨테이너의 Blob을 읽도록 지시합니다. `queueTrigger` 매개 변수에 제공되는 &mdash; 큐 메시지의 내용은 &mdash; Blob의 이름입니다.

```cs
public static void Run(
    [QueueTrigger("items")] string myQueueItem,
    [Blob("workitems/{queueTrigger}", FileAccess.Read)] Stream myBlob,
    TraceWriter log)
{
    log.Info($"BlobInput processed blob\n Name:{myQueueItem} \n Size: {myBlob.Length} bytes");
}
```

### <a name="versions-2x-and-3x"></a>2.x 및 3.x 버전

이 지침에서는 WebJobs SDK 버전 3.x 프로젝트를 만드는 방법을 설명합니다. 3.x 버전에 도입된 주요 변경 내용은 .NET Framework 대신 .NET Core를 사용하는 것입니다. 이 문서에서는 v2.x와 v3.x 간의 차이점을 설명합니다.

### <a name="azure-functions"></a>Azure 기능

[Azure Functions](../azure-functions/functions-overview.md)는 함수에 대한 서버리스 옵션을 제공합니다. Functions는 WebJobs SDK를 기반으로 하며, WebJobs SDK를 직접 사용할 필요가 없는 경우에 선택할 수 있는 옵션입니다. Azure Functions 2.x는 WebJobs SDK 3.x를 사용합니다. 자세한 내용은 [Azure Functions 및 WebJobs SDK 비교](../azure-functions/functions-compare-logic-apps-ms-flow-webjobs.md#compare-functions-and-webjobs)를 참조하세요.

## <a name="prerequisites"></a>필수 조건

이 문서에서는 [Azure 계정](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio)이 있고, [Azure App Service에서 앱을 사용한 경험](overview.md)이 있다고 가정합니다. 이 문서의 단계를 완료하려면 다음을 수행합니다.

* **Azure 개발** 워크로드를 사용하여 [Visual Studio 2017을 설치](/visualstudio/install/)합니다. Visual Studio는 있지만 해당 워크로드가 없는 경우 **도구 > 도구 및 기능 가져오기**를 선택하여 워크로드를 추가합니다.
* [App Service 앱을 만듭니다](app-service-web-get-started-dotnet-framework.md). WebJob을 배포할 수 있는 앱이 이미 있는 경우 새 앱을 만드는 대신 해당 앱을 사용할 수 있습니다.

## <a name="create-a-project"></a>프로젝트 만들기

1. Visual Studio에서 **파일 -> 새로 만들기 > 프로젝트**를 선택합니다.

2. **클래식 Windows 데스크톱 > 콘솔 앱(.NET Framework)** 을 차례로 선택합니다.

3. *WebJobsSDKSample* 프로젝트의 이름을 지정한 다음, **확인**을 선택합니다.

   ![새 프로젝트 대화 상자](./media/webjobs-sdk-get-started/new-project.png)

## <a name="webjobs-nuget-packages"></a>WebJobs NuGet 패키지

WebJobs SDK를 설치하는 NuGet 패키지는 v2.x와 v3.x 간에 약간 차이가 있습니다.

### <a name="install-sdk-version-3x-packages"></a>SDK 버전 3.x 패키지 설치

1. 다음 NuGet 패키지의 안정적인 최신 3.x 버전을 설치합니다.

    * `Microsoft.Azure.WebJobs`
    * `Microsoft.Azure.WebJobs.Extensions`

    3.0.3 버전에 대한 **패키지 관리자 콘솔** 명령은 다음과 같습니다.

    ```powershell
    Install-Package Microsoft.Azure.WebJobs -version 3.0.2
    Install-Package Microsoft.Azure.WebJobs.Extensions -version 3.0.1
    ```

### <a name="install-the-sdk-version-2x-package"></a>SDK 버전 2.x 패키지 설치

1. 안정적인 최신 2.x 버전의 NuGet 패키지인 `Microsoft.Azure.WebJobs`를 설치합니다.

    2.2.0 버전에 대한 **패키지 관리자 콘솔** 명령은 다음과 같습니다.

    ```powershell
    Install-Package Microsoft.Azure.WebJobs -version 2.2.0
    ```

## <a name="create-the-host"></a>호스트 만들기

호스트는 함수에 대한 런타임 컨테이너이며, 트리거를 수신 대기하고 함수를 호출합니다. 만드는 호스트는 SDK 버전에 따라 다릅니다.

### <a name="version-3x"></a>버전 3.x

다음은 ASP.NET Core의 일반 호스트인 [`IHost`](/dotnet/api/microsoft.extensions.hosting.ihost)를 구현하는 호스트를 만듭니다. 

1. *Program.cs*에 `using` 문을 추가합니다.

    ```cs
    using Microsoft.Extensions.Hosting;
    ```

1. `Main` 메서드를 다음 코드로 바꿉니다.

    ```cs
    static void Main(string[] args)
    {
        var builder = new HostBuilder();
        builder.ConfigureWebJobs(b =>
                {
                    b.AddAzureStorageCoreServices();
                });
        var host = builder.Build();
        using (host)
        {
            host.Run();
        }
    }
    ```

ASP.NET Core에서 호스트 구성은 [`HostBuilder`](/dotnet/api/microsoft.extensions.hosting.hostbuilder) 인스턴스의 메서드를 호출하여 설정됩니다. 자세한 내용은 [.NET 일반 호스트](/aspnet/core/fundamentals/host/generic-host)를 참조하세요. `ConfigureWebJobs` 확장 메서드는 WebJobs 호스트를 초기화합니다.

### <a name="version-2x"></a>버전 2.x

다음 코드는 **JobHost** 개체를 만듭니다.

1. *Program.cs*에 `using` 문을 추가합니다.

   ```cs
   using Microsoft.Azure.WebJobs;
   ```

1. `Main` 메서드를 다음 코드로 바꿉니다.

   ```cs
   static void Main()
   {
       var config = new JobHostConfiguration();
       var host = new JobHost(config);
       host.RunAndBlock();
   }
   ```

호스트 구성은 `JobHost`를 만드는 데 사용되는 `JobHostConfiguration` 인스턴스에서 설정됩니다.

## <a name="enable-console-logging"></a>콘솔 로깅 사용

ASP.NET Core용으로 개발된 로깅 프레임워크는 WebJobs SDK 버전 2.x 및 3.x에 모두 권장됩니다. 이 프레임워크는 저장소 미디어 및 필터링에 더 나은 성능과 더 많은 유연성을 제공합니다. 자세한 내용은 [ASP.NET Core의 로깅](/aspnet/core/fundamentals/logging)을 참조하세요.

이 섹션에서는 이 프레임워크를 사용하는 콘솔 로깅을 설정합니다.

1. 다음과 같은 안정적인 최신 버전의 NuGet 패키지를 설치합니다.

   * `Microsoft.Extensions.Logging` - 로깅 프레임워크입니다.
   * `Microsoft.Extensions.Logging.Console` - 콘솔 *공급자*입니다. 공급자는 로그를 특정 대상(이 경우 콘솔)으로 보냅니다.

   2.2.0 버전에 대한 **패키지 관리자 콘솔** 명령은 다음과 같습니다.

   ```powershell
   Install-Package Microsoft.Extensions.Logging -version 2.2.0
   ```

   ```powershell
   Install-Package Microsoft.Extensions.Logging.Console -version 2.2.0
   ```

1. *Program.cs*에 `using` 문을 추가합니다.

   ```cs
   using Microsoft.Extensions.Logging;
   ```

1. 로깅 코드 업데이트는 SDK 버전에 따라 다릅니다.

    **버전 3.x**

    [`HostBuilder`](/dotnet/api/microsoft.extensions.hosting.hostbuilder)에서 [`ConfigureLogging`](/dotnet/api/microsoft.aspnetcore.hosting.webhostbuilderextensions.configurelogging) 메서드를 호출합니다. [`AddConsole`](/dotnet/api/microsoft.extensions.logging.consoleloggerextensions.addconsole) 메서드는 구성에 콘솔 로깅을 추가합니다.

    ```cs
    builder.ConfigureLogging((context, b) =>
    {
        b.AddConsole();
    });
    ```

    `Main` 메서드는 이제 다음과 같습니다.

    ```cs
    static void Main(string[] args)
    {
        var builder = new HostBuilder();
        builder.ConfigureWebJobs(b =>
                {
                    b.AddAzureStorageCoreServices();
                });
        builder.ConfigureLogging((context, b) =>
                {
                    b.AddConsole();
                });
        var host = builder.Build();
        using (host)
        {
            host.Run();
        }
    }
    ```

    **버전 2.x**

    `Main` 메서드에서 `JobHost`를 만들기 전에 `JobHostConfiguration`을 업데이트하는 코드를 추가합니다.

    ```cs
    config.DashboardConnectionString = "";
    var loggerFactory = new LoggerFactory();
    config.LoggerFactory = loggerFactory
        .AddConsole();
    ```

    `Main` 메서드는 이제 다음과 같습니다.

    ```cs
    var config = new JobHostConfiguration();
    config.DashboardConnectionString = "";
    var loggerFactory = new LoggerFactory();
    config.LoggerFactory = loggerFactory
        .AddConsole();
    var host = new JobHost(config);
    host.RunAndBlock();
    ```

    이러한 업데이트는 다음을 수행합니다.

    * [대시보드 로깅](https://github.com/Azure/azure-webjobs-sdk/wiki/Queues#logs)을 사용하지 않도록 설정합니다. 대시보드는 레거시 모니터링 도구이며, 처리량이 많은 프로덕션 시나리오에서는 대시보드 로깅을 사용하지 않는 것이 좋습니다.
    * 콘솔 공급자에 기본 [필터링](webjobs-sdk-how-to.md#log-filtering)을 추가합니다.

이제 [Azure Storage 큐](../azure-functions/functions-bindings-storage-queue.md)에 도착하는 메시지에 의해 트리거되는 함수를 추가할 수 있습니다.

## <a name="install-binding-extensions"></a>바인딩 확장 설치

버전 3.x부터, 함수에서 사용하는 WebJobs SDK 바인딩 특성을 위해 확장을 명시적으로 설치해야 합니다. 단, 확장이 필요하지 않은 [타이머 트리거](../azure-functions/functions-bindings-timer.md) 및 [HTTP 트리거](../azure-functions/functions-bindings-http-webhook.md)는 예외입니다. WebJobs SDK 버전 2.x에서는 확장을 사용하지 않으며, 확장이 SDK에 포함되어 있습니다. 버전 2.x를 사용하는 경우 다음 섹션으로 건너뜁니다.

1. [Microsoft.Azure.WebJobs.Extensions.Storage](https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.Storage) NuGet 패키지의 안정적인 최신 버전인 버전 3.x를 설치합니다. 

    버전 3.0.2에 대한 **패키지 관리자 콘솔** 명령은 다음과 같습니다.

    ```powershell
    Install-Package Microsoft.Azure.WebJobs.Extensions.Storage -Version 3.0.2
    ```

2. `ConfigureWebJobs` 확장 메서드에서 [`HostBuilder`](/dotnet/api/microsoft.extensions.hosting.hostbuilder) 인스턴스의 `AddAzureStorage` 메서드를 호출하여 Storage 확장을 초기화합니다. 이 시점에서 `ConfigureWebJobs` 메서드는 다음 예제와 유사합니다.

    ```cs
    builder.ConfigureWebJobs(b =>
                    {
                        b.AddAzureStorageCoreServices();
                        b.AddAzureStorage();
                    });
    ```

## <a name="create-a-function"></a>함수 만들기

1. 프로젝트를 마우스 오른쪽 단추로 클릭하고 **추가** > **새 항목...** 을 선택한 다음, 새 C# 클래스 파일의 이름을 *Functions.cs*로 지정합니다.

1. Functions.cs에서 생성된 템플릿을 다음 코드로 바꿉니다.

   ```cs
   using Microsoft.Azure.WebJobs;
   using Microsoft.Extensions.Logging;

   namespace WebJobsSDKSample
   {
       public class Functions
       {
           public static void ProcessQueueMessage([QueueTrigger("queue")] string message, ILogger logger)
           {
               logger.LogInformation(message);
           }
       }
   }
   ```

   `QueueTrigger` 특성은 런타임에 새 메시지가 `queue`라는 Azure Storage 큐에 기록될 때 이 함수를 호출하도록 지시합니다. 큐 메시지의 내용은 `message` 매개 변수의 메서드 코드에 제공됩니다. 메서드 본문은 트리거 데이터를 처리하는 위치입니다. 이 예제에서 코드는 단지 메시지만 기록합니다.

   `message` 매개 변수는 문자열일 필요는 없습니다. JSON 개체, 바이트 배열 또는 [CloudQueueMessage](https://docs.microsoft.com/dotnet/api/microsoft.windowsazure.storage.queue.cloudqueuemessage) 개체에 바인딩할 수도 있습니다. [큐 트리거 사용을 참조하세요](../azure-functions/functions-bindings-storage-queue.md#trigger---usage). 각 바인딩 형식(예: 큐, Blob 또는 테이블)에는 바인딩할 수 있는 다른 매개 변수 형식 집합이 있습니다.

## <a name="create-a-storage-account"></a>저장소 계정 만들기

로컬로 실행되는 Azure Storage 에뮬레이터에는 WebJobs SDK에 필요한 모든 기능이 없습니다. 따라서 이 섹션에서는 Azure에서 Storage 계정을 만들고 이를 사용하도록 프로젝트를 구성합니다.

1. Visual Studio에서 **서버 탐색기**를 열고 Azure에 로그인합니다. **Azure** 노드를 마우스 오른쪽 단추로 클릭한 다음, **Microsoft Azure 구독에 연결**을 선택합니다.

   ![Azure에 로그인](./media/webjobs-sdk-get-started/sign-in.png)

1. **서버 탐색기**의 **Azure** 노드 아래에서 **Storage**를 마우스 오른쪽 단추로 클릭한 다음, **Storage 계정 만들기**를 선택합니다.

   ![Storage 계정 만들기 메뉴](./media/webjobs-sdk-get-started/create-storage-account-menu.png)

1. **Storage 계정 만들기** 대화 상자에서 Storage 계정에 대한 고유한 이름을 입력합니다.

1. App Service 앱을 만든 지역과 동일한**지역** 또는 가까운 지역을 선택합니다.

1. **만들기**를 선택합니다.

   ![Storage 계정 만들기](./media/webjobs-sdk-get-started/create-storage-account.png)

1. **서버 탐색기**의 **Storage** 노드 아래에서 새 Storage 계정을 선택합니다. **속성** 창에서 **연결 문자열** 값 필드의 오른쪽에 있는 줄임표(**...**)를 선택합니다.

   ![연결 문자열 줄임표](./media/webjobs-sdk-get-started/conn-string-ellipsis.png)

1. 연결 문자열을 복사하고, 이 값을 쉽게 다시 복사할 수 있는 특정 위치에 저장합니다.

   ![연결 문자열 복사](./media/webjobs-sdk-get-started/copy-key.png)

## <a name="configure-storage-to-run-locally"></a>로컬로 실행되도록 스토리지 구성

WebJobs SDK는 Azure의 애플리케이션 설정에서 스토리지 연결 문자열을 찾습니다. 로컬로 실행하는 경우 로컬 구성 파일 또는 환경 변수에서 이 값을 찾습니다.

### <a name="appsettingsjson-sdk-version-3x"></a>appsettings.json(SDK 버전 3.x)

1. 다음 예제와 같이 *appsettings.json* 파일을 만들거나 `AzureWebJobsStorage` 필드를 추가합니다.

    ```json
    {
        "AzureWebJobsStorage": "{storage connection string}"
    }
    ```

1. *{저장소 연결 문자열}* 을 앞에서 복사한 연결 문자열로 바꿉니다.

### <a name="appconfig-sdk-version-2x"></a>App.config(SDK 버전 2.x)

1. 여는 `<configuration>` 태그 바로 뒤에 있는 *App.config* 파일에 다음 XML을 추가합니다.

   ```xml
   <connectionStrings>
     <add name="AzureWebJobsStorage" connectionString="{storage connection string}" />
   </connectionStrings>
   ```

1. *{저장소 연결 문자열}* 을 앞에서 복사한 연결 문자열로 바꿉니다.

나중에 Azure App Service의 앱에 동일한 연결 문자열 앱 설정을 추가합니다.

## <a name="test-locally"></a>로컬에서 테스트

이 섹션에서는 프로젝트를 로컬로 빌드하고, 실행하고, 큐 메시지를 만들어 함수를 트리거합니다.

1. Ctrl+F5를 눌러 프로젝트를 실행합니다.

   콘솔에는 런타임에서 함수를 찾았고 이 함수를 트리거하기 위해 큐 메시지를 기다리고 있음이 표시됩니다. 다음 출력은 v3.x 호스트에 의해 생성됩니다.

   ```console
    info: Microsoft.Azure.WebJobs.Hosting.JobHostService[0]
          Starting JobHost
    info: Host.Startup[0]
          Found the following functions:
          WebJobsSDKSample.Functions.ProcessQueueMessage

    info: Host.Startup[0]
          Job host started
    Application started. Press Ctrl+C to shut down.
    Hosting environment: Development
    Content root path: C:\WebJobsSDKSample\WebJobsSDKSample\bin\Debug\netcoreapp2.1\
   ```

1. 콘솔 창을 닫습니다.

1. Visual Studio의 **서버 탐색기**에서 새 저장소 계정에 대한 노드를 펼친 다음, **큐**를 마우스 오른쪽 단추로 클릭합니다. 

1. **큐 만들기**를 선택합니다.

1. 큐에 대한 이름으로 *queue*를 입력한 다음, **확인**을 선택합니다.

   ![큐 만들기](./media/webjobs-sdk-get-started/create-queue.png)

1. 새 큐에 대한 노드를 마우스 오른쪽 단추로 클릭한 다음, **큐 보기**를 선택합니다.

1. **메시지 추가** 아이콘을 선택합니다.

   ![큐 만들기](./media/webjobs-sdk-get-started/create-queue-message.png)

1. **메시지 추가** 대화 상자에서 *Hello World!* 를 **메시지 텍스트**로 입력한 다음, **확인**을 선택합니다.

   ![큐 만들기](./media/webjobs-sdk-get-started/hello-world-text.png)

1. 프로젝트를 다시 실행합니다.

   `ProcessQueueMessage` 함수에서 `QueueTrigger` 특성을 사용했으므로 WeJobs SDK 런타임은 시작할 때 큐 메시지를 수신 대기합니다. *queue*라는 큐에서 새 큐 메시지를 찾아 함수를 호출합니다.

   [큐 폴링 지수 백오프](../azure-functions/functions-bindings-storage-queue.md#trigger---polling-algorithm)로 인해 런타임에서 메시지를 찾고 함수를 호출하는 데 2분 정도 걸릴 수 있습니다. 이 대기 시간은 [개발 모드](webjobs-sdk-how-to.md#host-development-settings)에서 실행하여 줄일 수 있습니다.

  버전 3.x의 경우 콘솔 출력은 다음과 같습니다.

   ```console
    info: Function.ProcessQueueMessage[0]
          Executing 'Functions.ProcessQueueMessage' (Reason='New queue message detected on 'queue'.', Id=2c319369-d381-43f3-aedf-ff538a4209b8)
    info: Function.ProcessQueueMessage[0]
          Trigger Details: MessageId: b00a86dc-298d-4cd2-811f-98ec39545539, DequeueCount: 1, InsertionTime: 1/18/2019 3:28:51 AM +00:00
    info: Function.ProcessQueueMessage.User[0]
          Hello World!
    info: Function.ProcessQueueMessage[0]
          Executed 'Functions.ProcessQueueMessage' (Succeeded, Id=2c319369-d381-43f3-aedf-ff538a4209b8)
   ```

1. 콘솔 창을 닫습니다.

## <a name="add-application-insights-logging"></a>Application Insights 로깅 추가

프로젝트가 Azure에서 실행되면 콘솔 출력을 확인하여 함수 실행을 모니터링할 수 없습니다. 권장되는 모니터링 솔루션은 [Application Insights](../azure-monitor/app/app-insights-overview.md)입니다. 자세한 내용은 [Azure Functions 모니터링](../azure-functions/functions-monitoring.md)을 참조하세요.

이 섹션에서는 Azure에 배포하기 전에 Application Insights 로깅을 설정하기 위해 다음 작업을 수행합니다.

* 사용할 App Service 앱과 Application Insights 인스턴스가 있는지 확인합니다.
* Application Insights 인스턴스와 이전에 만든 저장소 계정을 사용하도록 App Service 앱을 구성합니다.
* Application Insights에 로깅할 프로젝트를 설정합니다.

### <a name="create-app-service-app-and-application-insights-instance"></a>App Service 앱 및 Application Insights 인스턴스 만들기

1. 사용할 수 있는 App Service 앱이 아직 없는 경우 [하나를 만듭니다](app-service-web-get-started-dotnet-framework.md). 앱을 만들 때 연결된 Application Insights 리소스를 만들 수도 있습니다. 이 작업을 수행하면 앱에서 `APPINSIGHTS_INSTRUMENTATIONKEY`가 자동으로 설정됩니다.

1. 사용할 수 있는 Application Insights 리소스가 아직 없는 경우 [하나를 만듭니다](../azure-monitor/app/create-new-resource.md ). **애플리케이션 종류**를 **일반**으로 설정하고, **계측 키 복사**를 수행하는 섹션을 건너뜁니다.

1. 사용하려는 Application Insights 리소스가 이미 있는 경우 [계측 키를 복사합니다](../azure-monitor/app/create-new-resource.md#copy-the-instrumentation-key).

### <a name="configure-app-settings"></a>앱 설정 구성 

1. Visual Studio의 **서버 탐색기**에서 **Azure** 아래의 **App Service** 노드를 펼칩니다.

1. App Service 앱이 있는 리소스 그룹을 펼친 다음, App Service 앱을 마우스 오른쪽 단추로 클릭합니다.

1. **설정 보기**를 선택합니다.

1. **연결 문자열** 상자에서 다음 항목을 추가합니다.

   |Name  |연결 문자열  |데이터베이스 유형|
   |---------|---------|------|
   |AzureWebJobsStorage | {앞에서 복사한 Storage 연결 문자열}|사용자 지정|

1. **애플리케이션 설정** 상자에 Application Insights 계측 키가 없으면 앞에서 복사한 계측 키를 추가합니다. (계측 키는 App Service 앱을 만든 방법에 따라 이미 있을 수 있습니다.)

   |Name  |값  |
   |---------|---------|
   |APPINSIGHTS_INSTRUMENTATIONKEY | {계측 키} |

1. *{계측 키}* 를 사용 중인 Application Insights 리소스의 계측 키로 바꿉니다.

1. **저장**을 선택합니다.

1. 로컬로 실행할 수 있도록 프로젝트에 Application Insights 연결을 추가합니다.

    **버전 3.x**

    *appsettings.json* 파일에서 다음 예제와 같이 `APPINSIGHTS_INSTRUMENTATIONKEY` 필드를 추가합니다.

    ```json
    {
        "AzureWebJobsStorage": "{storage connection string}",
        "APPINSIGHTS_INSTRUMENTATIONKEY": "{instrumentation key}"
    }
    ```

    **버전 2.x**

    연결 문자열 컬렉션 바로 뒤에 있는 *App.config* 파일에 다음 XML을 추가합니다.

    ```xml
    <appSettings>
        <add key="APPINSIGHTS_INSTRUMENTATIONKEY" value="{instrumentation key}" />
    </appSettings>
    ```

    두 버전 중 하나에서 ‘{계측 키}’를 사용 중인 Application Insights 리소스의 계측 키로 바꿉니다.

1. 변경 내용을 저장합니다.

### <a name="add-application-insights-logging-provider"></a>Application Insights 로깅 공급자 추가

[Application Insights](../azure-monitor/app/app-insights-overview.md) 로깅을 활용하려면 다음을 수행하도록 로깅 코드를 업데이트합니다.

* 기본 [필터링](webjobs-sdk-how-to.md#log-filtering)을 사용하여 Application Insights 로깅 공급자를 추가합니다. 로컬로 실행 중인 경우 모든 정보 및 상위 수준의 로그가 콘솔 및 Application Insights 둘 다로 이동합니다.
* 호스트가 종료될 때 로그 출력이 플러시되도록 `LoggerFactory` 개체를 `using` 블록에 배치합니다.

#### <a name="version-3x"></a>버전 3.x

1. Application Insights 로깅 공급자에 대한 NuGet 패키지의 안정적인 최신 3.x 버전인 `Microsoft.Azure.WebJobs.Logging.ApplicationInsights`를 설치합니다.

   버전 3.0.2에 대한 **패키지 관리자 콘솔** 명령은 다음과 같습니다.

   ```powershell
   Install-Package Microsoft.Azure.WebJobs.Logging.ApplicationInsights -Version 3.0.2
   ```

1. *Program.cs*를 열고 `Main` 메서드의 코드를 다음 코드로 바꿉니다.

    ```cs
    static void Main(string[] args)
    {
        var builder = new HostBuilder();
        builder.UseEnvironment(EnvironmentName.Development);
        builder.ConfigureWebJobs(b =>
                {
                    b.AddAzureStorageCoreServices();
                    b.AddAzureStorage();
                });
        builder.ConfigureLogging((context, b) =>
                {
                    b.AddConsole();

                    // If the key exists in settings, use it to enable Application Insights.
                    string instrumentationKey = context.Configuration["APPINSIGHTS_INSTRUMENTATIONKEY"];
                    if (!string.IsNullOrEmpty(instrumentationKey))
                    {
                        b.AddApplicationInsights(o => o.InstrumentationKey = instrumentationKey);
                    }
                });
        var host = builder.Build();
        using (host)
        {
            host.Run();
        }
    }
    ```

#### <a name="version-2x"></a>버전 2.x

1. Application Insights 로깅 공급자에 대한 안정적인 최신 2.x 버전의 NuGet 패키지인 `Microsoft.Azure.WebJobs.Logging.ApplicationInsights`를 설치합니다.

   2.2.0 버전에 대한 **패키지 관리자 콘솔** 명령은 다음과 같습니다.

   ```powershell
   Install-Package Microsoft.Azure.WebJobs.Logging.ApplicationInsights -version 2.2.0
   ``` 

1. .NET 구성 관리자에 대한 안정적인 최신 4.x 버전의 NuGet 패키지인 `System.Configuration.ConfigurationManager`를 설치합니다.

   4.4.1 버전에 대한 **패키지 관리자 콘솔** 명령은 다음과 같습니다.

   ```powershell
   Install-Package System.Configuration.ConfigurationManager -version 4.4.1
   ``` 

1. *Program.cs*를 열고, 구성 관리자에 대한 `using` 문을 추가합니다.

   ```csharp
   using System.Configuration;
   ```

1. `Main` 메서드의 코드를 다음 코드로 바꿉니다.

   ```csharp
   using (var loggerFactory = new LoggerFactory())
   {
       var config = new JobHostConfiguration();
       var instrumentationKey =
           ConfigurationManager.AppSettings["APPINSIGHTS_INSTRUMENTATIONKEY"];
       config.DashboardConnectionString = "";
       config.LoggerFactory = loggerFactory
           .AddApplicationInsights(instrumentationKey, null)
           .AddConsole();
       var host = new JobHost(config);
       host.RunAndBlock();
   }
   ```

## <a name="test-application-insights-logging"></a>Application Insights 로깅 테스트

이 섹션에서는 로컬로 다시 실행하여 로깅 데이터가 이제 Application Insights 및 콘솔 둘 다로 이동하는지 확인합니다.

1. *Hello App Insights!* 를 메시지 텍스트로 입력하는 것을 제외하고는 [이전](#trigger-the-function-in-azure)과 동일한 방식으로 Visual Studio의 **서버 탐색기**를 사용하여 큐 메시지를 만듭니다.

1. 프로젝트를 실행합니다.

   WebJobs SDK에서 큐 메시지를 처리하고, 콘솔 창에 로그를 표시합니다.

1. 콘솔 창을 닫습니다.

1. [Azure Portal](https://portal.azure.com/)을 열고, Application Insights 리소스로 이동합니다.

1. **검색**을 선택합니다.

   ![검색 선택](./media/webjobs-sdk-get-started/select-search.png)

1. *Hello App Insights!* 가 표시되지 않으면 몇 분 동안 정기적으로 **새로 고침**을 선택합니다. (Application Insights 클라이언트에서 처리하는 로그를 플러시하는 데 시간이 걸리기 때문에 로그가 즉시 표시되지 않습니다.)

   ![Application Insights의 로그](./media/webjobs-sdk-get-started/logs-in-ai.png)

1. 콘솔 창을 닫습니다.

## <a name="deploy-as-a-webjob"></a>WebJob으로 배포

이 섹션에서는 프로젝트를 WebJob으로 배포합니다. [앞에서 만든](#create-app-service-app-and-application-insights-instance) App Service 앱에 배포합니다. Azure에서 실행되는 동안 코드를 테스트하려면 큐 메시지를 만들어 함수 호출을 트리거합니다.

1. **솔루션 탐색기**에서 프로젝트를 마우스 오른쪽 단추로 클릭한 다음, **Azure WebJob으로 게시**를 선택합니다.

1. **Azure WebJob** 대화 상자에서 **확인**을 선택합니다.

   ![Azure WebJob 추가](./media/webjobs-sdk-get-started/add-azure-webjob.png)

   Visual Studio에서 WebJob 게시를 위한 NuGet 패키지를 자동으로 설치합니다.

1. **게시** 마법사의 **프로필** 단계에서 **Microsoft Azure App Service**를 선택합니다.

   ![게시 대화 상자](./media/webjobs-sdk-get-started/publish-dialog.png)

1. **App Service** 대화 상자에서 **리소스 그룹 > App Service 앱**을 차례로 선택한 다음, **확인**을 선택합니다.

   ![App Service 대화 상자](./media/webjobs-sdk-get-started/app-service-dialog.png)

1. 게시 프로필이 생성된 후 **게시**를 선택합니다.

## <a name="trigger-the-function-in-azure"></a>Azure에서 함수 트리거

1. 로컬로 실행하고 있지 않은지 확인합니다(아직 열려 있는 경우 콘솔 창을 닫음). 그렇지 않으면 만든 큐 메시지를 로컬 인스턴스에서 가장 먼저 처리할 수 있습니다.

1. Visual Studio의 **큐** 페이지에서 이전처럼 큐에 메시지를 추가합니다.

1. **큐** 페이지를 새로 고치면 Azure에서 실행 중인 함수가 새 메시지를 처리했기 때문에 새 메시지가 사라집니다.

   > [!TIP]
   > Azure에서 테스트하는 경우 [개발 모드](webjobs-sdk-how-to.md#host-development-settings)를 사용하여 큐 트리거 함수를 즉시 호출하고 [큐 폴링 지수 백오프](../azure-functions/functions-bindings-storage-queue.md#trigger---polling-algorithm)로 인한 지연을 방지하도록 합니다.

### <a name="view-logs-in-application-insights"></a>Application Insights에서 로그 보기

1. [Azure Portal](https://portal.azure.com/)을 열고, Application Insights 리소스로 이동합니다.

1. **검색**을 선택합니다.

1. *Hello Azure!* 메시지가 표시되지 않으면 몇 분 동안 정기적으로 **새로 고침**을 선택합니다.

   이전 섹션에서 입력한 *Hello Azure!* 텍스트를 포함하여 WebJob에서 실행되는 함수의 로그가 표시됩니다.

## <a name="add-an-input-binding"></a>입력 바인딩 추가

입력 바인딩은 데이터를 읽는 코드를 간소화합니다. 다음 예제에서는 큐 메시지가 Blob 이름이 되고, Azure Storage에서 Blob 이름을 사용하여 Blob을 찾고 읽습니다.

1. *Functions.cs*에서 `ProcessQueueMessage` 메서드를 다음 코드로 바꿉니다.

   ```cs
   public static void ProcessQueueMessage(
       [QueueTrigger("queue")] string message,
       [Blob("container/{queueTrigger}", FileAccess.Read)] Stream myBlob,
       ILogger logger)
   {
       logger.LogInformation($"Blob name:{message} \n Size: {myBlob.Length} bytes");
   }
   ```

   이 코드에서 `queueTrigger`는 런타임에 다른 값으로 확인되는 [바인딩 식](../azure-functions/functions-triggers-bindings.md#binding-expressions-and-patterns)입니다.  런타임에 큐 메시지의 내용이 포함됩니다.

1. `using`을 추가합니다.

   ```cs
   using System.IO;
   ```

1. 저장소 계정에 Blob 컨테이너를 만듭니다.

   a. Visual Studio의 **서버 탐색기**에서 저장소 계정에 대한 노드를 펼치고, **Blob**을 마우스 오른쪽 단추로 클릭한 다음, **Blob 컨테이너 만들기**를 선택합니다.

   b. **Blob 컨테이너 만들기** 대화 상자에서 *container*를 컨테이너 이름으로 입력한 다음, **확인**을 클릭합니다.

1. *Program.cs* 파일을 Blob 컨테이너에 업로드합니다. (여기서는 이 파일이 예제로 사용되며, 모든 텍스트 파일을 업로드하고, 파일 이름이 포함된 큐 메시지를 만들 수 있습니다.)

   a. **서버 탐색기**에서 만든 컨테이너의 노드를 두 번 클릭합니다.

   b. **컨테이너** 창에서 **업로드** 단추를 선택합니다.

   ![Blob 업로드 단추](./media/webjobs-sdk-get-started/blob-upload-button.png)

   다. *Program.cs*를 찾아 선택한 다음, **확인**을 선택합니다.

1. 메시지 텍스트로 *Program.cs*가 포함된 큐 메시지를 앞에서 만든 큐에 만듭니다.

   ![Program.cs 큐 메시지](./media/webjobs-sdk-get-started/queue-msg-program-cs.png)

1. 프로젝트를 실행합니다.

   큐 메시지는 함수를 트리거하여 Blob을 읽고 해당 길이를 로깅합니다. 콘솔 출력은 다음과 같습니다.

   ```console
   Found the following functions:
   ConsoleApp1.Functions.ProcessQueueMessage
   Job host started
   Executing 'Functions.ProcessQueueMessage' (Reason='New queue message detected on 'queue'.', Id=5a2ac479-de13-4f41-aae9-1361f291ff88)
   Blob name:Program.cs
   Size: 532 bytes
   Executed 'Functions.ProcessQueueMessage' (Succeeded, Id=5a2ac479-de13-4f41-aae9-1361f291ff88)
   ```

## <a name="add-an-output-binding"></a>출력 바인딩 추가

출력 바인딩은 데이터를 쓰는 코드를 간소화합니다. 이 예제에서는 크기를 로깅하지 않고 Blob 복사본을 작성하여 이전 예제를 수정합니다. Blob Storage 바인딩은 이전에 설치한 Azure Storage 확장 패키지에 포함되어 있습니다.

1. `ProcessQueueMessage` 메서드를 다음 코드로 바꿉니다.

   ```cs
   public static void ProcessQueueMessage(
       [QueueTrigger("queue")] string message,
       [Blob("container/{queueTrigger}", FileAccess.Read)] Stream myBlob,
       [Blob("container/copy-{queueTrigger}", FileAccess.Write)] Stream outputBlob,
       ILogger logger)
   {
       logger.LogInformation($"Blob name:{message} \n Size: {myBlob.Length} bytes");
       myBlob.CopyTo(outputBlob);
   }
   ```

1. 메시지 텍스트로 *Program.cs*가 포함된 또 다른 큐 메시지를 만듭니다.

1. 프로젝트를 실행합니다.

   큐 메시지는 함수를 트리거하여 Blob을 읽고, 해당 길이를 로깅하고, 새 Blob을 만듭니다. 콘솔 출력은 동일하지만, Blob 컨테이너 창으로 이동하여 **새로 고침**을 선택하면 *copy-Program.cs*라는 새 Blob이 표시됩니다.

## <a name="next-steps"></a>다음 단계

이 가이드에서는 WebJobs SDK 프로젝트를 만들고, 실행하고, 배포하는 방법을 보여 주었습니다.

WebJobs SDK 프로젝트에 포함되는 모든 항목을 보여 주기 위해 지침에 따라 프로젝트를 처음부터 만들었습니다. 그러나 다음 프로젝트를 만들 때는 **클라우드** 범주의 **Azure WebJob** 템플릿을 사용하는 것이 좋습니다. 이 템플릿은 이미 설정된 NuGet 패키지와 샘플 코드가 포함된 프로젝트를 만듭니다. 새 로깅 프레임워크를 사용하도록 샘플 코드를 변경해야 할 수도 있습니다.

> [!div class="nextstepaction"]
> [WebJobs SDK에 대한 자세한 정보](webjobs-sdk-how-to.md)
