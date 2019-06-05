---
title: 최종 사용자 보호 (미리 보기)-Azure Active Directory 기본 정책
description: 사용자에 대 한 다단계 인증을 요구 하도록 조건부 액세스 정책
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
ms.openlocfilehash: 104665774eee885cc2f562e9813cffcf23aa943e
ms.sourcegitcommit: 509e1583c3a3dde34c8090d2149d255cb92fe991
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/27/2019
ms.locfileid: "66235497"
---
# <a name="baseline-policy-end-user-protection-preview"></a>기본 정책: 최종 사용자 보호 (미리 보기)

관리자 계정에 multi-factor authentication (MFA)을 사용 하 여 보호 해야 하는 유일한 계정은 생각 하는 경향이 있습니다. 관리자는 중요 한 정보에 대 한 광범위 한 액세스 권한이 및 구독 전체의 설정을 변경할 수 있습니다. 그러나 믿지 못할 자 대상 최종 사용자에 게는 경향이 있습니다. 믿지 못할 자 이러한 액세스를 권한을 받은 후 원래 계정 소유자를 대신 하 여 권한 있는 정보에 대 한 액세스를 요청 하거나 다운로드에서 전체 디렉터리를 전체 조직에 피싱 공격을 수행할 수 있습니다. 모든 사용자에 대 한 보호를 향상 시키는 한 가지 일반적인 방법은 강력한 다단계 인증 (MFA)와 같은 계정 확인을 요구 하는 합니다.

보안 및 유용성의 적절 한 균형을 달성 하려면 사용자가에 로그인 할 때마다 단일가 라는 메시지가 표시 해서는 안 됩니다. 동일한 위치에서 동일한 장치에서 로그인과 같은 일반 사용자 동작을 반영 하는 인증 요청이 손상 가능성이 낮은 경우 MFA 챌린지가 로그인 위험한 것으로 간주 되 고 잘못 된 행위자의 특징을 보여 주는 라는 메시지가 나타납니다.

![사용자에 대해 MFA 요구](./media/howto-baseline-protect-end-users/baseline-policy-end-user-protection.png)

최종 사용자 보호는 위험 기반 MFA [기준 정책](concept-baseline-protection.md) 를 모든 관리자 역할을 포함 하는 디렉터리의 모든 사용자를 보호 하는 합니다. 이 정책을 사용 하면 Authenticator 앱을 사용 하 여 MFA에 등록 하는 모든 사용자가 필요 합니다. 사용자는 차단 됩니다 MFA에 등록 될 때까지 로그인에서 14 일간 MFA 등록 프롬프트를 무시할 수 있습니다. MFA에 등록 되 면 위험한 로그인 시도 하는 동안에 사용자 MFA에 대 한 메시지가 표시 됩니다. 손상 된 사용자 계정 암호 다시 설정 되 고 위험 이벤트 해제 된 될 때까지 차단 됩니다.

> [!NOTE]
> 이 정책은 게스트 계정을 포함 하 여 모든 사용자에 게 적용 되며 모든 응용 프로그램에 로그인 할 때 평가 됩니다.

## <a name="recovering-compromised-accounts"></a>손상 된 계정 복구

고객을 보호 하기 위해, Microsoft의 유출 된 자격 증명 서비스는 공개적으로 사용할 수 있는 사용자 이름/암호 쌍을 찾습니다. 일치 하는 경우 사용자에 해당 계정을 즉시 보호 있도록 도와 드립니다. 유출 된 자격 증명을 가진 것으로 식별 된 사용자는 손상 확인 됩니다. 이러한 사용자가 암호 재설정 될 때까지 로그인에서 차단 됩니다.

Azure AD Premium 라이선스가 할당 된 사용자가 디렉터리에서 기능을 사용 하는 경우 셀프 서비스 암호 재설정 (SSPR)을 통한 액세스를 복원할 수 있습니다. 차단 되는 premium 라이선스가 없는 사용자가 암호 직접 재설정을 수행 하 고 플래그가 지정 된 사용자 위험 이벤트를 해제 하려면 관리자에 문의 해야 합니다.

### <a name="steps-to-unblock-a-user"></a>사용자 차단 해제 하는 단계

사용자의 로그인 로그를 검사 하 여 사용자 정책에 의해 차단 되었다는 것을 확인 합니다.

1. 관리자로 로그인 해야 합니다 **Azure portal** 이동할 **Azure Active Directory** > **사용자** > 사용자의 이름을 클릭 하 고 이동 로그인 합니다.
1. 차단 된 사용자에 암호 재설정을 시작 하려면 관리자로 이동 해야 **Azure Active Directory** > **위험 플래그가 지정 된 사용자**
1. 사용자의 최근 로그인 활동에 대 한 정보를 보려는 계정이 차단 되는 사용자를 클릭 합니다.
1. 다음 로그인 시 변경 해야 하는 임시 암호를 할당 하는 데 암호 재설정을 클릭 합니다.
1. 사용자의 위험 점수를 다시 설정 하려면 모든 이벤트를 해제 하는 클릭 합니다.

사용자 수 이제 암호를 재설정 하 고 응용 프로그램에 액세스 합니다.

## <a name="deployment-considerations"></a>배포 고려 사항

때문에 합니다 **최종 사용자 보호** 디렉터리의 모든 사용자에 게 정책 적용, 원활한 배포에 대 한 몇 가지 고려 사항이 필요 합니다. 이러한 고려 사항은 사용자 및 응용 프로그램 및 최신 인증을 지원 하지 않는 조직에서 사용 하는 클라이언트와 함께 MFA를 수행 하지 않아야 하거나 없는 Azure AD에서 서비스 주체를 식별을 포함 합니다.

### <a name="legacy-protocols"></a>레거시 프로토콜

레거시 인증 프로토콜 (예: IMAP, SMTP, POP3, 등)는 인증 요청을 메일 클라이언트에서 사용 됩니다. 이러한 프로토콜에서 MFA를 지원 하지 않습니다.  대부분의 Microsoft에서 표시 되는 계정 손상은 MFA를 바이패스 하는 동안 레거시 프로토콜에 대 한 공격을 수행 하는 잘못 된 행위자가 발생 합니다. 계정에 로그인 할 때 MFA가 필요한 및 믿지 못할 자 MFA를 바이패스 하 수 없는 되도록이 정책에서 레거시 프로토콜 관리자 계정에 대 한 모든 인증 요청을 차단 합니다.

> [!WARNING]
> 이 정책은 사용 하기 전에 사용자가 레거시 인증 프로토콜을 사용 하지 않는 있는지 확인 합니다. 문서를 참조 [방법: Azure ad 조건부 액세스를 사용 하 여 레거시 인증 블록](howto-baseline-protect-legacy-auth.md#identify-legacy-authentication-use) 자세한 내용은 합니다.

### <a name="user-exclusions"></a>사용자 제외

이 기준 정책을 사용자를 제외 하는 옵션을 제공 합니다. 정책이 테 넌 트를 설정 하기 전에 다음 계정을 제외 하는 것이 좋습니다.

* **응급 액세스** 나 **비상** 테 넌 트 전체 계정 잠금을 방지 하기 위해 계정. 모든 관리자가 테 넌 트를 잠글 가능성이 적은 시나리오에서 응급 액세스 관리 계정의 테 넌 트에 대 한 액세스 복구 단계를 수행을 로그인에 사용할 수 있습니다.
   * 자세한 내용은 문서에서 찾을 수 있습니다 [Azure AD에서 응급 액세스 계정을 관리할](../users-groups-roles/directory-emergency-access.md)합니다.
* **서비스 계정** 하 고 **원칙 서비스**, Azure AD Connect 동기화 계정 등입니다. 서비스 계정에는 특정 사용자에 게 고정 되지 않은 비 대화형 계정입니다. 백 엔드 서비스에서 일반적으로 사용 하는 하며 응용 프로그램에 대 한 프로그래밍 방식의 액세스를 허용 합니다. MFA는 프로그래밍 방식으로 완료할 수 없습니다 때문 서비스 계정은 제외 해야 합니다.
   * 조직에 이러한 계정은 스크립트 또는 코드에서 사용 중인 경우 대체 하 여 고려해 야 [identities 관리](../managed-identities-azure-resources/overview.md)합니다. 임시 해결 방법으로 이러한 특정 계정 기본 정책에서 제외할 수 있습니다.
* 사용자 없는 또는 스마트 폰을 사용할 수 없습니다.
   * 이 정책에는 Microsoft Authenticator 앱을 사용 하 여 MFA에 등록 하려면 사용자가 필요 합니다.

## <a name="enable-the-baseline-policy"></a>기준 정책을 사용 하도록 설정

정책 **기준 정책: 최종 사용자 보호 (미리 보기)** 미리 구성 되어 및 Azure portal에서 조건부 액세스 블레이드로 이동 하면 위쪽에 표시 됩니다.

이 정책을 사용 하도록 설정 및 사용자를 보호 합니다.

1. 에 로그인 합니다 **Azure portal** 전역 관리자, 보안 관리자 또는 조건부 액세스 관리자입니다.
1. 이동할 **Azure Active Directory** > **조건부 액세스**합니다.
1. 정책 목록에서 선택 **기준 정책: 최종 사용자 보호 (미리 보기)** 합니다.
1. 설정할 **정책을 사용 하도록 설정** 하 **즉시 정책을 사용 하 여**입니다.
1. 클릭 하 여 모든 사용자 제외를 추가할 **사용자** > **제외 된 사용자 선택** 제외할 수 해야 하는 사용자를 선택 합니다. 클릭 **선택** 한 다음 **수행**합니다.
1. 클릭 **저장할**합니다.

## <a name="next-steps"></a>다음 단계

자세한 내용은 다음을 참조하세요.

* [조건부 액세스 기준 보호 정책](concept-baseline-protection.md)
* [ID 인프라를 보호하기 위한 5단계](../../security/azure-ad-secure-steps.md)
* [Azure Active Directory의 조건부 액세스란?](overview.md)
