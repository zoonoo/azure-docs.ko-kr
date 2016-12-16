---
title: "Azure Active Directory 개발자 가이드 | Microsoft Docs"
description: "이 문서는 Azure Active Directory의 개발자 중심 리소스에 대한 포괄적인 가이드를 제공합니다."
services: active-directory
documentationcenter: dev-center-name
author: bryanla
manager: mbaldwin
editor: 
ms.assetid: 5c872c89-ef04-4f4c-98de-bc0c7460c7c2
ms.service: active-directory
ms.devlang: na
ms.topic: hero-article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 10/24/2016
ms.author: mbaldwin
translationtype: Human Translation
ms.sourcegitcommit: 219dcbfdca145bedb570eb9ef747ee00cc0342eb
ms.openlocfilehash: 46661a5cbe1a31aa8541cfc38c8f5fe519c0f6e4


---
# <a name="azure-active-directory-developers-guide"></a>Azure Active Directory 개발자 가이드
## <a name="overview"></a>개요
IDMaaS(Identity Management As A Service) 플랫폼인 Azure AD(Active Directory)는 개발자에게 응용 프로그램에 ID 관리 기능을 통합하는 효과적인 방법을 제공합니다. 다음 문서에서는 Azure AD의 구현 및 주요 기능에 대한 개요를 제공합니다. 자세히 살펴볼 준비가 되면 순서대로 읽어보거나 [시작](#getting-started) 부터 진행합니다.

1. [Azure AD 통합의 장점](develop/active-directory-how-to-integrate.md): Azure AD와 통합할 경우 보안 로그인 및 권한 부여를 위한 최상의 솔루션이 구현될 수 있는 이유를 알아보세요.
2. [Azure AD 인증 시나리오](active-directory-authentication-scenarios.md): Azure AD의 간단한 인증을 활용하여 응용 프로그램에 로그인을 제공하세요.
3. [Azure AD와 응용 프로그램 통합](active-directory-integrating-applications.md): Azure AD에서 응용 프로그램을 추가, 업데이트 및 제거하는 방법 및 통합된 앱에 대한 브랜딩 지침에 대해 알아보세요.
4. [Azure AD Graph API](active-directory-graph-api.md): Azure AD Graph API를 사용하여 프로그래밍 방식으로 REST API 끝점을 통해 Azure AD에 액세스하세요. 또한 Azure AD Graph API는 [Microsoft Graph](https://graph.microsoft.io/)를 통해 액세스할 수 있습니다. Microsoft Graph는 단일 REST API 끝점을 통하고 단일 액세스 토큰을 사용하여 여러 Microsoft 클라우드 서비스 API에 액세스할 수 있도록 하는 통합 API를 제공합니다.
5. [Azure AD 인증 라이브러리](active-directory-authentication-libraries.md): .NET, JavaScript, Objective-C, Android 등에 대한 Azure AD 인증 라이브러리를 사용하여 쉽게 사용자를 인증하고 액세스 토큰을 얻도록 할 수 있습니다.

## <a name="getting-started"></a>시작
이러한 자습서는 여러 플랫폼에 맞게 작성되었으며 Azure Active Directory를 사용하여 빠르게 개발을 시작할 수 있도록 도와줄 수 있습니다. 필수 조건으로, 먼저 [Azure Active Directory 테넌트를 가져와야](active-directory-howto-tenant.md)합니다.

### <a name="mobile-and-pc-application-quickstart-guides"></a>모바일 및 PC 응용 프로그램 빠른 시작 가이드
| [![iOS](./media/active-directory-developers-guide/ios.png)](active-directory-devquickstarts-ios.md) | [![Android](./media/active-directory-developers-guide/android.png)](active-directory-devquickstarts-android.md) | [![.NET](./media/active-directory-developers-guide/net.png)](active-directory-devquickstarts-dotnet.md) | [![Windows Universal](./media/active-directory-developers-guide/windows.png)](active-directory-devquickstarts-windowsstore.md) | [![Xamarin](./media/active-directory-developers-guide/xamarin.png)](active-directory-devquickstarts-xamarin.md) | [![Cordova](./media/active-directory-developers-guide/cordova.png)](active-directory-devquickstarts-cordova.md) | [![OAuth 2.0](./media/active-directory-developers-guide/oauth-2.png)](active-directory-protocols-oauth-code.md) |
|:---:|:---:|:---:|:---:|:---:|:---:|:---:|:---:|
| [iOS](active-directory-devquickstarts-ios.md) |[Android](active-directory-devquickstarts-android.md) |[.NET](active-directory-devquickstarts-dotnet.md) |[Windows 범용](active-directory-devquickstarts-windowsstore.md) |[Xamarin](active-directory-devquickstarts-xamarin.md) |[Cordova](active-directory-devquickstarts-cordova.md) |[OAuth 2.0을 사용하여 직접 통합](active-directory-protocols-oauth-code.md) |

### <a name="web-application-quickstart-guides"></a>웹 응용 프로그램 빠른 시작 가이드
| [![.NET](./media/active-directory-developers-guide/net.png)](active-directory-devquickstarts-webapp-dotnet.md) | [![Java](./media/active-directory-developers-guide/java.png)](active-directory-devquickstarts-webapp-java.md) | [![AngularJS](./media/active-directory-developers-guide/angularjs.png)](active-directory-devquickstarts-angular.md) | [![Javascript](./media/active-directory-developers-guide/javascript.png)](https://github.com/Azure-Samples/active-directory-javascript-singlepageapp-dotnet-webapi) | [![Node.JS](./media/active-directory-developers-guide/nodejs.png)](active-directory-devquickstarts-openidconnect-nodejs.md) | [![OpenID Connect](./media/active-directory-developers-guide/openid-connect.png)](active-directory-protocols-openid-connect-code.md) |
|:---:|:---:|:---:|:---:|:---:|:---:|
| [.NET](active-directory-devquickstarts-webapp-dotnet.md) |[Java](active-directory-devquickstarts-webapp-java.md) |[AngularJS](active-directory-devquickstarts-angular.md) |[Javascript](https://github.com/Azure-Samples/active-directory-javascript-singlepageapp-dotnet-webapi) |[Node.JS](active-directory-devquickstarts-openidconnect-nodejs.md) |[OpenID Connect를 사용하여 직접 통합](active-directory-protocols-openid-connect-code.md) |

### <a name="web-api-quickstart-guides"></a>웹 API 빠른 시작 가이드
| [![.NET](./media/active-directory-developers-guide/net.png)](active-directory-devquickstarts-webapi-dotnet.md) | [![Node.JS](./media/active-directory-developers-guide/nodejs.png)](active-directory-devquickstarts-webapi-nodejs.md) |
|:---:|:---:|
| [.NET](active-directory-devquickstarts-webapi-dotnet.md) |[Node.JS](active-directory-devquickstarts-webapi-nodejs.md) |

### <a name="querying-the-directory-quickstart-guide"></a>디렉터리 쿼리 빠른 시작 가이드
| [![.NET](./media/active-directory-developers-guide/graph.png)](active-directory-graph-api-quickstart.md) |
|:---:|
| [그래프 API](active-directory-graph-api-quickstart.md) |

## <a name="howtos"></a>방법
이러한 문서는 Azure Active Directory를 사용하여 특정 작업을 수행하는 방법을 설명합니다.

* [Azure AD 테넌트 가져오기](active-directory-howto-tenant.md)
* [다중 테넌트 응용 프로그램 패턴을 사용하는 모든 Azure AD 사용자 로그인](active-directory-devhowto-multi-tenant-overview.md)
* [Android](active-directory-sso-android.md) 및 [iOS](active-directory-sso-ios.md) 장치에서 ADAL을 사용하여 앱 간 SSO 사용
* [Azure AD에 대한 AppSource 인증된 응용 프로그램](active-directory-devhowto-appsource-certified.md)
* [Azure AD 응용 프로그램 갤러리에 응용 프로그램 나열](active-directory-app-gallery-listing.md)
* [판매자 대시보드로 Office 365용 웹앱 제출](https://msdn.microsoft.com/office/office365/howto/submit-web-apps-seller-dashboard)
* [Azure Active Directory 응용 프로그램 매니페스트 이해](active-directory-application-manifest.md)
* [클라이언트 응용 프로그램에서 로그인 및 앱 취득 버튼에 대한 브랜딩 지침 이해](active-directory-branding-guidelines.md)
* [미리 보기: 개인 및 회사 또는 학교 계정 모두를 사용하여 로그인할 수 있는 앱을 빌드하는 방법](active-directory-appmodel-v2-overview.md)
* [미리 보기: 사용자를 등록하고 로그인하게하는 앱을 빌드하는 방법](../active-directory-b2c/active-directory-b2c-overview.md)
* [미리 보기: PowerShell을 사용하여 Azure AD의 토큰 수명 구성](active-directory-configurable-token-lifetimes.md) Azure AD Graph API를 통해 구성하는 방법에 대한 자세한 내용은 [정책 작업](https://msdn.microsoft.com/library/azure/ad/graph/api/policy-operations) 및 [정책 엔터티](https://msdn.microsoft.com/library/azure/ad/graph/api/entity-and-complex-type-reference#policy-entity)를 참조하세요.

## <a name="reference"></a>참조
이러한 문서는 REST 및 인증 라이브러리 API, 프로토콜, 오류, 코드 샘플 및 끝점에 대한 기초 참조를 제공합니다.  

### <a name="support"></a>지원
* [태그가 지정된 질문](http://stackoverflow.com/questions/tagged/azure-active-directory): [azure-active-directory](http://stackoverflow.com/questions/tagged/azure-active-directory) 및 [adal](http://stackoverflow.com/questions/tagged/adal) 태그를 검색하여 Stack Overflow에서 Azure Active Directory 솔루션을 찾습니다.
* 응용 프로그램 개발 및 통합에 관련된 자주 사용되는 용어에 대한 정의는 [Azure AD 개발자 용어집](active-directory-dev-glossary.md) 을 참조하세요.

### <a name="code"></a>코드
* [Azure Active Directory 오픈 소스 라이브러리](http://github.com/AzureAD): 라이브러리의 소스를 찾는 가장 쉬운 방법은 [라이브러리 목록](active-directory-authentication-libraries.md)을 사용하는 것입니다.
* [Azure Active Directory 샘플](https://github.com/azure-samples?query=active-directory): 샘플 목록을 탐색하는 가장 쉬운 방법은 [코드 샘플 인덱스](active-directory-code-samples.md)를 사용하는 것입니다.
* [.NET용 ADAL(Active Directory 인증 라이브러리)](https://github.com/AzureAD/azure-activedirectory-library-for-dotnet) - 참조 설명서는 [최신 주요 버전](https://docs.microsoft.com/active-directory/adal/microsoft.identitymodel.clients.activedirectory) 및 [이전 주요 버전](https://docs.microsoft.com/active-directory/adal/v2/microsoft.identitymodel.clients.activedirectory) 모두에 사용할 수 있습니다.

### <a name="graph-api"></a>그래프 API
* [Graph API 참조](https://msdn.microsoft.com/library/azure/hh974476.aspx): Azure Active Directory Graph API에 대한 REST 참조입니다. [대화형 Graph API 참조 환경을 확인해보세요](https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/api-catalog).
* [Graph API 사용 권한 범위](https://msdn.microsoft.com/Library/Azure/Ad/Graph/howto/azure-ad-graph-api-permission-scopes): 앱이 테넌트의 디렉터리 데이터에 대해 가져야 하는 액세스 권한을 제어하는 데 사용되는 OAuth 2.0 사용 권한 범위입니다.

### <a name="authentication-and-authorization-protocols"></a>인증 및 권한 부여 프로토콜
* [Azure AD에서 서명 키 롤오버](active-directory-signing-key-rollover.md): Azure AD의 서명 키 롤오버 주기 및 가장 일반적인 응용 프로그램 시나리오에서 키를 업데이트하는 방법에 대해 알아봅니다.
* [OAuth 2.0 프로토콜: 권한 부여 코드 부여 사용](active-directory-protocols-oauth-code.md): OAuth 2.0 프로토콜의 "권한부여코드 허용(authorization code grant)" 을 사용하여 Azure Active Directory 테넌트의 웹 응용 프로그램 및 웹 API에 대한 액세스 권한을 부여할 수 있습니다.
* [OAuth 2.0 프로토콜: 암시적 부여 이해](active-directory-dev-understanding-oauth2-implicit-grant.md): 암시적 권한 부여에 대한 자세한 정보 및 응용 프로그램에 적합한지 여부를 알아봅니다.
* [OAuth 2.0 프로토콜: 클라이언트 자격 증명을 사용하여 서비스를 호출하는 서비스](active-directory-protocols-oauth-service-to-service.md): OAuth 2.0 클라이언트 자격 증명 부여는 사용자를 가장하는 대신 다른 웹 서비스를 호출할 때 웹 서비스(기밀 클라이언트)가 자체 자격 증명을 사용하여 인증하도록 허용합니다. 이 시나리오에서 클라이언트는 일반적으로 중간 계층 웹 서비스, 데몬 서비스 또는 웹 사이트입니다.
* [OpenID Connect 1.0 프로토콜: 로그인 및 인증](active-directory-protocols-openid-connect-code.md): OpenID Connect 1.0 프로토콜은 인증 프로토콜로 사용하는 OAuth 2.0을 확장합니다. 클라이언트 응용 프로그램은 id_token을 수신하여 로그인 프로세스를 관리하거나 권한 부여 코드 흐름을 보강하여 id_token 및 권한 부여 코드를 수신할 수 있습니다.
* [SAML 2.0 프로토콜 참조](active-directory-saml-protocol-reference.md): SAML 2.0 프로토콜을 사용하면 응용 프로그램에서 사용자에게 Single Sign-On 환경을 제공할 수 있습니다.
* [WS-Federation 1.2 프로토콜](http://docs.oasis-open.org/wsfed/federation/v1.2/os/ws-federation-1.2-spec-os.html): Azure Active Directory는 웹 서비스 페더레이션 버전 1.2 사양에 따라 WS-Federation 1.2를 지원합니다. 페더레이션 메타데이터 문서에 대한 자세한 정보는 [페더레이션 메타데이터](active-directory-federation-metadata.md)를 참조하세요.
* [지원되는 토큰 및 클레임 유형](active-directory-token-and-claims.md): 이 가이드를 사용하여 SAML 2.0 및 JWT(JSON 웹 토큰) 토큰에서 클레임을 파악 및 평가할 수 있습니다.

## <a name="videos"></a>비디오
### <a name="build"></a>빌드
엔지니어링 팀에서 직접 작업하는 Azure Active Directory 발표자가 앱을 개발하는 것을 보여주는 개요 프레젠테이션입니다. 프레젠테이션은 IDMaaS, 인증, ID 페더레이션 및 단일 로그인을 비롯한 기본 항목을 다룹니다.

* [Microsoft ID: Union의 상태 및 향후 방향](https://azure.microsoft.com/documentation/videos/build-2016-microsoft-identity-state-of-the-union-and-future-direction/)
* [Azure Active Directory: 최신 응용 프로그램에 대한 ID 관리 서비스(영문)](https://azure.microsoft.com/documentation/videos/build-2015-azure-active-directory-identity-management-as-a-service-for-modern-applications/)
* [Azure Active Directory를 사용하여 최신 웹 응용 프로그램 개발(영문)](https://azure.microsoft.com/documentation/videos/build-2015-develop-modern-web-applications-with-azure-active-directory/)
* [Azure Active Directory를 사용하여 최신 네이티브 응용 프로그램 개발(영문)](https://azure.microsoft.com/documentation/videos/build-2015-develop-modern-native-applications-with-azure-active-directory/)

### <a name="azure-friday"></a>Azure Friday
[Azure Friday](https://azure.microsoft.com/documentation/videos/azure-friday/) 는 금요일마다 전문가를 대상으로 진행되는 다양한 Azure 토픽과 관련된 10~15분 정도의 짧은 인터뷰를 집중적으로 소개하는 1:1 동영상 시리즈입니다.  이 페이지의 서비스 필터 기능을 사용하여 모든 Azure Active Directory 비디오를 시청할 수 있습니다.

* [Azure Identity 101(영문)](https://azure.microsoft.com/documentation/videos/azure-identity-basics/)
* [Azure Identity 102(영문)](https://azure.microsoft.com/documentation/videos/azure-identity-creating-active-directory/)
* [Azure Identity 103(영문)](https://azure.microsoft.com/documentation/videos/azure-identity-application-to-authenticate/)

## <a name="social"></a>소셜
* [Active Directory 팀 블로그](http://blogs.technet.com/b/ad/)(영문): Azure Active Directory 분야에서 최근에 진행된 개발을 살펴봅니다.
* [Azure Active Directory Graph 팀 블로그](http://blogs.msdn.com/b/aadgraphteam): Graph API와 관련된 Azure Active Directory 정보입니다.
* [클라우드 ID](http://www.cloudidentity.net)(영문): Identity Management as a Service에 대한 선임 Azure Active Directory PM의 견해입니다.  
* [Azure Active Directory Twitter](https://twitter.com/azuread): 140자 이하의 Azure Active Directory 알림입니다.

## <a name="windows-server-onpremises-development"></a>Windows Server 온-프레미스 배포
Windows Server 및 ADFS(Active Directory Federation Services) 개발 사용에 대한 지침은 다음을 참조하세요.

* [개발자를 위한 AD FS 시나리오](https://technet.microsoft.com/windows-server-docs/identity/ad-fs/overview/ad-fs-scenarios-for-developers): AD FS 구성 요소 및 원리의 개요, 지원되는 인증/권한 부여 시나리오에 대한 자세한 내용을 제공합니다.
* [AD FS 연습](https://technet.microsoft.com/windows-server-docs/identity/ad-fs/ad-fs-development): 관련된 인증/권한 부여 흐름을 구현하는 방법에 대한 단계별 지침을 제공하는 연습 문서의 목록입니다.




<!--HONumber=Nov16_HO2-->


