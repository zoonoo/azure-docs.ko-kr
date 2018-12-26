---
title: 인증 및 권한 부여를 위해 Azure AD와 통합되는 .NET 웹 API 빌드 | Microsoft Docs
description: 인증 및 권한 부여를 위해 Azure AD와 통합되는 .NET MVC Web API를 빌드하는 방법입니다.
services: active-directory
documentationcenter: .net
author: CelesteDG
manager: mtillman
editor: ''
ms.assetid: 67e74774-1748-43ea-8130-55275a18320f
ms.service: active-directory
ms.component: develop
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: quickstart
ms.date: 09/24/2018
ms.author: celested
ms.reviewer: jmprieur, andret
ms.custom: aaddev
ms.openlocfilehash: 239c0d0adbe89dd3d1d7bc7244a52ab079a36ad4
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/24/2018
ms.locfileid: "46952548"
---
# <a name="quickstart-build-a-net-web-api-that-integrates-with-azure-ad-for-authentication-and-authorization"></a>빠른 시작: 인증 및 권한 부여를 위해 Azure AD와 통합되는 .NET 웹 API 빌드

[!INCLUDE [active-directory-develop-applies-v1](../../../includes/active-directory-develop-applies-v1.md)]

보호된 리소스에 액세스할 수 있는 애플리케이션을 빌드하는 경우 허가되지 않은 액세스로부터 해당 리소스를 보호하는 방법을 알고 있어야 합니다. Azure Active Directory(Azure AD)를 사용하면 몇 개의 코드 줄만으로 단순하고 간편하게 OAuth 2.0 Bearer 액세스 토큰을 사용하여 Web API를 보호할 수 있습니다.

ASP.NET 웹앱에서는 .NET Framework 4.5에 포함된 Microsoft에서 구현한 커뮤니티 기반 OWIN 미들웨어를 사용하여 이 작업을 수행할 수 있습니다. 여기서는 OWIN을 사용하여 "할 일 목록" 웹 API를 빌드합니다.

* 보호되는 API를 지정합니다.
* 웹 API 호출에 유효한 액세스 토큰이 포함되어 있는지 확인합니다.

이 빠른 시작에서는 할 일 목록 API를 빌드하고 다음 작업을 수행하는 방법을 알아봅니다.

1. Azure AD에 애플리케이션을 등록합니다.
2. OWIN 인증 파이프라인을 사용하도록 앱을 설정합니다.
3. Web API를 호출하도록 클라이언트 애플리케이션을 구성합니다.

## <a name="prerequisites"></a>필수 조건

시작하려면 다음과 같은 필수 조건을 완료하세요.

* [앱 기본 사항을 다운로드](https://github.com/AzureADQuickStarts/WebAPI-Bearer-DotNet/archive/skeleton.zip)하거나 [완성된 샘플을 다운로드](https://github.com/AzureADQuickStarts/WebAPI-Bearer-DotNet/archive/complete.zip)합니다. 각 샘플은 Visual Studio 2013 솔루션입니다.
* 응용 프로그램을 등록할 Azure AD 테넌트를 설정합니다. 테넌트가 아직 없는 경우 [가져오는 방법을 알아봅니다](quickstart-create-new-tenant.md).

## <a name="step-1-register-an-application-with-azure-ad"></a>1단계: Azure AD에 응용 프로그램 등록

애플리케이션 보안을 유지하려면 먼저 테넌트에서 애플리케이션을 만들고 몇 가지 중요 정보로 Azure AD를 제공해야 합니다.

1. [Azure Portal](https://portal.azure.com)에 로그인합니다.
2. 페이지 오른쪽 위 모서리에서 계정을 선택하고, **디렉터리 전환** 탐색을 선택한 다음, 적절한 테넌트를 선택하여 Azure AD 테넌트를 선택합니다.
    * 계정에 하나의 Azure AD 테넌트만 있거나 이미 적절한 Azure AD 테넌트를 선택한 경우 이 단계를 건너뛰세요.

3. 왼쪽 탐색 창에서 **Azure Active Directory**를 선택합니다.
4. **앱 등록**을 선택하고 **추가**를 선택합니다.
5. 프롬프트에 따라 새 **웹 애플리케이션 및/또는 Web API**를 만듭니다.
    * **이름**은 사용자에게 응용 프로그램을 설명합니다. **To Do List Service**를 입력합니다.
    * **리디렉션 URI**는 Azure AD가 앱이 요청한 토큰을 반환하는 데 사용하는 구성표 및 문자열 조합입니다. 이 값으로 `https://localhost:44321/` 을 입력합니다.

6. 응용 프로그램에 대한 **설정 > 속성** 페이지에서 앱 ID URI를 업데이트합니다. 테넌트별 식별자를 입력합니다. 예를 들어 `https://contoso.onmicrosoft.com/TodoListService`을 입력합니다.
7. 구성을 저장합니다. 잠시 후에 클라이언트 응용 프로그램을 등록해야 하므로 포털을 열어둡니다.

## <a name="step-2-set-up-the-app-to-use-the-owin-authentication-pipeline"></a>2단계: OWIN 인증 파이프라인을 사용하도록 앱 설정

들어오는 요청 및 토큰 유효성을 검사하려면 Azure AD와 통신하도록 응용 프로그램을 설정해야 합니다.

1. 시작하려면 솔루션을 열고 패키지 관리자 콘솔을 사용하여 OWIN 미들웨어 NuGet 패키지를 TodoListService 프로젝트에 추가합니다.

    ```
    PM> Install-Package Microsoft.Owin.Security.ActiveDirectory -ProjectName TodoListService
    PM> Install-Package Microsoft.Owin.Host.SystemWeb -ProjectName TodoListService
    ```

2. OWIN Startup 클래스를 `Startup.cs`라는 TodoListService 프로젝트에 추가합니다.  프로젝트를 마우스 오른쪽 단추로 클릭하고 **추가 > 새 항목**을 선택한 다음, **OWIN**을 검색합니다. OWIN 미들웨어는 앱이 시작되면 `Configuration(…)` 메서드를 호출합니다.

3. 클래스 선언을 `public partial class Startup`으로 변경합니다. 다른 파일에서 이 클래스의 일부를 이미 구현했습니다. `Configuration(…)` 메서드에서 `ConfgureAuth(…)`를 호출하여 웹앱에 대한 인증을 설정합니다.

    ```csharp
    public partial class Startup
    {
        public void Configuration(IAppBuilder app)
        {
            ConfigureAuth(app);
        }
    }
    ```

4. `App_Start\Startup.Auth.cs` 파일을 열고 `ConfigureAuth(…)` 메서드를 구현합니다. `WindowsAzureActiveDirectoryBearerAuthenticationOptions`에 제공하는 매개 변수는 앱이 Azure AD와 통신하기 위한 좌표로 사용됩니다.

    ```csharp
    public void ConfigureAuth(IAppBuilder app)
    {
        app.UseWindowsAzureActiveDirectoryBearerAuthentication(
            new WindowsAzureActiveDirectoryBearerAuthenticationOptions
            {
                Audience = ConfigurationManager.AppSettings["ida:Audience"],
                Tenant = ConfigurationManager.AppSettings["ida:Tenant"]
            });
    }
    ```

5. `[Authorize]` 특성을 사용하면 JWT(JSON Web Token) 전달자 인증으로 컨트롤러 및 작업을 보호하는 데 도움이 됩니다. authorize 태그로 `Controllers\TodoListController.cs` 클래스를 데코레이팅하여 사용자가 해당 페이지에 액세스하기 전에 로그인하도록 강제 적용합니다.

    ```csharp
    [Authorize]
    public class TodoListController : ApiController
    {
    ```

    권한 있는 호출자가 `TodoListController` API 중 하나를 호출하면 작업은 호출자에 대한 정보에 액세스해야 합니다. OWIN은 `ClaimsPrincpal` 개체를 통해 전달자 토큰 내의 클레임에 액세스할 수 있도록 합니다.  

6. 웹 API에 대한 일반적인 요구 사항은 토큰에 있는 "범위"의 유효성을 검사하여 사용자가 To Do List Service에 액세스하는 데 필요한 권한에 동의했는지 확인하는 것입니다.

    ```csharp
    public IEnumerable<TodoItem> Get()
    {
        // user_impersonation is the default permission exposed by applications in Azure AD
        if (ClaimsPrincipal.Current.FindFirst("http://schemas.microsoft.com/identity/claims/scope").Value != "user_impersonation")
        {
            throw new HttpResponseException(new HttpResponseMessage {
              StatusCode = HttpStatusCode.Unauthorized,
              ReasonPhrase = "The Scope claim does not contain 'user_impersonation' or scope claim not found"
            });
        }
        ...
    }
    ```

7. TodoListService 프로젝트의 루트에서 `web.config` 파일을 열고 `<appSettings>` 섹션에 구성 값을 입력합니다.
    * `ida:Tenant`는 Azure AD 테넌트의 이름(예: contoso.onmicrosoft.com)입니다.
    * `ida:Audience`는 Azure 포털에 입력한 응용 프로그램의 앱 ID URI입니다.

## <a name="step-3-configure-a-client-application-and-run-the-service"></a>3단계: 클라이언트 응용 프로그램 구성 및 서비스 실행

To Do List Service가 작동하는 것을 보려면 먼저 Azure AD에서 토큰을 가져오고 서비스를 호출할 수 있도록 To Do List Client를 구성해야 합니다.

1. [Azure Portal](https://portal.azure.com)로 이동합니다.
1. Azure AD 테넌트에 새 응용 프로그램을 만들고 결과 프롬프트에서 **네이티브 클라이언트 응용 프로그램** 을 선택합니다.
    * **이름**은 사용자에게 응용 프로그램을 설명합니다.
    * **리디렉션 URI** 값으로 `http://TodoListClient/`를 입력합니다.

1. 등록을 완료한 후에는 Azure AD가 사용자 앱에 고유한 응용 프로그램 ID를 할당합니다. 이 값은 다음 단계에서 필요하므로 응용 프로그램 페이지에서 복사해 둡니다.
1. **설정** 페이지에서 **필요한 사용 권한**, **추가**를 차례로 선택합니다. To Do List Service를 찾아서 선택하고 **위임된 사용 권한** 아래에 **TodoListService 액세스** 사용 권한을 추가한 다음, **완료**를 선택합니다.
1. Visual Studio의 TodoListClient 프로젝트에서 `App.config`를 열고 `<appSettings>` 섹션에 구성 값을 입력합니다.

    * `ida:Tenant`는 Azure AD 테넌트의 이름(예: contoso.onmicrosoft.com)입니다.
    * `ida:ClientId`는 Azure Portal에서 복사한 앱 ID입니다.
    * `todo:TodoListResourceId`는 Azure Portal에 입력한 To Do List Service 응용 프로그램의 앱 ID URI입니다.

1. 각 프로젝트를 정리하고 빌드한 후 실행합니다.
1. 아직 수행하지 않은 경우 *.onmicrosoft.com 도메인을 사용하여 테넌트에 새 사용자를 만듭니다.
1. 해당 사용자로 To Do List 클라이언트에 로그인하고 사용자의 To Do List에 일부 작업을 추가합니다.

## <a name="next-steps"></a>다음 단계

* 참조용 자료로 [GitHub](https://github.com/AzureADQuickStarts/WebAPI-Bearer-DotNet/archive/complete.zip)에서 완성된 샘플(사용자 구성 값 제외)을 다운로드하세요. 이제 다른 ID 시나리오로 이동할 수 있습니다.