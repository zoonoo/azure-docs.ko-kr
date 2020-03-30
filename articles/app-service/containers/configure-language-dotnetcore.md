---
title: 리눅스 ASP.NET 코어 애플 리 케이 션을 구성
description: 앱에 대해 미리 빌드된 ASP.NET Core 컨테이너를 구성하는 방법을 알아봅니다. 이 문서에서는 가장 일반적인 구성 작업을 보여줍니다.
ms.devlang: dotnet
ms.topic: article
ms.date: 08/13/2019
ms.openlocfilehash: b1d9e59109f5ace25abb9840b48e44ff03d394e7
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "78255902"
---
# <a name="configure-a-linux-aspnet-core-app-for-azure-app-service"></a>Azure 앱 서비스에 대한 Linux ASP.NET 핵심 앱 구성

ASP.NET 핵심 앱은 컴파일된 바이너리로 배포되어야 합니다. Visual Studio 게시 도구는 솔루션을 빌드한 다음 컴파일된 바이너리를 직접 배포하는 반면 App Service 배포 엔진은 코드 리포지토리를 먼저 배포한 다음 바이너리를 컴파일합니다.

이 가이드에서는 앱 서비스에서 기본 제공 Linux 컨테이너를 사용하는 ASP.NET 핵심 개발자를 위한 주요 개념 및 지침을 제공합니다. Azure 앱 서비스를 사용한 적이 없는 경우 먼저 sql Database 자습서를 사용하여 [ASP.NET 코어 퀵스타트](quickstart-dotnetcore.md) 및 [ASP.NET 코어를](tutorial-dotnetcore-sqldb-app.md) 따릅니다.

## <a name="show-net-core-version"></a>.NET 코어 버전 표시

현재 .NET Core 버전을 표시하려면 [클라우드 셸에서](https://shell.azure.com)다음 명령을 실행합니다.

```azurecli-interactive
az webapp config show --resource-group <resource-group-name> --name <app-name> --query linuxFxVersion
```

지원되는 모든 .NET Core 버전을 표시하려면 [클라우드 셸에서](https://shell.azure.com)다음 명령을 실행합니다.

```azurecli-interactive
az webapp list-runtimes --linux | grep DOTNETCORE
```

## <a name="set-net-core-version"></a>설정 .NET 코어 버전

[클라우드 셸에서](https://shell.azure.com) 다음 명령을 실행하여 .NET Core 버전을 2.1로 설정합니다.

```azurecli-interactive
az webapp config set --name <app-name> --resource-group <resource-group-name> --linux-fx-version "DOTNETCORE|2.1"
```

## <a name="customize-build-automation"></a>빌드 자동화 사용자 지정

빌드 자동화가 켜져 있는 Git 또는 zip 패키지를 사용하여 앱을 배포하는 경우 App Service는 다음 순서를 통해 자동화 단계를 빌드합니다.

1. 에 의해 지정된 `PRE_BUILD_SCRIPT_PATH`경우 사용자 지정 스크립트를 실행합니다.
1. NuGet 종속성을 복원하려면 실행합니다. `dotnet restore`
1. 프로덕션을 위한 바이너리를 빌드하려면 실행합니다. `dotnet publish`
1. 에 의해 지정된 `POST_BUILD_SCRIPT_PATH`경우 사용자 지정 스크립트를 실행합니다.

`PRE_BUILD_COMMAND`기본적으로 `POST_BUILD_COMMAND` 비어 있는 환경 변수입니다. 미리 빌드 명령을 실행하려면 `PRE_BUILD_COMMAND`을 정의합니다. 빌드 후 명령을 실행하려면 `POST_BUILD_COMMAND`을 정의합니다.

다음 예제에서는 쉼표로 구분된 일련의 명령으로 두 변수를 지정합니다.

```azurecli-interactive
az webapp config appsettings set --name <app-name> --resource-group <resource-group-name> --settings PRE_BUILD_COMMAND="echo foo, scripts/prebuild.sh"
az webapp config appsettings set --name <app-name> --resource-group <resource-group-name> --settings POST_BUILD_COMMAND="echo foo, scripts/postbuild.sh"
```

빌드 자동화를 사용자 지정하는 추가 환경 변수는 [Oryx 구성을](https://github.com/microsoft/Oryx/blob/master/doc/configuration.md)참조하십시오.

앱 서비스가 Linux에서 ASP.NET 핵심 앱을 실행하고 빌드하는 방법에 대한 자세한 내용은 [Oryx 설명서: .NET Core 앱을 검색하고 빌드하는 방법을](https://github.com/microsoft/Oryx/blob/master/doc/runtimes/dotnetcore.md)참조하십시오.

## <a name="access-environment-variables"></a>환경 변수 액세스

App Service에서, 앱 코드 외부에서 [앱 설정](../configure-common.md?toc=%2fazure%2fapp-service%2fcontainers%2ftoc.json#configure-app-settings)을 지정할 수 있습니다. 그런 다음 표준 ASP.NET Core 종속성 주입 패턴을 사용하여 모든 클래스에서 액세스할 수 있습니다.

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

예를 들어 App Service및 *appsettings.json에서*동일한 이름으로 앱 설정을 구성하는 경우 앱 서비스 값이 *appsettings.json* 값보다 우선합니다. 로컬 *appsettings.json* 값을 사용하면 앱을 로컬로 디버깅할 수 있지만 앱 서비스 값을 사용하면 프로덕션 설정을 통해 제품에서 앱을 실행할 수 있습니다. 연결 문자열도 같은 방식으로 작동합니다. 이렇게 하면 코드 리포지토리 외부에 응용 프로그램 암호를 유지하고 코드를 변경하지 않고도 적절한 값에 액세스할 수 있습니다.

## <a name="get-detailed-exceptions-page"></a>자세한 예외 페이지 받기

ASP.NET 앱이 Visual Studio 디버거에서 예외를 생성하면 브라우저에 자세한 예외 페이지가 표시되지만 앱 서비스에서 해당 페이지가 일반 **HTTP 500** 오류로 대체되거나 **요청을 처리하는 동안 오류가 발생합니다.** 메시지가 표시됩니다. 앱 서비스에서 자세한 예외 페이지를 표시하려면 `ASPNETCORE_ENVIRONMENT` <a target="_blank" href="https://shell.azure.com" >Cloud Shell에서</a>다음 명령을 실행하여 앱에 앱 설정을 추가합니다.

```azurecli-interactive
az webapp config appsettings set --name <app-name> --resource-group <resource-group-name> --settings ASPNETCORE_ENVIRONMENT="Development"
```

## <a name="detect-https-session"></a>HTTPS 세션 검색

App Service에서, [SSL 종료](https://wikipedia.org/wiki/TLS_termination_proxy)는 네트워크 부하 분산 장치에서 발생하므로 모든 HTTPS 요청은 암호화되지 않은 HTTP 요청으로 앱에 도달합니다. 앱 논리에서 사용자 요청이 암호화되었는지 아닌지 알아야 하는 경우 *Startup.cs*전달된 헤더 미들웨어를 구성합니다.

- `Startup.ConfigureServices`에서 `X-Forwarded-For` 및 `X-Forwarded-Proto` 헤더를 전달하도록 [ForwardedHeadersOptions](https://docs.microsoft.com/dotnet/api/microsoft.aspnetcore.builder.forwardedheadersoptions)를 사용하여 미들웨어를 구성합니다.
- 미들웨어가 앱 서비스 로드 밸러버를 신뢰할 수 있도록 알려진 네트워크에 개인 IP 주소 범위를 추가합니다.
- 다른 미들웨어를 `Startup.Configure` 호출하기 전에 [UseForwardedHeaders 메서드를](https://docs.microsoft.com/dotnet/api/microsoft.aspnetcore.builder.forwardedheadersextensions.useforwardedheaders) 호출합니다.

세 가지 요소를 모두 결합하면 코드는 다음과 같습니다.

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

자세한 내용은 [프록시 서버 및 부하 분산 장치를 사용하도록 ASP.NET Core 구성](https://docs.microsoft.com/aspnet/core/host-and-deploy/proxy-load-balancer)을 참조하세요.

## <a name="deploy-multi-project-solutions"></a>다중 프로젝트 솔루션 배포

루트 디렉터리에서 *.csproj* 파일을 사용하여 배포 엔진에 ASP.NET 리포지토리를 배포하면 엔진이 프로젝트를 배포합니다. 루트 디렉터리에서 *.sln* 파일이 있는 ASP.NET 리포지토리를 배포할 때 엔진은 앱 서비스 앱으로 찾은 첫 번째 웹 사이트 또는 웹 응용 프로그램 프로젝트를 선택합니다. 엔진이 원하는 프로젝트를 선택하지 않을 수 있습니다.

다중 프로젝트 솔루션을 배포하려면 다음 두 가지 방법으로 앱 서비스에서 사용할 프로젝트를 지정할 수 있습니다.

### <a name="using-deployment-file"></a>.deployment 파일 사용

리포지토리 루트에 *.deployment* 파일을 추가하고 다음 코드를 추가합니다.

```
[config]
project = <project-name>/<project-name>.csproj
```

### <a name="using-app-settings"></a>앱 설정 사용

Azure <a target="_blank" href="https://shell.azure.com">클라우드 셸에서</a>다음 CLI 명령을 실행하여 앱 서비스 앱에 앱 설정을 추가합니다. * \<앱 이름>, * * \<리소스 그룹 이름>* 및 * \<프로젝트 이름>* 적절한 값으로 바꿉니다.

```azurecli-interactive
az webapp config appsettings set --name <app-name> --resource-group <resource-group-name> --settings PROJECT="<project-name>/<project-name>.csproj"
```

## <a name="access-diagnostic-logs"></a>진단 로그 액세스

[!INCLUDE [Access diagnostic logs](../../../includes/app-service-web-logs-access-no-h.md)]

## <a name="open-ssh-session-in-browser"></a>브라우저에서 SSH 세션 열기

[!INCLUDE [Open SSH session in browser](../../../includes/app-service-web-ssh-connect-builtin-no-h.md)]

[!INCLUDE [robots933456](../../../includes/app-service-web-configure-robots933456.md)]

## <a name="next-steps"></a>다음 단계

> [!div class="nextstepaction"]
> [자습서: SQL 데이터베이스와 ASP.NET 핵심 응용 프로그램](tutorial-dotnetcore-sqldb-app.md)

> [!div class="nextstepaction"]
> [App Service Linux FAQ](app-service-linux-faq.md)
