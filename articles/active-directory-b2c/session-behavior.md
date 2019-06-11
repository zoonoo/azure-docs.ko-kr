---
title: 세션 동작-Azure Active Directory B2C를 구성 합니다. | Microsoft Docs
description: Azure Active Directory B2C의 세션 동작을 구성 합니다.
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 04/16/2019
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: 3167a63be46e0d777fbec0c6a3301684fb7636bd
ms.sourcegitcommit: adb6c981eba06f3b258b697251d7f87489a5da33
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/04/2019
ms.locfileid: "66512557"
---
# <a name="configure-session-behavior-in-azure-active-directory-b2c"></a>Azure Active Directory B2C의 세션 동작을 구성 합니다.

이 기능은 다음의 [사용자 흐름별](active-directory-b2c-reference-policies.md) 세분화된 제어를 제공합니다.

- Azure AD B2C에서 관리하는 웹 애플리케이션 세션의 수명.
- Azure AD B2C 테넌트에 있는 여러 앱 및 사용자 흐름의 SSO(Single Sign-On) 동작

이러한 설정을 암호 재설정 사용자 흐름에는 사용할 수 없습니다.

Azure AD B2C는 웹 애플리케이션에 대한 보안 로그인을 활성화하도록 [OpenID Connect 인증 프로토콜](active-directory-b2c-reference-oidc.md)을 지원합니다. 다음 속성을 사용하여 웹 애플리케이션 세션을 관리할 수 있습니다.

## <a name="session-behavior-properties"></a>세션 동작 속성

- **웹앱 세션 수명(분)** - 인증 성공 시 사용자의 브라우저에 저장된 Azure AD B2C 세션 쿠키의 수명입니다.
    - 기본값: 1440분.
    - 최소(포함) = 15분.
    - 최대(포함) = 1440분.
- **웹앱 세션 제한 시간** - 이 스위치가 **절대**로 설정된 경우 사용자는 **웹앱 세션 수명(분)** 에서 지정한 기간이 경과된 후 강제로 다시 인증합니다. 이 스위치가 **롤링** (기본 설정)으로 설정된 경우 사용자는 웹 애플리케이션에서 지속적으로 활성 상태인 한 로그인 상태로 유지됩니다.
- **Single sign-on 구성** B2C 테 넌 트에서 여러 응용 프로그램 및 사용자 흐름을 있다면 이들 간에 사용자 상호 작용을 관리할 수 있습니다 사용 하는 **Single sign-on 구성** 속성입니다. 다음 설정 중 하나에 속성을 설정할 수 있습니다.
    - **테넌트** - 이 설정은 기본값입니다. 이 설정을 사용하여 B2C 테넌트의 여러 애플리케이션 및 사용자 흐름이 동일한 사용자 세션을 공유할 수 있습니다. 예를 들어 사용자가 애플리케이션에 로그인하면 액세스의 관점에서 볼 때 다른 앱인 Contoso Pharmacy에도 원활하게 로그인할 수 있습니다.
    - **애플리케이션** - 이 설정을 통해 다른 애플리케이션과 독립적으로 애플리케이션에 대한 독점적인 사용자 세션을 유지할 수 있습니다. 예를 들어 동일한 B2C 테넌트에 있는 다른 애플리케이션인 Contoso Shopping에 이미 로그인한 경우라 하더라도 (동일한 자격 증명을 사용하여) 사용자가 Contoso Pharmacy에 로그인하려는 경우가 있습니다. 
    - **정책** - 이 설정을 통해 이를 사용하는 애플리케이션과 독립적으로 사용자 흐름에 대한 독점적인 사용자 세션을 유지할 수 있습니다. 예를 들어 사용자가 이미 로그인하고 MFA(다단계 인증) 단계를 완료한 경우 사용자 흐름에 연결된 세션이 만료되지 않는 한 여러 애플리케이션의 높은 수준의 보안 부분에 대한 액세스를 부여 받을 수 있습니다.
    - **사용 안 함** - 사용자가 모든 정책 실행에서 전체 사용자 흐름을 통해 강제로 실행하도록 합니다.

다음 사용 사례는 이러한 속성을 사용하여 사용하도록 설정됩니다.

- 적절한 웹 애플리케이션 세션 수명을 설정하여 업계의 보안 및 규정 준수 요구 사항을 충족합니다.
- 사용자가 웹 애플리케이션의 높은 수준의 보안 일부와 상호 작용하는 동안 설정 기간 후 강제로 인증합니다. 

## <a name="configure-the-properties"></a>속성 구성

1. [Azure Portal](https://portal.azure.com)에 로그인합니다.
2. 맨 위 메뉴에서 **디렉터리 및 구독 필터**를 클릭하고 Azure AD B2C 테넌트가 포함된 디렉터리를 선택하여 Azure AD B2C 테넌트가 포함된 디렉터리를 사용합니다.
3. Azure Portal의 왼쪽 위에서 **모든 서비스**를 선택하고 **Azure AD B2C**를 검색하여 선택합니다.
4. 선택 **사용자 흐름 (정책)** 합니다.
5. 이전에 만든 사용자 흐름을 엽니다. 
6. **속성**을 선택합니다.
7. 구성 **웹 앱 세션 수명 (분)** , **웹 앱 세션 시간 제한**합니다 **Single sign-on 구성**, 및 **로그 아웃 요청에서 ID 토큰 필요**  필요에 따라 합니다.

    ![세션 동작 구성](./media/session-behavior/session-behavior.png)
    
8. **저장**을 클릭합니다.



