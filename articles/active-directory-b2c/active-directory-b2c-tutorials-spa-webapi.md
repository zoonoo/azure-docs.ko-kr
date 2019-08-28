---
title: 자습서 - 단일 페이지 애플리케이션에서 ASP.NET Core 웹 API에 대한 액세스 권한 부여 - Azure Active Directory B2C
description: Active Directory B2C를 사용하여 .NET Core 웹 API를 보호하고 단일 페이지 Node.js 애플리케이션에서 API를 호출하는 방법을 알아봅니다.
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.author: marsma
ms.date: 07/24/2019
ms.custom: mvc
ms.topic: tutorial
ms.service: active-directory
ms.subservice: B2C
ms.openlocfilehash: b53ce30f4c49580bcd8ad3e259adf0300d8bd4a6
ms.sourcegitcommit: c71306fb197b433f7b7d23662d013eaae269dc9c
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/22/2019
ms.locfileid: "68369314"
---
# <a name="tutorial-grant-access-to-an-aspnet-core-web-api-from-a-single-page-application-using-azure-active-directory-b2c"></a>자습서: Azure Active Directory B2C를 사용하여 단일 페이지 애플리케이션에서 ASP.NET Core 웹 API로의 액세스 권한 부여

이 자습서에서는 단일 페이지 애플리케이션에서 Azure AD(Azure Active Directory) B2C로 보호된 ASP.NET Core 웹 API 리소스를 호출하는 방법을 보여 줍니다.

이 자습서에서는 다음 방법에 대해 알아봅니다.

> [!div class="checklist"]
> * 웹 API 애플리케이션 추가
> * 웹 API에 대한 범위 구성
> * 웹 API에 사용 권한 부여
> * 애플리케이션을 사용하도록 샘플 구성

## <a name="prerequisites"></a>필수 조건

* [자습서: Azure Active Directory B2C를 사용하여 단일 페이지 애플리케이션에서 인증을 사용하도록 설정](active-directory-b2c-tutorials-spa.md)의 단계와 필수 조건을 완료합니다.
* Visual Studio 2019 이상 또는 Visual Studio Code
* .NET Core 2.2 이상
* Node.js

## <a name="add-a-web-api-application"></a>웹 API 애플리케이션 추가

액세스 토큰을 제공하는 클라이언트 애플리케이션을 통해 보호된 리소스 요청을 수락하고 이에 응답하려면, 먼저 웹 API 리소스를 테넌트에 등록해야 합니다.

1. [Azure Portal](https://portal.azure.com)에 로그인합니다.
1. Azure AD B2C 테넌트를 포함하는 디렉터리를 사용하려면 위쪽 메뉴에서 **디렉터리 및 구독 필터**를 클릭하고 테넌트가 포함된 디렉터리를 선택합니다.
1. Azure Portal의 왼쪽 위에서 **모든 서비스**를 선택하고 **Azure AD B2C**를 검색하여 선택합니다.
1. **애플리케이션**을 선택하고 **추가**를 선택합니다.
1. 애플리케이션의 이름을 입력합니다. 예를 들어 *webapi1*과 같습니다.
1. **웹앱/웹 API 포함** 및 **암시적 흐름 허용**에 **예**를 선택합니다.
1. **회신 URL**에는 Azure AD B2C에서 애플리케이션이 요청한 토큰을 반환하는 엔드포인트를 입력합니다. 이 자습서의 샘플은 로컬에서 실행되고 `https://localhost:5000`에서 수신 대기합니다.
1. **앱 ID URI**에는 표시된 URI에 대한 API 엔드포인트 식별자를 입력합니다. 자습서의 경우 전체 URI가 `https://contosotenant.onmicrosoft.com/api`와 비슷하도록 `api`를 입력합니다.
1. **만들기**를 클릭합니다.
1. *webapi1* 애플리케이션을 선택하여 해당 속성 페이지를 엽니다.
1. 속성 페이지에 표시된 **애플리케이션 ID**를 적어둡니다. 이 ID는 나중의 단계에서 웹 애플리케이션을 구성할 때 필요합니다.

## <a name="configure-scopes"></a>범위 구성

범위는 보호된 리소스에 대한 액세스를 제어하는 방법을 제공합니다. 범위는 웹 API에서 범위 기반 액세스 제어를 구현하는 데 사용됩니다. 예를 들어 일부 사용자는 읽기 및 쓰기 권한을 모두 가질 수 있지만, 다른 사용자는 읽기 전용 권한을 가질 수 있습니다. 이 자습서에서는 웹 API에 대한 읽기 및 쓰기 권한을 모두 정의합니다.

1. 속성 페이지가 아직 열려 있지 않은 경우 **애플리케이션** , *webapi1*을 차례로 선택하여 엽니다.
1. **게시된 범위**를 선택합니다.
1. **범위**에 대해 `Hello.Read`를 입력하고, **설명**에 대해 `Read access to hello`를 입력합니다.
1. **범위**에 대해 `Hello.Write`를 입력하고, **설명**에 대해 `Write access to hello`를 입력합니다.
1. **저장**을 선택합니다.
1. 나중의 단계에서 단일 페이지 애플리케이션을 구성할 때 사용하기 위해 `Hello.Read` 범위의 **전체 범위 값**을 적어둡니다. 전체 범위 값은 `https://yourtenant.onmicrosoft.com/api/Hello.Read`와 비슷합니다.

게시된 범위는 클라이언트 앱 사용 권한을 웹 API에 부여하는 데 사용할 수 있습니다.

## <a name="grant-permissions"></a>권한 부여

다른 애플리케이션에서 보호된 웹 API를 호출하려면 해당 애플리케이션 권한을 웹 API에 부여해야 합니다.

필수 조건 자습서에서 *webapp1*이라는 웹 애플리케이션을 만들었습니다. 이 자습서에서는 이전 섹션에서 만든 웹 API( *webapi1*)를 호출하도록 해당 애플리케이션을 구성합니다.

1. Azure Portal에서 B2C 테넌트로 이동합니다.
1. **애플리케이션**을 선택한 후 *webapp1*을 선택합니다.
1. **API 액세스**를 선택한 후 **추가**를 선택합니다.
1. **API 선택** 드롭다운에서 *webapi1*을 선택합니다.
1. **범위 선택** 드롭다운에서 이전에 정의한 **Hello.Read** 및 **Hello.Write**를 선택합니다.
1. **확인**을 클릭합니다.

보호된 웹 API를 호출할 수 있도록 단일 페이지 웹 애플리케이션이 등록됩니다. 사용자는 단일 페이지 애플리케이션을 사용하기 위해 Azure AD B2C에서 인증됩니다. 단일 페이지 앱이 Azure AD B2C로부터 보호된 웹 API에 액세스할 수 있는 권한을 부여받습니다.

## <a name="configure-the-sample"></a>샘플 구성

이제 웹 API가 등록되었고 범위가 정의되었으므로 웹 API 코드에서 Azure AD B2C 테넌트를 사용하도록 구성합니다. 이 자습서에서는 GitHub에서 다운로드하는 .NET Core 웹 애플리케이션 샘플을 구성합니다.

[\*.zip 보관 파일을 다운로드](https://github.com/Azure-Samples/active-directory-b2c-dotnetcore-webapi/archive/master.zip)하거나 GitHub에서 웹 API 프로젝트 샘플을 복제합니다.

```console
git clone https://github.com/Azure-Samples/active-directory-b2c-dotnetcore-webapi.git
```

### <a name="configure-the-web-api"></a>Web API 구성

1. Visual Studio 또는 Visual Studio Code에서 *B2C-WebApi/**appsettings.json***  파일을 엽니다.
1. 테넌트 이름, 웹 API 애플리케이션의 애플리케이션 ID, 가입/로그인 정책 이름 및 앞에서 정의한 범위를 반영하도록 `AzureAdB2C` 블록을 수정합니다. 블록은 적절한 `Tenant` 및 `ClientId` 값을 사용하여 다음 예제와 비슷해야 합니다.

    ```json
    "AzureAdB2C": {
      "Tenant": "<your-tenant-name>.onmicrosoft.com",
      "ClientId": "<webapi-application-ID>",
      "Policy": "B2C_1_signupsignin1",

      "ScopeRead": "Hello.Read",
      "ScopeWrite": "Hello.Write"
    },
    ```

#### <a name="enable-cors"></a>CORS를 사용하도록 설정

단일 페이지 애플리케이션에서 ASP.NET Core 웹 API를 호출할 수 있게 하려면 웹 API에서 [CORS](https://docs.microsoft.com/aspnet/core/security/cors)를 사용하도록 설정해야 합니다.

1. *Startup.cs*에서 CORS를 `ConfigureServices()` 메서드에 추가합니다.

    ```csharp
    public void ConfigureServices(IServiceCollection services)
    {
        services.AddCors();
    ```

1. 또한 `ConfigureServices()` 메서드 내에서 `jwtOptions.Authority` 값을 다음 토큰 발급자 URI로 설정합니다.

    `<your-tenant-name>`을 B2C 테넌트 이름으로 바꿉니다.

    ```csharp
    jwtOptions.Authority = $"https://<your-tenant-name>.b2clogin.com/{Configuration["AzureAdB2C:Tenant"]}/{Configuration["AzureAdB2C:Policy"]}/v2.0";
    ```

1. `Configure()` 메서드에서 CORS를 구성합니다.

    ```csharp
    public void Configure(IApplicationBuilder app, IHostingEnvironment env, ILoggerFactory loggerFactory)
    {
        app.UseCors(builder =>
            builder.WithOrigins("http://localhost:6420").AllowAnyHeader().AllowAnyMethod());
    ```

1. (Visual Studio에만 해당) 솔루션 탐색기의 **속성** 아래에서 *launchSettings.json* 파일을 연 다음, `iisExpress` 블록을 찾습니다.
1. (Visual Studio에만 해당) `applicationURL` 값을 이전 단계에서 *webapi1* 애플리케이션을 등록할 때 지정한 포트 번호로 업데이트합니다. 예:

    ```json
    "iisExpress": {
      "applicationUrl": "http://localhost:5000/",
      "sslPort": 0
    }
    ```

### <a name="configure-the-single-page-application"></a>단일 페이지 애플리케이션 구성

이 시리즈의 [이전 자습서](active-directory-b2c-tutorials-spa.md)에 나오는 SPA(단일 페이지 애플리케이션)는 사용자 가입 및 로그인에 Azure AD B2C를 사용하고, *frabrikamb2c* 데모 테넌트에서 보호되는 ASP.NET Core 웹 API를 호출합니다.

이 섹션에서는 *사용자*의 Azure AD B2C 테넌트에서 보호되고 로컬 머신에서 실행되는 ASP.NET Core 웹 API를 호출하도록 단일 페이지 애플리케이션을 업데이트합니다.

SPA의 설정을 변경하려면 다음을 수행합니다.

1. 이전 자습서에서 다운로드하거나 복제한 [active-directory-b2c-javascript-msal-singlepageapp][github-js-spa] 프로젝트에서 *index.html* 파일을 엽니다.
1. 앞에서 만든 *Hello.Read* 범위의 URI와 웹 API의 URL을 사용하여 샘플을 구성합니다.
    1. `appConfig` 정의에서 `b2cScopes` 값을 범위의 전체 URI(앞에서 적어둔 **전체 범위 값**)로 바꿉니다.
    1. `webApi` 값을 이전 섹션에서 지정한 `applicationURL` 값으로 변경합니다.

    `appConfig` 정의는 다음 코드 블록과 비슷해야 합니다(`<your-tenant-name>` 대신 테넌트 이름 사용).

    ```javascript
    // The current application coordinates were pre-registered in a B2C tenant.
    var appConfig = {
      b2cScopes: ["https://<your-tenant-name>.onmicrosoft.com/api/Hello.Read"],
      webApi: "http://localhost:5000/"
    };
    ```

## <a name="run-the-spa-and-web-api"></a>SPA 및 웹 API 실행

마지막으로 로컬 머신에서 ASP.NET Core 웹 API와 Node.js 단일 페이지 애플리케이션을 모두 실행합니다. 그런 다음, 단일 페이지 애플리케이션에 로그인하고, 보호된 API에 대한 요청을 시작하는 단추를 누릅니다.

이 자습서에서 두 애플리케이션은 모두 로컬로 실행되지만, Azure AD B2C를 사용하여 안전하게 가입/로그인하고 보호된 웹 API에 대한 액세스 권한을 부여합니다.

### <a name="run-the-aspnet-core-web-api"></a>ASP.NET Core 웹 API 실행

Visual Studio에서 **F5** 키를 눌러 *B2C-WebAPI.sln* 솔루션을 빌드하고 디버그합니다. 프로젝트가 시작되면 기본 브라우저에 웹 API를 요청할 수 있다고 알려주는 웹 페이지가 표시됩니다.

Visual Studio 대신 `dotnet` CLI를 사용하려면 다음을 수행합니다.

1. 콘솔 창을 열고, *\*.csproj* 파일이 있는 디렉터리로 변경합니다. 예:

    `cd active-directory-b2c-dotnetcore-webapi/B2C-WebApi`

1. `dotnet run`을 실행하여 웹 API를 빌드하고 실행합니다.

    API가 시작되어 실행되면 다음과 비슷한 출력이 표시됩니다(이 자습서에서 `NETSDK1059` 경고는 안전하게 무시할 수 있음).

    ```console
    $ dotnet run
    Hosting environment: Production
    Content root path: /home/user/active-directory-b2c-dotnetcore-webapi/B2C-WebApi
    Now listening on: http://localhost:5000
    Application started. Press Ctrl+C to shut down.
    ```

### <a name="run-the-single-page-app"></a>단일 페이지 앱 실행

1. 콘솔 창을 열고, Node.js 샘플이 있는 디렉터리로 변경합니다. 예:

    `cd active-directory-b2c-javascript-msal-singlepageapp`

1. 다음 명령을 실행합니다.

    ```console
    npm install && npm update
    node server.js
    ```

    콘솔 창에는 애플리케이션이 호스트되는 포트 번호가 표시됩니다.

    ```console
    Listening on port 6420...
    ```

1. 브라우저에서 `http://localhost:6420`으로 이동하여 애플리케이션을 확인합니다.
1. [이전 자습서](active-directory-b2c-tutorials-spa.md)에서 사용한 이메일 주소와 암호를 사용하여 로그인합니다. 로그인에 성공하면 `User 'Your Username' logged-in` 메시지가 표시됩니다.
1. **웹 API 호출** 단추를 선택합니다. SPA에서 Azure AD B2C로부터 권한을 부여받은 다음, 보호된 웹 API에 액세스하여 인덱스 페이지의 내용을 표시합니다.

    ```Output
    Web APi returned:
    "<html>\r\n<head>\r\n  <title>Azure AD B2C API Sample</title>\r\n ...
    ```

## <a name="next-steps"></a>다음 단계

이 자습서에서는 다음 방법에 대해 알아보았습니다.

> [!div class="checklist"]
> * 웹 API 애플리케이션 추가
> * 웹 API에 대한 범위 구성
> * 웹 API에 사용 권한 부여
> * 애플리케이션을 사용하도록 샘플 구성

이제 SPA가 보호된 웹 API에서 리소스를 요청하는 방법을 살펴보았으므로 이러한 애플리케이션 유형에서 서로 간에 상호 작용하고 Azure AD B2C와 상호 작용하는 방법에 대해 자세히 이해할 수 있습니다.

> [!div class="nextstepaction"]
> [Azure Active Directory B2C에서 사용할 수 있는 애플리케이션 유형 >](active-directory-b2c-apps.md)

<!-- Links - EXTERNAL -->
[github-js-spa]: https://github.com/Azure-Samples/active-directory-b2c-javascript-msal-singlepageapp
