---
title: Microsoft Identity Web 인증 라이브러리 개요
titleSuffix: Microsoft identity platform
description: Azure Active Directory, Azure AD B2C, Microsoft Graph 및 기타 웹 API와 통합되는 ASP.NET Core 애플리케이션에 대한 인증 및 권한 부여 라이브러리인 Microsoft Identity Web에 대해 알아봅니다.
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
ms.openlocfilehash: 8bc9773fa7e1b3db2e249e803f4d2a3cf39edb48
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/29/2021
ms.locfileid: "100102549"
---
# <a name="microsoft-identity-web-authentication-library"></a>Microsoft Identity Web 인증 라이브러리

Microsoft Identity Web은 Microsoft ID 플랫폼과 통합되는 웹앱 및 웹 API에 대한 인증 및 권한 부여 지원을 간소화하는 ASP.NET Core 라이브러리 집합입니다. ASP.NET Core, 인증 미들웨어 및 [.NET용 MSAL(Microsoft Authentication Library)](https://github.com/azuread/microsoft-authentication-library-for-dotnet)을 함께 연결하는 단일 Surface API 편의 계층을 제공합니다.

## <a name="supported-application-scenarios"></a>지원되는 애플리케이션 시나리오

ASP.NET Core 웹앱 또는 웹 API를 빌드하고 IAM(ID 및 액세스 관리)에 Azure AD(Azure Active Directory) 또는 Azure AD B2C를 사용하려는 경우 다음과 같은 모든 시나리오에 Microsoft Identity Web을 사용하는 것이 좋습니다.

- [사용자가 로그인하는 웹앱](scenario-web-app-sign-user-overview.md)
- [사용자를 로그인하고 사용자를 대신하여 웹 API를 호출하는 웹앱](scenario-web-app-call-api-overview.md)
- [인증된 사용자만 액세스할 수 있는 보호된 웹 API](scenario-protected-web-api-overview.md)
- [로그인한 사용자를 대신하여 또 다른 다운스트림 웹 API를 호출하는 보호된 웹 API](scenario-web-api-call-api-overview.md)

## <a name="get-the-library"></a>라이브러리 가져오기

Microsoft Identity Web은 [NuGet](#nuget), [.NET Core 프로젝트 템플릿](#project-templates) 및 [GitHub](#github)에서 가져올 수 있습니다.

#### <a name="nuget"></a>NuGet

Microsoft Identity Web은 NuGet에서 앱의 요구에 따라 모듈형 기능을 제공하는 패키지 집합으로 사용할 수 있습니다. .NET CLI의 `dotnet add` 명령 또는 Visual Studio의 **NuGet 패키지 관리자** 를 사용하여 프로젝트에 적합한 패키지를 설치합니다.

- [Microsoft.Identity.Web](https://www.nuget.org/packages/Microsoft.Identity.Web) - 주 패키지입니다. Microsoft Identity Web을 사용하는 모든 앱에 필요합니다.
- [Microsoft.Identity.Web.UI](https://www.nuget.org/packages/Microsoft.Identity.Web.UI) - 선택 사항입니다. 웹앱에 대한 사용자 로그인 및 로그아웃과 연결된 컨트롤러에 대한 UI를 추가합니다.
- [Microsoft.Identity.Web.MicrosoftGraph](https://www.nuget.org/packages/Microsoft.Identity.Web.MicrosoftGraph) - 선택 사항입니다. Microsoft Graph API와의 간소화된 상호 작용을 제공합니다.
- [Microsoft.Identity.Web.MicrosoftGraphBeta](https://www.nuget.org/packages/Microsoft.Identity.Web.MicrosoftGraphBeta) - 선택 사항입니다. Microsoft Graph API [베타 엔드포인트](/graph/api/overview?view=graph-rest-beta&preserve-view=true)와의 간소화된 상호 작용을 제공합니다.

#### <a name="project-templates"></a>프로젝트 템플릿

Microsoft Identity Web 프로젝트 템플릿은 .NET 5.0에 포함되어 있으며 ASP.NET Core 3.1 프로젝트를 다운로드할 수 있습니다.

ASP.NET Core 3.1을 사용하는 경우 .NET CLI를 사용하여 템플릿을 설치합니다.

```dotnetcli
dotnet new --install Microsoft.Identity.Web.ProjectTemplates::1.0.0
```

다음 다이어그램은 지원되는 앱 형식 및 관련 인수에 대한 상위 수준 보기를 보여줍니다.

:::image type="content" source="media/microsoft-identity-web-overview/diagram-microsoft-identity-web-templates.png" lightbox="media/microsoft-identity-web-overview/diagram-microsoft-identity-web-templates.png" alt-text="Microsoft Identity Web에 사용 가능한 .NET CLI 프로젝트 템플릿 다이어그램":::
<br /><sup><b>*</b></sup> `MultiOrg`는 `webapi2`에서 지원되지 않지만 테넌트를 `common` 또는 `organizations`로 설정하여 *appsettings.json* 에서 사용하도록 설정할 수 있습니다.
<br /><sup><b>**</b></sup> `--calls-graph`는 Azure AD B2C에서 지원되지 않습니다.

[Blazor Server 자습서](tutorial-blazor-server.md)에서 가져온 이 .NET CLI 명령 예는 올바른 패키지 및 시작 코드(표시된 자리 표시자 값)를 포함하는 새 Blazor Server 프로젝트를 생성합니다.

```dotnetcli
dotnet new blazorserver2 --auth SingleOrg --calls-graph --client-id "00000000-0000-0000-0000-000000000000" --tenant-id "11111111-1111-1111-1111-111111111111" --output my-blazor-app
```

#### <a name="github"></a>GitHub

Microsoft Identity Web은 GitHub: <a href="https://github.com/AzureAD/microsoft-identity-web" target="_blank">AzureAD/microsoft-identity-web</a>에서 호스트되는 오픈 소스 프로젝트입니다.

[리포지토리 wiki](https://github.com/AzureAD/microsoft-identity-web/wiki)에는 추가 설명서가 포함되어 있습니다. 도움이 필요하거나 버그가 발견된 경우 [문제를 제출](https://github.com/AzureAD/microsoft-identity-web/issues)할 수 있습니다.

## <a name="features"></a>기능

Microsoft Identity Web에는 기본 ASP.NET 3.1 프로젝트 템플릿을 사용하는 경우 제공되지 않는 몇 가지 기능이 포함되어 있습니다.

| 기능                                                                                  | ASP.NET Core 3.1                                                     | Microsoft ID 웹                                                                                  |
|------------------------------------------------------------------------------------------|----------------------------------------------------------------------|---------------------------------------------------------------------------------------------------------|
| 웹앱에서 [사용자 로그인](scenario-web-app-sign-user-app-configuration.md)             | <li>회사 또는 학교 계정<li>소셜 ID(Azure AD B2C) | <li>회사 또는 학교 계정<li>개인 Microsoft 계정<li>소셜 ID(Azure AD B2C)     |
| [웹 API 보호](scenario-protected-web-api-app-configuration.md#microsoftidentityweb) | <li>회사 또는 학교 계정<li>소셜 ID(Azure AD B2C) | <li>회사 또는 학교 계정<li>개인 Microsoft 계정<li>소셜 ID(Azure AD B2C)     |
| 다중 테넌트 앱의 발급자 유효성 검사                                                   | 예                                                                   | 예, [모든 클라우드](authentication-national-cloud.md) 및 [Azure AD B2C](../../active-directory-b2c/index.yml) |
| 웹앱/API[Microsoft Graph 호출][scenario-api-call-graph]                             | 예                                                                   | 예                                                                                                     |
| 웹앱/API[웹 API 호출][scenario-api-call-api]                                       | 예                                                                   | 예                                                                                                     |
| 인증서 자격 증명 지원                                                         | 예                                                                   | 예, Azure Key Vault 포함                                                                          |
| 웹앱의 증분 동의 및 조건부 액세스 지원                           | 예                                                                   | 예, MVC, Razor 페이지 및 Blazor                                                                    |
| 웹 API의 토큰 암호화 인증서                                                | 예                                                                   | 예                                                                                                     |
| 웹 API의 [범위/앱 역할 유효성 검사][scenario-api-validation]                        | 예                                                                   | 예                                                                                                     |
| 웹 API의 `WWW-Authenticate` 헤더 생성                                         | 예                                                                   | 예                                                                                                     |

## <a name="next-steps"></a>다음 단계

작동 중인 Microsoft Identity Web을 보려면 Blazor Server 자습서를 사용해 보세요.

[자습서: 인증을 위해 Microsoft ID 플랫폼을 사용하는 Blazor Server 앱 만들기](tutorial-blazor-server.md)

GitHub의 Microsoft Identity Web wiki에는 라이브러리의 다양한 기능에 대한 광범위한 참조 설명서가 포함되어 있습니다. 예를 들어 인증서 사용, 증분 동의 및 조건부 액세스 참조는 다음 위치에서 확인할 수 있습니다.

- <a href="https://github.com/AzureAD/microsoft-identity-web/wiki/Using-certificates" target="_blank">Microsoft.Identity.Web에서 인증서 사용</a>(GitHub)
- <a href="https://github.com/AzureAD/microsoft-identity-web/wiki/Managing-incremental-consent-and-conditional-access" target="_blank">증분 동의 및 조건부 액세스</a>(GitHub)

<!-- LINKS -->
<!--  [miw-certs]: microsoft-identity-web-certificates.md  -->
<!--  [miw-certs-decrypt]: microsoft-identity-web-certificates.md#decryption-certificates  -->
<!--  [miw-inc-consent-ca-header]: microsoft-identity-web-consent-conditional-access.md#handling-incremental-consent-or-conditional-access-in-web-apis  -->
<!--  [miw-inc-consent-ca]: microsoft-identity-web-consent-conditional-access.md  -->
[scenario-api-call-api]: scenario-web-api-call-api-call-api.md#option-1-call-microsoft-graph-with-the-sdk  
[scenario-api-call-graph]: scenario-web-api-call-api-call-api.md#option-1-call-microsoft-graph-with-the-sdk  
[scenario-api-validation]: scenario-protected-web-api-verification-scope-app-roles.md  
