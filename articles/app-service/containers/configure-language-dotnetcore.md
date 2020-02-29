---
title: Linux ASP.NET Core 앱 구성
description: 앱에 대해 미리 작성 된 ASP.NET Core 컨테이너를 구성 하는 방법에 대해 알아봅니다. 이 문서에서는 가장 일반적인 구성 작업을 보여줍니다.
ms.devlang: dotnet
ms.topic: article
ms.date: 08/13/2019
ms.openlocfilehash: 30cd6ad1b5516eb3bc7e858ae364a88ace1b93b3
ms.sourcegitcommit: 3c925b84b5144f3be0a9cd3256d0886df9fa9dc0
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/28/2020
ms.locfileid: "77917633"
---
# <a name="configure-a-linux-aspnet-core-app-for-azure-app-service"></a>Azure App Service에 대 한 Linux ASP.NET Core 앱 구성

ASP.NET Core 앱을 컴파일된 이진 파일로 배포 해야 합니다. Visual Studio 게시 도구는 솔루션을 빌드한 다음 컴파일된 이진 파일을 직접 배포 하는 반면, App Service 배포 엔진은 코드 리포지토리를 먼저 배포한 다음 이진 파일을 컴파일합니다.

이 가이드에서는 App Service에서 기본 제공 Linux 컨테이너를 사용 하는 ASP.NET Core 개발자를 위한 주요 개념 및 지침을 제공 합니다. Azure App Service를 사용한 적이 없는 경우 [ASP.NET Core 빠른](quickstart-dotnetcore.md) 시작 및 [ASP.NET Core SQL Database 자습서](tutorial-dotnetcore-sqldb-app.md) 를 먼저 수행 합니다.

## <a name="show-net-core-version"></a>.NET Core 버전 표시

현재 .NET Core 버전을 표시 하려면 [Cloud Shell](https://shell.azure.com)에서 다음 명령을 실행 합니다.

```azurecli-interactive
az webapp config show --resource-group <resource-group-name> --name <app-name> --query linuxFxVersion
```

모든 지원 되는 .NET Core 버전을 표시 하려면 [Cloud Shell](https://shell.azure.com)에서 다음 명령을 실행 합니다.

```azurecli-interactive
az webapp list-runtimes --linux | grep DOTNETCORE
```

## <a name="set-net-core-version"></a>.NET Core 버전 설정

[Cloud Shell](https://shell.azure.com) 에서 다음 명령을 실행 하 여 .net Core 버전을 2.1로 설정 합니다.

```azurecli-interactive
az webapp config set --name <app-name> --resource-group <resource-group-name> --linux-fx-version "DOTNETCORE|2.1"
```

## <a name="customize-build-automation"></a>빌드 자동화 사용자 지정

빌드 자동화가 설정 된 상태에서 Git 또는 zip 패키지를 사용 하 여 앱을 배포 하는 경우 App Service 다음 시퀀스를 통해 자동화 단계를 빌드합니다.

1. `PRE_BUILD_SCRIPT_PATH`에 지정 된 경우 사용자 지정 스크립트를 실행 합니다.
1. `dotnet restore`를 실행 하 여 NuGet 종속성을 복원 합니다.
1. `dotnet publish`를 실행 하 여 프로덕션을 위한 이진 파일을 빌드합니다.
1. `POST_BUILD_SCRIPT_PATH`에 지정 된 경우 사용자 지정 스크립트를 실행 합니다.

`PRE_BUILD_COMMAND` 및 `POST_BUILD_COMMAND`은 기본적으로 비어 있는 환경 변수입니다. 빌드 전 명령을 실행 하려면 `PRE_BUILD_COMMAND`를 정의 합니다. 빌드 후 명령을 실행 하려면 `POST_BUILD_COMMAND`를 정의 합니다.

다음 예에서는 일련의 명령에 대 한 두 변수를 쉼표로 구분 하 여 지정 합니다.

```azurecli-interactive
az webapp config appsettings set --name <app-name> --resource-group <resource-group-name> --settings PRE_BUILD_COMMAND="echo foo, scripts/prebuild.sh"
az webapp config appsettings set --name <app-name> --resource-group <resource-group-name> --settings POST_BUILD_COMMAND="echo foo, scripts/postbuild.sh"
```

빌드 자동화를 사용자 지정 하는 추가 환경 변수는 [Oryx 구성](https://github.com/microsoft/Oryx/blob/master/doc/configuration.md)을 참조 하세요.

App Service를 실행 하 고 Linux에서 ASP.NET Core 앱을 빌드하는 방법에 대 한 자세한 내용은 [Oryx 설명서: .Net Core 앱이 검색 되 고 빌드되는 방법](https://github.com/microsoft/Oryx/blob/master/doc/runtimes/dotnetcore.md)을 참조 하세요.

## <a name="access-environment-variables"></a>환경 변수 액세스

App Service에서, 앱 코드 외부에서 [앱 설정](../configure-common.md?toc=%2fazure%2fapp-service%2fcontainers%2ftoc.json#configure-app-settings)을 지정할 수 있습니다. 그런 다음 표준 ASP.NET Core 종속성 주입 패턴을 사용 하 여 모든 클래스에서 액세스할 수 있습니다.

```csharp
using Microsoft.Extensions.Configuration;

namespace SomeNamespace 
{
    public class SomeClass
    {
        private IConfiguration _configuration;
    
        public SomeClass(IConfiguration configuration)
        {
            _configuration = configuration;
        }
    
        public SomeMethod()
        {
            // retrieve App Service app setting
            var myAppSetting = _configuration["MySetting"];
            // retrieve App Service connection string
            var myConnString = _configuration.GetConnectionString("MyDbConnection");
        }
    }
}
```

예를 들어 App Service 및 *appsettings*에서 같은 이름을 사용 하 여 앱 설정을 구성 하는 경우 App Service 값이 *appsettings* 값 보다 우선 합니다. 로컬 *appsettings* 값을 사용 하면 응용 프로그램을 로컬로 디버그할 수 있지만 App Service 값을 사용 하면 프로덕션 설정으로 제품에서 앱을 실행할 수 있습니다. 연결 문자열은 동일한 방식으로 작동 합니다. 이러한 방식으로 코드를 변경 하지 않고 응용 프로그램 비밀을 코드 리포지토리 외부에 유지 하 고 적절 한 값에 액세스할 수 있습니다.

## <a name="get-detailed-exceptions-page"></a>자세한 예외 페이지 가져오기

ASP.NET 앱이 Visual Studio 디버거에서 예외를 생성 하는 경우 브라우저는 자세한 예외 페이지를 표시 하지만 해당 페이지가 일반 **HTTP 500** 오류로 대체 되거나 **요청을 처리 하는 동안 오류가 발생 한 App Service 합니다.** 않습니다. App Service에서 자세한 예외 페이지를 표시 하려면 <a target="_blank" href="https://shell.azure.com" >Cloud Shell</a>에서 다음 명령을 실행 하 여 앱에 `ASPNETCORE_ENVIRONMENT` 앱 설정을 추가 합니다.

```azurecli-interactive
az webapp config appsettings set --name <app-name> --resource-group <resource-group-name> --settings ASPNETCORE_ENVIRONMENT="Development"
```

## <a name="detect-https-session"></a>HTTPS 세션 검색

App Service에서, [SSL 종료](https://wikipedia.org/wiki/TLS_termination_proxy)는 네트워크 부하 분산 장치에서 발생하므로 모든 HTTPS 요청은 암호화되지 않은 HTTP 요청으로 앱에 도달합니다. 사용자 요청이 암호화 되었는지 여부를 응용 프로그램 논리에서 알고 있어야 하는 경우 *Startup.cs*에서 전달 된 헤더 미들웨어를 구성 합니다.

- `Startup.ConfigureServices`에서 `X-Forwarded-For` 및 `X-Forwarded-Proto` 헤더를 전달 하도록 [ForwardedHeadersOptions](https://docs.microsoft.com/dotnet/api/microsoft.aspnetcore.builder.forwardedheadersoptions) 를 사용 하 여 미들웨어를 구성 합니다.
- 미들웨어가 App Service 부하 분산 장치를 신뢰할 수 있도록 개인 IP 주소 범위를 알려진 네트워크에 추가 합니다.
- 다른 미들웨어를 호출 하기 전에 `Startup.Configure`에서 [UseForwardedHeaders](https://docs.microsoft.com/dotnet/api/microsoft.aspnetcore.builder.forwardedheadersextensions.useforwardedheaders) 메서드를 호출 합니다.

세 요소를 모두 함께 배치 하면 코드는 다음 예제와 같습니다.

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddMvc();

    services.Configure<ForwardedHeadersOptions>(options =>
    {
        options.ForwardedHeaders =
            ForwardedHeaders.XForwardedFor | ForwardedHeaders.XForwardedProto;
        options.KnownNetworks.Add(new IPNetwork(IPAddress.Parse("::ffff:10.0.0.0"), 104));
        options.KnownNetworks.Add(new IPNetwork(IPAddress.Parse("::ffff:192.168.0.0"), 112));
        options.KnownNetworks.Add(new IPNetwork(IPAddress.Parse("::ffff:172.16.0.0"), 108));
    });
}

public void Configure(IApplicationBuilder app, IHostingEnvironment env)
{
    app.UseForwardedHeaders();

    ...

    app.UseMvc();
}
```

자세한 내용은 [프록시 서버 및 부하 분산 장치를 사용 하도록 ASP.NET Core 구성](https://docs.microsoft.com/aspnet/core/host-and-deploy/proxy-load-balancer)을 참조 하세요.

## <a name="deploy-multi-project-solutions"></a>다중 프로젝트 솔루션 배포

루트 디렉터리에서 *.csproj* 파일을 사용 하 여 배포 엔진에 ASP.NET 리포지토리를 배포 하는 경우 엔진은 프로젝트를 배포 합니다. 루트 디렉터리에 *.sln* 파일을 사용 하 여 ASP.NET 리포지토리를 배포 하는 경우 엔진은 검색 된 첫 번째 웹 사이트 또는 웹 응용 프로그램 프로젝트를 선택 하 여 App Service 합니다. 엔진에서 원하는 프로젝트를 선택 하지 못할 수 있습니다.

다중 프로젝트 솔루션을 배포 하려면 App Service에서 사용할 프로젝트를 다음 두 가지 방법으로 지정할 수 있습니다.

### <a name="using-deployment-file"></a>배포 파일 사용

*배포* 파일을 리포지토리 루트에 추가 하 고 다음 코드를 추가 합니다.

```
[config]
project = <project-name>/<project-name>.csproj
```

### <a name="using-app-settings"></a>앱 설정 사용

<a target="_blank" href="https://shell.azure.com">Azure Cloud Shell</a>에서 다음 CLI 명령을 실행 하 여 App Service 앱에 앱 설정을 추가 합니다. *\<앱 이름 >* , *\<리소스 그룹 이름 >* , *\<프로젝트 이름 >* 을 적절 한 값으로 바꿉니다.

```azurecli-interactive
az webapp config appsettings set --name <app-name> --resource-group <resource-group-name> --settings PROJECT="<project-name>/<project-name>.csproj"
```

## <a name="access-diagnostic-logs"></a>진단 로그 액세스

[!INCLUDE [Access diagnostic logs](../../../includes/app-service-web-logs-access-no-h.md)]

## <a name="open-ssh-session-in-browser"></a>브라우저에서 SSH 세션 열기

[!INCLUDE [Open SSH session in browser](../../../includes/app-service-web-ssh-connect-builtin-no-h.md)]

## <a name="next-steps"></a>다음 단계

> [!div class="nextstepaction"]
> [자습서: SQL Database를 사용 하 여 앱 ASP.NET Core](tutorial-dotnetcore-sqldb-app.md)

> [!div class="nextstepaction"]
> [App Service Linux FAQ](app-service-linux-faq.md)
