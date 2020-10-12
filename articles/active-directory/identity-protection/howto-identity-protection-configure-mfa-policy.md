---
title: MFA 등록 정책 구성-Azure Active Directory Identity Protection
description: Azure AD ID 보호 다단계 인증 등록 정책을 구성하는 방법에 대해 알아봅니다.
services: active-directory
ms.service: active-directory
ms.subservice: identity-protection
ms.topic: how-to
ms.date: 06/05/2020
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: sahandle
ms.collection: M365-identity-device-management
ms.openlocfilehash: d4c6b57eaa2a68906053faade48dd0e63fbf0db7
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/09/2020
ms.locfileid: "84464337"
---
# <a name="how-to-configure-the-azure-multi-factor-authentication-registration-policy"></a>방법: Azure Multi-Factor Authentication 등록 정책 구성

Azure AD ID 보호은 로그인 하는 최신 인증 앱에 관계 없이 MFA 등록을 요구 하는 조건부 액세스 정책을 구성 하 여 Azure Multi-Factor Authentication (MFA) 등록의 롤아웃을 관리 하는 데 도움이 됩니다.

## <a name="what-is-the-azure-multi-factor-authentication-registration-policy"></a>Azure Multi-Factor Authentication 등록 정책은 무엇 인가요?

Azure Multi-Factor Authentication은 사용자 이름과 암호를 사용 하는 사용자를 확인 하는 수단을 제공 합니다. 사용자 로그인에 대 한 두 번째 보안 계층을 제공 합니다. 사용자가 MFA 프롬프트에 응답할 수 있도록 하려면 먼저 Azure Multi-Factor Authentication에 등록 해야 합니다.

사용자 로그인에는 Azure Multi-Factor Authentication가 필요 하므로 다음을 수행 하는 것이 좋습니다.

- 에서는 다양 한 확인 옵션을 통해 강력한 인증을 제공 합니다.
- 는 Id 보호에서 위험 검색을 자동으로 재구성 하도록 조직을 준비 하는 데 중요 한 역할을 합니다.

Azure Multi-Factor Authentication에 대 한 자세한 내용은 [azure Multi-Factor Authentication 란?](../authentication/howto-mfa-getstarted.md) 을 참조 하세요.

## <a name="policy-configuration"></a>정책 구성

1. [Azure Portal](https://portal.azure.com)로 이동합니다.
1. **Azure Active Directory**  >  **보안**  >  **id 보호**  >  **MFA 등록 정책**으로 이동 합니다.
   1. **할당** 아래
      1. **사용자** -출시를 제한 하는 경우 **모든 사용자** 를 선택 하거나 **개인 및 그룹을 선택** 합니다.
         1. 필요에 따라 정책에서 사용자를 제외 하도록 선택할 수 있습니다.
   1. **컨트롤** 에서
      1. **AZURE MFA 등록 필요** 확인란을 선택 했는지 확인 하 고 **선택**을 선택 합니다.
   1. **정책 적용**  -  **설정**
   1. **저장**

## <a name="user-experience"></a>사용자 환경

사용자에 게 다음에 대화형으로 로그인 할 때 등록 하 라는 메시지가 표시 되 고 등록을 완료 하는 데 14 일이 Azure Active Directory Identity Protection 됩니다. 이 14 일 동안에는 등록을 무시할 수 있지만 로그인 프로세스를 완료 하기 전에 등록 해야 하는 기간이 종료 되었습니다.

관련 사용자 환경에 대한 개요는 다음을 참조하세요.

- [Azure AD ID 보호를 사용 하는 로그인 환경](concept-identity-protection-user-experience.md)  

## <a name="next-steps"></a>다음 단계

- [로그인 및 사용자 위험 정책 사용](howto-identity-protection-configure-risk-policies.md)

- [Azure AD 셀프 서비스 암호 재설정 사용](../authentication/howto-sspr-deployment.md)

- [Azure Multi-Factor Authentication 사용](../authentication/howto-mfa-getstarted.md)
