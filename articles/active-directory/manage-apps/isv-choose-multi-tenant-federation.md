---
title: 다중 테 넌 트 응용 프로그램에 대 한 올바른 페더레이션 프로토콜 선택
description: Azure Active Directory와 통합에 독립 소프트웨어 공급 업체에 대 한 지침
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
ms.openlocfilehash: 53b315b87200b37cda215a29a65be9babaf54f43
ms.sourcegitcommit: 66237bcd9b08359a6cce8d671f846b0c93ee6a82
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/11/2019
ms.locfileid: "67795189"
---
# <a name="choose-the-right-federation-protocol-for-your-multi-tenant-application"></a>다중 테 넌 트 응용 프로그램에 대 한 올바른 페더레이션 프로토콜 선택

서비스 (SaaS) 응용 프로그램으로 소프트웨어를 개발 하는 경우 귀하와 귀하 고객의 요구에 가장 부합 하는 페더레이션 프로토콜을 선택 해야 합니다. 이 의사 결정은 개발 플랫폼 및 고객의 Office 365 및 Azure AD 에코 시스템 내에서 사용할 수 있는 데이터와 통합 하 여 원하는 기반으로 합니다.

전체 목록을 보려면 [SSO 통합을 사용할 수 있는 프로토콜](what-is-single-sign-on.md) Azure Active Directory를 사용 하 여 합니다.
다음 테이블 비교 
* Open Authentication 2.0 (OAuth 2.0)
* Open ID Connect (OIDC)
* SAML(Security Assertion Markup Language )
* 웹 서비스 페더레이션 (WSFed)

| 기능| OAuth / OIDC| SAML / WSFed |
| - |-|-|
| 웹 기반 single sign on| √| √ |
| 웹 기반 (Single sign-out)| √| √ |
| Mobile 기반 single sign on| √| √* |
| Mobile 기반 (Single sign-out)| √| √* |
| 모바일 응용 프로그램에 대 한 조건부 액세스 정책| √| X |
| 모바일 응용 프로그램에 대 한 원활한 MFA 환경| √| X |
| Access Microsoft Graph| √| X |

\* 하지만 Microsoft 샘플 또는 지침을 제공 하지 않습니다.

## <a name="oauth-20-and-open-id-connect"></a>OAuth 2.0 및 Openid Connect

OAuth 2.0은는 [업계 표준](https://oauth.net/2/) 권한 부여에 대 한 프로토콜입니다. OIDC (OpenID Connect) 되는 [산업 표준](https://openid.net/connect/) id 인증 계층 Oath 2.0 프로토콜을 기반으로 합니다.

### <a name="benefits"></a>이점

동일 하 게 인증 및 권한 부여 프로토콜에 기본 제공 OIDC/OAuth 2.0을 사용 하는 것이 좋습니다. SAML을 사용 하 여 또한 권한 부여를 구현 해야 합니다.

이러한 프로토콜에 내재 된 권한 부여는 응용을 프로그램을 액세스 하 고 다양 한 사용자 및 Microsoft Graph API를 통해 조직 데이터를 통합할 수 있습니다.

OAuth 2.0 및 OIDC를 사용 하 여 응용 프로그램에 SSO를 도입 하는 경우 고객의 최종 사용자 환경을 간소화 합니다. 쉽게 관리자나 최종 사용자 동의를 자동으로 표시 한 다음는 사용 권한 집합 필요한을 정의할 수 있습니다.

또한 이러한 프로토콜을 사용 하 여 조건부 액세스 및 MFA 정책 응용 프로그램에 대 한 액세스를 제어 하는 데 사용할 수가 있습니다. Microsoft는 라이브러리를 제공 하 고 [코드 샘플은 여러 기술 플랫폼](https://github.com/AzureAD/microsoft-authentication-library-for-js/wiki/Samples) 개발을 지원 하기 위해.  

### <a name="implementation"></a>구현

OAuth 2.0 공급자는 Microsoft Id를 사용 하 여 응용 프로그램을 등록 합니다. 그런 다음 OAuth 2.0 기반 응용 프로그램 통합 하려는 다른 Id 공급자와도 등록할 수 있습니다. 

응용 프로그램을 등록 하 고 SSO에 대 한 웹 앱에 이러한 프로토콜을 구현 하는 방법에 대 한 자세한 내용은 [OpenID Connect 및 Azure Active Directory를 사용 하 여 웹 응용 프로그램에 대 한 액세스 권한을 부여](../develop/sample-v2-code.md)합니다.  모바일 앱에서 SSO에 대 한 이러한 프로토콜을 구현 하는 방법에 대 한 내용은 다음을 참조 합니다. 

* [Android](../develop/quickstart-v2-android.md)

* [iOS](../develop/quickstart-v2-ios.md)

* [유니버설 Windows 플랫폼](../develop/quickstart-v2-uwp.md)

## <a name="saml-20-and-wsfed"></a>SAML 2.0 및 WSFed

SAML security Assertion Markup Language ()는 웹 응용 프로그램에 일반적으로 사용 됩니다. 참조 [SAML 프로토콜을 사용 하 여 Azure](../develop/active-directory-saml-protocol-reference.md) 에 대 한 개요입니다. 

웹 서비스 페더레이션 (WSFed)은는 [산업 표준](http://docs.oasis-open.org/wsfed/federation/v1.2/ws-federation.html) .NET 플랫폼을 사용 하 여 개발 된 웹 응용 프로그램에 대 한 일반적으로 사용 됩니다.

### <a name="benefits"></a>이점

SAML 2.0은 완성도 높은 standard 및 SAML 2.0에 대 한 대부분의 기술 플랫폼 지원 오픈 소스 라이브러리입니다. 고객에 게 SAML SSO를 구성 하는 관리 인터페이스를 제공할 수 있습니다. Microsoft Azure AD 및 SAML 2를 지 원하는 다른 id 공급자에 대 한 SAML SSO를 구성할 수 있습니다.

### <a name="trade-offs"></a>장단점

모바일 응용 프로그램에 대 한 WSFed 또는 SAML 2.0 프로토콜을 사용 하는 경우 multi-factor Authentication (MFA)을 포함 하 여 특정 조건부 액세스 정책을 성능 저하를 경험할 합니다. 또한 Microsoft Graph에 액세스 하려는 경우에 필요한 토큰을 생성 하기를 통해 OAuth 2.0 권한 부여를 구현 해야 합니다. 

### <a name="implementation"></a>구현

Microsoft은 SAML 구현을 위한 라이브러리를 제공 하지 않거나 특정 라이브러리는 것이 좋습니다. 사용 가능한 다양 한 오픈 소스 라이브러리가 있습니다.

## <a name="sso-and-using-microsoft-graph-rest-api"></a>SSO 및 Microsoft Graph를 사용 하 여 Rest API 

Microsoft Graph는 Microsoft 365, Office 365, Windows 10 및 Enterprise Mobility 및 보안 및 Dynamics 365 등의 추가 제품을 포함 하 여 모든 데이터 패브릭은 합니다. Core 스키마 사용자, 그룹, 일정, 메일, 파일 및 자세한 내용은 해당 사용자 생산성을 비롯 한 엔터티를 포함합니다. 개발자는 REST 기반 API, Microsoft Graph 데이터 연결 및 개발자를 허용 하는 커넥터에 대 한 Microsoft Graph는 세 가지 인터페이스를 제공 Microsoft Graph에 자신의 데이터를 추가 합니다.  

위의 프로토콜 중 하나를 사용 하 여 SSO에 대 한 Microsoft Graph REST API를 통해 사용할 수 있는 다양 한 데이터를 응용 프로그램의 액세스할을 수 있습니다. 따라서 Microsoft 365에 대 한 투자에서 더 많은 가치를 고객에 게 있습니다. 예를 들어 응용 프로그램이 고객의 Office 365 인스턴스 및 화면 사용자의 Microsoft Office 응용 프로그램 내에서 SharePoint 항목을 사용 하 여 통합 하는 Microsoft Graph API를 호출할 수 있습니다. 

사용 중인 경우 Open ID Connect 인증을 위해 OAuth2 사용 해야 하므로 개발 환경을 원활 하 게 됩니다, Open ID Connect, 토큰을 획득 하의 기초가 Microsoft Graph Api를 호출 하는 데 사용할 수 있습니다. 응용 프로그램이 SAML 또는 WSFed를 사용 중인 경우에 Microsoft Graph Api를 호출 하는 데 필요한 토큰을 획득 하는 이러한 OAuth2를 가져오려는 응용 프로그램 내에서 추가 코드를 추가 해야 합니다. 

## <a name="next-steps"></a>다음 단계

[다중 테 넌 트 응용 프로그램에 대해 SSO를 사용 하도록 설정](isv-sso-content.md)

[다중 테 넌 트 응용 프로그램에 대 한 문서를 만들려면](isv-create-sso-documentation.md)
