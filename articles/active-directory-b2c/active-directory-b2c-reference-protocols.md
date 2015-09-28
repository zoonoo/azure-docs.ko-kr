<properties
	pageTitle="Azure AD B2C 미리 보기 | Microsoft Azure"
	description="Azure AD B2C 미리 보기에서 지원하는 프로토콜을 사용하여 직접 앱을 빌드하는 방법입니다."
	services="active-directory-b2c"
	documentationCenter=""
	authors="dstrockis"
	manager="msmbaldwin"
	editor=""/>

<tags
	ms.service="active-directory-b2c"
	ms.workload="identity"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="09/04/2015"
	ms.author="dastrock"/>

# Azure AD B2C 미리 보기: 인증 프로토콜

Azure AD B2C은 두 개의 업계 표준 프로토콜, OpenID Connect 및 OAuth 2.0을 지원하여 앱에 대한 identity-as-a-service를 제공합니다. 서비스는 표준을 준수하지만 이러한 프로토콜의 두 구현 간에는 약간의 차이가 있을 수 있습니다. 열린 원본 라이브러리 중 하나를 사용하는 대신 HTTP 요청을 직접 전송 및 처리하여 코드를 작성하는 경우 여기에 포함된 정보가 유용합니다. 각 특정 프로토콜의 세부 사항을 알아보기 전에 이 페이지에서 간략한 정보를 읽는 것이 좋지만 이미 Azure AD B2C를 잘 알고 있다면 [프로토콜 참조 가이드](#protocols)로 바로 이동할 수 있습니다.

<!-- TODO: Need link to libraries above -->

[AZURE.INCLUDE [active-directory-b2c-preview-note](../../includes/active-directory-b2c-preview-note.md)]
	
## 기본 사항
Azure AD B2C를 사용하는 모든 앱은 [Azure 포털](https://portal.azure.com)의 B2C 디렉터리에 등록해야 합니다. 앱 등록 프로세스는 몇 개의 값을 수집하고 앱에 할당합니다.

- 앱을 고유하게 식별하는 **응용 프로그램 ID**
- 응답을 다시 앱으로 보내는 데 사용할 수 있는 **리디렉션 URI** 또는 **패키지 식별자**
- 다른 몇 가지 시나리오 관련 값. 자세한 내용은 [앱 등록](active-directory-b2c-app-registration.md) 방법을 참조하세요.

등록되면 앱이 v2.0 끝점에 요청을 보내기 위해 Azure AD와 통신합니다.

```
https://login.microsoftonline.com/common/oauth2/v2.0/authorize
https://login.microsoftonline.com/common/oauth2/v2.0/token
```

거의 모든 OAuth 및 OpenID Connect 흐름에서 교환에 참여하는 다음 네 가지 요소가 있습니다.

![OAuth 2.0 역할](./media/active-directory-b2c-reference-protocols/protocols_roles.png)

- **권한 부여 서버**는 Azure AD v2.0 끝점입니다. 사용자 ID를 확인하고 리소스에 대한 액세스 권한을 부여 및 해지하고, 토큰을 발급합니다. ID 공급자라고도 하며 사용자 정보, 해당 액세스 및 흐름의 요소 간 트러스트 관계와 관련된 모든 사항을 안전하게 처리합니다.
- **리소스 소유자**는 일반적으로 최종 사용자입니다. 데이터를 소유하는 당사자이며 제3자가 해당 데이터 또는 리소스에 액세스하도록 허용할 권한이 있습니다.
- **OAuth 클라이언트**는 해당 응용 프로그램 ID로 식별되는 앱입니다. 일반적으로 최종 사용자가 상호 작용하는 요소이며 권한 부여 서버의 토큰을 요청합니다. 리소스 소유자가 리소스에 액세스할 수 있는 권한을 클라이언트에 부여해야 합니다.
- **리소스 서버**는 리소스 또는 데이터가 있는 곳입니다. OAuth 클라이언트를 안전하게 인증하고 권한을 부여할 수 있도록 권한 부여 서버를 신뢰하고 전달자 access\_token을 사용하여 리소스에 대한 액세스 권한을 부여할 수 있도록 합니다.

## 정책

Azure AD B2C **정책**은 서비스의 가장 중요한 기능입니다. Azure AD B2C는 정책을 도입하여 표준 OAuth 2.0 및 OpenID Connect 프로토콜을 확장하며 이로써 Azure AD B2C가 단순한 인증 및 권한 부여 이상을 수행할 수 있습니다. 정책은 등록, 로그인 또는 프로필 편집과 같은 소비자 ID 환경을 완벽하게 설명합니다. 관리 UI에서 정의되고 HTTP 인증 요청에서 특별한 쿼리 매개 변수를 사용하여 실행될 수 있습니다. 정책은 OAuth 2.0 및 OpenID Connect의 표준 기능이 아니므로 이해하는 데 시간을 할애해야 합니다. 자세한 내용은 [Azure AD B2C 정책 참조 가이드](active-directory-b2c-reference-policies)를 읽습니다.


## 토큰
Azure AD B2C의 OAuth 2.0 및 OpenID Connect 구현은 JWT로 표현되는 전달자 토큰을 포함하여 전달자 토큰을 광범위하게 활용합니다. 전달자 토큰은 보호된 리소스에 대한 "전달자" 액세스 권한을 부여하는 간단한 보안 토큰입니다. 이런 의미에서, "전달자"는 토큰을 제공할 수 있는 당사자입니다. 이 당사자는 전달자 토큰을 수신하려면 먼저 Azure AD를 사용하여 인증해야 합니다. 하지만 전송 및 저장 시 토큰 보안을 유지하는 데 필요한 단계를 취하지 않는 경우 의도하지 않은 당사자가 토큰을 가로채서 사용할 수 있습니다. 일부 보안 토큰에는 권한 없는 당사자가 사용하는 것을 방지하는 기본 제공 메커니즘이 있지만, 전달자 토큰에는 이러한 메커니즘이 없으므로 전송 계층 보안(HTTPS)과 같은 보안 채널에서 전달자 토큰을 전송해야 합니다. 전달자 토큰이 일반 텍스트 상태로 전송되는 경우 악의적인 사용자가 메시지 가로채기(man-in-the-middle) 공격을 사용해서 토큰을 획득하고 보호된 리소스에 무단으로 액세스하는 데 이 토큰을 사용할 수 있습니다. 나중에 사용하기 위해 전달자 토큰을 저장하거나 캐싱할 때도 동일한 보안 원칙이 적용됩니다. 항상 앱이 안전한 방식으로 전달자 토큰을 전송하고 저장하도록 합니다. 전달자 토큰의 보안 고려 사항을 자세히 알아보려면 [RFC 6750 Section 5](http://tools.ietf.org/html/rfc6750)를 참조하세요.

Azure AD B2C에서 사용되는 다양한 토큰 형식에 대한 자세한 내용은 [Azure AD 토큰 참조](active-directory-b2c-reference-tokens.md)를 참조하세요.

## 프로토콜

일부 예제 요청을 확인할 준비가 되었다면 아래 자습서 중 하나를 시작합니다. 각각 특정 인증 시나리오에 해당합니다. 사용자에게 맞는 흐름을 결정하는 데 도움이 필요하면 [Azure AD B2C로 빌드할 수 있는 앱 형식](active-directory-b2c-apps)을 확인합니다.

- [OAuth 2.0를 사용하여 모바일 및 네이티브 응용 프로그램 빌드](active-directory-b2c-reference-oauth-code.md)
- [Open ID Connect를 사용하는 웹앱 빌드](active-directory-b2c-reference-oidc.md)
- OAuth 2.0 암시적 흐름으로 단일 페이지 앱 구축(출시 예정)
- OAuth 2.0 클라이언트 자격 증명 흐름으로 데몬 또는 서버 쪽 프로세스 빌드(출시 예정)
- OAuth 2.0 리소스 소유자 암호 자격 증명 흐름으로 사용자 이름 및 암호를 사용하여 토큰 가져오기(출시 예정)
- 흐름 대신 OAuth 2.0으로 웹 API에서 토큰 가져오기(출시 예정)

<!-- [Call the Azure AD Graph API using the OAuth 2.0 Client Credentials Flow](active-directory-reference-graph.md) -->

<!---HONumber=Sept15_HO3-->