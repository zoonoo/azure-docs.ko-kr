---
title: OpenID Connect로 등록 및 로그인 설정
titleSuffix: Azure AD B2C
description: Azure Active Directory B2C에서 OpenID Connect IdP(ID 공급자)를 사용하여 등록 및 로그인을 설정합니다.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 04/30/2021
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 51639df9115032beefd04e6eb114c0d62e61ed88
ms.sourcegitcommit: 02d443532c4d2e9e449025908a05fb9c84eba039
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/06/2021
ms.locfileid: "122642093"
---
# <a name="set-up-sign-up-and-sign-in-with-openid-connect-using-azure-active-directory-b2c"></a>Azure Active Directory B2C를 사용하여 OpenID Connect를 통한 등록 및 로그인 설정

[OpenID Connect](openid-connect.md)는 보안 사용자 로그인에 사용할 수 있는 OAuth 2.0을 기반으로 빌드된 인증 프로토콜입니다. 이 프로토콜을 사용하는 대부분의 ID 공급자는 Azure AD B2C에서 지원됩니다. 

이 문서에서는 사용자 지정 OpenID Connect ID 공급 기업을 사용자 흐름에 추가하는 방법을 설명합니다.

[!INCLUDE [active-directory-b2c-https-cipher-tls-requirements](../../includes/active-directory-b2c-https-cipher-tls-requirements.md)]

## <a name="add-the-identity-provider"></a>ID 공급자 추가

1. Azure AD B2C 테넌트의 전역 관리자로 [Azure Portal](https://portal.azure.com/)에 로그인합니다.
1. 위쪽 메뉴에서 **디렉터리 + 구독** 필터를 클릭하고 테넌트가 포함된 디렉터리를 선택하여 Azure AD B2C 테넌트가 포함된 디렉터리를 사용하고 있는지 확인합니다.
1. Azure Portal의 왼쪽 상단 모서리에서 **모든 서비스** 를 선택하고 **Azure AD B2C** 를 검색하여 선택합니다.
1. **ID 공급자** 를 선택한 다음, **새 OpenID Connect 공급자** 를 선택합니다.
1. **이름** 을 입력합니다. 예를 들어 *Contoso* 를 입력합니다.

## <a name="configure-the-identity-provider"></a>ID 공급자 구성

모든 OpenID Connect ID 공급자는 로그인을 수행하는 데 필요한 대부분의 정보를 포함하는 메타데이터 문서를 설명합니다. 여기에는 사용할 URL, 서비스의 공개 서명 키의 위치 등과 같은 정보가 포함됩니다. OpenID Connect 메타데이터 문서는 항상 `.well-known/openid-configuration`으로 끝나는 엔드포인트에 있습니다. 추가하려는 OpenID Connect ID 공급자의 경우 해당 메타데이터 URL을 입력합니다.

## <a name="client-id-and-secret"></a>클라이언트 ID 및 비밀

사용자가 로그인할 수 있도록 허용하기 위해 ID 공급자는 개발자가 해당 서비스에 애플리케이션을 등록하도록 요청합니다. 이 애플리케이션에는 ID(**클라이언트 ID** 라고 함) 및 **클라이언트 비밀** 이 있습니다. ID 공급자에서 이러한 값을 복사하여 해당하는 필드에 입력합니다.

> [!NOTE]
> 클라이언트 비밀은 선택 사항입니다. 하지만 비밀을 사용하여 토큰에 대한 코드를 교환하는 [권한 부여 코드 흐름](https://openid.net/specs/openid-connect-core-1_0.html#CodeFlowAuth)을 사용하려는 경우에는 클라이언트 암호를 입력해야 합니다.

## <a name="scope"></a>범위

범위는 ID 공급자로부터 수집하려는 정보 및 권한을 정의합니다(예: `openid profile`). ID 공급자로부터 ID 토큰을 받으려면 `openid` 범위를 지정해야 합니다. 사용자는 ID 토큰이 없으면 사용자 지정 ID 공급자를 사용하여 Azure AD B2C에 로그인할 수 없습니다. 다른 범위를 공백으로 구분하여 추가할 수 있습니다. 사용할 수 있는 다른 범위를 확인하려면 사용자 지정 ID 공급자의 설명서를 참조하세요.

## <a name="response-type"></a>응답 형식

응답 유형은 초기 호출 시 사용자 지정 ID 공급자의 `authorization_endpoint`로 반송되는 정보의 종류를 설명합니다. 다음과 같은 응답 유형을 사용할 수 있습니다.

* `code`: [권한 부여 코드 흐름](https://openid.net/specs/openid-connect-core-1_0.html#CodeFlowAuth)에 따라 코드가 Azure AD B2C로 다시 반환됩니다. 그러면 Azure AD B2C는 `token_endpoint` 호출을 진행하여 토큰용 코드를 교환합니다.
* `id_token`: ID 토큰이 사용자 지정 ID 공급자에서 Azure AD B2C로 다시 반환됩니다.

## <a name="response-mode"></a>응답 모드

응답 모드는 사용자 지정 ID 공급자에서 Azure AD B2C로 데이터를 다시 보내는 데 사용해야 하는 메서드를 정의합니다. 다음 응답 모드를 사용할 수 있습니다.

* `form_post`: 최상의 보안을 위해 이 응답 모드를 사용하는 것이 좋습니다. 응답이 `application/x-www-form-urlencoded` 형식을 사용하여 본문에서 인코딩되는 코드 또는 토큰으로 HTTP `POST` 메서드를 통해 전송됩니다.
* `query`: 코드 또는 토큰이 쿼리 매개 변수로 반환됩니다.

## <a name="domain-hint"></a>도메인 힌트

도메인 힌트를 사용하면 사용자가 사용할 수 있는 ID 공급자의 목록 중에 선택하는 대신 지정된 ID 공급자의 로그인 페이지로 바로 건너뛸 수 있습니다. 이러한 종류의 동작을 허용하려면 도메인 힌트에 대한 값을 입력합니다. 사용자 지정 ID 공급자로 점프하려면 로그인을 위해 Azure AD B2C를 호출할 때 매개 변수 `domain_hint=<domain hint value>`를 사용자 요청의 끝에 추가합니다.

## <a name="claims-mapping"></a>클레임 매핑

사용자 지정 ID가 ID 토큰을 Azure AD B2C로 다시 보낸 후, Azure AD B2C는 수신된 토큰의 클레임을 Azure AD B2C가 인식 및 사용하는 클레임으로 매핑할 수 있어야 합니다. 아래 각 매핑에 대해 ID 공급자 토큰에서 다시 반환되는 클레임을 파악하려면 사용자 지정 ID 공급자의 설명서를 참조하세요.

* **사용자 ID**: 로그인한 사용자에 대한 *고유 식별자* 를 제공하는 클레임을 입력합니다.
* **표시 이름**: 사용자에 대한 *표시 이름* 또는 *전체 이름* 을 제공하는 클레임을 입력합니다.
* **지정된 이름**: 사용자의 *이름* 을 제공하는 클레임을 입력합니다.
* **Surname(성)** : 사용자의 *성* 을 제공하는 클레임을 입력합니다.
* **이메일**: 사용자의 *이메일 주소* 를 제공하는 클레임을 입력합니다.

## <a name="add-the-identity-provider-to-a-user-flow"></a>사용자 흐름에 ID 공급자 추가 

1. Azure AD B2C 테넌트에서 **사용자 흐름** 을 선택합니다.
1. ID 공급자를 추가할 사용자 흐름을 클릭합니다. 
1. **소셜 ID 공급자** 에서 추가한 ID 공급자를 선택합니다. 예를 들면 *Contoso* 입니다.
1. **저장** 을 선택합니다.
1. 정책을 테스트하려면 **사용자 흐름 실행** 을 선택합니다.
1. **애플리케이션** 의 경우 이전에 등록한 *testapp1* 이라는 웹 애플리케이션을 선택합니다. **회신 URL** 에는 `https://jwt.ms`가 표시되어야 합니다.
1. **사용자 흐름 실행** 단추를 선택합니다.
1. 등록 또는 로그인 페이지에서 로그인할 ID 공급자를 선택합니다. 예를 들면 *Contoso* 입니다.

로그인 프로세스가 성공하면 브라우저가 `https://jwt.ms`로 리디렉션됩니다. 이는 Azure AD B2C에서 반환된 토큰의 내용을 표시합니다.
