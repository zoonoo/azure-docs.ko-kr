---
title: .NET 용 Microsoft 인증 라이브러리의 AD FS 지원 | Azure
description: Active Directory Federation Services (AD FS) 지원에에서 대 한 Microsoft 인증 라이브러리.NET (MSAL.NET)에 대해 알아봅니다.
services: active-directory
documentationcenter: dev-center-name
author: rwike77
manager: CelesteDG
editor: ''
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 06/03/2019
ms.author: jmprieur
ms.reviewer: saeeda
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: e5963c5e83b5af3848edd934328caa1f095bd184
ms.sourcegitcommit: adb6c981eba06f3b258b697251d7f87489a5da33
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/04/2019
ms.locfileid: "66676730"
---
# <a name="active-directory-federation-services-support-in-msalnet"></a>Active Directory Federation Services MSAL.NET 지원
Active Directory Federation Services (AD FS) Windows server에서를 사용 하면 OpenID Connect를 추가할 수 있습니다 하 고 OAuth 2.0 인증 기반 응용 프로그램을 개발 하는 하 고 해당 응용 프로그램에 권한 부여는 AD FS에 대해 직접 사용자를 인증 합니다. 자세한 내용은 [개발자를 위한 AD FS 시나리오](/windows-server/identity/ad-fs/overview/ad-fs-scenarios-for-developers)합니다.

Microsoft 인증 라이브러리.NET (MSAL.NET)에 대 한 AD FS에 대해 인증 하기 위한 두 가지 시나리오를 지원 합니다.

- Azure Active directory, MSAL.NET 설명 됩니다 *페더레이션* AD FS를 사용 합니다.
- MSAL.NET 토크 *직접* 을 AD FS 버전에는 OpenID Connect (AD FS 2019부터) 규정을 준수 하는 위치, AD FS 권한. 실행 되는 앱을 사용 하 여 인증 하는 MSAL.NET을 사용 하면 AD FS에 직접 연결 [Azure Stack](https://azure.microsoft.com/overview/azure-stack/)합니다.

## <a name="msal-connects-to-azure-ad-which-is-federated-with-ad-fs"></a>MSAL이 AD FS와 페더레이션 되는 Azure AD에 연결
MSAL.NET (사용자를 Azure AD에서 관리 되는) 관리-사용자가 로그인 또는 사용자 (사용자가 AD FS와 같은 다른 id 공급자에서 관리)를 페더레이션 하는 Azure AD에 연결을 지원 합니다. 사용자가 페더레이션 되는 팩트에 대 한 MSAL.NET을 인식 하지 못합니다. 와 관련 된 것으로 Azure AD에 설명 합니다.

합니다 [기관](msal-client-application-configuration.md#authority) 있습니다이 경우에 사용 하는 일반적인 기관 (인증 기관 호스트 이름 + 테 넌 트, 공통 된 또는 조직).

### <a name="acquiring-a-token-interactively"></a>대화형 토큰 획득
호출 하는 경우는 `AcquireTokenInteractive` 메서드를 사용자 환경은 일반적으로:

1. 사용자가 계정 ID 입력
2. Azure AD는 간단 하 게 "페이지로 이동 하 여 조직의" 메시지를 표시 합니다.
3. 사용자가 id 공급자의 로그인 페이지로 리디렉션됩니다. 조직 로고를 사용 하 여 로그인 페이지 사용자 지정 일반적으로 됩니다.

이 페더레이션된 시나리오에서 지원 되는 AD FS 버전은 AD FS v2, AD FS v3 (Windows Server 2012 R2) 및 AD FS v4 (AD FS 2016).

### <a name="acquiring-a-token-using-acquiretokenbyintegratedauthentication-or-acquiretokenbyusernamepassword"></a>AcquireTokenByIntegratedAuthentication 또는 AcquireTokenByUsernamePassword를 사용 하 여 토큰 가져오기
사용 하 여 토큰을 획득 하는 경우는 `AcquireTokenByIntegratedAuthentication` 또는 `AcquireTokenByUsernamePassword` 메서드 MSAL.NET 가져옵니다는 id 공급자에 연결할 사용자 이름에 기반 합니다.  MSAL.NET 수신를 [SAML 1.1 토큰](reference-saml-tokens.md) id 공급자에 연결 합니다.  MSAL.NET 사용자 어설션으로 Azure AD에 SAML 토큰을 제공 (비슷합니다는 [-대신-의 흐름](msal-authentication-flows.md#on-behalf-of)) JWT 돌아옵니다.

## <a name="msal-connects-directly-to-ad-fs"></a>MSAL은 AD FS에 직접 연결
MSAL.NET Open ID Connect 호환 되는 AD FS 2019에 연결을 지원 합니다. AD FS에 직접 연결 하는 경우 응용 프로그램 빌드를 사용 하는 것이 좋습니다 기관 비슷합니다 `https://mysite.contoso.com/adfs/`합니다.

현재 AD FS 2016에서 AD FS v2에 직접 연결을 지원 하기 위해 (하지 않는 OpenID Connect 규격) 계획이 없는 합니다. AD FS 2016에 직접 연결을 요구 하는 시나리오를 지원 해야 하는 경우 최신 버전의를 사용 하세요 [Azure Active Directory 인증 라이브러리](active-directory-authentication-libraries.md#microsoft-supported-client-libraries)합니다. AD FS 2019에 온-프레미스 시스템을 업그레이드 하는 경우 MSAL.NET을 사용할 수 있습니다.
