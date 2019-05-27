---
title: 조건부 액세스 기준 보호 정책-Azure Active Directory
description: 기준 조건부 액세스 정책을 일반적인 공격 으로부터 조직 보호
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
ms.openlocfilehash: ca062f4024bb5b0946812e00c8ccc1254b56d333
ms.sourcegitcommit: 13cba995d4538e099f7e670ddbe1d8b3a64a36fb
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/22/2019
ms.locfileid: "66003298"
---
# <a name="what-are-baseline-policies"></a>기본 정책 이란?

기본 정책은 조직에서는 여러 일반적인 공격 으로부터 보호 하는 미리 정의 된 정책 집합. 이러한 일반적인 공격 암호 스프레이, 재생 및 피싱에 포함할 수 있습니다. 기본 정책은 Azure AD의 모든 버전에서 사용할 수 있습니다. Microsoft 기본 보호 정책은 이러한에서 제공 하 모든 사용자에 게 id를 기반으로 공격 지난 몇 년간 점점 증가 하 고 되었기 때문입니다. 이러한 네 가지 정책의 목적은 모든 조직 기준 없이 사용 하도록 설정 하는 보안 수준을 갖도록 추가 비용입니다.  

사용자 지정된 조건부 액세스 정책 관리는 Azure AD Premium 라이선스가 필요 합니다.

## <a name="baseline-policies"></a>기준 정책

![Azure portal에서 조건부 액세스 기준 정책](./media/concept-baseline-protection/conditional-access-baseline-policies.png)

조직에서 사용할 수 있는 네 가지 기준 정책을 가지가 있습니다.

* [관리자에 대해 MFA 요구](howto-baseline-protect-administrators.md)
* [최종 사용자 보호 (미리 보기)](howto-baseline-protect-end-users.md)
* [블록 레거시 인증 (미리 보기)](howto-baseline-protect-legacy-auth.md)
* [서비스 관리 (미리 보기)에 대해 MFA 요구](howto-baseline-protect-azure.md)

이러한 정책의 모든 4 이전 Office 데스크톱 클라이언트, POP 및 IMAP 등 기존 인증 흐름에 영향을 줍니다.

### <a name="require-mfa-for-admins"></a>관리자에 대해 MFA 요구

전원 및 액세스 하는 관리자 계정으로 인해 특수 신중 하 게 처리 해야 있습니다. 권한 있는 계정의 보호를 향상 시키는 한 가지 일반적인 방법은 로그인을 사용할 때 보다 강력한 형식의 계정 확인을 요구 하는 합니다. Azure Active Directory에서 관리자에 대 한 등록 및 Azure Multi-factor Authentication을 사용 하 여 강력한 계정 확인을 가져올 수 있습니다.

[관리자에 대 한 MFA를 요구할](howto-baseline-protect-administrators.md) multi-factor authentication (MFA)을 요구 하는 기준 정책을 다음 디렉터리 역할의 경우 Azure AD 역할은 가장 권한이 있는 것으로 간주 됩니다.

* 전역 관리자
* SharePoint administrator
* Exchange 관리자
* 조건부 액세스 관리자
* 보안 관리자
* 기술 지원팀 관리자 / 암호 관리자
* 대금 청구 관리자
* 사용자 관리자

조직에 이러한 계정은 스크립트 또는 코드에서 사용 중인 경우 대체 하 여 고려해 야 [identities 관리](../managed-identities-azure-resources/overview.md)합니다. 임시 해결 방법으로 기준 정책에서 특정 사용자 계정을 제외할 수 있습니다.

### <a name="end-user-protection-preview"></a>최종 사용자 보호 (미리 보기)

높은 권한 있는 관리자 공격에서 대상 것이 아닙니다. 일반 사용자를 대상으로 믿지 못할 자는 경향이 있습니다. 엑세스를 믿지 못할 자 이러한 수 원래 계정 소유자를 대신 하 여 권한 있는 정보에 대 한 액세스를 요청 또는 전체 디렉터리 다운로드 후 전체 조직에 피싱 공격을 수행 합니다. 모든 사용자에 대 한 보호를 향상 시키는 한 가지 일반적인 방법은 위험한 로그인 감지 되 면 보다 강력한 형식의 계정 확인을 요구 하는 경우

**최종 사용자 보호 (미리 보기)** 디렉터리의 모든 사용자를 보호 하는 기준 정책입니다. 이 정책을 사용 하면 14 일 이내에 Azure Multi-factor Authentication에 등록 하는 모든 사용자가 필요 합니다. 등록 되 면 위험한 로그인 시도 하는 동안에 사용자 MFA에 대 한 메시지가 표시 됩니다. 손상 된 사용자 계정 암호 다시 설정 하 고 사건의 위험이 될 때까지 차단 됩니다.

### <a name="block-legacy-authentication-preview"></a>블록 레거시 인증 (미리 보기)

레거시 인증 프로토콜 (예: IMAP, SMTP, POP3)는 일반적으로 오래 된 메일 클라이언트 인증에 의해 사용 되는 프로토콜입니다. 레거시 프로토콜 multi-factor authentication 인증을 지원 하지 않습니다. 디렉터리에 대해 multi-factor authentication 인증을 요구 하는 정책을 하는 경우에 악의적 행위자가 이러한 레거시 프로토콜 중 하나를 사용 하 여 인증 하 고 multi-factor authentication을 바이패스할 수 있습니다.

계정을 보호 하기 위해 레거시 프로토콜에서 악의적인 인증 요청에서 가장 좋은 방법은 차단할 것입니다.

합니다 **블록 레거시 인증 (미리 보기)** 기준 정책 레거시 프로토콜을 사용 하 여 만들어진 인증 요청을 차단 합니다. 최신 인증을 성공적으로 모든 사용자에 대 한 로그인에 사용 되어야 합니다. 다른 기준 정책과 함께에서 사용 되는, 레거시 프로토콜에서 들어오는 요청 차단 됩니다. 또한 모든 사용자는 필요할 때마다 MFA를 해야 합니다. 이 정책은 Exchange ActiveSync를 차단 하지 않습니다.

### <a name="require-mfa-for-service-management-preview"></a>서비스 관리 (미리 보기)에 대해 MFA 요구

다양 한 Azure 서비스를 사용 하 여 조직과 같은 Azure Resource Manager 기반 도구에서 관리:

* Azure portal
* Azure PowerShell
* Azure CLI

이러한 도구 중 하나를 사용 하 여 리소스 관리를 수행 하려면 높은 권한이 필요한 작업입니다. 이러한 도구는 서비스 설정 및 구독 청구와 같은 구독 전체의 구성을 변경할 수 있습니다.

권한 있는 작업을 보호 하기 위해 이렇게 **서비스 관리 (미리 보기)에 대 한 MFA를 요구** 정책을 Azure portal, Azure PowerShell 또는 Azure CLI를 액세스 하는 모든 사용자에 대 한 multi-factor authentication을 요구 합니다.

## <a name="enable-a-baseline-policy"></a>기준 정책 사용

기준 정책을 사용할 수 있도록 합니다.

1. 에 로그인 합니다 **Azure portal** 전역 관리자, 보안 관리자 또는 조건부 액세스 관리자입니다.
1. 이동할 **Azure Active Directory** > **조건부 액세스**합니다.
1. 정책 목록에서 사용 하도록 설정 하려는 기준 정책을 선택 합니다.
1. 설정할 **정책을 사용 하도록 설정** 하 **에서**합니다.
1. 저장을 클릭 합니다.

## <a name="next-steps"></a>다음 단계

자세한 내용은 다음을 참조하세요.

* [ID 인프라를 보호하기 위한 5단계](../../security/azure-ad-secure-steps.md)
* [Azure Active Directory의 조건부 액세스란?](overview.md)
* [관리자에 대해 MFA 요구](howto-baseline-protect-administrators.md)
* [최종 사용자 보호 (미리 보기)](howto-baseline-protect-end-users.md)
* [블록 레거시 인증 (미리 보기)](howto-baseline-protect-legacy-auth.md)
* [서비스 관리 (미리 보기)에 대해 MFA 요구](howto-baseline-protect-azure.md)