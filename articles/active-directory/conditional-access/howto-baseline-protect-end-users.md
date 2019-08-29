---
title: 기준 정책 최종 사용자 보호 (미리 보기)-Azure Active Directory
description: 사용자에 대해 multi-factor authentication을 요구 하는 조건부 액세스 정책
services: active-directory
ms.service: active-directory
ms.subservice: conditional-access
ms.topic: conceptual
ms.date: 05/16/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: calebb, rogoya
ms.collection: M365-identity-device-management
ms.openlocfilehash: 0f1a2e0bad39b54edc153416e4120bbc6912578c
ms.sourcegitcommit: 07700392dd52071f31f0571ec847925e467d6795
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/28/2019
ms.locfileid: "70125462"
---
# <a name="baseline-policy-end-user-protection-preview"></a>기준 정책: 최종 사용자 보호 (미리 보기)

관리자 계정도 MFA (multi-factor authentication)를 사용 하 여 보호 해야 하는 유일한 계정 이라고 생각 하는 경향이 있습니다. 관리자는 중요 한 정보에 대 한 광범위 한 액세스 권한을 가지 며 구독 전체 설정을 변경할 수 있습니다. 그러나 잘못 된 행위자는 최종 사용자를 대상으로 하는 경향이 있습니다. 액세스 권한을 획득 한 후 이러한 잘못 된 행위자는 원래 계정 소유자를 대신 하 여 권한 있는 정보에 대 한 액세스를 요청 하거나 전체 디렉터리를 다운로드 하 여 전체 조직에서 피싱 공격을 수행할 수 있습니다. 모든 사용자에 대 한 보호를 개선 하는 일반적인 방법 중 하나는 MFA (multi-factor authentication)와 같은 더 강력한 형태의 계정 확인을 요구 하는 것입니다.

보안 및 사용 편리성의 적절 한 균형을 유지 하려면 사용자가 로그인 할 때마다 사용자에 게 메시지를 표시 해서는 안 됩니다. 동일한 위치에서 동일한 장치에 로그인 하는 것과 같이 정상적인 사용자 동작을 반영 하는 인증 요청은 손상 가능성이 낮습니다. 위험한 것으로 간주 되 고 잘못 된 행위자의 특징을 표시 하는 로그인만 MFA 문제를 표시 합니다.

최종 사용자 보호는 모든 관리자 역할을 포함 하 여 디렉터리의 모든 사용자를 보호 하는 위험 기반 MFA [기준 정책](concept-baseline-protection.md) 입니다. 이 정책을 사용 하도록 설정 하려면 모든 사용자가 Authenticator 앱을 사용 하 여 MFA에 등록 해야 합니다. 사용자는 mfa 등록 프롬프트를 14 일 동안 무시할 수 있으며 그 후에는 MFA에 등록 하기 전까지 로그인이 차단 됩니다. MFA에 등록 되 면 사용자에 게 위험한 로그인 시도 중에만 MFA를 묻는 메시지가 표시 됩니다. 손상 된 사용자 계정은 해당 암호를 다시 설정 하 고 위험 검색을 해제할 때까지 차단 됩니다.

> [!NOTE]
> 이 정책은 게스트 계정을 포함 하는 모든 사용자에 게 적용 되며 모든 응용 프로그램에 로그인 할 때 평가 됩니다.

## <a name="recovering-compromised-accounts"></a>손상 된 계정 복구

Microsoft의 누출 자격 증명 서비스는 고객을 보호 하기 위해 공개적으로 사용할 수 있는 사용자 이름/암호 쌍을 찾습니다. 사용자 중 하 나와 일치 하는 경우 해당 계정을 즉시 보호 하는 데 도움이 됩니다. 누출 된 자격 증명이 있는 것으로 확인 된 사용자가 손상 된 것으로 확인 됩니다. 이러한 사용자는 암호를 다시 설정할 때까지 로그인이 차단 됩니다.

해당 디렉터리에서 기능을 사용 하도록 설정한 경우 Azure AD Premium 라이선스를 할당 받은 사용자는 셀프 서비스 암호 재설정 (SSPR)을 통해 액세스를 복원할 수 있습니다. 차단 될 프리미엄 라이선스가 없는 사용자는 관리자에 게 문의 하 여 수동 암호 재설정을 수행 하 고 플래그가 지정 된 사용자 위험 검색을 해제 해야 합니다.

### <a name="steps-to-unblock-a-user"></a>사용자를 차단 해제 하는 단계

사용자의 로그인 로그를 검사 하 여 사용자가 정책에 의해 차단 되었는지 확인 합니다.

1. 관리자는 **Azure Portal** 에 로그인 하 고 **Azure Active Directory** > >**사용자** 로 이동 하 여 사용자의 이름을 클릭 하 고 로그인으로 이동 해야 합니다.
1. 차단 된 사용자에 대해 암호 재설정을 시작 하려면 관리자가**위험 플래그가 지정** 된 **Azure Active Directory** > 사용자로 이동 해야 합니다.
1. 계정이 차단 된 사용자를 클릭 하 여 사용자의 최근 로그인 활동에 대 한 정보를 볼 수 있습니다.
1. 다음 로그인 시 변경 해야 할 임시 암호를 할당 하려면 암호 다시 설정을 클릭 합니다.
1. 모든 이벤트 해제를 클릭 하 여 사용자의 위험 점수를 다시 설정 합니다.

이제 사용자는 로그인 하 여 암호를 재설정 하 고 응용 프로그램에 액세스할 수 있습니다.

## <a name="deployment-considerations"></a>배포 고려 사항

**최종 사용자 보호** 정책은 디렉터리의 모든 사용자에 게 적용 되므로 원활한 배포를 위해 몇 가지 사항을 고려해 야 합니다. 이러한 고려 사항에는 MFA를 수행할 수 없는 Azure AD의 사용자 및 서비스 원칙과 최신 인증을 지원 하지 않는 조직에서 사용 하는 응용 프로그램 및 클라이언트를 식별 하는 작업이 포함 됩니다.

### <a name="legacy-protocols"></a>레거시 프로토콜

메일 클라이언트가 인증 요청을 수행 하는 데 사용 되는 레거시 인증 프로토콜 (IMAP, SMTP, POP3 등)입니다. 이러한 프로토콜은 MFA를 지원 하지 않습니다.  Microsoft에서 발생 하는 대부분의 계정 손상은 대부분 MFA를 바이패스 하는 레거시 프로토콜에 대 한 공격을 수행 하는 잘못 된 행위자에 의해 발생 합니다. 계정에 로그인 할 때 MFA가 필요 하 고 잘못 된 행위자가 MFA를 무시할 수 없도록 하려면이 정책은 레거시 프로토콜의 관리자 계정에 대 한 모든 인증 요청을 차단 합니다.

> [!WARNING]
> 이 정책을 사용 하도록 설정 하기 전에 사용자가 레거시 인증 프로토콜을 사용 하 고 있지 않은지 확인 합니다. [자세한 내용은 다음을 참조 하세요. 자세한 내용은 조건부 액세스](howto-baseline-protect-legacy-auth.md#identify-legacy-authentication-use) 를 사용 하 여 Azure AD에 대 한 레거시 인증을 차단 합니다.

## <a name="enable-the-baseline-policy"></a>기준 정책 사용

정책 **기준 정책: 최종 사용자 보호 (미리 보기** )는 미리 구성 되어 있으며 Azure Portal의 조건부 액세스 블레이드로 이동 하면 위쪽에 표시 됩니다.

이 정책을 사용 하도록 설정 하 고 사용자를 보호 하려면:

1. 전역 관리자, 보안 관리자 또는 조건부 액세스 관리자 권한으로 **Azure Portal** 에 로그인 합니다.
1. **Azure Active Directory** > **조건부 액세스**로 이동 합니다.
1. 정책 목록에서 기준 정책을 선택 **합니다. 최종 사용자 보호 (미리 보기**).
1. 정책 **사용을 즉시 정책**사용으로 설정 합니다.
1. **Save**을 클릭합니다.

## <a name="next-steps"></a>다음 단계

자세한 내용은 다음을 참조하세요.

* [조건부 액세스 기준 보호 정책](concept-baseline-protection.md)
* [ID 인프라를 보호하기 위한 5단계](../../security/fundamentals/steps-secure-identity.md)
* [Azure Active Directory의 조건부 액세스란?](overview.md)
