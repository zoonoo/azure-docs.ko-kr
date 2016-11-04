---
title: v2.0 끝점 제한 사항 | Microsoft Docs
description: Azure AD v2.0 끝점의 제한 사항 목록입니다.
services: active-directory
documentationcenter: ''
author: dstrockis
manager: mbaldwin
editor: ''

ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/30/2016
ms.author: dastrock

---
# <a name="should-i-use-the-v2.0-endpoint?"></a>v2.0 끝점을 사용해야 하나요?
Azure Active Directory와 통합되는 응용 프로그램을 빌드할 때 v2.0 끝점 및 인증 프로토콜이 사용자 요구를 충족할지 결정해야 합니다.  원래 Azure AD 끝점은 완벽하게 지원되며 v2.0보다 더 풍부한 기능을 갖추고 있습니다.  그러나 v2.0 끝점은 사용자가 새로운 프로그래밍 모델을 사용하도록 유도할 수 있는 개발자에게 [상당한 혜택을 소개](active-directory-v2-compare.md) 합니다.

이 시점에서 v 2.0 끝점을 사용하는 경우 권장 사항은 다음과 같습니다.

* 응용 프로그램에서 개인 Microsoft 계정을 지원하려는 경우 v 2.0 끝점을 사용해야 합니다.  하지만 특히 회사 및 학교 계정과 관련하여 이 문서에 나열된 제한 사항을 이해해야 합니다.
* 응용 프로그램이 회사 및 학교 계정을 지원해야 하는 경우 [원래 Azure AD 끝점](active-directory-developers-guide.md)을 사용해야 합니다.

시간이 지남에 따라 v2.0 끝점은 여기에 나열된 제한 사항을 제거하게 되어 v2.0 끝점을 사용하도록 만듭니다.  그 동안 이 문서는 v2.0 끝점이 사용자에게 적합한지 결정하는 데 도움이 되도록 작성되었습니다.  v2.0 끝점의 현재 상태를 반영하도록 시간에 따라 이 문서를 계속 업데이트할 예정이므로 다시 확인하여 v2.0 기능에 대한 요구 사항을 다시 평가합니다.

v2.0 끝점을 사용하지 않는 Azure AD 사용 기존 앱이 있는 경우 처음부터 시작할 필요가 없습니다.  나중에 v2.0 끝점에 사용할 기존 Azure AD 응용 프로그램을 설정하는 방법을 제공할 예정입니다.

## <a name="restrictions-on-apps"></a>앱에 대한 제한 사항
다음 유형의 앱은 v2.0 끝점에서 현재 지원되지 않습니다.  지원되는 앱 형식에 대한 설명은 [이 문서](active-directory-v2-flows.md)를 참조하세요.

##### <a name="standalone-web-apis"></a>독립 실행형 Web API
v2.0 끝점으로는 [OAuth 2.0을 사용하여 보안된 Web API를 빌드](active-directory-v2-flows.md#web-apis)할 수 있습니다.  그러나 해당 Web API는 동일한 응용 프로그램 ID를 공유하는 응용 프로그램에서만 토큰을 받을 수 있습니다.  여러 다른 응용 프로그램 ID로 클라이언트에서 액세스되는 Web API 빌드는 지원되지 않습니다.  해당 클라이언트는 Web API에 대한 권한을 요청하거나 얻을 수 없습니다.

동일한 App ID를 가진 클라이언트에서 토큰을 수락하는 Web API를 빌드하는 방법을 확인하려면 [시작](active-directory-appmodel-v2-overview.md#getting-started)에서 v2.0 끝점 Web API 샘플을 참조하세요.

##### <a name="web-api-on-behalf-of-flow"></a>Web API On-Behalf-Of 흐름
많은 아키텍처에는 다른 다운스트림 Web API를 호출해야 하는 Web API가 포함되어 있으며 둘 다 v2.0 끝점에 의해 보안이 유지됩니다.  이 시나리오는 Web API 백 엔드를 포함하는 네이티브 클라이언트에서 일반적이며, Web API 백 엔드가 다시 Microsoft 온라인 서비스 또는 Azure AD를 지원하는 사용자 지정 작성 Web API를 호출합니다.

On-Behalf-Of 흐름이라고도 하는 OAuth 2.0 Jwt 전달자 자격 증명 권한 부여를 사용하여 이 시나리오를 지원할 수 있습니다.  그러나 On-Behalf-Of 흐름은 현재 v2.0 끝점에 대해 지원되지 않습니다.  일반 공급 Azure AD 서비스에서 이 흐름이 작동하는 방식을 확인하려면 [GitHub의 On-Behalf-Of 코드 샘플](https://github.com/AzureADSamples/WebAPI-OnBehalfOf-DotNet)을 참조하세요.

## <a name="restrictions-on-app-registrations"></a>앱 등록에 대한 제한 사항
이 시점에서 v2.0 끝점과 통합하려는 모든 앱은 [apps.dev.microsoft.com](https://apps.dev.microsoft.com)에서 새로운 앱 등록을 만들어야 합니다.  기존 Azure AD 또는 Microsoft 계정 앱은 v2.0 끝점과 호환되지 않으며 새로운 앱 등록 포털 외에는 어떤 포털에서도 앱이 등록되지 않습니다.  V 2.0 앱으로 사용할 기존 응용 프로그램을 사용하도록 설정하는 방법을 제공할 예정입니다. 그러나 이 시점에서는 v2.0 끝점에 대한 앱의 마이그레이션 경로가 없습니다.

마찬가지로, 새 앱 등록 포털에 등록된 앱은 원래 Azure AD 인증 끝점에 대해 작동하지 않습니다.  그러나 Microsoft 계정 인증 끝점 `https://login.live.com`과 성공적으로 통합하기 위해 앱 등록 포털에서 만든 앱을 사용할 수 있습니다.

또한 [apps.dev.microsoft.com](https://apps.dev.microsoft.com) 에 생성된 앱 등록에는 다음과 같은 주의 사항이 있습니다.

* **로그온 URL**이라고도 하는 **홈페이지** 속성은 지원되지 않습니다.  홈페이지가 없으면 이러한 응용 프로그램은 Office MyApps 패널에 표시되지 않습니다.
* 이 시점에는 응용 프로그램 ID당 두 개의 앱 암호만이 허용됩니다.
* 앱 등록은 단일 개발자 계정에서만 보고 관리할 수 있습니다.  여러 개발자 간에 공유할 수 없습니다.
* 허용된 redirect_uri의 형식에 대한 몇 가지 제한 사항이 있습니다.  자세한 내용은 다음 섹션을 참조하세요.

## <a name="restrictions-on-redirect-uris"></a>리디렉션 URI에 대한 제한
새로운 응용 프로그램 등록 포털에서 등록된 앱은 현재 제한된 redirect_uri 값 집합으로 한정됩니다.  웹앱 및 서비스에 대한 redirect_uri는 스키마 `https`로 시작해야 하고 모든 redirect_uri 값은 단일 DNS 도메인을 공유해야 합니다.  예를 들어 redirect_uris가 있는 웹앱은 등록할 수 없습니다.

`https://login-east.contoso.com`  
`https://login-west.contoso.com`

등록 시스템은 기존 redirect_uri의 전체 DNS 이름을 편집하려는 redirect_uri의 DNS 이름과 비교합니다. 다음 조건 중 하나라도 충족되는 경우 DNS 이름을 추가하는 요청이 실패하게 됩니다.  

* 새 redirect_uri의 전체 DNS 이름이 기존 redirect_uri의 DNS 이름과 일치하지 않는 경우
* 새 redirect_uri의 전체 DNS 이름이 기존 redirect_uri의 DNS 이름의 하위 도메인이 아닌 경우

예를 들어 앱이 현재 redirect_uri를 갖는 경우

`https://login.contoso.com`

DNS 이름과 정확히 일치하는 다음을 추가합니다.

`https://login.contoso.com/new`

또는 다음을 추가할 수 있습니다.

`https://new.login.contoso.com`

(login.contoso.com의 DNS 하위 도메인에 해당)  redirect_uris로 login-east.contoso.com 및 login-west.contoso.com을 갖는 앱을 원할 경우 다음 redirect_uris를 순서대로 추가해야 합니다.

`https://contoso.com`  
`https://login-east.contoso.com`  
`https://login-west.contoso.com`  

뒤의 두 개는 첫 번째 redirect_uri의 하위 도메인이므로 추가할 수 있습니다. 이 제한은 향후 릴리스에서 제거될 예정입니다.

새로운 응용 프로그램 등록 포털에서 앱을 등록하는 방법을 알아보려면 [이 문서](active-directory-v2-app-registration.md)를 참조하세요.

## <a name="restrictions-on-services-&-apis"></a>서비스 및 API에 대한 제한 사항
v2.0 끝점은 [지원되는 인증 흐름](active-directory-v2-flows.md)목록에 해당하는 경우 현재 새로운 응용 프로그램 등록 포털에서 등록된 모든 앱에 대해 로그인을 지원합니다.  그러나 이러한 앱은 매우 제한된 리소스 집합에 대한 OAuth 2.0 액세스 토큰만 획득할 수 있습니다.  v2.0 끝점은 다음에 대한 access_token만 발급합니다.

* 토큰을 요청한 앱.  논리 앱이 여러 구성 요소나 계층으로 이루어진 경우 앱은 자신에 대한 access_token을 획득할 수 있습니다.  이 시나리오의 작동 방식을 확인하려면 [시작](active-directory-appmodel-v2-overview.md#getting-started) 자습서를 확인하세요.
* Outlook 메일, 일정 및 연락처 REST API는 모두 https://outlook.office.com에 있습니다.  이러한 API에 액세스하는 앱을 작성하는 방법을 알아보려면 [Office 시작](https://www.msdn.com/office/office365/howto/authenticate-Office-365-APIs-using-v2) 자습서를 참조하세요.
* Microsoft Graph API.  Microsoft Graph 및 사용할 수 있는 모든 데이터에 대해 알아보려면 [https://graph.microsoft.io](https://graph.microsoft.io)를 방문하세요.

현재 다른 서비스는 지원되지 않습니다.  조만간 사용자 고유의 사용자 지정 작성 Web API 및 서비스 지원뿐 아니라 더 많은 Microsoft 온라인 서비스가 추가될 예정입니다.

## <a name="restrictions-on-libraries-&-sdks"></a>라이브러리 및 SDK에 대한 제한 사항
이 시점에서 v2.0 끝점에 대한 라이브러리 지원은 상당히 제한적입니다.  프로덕션 응용 프로그램에서 v2.0 끝점을 사용하려는 경우 다음 옵션이 있습니다.

* 웹 응용 프로그램을 작성하는 경우 일반적으로 사용 가능한 서버 쪽 미들웨어를 안전하게 사용하여 로그인 및 토큰 유효성 검사를 수행할 수 있습니다.  여기에는 ASP.NET용 OWIN Open ID Connect 미들웨어 및 NodeJS Passport 플러그 인이 포함됩니다.  미들웨어를 사용하는 코드 샘플도 [시작](active-directory-appmodel-v2-overview.md#getting-started) 섹션에서 제공됩니다.
* 다른 플랫폼과 네이티브 및 모바일 응용 프로그램의 경우 응용 프로그램 코드에서 프로토콜 메시지를 직접 전송 및 수신하여 v2.0 끝점과 통합할 수도 있습니다.  v2.0 OpenID Connect 및 OAuth 프로토콜은 [명시적으로 문서화](active-directory-v2-protocols.md) 되어 있어 이러한 통합을 수행하는 데 도움이 됩니다.
* 마지막으로, v2.0 끝점과 통합하는 데 오픈 소스 Open ID Connect 및 OAuth 라이브러리를 사용할 수 있습니다.  v2.0 프로토콜은 크게 변경되지 않고 다양한 오픈 소스 프로토콜 라이브러리와 호환되어야 합니다.  이러한 라이브러리의 사용 가능 여부는 언어 및 플랫폼마다 다릅니다. [Open ID Connect](http://openid.net/connect/) 및 [OAuth 2.0](http://oauth.net/2/) 웹 사이트는 주요 구현 목록을 유지 관리합니다. v2.0 끝점으로 테스트한 오픈 소스 클라이언트 라이브러리 및 샘플의 목록을 자세히 알아보려면 [Azure AD(Active Directory) v2.0 및 인증 라이브러리](active-directory-v2-libraries.md)를 참조하세요.

.NET 전용 [MSAL(Microsoft 인증 라이브러리)](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet) 초기 미리 보기도 릴리스했습니다.  .NET 클라이언트 및 서버 응용 프로그램에서 이 라이브러리를 사용해보세요. 하지만 미리 보기 라이브러리이기 때문에 GA 품질은 지원되지 않습니다.

## <a name="restrictions-on-protocols"></a>프로토콜에 대한 제한 사항
v2.0 끝점만 Open ID Connect 및 OAuth 2.0을 지원합니다.  그러나 다음과 같은 각 프로토콜의 일부 특징과 기능은 v2.0 끝점에 통합되어 있지 않습니다.

* OpenID Connect `end_session_endpoint`을 사용하면 앱이 v2.0 끝점으로 사용자의 세션을 종료할 수 있습니다.
* v2.0 끝점에서 발급한 id_tokens은 사용자에 대해 쌍별 식별자를 포함합니다.  즉, 두 개의 서로 다른 응용 프로그램은 동일한 사용자에 대해 다른 ID를 받게 됩니다.  Microsoft Graph `/me` 끝점을 쿼리하여 응용 프로그램에서 사용할 수 있는 사용자에 대해 상호 연결 가능한 ID를 받을 수 있습니다.
* 이 시점에서 전자 메일을 볼 수 있는 사용자의 승인을 획득하더라도 v2.0 끝점에서 발급한 id_tokens에는 사용자에 대해 `email` 클레임이 포함되지 않습니다.
* OpenID Connect 사용자 정보 끝점 이 시점에서 사용자 정보 끝점은 v2.0 끝점에서 구현되지 않습니다.  그러나 이 끝점에서 잠재적으로 발생하는 모든 사용자 프로필 데이터는 Microsoft Graph `/me` 끝점에서 사용할 수 있습니다.
* 역할 및 그룹 클레임  이 때 v2.0 끝점은 id_tokens에서 역할이나 그룹 클레임을 발급하도록 지원하지 않습니다.

v2.0 끝점에서 지원되는 프로토콜 기능의 범위를 더 잘 이해하려면 [OpenID Connect 및 OAuth 2.0 프로토콜 참조](active-directory-v2-protocols.md)를 참조하세요.

## <a name="restrictions-for-work-&-school-accounts"></a>회사 및 학교 계정에 대한 제한 사항
Microsoft 조직/비즈니스 사용자에 대해 특정된 몇 가지 기능은 v2.0 끝점에서 아직 지원하지 않습니다.

##### <a name="device-based-conditional-access,-native-and-mobile-apps,-and-the-microsoft-graph"></a>장치 기반 조건부 액세스, 네이티브 및 모바일 앱 및 Microsoft Graph
v2.0 끝점은 iOS 또는 Android에서 실행되는 네이티브 앱과 같은 모바일 및 네이티브 응용 프로그램의 장치 인증을 아직 지원하지 않습니다.  이 기능은 특정 조직에 대해 Microsoft Graph를 호출하여 네이티브 응용 프로그램을 차단할 수 있습니다.  장치 인증은 관리자가 응용 프로그램에 장치 기반 조건부 액세스 정책을 설정하는 경우에 필요합니다.  v2.0 끝점의 경우 장치 기반 조건부 액세스에 대한 가장 가능성이 높은 시나리오는 관리자가 Outlook API와 같은 Microsoft Graph에서 리소스에 대한 정책을 설정하는 것입니다.  관리자가 이 정책을 설정하고 네이티브 응용 프로그램이 Microsoft Graph에 토큰을 요청하는 경우 장치 인증이 아직 지원되지 않으므로 요청은 궁극적으로 실패하게 됩니다.  그러나 장치 기반 정책이 구성되는 경우 Microsoft Graph에 토큰을 요청하는 웹 응용 프로그램이 지원됩니다.  웹앱 시나리오에서 장치 인증은 사용자의 웹 브라우저를 통해 수행됩니다.

개발자는 수행되는 시기를 인식하더라도 Microsoft Graph 리소스에 정책을 설정할 때에 대한 제어가 불가능할 가능성이 높습니다.  회사 및 학교 사용자를 위해 응용 프로그램을 빌드하는 경우 v2.0 끝점이 장치 인증을 지원할 때까지는 [원래 Azure AD 끝점](active-directory-developers-guide.md) 을 사용해야 합니다.  장치 기반 조건부 액세스에 대한 자세한 내용은 [이 문서](active-directory-conditional-access.md#device-based-conditional-access)를 확인하세요.

##### <a name="windows-integrated-authentication-for-federated-tenants"></a>페더레이션된 테넌트에 대한 Windows 통합 인증
Windows 응용 프로그램에서 ADAL(및 원래 Azure AD 끝점)을 사용해본 경우 SAML 어설션 권한 부여로 알려진 기능을 활용할 수 있습니다.  이 권한 부여를 통해 페더레이션된 Azure AD 테넌트의 사용자는 자격 증명을 입력하지 않고도 해당 온-프레미스 Active Directory 인스턴스로 자동으로 인증할 수 있습니다.  SAML 어설션 권한 부여는 현재 v2.0 끝점에서 지원되지 않습니다.

<!--HONumber=Oct16_HO2-->


