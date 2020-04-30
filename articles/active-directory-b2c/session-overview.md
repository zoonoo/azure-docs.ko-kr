---
title: Azure Active Directory B2C의 SSO 세션 | Microsoft Docs
description: Azure Active Directory B2C에서 세션 동작을 구성 합니다.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 04/28/2019
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 1f7be4d01dd930e9ff421b2a163f1648f1793da9
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/28/2020
ms.locfileid: "82230913"
---
# <a name="azure-ad-b2c-session"></a>Azure AD B2C 세션

SSO (Single sign-on)는 사용자가 Azure Active Directory B2C (Azure AD B2C)에서 응용 프로그램을 통해 로그인 할 때 보안 및 편리 함을 추가 합니다. 이 문서에서는 Azure AD B2C에서 사용 되는 Single Sign-On 방법을 설명 하 고 정책을 구성할 때 가장 적합 한 SSO 방법을 선택 하는 데 도움을 줍니다.

Single Sign-On를 통해 사용자는 단일 계정으로 한 번 로그인 하 여 여러 응용 프로그램에 액세스할 수 있습니다. 응용 프로그램은 플랫폼 또는 도메인 이름에 관계 없이 웹, 모바일 또는 단일 페이지 응용 프로그램 일 수 있습니다.

사용자가 응용 프로그램에 처음으로 로그인 하면 Azure AD B2C 쿠키 기반 세션을 유지 합니다. 후속 인증 요청 시 Azure AD B2C은 쿠키 기반 세션을 읽고 유효성을 검사 하며, 사용자에 게 다시 로그인 하 라는 메시지를 표시 하지 않고 액세스 토큰을 발급 합니다. 쿠키 기반 세션이 만료 되거나 유효 하지 않게 되 면 사용자에 게 다시 로그인 하 라는 메시지가 표시 됩니다.  

## <a name="sso-session-types"></a>SSO 세션 유형

Azure AD B2C와의 통합에는 세 가지 유형의 SSO 세션이 포함 됩니다.

- Azure AD B2C에서 관리 하는 **Azure AD B2C** 세션
- **페더레이션 id** 공급자-id 공급자가 관리 하는 세션 (예: Facebook, Salesforce 또는 Microsoft 계정)
- 웹, 모바일 또는 단일 페이지 응용 프로그램에서 관리 하는 **응용 프로그램** 세션

![SSO 세션](media/session-overview/sso-session-types.png)

### <a name="azure-ad-b2c-sso"></a>Azure AD B2C SSO 

사용자가 로컬 또는 소셜 계정으로 성공적으로 인증 하면 Azure AD B2C는 사용자의 브라우저에 쿠키 기반 세션을 저장 합니다. 쿠키는 Azure AD B2C 테 넌 트 도메인 이름 (예:)에 `https://contoso.b2clogin.com`저장 됩니다.

사용자가 처음에 페더레이션된 계정으로 로그인 한 후 세션 시간 창 (TTL (time-to-live) 또는 TTL)이 동일한 앱 또는 다른 앱에 로그인 하는 경우 Azure AD B2C 페더레이션 id 공급자 로부터 새 액세스 토큰을 획득 하려고 시도 합니다. 페더레이션된 id 공급자 세션이 만료 되거나 잘못 된 경우 페더레이션 id 공급자는 사용자에 게 자격 증명을 입력 하 라는 메시지를 표시 합니다. 세션이 아직 활성 상태 이면 (또는 사용자가 페더레이션된 계정 대신 로컬 계정으로 로그인 한 경우) Azure AD B2C 사용자에 게 권한을 부여 하 고 추가 프롬프트를 제거 합니다.

세션 TTL 및 Azure AD B2C 정책과 응용 프로그램 간에 세션을 공유 하는 방법을 비롯 하 여 세션 동작을 구성할 수 있습니다.

### <a name="federated-identity-provider-sso"></a>페더레이션된 id 공급자 SSO

소셜 또는 엔터프라이즈 id 공급자는 자체 세션을 관리 합니다. 쿠키는 id 공급자의 도메인 이름 (예:)으로 `https://login.salesforce.com`저장 됩니다. Azure AD B2C 페더레이션된 id 공급자 세션을 제어 하지 않습니다. 대신, 세션 동작은 페더레이션된 id 공급자에 의해 결정 됩니다. 

다음과 같은 시나리오를 고려해 보세요.

1. 사용자가 Facebook에 로그인 하 여 피드를 확인 합니다.
2. 나중에 사용자가 응용 프로그램을 열고 로그인 프로세스를 시작 합니다. 응용 프로그램은 Azure AD B2C 사용자를 리디렉션하여 로그인 프로세스를 완료 합니다.
3. Azure AD B2C 등록 또는 로그인 페이지에서 사용자는 Facebook 계정으로 로그인 하도록 선택 합니다. 사용자가 Facebook으로 리디렉션됩니다. Facebook에 활성 세션이 있는 경우 사용자에 게 자격 증명을 제공 하 라는 메시지가 표시 되지 않으며 Facebook 토큰을 사용 하 여 Azure AD B2C로 즉시 리디렉션됩니다.

### <a name="application-sso"></a>응용 프로그램 SSO

웹, 모바일 또는 단일 페이지 응용 프로그램은 OAuth 액세스, ID 토큰 또는 SAML 토큰으로 보호할 수 있습니다. 사용자가 앱의 보호 된 리소스에 액세스 하려고 하면 앱은 응용 프로그램 쪽에 활성 세션이 있는지 여부를 확인 합니다. 앱 세션이 없거나 세션이 만료 된 경우 앱은 사용자에 게 로그인 페이지 Azure AD B2C 하 게 됩니다.

응용 프로그램 세션은와 `https://contoso.com`같은 응용 프로그램 도메인 이름으로 저장 된 쿠키 기반 세션이 될 수 있습니다. 모바일 응용 프로그램은 다른 방식으로 세션을 저장할 수 있지만 비슷한 방법을 사용 합니다.

## <a name="azure-ad-b2c-session-configuration"></a>Azure AD B2C 세션 구성

### <a name="session-scope"></a>세션 범위

다음 범위를 사용 하 여 Azure AD B2C 세션을 구성할 수 있습니다.

- **테넌트** - 이 설정은 기본값입니다. 이 설정을 사용하여 B2C 테넌트의 여러 애플리케이션 및 사용자 흐름이 동일한 사용자 세션을 공유할 수 있습니다. 예를 들어 사용자가 응용 프로그램에 로그인 하면 사용자는 액세스할 때 다른 사용자에 게 원활 하 게 로그인 할 수 있습니다.
- **애플리케이션** - 이 설정을 통해 다른 애플리케이션과 독립적으로 애플리케이션에 대한 독점적인 사용자 세션을 유지할 수 있습니다. 예를 들어 사용자가 Contoso 잡화에 이미 로그인 했는지 여부에 관계 없이 사용자가 Contoso Pharmacy에 로그인 할 수 있도록 하려면이 설정을 사용할 수 있습니다.
- **정책** - 이 설정을 통해 이를 사용하는 애플리케이션과 독립적으로 사용자 흐름에 대한 독점적인 사용자 세션을 유지할 수 있습니다. 예를 들어 사용자가 이미 로그인 하 고 MFA (multi-factor authentication) 단계를 완료 한 경우 사용자 흐름에 연결 된 세션이 만료 되지 않는 한 사용자에 게 여러 응용 프로그램의 더 높은 보안 부분에 대 한 액세스 권한을 부여할 수 있습니다.
- **사용 안 함** -이 설정은 사용자가 정책을 실행할 때마다 전체 사용자 흐름을 통해 실행 되도록 합니다.

### <a name="session-life-time"></a>세션 수명 시간

**세션 수명은** 인증에 성공한 후에 Azure AD B2C 세션 쿠키가 사용자 브라우저에 저장 되는 시간입니다. 세션 수명은 15에서 720 분 사이의 값으로 설정할 수 있습니다.

### <a name="keep-me-signed-in"></a>로그인 유지

[로그인 유지](custom-policy-keep-me-signed-in.md) 기능은 영구 쿠키를 사용 하 여 세션 수명 시간을 연장 합니다. 사용자가 브라우저를 닫았다가 다시 열고 나면 세션이 활성 상태로 유지 됩니다. 사용자가 로그 아웃 한 경우에만 세션이 해지 됩니다. 로그인 유지 기능은 로컬 계정으로 로그인 하는 경우에만 적용 됩니다.

로그인 유지 기능이 세션 수명 보다 우선적으로 적용 됩니다. 로그인 유지 기능이 사용 하도록 설정 되어 있고 사용자가 선택 하는 경우이 기능은 세션이 만료 되는 시간을 결정 합니다. 

### <a name="session-expiry-type"></a>세션 만료 유형

세션 **만료 유형은** 세션이 세션 수명 시간 설정 또는 로그인 유지 설정에 의해 확장 되는 방법을 나타냅니다.

- **롤링** -사용자가 쿠키 기반 인증 (기본값)을 수행할 때마다 세션이 확장 됨을 나타냅니다.
- **절대** -지정 된 기간 후 사용자가 다시 인증 해야 함을 나타냅니다.

## <a name="sign-out"></a>로그아웃

응용 프로그램에서 사용자를 로그 아웃 하려는 경우 응용 프로그램의 쿠키를 지우거 나 사용자를 사용 하 여 세션을 종료 하는 것 만으로는 충분 하지 않습니다. 로그 아웃 하려면 Azure AD B2C 사용자를 리디렉션해야 합니다. 그렇지 않으면 사용자가 자격 증명을 다시 입력 하지 않고도 응용 프로그램에 다시 인증할 수 있습니다.

로그 아웃 요청 시 다음을 Azure AD B2C 합니다.

1. Azure AD B2C 쿠키 기반 세션을 무효화 합니다.
1. 페더레이션된 id 공급자에서 로그 아웃 하려고 합니다.
   - Openid connect Connect-id 공급자의 잘 알려진 구성 끝점이 위치를 `end_session_endpoint` 지정 하는 경우입니다.
   - SAML-id 공급자 메타 데이터에 `SingleLogoutService` 위치가 포함 된 경우
1. 필요에 따라 다른 응용 프로그램에서 로그 아웃 합니다. 자세한 내용은 [Single sign-on](#single-sign-out) 섹션을 참조 하세요.

> [!NOTE]
> 로그 아웃 하면 Azure AD B2C를 사용 하 여 사용자의 Single Sign-On 상태가 지워지므로 사용자의 소셜 id 공급자 세션에서 로그 아웃 하지 못할 수 있습니다. 사용자가 후속 로그인 중에 동일한 id 공급자를 선택 하는 경우 자격 증명을 입력 하지 않고 다시 인증할 수 있습니다. 사용자가 응용 프로그램에서 로그 아웃 하려는 경우 반드시 Facebook 계정에서 로그 아웃 하려는 것은 아닙니다. 그러나 로컬 계정을 사용 하는 경우 사용자의 세션이 제대로 종료 됩니다.

### <a name="single-sign-out"></a>Single Sign-Out

사용자를 Azure AD B2C 로그 아웃 끝점으로 리디렉션하는 경우 (OAuth2 및 SAML 프로토콜 모두) 브라우저에서 사용자의 세션을 지웁니다 Azure AD B2C. 그러나 사용자는 인증을 위해 Azure AD B2C를 사용 하는 다른 응용 프로그램에 여전히 로그인 되어 있을 수 있습니다. 이러한 응용 프로그램이 사용자에 게 동시에 로그인 할 수 있도록 하기 위해 Azure AD B2C는 현재 사용자가 로그인 `LogoutUrl` 되어 있는 모든 응용 프로그램의 등록 된에 HTTP GET 요청을 보냅니다.

애플리케이션은 사용자를 식별하는 모든 세션을 지우고 `200` 요청을 반환하여 이 요청에 응답해야 합니다. 응용 프로그램에서 single sign-on을 지원 하려면 응용 프로그램의 코드에서를 `LogoutUrl` 구현 해야 합니다. Azure Portal에서 `LogoutUrl`을 설정할 수 있습니다.

1. [Azure Portal](https://portal.azure.com)로 이동 합니다.
1. 페이지의 오른쪽 위 모서리에서 계정을 클릭 하 여 Active B2C 디렉터리를 선택 합니다.
1. 왼쪽 탐색 패널에서 **Azure AD B2C**를 선택 하 고 **앱 등록**을 선택한 다음 응용 프로그램을 선택 합니다.
1. **설정**을 선택 하 고 **속성**을 선택한 다음 **로그 아웃 URL** 텍스트 상자를 찾습니다. 


## <a name="next-steps"></a>다음 단계

- [사용자 흐름에서 세션 동작을 구성](session-behavior.md)하는 방법에 대해 알아봅니다.
- [사용자 지정 정책에서 세션 동작을 구성](custom-policy-manage-sso-and-token-config.md#session-behavior-and-sso)하는 방법에 대해 알아봅니다.