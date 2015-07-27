<properties
   pageTitle="Azure Active Directory 개발자 가이드"
   description="Azure Active Directory의 개발자 중심 리소스에 대한 포괄적인 가이드"
   services="active-directory"
   documentationCenter="dev-center-name"
   authors="msmbaldwin"
   manager="mbaldwin"
   editor=""/>

<tags
   ms.service="active-directory"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="identity"
   ms.date="06/10/2015"
   ms.author="mbaldwin"/>


# Azure Active Directory 개발자 가이드

## 개요
IDMaaS(Identity Management As A Service) 플랫폼인 Azure Active Directory는 개발자에게 응용 프로그램에 ID 관리 기능을 통합하는 효과적인 방법을 제공합니다. 다음 문서에서는 Azure Active Directory의 구현 및 주요 기능에 대한 개요를 제공합니다. 자세히 살펴볼 준비가 되면 순서대로 읽어보거나 [시작](#getting-started)부터 진행합니다.


1. **[Azure AD 통합의 장점](active-directory-how-to-integrate.md)**: Azure Active Directory와 통합할 경우 보안 로그인 및 권한 부여를 위한 최상의 솔루션이 구현될 수 있는 이유를 알아보세요.

1. **[로그인에 Azure AD 사용](active-directory-authentication-scenarios.md)**: Azure Active Directory의 간단한 인증을 활용하여 응용 프로그램에 대한 로그온을 제공하세요.

1. **[디렉터리 쿼리](https://msdn.microsoft.com/library/azure/hh974476.aspx)**: Azure Active Directory Graph API를 사용하여 프로그래밍 방식으로 REST API 끝점을 통해 Azure AD에 액세스하세요.

1. **[응용 프로그램 모델 이해](https://msdn.microsoft.com/library/azure/dn151122.aspx)**: 응용 프로그램 등록 및 다중 테넌트 응용 프로그램에 대한 브랜딩 지침에 대해 알아보세요.

1. **[라이브러리](https://msdn.microsoft.com/library/azure/dn151135.aspx)**: 사용자가 Azure 인증 라이브러리에서 액세스 토큰을 얻을 수 있도록 쉽게 인증할 수 있습니다.

빌드 2015 회의에서 제시한 AAD 개요를 보려면 아래 [비디오](#videos) 섹션을 참조하세요.


## 시작

이러한 자습서는 여러 플랫폼에 맞게 작성되었으며 Azure Active Directory를 사용하여 빠르게 개발을 시작할 수 있도록 도와줍니다. 필수 구성 요소로 먼저 [Azure Active Directory 테넌트를 가져와야](active-directory-howto-tenant.md) 합니다.

#### 모바일 또는 PC 응용 프로그램 빠른 시작 가이드

- [iOS](active-directory-devquickstarts-ios.md)
- [Android](active-directory-devquickstarts-android.md)
- [.NET](active-directory-devquickstarts-dotnet.md)
- [Windows Phone](active-directory-devquickstarts-windowsphone.md)
- [Windows 스토어](active-directory-devquickstarts-windowsstore.md)
- [Xamarin](active-directory-devquickstarts-xamarin.md)
- [Cordova](active-directory-devquickstarts-cordova.md)


####웹 응용 프로그램 또는 웹 API 빠른 시작 가이드

- [.NET 웹앱](active-directory-devquickstarts-webapp-dotnet.md)
- [.NET 웹 API](active-directory-devquickstarts-webapi-dotnet.md)
- [Javascript](active-directory-devquickstarts-angular.md)
- [Node.JS](active-directory-devquickstarts-webapi-nodejs.md)


## 방법

이러한 문서는 Azure AD(Active Directory)를 사용하여 특정 작업을 수행하는 방법을 설명합니다.

- [Azure AD 테넌트를 가져오는 방법](active-directory-howto-tenant.md)
- [Azure AD 응용 프로그램 갤러리에 응용 프로그램을 나열하는 방법](active-directory-app-gallery-listing.md)
- [앱에서 Office 365 API를 시작하는 방법](https://msdn.microsoft.com/office/office365/howto/getting-started-Office-365-APIs)(영문)
- [판매자 대시보드로 Office 365용 웹앱을 제출하는 방법](https://msdn.microsoft.com/office/office365/howto/submit-web-apps-seller-dashboard)


## 참조

이러한 문서는 REST 및 인증 라이브러리 API, 프로토콜, 오류, 코드 샘플 및 끝점에 대한 기초 참조를 제공합니다.

###  지원
- **[지원 받는 위치](http://stackoverflow.com/questions/tagged/azure-active-directory)**: [azure-active-directory](http://stackoverflow.com/questions/tagged/azure-active-directory)(영문) 및 [adal](http://stackoverflow.com/questions/tagged/adal)(영문) 태그를 검색하여 스택 오버플로에서 Azure AD 솔루션을 찾습니다.

### 코드

- **[Azure AD 오픈 소스 라이브러리](http://github.com/AzureAD)**: 라이브러리의 소스를 찾는 가장 쉬운 방법은 [라이브러리 목록](https://msdn.microsoft.com/library/azure/dn151135.aspx)을 사용하는 것입니다.

- **[Azure AD 샘플](http://github.com/AzureADSamples)**(영문): 샘플 목록을 탐색하는 가장 쉬운 방법은 [코드 샘플 인덱스](active-directory-code-samples.md)를 사용하는 것입니다.


### 그래프 API

- **[Graph API 참조](https://msdn.microsoft.com/library/azure/hh974476.aspx)**: Azure Active Directory Graph API에 대한 REST 참조입니다. [새로운 대화형 Graph API 참조 환경을 확인해보세요](https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/api-catalog).

- **[Graph API 사용 권한 범위](https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/graph-api-permission-scopes)**(영문): 앱이 테넌트의 디렉터리 데이터에 대해 가져야 하는 액세스 권한을 제어하는 데 사용되는 OAuth 2.0 사용 권한 범위입니다.


### 인증 프로토콜

- **[SAML 2.0 프로토콜 참조](https://msdn.microsoft.com/library/azure/dn195591.aspx)**: SAML 2.0 프로토콜을 사용하여 응용 프로그램은 사용자에게 Single Sign-On 환경을 제공할 수 있습니다.


- **[OAuth 2.0 프로토콜 참조](https://msdn.microsoft.com/library/azure/dn645545.aspx)**: OAuth 2.0 프로토콜을 사용하여 사용자는 Azure AD 테넌트에서 웹 응용 프로그램 및 웹 API에 대한 액세스 권한을 부여할 수 있습니다.


- **[OpenID Connect 1.0 프로토콜 참조](https://msdn.microsoft.com/library/azure/dn645541.aspx)**(영문): OpenID Connect 1.0 프로토콜은 인증 프로토콜로 사용할 수 있게 OAuth 2.0을 확장합니다.


- **[WS-Federation 1.2 프로토콜 참조](https://msdn.microsoft.com/library/azure/dn903702.aspx)**: 웹 서비스 페더레이션 버전 1.2 사양에 지정된 WS-Federation 1.2 프로토콜입니다.

- **[지원되는 보안 토큰 및 클레임](active-directory-token-and-claims.md)**: SAML 2.0 및 JWT(JSON 웹 토큰) 토큰의 클레임을 이해하고 평가하기 위한 가이드입니다.

## 비디오

### 빌드 2015

Azure Active Directory를 사용한 앱 개발에 관한 이 개요 프레젠테이션에는 직접적으로 엔지니어링 팀에서 일하는 발표자와 IDMaaS, 인증, ID 페더레이션 및 Single Sign-On을 비롯한 기본적인 연습 항목이 포함되어 있습니다.

- **[Azure Active Directory: 최신 응용 프로그램에 대한 ID 관리 서비스](http://azure.microsoft.com/documentation/videos/build-2015-azure-active-directory-identity-management-as-a-service-for-modern-applications)**(영문)
- **[Azure Active Directory를 사용하여 최신 웹 응용 프로그램 개발](http://azure.microsoft.com/documentation/videos/build-2015-develop-modern-web-applications-with-azure-active-directory)**(영문)
- **[Azure Active Directory를 사용하여 최신 네이티브 응용 프로그램 개발](http://azure.microsoft.com/documentation/videos/build-2015-develop-modern-native-applications-with-azure-active-directory)**(영문)

### Azure Friday
[Azure Friday](http://azure.microsoft.com/documentation/videos/azure-friday/)는 금요일마다 다양한 전문가를 대상으로 진행된 Azure 토픽에 대한 10~15분 정도의 짧은 인터뷰를 집중적으로 소개하는 1:1 비디오 시리즈입니다. 이 페이지의 서비스 필터 기능을 사용하여 모든 Azure Active Directory 비디오를 시청할 수 있습니다.

- **[Azure Identity 101](http://azure.microsoft.com/documentation/videos/azure-identity-basics/)**(영문)
- **[Azure Identity 102](http://azure.microsoft.com/documentation/videos/azure-identity-creating-active-directory/)**(영문)
- **[Azure Identity 103](http://azure.microsoft.com/documentation/videos/azure-identity-application-to-authenticate/)**(영문)

## 소셜

- **[Active Directory 팀 블로그](http://blogs.technet.com/b/ad/)**(영문): Azure AD 분야에서 최근에 진행된 개발을 살펴봅니다.

- **[Azure AD 그래프 블로그](http://blogs.msdn.com/b/aadgraphteam)**(영문): Graph API에 관련된 Azure AD 정보입니다.

- **[클라우드 ID](http://www.cloudidentity.net)**(영문): Identity Management as a Service에 대한 선임 Azure Active Directory PM의 견해

- **[Twitter의 Azure AD](https://twitter.com/azuread)**(영문): 140자 이하의 Azure AD 알림입니다.

<!---HONumber=July15_HO3-->