---
title: SAML 기반 Single Sign-On 구성 된 앱에 로그인 하는 문제
description: 를 사용 하 여 SAML 기반 페더레이션된 Single Sign-On에 대해 구성한 응용 프로그램에 로그인 할 때 특정 오류에 대 한 지침 Azure Active Directory
services: active-directory
author: kenwith
manager: celestedg
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.topic: troubleshooting
ms.date: 02/18/2019
ms.author: kenwith
ms.reviewer: luleon, asteen
ms.custom: contperfq2
ms.openlocfilehash: ec39a6d106973808e26b7c06dce8b3054af490ff
ms.sourcegitcommit: 6906980890a8321dec78dd174e6a7eb5f5fcc029
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/22/2020
ms.locfileid: "92427372"
---
# <a name="problems-signing-in-to-saml-based-single-sign-on-configured-apps"></a>SAML 기반 Single Sign-On 구성 된 앱에 로그인 하는 문제
아래의 로그인 문제를 해결 하려면 다음을 수행 하 여 진단 및 해결 단계를 자동화 하는 것이 좋습니다.

- Azure Portal에서 테스트 환경을 사용할 때 더 나은 진단 및 해결 방법을 제공 하기 위해 [My Apps 보안 브라우저 확장](access-panel-extension-problem-installing.md) 을 설치 하 여 Azure AD (Azure Active Directory)를 지원 합니다.
- Azure Portal의 앱 구성 페이지에서 테스트 환경을 사용 하 여 오류를 재현 합니다. [SAML 기반 Single Sign-On 응용 프로그램 디버그](../azuread-dev/howto-v1-debug-saml-sso-issues.md) 에 대 한 자세한 정보

내 앱 보안 브라우저 확장을 사용 하 여 Azure Portal에서 [테스트 환경을](../azuread-dev/howto-v1-debug-saml-sso-issues.md) 사용 하는 경우 아래 단계를 수동으로 수행 하 여 SAML 기반 Single Sign-On 구성 페이지를 열 필요가 없습니다.

SAML 기반 Single Sign-On 구성 페이지를 열려면 다음을 수행 합니다.
1.  [**Azure Portal**](https://portal.azure.com/) 을 열고 **전역 관리자** 또는 **공동 관리자**권한으로 로그인 합니다.
1.  왼쪽의 왼쪽 탐색 메뉴 맨 위에 있는 **모든 서비스** 를 선택 하 여 **Azure Active Directory 확장** 을 엽니다.
1.  필터 검색 상자에 **"Azure Active Directory"** 를 입력 하 고 **Azure Active Directory** 항목을 선택 합니다.
1.  Azure Active Directory 왼쪽 탐색 메뉴에서 **엔터프라이즈 응용 프로그램** 을 선택 합니다.
1.  모든 **응용 프로그램을 선택 하** 여 모든 응용 프로그램의 목록을 봅니다.
    여기에 표시 하려는 응용 프로그램이 표시 되지 않으면 **모든 응용 프로그램 목록** 의 맨 위에 있는 **필터** 컨트롤을 사용 하 고 **표시** 옵션을 **모든 응용 프로그램**으로 설정 합니다.
1.  Single Sign-On을 구성하려는 애플리케이션을 선택합니다.
1. 응용 프로그램이 로드 되 면 응용 프로그램의 왼쪽 탐색 메뉴에서 **Single sign-on** 을 선택 합니다.
1. SAML 기반 SSO를 선택 합니다.

## <a name="application-not-found-in-directory"></a>애플리케이션을 디렉터리에서 찾을 수 없습니다
`Error AADSTS70001: Application with Identifier 'https:\//contoso.com' was not found in the directory.`

**가능한 원인**

`Issuer`응용 프로그램에서 SAML 요청의 AZURE ad로 전송 되는 특성은 AZURE ad에서 응용 프로그램에 대해 구성 된 식별자 값과 일치 하지 않습니다.

**해결 방법**

`Issuer`SAML 요청의 특성이 AZURE AD에 구성 된 식별자 값과 일치 하는지 확인 합니다.

SAML 기반 SSO 구성 페이지의 **기본 SAML 구성** 섹션에서 식별자 텍스트 상자의 값이 오류에 표시 된 식별자 값의 값과 일치 하는지 확인 합니다.

## <a name="the-reply-address-does-not-match-the-reply-addresses-configured-for-the-application"></a>회신 주소가 응용 프로그램에 대해 구성 된 회신 주소와 일치 하지 않습니다.
`Error AADSTS50011: The reply address 'https:\//contoso.com' does not match the reply addresses configured for the application.`

**가능한 원인**

`AssertionConsumerServiceURL`SAML 요청의 값이 AZURE AD에 구성 된 회신 URL 값 또는 패턴과 일치 하지 않습니다. `AssertionConsumerServiceURL`SAML 요청의 값은 오류에 표시 되는 URL입니다.

**해결 방법**

`AssertionConsumerServiceURL`SAML 요청의 값이 AZURE AD에 구성 된 회신 URL 값과 일치 하는지 확인 합니다. 

SAML 요청의 값과 일치 하도록 회신 URL 텍스트 상자의 값을 확인 하거나 업데이트 `AssertionConsumerServiceURL` 합니다.   
    
Azure AD에서 회신 URL 값을 업데이트 하 고 SAML 요청에서 응용 프로그램에 의해 전송 된 값과 일치 하는 경우 응용 프로그램에 로그인 할 수 있어야 합니다.

## <a name="user-not-assigned-a-role"></a>역할이 지정되지 않은 사용자
`Error AADSTS50105: The signed in user 'brian\@contoso.com' is not assigned to a role for the application.`

**가능한 원인**

사용자는 Azure AD에서 애플리케이션에 대한 액세스 권한을 부여받지 않았습니다.

**해결 방법**

응용 프로그램에 하나 이상의 사용자를 직접 할당 하려면 빠른 시작 [: 앱에 사용자 할당](add-application-portal-assign-users.md)을 참조 하세요.

## <a name="not-a-valid-saml-request"></a>유효한 SAML 요청이 아닙니다.
`Error AADSTS75005: The request is not a valid Saml2 protocol message.`

**가능한 원인**

Azure AD에서는 Single Sign-On의 애플리케이션에서 보낸 SAML 요청을 지원하지 않습니다. 일반적인 문제는 다음과 같습니다.
- SAML 요청에서 필수 필드 누락
- SAML 요청 인코딩 방법

**해결 방법**

1. SAML 요청을 캡처합니다. Saml [기반 Single Sign-On를 AZURE AD의 응용 프로그램에 디버그](../azuread-dev/howto-v1-debug-saml-sso-issues.md) 하는 방법 자습서에 따라 saml 요청을 캡처하는 방법을 알아봅니다.
1. 애플리케이션 공급 업체에 연락하여 다음 정보를 공유합니다.
    - SAML 요청
    - [Azure AD Single Sign-On SAML 프로토콜 요구 사항](../develop/single-sign-on-saml-protocol.md)

응용 프로그램 공급 업체는 Single Sign-On에 대 한 Azure AD SAML 구현을 지원 하는지 확인 해야 합니다.

## <a name="misconfigured-application"></a>잘못 구성 되는 응용 프로그램
`Error AADSTS650056: Misconfigured application. This could be due to one of the following: The client has not listed any permissions in the requested permissions in the client's application registration. Or, The admin has not consented in the tenant. Or, Check the application identifier in the request to ensure it matches the configured client application identifier. Please contact your admin to fix the configuration or consent on behalf of the tenant.`

**가능한 원인**

`Issuer`응용 프로그램에서 SAML 요청의 AZURE ad로 보낸 특성이 AZURE ad의 응용 프로그램에 대해 구성 된 식별자 값과 일치 하지 않습니다.

**해결 방법**

`Issuer`SAML 요청의 특성이 AZURE AD에 구성 된 식별자 값과 일치 하는지 확인 합니다. 

식별자 텍스트 상자의 값이 오류에 표시 된 식별자 값의 값과 일치 하는지 확인 합니다.

## <a name="certificate-or-key-not-configured"></a>인증서 또는 키가 구성되지 않음
`Error AADSTS50003: No signing key configured.`

**가능한 원인**

애플리케이션 개체가 손상되어 Azure AD에서 애플리케이션에 대해 구성된 인증서를 인식하지 못합니다.

**해결 방법**

기존 인증서를 삭제하고 새 인증서를 만들려면 다음 단계를 수행합니다.
1. SAML 기반 SSO 구성 화면의 **saml 서명 인증서** 섹션에서 **새 인증서 만들기** 를 선택 합니다.
1. 만료 날짜를 선택 하 고 **저장**을 클릭 합니다.
1. **새 인증서를 활성 상태로 만들기** 를 선택 하 여 활성 인증서를 재정의 합니다. 그런 다음, 창 맨 위에 있는 **저장**을 클릭하고 롤오버 인증서를 활성화하도록 수락합니다.
1. **SAML 서명 인증서** 섹션에서 **제거**를 클릭하여 **사용하지 않는** 인증서를 제거합니다.

## <a name="saml-request-not-present-in-the-request"></a>요청에 SAML 요청이 없습니다.
`Error AADSTS750054: SAMLRequest or SAMLResponse must be present as query string parameters in HTTP request for SAML Redirect binding.`

**가능한 원인**

Azure AD가 HTTP 요청의 URL 매개 변수 내에서 SAML 요청을 식별할 수 없었습니다. 이 문제는 응용 프로그램이 Azure AD에 SAML 요청을 보낼 때 HTTP 리디렉션 바인딩을 사용 하지 않는 경우에 발생할 수 있습니다.

**해결 방법**

응용 프로그램은 HTTP 리디렉션 바인딩을 사용 하 여 location 헤더로 인코드된 SAML 요청을 보내야 합니다. 구현 방법에 대한 자세한 내용은 [SAML 프로토콜 사양 문서](https://docs.oasis-open.org/security/saml/v2.0/saml-bindings-2.0-os.pdf)의 HTTP 리디렉션 바인딩 섹션을 참조하세요.

## <a name="azure-ad-is-sending-the-token-to-an-incorrect-endpoint"></a>Azure AD가 토큰을 잘못 된 끝점으로 보내고 있습니다.
**가능한 원인**

Single Sign-On 하는 동안 로그인 요청에 명시적 회신 URL (Assertion Consumer Service URL)이 포함 되어 있지 않으면 Azure AD는 해당 응용 프로그램에 대해 구성 된 회신 Url을 선택 합니다. 응용 프로그램에 명시적 회신 URL이 구성 되어 있는 경우에도 사용자는 리디렉션되는 것일 수 있습니다 https://127.0.0.1:444 . 

애플리케이션을 비-갤러리 앱으로 추가하면 Azure Active Directory에서는 이 회신 URL을 기본값으로 생성했습니다. 이 동작이 변경되어 Azure Active Directory는 기본적으로 이 URL을 더 이상 추가하지 않습니다. 

**해결 방법**

응용 프로그램에 대해 구성 된 사용 하지 않는 회신 Url을 삭제 합니다.

SAML 기반 SSO 구성 페이지의 **회신 URL (Assertion Consumer Service URL)** 섹션에서 시스템에 의해 생성 된 사용 하지 않거나 기본 회신 url을 삭제 합니다. 예: `https://127.0.0.1:444/applications/default.aspx`.

## <a name="problem-when-customizing-the-saml-claims-sent-to-an-application"></a>애플리케이션에 전송된 SAML 클레임을 사용자 지정할 때 발생하는 문제
응용 프로그램에 전송 된 SAML 특성 클레임을 사용자 지정 하는 방법을 알아보려면 [Azure Active Directory에서 클레임 매핑](../develop/active-directory-claims-mapping.md)을 참조 하세요.

## <a name="errors-related-to-misconfigured-apps"></a>잘못 구성 된 앱과 관련 된 오류
포털의 구성이 앱의 구성과 일치하는지 확인합니다. 특히, 클라이언트/애플리케이션 ID, 회신 URL, 클라이언트 비밀/키 및 앱 ID URI를 비교합니다.

구성한 리소스만을 요청하기 위해 **필요한 리소스** 탭에서 구성된 권한이 있는 코드에서 액세스를 요청하는 리소스를 비교합니다.

## <a name="next-steps"></a>다음 단계
- [애플리케이션 관리에 대한 빠른 시작 시리즈](add-application-portal-assign-users.md)
- [Azure AD에서 SAML 기반 Single Sign-On을 애플리케이션에 디버그하는 방법](../azuread-dev/howto-v1-debug-saml-sso-issues.md)
- [Azure AD Single Sign-On SAML 프로토콜 요구 사항](../develop/active-directory-single-sign-on-protocol-reference.md)
