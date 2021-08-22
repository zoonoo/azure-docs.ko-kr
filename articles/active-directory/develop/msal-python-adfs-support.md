---
title: Azure AD FS 지원(MSAL Python)
titleSuffix: Microsoft identity platform
description: Python용 Microsoft 인증 라이브러리의 AD FS(Active Directory Federation Services) 지원에 대해 알아 봅니다.
services: active-directory
author: abhidnya13
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 11/23/2019
ms.author: abpati
ms.reviewer: nacanuma
ms.custom: aaddev, devx-track-python, has-adal-ref
ms.openlocfilehash: a7c8c2a00f3238c5d4963c37a89b6c7e24441076
ms.sourcegitcommit: 1deb51bc3de58afdd9871bc7d2558ee5916a3e89
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/19/2021
ms.locfileid: "122530974"
---
# <a name="active-directory-federation-services-support-in-msal-for-python"></a>Python용 MSAL에서 Active Directory Federation Services 지원

Windows Server의 AD FS(Active Directory Federation Services)를 사용하면 Python용 MSAL(Microsoft 인증 라이브러리)을 사용하여 OpenID Connect 및 OAuth 2.0 기반 인증 및 권한 부여를 앱에 추가할 수 있습니다. Python용 MSAL 라이브러리를 사용하면 앱에서 AD FS에 대해 사용자를 직접 인증할 수 있습니다. 시나리오에 대한 자세한 내용은 참조 [개발자를 위한 AD FS 시나리오](/windows-server/identity/ad-fs/ad-fs-development)를 참조하세요.

일반적으로 AD FS에 대해 인증하는 방법에는 두 가지가 있습니다.

- MSAL Python은 다른 ID 공급자와 페더레이션되는 Azure Active Directory와 통신합니다. 페더레이션은 AD FS를 통해 수행됩니다. MSAL Python은 Azure AD에서 관리되는 사용자(관리형 사용자) 또는 AD FS와 같은 다른 ID 공급자가 관리하는 사용자(페더레이션된 사용자)를 로그인하는 Azure AD에 연결합니다. MSAL Python은 사용자가 페더레이션되었음을 인식하지 못합니다. 단지 Azure AD와만 통신합니다. 이 경우 사용하는 [기관](msal-client-application-configuration.md#authority)은 일반적인 기관(기관 호스트 이름 + 테넌트, 공용 또는 조직)입니다.
- MSAL Python은 AD FS 기관과 직접 통신합니다. 이는 AD FS 2019 이상에서만 지원됩니다.

## <a name="connect-to-active-directory-federated-with-ad-fs"></a>AD FS와 페더레이션된 Active Directory에 연결

### <a name="acquire-a-token-interactively-for-a-federated-user"></a>대화형으로 페더레이션된 사용자에 대한 토큰 획득

다음은 AD FS(Active Directory Federation Services)에 직접 연결하거나 Active Directory를 통해 연결하는지 여부에 관계없이 적용됩니다.

`acquire_token_by_authorization_code` 또는 `acquire_token_by_device_flow`를 호출하는 경우 사용자 환경은 일반적으로 다음과 같습니다.

1. 사용자가 자신의 계정 ID를 입력합니다.
2. Azure AD에서 "조직의 페이지로 이동"이라는 메시지를 잠시 표시히고, 사용자는 ID 공급자의 로그인 페이지로 리디렉션됩니다. 로그인 페이지는 일반적으로 조직의 로고를 사용하여 사용자 지정됩니다.

이 페더레이션된 시나리오에서 지원되는 AD FS 버전은 다음과 같습니다.
- Active Directory Federation Services FS v2
- Active Directory Federation Services v3(Windows Server 2012 R2)
- Active Directory Federation Services v4(AD FS 2016)

### <a name="acquire-a-token-via-username-and-password"></a>사용자 이름과 암호를 통해 토큰 획득

다음은 AD FS(Active Directory Federation Services)에 직접 연결하거나 Active Directory를 통해 연결하는지 여부에 관계없이 적용됩니다.

`acquire_token_by_username_password`를 사용하여 토큰을 획득하는 경우 MSAL Python은 사용자 이름에 따라 연결할 ID 공급자를 가져옵니다. MSAL Python은 ID 공급자로부터 [SAML 1.1 토큰](reference-saml-tokens.md)을 가져온 다음, JWT(JSON Web Token)를 반환하는 Azure AD에 제공합니다.

## <a name="connecting-directly-to-ad-fs"></a>AD FS에 직접 연결

디렉터리를 AD FS에 연결하는 경우 애플리케이션을 빌드하는 데 사용하려는 기관은 `https://somesite.contoso.com/adfs/`와 같습니다.

MSAL Python은 ADFS 2019를 지원합니다.

ADFS 2016 또는 ADFS v2에 대한 직접 연결은 지원하지 않습니다. ADFS 2016에 직접 연결해야 하는 시나리오를 지원하려면 최신 버전의 ADAL Python을 사용합니다. 온-프레미스 시스템이 ADFS 2019로 업그레이드되면 MSAL Python을 사용할 수 있습니다.

## <a name="next-steps"></a>다음 단계

- 페더레이션된 사례는 [홈 영역 검색 정책을 사용하여 애플리케이션에 대한 Azure Active Directory 로그인 동작 구성](../manage-apps/configure-authentication-for-federated-users-portal.md)을 참조하세요.
