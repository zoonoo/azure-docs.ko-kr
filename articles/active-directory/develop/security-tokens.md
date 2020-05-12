---
title: 보안 토큰 | Microsoft
titleSuffix: Microsoft identity platform
description: V2.0 (Microsoft identity platform)의 보안 토큰에 대 한 기본 사항에 대해 알아봅니다.
services: active-directory
author: rwike77
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 05/11/2020
ms.author: ryanwi
ms.reviewer: jmprieur, saeeda, sureshja, hirsin
ms.custom: aaddev, identityplatformtop40, scenarios:getting-started
ms.openlocfilehash: def198a15710d0aff4a943300eedc338a7772e46
ms.sourcegitcommit: a8ee9717531050115916dfe427f84bd531a92341
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/12/2020
ms.locfileid: "83115798"
---
# <a name="security-tokens"></a>보안 토큰

중앙 집중식 id 공급자는 회사 네트워크에서 반드시 로그인 하지 않아도 되는 전 세계에 있는 사용자가 있는 앱에 특히 유용 합니다. Microsoft id 플랫폼은 사용자를 인증 하 고 [액세스 토큰](developer-glossary.md#access-token), [새로 고침 토큰](developer-glossary.md#refresh-token)및 [ID 토큰과](developer-glossary.md#id-token)같은 보안 토큰을 제공 하 여 [클라이언트 응용 프로그램이](developer-glossary.md#client-application) [리소스 서버](developer-glossary.md#resource-server)에서 보호 되는 리소스에 액세스할 수 있도록 합니다.

**액세스 토큰** 은 [OAuth 2.0](active-directory-v2-protocols.md) 흐름의 일부로 [권한 부여 서버](developer-glossary.md#authorization-server) 에서 발급 하는 보안 토큰입니다. 토큰에 사용 되는 사용자 및 앱에 대 한 정보를 포함 합니다. 웹 Api 및 기타 보호 된 리소스에 액세스 하는 데 사용할 수 있습니다. Microsoft id 플랫폼에서 토큰 액세스를 발급 하는 방법에 대 한 자세한 내용은 [액세스 토큰](access-tokens.md)을 참조 하세요.

액세스 토큰은 짧은 기간 동안만 유효 하므로 권한 부여 서버는 때때로 액세스 토큰이 발급 될 때 **새로 고침 토큰** 을 발급 합니다. 클라이언트 응용 프로그램은 필요한 경우 새 액세스 토큰에 대해이 새로 고침 토큰을 교환할 수 있습니다. Microsoft id 플랫폼에서 새로 고침 토큰을 사용 하 여 권한을 해지 하는 방법에 대 한 자세한 내용은 [토큰 해지](access-tokens.md#token-revocation)를 참조 하세요.

**ID 토큰** 은 [openid connect Connect](v2-protocols-oidc.md) 흐름의 일부로 클라이언트 응용 프로그램에 전송 됩니다. 액세스 토큰과 함께 또는 액세스 토큰 대신 보낼 수 있고 클라이언트가 사용자를 인증하는 데 사용됩니다. Microsoft id 플랫폼에서 ID 토큰을 발급 하는 방법에 대해 자세히 알아보려면 [id 토큰](id-tokens.md)을 참조 하세요.

> [!NOTE]
> 이 문서에서는 OAuth2 및 Openid connect Connect 프로토콜에서 사용 하는 보안 토큰을 설명 합니다. 많은 엔터프라이즈 응용 프로그램은 SAML를 사용 하 여 사용자를 인증 합니다. SAML 어설션에 대 한 자세한 내용은 [AZURE AD saml 토큰 참조](reference-saml-tokens.md) 를 참조 하세요.

## <a name="validating-security-tokens"></a>보안 토큰 유효성 검사

토큰의 유효성을 검사 하기 위해 토큰을 생성 한 앱, 사용자에 게 로그인 한 웹 앱 또는 호출 되는 web API에 따라 결정 됩니다. 토큰은 개인 키를 사용 하 여 **보안 토큰 서버 (STS)** 에 의해 서명 됩니다. STS는 해당 공개 키를 게시 합니다. 토큰의 유효성을 검사 하기 위해 앱은 STS 공개 키를 사용 하 여 서명을 확인 하 고 개인 키를 사용 하 여 서명을 만들었는지 확인 합니다.

토큰은 제한 된 시간 동안만 유효 합니다. 일반적으로 STS는 토큰 쌍을 제공 합니다.

* 응용 프로그램 또는 보호 된 리소스에 액세스 하기 위한 액세스 토큰 및
* 액세스 토큰이 만료 될 때 액세스 토큰을 새로 고치는 데 사용 되는 새로 고침 토큰입니다.

액세스 토큰은 웹 API에 헤더의 전달자 토큰으로 전달 됩니다 `Authorization` . 앱은 STS에 새로 고침 토큰을 제공할 수 있으며, 앱에 대 한 사용자 액세스가 취소 되지 않은 경우 새 액세스 토큰과 새로 고침 토큰을 다시 받게 됩니다. 이는 엔터프라이즈를 종료 하는 사람의 시나리오를 처리 하는 방법입니다. STS는 새로 고침 토큰을 받을 때 사용자에 게 더 이상 권한이 부여 되지 않은 경우 다른 유효한 액세스 토큰을 발급 하지 않습니다.

## <a name="json-web-tokens-jwts-and-claims"></a>JWTs (JSON 웹 토큰) 및 클레임

Microsoft id 플랫폼은 **클레임**을 포함 하는 Jwt **(JSON 웹 토큰)** 로 보안 토큰을 구현 합니다. JWTs는 보안 토큰으로 사용 되므로 이러한 형식의 인증을 **JWT 인증**이 라고도 합니다.

[클레임](developer-glossary.md#claim) 은 클라이언트 응용 프로그램 또는 [리소스 소유자](developer-glossary.md#resource-owner)와 같은 하나의 엔터티에 대 한 어설션을 리소스 서버와 같은 다른 엔터티에 제공 합니다. 클레임을 JWT 클레임 또는 JSON Web Token 클레임 이라고 할 수도 있습니다.

클레임은 토큰 주체에 대 한 팩트를 릴레이 하는 이름/값 쌍입니다. 예를 들어 클레임은 권한 부여 서버에서 인증 된 보안 주체에 대 한 팩트를 포함할 수 있습니다. 지정 된 토큰에 있는 클레임은 토큰 유형, 주체를 인증 하는 데 사용 되는 자격 증명 유형, 응용 프로그램 구성 등을 비롯 한 다양 한 사항에 따라 달라 집니다.

응용 프로그램은 다음과 같은 다양 한 작업에 대해 클레임을 사용할 수 있습니다.

* 토큰 유효성 검사
* 토큰 주체의 [테 넌 트](developer-glossary.md#tenant) 식별
* 사용자 정보 표시
* 주체의 권한 부여 확인

클레임은 다음과 같은 정보를 제공 하는 키-값 쌍으로 구성 됩니다.

* 토큰을 생성 한 보안 토큰 서버
* 토큰이 생성 된 날짜
* Subject (예: 디먼를 제외한 사용자)
* 토큰이 생성 된 앱 인 대상 그룹
* 토큰을 요청 하는 앱 (클라이언트)입니다. 웹 앱의 경우이는 대상 그룹과 동일할 수 있습니다.

Microsoft id 플랫폼에서 토큰 및 클레임 정보를 구현 하는 방법에 대 한 자세한 내용은 [액세스 토큰](access-tokens.md) 및 [ID 토큰](id-tokens.md)을 참조 하세요.

## <a name="how-each-flow-emits-tokens-and-codes"></a>각 흐름에서 토큰 및 코드를 내보내는 방법

클라이언트를 구축 하는 방법에 따라 Microsoft id 플랫폼에서 지원 되는 인증 흐름 중 하나 (또는 여러 개)를 사용할 수 있습니다. 이러한 흐름은 다양 한 토큰 (ID 토큰, 새로 고침 토큰, 액세스 토큰) 및 권한 부여 코드를 생성할 수 있으며, 서로 다른 토큰을 사용 하 여 작업을 수행 해야 합니다. 이 차트에서는 다음 개요를 제공 합니다.

|흐름 | 위해서는 | ID 토큰 | 액세스 토큰 | 토큰 새로 고침 | 인증 코드 |
|-----|----------|----------|--------------|---------------|--------------------|
|[인증 코드 흐름](v2-oauth2-auth-code-flow.md) | | x | x | x | x|
|[암시적 흐름](v2-oauth2-implicit-grant-flow.md) | | x        | x    |      |                    |
|[하이브리드 OIDC 흐름](v2-protocols-oidc.md#get-access-tokens)| | x  | |          |            x   |
|[토큰 상환 새로 고침](v2-oauth2-auth-code-flow.md#refresh-the-access-token) | 토큰 새로 고침 | x | x | x| |
|[흐름에 대 한](v2-oauth2-on-behalf-of-flow.md) | 액세스 토큰| x| x| x| |
|[클라이언트 자격 증명](v2-oauth2-client-creds-grant-flow.md) | | | x (앱 전용)| | |

암시적 모드를 통해 발급 된 토큰은 URL을 통해 브라우저에 다시 전달 되기 때문에 길이 제한이 있습니다 (여기서 `response_mode` 은 `query` 또는 `fragment` ).  일부 브라우저는 브라우저 표시줄에 배치할 수 있는 URL의 크기에 제한이 있으며 너무 길면 실패할 수 있습니다.  따라서 이러한 토큰에는 `groups` 또는 클레임이 없습니다 `wids` .

## <a name="next-steps"></a>다음 단계

인증 및 권한 부여 기본 사항에 대해 설명 하는 다른 항목:

* Microsoft id 플랫폼의 인증 및 권한 부여에 대 한 기본 개념에 대해 알아보려면 [인증 및 권한 부여](authentication-vs-authorization.md) 를 참조 하세요.
* Microsoft id 플랫폼과 통합 될 수 있도록 응용 프로그램을 등록 하는 프로세스에 대해 알아보려면 [응용 프로그램 모델](application-model.md) 을 참조 하세요.
* Microsoft id 플랫폼에서 웹, 데스크톱 및 모바일 앱의 로그인 흐름에 대해 알아보려면 [앱 로그인 흐름](app-sign-in-flow.md) 을 참조 하세요.
