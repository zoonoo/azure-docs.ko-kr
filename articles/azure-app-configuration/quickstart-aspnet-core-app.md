---
title: ASP.NET Core 지원 Azure App Configuration용 빠른 시작 | Microsoft Docs
description: ASP.NET Core 앱 지원 Azure App Configuration 사용 빠른 시작
services: azure-app-configuration
documentationcenter: ''
author: yegu-ms
manager: balans
editor: ''
ms.assetid: ''
ms.service: azure-app-configuration
ms.devlang: csharp
ms.topic: quickstart
ms.tgt_pltfrm: ASP.NET Core
ms.workload: tbd
ms.date: 02/24/2019
ms.author: yegu
ms.openlocfilehash: ce19041b29d567f061dde59fbe041adf61f889a0
ms.sourcegitcommit: fdd6a2927976f99137bb0fcd571975ff42b2cac0
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/27/2019
ms.locfileid: "56961485"
---
# <a name="quickstart-create-an-aspnet-core-app-with-azure-app-configuration"></a>빠른 시작: App Configuration을 사용하여 ASP.NET Core 앱 만들기

Azure App Configuration은 Azure의 관리 구성 서비스로서, 코드와 구분하여 한 곳에서 모든 애플리케이션 설정을 쉽게 저장하고 관리할 수 있습니다. 이 빠른 시작은 ASP.NET Core 웹앱으로 서비스를 통합하는 방법을 보여줍니다. ASP.NET Core는 *구성 공급자*라고 하는 애플리케이션에서 지정한 하나 이상의 데이터 원본의 설정을 사용하는 단일 키 값 기반 구성 개체를 빌드합니다. App Configuration의 .NET Core 클라이언트가 공급자 등으로 구현되므로, 서비스는 또 다른 데이터 원본인 것처럼 나타납니다.

이 빠른 시작의 단계를 완료하려면 아무 코드 편집기나 사용할 수 있습니다. 그러나 [Visual Studio Code](https://code.visualstudio.com/)는 Windows, macOS 및 Linux 플랫폼에서 사용할 수 있는 뛰어난 옵션입니다.

## <a name="prerequisites"></a>필수 조건

이 빠른 시작을 완료하려면 [.NET Core SDK](https://dotnet.microsoft.com/download)를 설치합니다.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="create-an-app-configuration-store"></a>앱 구성 저장소 만들기

[!INCLUDE [azure-app-configuration-create](../../includes/azure-app-configuration-create.md)]

## <a name="create-an-aspnet-core-web-app"></a>ASP.NET Core 웹앱 만들기

[.NET Core CLI(명령줄 인터페이스)](https://docs.microsoft.com/dotnet/core/tools/)를 사용하여 새 ASP.NET Core MVC 웹앱 프로젝트를 만듭니다. Visual Studio보다 .NET Core CLI를 사용할 때의 이점은 Windows, macOS 및 Linux 플랫폼에서 사용할 수 있다는 것입니다.

1. 프로젝트에 대한 새 폴더를 만듭니다. 이 빠른 시작에서는 *TestAppConfig*라고 이름을 지정합니다.

2. 새 폴더에서 다음 명령을 실행하여 새 ASP.NET Core MVC 웹앱 프로젝트를 만듭니다.

        dotnet new mvc

## <a name="add-secret-manager"></a>비밀 관리자 추가

프로젝트에 [비밀 관리자 도구](https://docs.microsoft.com/aspnet/core/security/app-secrets)를 추가합니다. 암호 관리자 도구는 개발 작업에 대한 중요한 데이터를 프로젝트 트리 외부에 저장합니다. 이 방법을 사용하면 소스 코드 내에서 앱 암호를 실수로 공유하는 경우를 방지할 수 있습니다.

- *.csproj* 파일을 엽니다. 아래 그림과 같이 `UserSecretsId` 요소를 추가하고 이 값을 고유한 값(일반적으로 GUID)으로 대체합니다. 파일을 저장합니다.

    ```xml
    <Project Sdk="Microsoft.NET.Sdk.Web">

    <PropertyGroup>
        <TargetFramework>netcoreapp2.1</TargetFramework>
        <UserSecretsId>79a3edd0-2092-40a2-a04d-dcb46d5ca9ed</UserSecretsId>
    </PropertyGroup>

    <ItemGroup>
        <PackageReference Include="Microsoft.AspNetCore.App" />
        <PackageReference Include="Microsoft.AspNetCore.Razor.Design" Version="2.1.2" PrivateAssets="All" />
    </ItemGroup>

    </Project>
    ```

## <a name="connect-to-app-configuration-store"></a>앱 구성 저장소에 연결

1. 다음 명령을 실행하여 `Microsoft.Extensions.Configuration.AzureAppConfiguration` NuGet 패키지에 대한 참조를 추가합니다.

        dotnet add package Microsoft.Extensions.Configuration.AzureAppConfiguration

2. 다음 명령을 실행하여 프로젝트에 대한 패키지를 복원합니다.

        dotnet restore

3. *ConnectionStrings:AppConfig*라는 비밀을 비밀 관리자에 추가합니다.

    이 비밀은 앱 구성 저장소에 액세스하기 위한 연결 문자열을 포함합니다. 아래 명령의 값을 앱 구성 저장소에 대한 연결 문자열로 바꿉니다.

    이 명령은 *.csproj* 파일이 있는 동일한 디렉터리에서 실행해야 합니다.

        dotnet user-secrets set ConnectionStrings:AppConfig "Endpoint=<your_endpoint>;Id=<your_id>;Secret=<your_secret>"

    비밀 관리자는 로컬로 웹앱을 테스트하는 데만 사용됩니다. 앱이 배포되면(예: [Azure App Service](https://azure.microsoft.com/services/app-service/web)에) 비밀 관리자로 연결 문자열을 저장하는 대신 애플리케이션 설정(예: App Service의 **연결 문자열**)을 사용하게 됩니다.

    이 비밀은 구성 API를 사용하여 액세스됩니다. 콜론(:)은 지원되는 모든 플랫폼의 구성 API에서 구성 이름으로 작동합니다. [환경별 구성](https://docs.microsoft.com/aspnet/core/fundamentals/configuration/index?tabs=basicconfiguration&view=aspnetcore-2.0#configuration-by-environment)을 참조하세요.

4. *Program.cs*를 열고 `config.AddAzureAppConfiguration()` 메서드를 호출하여 App Configuration을 사용하도록 `CreateWebHostBuilder` 메서드를 업데이트합니다.

    ```csharp
    public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
        WebHost.CreateDefaultBuilder(args)
            .ConfigureAppConfiguration((hostingContext, config) =>
            {
                var settings = config.Build();
                config.AddAzureAppConfiguration(settings["ConnectionStrings:AppConfig"]);
            })
            .UseStartup<Startup>();
    ```

5. *보기* > *홈* 디렉터리에서 *Index.cshtml*을 열고 다음 코드로 콘텐츠를 바꿉니다.

    ```html
    @using Microsoft.Extensions.Configuration
    @inject IConfiguration Configuration

    <!DOCTYPE html>
    <html lang="en">
    <style>
        body {
            background-color: @Configuration["TestApp:Settings:BackgroundColor"]
        }
        h1 {
            color: @Configuration["TestApp:Settings:FontColor"];
            font-size: @Configuration["TestApp:Settings:FontSize"];
        }
    </style>
    <head>
        <title>Index View</title>
    </head>
    <body>
        <h1>@Configuration["TestApp:Settings:Message"]</h1>
    </body>
    </html>
    ```

6. *보기* > *공유* 디렉터리에서 *_Layout.cshtml*을 열고 다음 코드로 콘텐츠를 바꿉니다.

    ```html
    <!DOCTYPE html>
    <html>
    <head>
        <meta charset="utf-8" />
        <meta name="viewport" content="width=device-width, initial-scale=1.0" />
        <title>@ViewData["Title"] - hello_world</title>

        <link rel="stylesheet" href="~/lib/bootstrap/dist/css/bootstrap.css" />
        <link rel="stylesheet" href="~/css/site.css" />
    </head>
    <body>
        <div class="container body-content">
            @RenderBody()
        </div>

        <script src="~/lib/jquery/dist/jquery.js"></script>
        <script src="~/lib/bootstrap/dist/js/bootstrap.js"></script>
        <script src="~/js/site.js" asp-append-version="true"></script>

        @RenderSection("Scripts", required: false)
    </body>
    </html>
    ```

## <a name="build-and-run-the-app-locally"></a>로컬로 앱 빌드 및 실행

1. .NET Core CLI를 사용하여 앱을 빌드하려면 명령 셸에서 다음 명령을 실행합니다.

        dotnet build

2. 빌드가 성공적으로 완료되면 다음 명령을 실행하여 웹앱을 로컬로 실행합니다.

        dotnet run

3. 브라우저 창을 시작하고 로컬 호스팅된 웹앱에 대한 기본 URL인 `http://localhost:5000`으로 이동합니다.

    ![빠른 시작 앱 시작 로컬](./media/quickstarts/aspnet-core-app-launch-local.png)

## <a name="clean-up-resources"></a>리소스 정리

[!INCLUDE [azure-app-configuration-cleanup](../../includes/azure-app-configuration-cleanup.md)]

## <a name="next-steps"></a>다음 단계

이 빠른 시작에서는 새 앱 구성 저장소를 만들고 .ASP.NET Core 웹앱에 사용해봤습니다. App Configuration을 사용하는 방법을 자세히 알아보려면 인증을 보여주는 다음 자습서를 계속 진행합니다.

> [!div class="nextstepaction"]
> [Azure Resources Integration에 대한 관리 ID](./integrate-azure-managed-service-identity.md)
