---
title: MFA 등록 정책 구성 - Azure Active 디렉터리 ID 보호
description: Azure AD ID 보호 다단계 인증 등록 정책을 구성하는 방법에 대해 알아봅니다.
services: active-directory
ms.service: active-directory
ms.subservice: identity-protection
ms.topic: conceptual
ms.date: 10/18/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: sahandle
ms.collection: M365-identity-device-management
ms.openlocfilehash: 2fa6a4cf184b426355f62117ea51642127eee529
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/27/2020
ms.locfileid: "74382135"
---
# <a name="how-to-configure-the-azure-multi-factor-authentication-registration-policy"></a>방법: Azure 다단계 인증 등록 정책 구성

Azure AD ID 보호를 사용하면 로그인하는 최신 인증 앱에 관계없이 MFA 등록을 요구하는 조건부 액세스 정책을 구성하여 MFA(Azure 다단계 인증) 등록의 롤아웃을 관리할 수 있습니다.

## <a name="what-is-the-azure-multi-factor-authentication-registration-policy"></a>Azure 다단계 인증 등록 정책은 무엇입니까?

Azure 다단계 인증은 사용자 이름과 암호 그 이상을 사용하는 사용자를 확인하는 수단을 제공합니다. 사용자 로그인에 대한 두 번째 보안 계층을 제공합니다. 사용자가 MFA 프롬프트에 응답하려면 먼저 Azure 다단계 인증에 등록해야 합니다.

다음과 같은 이유로 사용자 로그인에 Azure 다단계 인증이 필요한 것이 좋습니다.

- 다양한 확인 옵션을 통해 강력한 인증을 제공합니다.
- ID 보호의 위험 탐지로부터 자체 수정할 수 있도록 조직을 준비하는 데 핵심적인 역할을 합니다.

Azure 다단계 인증에 대한 자세한 [내용은 Azure 다단계 인증이란 무엇입니까?](../authentication/howto-mfa-getstarted.md)

## <a name="policy-configuration"></a>정책 구성

1. [Azure 포털로](https://portal.azure.com)이동합니다.
1. Azure **Active 디렉터리** > **보안** > **ID 보호** > **MFA 등록 정책으로**이동합니다.
   1. **과제** 중
      1. **사용자** - 롤아웃을 제한하는 경우 **모든 사용자를** 선택하거나 개인 및 **그룹을 선택합니다.**
         1. 선택적으로 정책에서 사용자를 제외하도록 선택할 수 있습니다.
   1. **언더 컨트롤**
      1. **Azure MFA 등록 필요** 확인란이 선택되어 있는지 확인하고 을 **선택합니다.**
   1. **정책** - **적용**
   1. **저장**

## <a name="user-experience"></a>사용자 환경

Azure Active Directory ID 보호는 사용자가 다음에 대화식으로 로그인할 때 등록하라는 메시지를 표시하며 등록을 완료하는 데 14일이 필요합니다. 이 14일 동안 등록을 우회할 수 있지만 기간이 끝나면 로그인 프로세스를 완료하기 전에 등록해야 합니다.

관련 사용자 환경에 대한 개요는 다음을 참조하세요.

- [Azure AD ID 보호에 대한 로그인 환경.](concept-identity-protection-user-experience.md)  

## <a name="next-steps"></a>다음 단계

- [로그인 및 사용자 위험 정책 사용](howto-identity-protection-configure-risk-policies.md)

- [Azure AD 셀프 서비스 암호 재설정 사용](../authentication/howto-sspr-deployment.md)

- [Azure Multi-Factor Authentication 사용](../authentication/howto-mfa-getstarted.md)
