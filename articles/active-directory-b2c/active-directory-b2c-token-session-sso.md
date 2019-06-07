---
title: 세션 및 single sign-on 구성-Azure Active Directory B2C | Microsoft Docs
description: 세션 및 Azure Active Directory B2C에서 single sign-on 구성 합니다.
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 04/16/2019
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: 472e494d445ae4a22c988076994649543ec1f7f8
ms.sourcegitcommit: adb6c981eba06f3b258b697251d7f87489a5da33
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/04/2019
ms.locfileid: "66508111"
---
# <a name="session-and-single-sign-on-configuration-in-azure-active-directory-b2c"></a>세션 및 Azure Active Directory B2C에서 single sign-on 구성

이 기능은 다음의 [사용자 흐름별](active-directory-b2c-reference-policies.md) 세분화된 제어를 제공합니다.

- Azure AD B2C에서 관리하는 웹 애플리케이션 세션의 수명.
- Azure AD B2C 테넌트에 있는 여러 앱 및 사용자 흐름의 SSO(Single Sign-On) 동작

## <a name="session-behavior"></a>세션 동작

Azure AD B2C는 웹 애플리케이션에 대한 보안 로그인을 활성화하도록 [OpenID Connect 인증 프로토콜](active-directory-b2c-reference-oidc.md)을 지원합니다. 다음 속성을 사용하여 웹 애플리케이션 세션을 관리할 수 있습니다.

- **웹앱 세션 수명(분)** - 인증 성공 시 사용자의 브라우저에 저장된 Azure AD B2C 세션 쿠키의 수명입니다.
    - 기본값: 1440분.
    - 최소(포함) = 15분.
    - 최대(포함) = 1440분.
- **웹앱 세션 제한 시간** - 이 스위치가 **절대**로 설정된 경우 사용자는 **웹앱 세션 수명(분)** 에서 지정한 기간이 경과된 후 강제로 다시 인증합니다. 이 스위치가 **롤링** (기본 설정)으로 설정된 경우 사용자는 웹 애플리케이션에서 지속적으로 활성 상태인 한 로그인 상태로 유지됩니다.

다음 사용 사례는 이러한 속성을 사용하여 사용하도록 설정됩니다.

- 적절한 웹 애플리케이션 세션 수명을 설정하여 업계의 보안 및 규정 준수 요구 사항을 충족합니다.
- 사용자가 웹 애플리케이션의 높은 수준의 보안 일부와 상호 작용하는 동안 설정 기간 후 강제로 인증합니다. 

이러한 설정을 암호 재설정 사용자 흐름에는 사용할 수 없습니다.

## <a name="single-sign-on-sso-configuration"></a>SSO(Single Sign-On) 구성

B2C 테넌트에 여러 애플리케이션 및 사용자 흐름이 있는 경우 **Single Sign-On 구성** 속성을 사용하여 이들 간에 사용자 상호 작용을 관리할 수 있습니다. 다음 설정 중 하나에 속성을 설정할 수 있습니다.

- **테넌트** - 이 설정은 기본값입니다. 이 설정을 사용하여 B2C 테넌트의 여러 애플리케이션 및 사용자 흐름이 동일한 사용자 세션을 공유할 수 있습니다. 예를 들어 사용자가 애플리케이션에 로그인하면 액세스의 관점에서 볼 때 다른 앱인 Contoso Pharmacy에도 원활하게 로그인할 수 있습니다.
- **애플리케이션** - 이 설정을 통해 다른 애플리케이션과 독립적으로 애플리케이션에 대한 독점적인 사용자 세션을 유지할 수 있습니다. 예를 들어 동일한 B2C 테넌트에 있는 다른 애플리케이션인 Contoso Shopping에 이미 로그인한 경우라 하더라도 (동일한 자격 증명을 사용하여) 사용자가 Contoso Pharmacy에 로그인하려는 경우가 있습니다. 
- **정책** - 이 설정을 통해 이를 사용하는 애플리케이션과 독립적으로 사용자 흐름에 대한 독점적인 사용자 세션을 유지할 수 있습니다. 예를 들어 사용자가 이미 로그인하고 MFA(다단계 인증) 단계를 완료한 경우 사용자 흐름에 연결된 세션이 만료되지 않는 한 여러 애플리케이션의 높은 수준의 보안 부분에 대한 액세스를 부여 받을 수 있습니다.
- **사용 안 함** - 사용자가 모든 정책 실행에서 전체 사용자 흐름을 통해 강제로 실행하도록 합니다.

이러한 설정을 암호 재설정 사용자 흐름에는 사용할 수 없습니다. 

