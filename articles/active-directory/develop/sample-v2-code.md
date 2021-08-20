---
title: Microsoft ID 플랫폼 인증 및 권한 부여 코드 샘플
description: 여러 애플리케이션 유형, 개발 언어, 프레임워크에서의 인증 및 권한 부여를 보여 주고 Microsoft에서 유지 관리하는 코드 샘플의 인덱스입니다.
services: active-directory
author: mmacy
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: sample
ms.workload: identity
ms.date: 07/06/2021
ms.author: marsma
ms.reviewer: jmprieur
ms.custom: aaddev, identityplatformtop40
ms.openlocfilehash: df892abcc28560e84d6708b794180c0b9182991d
ms.sourcegitcommit: 92dd25772f209d7d3f34582ccb8985e1a099fe62
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/15/2021
ms.locfileid: "114228191"
---
# <a name="microsoft-identity-platform-code-samples"></a>Microsoft ID 플랫폼 코드 샘플

Microsoft에서 빌드하고 유지 관리하는 이 코드 샘플은 여러 [애플리케이션 유형](v2-app-types.md), 개발 언어, 프레임워크에서 Azure AD와 Microsoft ID 플랫폼을 사용하여 인증 및 권한 부여를 보여 줍니다.

- 사용자를 웹 애플리케이션에 로그인하고 보호된 웹 API에 대한 권한 있는 액세스를 제공합니다.
- API 작업 수행 시 액세스 토큰을 요구하여 웹 API를 보호합니다.

각 코드 샘플에는 프로젝트를 빌드하고(해당하는 경우) 샘플 애플리케이션을 실행하는 방법을 설명하는 _README.md_ 파일이 포함되어 있습니다. 코드의 주석은 인증 라이브러리와 ID 플랫폼을 사용하여 인증 및 권한 부여를 구현하는 중요한 섹션을 이해하는 데 도움이 됩니다.

## <a name="single-page-applications"></a>단일 페이지 애플리케이션

이 샘플에서는 Microsoft ID 플랫폼으로 보안이 설정된 단일 페이지 애플리케이션을 작성하는 방법을 보여 줍니다. 이 샘플은 MSAL.js의 특징 중 하나를 사용합니다.

> [!div class="mx-tdCol2BreakAll"]
> | 언어/<br/>플랫폼 | 코드 샘플 | 설명 | 인증 라이브러리 | 인증 흐름 |  
> | ------- | -------- | --------------------------- | ------------- | -------------- |  
> | Angular | [GitHub 리포지토리](https://github.com/Azure-Samples/ms-identity-javascript-angular-tutorial) | &#8226; [사용자 로그인](https://github.com/Azure-Samples/ms-identity-javascript-angular-tutorial/tree/main/1-Authentication/1-sign-in/README.md)<br/>&#8226; [사용자 로그인(B2C)](https://github.com/Azure-Samples/ms-identity-javascript-angular-tutorial/tree/main/1-Authentication/2-sign-in-b2c/README.md) <br/> &#8226; [Microsoft Graph 호출](https://github.com/Azure-Samples/ms-identity-javascript-angular-tutorial/tree/main/2-Authorization-I/1-call-graph/README.md)<br/>&#8226; [.NET Core 웹 API 호출](https://github.com/Azure-Samples/ms-identity-javascript-angular-tutorial/tree/main/3-Authorization-II/1-call-api)<br/>&#8226; [.NET Core 웹 API 호출(B2C)](https://github.com/Azure-Samples/ms-identity-javascript-angular-tutorial/tree/main/3-Authorization-II/2-call-api-b2c)<br/>&#8226; [OBO를 통해 Microsoft Graph 호출](https://github.com/Azure-Samples/ms-identity-javascript-angular-tutorial/tree/main/7-AdvancedScenarios/1-call-api-obo/README.md)<br/>&#8226; [PoP를 사용하여 .NET Core 웹 API 호출](https://github.com/Azure-Samples/ms-identity-javascript-angular-tutorial/tree/main/7-AdvancedScenarios/2-call-api-pop/README.md)<br/>&#8226; [액세스 제어를 위해 앱 역할 사용](https://github.com/Azure-Samples/ms-identity-javascript-angular-tutorial/tree/main/5-AccessControl/1-call-api-roles/README.md)<br/>&#8226; [액세스 제어를 위해 보안 그룹 사용](https://github.com/Azure-Samples/ms-identity-javascript-angular-tutorial/tree/main/5-AccessControl/2-call-api-groups/README.md)<br/>&#8226; [Azure Storage 및 App Service에 배포](https://github.com/Azure-Samples/ms-identity-javascript-angular-tutorial/tree/main/4-Deployment/README.md)| MSAL Angular | &#8226; 인증 코드 흐름(PKCE 사용)<br/>&#8226; OBO(On-Behalf-Of) 흐름<br/>&#8226; PoP(소유 증명)|
> | Blazor WebAssembly | [GitHub 리포지토리](https://github.com/Azure-Samples/ms-identity-blazor-wasm) | &#8226; [사용자 로그인](https://github.com/Azure-Samples/ms-identity-blazor-wasm/blob/main/WebApp-OIDC/MyOrg/README.md)<br/>&#8226; [사용자 로그인(B2C)](https://github.com/Azure-Samples/ms-identity-blazor-wasm/blob/main/WebApp-OIDC/B2C/README.md)<br/>&#8226; [Microsoft Graph 호출](https://github.com/Azure-Samples/ms-identity-blazor-wasm/blob/main/WebApp-graph-user/Call-MSGraph/README.md)<br/>&#8226; [Azure App Service에 배포](https://github.com/Azure-Samples/ms-identity-blazor-wasm/blob/main/Deploy-to-Azure/README.md) | MSAL.js | 인증 코드 흐름(PKCE 사용) |
> | JavaScript | [GitHub 리포지토리](https://github.com/Azure-Samples/ms-identity-javascript-tutorial) | &#8226; [사용자 로그인](https://github.com/Azure-Samples/ms-identity-javascript-tutorial/tree/main/1-Authentication/1-sign-in/README.md)<br/>&#8226; [사용자 로그인(B2C)](https://github.com/Azure-Samples/ms-identity-javascript-tutorial/tree/main/1-Authentication/2-sign-in-b2c/README.md) <br/> &#8226; [Microsoft Graph 호출](https://github.com/Azure-Samples/ms-identity-javascript-tutorial/tree/main/2-Authorization-I/1-call-graph/README.md)<br/>&#8226; [Node.js 웹 API 호출](https://github.com/Azure-Samples/ms-identity-javascript-tutorial/tree/main/3-Authorization-II/1-call-api/README.md)<br/>&#8226; [Node.js 웹 API 호출(B2C)](https://github.com/Azure-Samples/ms-identity-javascript-tutorial/tree/main/3-Authorization-II/2-call-api-b2c/README.md)<br/>&#8226; [OBO를 통해 Microsoft Graph 호출](https://github.com/Azure-Samples/ms-identity-javascript-tutorial/tree/main/4-AdvancedGrants/1-call-api-graph/README.md)<br/>&#8226; [OBO 및 CA를 통해 Node.js 웹 API 호출](https://github.com/Azure-Samples/ms-identity-javascript-tutorial/tree/main/4-AdvancedGrants/2-call-api-api-ca/README.md)<br/>&#8226; [Azure Storage 및 App Service에 배포](https://github.com/Azure-Samples/ms-identity-javascript-tutorial/tree/main/5-Deployment/README.md)| MSAL.js | &#8226; 인증 코드 흐름(PKCE 사용)<br/>&#8226; OBO(On-Behalf-Of) 흐름<br/>&#8226; CA(조건부 액세스.) |
> | React | [GitHub 리포지토리](https://github.com/Azure-Samples/ms-identity-javascript-react-tutorial) | &#8226; [사용자 로그인](https://github.com/Azure-Samples/ms-identity-javascript-react-tutorial/tree/main/1-Authentication/1-sign-in/README.md)<br/>&#8226; [사용자 로그인(B2C)](https://github.com/Azure-Samples/ms-identity-javascript-react-tutorial/tree/main/1-Authentication/2-sign-in-b2c/README.md) <br/> &#8226; [Microsoft Graph 호출](https://github.com/Azure-Samples/ms-identity-javascript-react-tutorial/tree/main/2-Authorization-I/1-call-graph/README.md)<br/>&#8226; [Node.js 웹 API 호출](https://github.com/Azure-Samples/ms-identity-javascript-react-tutorial/tree/main/3-Authorization-II/1-call-api)<br/>&#8226; [Node.js 웹 API 호출(B2C)](https://github.com/Azure-Samples/ms-identity-javascript-react-tutorial/tree/main/3-Authorization-II/2-call-api-b2c)<br/>&#8226; [OBO를 통해 Microsoft Graph 호출](https://github.com/Azure-Samples/ms-identity-javascript-react-tutorial/tree/main/6-AdvancedScenarios/1-call-api-obo/README.md)<br/>&#8226; [PoP를 사용하여 Node.js 웹 API 호출](https://github.com/Azure-Samples/ms-identity-javascript-react-tutorial/tree/main/6-AdvancedScenarios/2-call-api-pop/README.md)<br/>&#8226; [액세스 제어를 위해 앱 역할 사용](https://github.com/Azure-Samples/ms-identity-javascript-react-tutorial/tree/main/5-AccessControl/1-call-api-roles/README.md)<br/>&#8226; [액세스 제어를 위해 보안 그룹 사용](https://github.com/Azure-Samples/ms-identity-javascript-react-tutorial/tree/main/5-AccessControl/2-call-api-groups/README.md)<br/>&#8226; [Azure Storage 및 App Service에 배포](https://github.com/Azure-Samples/ms-identity-javascript-react-tutorial/tree/main/4-Deployment/1-deploy-storage/README.md)<br/>&#8226; [Azure Static Web Apps에 배포](https://github.com/Azure-Samples/ms-identity-javascript-react-tutorial/tree/main/4-Deployment/2-deploy-static/README.md)| MSAL 반응  | &#8226; 인증 코드 흐름(PKCE 사용)<br/>&#8226; OBO(On-Behalf-Of) 흐름<br/>&#8226; CA(조건부 액세스.)<br/>&#8226; PoP(소유 증명) |

## <a name="web-applications"></a>웹 애플리케이션

다음 샘플은 사용자를 로그인하는 웹 애플리케이션을 보여 줍니다. 일부 샘플은 사용자 ID를 사용하여 Microsoft Graph 또는 사용자 고유의 웹 API를 호출하는 애플리케이션도 보여 줍니다.

> [!div class="mx-tdCol2BreakAll"]
> | 언어/<br/>플랫폼 | 코드 샘플<br/>GitHub | Description | 사용되는 인증 라이브러리 | 인증 흐름 |
> | ------- | -------- | --------------------------- | ------------- | -------------- |
> | ASP.NET Core|[GitHub 리포지토리](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2) | ASP.NET Core 시리즈 <br/> &#8226; [사용자 로그인](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/blob/master/1-WebApp-OIDC/README.md) <br/> &#8226; [사용자 로그인(B2C)](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/blob/master/1-WebApp-OIDC/1-5-B2C/README.md) <br/> &#8226; [Microsoft Graph 호출](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/blob/master/2-WebApp-graph-user/2-1-Call-MSGraph/README.md) <br/> &#8226; [토큰 캐시 사용자 지정](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/blob/master/2-WebApp-graph-user/2-2-TokenCache/README.md) <br/> &#8226; [Graph 호출(다중 테넌트)](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/blob/master/2-WebApp-graph-user/2-3-Multi-Tenant/README.md) <br/> &#8226; [Azure REST API 호출](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/blob/master/3-WebApp-multi-APIs/README.md) <br/> &#8226; [웹 API 보호](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/blob/master/4-WebApp-your-API/4-1-MyOrg/README.md) <br/> &#8226; [웹 API 보호(B2C)](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/blob/master/4-WebApp-your-API/4-2-B2C/README.md) <br/> &#8226; [다중 테넌트 웹 API 보호](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/blob/master/4-WebApp-your-API/4-3-AnyOrg/Readme.md) <br/> &#8226; [액세스 제어를 위해 앱 역할 사용](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/blob/master/5-WebApp-AuthZ/5-1-Roles/README.md) <br/> &#8226; [액세스 제어를 위해 보안 그룹 사용](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/blob/master/5-WebApp-AuthZ/5-2-Groups/README.md) <br/> &#8226; [Azure Storage 및 App Service에 배포](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/blob/master/6-Deploy-to-Azure/README.md) | &#8226; [MSAL.NET](https://aka.ms/msal-net) <br/> &#8226; [Microsoft.Identity.Web](https://aka.ms/microsoft-identity-web) | &#8226; [OIDC 흐름](./v2-protocols-oidc.md) <br/> &#8226; [인증 코드 흐름](./v2-oauth2-auth-code-flow.md) <br/> &#8226; [OBO(On-Behalf-Of) 흐름](./v2-oauth2-on-behalf-of-flow.md) |
> | Blazor | [GitHub 리포지토리](https://github.com/Azure-Samples/ms-identity-blazor-server/) | Blazor Server 시리즈 <br/> &#8226; [사용자 로그인](https://github.com/Azure-Samples/ms-identity-blazor-server/tree/main/WebApp-OIDC/MyOrg) <br/> &#8226; [사용자 로그인(B2C)](https://github.com/Azure-Samples/ms-identity-blazor-server/tree/main/WebApp-OIDC/B2C) <br/> &#8226; [Microsoft Graph 호출](https://github.com/Azure-Samples/ms-identity-blazor-server/tree/main/WebApp-graph-user/Call-MSGraph) <br/> &#8226; [웹 API 호출](https://github.com/Azure-Samples/ms-identity-blazor-server/tree/main/WebApp-your-API/MyOrg) <br/> &#8226; [웹 API 호출(B2C)](https://github.com/Azure-Samples/ms-identity-blazor-server/tree/main/WebApp-your-API/B2C) | MSAL.NET | |
> | ASP.NET Core|[GitHub 리포지토리](https://github.com/Azure-Samples/ms-identity-dotnet-advanced-token-cache) | [고급 토큰 캐시 시나리오](https://github.com/Azure-Samples/ms-identity-dotnet-advanced-token-cache) | &#8226; [MSAL.NET](https://aka.ms/msal-net) <br/> &#8226; [Microsoft.Identity.Web](https://aka.ms/microsoft-identity-web) | [OBO(On-Behalf-Of) 흐름](./v2-oauth2-on-behalf-of-flow.md) |
> | ASP.NET Core|[GitHub 리포지토리](https://github.com/Azure-Samples/ms-identity-dotnetcore-ca-auth-context-app/blob/main/README.md) | [조건부 액세스 인증 컨텍스트를 사용하여 버전 업그레이드 인증 수행](https://github.com/Azure-Samples/ms-identity-dotnetcore-ca-auth-context-app/blob/main/README.md) | &#8226; [MSAL.NET](https://aka.ms/msal-net) <br/> &#8226; [Microsoft.Identity.Web](https://aka.ms/microsoft-identity-web) | [인증 코드 흐름](./v2-oauth2-auth-code-flow.md) |
> | ASP.NET Core|[GitHub 리포지토리](https://github.com/Azure-Samples/ms-identity-dotnet-adfs-to-aad) | [Active Directory FS에서 Azure AD로 마이그레이션](https://github.com/Azure-Samples/ms-identity-dotnet-adfs-to-aad) | [MSAL.NET](https://aka.ms/msal-net) |  |
> | ASP.NET |[GitHub 리포지토리](https://github.com/AzureAdQuickstarts/AppModelv2-WebApp-OpenIDConnect-DotNet) | [빠른 시작: 사용자 로그인](https://github.com/AzureAdQuickstarts/AppModelv2-WebApp-OpenIDConnect-DotNet) | [MSAL.NET](https://aka.ms/msal-net) |  |
> | ASP.NET |[GitHub 리포지토리](https://github.com/Azure-Samples/ms-identity-aspnet-webapp-openidconnect) | [사용자 로그인 및 Microsoft Graph 호출](https://github.com/Azure-Samples/ms-identity-aspnet-webapp-openidconnect) | [MSAL.NET](https://aka.ms/msal-net) |  |
> | ASP.NET |[GitHub 리포지토리](https://github.com/azure-samples/active-directory-dotnet-admin-restricted-scopes-v2) | [관리자 제한 범위 <br/> &#8226; 사용자 로그인 <br/> &#8226; Microsoft Graph 호출](https://github.com/azure-samples/active-directory-dotnet-admin-restricted-scopes-v2) | [MSAL.NET](https://aka.ms/msal-net) |  |
> | ASP.NET |[GitHub 리포지토리](https://github.com/microsoftgraph/msgraph-training-aspnetmvcapp) | Microsoft Graph 학습 샘플 | [MSAL.NET](https://aka.ms/msal-net) |  |
> | Java </p> Spring |[GitHub 리포지토리](https://github.com/Azure-Samples/ms-identity-java-spring-tutorial) | Azure AD Spring Boot Starter 시리즈 <br/> &#8226; [사용자 로그인](https://github.com/Azure-Samples/ms-identity-java-spring-tutorial/tree/main/1-Authentication/sign-in) <br/> &#8226; [사용자 로그인(B2C)](https://github.com/Azure-Samples/ms-identity-java-spring-tutorial/tree/main/1-Authentication/sign-in-b2c) <br/> &#8226; [Microsoft Graph 호출](https://github.com/Azure-Samples/ms-identity-java-spring-tutorial/tree/main/2-Authorization-I/call-graph) <br/> &#8226; [액세스 제어를 위해 앱 역할 사용](https://github.com/Azure-Samples/ms-identity-java-spring-tutorial/tree/main/3-Authorization-II/roles) <br/> &#8226; [액세스 제어를 위해 그룹 사용](https://github.com/Azure-Samples/ms-identity-java-spring-tutorial/tree/main/3-Authorization-II/groups) <br/> &#8226; [Azure App Service에 배포](https://github.com/Azure-Samples/ms-identity-java-spring-tutorial/tree/main/4-Deployment/deploy-to-azure-app-service) | MSAL Java <br/> AAD Boot Starter | [인증 코드 흐름](./v2-oauth2-auth-code-flow.md) |
> | Java </p> 서블릿 |[GitHub 리포지토리](https://github.com/Azure-Samples/ms-identity-java-servlet-webapp-authentication) | Spring 제외 서블릿 시리즈 <br/> &#8226; [사용자 로그인](https://github.com/Azure-Samples/ms-identity-java-servlet-webapp-authentication/tree/main/1-Authentication/sign-in) <br/> &#8226; [사용자 로그인(B2C)](https://github.com/Azure-Samples/ms-identity-java-servlet-webapp-authentication/tree/main/1-Authentication/sign-in-b2c) <br/> &#8226; [Microsoft Graph 호출](https://github.com/Azure-Samples/ms-identity-java-servlet-webapp-authentication/tree/main/2-Authorization-I/call-graph) <br/> &#8226; [액세스 제어를 위해 앱 역할 사용](https://github.com/Azure-Samples/ms-identity-java-servlet-webapp-authentication/tree/main/3-Authorization-II/roles) <br/> &#8226; [액세스 제어를 위해 보안 그룹 사용](https://github.com/Azure-Samples/ms-identity-java-servlet-webapp-authentication/tree/main/3-Authorization-II/groups) <br/> &#8226; [Azure App Service에 배포](https://github.com/Azure-Samples/ms-identity-java-servlet-webapp-authentication/tree/main/4-Deployment/deploy-to-azure-app-service) | MSAL Java | [인증 코드 흐름](./v2-oauth2-auth-code-flow.md) |
> | Java |[GitHub 리포지토리](https://github.com/Azure-Samples/ms-identity-java-webapp) | 사용자 로그인, Microsoft Graph 호출 | MSAL Java | [인증 코드 흐름](./v2-oauth2-auth-code-flow.md) |
> | Java </p> Spring|[GitHub 리포지토리](https://github.com/Azure-Samples/ms-identity-java-webapi) | 사용자 로그인 및 OBO를 통해 Microsoft Graph 호출 </p> &#8226; 웹 API | MSAL Java | &#8226; [인증 코드 흐름](./v2-oauth2-auth-code-flow.md) <br/> &#8226; [OBO(On-Behalf-Of) 흐름](./v2-oauth2-on-behalf-of-flow.md) |
> | Node.js </p> Express |[GitHub 리포지토리](https://github.com/Azure-Samples/ms-identity-node) | Express 웹앱 샘플 <br/> &#8226; 사용자 로그인 | MSAL 노드 | [인증 코드 흐름](./v2-oauth2-auth-code-flow.md) |
> | Node.js </p> Express |[GitHub 리포지토리](https://github.com/Azure-Samples/ms-identity-node) | Express 웹앱 시리즈 <br/> &#8226; [사용자 로그인](https://github.com/Azure-Samples/ms-identity-javascript-nodejs-tutorial/blob/main/1-Authentication/1-sign-in/README.md)<br/> &#8226; [사용자 로그인(B2C)](https://github.com/Azure-Samples/ms-identity-javascript-nodejs-tutorial/blob/main/1-Authentication/2-sign-in-b2c/README.md)<br/> &#8226; [Microsoft Graph 호출](https://github.com/Azure-Samples/ms-identity-javascript-nodejs-tutorial/blob/main/2-Authorization/1-call-graph/README.md)<br/> &#8226; [Azure App Service에 배포](https://github.com/Azure-Samples/ms-identity-javascript-nodejs-tutorial/blob/main/3-Deployment/README.md)<br/> &#8226; [액세스 제어를 위해 앱 역할 사용](https://github.com/Azure-Samples/ms-identity-javascript-nodejs-tutorial/blob/main/4-AccessControl/1-app-roles/README.md)<br/> &#8226; [액세스 제어를 위해 보안 그룹 사용](https://github.com/Azure-Samples/ms-identity-javascript-nodejs-tutorial/blob/main/4-AccessControl/2-security-groups/README.md) | MSAL 노드 | [인증 코드 흐름](./v2-oauth2-auth-code-flow.md) |
> | Python </p> Flask |[GitHub 리포지토리](https://github.com/Azure-Samples/ms-identity-python-flask-tutorial) | Flask 시리즈 <br/> &#8226; 사용자 로그인 <br/> &#8226; 사용자 로그인(B2C) <br/> &#8226; Microsoft Graph 호출 <br/> &#8226; Azure App Service에 배포 | MSAL Python | [인증 코드 흐름](./v2-oauth2-auth-code-flow.md) |
> | Python </p> Django |[GitHub 리포지토리](https://github.com/Azure-Samples/ms-identity-python-django-tutorial) | Django 시리즈 <br/> &#8226; [사용자 로그인](https://github.com/Azure-Samples/ms-identity-python-django-tutorial/tree/main/1-Authentication/sign-in) <br/> &#8226; [사용자 로그인(B2C)](https://github.com/Azure-Samples/ms-identity-python-django-tutorial/tree/main/1-Authentication/sign-in-b2c) <br/> &#8226; [Microsoft Graph 호출](https://github.com/Azure-Samples/ms-identity-python-django-tutorial/tree/main/2-Authorization-I/call-graph) <br/> &#8226; [Azure App Service에 배포](https://github.com/Azure-Samples/ms-identity-python-django-tutorial/tree/main/3-Deployment/deploy-to-azure-app-service)| MSAL Python | [인증 코드 흐름](./v2-oauth2-auth-code-flow.md) |
> | Python </p> Flask |[GitHub 리포지토리](https://github.com/Azure-Samples/ms-identity-python-webapp) | Flask 독립 실행형 샘플 <br/> [사용자 로그인 및 Microsoft Graph 호출](https://github.com/Azure-Samples/ms-identity-python-webapp) | MSAL Python | [인증 코드 흐름](./v2-oauth2-auth-code-flow.md) |
> | Ruby |[GitHub 리포지토리](https://github.com/microsoftgraph/msgraph-training-rubyrailsapp) | Graph 학습 <br/> &#8226; [로그인 및 Microsoft Graph](https://github.com/microsoftgraph/msgraph-training-rubyrailsapp) | | |

## <a name="desktop-and-mobile-public-client-apps"></a>데스크톱 및 모바일 공용 클라이언트 앱

다음 샘플은 사용자 이름으로 Microsoft Graph API 또는 사용자 고유의 Web API에 액세스하는 퍼블릭 클라이언트 애플리케이션(데스크톱/모바일 애플리케이션)을 보여 줍니다. *WAM을 사용하는 데스크톱(콘솔)* 샘플 외에도 이러한 클라이언트 애플리케이션은 모두 MSAL(Microsoft 인증 라이브러리)을 사용합니다.

| 클라이언트 애플리케이션 | 플랫폼 | 흐름/권한 부여 | Microsoft Graph 호출 | ASP.NET Core Web API 호출 |
| ------------------ | -------- |  ----------| ---------- | ------------------------- |
| 바탕 화면 자습서(.NET Core) - 필요에 따라 다음을 사용합니다.</p>- 플랫폼 간 토큰 캐시</p>- 사용자 지정 웹 UI | ![이 이미지는 .NET/C# 로고를 보여 줍니다.](media/sample-v2-code/logo_NETcore.png) | [인증 코드](msal-authentication-flows.md#authorization-code)| [ms-identity-dotnet-desktop-tutorial](https://github.com/azure-samples/ms-identity-dotnet-desktop-tutorial) | |
| 데스크톱(WPF)      | ![이 이미지는 .NET 데스크톱/C# 로고를 보여줍니다.](media/sample-v2-code/logo_NET.png) | [인증 코드](msal-authentication-flows.md#authorization-code)| [dotnet-desktop-msgraph-v2](https://github.com/azure-samples/active-directory-dotnet-desktop-msgraph-v2) | [dotnet-native-aspnetcore-v2](https://aka.ms/msidentity-aspnetcore-webapi) |
| 데스크톱(콘솔)   | ![.NET/C#(데스크톱) 로고를 보여주는 이미지](media/sample-v2-code/logo_NET.png) | [Windows 통합 인증](msal-authentication-flows.md#integrated-windows-authentication) | [dotnet-iwa-v2](https://github.com/azure-samples/active-directory-dotnet-iwa-v2) |  |
| 데스크톱(콘솔) | ![.NET/C#(데스크톱) 로고를 보여주는 이미지](media/sample-v2-code/logo_NET.png) |  [인증 코드](msal-authentication-flows.md#authorization-code) | [active-directory-dotnetcore-daemon-v2](https://github.com/Azure-Samples/active-directory-dotnetcore-daemon-v2/tree/master/1-Call-MSGraph) |[active-directory-dotnetcore-daemon-v2](https://github.com/Azure-Samples/active-directory-dotnetcore-daemon-v2/blob/master/2-Call-OwnApi/README.md)  |
| 데스크톱(콘솔) <br> 비밀 대신 인증서 사용 | ![.NET/C#(데스크톱) 로고를 보여주는 이미지](media/sample-v2-code/logo_NET.png) |  [인증 코드](msal-authentication-flows.md#authorization-code) | [active-directory-dotnetcore-daemon-v2](https://github.com/Azure-Samples/active-directory-dotnetcore-daemon-v2/tree/master/1-Call-MSGraph#variation-daemon-application-using-client-credentials-with-certificates) |[active-directory-dotnetcore-daemon-v2](https://github.com/Azure-Samples/active-directory-dotnetcore-daemon-v2/tree/master/2-Call-OwnApi#variation-daemon-application-using-client-credentials-with-certificates)  |
| 데스크톱(콘솔)   | ![이 이미지는 Java 로고를 표시](media/sample-v2-code/logo_java.png) | [Windows 통합 인증](msal-authentication-flows.md#integrated-windows-authentication) |[ms-identity-java-desktop](https://github.com/Azure-Samples/ms-identity-java-desktop/) |  |
| 데스크톱(콘솔)   | ![.NET/C#(데스크톱) 로고입니다.](media/sample-v2-code/logo_NETcore.png) | [사용자 이름/암호](msal-authentication-flows.md#usernamepassword) |[dotnetcore-up-v2](https://github.com/azure-samples/active-directory-dotnetcore-console-up-v2) |  |
| WAM을 사용하는 데스크톱(콘솔)  | ![.NET/C#(데스크톱)의 로고입니다.](media/sample-v2-code/logo_NETcore.png) | [웹 계정 관리자](/windows/uwp/security/web-account-manager)(WAM)를 사용하는 대화형 |[dotnet-native-uwp-wam](https://github.com/azure-samples/active-directory-dotnet-native-uwp-wam) |  |
| 데스크톱(콘솔)   | ![이 이미지는 Java 로고를 표시](media/sample-v2-code/logo_java.png) | [사용자 이름/암호](msal-authentication-flows.md#usernamepassword) |[ms-identity-java-desktop](https://github.com/Azure-Samples/ms-identity-java-desktop/) |  |
| 데스크톱(콘솔)   | ![이 이미지는 Python 로고를 표시](media/sample-v2-code/logo_python.png) | [사용자 이름/암호](msal-authentication-flows.md#usernamepassword) |[ms-identity-python-desktop](https://github.com/Azure-Samples/ms-identity-python-desktop) |  |
| 데스크톱(전자)   | ![이 이미지는 Node.js 로고를 표시](media/sample-v2-code/logo_nodejs.png)</p>Node.js(MSAL 노드) | [인증 코드(PKCE)](msal-authentication-flows.md#authorization-code) |[ms-identity-javascript-nodejs-desktop](https://github.com/Azure-Samples/ms-identity-javascript-nodejs-desktop) |  |
| 모바일(Android, iOS, UWP)   | ![이 이미지는 .NET/C#(Xamarin) 로고를 표시](media/sample-v2-code/logo_xamarin.png) | [인증 코드](msal-authentication-flows.md#authorization-code) |[xamarin-native-v2](https://github.com/azure-samples/active-directory-xamarin-native-v2) |  |
| 모바일(iOS)       | ![이 이미지는 iOS/Objective-C 또는 Swift를 표시](media/sample-v2-code/logo_iOS.png) | [인증 코드](msal-authentication-flows.md#authorization-code) |[ios-swift-objc-native-v2](https://github.com/azure-samples/active-directory-ios-swift-native-v2) </p> [ios-native-nxoauth2-v2](https://github.com/azure-samples/active-directory-ios-native-nxoauth2-v2) |  |
| 데스크톱(macOS)       | macOS | [인증 코드](msal-authentication-flows.md#authorization-code) |[macOS-swift-objc-native-v2](https://github.com/Azure-Samples/ms-identity-macOS-swift-objc) |  |
| 모바일(Android-Java)   | ![이 이미지는 Android 로고를 보여 줍니다.](media/sample-v2-code/logo_Android.png) | [인증 코드](msal-authentication-flows.md#authorization-code) |  [android-Java](https://github.com/Azure-Samples/ms-identity-android-java) |  |
| 모바일(Android-Kotlin)   | ![이 이미지는 Android 로고를 보여 줍니다.](media/sample-v2-code/logo_Android.png) | [인증 코드](msal-authentication-flows.md#authorization-code) |  [android-Kotlin](https://github.com/Azure-Samples/ms-identity-android-kotlin) |  |

## <a name="daemon-applications"></a>디먼 애플리케이션

다음 샘플은 고유 ID(사용자 없음)로 Microsoft Graph API에 액세스하는 애플리케이션을 보여 줍니다.

| 클라이언트 애플리케이션 | 플랫폼 | 흐름/권한 부여 | Microsoft Graph 호출 |
| ------------------ | -------- | ---------- | -------------------- |
| 콘솔 | ![이 이미지는 .NET Core 로고를 표시](media/sample-v2-code/logo_NETcore.png)</p> ASP.NET  | [클라이언트 자격 증명](msal-authentication-flows.md#client-credentials) | [dotnetcore-daemon-v2](https://github.com/azure-samples/active-directory-dotnetcore-daemon-v2) |
| 웹앱 | ![ASP.NET 로고를 보여주는 스크린샷.](media/sample-v2-code/logo_NETframework.png)</p> ASP.NET  | [클라이언트 자격 증명](msal-authentication-flows.md#client-credentials) | [dotnet-daemon-v2](https://github.com/azure-samples/active-directory-dotnet-daemon-v2) |
| 콘솔 | ![이 이미지는 Java 로고를 표시](media/sample-v2-code/logo_java.png) | [클라이언트 자격 증명](msal-authentication-flows.md#client-credentials) | [ms-identity-java-daemon](https://github.com/Azure-Samples/ms-identity-java-daemon) |
| 콘솔 | ![이 이미지는 Node.js 로고를 표시](media/sample-v2-code/logo_nodejs.png)</p>Node.js(MSAL 노드)| [클라이언트 자격 증명](msal-authentication-flows.md#client-credentials) | [ms-identity-javascript-nodejs-console](https://github.com/Azure-Samples/ms-identity-javascript-nodejs-console) |
| 콘솔 | ![이 이미지는 Python 로고를 표시](media/sample-v2-code/logo_python.png) | [클라이언트 자격 증명](msal-authentication-flows.md#client-credentials) | [ms-identity-python-daemon](https://github.com/Azure-Samples/ms-identity-python-daemon) |

## <a name="headless-applications"></a>헤드리스 애플리케이션

다음 샘플은 웹 브라우저가 없는 디바이스에서 실행되는 공용 클라이언트 애플리케이션을 보여 줍니다. 앱은 명령줄 도구이거나 Linux나 Mac에서 실행되는 앱 또는 IoT 애플리케이션일 수 있습니다. 샘플에는 다른 디바이스(예: 휴대폰)에서 대화형으로 로그인하는 사용자 이름으로 Microsoft Graph API에 액세스하는 앱이 있습니다. 이 클라이언트 애플리케이션은 MSAL(Microsoft 인증 라이브러리)을 사용합니다.

| 클라이언트 애플리케이션 | 플랫폼 | 흐름/권한 부여 | Microsoft Graph 호출 |
| ------------------ | -------- |  ----------| ---------- |
| 데스크톱(콘솔)   | ![이 이미지는 .NET/C#(데스크톱) 로고를 표시](media/sample-v2-code/logo_NETcore.png) | [디바이스 코드 흐름](msal-authentication-flows.md#device-code) |[dotnetcore-devicecodeflow-v2](https://github.com/azure-samples/active-directory-dotnetcore-devicecodeflow-v2) |
| 데스크톱(콘솔)   | ![이 이미지는 Java 로고를 표시](media/sample-v2-code/logo_java.png) | [디바이스 코드 흐름](msal-authentication-flows.md#device-code) |[ms-identity-java-devicecodeflow](https://github.com/Azure-Samples/ms-identity-java-devicecodeflow) |
| 데스크톱(콘솔)   | ![이 이미지는 Python 로고를 표시](media/sample-v2-code/logo_python.png) | [디바이스 코드 흐름](msal-authentication-flows.md#device-code) |[ms-identity-python-devicecodeflow](https://github.com/Azure-Samples/ms-identity-python-devicecodeflow) |

## <a name="multi-tenant-saas-applications"></a>다중 테넌트 SaaS 애플리케이션

다음 샘플에서는 모든 Azure AD(Azure Active Directory) 테넌트에서 로그인을 허용하도록 애플리케이션을 구성하는 방법을 보여 줍니다. *다중 테넌트* 가 되도록 애플리케이션을 구성하면 **SaaS(Software as a Service)** 애플리케이션을 많은 조직에 제공할 수 있으므로 해당 동의한 후에 애플리케이션에 로그인할 수 있습니다.

| 플랫폼 | Description | 링크 |
| -------- | --------------------- | -------- |
| ![Angular 로고를 보여 주는 이미지](media/sample-v2-code/logo_angular.png) [Angular(MSAL Angular 2.0)](https://github.com/AzureAD/microsoft-authentication-library-for-js/blob/dev/lib/msal-angular) | 다중 테넌트 SPA에서 다중 테넌트 사용자 지정 웹 API 호출 |[ms-identity-javascript-angular-spa-aspnet-webapi-multitenant](https://github.com/Azure-Samples/ms-identity-javascript-angular-spa-aspnet-webapi-multitenant/tree/master/Chapter2) |
| ![ASP.NET Core 로고를 보여 주는 이미지](media/sample-v2-code/logo_NETcore.png) [.NET Core(MSAL.NET)](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet) | ASP.NET Core MVC 웹 애플리케이션 호출 Graph API |[active-directory-aspnetcore-webapp-openidconnect-v2](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/tree/master/2-WebApp-graph-user/2-3-Multi-Tenant) |
| ![ASP.NET Core 로고를 보여 주는 이미지](media/sample-v2-code/logo_NETcore.png) [.NET Core(MSAL.NET)](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet) | ASP.NET Core MVC 웹 애플리케이션 호출 ASP.NET Core API |[active-directory-aspnetcore-webapp-openidconnect-v2](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/tree/master/4-WebApp-your-API/4-3-AnyOrg) |

## <a name="web-apis"></a>Web API

다음 샘플에서는 Microsoft ID 플랫폼을 사용하여 웹 API를 보호하는 방법과 웹 API에서 다운스트림 API를 호출하는 방법을 보여줍니다.

| 플랫폼 | 샘플 |
| -------- | ------------------- |
| ![이 이미지는 ASP.NET Core 로고를 표시](media/sample-v2-code/logo_NETcore.png)</p>ASP.NET Core | [dotnet-native-aspnetcore-v2](https://aka.ms/msidentity-aspnetcore-webapi-calls-msgraph)의 ASP.NET Core Web API(서비스)  |
| ![이 이미지는 ASP.NET 로고를 표시](media/sample-v2-code/logo_NET.png)</p>ASP.NET MVC | [ms-identity-aspnet-webapi-onbehalfof](https://github.com/Azure-Samples/ms-identity-aspnet-webapi-onbehalfof)의 Web API(서비스) |
| ![이 이미지는 Java 로고를 표시](media/sample-v2-code/logo_java.png) | [ms-identity-java-webapi](https://github.com/Azure-Samples/ms-identity-java-webapi)의 Web API(서비스) |
| ![이 이미지는 Node.js 로고를 표시](media/sample-v2-code/logo_nodejs.png)</p>Node.js(Passport.js)| [active-directory-javascript-nodejs-webapi-v2](https://github.com/Azure-Samples/active-directory-javascript-nodejs-webapi-v2)의 Web API(서비스) |
| ![이 이미지는 Node.js 로고를 표시](media/sample-v2-code/logo_nodejs.png)</p>Node.js(Passport.js)| [active-directory-b2c-javascript-nodejs-webapi](https://github.com/Azure-Samples/active-directory-b2c-javascript-nodejs-webapi)의 B2C Web API(서비스) |

## <a name="azure-functions-as-web-apis"></a>Web API로의 Azure Functions

다음 샘플에서는 Microsoft ID 플랫폼을 사용하여 웹 API를 노출하고 HttpTrigger를 사용하여 Azure 함수를 보호하는 방법 및 웹 API에서 다운스트림 API를 호출하는 방법을 보여줍니다.

| 플랫폼 | 샘플 |
| -------- | ------------------- |
| ![이 이미지는 ASP.NET Core 로고를 표시](media/sample-v2-code/logo_NETcore.png)</p>ASP.NET Core | [dotnet-native-aspnetcore-v2](https://github.com/Azure-Samples/ms-identity-dotnet-webapi-azurefunctions)의 ASP.NET Core Web API(서비스) Azure 함수  |
| ![이 이미지는 Python 로고를 표시](media/sample-v2-code/logo_python.png)</p>Python | [Python](https://github.com/Azure-Samples/ms-identity-python-webapi-azurefunctions)의 Web API(서비스) |
| ![이 이미지는 Node.js 로고를 표시](media/sample-v2-code/logo_nodejs.png)</p>Node.js(Passport.js)| [Node.js 및 passport-azure-ad](https://github.com/Azure-Samples/ms-identity-nodejs-webapi-azurefunctions)의 Web API(서비스) |
| ![이 이미지는 Node.js 로고를 표시](media/sample-v2-code/logo_nodejs.png)</p>Node.js(Passport.js)| [다음을 대신 사용하는 Node.js 및 passport-azure-ad](https://github.com/Azure-Samples/ms-identity-nodejs-webapi-onbehalfof-azurefunctions)의 Web API(서비스) |

## <a name="other-microsoft-graph-samples"></a>다른 Microsoft Graph 샘플

Azure AD 인증을 포함하여 Microsoft Graph API에 대한 여러 사용 패턴을 보여주는 [샘플](https://github.com/microsoftgraph/msgraph-community-samples/tree/master/samples#aspnet) 및 자습서에 대한 내용은 [Microsoft Graph 커뮤니티 샘플 및 자습서](https://github.com/microsoftgraph/msgraph-community-samples)를 참조하세요.

## <a name="see-also"></a>참고 항목

[Microsoft Graph API 개념 및 참조](/graph/use-the-api?context=graph%2fapi%2fbeta&view=graph-rest-beta&preserve-view=true)