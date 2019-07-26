---
title: ASP.NET Core에서 Azure App Configuration 동적 구성 사용 자습서 | Microsoft Docs
description: 이 자습서에서는 ASP.NET Core 앱의 구성 데이터를 동적으로 업데이트하는 방법을 알아봄
services: azure-app-configuration
documentationcenter: ''
author: yegu-ms
manager: balans
editor: ''
ms.assetid: ''
ms.service: azure-app-configuration
ms.workload: tbd
ms.devlang: csharp
ms.topic: tutorial
ms.date: 02/24/2019
ms.author: yegu
ms.custom: mvc
ms.openlocfilehash: 9eccb4ca505dac312dd22123a3585863c67f3ad7
ms.sourcegitcommit: 4b647be06d677151eb9db7dccc2bd7a8379e5871
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/19/2019
ms.locfileid: "68359862"
---
# <a name="tutorial-use-dynamic-configuration-in-an-aspnet-core-app"></a>자습서: ASP.NET Core 앱에서 동적 구성 사용

ASP.NET Core에는 다양한 원본에서 구성 데이터를 읽을 수 있는 플러그형 구성 시스템이 있습니다. 애플리케이션을 다시 시작하지 않고 변경 내용을 즉시 처리할 수 있습니다. ASP.NET Core는 강력한 형식의 .NET 클래스에 대한 구성 설정 바인딩을 지원합니다. 다양한 `IOptions<T>` 패턴을 사용하여 코드에 삽입합니다. 기본 데이터가 변경되면 특히 이러한 패턴 중 하나인 `IOptionsSnapshot<T>`는 애플리케이션의 구성을 자동으로 다시 로드합니다. 애플리케이션의 컨트롤러에 `IOptionsSnapshot<T>`을 삽입하여 Azure App Configuration에 저장된 최신 구성에 액세스할 수 있습니다.

미들웨어를 사용하여 구성 설정 세트를 동적으로 새로 고치도록 App Configuration ASP.NET Core 클라이언트 라이브러리를 설정할 수도 있습니다. 웹앱이 계속해서 요청을 받는 한 구성 설정은 구성 저장소로 계속 업데이트됩니다.

설정을 업데이트하고 구성 저장소에 대한 너무 많은 호출을 피하기 위해 각 설정에 대해 캐시를 사용합니다. 설정의 캐시된 값이 만료될 때까지 새로 고침 작업은 구성 저장소에서 값이 변경된 경우에도 값을 업데이트하지 않습니다. 각 요청의 기본 만료 시간은 30초지만, 필요한 경우 재정의할 수 있습니다.

이 자습서에서는 코드에서 동적 구성 업데이트를 구현하는 방법을 보여줍니다. 빠른 시작에 소개된 웹앱을 기반으로 합니다. 계속 진행하기 전에 먼저 [App Configuration을 사용하여 ASP.NET Core 앱 만들기](./quickstart-aspnet-core-app.md)를 완료합니다.

이 자습서의 단계는 임의의 코드 편집기를 사용하여 수행할 수 있습니다. [Visual Studio Code](https://code.visualstudio.com/)는 Windows, macOS 및 Linux 플랫폼에서 사용할 수 있는 훌륭한 옵션입니다.

이 자습서에서는 다음 방법에 대해 알아봅니다.

> [!div class="checklist"]
> * 앱 구성 저장소의 변경에 따라 해당 구성을 업데이트하도록 애플리케이션을 설정합니다.
> * 애플리케이션의 컨트롤러에 최신 구성을 삽입합니다.

## <a name="prerequisites"></a>필수 조건

이 자습서를 수행하려면 [.NET Core SDK](https://dotnet.microsoft.com/download)를 설치합니다.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="reload-data-from-app-configuration"></a>App Configuration에서 데이터 다시 로드

1. *Program.cs*를 열고, `CreateWebHostBuilder` 메서드를 업데이트하여 `config.AddAzureAppConfiguration()` 메서드를 추가합니다.

    ```csharp
    public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
        WebHost.CreateDefaultBuilder(args)
            .ConfigureAppConfiguration((hostingContext, config) =>
            {
                var settings = config.Build();

                config.AddAzureAppConfiguration(options =>
                {
                    options.Connect(settings["ConnectionStrings:AppConfig"])
                           .ConfigureRefresh(refresh =>
                           {
                               refresh.Register("TestApp:Settings:BackgroundColor")
                                      .Register("TestApp:Settings:FontColor")
                                      .Register("TestApp:Settings:Message")
                           });
                }
            })
            .UseStartup<Startup>();
    ```

    `ConfigureRefresh` 메서드는 새로 고침 작업이 트리거될 때 앱 구성 저장소로 구성 데이터를 업데이트하는 데 사용되는 설정을 지정하는 데 사용됩니다. 새로 고침 작업을 실제로 트리거하려면, 변경이 발생할 때 구성 데이터를 새로 고치도록 새로 고침 미들웨어를 애플리케이션에 대해 구성해야 합니다.

2. 새 `Settings` 클래스를 정의하고 구현하는 *Settings.cs* 파일을 추가합니다.

    ```csharp
    namespace TestAppConfig
    {
        public class Settings
        {
            public string BackgroundColor { get; set; }
            public long FontSize { get; set; }
            public string FontColor { get; set; }
            public string Message { get; set; }
        }
    }
    ```

3. *Startup.cs*를 열고, `Settings` 클래스에 구성 데이터를 바인딩하도록 `ConfigureServices` 메서드를 업데이트합니다.

    ```csharp
    public void ConfigureServices(IServiceCollection services)
    {
        services.Configure<Settings>(Configuration.GetSection("TestApp:Settings"));

        services.Configure<CookiePolicyOptions>(options =>
        {
            options.CheckConsentNeeded = context => true;
            options.MinimumSameSitePolicy = SameSiteMode.None;
        });

        services.AddMvc().SetCompatibilityVersion(CompatibilityVersion.Version_2_1);
    }
    ```

4. `Configure` 메서드를 업데이트하여 ASP.NET Core 웹앱이 요청을 계속 받는 동안 새로 고침을 위해 등록된 구성 설정을 업데이트할 수 있는 미들웨어를 추가합니다.

    ```csharp
    public void Configure(IApplicationBuilder app, IHostingEnvironment env)
    {
        app.UseAzureAppConfiguration();
        app.UseMvc();
    }
    ```
    
    미들웨어는 `Program.cs`의 `AddAzureAppConfiguration` 메서드에 지정된 새로 고침 구성을 사용하여 ASP.NET Core 웹앱에서 받은 각 요청에 대해 새로 고침을 트리거합니다. 각 요청마다 새로 고침 작업이 트리거되고 클라이언트 라이브러리는 등록된 구성 설정의 캐시된 값이 만료되었는지 확인합니다. 만료된 캐시된 값은, 설정 값이 앱 구성 저장소로 업데이트되고 나머지 값은 그대로 유지됩니다.
    
    > [!NOTE]
    > 구성 설정에 대한 기본 캐시 만료 시간은 30초이지만 `ConfigureRefresh` 메서드에 대한 인수로 전달된 옵션 이니셜라이저의 `SetCacheExpiration` 메서드를 호출하여 재정의할 수 있습니다.

## <a name="use-the-latest-configuration-data"></a>최신 구성 데이터 사용

1. 컨트롤러 디렉터리에서 *HomeController.cs*를 열고, `Microsoft.Extensions.Options` 패키지의 참조를 추가합니다.

    ```csharp
    using Microsoft.Extensions.Options;
    ```

2. 종속성 주입을 통해 `Settings`를 받도록 `HomeController` 클래스를 업데이트하고 해당 값을 사용합니다.

    ```csharp
    public class HomeController : Controller
    {
        private readonly Settings _settings;
        public HomeController(IOptionsSnapshot<Settings> settings)
        {
            _settings = settings.Value;
        }

        public IActionResult Index()
        {
            ViewData["BackgroundColor"] = _settings.BackgroundColor;
            ViewData["FontSize"] = _settings.FontSize;
            ViewData["FontColor"] = _settings.FontColor;
            ViewData["Message"] = _settings.Message;

            return View();
        }
    }
    ```

3. 보기 > 홈 디렉터리에서 *Index.cshtml*을 열고, 해당 콘텐츠를 다음 스크립트로 바꿉니다.

    ```html
    <!DOCTYPE html>
    <html lang="en">
    <style>
        body {
            background-color: @ViewData["BackgroundColor"]
        }
        h1 {
            color: @ViewData["FontColor"];
            font-size: @ViewData["FontSize"];
        }
    </style>
    <head>
        <title>Index View</title>
    </head>
    <body>
        <h1>@ViewData["Message"]</h1>
    </body>
    </html>
    ```

## <a name="build-and-run-the-app-locally"></a>로컬로 앱 빌드 및 실행

1. .NET Core CLI를 사용하여 앱을 빌드하려면 명령 셸에서 다음 명령을 실행합니다.

        dotnet build

2. 빌드가 성공적으로 완료되면 다음 명령을 실행하여 웹앱을 로컬로 실행합니다.

        dotnet run

3. 브라우저 창을 열고, 로컬로 호스팅되는 웹앱에 대한 기본 URL인 `http://localhost:5000`으로 이동합니다.

    ![로컬로 빠른 시작 앱 시작](./media/quickstarts/aspnet-core-app-launch-local-before.png)

4. [Azure Portal](https://portal.azure.com)에 로그인합니다. **모든 리소스**를 선택하고, 빠른 시작에서 만든 앱 구성 저장소 인스턴스를 선택합니다.

5. **구성 탐색기**를 선택하고, 다음 키의 값을 업데이트합니다.

    | 키 | 값 |
    |---|---|
    | TestApp:Settings:BackgroundColor | green |
    | TestApp:Settings:FontColor | lightGray |
    | TestApp:Settings:Message | 이제 라이브 업데이트를 사용하여 Azure App Configuration 데이터 업데이트! |

6. 새 구성 설정을 확인하려면 브라우저 페이지를 새로 고칩니다. 변경 내용이 반영되려면 브라우저 페이지를 두 번 이상 새로 고쳐야 할 수 있습니다.

    ![로컬로 빠른 시작 앱 새로 고침](./media/quickstarts/aspnet-core-app-launch-local-after.png)
    
    > [!NOTE]
    > 구성 설정은 기본 만료 시간 30초로 캐시되기 때문에 앱 구성 저장소의 설정이 변경되면 캐시가 만료되어야만 웹앱에 반영됩니다.

## <a name="clean-up-resources"></a>리소스 정리

[!INCLUDE [azure-app-configuration-cleanup](../../includes/azure-app-configuration-cleanup.md)]

## <a name="next-steps"></a>다음 단계

이 자습서에서는 Azure 관리형 서비스 ID를 추가하여 App Configuration에 대한 액세스 관리를 간소화하고 앱에 대한 자격 증명 관리를 개선했습니다. App Configuration을 사용하는 방법에 대해 자세히 알아보려면 Azure CLI 샘플로 계속 진행하세요.

> [!div class="nextstepaction"]
> [CLI 샘플](./cli-samples.md)
