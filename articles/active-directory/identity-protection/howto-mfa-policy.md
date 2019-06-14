---
title: Azure Active Directory Identity Protection에서 다단계 인증 등록 정책을 구성하는 방법 | Microsoft Docs
description: Azure AD ID 보호 다단계 인증 등록 정책을 구성하는 방법에 대해 알아봅니다.
services: active-directory
ms.service: active-directory
ms.subservice: identity-protection
ms.topic: article
ms.date: 05/01/2019
author: MicrosoftGuyJFlo
manager: daveba
ms.author: joflore
ms.reviewer: sahandle
ms.collection: M365-identity-device-management
ms.openlocfilehash: 434d07163713a139b42a5dbe1664f81dafc2a1ca
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/13/2019
ms.locfileid: "67108932"
---
# <a name="how-to-configure-the-azure-multi-factor-authentication-registration-policy"></a>방법: Azure Multi-factor Authentication 등록 정책 구성

Azure AD Id 보호에 어떤 앱에 로그인 없이 등록 MFA를 요구 하도록 조건부 액세스 정책을 구성 하 여 multi-factor authentication (MFA) 등록의 롤아웃을 관리할 수 있습니다. 이 문서에는 어떤 정책에 사용할 수 있습니다 및 구성 하는 방법을 설명 합니다.

## <a name="what-is-the-azure-multi-factor-authentication-registration-policy"></a>Azure Multi-factor Authentication 등록 정책 이란?

Azure Multi-factor Authentication 사용 하는 누구 인지 확인 하는 방법을 보다 방금 사용자 이름과 암호를 제공 합니다. 두 번째 사용자가 로그인 하는 보안 계층을 제공합니다. 사용자가 MFA 프롬프트에 응답할 수 순서로 Azure Multi-factor Authentication에 대 한 등록 먼저 해야 합니다.

때문에 사용자가 로그인에 대 한 Azure Multi-factor Authentication을 요구 하는 것이 좋습니다 것:

- 다양한 간편한 확인 옵션과 함께 강력한 인증 제공
- 보호 및 Id 보호에서 위험 이벤트에서 복구 하기 위해 조직 준비 과정의 중요 한 역

MFA에 대 한 자세한 내용은 참조 하세요. [Azure Multi-factor Authentication 이란?](../authentication/howto-mfa-getstarted.md)

## <a name="how-do-i-access-the-registration-policy"></a>등록 정책에 액세스 하려면 어떻게 해야 합니까?

MFA 등록 정책은 **Azure AD ID 보호 페이지**의 [구성](https://portal.azure.com/#blade/Microsoft_AAD_ProtectionCenter/IdentitySecurityDashboardMenuBlade/SignInPolicy) 섹션에 있습니다.

![MFA 정책](./media/howto-mfa-policy/1014.png)

## <a name="policy-settings"></a>정책 설정

MFA 등록 정책을 구성할 때 다음 구성 변경 내용을 적용 해야 합니다.

- 사용자 및 그룹 정책에 적용 됩니다. 조직의 제외 해야 [응급 액세스 계정은](../users-groups-roles/directory-emergency-access.md)합니다.

    ![개요](./media/howto-mfa-policy/11.png)

- -적용할 컨트롤 **필요한 Azure MFA 등록**

    ![Access](./media/howto-mfa-policy/12.png)

- 적용 정책에 설정할 **에서**합니다.

    ![정책 적용](./media/howto-mfa-policy/14.png)

- **저장** 정책

## <a name="user-experience"></a>사용자 경험

관련 사용자 환경에 대한 개요는 다음을 참조하세요.

- [Multi-Factor Authentication 등록 흐름](flows.md#multi-factor-authentication-registration)  
- [Azure AD ID 보호를 사용하는 로그인 환경](flows.md)  

## <a name="next-steps"></a>다음 단계

Azure AD ID 보호의 개요는 [Azure AD ID 보호 개요](overview.md)를 참조하세요.
