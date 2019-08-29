---
title: Azure Active Directory Identity Protection에서 다단계 인증 등록 정책을 구성하는 방법 | Microsoft Docs
description: Azure AD ID 보호 다단계 인증 등록 정책을 구성하는 방법에 대해 알아봅니다.
services: active-directory
ms.service: active-directory
ms.subservice: identity-protection
ms.topic: conceptual
ms.date: 05/01/2019
author: MicrosoftGuyJFlo
manager: daveba
ms.author: joflore
ms.reviewer: sahandle
ms.collection: M365-identity-device-management
ms.openlocfilehash: 939f08fd16cf27e641cf6436a00396ad2db8e6c3
ms.sourcegitcommit: 07700392dd52071f31f0571ec847925e467d6795
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/28/2019
ms.locfileid: "70126384"
---
# <a name="how-to-configure-the-azure-multi-factor-authentication-registration-policy"></a>방법: Azure Multi-Factor Authentication 등록 정책 구성

Azure AD ID 보호은 로그인 하는 최신 인증 앱에 관계 없이 MFA 등록을 요구 하는 조건부 액세스 정책을 구성 하 여 MFA (multi-factor authentication) 등록의 롤아웃을 관리 하는 데 도움이 됩니다. 이 문서에서는 정책을 사용할 수 있는 대상과 구성 하는 방법에 대해 설명 합니다.



## <a name="what-is-the-azure-multi-factor-authentication-registration-policy"></a>Azure Multi-Factor Authentication 등록 정책은 무엇 인가요?

Azure Multi-Factor Authentication은 사용자 이름과 암호를 사용 하는 사용자를 확인 하는 수단을 제공 합니다. 사용자 로그인에 대 한 두 번째 보안 계층을 제공 합니다. 사용자가 MFA 프롬프트에 응답할 수 있도록 하려면 먼저 Azure Multi-Factor Authentication에 등록 해야 합니다.

사용자 로그인에는 Azure Multi-Factor Authentication가 필요 하므로 다음을 수행 하는 것이 좋습니다.

- 다양한 간편한 확인 옵션과 함께 강력한 인증 제공
- Id 보호에서 위험 검색을 보호 하 고 복구 하기 위해 조직을 준비 하는 데 중요 한 역할을 합니다.

MFA에 대 한 자세한 내용은 [Azure Multi-Factor Authentication 이란?](../authentication/howto-mfa-getstarted.md) 을 참조 하세요.

## <a name="how-do-i-access-the-registration-policy"></a>등록 정책에 액세스 어떻게 할까요??

MFA 등록 정책은 **Azure AD ID 보호 페이지**의 [구성](https://portal.azure.com/#blade/Microsoft_AAD_ProtectionCenter/IdentitySecurityDashboardMenuBlade/SignInPolicy) 섹션에 있습니다.

![MFA 정책](./media/howto-mfa-policy/1014.png)

## <a name="policy-settings"></a>정책 설정

MFA 등록 정책을 구성할 때 다음과 같은 구성을 변경 해야 합니다.

- 정책이 적용 되는 사용자 및 그룹입니다. 조직의 [응급 액세스 계정을](../users-groups-roles/directory-emergency-access.md)제외 해야 합니다.

    ![사용자 및 그룹](./media/howto-mfa-policy/11.png)

- 적용 하려는 컨트롤- **AZURE MFA 등록이 필요** 합니다.

    ![액세스](./media/howto-mfa-policy/12.png)

- 정책 적용을 **On**으로 설정 해야 합니다.

    ![정책 적용](./media/howto-mfa-policy/14.png)

- 정책 **저장**

## <a name="user-experience"></a>사용자 경험

Azure Active Directory Identity Protection는 다음에 대화형으로 로그인 할 때 사용자에 게 등록 하 라는 메시지를 표시 합니다.

관련 사용자 환경에 대한 개요는 다음을 참조하세요.

- [Multi-Factor Authentication 등록 흐름](flows.md#multi-factor-authentication-registration)  
- [Azure AD ID 보호를 사용하는 로그인 환경](flows.md)  

## <a name="next-steps"></a>다음 단계

Azure AD ID 보호의 개요는 [Azure AD ID 보호 개요](overview.md)를 참조하세요.
