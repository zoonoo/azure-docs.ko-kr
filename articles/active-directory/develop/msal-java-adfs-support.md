---
title: AD FS 지원(Java용 MSAL)
titleSuffix: Microsoft identity platform
description: Java용 Microsoft 인증 라이브러리(MSAL4j)에서 AD FS(활성 디렉터리 페더레이션 서비스) 지원에 대해 알아봅니다.
services: active-directory
author: sangonzal
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 11/21/2019
ms.author: sagonzal
ms.reviewer: nacanuma
ms.custom: aaddev
ms.openlocfilehash: 3d834a8d1524595304c22fed9897094622dfd93f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/27/2020
ms.locfileid: "76696217"
---
# <a name="active-directory-federation-services-support-in-msal-for-java"></a>JAVA용 MSAL에서 활성 디렉토리 페더레이션 서비스 지원

Windows 서버의 Active Directory 페더레이션 서비스(AD FS)를 사용하면 Java용 MSAL(Java용 MSAL) 앱에 OpenID Connect 및 OAuth 2.0 기반 인증 및 인증을 추가할 수 있습니다. 통합되면 앱은 Azure AD를 통해 페더레이션된 AD FS의 사용자를 인증할 수 있습니다. 시나리오에 대한 자세한 내용은 [개발자를 위한 AD FS 시나리오를](/windows-server/identity/ad-fs/ad-fs-development)참조하십시오.

Java에 MSAL을 사용하는 앱은 Azure Active Directory(Azure AD)와 대화한 다음 AD FS로 페더레이션합니다.

Java용 MSAL은 Azure AD(관리되는 사용자)에서 관리되는 사용자 또는 AD FS(페더레이션된 사용자)와 같은 다른 ID 공급자가 관리하는 사용자에 서명하는 Azure AD에 연결합니다. Java용 MSAL은 사용자가 페더레이션되는 것을 알지 못합니다. 그것은 단순히 Azure AD에 이야기합니다.

이 경우 사용하는 [권한은](msal-client-application-configuration.md#authority) 일반적인 권한(권한 호스트 이름 + 테넌트, 공통 또는 조직)입니다.

## <a name="acquire-a-token-interactively-for-a-federated-user"></a>페더레이션된 사용자를 위해 대화식으로 토큰 획득

사용자가 `ConfidentialClientApplication.AcquireToken()` 전화하거나 `PublicClientApplication.AcquireToken()` `AuthorizationCodeParameters` `DeviceCodeParameters`또는 을 사용할 때 사용자 환경은 일반적으로 다음과 같은 것입니다.

1. 사용자가 계정 ID를 입력합니다.
2. Azure AD는 "조직의 페이지로 이동"을 간략하게 표시하고 사용자는 ID 공급자의 로그인 페이지로 리디렉션됩니다. 로그인 페이지는 일반적으로 조직의 로고로 사용자 지정됩니다.

이 페더레이션 시나리오에서 지원되는 AD FS 버전은 다음과 같습니다.
- Active 디렉토리 페더레이션 서비스 FS v2
- Active Directory 페더레이션 서비스 v3(Windows 서버 2012 R2)
- 활성 디렉토리 페더레이션 서비스 v4 (AD FS 2016)

## <a name="acquire-a-token-via-username-and-password"></a>사용자 이름과 암호를 통해 토큰 획득

Java용 `ConfidentialClientApplication.AcquireToken()` 또는 `PublicClientApplication.AcquireToken()` 또는 를 `IntegratedWindowsAuthenticationParameters` `UsernamePasswordParameters`사용하여 또는 또는 , MSAL을 사용하여 토큰을 획득하면 ID 공급자가 사용자 이름을 기반으로 연락하도록 됩니다. Java용 MSAL은 ID 공급자로부터 [SAML 1.1 토큰토큰을](reference-saml-tokens.md) 가져옵니다.

## <a name="next-steps"></a>다음 단계

페더레이션된 사례의 경우 [홈 Realm 검색 정책을 사용하여 응용 프로그램에 대한 Azure Active Directory 로그인 동작 구성을](https://docs.microsoft.com/azure/active-directory/manage-apps/configure-authentication-for-federated-users-portal) 참조하십시오.