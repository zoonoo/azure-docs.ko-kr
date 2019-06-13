---
title: 자습서 - Azure Active Directory B2C를 사용하여 단일 페이지 애플리케이션에서 ASP.NET Core 웹 API로의 액세스 권한 부여 | Microsoft Docs
description: Active Directory B2C를 사용하여 .NET Core Web api를 보호하고 단일 페이지 앱에서 호출하는 방법에 대한 자습서입니다.
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.author: marsma
ms.date: 02/04/2019
ms.custom: mvc
ms.topic: tutorial
ms.service: active-directory
ms.subservice: B2C
ms.openlocfilehash: 609e7bc4104a445c5d77109c9470fb487b2f1336
ms.sourcegitcommit: adb6c981eba06f3b258b697251d7f87489a5da33
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/04/2019
ms.locfileid: "66507741"
---
# <a name="tutorial-grant-access-to-an-aspnet-core-web-api-from-a-single-page-application-using-azure-active-directory-b2c"></a>자습서: Azure Active Directory B2C를 사용하여 단일 페이지 애플리케이션에서 ASP.NET Core 웹 API로의 액세스 권한 부여

이 자습서에서는 단일 페이지 애플리케이션에서 Azure AD(Azure Active Directory) B2C로 보호되는 ASP.NET Core 웹 API 리소스를 호출하는 방법을 보여줍니다.

이 자습서에서는 다음 방법에 대해 알아봅니다.

> [!div class="checklist"]
> * 웹 API 애플리케이션 추가
> * 웹 API에 대한 범위 구성
> * 웹 API에 사용 권한 부여
> * 애플리케이션을 사용하도록 샘플 구성

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>필수 조건

[자습서: Azure Active Directory B2C를 사용하여 단일 페이지 앱으로 계정을 인증하도록 설정](active-directory-b2c-tutorials-spa.md)의 단계와 필수 조건을 완료합니다.

## <a name="add-a-web-api-application"></a>웹 API 애플리케이션 추가

액세스 토큰을 제공하는 클라이언트 애플리케이션을 통해 보호된 리소스 요청을 수락하고 이에 응답하려면, 먼저 웹 API 리소스를 테넌트에 등록해야 합니다.

1. [Azure Portal](https://portal.azure.com)에 로그인합니다.
2. Azure AD B2C 테넌트를 포함하는 디렉터리를 사용하려면 위쪽 메뉴에서 **디렉터리 및 구독 필터**를 클릭하고 테넌트가 포함된 디렉터리를 선택합니다.
3. Azure Portal의 왼쪽 위에서 **모든 서비스**를 선택하고 **Azure AD B2C**를 검색하여 선택합니다.
4. **응용 프로그램**을 선택하고 **추가**를 선택합니다.
5. 애플리케이션의 이름을 입력합니다. 예를 들어 *webapi1*과 같습니다.
6. **웹앱/웹 API 포함** 및 **암시적 흐름 허용**에 **예**를 선택합니다.
7. **회신 URL**에는 Azure AD B2C에서 애플리케이션이 요청한 토큰을 반환하는 엔드포인트를 입력합니다. 이 자습서의 샘플은 로컬에서 실행되고 `https://localhost:5000`에서 수신 대기합니다.
8. **앱 ID URI**에 웹 API에 사용하는 식별자를 입력합니다. 도메인을 포함하는 전체 식별자 URI가 생성됩니다. 예: `https://contosotenant.onmicrosoft.com/api`
9. **만들기**를 클릭합니다.
10. 속성 페이지에서 웹 애플리케이션을 구성할 때 사용할 애플리케이션 ID를 기록합니다.

## <a name="configure-scopes"></a>범위 구성

범위는 보호된 리소스에 대한 액세스를 제어하는 방법을 제공합니다. 범위는 웹 API에서 범위 기반 액세스 제어를 구현하는 데 사용됩니다. 예를 들어 일부 사용자는 읽기 및 쓰기 권한을 모두 가질 수 있지만, 다른 사용자는 읽기 전용 권한을 가질 수 있습니다. 이 자습서에서는 Web API에 대한 읽기 권한을 정의합니다.

1. **애플리케이션**을 선택한 후 *webapi1*을 선택합니다.
2. **게시된 범위**를 선택합니다.
3. **범위**에 `Hello.Read`를 입력하고, 설명에 `Read access to hello`를 입력합니다.
4. **범위**에 `Hello.Write`를 입력하고, 설명에 `Write access to hello`를 입력합니다.
5. **저장**을 클릭합니다.

게시된 범위는 클라이언트 앱 사용 권한을 웹 API에 부여하는 데 사용할 수 있습니다.

## <a name="grant-permissions"></a>권한 부여

애플리케이션에서 보호된 웹 API를 호출하려면 애플리케이션 사용 권한을 API에 부여해야 합니다. 필수 조건 자습서에서 *webapp1*이라는 웹 애플리케이션을 Azure AD B2C에 만들었습니다. 이 애플리케이션은 웹 API를 호출하는 데 사용합니다.

1. **애플리케이션**을 선택한 후 *webapp1*을 선택합니다.
2. **API 액세스**를 선택한 후 **추가**를 선택합니다.
3. **API 선택** 드롭다운에서 *webapi1*을 선택합니다.
4. **범위 선택** 드롭다운에서 이전에 정의한 **Hello.Read** 및 **Hello.Write**를 선택합니다.
5. **확인**을 클릭합니다.

**내 샘플 단일 페이지 앱**은 보호되는 **Hello Core API**를 호출하도록 등록되었습니다. 사용자가 Azure AD B2C로 인증하여 단일 페이지 애플리케이션을 사용합니다. 단일 페이지 앱은 Azure AD B2C에서 권한 부여를 받아 보호되는 웹 API에 액세스합니다.

## <a name="configure-the-sample"></a>샘플 구성

이제 웹 API가 등록되었고 범위가 정의되었으므로 웹 API 코드에서 Azure AD B2C 테넌트를 사용하도록 구성합니다. 이 자습서에서는 GitHub에서 다운로드할 수 있는 샘플 .NET Core 웹 애플리케이션을 구성합니다. GitHub에서 [Zip 파일을 다운로드](https://github.com/Azure-Samples/active-directory-b2c-dotnetcore-webapi/archive/master.zip)하거나 샘플 웹앱을 복제합니다.

```
git clone https://github.com/Azure-Samples/active-directory-b2c-dotnetcore-webapi.git
```

### <a name="configure-the-web-api"></a>Web API 구성

1. Visual Studio에서 **B2C-WebAPI.sln** 솔루션을 엽니다.
2. **appsettings.json** 파일을 엽니다. 다음 값을 업데이트하여 사용자의 테넌트를 사용하도록 Web api를 구성합니다.

    ```javascript
    "AzureAdB2C": 
      {
        "Tenant": "<your tenant name>.onmicrosoft.com", 
        "ClientId": "<application-ID>",
        "Policy": "B2C_1_signupsignin1>",
        "ScopeRead": "Hello.Read"  
      },
    ```

#### <a name="enable-cors"></a>CORS를 사용하도록 설정

단일 페이지 애플리케이션에서 ASP.NET Core 웹 API를 호출할 수 있으려면 [CORS](https://docs.microsoft.com/aspnet/core/security/cors)를 사용하도록 설정해야 합니다.

1. **Startup.cs**에서 CORS를 `ConfigureServices()` 메서드에 추가합니다.

    ```C#
    public void ConfigureServices(IServiceCollection services) {
      services.AddCors();
    ```

2. **Startup.cs**에서 CORS를 `Configure()` 메서드에 구성합니다.

    ```C#
    public void Configure(IApplicationBuilder app, IHostingEnvironment env, ILoggerFactory loggerFactory) {
      app.UseCors(builder =>
        builder.WithOrigins("http://localhost:6420").AllowAnyHeader().AllowAnyMethod());
    ```

3. **속성**에서 **launchSettings.json** 파일을 열고, **iisSettings** *applicationURL* 설정을 찾고, 포트 번호를 API Reply URL `http://localhost:5000`용으로 등록된 값으로 설정합니다.

### <a name="configure-the-single-page-application"></a>단일 페이지 애플리케이션 구성

단일 페이지 애플리케이션은 사용자 등록, 로그인 및 보호되는 ASP.NET Core 웹 API 호출에 Azure AD B2C를 사용합니다. .NET Core 웹 API를 호출하도록 단일 페이지 애플리케이션을 업데이트합니다.

앱 설정을 변경하려면 다음을 수행합니다.

1. `index.html` 파일을 엽니다.
2. Azure AD B2C 테넌트 등록 정보를 사용하여 샘플을 구성합니다. 다음 코드에서 테넌트 이름을 **b2cScopes**에 추가하고 **webApi** 값을 이전에 기록한 *applicationURL* 값으로 변경합니다.

    ```javascript
    // The current application coordinates were pre-registered in a B2C tenant.
    var applicationConfig = {
        clientID: '<application-ID>',
        authority: "https://<your-tenant-name>.b2clogin.com/tfp/<your-tenant-name>.onmicrosoft.com/B2C_1_signupsignin1",
        b2cScopes: ["https://<Your tenant name>.onmicrosoft.com/api/Hello.Read"],
        webApi: 'http://localhost:5000/api/values',
    };
    ```

## <a name="run-the-spa-application-and-web-api"></a>SPA 애플리케이션 및 웹 API 실행

Node.js 단일 페이지 애플리케이션과 .NET Core Web API를 모두 실행해야 합니다.

### <a name="run-the-aspnet-core-web-api"></a>ASP.NET Core Web API 실행 

Visual Studio에서 **F5** 키를 눌러 **B2C-WebAPI.sln** 솔루션을 디버그합니다.

프로젝트가 시작되면 Web API를 요청에 사용할 수 있다고 알려주는 웹 페이지가 기본 브라우저에 표시됩니다.

### <a name="run-the-single-page-app"></a>단일 페이지 앱 실행

1. Node.js 명령 프롬프트를 시작합니다.
2. Node.js 샘플이 포함된 디렉터리로 변경합니다. 예: `cd c:\active-directory-b2c-javascript-msal-singlepageapp`
3. 다음 명령을 실행합니다.
    ```
    npm install && npm update
    node server.js
    ```

    콘솔 창에는 애플리케이션이 호스트되는 포트 번호가 표시됩니다.
    
    ```
    Listening on port 6420...
    ```

4. 애플리케이션을 보려면 브라우저를 사용하여 `http://localhost:6420` 주소로 이동합니다.
5. [단일 페이지 애플리케이션(JavaScript)에서 Azure Active Directory B2C를 사용하여 사용자 인증](active-directory-b2c-tutorials-spa.md)에서 사용한 이메일 주소와 암호를 사용하여 로그인합니다.
6. **호출 API**를 클릭합니다.

사용자 계정을 등록하거나 사용자 계정으로 로그인하면 샘플에서 보호되는 Web api를 호출하고 결과를 반환합니다.

## <a name="next-steps"></a>다음 단계

이 자습서에서는 다음 방법에 대해 알아보았습니다.

> [!div class="checklist"]
> * 웹 API 애플리케이션 추가
> * 웹 API에 대한 범위 구성
> * 웹 API에 사용 권한 부여
> * 애플리케이션을 사용하도록 샘플 구성

> [!div class="nextstepaction"]
> [자습서: Azure Active Directory B2C의 애플리케이션에 ID 공급자 추가](tutorial-add-identity-providers.md)
