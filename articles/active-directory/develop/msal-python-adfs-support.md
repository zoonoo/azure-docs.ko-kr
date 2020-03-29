---
title: Azure AD FS 지원(MSAL 파이썬)
titleSuffix: Microsoft identity platform
description: 파이썬용 Microsoft 인증 라이브러리에서 Active Directory 페더레이션 서비스(AD FS) 지원에 대해 알아보기
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
ms.custom: aaddev
ms.openlocfilehash: 01d4cb626aabc83117e864b75b49eec63a6c0af0
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/27/2020
ms.locfileid: "76699549"
---
# <a name="active-directory-federation-services-support-in-msal-for-python"></a>파이썬에 대한 MSAL에서 활성 디렉토리 페더레이션 서비스 지원

Windows 서버의 Active Directory 페더레이션 서비스(AD FS)를 사용하면 파이썬용 MSAL(Microsoft 인증 라이브러리)을 사용하여 앱에 OpenID Connect 및 OAuth 2.0 기반 인증 및 인증을 추가할 수 있습니다. 파이썬용 MSAL 라이브러리를 사용하여 앱은 AD FS에 대해 사용자를 직접 인증할 수 있습니다. 시나리오에 대한 자세한 내용은 [개발자를 위한 AD FS 시나리오를](/windows-server/identity/ad-fs/ad-fs-development)참조하십시오.

일반적으로 AD FS에 대해 인증하는 방법에는 두 가지가 있습니다.

- MSAL 파이썬은 Azure Active Directory와 통신하며, 이 디렉터리 자체는 다른 ID 공급자와 페더레이션됩니다. 페더레이션은 AD FS를 통해 발생합니다. MSAL Python은 Azure AD(관리되는 사용자)에서 관리되는 사용자 또는 AD FS(페더레이션된 사용자)와 같은 다른 ID 공급자가 관리하는 사용자에 서명하는 Azure AD에 연결합니다. MSAL 파이썬은 사용자가 페더레이션되어 있다는 것을 알지 못합니다. 그것은 단순히 Azure AD에 이야기합니다. 이 경우 사용하는 [권한은](msal-client-application-configuration.md#authority) 일반적인 권한(권한 호스트 이름 + 테넌트, 공통 또는 조직)입니다.
- MSAL 파이썬은 AD FS 당국과 직접 대화합니다. 이는 AD FS 2019 이상에서만 지원됩니다.

## <a name="connect-to-active-directory-federated-with-ad-fs"></a>AD FS로 페더레이션된 Active Directory에 연결

### <a name="acquire-a-token-interactively-for-a-federated-user"></a>페더레이션된 사용자를 위해 대화식으로 토큰 획득

다음은 AD FS(활성 디렉터리 페더레이션 서비스)에 직접 연결하든 Active Directory를 통해 연결하든 관계없이 적용됩니다.

호출하거나 `acquire_token_by_authorization_code` `acquire_token_by_device_flow`' 사용자 환경은 일반적으로 다음과 같습니다.

1. 사용자가 계정 ID를 입력합니다.
2. Azure AD는 "조직의 페이지로 이동"이라는 메시지를 간략하게 표시하고 사용자가 ID 공급자의 로그인 페이지로 리디렉션됩니다. 로그인 페이지는 일반적으로 조직의 로고로 사용자 지정됩니다.

이 페더레이션 시나리오에서 지원되는 AD FS 버전은 다음과 같습니다.
- Active 디렉토리 페더레이션 서비스 FS v2
- Active Directory 페더레이션 서비스 v3(Windows 서버 2012 R2)
- 활성 디렉토리 페더레이션 서비스 v4 (AD FS 2016)

### <a name="acquire-a-token-via-username-and-password"></a>사용자 이름과 암호를 통해 토큰 획득

다음은 AD FS(활성 디렉터리 페더레이션 서비스)에 직접 연결하든 Active Directory를 통해 연결하든 관계없이 적용됩니다.

을 사용하여 `acquire_token_by_username_password`토큰을 획득하면 MSAL Python은 사용자 이름을 기반으로 ID 공급자가 연락하도록 가져옵니다. MSAL Python은 ID 공급자로부터 [SAML 1.1 토큰을](reference-saml-tokens.md) 가져옵니다.

## <a name="connecting-directly-to-ad-fs"></a>AD FS에 직접 연결

디렉터리를 AD FS에 연결하면 응용 프로그램을 빌드하는 데 사용할 권한은 다음과 같습니다.`https://somesite.contoso.com/adfs/`

MSAL 파이썬은 ADFS 2019를 지원합니다.

ADFS 2016 또는 ADFS v2에 대한 직접 연결을 지원하지 않습니다. ADFS 2016에 직접 연결해야 하는 시나리오를 지원해야 하는 경우 최신 버전의 ADAL 파이썬을 사용하십시오. 온-프레미스 시스템을 ADFS 2019로 업그레이드한 후에는 MSAL 파이썬을 사용할 수 있습니다.

## <a name="next-steps"></a>다음 단계

- 페더레이션된 사례의 경우 [홈 Realm 검색 정책을 사용하여 응용 프로그램에 대한 Azure Active Directory 로그인 동작 구성을](../manage-apps/configure-authentication-for-federated-users-portal.md) 참조하십시오.