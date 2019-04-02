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
ms.openlocfilehash: a721cc2252619923496ee5a3a8ae590a5cda3b04
ms.sourcegitcommit: 0dd053b447e171bc99f3bad89a75ca12cd748e9c
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/26/2019
ms.locfileid: "58487552"
---
# <a name="quickstart-create-an-aspnet-core-app-with-azure-app-configuration"></a>빠른 시작: App Configuration을 사용하여 ASP.NET Core 앱 만들기

Azure App Configuration은 Azure의 관리형 구성 서비스로서, 코드와 분리된 한 곳에서 모든 애플리케이션 설정을 쉽게 저장하고 관리할 수 있습니다. 이 빠른 시작은 ASP.NET Core 웹앱으로 서비스를 통합하는 방법을 보여줍니다. 

ASP.NET Core는 애플리케이션에서 지정한 하나 이상의 데이터 원본의 설정을 사용하여 단일 키-값 기반 구성 개체를 작성합니다. 이러한 데이터 원본은 *구성 공급자*라고 합니다. App Configuration의 .NET Core 클라이언트가 이러한 공급자로 구현되므로 서비스가 다른 데이터 원본처럼 표시됩니다.

이 빠른 시작의 단계는 임의의 코드 편집기를 사용하여 수행할 수 있습니다. [Visual Studio Code](https://code.visualstudio.com/)는 Windows, macOS 및 Linux 플랫폼에서 사용할 수 있는 훌륭한 옵션입니다.

## <a name="prerequisites"></a>필수 조건

이 빠른 시작을 수행하려면 [.NET Core SDK](https://dotnet.microsoft.com/download)를 설치합니다.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="create-an-app-configuration-store"></a>앱 구성 저장소 만들기

[!INCLUDE [azure-app-configuration-create](../../includes/azure-app-configuration-create.md)]

## <a name="create-an-aspnet-core-web-app"></a>ASP.NET Core 웹앱 만들기

[.NET Core CLI(명령줄 인터페이스)](https://docs.microsoft.com/dotnet/core/tools/)를 사용하여 새 ASP.NET Core MVC 웹앱 프로젝트를 만듭니다. Visual Studio 대신 .NET Core CLI를 사용하면 Windows, macOS 및 Linux 플랫폼에서 사용할 수 있다는 이점이 있습니다.

1. 프로젝트에 대한 새 폴더를 만듭니다. 이 빠른 시작에서는 *TestAppConfig*라는 이름으로 지정합니다.

2. 새 폴더에서 다음 명령을 실행하여 새 ASP.NET Core MVC 웹앱 프로젝트를 만듭니다.

        dotnet new mvc

## <a name="add-secret-manager"></a>비밀 관리자 추가

[비밀 관리자 도구](https://docs.microsoft.com/aspnet/core/security/app-secrets)를 프로젝트에 추가합니다. 암호 관리자 도구는 개발 작업에 대한 중요한 데이터를 프로젝트 트리 외부에 저장합니다. 이 방법을 사용하면 소스 코드 내에서 앱 암호를 실수로 공유하는 경우를 방지할 수 있습니다.

- *.csproj* 파일을 엽니다. 아래와 같이 `UserSecretsId` 요소를 추가하고, 해당 값을 고유한 값(일반적으로 GUID)으로 바꿉니다. 파일을 저장합니다.

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

## <a name="connect-to-an-app-configuration-store"></a>앱 구성 저장소에 연결

1. 다음 명령을 실행하여 `Microsoft.Extensions.Configuration.AzureAppConfiguration` NuGet 패키지에 대한 참조를 추가합니다.

        dotnet add package Microsoft.Extensions.Configuration.AzureAppConfiguration --version 1.0.0-preview-007830001

2. 다음 명령을 실행하여 프로젝트에 대한 패키지를 복원합니다.

        dotnet restore

3. *ConnectionStrings:AppConfig*라는 비밀을 비밀 관리자에 추가합니다.

    이 비밀에는 앱 구성 저장소에 액세스하기 위한 연결 문자열이 포함되어 있습니다. 다음 명령의 값을 앱 구성 저장소에 대한 연결 문자열로 바꿉니다.

    이 명령은 *.csproj* 파일이 있는 동일한 디렉터리에서 실행해야 합니다.

        dotnet user-secrets set ConnectionStrings:AppConfig "Endpoint=<your_endpoint>;Id=<your_id>;Secret=<your_secret>"

    비밀 관리자는 웹앱을 로컬로 테스트하는 데만 사용됩니다. 앱이 배포되면(예: [Azure App Service](https://azure.microsoft.com/services/app-service/web)에) 애플리케이션 설정(예: App Service의 **연결 문자열**)을 사용합니다. 비밀 관리자에서 연결 문자열을 저장하는 대신 이 설정을 사용합니다.

    이 비밀은 구성 API를 사용하여 액세스됩니다. 콜론(:)은 지원되는 모든 플랫폼에서 구성 API를 통해 구성 이름으로 작동합니다. [환경별 구성](https://docs.microsoft.com/aspnet/core/fundamentals/configuration/index?tabs=basicconfiguration&view=aspnetcore-2.0)을 참조하세요.

4. Program.cs를 열고, `config.AddAzureAppConfiguration()` 메서드를 호출하여 App Configuration을 사용하도록 `CreateWebHostBuilder` 메서드를 업데이트합니다.

    ```csharp
    using Microsoft.Extensions.Configuration.AzureAppConfiguration;

    ...

    public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
        WebHost.CreateDefaultBuilder(args)
            .ConfigureAppConfiguration((hostingContext, config) =>
            {
                var settings = config.Build();
                config.AddAzureAppConfiguration(options => {
                    options.Connect(settings["ConnectionStrings:AppConfig"])
                           .SetOfflineCache(new OfflineFileCache());
                });
            })
            .UseStartup<Startup>();
    ```

5. 보기 > 홈 디렉터리에서 Index.cshtml을 열고, 해당 콘텐츠를 다음 코드로 바꿉니다.

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

6. 보기 > 공유 디렉터리에서 _Layout.cshtml을 열고, 해당 콘텐츠를 다음 코드로 바꿉니다.

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

3. 브라우저 창을 열고, 로컬로 호스팅되는 웹앱에 대한 기본 URL인 `http://localhost:5000`으로 이동합니다.

    ![로컬로 빠른 시작 앱 시작](./media/quickstarts/aspnet-core-app-launch-local.png)

## <a name="clean-up-resources"></a>리소스 정리

[!INCLUDE [azure-app-configuration-cleanup](../../includes/azure-app-configuration-cleanup.md)]

## <a name="next-steps"></a>다음 단계

이 빠른 시작에서는 새 앱 구성 저장소를 만들고, [앱 구성 공급자](https://go.microsoft.com/fwlink/?linkid=2074664)를 통해 ASP.NET Core 웹앱에서 사용했습니다. App Configuration을 사용하는 방법을 자세히 알아보려면 인증에 대해 설명하는 다음 자습서로 계속 진행하세요.

> [!div class="nextstepaction"]
> [Azure 리소스 통합을 위한 관리 ID](./integrate-azure-managed-service-identity.md)
