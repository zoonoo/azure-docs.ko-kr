---
title: MSAL.NET AD FS 지원 | Azure
titleSuffix: Microsoft identity platform
description: .NET(MSAL.NET)에 대한 Microsoft 인증 라이브러리에서 AD FS(활성 디렉터리 페더레이션 서비스) 지원에 대해 알아봅니다.
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
ms.custom: aaddev
ms.openlocfilehash: 15af18177cea217612a4d5276d130abe02d339f4
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/27/2020
ms.locfileid: "77160763"
---
# <a name="active-directory-federation-services-support-in-msalnet"></a>MSAL.NET 활성 디렉토리 페더레이션 서비스 지원
Windows 서버의 AD FS(Active Directory Federation Services)를 사용하면 개발 중인 응용 프로그램에 OpenID Connect 및 OAuth 2.0 기반 인증 및 권한 부여를 추가할 수 있습니다. 그런 다음 이러한 응용 프로그램은 AD FS에 대해 사용자를 직접 인증할 수 있습니다. 자세한 내용은 [개발자를 위한 AD FS 시나리오를](/windows-server/identity/ad-fs/overview/ad-fs-openid-connect-oauth-flows-scenarios)참조하십시오.

.NET(MSAL.NET)에 대한 Microsoft 인증 라이브러리는 AD FS에 대해 인증하기 위한 두 가지 시나리오를 지원합니다.

- MSAL.NET AD FS와 함께 *페더레이션되는* Azure Active Directory에 대해 이야기합니다.
- MSAL.NET ADFS 당국과 **직접** 대화를 나눈다. 이는 AD FS 2019 이상에서만 지원됩니다. 이 주요 시나리오 중 하나는 [Azure 스택](https://azure.microsoft.com/overview/azure-stack/) 지원


## <a name="msal-connects-to-azure-ad-which-is-federated-with-ad-fs"></a>MSAL은 AD FS와 연합되는 Azure AD에 연결됩니다.
MSAL.NET 관리 되는 사용자 (Azure AD에서 관리 되는 사용자) 또는 페더레이션 된 사용자 (AD FS와 같은 다른 ID 공급자에 의해 관리 되는 사용자)에 서명 하는 Azure AD에 연결 지원 합니다. MSAL.NET 사용자가 페더레이션된다는 사실에 대해 알지 못합니다. 이에 관한 한 Azure AD와 관련이 있습니다.

이 경우 사용하는 [권한은](msal-client-application-configuration.md#authority) 일반적인 권한(권한 호스트 이름 + 테넌트, 공통 또는 조직)입니다.

### <a name="acquiring-a-token-interactively"></a>대화식으로 토큰 획득
메서드를 `AcquireTokenInteractive` 호출할 때 사용자 환경은 일반적으로 다음과 같은 것입니다.

1. 사용자가 계정 ID를 입력합니다.
2. Azure AD에는 "조직의 페이지로 이동"이라는 메시지가 간략하게 표시됩니다.
3. 사용자가 ID 공급자의 로그인 페이지로 리디렉션됩니다. 로그인 페이지는 일반적으로 조직의 로고로 사용자 지정됩니다.

이 페더레이션 시나리오에서 지원되는 AD FS 버전은 AD FS v2, AD FS v3(Windows 서버 2012 R2) 및 AD FS v4(AD FS 2016)입니다.

### <a name="acquiring-a-token-using-acquiretokenbyintegratedauthentication-or-acquiretokenbyusernamepassword"></a>AcquireTokenBy통합 인증 또는 acquireTokenBy사용자 이름 암호를 사용하여 토큰 획득
`AcquireTokenByIntegratedAuthentication` 또는 `AcquireTokenByUsernamePassword` 메서드를 사용하여 토큰을 획득할 때 MSAL.NET ID 공급자가 사용자 이름을 기반으로 연락하도록 합니다.  MSAL.NET ID 공급자에게 문의한 후 [SAML 1.1 토큰을](reference-saml-tokens.md) 받습니다.  그런 다음 MSAL.NET Azure AD에 JWT를 다시 복구하기 위해 사용자 어설션(흐름을 [대신하는](msal-authentication-flows.md#on-behalf-of)것과 유사)으로 SAML 토큰을 제공합니다.

## <a name="msal-connects-directly-to-ad-fs"></a>MSAL은 AD FS에 직접 연결됩니다.
MSAL.NET 오픈 ID 커넥트(Open ID Connect)를 준수하고 PKCE와 범위를 이해하는 AD FS 2019에 접속할 수 있도록 지원합니다. 이 지원을 위해서는 서비스 팩 [KB 4490481이](https://support.microsoft.com/en-us/help/4490481/windows-10-update-kb4490481) Windows 서버에 적용되어야 합니다. AD FS에 직접 연결할 때 응용 프로그램을 빌드하는 데 사용할 `https://mysite.contoso.com/adfs/`권한은 와 비슷합니다.

현재 다음과 같은 직접 연결을 지원할 계획은 없습니다.

- AD FS 16은 PKCE를 지원하지 않으며 범위가 아닌 리소스를 계속 사용하기 때문에
- AD FS v2, 이는 OIDC 를 준수하지 않습니다.

 AD FS 2016에 직접 연결해야 하는 시나리오를 지원해야 하는 경우 최신 버전의 [Azure Active Directory 인증 라이브러리를](../azuread-dev/active-directory-authentication-libraries.md#microsoft-supported-client-libraries)사용합니다. 온-프레미스 시스템을 AD FS 2019로 업그레이드하면 MSAL.NET 사용할 수 있습니다.

## <a name="next-steps"></a>다음 단계

페더레이션된 사례의 경우 [홈 Realm 검색 정책을 사용하여 응용 프로그램에 대한 Azure Active Directory 로그인 동작 구성을](https://docs.microsoft.com/azure/active-directory/manage-apps/configure-authentication-for-federated-users-portal) 참조하십시오.
