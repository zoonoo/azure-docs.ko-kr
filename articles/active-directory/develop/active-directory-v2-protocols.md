---
title: Microsoft id 플랫폼을 지 원하는 인증 프로토콜에 알아봅니다 | Azure
description: Microsoft id 플랫폼 끝점에서 지원 되는 OAuth 2.0 및 OpenID Connect 프로토콜 가이드입니다.
services: active-directory
documentationcenter: ''
author: rwike77
manager: CelesteDG
editor: ''
ms.assetid: 5fb4fa1b-8fc4-438e-b3b0-258d8c145f22
ms.service: active-directory
ms.subservice: develop
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/30/2019
ms.author: ryanwi
ms.reviewer: hirsin
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: cfc9c027806cb1a3f65e67eda771894a7250ee67
ms.sourcegitcommit: c05618a257787af6f9a2751c549c9a3634832c90
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/30/2019
ms.locfileid: "66417661"
---
# <a name="microsoft-identity-platform-protocols"></a>Microsoft id 플랫폼 프로토콜

Identity-as a service 산업 표준 프로토콜, OpenID Connect 및 OAuth 2.0을 사용 하 여에 대 한 Microsoft id 플랫폼 끝점입니다. 서비스는 표준을 준수하지만 이러한 프로토콜의 두 구현 간에는 약간의 차이가 있을 수 있습니다. 여기에 있는 정보는 [오픈 소스 라이브러리](reference-v2-libraries.md) 중 하나를 사용하는 대신 HTTP 요청을 직접 전송 및 처리하여 코드를 작성하거나 타사 오픈 소스 라이브러리를 사용하도록 선택한 경우에 유용합니다.

> [!NOTE]
> 모든 Azure AD 시나리오 및 기능 Microsoft id 플랫폼 끝점에서 지원 됩니다. Microsoft id 플랫폼 끝점을 사용 해야 하는 경우를 확인 하려면에 대해 알아보세요 [Microsoft identity 플랫폼 제한](active-directory-v2-limitations.md)합니다.

## <a name="the-basics"></a>기본 사항

거의 모든 OAuth 2.0 및 OpenID Connect 흐름에서 교환에 참여하는 다음 네 가지 요소가 있습니다.

![OAuth 2.0 역할](./media/active-directory-v2-flows/protocols-roles.svg)

* 합니다 **권한 부여 서버** Microsoft id 플랫폼 끝점 이며 사용자의 id를 확인 하는 것에 대 한 책임을 부여 하 고 리소스에 대 한 액세스 권한을 취소할 발급 토큰입니다. 권한 부여 서버는 ID 공급자라고도 하며 사용자 정보, 해당 액세스 및 흐름의 요소 간 트러스트 관계와 관련된 모든 사항을 안전하게 처리합니다.
* **리소스 소유자**는 일반적으로 최종 사용자입니다. 데이터를 소유하는 당사자이며 제3자가 해당 데이터 또는 리소스에 액세스하도록 허용할 권한이 있습니다.
* **OAuth 클라이언트**는 해당 애플리케이션 ID로 식별되는 앱입니다. 일반적으로 OAuth 클라이언트는 최종 사용자가 상호 작용하는 요소이며 권한 부여 서버의 토큰을 요청합니다. 리소스 소유자가 리소스에 액세스할 수 있는 권한을 클라이언트에 부여해야 합니다.
* **리소스 서버** 는 리소스 또는 데이터가 있는 곳입니다. 안전 하 게 인증 하 고 OAuth 클라이언트에 권한을 부여 하는 권한 부여 서버를 트러스트 하 고 전달자 액세스 토큰을 사용 하 여 리소스에 대 한 액세스를 부여할 수 있습니다 되도록 합니다.

## <a name="app-registration"></a>앱 등록

모두 개인 및 회사 또는 학교 계정에 적용 하려는 모든 앱을 통해 등록 되어야 합니다는 **앱 등록** 환경을 합니다 [Azure portal](https://aka.ms/appregistrations) OAuth 2.0을 사용 하 여 이러한 사용자를 로그인 할 수 전에 또는 OpenID Connect입니다. 앱 등록 프로세스는 몇 개의 값을 수집하고 앱에 할당합니다.

* 앱을 고유하게 식별하는 **애플리케이션 ID**
* A **리디렉션 URI** (선택 사항) 사용할 수 있는 앱에 응답을 다시 보내기 위해
* 다른 몇 가지 시나리오 관련 값.

자세한 내용은 [앱 등록](quickstart-register-app.md)방법을 참조하세요.

## <a name="endpoints"></a>엔드포인트

등록 되 면 응용 프로그램 끝점에 요청을 전송 하 여 Microsoft id 플랫폼을 사용 하 여 통신 합니다.

```
https://login.microsoftonline.com/{tenant}/oauth2/v2.0/authorize
https://login.microsoftonline.com/{tenant}/oauth2/v2.0/token
```

여기서 `{tenant}` 은 네 개의 서로 다른 값 중 하나를 가질 수 있습니다.

| Value | 설명 |
| --- | --- |
| `common` | 개인 Microsoft 계정과 Azure AD의 회사/학교 계정이 모두 있는 사용자가 애플리케이션에 로그인할 수 있습니다. |
| `organizations` | Azure AD의 회사/학교 계정이 있는 사용자만 애플리케이션에 로그인할 수 있습니다. |
| `consumers` | 개인 Microsoft 계정(MSA)이 있는 사용자만이 애플리케이션에 로그인 할 수 있습니다. |
| `8eaef023-2b34-4da1-9baa-8bc8c9d6a490` 또는 `contoso.onmicrosoft.com` | 특정 Azure AD 테넌트의 회사/학교 계정이 있는 사용자만 애플리케이션에 로그인할 수 있습니다. Azure AD 테넌트의 친숙한 도메인 이름 또는 테넌트의 GUID 식별자를 사용할 수 있습니다. |

이러한 엔드포인트와 상호 작용하는 방법을 알아보려면 [프로토콜](#protocols) 섹션에서 특정 앱 형식을 선택하고 자세한 정보 링크를 따릅니다.

> [!TIP]
> Azure AD에 등록 된 모든 앱 개인 계정에 로그인 하지 하는 경우에 Microsoft id 플랫폼 끝점을 사용할 수 있습니다.  이 이렇게 하면 Microsoft id 플랫폼에 기존 응용 프로그램을 마이그레이션할 수 있습니다 하 고 [MSAL](reference-v2-libraries.md) 응용 프로그램을 다시 만들어야만 합니다.  

## <a name="tokens"></a>토큰

OAuth 2.0 및 OpenID Connect의 Microsoft id 플랫폼 구현을 확인 Jwt로 표현 되는 전달자 토큰을 포함 하 여 전달자 토큰을 광범위 하 게 사용 합니다. 전달자 토큰은 보호된 리소스에 대한 "전달자" 액세스 권한을 부여하는 간단한 보안 토큰입니다. 이런 의미에서, "전달자"는 토큰을 제공할 수 있는 당사자입니다. 먼저 전송 및 저장에 토큰을 보호 하는 필수 단계를 취하지 않는 경우 파티 전달자 토큰을 수신 하도록 Microsoft id 플랫폼을 사용 하 여 인증 해야 합니다, 있지만 가로챌 하 고 의도 하지 않은 당사자에서 사용 될 수 있습니다. 일부 보안 토큰에는 권한 없는 당사자가 사용하는 것을 방지하는 기본 제공 메커니즘이 있지만, 전달자 토큰에는 이러한 메커니즘이 없으므로 전송 계층 보안(HTTPS)과 같은 보안 채널에서 전달자 토큰을 전송해야 합니다. 전달자 토큰이 보안되지 않은 상태로 전송되는 경우, 악의적 사용자가 메시지 가로채기(man-in-the-middle) 공격으로 토큰을 획득하고 보호된 리소스에 무단으로 액세스하는 데 이 토큰을 사용할 수 있습니다. 나중에 사용하기 위해 전달자 토큰을 저장하거나 캐싱할 때도 동일한 보안 원칙이 적용됩니다. 항상 앱이 안전한 방식으로 전달자 토큰을 전송하고 저장하도록 합니다. 전달자 토큰의 보안 고려 사항을 자세히 알아보려면 [RFC 6750 Section 5](https://tools.ietf.org/html/rfc6750)를 참조하세요.

다양 한 microsoft id 플랫폼 끝점에서 사용할 수는 사용 되는 토큰에 대 한 자세한 내용은 [Microsoft id 플랫폼 끝점 토큰 참조](v2-id-and-access-tokens.md)합니다.

## <a name="protocols"></a>프로토콜

일부 예제 요청을 확인할 준비가 되었다면 아래 자습서 중 하나를 시작합니다. 각각 특정 인증 시나리오에 해당합니다. 사용자에 게 맞는 흐름 결정을 할 경우 체크 아웃 [Microsoft id 플랫폼을 사용 하 여 빌드할 수 있는 앱 유형을](v2-app-types.md)합니다.

* [OAuth 2.0를 사용하여 모바일 및 네이티브 애플리케이션 빌드](v2-oauth2-auth-code-flow.md)
* [OpenID Connect를 사용하는 웹앱 빌드](v2-protocols-oidc.md)
* [OAuth 2.0 암시적 흐름으로 단일 페이지 앱 구축](v2-oauth2-implicit-grant-flow.md)
* [OAuth 2.0 클라이언트 자격 증명 흐름으로 디먼 또는 서버 쪽 프로세스 빌드](v2-oauth2-client-creds-grant-flow.md)
* [OAuth 2.0 On Behalf Of 흐름으로 웹 API에서 토큰 가져오기](v2-oauth2-on-behalf-of-flow.md)
