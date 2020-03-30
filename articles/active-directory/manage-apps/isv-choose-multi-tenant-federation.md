---
title: 다중 테넌트 응용 프로그램에 적합한 페더레이션 프로토콜 선택
description: Azure Active Directory와의 통합에 대한 독립 소프트웨어 공급업체에 대한 지침
services: active-directory
author: barbaraselden
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.topic: conceptual
ms.workload: identity
ms.date: 05/22/2019
ms.author: baselden
ms.reviewer: jeeds
ms.collection: M365-identity-device-management
ms.openlocfilehash: 3b3edbbe037c3874d639476e516b3732b7573d9b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/27/2020
ms.locfileid: "75443387"
---
# <a name="choose-the-right-federation-protocol-for-your-multi-tenant-application"></a>다중 테넌트 응용 프로그램에 적합한 페더레이션 프로토콜 선택

소프트웨어를 서비스(SaaS) 응용 프로그램으로 개발할 때는 귀사 및 고객의 요구에 가장 적합한 페더레이션 프로토콜을 선택해야 합니다. 이 결정은 개발 플랫폼과 고객의 Office 365 및 Azure AD 에코시스템 내에서 사용할 수 있는 데이터와 통합하려는 욕구를 기반으로 합니다.

Azure Active Directory와의 [SSO 통합에 사용할 수 있는 전체 프로토콜 목록을 참조하세요.](what-is-single-sign-on.md)
다음 표를 비교합니다. 
* 오픈 인증 2.0 (OAuth 2.0)
* 오픈 ID 커넥트(OIDC)
* SAML(Security Assertion Markup Language )
* 웹 서비스 페더레이션(WSFed)

| 기능| 오트 / OIDC| 샘 / WSFed |
| - |-|-|
| 웹 기반 단일 사인온| √| √ |
| 웹 기반 단일 로그아웃| √| √ |
| 모바일 기반 단일 사인온| √| √* |
| 모바일 기반 단일 로그아웃| √| √* |
| 모바일 응용 프로그램에 대한 조건부 액세스 정책| √| X |
| 모바일 애플리케이션을 위한 원활한 MFA 경험| √| X |
| 액세스 마이크로 소프트 그래프| √| X |

*가능하지만 Microsoft는 샘플이나 지침을 제공하지 않습니다.

## <a name="oauth-20-and-open-id-connect"></a>OAuth 2.0 및 개방형 ID 연결

OAuth 2.0은 인증을 위한 [업계 표준](https://oauth.net/2/) 프로토콜입니다. OIDC(OpenID Connect)는 OAuth 2.0 프로토콜 위에 구축된 [업계 표준](https://openid.net/connect/) ID 인증 계층입니다.

### <a name="benefits"></a>이점

프로토콜에 대한 인증 및 권한 부여가 내장되어 있기 때문에 OIDC/OAuth 2.0을 사용하는 것이 좋습니다. SAML에서는 권한 부여를 추가로 구현해야 합니다.

이러한 프로토콜에 내재된 권한 부여를 통해 응용 프로그램은 Microsoft Graph API를 통해 풍부한 사용자 및 조직 데이터에 액세스하고 통합할 수 있습니다.

OAuth 2.0 및 OIDC를 사용하면 애플리케이션에 SSO를 채택할 때 고객의 최종 사용자 환경을 간소화할 수 있습니다. 필요한 권한 집합을 쉽게 정의할 수 있으며, 이 세트는 관리자 또는 최종 사용자 동의에 자동으로 표시됩니다.

또한 이러한 프로토콜을 사용하면 고객이 조건부 액세스 및 MFA 정책을 사용하여 응용 프로그램에 대한 액세스를 제어할 수 있습니다. Microsoft는 여러 기술 플랫폼에서 라이브러리 및 [코드 샘플을](https://github.com/AzureAD/microsoft-authentication-library-for-js/wiki/Samples) 제공하여 개발에 도움을 줍니다.  

### <a name="implementation"></a>구현

OAuth 2.0 공급자인 Microsoft ID에 응용 프로그램을 등록합니다. 그런 다음 통합하려는 다른 ID 공급자에 OAuth 2.0 기반 응용 프로그램을 등록할 수도 있습니다. 

응용 프로그램을 등록하고 웹 앱에 SSO에 대한 이러한 프로토콜을 구현하는 방법에 대한 자세한 내용은 [OpenID Connect 및 Azure Active Directory를 사용하여 웹 응용 프로그램에 대한 액세스 권한 부여를](../develop/sample-v2-code.md)참조하세요.  모바일 앱에서 SSO에 대해 이러한 프로토콜을 구현하는 방법에 대한 자세한 내용은 다음을 참조하십시오. 

* [Android](../develop/quickstart-v2-android.md)

* [iOS](../develop/quickstart-v2-ios.md)

* [범용 윈도우 플랫폼](../develop/quickstart-v2-uwp.md)

## <a name="saml-20-and-wsfed"></a>SAML 2.0 및 WSFed

SAML(보안 어설션 태그 언어)은 일반적으로 웹 응용 프로그램에 사용됩니다. [Azure에서 SAML 프로토콜을 사용하여](../develop/active-directory-saml-protocol-reference.md) 개요를 사용하는 방법을 참조하세요. 

웹 서비스 페더레이션(WSFed)은 .Net 플랫폼을 사용하여 개발된 웹 응용 프로그램에 일반적으로 사용되는 [업계 표준입니다.](https://docs.oasis-open.org/wsfed/federation/v1.2/ws-federation.html)

### <a name="benefits"></a>이점

SAML 2.0은 성숙한 표준이며 대부분의 기술 플랫폼은 SAML 2.0을 위한 오픈 소스 라이브러리를 지원합니다. SAML SSO를 구성하는 관리 인터페이스를 고객에게 제공할 수 있습니다. Microsoft Azure AD 및 SAML 2를 지원하는 기타 ID 공급자에 대해 SAML SSO를 구성할 수 있습니다.

### <a name="trade-offs"></a>장단점

모바일 응용 프로그램에 SAML 2.0 또는 WSFed 프로토콜을 사용하는 경우 MFA(다단계 인증)를 포함한 특정 조건부 액세스 정책은 성능이 저하됩니다. 또한 Microsoft 그래프에 액세스하려면 OAuth 2.0을 통해 권한 부여를 구현하여 필요한 토큰을 생성해야 합니다. 

### <a name="implementation"></a>구현

Microsoft는 SAML 구현을 위한 라이브러리를 제공하지 않거나 특정 라이브러리를 권장하지 않습니다. 많은 오픈 소스 라이브러리를 사용할 수 있습니다.

## <a name="sso-and-using-microsoft-graph-rest-api"></a>SSO 및 마이크로소프트 그래프 나머지 API 사용 

마이크로소프트 그래프는 마이크로소프트의 모든 데이터 패브릭 365, 오피스를 포함 하 여 365, 윈도우 10 그리고 엔터프라이즈 이동성 및 보안, 그리고 Dynamics 와 같은 추가 제품 365. 여기에는 사용자 생산성을 높이는 사용자, 그룹, 일정, 메일, 파일 등과 같은 엔터티의 핵심 스키마가 포함됩니다. Microsoft Graph는 개발자에게 REST 기반 API, Microsoft 그래프 데이터 연결 및 개발자가 자신의 데이터를 Microsoft 그래프에 추가할 수 있는 커넥터를 위한 세 가지 인터페이스를 제공합니다.  

SSO에 대한 위의 프로토콜을 사용하면 Microsoft 그래프 REST API를 통해 사용할 수 있는 리치 데이터에 대한 응용 프로그램의 액세스를 사용할 수 있습니다. 이를 통해 고객은 Microsoft 365에 대한 투자를 통해 더 많은 가치를 얻을 수 있습니다. 예를 들어 응용 프로그램은 Microsoft 그래프 API를 호출하여 고객의 Office 365 인스턴스및 응용 프로그램 내의 사용자 Microsoft Office 및 SharePoint 항목과 통합할 수 있습니다. 

인증에 오픈 ID 연결을 사용하는 경우, 오픈 ID 연결의 기초인 OAuth2를 사용하여 토큰을 획득하는 데 Microsoft 그래프 API를 호출하는 데 사용할 수 있으므로 개발 환경이 원활합니다. 응용 프로그램이 SAML 또는 WSFed를 사용하는 경우 이러한 OAuth2를 가져오려면 응용 프로그램 내에 코드를 추가하여 Microsoft 그래프 API를 호출하는 데 필요한 토큰을 획득해야 합니다. 

## <a name="next-steps"></a>다음 단계

[다중 테넌트 애플리케이션용 SSO 사용](isv-sso-content.md)

[다중 테넌트 응용 프로그램에 대한 설명서 작성](isv-create-sso-documentation.md)
