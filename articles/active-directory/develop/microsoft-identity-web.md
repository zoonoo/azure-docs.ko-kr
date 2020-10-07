---
title: Microsoft Id 웹 인증 라이브러리 개요
titleSuffix: Microsoft identity platform
description: Azure Active Directory, Azure AD B2C, Microsoft Graph 및 기타 웹 Api와 통합 되는 ASP.NET Core 응용 프로그램에 대 한 인증 및 권한 부여 라이브러리인 Microsoft Id 웹에 대해 알아봅니다.
services: active-directory
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 10/09/2020
ms.author: jmprieur
ms.reviewer: marsma
ms.custom: devx-track-csharp, aaddev
ms.openlocfilehash: 4d915cee962546e355ee9d53c683581730bbbf36
ms.sourcegitcommit: ef69245ca06aa16775d4232b790b142b53a0c248
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/06/2020
ms.locfileid: "91778857"
---
# <a name="microsoft-identity-web-authentication-library"></a>Microsoft Id 웹 인증 라이브러리

Microsoft Identity Web은 웹 앱 및 웹 Api에 Microsoft id 플랫폼과 통합 되는 인증 및 권한 부여 지원을 간소화 하는 ASP.NET Core 라이브러리 집합입니다. [.Net 용 MSAL (Microsoft Authentication Library)](https://github.com/azuread/microsoft-authentication-library-for-dotnet)및 ASP.NET Core, 해당 인증 미들웨어를 함께 연결 하는 단일 surface API 편의 계층을 제공 합니다.

## <a name="supported-application-scenarios"></a>지원 되는 응용 프로그램 시나리오

웹 앱 또는 웹 Api ASP.NET Core를 빌드하고 Azure Active Directory (Azure AD) 또는 IAM (id 및 액세스 관리)에 대 한 Azure AD B2C를 사용 하려는 경우 다음과 같은 모든 시나리오에 Microsoft Identity Web을 사용 하는 것이 좋습니다.

- [사용자가 로그인하는 웹앱](scenario-web-app-sign-user-overview.md)
- [사용자를 로그인 하 고 대신 웹 API를 호출 하는 웹 앱](scenario-web-app-call-api-overview.md)
- [인증 된 사용자만 액세스할 수 있는 보호 된 웹 API](scenario-protected-web-api-overview.md)
- [로그인 한 사용자를 대신 하 여 다른 (다운스트림) 웹 API를 호출 하는 보호 된 web API](scenario-web-api-call-api-overview.md)

## <a name="get-the-library"></a>라이브러리 가져오기

[NuGet](#nuget), [.net Core 프로젝트 템플릿](#project-templates)및 [GitHub](#github)에서 Microsoft Identity Web을 가져올 수 있습니다.

#### <a name="nuget"></a>NuGet

Microsoft Identity Web은 NuGet에서 앱의 요구에 따라 모듈형 기능을 제공 하는 패키지 집합으로 사용할 수 있습니다. .NET CLI의 `dotnet add` 명령 또는 Visual Studio의 **NuGet 패키지 관리자** 를 사용 하 여 프로젝트에 적절 한 패키지를 설치 합니다.

- [Microsoft. Identity](https://www.nuget.org/packages/Microsoft.Identity.Web) -주 패키지입니다. Microsoft Identity Web을 사용 하는 모든 앱에 필요 합니다.
- [Microsoft. id](https://www.nuget.org/packages/Microsoft.Identity.Web.UI) -선택 사항입니다. 사용자 로그인 및 로그 아웃 및 웹 앱에 대 한 연결 된 컨트롤러에 대 한 UI를 추가 합니다.
- [Microsoft.azure.webjobs.extensions.microsoftgraph](https://www.nuget.org/packages/Microsoft.Identity.Web.MicrosoftGraph) -선택 사항입니다. Microsoft Graph API와의 간소화 된 상호 작용을 제공 합니다.

#### <a name="project-templates"></a>프로젝트 템플릿

Microsoft Id 웹 프로젝트 템플릿은 .NET 5.0에 포함 되어 있으며 ASP.NET Core 3.1 프로젝트를 다운로드할 수 있습니다.

ASP.NET Core 3.1를 사용 하는 경우 .NET CLI를 사용 하 여 템플릿을 설치 합니다.

```dotnetcli
dotnet new --install Microsoft.Identity.Web.ProjectTemplates::1.0.0
```

다음 다이어그램은 지원 되는 앱 형식 및 관련 인수에 대 한 개략적인 뷰를 보여 줍니다.

:::image type="content" source="media/microsoft-identity-web-overview/diagram-microsoft-identity-web-templates.png" lightbox="media/microsoft-identity-web-overview/diagram-microsoft-identity-web-templates.png" alt-text="Microsoft Identity Web 용 사용 가능한 점 net CLI 프로젝트 템플릿 다이어그램&quot;:::
<br /><sup><b>*</b></sup>`MultiOrg`는에서 지원 되지 `webapi2` 않지만 테 넌 트를 또는로 설정 하 여 *appsettings.js* 에서 사용 하도록 설정할 수 있습니다. `common``organizations`
<br /><sup><b>**</b></sup>`--calls-graph`는 Azure AD B2C 지원 되지 않습니다.

[Blazor Server 자습서](tutorial-blazor-server.md)에서 가져온이 예제 .net CLI 명령은 올바른 패키지 및 시작 코드 (표시 된 자리 표시자 값)를 포함 하는 새 Blazor 서버 프로젝트를 생성 합니다.

```dotnetcli
dotnet new blazorserver2 --auth SingleOrg --calls-graph --client-id &quot;00000000-0000-0000-0000-000000000000&quot; --tenant-id &quot;11111111-1111-1111-1111-111111111111" --output my-blazor-app
```

#### <a name="github"></a>GitHub

Microsoft Identity Web은 GitHub: [AzureAD/microsoft-Identity-Web](https://github.com/AzureAD/microsoft-identity-web?azure-portal=true) 에서 호스트 되는 오픈 소스 프로젝트입니다.

[리포지토리 wiki](https://github.com/AzureAD/microsoft-identity-web/wiki) 에는 추가 설명서가 포함 되어 있습니다. 도움이 필요 하거나 버그를 검색 하는 경우 [문제](https://github.com/AzureAD/microsoft-identity-web/issues)를 해결할 수 있습니다.

## <a name="features"></a>기능

Microsoft Identity Web에는 기본 ASP.NET 3.1 프로젝트 템플릿을 사용 하는 경우 제공 되지 않는 몇 가지 기능이 포함 되어 있습니다.

| 기능                                                                                  | ASP.NET Core 3.1                                                     | Microsoft ID 웹                                                                                  |
|------------------------------------------------------------------------------------------|----------------------------------------------------------------------|---------------------------------------------------------------------------------------------------------|
| 웹 앱에서 [사용자 로그인](scenario-web-app-sign-user-app-configuration.md)             | <li>회사 또는 학교 계정<li>소셜 id (Azure AD B2C) | <li>회사 또는 학교 계정<li>개인 Microsoft 계정<li>소셜 id (Azure AD B2C)     |
| [웹 Api 보호](scenario-protected-web-api-app-configuration.md#microsoftidentityweb) | <li>회사 또는 학교 계정<li>소셜 id (Azure AD B2C) | <li>회사 또는 학교 계정<li>개인 Microsoft 계정<li>소셜 id (Azure AD B2C)     |
| 다중 테 넌 트 앱의 발급자 유효성 검사                                                   | 예                                                                   | 예, [모든 클라우드](authentication-national-cloud.md) 및 [Azure AD B2C](/azure/active-directory-b2c) |
| 웹 앱/a p i [Microsoft graph 호출] [시나리오-api-호출 그래프]                             | 예                                                                   | 예                                                                                                     |
| 웹 앱/a p i [웹 API 호출] [시나리오-api 호출]                                       | 예                                                                   | 예                                                                                                     |
| 인증서 자격 증명 지원                                                         | 예                                                                   | 예, Azure Key Vault 포함                                                                          |
| 웹 앱의 증분 승인 및 조건부 액세스 지원                           | 예                                                                   | 예, MVC, Razor 페이지 및 Blazor                                                                    |
| 웹 Api의 토큰 암호화 인증서                                                | 예                                                                   | 예                                                                                                     |
| [범위/앱 역할 유효성 검사] [시나리오-api-유효성 검사] 웹 Api                        | 예                                                                   | 예                                                                                                     |
| `WWW-Authenticate` 웹 Api의 헤더 생성                                         | 예                                                                   | 예                                                                                                     |

## <a name="next-steps"></a>다음 단계

작동 중인 Microsoft Identity Web을 보려면 Blazor Server 자습서를 사용해 보세요.

[자습서: 인증을 위해 Microsoft id 플랫폼을 사용 하는 Blazor 서버 앱 만들기](tutorial-blazor-server.md)

GitHub의 Microsoft Identity 웹 wiki에는 라이브러리의 다양 한 기능에 대 한 광범위 한 참조 설명서가 포함 되어 있습니다. 예를 들어 인증서 사용, 증분 승인 및 조건부 액세스 참조는 다음 위치에서 찾을 수 있습니다.

- [Microsoft에서 인증서 사용](https://github.com/AzureAD/microsoft-identity-web/wiki/Using-certificates?azure-portal=true) (GitHub)
- [증분 동의 및 조건부 액세스](https://github.com/AzureAD/microsoft-identity-web/wiki/Managing-incremental-consent-and-conditional-access?azure-portal=true) (GitHub)

<!-- LINKS -->
<!--  [miw-certs]: microsoft-identity-web-certificates.md  -->
<!--  [miw-certs-decrypt]: microsoft-identity-web-certificates.md#decryption-certificates  -->
<!--  [miw-inc-consent-ca-header]: microsoft-identity-web-consent-conditional-access.md#handling-incremental-consent-or-conditional-access-in-web-apis  -->
<!--  [miw-inc-consent-ca]: microsoft-identity-web-consent-conditional-access.md  -->
[시나리오-api 호출]: 시나리오-웹-api-version-api 호출-api. md # 옵션-1--sdk [시나리오-api---------------------------------------.
