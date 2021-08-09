---
title: MSAL.NET의 AD FS 지원 | Azure
titleSuffix: Microsoft identity platform
description: .NET용 Microsoft 인증 라이브러리(MSAL.NET)의 Active Directory Federation Services(AD FS) 지원에 대해 알아봅니다.
services: active-directory
author: mmacy
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 07/16/2019
ms.author: marsma
ms.reviewer: saeeda
ms.custom: devx-track-csharp, aaddev
ms.openlocfilehash: a146b310e6056954ac2655ff2fd99e1e3d7c694f
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/29/2021
ms.locfileid: "98063640"
---
# <a name="active-directory-federation-services-support-in-msalnet"></a>MSAL.NET의 Active Directory Federation Services 지원
Windows Server에서 Active Directory Federation Services(AD FS)를 사용하면 개발 중인 애플리케이션에 OpenID Connect 및 OAuth 2.0 기반의 인증과 권한 부여를 추가할 수 있습니다. 이러한 애플리케이션은 AD FS에 대해 직접 사용자를 인증할 수 있습니다. 자세한 내용은 [개발자를 위한 AD FS 시나리오](/windows-server/identity/ad-fs/overview/ad-fs-openid-connect-oauth-flows-scenarios)를 참조하세요.

.NET용 Microsoft 인증 라이브러리(MSAL.NET)는 AD FS에 대해 인증하는 두 가지 시나리오를 지원합니다.

- MSAL.NET은 AD FS와 *페더레이션되는* Azure Active Directory와 통신합니다.
- MSAL.NET은 ADFS 기관과 **직접** 통신합니다. 이는 AD FS 2019 이상에서만 지원됩니다. 여기서 강조하는 시나리오 중 하나는 [Azure Stack](https://azure.microsoft.com/overview/azure-stack/) 지원입니다.


## <a name="msal-connects-to-azure-ad-which-is-federated-with-ad-fs"></a>MSAL은 AD FS와 페더레이션되는 Azure AD에 연결합니다.
MSAL.NET은 관리 사용자(Azure AD에서 관리되는 사용자) 또는 페더레이션 사용자(AD FS와 같은 다른 ID 공급자로 관리되는 사용자)를 로그인시키는 Azure AD에 대한 연결을 지원합니다. MSAL.NET은 사용자가 페더레이션되었다는 사실을 알지 못합니다. 이에 관한 한 Azure AD와 통신합니다.

이 경우 사용하는 [기관](msal-client-application-configuration.md#authority)은 일반적인 기관(기관 호스트 이름 + 테넌트, 공용 또는 조직)입니다.

### <a name="acquiring-a-token-interactively"></a>대화형으로 토큰 획득하기
`AcquireTokenInteractive` 메서드를 호출하는 경우 사용자 환경은 일반적으로 다음과 같습니다.

1. 사용자가 자신의 계정 ID를 입력합니다.
2. Azure AD는 “조직 페이지로 이동”이라는 메시지를 간략하게 표시합니다.
3. 사용자가 ID 공급자의 로그인 페이지로 리디렉션됩니다. 로그인 페이지는 일반적으로 조직의 로고를 사용하여 사용자 지정됩니다.

이 페더레이션된 시나리오에서 지원되는 AD FS 버전은 AD FS v2, AD FS v3(Windows Server 2012 R2), AD FS v4(AD FS 2016)입니다.

### <a name="acquiring-a-token-using-acquiretokenbyintegratedauthentication-or-acquiretokenbyusernamepassword"></a>AcquireTokenByIntegratedAuthentication 또는 AcquireTokenByUsernamePassword를 사용하여 토큰 획득하기
`AcquireTokenByIntegratedAuthentication` 또는 `AcquireTokenByUsernamePassword` 메서드를 사용하여 토큰을 획득하는 경우 MSAL.NET은 사용자 이름을 기준으로 연락할 ID 공급자를 가져옵니다.  MSAL.NET은 ID 공급자에 연락한 후 [SAML 1.1 토큰](reference-saml-tokens.md)을 수신합니다.  그런 다음, MSAL.NET은 JWT를 가져오기 위해 Azure AD에 사용자 어설션으로 SAML 토큰을 제공합니다([On-Behalf-Of 흐름](msal-authentication-flows.md#on-behalf-of)과 유사).

## <a name="msal-connects-directly-to-ad-fs"></a>MSAL은 AD FS에 직접 연결됩니다.
MSAL.NET은 Open ID Connect 규격이며 PKCE 및 범위를 이해하는 AD FS 2019에 대한 연결을 지원합니다. 이 지원을 사용하려면 서비스 팩 [KB 4490481](https://support.microsoft.com/en-us/help/4490481/windows-10-update-kb4490481)이 Windows Server에 적용되어야 합니다. AD FS에 직접 연결하는 경우 애플리케이션을 빌드하는 데 사용하려는 기관은 `https://mysite.contoso.com/adfs/`와 비슷합니다.

현재 다음에 대한 직접 연결을 지원할 계획이 없습니다.

- PKCE를 지원하지 않고 범위가 아니는 리소스를 계속 사용하는 AD FS 16
- OIDC 규격이 아닌 AD FS v2

 AD FS 2016에 대한 직접 연결을 요구하는 시나리오를 지원해야 하는 경우 [Azure Active Directory 인증 라이브러리](../azuread-dev/active-directory-authentication-libraries.md#microsoft-supported-client-libraries)의 최신 버전을 사용합니다. 온-프레미스 시스템을 AD FS 2019로 업그레이드한 경우 MSAL.NET을 사용할 수 있습니다.

## <a name="next-steps"></a>다음 단계

페더레이션된 사례는 [홈 영역 검색 정책을 사용하여 애플리케이션에 대한 Azure Active Directory 로그인 동작 구성](../manage-apps/configure-authentication-for-federated-users-portal.md)을 참조하세요.